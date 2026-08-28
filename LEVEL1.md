# Level 1 — Surface Ruins, build sheet

One enclosed vertical shaft. Six landings, alternating left and right, 700 units apart.
You run to the open end, drop, and repeat. Geometry enforces "no backtracking upward" — you
physically cannot get back up.

- Movement axis is **X**. The play plane is **Y = 0**.
- All rotations `0,0,0` unless noted.
- `SM_Cube` is 100×100×100, so **Scale 16 = 1600 units long**.
- Slab pivots are centred: a floor at `Z = 0` with Scale Z `0.5` has its **walking surface at Z = 25**.
- Material on structure: `MI_PrototypeGrid_Gray`.
- **Every Blueprint actor is placed at Scale `1, 1, 1`.** Never scale a Blueprint. Their size lives
  inside the Blueprint, and scaling the actor multiplies on top of it. Scale only applies to raw `SM_Cube`.
- Blueprint origins sit **on the walking surface**, so a Blueprint's Z is always the landing's top Z.

Shaft interior is X `0`–`2000`, Z `+700` down to `-3600`. Back walls at **Y = -325**.

> **Where I am up to:** Blueprint fixes below not started. Nothing placed yet.

---

## 0. Blueprint fixes — do these before placing anything

Each Blueprint currently has its size and offsets baked into its components from being dragged in the
viewport rather than typed. Six of them break placement or collision. Fix these once, then every
placement below is a clean Scale `1,1,1`.

| Blueprint | Component | Set Location | Set Scale / Extent |
|---|---|---|---|
| `BP_SlidingFloor` | `StaticMesh` | `0, 0, -25` | Scale `8, 6, 0.5` |
| | `Box` | `0, 0, 50` | Box Extent `400, 300, 100` |
| `BP_BreakablePlatform` | `StaticMesh` | `0, 0, -25` | Scale `4, 6, 0.5` |
| | `Box` | `0, 0, 50` | Box Extent `200, 300, 100` |
| `BP_SpikeTrap` | `StaticMesh` | `0, 0, 50` | Scale `1, 1, 1` |
| | `Box` | `0, 0, 100` | Box Extent `100, 150, 100` |
| `BP_AirPocket` | `Box` | `0, 0, 150` | Box Extent `150, 150, 150` |
| `BP_Lever` | `Handle` | `0, 0, 111` | — (zeroes the X/Y drift off the play plane) |
| `BP_LockedDoor` | `door` | `0, 0, 150` | Scale `2, 6, 3` |

Why these six and not the rest: the others (`BP_Checkpoint` marker scale `0.4475`, `BP_Key` scale
`6.667`, `BP_Crystal` scale `3.333`, `BP_Echo` scale `5`) have ugly numbers but nothing that affects
collision or placement. Leave them.

The one that matters most is `BP_SpikeTrap` — its trigger box is currently **29 units wide**, so at
600 u/s you run straight through it without taking damage.

---

## Shaft structure

Place these four first. They are the whole enclosure.

| Actor | Location | Scale |
|---|---|---|
| `SM_Cube` — left wall | `-25, 0, -1450` | `0.5, 6, 43` |
| `SM_Cube` — right wall | `2025, 0, -1450` | `0.5, 6, 43` |
| `SM_Cube` — back wall | `1000, -325, -1450` | `20, 0.5, 43` |
| `SM_Cube` — ceiling | `1000, 0, 675` | `20, 6, 0.5` |

---

## Landings

Each landing is one `SM_Cube` at Scale `16, 6, 0.5` unless noted. The 400-wide gap alternates sides —
that gap is the way down.

| # | Location | Spans X | Walking surface | Gap |
|---|---|---|---|---|
| L0 | `800, 0, 0` | 0 → 1600 | `25` | X 1600–2000, right |
| L1 | see below — split for the sliding floor | 400 → 2000 | `-675` | X 0–400, left |
| L2 | `800, 0, -1400` | 0 → 1600 | `-1375` | X 1600–2000, right |
| L3 | `1200, 0, -2100` | 400 → 2000 | `-2075` | X 0–400, left |
| L4 | `800, 0, -2800` | 0 → 1600 | `-2775` | X 1600–2000, right |
| L5 | `1000, 0, -3500` · Scale `20, 6, 0.5` | 0 → 2000 | `-3475` | none — bottom |

L1 is two slabs with an 800-wide hole for the sliding floor:

| Actor | Location | Scale |
|---|---|---|
| `SM_Cube` — L1 floor A | `1700, 0, -700` | `6, 6, 0.5` |
| `SM_Cube` — L1 floor B | `500, 0, -700` | `2, 6, 0.5` |

---

## L0 — Z 0, run right · move, jump, fall

| Actor | Location | Notes |
|---|---|---|
| `PlayerStart` | `200, 0, 120` | — |
| `BP_SpikeTrap` | `900, 0, 25` | jump it |
| `BP_BreakablePlatform` | `1800, 0, 25` | fills the gap X 1600–2000 — **this is the exit** |

It looks like solid floor. Step on it, it collapses, you drop to L1. No tutorial text.

## L1 — Z -700, run left · checkpoint, oxygen, slide

| Actor | Location | Notes |
|---|---|---|
| `BP_Checkpoint` | `1800, 0, -675` | you land next to it |
| `BP_AirPocket` | `1550, 0, -675` | — |
| `BP_SlidingFloor` | `1000, 0, -675` | fills X 600–1400 — cross it sliding or take 20 |

Nothing to jump to and no way back up, so the only way past is through. Walk off at X 400.

## L2 — Z -1400, run right · torch, crystal, Echo 1

| Actor | Location | Notes |
|---|---|---|
| `BP_AirPocket` | `300, 0, -1375` | — |
| `BP_Torch` | `600, 0, -1375` | — |
| `BP_Crystal` | `1000, 0, -1375` | **Linked Torch** = that torch |
| `SM_Cube` — hidden shelf | `1300, 0, -1290` | Scale `4, 6, 0.3` · Crystal's **Linked Platform** |
| `BP_Echo` | `1300, 0, -1275` | **Echo ID** `Echo1` |

Shelf surface is 100 above the floor, so it is a plain jump once it exists.

## L3 — Z -2100, run left · Denizen, key, locked door

| Actor | Location | Notes |
|---|---|---|
| `BP_Checkpoint` | `1800, 0, -2075` | — |
| `BP_AirPocket` | `1550, 0, -2075` | — |
| `BP_Denizen` | `1200, 0, -2075` | drops the key |
| `BP_LockedDoor` | `700, 0, -2075` | **Move Offset** `0, 0, 350` |

The door rises into the ceiling. 350 is the most it can travel before it clips L2's underside.

## L4 — Z -2800, run right · lever, Echo 2

| Actor | Location | Notes |
|---|---|---|
| `BP_Checkpoint` | `300, 0, -2775` | — |
| `BP_SpikeTrap` | `700, 0, -2775` | — |
| `BP_Echo` | `1000, 0, -2775` | **Echo ID** `Echo2` |
| `BP_Lever` | `1300, 0, -2775` | Target Actor = the hatch · **Move Offset** `0, 0, 500` |
| `SM_Cube` — hatch | `1800, 0, -2800` | Scale `4, 6, 0.5` · **Mobility: Movable** |

The hatch sits flush in the gap and reads as floor. The lever grinds it upward out of the way,
opening the shaft below it. It clears head height, so you walk under it and drop through.

## L5 — Z -3500, run left · Echo 3, tool, end

| Actor | Location | Notes |
|---|---|---|
| `BP_AirPocket` | `1700, 0, -3475` | — |
| `BP_Echo` | `1000, 0, -3475` | **Echo ID** `Echo3` |
| `BP_ToolPickup` | `300, 0, -3475` | **Tool Name** `Pick` — end of level |

---

## World

| Actor | Location | Notes |
|---|---|---|
| `BP_KillVolume` | `1000, 0, -4200` | Scale `1,1,1` — safety net below L5, unreachable in normal play |

---

## Mechanic coverage

Every system built in Blocks 2–9 gets a forced-use beat:

| Mechanic | Where |
|---|---|
| Move, jump | L0 |
| Breakable platform | L0 exit |
| Checkpoint / respawn | L1, L3, L4 |
| Oxygen + air pocket | all landings |
| Slide | L1 sliding floor |
| Ledge grab | fires naturally on every drop — tune it after the first playtest |
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

1. Do the six Blueprint fixes in section 0.
2. Place the four structure cubes.
3. Place L0 completely, play it, confirm the drop to nothing works.
4. Place the L1 slabs and contents, play it.
5. Repeat down. Build one landing, play it, then build the next.

Grid snap set to **100** lets you drag most of these to position instead of typing six fields.
To place a landing quickly: build one, select its actors, then **Alt+drag** the group down 700.

---

## Checks

- All three Echo IDs must differ — `Echo1`, `Echo2`, `Echo3`.
- Every Blueprint actor placed at Scale `1, 1, 1`.
- `BP_SlidingFloor`'s StaticMesh must be `BlockAll` or you fall through it.
- The L4 hatch needs **Mobility: Movable** or it won't move.
- Delete `BP_TestInteract` from the level.
