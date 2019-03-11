---
layout: post
title: Akka Stream and Http 2.0.1 Released!
author: Konrad 'ktoso' Malawski
short: Akka Stream and Http 2.0.1 Released!
category: news
redirect_from: /news/2015/12/23/akka-streams-2.0.1-released.html
tags: [releases]
---

*Dear Hakkers,*

we—the Akka committers—are very pleased to bring to you a quick follow-up maintenance release  to [Akka Streams & HTTP 2.0](https://akka.io/news/2015/12/21/akka-streams-2.0-released.html) which was released earlier this week. 

This release primarily aims to resolve a bug we found shortly after releasing that affected code and directives using InputStream as Source, such as the `getFromResource` directive. Having that fixed, we pack up the 2.0.1 release and deliver it to your christmas trees, in hope that you'll give Akka Streams and HTTP a spin over the holidays – we are, as always, eagerly awaiting your feedback!

The reference documentation for the 2.0.1 release can be found here for [Java](https://doc.akka.io/docs/akka-stream-and-http-experimental/2.0.1/java.html) and [Scala](https://doc.akka.io/docs/akka-stream-and-http-experimental/2.0.1/scala.html).

The full list of changes since the last milestone is available under the [streams-2.0.1](https://github.com/akka/akka/issues?q=is%3Aissue+milestone%3Astreams-2.0.1+is%3Aclosed) and [http-2.0.1](https://github.com/akka/akka/issues?q=is%3Aissue+milestone%3Ahttp-2.0.1+is%3Aclosed) milestones on github for your reference.

#### Credits ####

We would like to thank Mathias Bogaert for swiftly trying out the latest release and submitting a small improvement to HTTP's domain model, thanks!

    commits added removed
          8   228      82 Konrad Malawski
          3   619      38 Roland Kuhn
          1    45      50 Viktor Klang
          1     1       0 Mathias Bogaert

Happy hakking,

-- The Akka Team
