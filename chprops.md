# Changeable Properties Protocol

Written by Athena Martin and published by the OpenStarscape team [via GitHub](https://github.com/OpenStarscape/Protocol).

Copyright (C) 2020 Athena Martin.
Permission is granted to copy, distribute, and/or modify this document under the terms of the GNU Free Documentation License, Version 1.3 or any later version published by the Free Software Foundation; with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts. A copy of the license is included in the collection containing this document.

THERE IS NO WARRANTY FOR THIS DOCUMENT, TO THE EXTENT PERMITTED BY APPLICABLE LAW. EXCEPT WHEN OTHERWISE STATED IN WRITING THE COPYRIGHT HOLDERS, PUBLISHERS, AND ALL OTHER PARTIES PROVIDE THIS DOCUMENT "AS IS" WITHOUT WARRANTY OF ANY KIND, EITHER EXPRESSED OR IMPLIED, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK AS TO THE QUALITY OF THIS DOCUMENT IS WITH ITS USER. SHOULD THIS DOCUMENT PROVE DEFECTIVE OR INCORRECT, ITS USER ASSUMES THE COST OF ALL NECESSARY CORRECTIVE ACTION OF ANY KIND.

If the disclaimer of warranty provided above cannot be given local legal effect according to its terms, reviewing courts shall apply local law that most closely approximates an absolute waiver of all civil liability in connection with this document, unless an express warranty or assumption of liability accompanies a copy of this document, and then such liability shall be assumed only by the party providing such warranty or assumption of liability.

## Introduction

The Changeable Properties Protocol is an application-layer protocol to handle systems that consist of numerous objects that have properties that change in real time. Different systems may be handled with the protocol, using different "specializations."

## General behavior

The protocol uses a session layer that provides reliable, in-order, two-way, connection-oriented delivery of JSON messages.

All messages are JSON objects which must contain a string property named `mtype`, representing the type of the message.

Either end may close the connection at any time, especially in response to a protocol violation.

## Client messages

The following are legal messages that the client may send to the server. The specialization may extend by adding further messages.

### `set`

Set a property's value.

* `id`: Number; unique identifier for the message, for use in the server's `status` response. Value may be reused once `status` is received.
* `object`: Number; identifier of the object with the property.
* `property`: String; the property to set.
* `value`: Any; the value to set it to.

The server may refuse to allow setting the property.

### `get`

Get the current value of a property, but only once.

* `id`: Number; unique identifier for the message, for use in the server's `status` response. Value may be reused once `status` is received.
* `object`: Number; identifier of the object with the property.
* `property`: String; the property to get.

The status message must include, if successful, a field `value` with the property's value.

### `subscribe`

Subscribe to receive updates on the value of certain properties until unsubscribed.

* `id`: Number; unique identifier for the message, for use in the server's `status` response. Value may be reused once `status` is received.
* `object`: Number; identifier of the object with the property.
* `properties`: String list; the properties to subscribe to.

The server may refuse to allow subscribing the property.

### `unsubscribe`

Delete subscriptions.

* `object`: Number; identifier of the object with the property.
* `properties`: String list; the properties to unsubscribe from.

## Server messages

The following are legal messages that the server may send to the client. The specialization may extend by adding further messages.

### `identify`

Identify the server and specialization details.

* `specialization`: String; the name of the specialization. Incompatible versions should be given different names.
* `server`: String; the name of the server software.

Additional properties may be specified by the specialization.

### `status`

Report status and results of a previous message with an `id` field sent by the client.

* `id`: Number; `id` value from the original message.
* `status`: String; indication of status. `"success"` if successful, otherwise an error reason, lowercase, with spaces and no punctuation.

May include additional fields as given by the specification for the client message. May be sent with an indefinite delay after the client's message. Standard error statuses are `"malformed"`, `"no such object"`, `"no such property"`, and `"not allowed"`.

### `update`

Give an update on a subscribed property.

* `object`: Number; identifier of the object with the property.
* `property`: String; the property the update is about.
* `value`: Any; the current value of the property.

## Guaranteed properties

All objects have these properties:

* `type`: String; the kind of object. Not settable. Not subscribable.
* `version`: String; the version number of the object's schema. Not settable. Not subscribable.
* `properties`: String list; the properties of the object. Not settable.

The specialization must define a minimum set of properties for each version of each type, and must not use different names for conceptually identical properties or the same names for conceptually different properties.

## Universe

The "universe" is an object that contains global properties about the whole system. Its identifier is always 0, and its type is always `universe`. Its version is 1 if as specified here, or greater than 10 if modified by the specialization.

* `objects`: Number list; the objects that are available. Not settable.
* `time`: Number; a monotonously-increasing timestamp. Not settable.

## History

The following is the history of this document as recommended by the GNU Free Documentation License:

* Initially written as "Changing properties protocol" by Athena Martin on March 20 2020. Not published.
* Generally revised and placed under the GNU Free Documentation License as "Changeable Properties Protocol" by Athena Martin on March 21 2020. Published [via GitHub](https://github.com/OpenStarscape/Protocol) by the OpenStarscape team.
* Revised to improve error reporting as "Changeable Properties Protocol" by Athena Martin on March 21 2020. Published [via GitHub](https://github.com/OpenStarscape/Protocol) by the OpenStarscape team.
