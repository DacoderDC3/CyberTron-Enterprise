# RB-004 — CyberTron Infrastructure Power Management

- **Runbook ID:** RB-004
- **Environment:** CyberTron Enterprise
- **Status:** Active
- **Last Updated:** 2026-08-27

---

## 1. Purpose

This runbook defines the power-management baseline for CyberTron physical infrastructure.

The objective is to prevent critical infrastructure hosts from automatically sleeping while allowing endpoint devices to use controlled sleep and Wake-on-LAN where appropriate.

---

## 2. Power Management Principle

CyberTron distinguishes between:

```text
Infrastructure
```

and:

```text
Endpoints
```

Infrastructure systems should remain continuously available.

Endpoint systems may use normal power-saving functionality where operationally appropriate.

---

## 3. Current Policy

| System | Role | Power Policy |
|---|---|---|
| ASUS Hyper-V Host | Hypervisor | Never automatically sleep on AC |
| Dell Proxmox Host | Hypervisor | Suspend/hibernate prohibited |
| CYB-SRV01 | Hyper-V VM | Dependent on Hyper-V host |
| Mac mini | Endpoint | Sleep permitted + Wake-on-LAN |
| HP Admin Laptop | Administration endpoint | Normal workstation power policy |

---

## 4. ASUS Hyper-V Host

The ASUS Hyper-V host supports Windows S3 standby.

Its previous AC sleep timeout was approximately:

```text
15 minutes
```

Automatic AC standby was disabled using:

```powershell
powercfg /change standby-timeout-ac 0
```

`0` means:

```text
Never automatically enter standby while on AC power
```

This is required because the ASUS host provides the Hyper-V platform for CYB-SRV01.

If the host sleeps:

```text
ASUS host unavailable
        ↓
Hyper-V unavailable
        ↓
CYB-SRV01 unavailable
```

---

## 5. Proxmox Host

The Dell Proxmox host is treated as always-on infrastructure.

The following systemd targets are masked:

```text
sleep.target
suspend.target
hibernate.target
hybrid-sleep.target
```

Configuration command:

```bash
systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

Verification:

```bash
systemctl is-enabled sleep.target suspend.target hibernate.target hybrid-sleep.target
```

Expected:

```text
masked
masked
masked
masked
```

---

## 6. Proxmox systemd-logind Policy

CyberTron uses the following drop-in:

```text
/etc/systemd/logind.conf.d/10-cybertron-server.conf
```

Configuration:

```ini
[Login]
HandleSuspendKey=ignore
HandleHibernateKey=ignore
HandleLidSwitch=ignore
IdleAction=ignore
```

This prevents accidental suspend/hibernate behaviour from systemd-logind.

Verify effective configuration:

```bash
systemd-analyze cat-config systemd/logind.conf |
grep -E 'HandleSuspendKey|HandleHibernateKey|HandleLidSwitch|IdleAction'
```

---

## 7. Mac mini

The Mac mini is permitted to sleep because it is currently treated as an endpoint rather than critical server infrastructure.

Wake-on-LAN capability was verified using:

```bash
pmset -g
```

The relevant result was:

```text
womp 1
```

`womp` means:

```text
Wake On Magic Packet
```

---

## 8. Mac Wake-on-LAN Test

Mac Ethernet MAC:

```text
98:5A:BE:C9:9E:13
```

Mac CyberTron address:

```text
172.16.10.25
```

The Mac was deliberately placed into sleep.

A Wake-on-LAN magic packet was transmitted from:

```text
cli-w11pro-01
172.16.10.20
```

to the CyberTron broadcast address:

```text
172.16.10.255
```

using UDP port:

```text
9
```

The Mac successfully woke.

Post-wake SSH connectivity was restored.

Wake-on-LAN is therefore:

```text
Configured: Yes
Tested:     Yes
Result:     Pass
```

---

## 9. Example Windows WoL PowerShell

The following PowerShell can be used from the administration workstation:

```powershell
$mac = "98:5A:BE:C9:9E:13"
$macBytes = $mac -split '[:-]' | ForEach-Object { [byte]("0x$_") }
$packet = [byte[]](,0xFF * 6) + ($macBytes * 16)

$udp = New-Object System.Net.Sockets.UdpClient
$udp.EnableBroadcast = $true
$udp.Connect("172.16.10.255", 9)
[void]$udp.Send($packet, $packet.Length)
$udp.Close()
```

---

## 10. Future Resilience Controls

Future infrastructure work should evaluate:

```text
Wake-on-LAN for physical hypervisors
Restore on AC Power Loss
UPS integration
Graceful VM shutdown
Automated infrastructure startup
Power-loss recovery testing
```

---

## 11. VLAN Consideration

Current Wake-on-LAN testing works because the systems reside in the same:

```text
172.16.10.0/24
```

broadcast domain.

Future VLAN segmentation will introduce Layer-3 boundaries.

Wake-on-LAN broadcasts do not automatically cross routers.

WoL requirements must therefore be deliberately incorporated into future segmentation design.

---

## 12. Related Documentation

- `BL-009-CYB-SRV01-Foundation-Baseline.md`
- `RB-003-Windows-Remote-Administration.md`
- `Network-Architecture-v0.2.md`

---

## 13. Runbook Status

**Active**