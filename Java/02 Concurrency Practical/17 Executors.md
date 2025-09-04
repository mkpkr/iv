```java
public interface Executor {
    void execute(Runnable command);
}
 
public interface ExecutorService extends Executor {
    <T> Future<T>  submit(Callable<T> task);

        Future<?>  submit(Runnable task);

    ...
}
```

Executor is not widely used—far more common is the ExecutorService interface that extends Executor and adds submit() as well as several life cycle methods, such as shutdown().

The JDK provides the Executors class, which is a collection of static helper methods (mostly factories). Four of the most commonly used methods follow:

- newSingleThreadExecutor()
- newFixedThreadPool(int nThreads)
- newCachedThreadPool()
- newScheduledThreadPool(int corePoolSize)

The executors are mostly not implemented as distinct types (except ScheduledThreadPoolExecutor) but instead represent different parameter choices when constructing an underlying threadpool.

# Single-Threaded Executor

This version of the executor is often useful for testing because it can be made more deterministic that other forms.

This is essentially an encapsulated combination of a single thread and a task queue (which is a blocking queue).

Client code places an executable task onto the queue via submit(). The single execution thread then takes the tasks one at a time and runs each to completion before taking the next task.

```java
var pool = Executors.newSingleThreadExecutor();

Runnable hello = () -> System.out.println("Hello world");

pool.submit(hello);
```

However, care must still be taken—for example, if the main thread exits immediately, the submitted job may not have had time to be collected by the pool thread and may not run. Instead of exiting straightaway, it is wise to call the shutdown() method on the executor first.

Note: the combination of a task that loops infinitely and an orderly shutdown request will interact badly, resulting in a threadpool that never shuts down.

# Fixed-Thread Pool

Essentially the multiple-thread generalization of the single-threaded executor. As with the single-threaded variant, if all threads are in use, new tasks are stored in a blocking queue until a thread becomes free.

This version of the threadpool is particularly useful if task flow is stable and known and if all submitted jobs are roughly the same size, in terms of computation duration.

```java
var pool = Executors.newFixedThreadPool(2);
```

If the executor threads die, they are not replaced. If the possibility exists of the submitted jobs throwing a runtime exception, this can lead to the threadpool starving.

# Cached Thread Pool

The fixed threadpool is often used when the activity pattern of the workload is known and fairly stable. However, if the incoming work is more uneven or bursty, then a pool with a fixed number of threads is likely to be suboptimal.

The CachedThreadPool is an unbounded pool, that will reuse threads if they are available but otherwise will create new threads as required to handle incoming tasks.

```
var pool = Executors.newCachedThreadPool();
```

Threads are kept in the idle cache for 60 seconds, and if they are still present at the end of that period, they will be removed from the cache and destroyed.

It is, of course, still very important that the tasks do actually terminate. If not, then the threadpool will, over time, create more and more threads and consume more and more of the machine’s resources and eventually crash or become unresponsive.

In general, the trade-off between fixed-size thread pools and cached thread pools is largely about reusing threads versus creating and destroying threads to achieve different effects. The design of the CachedThreadPool should give better performance with small asynchronous tasks as compared to the performance achieved from fixed-size pools.

# ScheduledThreadPoolExecutor

ScheduledExecutorService pool = Executors.newScheduledThreadPool(4);

The ScheduledThreadPoolExecutor is a surprisingly capable choice of executor and can be used across a wide range of circumstances.

The scheduled service extends the usual executor service and adds a small amount of new capabilities:

- schedule()
	- runs a one-off task after a specified delay

- scheduleAtFixedRate()
	- activate a new copy of the task on a fixed timetable
	- if any execution of the task encounters an exception, subsequent executions are suppressed, otherwise, the task will only terminate via cancellation or termination of the executor
	- if any execution of this task takes longer than its period, then subsequent executions may start late, but will not concurrently execute

- scheduleWithFixedDelay()
	- activate a new copy of the task with the given delay between the termination of one execution and the commencement of the next
	- if any execution of the task encounters an exception, subsequent executions are suppressed, otherwise, the task will only terminate via cancellation or termination of the executor