---
layout: post
title: Replicated Event Sourcing data modelling
author: Christopher Batey
short: Replicated Event Sourcing data modelling 
category: news
tags: [videos]
youtubeId: 8PnJxTlOP6o
---

{% include youtubePlayer.html id=page.youtubeId %}

This is the second video about [Replicated Event Sourcing](https://doc.akka.io/docs/akka/current/typed/replicated-eventsourcing.html), new in Akka 2.6.9. 
The [first video](./2020-10-22-replicated-event-sourcing-data-modelling.md) introduced the new feature whereas this video goes into how to model your domain when using Replicated Event Sourcing.

Examples domains include:

* Back account
* Ebay style auction

With different techniques demonstrated such as:

* Using the built-in CRDTs
* Building your own CRDT like model
* Detecting conflicts and executing reconciliation actions

This video builds on top of the existing videos:

* [Cluster Sharding](https://akka.io/blog/news/2019/12/16/akka-cluster-sharding-intro-video)
* [Event Sourcing with Akka 2.6](https://akka.io/blog/news/2020/01/07/akka-event-sourcing-video)
