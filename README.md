# URFS Web

A single-file web parity build of the URFS Android app — workaround
for iOS testers (no Mac → no native iOS build).

## What it is

Open `index.html` in any modern browser. Everything lives in one
file: HTML + CSS + JS. Data persists locally per device via
IndexedDB. No build step, no toolchain, no server.

## What it has (v0.0.2)

- **Garage** — list, create, edit, delete robots
- **Robot editor** — name, silhouette, frame stats (FS / FD / FM),
  components by definition + facing + frame flag, modules per
  component (filtered by `definitionLimit` and `maxPerComponent`),
  derived BP / weight / power preview
- **Module catalog** — 29 modules from the urfs-core pack, full
  rule fields (weight, power, definitionLimit, fuelCapacity,
  shieldsMultiplier, armorMultiplier, boost, etc.)
- **Equipment** — list, builder with keyword toggles (15 keywords
  including Mount Point, Melee, Ranged, Burst, Penetrating,
  Tethered, Limited, Heavy, Energy-Reflective, Overcharged, ...)
- **Pilots** — list, builder with stats (Physical / Mental /
  Training), rank, AI flag, 11 talents to toggle
- **Forces** — list, builder with BP cap + robot picker, DEPLOY
  action that snapshots the force into a live battle
- **Battle overview** — YOUR FORCE robot cards with status / SIL /
  ARMOR / FUEL / SHLD / STR / round + priority tracker
- **Per-robot battle screen** — VitalsPanel with 8 tappable cells:
  - ARM / FUEL / SHLD / P TOK
  - STR / CAP / GSPD / BOOST
  - Each cell opens a direct-edit dialog (±1, ±5, spend cap, etc.)
- **Status picker** — STANDING / CROUCHED / FLYING / GRAPPLED
  (canonical mutually-exclusive)
- **Action sheet** — 28 actions across PRIMARY / REACTION /
  INCIDENTAL buckets
- **Action resolve modal** — every action shows brief + COST +
  expandable rules block, multi-pilot picker for routing strain,
  RESOLVE button that applies the correct strain/token bump per
  rulebook
- **Pilot Token badge** — shows cockpit count + warning glyph
  rather than misleading first-pilot number
- **Component damage map** — 2D schematic (BFS layout, facing
  offsets, mirror convention LEFT→+X, post-pass collision
  resolution) — same layout algorithm as the Android
  `SchematicRenderer.kt`. Tappable nodes color-coded green/amber/red
  by HP ratio, DESTROYED tiles get a red border. Renders in BOTH
  the garage robot editor AND the per-robot battle screen.
- **Component sheet** — armor ±1 / ±5 / repair / UNDO·RESTORE on
  destroyed components, module toggles, equipment armor with
  DAMAGE / REPAIR controls
- **Dice roller** — 🎲 ROLL button on every per-robot battle
  header AND an OPEN DICE ROLLER button inside each
  ActionResolveDialog (when the action has a roll kind). Roller
  ports the chip catalog from the Android `DiceTalentChips.kt`:
  motion / open-air cockpits, Sharpshooter, Deadeye, targeting /
  melee computers, crouched-on-grabbed, inertia driver, brawler.
  Pool ±, hits-on (4+/5+/6+), per-die hit highlighting + count.
- **Image picker** — PICK PHOTO on every robot. Reads via
  FileReader, center-crops to square, downscales to 512px max,
  encodes as JPEG (quality 0.82), persists as a data URL on
  `robot.imageData`. Fully local — no upload, no server. Works on
  iOS Safari.
- **Export / import JSON** — EXPORT JSON button writes a versioned
  blob (`schemaVersion: 1`) covering robots, pilots, equipment,
  forces, and battles. IMPORT JSON prompts merge-vs-replace and
  rebuilds the IndexedDB stores. Round-trip with future Android
  side once it adds the matching importer.

## How to use

### Open locally

```sh
open index.html
```

(macOS) or just double-click the file. Works file://, IndexedDB
included.

### Host on GitHub Pages

1. Push this directory to a GitHub repo.
2. Settings → Pages → Source: deploy from `main` branch → `/` root.
3. Wait ~30s, your URL is `https://<you>.github.io/<repo>/`.
4. Send the URL to iOS testers; they open in Safari, add to home
   screen for an app-like icon.

### Data export / migration

EXPORT JSON in the Garage top bar writes a `schemaVersion: 1` blob
covering robots, pilots, equipment, forces, and battles. IMPORT
JSON re-loads it — prompts merge-vs-replace. The shapes match the
Android `RobotEntity` / `ComponentEntity` / `ModuleInstance` /
`BattleState` 1:1, so once the Android side adds a matching
importer the round-trip closes.

## Known gaps vs Android

- **No 3D schematic renderer.** Tom marked this lowest-priority
  glitter. 2D schematic shipped in v0.0.2.
- **No multi-pilot cockpit assignment at deploy.** Pilots aren't
  bound to specific cockpit slots yet — when a robot has cockpits,
  the battle state's `cockpitPilots` array starts empty. The
  action-resolve pilot picker hides itself until that's wired. The
  dice roller's pilot-talent chips (Sharpshooter / Deadeye /
  Brawler) likewise stay hidden until pilots are bound.
- **No rules engine** for damage calc, frame saves, Salvo Mental
  checks, etc. — players resolve at the table, then bump strain /
  HP / shields using the direct-edit dialogs, and use the dice
  roller for the dice math. This matches how many groups already
  play.
- **Force is single-sided.** P1 only — P2 deploys would be another
  feature.

## File layout

Just `index.html`. ~135KB. No build step, no dependencies.

## Version

v0.0.29 — 2026-06-06

### Changelog

- **v0.0.29** (2026-06-06): Strafe composes from prior slices —
  Charge's distance picker (½GSPD spent) + Salvo's per-target
  saturation tokens. Three one-liner condition extensions; no new
  UI surfaces.
- **v0.0.28** (2026-06-06): Charge gains a distance picker. New
  CHARGE DISTANCE input in the ActionResolveDialog accepts 0 up to
  ½ Ground Speed minus what's already spent this round; on RESOLVE
  the chosen distance is clamped to the remaining budget and added
  to `r.gspdSpent`. Round advance resets the counter. Melee dice
  still roll via the existing OPEN DICE ROLLER button.
- **v0.0.27** (2026-06-06): Salvo gains a multi-target picker
  (stage 6 begins). ActionResolveDialog for SALVO shows every
  other battle robot as a tappable card; each pick increments a
  saturation token on that target. If targets > pilot Mental, an
  inline amber warning flags the extra Mental check.
- **v0.0.26** (2026-06-06): Heat Vent Mental check auto-resolves on
  RESOLVE. Picked pilot's mental d6 vs 5+; on hits ≥ 1, sysStrain
  drops by hits and shutdownPending clears if it falls below
  threshold. MANUAL bypasses the auto-roll. Closes stage 5 strain
  effects.
- **v0.0.25** (2026-06-06): Strain Clear now also clears the
  Shutdown Pending flag when sysStrain drops below threshold —
  closing the loop where you can use the incidental to escape the
  Shutdown gate without rolling the reboot test.
- **v0.0.24** (2026-06-06): Pilot Knockout. When a cockpit pilot's
  `pilotStrain` reaches `5 + training`, `isKO` flips true with a
  toast announcement. The PILOTS section dims the row to 0.5 and
  adds a red KO chip, and the action-resolve picker treats the slot
  as absent.
- **v0.0.23** (2026-06-06): Shutdown reboot test. Replaced the
  v0.0.22 placeholder with the real FD d6 vs 5+ dice roll. Success
  → sysStrain −3 + `shutdownPending` cleared (robot can act).
  Failure → `shutdown=true` + skip turn until round advance, which
  now clears the flag in both PASS double-pass and manual + ROUND
  paths.
- **v0.0.22** (2026-06-06): System Strain threshold gate (stage 5
  begins). When sysStrain hits the threshold, `r.shutdownPending`
  is flagged and the next action attempt is intercepted into a
  Shutdown overlay — except STRAIN_CLEAR / HEAT_VENT (escape
  hatches) and PASS. The actual FD d6 vs 5+ reboot roll ships next
  slice; for now the overlay is a banner + DISMISS button.
- **v0.0.21** (2026-06-06): UNDO·RESTORE for destroyed components
  (mistake recovery). Existing button on the comp sheet's destroyed
  overlay now calls `recomputeDerivedStats(r)` after clearing
  `isDestroyed` and refilling armor, so shields/cap/strain snap back
  the moment you un-destroy. Cascade collateral is NOT auto-rewound —
  the player re-restores each one individually if they want it. Closes
  stage 4 damage pipeline.
- **v0.0.20** (2026-06-06): Derived stats recompute after each
  destruction. `recomputeDerivedStats` re-derives shields max,
  capacitor pool, and strain threshold from the alive component set
  (destroyed modules no longer contribute) and clamps current values
  to the new max. Modules whose `requires` chain points at a now-
  destroyed module get toggled off (handles aux-reactor-style
  dependents). Wired from `destroyComponent`'s tail.
- **v0.0.19** (2026-06-05): Destruction cascade. Failed Frame Save
  destroys the component AND fires: (1) recursive child cascade via
  `parentComponentId` (Arm goes down → its Hand goes too), (2)
  silhouette spillage from fuel-tank-carrying components onto
  siblings + parent (direct to buffer, no nested save), (3)
  equipment[] drops into `r.droppedEquipment`. cascadeLog records
  every event.
- **v0.0.18** (2026-06-05): Frame Save dialog on breach. After
  armor absorption, breach > 0 pushes the modal: FD d6 vs (7-FM)+
  with per-die amber/muted chips and binary outcome — ✓ SAVED
  (component intact) or ✗ DESTROYED (`isDestroyed=true`, no HP loss
  per rulebook). SKIP button preserves at-table resolution. Cascade
  fires next slice.
- **v0.0.17** (2026-06-05): Armor now actually absorbs damage.
  `applyDamage` computes `absorbed = min(armor, amount)` and
  `breach = amount - absorbed`, mutates the component's armor down
  toward 0, logs the absorbed/breach pair on the robot. Frame Save
  on breach ships in the next slice.
- **v0.0.16** (2026-06-05): APPLY DAMAGE dialog (stage 4 begins).
  New button on the per-robot schematic toolbar opens a modal with
  component picker (alive only), amount input, and Physical/Energy
  type toggle. Submit calls `applyDamage(robotId, cid, amount, type)`
  — currently a stub that logs the call; the buffer→frame-save→
  cascade pipeline ships in the next slices.
- **v0.0.15** (2026-06-05): End-of-round refresh wired. Round
  advance (PASS double-pass OR manual + ROUND) now ticks every
  robot's capacitor by +1, clamped to its capacitorsMax (a new
  field stored at deploy). Manual + ROUND now also resets the
  budget counters / pass flags / priority to mirror the natural
  pass-priority round advance. Closes stage 3.
- **v0.0.14** (2026-06-05): Per-robot screen now shows an
  ACTIONS THIS ROUND chip row between PILOTS and the action sheet:
  P:n/1, R:n/2, I:n. Chip turns amber when its bucket is at cap.
- **v0.0.13** (2026-06-05): Spent actions now dim + disable in the
  action sheet, with a "PRIMARY SPENT" / "REACTION SPENT" reason
  chip. PASS stays available (it's how you end the round even with
  primary spent). Handler also guards against programmatic bypass.
- **v0.0.12** (2026-06-05): Per-robot action budget tracked. Each
  battle robot has `actionBudget: {primaryUsed, reactionsUsed,
  incidentalsUsed}`. RESOLVE increments the right counter by
  bucket; round advance (PASS double-pass or solo PASS) resets
  every robot back to 0/0/0. UI affordances next.
- **v0.0.11** (2026-06-05): Priority handoff state machine.
  PASS PRIORITY now reads the current holder, clears THAT side's
  pilot strain (was a long-standing bug — old code only ever
  cleared P1), marks that side passed, and flips priority. When
  both sides have passed, the round advances and flags reset. Per-
  side header chip shows "✓ PASSED" (muted) or "PRIORITY"
  (amber) so the table state is legible at a glance. Closes
  stage-2 two-sided combat.
- **v0.0.10** (2026-06-05): Battle overview now color-codes the two
  sides — YOUR FORCE header + robot cards get a cyan left-border
  accent, OPPONENT FORCE gets red. Tap-to-detail works on either
  side via `findBattleRobot`.
- **v0.0.9** (2026-06-05): P2 force can now join an existing battle.
  New "+ ADD OPPONENT FORCE" button on the battle overview opens a
  force picker (excluding the current P1 force AND any force that
  shares a robot with it — prevents lookup collisions). Reuses the
  pilot-assign modal for the P2 cockpits. Battle overview now
  shows both YOUR FORCE and OPPONENT FORCE sections with their
  robot cards. Round-trip preserves both sides.
- **v0.0.8** (2026-06-05): Battle state foundation for two-sided
  combat. New `findBattleRobot(b, rid)` helper searches BOTH
  `p1Robots[]` and `p2Robots[]`; 31 prior lookups refactored to
  use it. Legacy battles auto-migrate (`p2Robots: []` default on
  load + import). Battle overview now renders an OPPONENT FORCE
  section when p2 is populated; solo battles unchanged. Sets up
  the P2 deploy + priority handoff slices.
- **v0.0.7** (2026-06-05): Pilot talent chips wake up in the dice
  roller. With a Sharpshooter pilot bound to a cockpit and SALVO
  open, the "+3 SHARPSHOOTER" chip now appears and toggles in
  (base 4 + Sharpshooter 3 = 7 d6). Closes stage-1 cockpit
  binding.
- **v0.0.6** (2026-06-05): ActionResolveDialog now shows a pilot
  picker for SALVO / CHARGE / MELEE_ASSAULT / HEAT_VENT and routes
  +1 Pilot Strain to the chosen cockpit pilot on RESOLVE (HEAT_VENT
  is Free per rulebook — picker just identifies the rolling pilot).
  PASS now correctly clears every cockpit's `pilotStrain` per the
  rulebook. Composite-key picker identity survives reorders;
  MANUAL escape-hatch preserved.
- **v0.0.5** (2026-06-05): Cockpit assignments now visible on the
  per-robot battle screen. New PILOTS bucket renders between vitals
  and the action sheet, listing each cockpit's assigned pilot (or
  A.I. Core / unassigned). Round-trip through export/import
  preserves the assignments (already worked through the generic
  battles store; verified via Playwright wipe+restore).
- **v0.0.4** (2026-06-05): Pilot-assignment step inserted into the
  force deploy flow. Tapping DEPLOY on a force with cockpit-bearing
  mechs opens a per-slot picker modal (with double-booking
  prevention and an empty-pilots fast-path to the Pilots tab). AI
  Core slots auto-assign; all-AI forces skip the modal. Battle's
  `cockpitPilots[]` is populated from the assignments. Sets up the
  per-robot strain routing + dice-roller talent chips in upcoming
  slices.
- **v0.0.3** (2026-06-05): `robotCockpitSlots(robot)` adapter —
  enumerates every cockpit slot per robot, returning
  `{componentId, componentName, moduleIndex, moduleId, moduleName,
  slotIndex, slotName, isAi}` per slot. First slice of stage-1
  cockpit-pilot binding from the full-port spec; pure adapter, no
  UI yet.
- **v0.0.2** (2026-06-04): 2D schematic renderer (BFS + collision
  pass, mirror convention) in garage editor + per-robot battle.
  Wired-up dice roller with chip catalog ported from
  `DiceTalentChips.kt`. Local image picker (FileReader →
  center-crop → 512px JPEG). Export / import JSON
  (`schemaVersion: 1`). Inline SVG favicon.
- **v0.0.1** (2026-06-04): initial single-file parity build.

URFS rulebook source of truth: `../android/app/src/main/assets/rulebook.md`
