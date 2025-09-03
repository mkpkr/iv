Safety (also called concurrent type safety) is about ensuring that object instances remain self-consistent, regardless of any other operations that may be happening at the same time. If a system of objects has this property, it’s said to be safe or concurrently typesafe.

One way to think about concurrency is in terms of an extension to the regular concepts of object modeling and type safety. In nonconcurrent code, you want to ensure that regardless of what public methods you call on an object, it’s in a well-defined and consistent state at the end of the method. The usual way to do this is to keep all of an object’s state private and expose a public API of methods that alter the object’s state only in a way that makes senses for the design domain.

Concurrent type safety is the same basic concept as type safety for an object, but applied to the much more complex world in which other threads are potentially operating on the same objects on different CPU cores at the same time.

Imagine a simple stack class, when used by single-threaded client code, this is fine. However, preemptive thread scheduling can cause problems. For example, a context switch between execution threads can occur at this point in the code

```java
private String[] values = new String[16];
private int current = 0;

public void push(String s) {
	if (current < values.length) {
		values[current] = s;
		//context switch here
		current = current + 1;
	}    
}
...
```

If the object is then viewed from another thread, **one part of the state (values) will have been updated but the other (current) will not.**

In general, one strategy for safety is to **never return from a nonprivate method in an inconsistent state, and to never call any nonprivate method (and certainly not a method on any other object) while in an inconsistent state**. If this practice is **combined with a way of protecting the object (such as a synchronization lock or critical section)** while it’s inconsistent, the system can be guaranteed to be safe.

