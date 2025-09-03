- **If all threads are compute-bound, there’s no point in having more threads than processor cores, but if they are somewhat or mostly I/O bound there is**
- 
- In the great book Java Concurrency in Practice (Addison-Wesley, 2006; [http://jcip.net](http://jcip.net/)), Brian Goetz and his co-authors give some advice on finding the optimal size for a thread pool. This advice is important because **if the number of threads in the pool is too big, the threads end up competing for scarce CPU and memory resources, wasting their time performing context switching. Conversely, if this number is too small, some of the cores of the CPU will remain underused**. Goetz suggests that you can calculate the right pool size to approximate a desired CPU use rate with the following formula:
- 
- **`Nthreads = Ncpu * Ucpu * (1 + W/C)`**
	- `Ncpu` is the number of cores, available through `Runtime.get-Runtime().availableProcessors()`
	- `Ucpu` is the target CPU use (between 0 and 1).
	- `W/C` is the ratio of wait time to compute time (0 – 100)
		- e.g when you choose 10 as the value of` W/C`, it means that the waiting time should be 10 times larger than the computing time
		- if there is no wait then **0 (CPU-bound task)**
			- **If all threads are compute-bound, there’s no point in having more threads than processor cores**
		- if we are mostly waiting **(99% of the time waiting) then 100**

- **Example, a laptop with 4 cores performing tasks with lots of HTTP requests:**
	- **The application is spending about 99 percent of its time waiting for the shops’ responses, so you could estimate a W/C ratio of 100.**
	- If your target is 100 percent CPU use, you should have a pool with ~400 threads. (4 * 1 * (1+100))

- **Example, we fetch prices from a pre-defined list of shops:**
	- **In practice, it’s wasteful to have more threads than shops, because you’ll have threads in your pool that are never used.** For this reason, you need to set up an Executor with a fixed number of threads equal to the number of shops you have to query, so that you have one thread for each shop. Also set an upper limit of threads to avoid a server crash for a larger number of shops.
		- **`Executors.newFixedThreadPool(Math.min(shops.size(), 400)`**

- In "Modern Java in Action", processing 5 shops (1 second simulated delay) takes 1021ms, processing 9 shops takes 1022 – this trend continues up until the 400 threshold we calculated.