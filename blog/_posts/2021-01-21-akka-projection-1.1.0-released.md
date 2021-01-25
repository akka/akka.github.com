---
layout: post
title: Akka Projections 1.1.0 Released
author: Renato Cavalcanti
short: We are happy to announce the 1.1.0 release of Akka Projections
category: news
tags: [releases]
---

Dear hakkers,

We're happy to announce the release of Akka Projections 1.1.0.

## Changes since 1.0.0

This release includes some changes on for the JDBC and Slick schema for PostgresSQL and H2 databases. The previous version was generating the schema using quoted and uppercased table and column names.

Although, it doesn't make any difference on how projections work, it is inconvenient to create schemas as such because it force queries to always use quoted and uppercased statements, eg: `select * from "AKKA_PROJECTION_OFFSET_STORE"`.

There is a backward compatibility mode in case you prefer to stay on the old schema. More details on the documentaion schema sections for [JDBC](https://doc.akka.io/docs/akka-projection/1.1.0/jdbc.html#schema) and [Slick](https://doc.akka.io/docs/akka-projection/1.1.0/slick.html#schema). 

In addtition to that, and in the same spirit of improving the develop expericence, `JdbcProjection` and `SlickProjection` now include a `dropOffsetTableIfExists` to drop the database. Useful for testing purposes.

The full documentation can be found at [https://doc.akka.io/docs/akka-projection/1.1.0/](https://doc.akka.io/docs/akka-projection/1.1.0/).

## API stability

We consider the API stable even though we’re still not making any bincompat promise and you should consider all the public API as [ApiMayChange](https://doc.akka.io/docs/akka/current/common/may-change.html). It might be changed based on feedback from initial usage.

### Contributing

Feedback, bug reports and feature requests are welcome as issues in [akka-projection/issues](https://github.com/akka/akka-projection/issues).

## Credits

For this release we had the help of 5 committers – thank you all very much!

```
commits   added  removed
      9     184      111 Patrik Nordwall
      7     676      381 Renato Cavalcanti
      4     139       73 Sean Glover
      2      37        7 Enno
      1      27        0 Ignasi Marimon-Clos
```

The Akka core team is employed by [Lightbend](https://www.lightbend.com/). If you're looking [to take your Akka systems to the next level](https://www.lightbend.com/lightbend-subscription), let's [set up a time](https://lightbend.com/contact) to discuss our enterprise-grade expert support, self-paced education courses, and technology enhancements that help you manage, monitor and secure your Akka systems - from development to production.

Happy hakking!

– The Akka Team
