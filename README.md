# websockets_from_scratch

Just a little fun project to learn what's going on under the hood with websockets.

### Run the project

Clone the repo and run `ruby main.rb`. Navigate to localhost:4567 and in your browser console type the following to initiate a connection and send / view a message.

```
var socket = new WebSocket("ws://localhost:4567");
socket.onmessage = function(event){console.log(event.data);};
socket.send("hello world!");
```

### Websockets and Specification

Similar to HTTP - websockets is a layer on top of TCP. While an HTTP response closes the connection, a websocket connection stays open. This makes websockets great for realtime functionality in chat apps, streaming, etc.

The websocket specification can be found here - https://tools.ietf.org/html/rfc6455

### Implementation details

1.  HTTP GET handshake from client to server. Request has `Connection: upgrade`, `Upgrade: websocket`, `Sec-WebSocket-Version`, and `Sec-WebSocket-Key` headers that indicates that the client is requesting a websocket connection as well as what type of response is needed.

2.  After handshake is established, client and server are free to exchange messages which are wrapped in 'frames'. The format of a successful websocket server response would be:

```
"HTTP/1.1 101 Switching Protocols\r\n" +
"Upgrade: websocket\r\n" +
"Connection: Upgrade\r\n" +
"Sec-WebSocket-Accept: #{ws_accept}\r\n"
```

3.  Websocket frame is a series of bytes:

```
byte 1 - Message completeness and content type
byte 2 - mask and length indicator (1, 2, or 8 bytes to represent length)
byte 3 to 6 - mask key
byte 7 to end - content
```

4.  Client MUST encode messages with a mask. The masking key needs to be unpredicable and derived from a strong source of entropy. This prevents malicious applications from changing bytes on the wire. The server cannot mask the message.

### TODO

```
- Handle thousands of concurrent connections
- ping/pong heartbeats
- handle messages that aren't UTF-8 text data
- security
- proxying
- handling different ws protocol versions (instead of just version 13)
- message fragmentation (instead of complete messages)
```

### Resources

https://blog.pusher.com/websockets-from-scratch/
https://tools.ietf.org/html/rfc6455
