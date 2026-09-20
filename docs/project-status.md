# Aging Parent Support / Mom Dashboard — Project Status

_Last updated: September 19, 2026_

## Status: Prototype 3 Working — Proactive Support, Visual Inventory, and Motion Automation Proven

The project has expanded beyond the original Roku proof of concept. The current system demonstrates entertainment, information, family connection, physical-environment control, proactive printed support, visual household inventory, and motion-triggered lighting.

The working pattern remains: **prove the smallest version -> observe whether it is useful -> measure whether it removes work -> automate maintenance only after the behavior is proven.**

## Proven Capabilities

### Entertainment / Information Dashboard — PASS

Current tested dashboard functions include The Lone Ranger, Johnny Carson, The Rifleman, WeatherNation, ABC News Live, KETV 7, Omaha/Yardley/Nashville weather cards, Family Updates, Living Room Lights ON/OFF, and dynamic greeting/date.

### Roku Long-Standby Reliability — PASS

With **Fast TV Start OFF**, a Roku TV left off for an extended period became unreachable by ping and ECP. With **Fast TV Start ON**, the .210 test TV passed an approximately five-hour off/standby wake test.

**Requirement:** Fast TV Start = ON for reliable long-standby network wake/control.

ECP text injection leaves the Roku on-screen keyboard focus on **A**, unlike manual typing. Deterministic navigation sequences account for this.

### Govee Local Smart Lighting — PASS

Two Govee H6004 living-room bulbs are locally controllable through the Windows bridge:
- 192.168.0.154
- 192.168.0.228
- UDP port 4003
- LAN Control enabled on both

### Prototype 3 — PASS

Milestone: **Entertainment + Information + Family + Physical Environment.** All current dashboard buttons were tested successfully.

### MOM — TODAY V1 — UNATTENDED PRODUCTION PASS

The large-print one-page MOM — TODAY sheet includes date, weather area, reminders, family note, and Don't Forget section.

Scheduled Task: `Mom Today Daily Print`
Schedule: **8:30 AM every morning**, local Central time, StartWhenAvailable.

On **September 17**, the first real-world scheduled morning print completed successfully without intervention.

**Result: MOM — TODAY Prototype 1 — PASS.**

This is a proactive support capability: useful information appears without Mom or the caregiver initiating it.

## Visual Inventory

### Refrigerator Camera — Prototype 1 PASS

A wireless Blink camera worked inside the closed refrigerator, including live view and infrared/night view. Mom normally uses only the top shelf while the caregiver is away. Initial target items: Milk, Coke, Pizza box.

A staged item-removal comparison demonstrated useful visual change detection and shopping inference.

The refrigerator will be cleaned before fixed zones are established. Next planned step: label `MILK | COKE | PIZZA`, establish a clean baseline, then observe normal use.

### Basement Visual Inventory Prototype 2 — BASELINE ESTABLISHED

On **September 17**, household supplies were consolidated and organized into a dedicated Mom inventory zone visible from the existing basement Blink camera. Approximately 90% of the items Mom would normally go into the basement to retrieve are now in this area.

The physical environment was deliberately simplified:
- recurring Mom supplies were moved into predictable locations;
- monitored locations were labeled;
- paper products were separated into fixed zones;
- unlabeled areas, including the caregiver's bottom shelf, are outside the monitoring scope.

The permanent Blink camera can see both the paper-product area and the organized supply shelving. A nighttime infrared image also showed the monitored zones clearly enough for category/zone-level comparison.

**Baseline rule:** human-confirmed counts/context are ground truth; camera images are primarily used to detect meaningful change from that baseline rather than repeatedly infer exact hidden counts.

Starting human-confirmed inventory notes include:
- Toothpaste: 4 boxes — PLENTY
- Disinfectant spray: 4 cans — PLENTY
- Bleach: 1 large jug — OK
- Laundry soap: multiple containers — PLENTY
- Paper plates: large package — PLENTY
- Trash bags: multiple boxes — PLENTY
- Toilet-tablet cleaner: 1 multi-tablet box, expected to last at least one year — PLENTY
- Floor cleaner: 1 large bottle — OK
- Wood cleaner: 4 bottles — PLENTY
- Comet cleanser: 2 containers — PLENTY
- Glass cleaner: 5 bottles — PLENTY
- Toilet paper and paper towels: large reserves — PLENTY

Exact visual counts are not required for every item. Occluded inventory can make camera-only absolute counts unreliable.

**Result: Basement Visual Inventory Prototype 2 — BASELINE ESTABLISHED.**

### Visual Inventory Model

Use small explicit states rather than false precision:
- PLENTY
- OK
- LOW
- OUT
- CAN'T TELL

Inventory status should combine **quantity + expected consumption rate**, not package count alone. One box may represent a year of supply.

The emerging operational model is:

`Human establishes ground truth -> fixed physical zones -> repeatable Blink image -> AI detects meaningful change -> caregiver reviews exceptions`

## Shopping Assistant — Concept Advanced

Initial model:
- **BUY** — genuinely needed/depleted
- **REMIND** — liked but routinely forgotten
- **DON'T BUY** — already plentiful
- **DELIVERY CANDIDATE** — heavy/bulky items difficult to carry

Keep humans in the purchase loop while accuracy and usefulness are measured.

## Motion Lighting Prototype 1 — PASS

Full tested chain:

`Hallway motion -> Blink -> Alexa -> Govee lights ON -> timed wait -> lights OFF`

Potential aging-in-place application: illuminate a nighttime path before Mom reaches it. Because the bathroom switch controls both light and fan, a simple rechargeable/local motion-sensor light remains the likely deployment solution.

## Emergency & Medical Readiness

A V1 Emergency Information Sheet has been completed separately.

DNR / advance-care planning remains on the backlog. No formal DNR should be represented as existing until appropriate documentation/medical orders are completed.

Sensitive medical and personal details should not be stored in this public/source repository.

## Key Design Lessons

1. **Task elimination beats instruction.**
2. **Ambient assistance is promising.**
3. **Change the environment when useful.** Fixed zones and labels reduce the intelligence required from computer vision.
4. **Physical redesign should remain useful without AI.** The new Mom inventory zone also makes supplies easier for Mom to locate.
5. **Human ground truth can anchor AI monitoring.** Known counts and consumption context should override uncertain camera inference.
6. **Detect exceptions rather than micromanage inventory.**
7. **Prototype before infrastructure.**
8. **Keep humans in consequential loops.**
9. **Failed tests reveal requirements.**

## Current Dependencies / Risks

- Windows Acer bridge remains a prototype dependency.
- Roku deterministic navigation depends on Roku UI/search layouts.
- Fast TV Start must remain enabled.
- Govee LAN control depends on stable networking and LAN Control remaining enabled.
- Blink/Alexa routines introduce cloud/service dependencies.
- Visual inventory can be fooled by occlusion, misplaced products, lighting/camera changes, or supplies stored outside monitored zones. Use CAN'T TELL rather than guessing.

## Near-Term Backlog

- Clean refrigerator, add fixed shelf zones, and establish refrigerator baseline.
- Observe basement inventory under normal use rather than staged changes.
- DNR / advance-care planning.
- Explore shopping-assistant V1 around Mom's small recurring item set.
- Consider low-cost rechargeable motion light for bathroom deployment.
- Continue reliability soak tests rather than changing known-good systems unnecessarily.

## Milestone Log

**Prototype 1:** Roku ECP, Lone Ranger, Johnny Carson, Fire-tablet dashboard, local Windows bridge, end-to-end one-touch playback.

**September 15 — Reliability baseline:** automatic bridge startup, restart recovery, AC sleep/hibernate disabled, lid-close reliability, unattended operation.

**September 16 — Prototype 2/3 expansion:** local Govee control; Rifleman, WeatherNation, ABC News, KETV; all dashboard buttons tested successfully.

**September 16 — Roku reliability:** Fast TV Start A/B finding; five-hour standby wake test passed.

**September 16 — MOM — TODAY setup:** large-print design proven; unattended printing method proven; 8:30 AM daily task installed.

**September 16 — Visual Inventory Prototype 1:** Blink camera worked inside closed refrigerator; item-removal comparison demonstrated useful inventory inference.

**September 16 — Motion Lighting Prototype 1:** hallway Blink motion -> Alexa -> Govee ON -> timed OFF, full loop passed.

**September 17 — MOM — TODAY Prototype 1:** first real-world scheduled 8:30 AM production print completed unattended — PASS.

**September 17 — Basement Visual Inventory Prototype 2:** Mom inventory zone consolidated/labeled; human ground-truth baseline established; permanent Blink daytime/night-IR views demonstrated usable zone-level monitoring — BASELINE ESTABLISHED.

## Current Interpretation

The project is becoming an early **ambient aging-in-place support system**. A particularly strong finding is that AI performance can improve by redesigning the environment around the task: consolidate relevant supplies, create fixed zones, establish human ground truth once, and ask AI to detect exceptions rather than continuously reconstruct the entire household state.

The next phase should continue favoring small real-world experiments over broad platform building.

## September 19 Addendum — MOM — TODAY + Automated Visual Inventory

### Refrigerator Visual Inventory Prototype 2 — BASELINE ESTABLISHED

A cleaned refrigerator layout established fixed visual zones for the initial monitored set:
- Milk — PRESENT
- Coke — PRESENT (Dr Pepper is temporarily standing in for Coke during testing)
- Pizza — PRESENT

Eggs and other refrigerator contents are outside the monitoring scope. The permanent refrigerator Blink mount remains pending; a phone image is serving as the temporary baseline while the physical mount is completed.

### MOM — TODAY Prototype 2 — PASS

The accepted one-page MOM — TODAY layout was expanded with a complete, readable **WHAT YOU HAVE AT HOME** inventory section rather than exceptions only. This is intentionally Mom-facing: it provides a daily reference for what is already in the house and gives her useful material to read each morning.

The printed inventory includes the established basement household inventory plus refrigerator items (Milk, Coke, Pizza). The test print remained readable and fit on one page.

Design split:
- **Mom view:** full simple inventory.
- **Caregiver/admin view:** changes and exceptions only.

Future inventory-state changes should feed the same MOM — TODAY inventory automatically.

### Bathroom Lighting Prototype 2 — FUNCTIONAL / LATENCY LIMIT FOUND

A spare Govee H6004 was tested for bathroom use. Blink -> Alexa -> Govee control works, but observed response latency was at least about five seconds, which is too slow for a bathroom immediately adjacent to the bedroom.

Govee Auto Run supports device-status triggers and overnight validity windows, but physical wall-switch power restoration does not generate the same ON trigger as an app-issued ON command. A wall-switch test therefore did not trigger the automation, while app control did.

Result: technically functional, but not suitable for this deployment requirement without a simpler/faster local trigger.

### Basement Visual Inventory Prototype 3 — AUTOMATIC IMAGE ACQUISITION PASS

The Acer prototype host was equipped with Python 3.13 and BlinkPy. It successfully:
1. authenticated to Blink;
2. enumerated the household Blink cameras;
3. selected **Mom’s Basement**;
4. requested a fresh camera snapshot; and
5. saved the raw camera image locally as `basement-latest.jpg`.

This removes the manual step of opening the Blink app and sending a screenshot.

### Inventory Change Detector Prototype 1 — PASS

A controlled bleach-removal test was performed using two automatically retrieved Blink images:
- `basement-baseline.jpg` — bleach present;
- `basement-latest.jpg` — bleach removed.

A Python/Pillow script cropped the known bleach shelf zone and calculated an average grayscale pixel-difference score. The test produced a score of **10.57** against an initial experimental threshold of 10 and correctly returned:

`Bleach = OUT`

The threshold is **not yet calibrated**. The next test should take several unchanged snapshots with bleach present to measure normal variation from IR exposure, JPEG compression, and camera/image noise. The goal is to establish separation between normal image variation and a meaningful inventory change before connecting the detector to MOM — TODAY.

Current proven chain:

`Blink camera -> Acer requests snapshot -> raw image saved -> fixed-zone comparison -> inventory state`

Next integration target:

`inventory state -> MOM — TODAY data -> dashboard/8:30 AM print`

### New Design Finding

The successful visual-inventory path is becoming a configuration problem rather than a separate script per product. One inventory processor can hold fixed zones, baselines, thresholds, and state rules for multiple basement and refrigerator items, then output the household state consumed by MOM — TODAY.

The next calibration work should avoid unnecessary complexity: measure normal image noise first, then decide whether simple fixed-zone pixel comparison is robust enough or whether a smarter visual comparison is warranted.
