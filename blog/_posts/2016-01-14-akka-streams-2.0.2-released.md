---
layout: post
title: Akka Stream and Http 2.0.2 Released!
author: Konrad 'ktoso' Malawski
short: Akka Stream and Http 2.0.2 Released!
category: news
redirect_from: /news/2016/01/14/akka-streams-2.0.2-released.html
tags: [releases]
---

*Dear Hakkers,*

we—the Akka committers—are happy to announce the second maintenance release of [Akka Streams & HTTP 2.0](https://akka.io/news/2015/12/21/akka-streams-2.0-released.html). 
This minor update addresses a number of stability issues discovered recently, mostly in the HTTP and WebSocket code.

Meanwhile, we have moved all new Streams & HTTP development to the master branch, which means that these, as previously announced, 
will very soon become part of Akka 2.4, refer to the 2.0 release announcement if you are curious for details.

The most notable fixes in this release include:

- Http client causes 'Cannot pull port twice' error [#19094](https://github.com/akka/akka/issues/19094)
- Empty.head in Websockets [#19417](https://github.com/akka/akka/issues/19417)
- scalac bug when using members of MediaTypes in a map [#19418](https://github.com/akka/akka/issues/19418)

The reference documentation for the 2.0.2 release can be found here for [Java](https://doc.akka.io/docs/akka-stream-and-http-experimental/2.0.2/java.html) and [Scala](https://doc.akka.io/docs/akka-stream-and-http-experimental/2.0.2/scala.html).

The full list of changes since the last milestone is available under the [stream-http-2.0.2](https://github.com/akka/akka/issues?q=is%3Aissue+milestone%3Astream-http-2.0.2+is%3Aclosed) milestone on github for your reference.

#### Credits ####

    commits added removed
          6   287      76 Roland Kuhn
          5   173     124 Konrad Malawski
          2    99       4 Endre Sándor Varga
          1    27      30 Martynas Mickevičius

Happy hakking,

-- The Akka Team
