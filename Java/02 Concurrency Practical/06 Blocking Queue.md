A queue is a wonderful abstraction for concurrent programming, it provides a simple and reliable way to assign work units to processing resources.

A number of patterns in multithreaded Java programming rely heavily on the thread-safe implementations of Queue. One very common use case is the use of a queue to transfer work units between threads. This pattern is often ideally suited for the simplest concurrent extension of Queue: the BlockingQueue.

# `put()` and `take()`

- When trying to put() to the queue, it will cause the putting thread to wait for space to become available if the queue is full.
- When trying to take() from the queue, it will cause the taking thread to block if the queue is empty.

These two properties are very useful because if one thread (or pool of threads) is outperforming the other, the faster thread is forced to wait, thus regulating the overall system.

Java ships with two basic implementations of the BlockingQueue interface: the LinkedBlockingQueue and the ArrayBlockingQueue. They offer slightly different properties; for example, the array implementation is very efficient when an exact bound is known for the size of the queue, whereas the linked implementation may be slightly faster under some circumstances.

However, the real difference between the implementations is in the implied semantics. **Although the linked variant can be constructed with a size limit, it is usually created without one**, which leads to an object with a **queue size of Integer.MAX_VALUE**. This is effectively infinite—a real application would never be able to recover from a backlog of over two billion items in one of its queues.

So, although in theory the **put() method on `LinkedBlockingQueue` can block, in practice, it never does**. This means that the **threads that are writing to the queue can effectively proceed at an unlimited rate.**

**In contrast, the ArrayBlockingQueue has a fixed size for the queue—the size of the array that backs it**. If the producer threads are putting objects into the queue faster than they are being processed by receivers, **at some point the queue will fill completely, further attempts to call put() will block**, and the producer threads will be forced to slow their rate of task production.

This property of the ArrayBlockingQueue is one form of what is known as back pressure, which is an important aspect of engineering concurrent and distributed systems.

Let’s see the BlockingQueue in action in an example: altering the account example to use queues and threads. The aim of the example will be to get rid of the need to lock both account objects.

![[Pasted image 20250903221440.png]]

```java
public class AccountManager {
    private ConcurrentHashMap<Integer, Account> accounts = new ConcurrentHashMap<>();
    private volatile boolean shutdown = false;
 
    private BlockingQueue<TransferTask> pending = new LinkedBlockingQueue<>();    //pending withdrawals
    private BlockingQueue<TransferTask> forDeposit = new LinkedBlockingQueue<>(); //successful withdrawal, pending deposit
    private BlockingQueue<TransferTask> failed = new LinkedBlockingQueue<>();     //failed withdrawals
 
    private Thread withdrawals;
    private Thread deposits;

    public void submit(TransferTask transfer) {
        if (shutdown) {
            return false;
        }
        pending.put(transfer);
    }

    public void init() {
        //initialize threads
    }

    public void await() throws InterruptedException {
        withdrawals.join();
        deposits.join();
    }
 
    public void shutdown() {
        shutdown = true;
    }

    ...
```

# Blocking Problem

However, the code as written does not execute cleanly, despite the calls to shutdown() and await() because of the blocking nature of the calls used.

If the threads are using the shutdown variable to decide whether to continue or not, then the manager thread will successfully stop putting requests to pending queue, however if withdrawal or deposit queues are empty, then the take() methods will block forever waiting for an element to arrive, and will never get to test the shutdown variable.

![[Pasted image 20250903221608.png]]
To solve this, we need to look at methods in BlockingQueue that use a timeout...

(Note we can also use an ExecutorService and call shutdown() see [Executors](onenote:#Executors&section-id={6313e19f-70e1-4eec-b1c4-645f3673344b}&page-id={c5d1575b-1657-4a78-8c34-e777b34db4c9}&end))

# BlockingQueue APIs

There are three ways to interact with a BlockingQueue if the queue is full/empty:

- Block
	- void put(E e)
	- E take()

- Return a value (boolean or null) indicating failure
	- boolean offer(E e)
	- boolean offer(E e, long timeout, TimeUnit unit)
	- E poll()
	- E poll(long timeout, TimeUnit unit)
	- E peek() //examine

- Throw an exception – try not to use this API
	- boolean add(E e)
	- boolean remove(Object o)
	- E element() //examine

 The second option  (without timeout) and third option are the optionsprovided by the Queue interface, which is the super interface of BlockingQueue.

The third option, add() and remove() are problematic for several reasons:

- They throw on IllegalStateException and NoSuchElementException respectively are runtime exceptions and so do not need to be explicitly handled
- Exceptions in general are to be used to deal with exceptional circumstances, that is, those that a program does not normally consider to be part of normal operation. The situation of an empty queue is, however, an entirely possible circumstance.
- **Exceptions are, in general, quite expensive to use, due to stack trace construction when the exception is instantiated and stack unwinding during the throw.** It is good practice not to create an exception unless it is going to immediately be thrown.

**For these reasons, we recommend against using the exception-throwing form of the BlockingQueue APIs.**