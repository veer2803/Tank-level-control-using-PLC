# PLC-Based Tank Level Control and Automation System
## Input / Output (I/O) List

## 1. Purpose

This document defines the PLC input signals, output signals, internal memory bits and timers used in the tank-level control system.

The I/O list provides a reference for PLC programming, electrical wiring, troubleshooting and future HMI/SCADA integration.

## 2. Digital Inputs

Digital inputs receive signals from field devices such as push buttons, selector switches and level sensors.

| PLC Address | Tag Name | Field Device | Description |
|---|---|---|---|
| `%IX0.0` | `PB_EStop` | Emergency Stop | Normally closed emergency-stop contact |
| `%IX0.1` | `PB_FaultReset` | Reset Push Button | Normally open push button used to reset faults |
| `%IX0.2` | `SW_ModeAuto` | Auto/Manual Selector | Selects automatic or manual operating mode |
| `%IX0.3` | `LS_LowLow` | Low-Low Level Sensor | Detects critically low tank level |
| `%IX0.4` | `LS_Low` | Low Level Sensor | Detects low tank level and initiates automatic filling |
| `%IX0.5` | `LS_High` | High Level Sensor | Detects high tank level and stops automatic filling |
| `%IX0.6` | `LS_HighHigh` | High-High Level Sensor | Detects an overflow-level condition |
| `%IX0.7` | `PB_ManDrain` | Manual Drain Push Button | Requests manual draining in manual mode |

## 3. Digital Outputs

Digital outputs operate actuators and provide status indications.

| PLC Address | Tag Name | Field Device | Description |
|---|---|---|---|
| `%QX0.0` | `MP1_FillPump` | Fill Pump | Controls the tank filling pump |
| `%QX0.1` | `SV1_FillValve` | Fill Solenoid Valve | Controls the inlet valve |
| `%QX0.2` | `SV2_DrainValve` | Drain Solenoid Valve | Controls the outlet/drain valve |
| `%QX0.3` | `HL1_RunLamp` | Green Indicator | Indicates filling/run status |
| `%QX0.4` | `HL2_AlarmLamp` | Red Indicator | Indicates an active fault or alarm |
| `%QX0.5` | `HL3_DrainLamp` | Yellow Indicator | Indicates draining status |

## 4. Internal Memory Bits

Internal memory bits store operating states and control conditions within the PLC program.

| PLC Address | Tag Name | Function |
|---|---|---|
| `%MX0.0` | `MR_MasterEnable` | Enables the control system when safety conditions are satisfied |
| `%MX0.1` | `MR_FaultOverflow` | Stores the overflow fault condition |
| `%MX0.2` | `MR_FaultDryRun` | Stores the dry-run fault condition |
| `%MX0.3` | `MR_FaultAny` | Combined indication of active faults |
| `%MX0.4` | `MR_AutoMode` | Indicates automatic operating mode |
| `%MX0.5` | `MR_ManualMode` | Indicates manual operating mode |
| `%MX0.6` | `MR_FillActive` | Indicates that the filling sequence is active |
| `%MX0.7` | `MR_DrainActive` | Indicates that the draining sequence is active |
| `%MX1.0` | `MR_TankEmpty` | Indicates the empty/low-low tank condition |
| `%MX1.1` | `MR_TankFull` | Indicates the high-level tank condition |
| `%MX1.2` | `MR_FaultFillTimeout` | Stores the filling timeout fault |
| `%MX1.3` | `MR_FaultDrainTimeout` | Stores the draining timeout fault |

## 5. Timers

Timers are used to monitor operating conditions and detect abnormal process behaviour.

| Timer Name | Timer Type | Preset Time | Purpose |
|---|---|---:|---|
| `TON_DryRunDelay` | TON | 5 seconds | Delays dry-run fault detection while the pump is operating |
| `TON_FillTimeout` | TON | 120 seconds | Detects filling operation exceeding the permitted duration |
| `TON_DrainTimeout` | TON | 180 seconds | Detects draining operation exceeding the permitted duration |

**TON:** On-delay timer. Its output becomes active after the input condition remains true for the preset duration.

## 6. Proposed HMI Variables

The following variables are proposed for future HMI integration. They are not included in the reference I/O and memory list and must be declared and mapped before implementation.

| Proposed Tag | Function |
|---|---|
| `HMI_Reset` | HMI-based fault reset command |
| `HMI_ManDrain` | HMI-based manual drain command |
| `HMI_ManFill` | HMI-based manual fill command |
| `HMI_SystemStatus` | Displays overall system status |
| `HMI_FaultMessage` | Displays active fault information |

## 7. Important Design Notes

1. The addresses and tag names in Sections 2–5 are based on the reference project documentation.
2. The physical sensor contact arrangement and signal polarity must be confirmed before wiring or implementation.
3. The emergency-stop function should use an appropriately designed safety circuit. A standard PLC input alone should not be treated as a safety-rated emergency-stop system.
4. HMI variables listed in Section 6 are proposed additions, not verified existing PLC variables.
5. The I/O list should be updated whenever the control architecture or PLC program is modified.

## 8. Document Status

**Status:** Documentation and design study

This I/O list is based on the reference project documentation and is intended to support further study, adaptation and development of the tank-level automation system.
