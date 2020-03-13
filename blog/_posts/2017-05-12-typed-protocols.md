---
layout: series_post
title: "Akka Typed: Protocols"
description: ""
author: Roland Kuhn & Patrik Nordwall 
redirect_from: /typed/2017/05/12/typed-protocols
series_title: Introducing Akka Typed
series_tag: typed
category: article
tags: [actor,typed]
---
{% include JB/setup %}

Note: Code examples in this blog post are **out of date**, see the [Akka documentation](https://doc.akka.io/docs/akka/current/typed/actors.html#a-more-complex-example) for latest information on this topic.

Message protocols are an essential part of designing actor based systems. The interaction between actors are often stateful and messages are supposed to be sent in a certain order, e.g. establishing a session, replying to a certain request, or acknowledging reception. In this blog post we will look at how the typed actor references can be used to model such messaging protocols.

Consider an Actor that runs a chat room: client Actors may connect by sending a message that contains their screen name and then they can post messages. The chat room Actor will disseminate all posted messages to all currently connected client Actors. The protocol definition could look like the following:

```scala
object ChatRoom {
  sealed trait Command
  final case class GetSession(screenName: String, replyTo: ActorRef[SessionEvent])
    extends Command
  private final case class PostSessionMessage(screenName: String, message: String)
    extends Command

  sealed trait SessionEvent
  final case class SessionGranted(handle: ActorRef[PostMessage]) extends SessionEvent
  final case class SessionDenied(reason: String) extends SessionEvent
  final case class MessagePosted(screenName: String, message: String) extends SessionEvent

  final case class PostMessage(message: String)
}
```

([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/typed/javadsl/ChatRoom.java))

Initially the client Actors only get access to an `ActorRef[GetSession]` which allows them to make the first step. Once a client’s session has been established it gets a `SessionGranted` message that contains a `handle` to unlock the next protocol step, posting messages. The `PostMessage` command will need to be sent to this particular address that represents the session that has been added to the chat room. The other aspect of a session is that the client has revealed its own address, via the `replyTo` argument, so that subsequent `MessagePosted` events can be sent to it.

This illustrates how Actors can express more than just the equivalent of method calls on Java objects. The declared message types and their contents describe a full protocol that can involve multiple Actors and that can evolve over multiple steps. The implementation of the chat room protocol would be as simple as the following:

```scala
  val behavior: Behavior[Command] =
    chatRoom(List.empty)

  private def chatRoom(sessions: List[ActorRef[SessionEvent]]): Behavior[Command] =
    Actor.immutable[Command] { (ctx, msg) ⇒
      msg match {
        case GetSession(screenName, client) ⇒
          val wrapper = ctx.spawnAdapter {
            p: PostMessage ⇒ PostSessionMessage(screenName, p.message)
          }
          client ! SessionGranted(wrapper)
          chatRoom(client :: sessions)
        case PostSessionMessage(screenName, message) ⇒
          val mp = MessagePosted(screenName, message)
          sessions foreach (_ ! mp)
          Actor.same
      }
    }
```

([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/typed/javadsl/ChatRoom.java))

When a new `GetSession` command comes in we add that client to the list that is in the returned behavior. Then we also need to create the session’s `ActorRef` that will be used to post messages. In this case we want to create a very simple Actor that just repackages the `PostMessage` command into a `PostSessionMessage` command which also includes the screen name. Such a wrapper Actor can be created by using the `spawnAdapter` method on the `ActorContext`, so that we can then go on to reply to the client with the `SessionGranted` result.

The behavior that we declare here can handle both subtypes of `Command`. `GetSession` has been explained already and the `PostSessionMessage` commands coming from the wrapper Actors will trigger the dissemination of the contained chat room message to all connected clients. But we do not want to give the ability to send `PostSessionMessage` commands to arbitrary clients, we reserve that right to the wrappers we create—otherwise clients could pose as completely different screen names (imagine the `GetSession` protocol to include authentication information to further secure this). Therefore `PostSessionMessage` has ``private`` visibility and can't be created outside the actor.

Another strategy for exposing only part of the messages is to `narrow` the `Behavior` or the `ActorRef` that is exposed to the outside to a type that represents the public messages.

`spawnAdapter` is useful when translating messages from one protocol to another. If the chat room included interactions with some other backend service that defined its own set of messages we could transform the reply from for example an authentication request to a type that the chat room can understand, i.e. a type extending `ChatRoom.Command`.

In order to see this chat room in action we need to write a client Actor that can use it:

```scala
  val gabbler =
    Actor.immutable[SessionEvent] { (_, msg) ⇒
      msg match {
        case SessionDenied(reason) ⇒
          println(s"cannot start chat room session: $reason")
          Actor.stopped
        case SessionGranted(handle) ⇒
          handle ! PostMessage("Hello World!")
          Actor.same
        case MessagePosted(screenName, message) ⇒
          println(s"message has been posted by '$screenName': $message")
          Actor.stopped
      }
    }
```

([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/typed/javadsl/Gabbler.java))

From this behavior we can create an Actor that will accept a chat room session, post a message, wait to see it published, and then terminate.

Now to try things out we must start both a chat room and a gabbler and we do that from the guardian supervisor:

```scala
  val root: Behavior[akka.NotUsed] =
    Actor.deferred { ctx ⇒
      val chatRoom = ctx.spawn(ChatRoom.behavior, "chatroom")
      val gabblerRef = ctx.spawn(gabbler, "gabbler")
      chatRoom ! GetSession("ol’ Gabbler", gabblerRef)

      Actor.empty
    }

  val system = ActorSystem("ChatRoomDemo", root)
```
([Same example in Java](https://github.com/patriknw/akka-typed-blog/blob/master/src/main/java/blog/typed/javadsl/ChatRoomApp.java))

As illustrated in this example the typed `ActorRef` is a great tool for describing message protocols. Actor references with different types can be exchanged in the messages to describe the next type of messages that can be sent in an interaction.

We are currently researching possibilities for deriving the message type definitions for a protocol from other sources, like a formal specification of [session types](http://groups.inf.ed.ac.uk/abcd/). First steps towards reusable and composable process steps for the implementing actors can be found at [akka-typed-session](https://github.com/rkuhn/akka-typed-session).

The full source code of these examples, including corresponding Java examples, are available in [patriknw/akka-typed-blog](https://github.com/patriknw/akka-typed-blog).

