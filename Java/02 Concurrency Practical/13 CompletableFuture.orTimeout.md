The orTimeout method uses a ScheduledThreadExecutor to complete the CompletableFuture with a TimeoutException after the specified timeout has elapsed, and it returns another CompletableFuture. 

By using this method, you can further chain your computation pipeline and deal with the TimeoutException by providing a friendly message back.

```java
Future<Double> futurePriceInUSD =
    CompletableFuture.supplyAsync(() -> shop.getPrice(product))
      .thenCombine(CompletableFuture.supplyAsync(() ->  exchangeService.getRate(Money.EUR, Money.USD)),
                   (price, rate) -> price * rate
    ))
    .orTimeout(3, TimeUnit.SECONDS);

```
