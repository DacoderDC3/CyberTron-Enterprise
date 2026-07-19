---
title: ADR-003 - Standardise on Windows 11 Pro Hyper-V for Enterprise Services
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
---

# ADR-003 — Standardise on Windows 11 Pro Hyper-V for Enterprise Services

## Status

**Accepted**

---

# Context

CyberTron requires a realistic Microsoft enterprise environment to support learning, experimentation and operational capability in:

- Windows Server Administration
- Active Directory
- Group Policy
- PowerShell
- Hyper-V
- Microsoft 365
- Identity Management
- Enterprise File Services

Initially, several approaches were considered, including consolidating everything onto Proxmox or deploying multiple Windows hosts.

The challenge was to create an enterprise-grade Windows environment while making effective use of the available refurbished hardware.

---

# Decision

The Legacy Desktop will become the dedicated **Windows Enterprise Services Host**.

The host operating system will remain **Windows 11 Pro**.

Microsoft Hyper-V will be used as the primary virtualisation platform for all Windows enterprise workloads.

The first virtual machine will be Windows Server 2022.

---

# Decision Evolution

## Initial Design

The legacy desktop was originally intended to function primarily as:

- Storage server
- X-Plane 11 gaming system
- General-purpose workstation

Storage Spaces was initially configured directly on the Windows 11 host.

---

## Revised Design

As the CyberTron architecture matured, the machine's role expanded.

The storage architecture was redesigned.

Hyper-V became available after:

- Enabling Intel VT-x
- Upgrading memory to 32 GB
- Installing a dedicated SSD for virtual machines

This created the opportunity to separate infrastructure responsibilities.

---

## Final Decision

The legacy desktop will become the dedicated Windows Enterprise Services platform.

Windows Server 2022 will operate as a Hyper-V virtual machine.

Enterprise services will execute inside virtual machines rather than directly on the Windows host.

The Windows host itself will remain lightweight and primarily provide:

- Hyper-V
- Windows management
- Local administration
- X-Plane 11
- Hyper-V storage

---

# Scope

The Windows Enterprise platform will eventually provide:

- Active Directory Domain Services
- DNS
- Group Policy
- SMB File Services
- Storage Spaces
- PowerShell Remoting
- Windows Administration
- Enterprise identity management

Future services may include:

- Certificate Services
- Windows Admin Center
- WSUS
- Microsoft Entra integration
- Microsoft Defender
- Microsoft 365 hybrid management

---

# Rationale

## 1. Enterprise Realism

Most organisations separate their Windows server infrastructure from Linux infrastructure.

This architecture mirrors that approach.

---

## 2. Hyper-V Experience

Hyper-V remains Microsoft's enterprise virtualisation platform.

Learning Hyper-V provides valuable experience directly applicable to Windows administration roles.

---

## 3. PowerShell Integration

Hyper-V integrates naturally with PowerShell.

This supports CyberTron's objective of developing scripting and automation skills.

---

## 4. Windows Server Learning

Running Windows Server inside Hyper-V enables practical experience with:

- Active Directory
- DNS
- Group Policy
- Enterprise storage
- Identity management

without requiring dedicated physical server hardware.

---

## 5. Hardware Utilisation

The upgraded hardware provides sufficient resources.

Current specification:

- Intel Core i7-2600
- 32 GB DDR3
- Windows 11 Pro
- Hyper-V
- SSD-backed VM storage

This is sufficient for the planned enterprise services.

---

# Memory Allocation Strategy

The initial Hyper-V allocation shall be:

| Component | Memory |
|-----------|--------:|
| Windows 11 Pro Host | 8 GB |
| Windows Server 2022 | 8 GB |
| Ubuntu Server (future) | 4 GB |
| Hyper-V Cache / Expansion | 12 GB |

This provides sufficient headroom for future expansion while avoiding unnecessary paging.

---

# Storage Strategy

Windows 11 Host

- 256 GB SSD
- Host operating system

Virtual Machines

- 480 GB SSD
- Hyper-V virtual disks
- VM checkpoints

Windows Server VM

- Direct access to three physical 2 TB HDDs

Storage Spaces

- Two-Way Mirror
- SMB File Shares
- Enterprise storage

---

# Alternatives Considered

## Windows Server on Bare Metal

### Rejected

Advantages

- Simpler deployment

Disadvantages

- No Hyper-V learning
- Reduced flexibility
- Harder rollback
- Limited experimentation

---

## Windows Server hosted on Proxmox

### Rejected

Advantages

- Single hypervisor

Disadvantages

- Reduced exposure to Hyper-V
- Less realistic Microsoft administration environment
- Missed opportunity to learn Microsoft's virtualisation ecosystem

---

## Windows 11 Only

### Rejected

Advantages

- Simplicity

Disadvantages

- No enterprise identity services
- No Active Directory
- No Group Policy
- Limited Windows administration experience

---

# Consequences

## Positive

- Dedicated Microsoft enterprise platform
- Hyper-V experience
- Active Directory experience
- Enterprise identity management
- PowerShell automation opportunities
- Clear separation from Linux infrastructure

## Negative

- Additional management overhead
- Separate backup strategy
- Two virtualisation platforms to maintain

These trade-offs are acceptable because they significantly increase enterprise administration experience.

---

# Related ADRs

- ADR-001 — CyberTron Enterprise Model
- ADR-002 — Proxmox Linux Infrastructure
- ADR-004 — Storage Architecture
- ADR-005 — Network Segmentation Strategy

---

# Design Principle

> Microsoft enterprise technologies should be implemented using Microsoft's own management ecosystem wherever practical.

This provides realistic experience with the tools, workflows and administration practices commonly found in SMB enterprise environments.
