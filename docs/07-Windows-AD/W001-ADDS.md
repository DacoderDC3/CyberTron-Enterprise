# CyberTron Active Directory Security Baseline — Pre-CIS Checkpoint

**Lab:** CyberTron Security Homelab  
**Domain:** `ad.cybertron.test`  
**Domain Controller:** `CYB-SRV01`  
**Operating System:** Windows Server 2022  
**Checkpoint Purpose:** Record the Active Directory configuration immediately before formal CIS Benchmark assessment and remediation.

---

## 1. Purpose

This document records the security configuration of the CyberTron Active Directory environment before formal assessment against the CIS Microsoft Windows Server 2022 Benchmark.

The objective is to preserve a known-good baseline so that later CIS hardening changes can be:

- traced;
- tested;
- validated;
- compared against the previous state;
- rolled back if necessary; and
- documented as security-control evidence.

This checkpoint represents the transition from initial Active Directory deployment into structured security hardening and compliance assessment.

---

## 2. Current Active Directory Environment

### Domain

| Item | Configuration |
|---|---|
| AD DNS Domain | `ad.cybertron.test` |
| NetBIOS Domain | `CYBERTRON` |
| Domain Controller | `CYB-SRV01` |
| Server IP | `172.16.10.10` |
| Server Role | Active Directory Domain Services / DNS |
| Operating System | Windows Server 2022 |

---

## 3. Organisational Unit Structure

The following custom OU hierarchy has been created beneath the root CyberTron OU.

```text
OU=CyberTron
│
├── OU=Users
│   ├── OU=Corporate
│   └── OU=IT
│
├── OU=Privileged
│   ├── OU=Admin Accounts
│   └── OU=Admin Workstations
│
├── OU=Computers
│   ├── OU=Windows
│   └── OU=macOS
│
├── OU=Servers
│   ├── OU=Windows
│   ├── OU=Linux
│   └── OU=macOS
│
├── OU=Groups
│   ├── OU=Security
│   └── OU=Distribution
│
├── OU=Service Accounts
│
└── OU=Disabled Objects
    ├── OU=Users
    └── OU=Computers