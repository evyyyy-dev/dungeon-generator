# Deterministic Dungeon Generator
> Modular dungeon generation with deterministic seeds and collision-safe placement.

---

## Features

**Core System**
- Deterministic (seed-based) generation
- Collision validation
- Easily expandable, weighted room system
- Backtracking support for dead-end recovery

**Tooling**
- Interactive state-machine driven control panel
- Manual seed + room count input
- Runtime regeneration & cleanup

---

## Showcase

[![Watch showcase](https://img.youtube.com/vi/vrHEV04Z8go/0.jpg)](https://www.youtube.com/watch?v=vrHEV04Z8go)

---

## Architecture

```
Workspace
 ├─ LoadedRooms                        │ The folder where all loaded rooms are inside of.
 └─ GeneratorModel                     │ State-machine handling room and seed input, that also listens for initialization/generation commands.
     └─ ControlPanel
         └─ SurfaceGui
            ├─ Frame   
            │   └─ InputContainer
            │       ├─ RoomInput       │ Number input for the amount of rooms to generate.
            │       └─ SeedInput       │ Number input for the seed, if nothing is specified it uses a random seed.
            └─ StartButton

ReplicatedStorage
 ├─ Modules
 │   ├─ Core
 │   │   └─ Generator                  │ The main module that handles placing, collision checking, backtracking, and more.
 │   └─ Utils
 │       └─ Shared                     │ Shared variables used across 2+ scripts.
 ├─ Rooms
 │   └─ ExampleRoom
 │       ├─  Connectors                │ A folder containing both start and end connectors, used for placement
 │       │  ├─ StartConnector          │ The StartConnector pivots to the previous room's EndConnector.
 │       │  └─ EndConnector
 │       ├─ Geometry
 │       │  ├─ Walls
 │       │  └─ Floor
 │       ├─ Props
 │       └─ MetadataValues             │ Metadata of the room containing it's weight (chance to get picked) and type.
 ├─ DefaultMetadata                    │ Used in case a room is missing MetadataValues.
 ├─ ModuleRegistry                     │ Centralized module access, so renaming a module requires updating only one variable.
 └─ StartGeneratingEvent

ServerScriptServie
 └─ RoomGeneration                     │ State machine that initializes, generates, and if needed, erases the whole dungeon.
```

#### System flow

- The GeneratorModel state-machine listens for the room count and seed input.

- On initialization, the Generator module validates all room templates, ensuring required metadata and primary parts exist.

- When generation begins, the starting room is placed at index 0.

- The generator enters a loop that continues until the required number of rooms has been placed.

- At each index, the system either attempts to place a forced room or selects a room using weighted random chances.

- If placement fails, the generator retries other available rooms that have not yet been attempted.

- If no valid room can be placed, the system backtracks several rooms and continues generation from there.

- If the retry limit is exceeded, generation fails to prevent infinite loops.

- When all rooms are successfully placed, the generator finishes and reports the generation time.

---

## Code snippets

Check out [code-snippets.lua](code-snippets.lua) for code examples.

---

## Why I Made This

I made this because I wanted to build something bigger and more organized than all of my previous projects.

My goal here was to design a modular system that could effortlessly be expanded later.

This was my first time making something like this, and I treated it as a way to improve my architectural decisions and performance awareness.

## What I Learned

This project helped me better understand state-driven systems, but mainly how generation flow can be structured cleanly.

I also learned how small decisions can significantly impact performance. My original approach cloned and positioned the complete room models to test for collisions. I later rewrote this to use `GetPartsInBoundBox` as an imaginary bounding box before instantiation, which reduced unnecessary operations and improved efficiency.

In addition, I gained more experience working with metadata-driven architecture instead of hardcoded logic. Designing the system around room metadata made it a lot easier to extend and maintain.

Lastly, I became more intentional about writing cleaner code by reducing redundancy and organizing logic in a way, so that it improves readability.

## What I'd Improve

I consider changing the layout generation completely, by generating only the full layout first (without models), validating it, and only then cloning and placing the room models. This could optimize performance even further and make the system more scalable.

---

> ✅ **Status:** Complete, although further improvements may be made.
