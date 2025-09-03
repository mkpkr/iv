[https://javascript.info/server-sent-events](https://javascript.info/server-sent-events)

EventSource is a less-powerful way of communicating with the server than WebSocket.

Why should one ever use it?

The main reason: it’s simpler. In many applications, the power of WebSocket is a little bit too much.

We need to receive a stream of data from server: maybe chat messages or market prices, or whatever. That’s what EventSource is good at. Also it supports auto-reconnect, something we need to implement manually with WebSocket. Besides, it’s a plain old HTTP, not a new protocol.