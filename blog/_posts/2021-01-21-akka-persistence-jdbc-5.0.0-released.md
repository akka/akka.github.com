---
layout: post
title: Akka Persistence JDBC 5.0.0 Released
author: Renato Cavalcanti
short: We are happy to announce the 5.0.0 release of Akka Persistence JDBC
category: news
tags: [releases]
---

Dear hakkers,

We are happy to announce the 5.0.0 release of Akka Persistence JDBC.

## Changes since 4.0.0

This release includes a new journal schema with tags persisted on a separated table solving a long stand [issue](https://github.com/akka/akka-persistence-jdbc/issues/168).

The serialized events are not wrapped with Akka’s `PersistentRepr` as before but directly saved as a blob payload into the column using Akka's serialization mechanism. `PersistentRepr` is an internal Akka API and, although stable, it's preferable to not have them serialized into the journal, hence the change.
In addition to that, the serializer manifest is now saved on a separated column making it possible to inspect it using a simple SELECT statement.

[Replicated Event Sourcing](https://doc.akka.io/docs/akka/current/typed/replicated-eventsourcing.html#replicated-event-sourcing) is now fully supported by the new schema.

The new schema can only be used with **new applications**. It's not yet possible to migrate an old journal to the new schema as it requires a migration tool capable of reading the serialized representation of `PersistentRepr`. That tool [doesn’t exist yet](https://github.com/akka/akka-persistence-jdbc/issues/317). However, if you have an existing journal, you can still upgrade to 5.0.0, but configure it to use the old schema, see [Migration](https://doc.akka.io/docs/akka-persistence-jdbc/5.0.0/migration.html).

## Credits

The complete list of closed issues can be found on the [5.0.0](https://github.com/akka/akka-persistence-jdbc/milestone/10?closed=1) and [5.0.0-RC1](https://github.com/akka/akka-persistence-jdbc/milestone/7?closed=1) milestones on GitHub.

For this release we had the help of 9 contributors – thank you all very much!

```
commits  added  removed
     17   1514     1303 Enno Runne
      7    669      266 Renato Cavalcanti
      2   1505      653 Christopher Batey
      1      5        2 Patrik Nordwall
      1      5        0 Justin Pihony
      1     10        7 Kostya
      1     11       11 Lukasz Zuchowski
      1      1        1 Johan Andrén
      1      3        1 Rémy DUBOIS
```

## Akka by Lightbend

The Akka core team is employed by [Lightbend](https://www.lightbend.com/). If you’re looking [to take your Akka systems to the next level](https://www.lightbend.com/lightbend-platform-subscription), let’s [set up a time](https://lightbend.com/contact) to discuss our enterprise-grade expert support, self-paced education courses, and technology enhancements that help you manage, monitor and secure your Akka systems - from development to production.

Happy hakking!

– The Akka Team
