# OpenStarscape Protocol
[OpenStarscape](https://github.com/OpenStarscape) is an open source multiplayer space flight simulation game. It uses an open protocol for client/server communication and allows modified and 3rd party clients. This repository contains the protocol spec.

## Project Architecture
OpenStarscape uses a client-server architecture.

### Server
Each game has exactly one server. It keeps track of all objects/properties, performs physics calculations, and communicates with clients. The [official OpenStarscape server](https://github.com/OpenStarscape/starscape-server) is MIT licensed and written in Rust. We don't anticipate the server will ever need to be reimplemented, although there's no reason that couldn't be done.

### Clients
Clients may perform a wide variety of tasks. The most obvious is to render things and allow the user to control a ship. This is what the MIT licensed [official web client](https://github.com/OpenStarscape/starscape-web) does. More clients like this may be built (by the core OpenStarscape team or by 3rd parties) to support different features or platforms. These may be forks of the official web client, or complete reimplementations. Clients without a user interface might be built to control AI ships, or for other purposes.

### Protocol
This protocol ([available here](https://github.com/OpenStarscape/starscape-protocol)) specifies how clients and servers talk to each other. It is licensed under CC BY-SA, which makes it the only component of the project that is not permissively licensed. If you make a derivative of the protocol, your implementation may be under whatever license you like but the protocol spec itself must remain open and licensed under CC BY-SA.

## Layers
The protocol is split into several layers:
- __[OpenStarscape API](api.md)__: the interfaces that make up a Starscape game, such as bodies, positions, orbits, etc.
- __[Core Protocol](core.md)__: the object oriented architecture the API is built on. Includes data types, objects, properties, signals and actions.
- __[Format Layer](format.md)__: the binary representation of messages. There may be multiple available formats, and clients can choose which one to use.
- __[Session Layer](session.md)__: the low level network connection such as TCP or WebSockets. Clients may choose the type of session to use.

---

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/).
