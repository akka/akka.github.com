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

<p class="notice">
This page is currently being created: we have seeded it with those projects
which were previously listed as community projects selected by the Akka team.
Now we ask for your help in adding the missing meta-data (like for which
releases of Akka a certain project is available). We will eventually clean up
and remove those projects which have not been updated.
</p>

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

* **[Akka WebSocket](http://backchatio.github.com/hookup/)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[Akka AMQP Integration](https://github.com/momania/akka-amqp)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[Akka AMQP Durable Mailbox](https://github.com/drexin/akka-amqp-mailbox)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[Akka Protostuff Serialization](https://github.com/romix/akka-protostuff-serialization)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[Akka Quickser Serialization](https://github.com/romix/akka-quickser-serialization)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[Akka Kryo Serialization](https://github.com/romix/akka-kryo-serialization)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[Akka Quartz](https://github.com/theatrus/akka-quartz)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[Bark: RPC-like system build on Akka IO](https://github.com/lab050/Bark)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[Sentinel: toolkit for creating reactive TCP clients/servers](https://github.com/gideondk/sentinel)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

## Extensions to Spray

The Spray project is in the process of being merged into Akka as the new HTTP
module, see also [this news
item](http://www.typesafe.com/blog/typesafe-gets-sprayed). We also track
extensions to Spray on this page to make it clear that the transition of Spray does not
mean that these will be abandoned, we intend to support the merger of the Spray
and Akka eco-systems. The details remain to be determined, but there will be a
migration path from Spray 1.3 to Akka HTTP.

* **[S4 – Spray Slick Scala Start](https://github.com/jacobus/s4)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[Spray template for Heroku](https://github.com/matanster/spray-heroku)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>


## Plugins to Akka Persistence

Before adding a plugin to this list, it should have been tested for compatibility with the [plugin compatibility testkit](https://github.com/krasserm/akka-persistence-testkit).

### Journal plugins

* **[BDB plugin](https://github.com/bseibel/akka-persistence-bdb)** (by [Brandon Seibel](https://github.com/bseibel))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.0-RC1</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.0-RC2</td></tr>
  <tr><td>Latest Release:</td><td>none (to be published locally)</td></tr>
  </table>

* **[Cassandra plugin](https://github.com/krasserm/akka-persistence-cassandra/)** (by [Martin Krasser](https://github.com/krasserm))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.0</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.0</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.github.krasserm" % "akka-persistence-cassandra_2.10" % "0.2"</code></td></tr>
  </table>

  Published to bintray at the URL `http://dl.bintray.com/krasserm/maven`.

* **[DynamoDB plugin](https://github.com/sclasen/akka-persistence-dynamodb/)** (by [Scott Clasen](https://github.com/sclasen))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.0-RC4</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.0</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.sclasen" % "akka-persistence-dynamodb_2.10" % "0.1-SNAPSHOT"</code></td></tr>
  </table>

  Published to Sonatype snapshots at the URL `https://oss.sonatype.org/content/repositories/snapshots`.

* **[HBase plugin](https://github.com/ktoso/akka-persistence-hbase/)** (by [Konrad Malawski](https://github.com/ktoso))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3-SNAPSHOT</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3-SNAPSHOT</td></tr>
  <tr><td>Latest Release:</td><td><code>"pl.project13.scala" % "akka-persistence-hbase_2.10" % "0.3"</code></td></tr>
  </table>

* **[MapDB plugin](https://github.com/drexin/akka-persistence-mapdb/)** (by [Dario Rexin](https://github.com/drexin))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.0-RC1</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.0-RC1</td></tr>
  <tr><td>Latest Release:</td><td>none (to be published locally)</td></tr>
  </table>

* **[MongoDB plugin](https://github.com/ddevore/akka-persistence-mongo/)** (by [Duncan DeVore](https://github.com/ddevore))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.0</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.0</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.github.ddevore" % "akka-persistence-mongo-casbah_2.10"  % "0.4-SNAPSHOT"</code></td></tr>
  </table>

* **[MongoDB plugin](https://github.com/scullxbones/akka-persistence-mongo/)** (by [Brian Scully](https://github.com/scullxbones))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.3.0</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.0</td></tr>
  <tr><td>Latest Release:</td><td><code>"com.github.scullxbones" % "akka-persistence-mongo-casbah_2.10" % "0.0.4"</code></td></tr>
  </table>

### Snapshot plugins

* **[HBase plugin](https://github.com/ktoso/akka-persistence-hbase/)** (by [Konrad Malawski](https://github.com/ktoso))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[MongoDB plugin](https://github.com/scullxbones/akka-persistence-mongo/)** (by [Brian Scully](https://github.com/scullxbones))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[MongoDB plugin](https://github.com/ddevore/akka-persistence-mongo/)** (by [Duncan DeVore](https://github.com/ddevore))

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>



## Projects Using Akka

* **[Play Web Framework](http://www.playframework.org/)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[Spray: REST library](http://spray.io)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[Spark: Cluster Computing](http://spark-project.org/)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[Socko: Web Server](http://sockoweb.org/)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[Scalatra: Web Framework](http://www.scalatra.org/)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[GeoTrellis: Geoprocessing Engine](http://www.azavea.com/products/geotrellis/)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[Gatling: Stress Test Tool](http://gatling-tool.org/)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[Raiku: Riak client](https://github.com/gideondk/Raiku)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[Riak Scala Client](http://riak.scalapenos.com/index.html)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[Redis Scala Client](https://github.com/debasishg/scala-redis-nb)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[Brando: Redis Client](https://github.com/chrisdinn/brando)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>

* **[rediscala: Redis Client](https://github.com/etaty/rediscala)**

  <table class="stats">
  <tr><td>Since Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Akka version:</td><td>unknown</td></tr>
  <tr><td>Latest Release:</td><td>unknown</td></tr>
  </table>
  
* **[rxmon: Monitoring akka applications with rx](https://github.com/vigdorchik/rxmon)**
  <table class="stats">
  <tr><td>Since Akka version:</td><td>2.2.4</td></tr>
  <tr><td>Latest Akka version:</td><td>2.3.0</td></tr>
  <tr><td>Latest Release:</td><td><code>"org.matmexrhino" % "rxmon_2.10" % "0.2.1"
                                  resolvers += "bintray-vigdorchik" at "http://dl.bintray.com/vigdorchik/maven"
                                  </code> (March 7 2014)</td></tr>
  </table>

A selected list of some of the projects hosted on GitHub is collected in the
[Akka Actor Corpus](http://actor-applications.cs.illinois.edu/akka.html)
project for the purpose of researching code patterns around this actor model
implementation.

