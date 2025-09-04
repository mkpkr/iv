The mental model for a Future and CompletableFuture is that of a computation that executes independently and concurrently. The result of the Future is available with get() after the computation completes. Thus, Futures are one-shot, executing code that runs to completion only once.

By contrast, the mental model for reactive programming is a Future-like object that, over time, yields multiple results. Consider a thermometer object. You expect this object to yield a result repeatedly, giving you a temperature value every few seconds.

1. The core point is that these examples are like Futures but differ in that they can complete (or yield) multiple times instead of being one-shot.
2. Another point is that in some cases, earlier results may be as important as ones seen later, whereas in others (e.g. thermometer), most users are interested only in the most-recent temperature.

You may think that the preceding idea is only a Stream. If your program fits naturally into the Stream model, a Stream may be the best implementation. In general, though, the reactive-programming paradigm is more expressive. A given Java Stream can be consumed by only one terminal operation. The Stream paradigm makes it hard to express Stream-like operations that can split a sequence of values between two processing pipelines (think fork) or process and combine items from two separate streams (think join). Streams have linear processing pipelines.

There are three main concepts:

- A **publisher** to which a subscriber can subscribe.
- The connection is known as a **subscription**.
- **Messages** (also known an **events**) are transmitted via the connection.

Multiple components can subscribe to a single publisher, a component can publish multiple separate streams, and a component can subscribe to multiple publishers.7

# Backpressure

Two simple but vital ideas that significantly complicate the Flow interfaces are pressure and backpressure. These ideas can appear to be unimportant, but they’re vital for thread utilization. Suppose that your thermometer, which previously reported a temperature every few seconds, was upgraded to a better one that reports a temperature every millisecond. Could your program react to these events sufficiently quickly, or might some buffer overflow and cause a crash? (Recall the problems giving thread pools large numbers of tasks if more than a few tasks might block.)

Now think of a vertical pipe containing messages written on balls. You also need a form of backpressure, such as a mechanism that restricts the number of balls being added to the column. Backpressure is implemented in the Java 9 Flow API by a request() method (in a new interface called Subscription) that invites the publisher to send the next item(s), instead of items being sent at an unlimited rate (the pull model instead of the push model).

*It’s worth noting that the requirement for built-in backpressure is **justified by the asynchronous nature** of the stream processing. In fact, **when synchronous invocations are being performed, the system is implicitly backpressured by the blocking APIs**. Unfortunately, this situation prevents you from executing any other useful task until the blocking operation is complete, so you end up wasting a lot of resources by waiting. Conversely, with asynchronous APIs you can maximize the use rate of your hardware, but run the risk of overwhelming some other slower downstream component.*

The Publisher may have multiple Subscribers, and you want backpressure to affect only the point-to-point connection involved. In the Java 9 Flow API, the Subscriber interface includes this method:

```java
void onSubscribe(Subscription subscription);
```

that’s called as the first event sent on the channel established between Publisher and Subscriber.

The Subscription object contains methods that enable the Subscriber to communicate with the Publisher, as follows:

```java
interface Subscription {
    void   cancel();  
    void   request(long n);  
}
```

- Do you send events to multiple Subscribers at the speed of the slowest, or do you have a separate queue of as-yet-unsent data for each Subscriber?
- **What happens when these queues grow excessively?**
- Do you drop events if the Subscriber isn’t ready for them?

The choice depends on the semantics of the data being sent. Losing one temperature report from a sequence may not matter, but losing a credit in your bank account certainly does!