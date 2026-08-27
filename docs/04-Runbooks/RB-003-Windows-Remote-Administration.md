# RB-003 — Windows Remote Administration

- **Runbook ID:** RB-003
- **Environment:** CyberTron Enterprise
- **Owner:** CyberTron Enterprise
- **Status:** Active
- **Last Updated:** 2026-08-27

---

## 1. Purpose

This runbook defines the current approved method for remotely administering CyberTron Windows infrastructure from the dedicated administration workstation.

Current administration workstation:

```text
cli-w11pro-01
172.16.10.20
```

Current Windows infrastructure targets:

```text
CYB-SRV01
172.16.10.10

ASUS Hyper-V Host
172.16.10.11
```

---

## 2. Current Management Architecture

```text
                 cli-w11pro-01
                  172.16.10.20
                        │
             ┌──────────┴──────────┐
             │                     │
             │ RDP + NLA           │ RDP + NLA
             ▼                     ▼
        CYB-SRV01              ASUS Hyper-V
       172.16.10.10           172.16.10.11
```

Remote administration is currently performed on the flat CyberTron LAN.

A dedicated management VLAN and jump host are planned for a future architecture phase.

---

## 3. Remote Desktop Protocol

Windows graphical administration uses Remote Desktop Protocol.

Default port:

```text
TCP 3389
UDP 3389
```

Remote Desktop is configured to require:

```text
Network Level Authentication
```

NLA requires authentication before a full interactive desktop session is established.

---

## 4. Enable RDP

Run from an elevated PowerShell session on the target Windows host:

```powershell
Set-ItemProperty `
  -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' `
  -Name 'fDenyTSConnections' `
  -Value 0
```

Enable the Windows Firewall Remote Desktop rule group:

```powershell
Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
```

Enable Network Level Authentication:

```powershell
Set-ItemProperty `
  -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' `
  -Name 'UserAuthentication' `
  -Value 1
```

---

## 5. Verify RDP Configuration

Verify RDP is enabled:

```powershell
Get-ItemProperty `
  'HKLM:\System\CurrentControlSet\Control\Terminal Server' `
  -Name fDenyTSConnections
```

Expected:

```text
fDenyTSConnections : 0
```

Verify NLA:

```powershell
Get-ItemProperty `
  'HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' `
  -Name UserAuthentication
```

Expected:

```text
UserAuthentication : 1
```

Verify Remote Desktop Services:

```powershell
Get-Service TermService
```

Expected:

```text
Running
```

---

## 6. Test RDP Port

From `cli-w11pro-01`:

```powershell
Test-NetConnection 172.16.10.10 -Port 3389
```

or:

```powershell
Test-NetConnection 172.16.10.11 -Port 3389
```

Expected:

```text
TcpTestSucceeded : True
```

---

## 7. Restrict RDP to the Administration Workstation

CyberTron currently restricts RDP access to:

```text
172.16.10.20
```

Apply the restriction:

```powershell
Get-NetFirewallRule -DisplayGroup "Remote Desktop" |
Where-Object Enabled -eq True |
Get-NetFirewallAddressFilter |
Set-NetFirewallAddressFilter -RemoteAddress 172.16.10.20
```

Verify:

```powershell
Get-NetFirewallRule -DisplayGroup "Remote Desktop" |
Where-Object Enabled -eq True |
Get-NetFirewallAddressFilter |
Select-Object InstanceID,RemoteAddress
```

Expected:

```text
RemoteDesktop-UserMode-In-TCP   172.16.10.20
RemoteDesktop-UserMode-In-UDP   172.16.10.20
RemoteDesktop-Shadow-In-TCP     172.16.10.20
```

---

## 8. Connect from the Administration Workstation

Press:

```text
Win + R
```

Run:

```text
mstsc
```

Connect to:

```text
172.16.10.10
```

or:

```text
172.16.10.11
```

While the systems remain standalone, specify the local account explicitly where necessary.

Example:

```text
CYB-SRV01\Administrator
```

---

## 9. Management Security Principles

CyberTron remote administration follows these principles:

```text
Windows Firewall remains enabled
RDP requires NLA
RDP is permitted only from the administration workstation
No RDP ports are forwarded through the WAN gateway
Management services remain internal to CyberTron
```

---

## 10. Break-Glass Access

If CYB-SRV01 network connectivity fails, use:

```text
Hyper-V Manager
        ↓
CYB-SRV01 console
```

The Hyper-V console is therefore considered the local break-glass administration path.

---

## 11. Future Architecture

The current management model is temporary.

Future architecture will introduce:

```text
Management VLAN
Dedicated jump host
PowerShell Remoting / WinRM
Role-based administrative accounts
Central identity authentication
Management firewall policy
```

The intended future model is:

```text
Admin Workstation
       │
       ▼
Management / Jump Host
       │
       ├── Windows administration
       ├── Linux administration
       ├── Network administration
       └── Security platform administration
```

---

## 12. Related Documentation

- `BL-009-CYB-SRV01-Foundation-Baseline.md`
- `Network-Architecture-v0.2.md`
- `Network-Inventory.md`
- `RB-004-CyberTron-Infrastructure-Power-Management.md`

---

## 13. Runbook Status

**Active**