---
title: CyberTron Enterprise Infrastructure Architecture
version: 0.1
milestone: Foundation Baseline
status: Draft
classification: Private
author: Wayne Stynder
repository: CyberTron-Enterprise
last_updated: 2026-07-19
---

# CyberTron Enterprise Infrastructure Architecture

> **Version 0.1 – Foundation Baseline**

---

# 1. Purpose

This document defines the initial enterprise architecture for the CyberTron Enterprise environment.

CyberTron is a one-person cybersecurity consultancy designed to emulate a modern Small-to-Medium Business (SMB) enterprise. The infrastructure serves both as the operational platform for CyberTron and as a continuous learning environment covering:

- Enterprise Windows Administration
- Linux Administration
- Cybersecurity Operations
- Network Engineering
- Microsoft 365 Administration
- Governance, Risk & Compliance (GRC)
- Infrastructure Automation
- Documentation and Engineering Practices

This document represents the approved baseline architecture before implementation begins.

---

# 2. Vision

Design and operate an enterprise-grade infrastructure using repurposed hardware while following professional engineering practices.

The environment should demonstrate:

- Infrastructure planning
- Systems engineering
- Security architecture
- Enterprise documentation
- Operational runbooks
- Automation
- Continuous improvement

---

# 3. Business Profile

| Item | Value |
|------|-------|
| Business | CyberTron |
| Industry | Cybersecurity Consulting |
| Current Staff | 1 |
| Target Size | 5–30 Employees |
| Primary Clients | Small & Medium Businesses |

---

# 4. Business Services

CyberTron intends to provide:

- Security Assessments
- Network Hardening
- Security Awareness Training
- Vulnerability Management
- Security Documentation
- Cyber Insurance Readiness
- Microsoft 365 Administration
- Incident Response
- Governance, Risk & Compliance (Future)

---

# 5. Design Principles

The following principles govern all architectural decisions.

## 5.1 Documentation First

Design before implementation.

## 5.2 Simplicity

Choose the simplest architecture that satisfies business requirements.

## 5.3 Security by Design

Security is built into every layer.

## 5.4 Enterprise Alignment

Use technologies and practices common within SMB enterprise environments.

## 5.5 Budget Conscious

Maximise value by repurposing existing hardware.

## 5.6 Repeatability

Every major configuration should be reproducible using documented procedures.

---

# 6. Enterprise Architecture

```
                    Internet

             Home Fibre ONT
                    │
             ASUS RT-AC88U
                    │
      Home Network / Family Devices

--------------------------------------------------

            Skinny 4G Broadband
                    │
          CyberTron Enterprise
                    │
        ┌───────────┴───────────┐
        │                       │
 Linux Infrastructure     Windows Infrastructure
```

---

# 7. Infrastructure Roles

| Device | Planned Role |
|---------|--------------|
| Dell OptiPlex 7050 | Linux Infrastructure Host (Proxmox) |
| Legacy Desktop | Windows Enterprise Services Host |
| Shuttle DS77U | Windows Administration Platform |
| HP ProBook 450 G6 | Primary Administration Workstation |
| Spare HP ProBook | Linux Mint Security Consultant Laptop |
| Mac mini | Apple Management Platform |

---

# 8. Storage Architecture

## Legacy Desktop

| Storage | Purpose |
|----------|----------|
| 256 GB SSD | Windows 11 Pro Host |
| 480 GB SSD | Hyper-V Virtual Machines |
| 3 × 2 TB HDD | Storage Spaces (Windows Server VM) |
| 500 GB HDD | Local Data / X-Plane |

Storage Spaces will use **Two-Way Mirror** for performance and resiliency.

---

# 9. Windows Platform

Planned technologies:

- Windows 11 Pro
- Hyper-V
- Windows Server 2022
- Active Directory
- DNS
- SMB File Services
- PowerShell
- Windows Admin Center (Future)

---

# 10. Linux Platform

Planned technologies:

- Proxmox
- Ubuntu Server
- Ubuntu Desktop
- Kali Linux
- Docker
- Git
- SSH
- Ansible (Future)

---

# 11. Security Platform

Planned technologies:

- pfSense
- Wazuh
- Microsoft Defender
- Security Onion (Future)
- Splunk Evaluation (Future)

---

# 12. Documentation Strategy

The GitHub repository is the authoritative source for all CyberTron engineering documentation.

The repository contains:

- Architecture Documents
- Architecture Decision Records (ADRs)
- Build Logs
- Runbooks
- Asset Inventory
- Network Documentation
- Lessons Learned

---

# 13. Implementation Roadmap

| Phase | Status |
|------|--------|
| Phase 1 – Hardware Baseline | 🔄 In Progress |
| Phase 2 – Windows Enterprise | ⏳ Planned |
| Phase 3 – Linux Infrastructure | ⏳ Planned |
| Phase 4 – Identity Services | ⏳ Planned |
| Phase 5 – Security Monitoring | ⏳ Planned |
| Phase 6 – Automation | ⏳ Planned |
| Phase 7 – Operational Readiness | ⏳ Planned |

---

# 14. Version History

| Version | Milestone | Status |
|----------|-----------|--------|
| v0.1 | Foundation Baseline | Current |

---

# Next Document

**ADR-001 — Why CyberTron Exists as an Enterprise Architecture Model**
