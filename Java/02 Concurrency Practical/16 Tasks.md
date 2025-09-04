The low-level nature of `java.lang.Thread` makes it hard for many programmers to work with correctly or efficiently. Other languages that were released after Java learned from Java’s experience with threads and built upon them to provide alternative approaches. Some of those approaches have, in turn, influenced the design of `java.util.concurrent` and later innovations in Java concurrency.

In this case, our immediate goal is to have tasks (or work units) that can be **executed without spinning up a new thread for each one**. Ultimately, this means that the tasks have to be modeled as code that can be called rather than directly represented as a thread.

Then, these tasks can be **scheduled on a shared resource—a pool of threads**—that executes a task to completion and then moves on to the next task.

# Modeling Tasks

We’ll look at two different ways of modeling tasks: the `Callable` interface and the `FutureTask` class. We could also consider Runnable, but it is not always that useful, because the run() method does not return a value, and, therefore, it can perform work only via side effects.

If we assume that our thread capacity is finite, tasks must complete in bounded time. If we have the possibility of an infinite loop, some tasks could “steal” an executor thread from the pool, and this would reduce the overall capacity for all tasks from then on. Over time, this could eventually lead to exhaustion of the thread pool resource and no further work being possible. As a result, we must be careful that **any tasks we construct must obey the “terminate in finite time” principle.**

## Callable

The Callable interface represents a very common abstraction. It represents a piece of code that can be called and returns a result. Despite being a straightforward idea, this is actually a subtle and powerful concept that can lead to some extremely useful patterns.

```java
Callable<String> cb = () -> out.toString();
```

## FutureTask

The `FutureTask` class is one commonly used implementation of the Future interface, which also implements Runnable. This means that a `FutureTask` can be fed to executors.

Two convenience constructors for `FutureTask` are provided: one that takes a Callable and one that takes a `Runnable` (which uses `Executors.callable()` to convert the `Runnable` to a `Callable`).

The class provides a simple state model for tasks and management of a task through that model:

![[Pasted image 20250904183834.png]]