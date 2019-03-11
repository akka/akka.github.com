---
layout: post
title: Akka Streams &amp; Http 0.7 Released!
author: Konrad 'ktoso' Malawski
short: Akka Streams and Http 0.7 Released
category: news
redirect_from: /news/2014/09/12/akka-streams-0.7-released.html
tags: [releases]
---

*Dear hAkkers,*

today we are very excited to share with you another early preview milestone of Akka Streams and Akka HTTP. 
Version 0.6 was not widely announced because we were preparing some major work on the API and most of that is now finished. 

Behold, the new FlowGraph DSL (we have implemented Scala first, the equivalent Java API will follow shortly):

    // first define some pipeline pieces
    val f1 = FlowFrom[Input].map(_.toIntermediate)
    val f2 = FlowFrom[Intermediate].map(_.enrich)
    val f3 = FlowFrom[Enriched].filter(_.isImportant)
    val f4 = FlowFrom[Intermediate].mapFuture(_.enrichAsync)

    // then add input and output placeholders
    val in = SubscriberSource[Input]
    val out = PublisherSink[Enriched]

(There are many more sources and sinks available, also for strict collections, iterators, futures and promises, etc.)

![akka streams processing graph]({{ site.baseurl }}/resources/images/news/akka-streams-07-example.png)

Then in order to connect these Flows into a complex stream processing graph  

    val g = FlowGraph { implicit b ⇒
      val bcast = Broadcast[Intermediate]
      val merge = Merge[Enriched]
    
      in ~> f1 ~> bcast ~> f2 ~> merge ~> f3 ~> out
      bcast ~> f4 ~> merge
    }.run()

    // now get out the real ins and outs for this run
    val sub = in.subscriber(g)
    val pub = out.publisher(g)

The [previous Flow/Duct DSL](https://doc.akka.io/api/akka-stream-and-http-experimental/0.6/#akka.stream.package) — which 
is still present in the scaladsl & javadsl packages and will be removed in the next milestone – has now been unified 
in the [scaladsl2 package](https://doc.akka.io/api/akka-stream-and-http-experimental/0.7/#akka.stream.scaladsl2.package) 
and sliced differently: all operations that mediate between one input stream and one output stream (map, filter, etc.; 
also groupBy produces only one primary output stream) are defined on Flow, while all other "plumbing" operations are 
modeled as a graph that can capture any desired topology — cycles are an advanced feature that needs to be explicitly 
enabled though. FlowGraphs and Flows are immutable descriptions of a processing machine that can be set in motion by the 
run() method, which allows you to freely share references to these and write libraries that provide and consume them.

The second very exciting aspect of today’s milestone release is that it also incorporates the first part of the HTTP 
routing DSL that is being ported from Spray—as a first step we just incorporate the existing Scala part and in a second 
step we will add an equivalent Java API as well. To those of you who have used Spray before it should look very familiar,
on the surface it will remain as source compatible as we can make it. Upon closer inspection you will find that the DSL 
is now safer than before, its result type is no longer Unit but RouteResult (which will result in better compiler errors 
for nonsensical route definitions) and the evaluation of all elements is now consistently "lazy", meaning that the inner 
part is not executed surprisingly for non-matching requests (that used to happen in certain edge cases).

The third exciting item is SSL support for our Streams, you can take a look at 
[SslTlsFlowSpec](https://github.com/akka/akka/blob/62a20195af79c7b405303b5f97970c9ca7a6891a/akka-stream/src/test/scala/akka/stream/io/SslTlsFlowSpec.scala) 
to get an impression of how we are integrating it. Currently session renegotiation is not yet implemented and HTTP does not know about it either.

As usual please be aware that this is still a *very early development preview*, we will likely change the API over the 
coming weeks (we will certainly move scaladsl2 back as scaladsl for example) but at this point the seas are calming down
and the weather should be approaching smooth sailing conditions soon.

Happy hakking!

## Closed tickets

The complete list of closed tickets can be found in the [streams-0.7 github issues milestone](https://github.com/akka/akka/issues?q=is%3Aissue+milestone%3Astreams-0.7+is%3Aclosed).

Akka Streams & Http 0.7 is released for Scala 2.10.4 and 2.11.2. 

### Credits ###

    commits added removed
       30    5922    1145 Patrik Nordwall
        8     109   10255 Johannes Rudolph
        7    1545     230 Martynas Mickevicius
        5    1678    1040 Konrad 'ktoso' Malawski
        5    4153     498 Mathias
        4    1883     469 Endre Sándor Varga
        4    1924     515 Björn Antonsson
        2     680     549 Roland Kuhn

*Happy hAkking!*
