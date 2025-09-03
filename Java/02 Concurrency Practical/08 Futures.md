# Future

The interface Future in java.util.concurrent is a simple representation of an asynchronous task: it is a type that holds the result from a task (usually on another thread) that may not have finished yet but may at some point in the future. The primary methods on a Future follow:

- get()
	- Gets the result. If the result isn’t yet available, will block until it is.
- get(long timeout, TimeUnit unit)
	- Same as get() but throws TimeoutException if blocks for timeout
- isDone()
	- Determine whether the computation has finished. It is nonblocking.
- cancel()
	- Allows the computation to be canceled before completion.
- isCancelled()

![[Pasted image 20250903221920.png]]
# CompletableFuture

The Java Future type is defined as an interface, rather than a concrete class. Any API that wants to use a Future-based style has to supply a concrete implementation of Future. These can be challenging for some developers to write and represents an obvious gap in the toolkit, so **from Java 8 onward, a new approach to futures was included in the JDK—a concrete implementation of Future that enhances capabilities.**

The class is called `CompletableFuture`—it is intended as a simple building block for building asynchronous applications. The central idea is that we can create instances of the `CompletableFuture<T>` type, and the object that is created represents the Future in an uncompleted (or “unfulfilled”) state.

Later, any thread that has a reference to the completable Future can call `complete()` on it and provide a value—this completes (or “fulfills”) the future. The completed value is immediately visible to all threads that are blocked on a `get()` call.

After completion, any further calls to `complete()` are ignored.

The `CompletableFuture` cannot cause different threads to see different values. It is either uncompleted or completed, and if it is completed, the value it holds is the value provided by the first thread to call complete().

This is obviously not immutability—the state of the `CompletableFuture` does change over time. However, it changes only once—from uncompleted to completed. There is no possibility of an inconsistent state being seen by different threads.

```java
public static Future<Long> getNthPrime(int n) {
    var numF = new CompletableFuture<Long>();
 
    new Thread( () -> {
		try {
            long num = NumberService.findPrime(n);
            numF.complete(num);
         } catch (Exception ex) {
            numF.completeExceptionally(ex); //get() will throw ExecutionException
         }
     } ).start();
 
    return numF;
}
```

One way to think about `CompletableFuture` is by analogy with client/server systems. The Future interface provides only a query method—` isDone()` and a blocking `get()`. This is playing the role of the client. An instance of CompletableFuture plays the role of the server side—it provides full control over the execution and completion of the code that is fulfilling the future and providing the value.

A slightly more concise way to achieve the same effect is to use the `CompletableFuture.supplyAsync() `method, passing a `Callable<T>` object representing the task to be executed. 

• `supplyAsync(Supplier<U> supplier)`
	○ Returns a new CompletableFuture that is asynchronously completed by a task running in the ForkJoinPool.commonPool() with the value obtained by calling the given Supplier.
• `supplyAsync(Supplier<U> supplier, Executor executor)`
	○ Returns a new CompletableFuture that is asynchronously completed by a task running in the given Executor with the value obtained by calling the given Supplier.


```java
public static Future<Long> getNthPrime(int n) {
    return CompletableFuture.supplyAsync(() -> NumberService.findPrime(n));
}
```
Runs on the common pool unless Executor specified:

```java
public Future<Double> getPriceAsync(String product) {
    return CompletableFuture.supplyAsync(() -> calculatePrice(product), myExecutor);
}
```
# Chaining Tasks

`CompletableFuture` **allows you to string tasks together in a chain**. You can use them to tell some worker thread to "go do some task X, and when you're done, go do this other thing using the result of X":


```java
ExecutorService exec = Executors.newSingleThreadExecutor();
CompletableFuture<Integer> f = CompletableFuture.supplyAsync(new MySupplier(), exec);
CompletableFuture<Integer> f2 = f.thenApply(new PlusOne());
```

- allOf()
	- Returns a new CompletableFuture that is completed when all of the given CompletableFutures complete.
- anyOf()
	- Returns a new CompletableFuture that is completed when any of the given CompletableFutures complete, with the same result.
- thenApply()
	- Run on same thread as future the method is called on
- thenApplyAsync()
	- Run on different thread
- thenCombine()
- thenCombineAsync()
- thenCompose()
- thenComposeAsync()
 
Note if Execturo isn't supplied then the ForkJoinPool.commonPool is used

`thenApply` methods accept a `Function<T,U>`

`thenCompose` methods accept a `Function<T,U extends CompletionStage<U>>`, providing a flattening capability - analogous to `Optional.flatMap` and `Stream.flatMap`. For example:

```java
Function<Long, CompletableFuture<Long>> f = l ->
  CompletableFuture.supplyAsync(() -> {
      System.out.println("Applying on thread: " +
                          Thread.currentThread().getName());
      return l * 2;
  });
```

We could pass this function to `thenApply()`, but the result would be a `CompletableFuture<CompletableFuture<Long>>`. Instead, `thenCompose()` flattens the result back to a `CompletableFuture<Long>`.


# FutureTask

See  [Tasks](onenote:#Tasks%20%20Execution&section-id={6313e19f-70e1-4eec-b1c4-645f3673344b}&page-id={7a7ee818-546e-4e15-a5d5-056ab0ccf892}&end)  