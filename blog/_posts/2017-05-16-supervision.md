---
layout: series_post
title: "Akka Typed: Supervision"
description: ""
author: Patrik Nordwall
redirect_from: /typed/2017/05/16/supervision
series_title: Introducing Akka Typed
series_tag: typed
category: article
tags: [actor,typed]
---
{% include JB/setup %}

Note: Code examples in this blog post are **out of date**, see the [Akka documentation](https://doc.akka.io/docs/akka/current/typed/fault-tolerance.html) for latest information on this topic.

Supervision semantics in Akka Typed have changed slightly compared to supervision in classic untyped actors. Untyped actors are by default restarted when an exception is thrown. The default for typed actors is instead to stop the failing actor. In this blog post I will show how to install restarting supervision behaviors in Akka Typed.

First we need something to play with, so let's use this dummy `FlakyWorker`:

```scala
object FlakyWorker {
  sealed trait Command
  final case class Job(payload: String) extends Command

  val workerBehavior: Behavior[Command] =
    active(count = 1)

  private def active(count: Int): Behavior[Command] =
    Actor.immutable[Command] { (ctx, msg) =>
      msg match {
        case Job(payload) =>
          if (ThreadLocalRandom.current().nextInt(5) == 0)
            throw new RuntimeException("Bad luck")

          ctx.system.log.info("Worker {} got job {}, count {}", ctx.self, payload, count)
          active(count + 1)
      }
    }
}
```

([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/typed/javadsl/FlakyWorker.java))

If used with ordinary `ctx.spawn` it will stop when the first exception is thrown and will not be able to process any more messages after that.

```scala
import FlakyWorker._
val worker = ctx.spawn(workerBehavior, "worker")
(1 to 20).foreach { n =>
  worker ! Job(n.toString)
}
```

To restart the actor we need to use `Actor.supervise` to wrap the behavior:

```scala
val strategy = SupervisorStrategy.restart
val worker = ctx.spawn(
  Actor.supervise(workerBehavior).onFailure[RuntimeException](strategy),
  "worker")
```

([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/typed/javadsl/FlakyWorkerApp.java))

That will restart the actor for all thrown `RuntimeException` or subclasses thereof.

There are a few other strategies that you recognize from untyped actors, read the API documentation for details.

```scala
val strategy2 = SupervisorStrategy.restart.withLoggingEnabled(false)
val strategy3 = SupervisorStrategy.resume
val strategy4 = SupervisorStrategy.restartWithLimit(maxNrOfRetries = 3, 1.second)
```

([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/typed/javadsl/FlakyWorkerApp.java#L38-L43))

Since a restarer is just a behavior that is wrapping another behavior it can do things that was not possible with supervision in untyped actors, such as restarts with backoff delays. That had to be implemented as another intermediate actor with untyped actors.

```scala
val backoff = SupervisorStrategy.restartWithBackoff(
  minBackoff = 200.millis, maxBackoff = 10.seconds, randomFactor = 0.1)
```

([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/typed/javadsl/FlakyWorkerApp.java#L45-L49))

The `restartWithBackoff` strategy in the above example will restart the actor with the following delays if exceptions occur repeatedly: 

```
0.2 ms, 0.4 s, 0.8 ms, 1.6 s, 3.2 s, 6.4 s, 10 s, 10 s
```

During the backoff delays incoming messages are dropped.

Those delays are also randomized with the `randomFactor`, e.g. the factor `0.1` adds up to `10%` delay. That is useful to avoid that all actors that depends on same (external) failing resource are restarted at the exact same time.

With the `supervisorStrategy` in untyped actors you can decide different actions depending on the type of the exception. It's not often that is needed, but it's still possible with typed actors by nesting several `Actor.supervise` handling different exception types. For example limited restarts of `IllegalStateException` and unlimited for other exceptions:

```scala
import FlakyWorker._
import Actor.supervise
import SupervisorStrategy._
val behv: Behavior[Command] =
  supervise(
    supervise(workerBehavior).onFailure[IllegalStateException](restartWithLimit(3, 1.second)))
    .onFailure[RuntimeException](restart)
```

([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/typed/javadsl/FlakyWorkerApp.java#L53-L60))

In untyped actors there is a strategy called `AllForOneStrategy`, which can restart or stop all siblings when one fails. We don't find that useful and it's not supported in Akka Typed.

One thing we have not mentioned yet is how to let the failure bubble up in the parent actor hierarchy, i.e. corresponding to `escalate` in untyped actors. For that we need to `watch` the child from the parent and `watch` is the topic of next blog post.

The full source code of these examples, including corresponding Java examples, are available in [patriknw/akka-typed-blog](https://github.com/patriknw/akka-typed-blog).

_Edit 2017-05-24: In Akka 2.5.2 `Actor.restarter` was renamed to `Actor.supervise`. This post and the example repository has been updated._
