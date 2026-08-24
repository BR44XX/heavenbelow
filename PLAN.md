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
| §4 | Gamepad: slide, interact, attack, pause | **not started** |
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
- [ ] **5.2** Oxygen bar on HUD (largest / most prominent element)
- [ ] **5.3** Zero oxygen → same death path as 3.1
- [ ] **5.4** `BP_AirPocket` actor refills on overlap; checkpoints refill too

## Block 6 — Echoes (GDD §3, §9)

- [ ] **6.1** `BP_Echo` actor — overlap, pickup FX, destroy
- [ ] **6.2** Echo count stored on `GI_HeavenBelow`
- [ ] **6.3** Echo counter on HUD
- [ ] **6.4** Place 3 Echoes off the critical path in Level 1

## Block 7 — Interact & light puzzles (GDD §7)

- [ ] **7.1** `IA_Interact` (E / gamepad X) added to `IMC_Default`
- [ ] **7.2** `BPI_Interactable` Blueprint Interface; trace from Diver, show prompt
- [ ] **7.3** `BP_Torch` — interact to light, casts a point light
- [ ] **7.4** `BP_Crystal` — lit by a torch, reveals a hidden platform
- [ ] **7.5** `BP_Lever` — interact to move a platform / open a gate

## Block 8 — Denizen, key, locked door (GDD §6.2, §7, §11)

- [ ] **8.1** `IA_Attack` (LMB / right trigger); melee montage on `BP_Diver`
- [ ] **8.2** `BP_Denizen` — `BPC_Health`, simple chase, damages on contact
- [ ] **8.3** Denizen drops `BP_Key` on death
- [ ] **8.4** `BP_LockedDoor` — opens only if key held
- [ ] **8.5** Equipped-tool indicator on HUD

## Block 9 — Climb / ledge grab (GDD §4, §14)

- [ ] **9.1** `IA_Climb` (W / stick up)
- [ ] **9.2** Forward + downward traces to detect a ledge
- [ ] **9.3** Hang state, then mantle up
- [ ] **9.4** Climbable surface tag/channel so only marked geometry works

## Block 10 — Level 1 build-out (GDD §11)

- [ ] **10.1** Block out the full Level 1 layout (Fig. F): multiple vertical drops, no upward backtracking
- [ ] **10.2** Forced-teaching sequence — spike jump, breakable platform drop, and a low overhead barrier over the sliding floor so it cannot be walked past (§11: teach by forced use)
- [ ] **10.3** Denizen + key + locked door gate
- [ ] **10.4** First tool pickup at the level end
- [ ] **10.5** Checkpoint spacing pass + oxygen pacing pass

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
