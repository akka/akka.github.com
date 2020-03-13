---
layout: series_post
title: "Akka Typed: Mutable vs. Immutable"
description: ""
author: Patrik Nordwall
redirect_from: /typed/2017/05/08/typed-mutable-vs-immutable
series_title: Introducing Akka Typed
category: article
series_tag: typed
tags: [actor,typed]
---
{% include JB/setup %}

Note: Code examples in this blog post are **out of date**, see the [Akka documentation](https://doc.akka.io/docs/akka/current/typed/coexisting.html) for latest information on this topic.

In the [introduction blog post]({{ site.baseurl }}/blog/2017/05/05/typed-intro) we introduced the APIs `Actor.mutable` and `Actor.immutable`. We recommend the immutable style as the “default choice” and now we will illustrate the two styles with another example and elaborate more around when to use the mutable vs. immutable style.

In this post we will look at how to implement a round-robin router with Akka Typed. By the way, [Routers](https://doc.akka.io/docs/akka/2.5/scala/routing.html) are something that is not implemented in Akka Typed yet. It can be a nice feature to package some typical routers as reusable behaviors with some configuration options. However, we will not implement the call-site routing in the router actor references, since that adds a lot of complexity. It's better to let a router be an ordinary actor.

Let's start with the immutable style. This is how a round-robin router may look like:

```scala
object ImmutableRoundRobin {

  def roundRobinBehavior[T](numberOfWorkers: Int, worker: Behavior[T]): Behavior[T] =
    Actor.deferred { ctx =>
      val workers = (1 to numberOfWorkers).map { n =>
        ctx.spawn(worker, s"worker-$n")
      }
      activeRoutingBehavior(index = 0, workers.toVector)
    }

  private def activeRoutingBehavior[T](index: Long, workers: Vector[ActorRef[T]]): Behavior[T] =
    Actor.immutable[T] { (ctx, msg) =>
      workers((index % workers.size).toInt) ! msg
      activeRoutingBehavior(index + 1, workers)
    }
}
```

([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/typed/javadsl/ImmutableRoundRobin.java))

A number of child actors for the routees, the workers, are created when the router `roundRobinBehavior` is started. We use `Actor.deferred` to spawn these actors via the `ActorContext` parameter. The actor refs for these actors are passed to the initial behavior.

When a message is received the routee for the current index is selected and the message is passed on. The index is increased in the behavior for the next message.

Let us look at how the same thing can be implemented with the mutable style and then we will discuss the trade-offs.

```scala
object MutableRoundRobin {
  def roundRobinBehavior[T](numberOfWorkers: Int, worker: Behavior[T]): Behavior[T] =
    Actor.mutable[T](ctx => new MutableRoundRobin(ctx, numberOfWorkers, worker))
}

class MutableRoundRobin[T](ctx: ActorContext[T], numberOfWorkers: Int, worker: Behavior[T]) extends Actor.MutableBehavior[T] {
  private var index = 0L
  private val workers = (1 to numberOfWorkers).map { n =>
    ctx.spawn(worker, s"worker-$n")
  }

  override def onMessage(msg: T): Behavior[T] = {
    workers((index % workers.size).toInt) ! msg
    index += 1
    this
  }
}
```

([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/typed/javadsl/MutableRoundRobin.java))

We recommend the immutable style because:

* immutable state is easier to reason about
* easier to compose immutable behaviors
* less boilerplate for writing/reading behaviors

If we recommend the immutable style, why did we then provide the alternative mutable style? We found the following reasons:

* lambdas in Java can only close over final or effectively final fields, making it impractical to use this style in behaviors that mutate their fields
* some state is not immutable, e.g. immutable collections are not widely used in Java
* it is more familiar and easier to migrate existing untyped actors to this style
* an encapsulating class can be a more convenient place for constructor parameters and the mutable state than passing it around in immutable function parameters
* mutable state can sometimes have better performance, e.g. mutable collections and avoiding allocating new instance for next behavior

The full source code of these examples, including corresponding Java examples, are available in [patriknw/akka-typed-blog](https://github.com/patriknw/akka-typed-blog).
