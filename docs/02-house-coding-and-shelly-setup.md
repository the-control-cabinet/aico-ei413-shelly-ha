# 02 · House Coding and Shelly Set-up

[← Back to README](../README.md)

## 1. House coding the Ei413 to the alarms

RadioLINK devices only act on signals from devices sharing their **house code**. Until the Ei413 is coded to the two Ei3030 alarms (via their Ei3000MRF modules), it ignores them, and the alarms ignore it.

Procedure followed (per the Ei413 instructions):

1. Power the Ei413 with the 12 V DC supply, and confirm the Ei3030 alarms are powered with their Ei3000MRF modules fitted.
2. Put the Ei413 and each alarm into house-code mode as the Aico instructions describe. The Ei413's LED indicates when it is in code mode and transmitting its serial number.
3. Leave the devices to learn each other's codes, then exit code mode.
4. **Verify:** press an alarm's test button. All alarms should sound, and the Ei413's Fire relay should operate (see the [test record](04-commissioning-and-test-record.md)).

> House coding is what separates this system from a neighbour's. Without it, a RadioLINK system could in principle respond to another house's alarms.

## 2. Shelly Plus i4 DC: inputs

| Setting | Value | Why |
|---|---|---|
| Input type (Fire, CO, Fault inputs) | **Switch** | The input follows the relay as a steady on/off and becomes a binary sensor in HA. *Button* type would only give press events |
| Inverted | Off | C-NO wiring means closed = alarm = On |
| Names | Fire / CO / Fault | Clear names carry through to HA |
| 4th input | Unused (always Off) | Spare |
| MQTT | Off | Native Shelly integration used |

In normal standby all four inputs show **Off** on the i4 DC's web page (contacts open). Pressing an alarm's test button turns the Fire input **On** for the duration of the alarm.

## 3. Shelly Plus 1: trigger relay

| Setting | Value | Why |
|---|---|---|
| Relay | Dry contact (O, I) to the Ei413 input | Volt-free closure, as the Ei413 expects from a "panel" |
| Output type | **Detached** | Nothing on the Plus 1's input can ever operate the relay; only HA can |
| Action on power on | **Turn OFF** | A power cut and restore can never sound the alarms |
| Auto-off | **3 s** | The trigger is always a short pulse and can't be left latched on |
| Input mode (Input 0) | Switch (input not used, shows Off) | The Plus 1 is used only for its relay |
| MQTT | Off | HA uses the native Shelly integration; MQTT would only add a duplicate path |
| Status LED | None on this model | Confirm health from the app, web UI or HA |

## 4. Home Assistant

1. Add both devices via the **Shelly** integration (discovered on the LAN), then put them in area **Control Room**.
2. Name the devices **Aico Smoke Alarm - Status** (Plus i4 DC) and **Aico Smoke Alarm - Trigger** (Plus 1).
3. Name the i4 DC inputs **Fire Alarm**, **CO Alarm** and **Smoke Alarm Fault**.
4. For each input entity: **Settings → Entities → (entity) → Show as**, and set Fire Alarm = **Smoke**, CO Alarm = **Carbon monoxide**, Smoke Alarm Fault = **Problem**.
