This is a simple, rough-and-ready model for reasoning about the efficiency of sharing work over multiple execution units.

The basic premise is that we have a single task that can be subdivided into smaller units for processing. This allows us to use multiple execution units to speed up the time taken to complete the work.

So, if we have N processors or threads, and if T1 is the time the job would take on a single processor, then we might naively expect the elapsed time to be T1 / N.

In this model, we can finish the job as quickly as we like by just adding processors/threads.

However, splitting up the work is not free! A (hopefully small) overhead is involved in the subdividing and recombination of the task. Let’s assume that this communication overhead (sometime called the serial part of the calculation) is an overhead that amounts to a few percent, and we can represent it by a number s (0 < s < 1). So, a typical value for s might be 0.05 (or 5%, whichever way you’d prefer to express it). This means that the task will always take at least s * T1 to complete—no matter how many processing units we throw at it.

This assumes that s does not depend upon N, of course, but in practice, the dividing up of work that s represents may get more complex and require more time as N increases. It is extremely difficult to conceive of a system architecture in which s decreases as N increases. So the simple assumption of “s is constant” is usually understood to be a best-case scenario.

So, the easiest way to think about Amdhal’s law is: if s is between 0 and 1, then the maximum speedup that can be achieved is 1 / s. This result is somewhat depressing—it means that if the communication overhead is just 2%, the maximum speedup that can ever be achieved (even with thousands of processors working at full speed) is 50X.

Amdahl’s law has a slightly more complex formulation, which is represented like this:

$$T(N) = s + (1/N) * (T1 - s)$$

This can be seen visually below. Note that the **x-axis is a logarithmic scale**—the convergence to 1 / s would be very hard to see in a linear scale representation.

![[Pasted image 20250903215509.png]]