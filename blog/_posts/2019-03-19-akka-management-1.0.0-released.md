---
layout: post
title: Akka Management 1.0.0 Released
author: Arnout Engelen
short: We are happy to announce the release of Akka Management 1.0.0
category: news
tags: [releases]
---

Dear hakkers,

We are happy to announce the general availability of Akka Management 1.0.0.

With this release we declare stable APIs for the core modules. The main
Discovery module, that was still part of Akka Management in 0.20.0, has even
been promoted to [the main Akka
project](https://doc.akka.io/docs/akka/current/discovery/index.html).
With the exception of the community maintained modules listed in the
[README](https://github.com/akka/akka-management#project-status), the
[Lightbend Platform Subscription](https://www.lightbend.com/lightbend-subscription)
now covers support for Akka Management.

This will make Akka Management a shared building block across the
[Lightbend Platform](https://www.lightbend.com/akka-platform),
and become the go-to mechanism for deploying and operating
[Akka](https://akka.io),
[Lagom](https://www.lagomframework.com/) and
[Play](https://www.playframework.com/) applications.

Beyond API stabilization, notable improvements include:

* Upgrade to Akka HTTP 10.1 [#281](https://github.com/akka/akka-management/issues/281)
* Upgrade to Akka 2.5.21 including using the new Akka Discovery API
* Pluggable health checks [#402](https://github.com/akka/akka-management/issues/402), including a built in one for Cluster membership
* New configuration structure for defining route providers
* Various documentation improvements and more convenient default configuration

For uses that are upgrading from earlier versions a 
[migration guide](https://doc.akka.io/docs/akka-management/current/migration.html)
is available.

– The Akka Team
