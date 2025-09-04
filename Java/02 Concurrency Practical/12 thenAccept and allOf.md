Now you want to have the best-price-finder application **display the price for a given shop as soon as it becomes available without waiting for the slowest one** (which may even time out). 

We no longer want to use `.map(CompletableFuture::join)`, but instead:

```java
.map(f -> f.thenAccept(System.out::println));
```

Although we display them as they come, we can still wait for all futures to complete before carrying on with any other code, using a join on allOf:

```java
CompletableFuture[] futures = findPricesStream("myPhone")
        .map(f -> f.thenAccept(System.out::println))
        .toArray(size -> new CompletableFuture[size]);

CompletableFuture.allOf(futures).join();
```
