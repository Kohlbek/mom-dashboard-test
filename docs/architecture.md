# LEO Household Support Platform — Architecture

_Updated September 22, 2026. Public overview; private configuration and care details are maintained separately._

## Purpose

LEO explores practical support for an older adult and her caregivers. It aims to make routine tasks easier, preserve independence, and keep essential household operations understandable to a backup caregiver. Mom-facing controls remain simple; caregiver diagnostics and configuration remain separate.

## Prototype topology

```text
Mom-facing tablet and daily printed sheet
             |
             v
    Local household controller
       |       |         |
       v       v         v
    TV UI   printing   caregiver Admin
                         |
                    device health,
                    camera images,
                    inventory state
```

The current controller is a Windows prototype host on the home network. The Mom-facing dashboard uses a local bridge for TV actions. A scheduled print produces the daily sheet. The caregiver Admin page shows status and runs explicit maintenance actions. The public website is a demonstration interface, not the live path to household devices.

## Working components

- **Mom-facing dashboard:** large, direct controls for tested entertainment choices, weather, date, family updates, and lighting.
- **MOM — TODAY:** a readable one-page morning print that has completed unattended scheduled production. Its displayed inventory still needs to read the shared state file.
- **Caregiver Admin:** system checks, printing controls, inventory refresh, TV reachability, lighting status, phone network-presence clue, and read-only garage opener health. Presence and network reachability are observations, not safety or door-position guarantees.
- **Visual inventory:** a camera request and configuration-driven processor produce shared inventory state. Item-specific sensing rules and human-confirmed baselines are used where appropriate; uncertain observations must remain uncertain.
- **Camera Admin v1:** a separate local caregiver page lists Blink cameras, available metadata, saved images, cached-thumbnail sync, and targeted snapshot requests. A full sync and one targeted refresh were tested on the Windows host. Download time is not proof of capture time. The backend retains the previous image and reports an error if retrieval fails; that failure path has not yet been tested on the Windows host. Camera arm/disarm and privacy controls are not exposed.
- **Garage opener health:** a read-only local diagnostic response supports connectivity and device-health reporting. It does not report physical door position. Camera-based OPEN / CLOSED / CAN'T TELL detection remains future work.

## Data and control boundaries

The inventory processor should be the sole writer of the shared state file. Admin already reads it; the daily sheet and Mom-facing dashboard are pending consumers. Live device commands and authentication remain under control of the local host. Caregiver actions that could spend money, change care decisions, or notify people require human approval.

Device addresses, identifiers, precise camera locations, private images, account details, medical information, and credentials are intentionally omitted from this public overview. The current Blink authentication file is configured under a potentially synced Desktop folder; moving it to protected, nonsynced storage is pending. Public source and documentation must not be treated as the live configuration or as a secret store.

## Continuity requirement

The platform must remain useful if the primary caregiver is unavailable. The planned **Normal → Emergency → Continuity** model will surface essential information, immediate and follow-up tasks, document locations, and a simplified backup-caregiver entry point. These modes are design work, not deployed emergency detection or automatic event handling.

## Design lessons

1. Reduce steps for the person being supported before adding another interface.
2. Structure physical storage and routines so observations are repeatable.
3. Use different sensing models for different items and preserve human-confirmed ground truth.
4. Distinguish network reachability, image download time, capture freshness, and real-world state.
5. Keep diagnostic observation separate from device control.
6. Record failed experiments and outliers; they reveal reliability requirements.

See [project status](project-status.md) for tested milestones and remaining work. Detailed operational records are kept privately.
