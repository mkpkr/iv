# Latency

Latency is the **end-to-end time taken to process a single work unit at a given workload**. Quite often, latency is quoted just for “normal” workloads, but an often-useful performance measure is the graph showing latency as a function of increasing workload.

The below graph shows a sudden, nonlinear degradation of a performance metric (e.g., latency) as the workload increases. This is usually called a performance elbow (or “hockey stick”).

![[Pasted image 20250904184619.png]]

# Throughput

Throughput is the **number of units of work that a system can perform in some time period with given resources**. One commonly quoted number is transactions per second on some reference platform (e.g., a specific brand of server with specified hardware, OS, and software stack).

# Utilization

Utilization represents **the percentage of available resources that are being used to handle work units**, instead of housekeeping tasks (or just being idle). People will commonly quote a server as being, for example, 10% utilized. This refers to the percentage of CPU processing work units during normal processing time. Note that **the difference can be very large between the utilization levels of different resources, such as CPU and memory**.

# Efficiency

The efficiency of a system is equal to the **throughput divided by the resources used**. A system that requires more resources to produce the same throughput is less efficient.

 If solution A requires twice as many servers as solution B for the same throughput, it’s half as efficient.

Remember that resources can also be considered in cost terms—if solution A costs twice as much (or requires twice as many staff to run the production environment) as solution B, then it’s only half as efficient.

# Capacity

Capacity is the **number of work units (such as transactions) that can be in flight through the system at any time**. That is, it’s the amount of simultaneous processing available at a specified latency or throughput.

# Scalability

**As resources are added to a system, the throughput (or latency) will change. This change in throughput or latency is the scalability of the system.**

If solution A doubles its throughput when the available servers in a pool are doubled, it’s scaling in a perfectly linear fashion. Perfect linear scaling is very, very difficult to achieve under most circumstances—remember Amdahl’s law.

You should also note that the scalability of a system depends on a number of factors, and it isn’t constant. A system can scale close to linearly up until some point and then begin to degrade badly. That’s a different kind of performance elbow.

# Degradation

**If you add more work units, or clients for network systems, without adding more resources, you’ll typically see a change in the observed latency or throughput.** This change is the degradation of the system under additional load.

The degradation will, under normal circumstances, be negative. That is, adding work units to a system will cause a negative effect on performance (such as causing the latency of processing to increase). But some circumstances exist under which degradation could be positive. For example, if the additional load causes some part of the system to cross a threshold and switch to a high-performance mode, this can cause the system to work more efficiently and reduce processing times, even though there is actually more work to be done. The JVM is a very dynamic runtime system, and several parts of it could contribute to this sort of effect.