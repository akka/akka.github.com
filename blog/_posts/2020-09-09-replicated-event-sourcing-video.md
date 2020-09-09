---
layout: post
title: Replicated Event Sourcing video
author: Christopher Batey
short: A short video detailing the new Replicated Event Sourcing feature. 
category: news
tags: [videos]
youtubeId: SuTPY5MxmUM
---

{% include youtubePlayer.html id=page.youtubeId %}

This video introduces [Replicated Event Sourcing](https://doc.akka.io/docs/akka/current/typed/replicated-eventsourcing.html), new in Akka 2.6.9. Replicated Event Sourcing builds on top of `EventSourcedBehavior`s allowing
multiple replicas to be kept in memory within an Akka Cluster. 

The use cases for doing so are:

* Running a replica per rack/availability zone to have zero downtime during rack/availability zone outages
* Running a replica per DC for high availability
* Running a replica per DC for data locality with clients

This video builds on top of the existing videos:

* [Cluster Sharding](https://akka.io/blog/news/2019/12/16/akka-cluster-sharding-intro-video)
* [Event Sourcing with Akka 2.6](https://akka.io/blog/news/2020/01/07/akka-event-sourcing-video)
