# 🏝️ Castaway

A single-player 3D survival game built with [Three.js](https://threejs.org/). Survive the night.

## Overview

You wake up as the sole survivor of a shipwreck, stranded on a small tropical island. The wreckage still smolders in the shallows. You have one in-game day — roughly three minutes — to gather resources, build a campfire, and raise defensive walls before hostile creatures rise from the water at nightfall.

Each day-night cycle follows a clear arc: **arrive → explore → gather → build → defend → survive**. Lasting until sunrise increments your "days survived" score and resets the loop, with enemies growing more aggressive each night.

---

## Team

| Name | Email | Student ID |
|------|-------|------------|
| Yahir Dimas | yd1mas@outlook.com | 406049814 |
| Jeremy Dimas | jeremydimas10@gmail.com | 606049078 |
| Brüno Cardenas-Bourges | brunocarbourges@ucla.edu | 005972407 |

---

## Gameplay

The island spans ~40 units in diameter, ringed by ocean that fades into a procedural sky. Three harvestable resource types are scattered across it:

- 🌴 **Trees** → Wood
- 🪨 **Rocks** → Stone
- 🫐 **Bushes** → Berries

Resources fuel two strategies: **build wall segments** to block enemy paths, or **hold your ground** within the campfire's light radius and fight back directly.

---

## Controls

| Input | Action |
|-------|--------|
| `WASD` | Move |
| Mouse | Look (pitch & yaw via PointerLockControls) |
| `Space` | Jump |
| `Left Click` | Chop / Mine / Attack |
| `Right Click` | Place wall segment (build mode) |
| `1–9` | Select inventory hotbar slot |
| `E` | Add fuel to campfire |
| `B` | Toggle build mode |
| `F` | Toggle wireframe debug overlay |

All interactions are resolved through a single `THREE.Raycaster`, giving the entire interaction model — chopping, mining, attacking, building — a consistent underlying mechanism.

---

## Technical Features

### Course Topics Covered (through Week 7)

- **Transformation matrices** — 4×4 matrices for placing all world objects and computing the player's view transform
- **Perspective camera** — First-person controls with pitch/yaw
- **Blinn-Phong lighting** — Via `MeshStandardMaterial`, with texture maps for sand, bark, stone, and water
- **Vertex & fragment shaders** — Day-night sky gradient and animated water surface
- **Raycasting** — Mouse picking and all player-environment interactions
- **Per-frame animation** — Interpolation of sun direction, water vertices, and mob positions
- **OBJLoader** — Player and mob model imports; Three.js primitives for vegetation and terrain

### Advanced Features

#### 1. Collision Detection (AABB)
Every solid object (trees, rocks, walls, mobs, player) is approximated as an axis-aligned bounding box. A coarse spatial hash on the island grid limits pairwise checks each frame. On overlap, movement is projected onto non-colliding axes so the player slides along surfaces. Combat uses ray-vs-AABB from the camera; mob damage uses AABB-vs-AABB intersection. AABB was chosen over SAT because all bounding volumes are axis-aligned, making it faster and fully sufficient.

#### 2. Real-Time Shadow Mapping
A directional light represents the sun, rotating around the X-axis through the day-night cycle. Shadow casting runs at 2048×2048 with a frustum fitted tightly around the playable island for high texel density. All world objects cast and receive shadows. At night, the campfire becomes a point light with its own shadow map, producing dramatic localized shadows. The gradual shift from long afternoon shadows into total darkness is the cycle's most visually striking moment.

#### 3. AI Mob Behavior (Finite State Machine + A\*)
Each mob runs a three-state FSM:

| State | Behavior |
|-------|----------|
| `WANDER` | Follows a random heading, re-rolled at intervals |
| `CHASE` | Steers toward the player once within visibility range; avoids getting stuck on walls |
| `ATTACK` | Deals damage on a cooldown when adjacent to the player |

For navigating around player-built walls, mobs use **A\*** on a coarse 1×1 unit grid that re-bakes whenever a wall is placed or destroyed. Keeping the grid coarse ensures pathfinding stays cheap even with multiple mobs active simultaneously.

---

## Stretch Goals

If the core build is stable before the final demo:

- **Bump/normal mapping** on sand, bark, and stone via `MeshStandardMaterial.normalMap`
- **Rising water level** that gradually shrinks the playable island over time
- **Particle system** releasing drifting light motes when a mob is defeated
- **Crafting** — combine wood + stone into an axe for one-hit tree felling

---

## Implementation Roles

**Yahir Dimas — World & Atmosphere**
Island geometry, water plane shader, vegetation and rock placement, sky and day-night cycle, sun and shadow setup, campfire point light.

**Jeremy Dimas — Player & Interaction**
First-person controls, raycasting for chopping/mining/attacking, inventory and hotbar system.

**Brüno Cardenas-Bourges — AI & Combat**
Mob OBJ models, spawning logic tied to time of day, finite-state machine, A\* pathfinding, combat math, mob health, sound integration.

---

## Built With

- [Three.js](https://threejs.org/)
- `OBJLoader` for character and mob models
- `PointerLockControls` for first-person camera
- `MeshStandardMaterial` for PBR lighting and texture maps
- Custom GLSL vertex/fragment shaders for sky and water
