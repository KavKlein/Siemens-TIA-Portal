# Industrial Automation Simulation Stack
 
A virtualized industrial automation system integrating PLC control logic, secure OPC UA communication, and SCADA visualization using Siemens TIA Portal and Ignition.
 
This repository contains a **work-in-progress implementation** of a modern industrial control architecture — from PLC logic execution to real-time browser-based monitoring and control.
 
> 📘 **Detailed architecture, ladder logic, OPC configuration, and technical documentation are in the `/docs` folder.**
 
> 🚧 **This project is actively under development. Core pipeline is functional. Process simulation and alarm layers are planned.**
 
---
 
## 🎯 Overview
 
This project demonstrates how industrial control systems are structured across **control, communication, and supervisory layers**.
 
Rather than focusing only on PLC programming, this system replicates how real-world automation environments operate end-to-end.
 
### Core Pipeline
 
```
PLC Logic → Virtual PLC → OPC UA → SCADA Gateway → Web HMI
```
 
### Key Objective
 
Build a **system-level understanding of industrial automation** by implementing:
 
- Structured PLC programming (IEC 61131-3, Ladder Diagram)
- Secure industrial communication (OPC UA with certificate authentication)
- SCADA-based monitoring and real-time control
- Bidirectional data flow across all layers
 
---
 
## ✨ What's Working
 
### ⚙️ Industrial Control Logic
- Function Block–based motor control (FB + instance DB pattern)
- Stop-dominant interlock (IEC 60204-1 compliant)
- Seal-in latch for motor run memory
- SR latch fault detection with mandatory operator reset
- Auto/Manual mode gating on start command
- Simultaneous Start/Stop conflict resolution
 
### 🔐 Secure OPC UA Communication
- OPC UA Server configured on simulated S7-1500 CPU
- Security policy: **Basic256Sha256 + SignAndEncrypt**
- Mutual certificate authentication (TIA Portal ↔ Ignition)
- Stable client-server connection verified at runtime
- Real-time tag synchronization across layers
 
### 📊 SCADA Visualization (Ignition Perspective)
- Browser-based HMI accessible at localhost
- Real-time motor control dashboard
- Bidirectional tag binding (Read/Write)
- Toggle-based command interface for all PLC inputs
- Live status feedback for outputs (Running, Fault, Run CMD)
 
### 🔌 Fully Virtualized Environment
- No physical hardware required
- PLCSIM Advanced V4.0 as virtual S7-1500 controller
- Communication over virtual Ethernet adapter
- Complete simulation of industrial network topology
 
---
 
## 🏗️ System Architecture
 
```
┌──────────────────────────────────────────┐
│         TIA Portal V17                   │
│  CPU 1511-1 PN │ Motor FB │ OPC UA cfg   │
└─────────────────┬────────────────────────┘
                  │ Download via Virtual Ethernet
                  ▼
┌──────────────────────────────────────────┐
│         PLCSIM Advanced V4.0             │
│  Simulated S7-1500 @ 192.168.1.10:4840  │
└─────────────────┬────────────────────────┘
                  │ OPC UA (Basic256Sha256, SignAndEncrypt)
                  ▼
┌──────────────────────────────────────────┐
│         Ignition Gateway 8.3.4           │
│  OPC UA Client │ Tag Provider │ Security │
└─────────────────┬────────────────────────┘
                  │ Perspective web session
                  ▼
┌──────────────────────────────────────────┐
│         Perspective Web HMI              │
│  Motor P101 Dashboard │ localhost:8088   │
└──────────────────────────────────────────┘
```
 
---
 
## 🔧 Technology Stack
 
| Layer | Technology |
|---|---|
| Engineering | Siemens TIA Portal V17 |
| Virtual PLC | PLCSIM Advanced V4.0 |
| PLC Hardware (simulated) | SIMATIC S7-1500, CPU 1511-1 PN |
| Communication Protocol | OPC UA, Port 4840 |
| OPC UA Security | Basic256Sha256 + SignAndEncrypt |
| SCADA Platform | Ignition Gateway 8.3.4 (Maker Edition) |
| HMI Framework | Ignition Perspective |
| License Management | Automation License Manager V6.2 SP5 |
| OS | Windows 11 Pro 23H2 |
 
---
 
## 🧩 Motor Control Unit — P101
 
### Tag Structure
 
**Inputs (SCADA → PLC via OPC UA Write):**
| Tag | Type | Description |
|---|---|---|
| start_btn | Bool | Start pushbutton command |
| stop_btn | Bool | Stop pushbutton command |
| auto_mode | Bool | Auto/Manual mode selector |
| permissive_ok | Bool | Process permissive interlock |
| reset_fault | Bool | Fault reset command |
| trip_active | Bool | Hardware trip signal |
 
**Outputs (PLC → SCADA via OPC UA Read):**
| Tag | Type | Description |
|---|---|---|
| running | Bool | Motor running feedback |
| run_cmd | Bool | Motor run command to drive |
| fault_detected | Bool | Latched fault status |
 
---
 
### Ladder Logic — Network Summary
 
| Network | Description | Pattern |
|---|---|---|
| 1 | Start/Stop interlock | Stop-dominant, NC stop contact in series with start |
| 2 | Motor running memory | Seal-in latch with auto_mode and permissive gating |
| 3 | Fault detection | SR latch, trip sets, reset_fault + no trip resets |
| 4 | Run command output | run_latch AND NOT fault → run_cmd |
| 5 | Running status | run_cmd → running (mode-independent) |
 
---
 
## 📡 Data Flow
 
```
User Input (Perspective Toggle)
        ↓
   OPC UA Write
        ↓
   PLC Input Tag
        ↓
  Ladder Logic Execution (FB_motor)
        ↓
   PLC Output Tag
        ↓
   OPC UA Read
        ↓
  SCADA Tag Update
        ↓
  Dashboard Reflects State
```
 
---
 
## 📈 Current Status
 
| Component | Status |
|---|---|
| TIA Portal project + CPU config | ✅ Complete |
| Motor FB logic (P101) | ✅ Complete |
| OPC UA server configuration | ✅ Complete |
| Certificate exchange (TIA ↔ Ignition) | ✅ Complete |
| Ignition OPC UA connection | ✅ Stable |
| Perspective dashboard (Motor P101) | ✅ Functional |
| End-to-end control loop | ✅ Verified |
| Alarm management | 🔲 Planned |
| Process simulation (tank/pump) | 🔲 Planned |
| Historical data logging | 🔲 Planned |
| Multi-motor scaling | 🔲 Planned |
 
---
 
## ⚠️ Known Limitations
 
- Single motor unit (P101) only — no process-level simulation yet
- No alarm or event management implemented
- No historical logging or trend visualization
- No multi-device or multi-zone scaling
- Trial license environment — 21-day Siemens trial
 
---
 
## 🚀 Planned Enhancements
 
- **Process simulation layer** — tank level control, pump sequencing, valve logic
- **Alarm management** — TIA Portal alarm tags mapped to Ignition alarm pipeline
- **Trend charts** — historical tag logging in Ignition and Perspective chart views
- **Multi-motor expansion** — P102, P103 with interlocked sequence logic
- **MQTT / IIoT layer** — ESP32 integration as auxiliary sensor node via Modbus TCP
 
---
 
## 📂 Repository Structure
 
```
/
├── src/                    → TIA Portal project files (.ap17)
├── hmi/                    → Ignition project export (.zip) + gateway backup (.gwbk)
├── docs/                   → Architecture document, technical summary, OPC UA config
└── assets/                 → Screenshots, dashboard images, network diagrams
```
 
---
 
## 🧠 Key Engineering Decisions
 
**Why Basic256Sha256 over No Security:**
Ignition 8.3.x has a known NullPointerException in the anonymous/no-security OPC UA path. Certificate-based security bypasses this entirely and is the correct industrial practice.
 
**Why Ethernet mode in PLCSIM Advanced:**
Local mode restricts OPC UA to TIA Portal only. Ethernet mode exposes the virtual CPU on the network adapter, making it reachable by Ignition as an independent OPC UA client.
 
**Why Ignition Maker Edition over WinCC RT:**
WinCC RT trial activation is restricted to verified industry accounts. Ignition Maker Edition provides full Perspective, OPC UA client, and tag functionality for non-commercial use — equivalent capability for this scope.
 
**Why Stop-dominant interlock:**
IEC 60204-1 machine safety standard requires that in any simultaneous Start+Stop condition, Stop must take priority. This is implemented in Network 1 as a normally-closed Stop contact in series with the Start path.
 
**Why SR latch for fault:**
A simple coil resets the fault the moment the trip clears. An SR latch enforces explicit operator acknowledgment before restart is permitted — the correct industrial behavior.
 
---
 
## 👤 Author
 
**Kavinda Rathnayaka**
Electrical & Electronic Engineer
 
Focus: Power Systems · Industrial Automation · Control Systems
