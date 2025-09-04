G1 is a relatively new collector for the Java platform. It became production-quality at Java 8u40 and was made the default collector with Java 9 (in 2017). It was originally intended as a low-pause collector but in practice has evolved into a **general-purpose collector** (hence its default status).

It is not only a generational garbage collector, but it is also regionalized, which means that the G1 Java heap divides the heap into equal-sized regions (such as 1, 2, or 4 MB each, and by default aiming for 2048 regions) utilizing non-continuous spaces which enables the G1 to efficiently deal with very large heap which can be dynamically sized.

Note how there are no longer just 2 survivor spaces (from and to).

![[Pasted image 20250904194020.png]]

Regionalization has been introduced to support the idea of **predictability of GC pauses**. Older collectors (such as Parallel) suffered from the problem that once a GC cycle had begun, it needed to run to completion, regardless of how long that took (i.e., they were all-or-nothing).

**Note the default maximum pause time is 200ms and can be adjusted with -`XX:MaxGCPauseMillis=<ms>`**

G1 provides a collection strategy that **should not result in longer pause times for larger heaps**. It was designed to avoid all-or-nothing behavior, and a key concept for this is the **pause goal**. This is how long the program can pause for GC before resuming execution. **G1 will do everything it can to hit your pause goals, within reason.**

During a pause, surviving objects are evacuated to another region (like Eden objects being moved to survivor spaces), and the region is placed back on the free list of empty regions.

**Young collections in G1 are fully STW and will run to completion.** This avoids race conditions between collection and allocation threads (which could occur if young collections ran concurrently with application threads). (The generational hypothesis is that only a small fraction of objects encountered during a young collection are still alive. So, the time taken for a young collection should be very small, and much less than the pause goal.)

The collection of old objects has a different character from young collections—first, because once objects have reached the old generation, they tend to live for a considerable length of time. Second, the space provided for the old generation tends to be much larger than the young generation.

**The first piece of the old collection is a concurrent marking phase.**

Once this completes, then a young collection is immediately triggered. This is followed by a mixed collection, which **collects old regions based on how much garbage they have in them** (which can be deduced from the statistics gathered during the concurrent mark). The regions with the least amount of live data i.e. the ones with most garbage, are collecte first; hence the name **Garbage First**.

Surviving objects from the old regions are evacuated into fresh old regions (and compacted).

The nature of the G1 collection strategy also **allows the platform to collect statistics on how long (on average) a single region takes to collect**. This is how pause goals are implemented—**G1 will collect only as many regions as it has time for** (although there may be overruns if the last region takes longer to collect than expected).

It is possible that the collection of the entire old generation cannot be completed in a single GC cycle. In this case, G1 just collects a set of regions and then completes the collection, releasing the CPU cores that were being used for GC.

One other point is worth mentioning: it is possible to allocate objects that are larger than a single region. In practice, this means a large array (often of bytes or other primitives). Such objects require a special type of region—a **humongous region**. These require special treatment by the GC because the space allocated for large arrays must be contiguous in memory. If sufficient free regions are adjacent to each other, they can be converted to a single humongous region and the array can be allocated.

If there isn’t anywhere in memory where the array can be allocated (even after a young collection), then memory is said to be fragmented. The GC must perform a fully STW and compacting collection to try to free up sufficient space for the allocation.