---
layout: post
title: "Spotlight: GraphStage emit and friends"
description: ""
author: Patrik Nordwall
redirect_from: /streams/2016/10/21/emit-and-friends
category: article
tags: [streams,spotlight]
---
{% include JB/setup %}


In Mastering GraphStages [Part I]({{ site.baseurl }}/blog/2016/07/30/mastering-graph-stage-part-1) and [Part II]({{ site.baseurl }}/blog/2016/08/25/simple-sink-source-with-graphstage) we have seen that push and pull are the primary methods to use when implementing a `GraphStage`. In this post you will learn that `GraphStage` also comes with other methods that can simplify the logic for some use cases.

As an example, let’s develop a stage that keeps track of the maximum element of a stream. It should consume elements from upstream as fast as possible and emit the maximum value downstreams when it has changed. When there is no request from downstream it should just accumulate current maximum and continue consuming elements from upstream.

A first stab may look like this:

```scala

import akka.stream.Attributes
import akka.stream.FlowShape
import akka.stream.Inlet
import akka.stream.Outlet
import akka.stream.stage.GraphStage
import akka.stream.stage.GraphStageLogic
import akka.stream.stage.InHandler
import akka.stream.stage.OutHandler

class MaxStage extends GraphStage[FlowShape[Int, Int]] {
  val in: Inlet[Int] = Inlet("MaxStage.in")
  val out: Outlet[Int] = Outlet("MaxStage.out")
  override val shape: FlowShape[Int, Int] = FlowShape(in, out)

  override def createLogic(inheritedAttributes: Attributes): GraphStageLogic =
    new GraphStageLogic(shape) {
      var maxValue = Int.MinValue
      var maxPushed = Int.MinValue

      setHandler(in, new InHandler {
        override def onPush(): Unit = {
          maxValue = math.max(maxValue, grab(in))
          if (isAvailable(out) && maxValue > maxPushed)
            pushMaxValue()
          pull(in)
        }

        override def onUpstreamFinish(): Unit = {
          if (maxValue > maxPushed)
            pushMaxValue()
          completeStage()
        }
      })

      setHandler(out, new OutHandler {
        override def onPull(): Unit = {
          if (maxValue > maxPushed)
            pushMaxValue()
          else if (!hasBeenPulled(in))
            pull(in)
        }
      })

      def pushMaxValue(): Unit = {
        maxPushed = maxValue
        push(out, maxPushed)
      }
    }
}
```

That looks rather straightforward, but there is a subtle bug in the code. Can you see it?

No worries, it’s difficult to spot.

How would we write a test for this stage? When we want full control of when elements are produced from upstream and consumed from downstream the `TestSource` and `TestSink` in the [Akka Streams Teskit](https://doc.akka.io/docs/akka/2.4/scala/stream/stream-testkit.html#Streams_TestKit) are handy.

```scala

      val (upstream, downstream) =
        TestSource.probe[Int]
          .via(new MaxStage3)
          .toMat(TestSink.probe)(Keep.both)
          .run()

      // send element 10 from upstream
      upstream.sendNext(10) 
      downstream.request(1)

      // and it is received by downstream
      downstream.expectNext(10)
      downstream.request(1)
      upstream.sendNext(9)
      upstream.sendNext(8)

      // no new max yet since 9 and 8 are < 10
      downstream.expectNoMsg(200.millis)
      upstream.sendNext(11)

       // new max emitted by the stage
      downstream.expectNext(11)
      upstream.sendNext(17)

      // end the stream
      upstream.sendComplete()

      // no request from downstream yet
      downstream.expectNoMsg(200.millis)
      downstream.request(1)

      // get the final element
      downstream.expectNext(17)
      downstream.expectComplete()
```

When running this test it fails with:

```
java.lang.IllegalArgumentException: requirement failed: Cannot push port (MaxStage.out) twice
	at scala.Predef$.require(Predef.scala:219)
	at akka.stream.stage.GraphStageLogic.push(GraphStage.scala:436)
	at blog.MaxStage$$anon$1.blog$MaxStage$$anon$$pushMaxValue(MaxStage.scala:51)
	at blog.MaxStage$$anon$1$$anon$2.onUpstreamFinish(MaxStage.scala:35)
```

Ah, the bug is in `onUpstreamFinish`. When pushing out the final element I forgot that downstream might not have requested anything yet, i.e. I’m not allowed to `push` yet.

Let’s try to fix that by adding a boolean `finishing` flag and push out the final element in `onPull` instead.

```scala
        var finishing = false

        override def onUpstreamFinish(): Unit = {
          if (maxValue > maxPushed) {
            if (isAvailable(out)) {
              pushMaxValue()
              completeStage()
            } else {
              // push final value and complete stage in onPull
              finishing = true
            }
          } else {
            completeStage()
          }
        }

        override def onPull(): Unit = {
          if (maxValue > maxPushed) {
            pushMaxValue()
            if (finishing)
              completeStage()
          } else if (!hasBeenPulled(in))
            pull(in)
        }
```

That works. Test is passing, but the logic is rather difficult to follow. 

Fortunately, `GraphStage` has some more utilities than the raw push and pull methods to handle such things. Let’s use **`emit`** instead. Then we don’t need to change the original `onPull` implementation and the new `onUpstreamFinish` looks like this:

```scala
        override def onUpstreamFinish(): Unit = {
          if (maxValue > maxPushed)
            emit(out, maxValue)
          completeStage()
        }
```

`emit` will push the element downstreams as soon as it is allowed to do so, i.e. when downstream has requested more elements. Also, note that it’s safe to call `completeStage` immediately after `emit`. It will automatically perform the `completeStage` action when the element has been pushed.

Nice!

There are other similar methods that can be good to be aware of, such as:

* `emitMultiple`: emit several elements from an `Iterable`
* `read` and `readN`: read one or more elements as they are pushed and react once the requested number of elements has been read

In some cases it is inconvenient and error prone to react on the regular state machine events with the signal based push/pull API. The API based on declarative sequencing of actions (e.g. `emit` and `read`) will greatly simplify some of those cases. The difference between the two APIs could be described as that the first one is signal driven from the outside, while the other is more active and drives its surroundings.

The complete source code for the example in this blog post is available in this [gist](https://gist.github.com/patriknw/65e94e0913db450fb0ea2da4c3e2d846). 
