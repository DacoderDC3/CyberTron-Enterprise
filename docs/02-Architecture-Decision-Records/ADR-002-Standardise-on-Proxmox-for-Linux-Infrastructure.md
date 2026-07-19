---
title: ADR-002 - Standardise on Proxmox VE as the Primary Linux Infrastructure Platform
version: 0.1
status: Accepted
classification: Private
author: Wayne Stynder
created: 2026-07-19
last_updated: 2026-07-19
related_documents:
  - ../01-Architecture/CyberTron-Enterprise-Infrastructure-v0.1.md
  - ADR-001-CyberTron-Enterprise-Model.md
---

# ADR-002 — Standardise on Proxmox VE as the Primary Linux Infrastructure Platform

## Status

**Accepted**

---

# Context

CyberTron requires a stable Linux infrastructure capable of supporting enterprise services, cybersecurity tooling, and virtualised laboratory environments.

Several virtualisation approaches were considered during the planning phase, including:

- Multiple Proxmox hosts
- Hyper-V
- VMware Workstation
- VirtualBox
- Native Linux installations
- KVM on Linux workstations

The available hardware consists primarily of refurbished business-class computers with varying capabilities.

A key design objective is to minimise operational complexity while maximising learning opportunities and maintaining enterprise realism.

---

# Decision

CyberTron will standardise on **Proxmox Virtual Environment (VE)** as the primary Linux virtualisation platform.

The Dell OptiPlex 7050 SFF will become the dedicated Linux infrastructure host.

It will remain a dedicated Proxmox server rather than being repurposed as a Windows workstation or dual-purpose system.

---

# Scope

The Dell OptiPlex will host infrastructure services including:

- Ubuntu Server
- Ubuntu Desktop
- Kali Linux
- Docker workloads
- pfSense (lab)
- Wazuh
- Security Onion (future)
- Test virtual machines

All Linux-based enterprise infrastructure will be consolidated onto this host wherever practical.

---

# Rationale

## 1. Single Virtualisation Platform

Maintaining one dedicated Linux hypervisor simplifies:

- administration
- backups
- storage
- networking
- troubleshooting
- documentation

---

## 2. Enterprise Alignment

Proxmox is widely used in:

- SMB environments
- managed service providers
- homelabs
- edge deployments

The knowledge gained is directly transferable to enterprise Linux virtualisation.

---

## 3. Hardware Optimisation

The Dell OptiPlex 7050 provides:

- Intel VT-x support
- sufficient RAM
- dedicated NVMe storage
- low power consumption
- enterprise-grade reliability

These characteristics make it the strongest Linux virtualisation platform available within the existing hardware inventory.

---

## 4. Separation of Responsibilities

Separating Windows and Linux infrastructure reduces operational complexity.

| Platform | Responsibility |
|----------|----------------|
| Dell OptiPlex | Linux Infrastructure |
| Legacy Desktop | Windows Enterprise Services |
| DS77U | Windows Administration |

This separation mirrors many enterprise environments.

---

## 5. Future Expansion

The platform provides sufficient flexibility to support future services including:

- Docker
- Kubernetes (future)
- Git services
- monitoring platforms
- additional Linux distributions
- cybersecurity appliances

without requiring architectural redesign.

---

# Alternatives Considered

## Multiple Proxmox Hosts

### Rejected

Advantages

- High availability possibilities
- Additional experimentation

Disadvantages

- Increased complexity
- More hardware maintenance
- Duplicate administration
- Greater power consumption
- Limited practical benefit for a single-engineer environment

---

## Hyper-V as the Primary Hypervisor

### Rejected

Advantages

- Excellent Windows integration

Disadvantages

- Linux infrastructure becomes secondary
- Less representative of enterprise Linux environments
- Reduces exposure to Linux virtualisation technologies

Hyper-V remains the preferred platform for Windows workloads only.

---

## VMware Workstation

### Rejected

Reasons

- Licensing uncertainty
- Product direction
- Limited benefit compared with Proxmox

---

## VirtualBox

### Rejected

Reasons

- Desktop-oriented
- Lower enterprise relevance
- Less suitable for long-running infrastructure services

---

## Bare-Metal Linux Servers

### Rejected

Advantages

- Simplicity

Disadvantages

- No snapshots
- Limited isolation
- Reduced flexibility
- Less opportunity to learn enterprise virtualisation

---

# Consequences

## Positive

- Single Linux virtualisation platform
- Clear separation between Windows and Linux environments
- Easier documentation
- Easier backups
- Simplified disaster recovery
- Better utilisation of existing hardware
- Excellent platform for cybersecurity tooling

## Negative

- Single point of failure for Linux workloads
- No high-availability capability
- Future hardware upgrades require downtime

These trade-offs are acceptable for a one-person consultancy.

---

# Future Considerations

Future expansion may include:

- Proxmox Backup Server
- Cluster evaluation
- Ceph evaluation
- High availability concepts

These are not required for the current business size and will be evaluated only if they support a defined learning or business objective.

---

# Implementation

Primary Host

**Dell OptiPlex 7050 SFF**

Primary Hypervisor

**Proxmox VE**

Primary Responsibilities

- Linux virtualisation
- Cybersecurity infrastructure
- Docker services
- Infrastructure experimentation

---

# Related ADRs

- ADR-001 — CyberTron Enterprise Model
- ADR-003 — Windows Enterprise Services Platform
- ADR-004 — Storage Architecture
- ADR-005 — Network Segmentation Strategy

---

> **Design Principle**

> CyberTron will minimise infrastructure complexity by assigning each physical platform a clearly defined responsibility. Linux virtualisation is standardised on Proxmox VE to provide a stable, enterprise-aligned platform for infrastructure services and cybersecurity workloads.
