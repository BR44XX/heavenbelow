# Level 1 — Surface Ruins, build sheet

One enclosed vertical shaft. Six landings, alternating left and right, 700 units apart.
Run to the open end, drop, repeat. Geometry enforces "no backtracking upward" — you physically
cannot get back up.

## Conventions — read these

- Movement axis is **X**. The play plane is **Y = 0**. All rotations `0,0,0` unless noted.
- **Use `Content/LevelPrototyping/Meshes/SM_Cube` for all structure.**
- **Never use `/Engine/BasicShapes/Cube`.** That asset is broken: its mesh is 100x100x100 but its
  simple collision is a box of **316.5 x 316.5 x 316.5**, overhanging every face by **108 units**.
  You float 108 above every floor and stop 108 short of every wall. `SM_Cube` uses a convex hull
  generated from the mesh, so its collision matches what you see.
- **`SM_Cube`'s pivot is at its minimum corner**, not its centre — bounds Origin `(50,50,50)`,
  Extent `(50,50,50)`, so it occupies local `0 → 100`. A cube at Location `L` with Scale `S`
  occupies **`L` → `L + 100·S`**. **Location is the corner it grows from**, and a slab's
  **walking surface is `Location Z + 100 · Scale Z`**.
- **The play plane sits at the FRONT of the geometry.** Landings are `Location Y = -550`,
  `Scale Y = 6`, spanning Y `-550` → `+50`. The Diver at Y `0` stands 50 from the front lip —
  enough to support the capsule (radius 35), little enough that nothing sits between you and the
  camera. The camera is a 900-unit spring arm along +Y; geometry in front of the play plane
  projects lower and wider than where it really is, which reads as floating and as invisible walls.
- **Every Blueprint actor is placed at Scale `1, 1, 1`.** Never scale a Blueprint — its size lives
  inside it, and scaling the actor multiplies on top.
- Blueprint origins sit **on the walking surface**, so a Blueprint's Z is just the landing's surface Z.
- Structure material: `MI_PrototypeGrid_Gray` (`Content/LevelPrototyping/Materials`).

Shaft interior is X `0`-`2000`, Y `-550`-`+50`, Z `-3600` to `+650`.
Walking surfaces at Z `0`, `-700`, `-1400`, `-2100`, `-2800`, `-3500`. Clear height 650 on every one.

> **Where I am up to:** rebuilding the level on `SM_Cube` after the `/Engine/BasicShapes/Cube`
> collision defect. Blueprint fixes in section 0 first.

---

## 0. Blueprint fixes

The meshes inside these Blueprints are `SM_Cube`, so they inherit the corner pivot. The `-550` on Y
is the same front-of-plane shift the landings use, so the slabs line up with the floor around them.

**`BP_SlidingFloor`** — 800 x 600 x 50 slab with its top face at the origin.

| Component | Location | Scale / Extent |
|---|---|---|
| `StaticMesh` | `-400, -550, -50` | Scale `8, 6, 0.5` |
| `Box` | — *(root, not editable)* | Box Extent `400, 300, 100` |

**`BP_BreakablePlatform`** — 400 x 600 x 50. **Drag `Box` onto `DefaultSceneRoot` first** — it is
parented under `StaticMesh` and inherits its scale, so any Extent you type gets multiplied by it.

| Component | Location | Scale / Extent |
|---|---|---|
| `StaticMesh` | `-200, -550, -50` | Scale `4, 6, 0.5` |
| `Box` | `0, 0, 0` | Box Extent `200, 300, 100` |

**`BP_LockedDoor`** — 200 x 600 x 300 standing on the origin.

| Component | Location | Scale |
|---|---|---|
| `door` | `-100, -550, 0` | Scale `2, 6, 3` |

**`BP_SpikeTrap`** — one field. Its cone hangs below the origin and would sit buried in the floor.

| Component | Location |
|---|---|
| `StaticMesh` | `0, 0, 50` |

**`BP_Diver`** — `InteractRange` has a dragged-in scale of `(0.39, 0.42, 0.35)` on it, cutting its
200 radius to about 70 and squashing it. Set its **Scale** to `1, 1, 1`.

The `Box` on `BP_SlidingFloor` and `BP_BreakablePlatform` stays centred on the actor origin — a
trigger only has to cover Y `0`, which it does.

Compile and Save each one.

---

## Shaft structure

Four cubes. Location is the corner each block grows from.

| Actor | Location | Scale | Occupies |
|---|---|---|---|
| `SM_Cube` — left wall | `-50, -600, -3600` | `0.5, 6.5, 43` | X -50 → 0 |
| `SM_Cube` — right wall | `2000, -600, -3600` | `0.5, 6.5, 43` | X 2000 → 2050 |
| `SM_Cube` — back wall | `-50, -600, -3600` | `21, 0.5, 43` | Y -600 → -550 |
| `SM_Cube` — ceiling | `-50, -600, 650` | `21, 6.5, 0.5` | Z 650 → 700 |

Walls, back wall and ceiling deliberately **overlap at the corners**. If the back wall only spanned
X `0`→`2000` it would meet the side walls edge-to-edge and leave an L-shaped notch running the full
height. Overlapping solid cubes interpenetrate; nothing z-fights.

---

## Landings

The 400-wide gap alternates sides — that gap is the way down.

| # | Location | Scale | Spans X | Surface | Gap |
|---|---|---|---|---|---|
| L0 | `0, -550, -50` | `16, 6, 0.5` | 0 → 1600 | `0` | X 1600-2000, right |
| L1 A | `1400, -550, -750` | `6, 6, 0.5` | 1400 → 2000 | `-700` | X 0-400, left |
| L1 B | `400, -550, -750` | `2, 6, 0.5` | 400 → 600 | `-700` | sliding floor fills 600-1400 |
| L2 A | `0, -550, -1450` | `10, 6, 0.5` | 0 → 1000 | `-1400` | hole X 1000-1400 |
| L2 B | `1400, -550, -1450` | `2, 6, 0.5` | 1400 → 1600 | `-1400` | X 1600-2000, right |
| L3 | `400, -550, -2150` | `16, 6, 0.5` | 400 → 2000 | `-2100` | X 0-400, left |
| L4 | `0, -550, -2850` | `16, 6, 0.5` | 0 → 1600 | `-2800` | X 1600-2000, hatch |
| L5 | `0, -550, -3550` | `20, 6, 0.5` | 0 → 2000 | `-3500` | none — bottom |

---

## L0 — surface, run right · move, jump, fall

| Actor | Location | Notes |
|---|---|---|
| `PlayerStart` | `200, 0, 100` | already in the level — just move it |
| `BP_SpikeTrap` | `900, 0, 0` | jump it |
| `BP_BreakablePlatform` | `1800, 0, 0` | fills the gap X 1600-2000 — **this is the exit** |

Looks like solid floor. Step on it, it collapses, you drop to L1. No tutorial text.

## L1 — run left · checkpoint, oxygen, slide

| Actor | Location | Notes |
|---|---|---|
| `BP_Checkpoint` | `1800, 0, -700` | you land next to it |
| `BP_AirPocket` | `1550, 0, -700` | — |
| `BP_SlidingFloor` | `1000, 0, -700` | fills X 600-1400 — cross it sliding or take 20 |

No way back up and nothing to jump to, so the only way past is through. Walk off at X 400.

## L2 — run right · torch, crystal, Echo 1

| Actor | Location | Scale / Notes |
|---|---|---|
| `BP_AirPocket` | `300, 0, -1400` | — |
| `BP_Torch` | `600, 0, -1400` | — |
| `BP_Crystal` | `800, 0, -1400` | **Linked Torch** = that torch · **Linked Platform** = the bridge |
| `SM_Cube` — bridge | `1000, -550, -1450` | `4, 6, 0.5` · fills the hole X 1000-1400 · tick **Actor Hidden In Game** |
| `BP_Echo` | `1500, 0, -1400` | **Echo ID** `Echo1` |

The floor stops at X `1000` with Echo1 visible across the hole. Light the torch, hit the crystal,
the bridge fills the gap, cross and take it. Skip it and you drop through onto L3 — you still
progress, you just lose the Echo.

**Height cannot gate an Echo.** The Diver jumps 287 (`JumpZVelocity` 750 against gravity 980), and
anything you can land on you can also clip through mid-jump. That is why this is a bridge across a
hole and not a shelf to jump onto.

## L3 — run left · Denizen, key, locked door

| Actor | Location | Notes |
|---|---|---|
| `BP_Checkpoint` | `1800, 0, -2100` | — |
| `BP_AirPocket` | `1550, 0, -2100` | — |
| `BP_Denizen` | `1200, 0, -2010` | floor +90 — Character capsule origin is at its centre |
| `BP_LockedDoor` | `700, 0, -2100` | **Move Offset** `0, 0, 350` |

The door rises into the ceiling. 350 is the most it can travel before it clips L2's underside at `-1450`.

## L4 — run right · lever, Echo 2

| Actor | Location | Scale / Notes |
|---|---|---|
| `BP_Checkpoint` | `300, 0, -2800` | — |
| `BP_SpikeTrap` | `700, 0, -2800` | — |
| `BP_Echo` | `1000, 0, -2800` | **Echo ID** `Echo2` |
| `BP_Lever` | `1300, 0, -2800` | Target Actor = the hatch · **Move Offset** `0, 0, 500` |
| `SM_Cube` — hatch | `1600, -550, -2850` | `4, 6, 0.5` · **Mobility: Movable** |

The hatch sits flush in the gap and reads as floor. The lever grinds it upward, opening the shaft
below it. It clears head height, so you walk under it and drop through.

## L5 — bottom · Echo 3, tool, end

| Actor | Location | Notes |
|---|---|---|
| `BP_AirPocket` | `1700, 0, -3500` | — |
| `BP_Echo` | `1000, 0, -3500` | **Echo ID** `Echo3` |
| `BP_ToolPickup` | `300, 0, -3500` | **Tool Name** `Pick` — end of level |

---

## World

| Actor | Location | Notes |
|---|---|---|
| `BP_KillVolume` | `1000, 0, -4200` | Scale `1,1,1` — safety net below L5, unreachable in normal play |

---

## Mechanic coverage

| Mechanic | Where |
|---|---|
| Move, jump | L0 |
| Breakable platform | L0 exit |
| Checkpoint / respawn | L1, L3, L4 |
| Oxygen + air pocket | all landings |
| Slide | L1 sliding floor |
| Ledge grab | fires naturally on every drop — tune after the first playtest |
| Interact + torch | L2 |
| Crystal → revealed platform | L2 |
| Echo collection | L2, L4, L5 |
| Attack + Denizen | L3 |
| Key → locked door | L3 |
| Lever + Timeline | L4 |
| Spike trap damage | L0, L4 |
| Tool pickup | L5 |

---

## Build order

1. The five Blueprint fixes in section 0.
2. The four structure cubes.
3. L0 completely, then play it — confirm the breakable drops you.
4. L1 slabs and contents, play it.
5. Repeat down. One landing, play it, then the next.

Grid snap **100** lets you drag most pieces into place instead of typing.
To place a landing quickly: build one, select its actors, **Alt+drag** the group down 700.

---

## Checks

- Every structure cube is `SM_Cube`, **never** `/Engine/BasicShapes/Cube`. If you float above a
  floor or stop short of a wall by roughly 100 units, you grabbed the wrong cube.
- All three Echo IDs differ — `Echo1`, `Echo2`, `Echo3`.
- Every Blueprint actor placed at Scale `1, 1, 1`.
- `BP_SlidingFloor`'s StaticMesh stays `BlockAll` or you fall through it.
- The L4 hatch needs **Mobility: Movable**.
- Props (`BP_Torch`, `BP_Crystal`, `BP_ToolPickup`) may float or sink slightly — nudge by eye.
