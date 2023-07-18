# OpenStarscape Format Layer
The format is the way the [core protocol](core.md) is represented on the wire. Currently a single JSON-based format is used. The code needs to be ported to the more compact JSON format described here.

## Opcodes
These are used when formats need to efficiently represent message types. Event (server to client) opcodes are 0-7 and request (client to server) opcodes are 8-15, meaning you can keep them in three bits if you really want to.

- `0`: fatal error
- `1`: object destroyed
- `2`: property value
- `3`: property value updated
- `4`: signal fired
- `8`: get property
- `9`: set property
- `10`: subscribe to property
- `11`: unsubscribe from property
- `12`: listen to signal
- `13`: unlisten from signal
- `14`: fire action

## JSON Format
Each message is a UTF-8 encoded JSON array. Messages are separated by newline (character `0x0A`), and do not contain newlines within (even in non-ascii characters `0x0A` will not appear in UTF-8). The first element of each message is the opcode. The following elements depend on it.

For fatal error:
```
[0,"reason for error"]
```

For object destroyed:
```
[1,object_id]
```

For member messages that contain a value:
```
[opcode,object_id,"member_name",value]
```

And for member messages that do not:
```
[opcode,object_id,"member_name"]
```

### Values
Many of the primitive types can be represented cleanly in JSON, but some need a little variation for disambiguation.
- `null`: JSON `null`
- `bool`: JSON boolean values
- `integer`: JSON number
- `scalar`: JSON number
- `string`: JSON string
- `object`: array-wrapped number (`[42]`)
- `vector3`: array of three numbers (`[1,-2,3.5]`)
- `array`: array-wrapped array of values (`[[1.5,[42],"foo"]]`)
- `map`: JSON object containing strings mapped to values (`{"object":[42]}`)

## JSON Format (legacy)
This is the format currently used by the client and the server

Each "packet" contains a JSON list of messages. Each message is a JSON object. They have the following properties:
- `mtype`: a string, the message type name
- `object`: a number, the object ID this message relates to. Does not exist if the message does not relate to an object.
- `property`: a string, the name of the member this message relates to (confusingly, the name "property" is used for signals and actions as well). Does not exist if the message does not relate to a member.
- `value`: a [Starscape value](#values). The same value format is used as the new JSON format above. Does not exist if the message does not have an associated value.
- `text`: a string, present only for fatal errors.

---

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/).
