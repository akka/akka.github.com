---
layout: post
title: Alpakka Kafka 2.0.0 Released
author: Enno Runne
short: We are happy to announce the release of Alpakka Kafka 2.0.0
category: news
tags: [releases]
---

Dear hakkers,

We are happy to announce the general availability of Alpakka Kafka 2.0.0!

Alpakka Kafka 2.0.0 uses the **Apache Kafka client 2.4.0** which contains improvements to the flow-control management that are very important to Alpakka Kafka.
A separate blog post is coming up to deep-dive and explain those changes - stay tuned.

The new `Producer.committableSink` behave as a combination of `Producer.flexiFlow` and `Committer.sink`, but has a special-purpose implementation which shows better performance in our benchmarks.

This release adds built-in support for **service discovery** with [Akka Discovery](https://doc.akka.io/docs/alpakka-kafka/2.0/discovery.html).

To reduce emitting duplicated records when partitions are revoked from Alpakka Kafka consumers, this release introduces dropping of messages when partitions are revoked.
 
The [Alpakka Kafka Testkit](https://doc.akka.io/docs/alpakka-kafka/2.0/testing.html) extends the use of [Testcontainers](https://www.testcontainers.org/) to which we contributed improvements so that is easier to start Kafka clusters for testing.

For detailed changes, please see the [release notes](https://doc.akka.io/docs/alpakka-kafka/current/release-notes/2.0.x.html) in the Alpakka Kafka documentation.

Happy hakking!

– The Alpakka Team
