# ADR-008 — Adopt MikroTik RouterOS as the CyberTron Enterprise Network Gateway

- **Status:** Accepted
- **Date:** 2026-08-21
- **Decision Owners:** CyberTron Enterprise
- **Architecture Area:** Network
- **Related Architecture:** Enterprise Architecture v0.2
- **Related Build Log:** `BL-008-CyberTron-Network-Migration.md`

---

## 1. Context

CyberTron Enterprise initially used a Skinny Broadband 4G router as the primary network gateway for the cybersecurity homelab.

The original arrangement placed the CyberTron systems directly on the 4G router's LAN:

```text
Skinny 4G Router
172.16.10.1/24
        │
        ├── Hyper-V
        ├── Proxmox
        ├── Ubuntu
        ├── Wazuh
        ├── Windows Server
        └── Administration Workstation