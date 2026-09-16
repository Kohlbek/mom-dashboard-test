# Aging Parent Support / Mom Dashboard — Project Status

_Last updated: September 15, 2026_

## Status: Prototype 1 Working — Basic Windows Reliability Hardened; Dashboard Capability Expansion Begun

The first end-to-end Mom Dashboard prototype is operational. The initial Windows-host reliability phase has passed, and the dashboard has now begun expanding beyond TV control.

From the Fire tablet, one button can turn on/control the Roku TV, find the intended program, choose the correct Roku result/provider, and begin playback.

Both initial program buttons have passed end-to-end testing:

- **The Lone Ranger — PASS**
- **Johnny Carson — PASS**

The bridge has also passed automatic-start, closed-lid, and extended unattended-idle testing while the Acer laptop is plugged in.

On September 15, the dashboard was expanded with a dynamic greeting/date, weather information, and a Family Updates page. The first real family update was successfully displayed.

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
- Dashboard now automatically displays a time-appropriate greeting: Good morning / Good afternoon / Good evening, Mom.
- Dashboard automatically displays the current date.
- Weather information is now displayed for Omaha, NE; Yardley, PA; and Nashville, TN, including current temperature, condition, high, and low.
- Weather degree-symbol encoding was corrected during testing.
- A large Family Updates button and dedicated Family Updates page have been added.

### Family Updates — first content test
The Family Updates capability has passed its first manual content test.

- Dedicated page includes family-member cards and a large Back to Home control.
- Grandson label was personalized to **Danny**.
- First real update displayed successfully: **Danny went back to college.**
- Dashboard/bridge was restarted through the existing Windows Scheduled Task without rebooting the computer, and the update appeared correctly on the Fire tablet.

**Current interpretation:** the presentation layer works. The next useful experiment is to separate family-update content from `roku-bridge.ps1`, so routine content changes do not require editing the working dashboard/Roku code. This creates a foundation for a later Human + AI workflow in which a family update can be provided to ChatGPT and propagated to Mom's dashboard with minimal caregiver effort.

### Local bridge
- A PowerShell HTTP bridge running on a Windows computer successfully accepts commands from the Fire tablet and translates them into Roku ECP actions.
- The bridge also serves the current Mom Dashboard interface.
- End-to-end TV path is working:

```text
Fire tablet -> dashboard -> Windows bridge -> Roku -> playback
```

### Windows bridge reliability hardening
The current Acer bridge host has been configured and tested for unattended operation while plugged in.

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

**Result:** basic Windows-host unattended reliability is considered passed for Prototype 1.

### Prototype preservation / restart workflow
- A frozen local backup of the known-good bridge was created as `roku-bridge-PROTOTYPE1.ps1` before capability expansion.
- `Test-Path` confirmed the backup exists.
- Dashboard changes can be activated without a Windows reboot by stopping and restarting the `Mom Dashboard Roku Bridge` Scheduled Task.

### GitHub / version control
- Repository established: `Kohlbek/mom-dashboard-test`
- GitHub Pages deployment was successfully tested.
- GitHub is being used to preserve project documentation.

## Problems encountered and what they taught us

### Amazon Alexa
Initial Alexa/Roku work did not provide the desired direct, reliable experience. This helped push the design toward a custom one-touch interface rather than making Mom interact with Alexa/Roku complexity.

### Fire tablet Roku app
The Amazon Appstore does not provide the official Roku app, so the project moved toward a browser-based interface.

### GitHub Pages to Roku
The public HTTPS dashboard was tested earlier. The current working prototype serves the dashboard through the local Windows bridge, which also performs Roku control.

### Windows HTTP listener permissions
PowerShell's `HttpListener` initially failed with `Access is denied`. The bridge was subsequently run successfully and began receiving tablet requests.

### Roku unreachable on network
During bridge testing, requests reached the bridge but the bridge reported `Unable to connect to the remote server`.

Direct testing showed the Roku temporarily unreachable. Earlier testing also found a Wi-Fi band/network-path issue; after correcting connectivity, ECP worked again.

During the September 15 reboot reliability test, the bridge itself restarted successfully but the first Fire-tablet attempt reported `TV didn't respond`. The Roku still showed IP `192.168.0.210` and the expected Wi-Fi band. ARP resolved `.210` to MAC `68-c8-c0-59-ea-21`, and a subsequent TCP test to Roku ECP port 8060 succeeded. A second Johnny Carson attempt then worked without further intervention.

**Current interpretation:** preserve this as a startup/network reconnection timing observation rather than changing the known-good bridge. The system recovered on its own.

### Laptop lid behavior
The Acer power plan initially did not display the lid-close setting in the normal power query. The hidden setting was exposed and showed AC and battery lid-close behavior were both set to Sleep. Changing only the AC lid-close action to Do nothing allowed the bridge to continue operating with the lid closed.

### Character encoding during dashboard expansion
Early dashboard edits produced malformed emoji/degree characters. HTML entities and subsequent corrections produced proper Fire-tablet display. This is a minor implementation lesson for future dashboard content.

## Current Prototype Dependencies

The working prototype currently depends on:

1. Fire tablet being connected to Mom's home network.
2. Windows bridge computer being powered on, plugged in, and connected to the network.
3. Windows user logon occurring so the current scheduled-task trigger can launch the bridge.
4. `roku-bridge.ps1` running through the scheduled task.
5. Roku TV being reachable at its reserved IP address.
6. Roku ECP / mobile-app control being enabled.
7. Roku Search/results layout remaining compatible with the tested navigation sequence.
8. Any external data source used by the weather implementation remaining reachable.

## Current Risk / Fragility

### High priority
- **Network reconnection/startup timing:** immediately after one Windows reboot, the bridge was up before the Roku path was ready; the first request failed and a later request succeeded automatically.
- **Windows laptop dependency:** reliability is substantially improved, but the prototype still depends on a consumer laptop, Windows logon, power, and Wi-Fi.

### Medium priority
- **Roku UI navigation dependency:** scripts currently rely on deterministic UI navigation. Roku search-result changes could break sequences.
- **Local IP assumptions:** DHCP reservations reduce but do not eliminate network configuration risks.
- **Content maintenance:** Family Updates are currently hard-coded into the bridge/dashboard and therefore still require a code edit plus bridge restart.

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

## Capability Expansion — Begun September 15, 2026

The project has moved beyond the original two-button entertainment proof of concept while preserving its working Roku sequences.

Completed initial capability tests:

1. **Contextual greeting:** automatic morning/afternoon/evening greeting addressed to Mom.
2. **Date:** automatic current date on the home screen.
3. **Weather:** dashboard weather cards successfully displayed for Omaha, Yardley, and Nashville.
4. **Family Updates UI:** dedicated page accessible from the home dashboard.
5. **Family Updates content:** first real update successfully displayed for Danny.

Recommended next Family Updates experiment:

- Move update content into a small separate data file rather than embedding it in `roku-bridge.ps1`.
- Keep the proven Roku/navigation code isolated from routine family-content changes.
- After that works, test a lower-friction update workflow such as: family/caregiver provides update -> AI organizes/simplifies -> dashboard data changes -> Mom sees update.
- Initially retain human approval before family information is published to Mom's dashboard.

Smart-light testing remains queued for when the bulbs arrive.

## Broader Aging Parent Support Project

The dashboard is one experiment inside a larger Human + AI aging-parent support project.

Potential modules include:
- Daily `MOM — TODAY` printed sheet
- One-touch entertainment — **prototype working**
- Family updates/photos — **initial text capability working**
- Weather/contextual daily information — **initial dashboard capability working**
- Simplified current-news summaries
- Calendar/appointment reminders
- Emergency/medical information binder
- Household inventory reminders
- Shopping assistance
- Bills/admin support for caregiver
- Smart-light routines — **hardware pending**
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

**September 15, 2026 — Dashboard Capability Expansion:**
- Known-good `roku-bridge.ps1` backed up locally as `roku-bridge-PROTOTYPE1.ps1` before expansion.
- Dynamic Good morning/afternoon/evening, Mom greeting added and verified on Fire tablet.
- Automatic current date added and verified.
- Weather cards added and displayed for Omaha, Yardley, and Nashville.
- Weather character-encoding issue corrected.
- Family Updates button/page added and verified.
- Family page personalized with Danny rather than generic Grandson label.
- First real family update successfully displayed: Danny went back to college.
- Scheduled-task stop/start procedure successfully used to reload dashboard changes without rebooting Windows.

**Current milestone:** the known-good entertainment/reliability baseline remains preserved, while the dashboard has begun functioning as a broader information hub. Next planned software experiment is separating Family Updates content from bridge code; smart-light testing can begin when hardware arrives.
