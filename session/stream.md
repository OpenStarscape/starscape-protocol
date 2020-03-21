# Session layer for Changeable Properties Protocol over stream transports

Written by Alexander Logan Martin and published by the OpenStarscape team [via GitHub](https://github.com/OpenStarscape/Protocol).

Copyright (C) 2020 Alexander Logan Martin.
Permission is granted to copy, distribute, and/or modify this document under the terms of the GNU Free Documentation License, Version 1.3 or any later version published by the Free Software Foundation; with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts. A copy of the license is included in the collection containing this document.

THERE IS NO WARRANTY FOR THIS DOCUMENT, TO THE EXTENT PERMITTED BY APPLICABLE LAW. EXCEPT WHEN OTHERWISE STATED IN WRITING THE COPYRIGHT HOLDERS, PUBLISHERS, AND ALL OTHER PARTIES PROVIDE THIS DOCUMENT "AS IS" WITHOUT WARRANTY OF ANY KIND, EITHER EXPRESSED OR IMPLIED, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK AS TO THE QUALITY OF THIS DOCUMENT IS WITH ITS USER. SHOULD THIS DOCUMENT PROVE DEFECTIVE OR INCORRECT, ITS USER ASSUMES THE COST OF ALL NECESSARY CORRECTIVE ACTION OF ANY KIND.

If the disclaimer of warranty provided above cannot be given local legal effect according to its terms, reviewing courts shall apply local law that most closely approximates an absolute waiver of all civil liability in connection with this document, unless an express warranty or assumption of liability accompanies a copy of this document, and then such liability shall be assumed only by the party providing such warranty or assumption of liability.

## Background

The Transmission Control Protocol (TCP), part of the Internet protocol suite, is a very popular reliable bidirectional stream protocol, used very heavily on the Internet.

Transport Layer Security (TLS), formerly known as Secure Sockets Layer (SSL), is a security protocol often used in combination with TCP.

Unix-domain `SOCK_STREAM` sockets are an inter-process communication mechanism in Unix-like operating systems.

## Specification

This specification provides a session layer that sits between any of the above-described stream transports and the Changeable Properties Protocol.

All messages must be converted to datagrams by serializing them with no newlines. Every datagram is terminated by exactly one newline and sent to the stream. This entire process is reversed on the receiving end.

## History

The following is the history of this document as recommended by the GNU Free Documentation License:

* Initially written as "Session layer for changing properties protocol over stream protocols" by Alexander Logan Martin on March 20 2020. Not published.
* Generally revised and placed under the GNU Free Documentation License as "Session layer for Changeable Properties Protocol over stream transports" by Alexander Logan Martin on March 21 2020. Published [via GitHub](https://github.com/OpenStarscape/Protocol) by the OpenStarscape team.
