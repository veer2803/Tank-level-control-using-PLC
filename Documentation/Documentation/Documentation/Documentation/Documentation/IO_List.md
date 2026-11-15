# PLC-Based Tank Level Control and Automation System
## Input / Output (I/O) List

## 1. Digital Inputs

The digital inputs are used to receive signals from operator pushbuttons, the mode selector, and tank-level sensors.

| Address (Siemens) | Tag Name | Description | Normal / Active State |
|---|---|---|---|
| I0.0 | PB_EStop | Emergency stop pushbutton (NC) | TRUE = Healthy |
| I0.1 | PB_FaultReset | Fault reset pushbutton | TRUE = Pressed |
| I0.2 | SW_ModeAuto | Auto/Manual mode selector | TRUE = Auto |
| I0.3 | LS_LowLow | Low-Low level switch | Sensor-dependent |
| I0.4 | LS_Low | Low-level switch | Sensor-dependent |
| I0.5 | LS_High | High-level switch | Sensor-dependent |
| I0.6 | LS_HighHigh | High-High overflow switch | Sensor-dependent |
| I0.7 | PB_ManDrain | Manual drain pushbutton | TRUE = Pressed |

**Note:** The exact electrical polarity and wiring arrangement of the level switches must be confirmed before implementation. The PLC logic assumes specific active and inactive states for level detection.

## 2. Digital Outputs

The digital outputs control the pump, solenoid valves, and status indicator lamps.

| Address (Siemens) | Tag Name | Description |
|---|---|---|
| Q0.0 | MP1_FillPump | Fill pump contactor |
| Q0.1 | SV1_FillValve | Fill solenoid valve |
| Q0.2 | SV2_DrainValve | Drain solenoid valve |
| Q0.3 | HL1_RunLamp | Fill operation indicator |
| Q0.4 | HL2_AlarmLamp | Fault alarm indicator |
| Q0.5 | HL3_DrainLamp | Drain operation indicator |

## 3. Internal Memory Bits

Internal memory bits are used to store operating states, mode selections, tank conditions, and fault conditions.

| Address (Siemens) | Tag Name | Description |
|---|---|---|
| M0.0 | MR_MasterEnable | Master system enable |
| M0.1 | MR_FaultOverflow | Overflow fault status |
| M0.2 | MR_FaultDryRun | Dry-run fault status |
| M0.3 | MR_FaultAny | Combined fault status |
| M0.4 | MR_AutoMode | Automatic mode active |
| M0.5 | MR_ManualMode | Manual mode active |
| M0.6 | MR_FillActive | Filling cycle active |
| M0.7 | MR_DrainActive | Draining cycle active |
| M1.0 | MR_TankEmpty | Tank empty status |
| M1.1 | MR_TankFull | Tank full status |
| M1.2 | MR_FaultFillTimeout | Fill timeout fault |
| M1.3 | MR_FaultDrainTimeout | Drain timeout fault |

## 4. Timers

Three on-delay timers are specified in the reference project for fault monitoring.

| Timer Name | Timer Type | Preset Time | Purpose |
|---|---|---|---|
| TON_DryRunDelay | TON | 5 seconds | Detects continued pump operation under the specified low-level condition |
| TON_FillTimeout | TON | 120 seconds | Detects failure to reach the HIGH-level sensor within the permitted filling time |
| TON_DrainTimeout | TON | 180 seconds | Detects failure to reach the empty condition within the permitted draining time |

## 5. HMI Tags

The following tags are described for operator monitoring and control through an HMI.

### 5.1 Monitoring Tags

| HMI Tag | PLC Tag | Function |
|---|---|---|
| HMI_LevelLowLow | LS_LowLow | Displays Low-Low sensor status |
| HMI_LevelLow | LS_Low | Displays Low sensor status |
| HMI_LevelHigh | LS_High | Displays High sensor status |
| HMI_LevelHighHigh | LS_HighHigh | Displays High-High sensor status |
| HMI_FillPumpRunning | MP1_FillPump | Displays fill pump status |
| HMI_FillValveOpen | SV1_FillValve | Displays fill valve status |
| HMI_DrainValveOpen | SV2_DrainValve | Displays drain valve status |
| HMI_FaultOverflow | MR_FaultOverflow | Displays overflow fault |
| HMI_FaultDryRun | MR_FaultDryRun | Displays dry-run fault |
| HMI_AutoMode | MR_AutoMode | Displays automatic mode status |

### 5.2 HMI Command Tags — Pending Verification

The reference README mentions the following HMI command bits, but they are not included in its PLC variable declaration table.

| Proposed HMI Tag | Referenced PLC Bit | Intended Function | Status |
|---|---|---|---|
| HMI_Btn_Reset | MX_HMI_Reset | HMI fault reset command | Not declared in reference variable list |
| HMI_Btn_ManDrain | MX_HMI_Drain | HMI manual drain command | Not declared in reference variable list |
| HMI_Btn_ManFill | MX_HMI_Fill | HMI manual fill command | Not declared in reference variable list |

These tags should be treated as proposed additions, not confirmed implemented variables. Manual filling also requires clarification because the reference project describes individual manual HMI controls in its overview, but its detailed control sequence only specifies manual draining.

## 6. Address Mapping Notes

The reference project provides both OpenPLC and Siemens-style addresses. The tables above use the Siemens-style addresses for documentation.

| Signal Type | OpenPLC Address Range | Siemens Address Range |
|---|---|---|
| Digital Inputs | %IX0.0–%IX0.7 | I0.0–I0.7 |
| Digital Outputs | %QX0.0–%QX0.5 | Q0.0–Q0.5 |
| Internal Memory | %MX0.0 onwards | M0.0 onwards |

The address mapping should be checked against the actual PLC hardware configuration before commissioning.

## 7. Important Implementation Considerations

1. Confirm the wiring and active-state polarity of all level sensors.
2. Verify that the emergency-stop circuit uses an appropriate safety relay or safety PLC. A standard PLC input and program should not be the sole safety mechanism.
3. Confirm the missing HMI command-bit declarations before implementing HMI control.
4. Verify timer presets and fault-reset behaviour against the intended process requirements.
5. Ensure electrical interlocking and PLC logic prevent simultaneous filling and draining.
6. Verify that pump and solenoid-valve outputs are connected through appropriately rated interface devices and protection.

---

**Documentation status:** Prepared from the reference project's README. Actual PLC source code, hardware wiring, and commissioning results have not yet been independently verified.
