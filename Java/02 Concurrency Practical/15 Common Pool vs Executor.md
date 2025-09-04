parallelStream() and CompletableFuture.supplyAsync() both internally use the same common pool that by default has a fixed number of threads equal to the one returned by Runtime.getRuntime().availableProcessors().

This is not always ideal, as discussed in Sizing Thread Pools

The `CompletableFuture` version has an advantage: by contrast with the parallel Streams API, it allows you to specify a different Executor to submit tasks to. 

```java
CompletableFuture.supplyAsync(() -> shop.getName() + " price is " +
                                    shop.getPrice(product), 
                               executor);
```

When using the technique in Sizing Thread Pools for thread pool size, processing 5 shops (1 second simulated delay) takes 1021ms, processing 9 shops takes 1022ms – this trend continues up until the 400 threshold we calculated.

# General Rule of Thumb

• If you’re doing computation-heavy operations with no I/O, the Stream interface provides the simplest implementation (parallelStream) and the one that’s likely to be most efficient. **(If all threads are compute-bound, there’s no point in having more threads than processor cores.)**
• If your parallel units of work involve waiting for I/O (including network connections), the CompletableFutures solution provides more flexibility and allows you to match the number of threads to the wait/computer (W/C) ratio, as discussed in Sizing Thread Pools. 
		○ Another reason to avoid using parallel streams when I/O waits are involved in the stream-processing pipeline is that the laziness of streams can make it harder to reason about when the waits happen.

# Note

Also see [[10 CompletableFuture.thenCombine]] part in red for which pool is used with composing methods
