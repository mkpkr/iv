You should know the following things before beginning any kind of technical work:

1. What observable aspects of your code you’re measuring
2. How to measure those observables
3. What the goals are for the observables
4. How you’ll recognize when you’re done with performance tuning
5. What the maximum acceptable cost is (in terms of developer time invested and additional complexity in the code) for the performance tuning

# 1. Know What To Measure

TIP: To be a good performance engineer, you should understand terms such as **mean, median, mode, variance, percentile, standard deviation, sample size, and normal distribution.**

You should always tie your measurements, objectives, and conclusions to one or more of the basic observables we introduced in [Terminology](onenote:#Terminology&section-id={01072069-94c3-43ed-b4e9-ebf8146c6fca}&page-id={5598d60f-b57f-4e99-be3e-7509968b88d8}&end). Some typical observables that are good targets for performance tuning are:

- **Average time taken** for the `handleRequest() `method to run (**after warmup**)
- The **90th percentile of the system’s end-to-end latency with 10 concurrent clients**
- The **degradation of the response time as you increase from 1 to 1,000 concurrent users**

# 2. Know How to Measure

We have two ways to determine precisely how long a method or other piece of Java code takes to run:

1. Measure it directly, by inserting measurement code into the source class.

```java
long t0 = System.currentTimeMillis();  

methodToBeMeasured();  

long t1 = System.currentTimeMillis();
```

- What happens if `methodToBeMeasured()` takes under a millisecond to run?
- There are also cold-start effects to worry about: JIT compilation means that later runs of the method may well be quicker than earlier runs.

2. Transform the class that is to be measured at class loading time.

- `methodToBeMeasured()` is loaded by a special class loader that adds in bytecode at the start and end of the method to record the times at which the method was entered and exited. These timings are typically written to a shared data structure, which is accessed by other threads. These threads act on the data, typically either writing output to log files or contacting a network-based server that processes the raw data.
- This technique lies at the heart of many professional-grade Java performance-monitoring tools (such as New Relic), but actively maintained open source tools that fill the same niche have been scarce. This situation may now be changing with the rise of the OpenTelemetry OSS libraries and standards and their Java auto-instrumentation subproject.

**Java methods start off interpreted, then switch to compiled mode. For true performance numbers, you have to discard the timings generated when in interpreted mode, because they can badly skew the results.** Later we’ll discuss in more detail how you can know when a method has switched to compiled mode.

The next question is, what do you want the numbers to look like when you’ve finished tuning?

# 3. Know Your Performance Goals

In most cases, this should be a simple and precisely stated goal, such as the following:

- Reduce the 90th percentile end-to-end latency by 20% at 10 concurrent users
- Reduce the mean latency of handleRequest() by 40%

**Be aware: optimizing for one performance goal can negatively impact on another.**

Sometimes it’s necessary to do some initial analysis, such as determining what the important methods are, before setting goals, such as making them run faster. This is fine, but after the initial exploration, it’s almost always better to stop and state your goals before trying to achieve them. Too often developers will plow on with the analysis without stopping to elucidate their goals.

# 4. Know When to Stop

It’s easy to get sucked into performance tuning. If things go well, the temptation to keep pushing and do even better can be very strong. Alternatively, if you’re struggling to reach your goal, it’s hard to keep from trying out different strategies in an attempt to hit the target.

Knowing when to stop involves having an awareness of your goals but also a sense of what they’re worth. **Getting 90% of the way to a performance goal can often be enough**, and the engineer’s time may well be spent better elsewhere.

Another important consideration is how much effort is being spent on rarely used code paths. **Optimizing code that accounts for 1% or less of the program’s runtime is almost always a waste of time.**

- **Optimize what matters**, not what is easy to optimize.
- **Hit the most important (usually the most often called) methods first.**
- **Take low-hanging fruit as you come across it,** but be aware of how often the code that it represents is called.

# 5. Know the Cost of Higher Performance

All performance tweaks have a price tag attached, such as the following:

- There’s the **time taken to do the analysis and develop an improvement** (and it’s worth remembering that the cost of developer time is almost always the greatest expense on any software project).
- There’s the **additional technical complexity that the fix will probably have introduced**. (There are performance improvements that also simplify the code, but they’re not the majority of cases.)
- **Additional threads may have been introduced** to perform auxiliary tasks to allow the main processing threads to go faster, and **these threads may have unforeseen effects on the overall system at higher loads.**

It often helps to have some idea of what the maximum acceptable cost for higher performance is. For example, a developer could decide that no more than a week should be spent optimizing, or that the optimized classes should not grow by more than 100% (double their original size).