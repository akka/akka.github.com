---
layout: post
title: Alpakka Kafka 2.1.0 Released
author: Sean Glover
short: Alpakka Kafka 2.1.0 has been released!
category: news
tags: [releases]
---

Dear hAkkers,

The Alpakka contributors are happy to announce the release of Alpakka Kafka 2.1.0 final.

Alpakka Kafka 2.1.0 uses Apache Kafka 2.7.0 and Akka 2.6 and has dropped support for Scala 2.11 releases. 
The Alpakka Kafka Testkit has had significant changes to improve Kafka testcontainers support.
Embedded Kafka is no longer available in the Testkit because it can pollute the test class path with unneeded dependencies and version conflicts.

The most notable feature of 2.1.0 is the protection against Kafka broker consumer group offset resets, by [@jyates](https://github.com/jyates).
This feature will prevent consumer group offsets from being accidentally reset to "earliest" due to several bugs in earlier versions of the Kafka broker.
To learn more read the [Unexpected consumer offset reset](https://doc.akka.io/docs/alpakka-kafka/2.1.0/errorhandling.html#unexpected-consumer-offset-reset) section of the Alpakka Kafka documentation.

## Notable changes since 2.0.7

- Scala 2.12 and 2.13 (dropped Scala 2.11) [#1102](https://github.com/akka/alpakka-kafka/issues/1102) by [@seglo](https://github.com/seglo)
- Upgrade Akka 2.6.14 and Kafka 2.7.0 [#1355](https://github.com/akka/alpakka-kafka/issues/1355) by [@seglo](https://github.com/seglo)
- Add configurable protection against server-bug induced resets [#1299](https://github.com/akka/alpakka-kafka/issues/1299) by [@jyates](https://github.com/jyates)
- Akka typed first documentation user experience [#1370](https://github.com/akka/alpakka-kafka/issues/1370) by [@seglo](https://github.com/seglo)
- The testkit no longer supports Embedded Kafka [#1114](https://github.com/akka/alpakka-kafka/issues/1114) by [@seglo](https://github.com/seglo)

### Alpakka Kafka core

- DiscoverySupport: remove deprecation [#1231](https://github.com/akka/alpakka-kafka/issues/1231) by [@ennru](https://github.com/ennru)
- Commit collector: avoid pushing twice [#1205](https://github.com/akka/alpakka-kafka/issues/1205) by [@ennru](https://github.com/ennru)
- Commit the offset when multi message is empty [#1201](https://github.com/akka/alpakka-kafka/issues/1201) by [@herzrasen](https://github.com/herzrasen)
- Add ProducerResultFactory.multiResult helper for MultiMessage [#1319](https://github.com/akka/alpakka-kafka/issues/1319) by [@ashendon](https://github.com/ashendon)

### Tests

- ScalaTest 3.1 fixes for it tests [#1210](https://github.com/akka/alpakka-kafka/issues/1210) by [@seglo](https://github.com/seglo)
- ScalaTest 3.1 with rewrites [#1207](https://github.com/akka/alpakka-kafka/issues/1207) by [@ennru](https://github.com/ennru)

### Alpakka Kafka Testkit

- Drop embedded-kafka testkit support [#1229](https://github.com/akka/alpakka-kafka/issues/1229) by [@seglo](https://github.com/seglo)
- Start & stop Kafka process within running container [#1235](https://github.com/akka/alpakka-kafka/issues/1235) by [@seglo](https://github.com/seglo)
- Default to Confluent Platform version to 6.0.0 [#1224](https://github.com/akka/alpakka-kafka/issues/1224) by [@ennru](https://github.com/ennru)
- Don't depend on scalatest in testcontainer singleton [#1330](https://github.com/akka/alpakka-kafka/issues/1330) by [@seglo](https://github.com/seglo)
- testkit: Admin instead of AdminClient [#1183](https://github.com/akka/alpakka-kafka/issues/1183) by [@ennru](https://github.com/ennru)

### Infrastructure

- Migrate to sonatype snapshot/release repositories [#1353](https://github.com/akka/alpakka-kafka/issues/1353) by [@seglo](https://github.com/seglo)
- Migrate build to GitHub actions [#1307](https://github.com/akka/alpakka-kafka/issues/1307) by [@seglo](https://github.com/seglo)

For this release we had the help of 11 committers – thank you all very much!

```
commits  added  removed
     44     52       52 Scala Steward
     35    494      275 Enno Runne
     31   2051     1810 Sean Glover
      2      4        4 Renato Cavalcanti
      1    928       74 Jesse Yates
      1    380       19 jsh
      1     93       64 Mateusz Zakarczemny
      1     21       22 Tomchakov Oleksandr
      1      8        0 Adon
      1      1        1 Krzysiek Ciesielski
      1      1        1 Johan Andrén


```


The Akka core team is employed by [Lightbend](https://www.lightbend.com/). If you're looking [to take your Akka systems to the next level](https://www.lightbend.com/lightbend-subscription), let's [set up a time](https://lightbend.com/contact) to discuss our enterprise-grade expert support, self-paced education courses, and technology enhancements that help you manage, monitor and secure your Akka systems - from development to production.

Happy hakking!

– The Akka & Alpakka Team
