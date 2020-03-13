---
layout: series_post
title: "Aeron in Artery"
description: ""
author: Patrik Nordwall
redirect_from: /artery/2016/12/05/aeron-in-artery
category: article
series_title: Artery
series_tag: artery
tags: [artery]
---
{% include JB/setup %}

We are using [Aeron](https://github.com/real-logic/Aeron) as the underlying transport in the new remoting implementation for Actor messages. The Aeron transport is based on UDP but it provides pretty much the same guarantees as TCP when it comes to message order and delivery. It is focused at performance and is more efficient than TCP.

Artery is designed from the ground up to support high-throughput in the magnitude of 1 million messages per second and low-latency in the magnitude of 100 microseconds.

Somewhat outdated by now, but this is what we measured in July between two m4.4xlarge EC2 instances (1 Gbit/s bandwidth) using the[ MaxThroughputSpec](https://github.com/akka/akka/blob/master/akka-remote-tests/src/multi-jvm/scala/akka/remote/artery/MaxThroughputSpec.scala) and[ LatencySpec](https://github.com/akka/akka/blob/master/akka-remote-tests/src/multi-jvm/scala/akka/remote/artery/LatencySpec.scala):

* 630,239 messages/s with message payload of 100 bytes
* 8,245 messages/s with messages payload of 10,000 bytes
* Round trip latency at a message rate of 10,000 messages/s: 50%ile: 155 µs, 90%ile: 173 µs, 99%ile: 196 µs

The Aeron API is based on busy spinning, i.e. a message offer or poll has to be retried until it is successful. An `offer` may not be accepted if the send log buffers are full, which may be caused by the receiver being slower and thereby applying backpressure. A poll must be retried until there is a message to be be received.

This is how the Aeron API can be used in a sink `GraphStage` to send a message:

```scala
      private val pub = aeron.addPublication(channel, streamId)
      override def onPush(): Unit = {
        publish(grab(in), 100)
      }

      @tailrec private def publish(envelope: EnvelopeBuffer,
          attemptsLeft: Int): Unit = {

        val msgSize = envelopeInFlight.byteBuffer.limit
        val result = pub.offer(envelope.aeronBuffer, 0, msgSize)
        if (result < 0)
          if (attemptsLeft > 0)
            publish(envelope, attemptsLeft - 1) // try again
          else
            failStage(new GaveUpMessageException)
        else
          pull(in)
      }
```

That is a simplified version of the [AeronSink](https://github.com/akka/akka/blob/master/akka-remote/src/main/scala/akka/remote/artery/AeronSink.scala) that we have in Artery. The real [AeronSink](https://github.com/akka/akka/blob/master/akka-remote/src/main/scala/akka/remote/artery/AeronSink.scala) and [AeronSource](https://github.com/akka/akka/blob/master/akka-remote/src/main/scala/akka/remote/artery/AeronSource.scala) stages also perform such retries of `offer` and `poll` directly in the stages, but we don’t want to spin for too long there because that would make the stage unresponsive to other things and it would result in too many threads performing busy spinning. Instead, after a few attempts inside the stage we delegate the retry tasks to one shared thread, called the [TaskRunner](https://github.com/akka/akka/blob/master/akka-remote/src/main/scala/akka/remote/artery/TaskRunner.scala). When an offer or poll task is successful the TaskRunner wakes up the stage again with an async callback.

To not consume too much CPU there is a backoff strategy in the busy spinning loop. After a number of immediate invocations the retry frequency is reduced by first yielding the thread a number of times and finally parking the thread with exponentially increasing delay until the maximum delay is reached.

Longer parking result in less CPU usage, but it also means that it takes longer time to notice that a new message has been received when the system is mostly idle. This tradeoff is [tunable](https://doc.akka.io/docs/akka/2.4/scala/remoting-artery.html#Fine-tuning_CPU_usage_latency_tradeoff). It is worth noting that if you perform latency testing at a low throughput rate (e.g. 1000 msg/s) you will see much worse results than when the system is more busy (e.g. 10000 msg/s). This is because the `TaskRunner` and Aeron media driver threads go into parking mode.

When sending messages that are larger than the UDP MTU size Aeron will fragment them into smaller data frames. On the receiving end we use the Aeron `FragmentAssembler` that takes care of reassembling the fragments into full message frames again. One thing that is worth noting is that the poll method of the Aeron `Subscriber` takes a `fragmentLimit` parameter. Since the AeronSource stage handles one message at a time we must use poll with `fragmentLimit` of 1. Otherwise we would have to introduce a buffer in the stage.

Aeron has support for unicast (point-to-point sender and receiver) and multicast (multiple senders, multiple receivers). We are only using unicast in Artery for the actor message communication. I can imagine that the multicast support can be interesting to explore in the future for things like the cluster gossip protocol.

Our experience of using Aeron has been very good. We have had very few problems, and when we had questions or found bugs Martin and Todd have been very responsive. Thank you! The most difficult part was to get the shutdown sequence right, including removal of the files for the embedded media driver. If some thread tried to use Aeron after it had been closed it would cause JVM segmentation faults. This problem is not specific for Aeron but something that must be carefully managed when working with memory mapped files.

We are looking forward to harden and optimize Artery even more and eventually make it the default transport for actor messages. Your help is welcome!

