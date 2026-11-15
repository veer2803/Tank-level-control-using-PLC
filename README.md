# Tank-level-control-using-PLC
# PLC-Based Tank Level Control and Automation System

An industrial tank-level automation project using **OpenPLC and Ladder Logic**, designed to automate water filling and draining operations while incorporating safety interlocks, fault detection and operator monitoring.

## 1. Project Overview

This project focuses on the automation of an industrial process tank using a PLC-based control system.

The system uses four discrete level sensors to monitor tank conditions and controls a filling pump, a filling solenoid valve and a drainage solenoid valve.

The controller supports automatic filling, manual drainage, fault detection and emergency-stop functionality.

**Controller:** OpenPLC  
**Programming Language:** Ladder Diagram (LD)  
**Application:** Industrial Process Automation

## 2. System Architecture

The system consists of:

- Process tank with four discrete level sensors.
- Electric filling pump.
- Inlet solenoid valve.
- Outlet solenoid valve.
- PLC-based control system.
- Operator monitoring and control interface (HMI).

### Control Sequence

1. Level sensors continuously monitor the tank.
2. In automatic mode, filling starts when the level drops below the LOW threshold.
3. Filling stops when the HIGH threshold is reached.
4. Manual mode allows operator-controlled drainage.
5. Fault conditions override normal operation and disable relevant outputs.
6. Alarm indicators communicate abnormal operating conditions.

## 3. Hardware and I/O Configuration

### Digital Inputs

| Address | Tag | Description |
|---|---|---|
| %IX0.0 | PB_EStop | Emergency stop |
| %IX0.1 | PB_FaultReset | Fault reset |
| %IX0.2 | SW_ModeAuto | Auto/Manual selector |
| %IX0.3 | LS_LowLow | Low-low level sensor |
| %IX0.4 | LS_Low | Low-level sensor |
| %IX0.5 | LS_High | High-level sensor |
| %IX0.6 | LS_HighHigh | High-high level sensor |
| %IX0.7 | PB_ManDrain | Manual drain command |

### Digital Outputs

| Address | Tag | Description |
|---|---|---|
| %QX0.0 | MP1_FillPump | Filling pump |
| %QX0.1 | SV1_FillValve | Inlet solenoid valve |
| %QX0.2 | SV2_DrainValve | Drain solenoid valve |
| %QX0.3 | HL1_RunLamp | Filling indicator |
| %QX0.4 | HL2_AlarmLamp | Fault indicator |
| %QX0.5 | HL3_DrainLamp | Drain indicator |

## 4. PLC Control Logic

The control program is organised into eight functional Ladder Logic networks:

1. Emergency Stop and Master Enable
2. Tank Level Status Detection
3. Fault Detection and Latching
4. Automatic/Manual Mode Selection
5. Automatic Tank Filling
6. Manual Tank Drainage
7. Physical Output Control and Interlocking
8. Status and Alarm Indicators

## 5. Protection and Fault Detection

The controller incorporates the following protection functions:

| Protection | Description |
|---|---|
| Overflow protection | Stops filling when HIGH-HIGH level is detected |
| Dry-run protection | Detects pump operation without sufficient liquid |
| Fill timeout | Detects failure to reach the HIGH level within the preset time |
| Drain timeout | Detects failure to empty the tank within the preset time |
| Emergency stop | Disables normal operation |
| Fill/drain interlock | Prevents simultaneous filling and drainage |

## 6. Timer Configuration

| Timer | Preset | Purpose |
|---|---:|---|
| TON_DryRunDelay | 5 seconds | Dry-run detection |
| TON_FillTimeout | 120 seconds | Filling watchdog |
| TON_DrainTimeout | 180 seconds | Drainage watchdog |

## 7. HMI/SCADA Monitoring

The planned operator interface includes:

- Tank-level status.
- Pump and valve operating status.
- Automatic/Manual mode indication.
- Fault and alarm monitoring.
- Operator reset and manual control commands.

## 8. Project Development

This repository contains an adapted study and development of an existing PLC tank-level control reference.

Planned development activities:

- Review and understand the original Ladder Logic implementation.
- Adapt the control program and variable declarations for OpenPLC.
- Improve fault handling and operating interlocks.
- Develop project documentation and HMI design.
- Prepare operating and fault test cases.

## 9. Reference and Attribution

Original reference: [PLC Ladder Logic Portfolio – Project 02: Tank Level Control](https://github.com/khandaghiamir-gif/PLC_Ladder_Logic_Portfolio/tree/main/Project_02_Tank_Level)

The original project is used as a learning and design reference. Adaptations and modifications will be documented separately in this repository.

## 10. Project Status

**Status: Under Development**

This repository currently documents the reference architecture and planned adaptations. Actual implementation and verification will be recorded as development progresses.
