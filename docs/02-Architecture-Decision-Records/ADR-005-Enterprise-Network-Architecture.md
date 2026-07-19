---
title: ADR-005 - Enterprise Network Architecture
version: 0.1
status: Accepted
classification: Internal
author: Wayne Stynder
created: 2026-07-19
last_updated: 2026-07-19
related_documents:
  - ../01-Architecture/CyberTron-Enterprise-Infrastructure-v0.1.md
---

# ADR-005 — Enterprise Network Architecture

## Purpose

Define the logical network architecture supporting both the CyberTron Enterprise environment and the household environment.

---

## Scope

This ADR defines:

- Network separation
- Internet connectivity
- Lab isolation
- Host responsibilities
- Future segmentation strategy

This ADR does not define firewall rules or IP addressing schemes.

---

## Audience

- Infrastructure Architects
- Systems Administrators
- Security Engineers

---

# Status

**Accepted**

---

# Context

CyberTron Enterprise shares a physical residence with a family home network.

The enterprise environment must support experimentation, malware analysis, Windows administration and cybersecurity training without affecting household devices.

The design should minimise operational complexity while providing realistic enterprise networking concepts.

---

# Decision

The network shall be separated into two logical environments.

| Network | Purpose |
|----------|----------|
| Home Network | Family devices, entertainment, IoT |
| CyberTron Enterprise | Enterprise infrastructure and security laboratory |

The CyberTron Enterprise environment shall use a dedicated 4G Internet connection, independent of the primary household Fibre connection.

---

# Decision Evolution

## Initial Design

Single home network with all systems connected.

---

## Revised Design

Considered VLAN segmentation using consumer networking equipment.

Rejected due to hardware limitations and unnecessary complexity.

---

## Final Decision

Maintain two physically separate environments.

Future internal segmentation may be introduced through pfSense as enterprise requirements grow.

---

# Rationale

- Protect household devices.
- Prevent experimentation affecting the home network.
- Simulate an isolated client environment.
- Simplify troubleshooting.
- Improve enterprise realism.

---

# Network Responsibilities

## Home

- ASUS RT-AC88U
- Mesh Wi-Fi
- Personal devices
- Entertainment
- IoT

## CyberTron Enterprise

- 4G Router
- Dell OptiPlex
- Legacy Desktop
- DS77U
- Security Workstations
- Enterprise Servers

---

# Future Expansion

Future phases may include:

- pfSense
- Internal VLANs
- VPN
- IDS/IPS
- Site-to-site VPN simulation
- Zero Trust concepts

---

# Consequences

## Positive

- Strong isolation
- Reduced operational risk
- Simple architecture
- Enterprise learning

## Negative

- Two Internet connections
- Duplicate networking hardware

---

# Related ADRs

- ADR-001
- ADR-002
- ADR-003
- ADR-004
