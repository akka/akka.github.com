---
layout: series_post
title: "Akka Typed: New Cluster Tools API"
description: ""
author: Patrik Nordwall
category: article
series_title: Akka Typed Cluster and Persistence
series_tag: typed-cluster
tags: [actor,typed,typed-cluster]
---
{% include JB/setup %}

Note: Code examples in this blog post are **out of date**, see the [Akka documentation](https://doc.akka.io/docs/akka/current/typed/cluster.html#higher-level-cluster-tools) for latest information on this topic.

In [previous post](https://akka.io/blog/2017/09/28/typed-cluster) we looked at the the Cluster and Receptionist for Akka Typed. In this post you will be introduced to the new typed APIs for Distributed Data, Cluster Singleton and Cluster Sharding. These features are still using the existing implementations from the untyped modules and they require that you run with the untyped ActorSystem as described in [Akka Typed: Coexistence](https://akka.io/blog/2017/05/06/typed-coexistence).

## Distributed Data

The API for [Distributed Data](https://doc.akka.io/docs/akka/current/scala/distributed-data.html) is very similar to the untyped API, with the difference that you have to pass the `replyTo` actor reference in the messages as there is no `sender`. Here is an example:

```scala
import akka.cluster.ddata.GCounter
import akka.cluster.ddata.GCounterKey
import akka.cluster.ddata.ReplicatedData
import akka.typed.ActorRef
import akka.typed.Behavior
import akka.typed.cluster.ddata.scaladsl.DistributedData
import akka.typed.cluster.ddata.scaladsl.Replicator
import akka.typed.scaladsl.Actor
import akka.typed.scaladsl.adapter._

object Counter {
  sealed trait ClientCommand
  final case object Increment extends ClientCommand
  final case class GetValue(replyTo: ActorRef[Int]) extends ClientCommand

  private sealed trait InternalMsg extends ClientCommand
  private case class InternalUpdateResponse[A <: ReplicatedData](rsp: Replicator.UpdateResponse[A]) extends InternalMsg
  private case class InternalGetResponse[A <: ReplicatedData](rsp: Replicator.GetResponse[A]) extends InternalMsg

  val Key = GCounterKey("counter")

  def behavior: Behavior[ClientCommand] =
    Actor.deferred[ClientCommand] { ctx ⇒
      // The ddata types still need the implicit untyped Cluster.
      // We will look into another solution for that.
      implicit val cluster = akka.cluster.Cluster(ctx.system.toUntyped)
      val replicator: ActorRef[Replicator.Command] = DistributedData(ctx.system).replicator

      // use message adapters to map the external messages (replies) to the message types
      // that this actor can handle (see InternalMsg)
      val updateResponseAdapter: ActorRef[Replicator.UpdateResponse[GCounter]] =
        ctx.spawnAdapter(InternalUpdateResponse.apply)

      val getResponseAdapter: ActorRef[Replicator.GetResponse[GCounter]] =
        ctx.spawnAdapter(InternalGetResponse.apply)

      Actor.immutable[ClientCommand] { (ctx, msg) ⇒
        msg match {
          case Increment ⇒
            replicator ! Replicator.Update(Key, GCounter.empty, Replicator.WriteLocal, updateResponseAdapter)(_ + 1)
            Actor.same

          case GetValue(replyTo) ⇒
            replicator ! Replicator.Get(Key, Replicator.ReadLocal, getResponseAdapter, Some(replyTo))
            Actor.same

          case internal: InternalMsg ⇒ internal match {
            case InternalUpdateResponse(_) ⇒ Actor.same // ok

            case InternalGetResponse(rsp @ Replicator.GetSuccess(Key, Some(replyTo: ActorRef[Int] @unchecked))) ⇒
              val value = rsp.get(Key).value.toInt
              replyTo ! value
              Actor.same

            case InternalGetResponse(rsp) ⇒
              Actor.unhandled // not dealing with failures
          }
        }
      }
    }

}

```

Note that the messages such as `Replicator.Update` are defined in `akka.typed.cluster.ddata.scaladsl.Replicator`, but the actual CRDTs are the same as in untyped, for example `akka.cluster.ddata.GCounter`.

## Cluster Singleton

The API for [Cluster Singleton](https://doc.akka.io/docs/akka/current/scala/cluster-singleton.html) is simplified compared to the untyped API. It is based on an extension that provides a single `spawn` method that starts both the `SingletonManager` and the `SingletonProxy`. It returns an `ActorRef` to the `SingletonProxy`, which is used for sending messages to the singleton instance that is running somewhere (oldest node) in the cluster. You should call this `ClusterSingleton.spawn` method on all nodes at system startup.

If the node doesn't have the given role then only the proxy is started, and not the manager.

Here is an example of a global sequence number generator:

```scala
object SequenceNumberGenerator {

  sealed trait Message
  final case class Next(replyTo: ActorRef[Long]) extends Message
  case object Stop extends Message

  def generator(n: Long = System.currentTimeMillis()): Behavior[Message] = Actor.immutable {
    // a real generator would perhaps store the counter with Distributed Data to
    // be able to continue with next number after fail over
    case (_, Next(replyTo)) =>
      val next = n + 1
      replyTo ! next
      generator(next)
    case (_, Stop) =>
      Actor.stopped
  }

}
```

and this is how to start the singleton manager and proxy:

```scala
val singletonProxy: ActorRef[SequenceNumberGenerator.Message] =
  ClusterSingleton(typedSystem).spawn(
    behavior = SequenceNumberGenerator.generator(),
    singletonName = "seqNr",
    props = Props.empty,
    settings = ClusterSingletonSettings(typedSystem),
    terminationMessage = SequenceNumberGenerator.Stop)
```
.

## Cluster Sharding

The API for [Cluster Sharding](https://doc.akka.io/docs/akka/current/scala/cluster-sharding.html) has also been simplified. You start the sharded type with the `ClusterSharding` extension:

```scala
import akka.typed.cluster.sharding.ClusterSharding
import akka.typed.cluster.sharding.ClusterShardingSettings

ClusterSharding(typedSystem).spawn[BlogCommand](
  behavior = BlogPost.shardingBehavior,
  props = Props.empty,
  typeKey = BlogPost.ShardingTypeName,
  settings = ClusterShardingSettings(typedSystem),
  maxNumberOfShards = BlogPost.MaxNumberOfShards,
  handOffStopMessage = PassivatePost)
```

The `BlogPost` actor is a persistent actor but we will not look at the details of that now. Persistent actors for Akka Typed will be described in a separate blog post in a few days.

The `typeKey` is defined as:

```scala
val ShardingTypeName = EntityTypeKey[BlogCommand]("BlogPost")
```

This key is then used to retrieve an `EntityRef` for a given entity identifier. Messages to a specific entity are then sent via this `EntityRef`.

```scala
  val postId = UUID.randomUUID().toString
  val content = PostContent(postId, "Title...", "Body...")

  val entityRef: EntityRef[BlogCommand] =
    sharding.entityRefFor(BlogPost.ShardingTypeName, postId)

  entityRef ! AddPost(content, addPostReplyAdapter)
```

Note that in above `ClusterSharding.spawn` we are not defining any `extractEntityId` or `extractShardId` functions. Instead, the messages are sent in an envelope message that carry the entity identifier, and the shard identifier is derived from the entity id and the `maxNumberOfShards` parameter with a simple hashing function. That means that the actual domain messages don't have to include the entity id. This is what the `EntityRef` is using under the hood. It has the entity id and can wrap the sent message in such an envelope.

It is still possible to define extractor functions and send messages directly to the shard region instead of using the `EntityRef`, if you prefer that.

The full source code of these examples, are available in [patriknw/akka-typed-blog](https://github.com/patriknw/akka-typed-blog).

Let us know what you think about these APIs. In the next blog post we will take a look at the typed APIs for Persistence.
