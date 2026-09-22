# Aging Parent Support / Mom Dashboard — Project Status

_Last updated: September 22, 2026_

## Status: Prototype 3 Working — Unified Inventory + Caregiver Operations Layer Proven

The project now has a working caregiver operations layer in addition to Mom-facing entertainment, information, lighting, and daily printing. On September 21 the basement inventory experiments moved from separate calibration scripts into a generalized inventory pipeline with a shared state file and an integrated Admin Panel.

The working pattern remains: **prove the smallest version -> observe whether it is useful -> measure whether it removes work -> automate maintenance only after the behavior is proven.**

## Proven Capabilities

### Entertainment / Information Dashboard — PASS

Current tested dashboard functions include The Lone Ranger, Johnny Carson, The Rifleman, WeatherNation, ABC News Live, KETV 7, Omaha/Yardley/Nashville weather cards, Family Updates, Living Room Lights ON/OFF, and dynamic greeting/date.

### Roku Long-Standby Reliability — PASS

With **Fast TV Start OFF**, a Roku TV left off for an extended period became unreachable by ping and ECP. With **Fast TV Start ON**, extended-standby network control worked.

**Requirement:** Fast TV Start = ON for reliable long-standby network wake/control.

Current TV mapping:
- Living Room TV: `192.168.0.87`
- Mom's Bedroom TV: `192.168.0.210`
- Roku ECP: port 8060

### Govee Local Smart Lighting — PASS

Two Govee H6004 living-room bulbs are locally controllable through the Windows bridge using local UDP on port 4003 with LAN Control enabled.

### MOM — TODAY Prototype 2 — UNATTENDED PRODUCTION PASS

Scheduled Task: `Mom Today Daily Print`  
Schedule: **8:30 AM every morning**, local Central time, StartWhenAvailable.

On **September 21**, the expanded Prototype 2 sheet completed a real-world scheduled unattended morning print. The complete household inventory remained readable and fit on one page.

**Result: PASS.**

## Visual Inventory — September 21 Calibration

### Bleach — CALIBRATED FOR PRESENT / ABSENT

A later occupancy-style calibration produced a much cleaner separation than the earlier whole-image difference score:
- three empty samples: approximately **0.00% slot changed**
- three present samples: approximately **14–28% slot changed**

This supports a simple present/absent model for bleach.

### Toothpaste — DATASET CAPTURED / MODEL LESSON

Controlled images were captured at 4, 3, 2, 1, and 0 boxes. Within-state readings were stable, but whole-zone occupancy did **not** reliably distinguish 1 box from 2 boxes.

Conclusion: toothpaste should not use a generic whole-zone percentage model. A future slot/position model or another product-specific representation is more appropriate.

### Trash Bags — ACTIVE + RESERVE MODEL PASS

Trash bags exposed an important behavioral requirement: Mom may move a reserve box into the active-use position, so the detector should model **how the product is used**, not the order in which calibration boxes were removed.

Two zones were established:
- active-use zone
- reserve-supply zone

Observed calibration:
- 3 boxes: active ~80.96%, reserve ~96.45%
- 2 boxes: active ~78.34%, reserve ~47.03%
- 1 box: new stable samples active ~75.8–76.0%, reserve ~0%
- 0 boxes: active ~0%, reserve ~0%

One earlier 1-box frame was a clear outlier, reinforcing the need for sanity/outlier handling.

Current state mapping:
- active + 2 reserves = PLENTY
- active + 1 reserve = PLENTY
- active + 0 reserves = LOW
- no active + no reserves = OUT

A fresh real-world Blink image with all three boxes restored correctly returned **Trash Bags = PLENTY**.

## Generalized Inventory Engine — PASS

A new configuration-driven pipeline is working:

```text
Blink snapshot
    |
    v
basement-latest.jpg
    |
    v
inventory-config.json
    |
    v
inventory-check.py
    |
    v
inventory-state.json
```

Current unified output:
- Bleach — OK
- Trash Bags — PLENTY
- Toothpaste — PLENTY (temporarily human-confirmed/manual until slot logic is configured)

The generalized engine correctly changed Trash Bags from LOW on a staged one-box image to PLENTY after a fresh Blink snapshot with all three boxes restored, with no detector code change.

**Generalized inventory pipeline — PASS.**

The shared `inventory-state.json` is now the intended single household inventory state source. Older experimental detectors should not overwrite it.

## Caregiver/Admin Panel v5 — PASS

Local interface:

`http://localhost:8081/`

### System Health

Dependency checks include:
- scheduled task
- print script
- Task Scheduler Operational log
- Python
- Blink script
- inventory script
- inventory folder

### MOM — TODAY Controls

- Run Scheduled Task Now
- Run Print Script Now
- Refresh Status

### Visual Inventory

The Admin Panel now reads `inventory-state.json` directly and displays current states plus the last inventory update.

**Refresh Inventory** performs the full sequence:
1. request fresh Blink snapshot;
2. run generalized inventory engine;
3. update shared state;
4. reload the Admin view.

September 21 end-to-end test:
- fresh Blink image: 23:19:06
- inventory state update: 23:19:07
- Bleach: OK
- Trash Bags: PLENTY
- Toothpaste: PLENTY

**One-button inventory refresh — PASS.**

Separate Blink Snapshot and Inventory Check controls remain available for troubleshooting.

### Roku Status — PASS

The Admin Panel checks actual Roku ECP reachability on port 8060 without sending control commands.

Test result:
- Living Room TV `192.168.0.87:8060` — ONLINE
- Bedroom TV `192.168.0.210:8060` — ONLINE

A Refresh Roku Status control updates the check and timestamp.

### Last Blink Image — PASS

The Admin Panel exposes the current `basement-latest.jpg`, its capture timestamp, and a link to open the latest image full-size.

### Recent Logs — PASS

The Admin Panel surfaces recent operational events from currently available system data, including:
- inventory state updates;
- Blink image captures;
- MOM — TODAY scheduled-task result.

This provides a compact caregiver view of what the system actually did and when.

## September 22 — Device Status Expansion

### Govee Status — PASS

Admin Panel now performs local, non-mutating Govee LAN status queries for four lights:
- Basement — `192.168.0.18`
- Living Room — `192.168.0.228`
- Living Room by the Stairs — `192.168.0.154`
- Hallway — `192.168.0.190`

All four reported ONLINE in the successful test, with power and brightness displayed.

### Mom's Phone Presence — PASS

Admin Panel now checks Mom's phone at `192.168.0.115` and reports **DETECTED / NOT DETECTED**. Initial test returned DETECTED.

This is treated as a network-presence clue, not proof of physical location and not a safety alarm.

### Local Device Registry — ADOPTED

As LAN-connected components increase, device addressing should move into one local configuration file, `mom-devices.json`, rather than remain duplicated across scripts. The registry contains friendly names, IP addresses, device types, and relevant ports. It is intended to become the authoritative local device map.

Known additional candidates:
- Chamberlain / MyQ-633 — `192.168.0.3`
- myQ front-of-door device — `192.168.0.172`; exact role still to be identified.

## Scripts / Source Workflow

Google Drive **Mom Dashboard / Scripts** remains the shared source-review location. The Acer continues executing controlled local/OneDrive Desktop copies.

Important current local components include:
- `mom-admin.ps1`
- `mom-today-print.ps1`
- `roku-bridge.ps1`
- `blink-test.py`
- `mom-inventory\inventory-config.json`
- `mom-inventory\inventory-check.py`
- `mom-inventory\inventory-state.json`

Design rule:

**Shared source/config may live in collaborative storage; live execution remains controlled locally. Secrets remain local.**

## Motion Lighting

**Motion Lighting Prototype 1 — PASS:** Hallway Blink motion -> Alexa -> Govee ON -> timed OFF.

Bathroom Govee testing works but cloud/motion latency remains too slow for the intended adjacent-bedroom use case.

## Key Design Lessons

1. **Task elimination beats instruction.**
2. **Ambient assistance is promising.**
3. **Change the environment when useful.**
4. **Human-confirmed ground truth anchors uncertain visual inference.**
5. **Different products may require different inventory models.**
6. **Model natural use behavior, not calibration order.**
7. **Quantity + consumption context matters more than raw package count.**
8. **One shared state source simplifies downstream interfaces.**
9. **Repeated diagnostics should become controls/status indicators.**
10. **Mom and caregiver need different information density.**
11. **Outliers should be treated as a system condition, not blindly converted into household state.**
12. **Development friction is itself a design signal.**

## Current Dependencies / Risks

- Windows Acer remains a prototype host.
- Roku deterministic navigation depends on Roku UI/search layouts.
- Fast TV Start must remain enabled.
- BlinkPy authentication persistence still needs to be engineered before claiming fully unattended long-term snapshot retrieval.
- Camera inference can be affected by IR exposure, framing, occlusion, JPEG compression, and item movement.
- Toothpaste remains manual until an appropriate slot/position detector is configured.
- Older experimental inventory scripts must not overwrite the generalized shared state file.

## Near-Term Backlog

- Make the generalized inventory engine the sole writer of `inventory-state.json`.
- Connect MOM — TODAY to the shared inventory state rather than hard-coded inventory values.
- Add the complete household inventory to the Mom Dashboard screen so screen and paper match.
- Add remaining household items only after selecting an appropriate product-specific model.
- Securely persist Blink authentication locally while preserving 2FA when Blink requires it.
- Continue refrigerator fixed-zone work and normal-use observation.
- Move existing scripts progressively to the shared local `mom-devices.json` device registry.
- Investigate Chamberlain/myQ read-only door-state integration.
- Prototype basement Blink motion -> basement Govee light.
- DNR / advance-care planning remains separate backlog work.

## Milestone Log

**Prototype 1:** Roku ECP, Lone Ranger, Johnny Carson, Fire-tablet dashboard, local Windows bridge, end-to-end one-touch playback.

**September 15:** automatic bridge startup, restart recovery, AC sleep/hibernate disabled, lid-close reliability.

**September 16:** local Govee control; additional Roku content; Fast TV Start reliability; MOM — TODAY unattended-printing method; refrigerator camera; motion-lighting loop.

**September 17:** first real-world scheduled MOM — TODAY print — PASS. Basement Mom inventory zone consolidated/labeled and human baseline established.

**September 19:** refrigerator Prototype 2 baseline; expanded MOM — TODAY inventory test print; automatic Blink image acquisition; first bleach removal experiment.

**September 20:** Admin Panel Prototype working. Print failure traced to moved script and resolved. Fresh-image path mismatch identified and corrected.

**September 21:** MOM — TODAY Prototype 2 unattended production print PASS; bleach occupancy calibration; toothpaste multi-state dataset; trash-bag active/reserve model; generalized inventory engine; shared inventory state; one-button inventory refresh; Roku ECP status; Last Blink Image; and Recent Logs all integrated into caregiver Admin Panel v5.

**September 22:** Govee Status for four LAN lights PASS; Mom's Phone Presence PASS; local device-registry architecture adopted; Chamberlain/myQ candidates identified for later read-only investigation.

## Current Interpretation

The project is becoming an early **ambient aging-in-place support system**. The strongest pattern remains that useful AI does not require Mom to operate AI. The environment is structured, observations are automated, known ground truth is retained, and useful information is surfaced in familiar forms.

September 21 also demonstrated a second layer: the caregiver's operational interface can itself be simplified. Instead of remembering commands and manually connecting separate prototypes, the Admin Panel now provides system health, one-button inventory observation, current state, Roku reachability, image evidence, and recent operational history.
