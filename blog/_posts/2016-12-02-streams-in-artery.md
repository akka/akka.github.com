---
layout: series_post
title: "Streams in Artery"
description: ""
author: Patrik Nordwall
redirect_from: /artery/2016/12/02/streams-in-artery
series_title: Artery
series_tag: artery
category: article
tags: [artery]
---
{% include JB/setup %}

The new remoting implementation for actor messages was released in Akka 2.4.11 two months ago. Artery is the code name for it. It’s a drop-in replacement to the old remoting in many cases, but the implementation is completely new and it comes with many important improvements such as:

* Focused on high-throughput, low-latency communication, mostly allocation-free operation

* Isolation of internal control messages from user messages improving stability and reducing false failure detection in case of heavy traffic by using a dedicated subchannel.

* Support for a separate subchannel for large messages to avoid interference with smaller messages

* Compression of actor paths on the wire to reduce overhead for smaller messages

* Support for faster serialization/deserialization using ByteBuffers directly

* Built-in Flight-Recorder to help debugging implementation issues without polluting users logs with implementation specific events

* Providing protocol stability across major Akka versions to support rolling cross-version updates of large-scale systems

In the [documentation](https://doc.akka.io/docs/akka/2.4/scala/remoting-artery.html) you can find how to use these features, so in the blog we will take a look under the hood and describe how we implemented some of it. In this first post I will show an overview of how we have used Akka Streams in Artery. You don’t need to know any of this to use Akka but you might be curious and learn some things from it.

Naturally, we wanted dogfood our own Akka Streams, but also when looking at it from an objective perspective Akka Streams is a good fit for Artery, and generally speaking any such protocol pipelines.

We are using [Aeron](https://github.com/real-logic/Aeron) as the underlying transport. The Aeron transport is based on UDP but it provides pretty much the same guarantees as TCP when it comes to message order and delivery. It is focused at performance and is more efficient than TCP.

Aeron channels are unidirectional, i.e. you need to bind a listen address (port) on each node and use different channels for sending messages in each direction. In contrast, TCP is bidirectional and you can use the same connection to send responses from the server back to the client that initiated the connection. The unidirectional nature fits nicely with the Akka peer-to-peer communication model. Actors send messages in one direction with fire and forget semantics. Other interaction patterns and delivery guarantees are built on top of these core semantics.

We have embraced this unidirectional aspect in the design of the Akka streams that we use for the remote messaging pipeline. We materialize (run) one Akka stream for each outbound connection. We materialize one Akka stream for inbound messages, i.e. the same inbound stream is used independent of what node that sent the message.

The following diagram illustrates the stages of the outbound and inbound streams for sending and receiving ordinary messages. Actors are located to the left of the green arrows and Aeron and the network to the right of the red arrows.

![Message stream]({{ site.baseurl }}/blog/assets/artery-streams1.png)

The ActorSystem must have a known address for inbound messages. This corresponds to an Aeron UDP channel that is bound to a hostname and port. This binding is created by the `AeronSource` stage in the inbound stream.

We wanted isolation of internal control and system messages from ordinary messages to avoid head of the line blocking for important messages, such as failure detection heartbeat messages. Therefore we have used two separate Aeron sub-channels (stream in Aeron [terminology](https://github.com/real-logic/Aeron/wiki/Protocol-Specification#terminology)) for each channel. One for control and system messages and another for user messages. There is optionally also a third Aeron sub-channel for [large messages](https://doc.akka.io/docs/akka/2.4/scala/remoting-artery.html#Dedicated_subchannel_for_large_messages). For each of these Aeron sub-channels we run a separate Akka stream.

The following diagram shows how the stages are composed for the control streams. Compared to the streams for the ordinary messages (see above) the control streams handle more things, e.g. reliable delivery of system messages. 

![Control stream]({{ site.baseurl }}/blog/assets/artery-streams2.png)

Maybe you have heard that Aeron has reliable delivery of messages, so why do we need something more for system messages? It is true that Aeron will not drop any messages as long as the session is alive, but in the case of long network partitions the session will be broken and no messages will be delivered. That is the same for TCP connections, where the classic remoting also had additional infrastructure to handle these situations. Therefore we implemented acknowledgments, resending and deduplication for system messages. For user messages such semantics can be achieved on the application level by utilising the `AtLeastOnceDelivery` trait.

Please note in the diagrams that some stages are the same in the control streams as in the streams for ordinary messages. This is a very nice aspect of using Akka Streams. Each stage is focused on a single task and can be tested in isolation, and then they can be composed together in different ways. For example the performance cost of system message delivery doesn’t have to be payed for ordinary messages.

There are some places where inbound and outbound streams must interact, e.g. when exchanging system UID in the initial handshake. For such things we have used side-channels, i.e. asynchronous callbacks in the stages. That works fine because these interactions are not performance critical and don’t require back-pressure.

By default these streams are running in fused mode, i.e. there are no asynchronous boundaries between the stages. That is very efficient with low latency overhead. However, you might ask if this sequential processing of the remote messages would be a bottleneck, especially for the shared inbound stream. The message serialization and deserialization are performed in this pipeline and that can absolutely become a bottleneck that would benefit from being executed in parallel. 

This feature is implemented and can be enabled with configuration, but currently we recommend against using it because it requires more hardening and performance optimizations. We will perform these improvements soon. The design is still valid and interesting to describe.

When 2 outbound and 2 inbound lanes are defined it looks like this:

![Lanes]({{ site.baseurl }}/blog/assets/artery-streams3.png)

That will result in an asynchronous boundary before the MergeHub in the outbound stream, i.e. serialization in the Encoder stages can be performed in parallel. Selection of lane is done with consistent hashing of the destination actor reference, i.e. all messages for the same destination actor always go through the same lane. The reason for that is to preserve message ordering. Messages to different actors are allowed to arrive in any order anyway and can therefore take different lanes.

In the inbound stream there will be an asynchronous boundary after the RouteHub, i.e. deserialization can be performed in parallel. The selection of lane is done based on hashing on the destination actor reference in the same way as for the outbound lanes.

The observant reader might notice that `RouteHub` doesn’t actually exist yet. That is true, we have implemented it with a naive `BroadcastHub + filter` in each lane. That is the main reason for why the performance of this feature is currently not where we want it to be in the end.

What about back-pressure? Akka Streams is all about back-pressure but actor messaging is fire-and-forget without any back-pressure. How is that handled in this design?

We can’t magically add back-pressure to actor messaging. That must still be handled on the application level using techniques for message flow control, such as acknowledgments, work-pulling, throttling.

When a message is sent to a remote destination it’s added to a queue that the first stage, called `SendQueue`, is processing. This queue is bounded and if it overflows the messages will be dropped, which is in line with the actor messaging at-most-once delivery nature. Large amount of messages should not be sent without application level flow control. For example, if serialization of messages is slow and can’t keep up with the send rate this queue will overflow.

Aeron will propagate back-pressure from the receiving node to the sending node, i.e. the `AeronSink` in the outbound stream will not progress if the `AeronSource` at the other end is slower and the buffers have been filled up. If messages are sent at a higher rate than what can be consumed by the receiving node the SendQueue will overflow and messages will be dropped. Aeron itself has large buffers to be able to handle bursts of messages.

The same thing will happen in the case of a network partition. When the Aeron buffers are full messages will be dropped by the `SendQueue`.

In the inbound stream the messages are in the end dispatched to the recipient actor. That is an ordinary actor tell that will enqueue the message in the actor’s mailbox. That is where the back-pressure ends on the receiving side. If the actor is slower than the incoming message rate the mailbox will fill up as usual.

Bottom line, flow control for actor messages must be implemented at the application level. Artery does not change that fact.

I hope this blog post gives you a better high-level understanding of how the new remoting is implemented. In next blog post I will describe in more detail how we have used Aeron.


