# OpenStarscape Format Layer
The format is the way the [core protocol](core.md) is represented on the wire. Currently a single JSON-based format is used. The code needs to be ported to the more compact JSON format described here.

## Opcodes
These are used when formats need to efficiently represent message types. Messages not associated with an object member start at 0, property opcodes are the 10s, signal opcodes are the 20s and action opcodes are the 30s. Events count up from 0 and requests count down from 9.

Non-member messages:
- `0`: object destroyed
- `1`: fatal error

Property messages:
- `10`: value
- `11`: update
- `16`: get
- `17`: set
- `18`: subscribe
- `19`: unsubscribe

Signal messages:
- `20`: event
- `28`: subscribe
- `29`: unsubscribe

Action Messages:
- `39`: fire

## JSON Format
__NOTE:__ the JSON format the server and web client currently implement is a different, less compact variant. They need to be migrated to the format described here. See currently used format [below](#json-format-legacy).

Each message is a UTF-8 encoded JSON array. Messages are separated by newline (character `10`), and do not contain newlines within. __NOTE:__ messages may contain strings with arbitrary UTF-8 text. Newlines are escaped in strings, but the byte value `10` may appear as part of another character. Therefore, any code that splits messages based on the newline delimiter must be unicode-aware.

The first element of each message is the opcode. The following elements depend on it.

For object destroyed:
```
[0, object_id]
```

For fatal error:
```
[1, "reason for error"]
```

For member messages that contain a value:
```
[opcode, object_id, "member_name", value]
```

And for member messages that do not:
```
[opcode, object_id, "member_name"]
```

### Values
Many of the primitive types can be represented cleanly in JSON, but some need a little variation for disambiguation.
- `null`: JSON `null`
- `bool`: JSON boolean values
- `integer`: JSON number
- `scalar`: JSON number
- `string`: JSON string
- `object`: array-wrapped number (`[42]`)
- `vector3`: array of three numbers (`[1, -2, 3.5]`)
- `array`: array-wrapped array of values (`[[1.5, [42], "foo"]]`)
- `map`: JSON object containing strings mapped to values

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
