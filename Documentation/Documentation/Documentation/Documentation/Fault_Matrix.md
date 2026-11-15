# PLC-Based Tank Level Control and Automation System
## Fault Matrix and Interlock Description

## 1. Purpose

This document identifies the major fault conditions considered in the tank-level control system, their detection methods and the expected response of the PLC.

The fault matrix is intended to support control-logic development, troubleshooting and future HMI/SCADA integration.

The descriptions below are based on the reference project's documentation. They describe intended behaviour and are not confirmation of physical testing.

## 2. Fault Matrix

| Fault Condition | Detection Method | Timer / Threshold | Expected PLC Response | Fault Reset |
|---|---|---|---|---|
| Emergency Stop | Emergency-stop input becomes unhealthy | Immediate | Disable master enable and control outputs | Release E-stop and follow reset procedure |
| Tank Overflow | High-high level sensor (`LS_HighHigh`) activates | Immediate | Latch overflow fault, stop filling and activate alarm | Clear condition and operate fault reset |
| Pump Dry Run | Low-low sensor (`LS_LowLow`) remains inactive while pump is operating | 5 seconds | Generate dry-run fault and prevent continued operation | Clear condition and operate fault reset |
| Filling Timeout | Tank fails to reach HIGH level during filling | 120 seconds | Stop filling and generate timeout fault | Clear condition and operate fault reset |
| Draining Timeout | Tank fails to reach empty condition during draining | 180 seconds | Stop draining and generate timeout fault | Clear condition and operate fault reset |

## 3. Operating Interlocks

Interlocks prevent unsafe or conflicting operating conditions.

### 3.1 Master Enable Interlock

- The system requires a healthy emergency-stop condition.
- The system must not have an active fault that blocks operation.
- The master-enable bit (`MR_MasterEnable`) permits normal control operation only when the required conditions are satisfied.

### 3.2 Filling Interlocks

The filling operation requires:

- Master enable active.
- Automatic filling conditions satisfied, or a separately defined manual-fill command if implemented.
- Draining inactive.
- No blocking fault.
- Tank not at the high-level stop condition.

### 3.3 Draining Interlocks

The draining operation requires:

- Master enable active.
- Manual mode selected.
- Manual drain command active.
- Tank not empty.
- Filling inactive.
- No blocking fault.

### 3.4 Mutual Exclusion

Filling and draining must never be active simultaneously.

| Fill Active | Drain Active | Expected Condition |
|---|---|---|
| 0 | 0 | Both operations stopped |
| 1 | 0 | Filling permitted if all other conditions are satisfied |
| 0 | 1 | Draining permitted if all other conditions are satisfied |
| 1 | 1 | Invalid state; both operations must be inhibited |

## 4. Fault Priority and Response

The intended response to major abnormal conditions is:

1. Emergency-stop activation must disable normal machine operation.
2. High-high level detection must immediately prevent further filling.
3. Dry-run detection must prevent continued pump operation.
4. Filling and draining timeouts must terminate their respective sequences.
5. Any active blocking fault must prevent normal operation until the fault condition is addressed and reset.

**Important:** This is a functional response hierarchy, not a verified safety-rated priority implementation. A safety-rated emergency-stop circuit and suitable protective devices must be designed independently of ordinary PLC logic.

## 5. Fault Indication

| Indicator | Intended Function |
|---|---|
| `HL1_RunLamp` | Indicates filling/run status |
| `HL2_AlarmLamp` | Indicates an active fault |
| `HL3_DrainLamp` | Indicates draining status |

The reference documentation does not consistently specify whether the overflow alarm flashes or remains steadily illuminated. The exact indication should be verified against the Ladder Logic program.

## 6. Points Requiring Verification

Before treating this fault matrix as a final implementation specification, verify:

- Whether the emergency-stop circuit is hardwired or only represented by a PLC input.
- The actual electrical polarity and wiring of all level sensors.
- Whether the dry-run condition correctly represents loss of liquid at the pump suction.
- Whether timeout bits are latched and how they are reset.
- Whether the master-enable logic blocks every relevant fault.
- Whether the invalid simultaneous fill-and-drain state is explicitly detected.
- Whether the overflow response also closes the inlet valve through the physical output logic.

## 7. Document Status

**Status:** Design documentation based on the reference project.

This fault matrix records the intended fault handling and interlock behaviour. Actual implementation and testing must be verified against the PLC program and hardware design.
