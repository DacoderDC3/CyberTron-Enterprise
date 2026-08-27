# LL-005 — Hyper-V Network Failure and Recovery

- **Lessons Learned ID:** LL-005
- **Project:** Windows Enterprise Platform
- **Date:** 2026-08-27
- **Status:** Incorporated

---

## 1. Incident Summary

During CYB-SRV01 foundation validation, both the ASUS Hyper-V host and CYB-SRV01 were found to have lost CyberTron LAN connectivity.

The ASUS host had previously been assigned:

```text
172.16.10.11
```

and CYB-SRV01:

```text
172.16.10.10
```

Both systems became unavailable from the CyberTron administration workstation.

---

## 2. Initial Symptoms

ASUS Hyper-V host:

```text
MikroTik DHCP reservation status: waiting
vEthernet (vSwitch-External): disconnected
```

CYB-SRV01:

```text
Microsoft Hyper-V Network Adapter
Status: Disconnected
```

The ASUS host also displayed a Hyper-V Default Switch address:

```text
172.21.80.1/20
```

This initially appeared suspicious.

---

## 3. Default Switch Was Not the Failure

Investigation showed that:

```text
172.21.80.1
```

belonged to:

```text
vEthernet (Default Switch)
```

This is a Hyper-V-managed internal/NAT network.

The actual CyberTron external network remained assigned to:

```text
vSwitch-External
```

Therefore the presence of `172.21.80.1` was unrelated to the CyberTron outage.

Lesson:

> Multiple virtual network adapters may legitimately exist on a Hyper-V host. An unfamiliar IP address does not automatically indicate incorrect routing.

---

## 4. Hyper-V Configuration Was Intact

The following were verified:

```text
vSwitch-External exists
Switch type: External
Physical adapter: Intel 82579V
AllowManagementOS: True
Hyper-V extensible switch binding: Enabled
CYB-SRV01 attached to vSwitch-External
Management OS attached to vSwitch-External
```

The Hyper-V logical configuration was therefore intact.

---

## 5. Physical Link Was Present

The ASUS Ethernet cable and TP-Link switch port were checked.

Link/activity LEDs remained active.

Alternate switch ports were tested.

The physical Ethernet carrier therefore appeared present.

Lesson:

> Link lights prove electrical Layer-1 carrier but do not prove that the operating system has a functioning network adapter.

---

## 6. Root Cause

Windows Plug-and-Play inspection identified:

```text
Intel(R) 82579V Gigabit Network Connection
Status: Error
```

The physical NIC existed but was not functioning correctly within Windows.

The adapter was re-enabled.

Network operation immediately recovered.

---

## 7. Recovery Chain

The recovery demonstrated the dependency chain:

```text
Intel 82579V NIC
       ↓
Hyper-V External vSwitch
       ↓
ASUS Management OS
       ↓
CYB-SRV01 Virtual NIC
       ↓
CyberTron LAN
       ↓
CYB-RTR-01 DHCP
```

After recovery:

```text
ASUS:       172.16.10.11
CYB-SRV01:  172.16.10.10
```

---

## 8. DHCP Reservation Issue

After network recovery, CYB-SRV01 initially obtained:

```text
172.16.10.199
```

instead of:

```text
172.16.10.10
```

The reason was a changed Hyper-V dynamic MAC.

Old reservation:

```text
00:15:5D:BE:61:00
```

Current VM MAC:

```text
00:15:5D:BE:61:01
```

The MikroTik DHCP reservation was updated.

CYB-SRV01 then reacquired:

```text
172.16.10.10
```

---

## 9. Static MAC Improvement

To prevent recurrence, CYB-SRV01's Hyper-V NIC was changed from dynamic to static:

```text
00:15:5D:BE:61:01
```

This stabilised the relationship between:

```text
VM identity
MAC address
DHCP reservation
IP address
```

---

## 10. Troubleshooting Method

The incident demonstrated the value of troubleshooting down the stack:

```text
IP addressing
      ↓
DHCP
      ↓
Virtual NIC
      ↓
Hyper-V switch
      ↓
Hyper-V binding
      ↓
Physical NIC
      ↓
Physical cable / switch
      ↓
PnP / driver status
```

At each stage, evidence was gathered before changes were made.

---

## 11. Security Principle

Security controls were not disabled as a general troubleshooting technique.

The following were retained:

```text
Windows Firewall
MikroTik firewall
Hyper-V network architecture
```

This reduced the risk of solving one problem while creating another.

---

## 12. Key Lessons

### Lesson 1

> Do not rebuild a virtual switch simply because the VM has no connectivity.

Validate the dependency chain first.

### Lesson 2

> A link LED does not prove the operating-system NIC is healthy.

### Lesson 3

> DHCP reservation stability depends on stable endpoint identity.

Infrastructure VM MAC addresses should be deliberately managed.

### Lesson 4

> The Hyper-V Default Switch is separate from the external enterprise network.

Do not delete it merely because it uses an unfamiliar subnet.

### Lesson 5

> Troubleshooting should reduce uncertainty before configuration changes are made.

---

## 13. Corrective Actions Completed

```text
Intel physical NIC restored
Hyper-V external networking recovered
ASUS DHCP reservation restored
CYB-SRV01 DHCP reservation updated
CYB-SRV01 static Hyper-V MAC configured
RDP administration enabled
RDP source restricted
Hyper-V host sleep disabled
```

---

## 14. Outcome

The incident was resolved without rebuilding Hyper-V networking or resetting CyberTron routing.

The lessons were incorporated into:

```text
Windows Server baseline
Remote administration runbook
Power-management runbook
Future infrastructure troubleshooting procedures
```

---

## 15. Status

**Lessons Incorporated**