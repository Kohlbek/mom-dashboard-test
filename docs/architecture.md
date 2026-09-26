# LEO Household Support Platform — Architecture

_Updated September 26, 2026. Public overview; private setup instructions and household details are maintained separately._

## Purpose

LEO explores simple household support for an older adult and practical diagnostics for caregivers. The person-facing interface and caregiver tools serve different needs.

## Prototype layout

The current prototype uses a local controller for a simple dashboard, scheduled daily printing, and a caregiver Admin page. The public website demonstrates the interface; it is not the live route to household devices.

## Components

- **Dashboard:** direct controls for tested entertainment and household actions.
- **Daily sheet:** one-page scheduled print. Some displayed data still needs to consume shared state.
- **Caregiver Admin:** system status, printing, inventory refresh, and read-only device health checks.
- **Visual inventory:** a configured processor requests images and writes shared inventory state. Item-specific sensing rules and human confirmation remain necessary.
- **Camera Admin:** a separate local caregiver page displays camera status and saved images. It supports cached-thumbnail sync, targeted snapshot requests, a health summary, favorites, an attention filter, and comparison of previous and current downloads. A new download does not establish a new capture. The backend preserves the last usable image after a retrieval failure; live host validation of that path is pending. Camera settings and arm/disarm are outside this page.
- **Live View:** the local player workflow is deployed separately from browser video rendering. It uses exact-length protocol reads to handle partial TCP reads, bounds payload handling, waits for the local consumer before starting the upstream feed, and performs bounded cleanup. The installed BlinkPy library was not changed. Five offline regression tests passed. A development test produced decoded video and the user confirmed visible moving playback; later production tests recorded decoded frames. Results vary by session, and decoding does not prove visible presentation or capture freshness. Cross-camera reliability remains under observation.
- **Device diagnostics:** read-only health observations do not necessarily establish physical state.

## Data and control boundaries

The inventory processor is intended to be the sole writer of shared inventory state. Additional displays and the daily sheet remain pending readers. Device commands and account authentication stay on the local host. Public source and documentation contain no live credentials or private device configuration.

## Design lessons

1. Keep the person-facing controls simple.
2. Preserve uncertainty in image interpretation and physical-state checks.
3. Distinguish download time from camera capture freshness.
4. Preserve a usable prior image and show errors when retrieval fails.
5. Treat decoded video frames as evidence of decoder output, not proof of visible presentation or camera freshness.

See [project status](project-status.md) for tested milestones and remaining work.
