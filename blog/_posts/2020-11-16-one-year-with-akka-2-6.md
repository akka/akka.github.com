---
layout: post
title: One year with Akka 2.6
author: Enno Runne
short: One year with Akka 2.6 
category: news
---

# One year with Akka 2.6

Just a year ago－in November 2019－we published the final release of Akka 2.6.0. Compared to the Akka 2.5.x series, it introduced new APIs to reduce pitfalls for new users and many production stability improvements.

Many more improvements continued to emerge in the patch releases of Akka 2.6.x, including the addition of completely new features. If you haven’t followed all announcements in detail, this post gives an overview of what has happened during the last year.

Note: if you are still on Akka 2.5.x or older we highly recommend that you [update to latest Akka 2.6.x](https://doc.akka.io/docs/akka/current/project/migration-guide-2.5.x-2.6.x.html). We will not backport any functionality to Akka 2.5, with the exception for important bug or security fixes in some cases.

## Akka Typed

When we released Akka 2.6, Akka Typed marked an important milestone: the “new actors API” (also known as “Akka Typed”) became ready for prime-time with a stable API. Type-safety for communication between actors eliminates a huge class of errors which made developing and maintaining solutions using the classic APIs hard at times, especially for new users. The research to implement a new API for Akka actors started much earlier, driven by [Dr. Roland Kuhn](https://twitter.com/rolandkuhn).

The [Actor introduction video](https://akka.io/blog/news/2019/12/03/akka-typed-actor-intro-video) and the [Try Akka Guide](https://akka.io/try-akka/) introduce actors in Akka from scratch. If you have used the classic actors, [Learning Akka Typed from Classic](https://doc.akka.io/docs/akka/2.6/typed/from-classic.html) will be a good starting point.

{% include youtubePlayer.html id='rIFqJxMJ1MM' %}

The new actors API allowed us to rethink many other APIs in Akka which simplified their use substantially. During this first year of Akka 2.6, we’ve continued to build new functionalities which leverage the new actors API.
For example, Akka’s API for event-sourcing and CQRS: [Akka Persistence](https://doc.akka.io/docs/akka/2.6/typed/index-persistence.html) and [Cluster Sharding](https://doc.akka.io/docs/akka/current/typed/cluster-sharding.html) became much simpler to use. To complement Akka Persistence, we created [Akka Projections](https://doc.akka.io/docs/akka-projection/current/) which makes CQRS easily available and integrated with a number of technologies (Relational Databases, Kafka, Cassandra).

{% include youtubePlayer.html id='0toyKxomdwo' %}

For new projects, we recommend using the new Actor API, but it’s important to note that the classic APIs are still fully supported and existing applications can continue to use the classic APIs. It is even possible to use the [new Actor APIs together with classic actors]((https://doc.akka.io/docs/akka/current/typed/coexisting.html)) in the same actor system.

## Akka Cluster and the Split Brain Resolver

Many more improvements around Akka Cluster were added in the subsequent releases of Akka 2.6.x. The formerly closed-source Lightbend Split Brain Resolver became part of the open source Akka project. The Split Brain Resolver lets an Akka Cluster make safe decisions about the state of the cluster when it can’t communicate as expected, for example when nodes in a cluster suddenly become unavailable, or network partitions occur. The Akka Cluster Split Brain Resolver is included in the Akka Cluster library and is enabled via configuration. [This video](https://akka.io/blog/news/2020/06/08/akka-split-brain-resolver-video) explains how the Split Brain Resolver decides which nodes are excluded from the cluster and will be stopped.
For more details see the [reference documentation](https://doc.akka.io/docs/akka/2.6/split-brain-resolver.html). 

{% include youtubePlayer.html id='vc6eTolxGbM' %}

To see the Split Brain Resolver in action, you can watch this demo presentation ["Akka Cluster Nodes Under Attack, Visualized"](https://www.lightbend.com/blog/interactive-demo-akka-cluster-nodes-under-attack-visualized) of a sample project by Hugh McKee, Developer Advocate at Lightbend. 

## Akka Cluster Sharding

To best take advantage of the full resources across all nodes in your cluster, Akka Cluster Sharding provides automatic distribution and lookup of actors. Your application can address the actors by their identifiers and is not concerned with selecting where they should run. 
Sometimes however, it is beneficial to colocate actors with sources of data such as partitions read from Kafka. This is now possible with the [External shard allocation strategies](https://doc.akka.io/docs/akka/2.6/typed/cluster-sharding.html#external-shard-allocation) which are explained in [a video](https://akka.io/blog/news/2020/03/18/akka-sharding-kafka-video). Alpakka Kafka [has support](https://doc.akka.io/docs/alpakka-kafka/current/cluster-sharding.html) to enable it.

{% include youtubePlayer.html id='Ad2DyOn4dlY' %}

Sharded actors start up in the cluster when a first message arrives to them. When an actor hasn’t received any messages for a while, Akka will passivate it. If your application has certain actors that should be around no-matter-what, you can configure [Remembering Entities](https://doc.akka.io/docs/akka/2.6/typed/cluster-sharding.html#remembering-entities) and Akka will make sure these actors stay around and are even started eagerly when the cluster starts. Performance and scalability of this feature was greatly improved in Akka 2.6.7.

Akka Cluster can make sure a certain actor runs on exactly one node within the cluster, also known as a [Cluster Singleton](https://doc.akka.io/docs/akka/2.6/typed/cluster-singleton.html). Following that idea, it is sometimes not just one actor, but a fixed number of actors that should exist in a cluster. This is now supported by [Sharded Daemon Processes](https://doc.akka.io/docs/akka/2.6/typed/cluster-sharded-daemon-process.html).

For the service that is always available, Akka Cluster supports rolling updates where the latest version of your application can join the running cluster without any downtime. When your application provides its version, Akka Cluster will be clever about where to allocate shards during a rolling update. See the [reference documentation](https://doc.akka.io/docs/akka/current/additional/rolling-updates.html) for all the details.

## Akka Cluster chatter

To make Akka Cluster work and the actor systems in many JVMs communicate efficiently, Akka implements remote communication and serialization. Since Akka 2.6.0 the default communication protocol is what we call [Artery](https://doc.akka.io/docs/akka/2.6/remoting-artery.html) which leverages Akka Streams TCP/TLS or the [Aeron transport](https://github.com/real-logic/Aeron) as an alternative.

Before Akka 2.6.0 user code could end up blocking Akka internals from operating correctly,  Artery and the use of a dedicated dispatcher for internal tasks protects Akka Clusters against that risk of thread starvation. Artery even gives much better throughput and reduced latency compared to the classic transport.

With Akka 2.6.0 the recommended serialization format is the [Jackson-based serializer that supports both JSON and the binary CBOR format](https://doc.akka.io/docs/akka/2.6/serialization-jackson.html). 

## Communicating and distributing data

Normally, messages between actors only provide at-most-once semantics. Sending data between nodes in a distributed system must always consider data loss and duplicated delivery of the same data. With [Reliable Delivery](https://doc.akka.io/docs/akka/2.6/typed/reliable-delivery.html) you can relax and leave it to Akka to get your data across the network, even for very large messages.

The common publish/subscribe communication pattern is now available within an actor system as well as within an Akka Cluster with [Distributed publish subscribe](https://doc.akka.io/docs/akka/2.6/typed/distributed-pub-sub.html).

When you have the need for data that is available and updateable on all nodes in an Akka Cluster, [Akka Distributed Data](https://doc.akka.io/docs/akka/2.6/typed/distributed-data.html) provides that based on Conflict Free Replicated Data Types (CRDTs). Internally, Akka uses this to share the state of the [Receptionist](https://doc.akka.io/docs/akka/2.6/typed/actor-discovery.html) and [Cluster aware routers](https://doc.akka.io/docs/akka/2.6/typed/routers.html) with all nodes.

## Replicated Event Sourcing

Similar to Akka Distributed Data this concept can now be extended to Akka Persistence with [Replicated Event Sourcing](https://doc.akka.io/docs/akka/2.6/typed/replicated-eventsourcing.html) which allows persisted data to be updated simultaneously in different parts of your cluster eg. different data centers. This novel concept is explained in [a video](https://akka.io/blog/news/2020/09/09/replicated-event-sourcing-video) and to help you design your data model accordingly, we get you started with [this video](https://akka.io/blog/news/2020/10/22/replicated-event-sourcing-data-modelling).

{% include youtubePlayer.html id='SuTPY5MxmUM' %}

{% include youtubePlayer.html id='8PnJxTlOP6o' %}

## Simpler Akka Streams API

When [Akka Streams](https://doc.akka.io/docs/akka/current/stream/index.html) were invented alongside the Reactive Streams standardization effort, this was a fundamentally new area. In Akka 2.6, their APIs were simplified so that the user just provides an actor system and doesn’t need to keep track of a materializer for Akka Streams.
Internally, the signalling of stream cancellation has been improved so that it distinguishes between cancellation because of an error from regular cancellation.

## From here into the future

This is just a short summary of many new features added to Akka since version 2.6.0. All other parts of Akka, such as Akka Streams and included test kits for all parts are still there as well.
Now that Akka 2.6 has been with us for more than a year, other projects in the Akka family will change to require Akka 2.6 in their next minor releases.

[Lightbend](https://www.lightbend.com/) employs the Akka core team. If you’re looking [to take your Akka systems to the next level](https://www.lightbend.com/lightbend-subscription), let’s [set up a time](https://lightbend.com/contact) to discuss our enterprise-grade expert support, self-paced education courses, and technology enhancements that help you manage, monitor and secure your Akka systems － from development to production.

Happy hakking!

– The Akka Team

### Akka 2.6.x release notes for reference

* [Akka 2.6.0](https://akka.io/blog/news/2019/11/06/akka-2.6.0-released) 
* [Akka 2.6.1](https://akka.io/blog/news/2019/12/06/akka-2.6.1-released)
* [Akka 2.6.2](https://akka.io/blog/news/2020/01/27/akka-2.6.2-released)
* [Akka 2.6.3](https://akka.io/blog/news/2020/01/28/akka-2.6.3-released)
* [Akka 2.6.4](https://akka.io/blog/news/2020/03/13/akka-2.6.4-released)
* [Akka 2.6.5](https://akka.io/blog/news/2020/04/30/akka-2.6.5-released) 
* [Akka 2.6.6](https://akka.io/blog/news/2020/06/08/akka-2.6.6-released-split-brain-resolver)
* [Akka 2.6.7](https://akka.io/blog/news/2020/07/10/akka-2.6.7-released) 
* [Akka 2.6.8](https://akka.io/blog/news/2020/07/16/akka-2.6.8-released)
* [Akka 2.6.9](https://akka.io/blog/news/2020/09/09/akka-2.6.9-released)
* [Akka 2.6.10](https://akka.io/blog/news/2020/10/09/akka-2.6.10-released) 

