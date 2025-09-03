An atomic operation is guaranteed to be performed by a single hardware operation:

• reference reads and assignments are atomic
• primitive reads and assignments are atomic except long and double
	• even on a 64 bit machine there are no guarantees - longs and doubles can use 2 x 32 bits and require 2 reads/writes for lower/upper 32 bits
	• however if we use volatile then they are atomic

```java
volatle double a = 1.0;
volatle double b = 9.0;

a = b;
```

Note that we still need volatile for other primitives for visibility guarantees.

Performance tip: do not synchronize getters for primitives as it will decrease performance, you can just use volatile for visibility guarantees.

# Atomic Classes

Classes under `java.util.concurrent.atomic` provide more advanced atomic operations.

The implementations of the atomics are written to take advantage of modern processor features, which means lock-free access on almost all modern hardware, so the atomics behave in a similar way to a volatile field. 

However, they are wrapped in a Class API that goes further than what’s possible with volatiles. This API includes atomic (meaning all-or-nothing) methods for suitable operations—including state-dependent updates (which are impossible to do with volatile variables without using a lock). The end result is that atomics can be a very simple way for a developer to avoid race conditions on shared data.

volatile shutdown rewritten to use AtomicBoolean:

```java
public class TaskManager implements Runnable {
    private final AtomicBoolean shutdown = new AtomicBoolean(false);
 
    public void shutdown() {
        shutdown.set(true);
    }
 
    @Override
    public void run() {
        while (!shutdown.get()) {
            // do some work - e.g. process a work unit
        }
    }
}
```


• AtomicBoolean
• AtomicInteger
• AtomicIntegerArray
• AtomicLong
• AtomicLongArray
• AtomicReference
• AtomicReferenceArray
• DoubleAccumulator
• DoubleAdder
• LongAccumulator
• LongAdder

