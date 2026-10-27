# 03 · Home Assistant and Points List

[← Back to README](../README.md) · Data: [`data/points-list.csv`](../data/points-list.csv)

## Points list

**HA devices** (area: Control Room)

| HA device | Hardware | Entities |
|---|---|---|
| Aico Smoke Alarm - Status | Shelly Plus i4 DC | 11 (3 alarm inputs + configuration and diagnostics) |
| Aico Smoke Alarm - Trigger | Shelly Plus 1 | 10 (relay, unused Input 0, configuration and diagnostics) |

**Alarm and command points**

| Point | Source | Contact | Shelly device / channel | Input type | HA entity (name) | Device class (Show as) | Normal | Active |
|---|---|---|---|---|---|---|---|---|
| Fire | Ei413 Fire relay | C-NO | Plus i4 DC / input 0 | Switch | Fire Alarm | smoke | Clear | Detected |
| CO | Ei413 CO relay | C-NO | Plus i4 DC / input 1 | Switch | CO Alarm | carbon_monoxide | Clear | Detected |
| Fault | Ei413 Fault relay | C-NO | Plus i4 DC / input 2 | Switch | Smoke Alarm Fault | problem | OK | Problem |
| Sound all alarms | HA | Plus 1 dry contact | Plus 1 / relay | n/a | Aico Smoke Alarm - Trigger | switch | Off | On |

**Interface health points** (diagnostic entities, values at time of writing)

| Point | Device | Value seen | Use |
|---|---|---|---|
| Availability | both | Online | Alert if unavailable: covers supply loss, Wi-Fi or device failure |
| Wi-Fi signal strength | i4 DC / Plus 1 | −33 dBm / −26 dBm | Both excellent (better than −60 dBm) |
| Uptime | both | 2 days | Unexpected resets show up as uptime dropping to zero |
| Internal temperature | Plus 1 | 47.3 °C | Normal for a Plus 1 in an enclosure; trend it if the enclosure is sealed |
| Firmware | both | Up to date | Maintenance |
| Cloud | both | Off | HA uses the local API only |

## Why device classes matter

The device class decides how HA words and displays a state ("Detected / Clear" rather than "On / Off") and how it groups the entity. On a BMS this is the equivalent of setting the point type and alarm text correctly at commissioning, so an operator sees "Fire" and not "Input 1 = 1".

## Behaviour summary

| Event | Ei413 | Shelly | HA |
|---|---|---|---|
| Any alarm detects smoke or heat, or its test button is pressed | Fire relay C-NO closes | i4 DC Fire input On | Smoke sensor **Detected** |
| Any alarm detects CO | CO relay closes | CO input On | CO sensor **Detected** |
| An alarm reports low battery or a contaminated chamber | Fault relay closes | Fault input On | Problem sensor **Problem** |
| HA turns the Plus 1 on | Input sees a closed contact → transmits a RadioLINK alarm | Plus 1 relay closed | All alarms sound |
| Supply lost | Interface down (alarms unaffected) | Both Shellys offline | Entities **Unavailable** |

**Entity IDs**

| Entity | ID |
|---|---|
| Fire Alarm | `binary_sensor.hall_aicom_smoke_alarm_input_0` |
| CO Alarm | `binary_sensor.hall_aicom_smoke_alarm_input_1` |
| Smoke Alarm Fault | `binary_sensor.hall_aicom_smoke_alarm_input_2` |
| Aico Smoke Alarm - Trigger | `switch.control_room_shelly_plus_1_aicom_smoke_alarm_trigger` |

The IDs keep the device's original "hall"/"aicom" naming. Renaming them would break the automations, so they're left as is.

## Alert automations (deployed)

Both are in [`config/homeassistant/automations.yaml`](../config/homeassistant/automations.yaml).

| Automation | Trigger | Action |
|---|---|---|
| Aico - Fire / CO / Fault alert | Any of the three inputs turns **on** (ignoring restarts from *unavailable*), or turns back **off** | Phone notification ("ALARM: …" / "Cleared: …") plus a persistent notification in HA |
| Aico - interface offline | The i4 DC or the Plus 1 is **unavailable for 5 min**, or comes back | Phone notification ("OFFLINE" / "back online") plus a persistent notification |

Design points:
- `not_from: unavailable / unknown` stops false alarms when HA or a Shelly restarts.
- The offline alert is the interface's own fault alarm. It's the HA-side answer to the shared-supply and unsupervised-wiring limitations, though it can't detect a broken signal wire.
- Testing note: the editor's **Run** button skips triggers, so the `choose` branches don't run. Test by changing state instead (see the [test record](04-commissioning-and-test-record.md)).
