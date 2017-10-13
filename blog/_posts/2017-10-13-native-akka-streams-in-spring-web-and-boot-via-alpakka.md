Native Akka Streams in Spring Web and Boot via Alpakka
------------------------------------------------------

[Alpakka](https://developer.lightbend.com/docs/alpakka/current), is our way of solving the *Reactive Enterprise Integration* problem. We do so by providing a collection of *streaming* plug-and-play connectors to various technologies. Today we'd like to highlight one specific integration, since it's the result and 

Today we'd like to show off the

The success of Reactive Streams With the [Reactive Streams](http://reactive-streams.org) initiative, and its [successful inclusion in the Java 9](http://download.java.net/java/jdk9/docs/api/java/util/concurrent/Flow.html) (launched just weeks earlier)

Also, Akka Streams is one of the first libraries to provide native support for Java 9's `java.util.concurrent.Flow.*` (as of writing, Spring does not provide this support yet, while RxJava provides bridges separately packaged), so if you're looking for a future-ready Reactive Streams implementation, simply use simply Akka Streams `2.5.6+` and you're ready to go. 

### Using Akka Streams natively in Spring web endpoints

Thanks to [Alpakka](https://developer.lightbend.com/docs/alpakka/current)'s integration code, you're now able to simply depend on the [akka-stream-alpakka-spring-web](https://developer.lightbend.com/docs/alpakka/latest/spring-web.html) Alpakka module: 

```
// build.gradle
dependencies {
  compile group: 'com.lightbend.akka', name: 'akka-stream-alpakka-spring-web_2.12', version: VERSION_HERE
}
```

And right away you're able to return Akka Streams in your endpoints, like this for example:

```
import akka.NotUsed;
import akka.stream.javadsl.Source;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class SampleController {

  @RequestMapping("/")
  public Source<String, NotUsed> index() {
    return
      Source.repeat("Hello world!")
        .intersperse("\n")
        .take(10);
  }

}
```

#### How does this work? Why is Reactive Streams important?

One may think that this integration was a lot of work, and in days before Reactive Streams that would have certainly been true. Now thankfully many major libraries provide support for Reactive Streams, which means that inter-op and the same semantics can be assumed from them.

In this case, since Akka Streams implement Reactive Streams, and Spring now provides a way to register Reactive Streams type adapters, libraries can use that to provide this inter-op very trivially – by exposing their internal `Flow.Publisher`/`Flow.Subscriber` which then "just works". The same can be said about adapting other RS implementations to be used in Akka Streams - you can simply wrap them into a Source or Sink, perserving all RS semantics.

In this specific case, we implemented a so-called `ReactiveAdapter`, and handle all the configuration for you. While we think that the naming here is somewhat misleading, since [Reactive is a lot more](https://www.oreilly.com/learning/why-reactive) than just the Reactive Streams interfaces (with which this Adapter works), this pluggable architecture makes it nice for implementations to provide their adapters. Spring's own Reactor, in fact, [uses](https://github.com/spring-projects/spring-framework/blob/ac1d3b22c9d092cd7d093cca9bbe4feede134fca/spring-core/src/main/java/org/springframework/core/ReactiveAdapterRegistry.java#L240) this Adapter mechanism, so this indeed can be seen as completely "native" support.

