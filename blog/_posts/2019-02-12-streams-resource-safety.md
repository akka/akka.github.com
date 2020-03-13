---
layout: series_post
title: "Streams and Resource Safety"
description: "While stream processing has become ubiquitous in the vocabulary of developers, the most important feature of such systems is often glossed over or not focused on nearly enough when introducing them to newcomers."
author: ""
series_title: Programming Reactive Systems
series_tag: course
category: article
tags: [actor,streams,course]
---
{% include JB/setup %}

While stream processing has become ubiquitous in the vocabulary of developers, the most important feature of such systems is often glossed over or not focused on nearly enough when introducing them to newcomers. In this excerpt from the course Programming Reactive Systems lecturer Konrad Malawski explains more about this important concept.

Stream processing is nothing new, as it has been around even in the form of combining processes using [UNIX pipelines](https://en.wikipedia.org/wiki/Pipeline_(Unix)) since the early seventies. In recent years however we have observed the rise of reactive streaming systems at a larger scale – in asynchronous programming as well as distributed systems.

## Resource Safety achieved as contract between asynchronous processors

A very important aspect of streaming in such systems is resource safety, which is a desirable property of a system that can be simplest described as not requiring the consumption of more resources in order to continue processing than it is initially allocated or allowed to consume. This guarantee comes in various forms, for example regarding memory or CPU consumption. The recent [Reactive Streams](http://www.reactive-streams.org/) initiative has set out to, as part of the standard, focus on the memory consumption aspect of it.

The protocol defined by Reactive Streams allows compliant implementations to communicate with one another (e.g. Akka Streams can inter-op seamlessly with RxJava and *vice versa*) while maintaining the same understanding of back-pressure which is signalled in terms of “demand”. Resource safety is achieved by a downstream processing stage never signalling more demand than it is ready to process. This allows the entire stream to self-regulate and never load more into memory than the downstreams are ready to process. This rides on the assumption though that the downstreams and operator implementations indeed request reasonable amounts of demand.

## Resource Safety achieved by careful operator implementations

While Reactive Streams give us a protocol that can be used to process streams within bounded resources, we still need to make sure that operators don’t violate our expectations. Guaranteeing bounded resource turns out to be non-trivial in face of inherently unbounded operators  such as “groupBy” or “unique”, which technically would require to keep in memory all keys that have been already observed or grouped on during the lifetime of a stream. As a stream is potentially infinite, and could be emitting unique elements, such retained set would also be potentially be infinite, posing a problem for achieving resource safety for those operations. In practice two approaches are taken in these situations.

The first is windowing, where such operator relaxes the notion of uniqueness to be bounded a) in number of elements, by bounding the number of “recent unique elements remembered” or b) in time, by bounding the remembered elements “within the last few seconds or minutes”, which oftentimes is not only “good enough” but also suitable from a business perspective in many fast data streaming applications.

The second approach is to utilize probabilistic data structures that store information in sub-linear space. For example, one can use a [Bloom filter](https://en.wikipedia.org/wiki/Bloom_filter) for retaining the “seen” elements in order to implement the “unique” operator. While this allows us to store the unique keys in a much smaller space, it does come with a possibility of triggering false positives, where we’d consider a key as seen, even if it has not been seen yet. The probability of such case happening can be estimated and tuned for the amount of expected keys in our stream, though.

As you can see, achieving resource safety in streaming systems takes a number of techniques and combining them all together. In the Programming Reactive Systems course we will look into some of them and hopefully inspire you to continue this journey on your own once the course completes.

## Learn more in Programming Reactive Systems

This post shines a tiny spotlight on one of the many aspects around the implementation of reactive systems that are discussed in the course [Programming Reactive Systems](https://www.edx.org/course/programming-reactive-systems) on edX as [announced earlier](https://akka.io/blog/2019/02/04/programming-reactive-systems). If you want to learn more you are more than welcome to enroll in this free course brought to you by former Akka team lead and co-author of the Reactive Manifesto [Dr. Roland Kuhn](https://www.rolandkuhn.com/), long-time Akka team and Reactive Streams expert group member [Konrad Malawski](https://www.kto.so/), and [Scala Center](https://scala.epfl.ch/) director of education [Julien Richard-Foy](http://julien.richard-foy.fr/).

