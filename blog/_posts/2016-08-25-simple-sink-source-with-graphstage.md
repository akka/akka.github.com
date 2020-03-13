---
layout: series_post
title: "A gentle introduction to building Sinks and Sources using GraphStage APIs (Mastering GraphStages, Part II)"
description: ""
author: Konrad Malawski & Johan Andrén
redirect_from: /integrations/2016/08/25/simple-sink-source-with-graphstage
series_title: Integration
series_tag: integration
category: article
tags: [streams,integration]
---
{% include JB/setup %}

As introduced in the previous blog article Akka Streams is highly extensible allowing you to write your own custom stages that can be used in fully asynchronous and back pressured streams. Out of the box there is a multitude of prewritten stages that can be combined to cover many use cases, sometimes however, you will stumble upon a problem that can not be covered or where composing existing stages simply isn’t efficient enough.

In order to keep things really simple and minimal, we’ll implement a very simple `Sink` and `Source`. Their functionality is on purpose trivial, and would be easily implemented using build-in operators. In this post however we want to focus on the APIs, not on the exciting functionality one could build with them. We'll show some more exciting stages and special features of `GraphStage` in the other upcoming posts in this series–very soon.

## Implementing a random numbers Source

A `GraphStage` consists of a number of key elements. Firstly there is the wrapper class. It defines the shape and ports of the stage we're about to build. The `Shape` is another key component you can learn about in depth in the [Modularity, Composition and Hierarchy](https://doc.akka.io/docs/akka/current/scala/stream/stream-composition.html) section of the documentation. In short however, it simply defines what this graph stage "looks like" (its shape). In that sense a `Source`, `Flow` or `Sink` are simply special cases of shapes, with one outlet (`Source`), one inlet and outlet (`Flow`) and only one inlet (`Sink`). It also means that you can implement any arbitrary shape using a GraphStage - even fan-in or fan-out stages.

As you know, Akka Streams have a materialization step which takes the stream's reusable blueprint and allocates all the resources needed to execute it, to then start the flow of data through it. This means that our GraphStage is actually a factory of the actual `GraphStageLogic` instances. Such logic instance will be created per-materialization (per-instance) and is allowed to keep mutable state inside it. For example if we want to keep a counter or something else for the stream, we would keep it inside the `GraphStageLogic`, not inside the `GraphStage` which is a shared object between all the materializations of a stream. Accessing mutable state within the logic is also *safe* (!) from any of the GraphStage callbacks (`onPush`, `onPull` and more) - so you can think of a `GraphStageLogic` behaving exacly like an Actor. Even though it may be executing on multiple threads, it preserves the single-threaded illusion you know well from Actors. We found this property to be invaluable while implementing stages - the amount of complexity you're guarded from by this single property is simply amazing (esp. if you'd compare with a Reactive Streams implementation which would tell you to just deal with re-entrancy and races on your own).

To create a custom `Source` we define a GraphStage with a `SourceShape`. This is a special shape that will make it possible to use our stage in all places where a `Source` is expected–or, to be more precise where a `Graph<SourceShape<T>>` is expected.

Next we install Handlers for each of the ports. In our case, we only have one `Outlet`, so we set an `AbstractOutHandler` to handle all of the interactions of this port. Instead of thinking about raw demand management as the raw Reactive Streams protocol does, the model here is simplified to just Push and Pull events. Akka Streams will automatically manage the buffers and request multiple elements in batches in accordance to your buffer filling-up / becoming empty again. This vastly simplifies what we have to do in order to implement our simple source: We simply will get an `onPull` signal from our downstream once it is  is ready for to receive element - so then we generate a random number and `push` it into the `out` outlet. Note that all these APIs are well-typed. You can't push a wrongly typed element into the `out` outlet.

We'll implement the below stages in `Java`, to show that the API is pretty nice and does not require special Scala features.
In Scala a few more tricks can be applied, for example the `GraphStageLogic` can directly mix-in the `OutHandler` trait,
however the API remains largely the same (it is the same class after-all).

```java
public class RandomNumberSource extends GraphStage<SourceShape<Integer>> {

  public final Outlet<Integer> out = Outlet.create("RandomNumberSource.out");
  private final SourceShape<Integer> shape = SourceShape.of(out);

  @Override
  public SourceShape<Integer> shape() {
    return shape;
  }

  @Override
  public GraphStageLogic createLogic(Attributes inheritedAttributes) {
    return new GraphStageLogic(shape) {
      private final Random random = new Random();

      {
        setHandler(out, new AbstractOutHandler() {
          @Override
          public void onPull() throws Exception {
            // we push to a specific port, because in general there could be multiple-ports
            // and depending on the element we may decide to push to one or the other (like `route`)
            push(out, random.nextInt());
          }
        });
      }
    };
  }
}

// running it would be as simple as:

// infrastructure you'd only create once per application:
final ActorSystem system = ActorSystem.create("StreamsExamples");
final Materializer mat = ActorMaterializer.create(system);


final Source<Integer, NotUsed> numbers = Source.fromGraph(new RandomNumberSource())
.mapMaterializedValue(o -> NotUsed.getInstance());

final RunnableGraph<Object> runnable = numbers.take(10).to(Sink.ignore());

// we can materialize the same stream multiple times:
runnable.run(mat);
runnable.run(mat);
runnable.run(mat);

// which would result in 3 streams running in parallel.
```

You can find the [complete source code](https://gist.github.com/johanandren/a5b9e4f88fc9fc6ce56dac10bb81a50e) here.

In practice, one could implement such a simple random numbers source using the following one-liner using existing APIs:

```java
  final Source<Integer, NotUsed> randomNumbers =
    Source.repeat("not-used")
      .map(n -> ThreadLocalRandom.current().nextInt());
```

## Implementing a "println" Sink

To create a `Sink` we create a `GraphStage` with exactly one input `Inlet` and make the `GraphStage` have a `SinkShape`. Since streams are demand driven, we also need to signal the upstream stage that we’re ready to receive an element–we do that by pulling the input port. Akka Streams handles the demand management automatically and the data will start flowing from the upstream stages to our Sink.

You'll notice that we keep some state in the `PrintlnSink` directly, instead of within the Logic as was suggested earlier.
In this case it is fine, because the shared data is immutable (the `String prefix`), and the same for all materializations of this `Sink`.
If this was some mutable state, we would move this state into the `GraphStageLogic`.

We then react on that element being pushed with our `AbstractInHandler`:

```java
final class PrintlnSink extends GraphStage<SinkShape<String>> {
  public final Inlet<String> in = Inlet.create("PrintlnSink.in");

  // safe to keep immutable state within the GraphStage itself:
  // mutable state should be kept in the GraphStageLogic instance.
  private final String prefix;

  public PrintlnSink(String prefix) {
    this.prefix = prefix;
  }

  @Override
  public SinkShape<String> shape() {
    return SinkShape.of(in);
  }

  @Override
  public GraphStageLogic createLogic(Attributes inheritedAttributes) {
    return new GraphStageLogic(shape()) {
      // a new GraphStageLogic will be created each time we materialize the sink

      // so all state that we want to keep for the stage should be within the logic.
      //
      // here we maintain a counter of how many messages we printed:
      private long count = 0;

      {
        setHandler(in, new AbstractInHandler() {
          @Override
          public void onPush() {
            // We grab the element from the input port.
            // Notice that it is properly typed as a String.
            //
            // Another reason we explicitly `grab` elements that sometimes one is not
            // immediately ready to consume an input and this is basically a buffer space of one for free.
            final String element = grab(in);

            // Since the GraphStage maintains the Actor-like single-threaded illusion we can safely mutate
            // our internal counter, even though the stage could be running on different threads.
            count += 1;

            // We print our message:
            System.out.println(String.format("[%s:%d] %s", prefix, count, element));

            // And signal that we'd like to receive more elements from the `in` port by pulling it:
            pull(in);

            // It is important to not pull a port "twice", that would lead to bugs so Akka Streams
            // prevents you from making this mistake and would throw
          }
        });
      }

      @Override
      public void preStart() {
        // initiate the flow of data by issuing a first pull on materialization:
        pull(in);
      }
    };
  }
```

In practice, of course, it would not be needed to implement a custom stage just to do a println, it would look like this:

```java
final Source<Long, NotUsed> numbers =
  Source.fromIterator(() -> new Iterator<Long>() {
    private long counter = 0;
    @Override public boolean hasNext() { return true; }
    @Override public Long next() { return counter++; }
  });

final Sink<String, NotUsed> printlnSink =
   Flow.of(String.class)
     .zip(numbers)
     .to(Sink.foreach(p ->
       System.out.println(String.format("[%s:%d] %s", prefix, (int) p.second(), p.first()))
     ));
```

## Implement once, use everywhere

An interesting side note here on the availability and compatibility of Java and Scala DSLs for Akka Streams.
As you know, the actual types of the DSL exist respectively in the `akka.stream.javadsl` and `akka.stream.scaladsl` packages.

You may have noticed that all of the Akka Stream's operators accept arguments in of types such as `akka.stream.Graph<SourceShape<T>, Mat>` or `akka.stream.Graph<FlowShape<A, B>, Mat>`, the exact reason it is like that is in order to allow custom stages to feel *native* as-if they were a built-in `Source` of `Flow` themselves.

Does this mean each custom operation has to be implemented twice? The answer is: No!

The APIs are designed such, that if you implement a `GraphStage` of a given shape it can be used in both DSLs.
For example, the above examples were implemented in Java, but they can seamlessly be used by Scala APIs.
The opposite is true as well - a stage implemented in Scala can seamlessly be used by Java users:

```scala
import akka.stream.scaladsl.Source

Source.single("happy hakking!") // ScalaDSL
  .to(new PrintlnSink("example")) // can seamlessly used a Java defined GraphStage
  .run()
```

The only place where adding a thin adapter around your stage might be needed is if it works on Scala or Java collections or Futures,
then understandably each language would prefer to use it's own collections. We handle this in all existing operators,
so regardless if you're using Java or Scala you always get a "native" feel from all the operators.



So as you can see, creating custom Sinks and Sources is pretty simple - you don’t have to handle any back-pressure or demand juggling manually, the stages can be run fused together with other stages or executed as an asynchronous island without any additional work - Akka Streams handles all the hard parts of the Reactive Streams protocol without exposing you to it’s intricate rules and requirements.

Happy hakking!
