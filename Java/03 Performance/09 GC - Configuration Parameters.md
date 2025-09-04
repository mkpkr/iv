The JVM ships with a huge number of useful parameters (at least a hundred) that can be used to customize many aspects of the runtime behavior of the JVM.

**If a switch starts with -X:, it’s nonstandard and may not be portable across JVM implementations** (such as HotSpot or Eclipse OpenJ9).

**If it starts with -XX:, it’s an extended switch and isn’t recommended for casual use. Many performance-relevant switches are extended switches.**

Some switches are **Boolean** in effect and take a **+ or -** in front of them to turn it on or off. Other switches take a parameter, such as -XX:CompileThreshold=20000 (which would set the number of times a method needs to be called before being considered for JIT compilation to 20000).

- Basic garbage collection switches
	- `-Xms<size in MB>m`
		- Initial size of the heap (default 1/64 physical memory)
	- `-Xmx<size in MB>m`
		- Maximum size of the heap (default 1/4 physical memory)
	- `-Xmn<size in MB>m`
		- Size of the young generation in the heap
	- `-XX:-DisableExplicitGC`
		- Prevents calls to System.gc() from having any effect

One unfortunately common technique is to set the size of -Xms to the same as -Xmx. This then means that the process will run with exactly that heap size and will not resize during execution. Superficially, this makes sense, and it gives the illusion of control to the developer. However, in practice, this approach is an antipattern. **Modern GCs have good dynamic sizing algorithms, and artificially constraining them almost always does more harm than good.**

**In 2022, best practice for most workloads, in the absence of any other evidence, is to set Xmx and to not set Xms at all.**

It’s also worth noting the behavior of the JVM in a container. For Java 11 and 17, “physical memory” means the container limit, so the heap max size must fit within any container limit and with space for the non-Java heap memory and any other processes other than the JVM. **Early versions of Java 8 do not necessarily respect container limits, so the advice is always to upgrade to Java 11 if you are running your application in containers.** Also talked about in [Docker Quick Note](onenote:..\Kubernetes\Intro.one#Docker%20Quick%20Note&section-id={f952fda3-d8df-4e3a-b202-66383b5e7a0c}&page-id={500cddc7-3984-4677-84ed-951cbb284b31}&end).

For the G1 collector, two other settings may be useful during tuning exercises

- `-XX:MaxGCPauseMillis=50`
	- Indicates to G1 that it should try to pause for no more than 50 ms during one collection
- `-XX:GCPauseIntervalMillis=200`
	- Indicates to G1 that it should try to run for at least 200 ms between collections

The switches can be combined, such as to set a maximum pause goal of 50 ms with pauses occurring no closer together than 200 ms. Of course, there’s a limit on how hard the GC system can be pushed. There has to be enough pause time to take out the trash. A pause goal of 1 ms per 100 years is certainly not going to be attainable or honored.