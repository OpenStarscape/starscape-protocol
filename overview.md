# Changeable Properties Protocol

<details>
<summary>Author and license</summary>

Written by William Wold and published by the OpenStarscape team [via GitHub](https://github.com/OpenStarscape/starscape-protocol).

Copyright (C) 2020 Alexander Logan Martin.
Permission is granted to copy, distribute, and/or modify this document under the terms of the GNU Free Documentation License, Version 1.3 or any later version published by the Free Software Foundation; with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts. A copy of the license is included in the collection containing this document.

THERE IS NO WARRANTY FOR THIS DOCUMENT, TO THE EXTENT PERMITTED BY APPLICABLE LAW. EXCEPT WHEN OTHERWISE STATED IN WRITING THE COPYRIGHT HOLDERS, PUBLISHERS, AND ALL OTHER PARTIES PROVIDE THIS DOCUMENT "AS IS" WITHOUT WARRANTY OF ANY KIND, EITHER EXPRESSED OR IMPLIED, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK AS TO THE QUALITY OF THIS DOCUMENT IS WITH ITS USER. SHOULD THIS DOCUMENT PROVE DEFECTIVE OR INCORRECT, ITS USER ASSUMES THE COST OF ALL NECESSARY CORRECTIVE ACTION OF ANY KIND.

If the disclaimer of warranty provided above cannot be given local legal effect according to its terms, reviewing courts shall apply local law that most closely approximates an absolute waiver of all civil liability in connection with this document, unless an express warranty or assumption of liability accompanies a copy of this document, and then such liability shall be assumed only by the party providing such warranty or assumption of liability.
</details>

## Introduction

[OpenStarscape](https://github.com/OpenStarscape) is an open source multiplayer space flight simulation game. It uses an open protocol for client/server communication and allows modified and 3rd party clients. This repository defines that protocol. This file gives an overview of how the protocol works and defines commonly used terms.

## Versioning

A server advertises a single protocol revision (a string) and a version (a number). Different protocol revisions are completely incompatible. Versions are backwards compatible, meaning that an old client should work with a later server version. More details in [core.md](core.md).

## Objects

The protocol is object oriented. The server keeps track of a collection of objects and allows clients to interact with them. Each client has it's own set of objects. The same server-side game entity may be exposed similarly to multiple clients, but those object IDs may be different.

Objects can have properties, methods and signals (collectively attributes). Every object has a `class` property that holds what type it is. These class names should be defined in [starscape.md](starscape.md), along with what attributes they support. All attributes for a class are in the same namespace (an object can't have a method and a property with the same name)

## Properties

A property is named value on an object. A client may get the value of a property, or subscribe to be notified of future changes. It may be able to set some properties, but others are defined as read-only.

## Methods

Methods are messages that a client can send to an object on the server. Methods are asynchronous and do not generate a response. Each method has a name and can have any number of values attached to it as arguments. A method must be given the correct number of arguments of the correct types.

## Signals

Signals are the inverse of methods, messages from a server object that a client can subscribe to. Like methods, they are async, do not expect a response and can have any number of arguments.
