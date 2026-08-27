# Level 1 — Surface Ruins, build sheet

Three enclosed corridors stacked vertically, switchbacking. Run right, drop, run left, drop, run right.

- Movement axis is **X**. The play plane is **Y = 0**.
- All rotations `0,0,0` unless noted.
- Assumes `SM_Cube` is 100×100×100, so **Scale 60 = 6000 units long**.
- Slab pivots are centred: a floor at `Z = 0` with Scale Z `0.5` has its **walking surface at Z = 25**.
- Material on structure: `MI_PrototypeGrid_Gray`.

**Back walls sit at Y = -325**, behind the player from the camera's point of view. Depth does not
affect gameplay — you are locked to Y = 0.

Build one corridor, play it, then build the next.

> **Where I am up to:** Corridor 1 complete. Corridor 2 shell built — next up is its **Contents** table,
> starting with the checkpoint at `5600, 0, -625`.

---

## Corridor 1 — Z 0, run right (X 0 → 6000)

### Structure

| Actor | Location | Scale |
|---|---|---|
| `SM_Cube` — floor | `2800, 0, 0` | `56, 6, 0.5` |
| `SM_Cube` — back wall | `3000, -325, 275` | `60, 0.5, 5` |
| `SM_Cube` — ceiling | `3000, 0, 550` | `60, 6, 0.5` |
| `SM_Cube` — left end wall | `0, 0, 275` | `0.5, 6, 5` |

Floor spans X `0`–`5600`. Interior is 500 units tall. The right end is open — that's the exit.

### Contents

| Actor | Location | Scale / Notes |
|---|---|---|
| `PlayerStart` | `300, 0, 120` | — |
| `BP_SpikeTrap` | `1500, 0, 75` | jump it |
| `BP_Checkpoint` | `2000, 0, 75` | — |
| `BP_SpikeTrap` | `2600, 0, 75` | — |
| `SM_Cube` — Echo shelf | `3600, 0, 110` | `4, 4, 0.3` |
| `BP_Echo` | `3600, 0, 200` | **Echo ID** `Echo1` |
| `BP_SpikeTrap` | `4400, 0, 75` | — |
| `BP_BreakablePlatform` | `5800, 0, 0` | `4, 6, 0.5` — **this is the exit** |

The breakable platform is the last 400 units of floor. Step on it, it collapses,
you fall into corridor 2. That's the transition — no gap to build.

---

## Corridor 2 — Z -700, run left (X 6000 → 0)

### Structure

| Actor | Location | Scale |
|---|---|---|
| `SM_Cube` — floor A | `4800, 0, -700` | `24, 6, 0.5` |
| `SM_Cube` — floor B | `1400, 0, -700` | `28, 6, 0.5` |
| `SM_Cube` — back wall | `3000, -325, -425` | `60, 0.5, 5` |
| `SM_Cube` — ceiling | `2700, 0, -150` | `54, 6, 0.5` |
| `SM_Cube` — right end wall | `6000, 0, -425` | `0.5, 6, 5` |

Floor A spans X `3600`–`6000`, floor B spans X `0`–`2800`. The sliding floor fills the gap.
Ceiling stops at X `5400` so you can fall in from corridor 1. Left end is open — that's the exit.

### Contents

| Actor | Location | Scale / Notes |
|---|---|---|
| `BP_Checkpoint` | `5600, 0, -625` | — |
| `BP_AirPocket` | `5200, 0, -550` | — |
| `BP_SlidingFloor` | `3200, 0, -700` | `8, 6, 0.5` — fills X `2800`–`3600` |
| `SM_Cube` — Echo shelf | `2400, 0, -520` | `4, 4, 0.3` |
| `BP_Echo` | `2400, 0, -430` | **Echo ID** `Echo2` |
| `BP_Denizen` | `1800, 0, -600` | drops the key |
| `BP_LockedDoor` | `800, 0, -450` | `1, 6, 1.25` · **Move Offset** `0, 0, -500` |

The door sinks into the floor rather than rising, because the ceiling is in the way.
Kill the Denizen, take the key, open the door, walk off the left end.

---

## Corridor 3 — Z -1400, run right (X 0 → 6000)

### Structure

| Actor | Location | Scale |
|---|---|---|
| `SM_Cube` — floor A | `2400, 0, -1400` | `48, 6, 0.5` |
| `SM_Cube` — floor B | `5700, 0, -1400` | `6, 6, 0.5` |
| `SM_Cube` — back wall | `3000, -325, -1125` | `60, 0.5, 5` |
| `SM_Cube` — ceiling | `3300, 0, -850` | `54, 6, 0.5` |
| `SM_Cube` — left end wall | `0, 0, -1125` | `0.5, 6, 5` |

Floor A spans X `0`–`4800`, floor B spans X `5400`–`6000`. The lever bridge fills the gap.
Ceiling starts at X `600` so you can fall in from corridor 2.

### Contents

| Actor | Location | Scale / Notes |
|---|---|---|
| `BP_Checkpoint` | `400, 0, -1325` | — |
| `BP_SpikeTrap` | `1400, 0, -1325` | — |
| `BP_Torch` | `2000, 0, -1325` | — |
| `BP_Crystal` | `2400, 0, -1325` | Linked Torch = that torch |
| `SM_Cube` — hidden shelf | `2800, 0, -1150` | `4, 4, 0.3` · Crystal's **Linked Platform** |
| `BP_Echo` | `2800, 0, -1060` | **Echo ID** `Echo3` |
| `BP_AirPocket` | `3000, 0, -1250` | — |
| `BP_Denizen` | `3600, 0, -1300` | — |
| `BP_SpikeTrap` | `4200, 0, -1325` | — |
| `BP_Lever` | `4600, 0, -1325` | Target Actor = the bridge · **Move Offset** `0, 0, 500` |
| `SM_Cube` — bridge | `5100, 0, -1900` | `6, 6, 0.5` · **Mobility: Movable** |
| `BP_ToolPickup` | `5800, 0, -1300` | **Tool Name** `Pick` — end of level |

Light the torch to reveal the Echo3 shelf. Pull the lever to raise the bridge across the gap.

---

## World

| Actor | Location | Notes |
|---|---|---|
| `BP_KillVolume` | `3000, 0, -2600` | Box Extent `5000, 5000, 200` |

---

## Checks

- All three Echo IDs must differ.
- `BP_SlidingFloor`'s StaticMesh must be `BlockAll` or you fall through it.
- The lever bridge needs **Mobility: Movable** or it won't move.
- Delete `BP_TestInteract` from the level.
- The ledge grab is **not used in this level** — the Blueprint stays, it just isn't needed here.
