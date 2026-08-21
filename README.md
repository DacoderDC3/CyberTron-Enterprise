# CyberTron Enterprise

> **Enterprise IT & Cybersecurity Infrastructure Project**

[![Status](https://img.shields.io/badge/Status-Foundation%20Phase-blue)](#current-project-status)
[![Architecture](https://img.shields.io/badge/Architecture-v0.1-green)](docs/01-Architecture/)
[![Documentation](https://img.shields.io/badge/Documentation-Engineering%20First-success)](docs/)
[![License](https://img.shields.io/badge/License-Private-lightgrey)](#)

---

Overview

CyberTron Enterprise is a long-term engineering project that designs, builds and operates the complete IT infrastructure for a fictional cybersecurity consultancy.

Rather than functioning as a traditional homelab, the project models a realistic Small-to-Medium Business (SMB) environment to develop practical experience in:

* Enterprise Infrastructure
* Windows Administration
* Linux Administration
* Networking
* Cybersecurity Operations
* Microsoft 365
* Infrastructure Automation
* Governance, Risk & Compliance (GRC)
* Engineering Documentation

Every major engineering decision is documented before implementation and every implementation is traceable back to an approved architectural decision.

⸻

Project Objectives

The CyberTron Enterprise project has four primary objectives.

* Design enterprise-grade infrastructure using repurposed hardware.
* Develop practical Windows, Linux and cybersecurity engineering skills.
* Produce professional engineering documentation.
* Build a portfolio demonstrating real-world infrastructure design, implementation and continual improvement.

⸻

CyberTron Engineering Management System (CEMS)

CyberTron follows a structured engineering methodology.

CyberTron Engineering Management System (CEMS)
├── CIEL
│   CyberTron Infrastructure Engineering Lifecycle
│
├── Enterprise Architecture
│
├── Architecture Decision Records
│
└── Operational Documentation

Every engineering activity follows the same lifecycle:

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

⸻

Documentation Structure

Folder	Description
docs/00-Governance	Engineering governance, methodology and standards
docs/01-Architecture	Enterprise architecture and strategic design
docs/02-Architecture-Decisions	Architecture Decision Records (ADRs)
docs/03-Build-Logs	Chronological implementation history
docs/04-Runbooks	Operational procedures
docs/05-Inventory	Hardware and software inventories
docs/06-Lessons-Learned	Continuous improvement and troubleshooting
docs/07-Projects	Individual implementation projects

⸻

Enterprise Architecture Pillars

CyberTron Enterprise is built around seven architectural pillars.

Pillar	Purpose
Business	Business objectives and operating model
Compute	Enterprise compute platforms
Storage	Enterprise storage and resiliency
Network	Connectivity and segmentation
Identity	Authentication and authorisation
Security	Monitoring and defence
Operations	Administration, automation and continual improvement

⸻

Current Project Status

Phase

Phase 1 — Enterprise Foundation

Completed

* Repository structure
* Engineering governance
* CyberTron Engineering Management System (CEMS)
* CyberTron Infrastructure Engineering Lifecycle (CIEL)
* Enterprise Architecture v0.2
* ADR-001 through ADR-008
* MikroTik RouterOS enterprise network gateway
* CyberTron 172.16.10.0/24 network foundation
* Skinny LTE upstream gateway integration
* Central DHCP and DNS forwarding
* Stateful firewall and source NAT baseline
* TP-Link Gigabit access switching
* Proxmox network integration
* Hyper-V network integration
* Windows Server 2022 network integration
* Ubuntu Server and Ubuntu client integration
* Wazuh SIEM network integration
* macOS endpoint integration
* Router management hardening
* Router configuration backups
* Controlled router recovery testing
* Network baseline and inventory documentation

Current Focus

Windows Enterprise Platform

The network foundation is now operational and provides the platform for the next enterprise-services phase.

Planned activities:

* Active Directory Domain Services
* Active Directory-integrated DNS
* Organisational Units
* Users and Groups
* Group Policy
* Enterprise Storage and SMB
* PowerShell Administration
* Windows Event Logging
* Wazuh endpoint/security integration

⸻

Enterprise Networking Status

Foundation

Complete

CyberTron now uses a dedicated MikroTik RouterOS gateway:

Internet
   │
   ▼
Skinny 4G Router
192.168.8.1/24
   │
   ▼
CYB-RTR-01
MikroTik RB960PGS
   │
   ▼
172.16.10.0/24
CyberTron LAN

CYB-RTR-01 currently provides:

* IPv4 routing
* DHCP
* DNS forwarding
* Stateful firewalling
* Source NAT
* Secure management services
* Infrastructure DHCP reservations
* Future VLAN/inter-VLAN routing capability

Current Network

Upstream WAN:   192.168.8.0/24
CyberTron LAN:  172.16.10.0/24
Gateway:        172.16.10.1
DHCP Pool:      172.16.10.100-199

Current Infrastructure

IP	System	Role
172.16.10.1	CYB-RTR-01	Router / Firewall / DHCP / DNS
172.16.10.10	CYB-SRV01	Windows Server 2022
172.16.10.11	ASUS Hyper-V Host	Windows 11 / Hyper-V
172.16.10.12	Proxmox	Dell OptiPlex 7050
172.16.10.13	srv-ubuntu-01	Ubuntu Server
172.16.10.14	siem-wazuh-01	Wazuh SIEM
172.16.10.20	cli-w11pro-01	Administration Workstation
172.16.10.25	cli-macos-01	Mac mini

Ordinary client systems currently use the dynamic DHCP pool.

⸻

Enterprise Networking — Next Phase

The network foundation is intentionally flat at this stage.

Planned

* Management VLAN
* Server VLAN
* Client VLAN
* Security / SIEM VLAN
* Untrusted / IoT VLAN
* Attack / Red-Team network
* Inter-VLAN firewall policy
* Policy-based routing
* Dedicated management network
* Additional MikroTik routers
* Managed VLAN-capable switching

VLAN segmentation will be introduced only after the current routing, DHCP, DNS, firewall and endpoint architecture is fully understood and documented.

⸻

Hardware Platforms

Platform	Primary Role
Dell OptiPlex 7050	Proxmox Virtualisation / Linux & Security Infrastructure
Legacy ASUS Desktop	Windows Enterprise Services / Hyper-V
Shuttle DS77U	Future CyberTron Compute / Network Lab Platform — role under evaluation
HP ProBook 450 G6	Administration Workstation
HP ProBook (Spare)	Linux Mint Security Consultant Workstation
Mac mini 7,1	Apple Enterprise Management / Endpoint

Additional hardware is retained for future security, testing and recovery exercises.

⸻

Current Technologies

Infrastructure

* Proxmox VE
* Hyper-V
* Windows Server 2022
* Ubuntu Server
* Linux Mint
* MikroTik RouterOS 7

Networking & Security

* MikroTik RouterOS Firewall
* MikroTik NAT
* DHCP
* DNS Forwarding
* Wazuh
* Microsoft Defender
* Security Onion (planned)
* VLAN Segmentation (planned)

Automation

* PowerShell
* Bash
* Docker
* Git

⸻

Operational Readiness

CyberTron infrastructure is being developed using configuration management and recovery principles.

Current controls include:

* Router configuration exports
* RouterOS binary backups
* Off-device backup storage
* Proxmox snapshots/backups
* Controlled recovery testing
* Network inventory
* Architecture documentation
* Architecture Decision Records
* Build logs
* Operational runbooks
* Lessons-learned records

The primary network router has successfully completed a controlled reboot and recovery test.

⸻

Repository Philosophy

CyberTron Enterprise is not intended to demonstrate how many technologies can be installed.

Instead, it demonstrates:

* Engineering discipline
* Enterprise architecture
* Structured decision making
* Documentation standards
* Change management
* Troubleshooting methodology
* Recovery planning
* Continuous improvement

Every technology deployed within CyberTron must satisfy a business requirement, support a learning objective, or improve the operational capability of the enterprise.

⸻

Repository Roadmap

* Enterprise Foundation
* Enterprise Networking — Foundation
* Enterprise Networking — VLAN Segmentation
* Windows Enterprise Platform — Active Directory
* Identity & Access Management
* Security Operations
* Automation
* Operational Readiness
* Microsoft 365 / Entra Integration
* Initial Operating Capability (IOC)
* Version 1.0 — Enterprise Baseline

⸻

Key Documentation

Architecture

* Network Architecture⁠￼

Architecture Decisions

* ADR-008 — MikroTik Network Gateway⁠￼

Build Logs

* BL-008 — CyberTron Network Migration⁠￼

Runbooks

* RB-001 — CYB-RTR-01 Backup and Restore⁠￼
* RB-002 — CYB-RTR-01 Recovery Verification⁠￼

Inventory

* Network Inventory⁠￼

Lessons Learned

* LL-004 — Network Migration and Troubleshooting⁠￼

Projects

* Enterprise Networking Project⁠￼

⸻

Author

Wayne Stynder

Founder — CyberTron
Enterprise Infrastructure | Windows Administration | Cybersecurity | Automation | Documentation
