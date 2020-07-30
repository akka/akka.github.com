---
layout: post
title: Split Brain Resolver in Akka Cluster
author: Patrik Nordwall
short: A short video that explains the Split Brain Resolver
category: news
tags: [videos]
youtubeId: vc6eTolxGbM
---

{% include youtubePlayer.html id=page.youtubeId %}

With the continued growth of Akka Cluster to power cloud native applications around the world, Lightbend has recognized that the timing is right to contribute the Split Brain Resolver to the open source core of Akka.

This short video explains why it is important to use a correct downing provider together with Akka Cluster Sharding. The Split Brain Resolver (SBR) makes the important decisions of what to do with crashed nodes, unhealthy nodes, and how to deal with network partitions. The video illustrates how these decisions are made.
     
The dashboard that is used in the demo is explained in Hugh McKee's video series [Akka Cluster Quickstart Dashboard](https://www.lightbend.com/blog/akka-cluster-quickstart-dashboard-part-1-getting-started).

For more information see the [Split Brain Resolver documentation](https://doc.akka.io/docs/akka/current/split-brain-resolver.html).

Also, learn about [Cluster Sharding in Lightbend Academy](https://academy.lightbend.com).
