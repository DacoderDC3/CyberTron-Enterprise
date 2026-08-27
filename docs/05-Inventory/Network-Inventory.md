# CyberTron Enterprise — Network Inventory

- **Document Type:** Infrastructure Inventory
- **Version:** 1.1
- **Date:** 2026-08-27
- **Status:** Active
- **Network:** CyberTron Enterprise

---

## 1. Purpose

This document provides the authoritative current inventory of CyberTron network infrastructure, addressing and network roles.

It supports:

- operations;
- troubleshooting;
- change management;
- security monitoring;
- asset management;
- remote administration;
- future network segmentation.

---

## 2. Network Summary

### CyberTron LAN

```text
Network: 172.16.10.0/24
Gateway: 172.16.10.1
DNS:     172.16.10.1
DHCP:    172.16.10.100-199
```

### Upstream Network

```text
Network: 192.168.8.0/24
Gateway: 192.168.8.1
```

---

## 3. Infrastructure Addressing

| IP Address | Hostname / Asset | Platform | Role | Address Method |
|---|---|---|---|---|
| `172.16.10.1` | CYB-RTR-01 | MikroTik RouterOS | Router / Firewall / DHCP / DNS | Static |
| `172.16.10.10` | CYB-SRV01 | Windows Server 2022 | Windows Enterprise Server | DHCP Reservation |
| `172.16.10.11` | ASUS Hyper-V Host | Windows 11 Pro | Hyper-V Host | DHCP Reservation |
| `172.16.10.12` | pve | Proxmox VE | Virtualisation Host | Local Static |
| `172.16.10.13` | srv-ubuntu-01 | Ubuntu Server | Linux Infrastructure Server | DHCP Reservation |
| `172.16.10.14` | siem-wazuh-01 | Ubuntu / Wazuh | SIEM / Security Monitoring | DHCP Reservation |
| `172.16.10.20` | cli-w11pro-01 | Windows 11 Pro | Administration Workstation | DHCP Reservation |
| `172.16.10.25` | cli-macos-01 | macOS | Apple Endpoint | DHCP Reservation |
| Dynamic | cli-ubuntu-01 | Ubuntu | Linux Client | DHCP |

---

## 4. Known MAC Addresses

| Asset | MAC Address |
|---|---|
| CYB-SRV01 | `00:15:5D:BE:61:01` |
| ASUS Hyper-V Host | `60:A4:4C:3D:DF:82` |
| srv-ubuntu-01 | `BC:24:11:9E:CB:B0` |
| siem-wazuh-01 | `BC:24:11:BD:09:40` |
| cli-w11pro-01 | `80:E8:2C:1B:7F:1A` |
| cli-macos-01 | `98:5A:BE:C9:9E:13` |

CYB-SRV01's Hyper-V MAC is statically configured.

---

## 5. Addressing Standard

| Range | Purpose |
|---|---|
| `.1` | Default gateway |
| `.2-.9` | Reserved infrastructure |
| `.10-.19` | Servers / Hypervisors / Security Infrastructure |
| `.20-.49` | Administration and Managed Endpoints |
| `.50-.99` | Future Infrastructure |
| `.100-.199` | Dynamic DHCP |
| `.200-.254` | Future Reserve |

---

## 6. Physical Network Infrastructure

### CYB-RTR-01

```text
Model: MikroTik RB960PGS hEX PoE
RouterOS: 7.21.5 Long-term
Hostname: CYB-RTR-01
```

Interfaces:

```text
ether1 → WAN
ether2 → CyberTron LAN / TL-SG1008D
ether3 → Unused
ether4 → Unused
ether5 → Unused
sfp1   → Unused
```

---

### TP-Link TL-SG1008D

```text
Type: Unmanaged Gigabit Ethernet Switch
Role: CyberTron access switch
```

Current topology:

```text
CYB-RTR-01 ether2
        │
        ▼
TP-Link TL-SG1008D
```

The switch is appropriate for the current flat LAN.

A managed VLAN-capable switch will be required for future segmentation.

---

## 7. WAN Infrastructure

### Skinny 4G Router

```text
Role: Cellular WAN Gateway
LAN IP: 192.168.8.1
```

The Skinny router provides upstream LTE connectivity.

CyberTron internal routing, DHCP and firewall responsibilities reside on CYB-RTR-01.

---

## 8. Virtualisation Infrastructure

### Proxmox

```text
Host: pve
IP: 172.16.10.12
Platform: Dell OptiPlex 7050
```

Known VMs:

| VM | Function |
|---|---|
| srv-ubuntu-01 | Ubuntu Server |
| cli-ubuntu-01 | Ubuntu Client |
| siem-wazuh-01 | Wazuh SIEM |

The Proxmox host is configured so that:

```text
sleep
suspend
hibernate
hybrid-sleep
```

are prohibited.

---

### Hyper-V

```text
Host: ASUS Windows 11 Pro
IP: 172.16.10.11
```

Known VM:

```text
CYB-SRV01
172.16.10.10
Windows Server 2022
```

The Hyper-V host is configured not to automatically sleep while on AC power.

---

## 9. Administration Workstation

Primary administration workstation:

```text
cli-w11pro-01
172.16.10.20
```

Current management capabilities:

```text
RDP → CYB-SRV01
RDP → ASUS Hyper-V Host
SSH → cli-macOS-01
VNC → cli-macOS-01
SSH → CYB-RTR-01
WinBox → CYB-RTR-01
Web UI / SSH → Proxmox
```

---

## 10. Windows Remote Administration

RDP targets:

```text
172.16.10.10
172.16.10.11
```

Both currently use:

```text
Network Level Authentication
```

Windows Firewall Remote Desktop rules are restricted to:

```text
172.16.10.20
```

---

## 11. macOS Remote Administration

Mac mini:

```text
Hostname: cli-macos-01
IP:       172.16.10.25
User:     wst
```

Remote management methods:

```text
SSH             TCP/22
Screen Sharing  TCP/5900
```

Both were successfully tested from the Windows administration workstation.

---

## 12. Mac Wake-on-LAN

Mac Ethernet MAC:

```text
98:5A:BE:C9:9E:13
```

Wake-on-LAN configuration:

```text
womp 1
```

A Wake-on-LAN magic packet was successfully sent from:

```text
172.16.10.20
```

through the CyberTron broadcast network.

The Mac successfully woke and returned to remote-management availability.

---

## 13. Network Services

CYB-RTR-01 currently provides:

```text
DHCP
DNS forwarding
IPv4 routing
Source NAT
Stateful firewall
Management services
```

Future services will include:

```text
Inter-VLAN routing
Zone-based firewall policy
Policy routing
Management VLAN
```

---

## 14. DNS

CyberTron endpoints currently receive:

```text
172.16.10.1
```

as their DNS server.

This is a temporary foundation configuration.

When Active Directory DNS is implemented:

```text
Domain members
      ↓
Active Directory DNS
      ↓
DNS Forwarders
      ↓
External DNS
```

will replace the current domain-member DNS model.

---

## 15. Management Architecture

Current temporary management model:

```text
                   cli-w11pro-01
                    172.16.10.20
                          │
          ┌───────────────┼─────────────────┐
          │               │                 │
         RDP             SSH/VNC        Web/SSH/WinBox
          │               │                 │
       Windows           macOS         Network/Linux
```

Future architecture will replace this flat-LAN model with:

```text
Management VLAN
Dedicated Jump Host
Role-based administrative access
```

---

## 16. Current Network Status

```text
Foundation Network:      Operational
DHCP:                    Operational
Internal Routing:        Operational
Firewall:                Operational
NAT:                     Operational
DNS Forwarding:          Operational
Remote Administration:   Operational
Hyper-V Networking:      Operational
Proxmox Availability:    Hardened
Mac Wake-on-LAN:         Tested
Router Recovery:         Verified
```

---

## 17. Change Control

Update this inventory whenever:

- infrastructure addressing changes;
- a new infrastructure device is added;
- DHCP reservations change;
- MAC addresses change;
- management paths change;
- a system is retired;
- VLANs are introduced;
- hostnames change;
- infrastructure roles change.

---

## 18. Related Documentation

- `Network-Architecture-v0.2.md`
- `ADR-008-MikroTik-Network-Gateway.md`
- `BL-008-CyberTron-Network-Migration.md`
- `BL-009-CYB-SRV01-Foundation-Baseline.md`
- `RB-003-Windows-Remote-Administration.md`
- `RB-004-CyberTron-Infrastructure-Power-Management.md`
- `Windows-Server-Inventory.md`