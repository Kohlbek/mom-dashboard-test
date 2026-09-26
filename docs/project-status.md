# LEO Household Support Platform — Project Status

_Updated September 26, 2026. Public overview; private configuration, household details, and test logs are maintained separately._

## Contents

- [Executive summary](#executive-summary)
- [Tested milestones](#tested-milestones)
- [Current limits](#current-limits)
- [Next work](#next-work)
- [Bibliography](#bibliography)

## Executive summary

LEO is evolving from a basic Mom-facing dashboard into a household support platform combining simple daily assistance with caregiver operations, household-device observation, and continuity planning. The Mom Dashboard and MOM — TODAY remain familiar interfaces; caregiver tools support the local host.

The local Live View player has been updated and deployed after investigation found a stream-framing issue: TCP reads can return partial data, but the prior implementation treated a short read as a complete frame. The player now reads exact protocol lengths, checks payload bounds, waits for its local consumer before starting the upstream feed, and performs bounded cleanup. Five offline regression tests passed. Visible moving video was confirmed in a development test; later production tests recorded decoded frames. Reliability is still being checked across cameras and sessions, and decoded frames do not prove visible presentation or camera capture freshness.

## Tested milestones

| Area | Status | Evidence and limit |
|---|---|---|
| Mom-facing dashboard | Working prototype | Core playback actions passed local end-to-end testing; availability depends on the host and connected devices. |
| Daily sheet | Scheduled test pass | A one-page sheet printed on schedule. Some data integration remains pending. |
| Local lighting | Tested | Status, control, and one triggered routine were demonstrated. |
| Visual inventory | Tested for selected items | A configured processor produces shared state; uncertain observations require confirmation. |
| Caregiver Admin | Tested controls | Status checks and explicit maintenance actions were demonstrated. |
| Camera Admin | Production host test pass | Camera summary, cached-image sync, targeted refresh, favorites, attention filter, image comparison, and navigation feedback were exercised. Download time does not prove capture freshness. |
| Live View player | Deployed; functional tests passed | Five offline regressions passed. A development session yielded 258 decoded frames over 12.4 seconds and the user confirmed visible moving video. Two later production sessions yielded 489 frames over 40 seconds and 478 frames over 24.6 seconds; the latter was closed by the user. Decoding does not prove what appeared on screen or that frames were freshly captured. |
| Device health | Read-only test pass | Diagnostics report health but do not establish physical state. |

## Current limits

- Prototype availability depends on the local host, network, and connected devices.
- Camera framing and image quality can affect inventory inference; unknown results remain unknown.
- A downloaded image can still be an older camera capture. Camera Admin shows local download time and labels capture freshness as unverified.
- Camera Admin retains a previously usable image on retrieval failure in backend tests; the failure display still needs a live host test.
- Live View now has a tested stream-handling fix and production deployment, but broad camera/session reliability remains under observation.
- Shared-state consumers and the complete Mom-facing inventory presentation remain unfinished.

## Next work

1. Validate camera-based state detection after obtaining suitable reference views.
2. Complete shared-state integration for the daily sheet and dashboard.
3. Test Camera Admin failure behavior on the production host and continue bounded cross-camera Live View reliability checks.
4. Improve operational resilience, caregiver handoff materials, and reliability measurement.

## Bibliography

- [BlinkPy PR #1232 — readexactly proposal](https://github.com/fronzbot/blinkpy/pull/1232)
- [BlinkPy Issue #1181 — short-payload termination report](https://github.com/fronzbot/blinkpy/issues/1181)

See [architecture](architecture.md) for the public system overview.
