---
layout: page-full-width
title: Community Projects
---

Akka has a vibrant and passionate user community, the members of which have
created many independent projects using Akka as well as extensions to it.

The list on this page is contributed and maintained by the community, so
credit, blame, feature requests and so on should be directed at the respective
individuals or projects. Discussion about these projects is very welcome on the
[akka-dev mailing list](https://groups.google.com/forum/#!forum/akka-dev),
especially if the project in question does not have a forum of its own.

If you find something that is outdated or missing please submit a pull request by
[editing](https://github.com/akka/akka.github.com/edit/master/community/index.md)
this page, following the style shown in the example below.  We welcome all
entries, but we also reserve the right to remove entries for any reason (for
example due to the project being dead or in violation of applicable law, but
we also do not tolerate any kind of abusive behavior).  Being listed here does
not represent an endorsement by or affiliation with Typesafe, Inc. or the Akka
open-source project.

### Example Section

* **[Example Project Name](https://github.com/akka)** (by [Akka Team](https://github.com/akka))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>0.5</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.0</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.typesafe.akka" % "akka-actor_2.10" % "2.3.0"</code> (March 5, 2014)</td></tr>
  </table>

  Here you are very welcome to add a description of what the project or
  extension does, notices on where the artifacts are published, etc. The
  artifact listed under Latest Release should always be given in the Maven
  normal form (i.e. without the Scala binary version shortcut of using `%%`).
  It is also very good to include a link to the bug tracker, discussion forum
  and so on.

## Extensions to Akka

* **[flow](https://github.com/jodersky/flow)** (by [Jakob Odersky](https://github.com/jodersky))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.2.0</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.10</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.github.jodersky" % "flow_2.10" % "2.1.1"</code> (May 6, 2015)<br/><code>"com.github.jodersky" % "flow_2.11" % "2.1.1"</code> (May 6, 2015)</td></tr>
  </table>

  Akka-IO extension for serial communication.

* **[Nucleus: RPC-like reactive system build on Akka IO](https://github.com/gideondk/nucleus)** (by [Gideon de Kok](https://github.com/gideondk))

  <table class="stats">
  <table class="stats">
    <tr><td>Since Akka version:</td><td>n/a</td></tr>
    <tr><td>Latest Akka version:</td><td>2.3.0</td></tr>
    <tr><td>Latest Release:</td><td><code>"nl.gideondk" %% "nucleus" % "0.1.2"</code> (March 12, 2014)</td></tr>
  </table>
  </table>

  Published to: `https://raw.github.com/gideondk/gideondk-mvn-repo/master`

* **[Sentinel: toolkit for creating reactive TCP clients & servers](https://github.com/gideondk/sentinel)** (by [Gideon de Kok](https://github.com/gideondk))

  <table class="stats">
  <table class="stats">
    <tr><td>Since Akka version:</td><td>n/a</td></tr>
    <tr><td>Latest Akka version:</td><td>2.3.0</td></tr>
    <tr><td>Latest Release:</td><td><code>"nl.gideondk" %% "sentinel" % "0.6.0"</code> (March 12, 2014)</td></tr>
  </table>
  </table>

  Published to: `https://raw.github.com/gideondk/gideondk-mvn-repo/master`

* **[rxmon: Monitoring akka applications with rx](https://github.com/vigdorchik/rxmon)** (by [Eugene Vigdorchik](https://github.com/vigdorchik))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.2.4</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.0</td></tr>
  <tr><td>Latest Release:</td><td><code>"org.matmexrhino" % "rxmon_2.10" % "0.2.1"</code> (March 12 2014)</td></tr>
  </table>

  Published to bintray at "http://dl.bintray.com/vigdorchik/maven".

* **[Akka Tracing](https://github.com/levkhomich/akka-tracing/wiki)** (by [Lev Khomich](https://github.com/levkhomich))

  <table class="stats">
    <tr><td>Since Akka version:</td><td>2.3.0</td></tr>
    <tr><td>Latest Akka version:</td><td>2.3.8</td></tr>
    <tr><td>Latest Release:</td><td><code>"com.github.levkhomich" %% "akka-tracing-core" % "0.4"</code> (December 25, 2014)</td></tr>
  </table>

* **[Akka Zookeeper Cluster Seed: bootstrap your akka cluster seed nodes via zookeeper](https://github.com/sclasen/akka-zk-cluster-seed)** (by [Scott Clasen](https://github.com/sclasen))

  <table class="stats">
    <tr><td>Since Akka version:</td><td>2.3.3</td></tr>
    <tr><td>Latest Akka version:</td><td>2.3.3</td></tr>
    <tr><td>Latest Release:</td><td><code>"com.sclasen" %% "akka-zk-cluster-seed" % "0.0.5"</code> (June 3, 2014)</td></tr>
  </table>  

* **[Akka Data Replication](https://github.com/patriknw/akka-data-replication)** (by [Patrik Nordwall](https://github.com/patriknw))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.3</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.9</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.github.patriknw" %% "akka-data-replication" % "0.11"</code> (April 12, 2015)</td></tr>
  </table>

  A replicated in-memory data store and library of Conflict Free Replicated Data Types (CRDT).

  Published to bintray URL `http://dl.bintray.com/patriknw/maven`.

* **[akka-log4j](https://github.com/hseeberger/akka-log4j)** (by [Heiko Seeberger](http://heikoseeberger.de))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.11</td></tr>
  <tr><td>Latest Akka version:</td><td>2.4.3</td></tr>
  <tr><td>Latest Release:</td><td><code>"de.heikoseeberger" %% "akka-log4j" % "1.1.3"</code> (April 1, 2016)</td></tr>
  </table>

  akka-log4j is a logging backend implementation for Akka based on Log4j 2. It is an alternative to the official akka-slf4j backend which uses SLF4J.

  Published to Maven Central and bintray at http://dl.bintray.com/hseeberger/maven.

* **[ConstructR](https://github.com/hseeberger/constructr)** (by [Heiko Seeberger](http://heikoseeberger.de))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.14</td></tr>
  <tr><td>Latest Akka version:</td><td>2.4.2</td></tr>
  <tr><td>Latest Release:</td><td><code>"de.heikoseeberger" %% "constructr-akka" % "0.9.1"</code> (February 17, 2016)</td></tr>
  </table>

  ConstructR aims at cluster bootstrapping (construction) by using a coordination service. Currently it provides libraries for bootstrapping Akka and Cassandra clusters via etcd and Consul.

  Published to Maven Central and bintray at http://dl.bintray.com/hseeberger/maven.

* **[Akka-DNS](https://github.com/ilya-epifanov/akka-dns)** (by [Ilya Epifanov](https://github.com/ilya-epifanov))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.4.0</td></tr>
  <tr><td>Latest Akka version:</td><td>2.4.7</td></tr>
  <tr><td>Latest Release:</td><td><code>"ru.smslv.akka" %% "akka-dns" % "2.4.0"</code> (June 04, 2016)</td></tr>
  </table>

  Provides an asynchronous DNS resolution using plain Akka actors. See example at https://github.com/ilya-epifanov/akka-dns/blob/master/README.md

  Published to Maven Central and bintray at https://dl.bintray.com/hajile/maven.

## Extensions to Akka HTTP

* **[akka-http-json](https://github.com/hseeberger/akka-http-json)** (by [Heiko Seeberger](http://heikoseeberger.de))

  <table class="stats">
  <tr><td>Since Akka HTTP version:</td><td>1.0-RC3</td></tr>
  <tr><td>Latest Akka version:</td><td>2.4.3</td></tr>
  <tr><td>Latest Releases:</td><td>
    <ul>
      <li><code>"de.heikoseeberger" %% "akka-http-argonaut" % "1.5.3"</code> (April 1, 2016)</li>
      <li><code>"de.heikoseeberger" %% "akka-http-circe" % "1.5.3"</code> (April 1, 2016)</li>
      <li><code>"de.heikoseeberger" %% "akka-http-json4s" % "1.5.3"</code> (April 1, 2016)</li>
      <li><code>"de.heikoseeberger" %% "akka-http-play-json" % "1.5.3"</code> (April 1, 2016)</li>
      <li><code>"de.heikoseeberger" %% "akka-http-upickle" % "1.5.3"</code> (April 1, 2016)</li>
    </ul>
  </td></tr>
  </table>

  akka-http-json provides JSON (un)marshalling support for Akka HTTP. It offers support for the following JSON libraries:
  - Argonaut
  - circe
  - Json4s
  - Play JSON
  - uPickle

  Published to Maven Central and bintray at http://dl.bintray.com/hseeberger/maven.

* **[akka-sse](https://github.com/hseeberger/akka-sse)** (by [Heiko Seeberger](http://heikoseeberger.de))

  <table class="stats">
  <tr><td>Since Akka HTTP version:</td><td>1.0-RC3</td></tr>
  <tr><td>Latest Akka version:</td><td>2.4.3</td></tr>
  <tr><td>Latest Releases:</td><td><code>"de.heikoseeberger" %% "akka-sse" % "1.7.2"</code> (April 1, 2016)</td></tr>
  </table>

  akka-sse adds support for Server-Sent Events (SSE) to Akka HTTP.

  Published to Maven Central and bintray at http://dl.bintray.com/hseeberger/maven.


* **[akka-http-extensions](https://github.com/denigma/akka-http-extensions)** (by [Anton Kulaga](https://github.com/antonkulaga))

  <table class="stats">
  <tr><td>Since Akka HTTP version:</td><td>1.0-RC2</td></tr>
  <tr><td>Latest Akka HTTP version:</td><td>1.0</td></tr>
  <tr><td>Latest Releases:</td><td>
    <ul>
      <li><code>"org.denigma" %% "akka-http-extensions" % "0.4"</code> (July 16, 2015)</li>
    </ul>
  </td></tr>
  </table>

  akka-http-extensions provide useful directives and utils for akka-http projects like login (with password encryption), cookie-based authorization/authentication and P-JAX.

  Published to bintray at https://bintray.com/denigma/denigma-releases/.


* **[akka-http-session](https://github.com/softwaremill/akka-http-session)** (by [Softwaremill](https://github.com/softwaremill))

  <table class="stats">
  <tr><td>Since Akka HTTP version:</td><td>1.0-RC4</td></tr>
  <tr><td>Latest Akka HTTP version:</td><td>1.0-RC4</td></tr>
  <tr><td>Latest Releases:</td><td>
    <ul>
      <li><code>"com.softwaremill" %% "akka-http-session" % "0.1.1"</code> (July 12, 2015)</li>
    </ul>
  </td></tr>
  </table>

  akka-http-session contains directives for client-side, cookie-based session handling (with type-safe, signed, optionally expiring and optionally encrypted session data), CSRF protection and "remember me" support via persistent cookies.

  Published to Maven Central.

* **[akka-http-cors](https://github.com/lomigmegard/akka-http-cors)** (by [Lomig Mégard](http://lomig.megard.ch/))

  <table class="stats">
  <tr><td>Since Akka HTTP version:</td><td>2.4.2</td></tr>
  <tr><td>Latest Akka HTTP version:</td><td>10.0.0-RC2</td></tr>
  <tr><td>Latest Releases:</td><td><code>"ch.megard" %% "akka-http-cors" % "0.1.9"</code> (November 10, 2016)</td></tr>
  </table>

  akka-http-cors adds support for the [CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) mechanism to Akka HTTP (server-side only). In particular, the [specification](https://www.w3.org/TR/cors/) written by the W3C is implemented by new directives, enabling cross origin requests in your application.

  Published to Maven Central.

## Extensions to Spray

The Spray project is in the process of being merged into Akka as the new HTTP
module, see also [this news
item](http://www.typesafe.com/blog/typesafe-gets-sprayed). We also track
extensions to Spray on this page to make it clear that the transition of Spray does not
mean that these will be abandoned, we intend to support the merger of the Spray
and Akka eco-systems. The details remain to be determined, but there will be a
migration path from Spray 1.3 to Akka HTTP.

* **[binarycamp/spray-contrib](https://bitbucket.org/binarycamp/spray-contrib/src)** (by [Marcel Mojzis, Lukas Kostial](https://bitbucket.org/binarycamp))

  <table class="stats">
  <tr><td>Since spray version:</td><td>1.3.0</td></tr>
  <tr><td>Latest spray version:</td><td>1.3.0</td></tr>
  <tr><td>Latest Release:</td><td>none (to be published locally)</td></tr>
  </table>

  Extension modules built on top of spray.io: session management, OAuth 2.0 server (preview)


* **[spray-aws – Spray AWS client](https://github.com/sclasen/spray-aws)** (by [Scott Clasen](https://github.com/sclasen))

  spray-client based clients for dynamodb, kinesis, and sqs.

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.1.0</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.0</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.sclasen" % "spray-aws" % "0.2.5"</code> (March 12, 2014)</td></tr>
  </table>

  Published to Maven Central. Snapshots at the URL `https://oss.sonatype.org/content/repositories/snapshots`.

* **[Spray WebSocket](https://github.com/wandoulabs/spray-websocket)** (by [Wandou Labs](https://github.com/wandoulabs))

  <table class="stats">
  <tr><td>Since spray version:</td><td>1.3.0</td></tr>
  <tr><td>Latest spray version:</td><td>1.3.0</td></tr>
  <tr><td>Latest Release:</td><td>0.1 (to be published locally) </td></tr>
  </table>

  WebSocket implementation for Spray. Passed all Autobahn test cases (both for "ws" and "wss")

* **[Spray socket.io](https://github.com/wandoulabs/spray-socketio)** (by [Wandou Labs](https://github.com/wandoulabs))

  <table class="stats">
  <tr><td>Since spray version:</td><td>1.3.0</td></tr>
  <tr><td>Latest spray version:</td><td>1.3.0</td></tr>
  <tr><td>Latest Release:</td><td>0.1 (to be published locally) </td></tr>
  </table>

  socket.io implementation on Spray with cluster sharding.

* **[spray-funnel](https://github.com/galarragas/spray-funnel)** (by [Stefano Galarraga](https://github.com/galarragas))

  <table class="stats">
  <tr><td>Since spray version:</td><td>1.2.0</td></tr>
  <tr><td>Latest spray version:</td><td>1.3.1</td></tr>
  <tr><td>Latest Release:</td><td>1.1</td></tr>
  </table>

  Throttling system for AKKA actors that has been specifically designed to support Request-Reply patterns and in particular for HttpRequest - HttpReply interactions via Spray. Can be used with Spray Client or Spray HTTP Server.

* **[spray-extensions](https://github.com/Jarlakxen/spray-extensions)** (by [Facundo Viale](https://github.com/Jarlakxen/))

  <table class="stats">
  <tr><td>Since spray version:</td><td>1.2.1</td></tr>
  <tr><td>Latest spray version:</td><td>1.2.1</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.github.jarlakxen" %% "spray-extensions" % "1.0"</code> (May 26, 2014)</td></tr>
  </table>

  Useful extensions for spray: pagination, [scalate](http://scalate.fusesource.org/) integration, json filtering, etc.


## Plugins to Akka Persistence

Before adding a plugin to this list, it must be tested for compatibility using the [Plugin Technology Compatibility Kit](http://doc.akka.io/docs/akka/current/scala/persistence.html#Plugin_TCK) available for each release of Akka Persistence.

### Journal plugins

* **[Android SQLite plugin](https://github.com/leafme/akka-persistence-android)** (by [Leaf](https://github.com/leafme))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.8</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.8</td></tr>
  <tr><td>Latest Release:</td><td><code>"me.leaf" % "akka-persistence-android_2.11" % "0.4"</code></td></tr>
  </table>

  Published to Maven Central. Compliant with [`akka-persistence-tck`](http://doc.akka.io/docs/akka/2.3.8/scala/persistence.html#plugin-tck).

* **[BDB plugin](https://github.com/bseibel/akka-persistence-bdb)** (by [Brandon Seibel](https://github.com/bseibel))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.0-RC1</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.1</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.github.bseibel" % "akka-persistence-bdb_2.10" % "1.0"</code></td></tr>
  </table>

  Published to bintray at the URL `http://dl.bintray.com/bseibel/release`.

* **[Cassandra plugin](https://github.com/akka/akka-persistence-cassandra)** (by [Martin Krasser](https://github.com/krasserm), Typesafe and contributors)

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.0</td></tr>
  <tr><td>Latest Akka version:</td><td>2.4.2</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.typesafe.akka" %% "akka-persistence-cassandra" % "0.10"</code></td></tr>
  </table>

  Published to maven central.

  Compliant with Akka Persistence TCK 2.4.2.

  Implements Persistence Queries: allPersistenceIds, eventsByPersistenceId, eventsByTag

* **[Kafka plugin](https://github.com/krasserm/akka-persistence-kafka/)** (by [Martin Krasser](https://github.com/krasserm))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.4</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.11</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.github.krasserm" % "akka-persistence-kafka_2.10" % "0.4"</code><br><code>"com.github.krasserm" % "akka-persistence-kafka_2.11" % "0.4"</code></td></tr>
  </table>

  Published to bintray at the URL `http://dl.bintray.com/krasserm/maven`.

  Compliant with <code>akka-persistence-testkit 0.3.4</code>.

* **[DynamoDB plugin](https://github.com/sclasen/akka-persistence-dynamodb/)** (by [Scott Clasen](https://github.com/sclasen))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.0-RC4</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.0</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.sclasen" % "akka-persistence-dynamodb_2.10" % "0.1"</code> (March 6, 2014)</td></tr>
  </table>

  Published to Maven Central. Snapshots at the URL `https://oss.sonatype.org/content/repositories/snapshots`.

* **[HBase plugin](https://github.com/ktoso/akka-persistence-hbase/)** (by [Konrad Malawski](https://github.com/ktoso))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3-SNAPSHOT</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.4</td></tr>
  <tr><td>Latest Release:</td><td><code>"pl.project13.scala" % "akka-persistence-hbase_2.10" % "0.4.1"</code><br/>
                                  <code>"pl.project13.scala" % "akka-persistence-hbase_2.11" % "0.4.1"</code></td></tr>
  </table>

* **[MapDB plugin](https://github.com/drexin/akka-persistence-mapdb/)** (by [Dario Rexin](https://github.com/drexin))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.0-RC1</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.0-RC1</td></tr>
  <tr><td>Latest Release:</td><td>none (to be published locally)</td></tr>
  </table>

* **[MongoDB plugin](https://github.com/ironfish/akka-persistence-mongo/)** (by [Duncan DeVore](https://github.com/ironfish))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.0</td></tr>
  <tr><td>Latest Akka version:</td><td>2.4.1</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.github.ironfish" % "akka-persistence-mongo_2.11"  % "1.0.0-SNAPSHOT"</code></td></tr></table>

  Published to Maven Central. Snapshots at [Maven Central Snapshots](https://oss.sonatype.org/content/repositories/snapshots/com/github/ironfish/akka-persistence-mongo_2.11/)

  [README.md](https://github.com/ironfish/akka-persistence-mongo/blob/master/README.md)

  [Issues](https://github.com/ironfish/akka-persistence-mongo/issues)

  Compliant with [Plugin TCK](http://doc.akka.io/docs/akka/current/scala/persistence.html#Plugin_TCK).

* **[MongoDB plugin](https://github.com/scullxbones/akka-persistence-mongo/)** (by [Brian Scully](https://github.com/scullxbones))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.0</td></tr>
  <tr><td>Latest Akka version:</td><td>2.4.0</td></tr>
  <tr><td>Latest Release:</td>
  <td>One of:
  <code>"com.github.scullxbones" % "akka-persistence-mongo-casbah_2.11" % "1.0.6"</code><br/>
  <code>"com.github.scullxbones" % "akka-persistence-mongo-rxmongo_2.11" % "1.0.6"</code>
  </td></tr></table>

  Published to Maven Central.  Compliant with latest plugin compatibility testkit.  

  [README](https://github.com/scullxbones/akka-persistence-mongo/blob/master/README.md)

  [Bug tracker](https://github.com/scullxbones/akka-persistence-mongo/issues)

* **[Couchbase plugin](https://github.com/Product-Foundry/akka-persistence-couchbase)** (by [Product Foundry](https://github.com/Product-Foundry))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.4.2</td></tr>
  <tr><td>Latest Akka version:</td><td>2.4.2</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.productfoundry" % "akka-persistence-couchbase_2.11" % "0.3"</code></td></tr>
  </table>

  Published to bintray at the URL `http://dl.bintray.com/productfoundry/maven`.

  Compliant with `akka-persistence-tck`.

* **[In-Memory plugin for testing](https://github.com/michaelpisula/akka-journal-inmemory)** (by [Michael Pisula](https://github.com/michaelpisula))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.0</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.0</td></tr>
  <tr><td>Latest Release:</td><td>"com.github.michaelpisula" %% "akka-persistence-inmemory" % "0.1.1" (May 09, 2014)</td></tr>
  </table>

* **[InMemory plugin](https://github.com/dnvriend/akka-persistence-inmemory)** (by [Dennis Vriend](https://github.com/dnvriend))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.4</td></tr>
  <tr><td>Latest Akka version:</td><td>2.4.2</td></tr>
  <tr>
    <td>Latest Release:</td>
      <td><code>"com.github.dnvriend" %% "akka-persistence-inmemory" % "1.2.8"</code> (March 3, 2016)</td>
  </tr>
  </table>

  Published to bintray at the URL `http://dl.bintray.com/dnvriend/maven`.

  Compliant with `akka-persistence-tck`.

* **[In-Memory/Mock plugin](https://github.com/pnerg/akka-persistence-mock)** (by [Peter Nerg](https://github.com/pnerg))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.4-M3</td></tr>
  <tr><td>Latest Akka version:</td><td>2.4.0</td></tr>
  <tr>
    <td>Latest Release:</td>
      <td><code>"org.dmonix.akka" %% "akka-persistence-mock" % "1.1"</code> (Oct 15, 2015)</td>
  </tr>
  </table>

  Published to [Maven Central](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22org.dmonix.akka%22).

  Compliant with [`akka-persistence-tck`](http://doc.akka.io/docs/akka/2.4-M3/scala/persistence.html#plugin-tck).

* **[JDBC plugin](https://github.com/dnvriend/akka-persistence-jdbc)** (by [Dennis Vriend](https://github.com/dnvriend))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.2</td></tr>
  <tr><td>Latest Akka version:</td><td>2.4.2</td></tr>
  <tr>
    <td>Latest Release:</td>
    <td>
      <td><code>"com.github.dnvriend" %% "akka-persistence-jdbc" % "2.2.11"</code> (March 9, 2016)</td>
    </td>
  </tr>
  </table>

  Published to bintray at the URL `http://dl.bintray.com/dnvriend/maven`.

  Compliant with `akka-persistence-tck`.

* **[MySQL & PostgreSQL plugin](https://github.com/okumin/akka-persistence-sql-async)** (by [okumin](https://github.com/okumin))

  <table class="stats">
    <tr><td>Since Akka version:</td><td>2.3.6</td></tr>
    <tr><td>Latest Akka version:</td><td>2.4.0</td></tr>
    <tr><td>Latest Release:</td><td><code>"com.okumin" %% "akka-persistence-sql-async" % "0.3.0"</code></td></tr>
  </table>

  Published to Maven Central. Compliant with [`akka-persistence-tck`](http://doc.akka.io/docs/akka/2.4.0/scala/persistence.html#plugin-tck).

* **[Shared In-Memory Plugin](https://github.com/jdgoldie/akka-persistence-shared-inmemory/)** (by [Joshua Goldie](https://github.com/jdgoldie))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.6</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.6</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.github.jdgoldie" % "akka-persistence-shared-inmemory_2.10" % "1.0.15"</code><br><code>"com.github.jdgoldie" % "akka-persistence-shared-inmemory_2.11" % "1.0.15"</code></td></tr>
  </table>

  Published to bintray at the URL `http://dl.bintray.com/jdgoldie/maven`.

  In-memory implementations of the journal and snapshot store designed to work with multi-node and single-node unit tests.  Tested against `akka-persistence-tck-experimental` version 2.3.6

* **[EventStore plugin](https://github.com/EventStore/EventStore.Akka.Persistence)** (by [Yaroslav Klymko](https://github.com/t3hnar))

  <table class="stats">
    <tr><td>Since Akka version:</td><td>2.3.2</td></tr>
    <tr><td>Latest Akka version:</td><td>2.3.6</td></tr>
    <tr><td>Latest Release:</td><td><code>"com.geteventstore" %% "akka-persistence-eventstore" % "1.1.0"</code> (Oct 19, 2014)</td></tr>
  </table>

  Published to Maven Central. Tested against [`akka-persistence-testkit`](https://github.com/krasserm/akka-persistence-testkit).


* **[Chronicle plugin](https://github.com/carrot-garden/akka-persistence-chronicle)** (by [Andrei Pozolotin](https://github.com/Andrei-Pozolotin))

  <table class="stats">
    <tr><td>Since Akka version:</td><td>2.4.0</td></tr>
    <tr><td>Latest Akka version:</td><td>2.4.0</td></tr>
    <tr><td>Latest Release:</td><td><code>"com.carrotgarden.akka" %% "akka-persistence-chronicle" % "1.1.0"</code> (Release TBD)</td></tr>
  </table>

  Artifacts on [Maven Central](http://search.maven.org/#search%7Cga%7C1%7Cakka-persistence-chronicle). Compliant with [`akka-persistence-tck`](http://doc.akka.io/docs/akka/snapshot/scala/persistence.html#plugin-tck).

* **[Redis plugin](https://github.com/hootsuite/akka-persistence-redis)** (by [Hootsuite](https://github.com/hootsuite/) and contributors)

  <table class="stats">
    <tr><td>Since Akka version:</td><td>2.3.12</td></tr>
    <tr><td>Latest Akka version:</td><td>2.4.0</td></tr>
    <tr><td>Latest Release:</td><td><code>"com.hootsuite" %% "akka-persistence-redis" % "0.3.0"</code></td></tr>
  </table>

  Published to [Bintray](https://bintray.com/hootsuite/maven/akka-persistence-redis/view). Compliant with [`akka-persistence-tck`](http://doc.akka.io/docs/akka/2.4.0/scala/persistence.html#plugin-tck).

* **[Titan plugin](https://github.com/acflorea/akka-persistence-titan)** (by [Adrian FLOREA](https://github.com/acflorea/))

  <table class="stats">
    <tr><td>Since Akka version:</td><td>2.4.0</td></tr>
    <tr><td>Latest Akka version:</td><td>2.4.1</td></tr>
    <tr><td>Latest Release:</td><td><code>"com.github.acflorea" %% "akka-persistence-titan" % "1.0.0"</code></td></tr>
  </table>

  Artifacts on [Maven Central](http://search.maven.org/#search%7Cga%7C1%7Cakka-persistence-titan). Compliant with [`akka-persistence-tck`](http://doc.akka.io/docs/akka/snapshot/scala/persistence.html#plugin-tck).

### Snapshot plugins

* **[Android SQLite plugin](https://github.com/leafme/akka-persistence-android)** (by [Leaf](https://github.com/leafme))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.8</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.8</td></tr>
  <tr><td>Latest Release:</td><td><code>"me.leaf" % "akka-persistence-android_2.11" % "0.4"</code></td></tr>
  </table>

  Published to Maven Central. Compliant with [`akka-persistence-tck`](http://doc.akka.io/docs/akka/2.3.8/scala/persistence.html#plugin-tck).

* **[HBase plugin](https://github.com/ktoso/akka-persistence-hbase/)** (by [Konrad Malawski](https://github.com/ktoso))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3-SNAPSHOT</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.4</td></tr>
  <tr><td>Latest Release:</td><td><code>"pl.project13.scala" % "akka-persistence-hbase_2.10" % "0.4.1"</code><br/>
                                  <code>"pl.project13.scala" % "akka-persistence-hbase_2.11" % "0.4.1"</code></td></tr>
  </table>

* **[MongoDB plugin](https://github.com/scullxbones/akka-persistence-mongo/)** (by [Brian Scully](https://github.com/scullxbones))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.0</td></tr>
  <tr><td>Latest Akka version:</td><td>2.4.0</td></tr>
  <tr><td>Latest Release:</td>
  <td>One of:
  <code>"com.github.scullxbones" % "akka-persistence-mongo-casbah_2.11" % "1.0.6"</code><br/>
  <code>"com.github.scullxbones" % "akka-persistence-mongo-rxmongo_2.11" % "1.0.6"</code>
  </td></tr></table>

  Published to Maven Central.  Compliant with latest plugin compatibility testkit.  

  [README](https://github.com/scullxbones/akka-persistence-mongo/blob/master/README.md)

  [Bug tracker](https://github.com/scullxbones/akka-persistence-mongo/issues)

* **[MongoDB plugin](https://github.com/ironfish/akka-persistence-mongo/)** (by [Duncan DeVore](https://github.com/ironfish))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.0</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.5</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.github.ironfish" % "akka-persistence-mongo_2.11"  % "1.0.0-SNAPSHOT"</code></td></tr></table>

  Published to Maven Central. Snapshots at [Maven Central Snapshots](https://oss.sonatype.org/content/repositories/snapshots/com/github/ironfish/akka-persistence-mongo_2.11/)

  [README.md](https://github.com/ironfish/akka-persistence-mongo/blob/master/README.md)

  [Issues](https://github.com/ironfish/akka-persistence-mongo/issues)

  Compliant with [Plugin TCK](http://doc.akka.io/docs/akka/current/scala/persistence.html#Plugin_TCK).

* **[Cassandra plugin](https://github.com/akka/akka-persistence-cassandra)** (by [Martin Krasser](https://github.com/krasserm), Typesafe and contributors)

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.2</td></tr>
  <tr><td>Latest Akka version:</td><td>2.4.2</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.typesafe.akka" %% "akka-persistence-cassandra" % "0.10"</code></td></tr>
  </table>

  Published to maven central.

  Compliant with Akka Persistence TCK 2.4.2.

* **[Kafka plugin](https://github.com/krasserm/akka-persistence-kafka/)** (by [Martin Krasser](https://github.com/krasserm))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.4</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.11</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.github.krasserm" % "akka-persistence-kafka_2.10" % "0.4"</code><br><code>"com.github.krasserm" % "akka-persistence-kafka_2.11" % "0.4"</code></td></tr>
  </table>

  Published to bintray at the URL `http://dl.bintray.com/krasserm/maven`.

  Compliant with <code>akka-persistence-testkit 0.3.4</code>.

* **[InMemory plugin](https://github.com/dnvriend/akka-persistence-inmemory)** (by [Dennis Vriend](https://github.com/dnvriend))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.4</td></tr>
  <tr><td>Latest Akka version:</td><td>2.4.2</td></tr>
  <tr>
    <td>Latest Release:</td>
      <td><code>"com.github.dnvriend" %% "akka-persistence-inmemory" % "1.2.8"</code> (March 3, 2016)</td>
  </tr>
  </table>

  Published to bintray at the URL `http://dl.bintray.com/dnvriend/maven`.

  Compliant with `akka-persistence-tck`.

* **[JDBC plugin](https://github.com/dnvriend/akka-persistence-jdbc)** (by [Dennis Vriend](https://github.com/dnvriend))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.2</td></tr>
  <tr><td>Latest Akka version:</td><td>2.4.2</td></tr>
  <tr>
    <td>Latest Release:</td>
    <td>
      <td><code>"com.github.dnvriend" %% "akka-persistence-jdbc" % "2.2.11"</code> (March 9, 2016)</td>
    </td>
  </tr>
  </table>

  Published to bintray at the URL `http://dl.bintray.com/dnvriend/maven`.

  Compliant with `akka-persistence-tck`.

* **[MySQL & PostgreSQL plugin](https://github.com/okumin/akka-persistence-sql-async)** (by [okumin](https://github.com/okumin))

  <table class="stats">
    <tr><td>Since Akka version:</td><td>2.3.6</td></tr>
    <tr><td>Latest Akka version:</td><td>2.4.0</td></tr>
    <tr><td>Latest Release:</td><td><code>"com.okumin" %% "akka-persistence-sql-async" % "0.3.0"</code></td></tr>
  </table>

  Published to Maven Central. Compliant with [`akka-persistence-tck`](http://doc.akka.io/docs/akka/2.4.0/scala/persistence.html#plugin-tck).

* **[Shared In-Memory Plugin](https://github.com/jdgoldie/akka-persistence-shared-inmemory/)** (by [Joshua Goldie](https://github.com/jdgoldie))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.6</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.6</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.github.jdgoldie" % "akka-persistence-shared-inmemory_2.10" % "1.0.15"</code><br><code>"com.github.jdgoldie" % "akka-persistence-shared-inmemory_2.11" % "1.0.15"</code></td></tr>
  </table>

  Published to bintray at the URL `http://dl.bintray.com/jdgoldie/maven`.

  In-memory implementations of the journal and snapshot store designed to work with multi-node and single-node unit tests.  Tested against `akka-persistence-tck-experimental` version 2.3.6

* **[EventStore plugin](https://github.com/EventStore/EventStore.Akka.Persistence)** (by [Yaroslav Klymko](https://github.com/t3hnar))

  <table class="stats">
    <tr><td>Since Akka version:</td><td>2.3.2</td></tr>
    <tr><td>Latest Akka version:</td><td>2.3.6</td></tr>
    <tr><td>Latest Release:</td><td><code>"com.geteventstore" %% "akka-persistence-eventstore" % "1.1.0"</code> (Oct 19, 2014)</td></tr>
  </table>

  Published to Maven Central. Tested against [`akka-persistence-testkit`](https://github.com/krasserm/akka-persistence-testkit).  

* **[Chronicle plugin](https://github.com/carrot-garden/akka-persistence-chronicle)** (by [Andrei Pozolotin](https://github.com/Andrei-Pozolotin))

  <table class="stats">
    <tr><td>Since Akka version:</td><td>2.4.0</td></tr>
    <tr><td>Latest Akka version:</td><td>2.4.0</td></tr>
    <tr><td>Latest Release:</td><td><code>"com.carrotgarden.akka" %% "akka-persistence-chronicle" % "1.1.0"</code> (Release TBD)</td></tr>
  </table>

  Artifacts on [Maven Central](http://search.maven.org/#search%7Cga%7C1%7Cakka-persistence-chronicle). Compliant with [`akka-persistence-tck`](http://doc.akka.io/docs/akka/snapshot/scala/persistence.html#plugin-tck).

* **[In-Memory/Mock plugin](https://github.com/pnerg/akka-persistence-mock)** (by [Peter Nerg](https://github.com/pnerg))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.4-M3</td></tr>
  <tr><td>Latest Akka version:</td><td>2.4.0</td></tr>
  <tr>
    <td>Latest Release:</td>
      <td><code>"org.dmonix.akka" %% "akka-persistence-mock" % "1.1"</code> (Oct 15, 2015)</td>
  </tr>
  </table>

  Published to [Maven Central](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22org.dmonix.akka%22).

  Compliant with [`akka-persistence-tck`](http://doc.akka.io/docs/akka/2.4-M3/scala/persistence.html#plugin-tck).

* **[Redis plugin](https://github.com/hootsuite/akka-persistence-redis)** (by [Hootsuite](https://github.com/hootsuite/) and contributors)

  <table class="stats">
    <tr><td>Since Akka version:</td><td>2.3.12</td></tr>
    <tr><td>Latest Akka version:</td><td>2.4.0</td></tr>
    <tr><td>Latest Release:</td><td><code>"com.hootsuite" %% "akka-persistence-redis" % "0.3.0"</code></td></tr>
  </table>

  Published to [Bintray](https://bintray.com/hootsuite/maven/akka-persistence-redis/view). Compliant with [`akka-persistence-tck`](http://doc.akka.io/docs/akka/2.4.0/scala/persistence.html#plugin-tck).

* **[Titan plugin](https://github.com/acflorea/akka-persistence-titan)** (by [Adrian FLOREA](https://github.com/acflorea/))

  <table class="stats">
    <tr><td>Since Akka version:</td><td>2.4.0</td></tr>
    <tr><td>Latest Akka version:</td><td>2.4.1</td></tr>
    <tr><td>Latest Release:</td><td><code>"com.github.acflorea" %% "akka-persistence-titan" % "1.0.0"</code></td></tr>
  </table>

  Artifacts on [Maven Central](http://search.maven.org/#search%7Cga%7C1%7Cakka-persistence-titan). Compliant with [`akka-persistence-tck`](http://doc.akka.io/docs/akka/snapshot/scala/persistence.html#plugin-tck).

## Other

* **[Akka Stream OmniGraffle Stencils](https://www.graffletopia.com/stencils/1493)** (by [Derek Wyatt](https://github.com/derekwyatt))

  <table class="stats">
    <tr><td>Since Akka version:</td><td>2.4.4</td></tr>
    <tr><td>Latest Akka version:</td><td>2.4.7 (although it doesn't matter too much)</td></tr>
  </table>

  Some [stencils](https://www.graffletopia.com/stencils/1493) for [OmniGraffle](https://www.omnigroup.com/omnigraffle/) that can be used to illustrate Akka Stream graphs.

## Projects Using Akka

* **[Play Framework](http://www.playframework.org/)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.0</td></tr>
  <tr><td>Latest Akka version:</td><td>2.2.4</td></tr>
  <tr><td>Latest Release:</td><td>2.2.2 (March 1, 2014)</td></tr>
  </table>

* **[Spray: REST library](http://spray.io)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>1.0</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.0</td></tr>
  <tr><td>Latest Release:</td><td>several, check <a href="http://spray.io/project-info/current-versions/">this page</a> for details</td></tr>
  </table>

* **[Raiku: Riak client](https://github.com/gideondk/Raiku)** (by [Gideon de Kok](https://github.com/gideondk))

  <table class="stats">
    <tr><td>Since Akka version:</td><td>n/a</td></tr>
    <tr><td>Latest Akka version:</td><td>2.2.0</td></tr>
    <tr><td>Latest Release:</td><td><code>"nl.gideondk" %% "raiku" % "0.6.1"</code> (December 23, 2013)</td></tr>
  </table>

  Published to: `https://raw.github.com/gideondk/gideondk-mvn-repo/master`

* **[elasticmq: message queue with an SQS-compatible interface](https://github.com/adamw/elasticmq)** (by [Adam Warski](https://twitter.com/adamwarski))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.1.4</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.11</td></tr>
  <tr><td>Latest Release:</td><td><code>"org.elasticmq" %% "elasticmq-rest-sqs" % "0.8.8"</code> (April 10, 2015)</td></tr>
  </table>

* **[Kamon: reactive applications monitoring](https://github.com/kamon-io/Kamon)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.2.0</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.2</td></tr>
  <tr><td>Latest Release:</td><td><code>"io.kamon" %% "kamon-core" % "0.3.2"</code> (July 29, 2014)</td></tr>
  </table>

  Kamon is a set of tools that helps you to get metrics out of your reactive applications. With Kamon you can get actor,
  trace and dispatcher metrics, user defined metrics (histograms, counters, gauges)  and automatic trace recognition for
  Spray and Play! applications. Currently we support NewRelic, Datadog and StatsD as backends for metric data and using
  our simple subscriptions messaging protocol you can get your metrics data anywhere you want. Head over to
  [kamon.io](http://kamon.io) for more information about how to get started with Kamon!

* **[akka-kafka: actor based kafka consumer](https://github.com/sclasen/akka-kafka)**  (by [Scott Clasen](https://github.com/sclasen))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.2</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.2</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.sclasen" %% "akka-kafka" % "0.0.2"</code> (April 21, 2014)</td></tr>
  </table>

  akka-kafka is an actor based kafka consumer built on top of the high-level kafka consumer that allows
  for asynchronous/concurrent processing of messages from kafka while keeping an upper bound on the number of
  in-flight messages, and explicitly managing the commit of offsets.

* **[akka-analytics](https://github.com/krasserm/akka-analytics/)** (by [Martin Krasser](https://github.com/krasserm))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.4</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.11</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.github.krasserm" % "akka-analytics-cassandra_2.10" % "0.3"</code><br><code>"com.github.krasserm" % "akka-analytics-cassandra_2.11" % "0.3"</code><br><code>"com.github.krasserm" % "akka-analytics-kafka_2.10" % "0.3"</code><br><code>"com.github.krasserm" % "akka-analytics-kafka_2.11" % "0.3"</code></td></tr>
  </table>

  Large-scale event processing with Akka Persistence and Apache Spark.

  Published to bintray at the URL `http://dl.bintray.com/krasserm/maven`.

* **[Project Blended](https://github.com/woq-blended/blended)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.2</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.5</td></tr>
  <tr><td>Latest Release:</td><td>1.0.7-M3</td></tr>
  </table>

  *Project Blended* is a collection of reusable bundles for OSGi development. The latest additions are
  focused on integrating Akka and OSGi to get the best out of the worlds. The first application of the Akka
  integration is to add spray support to an OSGi container. The project is the application core for several
  retail applications. Get started by reading our ideas on [akka and spray integration](http://www.wayofquality.de/projects/blended/)
  into an OSGi container.

  We have just added an integration test framework based on Akka, Docker and Scalatest.

* **[Streamz](https://github.com/krasserm/streamz)** (by [Martin Krasser](https://github.com/krasserm))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.3</td></tr>
  <tr><td>Latest Akka version:</td><td>2.4.11</td></tr>
  <tr><td>Latest Release:</td><td>0.5</td></tr>
  </table>

  Streamz is a  library that supports the conversion of [Akka Stream](http://doc.akka.io/docs/akka/2.4/scala/stream/index.html) `Source`s, `Flow`s and `Sink`s to and from FS2 `Stream`s, `Pipe`s and `Sink`s, respectively. It also supports the usage of [Apache Camel](http://camel.apache.org/) endpoints in FS2 `Stream`s.

  Published to bintray at the URL `http://dl.bintray.com/krasserm/maven`.

* **[Akka RabbitMQ client](https://github.com/thenewmotion/akka-rabbitmq)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.0.5</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.7</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.thenewmotion.akka" %% "akka-rabbitmq" % "1.2.3"</code> (Dec 14, 2014)</td></tr>
  </table>

   Actors based client for RabbitMQ

  Published to http://nexus.thenewmotion.com.

* **[Op-Rabbit](https://github.com/SpinGo/op-rabbit)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.7</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.11</td></tr>
  <tr><td>Latest Akka Stream and HTTP Experimental version:</td><td>1.0</td></tr>
  <tr>
    <td>Latest Release:</td>
    <td><pre>
    val opRabbitVersion = "1.0.0-RC2"
    Seq(
      "com.spingo" %% "op-rabbit-core"         % opRabbitVersion,
      "com.spingo" %% "op-rabbit-play-json"    % opRabbitVersion, // optional Play-Json marshalling integration
      "com.spingo" %% "op-rabbit-json4s"       % opRabbitVersion, // optional Json4s marshalling integration
      "com.spingo" %% "op-rabbit-akka-stream"  % opRabbitVersion  // optional Akka Stream integration
    )
    </pre></td>
  </tr>
  </table>

  High-level, opinionated messaging framework for RabbitMQ / Akka. Builds on `akka-rabbitmq`.

  Published to http://spingo-oss.s3.amazonaws.com/repositories/releases.

* **[Eventuate](https://github.com/RBMHTechnology/eventuate)** (by [Red Bull Media House Technology](https://github.com/RBMHTechnology))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.11</td></tr>
  <tr><td>Latest Akka version:</td><td>2.4.12</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.rbmhtechnology" % "eventuate_2.11" % "0.8"</code></td></tr>
  </table>

  Eventuate is a toolkit for building applications composed of event-driven and event-sourced services that communicate via causally ordered event streams. Services can either be co-located on a single node or distributed up to global scale. Services can also be replicated with causal consistency and remain available for writes during network partitions. Eventuate has a [Java](http://www.oracle.com/technetwork/java/javase/overview/index.html) and [Scala](http://www.scala-lang.org/) API, is written in Scala and built on top of [Akka](http://akka.io), a toolkit for building highly concurrent, distributed, and resilient message-driven applications on the JVM. Eventuate

    - provides abstractions for building stateful event-sourced services, persistent and in-memory query databases and event processing pipelines
    - enables services to communicate over a reliable and partition-tolerant event bus with causal event ordering and distribution up to global scale
    - supports stateful service replication with causal consistency and concurrent state updates with automated and interactive conflict resolution
    - provides implementations of operation-based CRDTs as specified in [A comprehensive study of Convergent and Commutative Replicated Data Types](http://hal.upmc.fr/file/index/docid/555588/filename/techreport.pdf)
    - supports the development of *always-on* applications by allowing services to be distributed across multiple availability zones (locations)
    - supports the implementation of reliable business processes from event-driven and command-driven service interactions
    - supports the aggregation of events from distributed services for updating query databases
    - provides adapters to 3rd-party stream processing frameworks for analyzing event streams

  Find out more in the [project documentation](http://rbmhtechnology.github.io/eventuate/). Eventuate binaries are [published](http://rbmhtechnology.github.io/eventuate/download.html) to bintray at the URL `https://dl.bintray.com/rbmhtechnology/maven`.

* **[CurioDB](https://github.com/stephenmcd/curiodb)**

  <table class="stats">
    <tr><td>Since Akka version:</td><td>2.4.0</td></tr>
    <tr><td>Latest Akka version:</td><td>2.4.0</td></tr>
  </table>

  CurioDB is a distributed and persistent Redis clone, built with Scala and Akka.
  You can read more about it in this [introductory article](http://blog.jupo.org/2015/07/08/curiodb-a-distributed-persistent-redis-clone/).

* **[Gearpump](https://github.com/intel-hadoop/gearpump)**

  <table class="stats">
    <tr><td>Since Akka version:</td><td>2.2.3</td></tr>
    <tr><td>Latest Akka version:</td><td>2.3.6</td></tr>
    <tr><td>Latest Release:</td><td><code>"com.github.intel-hadoop" %% "gearpump" % "0.4.1"</code></td></tr>
  </table>

  Gearpump is a lightweight real-time big data streaming engine built on Akka and written in Scala. Gearpump features in

  - using Actor everywhere
  - exactly once semantics
  - topology DAG DSL and high-level DSL
  - flow control and back-pressure
  - no inherent latency and high performance message passing
  - ability to handle in out-of-order message
  - high availability
  - dynamic DAG
  - fancy built-in dashboard

  Find out more at [project site](http://www.gearpump.io/). Gearpump binaries are published to [sonatype](https://oss.sonatype.org/).

* **[Ainterface](https://github.com/ainterface/ainterface)** (by [okumin](https://github.com/okumin))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.12</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.12</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.okumin" %% "ainterface" % "0.1"</code> (August 19, 2015)</td></tr>
  </table>

  Ainterface is a tool for Akka actors to communicate with Erlang processes.
  APIs of Ainterface are designed to integrate Akka with Erlang seamlessly.

* **[Quckoo](https://github.com/alonsodomin/quckoo)** (by [alonsodomin](https://github.com/alonsodomin))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.4.9</td></tr>
  <tr><td>Latest Akka version:</td><td>2.4.9</td></tr>
  <tr><td>Latest Release:</td><td><code>"io.quckoo" %% "quckoo" % "0.1.0"</code> (August 31, 2016)</td></tr>
  </table>

  Quckoo is a fault tolerant distributed task scheduler. Is only focused on short-running,
  recurrent tasks. Quckoo features the following:

  - Linear scalable for running jobs as well as for triggering.
  - Automatic resolution of job's dependencies.
  - Internal registry of job definitions.
  - Client libraries in Scala & ScalaJS
  - HTTP REST service
  - Built-in administration console

A selected list of some of the projects hosted on GitHub is collected in the
[Akka Actor Corpus](http://actor-applications.cs.illinois.edu/akka.html)
project for the purpose of researching code patterns around this actor model
implementation.
