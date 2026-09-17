# Mom Dashboard — Current Architecture

_Last updated: September 16, 2026_

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
      +---- snapshots/live view ----> visual inventory experiments
```

The Windows bridge remains the prototype local controller. GitHub is the source/documentation repository; no router port forwarding is required.

## Fire Tablet / Dashboard

The Fire tablet uses Amazon Silk to open the local dashboard. The current proven Prototype 3 dashboard includes:

- WATCH THE LONE RANGER
- WATCH JOHNNY CARSON
- WATCH THE RIFLEMAN
- WATCH WEATHER (WeatherNation)
- ABC NEWS
- KETV 7
- Family Updates
- Living Room Lights ON/OFF
- Omaha, Yardley, and Nashville weather cards
- Dynamic greeting and date

Family Updates currently remain hard-coded in the bridge. Current Danny update: `Danny went back to college.` A future maintenance improvement is to separate routine content from the bridge code.

## Roku Control

Test TV: `192.168.0.210`

Other/Mom primary TV: `192.168.0.87`

Roku ECP on port 8060 is used for PowerOn, Home, navigation, Select, and Lit_ text injection. Search-result navigation remains deterministic and therefore somewhat brittle if Roku changes its UI.

### Proven content sequences

- The Lone Ranger — end-to-end from TV off through playback.
- Johnny Carson — correct 1972–1992 listing and Roku Channel playback.
- The Rifleman — search `the rifleman`, Right x6, Down x2, Select, wait 7 seconds, Select.
- ABC News Live — search `abc news live`, Right x6, Down x1, Select.
- KETV 7 — search `ketv7`, Right x6, Down x1, Select, wait 3 seconds, Select.
- WeatherNation — search `weathern`, Right x6, Down x1, Select.

### Important Roku findings

**Fast TV Start must be ON.** With Fast TV Start off, a Roku left off for an extended period became unreachable by ping/ECP and could not be awakened by the dashboard. After enabling Fast TV Start, the `.210` TV passed a five-hour off/standby test and woke successfully through the dashboard. Treat Fast TV Start as a deployment requirement.

ECP text injection leaves Roku keyboard focus on the letter **A**, rather than moving focus to the last typed character as manual remote typing does. Navigation sequences must be designed from that known focus position.

## Govee Local Lighting

Two Govee H6004 bulbs are installed in the living room.

- Bulb 1: `192.168.0.154`
- Bulb 2: `192.168.0.228`
- Local control: UDP port 4003
- LAN Control must be enabled independently on each bulb.

The bulbs use 2.4 GHz Wi-Fi; the Acer bridge can remain on 5 GHz because the router routes between bands. Local ON/OFF has been proven without depending on the Govee cloud API.

## MOM — TODAY Printing

The proactive morning-sheet prototype is operational.

- Printer: Brother MFC-L2760DW
- Script: `C:\Users\dan\OneDrive\Desktop\mom-today-print.ps1`
- Scheduled Task: `Mom Today Daily Print`
- Schedule: daily at 8:30 AM local Central time, StartWhenAvailable
- Rendering/printing: HTML launched in a separate Microsoft Edge user-data profile with kiosk printing and `window.print()`.

The large-print physical design was tested and accepted. The current content includes date, weather area, reminders, family note, and Don't Forget section.

## Blink / Visual Inventory

September 16 produced a working visual-inventory proof of concept.

A wireless Blink camera was placed inside the refrigerator on the top shelf. It maintained connectivity with the refrigerator door closed, updated its thumbnail, and provided live view using infrared/night vision.

Mom normally uses the top shelf when the caregiver is away. The initial target inventory is deliberately small and predictable:

- Milk
- Coke
- Pizza box

The proposed shelf design uses labeled fixed zones (`MILK | COKE | PIZZA`) to make visual detection more reliable. The intended status model is `PLENTY / OK / LOW / OUT / CAN'T TELL`, with simple `PRESENT / LOW / OUT` sufficient for the refrigerator V1.

A two-image test with an item removed demonstrated that a visual change could be detected and converted into an inventory/shopping recommendation. This is **Visual Inventory Prototype 1 — PASS**.

Basement photographs also demonstrated the value of visual inventory for household supplies, especially excess paper towels and toilet paper. An important design finding is that household inventory can be distributed across multiple locations, so future recommendations should consider total household inventory rather than a single storage point.

## Shopping Assistant Direction

The emerging model is:

```text
Visual inventory + known recurring needs/preferences
                  |
                  v
          Shopping recommendations
        BUY / REMIND / DON'T BUY
                  |
                  v
      future store-arrival reminder
                  |
                  v
 future delivery/cart preparation + human approval
```

Initial examples:

- REMIND: bananas (liked but often forgotten)
- DON'T BUY: paper towels and toilet paper while household reserve is abundant
- BUY: genuinely depleted regular items
- DELIVERY CANDIDATE: heavy/bulky items that are increasingly difficult for Mom to carry

Mom's regular shopping is highly repetitive, which makes a small known-item model preferable to a general-purpose shopping system.

## Motion Lighting

A hallway Blink camera was successfully used as an Alexa Routine motion trigger. Alexa then controlled the existing Govee living-room lights.

Full tested loop:

```text
Hallway motion -> Blink -> Alexa Routine -> Govee lights ON
                                      -> wait
                                      -> Govee lights OFF
```

Both ON and timed OFF were verified. This is **Motion Lighting Prototype 1 — PASS**.

The intended aging-in-place application is nighttime pathway/bathroom lighting. The existing bathroom wall switch also controls the fan, so replacing the bathroom bulb with an always-powered smart bulb is not appropriate. A simple rechargeable/local motion-sensor light is a likely deployment option; the Blink/Alexa/Govee experiment already proves the more general automation chain.

## Design Principles Emerging from Testing

1. Prove the smallest useful behavior before automating its maintenance.
2. Prefer removing tasks from Mom over requiring her to learn new interfaces.
3. Change the physical environment when that makes AI simpler and more reliable (for example, labeled refrigerator shelf zones).
4. Use AI for exception detection rather than unnecessary continuous micromanagement.
5. Keep purchase decisions human-approved while shopping automation is experimental.
6. Prefer simple local solutions when they solve the real problem more reliably than a sophisticated cloud chain.
7. Treat failed experiments as useful constraint discovery.

## Security / Privacy

- Roku and Govee local controls remain on the home LAN.
- No router port forwarding is used.
- GitHub should not contain private medical, financial, password, or account information.
- Camera-based experiments should remain purpose-limited and transparent to Mom.

## Longer-Term Direction

The Windows laptop is still a prototype host. A small always-on controller may eventually consolidate dashboard hosting, Roku control, printing, lighting, visual-inventory processing, shopping assistance, family updates, and other routines while keeping Mom's interface extremely simple.