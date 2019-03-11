---
layout: post
title: Akka Stream and Http 2.0-M1 Released!
author: Endre Sándor Varga
short: Akka Stream and Http 2.0-M1 Released!
category: news
redirect_from: /news/2015/11/05/akka-streams-2.0-M1-released.html
tags: [releases]
---

*Dear Hakkers*

we—the Akka committers—are very pleased to announce Akka Streams & HTTP 2.0-M1, the first milestone of the upcoming 2.0 release. The overarching theme of this milestone is unification, both in internals and API. Thanks go to all of you who tried out Streams & HTTP 1.0, your feedback—both direct and indirect—has been instrumental in making this library more accessible and intuitive to use.

There have been several changes in user facing APIs mostly driven by two goals. First, reducing the number of overloaded methods helps IDEs and compilers for both Scala and Java (especially Java 8), leads to more meaningful error messages, and also makes discovery of methods easier via API documentation or code completion. Second, we reduced the different ways certain tasks can be achieved by reducing the number of helper methods, taking away potentially confusing choices. While this means that in certain cases the amount of boilerplate has grown slightly, the APIs reflect more directly the underlying uniform model and hence feel more consistent than before. Some highlights of these changes are (for the full list see the **migration guide** for [Java](https://doc.akka.io/docs/akka-stream-and-http-experimental/2.0-M1/java/migration-guide-1.0-2.x-java.html) and [Scala](https://doc.akka.io/docs/akka-stream-and-http-experimental/2.0-M1/scala/migration-guide-1.0-2.x-scala.html)):

 - Methods for creating Flows, Sources etc. from Graphs are now called `fromGraph` instead of `wrap`.
 - The number of FlowGraph builder methods has been reduced to the necessary minimum.
 - In the Scala DSL the only graph building API is the arrow ~> notation, the edge-based alternative (more klunky) API has been removed.
 - Creating Flows, Sources, etc. from Graphs now can be only done in two steps, first creating the Graph, then using fromGraph to turn it into the desired target type. The second step is only necessary when subsequently using the combinators defined on the Source/Flow types, reuse of processing setups is better done using the language-agnostic Graph type.

*For the full list of changes see the Github [milestone](https://github.com/akka/akka/issues?q=milestone%3Astreams-2.0-M1) and
[http-2.0-M1](https://github.com/akka/akka/issues?q=milestone%3Ahttp-2.0-M1)*

On the internals front the most important change is that we removed almost all of the custom actor-based stage implementations, thanks to a new abstraction called *GraphStage*. GraphStage is a new user API that replaces FlexiMerge, FlexiRoute and AsyncStage. Compared to the previous abstractions GraphStage is capable of modeling a stage with any number of input and output ports and asynchronous notifications and without the limitations that the FlexiMerge/FlexiRoute implementations suffered. A testament to the flexibility and usefulness of this new stage design is that nearly all of the built-in stages are now GraphStage based implementations (with the remaining ones being scheduled to be rewritten—there is no reason why dedicated actors are still needed given the expressive power of GraphStage). A fun fact is that this rewrite also resulted in 29% jar size reduction compared to the 1.0 artifact while having more features than ever before.

The new GraphStage already enabled us to unify and simplify a large amount of internal code, it also provides a richer customization API for users for building new stages, but this is just the beginning! This new abstraction is the first step towards a very important goal we have, which is called *fusing*. This feature will allow declarative demarcation of actor boundaries backing a streaming graph, i.e. it will be now possible to execute multiple stream processing steps inside one actor, reducing the number of thread-hops where they are not necessary. This is also the feature that will increase performance for various use cases, including HTTP.

On the Akka HTTP front much has happened as well. The documentation is now mostly complete and several API, marshalling as well as configuration improvements have been merged as well.

We hope that this evolution of Akka Streams & HTTP goes into the right direction, enabling Java and Scala developers to pick up this library as a tool that is universally useful in their day to day coding. To this end we continue to depend on all your excellent and honest feedback, so please try it out!

We would like to give our special thanks and kudos to Alexander Golubev, who has been contributing a steady stream of high quality, and often non-trivial pull requests to Akka Streams. Thanks!

Similarly, we would like to give a huge thanks and kudos to @2beaucoup, a long-time Spray contributor, who has been helping Akka HTTP succeed with his excellent in-depth insights and non-trivial pull requests. Thank you very much!

For this release we had the help of 30 committers.

    63    5469    1117 Johannes Rudolph
    46    5747    1609 Konrad Malawski
    23    3331     688 Alexander Golubev
    17    7503    2397 Endre Sándor Varga
    15    2756    2955 Viktor Klang
    12     499     345 2beaucoup
    10    4160     689 Mathias
    10     742     143 Martynas Mickevičius
     7    3900    8463 Roland Kuhn
     3     383      61 Johan Andrén
     3      16       1 hepin
     2       2       2 netcomm
     1     243       0 Philipp
     1      64       2 kulikov
     1      25       1 James Roper
     1       1       1 egisj
     1       1       1 Sören Brunk
     1      16      16 何品
     1     227      64 pjan vandaele
     1       1       1 luben karavelov
     1     518       1 Patrik Nordwall
     1      81      81 kukido
     1      37       8 Frank Murphy
     1       6       0 Ryan Bair
     1       0       2 Richard Bradley
     1       8       8 Balazs Kossovics
     1       3       3 Fehmi Can Saglam
     1      66      22 Mathias Bogaert
     1     180     223 André Rüdiger
     1       1       1 Jesse Haber-Kucharsky

Happy hakking,

-- The Akka Team
