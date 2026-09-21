# Mom Dashboard — Current Architecture

_Last updated: September 21, 2026_

## Purpose

The Mom Dashboard is a Human + AI aging-parent support experiment. The goal is to increase Mom's independence, safety, connection, and comfort while reducing repetitive caregiver work. The design preference is to remove unnecessary tasks from Mom rather than teach her additional technology.

## Current Architecture

```text
Mom-facing layer
  Fire tablet dashboard
  MOM — TODAY 8:30 AM print
          |
          v
Windows Acer prototype host
  192.168.0.241
     |---- port 8080 ---- Mom Dashboard
     |---- port 8081 ---- Caregiver/Admin Panel
     |---- Roku ECP ----> Hisense Roku TVs
     |---- local UDP ---> Govee H6004 bulbs
     |---- scheduled ---> Brother MFC-L2760DW
     |
     +---- Python/BlinkPy ---> Blink snapshot
                                |
                                v
                         fixed inventory zones
                                |
                                v
                       inventory-state processor
                                |
              +-----------------+-----------------+
              v                                   v
      Mom full inventory                 caregiver exceptions/
      print + dashboard                   diagnostics
```

The Windows bridge remains the prototype local controller. No router port forwarding is required.

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

Planned synchronization: add the same complete household inventory shown on MOM — TODAY so Mom's paper and screen views match.

## Caregiver/Admin Panel

A separate admin interface runs locally at:

`http://localhost:8081/`

It intentionally remains separate from Mom's simplified UI.

Current dependency checks:
- scheduled task FOUND/MISSING;
- print script FOUND/MISSING;
- Task Scheduler Operational log enabled;
- Python FOUND/MISSING;
- Blink script FOUND/MISSING;
- inventory script FOUND/MISSING;
- inventory folder FOUND/MISSING.

Current actions:
- run scheduled print task;
- run print script;
- refresh status;
- request Blink snapshot;
- run inventory check;
- open inventory folder.

The admin panel was created after a scheduled-print failure required repeated PowerShell diagnostics. The architectural lesson is that recurring development/diagnostic commands should become controls and status indicators rather than requiring the caregiver to remember commands.

## Roku Control

Test TV: `192.168.0.210`  
Mom primary TV: `192.168.0.87`  
Roku ECP: port 8060.

**Deployment requirement:** Fast TV Start = ON.

ECP text injection leaves on-screen keyboard focus on **A**; deterministic navigation sequences account for this.

## Govee Local Lighting

Known H6004 bulbs:
- `192.168.0.154`
- `192.168.0.228`
- UDP port 4003
- LAN Control enabled independently

Local ON/OFF does not require Govee cloud API.

## MOM — TODAY Printing

- Printer: Brother MFC-L2760DW
- Script: `C:\Users\dan\OneDrive\Desktop\mom-today-print.ps1`
- Scheduled Task: `Mom Today Daily Print`
- Daily: 8:30 AM local Central time, StartWhenAvailable
- Rendering: HTML in separate Microsoft Edge user-data profile with kiosk printing and `window.print()`

Prototype 2 adds the complete **WHAT YOU HAVE AT HOME** inventory.

On **September 21, 2026**, the expanded Prototype 2 sheet completed a scheduled unattended production print and remained readable on one page.

**MOM — TODAY Prototype 2 scheduled unattended production — PASS.**

## Blink / Visual Inventory

### Refrigerator

Monitored Prototype 2 set:
- Milk
- Coke (Dr Pepper stand-in during testing)
- Pizza

Eggs and other refrigerator contents are outside scope.

### Basement

Human-confirmed baseline plus fixed physical zones remain authoritative. The camera is used primarily to detect meaningful changes rather than reconstruct hidden exact quantities.

Current automatic path:

```text
Admin Blink Snapshot
       |
       v
blink-test.py
       |
       v
C:\Users\dan\OneDrive\Desktop\mom-inventory\basement-latest.jpg
       |
       v
check-inventory.py
       |
       v
known fixed-zone comparison
       |
       v
inventory state
```

The snapshot output was deliberately standardized to the same `mom-inventory` path consumed by the detector. Earlier, Blink wrote a fresh image to Desktop while the detector read an older file inside `mom-inventory`, producing a stale 10.57/OUT result. Standardizing the handoff removed that integration bug.

### Bleach Detector Calibration

Controlled bleach-removed test:
- score 10.57
- Bleach = OUT

Fresh bleach-present test:
- score 8.20
- Bleach = OK

The current threshold of 10 is experimental. The 2.37-point separation is not sufficient evidence for production reliability. Multiple unchanged/present and removed samples are required before selecting a stable threshold. If score distributions overlap, tighten the crop or adopt a more robust comparison.

## Household Inventory State

Target architecture:

```text
human-confirmed baseline
        +
fixed camera zones
        +
consumption context
        |
        v
household inventory state
        |
        +----> Mom Dashboard: full readable inventory
        +----> MOM — TODAY: full readable inventory
        +----> Admin Panel: changes, errors, exceptions
```

Mom and caregiver intentionally receive different views:
- **Mom:** complete simple inventory, useful as a daily reminder/reference.
- **Caregiver:** exceptions and system health.

## Print Failure / Dependency Monitoring

On September 20 the scheduled task launched PowerShell normally but returned `4294770688 (0xFFFD0000)`. The root cause was that `mom-today-print.ps1` had been moved while backup copies were being organized.

Restoring the expected path returned the task to SUCCESS. The September 21 scheduled print confirmed recovery.

Architectural response: dependencies should be explicit and visible in the admin panel. A missing script should be reported as **MISSING**, not discovered through downstream failure.

## Source / Script Workflow

Google Drive contains a **Mom Dashboard / Scripts** folder for shared source review. The Acer can continue executing controlled local/OneDrive Desktop copies.

Current important local scripts include:
- `mom-admin.ps1`
- `mom-today-print.ps1`
- `roku-bridge.ps1`
- `blink-test.py`
- `mom-inventory\check-inventory.py`

Operational principle:

**Collaborative code/config can live in shared storage; live execution remains controlled locally. Secrets remain local.**

Blink credentials/authentication should not be stored in GitHub or ordinary shared source files. Secure local credential/token persistence is a planned next step. If Blink requires fresh 2FA, the system should surface that condition rather than bypass it.

## Motion Lighting

Tested loop:

```text
Hallway motion -> Blink -> Alexa -> Govee ON -> wait -> Govee OFF
```

**Motion Lighting Prototype 1 — PASS.**

Bathroom Blink/Alexa/Govee testing works but has roughly five-second-or-more latency, too slow for the intended adjacent-bedroom use. The route is parked pending a simpler/faster local solution.

## Security / Privacy

- Roku and Govee controls remain on the home LAN.
- No router port forwarding.
- GitHub should not contain private medical, financial, password, account, or camera credential information.
- Shared Drive source folders should not contain authentication secrets.
- Camera experiments should remain purpose-limited and transparent to Mom.

## Design Principles

1. Prove the smallest useful behavior before automating maintenance.
2. Prefer removing tasks from Mom over requiring new interfaces.
3. **Structure the environment so AI has fewer things it needs to be intelligent about.**
4. Human-confirmed ground truth overrides uncertain visual inference.
5. Use cameras primarily for change/exception detection.
6. Inventory status combines quantity and expected consumption, not package count alone.
7. Mom-facing output can be comprehensive and readable; caregiver output should emphasize exceptions.
8. Prefer simple local solutions when they solve the real problem more reliably than sophisticated cloud chains.
9. Failed experiments reveal requirements.
10. **Repeated diagnostic work should become an admin control or status indicator.**
11. **Make dependencies observable.**
12. **Automate the observation after the environment has been structured.**

## Near-Term Architecture Work

- Collect detector calibration samples without unnecessary physical handling.
- Put inventory result/state directly into the Admin Panel.
- Add complete household inventory to Mom Dashboard screen.
- Secure Blink authentication locally for unattended operation where Blink permits it.
- Move toward one configuration-driven inventory processor for multiple items/zones.
- Continue real-world soak testing before replacing proven components.
