---
layout: post
title: How Akka Split Brain Resolver Works - Raspberry Pi Cluster Demo
author: Alexander Golubev
short: A short video that explains and demonstrates the Split Brain Resolver on Raspberry Pi Cluster
category: news
tags: [videos]
youtubeId: hJW97fOyNI0
---

{% include youtubePlayer.html id=page.youtubeId %}

Akka Cluster and Cluster Sharding provide a powerful way to scale your microservice while staying Reactive. It does not matter if you are running your service on premise or in the cloud, Akka toolkit can work in pretty much any environment. Recently, an important component was published to the open source - the Split Brain Resolver (SBR). For many years it was successfully used by Lightbend customers and now it allows a wide community to achieve a high level of stability even on a great scale.

In this short demo with Alex Golubev, consultant at Lightbend, you’ll be able to learn basic aspects of Akka Cluster Sharding and see how it works on a Raspberry Pi cluster. We’ll consider failure scenarios where one node becomes unreachable and see how Split Brain Resolver can help us with this.

Raspberry Pi cluster assembly was developed specifically for demo purposes to reveal processes that’s going on underneath in Akka Cluster. You will be able to catch these processes easily as main pieces of information will be shown on small screens attached to each node.

For more information see the [Split Brain Resolver documentation](https://doc.akka.io/docs/akka/current/split-brain-resolver.html).

Also, learn about [Cluster Sharding in Lightbend Academy](https://academy.lightbend.com).
