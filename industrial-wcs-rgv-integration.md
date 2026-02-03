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

