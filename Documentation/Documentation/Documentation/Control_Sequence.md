# PLC-Based Tank Level Control and Automation System
## Control Sequence and Operating Logic

## 1. Purpose

This document describes the operating sequence of the tank-level automation system. It explains how the PLC coordinates filling, draining, operating modes and fault handling using level sensors, operator commands and internal control logic.

The sequence is based on the reference project's documentation. It is a functional description, not proof of a physically tested implementation.

## 2. System Start-Up

1. The PLC reads the emergency-stop input and all level-sensor signals.
2. The system checks whether the emergency stop is healthy and whether any fault is active.
3. If the required conditions are satisfied, the master-enable bit (`MR_MasterEnable`) becomes active.
4. The operator selects Auto or Manual mode using the mode selector.
5. The system remains disabled if the emergency stop is activated or a fault prevents operation.

## 3. Automatic Filling Sequence

The automatic filling sequence maintains the tank level between the LOW and HIGH level limits.

1. The PLC monitors the LOW and HIGH level sensors.
2. When the tank level falls below the LOW level, the filling sequence is initiated if the system is enabled.
3. The PLC activates the fill operation (`MR_FillActive`).
4. The fill pump (`MP1_FillPump`) and inlet solenoid valve (`SV1_FillValve`) are energized.
5. The tank continues filling while the HIGH level sensor remains inactive.
6. When the HIGH level sensor becomes active, the filling sequence stops.
7. The pump and inlet valve are de-energized.
8. The system waits until the tank level falls below the LOW threshold before initiating another filling cycle.

**Important:** The LOW sensor provides the start threshold, while the HIGH sensor provides the stop threshold. This separation helps avoid rapid pump switching around a single level.

## 4. Manual Operating Mode

Manual mode allows operator-initiated control rather than automatic level-based operation.

The reference documentation specifically describes a manual drain command:

1. The operator selects Manual mode.
2. The operator presses the manual drain push button (`PB_ManDrain`).
3. The PLC checks that the tank is not empty, filling is inactive and no fault is present.
4. If the conditions are satisfied, the drain sequence (`MR_DrainActive`) is activated.
5. The drain solenoid valve (`SV2_DrainValve`) opens.
6. Draining stops when the tank-empty condition is detected, the push button is released, a fault occurs or the system loses its master enable.

**Documentation gap:** The reference project describes individual manual pump and valve commands through an HMI in its general overview, but its specific control-network description only identifies manual draining. Manual filling is therefore not treated as an implemented function in this document.

## 5. Interlocks Between Filling and Draining

Filling and draining must not operate simultaneously.

The control logic uses the following conditions:

- The filling sequence is blocked while draining is active.
- The draining sequence is blocked while filling is active.
- The fill pump and inlet valve operate only when filling is active and draining is inactive.
- The drain valve operates only when draining is active and filling is inactive.

These interlocks prevent conflicting process commands.

## 6. Fault Detection and Response

### 6.1 Emergency Stop

- The emergency-stop input is normally closed.
- Activating the emergency stop removes the healthy condition required for master enable.
- The control outputs are disabled through the control logic.

A real industrial installation should use a suitable safety-rated emergency-stop circuit rather than relying only on a standard PLC program.

### 6.2 Overflow Fault

- The high-high level sensor (`LS_HighHigh`) detects an abnormal high tank level.
- An overflow fault (`MR_FaultOverflow`) is generated and latched.
- Filling outputs are immediately disabled by the control logic.
- The alarm indication is activated.

### 6.3 Dry-Run Fault

- The PLC monitors the low-low level sensor (`LS_LowLow`) while the fill pump is operating.
- If the documented dry-run condition persists for the preset delay of 5 seconds, a dry-run fault is generated.
- The fault prevents continued operation through the master-enable and fault logic.

The exact sensor polarity and field wiring must be verified before implementing this condition.

### 6.4 Filling Timeout

- The fill timeout timer (`TON_FillTimeout`) monitors the filling operation.
- Its preset duration is 120 seconds.
- If the tank does not reach the HIGH level condition within the permitted duration, a filling timeout fault is generated.
- The filling sequence is stopped.

### 6.5 Draining Timeout

- The drain timeout timer (`TON_DrainTimeout`) monitors the draining operation.
- Its preset duration is 180 seconds.
- If the tank does not reach the empty condition within the permitted duration, a draining timeout fault is generated.
- The draining sequence is stopped.

## 7. Fault Reset

1. The operator checks the cause of the fault.
2. The underlying fault condition must be cleared.
3. The operator presses the fault-reset push button (`PB_FaultReset`).
4. The PLC processes the reset command according to the fault-latching logic.
5. Normal operation can resume only when the required safety and operating conditions are satisfied.

The exact reset behaviour for each individual fault must be confirmed against the actual PLC program before implementation.

## 8. Output Status and Indications

| Indicator | Meaning |
|---|---|
| Green run lamp (`HL1_RunLamp`) | Indicates filling/run status |
| Red alarm lamp (`HL2_AlarmLamp`) | Indicates an active fault |
| Yellow drain lamp (`HL3_DrainLamp`) | Indicates draining status |

The reference documentation is inconsistent about whether the overflow alarm flashes or remains steadily illuminated. The final indication behaviour should be confirmed against the actual PLC logic.

## 9. Operating Sequence Summary

| Condition | Expected System Response |
|---|---|
| Tank level below LOW in Auto mode | Initiate filling |
| HIGH level reached | Stop filling |
| Manual drain command in Manual mode | Initiate draining if interlocks permit |
| Tank-empty condition reached | Stop draining |
| High-high level detected | Latch overflow fault and stop filling |
| Dry-run condition persists for 5 seconds | Generate dry-run fault |
| Filling exceeds 120 seconds | Generate filling timeout fault |
| Draining exceeds 180 seconds | Generate draining timeout fault |
| Emergency stop activated | Disable system operation |
| Fault reset pressed after fault condition is cleared | Process fault reset |

## 10. Document Status and Verification

**Status:** Functional design documentation based on the reference project.

The following points require verification against the actual Ladder Logic program:

- Sensor contact polarity and signal interpretation.
- Exact fault-latching and reset conditions.
- Manual filling functionality.
- Overflow alarm flashing or steady indication.
- Timer reset behaviour when an operating sequence stops.
- Exact conditions used to activate and deactivate the master-enable bit.

These items should be resolved before describing the system as fully implemented or tested.
