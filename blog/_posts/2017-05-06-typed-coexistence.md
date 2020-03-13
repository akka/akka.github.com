---
layout: series_post
title: "Akka Typed: Coexistence"
description: ""
author: Patrik Nordwall
redirect_from: /typed/2017/05/06/typed-coexistence
series_title: Introducing Akka Typed
series_tag: typed
category: article
tags: [actor,typed]
---
{% include JB/setup %}

Note: Code examples in this blog post are **out of date**, see the [Akka documentation](https://doc.akka.io/docs/akka/current/typed/coexisting.html) for latest information on this topic.

We believe Akka Typed will be adopted in existing systems gradually and therefore it's important to be able to use typed and untyped actors together, within the same `ActorSystem`. Also, we will not be able to integrate with all existing modules in one big bang release and that is another reason for why these two ways of writing actors must be able to coexist.

There are two different `ActorSystem` implementations; `akka.actor.ActorSystem` and `akka.typed.ActorSystem`. The latter can currently only host and run typed actors and can therefore only be used for greenfield projects that are only using typed actors. It doesn't have any integration with other Akka modules yet, such as Akka Remoting, Cluster, and Streams. That will of course be implemented. The advantage of the new `ActorSystem` implementation is that it is simplified and also more efficient, e.g. messages are not wrapped in envelope because the sender reference is removed.

`akka.typed.ActorSystem` is interesting, but currently very limited. Good news is that the untyped `akka.actor.ActorSystem` can host and run a mix of untyped and typed actors via an adapter layer. Pretty much everything you would expect works, such as:

* send message from typed to untyped, and opposite
* spawn and supervise typed child from untyped parent, and opposite
* watch typed from untyped, and opposite

First we start an ordinary `ActorSystem` and create an untyped actor:

```scala
val system = akka.actor.ActorSystem("sys")
system.actorOf(MyUntyped1.props(), "first")
```

([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/typed/javadsl/CoexistenceApp1.java))

The untyped actor looks like this:

```scala
import akka.typed.scaladsl.adapter._

object MyUntyped1 {
  def props(): akka.actor.Props = akka.actor.Props(new MyUntyped1)
}

class MyUntyped1 extends akka.actor.Actor {

  // context.spawn is an implicit extension method
  val second: akka.typed.ActorRef[MyTyped1.Command] =
    context.spawn(MyTyped1.behavior, "second")

  // context.watch is an implicit extension method
  context.watch(second)

  // self can be used as the `replyTo` parameter here because
  // there is an implicit conversion from akka.actor.ActorRef to
  // akka.typed.ActorRef
  second ! MyTyped1.Ping(self)

  override def receive = {
    case MyTyped1.Pong =>
      println(s"$self got Pong from ${sender()}")
      // context.stop is an implicit extension method
      context.stop(second)
    case akka.actor.Terminated(ref) =>
      println(s"$self observed termination of $ref")
      context.stop(self)
  }

}
```

([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/typed/javadsl/Coexistence1.java#L15))

It spawns a typed child:

```scala
object MyTyped1 {
  sealed trait Command
  final case class Ping(replyTo: akka.typed.ActorRef[Pong.type]) extends Command
  case object Pong

  val behavior: Behavior[Command] =
    akka.typed.scaladsl.Actor.immutable { (ctx, msg) =>
      msg match {
        case Ping(replyTo) =>
          println(s"${ctx.self} got Ping from $replyTo")
          replyTo ! Pong
          same
      }
    }
}
```

([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/typed/javadsl/Coexistence1.java#L45))

There is one `import` that is needed to make that work:

```scala
import akka.typed.scaladsl.adapter._
```

That adds some implicit extension methods that are added to untyped and typed `ActorSystem` and `ActorContext` in both directions. Note the inline comments in the example above. In the `javadsl` the corresponding adapter methods are static methods in `akka.typed.javadsl.Adapter`.

Let's turn the example upside down and first start the typed actor and then the untyped as a child.

```scala
import akka.typed.scaladsl.adapter._
val system = akka.actor.ActorSystem("sys")
// system.spawn is an implicit extension method
system.spawn(MyTyped2.behavior, "first")
```

([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/typed/javadsl/CoexistenceApp2.java))

The typed actor looks like this:

```scala
import akka.typed.scaladsl.adapter._

object MyTyped2 {
  final case class Ping(replyTo: akka.typed.ActorRef[Pong.type])
  sealed trait Command
  case object Pong extends Command

  val behavior: Behavior[Command] =
    akka.typed.scaladsl.Actor.deferred { context =>
      // context.spawn is an implicit extension method
      val second: akka.actor.ActorRef =
        context.actorOf(MyUntyped2.props(), "second")

      // context.watch is an implicit extension method
      context.watch(second)

      // illustrating how to pass sender, toUntyped is an implicit extension method
      second.tell(MyTyped2.Ping(context.self), context.self.toUntyped)

      akka.typed.scaladsl.Actor.immutable[Command] { (ctx, msg) =>
        msg match {
          case Pong =>
            // it's not possible to get the sender, that must be sent in message
            println(s"${ctx.self} got Pong")
            // context.stop is an implicit extension method
            ctx.stop(second)
            same
        }
      } onSignal {
        case (ctx, akka.typed.Terminated(ref)) =>
          println(s"${ctx.self} observed termination of $ref")
          stopped
      }
    }
}
```

([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/typed/javadsl/Coexistence2.java#L16))

It spawns an untyped child:

```scala
object MyUntyped2 {
  def props(): akka.actor.Props = akka.actor.Props(new MyUntyped2)
}

class MyUntyped2 extends akka.actor.Actor {

  override def receive = {
    case MyTyped2.Ping(replyTo) =>
      // we use the replyTo ActorRef in the message,
      // but could use sender() if needed and it was passed
      // as parameter to tell
      println(s"$self got Pong from ${sender()}")
      replyTo ! MyTyped2.Pong
  }
}
```

([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/typed/javadsl/Coexistence2.java#L61))

There is one caveat regarding supervision of untyped child from typed parent. If the child throws an exception we would expect it to be restarted, but supervision in Akka Typed defaults to stopping the child in case it fails. The restarting facilities in Akka Typed will not work with untyped children. However, the workaround is simply to add another untyped actor that takes care of the supervision, i.e. restarts in case of failure if that is the desired behavior.

In this blog post we have illustrated how you can try out Akka Typed in your existing projects and use it for some actors while not having to migrate everything in one go.

The full source code of these examples, including corresponding Java examples, are available in [patriknw/akka-typed-blog](https://github.com/patriknw/akka-typed-blog).
  
