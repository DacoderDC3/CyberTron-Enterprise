---
title: ADR-004 - Enterprise Storage Architecture
version: 0.1
status: Accepted
classification: Private
author: Wayne Stynder
created: 2026-07-19
last_updated: 2026-07-19
related_documents:
  - ../01-Architecture/CyberTron-Enterprise-Infrastructure-v0.1.md
  - ADR-001-CyberTron-Enterprise-Model.md
  - ADR-002-Standardise-on-Proxmox-for-Linux-Infrastructure.md
  - ADR-003-Windows-Enterprise-Services-Platform.md
---

# ADR-004 — Standardise on SSD-Tiered Storage with Mirrored Storage Spaces

## Status

**Accepted**

---

# Context

CyberTron requires storage capable of supporting:

- Hyper-V virtual machines
- Enterprise file services
- Security log retention
- VM backups
- ISO repositories
- Git repositories
- PowerShell scripts
- Project documentation

The available hardware consists entirely of repurposed consumer hardware.

The objective is to maximise performance, reliability and learning value while remaining cost conscious.

---

# Decision

The Windows Enterprise Services Host will implement a tiered storage architecture.

The operating system, virtual machines and enterprise storage will be separated across dedicated storage devices.

Windows Server 2022 will manage Storage Spaces directly rather than the Windows 11 host.

Storage Spaces will use **Two-Way Mirror** resiliency.

---

# Decision Evolution

## Initial Design

The original implementation created a Storage Spaces pool directly within Windows 11 Pro.

This demonstrated Storage Spaces functionality but tightly coupled enterprise storage to the host operating system.

The pool initially used **Parity** to maximise available capacity.

---

## Revised Design

Following performance analysis and refinement of the CyberTron architecture:

- Storage management moved into the Windows Server 2022 VM.
- VM storage was relocated to a dedicated SSD.
- Storage resiliency was reconsidered.

The workload was recognised as being dominated by random read/write activity rather than archival storage.

---

## Final Decision

Storage Spaces will be implemented inside the Windows Server VM.

The pool will use **Two-Way Mirror** rather than Parity.

Enterprise data will therefore prioritise:

- performance
- responsiveness
- resilience
- simplified recovery

over maximum storage utilisation.

---

# Storage Layout

## Windows Host

| Device | Purpose |
|---------|----------|
| 256 GB SSD | Windows 11 Pro |
| 480 GB SSD | Hyper-V VMs, checkpoints and configuration |
| 500 GB HDD | Local applications, X-Plane, miscellaneous data |

---

## Windows Server VM

| Device | Purpose |
|---------|----------|
| 3 × 2 TB HDD | Storage Spaces Pool (Two-Way Mirror) |

Primary services:

- SMB Shares
- Project storage
- VM backups
- Security logs
- Documentation
- ISO repository

---

# Rationale

## 1. Performance

CyberTron is not an archival storage environment.

Expected workloads include:

- Hyper-V
- SMB
- PowerShell
- Git
- VM backups
- Security logging

These workloads benefit significantly from mirrored storage compared with parity.

---

## 2. Enterprise Realism

Enterprise file servers frequently separate:

- Operating System
- Virtual Machine Storage
- Data Storage

This architecture reflects that design philosophy.

---

## 3. Storage Isolation

Separating:

- OS
- Virtual Machines
- Enterprise Data

reduces operational risk and simplifies recovery.

---

## 4. Learning Objectives

Implementing Storage Spaces within Windows Server provides experience with:

- enterprise storage
- SMB
- NTFS permissions
- Storage Spaces
- Hyper-V storage planning

rather than treating storage as a host-only feature.

---

## 5. Future Growth

The design supports future implementation of:

- File Server Resource Manager (FSRM)
- DFS Namespaces
- DFS Replication
- Shadow Copies
- Windows Server Backup
- Quotas
- File screening

without redesigning the storage platform.

---

# Alternatives Considered

## Storage Spaces on Windows 11 Host

### Rejected

Advantages

- Simpler implementation

Disadvantages

- Enterprise storage tied to host
- Less realistic server architecture
- Reduced Windows Server learning

---

## Parity

### Rejected

Advantages

- Greater usable capacity

Disadvantages

- Lower write performance
- Slower rebuilds
- Less suitable for VM workloads
- Higher write amplification

---

## Individual Disks

### Rejected

Advantages

- Simplicity

Disadvantages

- No redundancy
- No enterprise storage experience
- Poor scalability

---

## RAID Controller

### Rejected

Reasons

- Additional hardware cost
- Reduced opportunity to learn Windows Storage Spaces
- Existing hardware already satisfies requirements

---

# Consequences

## Positive

- Better random I/O performance
- Improved Hyper-V responsiveness
- Better enterprise alignment
- Clear separation of storage responsibilities
- Greater learning value

## Negative

- Reduced usable capacity
- Additional configuration effort
- More storage planning required

These trade-offs are considered acceptable because CyberTron's primary objective is enterprise capability rather than maximum storage utilisation.

---

# Implementation Summary

Windows 11 Pro Host

- Hyper-V
- VM Storage
- Local administration

Windows Server 2022

- Storage Spaces
- SMB
- Enterprise storage
- File services

---

# Related ADRs

- ADR-001 — CyberTron Enterprise Model
- ADR-002 — Proxmox Linux Infrastructure
- ADR-003 — Windows Enterprise Services Platform
- ADR-005 — Enterprise Network Architecture

---

# Design Principle

> Enterprise storage should be designed around workload characteristics rather than maximum capacity.

CyberTron prioritises responsiveness, recoverability and operational simplicity over storage efficiency.
