Within the JDK, this replaces (but doesn’t remove) HttpURLConnection while aiming to put a usable HTTP API “in the box,” as it were, because many developers have reached for external libraries to fulfill their HTTP-related needs.

The resulting HTTP/2- and web socket–compatible API came first to Java 9 as an Incubating feature. JEP 321 moved it to its permanent home in Java 11 under java.net.http.

The new API supports HTTP 1.1 as well as HTTP/2 and can fall back to HTTP 1.1 when a server being called doesn’t support HTTP/2.

Interactions with the new API start from the HttpRequest and HttpClient types. These are instantiated via builders, setting configurations before issuing the actual HTTP call.

# Sync

```java
var client = HttpClient.newBuilder().build();

var uri = new URI("https://google.com");
var request = HttpRequest.newBuilder(uri).build();
 
var response = client.send(
    request,
    HttpResponse.BodyHandlers.ofString(
        Charset.defaultCharset()));
 
System.out.println(response.body());
```

The first parameter to send is the request we set up, but the second deserves a closer look.

Rather than expecting to always return a single type, the send method expects us to provide an implementation of the `HttpResponse.BodyHandler<T>` interface to tell it how to handle the response.` HttpResponse.BodyHandlers` provides some useful basic handlers for receiving your response as a byte array, as a string, or as a file.

But customizing this behavior is just an implementation of `BodyHandler` away. All of this plumbing is based on the `java.util.concurrent.Flow` publisher and subscriber mechanisms, a form of programming known as reactive streams.

# Async

One of the most significant benefits of HTTP/2 is its built-in multiplexing. Only using a synchronous send doesn’t really gain those benefits, so it should come as no surprise that HttpClient also supports a sendAsync method.

sendAsync returns a CompletableFuture wrapped around the HttpResponse, providing a rich set of capabilities that may be familiar from other parts of the platform.

```java
var client = HttpClient.newBuilder().build();
 
var uri = new URI("https://google.com");
var request = HttpRequest.newBuilder(uri).build();
 
var handler = HttpResponse.BodyHandlers.ofString();

CompletableFuture.allOf(
    client.sendAsync(request, handler)
        .thenAccept((resp) ->
                      System.out.println(resp.body()),
    client.sendAsync(request, handler)
        .thenAccept((resp) ->
                      System.out.println(resp.body()),
    client.sendAsync(request, handler) 
        .thenAccept((resp) ->
                      System.out.println(resp.body())
).join();

```

Here we set up a request and client again, but then we asynchronously repeat the call three separate times. CompletableFuture.allOf combines these three futures, so we can wait on them all to finish with a single join.

Building off the futures and reactive streams, the new HTTP API in the JDK provides an attractive alternative to the large libraries that have dominated the ecosystem in the HTTP space. Designed with modern asynchronous programming at the forefront, java.net.http puts Java in an excellent place for wherever the web evolves to in the future.