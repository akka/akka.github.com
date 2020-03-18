---
layout: post
title: How to co-locate Kafka Partitions with Akka Cluster Shards 
author: Christopher Batey
short: How to co-locate Kafka Partitions with Akka Cluster Shards 
category: news
tags: [videos]
youtubeId: Ad2DyOn4dlY
---

{% include youtubePlayer.html id=page.youtubeId %}

In this 14 minute video we go through how to do distributed, stateful processing with Akka Cluster Sharding and Kafka. We
demonstrate how to co-locate the consumption of Kafka partitions with the Akka Cluster Shards using the new External
Shard Allocation strategy in Akka 2.6.

The demo uses [this Akka sample](https://github.com/akka/akka-samples/tree/2.6/akka-sample-kafka-to-sharding-scala) and contains instructions for how to run everything locally.

To learn more see the documentation on the [External Shard Allocation
Strategy](https://doc.akka.io/docs/akka/current/typed/cluster-sharding.html#external-shard-allocation) and the [Alpakka
extension for cluster sharding](https://doc.akka.io/docs/alpakka-kafka/snapshot/cluster-sharding.html).

Also, read about [Akka Enhancements](https://doc.akka.io/docs/akka-enhancements) and learn more from other [Lightbend Videos and Webinars](https://www.lightbend.com/videos-and-webinars).
