Java’s threading model is based on the following two fundamental concepts:

- Shared, visible-by-default mutable state
- Preemptive thread scheduling by the operating system

Let’s consider the following most important aspects of these ideas:

- Objects can be easily shared between all threads within a process.
- Objects can be changed (“mutated”) by any threads that have a reference to them.
- The thread scheduler (the operating system) can swap threads on and off cores at any time, more or less.
- Methods must be able to be swapped out while they’re running (otherwise, a method with an infinite loop would steal the CPU forever).
- This, however, runs the risk of an unpredictable thread swap, leaving a method “half-done” and an object in an inconsistent state.
- Objects can be locked to protect vulnerable data.

- Technically, Java provides monitors on each of its objects, which combine a lock (aka mutual exclusion) with the ability to wait for a certain condition to become true.