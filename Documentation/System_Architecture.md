# System Architecture — Tank Level Control System

## 1. Introduction

The system is designed to automate the filling and controlled drainage of an industrial process tank using a PLC-based control architecture.

The PLC receives signals from discrete level sensors and operator controls, processes the input conditions through Ladder Logic, and operates the filling pump and solenoid valves.

The system provides automatic filling, manual drainage, fault detection and emergency-stop functionality.

## 2. Main System Components

| Component | Quantity | Function |
|---|---:|---|
| Process tank | 1 | Stores process liquid |
| Filling pump (MP1) | 1 | Transfers liquid into the tank |
| Inlet solenoid valve (SV1) | 1 | Controls liquid inlet |
| Drain solenoid valve (SV2) | 1 | Controls liquid outlet |
| Low-Low level sensor | 1 | Detects critically low level |
| Low level sensor | 1 | Initiates automatic filling |
| High level sensor | 1 | Stops automatic filling |
| High-High level sensor | 1 | Detects overflow condition |
| PLC | 1 | Executes control logic |
| Operator interface | 1 | Provides monitoring and manual commands |

## 3. Functional Architecture

The system is divided into four functional sections:

### A. Input and Sensing Layer

The PLC receives digital signals from:

- Emergency-stop pushbutton.
- Fault-reset pushbutton.
- Auto/Manual selector switch.
- Four tank-level sensors.
- Manual drainage pushbutton.

### B. Processing and Control Layer

The PLC executes the Ladder Logic program to:

- Determine the current tank-level condition.
- Select automatic or manual operating mode.
- Control filling and drainage sequences.
- Detect abnormal operating conditions.
- Execute safety interlocks.
- Generate alarm signals.

### C. Actuation Layer

The PLC controls:

- Filling pump contactor.
- Inlet solenoid valve.
- Drain solenoid valve.
- Run, drain and alarm indicators.

### D. Monitoring Layer

The operator interface is intended to display:

- Tank-level status.
- Pump operating status.
- Valve status.
- Operating mode.
- Active faults and alarms.
- Operator control commands.

## 4. Tank-Level Sensor Arrangement

The tank uses four discrete level sensors positioned at different heights.

| Sensor | Position | Purpose |
|---|---|---|
| LS_LowLow | Bottom | Detects critically low or empty condition |
| LS_Low | Lower level | Initiates filling |
| LS_High | Upper level | Terminates filling |
| LS_HighHigh | Near maximum level | Detects overflow |

The automatic filling cycle operates between the LOW and HIGH thresholds.

The LOW-LOW sensor is used for empty-level and dry-run protection, while the HIGH-HIGH sensor provides an additional overflow safeguard.

## 5. Operating Modes

### Automatic Mode

1. The operator selects Automatic mode.
2. The PLC monitors tank-level signals.
3. When the level falls below LOW, the filling sequence is initiated.
4. The filling pump and inlet valve are energised.
5. When HIGH level is detected, filling stops.
6. The system remains idle until another filling demand occurs.

### Manual Mode

1. The operator selects Manual mode.
2. Automatic filling control is disabled.
3. The operator can request drainage using the manual drain command.
4. The drain valve opens when the operating permissives are satisfied.
5. Drainage stops when the tank reaches the empty condition or another stop condition occurs.

## 6. Protection and Interlocking

The system includes the following protective functions:

- Emergency-stop interlock.
- High-High overflow detection.
- Low-level dry-run protection.
- Filling timeout supervision.
- Drainage timeout supervision.
- Mutual exclusion between filling and drainage.
- Fault latching and manual reset.

The safety conditions take priority over normal operating commands.

## 7. Overall Control Sequence

**Inputs → PLC Processing → Output Control → Feedback and Monitoring**

The PLC continuously evaluates sensor inputs and operating conditions. Based on the selected mode and active permissives, it controls the pump and valves while monitoring fault conditions.

## 8. Design Scope and Limitations

This project is based on discrete level sensing using float switches or point-level probes.

It is intended as an educational industrial automation design. It does not implement continuous analog level measurement or PID-based level regulation.

The original reference design is being studied and adapted. Implementation details and verification results will be documented as the project develops.
