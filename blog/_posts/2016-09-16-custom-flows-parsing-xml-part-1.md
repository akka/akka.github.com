---
layout: series_post
title: "Custom Flows: Parsing XML (part I)"
description: ""
author: Endre Varga
redirect_from: /integrations/2016/09/16/custom-flows-parsing-xml-part-1
category: article
series_title: Integration
series_tag: integration
tags: [streams,integration]
---
{% include JB/setup %}

In previous posts we have shown how to build basic [Sinks and Sources]({{ site.baseurl }}/blog/2016/08/25/simple-sink-source-with-graphstage), and how to [integrate with existing APIs]({{ site.baseurl }}/blog/2016/08/29/connecting-existing-apis) and handle [backpressure in various ways](http://blog.akka.io/integrations/2016/09/05/flow-control-at-the-akka-stream-boundary). In this post we move on to the more advanced territory of custom Flows.

If you recall, every processing entity in Akka Streams has a number of input and output ports which it can consume elements from and push elements to (if you need a refresher, look at [this documentation section](https://doc.akka.io/docs/akka/2.4/java/stream/stream-composition.html)). This means that your knowledge from Sinks and Sources is applicable to Flows, too. We will build on this knowledge to build a streaming XML parser. When we talk about streaming XML here we don’t mean XML documents concatenated with some separator, but a large, valid XML file which contains  many XML elements. We will show you how to approach this problem with Akka Streams. For this, we will use the awesome [Aalto XML parser](https://github.com/FasterXML/aalto-xml) which will do the heavy lifting for us.

(The full source of the stage that we build in this post is available  [here](https://github.com/akka/akka-stream-contrib/blob/master/xmlparser/src/main/scala/com/drewhk/stream/xml/Xml.scala). It is also
part of the [akka-stream-contrib](https://github.com/akka/akka-stream-contrib) project if you just want to use it immediately.)

> Unlike in our previous posts which used Java to build custom stages, here we will use Scala. The APIs are quite similar though so it is not hard to port the samples to Java, especially since the underlying Aalto XML parser has a Java API.

As always, when it comes to building custom stages, we need to start with some boilerplate. First, we need to subclass `GraphStage`, then define a `Shape`, and finally a factory method for our actual logic, which is a subclass of `GraphStageLogic`:

```scala
// A stage is always an instance of a GraphStage with a certain Shape.
class StreamingXmlParser extends GraphStage[FlowShape[ByteString, ParseEvent]] {
 // The input port to consume ByteStrings from
 val in: Inlet[ByteString] = Inlet("XMLParser.in")
 // The output port to emit parse events to 
 val out: Outlet[ParseEvent] = Outlet("XMLParser.out")
 // Since we have only one input and output, we have a FlowShape
 override val shape: FlowShape[ByteString, ParseEvent] = FlowShape(in, out)

 // Never put mutable state here, 
 // all such state must go into the GraphStageLogic!
     
 override def createLogic(inheritedAttributes: Attributes): GraphStageLogic =
   new GraphStageLogic(shape) with InHandler with OutHandler {
     //Add stateful XML parser here

     override def onPush(): Unit = ???
     override def onPull(): Unit = ???

     setHandlers(in, out, this)
   }
}
```

As we see from the `Shape` definition, our custom stage will have exactly one input and output (i.e. it has a `FlowShape`), one from which we will consume raw bytes in chunks, and one for emitting `ParseEvents` (we look at this soon). We also need to implement the factory method, `createLogic`, which will create a `GraphStageLogic` for our stage and which will encapsulate all of our state - in this case the XML parser itself.

> In Java you will need to use the `AbstractInHandler` and `AbstractOutHandler` types to create your handlers for downstream and upstream handlers like `push()` and `pull()`. Since these are abstract types and not interfaces, you have to create these as anonymous inner classes in your GraphStageLogic. See our [earlier introductions]({{ site.baseurl }}/blog/2016/08/25/simple-sink-source-with-graphstage) for examples.

Now, we design the events that will be emitted by our stage. We chose here a simplified subset of the XML events the Aalto XML parser can give us and encode them as case classes:

```scala
sealed trait ParseEvent
sealed trait TextEvent extends ParseEvent {
 def text: String
}

case object StartDocument extends ParseEvent
case object EndDocument extends ParseEvent
final case class StartElement(localName: String, attributes: Map[String, String])
  extends ParseEvent
final case class EndElement(localName: String) extends ParseEvent
final case class Characters(text: String) extends TextEvent
final case class ProcessingInstruction(target: Option[String], data: Option[String]) 
  extends ParseEvent
final case class Comment(text: String) extends ParseEvent
final case class CData(text: String) extends TextEvent
```

These roughly correspond to the standard Java `XmlEvent` types. At this point we have almost set up the ground for implementing our Flow, the only piece missing is figuring out what the Aalto XML parse expects us to do to drive it. This parser is a so-called *push-pull* parser, which basically means that it has two sides, one from where we can feed it new data, and another where we can pull parsed events out, or get a signal that there isn’t any new event at this point. This is different from traditional XML *pull parsers* where consumption of data is hidden from us (i.e. there is no API where we can explicitly feed the parser chunk by chunk) and where the thread is blocked when we ask for a new event but there is none available yet. To see this in action, first, let's set up our parser inside the `GraphStageLogic`:

```scala
// inside GraphStageLogic
private val feeder: AsyncXMLInputFactory = 
  new InputFactoryImpl()
private val parser: AsyncXMLStreamReader[AsyncByteArrayFeeder] = 
  feeder.createAsyncFor(Array.empty)   
```

Now we have a parser, all we need to figure out is how to feed it and read from it. This is very simple actually:

 * To check if there are available events to read, we can call `parser.hasNext`.
 * To read the actual event, we can call `parser.next()` which will return us an event code (one of `javax.xml.stream.XMLStreamConstants`). If this is an actual event, we can get the details of the event using the various getter methods on the parser. There is an extra event in addition to the ordinary Java XML pull parser world: `AsyncXMLStreamReader.EVENT_INCOMPLETE` which signals us that there are no events available and we need to feed the parser with more bytes.
 * To feed the parser with new data, we need to call `parser.getInputFeeder.feedInput()` with an array of bytes
 
 With this knowledge we are ready to sketch out a *duty-cycle* of our stage, i.e. a sequence of events which can return to the initial state and can touch all or most of the intermediate states:
 
1. Wait for onPull
2. Check if the parser has an available event
3. If yes, decode the event into our event classes and emit it, go to 1 (unless it is END_DOCUMENT in which case we complete the stage)
4. If no, pull our upstream for new data 
5. Wait for onPush
6. Feed the parser with new data and go to 2

> When designing custom stages it is usually good practice to map out the so-called duty-cycle of the stage we design. This usually means recognizing the various states our stage can be in and sketching out a full cycle that drives it through every state returning to some initial position. In our XML example we have two states, `Emitting` (when the parser has elements to emit) and `Feeding` (when the parser needs more data) which we cycle through: `Emitting`→`Feeding`→`Emitting`→... .

Observing carefully, we recognize that the push and pull events both have a common subsequence starting from step 2. This means that we can extract this piece of logic into a method: check the parser for its state, if it has events, emit them, else pull new data. Putting together all this, we end up with the following logic:

```scala
override def onPull(): Unit = advanceParser()

override def onPush(): Unit = {
 val array = grab(in).toArray
 parser.getInputFeeder.feedInput(array, 0, array.length)
 advanceParser()
}

private def advanceParser(): Unit = {
  if (parser.hasNext) {
    parser.next() match {
      case AsyncXMLStreamReader.EVENT_INCOMPLETE =>
        if (!isClosed(in)) pull(in)
        else failStage(
          new IllegalStateException("Stream finished early.")
        )

      case XMLStreamConstants.START_DOCUMENT =>
        push(out, StartDocument)

      case XMLStreamConstants.END_DOCUMENT =>
        push(out, EndDocument)
        completeStage()

      case XMLStreamConstants.START_ELEMENT =>
        val attributes = (0 until parser.getAttributeCount).map { i =>
          parser.getAttributeLocalName(i) -> parser.getAttributeValue(i)
        }.toMap

        push(out, StartElement(parser.getLocalName, attributes))

      case XMLStreamConstants.END_ELEMENT =>
        push(out, EndElement(parser.getLocalName))

        //.. more decoded events omitted here.
      }
    } else completeStage()
  }
}
```

We are ready! Well, almost. We still have not handled completion events from our downstream consumer and upstream producer. What should we do if the downstream consumer of XML events cancels? The only sensible solution seems to be to simply stop ourselves and cancel our own upstream. This is the default, so we don’t need to do anything here. What should we do if our upstream closes signalling that it has no more data for us? We cannot simply stop because **the parser might have multiple events we have not consumed yet**. What we must do is to signal to the parser that we will not give it more data: `parser.getInputFeeder.endOfInput()`, then, we check if the parser has still events. If not, we can complete our stage. Otherwise, we must check if our downstream is ready for consuming an element (upstream completion might come in a time where we have not yet been pulled!) and then feed it. This is how this looks like in code:

```scala
override def onUpstreamFinish(): Unit = {
  parser.getInputFeeder.endOfInput()
  if (!parser.hasNext) completeStage()
  else if (isAvailable(out)) advanceParser()
}
```

Are we now ready? Almost :) We still need to think about error handling. We have two sources of failures we can encounter here:

 * Inside the stage (a bug in our code or in the XML parser)
 * Failure of the upstream producer
 
The first case is simple enough, if any exception is thrown from our logic the Akka Stream infrastructure will automatically catch it, complete our stage and propagate the failure downwards to the next consumer. The second case means that we cannot drive our parser anymore and we did not finish yet so the reasonable action seems to be to fail ourselves, too. Since the default onUpstreamFailure implementation does this already, we need to do nothing.

Done! We now are able to feed a large XML document from a streaming source (file, network, etc) into our parser and consume parsed events. Using it is as simple as:

```scala
val xmlSource = dataSource.via(new StreamingXmlParser)
```

If you liked this article and feeling adventurous, you can try to adapt the code to use the Actson JSON parser which is a similar push-pull parser that we used, but for parsing JSON: [https://github.com/michel-kraemer/actson](https://github.com/michel-kraemer/actson).

In part II. we will show how to deal with the streamed events we get from the parser. Stay tuned!
