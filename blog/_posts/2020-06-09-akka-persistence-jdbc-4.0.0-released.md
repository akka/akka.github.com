---
layout: post
title: Akka Persistence JDBC 4.0 Released
author: Renato Cavalcanti
short: Please meet Akka Persistence JDBC 4.0
category: news
tags: [releases]
---

Dear Hakkers,

The Akka contributors are happy to announce **Akka Persistence JDBC 4.0**!

This is the first major release since the project joined the Akka organisation.

Highlights of the release:

* Use ordering Offset in eventsByPersistenceId, #91 [#385](https://github.com/akka/akka-persistence-jdbc/issues/385) by [@patriknw](https://github.com/patriknw)
* Added support for EventEnvelope timestamp [#365](https://github.com/akka/akka-persistence-jdbc/issues/365) by [@renatocaval](https://github.com/renatocaval)
* Fix eventsByTag query for tags that share a prefix #168 by [@renatocaval](https://github.com/renatocaval)
* Read events in batches from asyncReplayMessages, #322 [#370](https://github.com/akka/akka-persistence-jdbc/issues/370) by [@patriknw](https://github.com/patriknw)

You can find the documentation [here](https://doc.akka.io/docs/akka-persistence-jdbc/current/).

## Adopting Akka Persistence JDBC 4.0

No data migration is needed when updating from 3.5.3.

This version requires Akka 2.6.4 or later.

The `groupdId` changed in this release. It's now being published under the `com.lightbend.akka` groupd ID. Make sure to remove any dependency on the previous `3.5.x` artifacts that were being published under `com.github.dnvriend`. There is a runtime check that will throw an exception when loading the plugin if it detects that both artifacts are in the classpath.

There are a few source and binary incompatible changes if you have implemented custom DAOs. The compiler will detect these changes when you recompile.

There is no major behavior change in this release except for one minor bug fix. Previously, when querying using `currentEventsByPersistenceId`, the offset field in the returned `EventEnvelope` was set to the event sequence number. This was not the intended behavior. The `offset` is now being correctly filled with the event offset. If you need to have access to the event sequence number, you should access it through `EventEnvelope.sequenceNr` field.

## Akka by Lightbend

The Akka core team is employed by [Lightbend](https://www.lightbend.com/). If you’re looking [to take your Akka systems to the next level](https://www.lightbend.com/lightbend-platform-subscription), let’s [set up a time](https://lightbend.com/contact) to discuss our enterprise-grade expert support, self-paced education courses, and technology enhancements that help you manage, monitor and secure your Akka systems - from development to production.

Happy hakking

- The Akka Team