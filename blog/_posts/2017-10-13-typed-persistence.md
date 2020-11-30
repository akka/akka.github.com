---
layout: series_post
title: "Akka Typed: New Persistence API"
description: ""
category: article
author: Patrik Nordwall
series_title: Akka Typed Cluster and Persistence
series_tag: typed-cluster
tags: [actor,typed,typed-cluster]
---
{% include JB/setup %}

Note: Code examples in this blog post are **out of date**, see the [Akka documentation](https://doc.akka.io/docs/akka/current/typed/index-persistence.html) for latest information on this topic.

How to use Cluster Sharding with Akka Typed was shown in [previous post](https://akka.io/blog/2017/10/04/typed-cluster-tools).
Sharding is typically used with persistent actors so in this post we will introduce the new APIs for typed persistent actors.

These features are still using the existing implementations from the untyped modules and they require that you run with the untyped ActorSystem as described in [Akka Typed: Coexistence](https://akka.io/blog/2017/05/06/typed-coexistence).

Akka Persistence enables stateful actors to persist their internal state so that it can be recovered when an actor is started, restarted after a JVM crash or by a supervisor, or migrated in a cluster. The key concept behind Akka persistence is that only changes to an actor’s internal state are persisted but never its current state directly (except for optional snapshots). Such stateful actors are recovered by replaying stored changes to these actors from which they can rebuild internal state.

This design of capturing all changes as domain events, which are immutable facts of things that have happened, is known as [event sourcing](https://docs.microsoft.com/en-us/previous-versions/msp-n-p/jj591559(v=pandp.10)).

## Stub

This is what a typed `PersistentActor` (`PersistentBehavior`) looks like before filling in the implementation details:

```scala
import akka.typed.persistence.scaladsl.PersistentActor
import akka.typed.Behavior

object BlogPost1 {

  def behavior: Behavior[BlogCommand] =
    PersistentActor.immutable[BlogCommand, BlogEvent, BlogState](
      persistenceId = "abc",
      initialState = BlogState.empty,
      commandHandler = PersistentActor.CommandHandler { (ctx, state, cmd) ⇒ ??? },
      eventHandler = (state, evt) ⇒ ???)

}
```

There are 3 type parameters:

* `Command` - the super class/interface of the commands
* `Event` - the super class/interface of the events
* `State` - the class of the state

and 4 parameters:

`persistenceId` is the unique identifier for the persistent actor.

`initialState` defines the `State` when the entity is first created.

`commandHandler` defines how to handle command and optional functions for other signals, e.g. `Termination` messages if `watch` is used.

`eventHandler` updates the current state when an event has been persisted.

## Command Handlers 

The commands for this example:

```scala
sealed trait BlogCommand extends Serializable

final case class AddPost(content: PostContent, replyTo: ActorRef[AddPostDone]) extends BlogCommand

final case class AddPostDone(postId: String)

final case class GetPost(replyTo: ActorRef[PostContent]) extends BlogCommand

final case class ChangeBody(newBody: String, replyTo: ActorRef[Done]) extends BlogCommand

final case class Publish(replyTo: ActorRef[Done]) extends BlogCommand

final case object PassivatePost extends BlogCommand
```

The function that process incoming commands is defined by the mandatory `commandHandler` parameter of the `PersistentActor`.

```scala
  private val commandHandler: CommandHandler[BlogCommand, BlogEvent, BlogState] =
    CommandHandler { (_, state, cmd) ⇒
      cmd match {
        case AddPost(content, replyTo) ⇒
          val evt = PostAdded(content.postId, content)
          Effect.persist(evt).andThen { state2 ⇒
            // After persist is done additional side effects can be performed
            replyTo ! AddPostDone(content.postId)
          }
        case ChangeBody(newBody, replyTo) ⇒
          val evt = BodyChanged(state.postId, newBody)
          Effect.persist(evt).andThen { _ ⇒
            replyTo ! Done
          }
        case Publish(replyTo) ⇒
          Effect.persist(Published(state.postId)).andThen { blog ⇒
            replyTo ! Done
          }
        case GetPost(replyTo) ⇒
          replyTo ! state.content.get
          Effect.none
        case PassivatePost =>
          Effect.stop
      }
    }
```

The command handler is a function with 3 parameters for the `ActorContext`, current `State`, and `Command`.

A command handler returns an `Effect` directive that defines what event or events, if any, to persist.

* `Effect.persist` will persist one single event or several events atomically, i.e. all events
  are stored or none of them are stored if there is an error
* `Effect.none` no events are to be persisted, for example a read-only command
* `Effect.unhandled` the command is unhandled (not supported) in current state

External side effects can be performed after successful persist with the `andThen` function. In the above example a reply is sent to the `replyTo`. Note that the new state after applying the event is passed as parameter to the `andThen` function.

## Event Handlers

The events for this example:

```scala
sealed trait BlogEvent extends Serializable

final case class PostAdded(
  postId: String,
  content: PostContent) extends BlogEvent

final case class BodyChanged(
  postId: String,
  newBody: String) extends BlogEvent

final case class Published(postId: String) extends BlogEvent
```

When an event has been persisted successfully the current state is updated by applying the event to the current state with the `eventHandler` function. The event handler returns the new state, which must be immutable so you return a new instance of the state. The same event handler is also used when the entity is started up to recover its state from the stored events.

```scala
  private def eventHandler(state: BlogState, event: BlogEvent): BlogState =
    event match {
      case PostAdded(postId, content) ⇒
        state.withContent(content)

      case BodyChanged(_, newBody) ⇒
        state.content match {
          case Some(c) ⇒ state.copy(content = Some(c.copy(body = newBody)))
          case None    ⇒ state
        }

      case Published(_) ⇒
        state.copy(published = true)
    }
```

## State

The state for this example without further ado:

```scala

object BlogState {
  val empty = BlogState(None, published = false)
}

final case class BlogState(
  content: Option[PostContent],
  published: Boolean) {

  def withContent(newContent: PostContent): BlogState =
    copy(content = Some(newContent))

  def isEmpty: Boolean = content.isEmpty

  def postId: String = content match {
    case Some(c) => c.postId
    case None    => throw new IllegalStateException("postId unknown before post is created")
  }
}

final case class PostContent(postId: String, title: String, body: String)
```

## Changing Behavior

After processing a message an ordinary, non-persistent, typed actor returns the `Behavior` that is used for next message. As you can see in the above examples that is not supported by typed persistent actors. Instead, the state is returned by `eventHandler`. The reason a new behavior can't be returned is that behavior is part of the actor's state and must also carefully be reconstructed during recovery. If it would have been supported it would mean that the behavior must be restored when replaying events and also encoded in the state anyway when snapshots are used. That would be very prone to mistakes.

For simple actors you can use the same set of command handlers independent of what state the entity is in, as shown in above example.

For more complex actors it's useful to be able to change the behavior in the sense that different functions for processing commands may be defined depending on what state the actor is in. This is useful when implementing finite state machine (FSM) like entities. The `CommandHandler` can be selected based on current state by using the `CommandHandler.byState` factory method. It is a function from current `State` to `CommandHandler`, which is called for each incoming command to select which `CommandHandler` to use to process the command.

This is how to define different behavior for different `State`:

```scala
  private def commandHandler: CommandHandler[BlogCommand, BlogEvent, BlogState] = CommandHandler.byState {
    case state if state.isEmpty  ⇒ initial
    case state if !state.isEmpty ⇒ postAdded
  }

  private def initial: CommandHandler[BlogCommand, BlogEvent, BlogState] =
    CommandHandler { (ctx, state, cmd) ⇒
      cmd match {
        case AddPost(content, replyTo) ⇒
          val evt = PostAdded(content.postId, content)
          Effect.persist(evt).andThen { state2 ⇒
            // After persist is done additional side effects can be performed
            replyTo ! AddPostDone(content.postId)
          }
        case PassivatePost =>
          Effect.stop
        case other ⇒
          Effect.unhandled
      }
    }

  private def postAdded: CommandHandler[BlogCommand, BlogEvent, BlogState] = {
    CommandHandler { (ctx, state, cmd) ⇒
      cmd match {
        case ChangeBody(newBody, replyTo) ⇒
          val evt = BodyChanged(state.postId, newBody)
          Effect.persist(evt).andThen { _ ⇒
            replyTo ! Done
          }
        case Publish(replyTo) ⇒
          Effect.persist(Published(state.postId)).andThen { _ ⇒
            println(s"Blog post ${state.postId} was published")
            replyTo ! Done
          }
        case GetPost(replyTo) ⇒
          replyTo ! state.content.get
          Effect.none
        case _: AddPost ⇒
          Effect.unhandled
        case PassivatePost =>
          Effect.stop
      }
    }
  }
```

The event handler is always the same independent of state. The main reason for not making the event handler part of the `CommandHandler` is that all events must be handled and that is typically independent of what the current state is. The event handler can of course still decide what to do based on the state if that is needed.

## Serialization

The same [serialization](https://doc.akka.io/docs/akka/current/scala/serialization.html) mechanism as for untyped actors is also used in Akka Typed, also for persistent actors. When picking serialization solution for the events you should also consider that it must be possible read old events when the application has evolved. Strategies for that can be found in the [Akka documentation](https://doc.akka.io/docs/akka/current/scala/persistence-schema-evolution.html).

The example code that comes with this blog post includes Protobuf serialization for the commands, events and state of the `BlogPost` example, see [BlogSerializer](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/scala/blog/typed/persistence/scaladsl/BlogSerializer.scala).

## Running

`PersistentActor.immutable` that was described in the beginning returns a `PersistentBehavior` that is a subclass of `Behavior` and that means that you can start the actor with the ordinary `spawn` method of the `ActorContext`.

You also have to configure an Akka Persistence journal. The full [example code](https://github.com/patriknw/akka-typed-blog) includes such configuration for [akka-persistence-cassandra](https://github.com/akka/akka-persistence-cassandra).

[Previous post](https://akka.io/blog/2017/10/04/typed-cluster-tools) describes how to use Cluster Sharding. There is one thing to be aware of. When used with Cluster Sharding the `persistenceId` is not known until the actor is started and typically based on the `entityId`, which is the actor name. Therefore, with sharding `PersistentActor.persistentEntity` must be used instead of `PersistentActor.immutable. It takes a function to create the `persistenceId`.

```scala
import akka.typed.cluster.sharding.EntityTypeKey

object BlogPost {

  val ShardingTypeName = EntityTypeKey[BlogCommand]("BlogPost")
  
  def shardingBehavior: Behavior[BlogCommand] =
    PersistentActor.persistentEntity[BlogCommand, BlogEvent, BlogState](
      persistenceIdFromActorName = name => ShardingTypeName.name + "-" + name,
      initialState = BlogState.empty,
      commandHandler = PersistentActor.CommandHandler { (ctx, state, cmd) ⇒ ??? },
      eventHandler = (state, evt) ⇒ ???)

}
```

This also highlights a limitation with the current implementation. The `PersistentBehavior` can't be wrapped in other behaviors, such as `Actor.deferred`. The reason for that is that we are running the `PersistentBehavior` with an untyped `PersistentActor` and that doesn't allow such wrapping. We intend to re-implement this with a pure typed actor and then wrapping will be possible. The untyped journals, e.g. [akka-persistence-cassandra](https://github.com/akka/akka-persistence-cassandra), will still be possible to use as is. When this limitation is removed the above `PersistentActor.persistentEntity` will not be needed, because the `persistenceId` can be created from the actor name inside `deferred`.

## Feedback wanted

The full source code of these examples, are available in [patriknw/akka-typed-blog](https://github.com/patriknw/akka-typed-blog).

By this we are ending this series of blog posts about Akka Typed APIs for cluster and persistence. We expect a few more iterations to improve the APIs, so your feedback would be very valuable. We are currently working on other things but we will get back to Akka Typed soon and then we will focus on things like:

* missing Java API, e.g. for Persistence
* testing, testkit 
* documentation
* general improvements of quality
* and all [those small things](https://github.com/akka/akka/issues?q=is%3Aopen+is%3Aissue+label%3At%3Atyped)

Help is of course welcome.
