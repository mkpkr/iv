- A thread pool with k threads can execute only k tasks concurrently. Any further task submissions are held in a queue and not allocated a thread until one of the existing tasks completes. This situation is generally good, in that it allows you to submit many tasks without accidentally creating an excessive number of threads, but you have to be wary of tasks that sleep or wait for I/O or network connections. In the context of blocking I/O, these tasks occupy worker threads but do no useful work while they’re waiting.
- Try taking four hardware threads and a thread pool of size 5 and submitting 20 tasks to it. You might expect that the tasks would run in parallel until all 20 have completed. But suppose that three of the first-submitted tasks sleep or wait for I/O. Then only two threads are available for the remaining 15 tasks, so you’re getting only half the throughput you expected (and would have if you created the thread pool with eight threads instead). It’s even possible to cause deadlock in a thread pool if earlier task submissions or already running tasks, need to wait for later task submissions, which is a typical use-pattern for Futures.

![[Pasted image 20250904184152.png]]
# Sleeping (and other blocking operations) considered harmful

- When you’re interacting with a human or an application that needs to restrict the rate at which things happen, one natural way to program is to use the sleep() method. A sleeping thread still occupies system resources, however.
- The lesson to remember is that tasks sleeping in a thread pool consume resources by blocking other tasks from starting to run. (They can’t stop tasks already allocated to a thread, as the operating system schedules these tasks.)
- It’s not only sleeping that can clog the available threads in a thread pool, of course. Any blocking operation can do the same thing. Blocking operations fall into two classes: waiting for another task to do something, such as invoking get() on a Future; and waiting for external interactions such as reads from networks, database servers, or human interface devices such as keyboards.

```java
	//bad
	work1();
	Thread.sleep(10000);
	work2();
	
	//better
	work1();
	scheduledExecutorService.schedule(ScheduledExecutorServiceExample::work2, 10, TimeUnit.SECONDS);

```

- This effect is something that you should always bear in mind when creating tasks. Tasks occupy valuable resources when they start executing, so you should aim to keep them running until they complete and release their resources. **Instead of blocking, a task should terminate after submitting a follow-up task to complete the work it intended to do.**
- As a final remark, we’ll note that code A and code B would be equally effective if threads were unlimited and cheap. (Project Loom)


*Whenever possible, this guideline applies to I/O, too. Instead of doing a classical blocking read, a task should issue a nonblocking “start a read” method call and terminate after asking the runtime library to schedule a follow-up task when the read is complete.*

*This design pattern may seem to lead to lots of hard-to-read code. But the Java CompletableFuture interface ([section 15.4](https://learning.oreilly.com/library/view/modern-java-in/9781617293566/kindle_split_029.html#ch15lev1sec4) and [chapter 16](https://learning.oreilly.com/library/view/modern-java-in/9781617293566/kindle_split_030.html#ch16)) abstracts this style of code within the runtime library, using combinators instead of explicit uses of blocking get() operations on Futures, as we discussed earlier.*