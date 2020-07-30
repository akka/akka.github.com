---
layout: post
title: Alpakka Kafka 1.0-RC2 Released
author: Enno Runne
short: We are happy to announce the release of Alpakka Kafka 1.0-RC2
category: news
tags: [releases]
---

Dear hakkers,

We're now reaching the end of the [Road to Alpakka 1.0](https://akka.io/blog/news/2018/08/30/alpakka-towards-1.0) and are very close to finish things up in both the main [Alpakka project](https://doc.akka.io/docs/alpakka/current/) and Alpakka Kafka. From version 1.0 Alpakka will stay [binary-compatible](https://doc.akka.io/docs/akka/current/common/binary-compatibility-rules.html#binary-compatibility-rules) between minor releases.

We do not expect any further changes before releasing Alpakka Kafka 1.0 final!

Highlights of the new release are:

* Expose consumer and producer factories as API
* Keep to the commit refresh deadlines per partition 
* Testkit support for JUnit 5 and use of [sbt-jupiter-interface](https://github.com/maichler/sbt-jupiter-interface) internally
* Upgrade to Akka 2.5.21

Some of these changes have been developed or initiated by the community. A big thank you for everyone involved!

For detailed changes and credits take a look at the [release notes](https://doc.akka.io/docs/alpakka-kafka/current/release-notes/1.0-RC2.html) in the Alpakka Kafka connector documentation.

Happy hakking!

– The Alpakka Team
