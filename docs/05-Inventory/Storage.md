# 4. `docs/05-Inventory/README.md`

- Add the following section to your inventory landing page. If that README already has separate sections for devices, put this under **Enterprise Storage**.

## Enterprise Storage Inventory

### CYB-SRV01 Storage Platform

```text
| Property | Value |
|---|---|
| Storage Server | `CYB-SRV01` |
| Hypervisor | Microsoft Hyper-V |
| Storage Management | Windows Server 2022 Storage Spaces |
| Storage Pool | `CyberTronStoragePool` |
| Virtual Disk | `CyberTronData` |
| Resiliency | Two-Way Mirror |
| Fault Domain Redundancy | 1 |
| Raw capacity | ~5.46 TB |
| Usable capacity | ~2.72 TB |
| Filesystem | NTFS |
| Allocation unit | 4096 bytes |
| Volume | `E:` |
| Volume Label | `CyberTronData` |
| Status | Healthy / Operational |
```

### Physical HDD Inventory

| Quantity | Model | Nominal Capacity | Role | Status |
|---:|---|---:|---|---|
| 3 | WDC WD2002FYPS-18U1B | 2 TB each | Storage Spaces mirror members | Healthy |

### Physical Storage Location

The three HDDs are physically installed in the Windows Hyper-V host but are not mounted by the host operating system.

They are:

1. Marked offline on the Windows 11 host.
2. Passed directly through Hyper-V.
3. Presented to `CYB-SRV01`.
4. Managed exclusively by Windows Server Storage Spaces.

### Logical Storage Map

```text
Physical HDD × 3
      │
      ▼
CyberTronStoragePool
      │
      ▼
CyberTronData
Two-Way Mirror
      │
      ▼
```
---
