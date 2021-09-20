# OpenStarscape Orbit Test Data
This directory contains [test data](orbit-test-data.json) to verify OpenStarscape implementations are calculating orbits correctly, and the [code](generate-orbit-test-data.json) to generate this test data. The same data can be used for both client and server tests. It's recommended that all clients that use orbits implement and pass these tests. The data is represented in JSON. Some projects (like the TypeScript web client which uses Jest for testing) can use this JSON directly in tests and others (like the Rust server) need to generate test code based on it.

## Server Test Ambiguity
This is applicable to server tests only. all parameter-defined orbits can be used to calculate a the location/velocity of a body, but some body states can be represented by multiple orbits. Specifically circular orbits (semi-major == semi-minor) can have any `periapsis_angle` value and orbits on the X/Y plane (inclination == 0) can have any `ascending_node_angle` value (but the chosen `ascending_node_angle` affects `periapsis_angle`, which may be meaningful. The tests here always use 0 in such ambiguous cases, and so server implementations must also use 0.

## JSON Format
The JSON file is an array of test cases. Each test case is a JSON object. Each test case has the following properties:
- `comment`: documentation comment. Only present in the first test.
- `name`: unique human-readable name of the test case. Contains only letters, numbers and spaces.
- `orbit`: the six scalar orbit parameters. Same as a [body's orbit property](../orbit.md#orbit-property), except the `grav_parent` object ID is excluded.
- `grav_param`: the [standard gravitational parameter](https://en.wikipedia.org/wiki/Standard_gravitational_parameter). Mass of the gravity parent × the gravitational constant.
- `at_time`: the "current" time when the test takes place.
- `position`: the position of the body relative to the gravity parent.
- `velocity`: the velocity of the body relative to the gravity parent's velocity.
