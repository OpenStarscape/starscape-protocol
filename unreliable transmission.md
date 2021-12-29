# Unreliable Data Transmission

In the future, there may be benefits of using protocols that support unreliable transmission (UDP and WebRTC). We may also wish to treat all network connections as unreliable in order to support dropped connection recovery. This is a proposal for how to support unreliable connections.

All communication happens in packets. Packets contain one or more bundles, and bundles contain zero or more messages. On underlying network protocols that do not support packets (such as TCP), our session or format layer will need to split them up.

Each packet contains:
- serial
- list of bundles.

Each bundle contains:
- in-game timestamp
- list of zero or more acked packets
- list of zero or more messages

A packet may not be partially delivered, and should not be processed until received in full. Packets may be dropped, or arrive out of order. If a packet is dropped the *packet* is not re-sent (but some or all of the contained bundles may be re-sent in new packets with new packet serials). Packet serials increase monotonically and are never reused.

Bundles may or may not be re-sent if their containing packet is dropped. Bundles may also be broken up into multiple bundles, multiple packets, or only partially resent. The only requirement is that packet acks and some messages must eventually arrive and be acked, and those must be in bundles with the timestamp of when they were initally sent.

When a packet is received, it's serial must be acked in some bundle. That bundle, like all bundles, will contain a timestamp of the current in-game time (which increases monotonically). Bundles acking the packet and with the original timestamp must keep being sent periodically until one of their containing packets is acked.

There may be multiple bundles with the same timestamp, and there is no correct order in which to process them. Implementations should try their best to process messages in bundles with different timestamps as if they arrived in that order (for example if a property gets set to `5` at `T+30s`, then a new packet is received that sets it to `3` at `T+20s`, the implementation should ignore the `3` because it would have already been overwritten had the messages been processed in order). This is especially important for subscribe/unsubscribe logic.

The only messages that are allowed to be dropped are `update` and `set` (however, these do represent the vast majority of traffic, so the benefits of these being unreliable may be significant). They should generally only be unreliable when they are being sent very frequently. When they are no longer being sent frequently, the last message should be sent reliably to prevent getting stuck on a stale value. Note that this last message will generally have the same value as the previous unreliable messages.

---

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/).
