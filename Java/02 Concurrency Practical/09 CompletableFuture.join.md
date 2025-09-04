By using .join(), you don’t have to bloat the lambda expression passed to this second map with a try/catch block like with using .get()

Finding prices from a list of shops:

```java
List<CompletableFuture<String>> priceFutures =
        shops.stream()
          .map(shop -> CompletableFuture.supplyAsync(
                       () -> String.format("%s price is %.2f", shop.getName(), shop.getPrice(product))))
          .collect(toList());
```

With this approach, you obtain a `List<CompletableFuture<String>>`, where each `CompletableFuture` in the List contains the String name of a shop when its computation is complete. 

But because we want to return a `List<String>`, you’ll have to wait for the completion of all these futures and extract the values they contain before returning the List.

To achieve this result, you can apply a second map operation to the original `List<CompletableFuture<String>>`, invoking a join on all the futures in the List and then waiting for their completion one by one. 

Note that the join method of the CompletableFuture class has the same meaning as the get method also declared in the Future interface, the only difference being that join doesn’t throw any checked exception. 

Putting everything together, you can rewrite the `findPrices` method as shown in the listing that follows.

```java
public List<String> findPrices(String product) {
    List<CompletableFuture<String>> priceFutures =
            shops.stream()
              .map(shop -> CompletableFuture.supplyAsync(
                           () -> shop.getName() + " price is " + shop.getPrice(product)))
              .collect(Collectors.toList());
    
    return priceFutures.stream()
              .map(CompletableFuture::join) 
              .collect(toList());
}
```

Note that you use two separate stream pipelines instead of putting the two map operations one after the other in the same stream-processing pipeline—and for a good reason. Given the lazy nature of intermediate stream operations, if you’d processed the stream in a single pipeline, you’d have succeeded only in executing all the requests to different shops synchronously and sequentially. The creation of each `CompletableFuture` to interrogate a given shop would start only when the computation of the previous one completed, letting the join method return the result of that computation.
