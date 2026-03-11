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
 └─ GeneratorModel                     │ Model used to interact with the state-machine.
     └─ ControlPanel
         └─ SurfaceGui
             ├─ Frame   
             │   └─ InputContainer
             │       ├─ RoomInput      │ Number input for the amount of rooms to generate.
             │       └─ SeedInput      │ Number input for the seed, if nothing is specified it uses a random seed.
             └─ StartButton


ReplicatedStorage
 ├─ Modules
 │   └─ Generator                      │ The main module that handles placing, collision checking, backtracking, and more.
 ├─ Rooms
 │   └─ ExampleRoom
 │       ├─ Connectors                 │ A folder containing both start and end connectors, used for placement.
 │       │   ├─ StartConnector         │ The StartConnector pivots to the previous room's EndConnector.
 │       │   └─ EndConnector
 │       ├─ Geometry
 │       │   ├─ Walls
 │       │   └─ Floor
 │       ├─ Props
 │       └─ MetadataValues             │ Metadata of the room containing its weight (chance to get picked) and (optional) flags (like isStart or isForced).
 ├─ DefaultMetadata                    │ Used in case a room is missing MetadataValues.
 └─ StartGeneratingEvent


ServerScriptServie
 └─ RoomGeneration                     │ State machine that initializes, generates, and if needed, erases the whole dungeon.


StarterCharacterScripts
 └─ RoomGenerationClient               │ Small script to listen for MouseButton1Click events inside of the StartButton (in GeneratorModel).
```

#### System flow
- `RoomGenerationClient` listens for `StartButton` clicks and fires `StartGeneratingEvent` when the room count and seed input are valid.

- On initialization, the `Generator` module validates all room templates, making sure that the required metadata and primary parts exist. If one is missing, it uses its default value.
 
- When generation begins, the starting room is placed at index 0.

- The generator enters a loop that continues until the required number of rooms has been placed.

- At each index, the system either attempts to place a forced room or selects a room using weighted random chances.

- If placement fails, the generator retries other available rooms that have not yet been attempted.

- If no valid room can be placed, the system backtracks several rooms and continues generation from there.

- If the backtracking retry limit is exceeded, generation fails to prevent infinite loops.

- When all rooms are successfully placed, the generator finishes and reports the generation time.

---

## Code snippets

Check out [code-snippets.lua](code-snippets.lua) for code examples.

---

## Why I Made This


I started this project after asking a friend what kind of system I should build next. She suggested a random map generator, similar to a rougelike, which gave me the idea to make a deterministic dungeon generator.

At the same time, I wanted to challenge myself by building something larger and more organized than anything I had made before. This project was a way to design a more complex system and improve my overall architecture.

## What I Learned

This project helped me better understand state-driven systems, but mainly how generation flow can be structured cleanly.

I also learned how small decisions can impact performance. My original approach cloned and positioned the complete room models to test for collisions. I later rewrote this to use `GetPartsInBoundBox` as an imaginary bounding box before placement, which reduced unnecessary operations and improved efficiency.

In addition, I gained more experience working with metadata-driven architecture instead of hardcoded logic. Designing the system around room metadata made it a lot easier to extend and maintain.

Lastly, I became more intentional about writing cleaner code by reducing redundancy and organizing logic in a way so that it becomes easier to read.

## What I'd Improve

I would consider changing the layout generation completely, by generating only the full layout first (without models), validating it, and only then cloning and placing the room models. This could optimize performance even further and make the system more scalable.

---

> ✅ **Status:** Complete, although further improvements may be made.
