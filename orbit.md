# Orbit Parameters
Orbits are complicated. It's possible to implement a Starscape client without using orbits, but it leads to poor performance. Since orbiting objects are constantly changing direction, a client that relies on position/velocity needs constant updates from the server. It's impractical to transmit the location of all bodies 30 or 60 times per second, so the framerate suffers. By transmitting orbits, much less frequent updates are needed for many bodies, and they can be animated in real time.

## References
- [orbitalmechanics.info](https://orbitalmechanics.info/) is an interactive visualiser that mostly matches the Starscape orbit parameters, very useful to help wrap your head around things.
- [The Wikipedia page](https://en.wikipedia.org/wiki/Orbital_elements) is, well, the Wikipedia page.

## Concepts
- __Ascending Node__: the point where the orbit crosses the global XY plane going up. Specifically, where the body's Z-position equals the parent's Z-position and the body has a positive Z-velocity.
- __Periapsis__: the closest point on the orbit to the parent. This is also the point at which the body is moving the fastest.
- __Mean Anomaly__: the current time within the orbit cycle, represented as an angle. At the start of an orbit, the mean anomaly is 0. After 1/4th the period time has elapsed, it's τ/4 radians. The mean anomaly is not particularly useful on it's own, but important in calculations.
- __Eccentric Anomaly__: what the angle of the body would if if the orbit were squished to a circle. Also useful in calculations.

## Orbit Property
The `orbit` property of a Body is either `null` or an `array` of 7 elements (six `scalars` and the gravity parent). Only elliptical/circular orbits can be represented.
- `semi_major_axis`: length (km) of the semi-major axis (long radius). Commonly `a`.
- `semi_minor_axis`: length (km) of the semi-minor axis (short radius). Commonly `b`.
- `inclination_angle`: tilt (radians) of the orbital plane above the global X/Y plane. Ranges from 0 to π. Commonly `i`.
- `ascending_node_angle`: the angle (radians) of the ascending node compared to the parent on the global X/Y plane. Commonly `Ω`.
- `periapsis_angle`: the angle in radians of the periapsis relative to the ascending node on the orbit plane. This means 0 puts the periapsis at the ascending node and π/2 puts the periapsis at the point on the orbit with the highest global Z value. Commonly `ω`.
- `base_time`: A time (seconds) at which the body was or will be at the periapsis (assuming it followed and will follow the current orbit). May be negative or after the current time.
- `period_time`: The time it takes to complete one full orbit.
- `grav_parent`: of type `object<Body>` (not `scalar`, like the rest). the same as the `grav_parent` property. Included here because it must be updated atomically with the rest of the orbit parameters.

If the body does not have a gravity parent or is not in an elliptical orbit around it's gravity parent, the orbit property is `null`.

TODO: add reference implementation that actually works

## Calculating Position
The most common user for an orbit is for a client to calculate a body's current position and velocity from it. This is, unfortunately, not easy. The following code is in Python-like pseudocode adapted from the web client.

The first step is to calculate mean anomaly.
```python
orbits_since_start = (time - base_time) / period_time
completed_portion_of_orbit = orbits_since_start - floor(orbits_since_start)
mean_anomaly = completed_portion_of_orbit * tau
```

We also need to calculate the orbit's eccentricity.
```python
distance_from_center_to_foci = sqrt(semi_major_axis**2 - semi_minor_axis**2)
eccentricity = distance_from_center_to_foci / semi_major_axis
```

Next, we use the mean anomaly to calculate the eccentric anomaly. I ripped this code out of [a StackOverflow answer](https://space.stackexchange.com/a/8915), and I have no idea why it works. Thanks 2012rcampion and jumpjack!
```python
eccentric_anomaly = mean_anomaly # default value to start with
for i in range(5): # 5 iterations is plenty for high accuracy
    delta = (
        (eccentric_anomaly - eccentricity * sin(eccentric_anomaly) - mean_anomaly) /
        (1 - eccentricity * cos(eccentric_anomaly))
    )
    eccentric_anomaly -= delta;
```

Finally, we use the eccentric anomaly to calculate the body's position.
```python
# Position on the unit circle
position = Vector3(
    cos(eccentric_anomaly),
    sin(eccentric_anomaly),
    0,
)

# Position on the orbital plane
position.x *= semi_major_axis
position.y *= semi_minor_axis
position.x -= distance_from_center_to_foci

# Position relative to the gravity parent
# Rotate by Z, then X, then Z again. We do not need to rotate by Y
position.rotate_z(periapsis_angle)
position.rotate_x(inclination_angle)
position.rotate_z(ascending_node_angle)

# Position in global coordinates
position += gravity_parent_position
```

## Calculating Velocity
To calculate velocity we need speed and direction. First, we calculate speed.
```python
standard_gravitational_parameter = semi_major_axis**3 / (period_time / tau)**2;
# distance_to_gravity_parent is also called "radius" or "r" in equations
distance_to_gravity_parent = (position - gravity_parent_position).length()
# vis-viva equation
speed = sqrt(standard_gravitational_parameter * ((2 / radius) - (1 / semi_major_axis)));
```

Then, we calculate direction
```python
# Direction on the unit circle
velocity = Vector3(
    -sin(eccentric_anomaly),
    cos(eccentric_anomaly),
    0,
)

# Direction on the orbital plane
velocity.x *= semi_major_axis
velocity.y *= semi_minor_axis

# Velocity on the orbital plane
velocity.set_length(speed)

# Velocity in global coordinates
# Rotate by Z, then X, then Z again. We do not need to rotate by Y
velocity.rotate_z(periapsis_angle)
velocity.rotate_x(inclination_angle)
velocity.rotate_z(ascending_node_angle)
```

## Tests
We provide a collection of test cases which can be used for both client and server implementations. See [orbit tests](orbit-tests).

---

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/).
