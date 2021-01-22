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

Although, it doesn't make any difference on how projections work, it is inconvennient to create schemas as such because it force queries to always use quoted and uppercased statements, eg: `select * from "AKKA_PROJECTION_OFFSET_STORE"`.

The PostgresSQL schema defaults now to unquoted lowercase, which allows queries as in `select * from akka_projection_offset_store`.

H2 requires the usage of quotes, but are now using lowercase, allowing queries as in `select * from "akka_projection_offset_store"`.

There is a backward compatibility mode in case you prefer to stay on the old schema. See migration section below. 

In addtition to that, and in the same spirit of improving the develop expericence, `JdbcProjection` and `SlickProjection` now include a `dropOffsetTableIfExists` to drop the database. Useful for testing purposes.

## Migration

As of version 1.1.0, the schema for PostgreSQL and H2 databases has changed. It now defaults to lowercase table and column names.
If you have a schema in production, we recommend applying an ALTER table script to change it accordingly.

Alternatively, if you prefer to keep using uppercase quoted names you can fallback to the legacy format.

Users of `akka-projection-jdbc` can revert to legacy format with the following configuration settings:

```hocon
akka.projection.jdbc.offset-store {
  table = "AKKA_PROJECTION_OFFSET_STORE"
  use-lowercase-schema = false
}

```hocon
Users of `akka-projection-slick` can revert to legacy format with the following configuration settings:

```hocon
akka.projection.slick.offset-store {
  table = "AKKA_PROJECTION_OFFSET_STORE"
  use-lowercase-schema = false
}
```

The full documentation can be found at [https://doc.akka.io/docs/akka-projection/current/](https://doc.akka.io/docs/akka-projection/current/).

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
