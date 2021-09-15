# OpenStarscape Core Protocol
OpenStarscape is built on an object-oriented data model. The server contains the objects. The client uses references to the objects to access and affect server state.

## Messages (Requests & Events)
_Messages_ are single units of information sent between the client and the server. _Requests_ are messages sent to the server, and _events_ are messages sent to the client.

## Primitive Data Types
The primitive data types used in the API are:
- `null`: `null` is the only value of type null.
- `bool`: `true` or `false`.
- `integer`: a 64-bit integer.
- `scalar`: a 64-bit floating point number. An integer is always allowed in place of a scalar.
- `string`: a sequence of 0 or more Unicode characters.
- `object`: a reference to a server-side object. Represented by a non-zero 64-bit integer.
- `vector3`: a vector with 3 64-bit float components (not to be confused with array).
- `array`: a sequence of 0 or more values of any type. Can contain values of different types.

Formats must unambiguously represent values of any type (an int and an object __must__ be distinguishable without context) with the exception is scalars and integers. whole-number scalars are allowed to be ambiguous with integers.

## Type Annotations
The OpenStarscape documentation annotates types with a format similar to TypeScript.
- Most primitive types are simply their name (`bool`, `integer`, etc).
- `array<T>` describes an array of type `T`.
- `[foo: T, bar: U]` describes an array that has a fixed number of named elements (these arrays are sometimes called tuples). The names (foo and bar) are not sent over the wire, just included for readability.
- `object<T>` describes a reference to an object of type `T` (`T` should be defined elsewhere in the documentation).
- The same value can sometimes be multiple types. For example `object<Foo | Bar> | null` could be `null`, a `Foo` object or a `Bar` object.

## Objects
An _object_ is a reference to a "thing" on the server which has associated data and/or behavior.

## Derivation
Object types may derive from zero or more other object types. This simply means the derived type has all the members of the type it derives from, in addition to it's own members.

## Root Object
The _root object_ is the client's API entry-point. It has ID `1`, and gives access (directly or indirectly) to everything the client needs.

## Members
Object _members_ can be _properties_, _signals_ or _events_. The members each object type has are defined in the [API](api.md). Member names can contain letters, numbers and `_`s. Member names do not start with a number. All members of an object have different names.

## Properties
_Properties_ represent a piece of server-side data. Properties that are settable clients are marked as RW (read-writable). Properties that are not settable by any clients are RO (read-only). Setting RW properties may still fail, if for example the given value is the wrong type, is outside the allowed range or the current client does not have permission to set it.

Requests:
- `set(value)`: sets the value. If successful, subscribers will be updated. May result in the value being changed to something other than the given value (for example if the allowed values are `0`-`100` and `105` is given, the property may be clamped to `100`). May fail, in which case nothing happens. Only RW properties are potentially settable.
- `get()`: results in the server sending a `value` event with the property's current value.
- `subscribe()`: also results in a `value` event, and subsequent `update` events when the property changes in the future. Does not result in a `value` if already subscribed.
- `unsubscribe()`: stops the stream of `update` events.

Events:
- `value(value)`: sent in response to a `set` or `subscribe`.
- `update(value)`: sent to subscribed clients in response to a change in the value.

## Signals
_Signals_ indicate to a client when something happens on the server. Signals are distinct from properties in that they have no persistent state (therefor no `get` request) and can be fired multiple times in a row with the same value. Signals may or may not have associated data (if they don't have any data than `value` is always `null`).

Requests:
- `subscribe()`: results in the server sending the client `event` events whenever the signal is fired.
- `unsubscribe()`: stops the stream of `event` events.

Events:
- `event(value)`: sent to subscribed clients when a signal is fired on the server.

## Actions
_Actions_ are like signals in the other direction. They allow clients to invoke one-shot actions on the server. Actions may or may not have associated data (if they don't have any data than `value` should always be `null`).

Requests:
- `fire(value)`: sent to the server to invoke the action.

## Errors
Events:
- `fatal(cause)`: a fatal error is the last event before the connection is shut down. `cause` is a human-readable string that describes what error occurred.

## TODO
- Change `subscribe`/`unsubscribe` to `listen`/`unlisten` for signals to disambiguate signals and properties in all requests
- Change the signal `event` event to `fire` to disambiguate it from the more general term "event"
- Change the action `fire` request to `invoke`
- Support non-fatal errors
- Support object destruction

---

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/).
