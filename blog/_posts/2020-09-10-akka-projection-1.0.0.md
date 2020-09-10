---
layout: post
title: Akka Projections 1.0.0 Released
author: Renato Cavalcanti
short: Please meet Akka Projections 1.0.0
category: news
tags: [releases]
youtubeId: 0toyKxomdwo
---

Dear hakkers,

We're proud to announce the release of Akka Projections 1.0.

## Overview

In Akka Projections you process a stream of events or records from a source to a projected model or external system. Each event is associated with an offset representing the position in the stream. This offset is used for resuming the stream from that position when the projection is restarted.

As the source you can select from:

* [Events from Akka Persistence](https://doc.akka.io/docs/akka-projection/current/eventsourced.html)
* [Messages from Kafka](https://doc.akka.io/docs/akka-projection/current/kafka.html)
* Building your own [SourceProvider](doc.akka.io/api/akka-projection/1.0.0/akka/projection/scaladsl/SourceProvider.html)

For the offset storage you can select from:

* [Offset in Cassandra](https://doc.akka.io/docs/akka-projection/current/cassandra.html)
* [Offset in a relational DB with JDBC](https://doc.akka.io/docs/akka-projection/current/jdbc.html)
* [Offset in a relational DB with Slick](https://doc.akka.io/docs/akka-projection/current/slick.html)

The full documentation can be found at [https://doc.akka.io/docs/akka-projection/current/](https://doc.akka.io/docs/akka-projection/current/).

### Introductory Video

{% include youtubePlayer.html id=page.youtubeId %}

### Supported by Lightbend

Starting at Akka Projections 1.0.0, this module is now officially
[Supported](https://developer.lightbend.com/docs/introduction/getting-help/support-terminology.html)
as part of a [Lightbend Subscription](https://www.lightbend.com/lightbend-subscription).

### API stability

We consider the API stable even though we’re still not making any bincompat promise and you should consider all the public API as [ApiMayChange](https://doc.akka.io/docs/akka/current/common/may-change.html). It might be changed based on feedback from initial usage.

### Migrating from previous Release Candidates

If you have been using a Release Candidate with JDBC or Slick, there is a small change in the offset store table. The `OFFSET` column has been renamed to `CURRENT_OFFSET`. Make sure to rename it in your existing schema before upgrading to version 1.0.0.

### Contributing

Feedback, bug reports and feature requests are welcome as issues in [akka-projection/issues](https://github.com/akka/akka-projection/issues).

## Credits

For this release we had the help of 9 committers – thank you all very much!

```
commits   added  removed
    128   16517     6609  Renato Cavalcanti
     88   18580     2529  Patrik Nordwall
     43    7784     3363  Sean Glover
     28    3278      661  Ignasi Marimon-Clos
     11     339       45  Enno Runne
      1       5        5  Evan Chan
      1      11       11  Christopher Batey
      1      39       34  Claudio Scandura
      1       1        1  Johan Andrén
```

The Akka core team is employed by [Lightbend](https://www.lightbend.com/). If you're looking [to take your Akka systems to the next level](https://www.lightbend.com/lightbend-subscription), let's [set up a time](https://lightbend.com/contact) to discuss our enterprise-grade expert support, self-paced education courses, and technology enhancements that help you manage, monitor and secure your Akka systems - from development to production.

Happy hakking!

– The Akka Team
