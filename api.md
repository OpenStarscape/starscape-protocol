# OpenStarscape API
This document defines the API clients use to communicate with an OpenStarscape server. The data types and concepts are from the [core protocol](core.md).

## Conventions
### Color Representation
Colors are represented as an RGB hex string starting with #. For example blue is represented as `"#0000FF"`.

## Physics
### Units
Starscape values are in the following units:
- Time: in-game seconds (real time seconds generally match in-game seconds) (s)
- Distance: kilometers (km)
- Velocity: kilometers per second (km/s)
- Mass: metric tons (t)

### Constants
The gravitational constant is 6.67430×10^-17 (the real gravitational constant represented in our units).

### Gravity Parents
To aid with orbits, autopilot, and other things, Starscape has the concept of "gravity parents". Most simply, a body's gravity parent can be thought of as the body it is orbiting around. Not all bodies have gravity parents at all times. A body has at most 1 gravity parent at a given time. Parents and parent's parents form a tree. In our solar system, Luna's parent is Earth and earth's parent is Sol. Sol has no parent.

The details of how parents are calculated is a little more complex. The child is always less massive than it's parent (this guarantees there will never be a circular relationship). The parent is the _least_ massive body for which the child is within the approximate [sphere of influence](https://en.wikipedia.org/wiki/Sphere_of_influence_(astrodynamics)). Sphere of influence is calculated as `rSOI = a(m/M)^(2/5)` (where `rSOI` is the radius of the sphere of influence, `a` is the semi-major axis, `m` is the mass of the smaller body and `M` is the mass of the larger body).

## Objects
### Root
The entry-point object.

Derives from: __none__

Properties:
- `time` (RO `scalar`): the current in-game time, in seconds.
- `time_per_time` (RW `scalar`): in-game seconds to simulate per real-world second. If this is set too high the game will not be able to perform all required physics ticks within `network_tick_interval`, and the rate of network ticks will slow down.
- `physics_tick_duration` (RW `scalar`): in-game time in seconds to simulate physics for each physics tick.
- `network_tick_interval` (RW `scaler`): target real-world time in seconds between messages to clients. Effective duration will be rounded up to the nearest physics tick. If the difference between this and `min_roundtrip_time` isn't enough time to do all required work then the game will slow down.
- `min_roundtrip_time` (RW `scalar`): minimum real-world time in seconds to idle during each network tick. Clients can respond to messages in a single tick if their ping is faster than this.
- `pause_at` (RW `scalar | null`): the in-game time at which to pause the game. When this time is reached the `paused` signal is fired, `time_per_time` is set to `0` and `pause_at` is set to `null`.
- `quit_at` (RW `scalar | null`): the in-game time at which to quit the game.
- `conn_count` (RO `integer`): the current number of clients connected to this server.
- `bodies` (RO `array<object<Body>>`): all currently existing bodies in space.
- `max_conn_count` (RW `integer`): the maximum number of allowed client connections. After this is reached, new connection attempts will be rejected.

Signals:
- `error` (`string`): a recoverable error occurred, generally due to the client making an invalid request.
- `paused` (`scalar`): the game has been paused, perhaps because `time_per_time` was set to `0` or `pause_at` was reached. The value is the in-game time that has been paused on. If the cause of the pause was `pause_at` then the pause time will be >= what `pause_at` was.
- `ship_created` (`object<Ship>`): notification that a new ship has been created (by this client or some other client).

Actions:
- `reset` (`null`): resets the game state. All bodies and other game objects will be removed and `time` will be set back to `0`. Settings such as `physics_tick_duration` and `time_per_time` are unchanged.
- `create_ship` (`{name?: string, position?: vector3, velocity?: vector3, radius?: scalar, mass?: scalar, max_accel?: scalar`): create a ship body.
- `create_celestial` (`{name?: string, color?: string, position?: vector3, velocity?: vector3, radius?: scalar, mass?: scalar`): create a celestial body, like a sun, planet or moon.
- `pause_on_proximity` (`{a: Object<Body>, b: Object<Body>, distance: number, velocity: number}`): used for tests. game is paused when the two bodies are are both closer than or equal to `distance` to each other and their velocities are closer than or equal to `velocity`. Note that unlike collision detection, a physics tick must end with the described state in order to trigger the pause.

### Body
The entry-point object.

Derives from: __none__

Properties:
- `class` (RO `string`): the type of body (can be `"celestial"` or `"ship"`).
- `grav_parent` (RO `object<Body> | null`): simply, can be thought of as the body, if any, this body is orbiting around. See (Gravity Parents)[#gravity-parents] for details.
- `orbit` (RO
    ```
    [
      semi_major_axis:      scalar,
      semi_minor_axis:      scalar,
      inclination_angle:    scalar,
      ascending_node_angle: scalar,
      periapsis_angle:      scalar,
      base_time:            scalar,
      period_time:          scalar,
      grav_parent:          object<Body>
    ] | null
    ```
    ): see [orbit.md](orbit.md).
- `position` (RW `vector3`): where the body currently is in 3D space (km).
- `velocity` (RW `vector3`): how fast and in what direction the body is moving (km/s).
- `mass` (RW `scalar`): mass of the body (t).
- `color` (RW `string | null`): optional hint to clients of what color they may use for the body. See [Color Representation](#color-representation).
- `name` (RW `string | null`): optional human-readable name of the body. May contain any Unicode.
- `size` (RW `scalar`): the size of this body (km). May be 0. Affects collision handling. All bodies are currently assumed to be spherical for the purposes of collision handling.

Signals: __none__

Actions: __none__

### Celestial
Unpowered body, such as a star, planet, moon, asteroid, etc.

Derives from: __Body__

Properties: __none__

Signals: __none__

Actions: __none__

### Ship
The entry-point object.

Derives from: __none__

Properties:
- `accel` (RW `vector3`): the amount and direction of thrust (km/s/s). Values higher than `max_accel` are clamped without changing direction.
- `max_accel` (RW `scalar`): the maximum amount of acceleration per second per second (km/s/s).
- `ap_scheme` (RW `string`): the type of autopilot to use (can be `"off"` or `"orbit"`).
- `ap_target` (RW `object<Body> | null`): in orbit autopilot, the body this ship should try to orbit around.
- `ap_distance` (RW `scalar | null`): in orbit autopilot, the distance away from the target to orbit (if null, a reasonable default is chosen).

Signals: __none__

Actions: __none__

---

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/).
