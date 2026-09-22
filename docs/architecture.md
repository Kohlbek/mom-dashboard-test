# Mom Dashboard — Current Architecture

_Last updated: September 22, 2026_

## Purpose

The Mom Dashboard is a Human + AI aging-parent support experiment. The goal is to increase Mom's independence, safety, connection, and comfort while reducing repetitive caregiver work. The design preference is to remove unnecessary tasks from Mom rather than teach her additional technology.

## Current Architecture

```text
Mom-facing layer
  Fire tablet dashboard
  MOM — TODAY 8:30 AM print
          |
          v
Windows Acer prototype host — 192.168.0.241
     |---- port 8080 ---- Mom Dashboard
     |---- port 8081 ---- Caregiver/Admin Panel
     |---- Roku ECP ----> Living Room TV (.87)
     |                 -> Bedroom TV (.210)
     |---- local UDP ---> Govee H6004 bulbs
     |---- scheduled ---> Brother MFC-L2760DW
     |
     +---- Python/BlinkPy ---> basement-latest.jpg
                                |
                                v
                        inventory-config.json
                                |
                                v
                         inventory-check.py
                                |
                                v
                         inventory-state.json
                          /             \
                         v               v
                 Admin Panel       Mom-facing outputs
                 current state     MOM — TODAY / dashboard
                 + diagnostics     (integration next)
```

The Windows Acer remains the prototype local controller. No router port forwarding is required.

## Mom Dashboard

Current Prototype 3 includes:
- The Lone Ranger
- Johnny Carson
- The Rifleman
- WeatherNation
- ABC News
- KETV 7
- Family Updates
- Living Room Lights ON/OFF
- Omaha, Yardley, Nashville weather cards
- Dynamic greeting/date

Planned synchronization: read the same shared household inventory state used by the Admin Panel so Mom's screen and paper views match.

## Caregiver/Admin Panel v5

Local interface:

`http://localhost:8081/`

The Admin Panel is intentionally separate from Mom's simplified UI.

### System Checks

- scheduled task FOUND/MISSING
- print script FOUND/MISSING
- Task Scheduler Operational log enabled
- Python FOUND/MISSING
- Blink script FOUND/MISSING
- inventory script FOUND/MISSING
- inventory folder FOUND/MISSING

### MOM — TODAY Operations

- Run Scheduled Task Now
- Run Print Script Now
- Refresh Status

### Visual Inventory Operations

- current inventory states from `inventory-state.json`
- last inventory update timestamp
- Refresh Inventory
- Blink Snapshot
- Inventory Check
- Open Inventory Folder

**Refresh Inventory** performs the integrated path:

```text
Admin button
   |
   v
blink-test.py
   |
   v
fresh basement-latest.jpg
   |
   v
inventory-check.py + inventory-config.json
   |
   v
inventory-state.json
   |
   v
Admin page reload
```

September 21 end-to-end test produced a Blink image at 23:19:06 and updated inventory state at 23:19:07.

### Roku Status

Status-only ECP checks:
- Living Room TV — `192.168.0.87:8060`
- Mom's Bedroom TV — `192.168.0.210:8060`

The Admin Panel reports ONLINE/OFFLINE by testing Roku ECP reachability on port 8060. It does **not** send TV control commands from this status section.

### Last Blink Image

The Admin Panel exposes:
- latest basement image;
- file/capture timestamp;
- link to open the image full-size.

### Recent Logs

The current Recent Logs view combines available operational evidence such as:
- inventory state update time and values;
- Blink image capture time;
- MOM — TODAY scheduled-task execution/result.

This is deliberately a lightweight operational history rather than a separate logging platform.

## Roku Control

Living Room TV: `192.168.0.87`  
Mom's Bedroom TV: `192.168.0.210`  
Roku ECP: port 8060.

**Deployment requirement:** Fast TV Start = ON.

Entertainment-control sequences remain in the Mom-facing dashboard/bridge. The caregiver Roku Status section is status-only.

## Local Device Registry

A local `mom-devices.json` registry is being introduced as the authoritative map for LAN device names, addresses, types, and relevant ports. Scripts should progressively read this registry instead of embedding IP addresses independently.

Current registry:
- Acer / LEO Command Center — `192.168.0.241`
- Living Room TV — `192.168.0.87:8060`
- Mom's Bedroom TV — `192.168.0.210:8060`
- Basement Govee — `192.168.0.18:4003`
- Living Room Govee — `192.168.0.228:4003`
- Living Room by the Stairs Govee — `192.168.0.154:4003`
- Hallway Govee — `192.168.0.190:4003`
- Mom's Phone — `192.168.0.115`
- Chamberlain / MyQ-633 candidate — `192.168.0.3`
- myQ front-of-door device — `192.168.0.172` (exact role still unidentified)

The registry is configuration, not a Windows DNS/hosts replacement. It provides one project-level source of truth for device addressing.

## Govee Local Lighting

Four Govee LAN devices are now represented in the Admin Panel:
- Basement — `192.168.0.18`
- Living Room — `192.168.0.228`
- Living Room by the Stairs — `192.168.0.154`
- Hallway — `192.168.0.190`
- UDP port 4003
- LAN Control enabled

Admin Govee Status uses a non-mutating local `devStatus` query and reports LAN reachability, power state, and brightness without changing the lights. September 22 test: all four devices reported ONLINE. Local ON/OFF does not require Govee cloud API.

## Mom's Phone Presence

Admin Panel phone presence monitors `192.168.0.115` and reports **DETECTED / NOT DETECTED**. The wording is intentionally conservative: a phone that does not answer a network check may be asleep, away, rebooting, or otherwise temporarily unreachable. DETECTED is useful evidence that the phone is still responding on the home network, not a location guarantee.

## MOM — TODAY Printing

- Printer: Brother MFC-L2760DW
- Script: `C:\Users\dan\OneDrive\Desktop\mom-today-print.ps1`
- Scheduled Task: `Mom Today Daily Print`
- Daily: 8:30 AM local Central time, StartWhenAvailable
- Rendering: HTML in separate Microsoft Edge user-data profile with kiosk printing and `window.print()`

Prototype 2 includes the complete **WHAT YOU HAVE AT HOME** inventory.

On **September 21, 2026**, the expanded Prototype 2 sheet completed a scheduled unattended production print and remained readable on one page.

**MOM — TODAY Prototype 2 scheduled unattended production — PASS.**

Current inventory values in the print remain to be connected to the new shared `inventory-state.json`.

## Blink / Visual Inventory

### Refrigerator

Monitored Prototype 2 set:
- Milk
- Coke (Dr Pepper stand-in during testing)
- Pizza

Eggs and other refrigerator contents are outside scope.

### Basement Image Acquisition

`blink-test.py` writes the fresh image to:

`C:\Users\dan\OneDrive\Desktop\mom-inventory\basement-latest.jpg`

This standardized handoff eliminated the earlier stale-image path mismatch.

## Generalized Inventory Architecture

Current files:

- `mom-inventory\inventory-config.json` — item models, zones, thresholds, state rules
- `mom-inventory\inventory-check.py` — generalized processor
- `mom-inventory\inventory-state.json` — shared current household state

The architecture deliberately supports multiple model types rather than forcing every product into one visual algorithm.

### Model: Present / Absent

Used for bleach.

Later occupancy calibration:
- empty samples ~0%
- present samples ~14–28%

This is substantially cleaner than the earlier experimental whole-zone difference scores.

### Model: Active + Reserve

Used for trash bags.

Zones:
- active-use position: `(956,338)-(1050,439)`
- reserve position: `(852,332)-(949,448)`

Observed states:
- 3 boxes: active ~81%, reserve ~96%
- 2 boxes: active ~78%, reserve ~47%
- 1 box: stable new samples active ~76%, reserve ~0%
- 0 boxes: ~0% in both zones

State interpretation:
- active + 2 reserve = PLENTY
- active + 1 reserve = PLENTY
- active + 0 reserve = LOW
- no active + no reserve = OUT

This model tolerates natural replenishment behavior: a reserve box can move into the active-use position without depending on calibration removal order.

### Model: Manual / Future Slot Logic

Toothpaste is temporarily represented as human-confirmed PLENTY.

A controlled 4/3/2/1/0 dataset showed that whole-zone occupancy was stable within states but could not reliably distinguish 1 from 2 boxes. The future detector should use fixed positions/slots or another representation appropriate to how toothpaste is stored.

## Shared Household Inventory State

Current generalized output is written in structured JSON with:
- update timestamp;
- source image;
- simple inventory states;
- detector details/scores.

Current tested state:
- Bleach — OK
- Trash Bags — PLENTY
- Toothpaste — PLENTY

**Architectural rule:** the generalized engine should become the sole writer of `inventory-state.json`. Experimental item-specific scripts may remain for calibration/debugging but should not overwrite the shared state.

Target downstream architecture:

```text
inventory-state.json
      |
      +----> Admin Panel — current state + diagnostics
      +----> MOM — TODAY — full readable inventory
      +----> Mom Dashboard — full readable inventory
```

## Source / Script Workflow

Google Drive contains a **Mom Dashboard / Scripts** folder for shared source review. The Acer continues executing controlled local/OneDrive Desktop copies.

Important current local scripts/files:
- `mom-admin.ps1`
- `mom-today-print.ps1`
- `roku-bridge.ps1`
- `blink-test.py`
- `mom-inventory\inventory-config.json`
- `mom-inventory\inventory-check.py`
- `mom-inventory\inventory-state.json`

Operational principle:

**Collaborative code/config can live in shared storage; live execution remains controlled locally. Secrets remain local.**

Blink credentials/authentication should not be stored in GitHub or ordinary shared source files.

## Motion Lighting

```text
Hallway motion -> Blink -> Alexa -> Govee ON -> wait -> Govee OFF
```

**Motion Lighting Prototype 1 — PASS.**

Bathroom Blink/Alexa/Govee testing works but latency remains too slow for the intended adjacent-bedroom use.

## Security / Privacy

- Roku and Govee controls remain on the home LAN.
- No router port forwarding.
- GitHub should not contain private medical, financial, password, account, or camera credential information.
- Shared Drive source folders should not contain authentication secrets.
- Camera experiments should remain purpose-limited and transparent to Mom.
- Caregiver Roku Status performs reachability checks only; it does not send TV control commands.

## Design Principles

1. Prove the smallest useful behavior before automating maintenance.
2. Prefer removing tasks from Mom over requiring new interfaces.
3. **Structure the environment so AI has fewer things it needs to be intelligent about.**
4. Human-confirmed ground truth overrides uncertain visual inference.
5. Use cameras primarily for change/exception detection.
6. Inventory status combines quantity and expected consumption, not package count alone.
7. **Different products may require different sensing models.**
8. **Model natural household behavior, not the staged calibration sequence.**
9. Mom-facing output can be comprehensive and readable; caregiver output should emphasize state, exceptions, and system health.
10. Prefer simple local solutions when they solve the real problem more reliably than sophisticated cloud chains.
11. Failed experiments and outliers reveal requirements.
12. **Repeated diagnostic work should become an admin control or status indicator.**
13. **Make dependencies observable.**
14. **Use one shared state source for downstream interfaces.**
15. **Automate observation after the environment has been structured.**

## Near-Term Architecture Work

- Make the generalized engine the sole writer of `inventory-state.json`.
- Connect MOM — TODAY to shared inventory state.
- Connect the Mom Dashboard inventory display to the same state.
- Add remaining products using the model appropriate to each storage/use pattern.
- Secure Blink authentication locally for unattended operation where Blink permits it.
- Continue real-world soak testing before replacing proven components.
- Move existing scripts progressively to the shared local `mom-devices.json` registry.
- Investigate Chamberlain/myQ read-only door-state options before considering any physical door control.
- Prototype basement Blink motion -> basement Govee lighting using the already proven motion-light pattern.
