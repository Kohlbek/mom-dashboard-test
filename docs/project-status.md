# Aging Parent Support / Mom Dashboard — Project Status

_Last updated: September 15, 2026_

## Status: Prototype 1 Working — Basic Windows Reliability Hardened

The first end-to-end Mom Dashboard prototype is operational, and the initial Windows-host reliability phase has passed.

From the Fire tablet, one button can turn on/control the Roku TV, find the intended program, choose the correct Roku result/provider, and begin playback.

Both initial program buttons have passed end-to-end testing:

- **The Lone Ranger — PASS**
- **Johnny Carson — PASS**

The bridge has also now passed automatic-start, closed-lid, and extended unattended-idle testing while the Acer laptop is plugged in.

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

### Windows bridge reliability hardening
The current Acer bridge host has now been configured and tested for unattended operation while plugged in.

- Windows Scheduled Task created: `Mom Dashboard Roku Bridge`.
- The task successfully launches `roku-bridge.ps1` at Windows logon.
- The bridge responded successfully at `/test` when launched by the scheduled task.
- After a Windows restart, the bridge came back automatically without manually launching PowerShell.
- AC sleep timeout changed from 15 minutes to **Never**.
- AC hibernate timeout changed from approximately 12 hours to **Never**.
- Battery sleep/hibernate behavior was intentionally left unchanged.
- Acer's hidden `Lid close action` setting was exposed and identified as the cause of the bridge stopping when the lid was closed.
- AC lid-close action changed from **Sleep** to **Do nothing**.
- Battery lid-close action remains **Sleep**.
- With the laptop plugged in and lid closed, The Lone Ranger launched successfully from the Fire tablet.
- After more than one hour plugged in, lid closed, and untouched, the Fire-tablet control still worked successfully.

**Result:** basic Windows-host unattended reliability is now considered passed for Prototype 1.

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

Direct testing showed the Roku temporarily unreachable. Earlier testing also found a Wi-Fi band/network-path issue; after correcting connectivity, ECP worked again.

During the September 15 reboot reliability test, the bridge itself restarted successfully but the first Fire-tablet attempt reported `TV didn't respond`. The Roku still showed IP `192.168.0.210` and the expected Wi-Fi band. ARP resolved `.210` to MAC `68-c8-c0-59-ea-21`, and a subsequent TCP test to Roku ECP port 8060 succeeded. A second Johnny Carson attempt then worked without further intervention.

**Current interpretation:** preserve this as a startup/network reconnection timing observation rather than changing the known-good bridge. The system recovered on its own.

### Laptop lid behavior
The Acer power plan initially did not display the lid-close setting in the normal power query. The hidden setting was exposed and showed AC and battery lid-close behavior were both set to Sleep. Changing only the AC lid-close action to Do nothing allowed the bridge to continue operating with the lid closed.

## Current Prototype Dependencies

The working TV prototype currently depends on:

1. Fire tablet being connected to Mom's home network.
2. Windows bridge computer being powered on, plugged in, and connected to the network.
3. Windows user logon occurring so the current scheduled-task trigger can launch the bridge.
4. `roku-bridge.ps1` running through the scheduled task.
5. Roku TV being reachable at its reserved IP address.
6. Roku ECP / mobile-app control being enabled.
7. Roku Search/results layout remaining compatible with the tested navigation sequence.

## Current Risk / Fragility

### High priority
- **Network reconnection/startup timing:** immediately after one Windows reboot, the bridge was up before the Roku path was ready; the first request failed and a later request succeeded automatically.
- **Windows laptop dependency:** reliability is substantially improved, but the prototype still depends on a consumer laptop, Windows logon, power, and Wi-Fi.

### Medium priority
- **Roku UI navigation dependency:** scripts currently rely on deterministic UI navigation. Roku search-result changes could break sequences.
- **Local IP assumptions:** DHCP reservations reduce but do not eliminate network configuration risks.

### Lower priority for prototype
- Dashboard aesthetics and polish.
- Additional entertainment choices.
- More sophisticated error messages.

## Reliability Phase — Completed and Remaining

Completed September 15, 2026:

1. Preserve the working two-button prototype.
2. Make bridge startup automatic at Windows logon.
3. Prevent AC sleep and hibernation.
4. Keep bridge running with the laptop lid closed while plugged in.
5. Verify scheduled-task bridge startup after Windows restart.
6. Verify closed-lid Fire-tablet control.
7. Verify more than one hour of unattended closed-lid operation.

Remaining reliability experiments worth doing before production deployment:

1. Test recovery after Roku restart/power interruption.
2. Test tablet reconnect after Wi-Fi interruption.
3. Revisit network/band behavior if the Roku becomes unreachable again.
4. Consider simple retry logic for the brief post-reboot Roku/network readiness window.
5. Eventually decide whether to replace the Windows laptop with a small always-on local controller.
6. Add simple success/failure feedback only when it provides practical value.

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

**September 15, 2026 — Prototype 1 Reliability Baseline:**
- Bridge automatic startup at Windows logon proven.
- Bridge recovery after Windows restart proven.
- AC sleep disabled.
- AC hibernation disabled.
- AC lid-close action changed to Do nothing; battery behavior retained.
- Closed-lid dashboard operation proven.
- More-than-one-hour unattended closed-lid operation proven.
- Brief post-reboot Roku/network readiness delay observed; system recovered without intervention.

**Current milestone:** Prototype 1 known-good baseline frozen with basic Windows-host reliability hardening complete. Continue targeted reliability experiments or begin the next Mom Dashboard capability without casually changing the proven Roku navigation sequences.
