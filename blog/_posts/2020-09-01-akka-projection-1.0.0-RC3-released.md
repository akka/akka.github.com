---
layout: post
title: Akka Projections 1.0.0-RC3 Released
author: Renato Cavalcanti
short: Please meet Akka Projections 1.0.0-RC3
category: news
tags: [releases]
---

Dear hakkers,

We're happy to announce the latest release candidate of Akka Projections, version 1.0.0-RC3. If no serious bug is reported this RC3 will become 1.0.0 GA next week.

We consider the API stable even though we’re still not making any bincompat promise and you should consider all the public API as ApiMayChange. It might be changed based on feedback from initial usage.

Feedback, bug reports and feature requests are welcome as issues in [akka-projection/issues](https://github.com/akka/akka-projection/issues).

Highlights of the release:

* Improved [Getting Started Guide](https://doc.akka.io/docs/akka-projection/current/getting-started/index.html), including examples in both Java and Scala.
* Fix off by one problem in `KafkaSourceProvider`, [#382](https://github.com/akka/akka-projection/issues/382)

The documentation can be found at [https://doc.akka.io/docs/akka-projection/current/](https://doc.akka.io/docs/akka-projection/current/).

A total of 22 issues were closed in this release. The complete list can be found on the [1.0.0-RC3 milestone](https://github.com/akka/akka-projection/milestone/8?closed=1) on github.

## Credits

For this release we had the help of 4 committers – thank you all very much!

```
commits   added  removed
     12    3235     1826 Sean Glover
      7     230      137 Patrik Nordwall
      1       1        1 Johan Andrén
      1      30       12 Renato Cavalcanti
```

The Akka core team is employed by [Lightbend](https://www.lightbend.com/). If you're looking [to take your Akka systems to the next level](https://www.lightbend.com/lightbend-subscription), let's [set up a time](https://lightbend.com/contact) to discuss our enterprise-grade expert support, self-paced education courses, and technology enhancements that help you manage, monitor and secure your Akka systems - from development to production.

Happy hakking!

– The Akka Team
