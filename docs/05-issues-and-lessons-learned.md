# 05 · Issues and Lessons Learned

[← Back to README](../README.md)

Each issue is written up as symptom → cause → fix → lesson.

### 1. Shelly Plus 1 wouldn't power up or pair
- **Symptom:** no response, and it couldn't be found to pair.
- **Cause:** the 12 V DC supply was on the wrong Plus 1 terminals (+ landed on N).
- **Fix:** moved + and − to the Plus 1's DC supply terminals as its wiring diagram shows for 12 V DC.
- **Lesson:** devices that accept AC or DC label their terminals for the AC case. Check the DC diagram, not the terminal names.

### 2. No sign of life on the Plus 1
- **Cause:** the Plus 1 has no status LED, unlike the i4 DC.
- **Fix:** confirm health from the Shelly app, web UI or HA.
- **Lesson:** know how each device shows it's alive before diagnosing it.

### 3. Crowded 0 V terminals
- **Symptom:** several 0 V conductors needed to land on one terminal.
- **Fix:** Wago connectors to distribute +12 V and 0 V, with max two conductors per screw terminal.
- **Lesson:** plan supply distribution before terminating, which is standard panel practice.

### 4. Pairing button did nothing
- **Cause:** see issue 1.
- **Lesson:** fix power first, then pair.

### 5. Getting a steady state from the inputs
- **Point:** in Button mode the i4 DC reports press events, not a held state.
- **Fix:** inputs set to **Switch** type, so each one follows its relay and becomes a binary sensor.
- **Lesson:** an alarm point needs a *state*, not an event, so that HA knows the alarm is still active after a restart.

### 6. Alert automation "didn't run" when tested
- **Symptom:** pressing **Run** in the automation editor sent nothing. The trace showed a `choose` with two options and neither taken.
- **Cause:** **Run** executes the actions without a trigger, so there's no trigger ID for the `active`/`cleared` branches to match.
- **Fix:** test by causing the trigger instead: Developer tools → States, set the input to `on` and then `off`, or press an alarm's test button.
- **Lesson:** test the whole path from trigger to action, not just the actions. Commissioning a BMS alarm means generating the alarm condition, not just checking the output.

## Transferable lessons

- **Read the interface module's documentation, not assumptions.** The Ei413 *needs* 11-30 V DC; it doesn't supply it.
- **NO vs NC is a design decision.** NO is simple, but a broken wire looks healthy. NC or an end-of-line resistor makes wiring faults visible. Fire interfaces normally choose supervision.
- **Interface, don't interfere.** The alarm system works the same with HA off. HA only listens, plus one deliberate trigger.
- **Device classes are part of commissioning.** The correct point type and alarm text matter as much as the wiring.
