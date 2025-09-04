G1 is established as a very effective collector across a wide variety of workloads and application types. However, for some workloads (e.g., those that need pure throughput or are still running on Java 8), then another collector, such as Parallel, may be of use.

Some terminology:

- **Throughput**: The percentage of total time spent in useful application activity versus memory allocation and garbage collection. For example, if your throughput is 95%, that means the application code is running 95% of the time and garbage collection is running 5% of the time. You want higher throughput for any high-load business application.
	- E.g: we care about total pause time over a period – rather than individual pauses – so we could tolerate a total of X seconds paused every Y period of time, regardless of how many or how long the individual pauses were

- **Latency**: Application responsiveness, which is affected by garbage collection pauses. In any application interacting with a human or some active process (such as a valve in a factory), you want the lowest possible latency.
	- E.g: we care about individual pause times – cannot tolerate anything over 200ms ever, even if we have a longer total paused time over a period of time

The parallel collector is also known as the **throughput collector** because it is often the best choice when **throughput is more important than latency**. You can **use the parallel collector when long pauses are acceptable, such as bulk data processing, batch jobs, etc**. If the application requirement is to achieve the highest throughput and if pauses of one second or more are acceptable, a parallel collector might be appropriate.

The Parallel collector was the default until Java 8, and it can still be used as an alternative choice to G1 today. The name Parallel needs a bit of explanation, because concurrent and parallel are both used to describe properties of GC algorithms:

- *Concurrent*—GC threads can run at the same time as application threads.
- *Parallel*—The GC algorithm is multithreaded and can use multiple cores.

The heap is not regionalized. Instead, the generations are contiguous areas of memory, which have headroom to grow and shrink as needed. In this heap configuration there are two survivor spaces. They are sometimes referred to as From and To as discussed in [GC – Mark  Sweep](onenote:#GC%20–%20Mark%20%20Sweep&section-id={01072069-94c3-43ed-b4e9-ebf8146c6fca}&page-id={4a127b8e-74eb-497a-97bc-176cdfac59dc}&end), and one of the survivor spaces is always empty unless a collection is under way.

Parallel is a very efficient collector—the most efficient one available in mainstream Java—but it comes with a drawback: **it has no real pause goal capability and old collections (that are STW) must run to completion, regardless of how long it takes.**