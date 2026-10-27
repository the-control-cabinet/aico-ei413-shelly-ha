# Changelog

## [1.2.0] - 2026-10-26
### Added
- Benefit and comparison versus Aico's Ei1000G SmartLINK Gateway (cost, local vs cloud, HA integration, two-way control, detail trade-off).
- Deployed alert automations: Fire/CO/Fault active and cleared, interface offline and back online (`config/homeassistant/automations.yaml`). Replaces the examples file.
- Tests 11-13: alert notifications, offline alert by unplugging the supply, no alarm on power restore.
- Entity IDs and input mapping (0 Fire, 1 CO, 2 Fault, 3 spare), Shelly Cloud off, issue 6 (the editor's Run button skips triggers).

## [1.1.0] - 2026-10-26
### Changed
- Corrected the power description: the Ei413 is **supplied with** 11-30 V DC (here a 12 V DC adapter) and doesn't provide a 12 V rail.
- Corrected the outputs: three volt-free relays with NC/C/NO contacts (wired C-NO), not "SW terminals".
- Product name corrected to Shelly Plus i4 DC; commissioning date corrected to August 2026.
### Added
- Plus 1 fail-safes: output Detached, power-on OFF, auto-off 3 s (tested). i4 DC inputs confirmed Switch, MQTT off on both.
- Shelly Plus 1 role documented: a dry-contact trigger into the Ei413 so HA can sound all alarms.
- House coding procedure, Shelly input type (Switch) and Plus 1 settings, device classes.
- Points list, wiring schedule, commissioning and test record, design limitations (unsupervised C-NO wiring, shared supply).
- Example automations (alerts, interface offline, guarded trigger script), portfolio links, video section.

## [1.0.0] - 2026-08
### Added
- Ei413 wired to the Shelly Plus i4 DC (Fire, CO, Fault) and Shelly Plus 1; both in Home Assistant.
- Polarity, terminal and pairing lessons.
