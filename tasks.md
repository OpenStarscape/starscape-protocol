# OpenStarscape tasks
## MVP
- # Integration tests
- # Improved autopilot
- # General graphics improvements
- # Improved camera motion
- # Simple UI framework
- Sensible UI layout
- Basic CSS
- Body display size dependent on body size?
- Make sure bodies are correct size
- Make sure scaling is sensible
- Missile-optimized autopilot
- Action for firing missle from ship
- Limits on missles fired from ship
- Missiles cause splash damage on detonation
- Missiles detonate when close to target
- Display missile explosions

## Polish
- Smoother camera motion
- Error messages in well-styled popups
- Reactive layout

## Refactor
- Split CSS into different files (index, toolkit (for classes used by create), game-ui, space-scene)
- Autopilot needs a refactor

## Infra
- filter-branch out deadname from old commits
- Merge server, web, protocol and deploy into single repo
- Archive GitHub and move to Codeberg
- Run server in container
- 2nd server program to manage CI and deployment?
- Allow running integration tests from command line
- Auto-build and deploy on push to main
- Build, run tests and spin up playable instance for each PR

## Optimize
- Investigate poor frontend perf (probably due to allocating too many objects (probably due to protocol parser))
- Build fancy collision detection 3D tree
- Are we really only sending orbit data when we need to?

## Tasks view
- # Fixed AnimationTimer bug
- # Fixed Spatial bugs
- # Made basic mockup
- Server state persistence across restarts
- Authorization system
- More extensive interface
