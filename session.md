# OpenStarscape Session Layer
The session is the low level network protocol used to communicate between client and server. The client may choose which session type to establish. The server can connect to multiple clients using different session types at once.

Sessions must guarantee data is reliable and ordered. There is currently no mechanism for ensuring this other than using a low level network protocol that has these guarantees.

## TCP
TCP is recommended for all applications that do not run in a browser. To connect, simply create a TCP connection to the server's IP.

## WebSockets
WebSockets are used by the web fronted. It's required because opening TCP connections is not possible inside a browser.

To connect, create a websocket with the URL the server is running on with two modifications:
- replace `https://` with `wss://`, or `http://` with `ws://`
- Add `/websocket` to the end

An example of creating a WebSocket session can be found [here](https://github.com/OpenStarscape/starscape-web/blob/170a2f65b065c6efa4b37d0829ae6395ece337a8/src/protocol/SsWebSocketSession.ts)

## WebRTC (not recommended)
The server has an unreliable WebRTC implementation. Since the rest of the stack does not currently support unreliable transmission, it will likely not yield a good experience. In theory unreliable transmission could prevent head-of-line blocking related lag by not resending data that is already out-of-date. In practice WebRTC was generally observed to perform worse than WebSockets with no packet loss and head-of-line blocking hasn't proven to be a problem yet.

---

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/).
