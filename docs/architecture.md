# Mom Dashboard — Current Architecture

_Last updated: September 17, 2026_

## Purpose

The Mom Dashboard is a Human + AI aging-parent support experiment. The goal is to increase Mom's independence, safety, connection, and comfort while reducing repetitive caregiver work. The design preference is to remove unnecessary tasks from Mom rather than teach her additional technology.

## Current Architecture

```text
Fire tablet
   |
   v
Windows bridge (192.168.0.241:8080)
   |---------------- Roku ECP ----------------> Hisense Roku TVs
   |---------------- local UDP ---------------> Govee H6004 bulbs
   |
   +---------------- scheduled printing ------> Brother MFC-L2760DW

Blink cameras ---- motion ----> Alexa routine ----> Govee lights
      |
      +---- snapshots/live view ----> visual inventory zones
                                             |
                                             v
                                human baseline + AI change detection
```

The Windows bridge remains the prototype local controller. No router port forwarding is required.

## Fire Tablet / Dashboard

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

## Roku Control

Test TV: `192.168.0.210`
Mom primary TV: `192.168.0.87`
Roku ECP: port 8060.

**Deployment requirement:** Fast TV Start = ON. The .210 TV passed an approximately five-hour off/standby wake test after Fast TV Start was enabled.

ECP text injection leaves on-screen keyboard focus on **A**; deterministic navigation sequences are designed from that position.

## Govee Local Lighting

Two Govee H6004 bulbs:
- `192.168.0.154`
- `192.168.0.228`
- UDP port 4003
- LAN Control enabled independently on both

Local ON/OFF does not require Govee cloud API.

## MOM — TODAY Printing

- Printer: Brother MFC-L2760DW
- Script: `C:\Users\dan\OneDrive\Desktop\mom-today-print.ps1`
- Scheduled Task: `Mom Today Daily Print`
- Daily: 8:30 AM local Central time, StartWhenAvailable
- Rendering: HTML in separate Microsoft Edge user-data profile with kiosk printing and `window.print()`

The large-print design was accepted. On **September 17, 2026**, the first real-world scheduled 8:30 AM production print completed successfully without intervention.

**MOM — TODAY Prototype 1 — PASS.**

## Blink / Visual Inventory Architecture

### Refrigerator

Prototype 1 proved that a Blink camera can remain connected and provide usable live/IR images inside the closed refrigerator.

Initial known-item model:
- Milk
- Coke
- Pizza box

Next step: clean refrigerator, establish fixed labeled zones `MILK | COKE | PIZZA`, then capture a baseline and observe normal use.

### Basement Mom Inventory Zone

On September 17, supplies were physically consolidated so approximately 90% of the items Mom normally goes into the basement to retrieve are in one monitored area.

Architecture:

```text
Known Mom supplies
      |
      v
fixed/labeled physical locations
      |
      v
permanent basement Blink camera
      |
      v
repeatable daytime / IR images
      |
      v
compare against human-confirmed baseline
      |
      v
PLENTY / OK / LOW / OUT / CAN'T TELL
      |
      v
surface meaningful exceptions
```

The Blink view covers:
- left paper-product zone;
- right organized Mom-supply shelving.

Unlabeled areas, including the caregiver's bottom shelf, are explicitly outside the monitoring scope.

The camera is not expected to reconstruct exact quantities hidden behind front items. Human-confirmed baseline quantities and item context are authoritative. AI should primarily detect meaningful visual changes from that known state.

Inventory state should account for **consumption rate as well as package count**. Example: a single box of toilet-tablet cleaner contains multiple tablets and is expected to last at least one year, so its status is PLENTY rather than LOW.

**Basement Visual Inventory Prototype 2 — BASELINE ESTABLISHED.**

## Shopping Assistant Direction

```text
human-confirmed baseline + recurring needs/preferences
                       |
Blink visual change ---+
                       |
                       v
             shopping recommendation
      BUY / REMIND / DON'T BUY / DELIVERY CANDIDATE
                       |
                       v
                human approval
```

The intended role of AI is exception detection, not continuous inventory micromanagement.

## Motion Lighting

Tested loop:

```text
Hallway motion -> Blink -> Alexa Routine -> Govee lights ON
                                      -> wait
                                      -> Govee lights OFF
```

**Motion Lighting Prototype 1 — PASS.**

A simple rechargeable/local motion-sensor light remains a likely bathroom deployment because the existing bathroom wall switch controls both the light and fan.

## Design Principles Emerging from Testing

1. Prove the smallest useful behavior before automating maintenance.
2. Prefer removing tasks from Mom over requiring new interfaces.
3. **Structure the environment so AI has fewer things it needs to be intelligent about.**
4. Fixed zones and labels serve two purposes: they help Mom and make camera interpretation more reliable.
5. Human-confirmed baseline data should override uncertain visual inference.
6. Use cameras primarily for change/exception detection rather than exact hidden-object counting.
7. Keep purchase decisions human-approved while shopping automation is experimental.
8. Prefer simple local solutions when they solve the real problem more reliably than sophisticated cloud chains.
9. Treat failed experiments as useful constraint discovery.

## Security / Privacy

- Roku and Govee controls remain on the home LAN.
- No router port forwarding.
- GitHub should not contain private medical, financial, password, or account information.
- Camera experiments should remain purpose-limited and transparent to Mom.

## Longer-Term Direction

The Windows laptop is still a prototype host. A small always-on controller may eventually consolidate dashboard hosting, Roku control, printing, lighting, visual-inventory processing, shopping assistance, family updates, and other routines while keeping Mom's interface extremely simple.