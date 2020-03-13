---
layout: series_post
title: "Akka Typed: Hello World in the new API"
description: ""
author: Patrik Nordwall
redirect_from: /typed/2017/05/05/typed-intro
category: article
series_title: Introducing Akka Typed
series_tag: typed
tags: [actor,typed]
---
{% include JB/setup %}

Note: Code examples in this blog post are **out of date**, see the [Akka documentation](https://doc.akka.io/docs/akka/current/typed/actors.html) for latest information on this topic.

Lack of type-safety in the Akka actor programming model has been lamented for a long time. Lately, we have focused our work on Akka Typed in an attempt to bring type-safety to the world of actors. We are excited to share our recent progress on the new typed core APIs.

In a series of blog posts we will show the new API and give a gentle introduction to Akka Typed for those of you that are already familiar with classic untyped actors. We will specifically look at commonalities and differences between typed and untyped actors.

When we say that classic actors are untyped we mean that the type `ActorRef` does not convey any information of what types of messages that can be sent via that `ActorRef`, and what type the destination `Actor` has. This can make the programs difficult to read and prone to programming mistakes. Personally I'm most concerned about the former shortcoming, I think the readablity/browsability aspect when navigating an unknown Akka code base can be problematic. Akka Typed adds that type parameter to the `ActorRef`:

```scala
val greeter: ActorRef[Greeter.Command] = 
  ctx.spawn(Greeter.greeterBehavior, "greeter") 
```

## Current Status

First, what is the current status of Akka Typed? We have focused on the core APIs for actors and we are rather happy with the results. API adjustments are still possible if you have ideas of what can be made better. It can be used for ordinary local actors, including things like:

* tell, ask
* spawning child actors, supervision and watch
* coexistence of typed and untyped actors in same `ActorSystem` 

This is what we know of that are currently missing or incomplete:

* remote messaging
* receptionist pattern (a typed replacement for `actorSelection`)
* persistence
* integration with other modules, such as Streams, Cluster, Distributed Data
* the [process DSL](https://github.com/rkuhn/akka-typed-process-demo)
* logging API
* performance optimizations

Some of the above limitations can probably be worked around by using the integration with untyped actors, but these things will of course be implemented. It's our goal to make Akka Typed a full-featured replacement of the existing untyped actors. However, untyped actors will not go away any time soon, so typed and untyped actors will be able to coexist.

Issues related to Akka Typed are marked with label [t:typed](https://github.com/akka/akka/issues?q=is%3Aopen+is%3Aissue+label%3At%3Atyped). Please don't hesitate to chime in and help is very welcome.

## Hello World

The examples in these blog posts are shown in Scala but corresponding examples in Java are available in the repository [patriknw/akka-typed-blog](https://github.com/patriknw/akka-typed-blog).

Let's start with a plain classic Hello World actor and then look at how that can be implemented with Akka Typed.

```scala
import akka.actor.Actor
import akka.actor.ActorRef
import akka.actor.Props

object Greeter1 {
  case object Greet
  final case class WhoToGreet(who: String)
}

class Greeter1 extends Actor {
  import Greeter1._

  private var greeting = "hello"

  override def receive = {
    case WhoToGreet(who) =>
      greeting = s"hello, $who"
    case Greet =>
      println(greeting)
  }
}
```

([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/classic/javadsl/Greeter1.java))

As you can see, the actor above keeps mutable state in `var greeting` that can be changed by sending the `WhoToGreet` message to the actor. It will print the greeting when it receives the message `Greet`.

Corresponding actor implemented with Akka Typed:

```scala
import akka.typed.Behavior
import akka.typed.scaladsl.Actor

object Greeter1 {
  sealed trait Command
  case object Greet extends Command
  final case class WhoToGreet(who: String) extends Command

  val greeterBehavior: Behavior[Command] =
    Actor.mutable[Command](ctx => new Greeter1)
}

class Greeter1 extends Actor.MutableBehavior[Greeter1.Command] {
  import Greeter1._

  private var greeting = "hello"

  override def onMessage(msg: Command): Behavior[Command] = {
    msg match {
      case WhoToGreet(who) =>
        greeting = s"hello, $who"
      case Greet =>
        println(greeting)
    }
    this
  }
}
```

([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/typed/javadsl/Greeter1.java))

It looks very similar and there are only a few small differences so far:

* It defines the type of messages that the actor can handle with the type parameter `Greeter1.Command`.
* `onMessage` is an abstract method in `MutableBehavior` that is invoked for each received message.
* `onMessage` returns the `Behavior` that will be used for next message. This example doesn't change behavior so it returns `this`, but we will look more at that later. Returning the next behavior is the way to `become` with typed actors.

Actors are almost always stateful and in the above two examples the state is kept in a mutable variable. A more idiomatic way to handle such state is to keep it in an immutable field and change the behavior instead. With untyped actors we use `become` for this:

```scala
object Greeter2 {

  case object Greet
  final case class WhoToGreet(who: String)
}

class Greeter2 extends Actor {
  import Greeter2._

  override def receive = onMessage(currentGreeting = "hello")

  def onMessage(currentGreeting: String): Receive = {
    case WhoToGreet(who) =>
      context.become(onMessage(currentGreeting = s"hello, $who"))
    case Greet =>
      println(currentGreeting)
  }
}
```

([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/classic/javadsl/Greeter2.java))

For typed actors we promote this style even more and it becomes very natural since processing of each message returns the behavior to be used for the next message. State is updated by returning a new behavior that holds the new immutable state.

```scala
object Greeter2 {
  sealed trait Command
  case object Greet extends Command
  final case class WhoToGreet(who: String) extends Command

  val greeterBehavior: Behavior[Command] = greeterBehavior(currentGreeting = "hello")

  private def greeterBehavior(currentGreeting: String): Behavior[Command] =
    Actor.immutable[Command] { (ctx, msg) =>
      msg match {
        case WhoToGreet(who) =>
          greeterBehavior(s"hello, $who")
        case Greet =>
          println(currentGreeting)
          Actor.same
      }
    }
}
```

([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/typed/javadsl/Greeter2.java))

This style is called `Actor.immutable` in the API, as opposed to the `Actor.mutable` that was shown first. We recommend the immutable style as the “default choice” but we will elaborate more around the mutable vs. immutable behavior in a separate blog post.

Note that with the immutable style there is no enclosing class for the actor. The actor is essentially defined as a function from message to next behavior (`T => Behavior[T]`). To keep the same behavior you return `Actor.same`.

The parameter to `Actor.immutable` is a total function, i.e. you have to handle all incoming message types. Therefore it's recommended that the root message type for the actor is defined as a `sealed` trait so that the compiler will warn if a message type is not handled in a pattern match. In contrast, the `receive` function in untyped actors is a partial function and unmatched messages are treated as unhandled. Typed actors can return `Actor.unhandled` to indicate that a message can't be handled, e.g. because it's invalid in current state.

You might be curious of the other parameter named `ctx`. It is the `ActorContext`, which you need for things like spawning child actors and watching actors.

To make the example complete we should also show how to start the actor system and the actor. With Akka Typed there can only be one top level `"user"` actor that is defined when starting the actor system. It looks like this:

```scala
val root = Actor.deferred[Nothing] { ctx =>
  import Greeter2._
  val greeter: ActorRef[Command] = ctx.spawn(greeterBehavior, "greeter")
  greeter ! WhoToGreet("World")
  greeter ! Greet

  Actor.empty
}
val system = ActorSystem[Nothing]("HelloWorld", root)
```

([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/typed/javadsl/HelloWorldApp2.java))

`Actor.deferred` is like a factory for a behavior. Creation of the behavior instance is deferred until the actor is started, as opposed to `Actor.immutable` that creates the behavior instance immediately before the actor is running. The factory function in `deferred` pass the `ActorContext` as parameter and that can for example be used for spawning child actors. Here we spawn the greeter actor and send some messages to it. Apart from that initialization the behavior of the `root` actor doesn't do anything in this example, i.e. it is `Actor.empty`. 

Note that the actor reference is typed, `ActorRef[Command]` so only messages implementing `Greeter2.Command` such as `WhoToGreet` and `Greet` can be sent via that `ActorRef`. Being able to use accurately typed actor references everywhere is the main goal of Akka Typed.

The full source code of these examples, including corresponding Java examples, are available in [patriknw/akka-typed-blog](https://github.com/patriknw/akka-typed-blog).