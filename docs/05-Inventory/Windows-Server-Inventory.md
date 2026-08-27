# CyberTron Enterprise — Windows Server Inventory

- **Document Type:** Infrastructure Inventory
- **Version:** 1.0
- **Date:** 2026-08-27
- **Status:** Active

---

## 1. CYB-SRV01

| Attribute | Value |
|---|---|
| Hostname | `CYB-SRV01` |
| Role | Windows Enterprise Services |
| Platform | Hyper-V VM |
| OS | Windows Server 2022 Standard Evaluation |
| Architecture | 64-bit |
| OS Build | 20348 |
| Domain State | WORKGROUP |
| Domain Role | StandaloneServer |
| IPv4 | `172.16.10.10` |
| Gateway | `172.16.10.1` |
| DNS | `172.16.10.1` |
| Address Method | MikroTik DHCP Reservation |
| Hyper-V MAC | `00:15:5D:BE:61:01` |
| MAC Allocation | Static |
| Hypervisor | ASUS Hyper-V Host |
| Hypervisor IP | `172.16.10.11` |
| vCPU | 2 |
| RAM | 8 GB |
| OS Disk | ~100 GB VHDX |
| Disk Health | Healthy |
| RDP | Enabled |
| NLA | Enabled |
| RDP Source | `172.16.10.20` only |
| Defender | Enabled |
| PUA Protection | Enabled |
| Security Log | 256 MB Circular |
| System Log | 128 MB Circular |
| Application Log | 128 MB Circular |

---

## 2. Current Server Roles

Current installed base includes:

```text
File and Storage Services
Storage Services
.NET Framework 4.8
WCF Services
Microsoft Defender Antivirus
Windows PowerShell
Windows PowerShell 5.1
```

---

## 3. Roles Not Yet Installed

The following are deliberately not yet installed:

```text
Active Directory Domain Services
DNS Server
```

These will be implemented under the Windows Enterprise Platform project.

---

## 4. Current Network Identity

```text
Hostname: CYB-SRV01

IPv4:
172.16.10.10/24

Gateway:
172.16.10.1

Current DNS:
172.16.10.1
```

Current DNS configuration is temporary.

When Active Directory-integrated DNS is implemented, domain DNS architecture will replace the current router-only DNS arrangement.

---

## 5. Hyper-V Network Identity

CYB-SRV01 uses:

```text
vSwitch-External
```

Static virtual MAC:

```text
00:15:5D:BE:61:01
```

MikroTik reservation:

```text
172.16.10.10
```

---

## 6. Remote Administration

Primary management source:

```text
cli-w11pro-01
172.16.10.20
```

Management method:

```text
Remote Desktop Protocol
Network Level Authentication
```

Firewall source restriction:

```text
172.16.10.20
```

---

## 7. Security Baseline

Microsoft Defender:

```text
Antivirus:             Enabled
Real-time protection:  Enabled
Behaviour monitoring:  Enabled
Network inspection:    Enabled
PUA Protection:        Enabled
```

Windows Firewall:

```text
Domain:  Enabled
Private: Enabled
Public:  Enabled
```

---

## 8. Event Logging

```text
Security:     256 MB / Circular
System:       128 MB / Circular
Application:  128 MB / Circular
```

Future Windows events will be integrated with Wazuh.

---

## 9. Hyper-V Checkpoint State

At baseline completion:

```text
Historical checkpoints: consolidated
Current disk:            VHDX
Active AVHDX chain:      none
```

A new checkpoint will be created immediately before the Active Directory implementation phase.

---

## 10. Known Technical Debt

### Evaluation Edition

CYB-SRV01 currently runs:

```text
Windows Server 2022 Standard Evaluation
```

Evaluation lifecycle must be tracked.

### DHCP-Based Infrastructure Address

CYB-SRV01 currently receives `.10` using a DHCP reservation.

Before domain-controller promotion, it should be configured with a static IPv4 address inside Windows.

### Single Domain Controller

The initial Active Directory phase will begin with a single DC.

Future architecture should introduce a second domain controller to improve directory and DNS resilience.

---

## 11. Planned Roles

Future CYB-SRV01 services include:

```text
Active Directory Domain Services
DNS
Global Catalog
Group Policy
Windows administration
Security auditing
Wazuh telemetry
```

---

## 12. Inventory Status

**Foundation Baseline Complete**