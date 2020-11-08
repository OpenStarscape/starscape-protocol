# OpenStarscape core

<details>
<summary>Author and license</summary>

Written by Alexander Logan Martin and published by the OpenStarscape team [via GitHub](https://github.com/OpenStarscape/protocol).

Copyright (C) 2020 Alexander Logan Martin.
Permission is granted to copy, distribute, and/or modify this document under the terms of the GNU Free Documentation License, Version 1.3 or any later version published by the Free Software Foundation; with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts. A copy of the license is included in the collection containing this document.

THERE IS NO WARRANTY FOR THIS DOCUMENT, TO THE EXTENT PERMITTED BY APPLICABLE LAW. EXCEPT WHEN OTHERWISE STATED IN WRITING THE COPYRIGHT HOLDERS, PUBLISHERS, AND ALL OTHER PARTIES PROVIDE THIS DOCUMENT "AS IS" WITHOUT WARRANTY OF ANY KIND, EITHER EXPRESSED OR IMPLIED, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK AS TO THE QUALITY OF THIS DOCUMENT IS WITH ITS USER. SHOULD THIS DOCUMENT PROVE DEFECTIVE OR INCORRECT, ITS USER ASSUMES THE COST OF ALL NECESSARY CORRECTIVE ACTION OF ANY KIND.

If the disclaimer of warranty provided above cannot be given local legal effect according to its terms, reviewing courts shall apply local law that most closely approximates an absolute waiver of all civil liability in connection with this document, unless an express warranty or assumption of liability accompanies a copy of this document, and then such liability shall be assumed only by the party providing such warranty or assumption of liability.
</details>

## Introduction

**This document is an early draft.**

The OpenStarscape core is a specialization of the Changeable Properties Protocol that provides object types and commands to represent the monitoring and operation of a spacecraft in a 3D environment.

The entire Changeable Properties Protocol specification and all associated session-layer specifications are incorporated by reference.

## General behavior

The specialization name is `"OpenStarscape v1"`

Until the client sends a `connect` message, no objects except the `universe` are available.

## Client messages

The following are legal messages that the client may sent to the server, in addition to those specified by the base Changeable Properties Protocol.

### `connect`

Specify a vessel to connect to.

* `vessel`: String; identifier of the vessel.
* `credential`: Any; credentials proving authorization to connect to the vessel.

### `waypoint`

Create a new `point` object.

The status message must include, if successful, a field `object` with the identifier of the new `point`, whose `pos` property must be settable.

## Object types

### `point`

A point in space. Version 1.

* `pos`: Object containing numbers; the `x`, `y`, and `z` coordinates of the point's position in megameters. May or may not be settable.

### `orientedpoint`

A point in space with an orientation. Version 1.

* All properties of `point`.
* `quatorient`: Object containing numbers; the `n`, `i`, `j`, and `k` components of a rotation quaternion describing the point's orientation relative to "up" being towards the positive Z-axis while facing the positive X-axis. May or may not be settable.
* `axangorient`: Object containing numbers; the `eliv` and `azim` angles of a unit vector and the `roll` angle of the angle-axis representation, all measured in radians, describing the point's orientation relative to "up" being towards the positive Z-axis while facing the positive X-axis. May or may not be settable.

### `body`

A physical object in space. Version 1.

* All properties of `point`.
* `vel`: Object containing numbers; the `x`, `y`, and `z` components of the body's velocity vector in megameters per second. Not settable.

### `rotatingbody`

* All properties of `orientedpoint`.
* All properties of `body`.
* `quatrotate`: Object containing numbers; the `n`, `i`, `j`, and `k` components of a rotation quaternion describing the body's orientation change per second relative. Not settable.
* `axangrotate`: Object containing numbers; the `eliv` and `azim` angles of a unit vector and the `roll` angle of the angle-axis representation, all measured in radians per second, describing the body's orientation change relative to "up" being towards the positive Z-axis while facing the positive X-axis. Not settable.

### `poweredbody`

An object in space under power. Version 1.

* All properties of `rotatingbody`.
* `accel`: Object containing numbers; the `x`, `y`, and `z` components of the body's acceleration vector in megameters per second per second. May or may not be settable.
* `quatdeltarotate`: Object containing numbers; the `n`, `i`, `j`, and `k` components of a rotation quaternion describing the body's delta-orientation change per second per second. Not settable.
* `axangdeltarotate`: Object containing numbers; the `eliv` and `azim` angles of a unit vector and the `roll` angle of the angle-axis representation, all measured in radians per second per second, describing the body's delta-orientation change. Not settable.

## History

The following is the history of this document as recommended by the GNU Free Documentation License:

* Initially drafted as "OpenStarscape core" by Alexander Logan Martin on March 21 2020. Published [via GitHub](https://github.com/OpenStarscape/protocol) by the OpenStarscape team.
