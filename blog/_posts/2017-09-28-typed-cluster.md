---
layout: series_post
title: "Akka Typed: New Cluster API"
description: ""
author: Patrik Nordwall
series_title: Akka Typed Cluster and Persistence
series_tag: typed
tags: [actor,typed]
---
{% include JB/setup %}

Akka Typed has taken a big step forward. It's now possible to use Akka Typed with Akka Cluster, Distributed Data, Cluster Sharding, Cluster Singleton, and Persistence. We have developed new typed APIs for all these features, and a new distributed registry of actor references that is the replacement of `ActorSelection` in untyped actors.

These features are still using the existing, proven, implementations from the untyped modules and they require that you run with the untyped `ActorSystem` as described in [Akka Typed: Coexistence](https://akka.io/blog/2017/05/06/typed-coexistence). The alternative would have been to re-implement everything in pure Akka Typed actors, but that would have taken much longer time to complete. That said, in the long term future we might do that.

This and two more blog posts will illustrate the new typed APIs, with the assumption that you are already familiar with corresponding untyped APIs. You find more information in the [API documentation](https://doc.akka.io/api/akka/current/akka/typed/index.html). We expect a few more iterations to improve the APIs, so your feedback would be very valuable.

## Starting the Cluster

Configuration of clustering (and remoting) is the same as in [untyped Cluster](https://doc.akka.io/docs/akka/current/scala/cluster-usage.html). Note that you must start an untyped `ActorSystem`.

```
import import akka.typed.scaladsl.adapter._
import akka.typed.cluster.scaladsl.Cluster
 
val untypedSystem = akka.actor.ActorSystem("Sys")
val system = untypedSystem.toTyped

val cluster = Cluster(system) 
```

The typed `Cluster` extension gives access to two management tasks (joining, leaving, downing, ...) and subscription of cluster membership events (`MemberUp`, `MemberRemoved`, `UnreachableMember`, ...). Those are exposed as two different actor references, i.e. it's a message based API. For example joining can be done like this:

```
cluster.manager ! JoinSeedNodes("akka://Sys@host1:2552" :: "akka://Sys@host2:2552" :: Nil)
```

Subscription of cluster events is done via the `cluster.subscriptions` actor reference and that will be illustrated in an example later in this blog post. 

There are also some accessors to current cluster state, such as `cluster.selfMember` and `cluster.state`.

## The Receptionist

With untyped actors you would use `ActorSelection` to "lookup" actors running on other nodes. Given an actor path with address information you can get hold of an `ActorRef` to any actor. `ActorSelection` doesn't exist in Akka Typed, so how do you get the actor references? You can send refs in messages but you need something to bootstrap the interaction.

For this purpose there is an actor called the `Receptionist`. You register the specific actors that should be discoverable from other nodes in the local `Receptionist` instance. The API of the receptionist is also based on actor messages, of course. This registry of actor references is then automatically distributed to all other nodes in the cluster. You can lookup such actors with the key that was used when they were registered. The reply to such a `Find` request is a `Listing`, which contains a `Set` of actor references that are registered for the key. Note that several actors can be registered to the same key.

The registry is dynamic. New actors can be registered during the lifecylce of the system. Entries are removed when registered actors are stopped or a node is removed from the cluster. To facilitate this dynamic aspect you can also subscribe to changes with the `Receptionist.Subscribe` message. It will send `Listing` messages to the subscriber when entries for a key are changed.

Let's look at how we can build a dynamic, cluster aware router using the `Receptionist`.

```
import java.util.concurrent.ThreadLocalRandom
import akka.typed.ActorRef
import akka.typed.Behavior
import akka.typed.cluster.Cluster
import akka.typed.cluster.Subscribe
import akka.typed.receptionist.Receptionist
import akka.typed.receptionist.Receptionist.Listing
import akka.typed.receptionist.Receptionist.ServiceKey
import akka.typed.scaladsl.Actor

object RandomRouter {

  def router[T](serviceKey: ServiceKey[T]): Behavior[T] =
    Actor.deferred[Any] { ctx ⇒
      ctx.system.receptionist ! Receptionist.Subscribe(serviceKey, ctx.self)

      def routingBehavior(routees: Vector[ActorRef[T]]): Behavior[Any] =
        Actor.immutable { (ctx, msg) ⇒
          msg match {
            case Listing(_, services) ⇒
              routingBehavior(services.toVector)
            case other: T @unchecked ⇒
              if (routees.isEmpty)
                Actor.unhandled
              else {
                val i = ThreadLocalRandom.current.nextInt(routees.size)
                routees(i) ! other
                Actor.same
              }
          }
        }

      routingBehavior(Vector.empty)
    }.narrow[T]
}    
```

You can see how the router subscribes to a key and keeps track of actor references of the active routees. Incoming messages are routed with a simple random selection among the routees.

The target routees are started on all or a subset of nodes in the cluster and register themselves to the receptionist:

```
object Routee {

  val PingServiceKey = Receptionist.ServiceKey[Ping]("pingService")

  final case class Ping(replyTo: ActorRef[Pong.type])
  final case object Pong

  val behavior: Behavior[Ping] =
    Actor.deferred { ctx ⇒
      ctx.system.receptionist ! Receptionist.Register(PingServiceKey, ctx.self, ctx.system.deadLetters)

      Actor.immutable[Ping] { (ctx, msg) ⇒
        msg match {
          case Ping(replyTo) ⇒
            println(s"Routee ${ctx.self} got ping")
            Actor.same
        }
      }
    }

}
```

Pretty amazing functionality in 20 lines of code!

The `Receptionist` works in the same way with a local actor system. The local implementation is picked if cluster is not enabled. The implementation of the cluster receptionist is using Distributed Data, and it's worth noting that the registry is eventually consistent, i.e. it may take a short while until registered actors are visible on other nodes.  

We can make the router even better. Routee entries are removed when nodes are removed from the cluster, but before that nodes can be observed by the failure detector as unreachable and it would be nice to avoid routing messages to such unreachable nodes. To add that functionality we need to subscribe to cluster reachability events like this:

```
object RandomRouter {
  private final case class WrappedReachabilityEvent(event: ReachabilityEvent)

  // same as above, but also subscribes to cluster reachability events and
  // avoids routees that are unreachable
  def clusterRouter[T](serviceKey: ServiceKey[T]): Behavior[T] =
    Actor.deferred[Any] { ctx ⇒
      ctx.system.receptionist ! Receptionist.Subscribe(serviceKey, ctx.self)

      val cluster = Cluster(ctx.system)
      // typically you have to map such external messages into this
      // actor's protocol with a message adapter
      val reachabilityAdapter: ActorRef[ReachabilityEvent] = ctx.spawnAdapter(WrappedReachabilityEvent.apply)
      cluster.subscriptions ! Subscribe(reachabilityAdapter, classOf[ReachabilityEvent])

      def routingBehavior(routees: Vector[ActorRef[T]], unreachable: Set[Address]): Behavior[Any] =
        Actor.immutable { (ctx, msg) ⇒
          msg match {
            case Listing(_, services) ⇒
              routingBehavior(services.toVector, unreachable)
            case WrappedReachabilityEvent(event) => event match {
              case UnreachableMember(m) =>
                routingBehavior(routees, unreachable + m.address)
              case ReachableMember(m) =>
                routingBehavior(routees, unreachable - m.address)
            }

            case other: T @unchecked ⇒
              if (routees.isEmpty)
                Actor.unhandled
              else {
                val reachableRoutes =
                  if (unreachable.isEmpty) routees
                  else routees.filterNot { r => unreachable(r.path.address) }

                val i = ThreadLocalRandom.current.nextInt(reachableRoutes.size)
                reachableRoutes(i) ! other
                Actor.same
              }
          }
        }

      routingBehavior(Vector.empty, Set.empty)
    }.narrow[T]
}
```

Another thing that is good to know is that the same [serialization](https://doc.akka.io/docs/akka/current/scala/serialization.html) mechanism for messages as for untyped actors is also used in Akka Typed. Actor references are typically included in the messages, since there is no `sender`. To serialize actor references to/from string representation you will use the `ActorRefResolver` extension as illustrated in the [PingSerializer](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/scala/blog/typed/cluster/scaladsl/PingSerializer.scala).

The full source code of these examples, are available in [patriknw/akka-typed-blog](https://github.com/patriknw/akka-typed-blog).

Let us know what you think about these APIs. In the next blog post we will take a look at the typed APIs for Distributed Data, Cluster Singleton, and Cluster Sharding.
