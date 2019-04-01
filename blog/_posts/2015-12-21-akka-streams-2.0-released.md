---
layout: post
title: Akka Stream and Http 2.0 Released!
author: Konrad 'ktoso' Malawski
short: Akka Stream and Http 2.0 Released!
category: news
redirect_from: /news/2015/12/21/akka-streams-2.0-released.html
tags: [releases]
---

*Dear Hakkers,*

we—the Akka committers—are very pleased to announce **Akka Streams & HTTP 2.0**. This major update represents the culmination of an inspiring community push after our 1.0 release more than five months ago.

The changes we made can be classified into two categories:

*   We streamlined the API in response to user feedback—both direct and indirect—by offering a well-rounded set of combinators for expressing most streaming operations. This means that dropping down to the GraphDSL or even custom processing stages should rarely be necessary—we consider these to be advanced tools that come with greater power as well as greater potential for making mistakes (like graph cycles that may deadlock, or like faulty logic in a GraphStage that will halt the whole stream). In this sense we have also changed the stream-of-stream operators like groupBy to behave in a more controlled and restricted fashion, avoiding the possibility of treating nested streams incorrectly.

*   We streamlined the internals and made (nearly) all elementary building blocks fusable—this means that it is now possible to group processing steps together such that they run within the same Actor. This avoids asynchronous communication overhead which can be sizable for small steps like a simple mapping function. Fusing is switched on in the default materializer but can be disabled with a configuration setting—the fusing algorithm itself introduces an added initialization cost that may not amortize for very short-lived streams. If fusing is enabled then part of the graph can be executed on different Actors by explicitly demarcating the asynchronous boundaries with a graph attribute.

While there are still many tickets open for future enhancement we consider Streams & HTTP 2.0 to be ready for general consumption. This release still bears the “experimental” marker due to upcoming changes on the Java side (more on that below), but after Christmas we will start with moving these modules onto the Akka master branch followed by a 2.4.x release by the end of January.

Moving to 2.4.x means that we will also have access to all nice Java 8 library features like CompletionStage, Optional, Stream and built-in functional interfaces. In order to offer an up-to-date Java experience we will change the API one last time, hence the remaining “experimental” designation in version 2.0.

**Performance Expectations**

We now have all mechanisms in place, but we have not yet actually optimized for performance—although sizable improvements are already there by enabling operator fusion. Both the functionality as well as the overhead of the fusing function will see improvements, and we have not even begun looking into the profiling data for our stream processing engine. While it is possible to run the HTTP server with a pre-fused flow, we have not included this enhancement in this release—it arrived too late to be certain that it works as expected. All this together means that during the next weeks there will be a lot of activity around optimizing all aspects of Akka Streams & HTTP and the hunting season for performance gains is now officially open: if you always wanted to contribute an Akka speed increment then now is the time to warm up your profiler and HTTP load generator.

**Closing Remarks**

We would like to give our special thanks and kudos to Alexander Golubev, who has continuously contributed a steady stream of new features to Akka Streams while we were busy working on the internals. Thanks! We also thank @2beaucoup for his continued effort in reviews and fixes in Http. And of course we thank all of the 42 individuals that contributed to this part of Akka’s success!

The reference documentation for the 2.0 release can be found here for [Java](https://doc.akka.io/docs/akka-stream-and-http-experimental/2.0/java.html) and [Scala](https://doc.akka.io/docs/akka-stream-and-http-experimental/2.0/scala.html).

The full list of changes since the last milestone is available under the [streams-2.0](https://github.com/akka/akka/issues?q=is%3Aissue+milestone%3Astreams-2.0+is%3Aclosed) and [http-2.0](https://github.com/akka/akka/issues?q=is%3Aissue+milestone%3Ahttp-2.0+is%3Aclosed) milestones on github for your reference.

We would like to thank everyone who contributed to this release of Akka Streams & HTTP since its 1.0 release a few months ago:

    commits added removed
       80    7822    2022 Konrad Malawski
       71    5803    1316 Johannes Rudolph
       42    5292    1142 Alexander Golubev
       32    7954    3082 Mathias
       27    3932    4035 Viktor Klang
       27   10637    3578 Endre Sándor Varga
       26   10587   12318 Roland Kuhn
       21     608     521 2beaucoup
       12    3232     941 Johan Andrén
       13    1679     930 Martynas Mickevičius
        6     573      82 lolski
        3      16       1 hepin
        2      35       3 Anton Karamanov
        2     205       7 Gilad Hoch
        2       2       2 netcomm
        2     148      24 Catalin Ursachi
        1       1       1 Johan Andrén
        1       3       3 Fehmi Can Saglam
        1      37       8 Frank Murphy
        1     180     223 André Rüdiger
        1      64       2 kulikov
        1       8       6 Heiko Seeberger
        1      26       1 Anderson Osagie
        1      29       6 Daniel Wegener
        1      81      81 kukido
        1       1       1 Jesse Haber-Kucharsky
        1       4       0 Harit Himanshu
        1      16      16 何品
        1     216       1 Iain Monro
        1       8       8 Balazs Kossovics
        1       6       0 André Cardoso
        1       4       1 Tim Harper
        1       6       0 Ryan Bair
        1       1       1 Sören Brunk
        1      66      22 Mathias Bogaert
        1     227      64 pjan vandaele
        1       1       1 luben karavelov
        1       0       2 Richard Bradley
        1      25       1 James Roper
        1     243       0 Philipp
        1     518       1 Patrik Nordwall
        1       1       1 egisj

Happy hakking,

-- The Akka Team
