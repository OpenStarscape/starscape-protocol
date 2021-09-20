# Orbit Parameters
Orbits are complicated. Its possible to implement a Starscape client without using them, but there are a number of issues that crop up. Since orbiting objects are constantly changing direction, a client that relies on position/velocity needs constant updates from the server. It's impractical to transmit the location of everything 30 or 60 times per second, so framerates suffer. By transmitting orbits, much less frequent updates are needed for many bodies, and they can be animated in real time.

## References
- [orbitalmechanics.info](https://orbitalmechanics.info/) is an interactive visualiser that mostly matches the Starscape orbit parameters, very useful to help wrap your head around things.
- [The Wikipedia page](https://en.wikipedia.org/wiki/Orbital_elements) is, well, the Wikipedia page.

## Concepts
- __Ascending Node__: the point where the orbit crosses the global XY plane going up. Specifically, where the body's Z-position equals the parent's Z-position and the body has a positive Z-velocity.
- __Periapsis__: the closest point on the orbit to the parent. This is also the point at which the body is moving the fastest.
- __Mean Anomaly__: the current time within the orbit cycle, represented as an angle. At the start of an orbit, the mean anomaly is 0. After 1/4th the period time has elapsed, it's τ/4 radians. The mean anomaly is not particularly useful on it's own, but important in calculations.
- __Eccentric Anomaly__: what the angle of the body would if if the orbit were squished to a circle. Also useful in calculations.

## Orbit Property
The `orbit` property of a Body is either `null` or `array` of 7 elements (six `scalars` and the gravity parent). Only elliptical/circular orbits can be represented.
- `semi_major_axis`: length (km) of the semi-major axis (long radius). Commonly `a`.
- `semi_minor_axis`: length (km) of the semi-minor axis (short radius). Commonly `b`.
- `inclination_angle`: tilt (radians) of the orbital plane above the global X/Y plane. Ranges from 0 to π. Commonly `i`.
- `ascending_node_angle`: the angle (radians) of the ascending node compared to the parent on the global X/Y plane. Commonly `Ω`.
- `periapsis_angle`: the angle in radians of the periapsis relative to the ascending node on the orbit plane. This means 0 puts the periapsis at the ascending node and π/2 puts the periapsis at the point on the orbit with the highest global Z value. Commonly `ω`.
- `base_time`: A time (seconds) at which the body was or will be at the periapsis (assuming it followed and will follow the current orbit). May be negative or after the current time.
- `period_time`: The time it takes to complete one full orbit.
- `grav_parent`: of type `object<Body>` (not `scalar`, like the rest). the same as the `grav_parent` property. Included here because it must be updated atomically with the rest of the orbit parameters.

If the body does not have a gravity parent or is not in an elliptical orbit around it's gravity parent, the orbit property is `null`.

# TODO
- Add pseudocode example of calculating position and velocity of a body from an orbit.
- Add Python reference implementation that passes the test cases.

## Tests
Because orbits are hard to implement right, we provide a collection of test cases which can be used for both client and server implementations. See [orbit tests](orbit-tests) for details.

---

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/).
