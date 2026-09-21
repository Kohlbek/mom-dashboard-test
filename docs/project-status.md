# Aging Parent Support / Mom Dashboard — Project Status

_Last updated: September 21, 2026_

## Status: Prototype 3 Working — Proactive Support, Visual Inventory, and Admin Diagnostics Proven

The project has expanded beyond the original Roku proof of concept. Current working capabilities include entertainment, information, family connection, local lighting, proactive daily printing, visual household inventory, motion-triggered lighting, and a caregiver/admin diagnostic interface.

The working pattern remains: **prove the smallest version -> observe whether it is useful -> measure whether it removes work -> automate maintenance only after the behavior is proven.**

## Proven Capabilities

### Entertainment / Information Dashboard — PASS

Current tested dashboard functions include The Lone Ranger, Johnny Carson, The Rifleman, WeatherNation, ABC News Live, KETV 7, Omaha/Yardley/Nashville weather cards, Family Updates, Living Room Lights ON/OFF, and dynamic greeting/date.

### Roku Long-Standby Reliability — PASS

With **Fast TV Start OFF**, a Roku TV left off for an extended period became unreachable by ping and ECP. With **Fast TV Start ON**, the .210 test TV passed an approximately five-hour off/standby wake test.

**Requirement:** Fast TV Start = ON for reliable long-standby network wake/control.

### Govee Local Smart Lighting — PASS

Two Govee H6004 living-room bulbs are locally controllable through the Windows bridge using local UDP on port 4003 with LAN Control enabled.

### MOM — TODAY Prototype 2 — UNATTENDED PRODUCTION PASS

The accepted large-print one-page MOM — TODAY sheet now includes date, weather area, reminders, family note, Don't Forget, and the full **WHAT YOU HAVE AT HOME** household inventory.

Scheduled Task: `Mom Today Daily Print`  
Schedule: **8:30 AM every morning**, local Central time, StartWhenAvailable.

On **September 21**, the expanded Prototype 2 sheet completed a real-world scheduled unattended morning print. The complete inventory remained readable and fit on one page.

The printed inventory included the established basement inventory plus refrigerator states:
- Bleach — OK
- Milk — PRESENT
- Coke — PRESENT
- Pizza — PRESENT
- other established household supplies — PLENTY / OK as appropriate

**Result: MOM — TODAY Prototype 2 scheduled unattended production run — PASS.**

This validates the Mom-facing design choice of showing the full simple inventory rather than only exceptions.

## Visual Inventory

### Refrigerator Visual Inventory Prototype 2 — BASELINE ESTABLISHED

Fixed monitored set:
- Milk — PRESENT
- Coke — PRESENT (Dr Pepper temporarily stands in for Coke during testing)
- Pizza — PRESENT

Eggs and other refrigerator contents are ignored for this prototype.

### Basement Visual Inventory Prototype 3 — AUTOMATIC IMAGE ACQUISITION PASS

The Acer runs Python/BlinkPy and can authenticate to Blink, select **Mom’s Basement**, request a fresh snapshot, and save the raw JPEG locally.

The fresh snapshot path was standardized to:

`C:\Users\dan\OneDrive\Desktop\mom-inventory\basement-latest.jpg`

This eliminated a stale-file handoff discovered during admin-panel testing.

### Inventory Change Detector Prototype 1 — PASS / CALIBRATION IN PROGRESS

A controlled bleach-removal image produced:
- difference score **10.57**
- `Bleach = OUT`

After correcting the fresh-image path and leaving bleach present, a new snapshot produced:
- difference score **8.20**
- `Bleach = OK`

The detector therefore responded correctly in both tested states, but the observed separation is only **2.37 points**. The current threshold of 10 remains experimental.

Next calibration step: collect several unchanged bleach-present snapshots and several bleach-removed snapshots. If the score ranges overlap, tighten the crop or use a more robust comparison rather than relying on a fragile threshold.

## Admin Panel Prototype — WORKING

A separate caregiver/admin interface is running locally at:

`http://localhost:8081/`

Current system checks include:
- scheduled task found;
- print script found;
- Task Scheduler Operational log enabled;
- Python found;
- Blink script found;
- inventory script found;
- inventory folder found.

Current actions include:
- Run Scheduled Task Now
- Run Print Script Now
- Refresh Status
- Blink Snapshot
- Inventory Check
- Open Inventory Folder

**Admin Panel -> Blink Snapshot -> fresh basement image — PASS.**

**Admin Panel -> Inventory Check -> fresh-image comparison — PASS after path correction.**

### September 20 Print Failure — RESOLVED

The scheduled 8:30 AM print failed because `mom-today-print.ps1` had been moved while backup copies were being organized. Task Scheduler itself launched normally; the configured script path no longer existed.

Restoring the script to:

`C:\Users\dan\OneDrive\Desktop\mom-today-print.ps1`

returned the task to SUCCESS, and the September 21 unattended print confirmed recovery.

This failure produced a useful design improvement: recurring development and diagnostic commands should become admin-panel controls/status indicators, and required dependencies should be shown explicitly as FOUND/MISSING.

## Scripts / Source Workflow

A Google Drive **Scripts** folder now provides a shared place for source scripts that need review or revision. The Acer may continue executing local/OneDrive Desktop copies so cloud edits do not unexpectedly break Mom's live system.

Design rule:

**Shared source/config may live in collaborative storage; live execution remains controlled locally. Secrets should remain local and should not be committed to GitHub or shared Drive source folders.**

## Motion Lighting

**Motion Lighting Prototype 1 — PASS:** Hallway Blink motion -> Alexa -> Govee ON -> timed OFF.

Bathroom Govee testing proved functionality but revealed cloud/motion latency of roughly five seconds or more, too slow for the adjacent-bedroom use case. That route remains parked pending a simpler/faster local solution.

## Key Design Lessons

1. **Task elimination beats instruction.**
2. **Ambient assistance is promising.**
3. **Change the environment when useful.**
4. **Human-confirmed ground truth anchors uncertain visual inference.**
5. **Detect exceptions rather than micromanage inventory.**
6. **Prototype before infrastructure.**
7. **Keep humans in consequential loops.**
8. **Failed tests reveal requirements.**
9. **Development friction is itself a design signal.** Repeated diagnostic commands should become buttons/status indicators.
10. **Dependency visibility matters.** A simple FOUND/MISSING check can prevent long debugging sessions.
11. **Mom and caregiver need different information density.** Mom benefits from the complete readable inventory; the admin view should emphasize changes, errors, and exceptions.

## Current Dependencies / Risks

- Windows Acer remains a prototype host.
- Roku deterministic navigation depends on Roku UI/search layouts.
- Fast TV Start must remain enabled.
- BlinkPy is a community integration and authentication persistence still needs to be engineered before claiming fully unattended snapshot retrieval.
- Visual inventory can be affected by IR exposure, JPEG compression, camera framing, occlusion, and item movement.
- The bleach detector threshold is not yet production-calibrated.

## Near-Term Backlog

- Calibrate bleach detector with multiple unchanged/present and removed snapshots.
- Surface inventory results directly in the admin page rather than separate console output.
- Continue expanding admin diagnostics only where repeated manual commands justify a control.
- Add the complete household inventory to the Mom Dashboard screen so screen and paper match.
- Securely persist Blink authentication locally while preserving 2FA when Blink requires it.
- Continue refrigerator fixed-zone work and normal-use observation.
- DNR / advance-care planning remains separate backlog work.

## Milestone Log

**Prototype 1:** Roku ECP, Lone Ranger, Johnny Carson, Fire-tablet dashboard, local Windows bridge, end-to-end one-touch playback.

**September 15:** automatic bridge startup, restart recovery, AC sleep/hibernate disabled, lid-close reliability.

**September 16:** local Govee control; additional Roku content; Fast TV Start reliability; MOM — TODAY unattended-printing method; refrigerator camera; motion-lighting loop.

**September 17:** first real-world scheduled MOM — TODAY print — PASS. Basement Mom inventory zone consolidated/labeled and human baseline established.

**September 19:** refrigerator Prototype 2 baseline; expanded MOM — TODAY inventory test print; automatic Blink image acquisition; bleach removal detector score 10.57 -> OUT.

**September 20:** Admin Panel Prototype working. Print failure traced to moved script and resolved. Admin Blink Snapshot PASS. Fresh-image path mismatch identified and corrected. Bleach-present comparison score 8.20 -> OK.

**September 21:** expanded MOM — TODAY Prototype 2 completed its real-world scheduled 8:30 AM unattended production print with the full household inventory — PASS.

## Current Interpretation

The project is becoming an early **ambient aging-in-place support system**. The strongest pattern remains that useful AI does not require Mom to operate AI. The environment is structured, observations are automated, known ground truth is retained, and the system surfaces useful information in familiar forms.

The Admin Panel adds a second important layer: caregiver-facing infrastructure should reduce the cognitive burden of operating and debugging the support system itself.
