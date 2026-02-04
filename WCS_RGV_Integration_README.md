# Warehouse Control System (WCS) - RGV Integration Layer

<img alt="Role Badge" src="https://img.shields.io/badge/ROLE-Software%20Engineer%20%20(Industrial%20Automation)-f22481"> <br>
![NOTE](https://img.shields.io/badge/PROJECT-INTRODUCTION-00C2FF?style=for-the-badge)




> This project represents an industrial automation integration solution
developed as part of a Warehouse Control System (WCS). The system
enables real-time communication between a Warehouse Management System
(WMS) and Rail Guided Vehicles (RGVs) operating within a fully automated
factory environment.

> The primary objective of this solution is to reliably dispatch
transport-related commands to RGV fleets while ingesting telemetry,
status updates, and operational reports from physical equipment.

> ⚠️ **Note**: Due to confidentiality agreements, source code cannot be
> shared. This document focuses on architecture, engineering patterns,
> and system design.

------------------------------------------------------------------------
## APPLICATIONS
<details open>
  <summary>
    <img src="https://img.shields.io/badge/01-WMS%20→%20RGV%20Command%20Dispatcher-00C2FF?style=for-the-badge" alt="Augmented Reality Exploration">
  </summary>

- A .NET-based service responsible for:

  - Monitoring shared memory for scheduler-issued instructions
  - Translating system and scheduler states into executable machine commands
  - Invoking RGV API endpoints with low-latency guarantees
  - Ensuring safe and deterministic command execution
  - Handling transport-related operations, including:
    - Transport Request
    - Transport Cancellation
    - Destination Change
    - System Mode Change

</details>

<details open>
  <summary>
    <img src="https://img.shields.io/badge/02-RGV%20→%20WMS%20Telemetry%20Service-00C2FF?style=for-the-badge" alt="RGV → WMS Telemetry Service">
  </summary>

- A service layer exposing API endpoints to receive operational feedback from RGV equipment

  - Handles incoming machine-generated reports, including:

    - Transport Status Reports
    - Destination Change Requests
    - Pass Reports
    - Equipment Status Reports
    - Equipment Error Reports
    - System Error / Alert Reports

  This service also updated shared memory structures to synchronize
equipment state across the WCS ecosystem.

</details>

------------------------------------------------------------------------
## DIAGRAMS
<details open>
  <summary>
    <img src="https://img.shields.io/badge/01-WCS%20↔%20RGV%20Integration%20Flow-00C2FF?style=for-the-badge" alt="WCS ↔ RGV Integration Flow">
  </summary>

- This diagram shows the interaction between **RGV2WMS** and **WMS2RGV** in a Warehouse Control System.

```text

                        Server 1                                                                              Server 2
                                                  +-------------------+           REST API Calls       +--------------------+
                                                  |                   |<------------------------------>|                    |
                                                  |      RGV2WMS      |                                |          RGV       |
                                    +-------------|  Telemetry &      |                                |  which provide api |  
                                    |             |  Status Reports   |------------------------------->| Transport / Cancel |
                                    |             |                   |          REST API Calls        | Destination Change |
   write sm status         +------------------+   +-------------------+                                +--------------------+
          +----------------|   Shared Memory  |---------------------+                                          ^
          |                +------------------+                     |                                          |
 +-------------------+                            +-----------------------+                                    |
 |                   |                            |                       |                                    |
 |                   |                            |      WMS2RGV          |                                    |
 |    RGV Shedular   |                            |  Command Dispatcher   |------------------------------------+
 |                   |                            |  Transport / Cancel   |
 |                   |                            |  Destination Change   |
 +-------------------+                            +-----------------------+


```

</details>


<details open>
  <summary>
    <img src="https://img.shields.io/badge/02-High%20Level%20Architecture-00C2FF?style=for-the-badge" alt="High-Level Architecture">
  </summary>

- A service layer exposing API endpoints to receive operational feedback from RGV equipment

```text


                    +------------------------------+
                    |   Warehouse Management       |
                    |           System (WMS)       |
                    +--------------+---------------+
                                   |
                                   v
                    +------------------------------+
                    |   Warehouse Control System   |
                    |            (WCS)             |
                    +--------------+---------------+
                                   |
                +---------------------------------------+
                |        |         |         |          |        
                v        v         v         v          v        

            Scheduler  Crane      SCADA   Database   Other
                      Control                       Services 
                                   |
                                   v
                    +------------------------------+                      Server 1
                    |        Shared Memory IPC     |
                    +--------------+---------------+
                                   |
                  -----------------------------------------
                  |                                       |
                  v                                       v

       +------------------------+         +------------------------+
       |   WMS → RGV            |         |   RGV → WMS            |
       |   Command Service      |         |   Telemetry Service    |
       +-----------+------------+         +-----------+------------+
                   |                                      ^
                   | REST / HTTP APIs                     |
                   v                                      |
            +------------------------------------------------------+
            |            Rail Guided Vehicles (RGVs)               |      Server 2
            |   Industrial PCs / Embedded Control Systems          |
            +------------------------------------------------------+


```

</details>

------------------------------------------------------------------------

## KEY FEATURES
<details open>
  <summary>
    <img src="https://img.shields.io/badge/01-Distributed%20Service%20Architecture-00C2FF?style=for-the-badge" alt="Augmented Reality Exploration">
  </summary>
Multiple independent .NET console/worker applications operated
concurrently on a central control server. Each application was
responsible for a dedicated automation domain, including:

  -   Fleet scheduling
  -   Crane control
  -   SCADA integration
  -   Database interaction
  -   Equipment and vehicle integration

This modular architecture improved fault isolation, maintainability, and
overall system reliability.
  
</details>

<details open>
  <summary>
    <img src="https://img.shields.io/badge/02-Inter%20Process%20Communication-00C2FF?style=for-the-badge" alt="Augmented Reality Exploration">
  </summary>
  
The system relied on **shared memory** as the primary IPC mechanism
between time-critical services.

**Why Shared Memory?**

-   Near-zero latency
-   Deterministic execution behavior
-   Suitable for real-time and industrial environments
-   Eliminates network and serialization overhead

This approach is commonly used in:

-   Robotics and automation systems
-   High-frequency trading platforms
-   Telecommunications infrastructure
-   Smart factory environments
  
</details>

<details open>
  <summary>
    <img src="https://img.shields.io/badge/03-Event%20Driven%20Command%20Execution-00C2FF?style=for-the-badge" alt="Augmented Reality Exploration">
  </summary>

Instead of traditional request polling, command execution was driven by
scheduler-updated memory states.

Example logic:
```text
    IF CMD = TRANSPORT AND GO = TRUE
        → Dispatch transport request to RGV
```

This event-driven approach ensured predictable, efficient, and timely
machine orchestration.
  
</details>

<details open>
  <summary>
    <img src="https://img.shields.io/badge/04-Distributed%20Service%20Architecture-00C2FF?style=for-the-badge" alt="Augmented Reality Exploration">
  </summary>

System behavior followed strict and deterministic state transitions to
prevent unsafe or undefined operations.

Example operational states:

-   READY
-   EXECUTING
-   COMPLETED
-   FAILED

State-driven control models are essential in industrial automation
systems where safety and reliability are non-negotiable.
  
</details>

------------------------------------------------------------------------

## Technology Stack

**Backend** - .NET Console Applications / Worker Services
- RESTful API communication

**Architecture** - Distributed Systems
- Event-Driven Design
- State Machine--Based Execution

**Communication** - Shared Memory IPC
- Machine-to-System API Integration

**Domain** - Warehouse Control Systems (WCS)
- Industrial Automation
- Equipment Telemetry and Feedback

------------------------------------------------------------------------

## Engineering Challenges

### Real-Time Reliability

Factory downtime has direct operational and financial impact. All
services were required to run continuously with high stability.

### Safe Command Dispatching

Incorrect or mistimed commands could halt equipment or create safety
risks within the factory.

### State Synchronization

Ensuring consistent system state across:

Physical Equipment ↔ Control Services ↔ Scheduler

### Fault Handling

Designing services capable of gracefully handling:

-   Equipment-level errors
-   Communication failures
-   Invalid or conflicting system states

------------------------------------------------------------------------

## Key Achievements

✅ Developed mission-critical industrial automation services<br>
✅ Enabled reliable, low-latency command exchange with RGV fleets<br>
✅ Implemented shared-memory--driven IPC workflows<br>
✅ Delivered production-grade distributed service architecture<br>
✅ Contributed to a real-world smart factory / Industry 4.0 environment<br>

------------------------------------------------------------------------

## What I Learned

This project significantly strengthened my experience in:

-   Industrial software engineering
-   Distributed and real-time system design
-   Equipment and vehicle integration
-   State-driven control logic
-   Failure-tolerant architecture

It also provided hands-on exposure to large-scale automation systems
aligned with **Industry 4.0 principles**.

------------------------------------------------------------------------

## 🔒 Confidentiality Notice

Specific implementation details, proprietary protocols, internal
identifiers, and organizational information have been intentionally
omitted to comply with confidentiality agreements.

