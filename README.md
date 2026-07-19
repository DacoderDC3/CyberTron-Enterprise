# CyberTron Enterprise

> **Enterprise IT & Cybersecurity Infrastructure Project**

[![Status](https://img.shields.io/badge/Status-Foundation%20Phase-blue)](#current-project-status)
[![Architecture](https://img.shields.io/badge/Architecture-v0.1-green)](docs/01-Architecture/)
[![Documentation](https://img.shields.io/badge/Documentation-Engineering%20First-success)](docs/)
[![License](https://img.shields.io/badge/License-Private-lightgrey)](#)

---

## Overview

CyberTron Enterprise is a long-term engineering project that designs, builds and operates the complete IT infrastructure for a fictional cybersecurity consultancy.

Rather than functioning as a traditional homelab, the project models a realistic Small-to-Medium Business (SMB) environment to develop practical experience in:

- Enterprise Infrastructure
- Windows Administration
- Linux Administration
- Networking
- Cybersecurity Operations
- Microsoft 365
- Infrastructure Automation
- Governance, Risk & Compliance (GRC)
- Engineering Documentation

Every major engineering decision is documented before implementation and every implementation is traceable back to an approved architectural decision.

---

# Project Objectives

The CyberTron Enterprise project has four primary objectives.

- Design enterprise-grade infrastructure using repurposed hardware.
- Develop practical Windows, Linux and cybersecurity engineering skills.
- Produce professional engineering documentation.
- Build a portfolio demonstrating real-world infrastructure design, implementation and continual improvement.

---

# CyberTron Engineering Management System (CEMS)

CyberTron follows a structured engineering methodology.

```text
CyberTron Engineering Management System (CEMS)

├── CIEL
│   CyberTron Infrastructure Engineering Lifecycle
│
├── Enterprise Architecture
│
├── Architecture Decision Records
│
└── Operational Documentation
```

Every engineering activity follows the same lifecycle:

```text
Business Requirement
        │
        ▼
Architecture
        │
        ▼
Architecture Decision Record
        │
        ▼
Implementation
        │
        ▼
Validation
        │
        ▼
Build Log
        │
        ▼
Runbook
        │
        ▼
Lessons Learned
```

---

# Documentation Structure

| Folder | Description |
|---------|-------------|
| `docs/00-Governance` | Engineering governance, methodology and standards |
| `docs/01-Architecture` | Enterprise architecture and strategic design |
| `docs/02-Architecture-Decisions` | Architecture Decision Records (ADRs) |
| `docs/03-Build-Logs` | Chronological implementation history |
| `docs/04-Runbooks` | Operational procedures |
| `docs/05-Inventory` | Hardware and software inventories |
| `docs/06-Lessons-Learned` | Continuous improvement and troubleshooting |
| `docs/07-Projects` | Individual implementation projects |

---

# Enterprise Architecture Pillars

CyberTron Enterprise is built around seven architectural pillars.

| Pillar | Purpose |
|---------|---------|
| Business | Business objectives and operating model |
| Compute | Enterprise compute platforms |
| Storage | Enterprise storage and resiliency |
| Network | Connectivity and segmentation |
| Identity | Authentication and authorisation |
| Security | Monitoring and defence |
| Operations | Administration, automation and continual improvement |

---

# Current Project Status

## Phase

**Phase 1 — Enterprise Foundation**

### Completed

- Repository structure
- Engineering governance
- CyberTron Engineering Management System (CEMS)
- CyberTron Infrastructure Engineering Lifecycle (CIEL)
- Enterprise Architecture v0.1
- ADR-001 through ADR-007

### Next Milestone

**Windows Enterprise Platform**

- Windows Server 2022
- Active Directory
- DNS
- Group Policy
- Enterprise Storage
- PowerShell Automation

---

# Hardware Platforms

| Platform | Primary Role |
|----------|--------------|
| Dell OptiPlex 7050 | Linux Infrastructure (Proxmox) |
| Legacy Desktop | Windows Enterprise Services |
| Shuttle DS77U | Windows Administration Platform |
| HP ProBook 450 G6 | Administration Workstation |
| HP ProBook (Spare) | Linux Mint Security Consultant Workstation |
| Mac mini | Apple Enterprise Management |

---

# Current Technologies

### Infrastructure

- Proxmox VE
- Hyper-V
- Windows Server 2022
- Ubuntu Server
- Linux Mint

### Security

- Wazuh
- Microsoft Defender
- pfSense *(planned)*
- Security Onion *(planned)*

### Automation

- PowerShell
- Bash
- Docker
- Git

---

# Repository Philosophy

CyberTron Enterprise is not intended to demonstrate how many technologies can be installed.

Instead, it demonstrates:

- Engineering discipline
- Enterprise architecture
- Structured decision making
- Documentation standards
- Continuous improvement

Every technology deployed within CyberTron must satisfy a business requirement, support a learning objective, or improve the operational capability of the enterprise.

---

# Repository Roadmap

- [ ] Windows Enterprise Platform
- [ ] Linux Infrastructure Platform
- [ ] Enterprise Networking
- [ ] Security Operations
- [ ] Automation
- [ ] Operational Readiness
- [ ] Version 1.0 – Initial Operating Capability (IOC)

---

# Author

**Wayne Stynder**

Founder — CyberTron

Enterprise Infrastructure | Windows Administration | Cybersecurity | Automation | Documentation
