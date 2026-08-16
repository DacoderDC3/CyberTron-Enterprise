---
title: ADR-004 - Enterprise Storage Architecture
version: 0.2
status: Accepted
classification: Private
author: Wayne Stynder
created: 2026-07-19
last_updated: 2026-08-16
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

# UPDATE 16 AUG 26
# ADR-004 — Enterprise Storage Architecture

## Purpose

This Architecture Decision Record defines the storage architecture used by the CyberTron Enterprise Windows infrastructure.

## Scope

This decision applies to the three 2 TB Western Digital enterprise HDDs physically installed in the Windows Hyper-V host and assigned to the CyberTron Windows Server platform.

## Audience

CyberTron infrastructure administrators, security administrators, and future maintainers of the lab environment.

---

## Status

**Implemented**

Implementation completed: **16 August 2026**

---

## Context

The CyberTron Windows host contains three approximately 2 TB Western Digital HDDs intended to provide resilient enterprise data storage.

The original design placed these disks in a Windows Storage Spaces parity pool on the Windows 11 host:

- Storage Pool: `DataVaultPool`
- Virtual Disk: `ProtectedStorage`
- Resiliency: Parity
- Volume: `S:`
- Filesystem: NTFS

Although functional, this design placed storage ownership on the Hyper-V host rather than the Windows Server environment.

The architecture was reconsidered as CyberTron evolved from a general-purpose homelab into a simulated enterprise infrastructure.

The Windows Server VM should own and manage the enterprise storage services directly.

---

## Decision Drivers

The selected architecture must:

- Provide resilience against a single physical disk failure.
- Provide better write performance than the previous parity configuration.
- Allow Windows Server to manage enterprise storage directly.
- Support future SMB file services.
- Support NTFS permissions and Active Directory security groups.
- Support future auditing, quotas, shadow copies, backup, and file-server administration.
- Provide practical experience with Windows Server Storage Spaces.
- Reuse existing physical hardware.

---

## Options Considered

### Option 1 — Storage Spaces Parity on Windows 11 Host

Three disks configured as a parity Storage Space on the Hyper-V host.

**Advantages**

- Higher usable capacity.
- Single-disk fault tolerance.
- Existing configuration already operational.

**Disadvantages**

- Poorer write performance.
- Enterprise storage owned by the workstation/hypervisor host.
- Reduced Windows Server storage administration experience.
- File services separated from direct storage management.

**Decision:** Rejected.

---

### Option 2 — Two-Way Mirror on Windows 11 Host

Storage Spaces mirror managed by the Hyper-V host.

**Advantages**

- Better write performance than parity.
- Single-disk fault tolerance.

**Disadvantages**

- Storage remains owned by the host.
- Windows Server does not directly manage its underlying enterprise storage.

**Decision:** Rejected.

---

### Option 3 — Physical Disk Passthrough to Windows Server with Two-Way Mirror

The three physical HDDs are taken offline on the Windows 11 Hyper-V host and passed directly to the Windows Server VM.

Windows Server then creates and manages the Storage Spaces pool.

**Advantages**

- Windows Server owns the enterprise storage stack.
- Better alignment with enterprise file-server administration.
- Two-way mirroring provides improved write performance.
- Survives failure of one physical disk.
- Supports future Windows Server file-services configuration.
- Provides hands-on Storage Spaces administration experience.

**Disadvantages**

- Approximately 50% storage efficiency.
- Physical disk passthrough ties the storage to the VM configuration.
- Mirroring does not replace a separate backup solution.

**Decision:** Selected.

---

## Final Decision

CyberTron Enterprise storage will use:

- **3 × 2 TB Western Digital enterprise HDDs**
- Hyper-V physical disk passthrough
- Windows Server 2022 Storage Spaces
- Two-way mirror resiliency
- Fixed provisioning
- GPT partitioning
- NTFS filesystem
- 4096-byte allocation units

The storage is managed by `CYB-SRV01`.

---

## Final Implementation

### Physical Layer

| Component | Configuration |
|---|---|
| Physical disks | 3 × WDC WD2002FYPS-18U1B |
| Nominal capacity | 2 TB each |
| Physical location | Hyper-V host |
| Host disk state | Offline |
| VM presentation | Hyper-V physical disk passthrough |

### Storage Spaces Layer

| Setting | Value |
|---|---|
| Storage Pool | `CyberTronStoragePool` |
| Raw pool capacity | ~5.46 TB |
| Virtual Disk | `CyberTronData` |
| Resiliency | Mirror |
| Fault Domain Redundancy | 1 |
| Provisioning | Fixed |
| Storage efficiency | 50% |
| Usable capacity | ~2.72 TB |

### Filesystem Layer

| Setting | Value |
|---|---|
| Partition style | GPT |
| Filesystem | NTFS |
| Allocation unit | 4096 bytes |
| Volume label | `CyberTronData` |
| Drive letter | `E:` |

---

## Resiliency

The two-way mirror maintains two copies of stored data across the three physical disks.

The virtual disk reports:

- `ResiliencySettingName: Mirror`
- `FaultDomainRedundancy: 1`
- `HealthStatus: Healthy`
- `OperationalStatus: OK`

The storage system can therefore tolerate the failure of one physical HDD without loss of the mirrored volume.

---

## Backup Consideration

Storage mirroring provides availability and hardware fault tolerance.

It does **not** protect against:

- Accidental deletion
- Malware or ransomware
- Logical corruption
- Administrative mistakes
- Loss of the physical host
- Site-level incidents

A separate CyberTron backup architecture will therefore be implemented independently of Storage Spaces.

---

## Decision Evolution

### Initial Design

Three 2 TB HDDs configured as a parity Storage Space on the Windows 11 host.

### Revised Design

Move ownership of the disks from the Hyper-V host into Windows Server using physical disk passthrough.

### Final Decision

Windows Server 2022 owns a three-disk Storage Spaces pool using a fixed two-way mirror and NTFS filesystem.

---

## Outcome

**ADR-004 has been implemented successfully.**

The storage subsystem is operational, healthy, and ready for the future CyberTron file-services layer.
