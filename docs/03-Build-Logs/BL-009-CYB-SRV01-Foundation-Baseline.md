# BL-009 — CYB-SRV01 Windows Server Foundation Baseline

- **Build Log ID:** BL-009
- **Project:** Windows Enterprise Platform
- **System:** CYB-SRV01
- **Platform:** Windows Server 2022 Standard Evaluation
- **Date:** 2026-08-27
- **Status:** Completed
- **Result:** Successful

---

## 1. Objective

Establish and validate a stable Windows Server foundation for CYB-SRV01 before introducing Active Directory Domain Services, DNS or other enterprise identity services.

The baseline was designed to confirm:

- operating-system identity;
- network configuration;
- Hyper-V network stability;
- patch status;
- Windows Defender operation;
- Windows Firewall configuration;
- remote administration;
- event logging;
- audit policy;
- time synchronisation;
- disk health;
- reboot state;
- Hyper-V checkpoint hygiene.

The server was intentionally retained as a standalone WORKGROUP server during this phase.

---

## 2. System Identity

Final baseline:

```text
Hostname:           CYB-SRV01
Operating System:   Windows Server 2022 Standard Evaluation
Version:            2009
Build:              20348
Architecture:       64-bit
Domain:             WORKGROUP
Domain Role:        StandaloneServer
```

The server has not yet been promoted to a domain controller.

---

## 3. Virtualisation Platform

CYB-SRV01 runs as a Generation 2 Hyper-V virtual machine on the ASUS CyberTron Hyper-V host.

Current Hyper-V host:

```text
Host: DESKTOP-BVBJJRV
CyberTron IP: 172.16.10.11
Platform: Windows 11 Pro / Hyper-V
```

CYB-SRV01 currently uses:

```text
CPU: 2 virtual processors
Memory: 8 GB
Virtual Disk: ~100 GB VHDX
Network: vSwitch-External
```

---

## 4. Network Configuration

Final CyberTron addressing:

```text
Hostname:        CYB-SRV01
IPv4:            172.16.10.10
Subnet:          255.255.255.0
Gateway:         172.16.10.1
DNS:             172.16.10.1
```

The current address is delivered by a MikroTik DHCP reservation.

CYB-SRV01 currently receives DNS forwarding through CYB-RTR-01.

This is a temporary pre-AD configuration.

Before domain-controller promotion, CYB-SRV01 will be configured with a locally static IPv4 address and an Active Directory-aware DNS design.

---

## 5. Stable Hyper-V MAC Address

During troubleshooting, CYB-SRV01's Hyper-V network adapter MAC changed from:

```text
00:15:5D:BE:61:00
```

to:

```text
00:15:5D:BE:61:01
```

The MikroTik DHCP reservation was updated accordingly.

To prevent future Hyper-V dynamic MAC changes from breaking infrastructure addressing, the VM network adapter was changed to a static Hyper-V MAC:

```text
00:15:5D:BE:61:01
```

The resulting identity chain is:

```text
CYB-SRV01
     │
     ▼
Static Hyper-V MAC
00:15:5D:BE:61:01
     │
     ▼
MikroTik DHCP Reservation
     │
     ▼
172.16.10.10
```

---

## 6. Hyper-V Network Incident

During the baseline process, both the ASUS Hyper-V host and CYB-SRV01 lost CyberTron network connectivity.

Symptoms included:

```text
ASUS Hyper-V host missing from DHCP
CYB-SRV01 network adapter disconnected
vEthernet (vSwitch-External) disconnected
ASUS physical Intel NIC showing abnormal state
```

Initial troubleshooting validated:

```text
MikroTik DHCP configuration
Hyper-V external switch configuration
VM-to-switch attachment
Management OS attachment
Hyper-V switch protocol binding
Physical Ethernet cable
TP-Link switch port
```

Windows Plug-and-Play inspection identified:

```text
Intel(R) 82579V Gigabit Network Connection
Status: Error
```

The physical NIC was re-enabled and returned to operational state.

The Hyper-V external networking path immediately recovered.

Following recovery:

```text
ASUS Host:     172.16.10.11
CYB-SRV01:     172.16.10.10
```

Network routing, Internet connectivity and DNS resolution were subsequently validated.

---

## 7. Network Validation

The following checks passed:

```text
CYB-SRV01 → 172.16.10.1
CYB-SRV01 → 8.8.8.8
CYB-SRV01 → DNS resolution
```

Verified configuration:

```text
IPv4:    172.16.10.10
Gateway: 172.16.10.1
DNS:     172.16.10.1
```

---

## 8. Windows Update Baseline

Windows updates were applied before completing the baseline.

Recent installed updates included updates installed on:

```text
2026-08-26
```

The server was rebooted after patching.

Final reboot-state checks:

```text
Component Based Servicing RebootPending: False
Windows Update RebootRequired:           False
```

The server therefore had no known pending Windows servicing restart at baseline completion.

---

## 9. Windows Firewall Baseline

All Windows Firewall profiles are enabled:

```text
Domain:  Enabled
Private: Enabled
Public:  Enabled
```

The current CyberTron network profile is:

```text
Private
```

This is expected while CYB-SRV01 remains a standalone WORKGROUP server.

After successful Active Directory integration, the appropriate network should transition to:

```text
DomainAuthenticated
```

Windows Firewall was not disabled during troubleshooting or configuration.

---

## 10. Microsoft Defender Baseline

Microsoft Defender was validated as operational.

Verified controls:

```text
AntivirusEnabled:          True
AntispywareEnabled:        True
RealTimeProtectionEnabled: True
BehaviorMonitorEnabled:    True
IoavProtectionEnabled:     True
NISEnabled:                True
```

Defender Potentially Unwanted Application protection was enabled:

```text
PUAProtection: 1
```

The server therefore retains:

- antivirus protection;
- real-time monitoring;
- behaviour monitoring;
- network inspection;
- IOAV protection;
- PUA protection.

---

## 11. Remote Administration

Remote Desktop was enabled to allow administration from the dedicated CyberTron Windows administration workstation:

```text
cli-w11pro-01
172.16.10.20
```

Remote Desktop uses:

```text
TCP/3389
UDP/3389
Network Level Authentication
```

RDP connectivity was successfully tested from the administration workstation.

The enabled Windows Firewall Remote Desktop rules were then restricted to:

```text
RemoteAddress: 172.16.10.20
```

Verified rules:

```text
RemoteDesktop-UserMode-In-TCP
RemoteDesktop-UserMode-In-UDP
RemoteDesktop-Shadow-In-TCP
```

All currently allow remote access only from:

```text
172.16.10.20
```

This implements a basic management-plane least-privilege control while CyberTron remains on a flat LAN.

---

## 12. Event Logging Baseline

The core Windows logs are enabled:

```text
Security
System
Application
```

All use circular logging.

Initial default maximum capacity was approximately:

```text
20 MB per log
```

The Security log had already reached approximately:

```text
17.1 MB
```

before Active Directory implementation.

To improve local investigation capability, maximum log sizes were increased to:

```text
Security:     256 MB
System:       128 MB
Application:  128 MB
```

Circular behaviour was retained.

Future central collection will be provided through Wazuh.

---

## 13. Audit Policy Baseline

The current Windows advanced audit policy was recorded using:

```powershell
auditpol /get /category:*
```

Existing auditing includes several security-relevant categories including:

```text
System Integrity
Security State Change
Logon
Logoff
Account Lockout
Special Logon
Audit Policy Change
Authentication Policy Change
Computer Account Management
Security Group Management
User Account Management
Credential Validation
Kerberos Authentication Service
Kerberos Service Ticket Operations
```

Several additional categories currently remain:

```text
No Auditing
```

including areas such as:

```text
Process Creation
File System
Registry
Sensitive Privilege Use
Directory Service Changes
```

These were intentionally not enabled individually.

A future CyberTron Advanced Audit Policy will be implemented using Group Policy after Active Directory is established.

---

## 14. Time Synchronisation

During early testing, CYB-SRV01 used the Hyper-V Integration Services time provider.

At final baseline validation the server was synchronising using:

```text
time.windows.com,0x8
```

NTP synchronisation was operational.

The current configuration is acceptable for the standalone-server phase.

Time architecture will be deliberately redesigned when CYB-SRV01 becomes the first domain controller and PDC Emulator.

---

## 15. Disk and Storage Baseline

CYB-SRV01 currently has one virtual OS disk:

```text
Disk:             0
Type:             Microsoft Virtual Disk
Operational:      Online
Health:           Healthy
Partition Style:  GPT
Capacity:         ~100 GB
```

No Windows Storage Spaces pool is currently implemented inside CYB-SRV01.

This is intentional.

Large-scale shared storage and log retention will be treated as a separate CyberTron storage architecture project rather than combined with the first domain controller.

---

## 16. Hyper-V Checkpoint Consolidation

During baseline review, CYB-SRV01 was found to be operating through a chain of multiple Hyper-V standard checkpoints.

Historical checkpoints included:

```text
Initial CYB-SRV01 checkpoint
BL-002 — Commissioned Windows Server
BL-003 — Commissioned Windows Server
CYB-SRV01 — Static MAC set
```

The active virtual disk was therefore an `.avhdx` differencing disk.

All historical checkpoints were removed using Hyper-V checkpoint management.

Hyper-V successfully merged the checkpoint chain.

Final state:

```text
Active virtual disk: .vhdx
Outstanding checkpoints: none
```

No `.avhdx` files were manually deleted.

---

## 17. Installed Roles and Features

The baseline intentionally contains no Active Directory Domain Services or DNS Server role.

Installed components currently include:

```text
File and Storage Services
Storage Services
.NET Framework 4.8 Features
WCF Services
TCP Port Sharing
Azure Arc Setup
Microsoft Defender Antivirus
System Data Archiver
Windows PowerShell
Windows PowerShell 5.1
WoW64 Support
XPS Viewer
```

The following enterprise identity roles are not yet installed:

```text
AD-Domain-Services
DNS
```

This provides a clean pre-identity configuration baseline.

---

## 18. Power and Availability Dependency

CYB-SRV01 depends on the ASUS Hyper-V host.

The ASUS host was therefore configured so that automatic sleep while on AC power is disabled.

This prevents the Hyper-V host from entering standby and unintentionally taking CYB-SRV01 offline.

---

## 19. Final Validation

The following final checks passed:

```text
Hostname correct
Standalone WORKGROUP state correct
IPv4 addressing correct
Default gateway correct
DNS forwarding correct
Gateway reachable
Internet reachable
DNS resolution working
Windows Firewall enabled
Microsoft Defender enabled
PUA protection enabled
RDP restricted to administration workstation
OS disk healthy
Security event-log retention increased
No pending CBS reboot
No pending Windows Update reboot
Hyper-V checkpoint chain consolidated
```

---

## 20. Result

**CYB-SRV01 Foundation Baseline — COMPLETE**

CYB-SRV01 is now considered ready for the next engineering change:

```text
Active Directory Domain Services
        +
Active Directory-integrated DNS
```

No Active Directory changes will be implemented until the domain and DNS architecture have been documented.

---

## 21. Related Documentation

- `ADR-008-MikroTik-Network-Gateway.md`
- `Network-Architecture-v0.2.md`
- `Network-Inventory.md`
- `RB-003-Windows-Remote-Administration.md`
- `RB-004-CyberTron-Infrastructure-Power-Management.md`
- `LL-005-Hyper-V-Network-Recovery.md`
- `Windows-Server-Inventory.md`

---

## 22. Build Status

**Completed Successfully — 2026-08-27**