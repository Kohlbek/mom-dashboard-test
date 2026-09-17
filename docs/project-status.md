# Aging Parent Support / Mom Dashboard — Project Status

_Last updated: September 16, 2026_

## Status: Prototype 3 Working — Proactive Support, Visual Inventory, and Motion Automation Proven

The project has expanded well beyond the original Roku proof of concept. The current system demonstrates entertainment, information, family connection, physical-environment control, proactive printed support, visual household inventory, and motion-triggered lighting.

The working pattern remains: **prove the smallest version -> observe whether it is useful -> measure whether it removes work -> automate maintenance only after the behavior is proven.**

## Proven Capabilities

### Entertainment / Information Dashboard — PASS

Current tested dashboard functions include:

- The Lone Ranger
- Johnny Carson
- The Rifleman
- WeatherNation
- ABC News Live
- KETV 7
- Omaha/Yardley/Nashville weather cards
- Family Updates
- Living Room Lights ON/OFF
- Dynamic greeting/date

The Roku sequences have been tested from TV off through playback.

### Roku Long-Standby Reliability Finding — PASS

A significant A/B reliability issue was isolated on September 16.

With **Fast TV Start OFF**, a Roku TV left off for an extended period became unreachable by ping and ECP. Manually turning it on restored network control immediately.

After **Fast TV Start ON** was enabled, the `.210` test TV was left off for approximately five hours and then successfully controlled through the dashboard.

**Conclusion:** Fast TV Start = ON is a system requirement for reliable long-standby network wake/control. Preserve the 15-minute power-reduction setting unless later testing shows a conflict.

### Roku Search Navigation Finding

ECP text injection leaves the Roku on-screen keyboard focus on **A**, unlike manual typing, which moves focus as characters are entered. This explains earlier navigation-count discrepancies and is now part of the deterministic sequence design.

### Govee Local Smart Lighting — PASS

Two Govee H6004 living-room bulbs are installed and locally controllable through the Windows bridge.

- `192.168.0.154`
- `192.168.0.228`
- UDP port 4003
- LAN Control enabled on both bulbs

The Acer can remain on 5 GHz while bulbs use 2.4 GHz. Local ON/OFF does not require the Govee cloud API.

### Prototype 3 — PASS

Prototype 3 demonstrates that the dashboard controls multiple distinct household systems, not just Roku.

**Milestone name:** Entertainment + Information + Family + Physical Environment.

All current dashboard buttons were tested successfully.

### MOM — TODAY V1 — OPERATIONAL

The first proactive daily-assistant capability is installed.

A large-print one-page `MOM — TODAY` sheet was physically printed and the final print design was judged successful. It uses large high-contrast text and includes date, weather area, reminders, family note, and a Don't Forget section.

Unattended Edge printing was proven using a separate Edge user-data profile, kiosk printing, and `window.print()`.

Scheduled Task: `Mom Today Daily Print`

Schedule: **8:30 AM every morning**, local Central time, with StartWhenAvailable.

This marks an important transition from a system Mom actively operates to a system that can proactively provide useful information.

## September 16 Visual Inventory Experiment

### Household Inventory Baseline

Basement photos demonstrated very high reserves of paper towels and toilet paper plus other household supplies. Additional toilet-paper stock was identified in the bathroom and hallway closet, revealing an important constraint: **household inventory is distributed across multiple locations**.

Future inventory recommendations should consider total household stock rather than assuming one shelf or room represents the entire supply.

### Refrigerator Camera — PASS

A wireless Blink camera was temporarily placed on the refrigerator's top shelf.

Proven:

- Camera remained connected with refrigerator door closed.
- Thumbnail updated.
- Live view worked with door closed.
- Infrared/night view produced a usable image.
- Mom normally uses only the top shelf when the caregiver is away, simplifying the problem.

Mom's recurring top-shelf items are currently:

- Milk
- Coke
- Pizza box

The planned physical design is to label fixed shelf zones (`MILK | COKE | PIZZA`) so products return to predictable positions.

A two-photo test with one item removed demonstrated useful visual change detection and shopping-list inference.

**Result: Visual Inventory Prototype 1 — PASS.**

### Visual Inventory Design Model

Use small, explicit status categories rather than pretend to know exact counts:

- PLENTY
- OK
- LOW
- OUT
- CAN'T TELL

For fixed refrigerator zones, `PRESENT / LOW / OUT` may be enough.

This experiment supports an important project principle: **AI reliability can sometimes be improved more effectively by changing the physical environment than by making the AI more sophisticated.** Shelf labels and predictable placement make the vision problem simpler and more auditable.

## Shopping Assistant — Concept Advanced

Mom currently uses a paper shopping list, but requiring her to photograph it or maintain a new digital list would create unwanted work, especially while the caregiver is away for much of the year.

Because her shopping pattern is highly repetitive, the emerging system can combine visual inventory with known recurring preferences.

Initial model:

- **BUY** — genuinely needed/depleted item
- **REMIND** — item Mom likes but often forgets, e.g. bananas
- **DON'T BUY** — household already has plenty, especially paper towels/toilet paper
- **DELIVERY CANDIDATE** — heavy/bulky items that are increasingly difficult for Mom to carry

Longer-term path:

`Visual inventory -> suggested list -> store-arrival reminder -> delivery/cart preparation -> Mom/Dan approval -> purchase`

Keep humans in the purchase loop while accuracy and usefulness are being measured.

Regular stores identified for future arrival-reminder testing:

- Supermercado Nuestra Familia, Harrison Street area
- Fareway Meat & Grocery, Papillion area

Geofencing is not required for V1. First prove the list/recommendation behavior; automate the trigger later.

## September 16 Motion Lighting Experiment

A hallway Blink camera was configured as an Alexa Routine motion trigger. The routine controlled the existing Govee living-room lights.

Full chain tested:

`Hallway motion -> Blink -> Alexa -> Govee lights ON -> timed wait -> lights OFF`

Both automatic ON and automatic timed OFF worked.

**Result: Motion Lighting Prototype 1 — PASS.**

Potential aging-in-place application: illuminate a path/bathroom before Mom reaches it at night.

The existing bathroom switch controls both the bathroom light and fan, so an always-powered smart bulb is not appropriate there. A low-cost rechargeable/local motion-sensor light is likely the simplest deployment solution. The more complex Blink/Alexa/Govee automation chain has already been proven with the living-room lights.

## Emergency & Medical Readiness

Milestone 5 has begun. A V1 Emergency Information Sheet has been completed separately.

A DNR / advance-care-planning discussion is on this week's backlog. Mom has stated a preference for DNR, but no formal DNR should be represented as existing until appropriate documentation/medical orders are completed.

Sensitive medical and personal details should not be stored in this public/source repository.

## Key Design Lessons

1. **Task elimination beats instruction.** Reduce the number of steps Mom must perform rather than teaching a complex existing process.
2. **Ambient assistance is promising.** Refrigerator inventory and motion lighting can provide help without requiring Mom to operate an AI interface.
3. **Change the environment when useful.** Shelf labels/fixed zones can make simple vision more reliable than a more complex recognition system.
4. **Detect exceptions.** The system should surface `LOW`, `OUT`, unexpected bills, etc., rather than require constant caregiver inspection.
5. **Prototype before infrastructure.** Phone/manual photos can validate vision before automating Blink acquisition; existing lights can validate motion logic before buying bathroom hardware.
6. **Keep humans in consequential loops.** Shopping orders and similar actions should remain approval-based during experimentation.
7. **Failed tests reveal requirements.** The bathroom fan/light switch constraint and Roku Fast TV Start behavior both improved the design without needing elaborate workarounds.

## Current Dependencies / Risks

- Windows Acer bridge remains a prototype dependency.
- Roku deterministic navigation depends on Roku UI/search layouts.
- Fast TV Start must remain enabled on controlled Roku TVs.
- Govee local LAN control depends on stable local networking and LAN Control remaining enabled.
- Blink/Alexa motion routines introduce cloud/service dependencies; simple local motion lights may be preferable for safety-critical nighttime illumination.
- Visual inventory can be fooled by occlusion, misplaced products, lighting/camera changes, or inventory stored elsewhere; use `CAN'T TELL` and household-zone logic instead of guessing.

## Near-Term Backlog

- Observe first unattended 8:30 AM `MOM — TODAY` production print.
- DNR / advance-care planning during the week.
- Add simple refrigerator shelf labels and observe normal use.
- Continue visual-inventory testing under normal household conditions rather than staged conditions.
- Explore shopping-assistant V1 around Mom's small recurring item set.
- Consider low-cost rechargeable motion light for bathroom deployment.
- Separate Family Updates content from bridge code when maintenance friction warrants it.
- Continue reliability soak tests rather than changing known-good systems unnecessarily.

## Milestone Log

**Prototype 1:** Roku ECP, Lone Ranger, Johnny Carson, Fire-tablet dashboard, local Windows bridge, end-to-end one-touch playback.

**September 15 — Reliability baseline:** automatic bridge startup, restart recovery, AC sleep/hibernate disabled, lid-close reliability, unattended operation.

**September 15 — Dashboard expansion:** greeting/date, weather, Family Updates, first Danny update.

**September 16 — Prototype 2/3 expansion:** local Govee control; Rifleman, WeatherNation, ABC News, KETV; all dashboard buttons tested successfully.

**September 16 — Roku reliability:** Fast TV Start A/B finding; five-hour off/standby wake test passed with Fast TV Start enabled.

**September 16 — MOM — TODAY:** large-print design proven; unattended printing method proven; 8:30 AM daily scheduled task installed.

**September 16 — Visual Inventory Prototype 1:** Blink camera worked inside closed refrigerator; small known-item refrigerator model established; item-removal comparison demonstrated useful inventory inference.

**September 16 — Motion Lighting Prototype 1:** hallway Blink motion -> Alexa -> Govee ON -> timed OFF, full loop passed.

## Current Interpretation

The project has moved from a one-touch TV controller into an early **ambient aging-in-place support system**. The strongest new direction is not asking Mom to interact with more AI. It is using simple interfaces, environmental cues, sensors, cameras, automation, and AI interpretation so useful support appears when needed with minimal additional effort from her.

The next phase should continue favoring small real-world experiments over broad platform building.