---
layout: page-full-width
title: Akka Documentation
---

## Search Reference Documentation

<p>
  Jump immediatly to: 
  <input type="search" id="search" class="form-control" /> 
  in
<!--  <select id="docs-language" class="form-control" style="width: 5em"> -->
<!--    <option selected="selected">scala</option> -->
<!--    <option>java</option> -->
<!--  </select> -->
  <a href="http://doc.akka.io/docs/akka/2.4.4">reference documentation</a>.
</p>

## Release Versions

### Akka 2.4.4 (current stable release) for Scala Scala 2.11 / 2.12.0-M3 and Java 8+

* Akka Documentation

  * HTML for [Java](http://doc.akka.io/docs/akka/2.4.4/java.html) and [Scala](http://doc.akka.io/docs/akka/2.4.4/scala.html)
  * PDF for [Java](http://doc.akka.io/docs/akka/2.4.4/AkkaJava.pdf) and [Scala](http://doc.akka.io/docs/akka/2.4.4/AkkaScala.pdf)
  * [EPUB](http://doc.akka.io/docs/akka/2.4.4/Akka.epub) (Combined Java and Scala)

* Akka API - for [Java](http://doc.akka.io/japi/akka/2.4.4/) and [Scala](http://doc.akka.io/api/akka/2.4.4/)

All artifacts are available on Maven Central.

Note that for Akka HTTP you most likely want to depend on `akka-http-experimental` which provides the Routing DSL,
rather than just `akka-http-core` which provides the raw HTTP model as well as low level HTTP server.

<div class="container">
  <ul class="tabs">
    <li class="tab-link sbt current" data-tab="stable-dependencies-sbt-tab">sbt</li>
    <li class="tab-link gradle" data-tab="stable-dependencies-gradle-tab">gradle</li>
    <li class="tab-link maven" data-tab="stable-dependencies-maven-tab">maven</li>
  </ul>

  <div id="stable-dependencies-sbt-tab" class="tab-content current">
    <pre><code id="stable-dependencies-sbt">    </code></pre>
  </div>
  <div id="stable-dependencies-gradle-tab" class="tab-content">
     <pre><code id="stable-dependencies-gradle">    </code></pre>
  </div>
  <div id="stable-dependencies-maven-tab" class="tab-content">
    <pre><code id="stable-dependencies-maven">    </code></pre>
  </div>
</div>

### Akka 2.3.15 (previous stable release) for Scala 2.10 / 2.11 and Java 6+

* Akka Documentation

  * HTML for [Java](http://doc.akka.io/docs/akka/2.3.15/java.html) and [Scala](http://doc.akka.io/docs/akka/2.3.15/scala.html)
  * PDF for [Java](http://doc.akka.io/docs/akka/2.3.15/AkkaJava.pdf) and [Scala](http://doc.akka.io/docs/akka/2.3.15/AkkaScala.pdf)
  * [EPUB](http://doc.akka.io/docs/akka/2.3.15/Akka.epub) (Combined Java and Scala)

* Akka API - for [Java](http://doc.akka.io/japi/akka/2.3.15/) and [Scala](http://doc.akka.io/api/akka/2.3.15/)

## Akka Snapshots

Automatically published documentation for the latest SNAPSHOT version of Akka can be found here:

* HTML for [Java](http://doc.akka.io/docs/akka/snapshot/java.html) and [Scala](http://doc.akka.io/docs/akka/snapshot/scala.html)
* PDF for [Java](http://doc.akka.io/docs/akka/snapshot/AkkaJava.pdf) and [Scala](http://doc.akka.io/docs/akka/snapshot/AkkaScala.pdf)
* [EPUB](http://doc.akka.io/docs/akka/snapshot/Akka.epub) (Combined Java and Scala)

Automatically published Scaladoc API for the latest SNAPSHOT version of Akka can be found here:

* Akka API - for [Java](http://doc.akka.io/japi/akka/snapshot/) and [Scala](http://doc.akka.io/api/akka/snapshot/)

## Old Versions

### Akka Streams and HTTP

The `2.0.x` series of the Akka Streams and HTTP is available if you need to use them on JDK 6.
This release is maintained by backporting important fixes and remains binary compatible withn it's own series.

If possible, it is strongly recommended to upgrade to Akka 2.4.x instead, which includes Akka Streams and HTTP since version 2.4.2.

* The artifacts are available on Maven Central (for use with Scala version `2.11.x`):
  * `"com.typesafe.akka" % "akka-stream-experimental_2.11" % "2.0.4"`
  * `"com.typesafe.akka" % "akka-http-core-experimental_2.11" % "2.0.4"`
  * `"com.typesafe.akka" % "akka-http-experimental_2.11" % "2.0.4"` (for Java & Scala DSL)
  * plus testkits and marshallers

* similarily artifacts for use with Scala version `2.10.x`, are available as:
  * `"com.typesafe.akka" % "akka-stream-experimental_2.10" % "2.0.4"`
  * `"com.typesafe.akka" % "akka-http-core-experimental_2.10" % "2.0.4"`
  * `"com.typesafe.akka" % "akka-http-experimental_2.10" % "2.0.4"` (for Java & Scala DSL)
  * plus testkits and marshallers

* API documentation for [Java](http://doc.akka.io/japi/akka-stream-and-http-experimental/2.0.4/) and [Scala](http://doc.akka.io/api/akka-stream-and-http-experimental/2.0.4/)

* Reference documentation for [Java](http://doc.akka.io/docs/akka-stream-and-http-experimental/2.0.4/java.html) and [Scala](http://doc.akka.io/docs/akka-stream-and-http-experimental/2.0.4/scala.html)

### Akka 2.2.5 for Scala 2.10 and Java 6+

* HTML for [Java](http://doc.akka.io/docs/akka/2.2.5/java.html) and [Scala](http://doc.akka.io/docs/akka/2.2.5/scala.html)
* PDF for [Java](http://doc.akka.io/docs/akka/2.2.5/AkkaJava.pdf) and [Scala](http://doc.akka.io/docs/akka/2.2.5/AkkaScala.pdf)
* [EPUB](http://doc.akka.io/docs/akka/2.2.5/Akka.epub) (Combined Java and Scala)
* Akka API - for [Java](http://doc.akka.io/japi/akka/2.2.5/) and [Scala](http://doc.akka.io/api/akka/2.2.5/)

### Akka 2.1.4 for Scala 2.10 and Java 6+

* Akka Documentation - as [HTML](http://doc.akka.io/docs/akka/2.1.4) (or as [PDF](http://doc.akka.io/docs/akka/2.1.4/Akka.pdf) or [EPUB](http://doc.akka.io/docs/akka/2.1.4/Akka.epub))
* Akka API - for [Java](http://doc.akka.io/japi/akka/2.1.4/) and [Scala](http://doc.akka.io/api/akka/2.1.4/)

### Akka 2.0.5 for Scala 2.9 and Java 6+

* Akka Documentation - as [HTML](http://doc.akka.io/docs/akka/2.0.5) (or as [PDF](http://doc.akka.io/docs/akka/2.0.5/Akka.pdf))
* Akka API - for [Scala](http://doc.akka.io/api/akka/2.0.5)


### Akka 1.3.1 for Scala 2.9 and Java 6+

* Akka Documentation - as [HTML](http://doc.akka.io/docs/akka/1.3.1) (or as [PDF](http://doc.akka.io/docs/akka/1.3.1/Akka.pdf))
* Akka Modules Documentation - as [HTML](http://doc.akka.io/docs/akka-modules/1.3.1) (or as [PDF](http://doc.akka.io/docs/akka-modules/1.3.1/AkkaModules.pdf))
* Akka API - for [Scala](http://doc.akka.io/api/akka/1.3.1)
* Akka Modules API - for [Scala](http://doc.akka.io/api/akka-modules/1.3.1)
