Two of the most fundamental questions when addressing latency are:

- Where is the nearest copy of the data that the CPU core needs to work on? and
- How long will it take to get to where the core can use it?

The main possibilities follow:

- **Registers**
	- A memory location that’s on the CPU and ready for immediate use.This is the part of memory that instructions operate on directly.
- **Main memory**
	- Usually DRAM. The access time for this is around 50 ns (but see later on for details about how processor caches are used to avoid this latency).
- **Solid-state drive (SSD)**
	- It takes 0.1 ms or less to access these disks, but they’re still typically more expensive compared to traditional hard disks.
- **Hard disk**
	- It takes around 5 ms to access the disk and load the required data into main memory.

Memory speed has improved more slowly than CPUs have added transistors, which means there’s a risk that the processing cores will fall idle due to not having the relevant data on hand to process.

To solve this problem, **caches**—small amounts of faster memory (SRAM, rather than DRAM)—have been introduced between the registers and main memory. This faster memory costs a lot more than DRAM, both in terms of money and transistor budget, which is why computers don’t simply use SRAM for their entire memory.

**Caches are referred to as L1 and L2** (some machines also have L3), with the numbers indicating **how physically close to the core the cache is** (closer caches will be faster).

![[Pasted image 20250904193403.png]]

When using synchronized or volatile, data has to be flushed to and read from main memory – this is much slower than a thread being able to work with data in its local cache.

# Understanding cache misses

For many high-throughput pieces of code, one of the main factors reducing performance is the number of L1 cache misses that are involved in executing application code. 

The following code runs over a 2 MiB array and prints the time taken to execute one of two loops. 

• The first loop increments 1 in every 16 entries of an int[]. 
	○ Almost always 64 bytes are in an L1 cache line (and a Java int is 4 bytes wide), so this means touching each cache line once.
• The second function, touchEveryItem(), increments every byte in the array, so it does 16 times as much work as touchEveryLine().

*Note that before you can get accurate results, we need to warm up the code, so that the JVM will compile the methods you’re interested in. We’ll talk about JIT warmup in JIT.*


```java
public class Caching {
    private final int ARR_SIZE = 2 * 1024 * 1024;
    private final int[] testData = new int[ARR_SIZE];
 
    private void touchEveryItem() {
        for (int i = 0; i < testData.length; i = i + 1) {
            testData[i] = testData[i] + 1;
        }
    }
 
    private void touchEveryLine() {
        for (int i = 0; i < testData.length; i = i + 16) {
            testData[i] = testData[i] + 1;
        }
    }
 
    private void run() {
        //warm up
        for (int i = 0; i < 10_000; i = i + 1) {
            touchEveryLine();
            touchEveryItem();
        }

        for (int i = 0; i < 100; i = i + 1) {
            long t0 = System.nanoTime();
            touchEveryLine();
            long t1 = System.nanoTime();
            touchEveryItem();
            long t2 = System.nanoTime();
            long el1 = t1 - t0;
            long el2 = t2 - t1;
            System.out.println("Line: "+ el1 +" ns ; Item: "+ el2);
        }
    }
 
    public static void main(String[] args) {
        Caching c = new Caching();
        c.run();
    }
}
```

`touchEveryItem()` does 16 times as much work as `touchEveryLine()`, but here are some sample results from a typical laptop:
 
```
Line: 487481 ns ; Item: 452421
Line: 425039 ns ; Item: 428397
Line: 415447 ns ; Item: 395332
Line: 372815 ns ; Item: 397519
Line: 366305 ns ; Item: 375376
Line: 332249 ns ; Item: 330512
```

The results of this code show that **`touchEveryItem()` doesn’t take 16 times as long to run as` touchEveryLine()`**. 

**It’s the memory transfer time—loading from main memory to CPU cache—that dominates the overall performance profile.** touchEveryLine() and touchEveryItem() have the same number of cache line reads, and the data transfer time vastly outweighs the cycles spent on actually modifying the data.

This demonstrates a key point: we need to develop at least a working understanding (or mental model) of how the CPU actually spends its time.