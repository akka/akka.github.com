---
layout: post
title: Alpakka Kafka 1.0-RC1 Released
author: Martynas Mickevičius
short: We are happy to announce the first release candidate of Alpakka Kafka 1.0
category: news
tags: [releases]
---

Dear hakkers,

We are pleased to announce the first release candidate of Alpakka Kafka 1.0. Highlights of the new release are:

* Alpakka Kafka connector has been upgraded to Kafka client 2.1.0 and now uses non-blocking poll API, which means no more `WakeupException`
* Introduced new `Committer.flow` API
* Testkit, which has been used in tests, is now published as a separate artifact
* Added Java API to all settings classes
* Many more bugfixes and improvements to the code as well as to the documentation

Many of these changes have been developed or initiated by the community. A big thank you for everyone involved!

For detailed changes and credits take a look at the [release notes](https://doc.akka.io/docs/alpakka-kafka/current/release-notes/1.0-RC1.html)
in the Alpakka Kafka connector documentation.

Happy hakking!

– The Alpakka Team

