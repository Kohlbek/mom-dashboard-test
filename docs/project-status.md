# LEO Household Support Platform — Project Status

_Updated September 22, 2026. Public overview; private operational records contain the configuration and test detail._

## Tested milestones

| Area | Status | Evidence and limit |
|---|---|---|
| Mom-facing TV dashboard | Working prototype | One-touch playback and local bridge have passed end-to-end tests. Reliability still depends on the host, network, and TV interface. |
| Daily morning sheet | Unattended production pass | The expanded one-page sheet printed on schedule. Inventory values on the sheet still need to use shared state. |
| Local lighting | Pass | Local status/control and a motion-triggered routine were demonstrated. |
| Generalized visual inventory | Pass for tested items | A configuration-driven processor produces shared state from requested camera images. Product-specific models and human confirmation remain necessary. |
| Caregiver Admin | Pass for tested controls | Printing, inventory refresh, TV reachability, lighting status, phone network-presence clue, and garage opener health were demonstrated. |
| Camera Admin v1 | Windows host test pass | Camera cards, cached-image sync, and one targeted refresh worked. Download time does not verify camera capture time; retrieval-failure display still needs a live failure test. |
| Garage opener health | Pass | Read-only diagnostics report system health. Door position is not available from that diagnostic response. |

The Admin lighting-address display was corrected after a PowerShell variable-name collision. Camera Admin does not provide arm/disarm or privacy-setting controls; garage diagnostics do not provide door controls.

## Current limits

- The Windows computer remains a prototype controller. Restarts, network changes, and changes to the TV interface can affect availability.
- Camera framing, lighting, occlusion, image compression, and item movement can affect inventory inference. Unknown results must not be presented as confirmed stock levels.
- A newly downloaded Blink image is not necessarily a newly captured frame. Capture freshness is explicitly unverified unless stronger metadata is available.
- The Blink authentication file is currently configured under a potentially synced Desktop path. Protected, nonsynced storage and renewal handling for two-factor authentication remain pending.
- The complete Mom-facing inventory display and shared-state integration into the printed sheet remain unfinished.

## Priority roadmap

1. Build and validate camera-based garage door OPEN / CLOSED / CAN'T TELL detection after mounting and collecting reference views.
2. Develop **Emergency Continuity & Caregiver Handoff**: immediate response, hospitalization and post-event checklists, progress tracking, document/contact locations, and a simple backup-caregiver starting point that works if the primary caregiver is unavailable. No automatic emergency trigger or mode switching has been deployed.
3. Connect the daily print and Mom-facing dashboard to the shared inventory state; keep the generalized processor as its sole writer.
4. Validate Camera Admin failure behavior and improve Blink authentication storage and renewal.
5. Migrate scripts progressively to one private local device registry; improve item-specific inventory detection where it reduces useful work.
6. Measure caregiver interventions, repeated problems, failures, and time saved. Explore human-reviewed bill/mail and shopping workflows, family/news data, and a simpler tablet interface.
7. Investigate read-only portable-generator diagnostics when running and read-only OBD-II diagnostics for the family car. The car's factory Wi-Fi was found to be a subscription hotspot rather than a way to join the home network.

Longer-term care, legal, appointment, transportation, and social-support experiments remain separate work. No private care or account details belong in this public repository.

See [architecture](architecture.md) for the system overview.
