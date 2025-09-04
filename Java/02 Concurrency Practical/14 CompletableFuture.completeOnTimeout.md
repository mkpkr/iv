Sometimes, it’s also acceptable to use a default value in case a service is momentarily unable to respond in a timely manner. You might decide that in the example in [CompletableFuture.orTimeout](onenote:#CompletableFuture.orTimeout&section-id={6313e19f-70e1-4eec-b1c4-645f3673344b}&page-id={e4fe107e-0be1-4bc8-85ec-4a371268116e}&end) you want to wait for the exchange service to provide the current EUR-to-USD exchange rate for no more than 1 second, but if the request takes longer to complete, you don’t want to abort the whole the computation with an Exception.

Instead, you can **fall back by using a predefined exchange rate**. You can easily add this second kind of timeout by using the `completeOnTimeout` method, also introduced in Java 9.

```java
Future<Double> futurePriceInUSD =

    CompletableFuture.supplyAsync(() -> shop.getPrice(product))

      .thenCombine(CompletableFuture.supplyAsync(() ->  exchangeService.getRate(Money.EUR, Money.USD))

                                    .completeOnTimeout(DEFAULT_RATE, 1, TimeUnit.SECONDS),

                   (price, rate) -> price * rate)

      .orTimeout(3, TimeUnit.SECONDS);
```