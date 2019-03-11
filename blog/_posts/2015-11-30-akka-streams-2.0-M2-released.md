---
layout: post
title: Akka Stream and Http 2.0-M2 Released!
author: Endre Sándor Varga
short: Akka Stream and Http 2.0-M2 Released!
category: news
redirect_from: /news/2015/11/30/akka-streams-2.0-M2-released.html
tags: [releases]
---

*Dear Hakkers*,

we—the Akka committers—are very pleased to announce Akka Streams & HTTP 2.0-M2, the second milestone of the upcoming 2.0 release. While most of the changes in this release are internal, there are some new features available, too.

The workhorse of the 2.0 series, *GraphStage* has now preliminary documentation for *both Java and Scala*. GraphStage is a new user API that replaces FlexiMerge, FlexiRoute, StatefulStage and AsyncStage (and probably DetachedStage in the near future). Compared to the previous abstractions GraphStage is capable of modeling a stage with any number of input and output ports and asynchronous notifications, without the limitations that the FlexiMerge/FlexiRoute implementations suffered. A testament to the flexibility and usefulness of this new stage design is that nearly all of the built-in stages are now GraphStage based implementations. The next milestone will likely replace all custom actor based implementations with this abstraction internally.

The GraphStage API provides full support for asynchronous, non-stream events like timers or other external events (Future completion, callbacks from 3rd pary APIs, etc). These features already have a short explanation in the documentation, but the full treatise and examples of these features will come with the next milestone.

The tireless refactoring of our internals have been continuing. In this release we managed to replace all the TCP related actors by GraphStages which will allow us to fuse these in the future. This was possible thanks to the new ActorRef support for GraphStages which enables stages to be individually addressed via specialized ActorRefs even if they are fused. This feature is highly experimental, but already available for *Hakkers* to try out. 

One new superpower Akka Streams gained thanks to the new unified internals is the *fuzzing-mode* execution which helps to achieve better code path coverage in stream tests. If this mode is enabled the execution engine will aggressively reschedule, or even explicitly reorder events that are not causally related—exposing many race conditions as a result. This mode is enabled for all of our internal test cases now for streams, and will be soon enabled for HTTP tests, too. See the testing documentation for details.

With the new milestone arrived new API refactorings. The File and Input/OutputStream related support is now moved to the Source and Sink classes directly (e.g. Source.file()) instead of their own classes for better discoverability (previously SynchronousFileSource, SynchronousFileSink, InputStreamSource and OutputStreamSink). See the migration guide for details.

There are some new features available as well, most notably there are now a `headOption` on Sink, a `throttle` on Sources and Flows.

The focus of the Akka HTTP work remained the same as in previous iterations. We have fixed and hardened parts of the HTTP server and continued preparing its internals for the upcoming graph fusing, which will yield the promised performance improvements in 2.0. We also fixed a critical actor-leak case in the HTTP Client, which would manifest itself when open connections failed.
Other than that, new documentation sections as well as simplified directives for common use cases, such as file uploads have been added.

The following bugs were fixed:

 - Idle timeout now works properly for TCP and HTTP connections
 - BidiFlow reverse now does not lead to a ClassCastException
 - HTTP Client error handling has been improved.
 - Some built-in stages were hardened thanks to the new fuzzing-mode
 
The full list of changes can be found on Github: [stream](https://github.com/akka/akka/issues?q=milestone%3Astreams-2.0-M2+is%3Aclosed) and [http](https://github.com/akka/akka/issues?q=milestone%3Ahttp-2.0-M2+is%3Aclosed)

What comes next? We have in progress several improvements that will likely arrive next milestone. Our GraphStage based TLS support is now almost ready. We also changed how stream-of-streams operations like groupBy are expressed in the API, making the DSL both safer, and easier to implement in non-local materialization contexts. At the same time, the algorithm to finally start fusing stages is progressing steadily to finally put the crown on top of our internals cleanup effort.

We would like to give our special thanks and kudos to Alexander Golubev, who has been continuing to contribute a steady stream of new feature to Akka Streams while we were busy working on the internals. Thanks! We also thank @2beaucoup for his continued effort in reviews and fixes in Http.

The full list of changes in this milestone is available under the streams-2.0-M2 and http-2.0-M2 milestones on github for your reference.

Migration guides from 1.0 are available for
[Java](https://doc.akka.io/docs/akka-stream-and-http-experimental/2.0-M2/java/migration-guide-1.0-2.x-java.html)
and
[Scala](https://doc.akka.io/docs/akka-stream-and-http-experimental/2.0-M2/scala/migration-guide-1.0-2.x-scala.html)

For this release we had the help of 12 committers.

    commits added removed
         18  1556     282 Konrad Malawski
         13  1278     623 Mathias
          7  2524     805 Endre Sándor Varga
          6   872      64 Johan Andrén
          5    34      20 2beaucoup
          3   946     728 Viktor Klang
          3    14       9 Roland Kuhn
          2   566      36 Alexander Golubev
          2   162      27 Johannes Rudolph
          1     0       2 Martynas Mickevicius
          1     8       6 Heiko Seeberger
          1     4       1 Tim Harper

Happy hakking,

-- The Akka Team
