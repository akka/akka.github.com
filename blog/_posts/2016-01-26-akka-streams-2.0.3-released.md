---
layout: post
title: Akka Stream and Http 2.0.3 Released!
author: Martynas Mickevičius
short: Akka Stream and Http 2.0.3 Released!
category: news
redirect_from: /news/2016/01/26/akka-streams-2.0.3-released.html
tags: [releases]
---

*Dear Hakkers,*

we—the Akka committers—are happy to announce the third maintenance release of [Akka Streams & HTTP 2.0](https://akka.io/news/2015/12/21/akka-streams-2.0-released.html).
This minor update addresses a couple of stability issues discovered recently for those who can not upgrade to [Akka 2.4.2-RC1](https://akka.io/news/2016/01/26/akka-2.4.2-RC1-released.html).

Fixes in this release include:

- Http client causes 'Cannot pull closed port' error [#19503](https://github.com/akka/akka/issues/19503)
- Akka HTTP WebSocket connections reset [#19467](https://github.com/akka/akka/issues/19467)
- Missing convenience class ContentTypes for javadsl [#18600](https://github.com/akka/akka/issues/18600)

The reference documentation for the 2.0.3 release can be found here for [Java](https://doc.akka.io/docs/akka-stream-and-http-experimental/2.0.3/java.html) and [Scala](https://doc.akka.io/docs/akka-stream-and-http-experimental/2.0.3/scala.html).

The full list of changes since the last milestone is available under the [stream-http-2.0.3](https://github.com/akka/akka/issues?q=is%3Aissue+milestone%3Astream-http-2.0.3+is%3Aclosed) milestone on github for your reference.

#### Credits ####

    commits added removed
          2    32      28 Roland Kuhn
          1     1       1 William Bowling
          1     5       5 Martynas Mickevičius
          1    43      23 Tim Harper

Happy hakking,

-- The Akka Team
