---
layout: series_post
title: "Akka Streams Integration, codename Alpakka"
description: ""
author: Patrik Nordwall
redirect_from: /integrations/2016/08/23/intro-alpakka
series_title: Integration
series_tag: integration
category: article
tags: [streams,integration]
---
{% include JB/setup %}


We believe that Akka Streams can be the tool for building a modern alternative to [Apache Camel](http://camel.apache.org/). That will not happen by itself overnight and this is a call for arms for the community to join us on this mission. The biggest asset of Camel is its rich set of [endpoint components](https://camel.apache.org/components.html). We would like to see that similar endpoints are developed for Akka Streams. Our goal is to build a strong and healthy community around such integrations. We've already seen quite some up-take in the community, including connectors to S3, Kafka and more. Akka Streams are built around the core concept of being simple to extend using the powerful yet simple to use APIs. Added components can be used together with all other great things in Akka Streams, such as easy transformation and manipulation of the data stream.

Don't hesitate to get involved!

In upcoming blog posts we will describe how to use the `GraphStage` API for building Sinks and Sources to connect to external data sources over various integration protocols. We will show how to handle challenges such as blocking and asynchronous communication. Transformations are also important for integration scenarios and we will illustrate how to implement a streaming XML parser as an example of such encoder/decoder stages.

Akka Streams already has a lot that are useful for integrations. Defining processing pipelines is what the Akka Streams DSL is all about and that is exactly what you need for operating on streaming data that cannot fit in memory as a whole. It handles backpressure in an efficient non-blocking way that prevents out-of-memory errors, which is a typical problem when using unbounded buffering with producers that are faster than consumers.

The following are examples of things that are readily available for building your integrations with Akka Streams today (all available with Java and Scala APIs).

* [Akka Http](https://doc.akka.io/docs/akka/2.4/java/http/index.html) - HTTP client and server components, including support for WebSockets.
* [Akka Stream Kafka](https://github.com/akka/reactive-kafka) - Connector to Kafka.
* [Reactive Streams](https://www.reactive-streams.org/) - Interoperate seamlessly with other Reactive Streams implementations. For example, you can use Akka Streams together with [MongoDB Reactive Streams Java Driver](https://mongodb.github.io/mongo-java-driver-reactivestreams/) for integrating with MongoDB.
* [Streaming TCP](https://doc.akka.io/docs/akka/2.4/java/stream/stream-io.html#Streaming_TCP) - Low level TCP based protocols.
* [Streaming File IO](https://doc.akka.io/docs/akka/2.4/java/stream/stream-io.html#Streaming_File_IO) - Reading and writing files.
* [mapAsync](https://doc.akka.io/docs/akka/2.4/java/stream/stream-integrations.html#integrating-with-external-services) - Integration with anything that has an asynchronous API based on CompletionStage or futures.
* [Framing](https://doc.akka.io/docs/akka/2.4/java/stream/stream-cookbook.html#Parsing_lines_from_a_stream_of_ByteStrings) - Decoding a stream of unstructured byte chunks into a stream of frames. Delimiter, length field, JSON.

Using Akka Streams and the currently available connectors, an [ETL](https://en.wikipedia.org/wiki/Extract,_transform,_load) example that deals with multiple data sources and destinations is as straightforward as this:

```java
    // Read huge file with Wikipedia content
    Source<WikipediaEntry, CompletionStage<IOResult>> wikipediaEntries =
      FileIO.fromPath(Paths.get("/tmp", "wiki"))
        .via(parseWikiEntries());

    // Enrich the data by fetching matching image from a
    // web service with HTTP
    Source<RichWikipediaEntry, CompletionStage<IOResult>> enrichedData =
      wikipediaEntries
        .via(enrichWithImageData);

    // Store content in Kafka and corresponding image in AWS S3
    enrichedData
      .alsoTo(s3ImageStorage())
      .to(kafkaTopic)
      .run(materializer);
```

In the above example we use [Akka Http](https://doc.akka.io/docs/akka/2.4/java/http/index.html) to enrich the data:

```java
    // parallel fetching of additional data using Akka HTTP, the response is an image
    final int parallelism = 8;
    final Http http = Http.get(system);
    Flow<WikipediaEntry, RichWikipediaEntry, NotUsed> enrichWithImageData =
      Flow.of(WikipediaEntry.class)
        .mapAsyncUnordered(parallelism, w -> {
          final HttpRequest request = HttpRequest.create(
              "http://images.example.com/?query=" + w.title());

          return http.singleRequest(request, mat)
            .thenCompose(response -> {
                final CompletionStage<HttpEntity.Strict> entity =
                  response.entity().toStrict(1000, materializer);
                return entity.thenApply(e -> new RichWikipediaEntry(w, e.getData()));
              }
            );
        });
```

We use [Akka Stream Kafka](https://github.com/akka/reactive-kafka) to publish the content to a Kafka topic:

```Java
    Sink<RichWikipediaEntry, NotUsed> kafkaTopic =
      Flow.of(RichWikipediaEntry.class)
        .map(entry -> entry.wikipediaEntry().content())
        .map(elem -> new ProducerRecord("contents", elem))
        .to(Producer.plainSink(producerSettings));
```

The Github repository to use for contributing your favorite integration component is [Alpakka](https://github.com/akka/alpakka). Please create issues and pull requests for discussion and proposals. Take a look at the list of [Camel components](https://camel.apache.org/components.html) for inspiration. Implementations in Java or Scala are welcome.

This will be great fun, we are looking forward to your contributions!
