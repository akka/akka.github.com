---
layout: post
title: "Threading & Concurrency in Akka Streams Explained (part I)"
description: ""
author: Endre Varga
redirect_from: /streams/2016/07/06/threading-and-concurrency-in-akka-streams-explained
tags: [streams,internals,intro]
---
{% include JB/setup %}

Akka Streams and streaming has different meaning to different people, but my view is that it is mostly a concurrency construct, like Actors and Futures. The primary goal of streams is to provide a simple way to:

* build *concurrent and memory bounded* computations 

* that can safely interact with various forms of non-blocking IO interfaces 

* without involving blocking 

* while embracing multi-core concurrency, 

* and solving the typical pitfall of missing backpressure: faster producers overwhelm slower consumers that run on a separate thread, resulting in OutOfMemoryExceptions. 

In this post, I will explore how Akka Streams processing pipelines or graphs are transformed to actual multi-threaded execution.

> All of the code in the post assumes the akka-stream artifact of at least version 2.4.2 to be present, and the following code implicitly being present in all samples. Always use the latest minor release – **2.4.7** as of writing this blog post.:

<pre><code class="scala">
import akka.actor.ActorSystem
import akka.stream._
import akka.stream.scaladsl._

val system = ActorSystem("LifecycleDemo")
implicit val materializer = ActorMaterializer.create(system)
</code></pre>

In Akka Streams, we mostly think in terms of computations -"boxes" that can accept and emit elements of a certain type in sequence on their various ports. In this view, a Source is not a static collection of elements; not like an Iterator, because computations can happen asynchronously, working concurrently with other computations. An Iterator always executes any chained computations on the caller thread. For more details on the role of modularity I recommend this section in the documentation: [https://doc.akka.io/docs/akka/2.4/scala/stream/stream-composition.html](https://doc.akka.io/docs/akka/2.4/scala/stream/stream-composition.html)

In this post I assume some familiarity with the concepts explained in the linked documentation page.

As our first step, let’s try a simple experiment and see if we can figure out how computations are mapped to threads:

```scala
println(Thread.currentThread().getName)

Source.single("Hello")
 .map(_ + " Stream World!")
 .to(Sink.foreach(s ⇒ println(Thread.currentThread().getName + " " + s)))
 .run()

println("running")
system.terminate()
```

Running the above example yields a rather surprising result on my machine:

```
run-main-1
running
```

We expected to see something printed out by the stream itself, but it did not happen! We clearly see the printed lines from our main thread `run-main-1`, but the program prints nothing else. And `.run()` was called on the graph. Before diving into the  explanation, let’s tweak the example a little bit:

```scala
println(Thread.currentThread().getName)

Source.single("Hello")
 .map(_ + " Stream World!")
 .to(Sink.foreach(s ⇒ println(Thread.currentThread().getName + " " + s)))
 .run()

println("running")
Thread.sleep(1000) // Wait a bit before we shut down the system

system.terminate()
```

Now the output looks different:

```
run-main-2
running
LifecycleDemo-akka.test.stream-dispatcher-5 Hello Stream World!
```

We immediately get an answer why our previous attempts failed. First, the stream that prints "Hello Stream World" is running on a different thread (`LifecycleDemo-akka.test.stream-dispatcher-5`) and it is not blocking our main thread since the main thread printed “running” before the stream has completely finished. *We stopped the ActorSystem running the stream before it even had the chance to execute*. This leads us to our first observation:

* Streams do not run on the caller thread, instead, they run on a different thread in the background, without blocking the caller.

(Observant readers probably noted the number "5" as the last character of the name of the thread. This *has* significance and will be explained later.)

We already know that the stream will run on a different thread, but it is not  clear yet how different pieces/computations of pipelines execute. We’re modifying our example by adding multiple stages of computations:

1. We replace `.to(..).run()` with the shorthand `.runWith()`
2. The above change also affected our materialized value (for those unaware of materialized values, please read the relevant section of the documentation [https://doc.akka.io/docs/akka/2.4/scala/stream/stream-composition.html#Materialized_values](https://doc.akka.io/docs/akka/2.4/scala/stream/stream-composition.html#Materialized_values)), returning a Future which completes once Sink.foreach completes processing.
3. We only shut down the ActorSystem after the stream has completed (by using `onComplete` on the returned materialized value which is a `Future`)
4. We print the current thread at each stage

```scala
import system.dispatcher

val completion = Source.single("Hello Stream World!\n")
 .map { s ⇒ println(Thread.currentThread().getName() + " " + s); s }
 .map { s ⇒ println(Thread.currentThread().getName() + " " + s); s }
 .runWith(Sink.foreach(s ⇒ println(Thread.currentThread().getName + " " + s)))

completion.onComplete(_ => system.terminate())
```

The following code snippet should give us information about all the threads involved in the two map operations and the foreach block. The output:

```
LifecycleDemo-akka.test.stream-dispatcher-6 Hello Stream World!
LifecycleDemo-akka.test.stream-dispatcher-6 Hello Stream World!
LifecycleDemo-akka.test.stream-dispatcher-6 Hello Stream World!
```

As we see, all of the operations happened on the same thread, sequentially. This is a little bit disappointing, haven’t I talked about concurrent computations in the first paragraph? We have seen that streams run in the background, concurrently from the callers thread, but this is hardly anything interesting. The *default* behavior of Akka Streams is to put all computations of a graph (where possible) in the same single-threaded "island" (I carefully avoid to say thread here, as the reality is more complicated, but bear with me) *unless *instructed otherwise. So let’s instruct it otherwise ;)

The following sample is an extension of the previous one:

* We extract the `map` steps into a method called `processingStage` returning a `Flow` to avoid repeating ourselves. (Remember, everything is a "processing box" in Akka Streams, `map` is no exception! You can think about map as a processor that takes elements in sequence through its input port, then emits them transformed (and in-sequence) through its output port. Flow is just a sequence of free-standing transformation stages, not attached to a particular Source yet.)
* We add a `.async` call after each of our `map` stages. We will see the significance of this immediately when we look at the output of the program.

```scala
import akka.NotUsed

def processingStage(name: String): Flow[String, String, NotUsed] = 
 Flow[String].map { s ⇒
   println(name + " started processing " + s + " on thread " + Thread.currentThread().getName)
   Thread.sleep(100) // Simulate long processing *don't sleep in your real code!*
   println(name + " finished processing " + s)
   s
 }

val completion = Source(List("Hello", "Streams", "World!"))
 .via(processingStage("A")).async
 .via(processingStage("B")).async
 .via(processingStage("C")).async
 .runWith(Sink.foreach(s ⇒ println("Got output " + s)))

completion.onComplete(_ => system.terminate())
```

And the output is now:

```
A started processing Hello on thread LifecycleDemo-akka.test.stream-dispatcher-5
A finished processing Hello
A started processing Streams on thread LifecycleDemo-akka.test.stream-dispatcher-5
B started processing Hello on thread LifecycleDemo-akka.test.stream-dispatcher-10
A finished processing Streams
A started processing World! on thread LifecycleDemo-akka.test.stream-dispatcher-5
B finished processing Hello
B started processing Streams on thread LifecycleDemo-akka.test.stream-dispatcher-10
C started processing Hello on thread LifecycleDemo-akka.test.stream-dispatcher-6
A finished processing World!
B finished processing Streams
B started processing World! on thread LifecycleDemo-akka.test.stream-dispatcher-10
C finished processing Hello
C started processing Streams on thread LifecycleDemo-akka.test.stream-dispatcher-6
Got output Hello
B finished processing World!
C finished processing Streams
Got output Streams
C started processing World! on thread LifecycleDemo-akka.test.stream-dispatcher-6
C finished processing World!
Got output World!
```

There are *many* interesting things here. Remember that number "5" at the end of one of the thread names before? Now there are others! On top of that, it seems like that things no longer happen in a simple order as before. Calls in stages A, B and C overlap. At the point when C starts processing “Hello”, A already started processing the final element, “World!”. On the other hand, if we look at the output of the final print, we see that “Hello”, “Streams” and “World!” arrived in sequence. In short, we have concurrency and at the same time we still maintain the order of elements. Even shorter: *an assembly line*. We also notice that we have several threads at play here. In fact, it seems like that there is a thread for each map stage we have. This is not exactly true though, the real explanation is that there is a thread-pool from which stages borrow threads to execute tasks, but the number of the threads in the pool is bounded and shared by all the streams that are executed. Why not create a thread for each stream or each stream stage? The reason is that it is not efficient enough, it is much better to share a common pool of threads, roughly containing as many threads as cores are available. It is out of the scope of this post to elaborate on this, but a good resource is on Intel’s site: [https://software.intel.com/en-us/node/506100](https://software.intel.com/en-us/node/506100)

Putting together the new facts that we learned:

* Stream stages usually share the same thread unless they are explicitly demarcated from each other by an asynchronous boundary (which can be added by calling `.async` between the stages we want to separate)

* Stages demarcated by async boundaries might run concurrently with each other

* Stages do not run on a dedicated thread but they borrow one from a common pool for a short period

I recommend to play around with the example, most notably try removing some, or all of the asynchronous boundaries we have introduced.

There is one feature of Akka Streams that we shall fully explain in a future post, but is worth noting here. I have been careful not calling pipelines of stages not separated by asynchronous boundaries "threads," but rather called them “islands”. One would assume that by running an infinite stream, like `Source.repeat("boo").runWith(Sink.ignore)`, we would consume a thread from the shared pool forever because the computation never stops. It does not involve any asynchronous boundaries, neither does it wait on any external event like IO, which would suspend execution. Fortunately, this is not true. Instead of taking a thread from the pool and running until stream completion (or waiting on external event), the engine behind Akka Streams is capable of suspending the stream and putting the thread back into the pool. Akka Streams does this completely transparently (except from ThreadLocals, they will not work), maintaining the illusion of single-threadedness, handling all thread-safety and visibility problems automatically. This is one of the features that sets Akka Streams apart from other similar frameworks. We shall explore more “hidden” safety features like this in the future in upcoming posts.

To summarize what we have learned:

* Streams do not run on the caller thread. Instead, they run on a different thread in the background, without blocking the caller.
* Stream stages usually share the same thread unless they are explicitly demarcated from each other by an asynchronous boundary (which can be added by calling .async between the stages we want to separate).
* Stages demarcated by asynchronous boundaries might run concurrently with each other.
* Stages do not run on a dedicated thread, but they borrow one from a common pool for a short period.

In the next part, we will explore more general graphs (we only looked at linear pipelines so far), how stream completion progresses, and how synchronous islands can still maintain concurrency and more generally, nondeterminism.
