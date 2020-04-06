---
layout: post
title: Akka Stream and Http 2.0.4 Released!
author: Martynas Mickevičius
short: Akka Stream and Http 2.0.4 Released!
category: news
redirect_from: /news/2016/04/01/akka-streams-2.0.4-released.html
tags: [releases]
---

*Dear Hakkers,*

we—the Akka committers—are happy to announce the fourth maintenance release of [Akka Streams & HTTP 2.0](https://akka.io/news/2015/12/21/akka-streams-2.0-released.html).
This update addresses stability issues for those who can not upgrade to Akka 2.4.x.

Fixes in this release include:

- SubSource double push ([#20157](https://github.com/akka/akka/pull/20157))
- UnzipWith double push ([#20167](https://github.com/akka/akka/pull/20167))
- FileIO publisher double onComplete ([#20168](https://github.com/akka/akka/pull/20168))
- Buffer overflow in Merge ([#20170](https://github.com/akka/akka/pull/20170))
- Sink.queue and Sink.actorRefWithAck mishandle of buffer of size one ([#20171](https://github.com/akka/akka/pull/20171))
- Sink.actorRefWithAck gets stuck when buffer runs full ([#20156](https://github.com/akka/akka/pull/20156))
- Race condition in FileSubscriber ([#20159](https://github.com/akka/akka/pull/20159))
- Fix for GraphStageLogic.readN ([#20172](https://github.com/akka/akka/pull/20172))
- Added autoflushing to OutputStream sink ([#20164](https://github.com/akka/akka/pull/20164))
- Timeouts with StreamConvertes.asInputStream ([#20165](https://github.com/akka/akka/pull/20165))
- Fix where InputBuffer attribute was not used ([#20187](https://github.com/akka/akka/pull/20187))
- Fix for content negotiation for non 2xx and Accept responses ([#20178](https://github.com/akka/akka/pull/20178))

The full list of changes since the last milestone is available under the [stream-http-2.0.4](https://github.com/akka/akka/issues?q=milestone%3Astream-http-2.0.4+is%3Aclosed) milestone on github for your reference.

The reference documentation for the 2.0.4 release can be found here for [Java](https://doc.akka.io/docs/akka/current/stream/?language=java) and [Scala](https://doc.akka.io/docs/akka/current/stream/?language=scala).

#### Credits ####

    commits added removed
          7   316     157 Roland Kuhn
          7   345      78 Johan Andrén
          5    62      16 Endre Sándor Varga
          3   142      22 Konrad Malawski
          1    80      25 Viktor Klang
          1    20       4 Alexander Golubev
          1     3       3 Martynas Mickevičius

Happy hakking,

-- The Akka Team
