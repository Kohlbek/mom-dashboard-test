# Aging Parent Support / Mom Dashboard — Project Status

_Last updated: September 15, 2026_

## Status: Prototype 1 Working

The first end-to-end Mom Dashboard prototype is operational.

From the Fire tablet, one button can now turn on/control the Roku TV, find the intended program, choose the correct Roku result/provider, and begin playback.

Both initial program buttons have passed end-to-end testing:

- **The Lone Ranger — PASS**
- **Johnny Carson — PASS**

## What has been proven

### Simplified TV control
A Roku task that normally requires multiple remote-control steps has been reduced to one large button press on the Fire tablet.

This is an important project result because it demonstrates **task elimination/redesign**, rather than merely providing Mom with instructions for performing the existing Roku task.

### Roku wake/control
- Roku ECP works over the local network.
- Fast TV Start allows the TV to wake through network control.
- PowerOn, Home, navigation, text entry, and Select commands have been tested.
- Full sequences work from TV off through program playback.

### Content targeting
- The Lone Ranger automation reaches the correct search result and Roku playback option.
- Johnny Carson automation reaches the intended 1972–1992 show listing and Roku Channel option rather than the similar incorrect listing.

### Fire tablet interface
- The tablet can present a much simpler UI than the Roku remote.
- Large, single-purpose controls are practical.
- Mom does not need the Roku mobile app installed on the Fire tablet.

### Local bridge
- A PowerShell HTTP bridge running on a Windows computer successfully accepts commands from the Fire tablet and translates them into Roku ECP actions.
- End-to-end path is working:

```text
Fire tablet -> dashboard -> Windows bridge -> Roku -> playback
```

### GitHub / version control
- Repository established: `Kohlbek/mom-dashboard-test`
- GitHub Pages deployment was successfully tested.
- GitHub is now being used to preserve project code and documentation.

## Problems encountered and what they taught us

### Amazon Alexa
Initial Alexa/Roku work did not provide the desired direct, reliable experience. This helped push the design toward a custom one-touch interface rather than making Mom interact with Alexa/Roku complexity.

### Fire tablet Roku app
The Amazon Appstore does not provide the official Roku app, so the project moved toward a browser-based interface.

### GitHub Pages to Roku
The public HTTPS dashboard could load on the tablet, but direct browser-to-local-Roku control was not a reliable architecture because of browser security boundaries between HTTPS/public pages and HTTP/private-network endpoints.

Result: introduce a local bridge.

### Windows HTTP listener permissions
PowerShell's `HttpListener` initially failed with `Access is denied`. The bridge was subsequently run successfully and began receiving tablet requests.

### Roku unreachable on network
During bridge testing, requests reached the bridge but the bridge reported `Unable to connect to the remote server`.

Direct testing showed:

```text
Invoke-WebRequest http://192.168.0.210:8060/query/device-info
```

failed, and ping returned `Destination host unreachable`.

The Roku was found to be on a different Wi-Fi band/network path. After correcting its Wi-Fi connection:
- Roku ECP returned HTTP 200.
- Ping succeeded with roughly 8–13 ms latency.
- Both Fire-tablet dashboard buttons worked end-to-end.

This is the first concrete reliability issue to preserve for later architecture decisions.

## Current Prototype Dependencies

The working TV prototype currently depends on:

1. Fire tablet being connected to Mom's home network.
2. Windows bridge computer being powered on, awake, and connected to the network.
3. `roku-bridge.ps1` running.
4. Roku TV being reachable at its reserved IP address.
5. Roku ECP / mobile-app control being enabled.
6. Roku Search/results layout remaining compatible with the tested navigation sequence.

## Current Risk / Fragility

### High priority
- **Windows computer dependency:** bridge disappears if laptop sleeps, restarts, disconnects, or script stops.
- **Network segmentation/band behavior:** Roku became unreachable while apparently connected to Wi-Fi.

### Medium priority
- **Roku UI navigation dependency:** scripts currently rely on deterministic UI navigation. Roku search-result changes could break sequences.
- **Local IP assumptions:** DHCP reservations reduce but do not eliminate network configuration risks.

### Lower priority for prototype
- Dashboard aesthetics and polish.
- Additional entertainment choices.
- More sophisticated error messages.

## Recommended next phase: Reliability before expansion

Do not immediately add many new dashboard features.

First turn the successful two-button prototype into a dependable appliance-like system.

Priority work:

1. Preserve/back up the currently working scripts.
2. Make bridge startup automatic.
3. Prevent the bridge host from sleeping when Mom needs it.
4. Test recovery after Windows restart.
5. Test recovery after Roku restart/power interruption.
6. Test tablet reconnect after Wi-Fi interruption.
7. Determine why the Roku Wi-Fi-band change caused loss of reachability.
8. Decide whether to replace the Windows laptop with a small always-on local controller.
9. Add simple success/failure feedback and possibly retry logic.
10. Only after reliability is acceptable, expand to other dashboard functions.

## Broader Aging Parent Support Project

The TV dashboard is one experiment inside a larger Human + AI aging-parent support project.

Potential future modules include:
- Daily `MOM — TODAY` printed sheet
- One-touch entertainment
- Family updates/photos
- Simplified current-news summaries
- Calendar/appointment reminders
- Emergency/medical information binder
- Household inventory reminders
- Shopping assistance
- Bills/admin support for caregiver
- Smart-light routines
- Front-door camera access
- Coffee routine with appropriate safety constraints

These should be introduced incrementally and evaluated against the project objective: increase Mom's independence and quality of life while reducing repetitive caregiver workload without replacing appropriate human judgment or care.

## Measurement ideas to retain

For each intervention, track where practical:
- caregiver time before/after
- repeated problems eliminated
- number of support calls/interventions
- Mom's ability to complete the task independently
- Mom's frustration
- caregiver stress
- mistakes/failures
- financial savings or avoided unnecessary purchases
- setup/maintenance complexity
- AI errors vs. human errors
- whether saved support time becomes higher-quality family interaction

A particularly useful project metric is **time converted**: support/troubleshooting time converted into ordinary family conversation or other higher-value interaction.

## Milestone Log

**September 2026 — Prototype 1:**
- Roku ECP proven.
- Lone Ranger PowerShell automation proven.
- Johnny Carson PowerShell automation proven.
- GitHub repository and Pages site established.
- Fire-tablet dashboard built.
- Local Windows bridge built.
- Fire tablet -> bridge -> Roku communication proven.
- The Lone Ranger one-touch playback passed.
- Johnny Carson one-touch playback passed.

**Current milestone:** Freeze and preserve Prototype 1, then begin reliability hardening.
