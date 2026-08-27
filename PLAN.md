# The Heaven Below — Build Plan

Tracks the GDD (v1.1) against what is actually in the project.
One task = one commit. Commit message = task number + short name (e.g. `3.1 death restart`).

---

## Status vs GDD

| GDD | Feature | State |
|---|---|---|
| §4 | Enhanced Input, single IMC_Default | done |
| §4 | Move left/right (A/D) | done |
| §4 | Jump (Space, gamepad bottom face) | done |
| §4 | Slide (Left Ctrl) | done — `bIsSliding` set |
| §4 | Climb / ledge grab (W) | **not started** |
| §4 | Interact (E) | **not started** |
| §4 | Attack (LMB) | **not started** |
| §4 | Pause (Esc) | **not started** |
| §13 | Gamepad support | **out of scope** — keyboard & mouse only this build |
| §7 | Health + damage | done — `BPC_Health` |
| §7 | Spike trap | done, 1 placed |
| §7 | Breakable platform | done, 1 placed |
| §7 | Sliding floor | built, **not placed** |
| §7 | Locked door + key | **not started** |
| §7 | Torch / crystal light puzzle | **not started** |
| §7 | Oxygen meter | **not started** |
| §8 | Respawn at checkpoint | **not started** — `OnDeath` fires into nothing |
| §9 | HUD (health, oxygen, Echoes, tool, toast) | **not started** |
| §3 | Echoes / lore fragments | **not started** |
| §6.2 | Denizen (drops key) | **not started** |
| §6.3 | Boss | **not started** |
| §11 | Level 1 full layout | greybox started |
| §11 | First tool pickup at level end | **not started** |
| §14 | Chaos destruction on breakable platform | placeholder (hide + disable collision) |
| §14 | Niagara, per-biome PPV, World Partition streaming | **not started** |


---

## Block 2 — Hazards (finish off)

- [x] **2.5** Place `BP_SlidingFloor` in `L_SurfaceRuins`
- [x] **2.6** Lock the 2.5D framing — plane-constrain CharacterMovement, fix the CameraBoom side-on, remove `IA_Look` + `IMC_MouseLook`

## Block 3 — Death & respawn (GDD §8)

- [x] **3.1** Guard `OnDeath` so it fires once; bind it in `BP_Diver` → disable input → delay → reload level
- [x] **3.2** Kill volume under the level so falling off = death
- [x] **3.3** `BP_Checkpoint` actor (box overlap, one-shot, visual)
- [x] **3.4** `GI_HeavenBelow` Game Instance stores last checkpoint transform; respawn there instead of level start
- [x] **3.5** I-frames — make `bIsInvulnerable` actually block damage for 1s after a hit

## Block 4 — HUD skeleton (GDD §9)

- [x] **4.1** `WBP_HUD` widget; add to viewport from `BP_ThirdPersonPlayerController`
- [x] **4.2** Health bar bound to `OnHealthChanged`
- [x] **4.3** Checkpoint-reached toast

## Block 5 — Oxygen (GDD §7, §8)

- [x] **5.1** `BPC_Oxygen` component — variables, tick depletion, `OnOxygenChanged` dispatcher, added to `BP_Diver`
- [x] **5.2** Oxygen bar on HUD (largest / most prominent element)
- [x] **5.3** Zero oxygen → same death path as 3.1
- [x] **5.4** `BP_AirPocket` actor refills on overlap; checkpoints refill too

## Block 6 — Echoes (GDD §3, §9)

- [x] **6.1** `BP_Echo` actor — unique ID, records into `GI_HeavenBelow`, destroys itself, stays collected after respawn
- [x] **6.2** Echo counter on HUD

## Block 7 — Interact & light puzzles (GDD §7)

- [x] **7.1** `IA_Interact` (E) added to `IMC_Default`
- [x] **7.2** `BPI_Interactable` interface + `InteractRange` sphere on the Diver tracking `CurrentInteractable`; E calls `Interact`
- [x] **7.3** "Press E" prompt on the HUD
- [x] **7.4** `BP_Torch` — interact to light, casts a point light
- [x] **7.5** `BP_Crystal` — lit by a torch, reveals a hidden platform
- [x] **7.6** `BP_Lever` — interact to move a platform / open a gate

## Block 8 — Denizen, key, locked door (GDD §6.2, §7, §11)

- [x] **8.1** `IA_Attack` (LMB) + `AM_Attack` montage plays on the Diver
- [x] **8.2** `BP_Denizen` — `BPC_Health`, simple chase, damages on contact
- [x] **8.3** Attack damages the Denizen (sphere check on swing)
- [x] **8.4** Denizen drops `BP_Key` on death
- [x] **8.5** `BP_LockedDoor` — opens only if key held
- [x] **8.6** Equipped-tool indicator on HUD

## Block 9 — Climb / ledge grab (GDD §4, §14) — built, not used in Level 1

- [x] **9.1** `IA_Climb` (W) + forward/down traces that detect a ledge, with debug drawing
- [x] **9.2** Snap into a hang when a ledge is found while falling
- [x] **9.3** Mantle up on Jump; drop off on Slide
- [x] **9.4** Climbable tag so only marked geometry grabs

## Block 10 — Level 1 build-out (GDD §11)

Numeric build sheet: [LEVEL1.md](LEVEL1.md)

- [ ] **10.1** Block out Level 1: three enclosed 6000-unit corridors, switchbacking down. **Target: ~5 min of play**
- [ ] **10.2** Forced-teaching pass — spike spacing, breakable-platform exit, sliding floor unavoidable (§11: teach by forced use)
- [ ] **10.3** Denizen + key + locked door gate
- [ ] **10.4** First tool pickup at the level end
- [ ] **10.5** Checkpoint spacing pass + oxygen pacing pass
- [ ] **10.6** Reposition the Echoes so each needs a real detour off the critical path

## Block 11 — Presentation pass (GDD §9, §10, §14)

- [ ] **11.1** Pause menu (Esc) — resume, restart, quit
- [ ] **11.2** Swap breakable platform to Chaos destruction
- [ ] **11.3** Niagara — dust motes, hazard FX
- [ ] **11.7** Hit feedback — mesh flash / camera shake on damage
- [ ] **11.4** Surface Ruins post-process volume + Lumen mood pass
- [ ] **11.5** Royalty-free ambient audio + hazard SFX
- [ ] **11.6** Strip the mobile touch path — `BPI_TouchInterface` off `BP_Diver`, touch widget off the PlayerController (PC-only per §1)

## Block 12 — Ship (GDD §12)

- [ ] **12.1** Main menu
- [ ] **12.2** Balance pass on hazard timing and oxygen
- [ ] **12.3** Packaged Windows Development build
- [ ] **12.4** Packaged Shipping build

---

## Out of scope for this build (GDD §13)

Biomes 2–5, bosses, New Game+, speedrun mode, full gamepad certification.
