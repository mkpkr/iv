This focusses on thenCompose, for thenCombine see CompletableFuture.thenCombine

• thenCompose: A CompletableFuture is dependent on another - the value resulting from the execution of the first is used as input to the second.
• thenCombine: Combine the results of two independent CompletableFutures, and you don’t want to wait for the first to complete before starting the second.

------------

`thenCompose` allows you to pipeline two asynchronous operations, passing the result of the first operation to the second operation when it becomes available. 

• Suppose you are getting prices for a product from multiple shops.
• All the shops have agreed to use a centralized discount service. 
• This service uses five discount codes, each of which has a different discount percentage. 
• You represent this idea by defining a Discount.Code enumeration:

```java
public class Discount {
    public enum Code {
        NONE(0), SILVER(5), GOLD(10), PLATINUM(15), DIAMOND(20);
        private final int percentage;
        Code(int percentage) {
            this.percentage = percentage;
        }
    }
    // Discount class implementation shown later
}
```

	• The shops have agreed on a format of the result of the getPrice method, which now returns a String in the format ShopName:price:DiscountCode. 
		○ e.g BestPrice:123.26:GOLD
	• Your sample implementation returns a random Discount.Code together with the random price already calculated, as follows:

```java
public String getPrice(String product) {
    delay(1000); //simulated remote service delay

    double price = random.nextDouble() * product.charAt(0) + product.charAt(1);
    Discount.Code code = Discount.Code.values()[random.nextInt(Discount.Code.values().length)];
    
    return String.format("%s:%.2f:%s", name, price, code);
}
```

	• The Discount service is implemented as follows

```java
public class Discount {
    public enum Code {
        // shown earlier
    }

    public static String applyDiscount(String quoteString) {
        Quote quote = parseQuote(quoteString); //method not shown

        delay(1000); //simulated remote service delay
        return quote.getShopName() + " price is " + format(quote.getPrice() * (100 - quote.getDiscountCode().percentage) / 100));
    }
}
```

---
# Implementing findPrices

## Sequential

```java
public List<String> findPrices(String product) {
    return shops.stream()
            .map(shop -> shop.getPrice(product))
            .map(Discount::applyDiscount)
            .collect(toList());
}
```

As expected, this code takes ~ 10 seconds to run, because the 5 seconds required to query the five shops sequentially is added to the 5 seconds consumed by the discount service in applying the discount code to the prices returned by the five shops. 

You already know that you can improve this result by converting the stream to a parallel one. But you also know (from Common Pool vs Executor ) that this solution doesn’t scale well when you increase the number of shops to be queried, due to the fixed common thread pool on which streams rely. Conversely, you learned that you can better use your CPU by defining a custom Executor that schedules the tasks performed by the CompletableFutures.


## Composing CompletableFutures

```java
public List<String> findPrices(String product) {
    List<CompletableFuture<String>> priceFutures =
        shops.stream()
             .map(shop -> CompletableFuture.supplyAsync(() -> shop.getPrice(product), executor))
             
             .map(future -> future.thenCompose(quote -> CompletableFuture.supplyAsync(() -> Discount.applyDiscount(quote), executor)))
             
             .collect(toList());

    return priceFutures.stream()
            .map(CompletableFuture::join)
            .collect(toList());
}
```
You’re performing the same map operations that you did in the synchronous solution, but you make those operations asynchronous, using the feature provided by the CompletableFuture class.

This takes ~ 2 seconds.
