# Mom Dashboard — Current Architecture

_Last updated: September 15, 2026_

## Purpose

The Mom Dashboard is a Human + AI aging-parent support experiment. The goal is to reduce routine caregiver workload while preserving Mom's independence and making everyday technology easier to use.

The current working prototype focuses on simplifying Roku TV use. Instead of asking Mom to navigate Roku menus, search results, providers, and playback screens, the dashboard reduces the task to one large button press.

## Current Working Architecture

```text
Fire tablet
   |
   | opens local dashboard in Silk
   v
Windows bridge computer (192.168.0.241:8080)
   |
   | PowerShell HTTP listener + Roku control logic
   v
Roku TV test target (192.168.0.210:8060)
   |
   | Roku ECP commands over local network
   v
Roku UI / Search / Roku Channel
   |
   v
The Lone Ranger or Johnny Carson plays
```

## Components

### 1. Fire tablet
- Acts as Mom's simplified control surface.
- Uses Amazon Silk browser.
- Displays very large buttons designed to avoid normal Roku navigation.
- Current working buttons:
  - `WATCH THE LONE RANGER`
  - `WATCH JOHNNY CARSON`

### 2. Windows bridge computer
- Current test IP: `192.168.0.241`
- Runs `roku-bridge.ps1`.
- Hosts the local dashboard on port `8080`.
- Receives browser requests such as:
  - `/lone-ranger`
  - `/johnny-carson`
  - `/home`
  - `/test`
- Sends Roku ECP commands to the TV.
- This is a prototype dependency and is not intended to be the permanent production host.

### 3. Roku TV control
- Test Roku IP: `192.168.0.210`
- Mom's primary Hisense Roku TV has also been tested separately and uses reserved IP `192.168.0.87`.
- Roku ECP control is enabled.
- Fast TV Start / wake-on-WLAN has been enabled and tested.
- DHCP reservations were configured to keep Roku IP addresses stable.

## Roku Control Method

The dashboard does not rely on the Roku mobile app or Alexa for playback.

The bridge sends local Roku ECP HTTP POST commands such as:

```text
http://ROKU_IP:8060/keypress/Home
http://ROKU_IP:8060/keypress/Right
http://ROKU_IP:8060/keypress/Select
http://ROKU_IP:8060/keypress/Lit_X
```

The prototype uses Roku Search and deterministic navigation sequences.

## The Lone Ranger sequence

Current proven logic:

1. PowerOn
2. Wait for wake
3. Home
4. Open left navigation
5. Move to Search
6. Enter Search
7. Type `The Lone Ranger` character-by-character through ECP
8. Move right six times from the keyboard
9. Select the first correct result
10. Wait for detail page
11. Select the first Roku Channel playback option

This sequence has been tested successfully from TV off through playback.

## Johnny Carson sequence

Current proven logic:

1. PowerOn
2. Wait for wake
3. Home
4. Open left navigation
5. Move to Search
6. Enter Search
7. Type `Johnny Carson` character-by-character through ECP
8. Move right six times to the autocomplete area
9. Move down to TV-show results
10. Move right to the correct 1972–1992 listing
11. Select it
12. Wait for detail page
13. Select the Roku Channel playback option

This sequence avoids the incorrect Johnny Carson listing and has been tested successfully from TV off through playback.

## Why GitHub Pages is not the active control path

A GitHub Pages dashboard was deployed successfully at:

`https://kohlbek.github.io/mom-dashboard-test/`

The page itself loaded correctly, but direct control of the local Roku from the HTTPS page failed. The local Roku control endpoint is HTTP on a private-network address, and browser mixed-content/private-network restrictions make this architecture unreliable.

Therefore the current working path is local:

```text
Fire tablet -> local Windows bridge -> Roku
```

GitHub remains useful as the source repository and backup/version-control location for project code and documentation.

## Network lessons learned

- Roku control works only when the bridge can route to the TV on the local network.
- One test failure was traced to a Roku being connected through a different Wi-Fi band/network path; after correcting Wi-Fi connectivity, ECP immediately worked again.
- DHCP reservations are important because dashboard code references stable local addresses.
- Router port forwarding is not required and should not be used for direct Roku exposure.

## Current security model

- Roku ECP remains local to the home network.
- No Roku port is exposed to the public internet.
- The dashboard bridge listens locally on port `8080`.
- GitHub Pages contains only interface code and no sensitive family, banking, or medical data.

## Planned Architecture Direction

The Windows bridge is a prototype host. The likely long-term design is:

```text
GitHub repository / remote content source
             |
             v
Small always-on local controller
             |
      --------------------
      |        |         |
      v        v         v
    Roku    Printer    Smart home
      |                  |
      v                  v
Fire tablet         Lights / routines
```

A small always-on controller could eventually host:
- Mom Dashboard
- Roku automation
- 8:30 AM morning routine
- Printer automation
- Govee lighting control
- Daily update generation
- Family-email/photo ingestion
- Other home automation

The user-facing design goal is to keep Mom's interaction extremely simple even as the back-end system becomes more capable.
