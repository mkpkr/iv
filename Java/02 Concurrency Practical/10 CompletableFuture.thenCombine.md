This focusses on thenCombine, for thenCompose see CompletableFuture.thenCompose

• thenCompose: A CompletableFuture is dependent on another - the value resulting from the execution of the first is used as input to the second.
• thenCombine: Combine the results of two independent CompletableFutures, and you don’t want to wait for the first to complete before starting the second.

------------

• f(x) and g(x) are long running tasks, r(y) takes the result of both f and g and performs another long running task.
• How would you write tasks to exploit threads perfectly in this case: two active threads while both f(x) and g(x) are executing, and one thread starting from when the first one completes up to the return statement.
• The third task can’t start before the first two finish.

CompletableFuture provides methods to compose Futures (maybe should have been ComposableFuture, but the complete method led to its name).
	 
One of those is:
	 
```java
CompletableFuture<V> thenCombine(CompletableFuture<U> other,
	                             BiFunction<T, U, V> fn)
```
	 
Example 1

```java
Future<Double> futurePriceInUSD = 
    CompletableFuture.supplyAsync(() -> shop.getPrice(product))      
                     .thenCombine(CompletableFuture.supplyAsync(() ->  exchangeService.getRate(Money.EUR, Money.USD)),
                                  (price, rate) -> price * rate) //BiFunction
    );
```

Example 2
 
```java
public class CFCombine {
 
    public static void main(String[] args) throws ExecutionException,
     InterruptedException {
 
        ExecutorService executorService = Executors.newFixedThreadPool(10);
        int x = 1337;
 
        CompletableFuture<Integer> a = new CompletableFuture<>();
        CompletableFuture<Integer> b = new CompletableFuture<>();
        CompletableFuture<Integer> c = a.thenCombine(b, (y, z)-> r(y,z));
        executorService.submit(() -> a.complete(f(x)));
        executorService.submit(() -> b.complete(g(x)));
 
        System.out.println(c.get());
        executorService.shutdown();
 
    }
}
```
 
The thenCombine line is critical: without knowing anything about computations in the Futures a and b, it creates a computation that’s scheduled to run in the thread pool only when both of the first two computations have completed. 

**Note: c runs on our executorService even though we didn't explicitly tell it to (it apparently can run in ForkJoinPool.commonPool() which gets lazily created when necessary, but I haven't been able to emulate that). I also ran with 2 ExecutorServices, one to run a and one to run b, and also tried switching a.combineWith(b) to b.combineWith(a) - the result there is that it always runs on the ExecutorService of the thread that finished last – so add delays and if a finishes last, it runs on the ExecutorService a ran on, and same with b.**

The third computation, c, adds their results and (most important) isn’t considered to be eligible to execute on a thread until the other two computations have completed (rather than starting to execute early and then blocking). 

**Therefore, no actual wait operation is performed – the alternative would be starting a thread to perform the third computation and waiting for the first 2 to finish, meaning more threads are waiting than necessary.** By contrast, using thenCombine schedules the summing computation only after both f(x) and g(x) have completed.

