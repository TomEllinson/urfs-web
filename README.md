# URFS Web

A browser-based companion for the **URFS** mech wargame —
designed for iOS playtesters who can't get the Android app.

Open the link, add it to your home screen, and you've got a
single-file app that tracks your mech build, runs a battle for
you, and stays out of the way at the table.

## Open it

Just visit the URL in any modern browser. On iOS Safari:

1. Open the page
2. Tap the **Share** button
3. Tap **Add to Home Screen**

It now behaves like an app — dark cyan/amber UI, no browser
chrome, opens straight to the Garage.

## What's inside

**Garage** — Build robots from scratch: silhouette, frame stats,
add components and pick what facing they sit on, install modules
from the urfs-core pack, attach equipment. Tap a robot to edit.

**Equipment & Pilots** — Catalog your weapons and crew. Pilots
have Physical / Mental / Training stats and a row of talents
(Sharpshooter, Brawler, Deadeye, …) that wire into the dice
roller automatically when they're sitting in a cockpit.

**Forces** — Group robots into a roster. When you tap DEPLOY, the
app asks you to assign a pilot to each cockpit slot. After
deploy, you can also add an opponent force from the battle
overview (cyan = your side, red = theirs).

**Battle** — Round tracker, two-sided priority handoff, per-robot
screen with vitals, schematic, cockpit pilots, and an action
sheet covering every URFS action: Salvo with target picker,
Charge / Strafe / Move with distance pickers, Heat Vent with an
auto-rolled Mental check, Pivot with vector-lock for flying
robots, Grab / Break Free, and more. Each rolls or counts what
the rulebook calls for; you read the dice at the table.

**Damage pipeline** — Tap APPLY DAMAGE on a component, choose
type, and the app walks armor → frame save → destruction cascade
(child components, fuel-tank spillage, equipment drop). Misclick
recovery: every destroyed component has an UNDO · RESTORE
affordance.

**Dice roller** — Opens from any rollable action. Pool starts at
your base attack/skill dice; toggleable chips show every
applicable bonus: pilot talents, cockpit type, equipped
computers, Crouched, Brawler / Inertia Driver, etc. The roller
shows hits — you read damage at the table.

## Your data stays on your device

Everything lives in your browser's IndexedDB. Nothing leaves the
device. Each tester has their own independent build — there's no
shared backend, no account, no sync.

To back up: tap **EXPORT JSON** at the top of the Garage. You'll
get a file with everything (robots, pilots, equipment, forces,
battles). **IMPORT JSON** restores it on the same or another
device. Merge or replace is your choice.

## How to give feedback

If something looks wrong, a button doesn't fire, or a rule
doesn't match what we played at the table — text Tom directly with
a screenshot. Mention which version you're on (the version
string is in the page source at the top of `index.html`, currently
shown in the changelog).

## A few honest gaps

- **3D schematic is intentionally out of scope** — the 2D
  schematic is the canonical battle view.
- **No multiplayer sync** — two devices = two independent battle
  states. Future work.
- **Some rules still resolve at the table.** Per-target ranged
  attacks open the roller for you; you roll once per target and
  apply the damage manually. Parry stacks a counter for the
  attacker's threshold bump — you apply that bump when you read
  the attacker's roll. The app makes the bookkeeping painless,
  not invisible.

## Project layout

A single file: [`index.html`](./index.html). No build, no
dependencies. The full spec that drove the build is in
[`SPEC.json5`](./SPEC.json5), and the development changelog with
every shipped slice is in [`CHANGELOG.md`](./CHANGELOG.md).

URFS rulebook source of truth lives in the (private) Android
project. This webapp implements the same rules but is a
self-contained client-side build.
