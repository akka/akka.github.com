---
layout: series_post
title: "Custom Flows: Parsing XML (part II)"
description: ""
author: Endre Varga
redirect_from: /integrations/2016/09/23/custom-flows-parsing-xml-part-2
series_title: Integration
series_tag: integration
category: article
tags: [streams,integration]
---
{% include JB/setup %}

In [part I]({{ site.baseurl }}/blog/2016/09/16/custom-flows-parsing-xml-part-1) we have built an XML parser that reads from a streamed data source and emits streamed XML parsing events as its output. This is a bit low level though to be directly usable, so in this post we will build two helper stages that illustrate how these events can be transformed (and also exercise our custom `Flow` skills along the way).

(The full sources of stages that we build in this post are available [here](https://github.com/akka/akka-stream-contrib/blob/master/xmlparser/src/main/scala/com/drewhk/stream/xml/Xml.scala). They are also
part of the [akka-stream-contrib](https://github.com/akka/akka-stream-contrib) project if you just want to use them immediately.)

One of the drawbacks of our parser `Flow` is how it handles `CData` and `Characters` events. Due to the bounded buffer in the [Aalto XML parser](https://github.com/FasterXML/aalto-xml) we don’t get large `CData` and `Characters` sections as one event, but a sequence of such events (if the buffer of the parser is full, it emits a chunk of these types instead of waiting for more data). Another drawback is that we treat `CData` and `Characters` as separate type of events. Our goal now is to perform a conversion that

 * Coalesce consequtive `Characters` events into a single `Characters` event
 * Convert `CData` to `Characters`, and coalesce it with a previous text event if possible.

We could add this functionality to the parser stage itself we built in the previous post, but with Akka Streams we prefer composition over putting everything into one big stage (and in general using built-in operations instead of building custom ones unless strictly necessary). What we need here is a transformer from a stream of `ParseEvents` to a new, coalesced stream of `ParseEvents`.

As before, we start with sketching out the *duty-cycle* of our new stage:

1. Wait for `onPull`
2. `pull` our upstream for the next event
3. If it is not a `TextEvent` (`CData` or `Characters`) then emit it as is, go to 1
4. If it is a `TextEvent`, buffer its contents and pull for next event and go back to 4 (after `onPull`)
5. If not a `TextEvent`, then we first emit all the buffered characters as a `Characters` event, then we emit the current event, then go to 1

In summary our main state transitions look like this: `PassThrough`→`Buffering`→`EmitTwoEvents`→`PassThrough`

Now that we have a solid  understanding of how our `Flow` should work, we can put this knowledge into practice and create the logic of our stage:

```scala
new GraphStageLogic(shape) with InHandler with OutHandler {
 private var isBuffering = false
 private val buffer = new StringBuilder

 override def onPull(): Unit = pull(in)

 override def onPush(): Unit = grab(in) match {
   case t: TextEvent =>
     buffer.append(t.text)
     isBuffering = true
     pull(in)
   case other =>
     if (isBuffering) {
       val coalesced = buffer.toString()
       isBuffering = false
       buffer.clear()
       emitMultiple(out, List(Characters(coalesced), other))
     } else {
       push(out, other)
     }
 }

 setHandlers(in, out, this)
}
```

We employed one useful trick in the logic: instead of encoding the states where we first emit a coalesced event then a non-text event directly, we use the helper method `emitMultiple()` which calls an optional callback once the elements have been successfully emitted (it installs a temporary `onPull` handler if necessary), in the case where we only have a single event the `emit()` helper can be used instead.

Are we done yet? No, we haven’t considered completion events from our upstream and downstream. Let’s enumerate the possible close events we can get:

* `onDownstreamFinish`: since the downstream is no longer interested in elements, we should just shut down, which is thankfully the default.
* `onUpstreamFailure`: our stream is broken and we cannot do anything other than to propagate the failure, which is again the default.
* `onUpstreamFinish`: the default would be to complete ourselves, but this is not always correct. There are two states where this leads to problems, `EmitTwoEvents` and `Buffering`:
  * `EmitTwoEvents`: `emitMultiple()` automatically ignores the completion event while the emitchain state machine is not yet finished, so this if fine. However, after the emit state machine finishes, we should check that the upstream has completed and complete ourselves.
  * `Buffering`: we have still one buffered event that we tried to coalesce. We should emit this last event before completing ourself.

We need to do two modifications, first, fixing our emit chain:

```scala
emitMultiple(out, List(Characters(coalesced), other), () => if (isClosed(in)) completeStage())
```

Then, we need to add a completion handler for upstream:

```scala
override def onUpstreamFinish(): Unit = {
 if (isBuffering) emit(out, Characters(buffer.toString()), () => completeStage())
 else completeStage()
}
```

We are certainly done now. Or do we? We introduced a buffer in our stage (the `StringBuilder`) but we have not installed a limit on how large it can grow. We can fix this easily by taking a `maximumTextLength` parameter on the stage and changing the logic where we append to the buffer:

```scala
// Take the maximum allowed text length as parameter
class Coalesce(maximumTextLength: Int) extends GraphStage[FlowShape[ParseEvent, ParseEvent]] {
...

override def createLogic(inheritedAttributes: Attributes): GraphStageLogic =
  ...
  case t: TextEvent =>
    // Protect against a too large buffer
    if (t.text.length + buffer.length > maximumTextLength)
       failStage(new IllegalStateException(s"Too long character sequence))
    else {
       buffer.append(t.text)
       isBuffering = true
       pull(in)
    }
    ...
}
```

Now we are really done. Using this stage is as simple as appending to the XML parser:

```scala
dataSource.via(new StreamingXmlParser).via(new Coalesce(1024))
```

This will stream us XML events where the text sections are conveniently packaged into one event instead of several. This pattern, adapted, could be also the basis of a parser that takes raw XML events and turns them into a class encoding a record in the file. 

We now attempt to solve a slightly more tricky problem, namely, taking out from the stream of XML events all the events that belong to a subpath and filter out everything else. For example, from a large XML file containing user information you want to only get the events that are under `<users><user><contact><email></email></contact></user></users>` and ignore everything else.

This is also an event stream transformer just like our `Coalesce` stage. What we try to end up is something that can be used like this:

```scala
dataSource
  .via(new StreamingXmlParser)
  .via(new Subslice(List(“users”, “user”, “contact”, “email”)))
```

Our duty cycle, if we remove the details how we track where we are in the XML tree, it looks relatively simple:

1. Wait for `onPull`
2. `pull` upstream
3. Wait for `onPush`
4. If event is `StartElement` or `EndElement`, update where we are in the tree, go to step 2
5. If event is other event, check if we are in the right path
6. If yes, emit, go to 1.
7. If no, go to step 2

In fact, almost all filtering type stages have a duty cycle that looks like this

1. Wait for `onPull`
2. `pull` upstream
3. Wait for `onPush`
4. If element is a non-match, go to 2
5. If element is a match, push element, go to 1

This is a fairly simple cycle and it does not really translate to an interesting state-machine. We still have to maintain the state where we are in the XML tree, which is a proper state machine (please don’t run away!). There are three interesting states where we can be
 * `passThrough`: we are inside the desired path, just emit events (and keep the path updated)
 * `partialMatch`: we matched part of the path, but not deep enough, for example we are at `<users><user>` but not yet encountered an opening tag for `<contact>` and `<email>`
 * `noMatch`: we are in a path that cannot be a match unless we go up the tree, for example we are in a tag `<users><user><permissions>`.

With these design considerations, we can start to flesh out the initial parts of our logic. We will keep two lists in the logic, one, where we keep track of what path we have matched so far, and another where we keep track of the list of the path we still need to match. We will also need to encode the three states. Since our pulling logic is to always pull the upstream (step 1-2 in the duty cycle) we don’t need to change it. All the interesting magic happens in `onPush` handlers, so we will encode our states as different `InHandler` instances (Note that due to the circular references involved in these handlers we need to use lazy vals):

```
override def createLogic(inheritedAttributes: Attributes): GraphStageLogic =
 new GraphStageLogic(shape) with OutHandler {
   private var expected = expectedPath
   private var matchedSoFar: List[String] = Nil

   override def onPull(): Unit = pull(in)

   // if the path we need to match is empty, just start from passThrough, otherwise
   // start from the partialMatch state
   if (path.isEmpty) setHandler(in, passThrough) else setHandler(in, partialMatch)
   setHandler(out, this)

   val passThrough: InHandler = new InHandler {
     override def onPush(): Unit = ???
   }

   lazy val partialMatch: InHandler = new InHandler {
     override def onPush(): Unit = ???
   }

   lazy val noMatch: InHandler = new InHandler {
     override def onPush(): Unit = ???
   }

 }
}
```

Now we need to implement the three states. In the state `passThrough`, we only keep track of how deep we are in the matched path (we can be in `<users><user><contact><email><work>`) but otherwise just emit events until we exit the mathed path (because we are in `<users><user><contact>` now so we need to match `<email>` again):

```scala
val passThrough: InHandler = new InHandler {
 var depth = 0

 override def onPush(): Unit = grab(in) match {
   case start: StartElement =>
     // Just record that we are one element deeper
     depth += 1
     push(out, start)
   case end: EndElement =>
     // We are exiting the current path, we need to 
     if (depth == 0) {      
       expected = matchedSoFar.head :: Nil
       matchedSoFar = matchedSoFar.tail
       setHandler(in, partialMatch)
       pull(in)
     } else {
       depth -= 1
       push(out, end)
     }
   case other =>
     // All other events pass through
     push(out, other)
 }
}
```

When we are in a partial match, we need to keep track of the exact path we are in, moving the name of an element between the `expected` and `matchedSoFar` lists depending on if it was an element start or end:

```scala
lazy val partialMatch: InHandler = new InHandler {

 override def onPush(): Unit = grab(in) match {
   case StartElement(name, _) =>
     // We match the next element in the path
     if (name == expected.head) {
       matchedSoFar = expected.head :: matchedSoFar
       expected = expected.tail
       if (expected.isEmpty) {
         // No more match needed, we are ready to pass through
         setHandler(in, passThrough)
       }
     } else {
       // We entered an element 
       setHandler(in, noMatch)
     }
     pull(in)
   case EndElement(name) =>
     // Since we exited from this element we need to match it
     // again.
     expected = matchedSoFar.head :: expected
     matchedSoFar = matchedSoFar.tail
     pull(in)
   case other =>
     pull(in)
 }

}
```

Finally, we need to model our `noMatch` state. In this state, we only need to track how deep down we are in the non-matching path, and go to `partialMatch` once we exited the element where we first detected a non-match:

```scala
lazy val noMatch: InHandler = new InHandler {
 var depth = 0

 override def onPush(): Unit = grab(in) match {
   case start: StartElement =>
     // We are even more deeper in the non-matching path
     depth += 1
     pull(in)
   case end: EndElement =>
     // Check if we finally exited the non-matching path	
     if (depth == 0) setHandler(in, partialMatch)
     else depth -= 1
     pull(in)
   case other =>
     pull(in)
 }
}
```

We are done with the main logic, now we need to consider the closing conditions and possible errors. Thankfully, our job here is done, as the stage does not buffer any elements so any completion events should just shut down the stage, which is the default. We cannot recover from upstream errors nor internal bugs either, so we are fine with the defaults again. We don’t have unbounded buffers either, the size of our `expected` and `matchedSoFar` lists are bounded and we keep counters only if we go deeper in the path. We are done!

This was quite a lot, and it is not expected that you understand everything at once. What is important is to understand the design steps involved in the design of a custom Flow (and other custom stages in general). These steps are:

1. Sketch out the duty cycle of a stage, i.e. a full cycle through it states until it hits its initial state again.
2. Identify the main states (that group together steps from the duty-cycle) in which your stage can be. 
3. Try to implement the logic that is derived from above. Eliminate trivial states by the use of emit() or emitMultiple() if possible.
4. Think about completion events (upstream and downstream) and what they mean in the context of each state you mapped out. Introduce new states or use emit() if necessary for a correct shutdown.
5. Double-check that all failure conditions are properly handled where possible (otherwise stick to the defaults).
6. If you use any kind of buffering in the stage double-check that it can never go arbitrarily large (unless you explicitly desire so). This means to check all call sites where anything is added to your container/buffer. Introduce an explicit capacity parameter to the stage if needed.

This concludes our Streaming XML parsing series. **Happy Hakking!**



