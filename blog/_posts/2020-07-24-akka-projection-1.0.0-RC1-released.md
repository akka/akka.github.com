---
layout: post
title: Akka Projections 1.0.0-RC1 Released
author: Renato Cavalcanti
short: Please meet Akka Projections 1.0.0-RC1
category: news
tags: [releases]
---

Dear Hakkers,

The Akka contributors are happy to announce the first release candidate of Akka Projections, version 1.0.0-RC1. If no serious bug is reported this RC1 will become GA.

We consider the API stable even though we’re still not making any bincompat promise and you should consider all the public API as ApiMayChange. It might be changed based on feedback from initial usage.

Feedback, bug reports and feature requests are welcome as issues in [akka-projection/issues](https://github.com/akka/akka-projection/issues).

Highlights of the release:

* Unified schemas for JDBC and Slick
* Support blocking in JdbcHandler start/stop
* StatusObserver fine grained callbacks
* Converged StatusObserver semantics to Telemetry: progress reports iff offset has been committed
* Fix: ClassCastException after restart when using Kafka source
* Fix: MetadataClientAdapter actor already terminated message when restarting a Projection
* Fix: JdbcSettings fails when using variable substitution
* Many documentation improvements

The documentation can be found at [https://doc.akka.io/docs/akka-projection/current/](https://doc.akka.io/docs/akka-projection/current/).

A total of 45 issues were closed in this release. The complete list can be found on the [1.0.0-RC1 milestone](https://github.com/akka/akka-projection/milestone/4?closed=1) on github.

## Credits

For this release we had the help of 4 committers – thank you all very much!

```
commits  added  removed
     24   1724     1102 Renato Cavalcanti
     24   1634      518 Ignasi Marimon-Clos
      8   1737      624 Sean Glover
      1      1        1 Patrik Nordwall
```

The Akka core team is employed by [Lightbend](https://www.lightbend.com/). If you're looking [to take your Akka systems to the next level](https://www.lightbend.com/lightbend-subscription), let's [set up a time](https://lightbend.com/contact) to discuss our enterprise-grade expert support, self-paced education courses, and technology enhancements that help you manage, monitor and secure your Akka systems - from development to production.

Happy hakking!

– The Akka Team
