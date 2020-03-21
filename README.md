# Protocol

The protocol suite OpenStarscape uses. Most of these documents are written in Markdown and licensed under the [GNU Free Documentation License](LICENSE.md).

## Changeable Properties Protocol

**This component of the protocol suite has not been finalized.**

The Changeable Properties Protocol is an application-layer protocol that provides the foundation for OpenStarscape. It allows a client to keep track of and manipulate the properties of a collection of objects, while also allowing the client to send miscellaneous other commands. At its core, OpenStarscape is a specialization of this protocol.

The [core Changeable Properties Protocol](chprops.md) is supplemented by "session layer" specifications that bind it to underlying transport layers, including [TCP, TLS, or Unix-domain stream sockets](session/stream.md) and [WebSocket, SCTP, or Unix-domain sequenced packet sockets](session/seqpacket.md). 

## OpenStarscape core

**This component of the protocol suite has not been finalized.**

The [OpenStarscape core](oscore.md) is a specialization of the Changeable Properties Protocol that provides a number of objects and commands to represent sensor data and vessel control functionality for a spacecraft in a 3D environment.
