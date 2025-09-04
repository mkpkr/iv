# Why is Java performance tuning hard?

Tuning for performance on the JVM (or, indeed, any other managed runtime) is inherently more difficult than for code that runs unmanaged. In a managed system, the entire point is to allow the runtime to take some control of the environment, so that the developer doesn’t have to cope with every detail.

Some of the most important aspects of the Java platform that contribute to making tuning hard follow:

- Thread scheduling
- Garbage collection (GC)
- Just-in-time (JIT) compilation

Some notes about time in Java:

- Most systems have several different clocks inside them.
- Millisecond timings are safe and reliable.
- Higher-precision time needs careful handling to avoid drift.