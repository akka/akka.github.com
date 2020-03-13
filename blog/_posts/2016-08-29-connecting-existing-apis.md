---
layout: series_post
title: "Writing Akka Streams Connectors for existing APIs"
description: ""
author: Johan Andrén
redirect_from: /integrations/2016/08/29/connecting-existing-apis
series_title: Integration
series_tag: integration
category: article
tags: [streams,integration]
---
{% include JB/setup %}

Writing an integration from ground up can be daunting and will be an expensive solution to maintain over time. Luckily there is rarely a need to start from zero: the Java ecosystem is rich and if there is something to connect to there often is a library to interface with it already available. With Akka Streams wrapping such APIs in a custom `GraphStage` is an easy way to allow for using it within an asynchronous backpressured stream. Let’s take a look at a few different scenarios and how source and sink graph stages can be written for each.

Two of the samples are written Java and one in Scala but the `GraphStage` API does not differ much so just knowing one of the languages should be sufficient to understand all samples.

## Asynchronous callbacks
Some libraries already have asynchronous APIs and allows a user to register a callback of some sort to react on data arriving or the completion of a write operation.

Graph stages–very much like Actors–are maintaining the single threaded illusion, meaning that inside a `GraphStageLogic` instance we do not have to deal with concurrency primitives and can safely keep mutable state.

When dealing with callbacks there is however one problem: we often do not have any control over what thread the callback is executed on. Which means that it is not safe to touch the internal state of the `GraphStageLogic`. We need to first get the message into the execution context of the stream. This is done by acquiring an instance of `AsyncCallback`. Calling `AsyncCallback.invoke` will safely trigger the internal graph stage logic it points to.

### FileTailSource
This sample code is taken from the [FileTailSource](https://github.com/akka/alpakka/blob/ae2e1e1d44d627ebc66a24ea35993398df7840bc/file/src/main/java/akka/stream/alpakka/file/javadsl/FileTailSource.java) sources of the [Alpakka project](https://github.com/akka/alpakka). In this case we are reading chunks of bytes from a file, but not stopping and completing the stream when we reach the end of the file. Instead we schedule a later read to see if data was appended to the file since the last read.

We first acquire a the `AsyncCallback` pointing to a local function that will be what actually handles the read bytes. `AsyncCallback` will only handle a single parameter, so we need to use a datatype that covers all the information we want to pass on, in this case we use the [scala.util.Try](http://www.scala-lang.org/api/2.11.8/index.html#scala.util.Try) data structure here which will either be a `Success(bytes)` or a `Failure(exception)` but this is not mandatory and you could of course use whatever class that fits your use case:

```java
chunkCallback = createAsyncCallback((tryInteger) -> {
 if (tryInteger.isSuccess()) {
   int readBytes = tryInteger.get();
   if (readBytes > 0) {
     buffer.flip();
     push(out, ByteString.fromByteBuffer(buffer));
     position += readBytes;
     buffer.clear();
   } else {
     // hit end, try again in a while
     scheduleOnce("poll", pollingInterval);
   }

 } else {
   failStage(tryInteger.failed().get());
 }
});
```
[complete sources](https://github.com/akka/alpakka/blob/ae2e1e1d44d627ebc66a24ea35993398df7840bc/file/src/main/java/akka/stream/alpakka/file/javadsl/FileTailSource.java#L95)

We then use that callback in the `CompletionHandler` which is how the `java.nio` API will call us back. Note that there is an “attachment” passed as a parameter from the read to the callback and the actual consumer is stateless meaning that in this specific case we can keep a consumer singleton and share it between all instances of the `GraphStageLogic` without problems.

We pass the callback and the completion handler to `read`:

```java
private void doPull() {
  channel.read(buffer, position, chunkCallback, completionHandler);
}
```
[complete sources](https://github.com/akka/alpakka/blob/ae2e1e1d44d627ebc66a24ea35993398df7840bc/file/src/main/java/akka/stream/alpakka/file/javadsl/FileTailSource.java#L121)

Which will invoke either of the two methods on the `CompletionHandler` when the read operation completes:

```java
new CompletionHandler<Integer, AsyncCallback<Try<Integer>>>() {
  @Override
  public void completed(Integer result, AsyncCallback<Try<Integer>> attachment) {
    attachment.invoke(new Success<>(result));
  }

  @Override
  public void failed(Throwable exc, AsyncCallback<Try<Integer>> attachment) {
    attachment.invoke(new Failure<>(exc));
  }
};
```
[complete sources](https://github.com/akka/alpakka/blob/ae2e1e1d44d627ebc66a24ea35993398df7840bc/file/src/main/java/akka/stream/alpakka/file/javadsl/FileTailSource.java#L47)

We have full control over when we trigger a read here. This will play nicely with backpressure and the stage will simply not read data when downstream is back pressuring.

### AMQPSource
A more complex example of an asynchronous callback is the AMQP connector source, also in [Alpakka](https://github.com/akka/alpakka), which uses the [RabbitMQ driver](https://www.rabbitmq.com/java-client.html) to connect to an AMQP capable message broker.

The graph stage registers a listener which will be invoked when a message is available, and we use an `AsyncCallback` to make sure execution will be on the right thread:

```scala
val consumerCallback = getAsyncCallback(handleDelivery)
val shutdownCallback = getAsyncCallback[Option[ShutdownSignalException]] {
  case Some(ex) => failStage(ex)
  case None     => completeStage()
}

val amqpSourceConsumer = new DefaultConsumer(channel) {
  override def handleDelivery(consumerTag: String, envelope: Envelope, properties: BasicProperties, body: Array[Byte]): Unit = {
    consumerCallback.invoke(IncomingMessage(ByteString(body), envelope, properties))
  }

  override def handleCancel(consumerTag: String): Unit = {
    // non consumer initiated cancel, for example happens when the queue has been deleted.
    shutdownCallback.invoke(None)
  }

  override def handleShutdownSignal(consumerTag: String, sig: ShutdownSignalException): Unit = {
    // "Called when either the channel or the underlying connection has been shut down."
    shutdownCallback.invoke(Option(sig))
  }
}

channel.basicConsume(
  settings.queue,
  false, // never auto-ack
  settings.consumerTag, // consumer tag
  settings.noLocal,
  settings.exclusive,
  settings.arguments.asJava,
  amqpSourceConsumer
)
```
[complete sources](https://github.com/akka/alpakka/blob/c1315a8d1979b4399b62db726c159d64149501f7/amqp/src/main/scala/akka/stream/alpakka/amqp/AmqpSourceStage.scala#L48)

This alone does not help us with backpressure though, luckily the RabbitMQ client also lets us define a prefetch size together with an ACK protocol, so that we can limit the amount of outstanding unacknowledged messages.

As an optimization we keep an internal buffer that can fit exactly this number of elements so that incoming messages can be batched rather than sent one by one from the message broker. When downstream is ready to process a message we take it out of the buffer and emit it downstream, we then send an ACK back to the broker so that we can receive new incoming messages.

```scala
def handleDelivery(message: IncomingMessage): Unit = {
  if (isAvailable(out)) {
    pushAndAckMessage(message)
  } else {
    queue.enqueue(message)
  }
}
```
[complete sources](https://github.com/akka/alpakka/blob/c1315a8d1979b4399b62db726c159d64149501f7/amqp/src/main/scala/akka/stream/alpakka/amqp/AmqpSourceStage.scala#L100)

It is important to isolate such stages on a separate dispatcher, preferably thread pool based ones. The number of threads will limit how many concurrent instances of the `GraphStageLogic` can run on the system but will not affect other parts.

Selecting a separate dispatcher in Akka Streams is done by returning it from the `initialAttributes` of the `GraphStage`. (Note that this will isolate the `GraphStageLogic` into a separate actor when materializing and therefore introduce an input buffer to optimize passing the asynchronous boundary, read more
in the docs: [Scala](https://doc.akka.io/docs/akka/2.4/scala/stream/stream-rate.html#Buffers_for_asynchronous_stages) and [Java](https://doc.akka.io/docs/akka/2.4/java/stream/stream-rate.html#Buffers_for_asynchronous_stages))

```scala
override protected def initialAttributes: Attributes = Attributes.name("AmsqpSink")
    .and(ActorAttributes.dispatcher("akka.stream.default-blocking-io-dispatcher"))
```

## Polling based APIs
Some libraries have APIs where data is acquired by calling a polling method, the legacy Java `InputStream` APIs are examples of this. There are a few variations of poll based APIs though, the mentioned Java ones being the first, where the poll will block the calling thread indefinitely (there are workarounds but in general this is how it often is used).

This one is hard to adapt, but most important is to isolate it from the rest of the system as mentioned in the Blocking Push section above.

### Timed and Non-blocking polling
A more resource friendly variation of blocking polling is where you can provide a timeout to the blocking poll making fail or return if there was no data. A special case of this is non-blocking polling, where the call always returns right away even if there is no elements. Both these two can be integrated in the same way, when there is demand, we poll (with a very short timeout in the timed case) and if we get no data back we schedule a slight back off, and then check again.

This can be implemented using the `TimerGraphStageLogic` which provides a facility for scheduling calls to a method which can then do the polling. In this sample we do exactly this to poll the WatchService for filesystem changes:

```java
private void schedulePoll() {
  scheduleOnce("poll", pollInterval);
}

@Override
public void onTimer(Object timerKey) {
  if (!isClosed(out)) {
    doPoll();
    if (!buffer.isEmpty()) {
      pushHead();
    } else {
      schedulePoll();
    }
  }
}
```
[complete sources](https://github.com/akka/alpakka/blob/c1315a8d1979b4399b62db726c159d64149501f7/file/src/main/java/akka/stream/alpakka/file/javadsl/DirectoryChangesSource.java#L133)

Let's combine two of these samples into a stream that will emit local log entries tailed from a textfile as they are written and push each line to an AMQP broker:

```java
final Path logfile = FileSystems.getDefault().getPath("/var/log/system.log");
final FiniteDuration pollingInterval = FiniteDuration.create(250, TimeUnit.MILLISECONDS);
final int maxLineLength = 4096;
FileTailSource.createLines(fs.getPath(path), maxLineLength, pollingInterval)
  .to(AmqpSink.simple(settings))
  .run(materializer);
```
[complete sources](https://gist.github.com/johanandren/41b096c9ee647863c6c04959be548b25)
