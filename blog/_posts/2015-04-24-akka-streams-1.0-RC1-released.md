---
layout: post
title: Akka Streams &amp; Http 1.0-RC1 Released!
author: Patrik Nordwall
short: Akka Streams and Http 1.0-RC1 Released
category: news
redirect_from: /news/2015/04/24/akka-streams-1.0-RC1-released.html
tags: [releases]
---

*Dear hAkkers,*

we—the Akka committers—are exceptionally proud to present the first RELEASE CANDIDATE or Akka Streams & HTTP. While this is not the end of the journey—several features are going to be added after 1.0—the time has come to declare a (very) useful subset of the intended functionality ready for public consumption. Since the last milestone we have added the following high-level features:

- a TestKit for streams
- proper naming for all parts of a flow topology (see `.named`)
- add SslTls stage including support for session renegotiation
- added ActorRefSink and ActorRefSource for simple Actor integration
- data flow logging by a prepackaged combinator (see `.log`)
- Source and Sink for files (using FileChannel) as well as for InputStream/OutputStream
- HTTP client with connection pooling and idempotent request retry
- … and (wait for it) … Websockets :-)

In addition we fixed many small things, as usual, and we also did some last renames and reorganizations in order to offer a consistent API:

- Java functional interfaces moved to akka-actor (2.3.10, see `akka.japi.function`)
- improved Java compilation error messages by adding arity to method name in flow factories
- made OperationAttributes language-independent and also extensible, dispatcher and supervision properties moved to ActorOperationAttributes
- removed `.section` in favor of `.withAttributes` and `.via`
- moved FlattenStrategy into the Java/Scala DSLs
- reorganized the project structure and package hierarchy of HTTP to offer consistent and equivalent Java & Scala APIs
- relaxed method signatures to accept Graphs instead of the more specific Source/Flow/Sink types to enable free reuse of blueprints between Java & Scala
- renamed StreamTcp to Tcp and the bind method takes interface and port parameters instead of InetSocketAddress

### On the State of HTTPS ###

While we now have all the ingredients—SslTls and HTTP are BidiFlows that can be connected—we do not yet have nice convenience APIs for using HTTP and SSL together. This will come in one of the next releases, perhaps even before 1.0.

### Things that are Known Missing ###

The cookbook section of the streams documentation has not yet been ported to Java, but the text of the Scala version applies to both languages. More documentation will follow in general, in particularly SslTls currently only has API docs.

The `akka-http-core` module is still missing the Java side of the multipart model (#15674).
Working with HTTPS (client- and server-side) is not yet as easy as it will be. 
Additionally not all of the directives that make up the high-level server-side API in `akka-http-scala` have proper counterparts in `akka-http-java` (#16436). We will close these gaps shortly.

### General Notices ###

The complete list of closed tickets can be found in the [streams-1.0-RC1](https://github.com/akka/akka/issues?q=milestone%3Astreams-1.0-RC1+is%3Aclosed) and [http-1.0-RC1](https://github.com/akka/akka/issues?q=milestone%3Ahttp-1.0-RC1+is%3Aclosed) github issues milestones.

The activator templates have also been updated:

- [Akka Streams with Java8!](https://www.lightbend.com/activator/template/akka-stream-java8)
- [Akka Streams with Scala!](https://www.lightbend.com/activator/template/akka-stream-scala)

We’d like to thank all of you for testing and for providing feedback on our progress.


### Credits ###

    commits added removed
       34    5010    3277 Mathias
       22    4711     256 Johannes Rudolph
       21    2211    1326 Patrik Nordwall
       13    2921     296 Konrad Malawski
        7    2712    1506 Roland Kuhn
        5    2040     365 Endre Sándor Varga
        5    1622    1182 Martynas Mickevičius
        1       1       1 Jun

*Happy hAkking!*
