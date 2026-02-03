# Warehouse Control System (WCS) – RGV Integration Layer

## Overview

This project represents an industrial automation integration service developed as part of a Warehouse Control System (WCS). The solution enables real-time communication between the Warehouse Management System (WMS) and Rail Guided Vehicles (RGVs) operating inside a fully automated factory environment.

The primary objective of this system is to reliably dispatch transport commands to RGV fleets while ingesting telemetry and operational reports from physical equipment.

> ⚠️ Note: Due to confidentiality agreements, source code cannot be shared. This document focuses on architecture, engineering patterns, and system design.

---

## My Role

**Software Engineer – Industrial Automation**

I designed and developed two mission-critical services:

### ✅ WMS → RGV Command Dispatcher
A .NET-based service responsible for:

- Monitoring shared memory for scheduler instructions
- Translating system states into machine commands
- Calling RGV API endpoints
- Ensuring low-latency command execution
- Handling transport operations such as:
  - Transport Request
  - Transport Cancellation
  - Destination Change
  - System Mode Change

---

### ✅ RGV → WMS Telemetry Service
A service layer exposing API endpoints for equipment to report operational data.

Handled incoming machine reports such as:

- Transport Status Reports  
- Destination Change Requests  
- Pass Reports  
- Equipment Status  
- Error Reports  
- System Alerts  

The service also synchronized equipment state back into shared memory to maintain system-wide consistency.

---
## High-Level Architecture
            +----------------------+
            | Warehouse Management |
            |        System        |
            +----------+-----------+
                       |
                       |
                       v
            +----------------------+
            | Warehouse Control    |
            |      System (WCS)    |
            +----------+-----------+
                       |
    -----------------------------------------
    |        |        |        |            |
    v        v        v        v            v
    Scheduler Crane SCADA DB WMS-RGV Bridge
Engine Control
                       |
                       v

            +----------------------+
            |   Shared Memory IPC  |
            +----------+-----------+
                       |
     -----------------------------------
     |                                 |
     v                                 v
+-------------------+ +-------------------+
| WMS → RGV | | RGV → WMS |
| Command Service |<------>| Telemetry Service |
+---------+---------+ +---------+---------+
| ^
| REST APIs |
v |
+--------------------------------------+
| Rail Guided Vehicles |
| (Industrial PCs / Embedded Systems) |
+--------------------------------------+


---

## System Characteristics

### Distributed Service Architecture
Multiple independent .NET console applications operated simultaneously on a central control server, each responsible for a specific automation domain such as:

- Fleet scheduling  
- Crane control  
- SCADA communication  
- Database operations  
- Equipment integration  

This improved system modularity and operational reliability.

---

### Inter-Process Communication (IPC)

The system relied on **shared memory** for ultra-fast communication between critical services.

**Why Shared Memory?**

- Near-zero latency  
- Deterministic behavior  
- Suitable for real-time environments  
- Avoids network overhead  

This pattern is commonly used in:

- Robotics  
- Trading systems  
- Telecommunications  
- Smart factories  

---

### Event-Driven Command Execution

Instead of traditional polling, command execution was triggered based on scheduler-updated memory states.

Example:

IF CMD = Transport AND GO = True
→ Dispatch transport request to RGV


This approach ensured predictable and efficient machine orchestration.

---

### State Machine-Based Control

The system behavior followed deterministic state transitions to prevent unsafe operations.

Example states:

- READY  
- EXECUTING  
- COMPLETED  
- FAILED  

State-driven architectures are critical in industrial automation where reliability is mandatory.

---

## Technology Stack

**Backend**
- .NET Console / Worker Services
- RESTful APIs

**Architecture**
- Distributed Systems
- Event-Driven Design
- State Machine Execution

**Communication**
- Shared Memory IPC
- Machine-to-System API Integration

**Domain**
- Warehouse Control Systems (WCS)
- Industrial Automation
- Equipment Telemetry

---

## Engineering Challenges

### Real-Time Reliability
Factory downtime directly impacts production. Services had to remain stable under continuous operation.

### Safe Command Dispatching
Incorrect commands could halt equipment or create safety risks.

### State Synchronization
Maintaining consistency between:

Physical Equipment ↔ Control System ↔ Scheduler

### Fault Handling
Designing services that gracefully handled:

👉 Equipment errors  
👉 Network failures  
👉 Invalid states  

---

## Key Achievements

✅ Developed mission-critical automation services  
✅ Enabled reliable command exchange with RGV fleet  
✅ Built low-latency IPC-driven workflows  
✅ Implemented production-grade service architecture  
✅ Contributed to a real-world smart factory environment  

---

## What I Learned

This project strengthened my expertise in:

- Industrial software engineering  
- Distributed architectures  
- Real-time processing  
- Equipment integration  
- Failure-safe system design  

It also provided exposure to large-scale automation environments aligned with **Industry 4.0 principles**.

---

## 🔒 Confidentiality Notice

Specific implementation details, proprietary protocols, and organizational information have been intentionally omitted.

---

## (Optional Section — Customize)

### Scale of Deployment
> Example:
- Number of RGVs: [ Fill ]
- Commands per hour: [ Fill ]
- Factory size: [ Fill ]

---

### Tools / Libraries Used
> Fill with anything notable.

- [ ]
- [ ]
- [ ]

---

### Future Improvements (Architectural Thinking)

If redesigned today, potential enhancements could include:

- Message broker integration (Kafka / RabbitMQ)
- Containerized deployment
- Observability dashboards
- Automated failover
- Digital twin modeling

---

## Author

**[Your Name]**  
Software Engineer – Industrial Systems  

LinkedIn: [ Add ]  
Portfolio: [ Add ]






