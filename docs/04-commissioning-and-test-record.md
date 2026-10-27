# 04 · Commissioning and Test Record

[← Back to README](../README.md)

**System:** Aico RadioLINK (2 × Ei3030 + Ei3000MRF) → Ei413 → Shelly Plus i4 DC / Plus 1 → Home Assistant · **Commissioned:** August 2026

| # | Test | Method | Expected | Result | Pass |
|---|---|---|---|---|---|
| 1 | Supply | 12 V DC adapter connected; check each device | Ei413 LED, i4 DC LED, Plus 1 online in the app | All powered (Plus 1 confirmed via app, no LED) | ✅ |
| 2 | House code | Ei413 coded to both Ei3030 alarms | Devices learn each other's code | Coded | ✅ |
| 3 | Shellys in HA | Shelly integration discovery | Both devices and entities present | Present | ✅ |
| 4 | Entity classification | Show as on each input | Fire Alarm / CO Alarm read Clear; Smoke Alarm Fault reads OK | Correct | ✅ |
| 5 | Fire path | Ei3030 test button | All alarms sound; Fire relay closes; Fire Alarm = Detected; returns to Clear | As expected | ✅ |
| 6 | CO path | Verified during commissioning | CO Alarm = Detected; returns to Clear | As expected | ✅ |
| 7 | Fault path | Verified during commissioning | Smoke Alarm Fault = Problem; returns to OK | As expected | ✅ |
| 8 | Command path | HA turns on Aico Smoke Alarm - Trigger | Ei413 transmits; all alarms sound | All alarms sounded | ✅ |
| 8a | Trigger fail-safes | Plus 1: Detached, power-on OFF, auto-off 3 s; trigger from HA | Alarms sound; switch returns to Off by itself after about 3 s | As expected | ✅ |
| 9 | Independence | Alarms tested regardless of HA | Interconnection and sounders work without HA | Unaffected | ✅ |
| 10 | Comms health | Diagnostic entities | Strong Wi-Fi, stable uptime | −33 dBm (i4 DC), −26 dBm (Plus 1); uptime 2 days at check | ✅ |
| 11 | Alarm alert | Developer tools → States: set an input to `on`, then back to `off` | "ALARM: …" then "Cleared: …" on the phone | Both received | ✅ |
| 12 | Interface-offline alert | 12 V adapter unplugged for 6 min, then restored | "OFFLINE" alerts after 5 min; "back online" alerts on restore | As expected | ✅ |
| 13 | Fail-safe on power restore | Watch the alarms as power returns (test 12) | Alarms do **not** sound (Plus 1 power-on OFF) | Did not sound | ✅ |

**Notes**

- Test 5 relies on the Ei413 closing its Fire relay on a test message as well as a real alarm, which is what makes routine weekly testing visible in HA.
- Test 11 used a simulated state change so the alerts could be checked without sounding the alarms. The full chain is also exercised whenever an alarm's test button is pressed (test 5).
- The alarm test button exercises the Fire path. The CO and Fault paths were each verified reaching HA during commissioning.
- Photos are to be added to `images/`.
