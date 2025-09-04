- A stream is a sequence of elements from a source that supports data-processing operations.
- Streams make use of internal iteration: the iteration is abstracted away through operations such as filter, map, and sorted.
- There are two types of stream operations: intermediate and terminal operations.
- Intermediate operations such as filter and map return a stream and can be chained together. They’re used to set up a pipeline of operations but don’t produce any result.
- Terminal operations such as forEach and count return a non-stream value and process a stream pipeline to return a result.
- The elements of a stream are computed on demand (“lazily”).

# (Non)Streams Code

The most important part of the streams paradigm is to structure your computation as a sequence of transformations where the result of each stage is as close as possible to a pure function of the result of the previous stage. A pure function is one whose result depends only on its input: it does not depend on any mutable state, nor does it update any state.
 
```java
// Uses the streams API but not the paradigm--Don't do this!
Map<String, Long> freq = new HashMap<>();
try (Stream<String> words = new Scanner(file).tokens()) {
    words.forEach(word -> {
        freq.merge(word.toLowerCase(), 1L, Long::sum);
    });
}
```

**This is not streams code at all; it’s iterative code masquerading as streams code.**
 
It derives no benefits from the streams API, and it’s (a bit) longer, harder to read, and less maintainable than the corresponding iterative code. 

The problem stems from the fact that **this code is doing all its work in a terminal forEach operation, using a lambda that mutates external state (the frequency table).**
 
**A `forEach` operation that does anything more than present the result of the computation performed by a stream is a “bad smell in code,” as is a lambda that mutates state.**
 
```java
// Proper use of streams to initialize a frequency table
Map<String, Long> freq;
try (Stream<String> words = new Scanner(file).tokens()) {
    freq = words.collect(groupingBy(String::toLowerCase, 
                                    counting()));
}
```

**The `forEach` operation should be used only to report the result of a stream computation**, not to perform the computation. Occasionally, it makes sense to use `forEach` for some other purpose, such as adding the results of a stream computation to a preexisting collection.
 
# Boxed vs Specialised Streams

Never use boxed types if possible – always prefer the specialised streams.
 
Whenever you find yourself performing operations to get a primitive (int/long/double), always prefer specialised streams IntStream, LongStream, DoubleStream, and use methods sum/min/max/average/summaryStatistics.
 
**To get specialised streams, use `mapToInt`/`mapToLong`/`mapToDouble` instead of map**.

