---
layout: post
title: Akka Persistence Cassandra 1.0 Released
author: Christopher Batey
short: Please meet Akka Persistence Cassandra 1.-
category: news
tags: [releases]
---

Dear Hakkers,

The Akka contributors are happy to announce **Akka Persistence Cassandra 1.0**!

This marks the plugin as fully supported. Highlights of the release:

* Major upgrade of the Cassandra driver. This is an incompatible change that has led us to restructure our configuration as the new driver now uses typesafe config. Many configuration options previously in the plugin's reference.conf have been replaced by using the driver's native configuration options
* Shared database connection pool between all parts of the plugin. For historic reasons, the various parts (journal, snapshots, and query) had their own connection pool. There is also an extension so user code can also use the connection.
* Clean up tool for deleting data for persistence ids that are no longer needed
* Reconciliation tool for deleting and rebuilding the events by tag view
* Many docs improvements
* Dropping Akka 2.5 support
* Disabling keyspace/table autocreate by default. We found many users went to production with this and this isn't recommended.
* A more efficient all persistence ids query

If upgrading from a previous version you'll need to follow the steps in the migration guide: https://doc.akka.io/docs/akka-persistence-cassandra/current/migrations.html

Docs: https://doc.akka.io/docs/akka-persistence-cassandra/current/


## Akka by Lightbend

The Akka core team is employed by [Lightbend](https://www.lightbend.com/). If you’re looking [to take your Akka systems to the next level](https://www.lightbend.com/lightbend-platform-subscription), let’s [set up a time](https://lightbend.com/contact) to discuss our enterprise-grade expert support, self-paced education courses, and technology enhancements that help you manage, monitor and secure your Akka systems - from development to production.

Happy hakking,
Your Alpakkas
