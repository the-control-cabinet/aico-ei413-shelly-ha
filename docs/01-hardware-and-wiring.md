# 01 · Hardware and Wiring

[← Back to README](../README.md)

## Equipment

| Item | Model | Role |
|---|---|---|
| Fire & CO alarms (×2) | Aico **Ei3030** multi-sensor (optical, heat, electrochemical CO), mains with lithium back-up | Detection |
| RadioLINK modules (×2) | Aico **Ei3000MRF**, one per Ei3030 | Wireless interconnection |
| Interface | Aico **Ei413** RadioLINK Panel Interface Module | RadioLINK ↔ volt-free relays and input |
| Input device | **Shelly Plus i4 DC** | Reads the Fire, CO and Fault relays |
| Output device | **Shelly Plus 1** | Dry-contact relay to trigger the Ei413 |
| Power | 12 V DC plug-in adapter | Supplies the Ei413 and both Shellys |
| Terminations | Wago lever connectors | Common 0 V and +12 V distribution |
| Location | Control room | Both Shellys and the Ei413 |

## Ei413: what it provides and what it needs

| Item | Detail (from the Aico documentation) |
|---|---|
| Supply | **11-30 V DC into its Vin terminals**. It is powered *by* a panel or an external supply; it doesn't provide a supply |
| Outputs | Three relays (Fire, CO, Fault), each with **NC / C / NO** volt-free contacts, rated 30 V 1 A |
| Fire relay | NO closes when an alarm **or test** message is received from a house-coded alarm |
| Input | A volt-free switched input from the "panel". Closing it makes the Ei413 transmit a RadioLINK alarm, sounding all alarms |
| Coding | Must be house-coded to the RadioLINK alarms ([docs/02](02-house-coding-and-shelly-setup.md)) |

## Power distribution

```
12 V DC adapter (+) ──┬── Ei413 Vin (+)
                      ├── Shelly Plus i4 DC supply (+)
                      └── Shelly Plus 1 12 V DC supply (+)
12 V DC adapter (−) ──┬── Ei413 Vin (−)
                      ├── Shelly Plus i4 DC supply (−)
                      └── Shelly Plus 1 DC supply (−)
```

- Distributed through Wago connectors, so no screw terminal carries more than two conductors.
- **Polarity matters on every device.** The Plus 1 didn't power up until + and − were on its correct DC supply terminals (see [issue 1](05-issues-and-lessons-learned.md)).

## Wiring schedule

| From | Terminals | To | Terminal | Purpose |
|---|---|---|---|---|
| Ei413 Fire relay | C, NO | Shelly Plus i4 DC | Input 0 | Fire → HA |
| Ei413 CO relay | C, NO | Shelly Plus i4 DC | Input 1 | CO → HA |
| Ei413 Fault relay | C, NO | Shelly Plus i4 DC | Input 2 (input 3 spare) | Fault → HA |
| Shelly Plus 1 relay | dry contact (O, I) | Ei413 input | volt-free input | HA → sound all alarms |
| 12 V DC adapter | +, − | Ei413 Vin, i4 DC, Plus 1 | DC supply | Power |

### Why C-NO

C-NO gives a simple "contact closes = alarm" input, matching how the Ei413 signals. The trade-off is that a broken wire is indistinguishable from "no alarm". Commercial fire interfaces use C-NC or end-of-line resistors so a wiring fault is detected. That's recorded as a [limitation](../README.md#10-design-limitations) and is on the roadmap.

Each Ei413 relay contact is volt-free and rated 30 V 1 A. A Shelly input draws only a few milliamps, well within the rating.
