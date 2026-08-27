# HANDOFF — read this first

Context for a Claude Code session picking this project up on a different machine.

---

## 1. What this is

**The Heaven Below** — a 2.5D atmospheric exploration platformer in **Unreal Engine 5.6**.
Blueprint-only, no C++, no `Source/` directory. Built solo by the user for BA (Hons) Game Design, UCA.

Premise: humanity built towers to reach heaven, found nothing, so now descends instead.
The player is a **Diver** going down through ruins. Tone is INSIDE / Journey — sparse, silent,
environmental storytelling.

Repo: `heavenbelow`, branch `main`. Git LFS tracks all `.uasset` / `.umap`.

---

## 2. How to work with this user — important

They are a **beginner in Unreal** and have said explicitly:

> "i dont really want explanations i just want instructions"
> "okay 1 by 1, what do i do"

So:

- **One task per reply.** Wait for "works, next" before giving the next one.
- **Click-by-click.** Exact panel names, pin names, values. Not "wire up the health component".
- **No rationale unless asked.** They will ask if they want to know why.
- They send **screenshots of Blueprint graphs** to be checked. Read the wiring carefully, name the
  specific pins that are wrong, and confirm what is already correct so they know what to leave alone.
- When they say something does not make sense, **they are usually right** — two level-design
  suggestions were wrong and they caught both.

### Bugs that came up repeatedly — check these first

1. **Unconnected exec pins.** Killed a whole chain silently, twice. Most common fault by far.
2. **`Target` left on `self`** when it should be a cast result — caused the Denizen to damage itself
   and the Diver to punch itself.
3. **Hand-typed Custom Events** where a genuine interface event or component bound event is needed.
   A real one is titled `Event Interact (From BPI Interactable)` or `On Death (BPC_Health)`.
4. **Literal values typed into pins** that should be wired (a `NewTool` text field instead of the pin).
5. **Collision.** `OverlapAllDynamic` ignores static geometry. Meshes need **Generate Overlap Events**.
6. **Mobility: Static** actors cannot be moved at runtime — set **Movable**.
7. **Array pins** on function nodes cannot be edited inline; they need a `Make Array` node.

---

## 3. Docs in this repo

| File | What it is |
|---|---|
| `PLAN.md` | Master task list mapped to the GDD. Blocks 2–12. One task = one commit. Tick items as they land. |
| `LEVEL1.md` | Numeric build sheet for Level 1 — exact Location and Scale per actor. Has a "Where I am up to" marker. |
| `HANDOFF.md` | This file. |

Commit convention: `git commit -m "10.2 forced teaching pass"` — task number then short name.

The GDD itself (`THE_HEAVEN_BELOW_GDD_UE5.6 (1).docx`) is **not in the repo** — it lives in the user's
Downloads folder. Section 4 below summarises what matters.

---

## 4. GDD summary (the doc is not in the repo)

- **§3 Core loop:** Explore → Descend → Avoid Hazards → Discover Lore → Fight Bosses → Continue Descent.
- **§3.1 Biomes:** Surface Ruins → Forgotten Caves → Infernal Core → Abyssal Garden → The Heaven Below.
  Only Surface Ruins is being built.
- **§4 Controls:** A/D move, Space jump, LeftCtrl slide (hold), W climb, E interact, LMB attack, Esc pause.
  **Gamepad is out of scope** for this build (§13 lists it as a stretch goal) — the user confirmed this.
- **§7 Hazards:** breakable platform, spike trap, sliding floor, locked door + key from a Denizen,
  torch/crystal light puzzles, oxygen meter.
- **§8 Win/lose:** reach the bottom; die from damage or from oxygen hitting zero; respawn at last checkpoint.
- **§9 HUD:** health (small), **oxygen (most prominent — it is the core pacing mechanic)**, Echo counter,
  equipped tool, checkpoint toast. Sparse, UMG.
- **§11 Level 1:** teaches every mechanic by **forced use, not tutorial prompts**. Progress is downward,
  no backtracking upward. Ends with the player getting their first tool.
- **§13 Scope:** solo project, keep it tight. 2–3 art-passed biomes max. Blueprint-first.
- **§14 Tech:** Lumen, Nanite, Chaos destruction, Enhanced Input, UMG, World Partition.

---

## 5. What is built and working

### Player — `BP_Diver` (Character)

Quinn mesh + `ABP_Unarmed`. CameraBoom + FollowCamera locked side-on.
**CharacterMovement is plane-constrained with normal `0,1,0`** — the Diver moves along **X**,
the play plane is **Y = 0**.

Components: `BPC_Health`, `BPC_Oxygen`, `InteractRange` (Sphere, radius 200, `OverlapAll`).

Variables: `bIsSliding`, `bIsHanging`, `bGrabCooldown`, `LedgeLocation`, `CurrentInteractable`, `AttackDamage`.

Event Graph handles: BeginPlay checkpoint teleport, `IA_MoveSide`, `IA_Jump` (or mantle),
`IA_Slide` (or ledge drop-off), `IA_Interact`, `IA_Attack`, ledge-detect traces on Tick,
`InteractRange` begin/end overlap, and the `On Death` bound event.

### Components

- **`BPC_Health`** — `MaxHealth` 100, `CurrentHealth`, `bIsInvulnerable`, `bIsDead`.
  `ApplyDamage(Amount)` gates on invulnerability, clamps, fires `OnHealthChanged`, runs `StartIFrames`
  (1 second), then fires `OnDeath` once via the `bIsDead` guard.
- **`BPC_Oxygen`** — `MaxOxygen` 100, `CurrentOxygen`, `DepletionRate` 2.0, `bDrowned`.
  Drains on Tick, fires `OnOxygenChanged`, applies 9999 damage at zero. `RefillOxygen()` tops it up.

### `GI_HeavenBelow` (Game Instance — survives level reload)

`CheckpointLocation`, `bHasCheckpoint`, `CollectedEchoes` (Name array), `bHasKey`, `EquippedTool`.
Dispatchers: `OnEchoCollected`, `OnToolChanged`.
**Set as the project's Game Instance Class** in Project Settings → Maps & Modes.

### `WBP_HUD`

Widgets: `HealthBar`, `OxygenBar` (large, bottom-centre), `EchoCounter`, `ToolIndicator`, `CheckpointToast`.
Custom events: `UpdateHealthBar`, `UpdateOxygenBar`, `UpdateEchoCounter`, `UpdateToolIndicator`,
`ShowCheckpointToast`, `ShowMessage(Text)`.
Created by `BP_ThirdPersonPlayerController` on BeginPlay (off the Sequence node's Then 2) and stored
there as `HUDRef` — that is how other actors reach the HUD.

### Actors

| Blueprint | Behaviour |
|---|---|
| `BP_Checkpoint` | One-shot overlap. Lights up, toasts, stores location on the GI, refills oxygen. |
| `BP_KillVolume` | Big box, applies 9999 damage. Routes falls through the normal death path. |
| `BP_AirPocket` | Overlap → `RefillOxygen`. |
| `BP_Echo` | Unique `EchoID` (Name, Instance Editable). Records into `CollectedEchoes` so it stays collected after death. Falls back to the actor display name if the ID is blank. |
| `BP_SpikeTrap` | Overlap → 20 damage, cooldown. |
| `BP_BreakablePlatform` | Overlap → delay → hide + disable collision. |
| `BP_SlidingFloor` | Cross it sliding = safe, not sliding = 20 damage. StaticMesh is `BlockAll` so it is walkable. |
| `BP_Denizen` | Character. Chases on Tick within `ChaseRange`, damages within `ContactRange`, has `BPC_Health`, drops `BP_Key` and destroys itself on death. |
| `BP_Key` | Spins, arms after 0.7s, sets `bHasKey` on the GI. |
| `BP_ToolPickup` | Sets `EquippedTool` on the GI, fires `OnToolChanged`. |
| `BP_TestInteract` | **Scaffolding — delete from the level.** |

### Interactables — `BPI_Interactable`

Interface with one function, `Interact`. The Diver's `InteractRange` sphere finds any actor
implementing it, stores it as `CurrentInteractable`, shows the `Press E` prompt, and E sends
`Interact (Message)`.

- **`BP_Torch`** — toggles a point light, has an `OnLitChanged` dispatcher.
- **`BP_Crystal`** — binds to a linked torch, reveals/hides a `LinkedPlatform` actor.
- **`BP_Lever`** — Timeline moves a `TargetActor` by `MoveOffset` over 1.5s.
- **`BP_LockedDoor`** — checks `bHasKey`; opens via Timeline, or shows "Locked. Find the key." on the HUD.

### Input — `IMC_Default`

| Action | Key |
|---|---|
| `IA_MoveSide` | A / D (Negate on A) |
| `IA_Jump` | Space |
| `IA_Slide` | Left Ctrl (**no triggers on the asset** — uses Started/Completed) |
| `IA_Interact` | E |
| `IA_Attack` | Left Mouse Button |
| `IA_Climb` | W |

`IA_Look` was removed. `AM_Attack` montage plays via the `DefaultSlot` node added to `ABP_Unarmed`.

---

## 6. Where the work is right now

`PLAN.md` Blocks 2 through 9 are **done and ticked**.

**Block 10 (level build-out) is in progress.** From `LEVEL1.md`:

- Corridor 1 — **complete**
- Corridor 2 — **shell built**, contents not yet placed
- Corridor 3 — not started

**Next task:** corridor 2's Contents table in `LEVEL1.md`, starting with the checkpoint at `5600, 0, -625`.

Level 1 is three enclosed 6000-unit corridors switchbacking downward (run right, drop, run left,
drop, run right), targeting **~5 minutes of play**. Back walls sit at **Y = -325**.

The ledge grab (Block 9) is fully built but **deliberately not used in Level 1** — it fights the flat
corridor layout. Leave it alone.

Blocks 11 (pause menu, Chaos destruction, Niagara, lighting, audio) and 12 (menu, balance, packaging)
have not been started.

---

## 7. Setting up on a new machine

1. Install **Git LFS** before cloning, or the `.uasset` files come down as text pointers:
   `git lfs install`
2. `git clone` the repo, then `git lfs pull` if the assets look wrong.
3. Open `heavenbelow.uproject` with **UE 5.6**.
4. Startup map is `L_SurfaceRuins`.
5. Read `PLAN.md`, then `LEVEL1.md`, then carry on from section 6 above.
