# URFS Web

A single-file web parity build of the URFS Android app — workaround
for iOS testers (no Mac → no native iOS build).

## What it is

Open `index.html` in any modern browser. Everything lives in one
file: HTML + CSS + JS. Data persists locally per device via
IndexedDB. No build step, no toolchain, no server.

## What it has (v0.0.1)

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
- **Component damage map** — 60dp tappable tiles color-coded
  green/amber/red by HP ratio, DESTROYED tiles get a red border
- **Component sheet** — armor ±1 / ±5 / repair / UNDO·RESTORE on
  destroyed components, module toggles, equipment armor with
  DAMAGE / REPAIR controls

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

There's no export-to-JSON button yet (v0.0.1). The shapes match
the Android `RobotEntity` / `ComponentEntity` / `ModuleInstance` /
`BattleState` 1:1, so a future export-as-JSON button on this side
plus an import-from-JSON button on the Android side completes the
round-trip. Until then, this is a self-contained tester app.

## Known gaps vs Android

- **No 2D / 3D schematic renderer.** The battle screen uses a
  simpler facing-clustered tile grid for damage tracking. Adding
  the schematic later means porting `SchematicRenderer.kt` →
  Canvas2D or SVG.
- **No multi-pilot cockpit assignment at deploy.** Pilots aren't
  bound to specific cockpit slots yet — when a robot has cockpits,
  the battle state's `cockpitPilots` array starts empty. The
  action-resolve pilot picker hides itself until that's wired.
- **No rules engine** for damage calc, frame saves, Salvo Mental
  checks, etc. — players resolve at the table, then bump strain /
  HP / shields using the direct-edit dialogs. This matches how
  many groups already play.
- **Force is single-sided.** P1 only — P2 deploys would be another
  feature.
- **No image upload for robot photos** — the field exists in the
  data model (`imageData`) but the UI doesn't expose a picker yet.

## File layout

Just `index.html`. ~62KB. No build step, no dependencies.

## Version

v0.0.1 — 2026-06-04

URFS rulebook source of truth: `../android/app/src/main/assets/rulebook.md`
