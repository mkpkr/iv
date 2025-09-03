Let’s start with some basic facts about concurrency and multithreading:

- Concurrent programming is fundamentally about performance.
- There are basically no good reasons for implementing a concurrent algorithm if the system you are running on has sufficient performance that a serial algorithm will work.
- Modern computer systems have multiple processing cores—even mobile phones have two or four cores today.
- All Java programs are multithreaded, even those that have only a single application thread.

- e.g., for JIT compilation or garbage collection
- the standard library also includes APIs that use concurrency to implement multithreaded algorithms for some execution tasks

It is entirely possible that a Java application will run faster just by upgrading the JVM it runs on, due to performance improvements in the runtime.