- Only objects—not primitives—can be locked.
- Locking an array of objects doesn’t lock the individual objects.
- A synchronized method can be thought of as equivalent to a synchronized (this) { ... } block that covers the entire method (but note that they’re represented differently in bytecode).
- A static synchronized method locks the Class object, because there’s no instance object to lock.
- If you need to lock a Class object, consider carefully whether you need to do so explicitly or by using getClass(), because the behavior of the two approaches will be different in a subclass.
- Synchronization in an inner class is independent of the outer class (to see why this is so, remember how inner classes are implemented).
- synchronized doesn’t form part of the method signature, so it can’t appear on a method declaration in an interface.
- Unsynchronized methods don’t look at or care about the state of any locks, and they can progress while synchronized methods are running.
- Java’s locks are reentrant—a thread holding a lock that encounters a synchronization point for the same lock (such as a synchronized method calling another synchronized method on the same object) will be allowed to continue.

# synchronized

What is being synchronized? It is the memory representation in different threads of the object being locked.

That is, after the synchronized method (or block) has completed, any and all changes that were made to the object being locked are flushed back to main memory before the lock is released.

In addition, when a synchronized block is entered, after the lock has been acquired, any changes to the locked object are read in from main memory, so the thread with the lock is synchronized to the main memory’s view of the object before the code in the locked section begins to execute.

# volatile

- The value seen by a thread is always reread from the main memory before use.
- Any value written by a thread is always flushed through to the main memory before the bytecode instruction completes.

This is sometimes described as being “like a tiny little synchronized block” around the single operation, but this is misleading because volatile does not involve any locking.

The action of synchronized is to use a mutual exclusion lock on an object to ensure that only one thread can execute a synchronized method on that object. Synchronized methods can contain many read and write operations on the object, and they will be executed as an indivisible unit (from the point of view of other threads) because the results of the method executing on the object are not seen until the method exits and the object is flushed back to main memory.

The key point about volatile is that it allows for only one operation on the memory location, which will be immediately flushed to memory. This means either a single read, or a single write.

For example the following are not safe to use on a volatile as they are a state-dependent update: ++, --, v = v + 1, v = v – 1