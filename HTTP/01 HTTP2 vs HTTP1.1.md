HTTP/2 is a transport-level update to the protocol focused on fixing these sorts of fundamental performance issues that don’t fit how the web really works today. With its performance focus on how bytes flow between client and server, HTTP/2 actually doesn’t alter many of the familiar HTTP concepts—request/response, headers, status codes, response bodies—all of these remain semantically the same in HTTP/2 vs. HTTP 1.1.

# Head-of-line Blocking

Communication in HTTP takes place over TCP sockets. Although HTTP 1.1 defaulted to reusing individual sockets to avoid repeating unnecessary setup costs, the protocol dictated that requests be returned in order, even when multiple requests shared a socket (known as pipelining).

![[Pasted image 20250903162337.png]]
This means that a slow response from the server blocked subsequent requests, which theoretically could have been returned sooner.

These effects are readily visible in places like browser rendering stalling on downloading assets. The same one-response-per-connection-at-a-time behavior can also limit JVM applications talking to HTTP-based services.

HTTP/2 is designed from the ground up to multiplex requests over the same connection

![[Pasted image 20250903162356.png]]Multiple streams between the client and server are always supported. It even allows for separately receiving the headers and the body of a single request.

This fundamentally changes assumptions that decades of HTTP 1.1 have made second nature to many developers. For instance, it’s long been accepted that returning lots of small assets on a website performed worse than making larger bundles. JavaScript, CSS, and images all have common techniques and tooling for smashing many smaller files together to return more efficiently. In HTTP/2, multiplexed responses mean your resources don’t get blocked behind other slow requests, and smaller responses may be more accurately cached, yielding a better experience overall.

# Restricted Connections

The HTTP 1.1 specification recommends limiting to two connections to a server at a time. This is listed as a should rather than a must, and modern web browsers often allow between six and eight connections per domain. This limit to concurrent downloads from a site has often led developers to serve sites from multiple domains or implement the sort of bundling mentioned before.

HTTP/2 addresses this situation: each connection can effectively be used to make as many simultaneous requests as desired. Browsers open only one connection to a given domain but can perform many requests over that same connection at the same time.

In our JVM applications, where we might have pooled HTTP 1.1 connections to allow for more concurrent activity, HTTP/2 gives us another built-in way to squeeze out more requests.

# HTTP Header Performance

A significant feature of HTTP is the ability to send headers alongside requests. Headers are a critical part of how the HTTP protocol itself is stateless, but our applications can maintain state between requests (such as the fact your user is logged in).

Although the body of HTTP 1.1 payloads may be compressed if the client and server can agree on the algorithm (typically gzip), headers don’t participate. As richer web applications make more and more requests, the repetition of increasingly large headers can be a problem, especially for larger websites.

HTTP/2 addresses this problem with a new binary format for headers. As a user of the protocol, you don’t have to think much about this—it’s simply built in to how headers are transmitted between client and server.

# TLS Everything

In 1997, HTTP 1.1 entered a very different internet than we see today. Commerce on the internet was only starting to take off, and security wasn’t always a top concern in early protocol designs. Computing systems were also slow enough to make practices like encryption often far too expensive.

HTTP/2 was officially accepted in 2015 into a world that was far more security conscious. In addition, the computing needs for ubiquitous encryption of web requests through TLS (known in earlier versions as SSL) are low enough to have removed most arguments over whether or not to encrypt.

As such, in practice, HTTP/2 is supported only with TLS encryption (the protocol does, in theory, allow for transmission in cleartext, but none of the major implementations provide it).

This has an operational impact on deploying HTTP/2, because it requires a certificate with a lifecycle of expiration and renewal. For enterprises, this increases the need for certificate management. Let’s Encrypt ([https://www.letsencrypt.org](https://www.letsencrypt.org)), and other private options have been growing in response to this need.

# Other Considerations

Although the future is trending toward the uptake of HTTP/2, deployment of it across the web hasn’t been fast. In addition to the encryption requirement, which even impacts local development, this delay may be attributable to the following rough edges and extra complexity:

- HTTP/2 is binary-only; working with an opaque format is challenging.
- HTTP layer products such as load balancers, firewalls, and debugging tools require updates to support HTTP/2.
- Performance benefits are aimed mainly at the browser-based use of HTTP. Backend services working over HTTP may see less benefit to updating.