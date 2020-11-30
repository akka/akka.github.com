---
layout: series_post
title: "Flow control at the boundary of Akka Streams and a data provider"
description: ""
author: Martynas Mickevičius
redirect_from: /integrations/2016/09/05/flow-control-at-the-akka-stream-boundary
series_title: Integration
series_tag: integration
category: article
tags: [streams,connectors,integration]
---
{% include JB/setup %}

When working with Akka Streams, one can be assured that all of the data is going to be processed in bounded memory. The reason Akka Streams can guarantee this, is that it implements the [Reactive Streams](https://www.reactive-streams.org/) protocol which describes how flow control is to be managed using demand signaling. Having a single protocol for flow control enables different libraries, that follow the same protocol, to interconnect seamlessly. However, when writing Akka Streams connectors for libraries that do not implement Reactive Streams, one needs to look at the library API and decide what is the best way to control the flow at the boundary between Akka Streams and the library.

Below are three strategies of doing that. Choosing one over another relies on what APIs the library provides and what guarantees the API of the library hold.

*Code examples below are in Scala as [AMQP](https://github.com/akka/alpakka/tree/master/amqp) and [MQTT](https://github.com/akka/alpakka/tree/master/mqtt) connectors in [Alpakka](https://github.com/akka/alpakka) project are written in Scala. However the GraphStage API is fairly similar for Java so it should be easy to follow even if you do not know Scala.*

## Requesting number of elements

A straightforward approach is to signal explicitly how many elements an Akka Streams connector is able to receive. When a connector takes this approach, an internal buffer is needed which is going to store this limited number of elements requested from the library.

This approach was taken when writing the [AMQP connector](https://github.com/akka/alpakka/tree/master/amqp) using the [RabbitMQ client library](https://www.rabbitmq.com/java-client.html). While the library does not have an explicit way of signaling how many elements are to be expected, it allows setting an [unacknowledged message limit](https://www.rabbitmq.com/consumer-prefetch.html).

```scala
channel.basicQos(bufferSize, true)
```
[complete sources](https://github.com/akka/alpakka/blob/c1315a8d1979b4399b62db726c159d64149501f7/amqp/src/main/scala/akka/stream/alpakka/amqp/AmqpSourceStage.scala#L47)

The unacknowledged messages limit combined with acknowledging messages that are sent downstream, ensures that there is not going to be more than a given number of messages in flight between the library and the connector.

```scala
setHandler(out, new OutHandler {
  override def onPull(): Unit = {
    if (queue.nonEmpty) {
      pushAndAckMessage(queue.dequeue())
    }
  }
})

def pushAndAckMessage(message: IncomingMessage): Unit = {
  push(out, message) // send message downstream
  // ack it as soon as we have passed it downstream
  channel.basicAck(message.envelope.getDeliveryTag, false)
}
```
[complete sources](https://github.com/akka/alpakka/blob/c1315a8d1979b4399b62db726c159d64149501f7/amqp/src/main/scala/akka/stream/alpakka/amqp/AmqpSourceStage.scala#L112)

Here messages are acknowledged one-by-one as they are sent downstream. A more performant alternative would be to acknowledge messages in batches. A tradeoff of this approach would be that on a stream failure a maximum of acknowledgement batch size messages could remain unacknowledged, even if they were already taken from the queue.

## Blocking the library thread

When a library provides a way to subscribe for data via callback execution, it might be the case that it guarantees that callbacks are executed sequentially and the library relies on the callee to block its thread to signal backpressure. This allows us to block the calling thread and in such way to signal the library that the flow of the elements should be paused for some time.

After consulting the documentation of the external library and making sure that it expects its thread to be blocked, we took this strategy when implementing the Akka Streams connector for [MQTT](https://github.com/akka/alpakka/tree/master/mqtt) using the [PAHO client library](https://eclipse.org/paho/clients/java/). The documentation of the [`messageArrived`](https://www.eclipse.org/paho/files/javadoc/org/eclipse/paho/client/mqttv3/MqttCallback.html#messageArrived-java.lang.String-org.eclipse.paho.client.mqttv3.MqttMessage-) method, that is called when new message arrives, states the following:

> Any additional messages which arrive while an implementation of this method is running, will build up in memory, and will then back up on the network.

We will create two hooks in the implementation of the `messageArrived` callback. `beforeHandleMessage` is going to be executed on the library thread, and `handleMessage` is going to be called in the context of `GraphStage` as an [`AsyncCallback`](https://doc.akka.io/api/akka/2.4/#akka.stream.stage.AsyncCallback):

```scala
def beforeHandleMessage(): Unit
val onMessage = getAsyncCallback[MqttMessage](handleMessage)

final override def preStart(): Unit = {
  ...

  mqttClient.setCallback(new MqttCallback {
    def messageArrived(topic: String, message: PahoMqttMessage) = {
      beforeHandleMessage()
      onMessage.invoke(MqttMessage(topic, ByteString(message.getPayload)))
    }

    ...
  }
}
```
[complete sources](https://github.com/akka/alpakka/blob/c1315a8d1979b4399b62db726c159d64149501f7/mqtt/src/main/scala/akka/stream/alpakka/mqtt/Mqtt.scala#L112)

This allows us to block in the implementation of `beforeHandleMessage` and protects us against uncontrolled message overflow.

```scala
override def beforeHandleMessage(): Unit = {
  backpressure.acquire()
}
```
[complete sources](https://github.com/akka/alpakka/blob/c1315a8d1979b4399b62db726c159d64149501f7/mqtt/src/main/scala/akka/stream/alpakka/mqtt/MqttSourceStage.scala#L51)

On every arrived message we take a permit from a semaphore which was initialized to have the same number of permits as is the size of the internal buffer. When we run out of permits, library thread is going to be blocked on this line, backpressuring all of the incoming message flow.

We give permits back to the semaphore on ever message push to downstream.

```scala
setHandler(out, new OutHandler {
  override def onPull(): Unit = {
    if (queue.nonEmpty) {
      pushMessage(queue.dequeue())
      backpressure.release()
    }
  }
})
```
[complete sources](https://github.com/akka/alpakka/blob/c1315a8d1979b4399b62db726c159d64149501f7/mqtt/src/main/scala/akka/stream/alpakka/mqtt/MqttSourceStage.scala#L38)

This signals to the blocked library thread (if any) that there is now space in the internal buffer and it can continue delivering messages.

The resulting `MqttSource` does not block any thread from the thread pool that the stream is running on. Instead we block the thread that is managed by the external library when it calls a callback.

## Unsubscribing from the message flow

If the library does not provide any fine grained control of controlling incoming messages, the only measure that the connector then can take to protect against message overflow is to notify the library that it is not interested in getting messages anymore.

However this approach needs to be taken carefully. This is only possible if the library guarantees that unsubscribing from new incoming messages is handled synchronously. Otherwise, during the time of notifying the library of canceling the subscription and the actual stop of the message flow, there might still be undefined number of incoming messages which the connector might not be able to handle.

## Conclusion

Akka Streams gives its user the privilege of not thinking of ensuring flow control at all when working inside the world of Akka Streams. But a wanderer like you, that travels on the edge between the world of Akka Streams and another library is going to have to take one or the other approach when implementing a connector to a library.

The general approach is to firstly see if it is possible to request only a set number of elements from the library. If that is not possible, see if blocking the library thread is going to be a good signal to stop any other messages from coming in. And finally, last resort is going to be toggling the interest of new messages.

Let us know how it goes, and if you find another approach useful.

happy hakking!
