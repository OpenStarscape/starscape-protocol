# OpenStarscape bundling protocol

OpenStarscape network data is packed into bundles for efficiency in transit over the network.

## Bundles

A bundle can be either reliable or unreliable. Reliable bundles have sequence numbers, are acknowledged when received, and are resent if an acknowledgement is not received.

All bundles have a timestamp in game ticks.

A bundle encoded in JSON looks like this:

```jsonc
{
	"seq": 82, // Sequence number goes up by one for each reliable bundle. Null for unreliable bundles.
	"time": 1.528, // Time is in seconds. Omitted for client bundles.
	"messages": [
		{
			"mtype": "ack", // An acknowledgement.
			"seq": 54 // Other end's sequence number to acknowledge.
		}
	]
}
```

Each end keeps track of the reliable bundles it has sent, but that have not been acknowledged. Each has a deadline for acknowledgement. When an acknowledgement arrives, that bundle can be forgotten about. If the deadline arrives, the bundle is resent.

The server flushes its messages to force a bundle to be sent and updates the current time at the beginning of each game tick.
