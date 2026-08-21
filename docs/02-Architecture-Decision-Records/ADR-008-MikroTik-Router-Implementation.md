# ADR-008 — Adopt MikroTik RouterOS as the CyberTron Enterprise Network Gateway
- **Status:** Accepted
- **Date:** 2026-08-21
- **Decision Owners:** CyberTron Enterprise
- **Architecture Area:** Network
- **Related Architecture:** Enterprise Architecture v0.2
- **Related Build Log:** `BL-008-CyberTron-Network-Migration.md`
---
## 1. Context
- CyberTron Enterprise initially used a Skinny Broadband 4G router as the primary network gateway for the cybersecurity homelab.
- The original arrangement placed the CyberTron systems directly on the 4G router's LAN:
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
```

- Although functional, this arrangement placed routing, DHCP, NAT, firewalling and Internet gateway responsibilities on a consumer-oriented device.

- The project objective is to model an enterprise-style SMB environment using repurposed hardware while developing practical skills in networking, infrastructure security, systems administration, monitoring and governance.

- A dedicated routing and security platform was therefore required to provide a clearer separation between:

* upstream Internet connectivity;
* enterprise network services;
* internal CyberTron systems;
* future segmented security zones.

⸻

## 2. Problem Statement

The existing 4G router did not provide an appropriate long-term platform for developing and demonstrating:

* enterprise-style routing;
* dedicated firewall policy;
* centralised DHCP;
* DNS forwarding;
* network segmentation;
* VLAN routing;
* policy-based routing;
* management-plane security;
* network troubleshooting and recovery;
* configuration backup and recovery.

- The CyberTron architecture also required the ability to retain the Skinny 4G service as an independent Internet connection while placing a dedicated enterprise network boundary between the cellular provider and CyberTron systems.

⸻

## 3. Options Considered

# Option A — Continue using the Skinny 4G router as the CyberTron gateway

- Advantages

* Already operational.
* No additional configuration required.
* Simple topology.

- Disadvantages

* Limited enterprise networking learning opportunity.
* Consumer-oriented routing and security controls.
* Less suitable for future VLAN and policy-routing development.
* Network and security responsibilities remain concentrated in the upstream device.
* Does not provide a dedicated enterprise network boundary.

- Decision: Rejected.

⸻

# Option B — Use a general-purpose firewall platform such as pfSense

- Advantages

* Enterprise-style firewall functionality.
* VLAN support.
* Extensive routing and security functionality.
* Strong learning value.

- Disadvantages

* Requires dedicating suitable hardware to the firewall platform.
* Adds another platform to the environment.
* Does not make use of the available MikroTik hardware.
* Introduces additional complexity while the underlying CyberTron network foundation is still being established.

- Decision: Deferred / not selected for the current architecture.

⸻

# Option C — Adopt the MikroTik RB960PGS hEX PoE as the CyberTron enterprise router

- Advantages

* Dedicated network appliance.
* RouterOS provides routing, firewalling, NAT, DHCP and DNS functions.
* Supports VLANs and inter-VLAN routing.
* Supports policy-based routing and multiple WAN designs.
* Provides hands-on experience with a widely used enterprise networking platform.
* Existing hardware is already available.
* Provides a strong platform for future CyberTron segmentation and network-security exercises.
* Supports configuration export, backup and recovery procedures.
* Allows the Skinny 4G router to remain an upstream Internet gateway without making it the primary enterprise network control point.

- Disadvantages

* Requires additional configuration and operational knowledge.
* Introduces a separate management platform.
* RouterOS requires deliberate configuration management and documentation.
* Initial architecture is more complex than retaining the existing consumer router.

- Decision: Accepted.

⸻

## 4. Decision

- CyberTron Enterprise will adopt a MikroTik RB960PGS hEX PoE running RouterOS 7.21.5 Long-term as the primary CyberTron network router and security boundary.

- The Skinny Broadband 4G router will remain as the upstream Internet gateway and cellular WAN device.

The resulting architecture is:
```text
                 Skinny 4G / LTE
                       │
                 192.168.8.1/24
                       │
                       ▼
              ┌─────────────────┐
              │   CYB-RTR-01    │
              │  MikroTik       │
              │  RB960PGS       │
              │  RouterOS 7     │
              └────────┬────────┘
                       │
                 172.16.10.1/24
                       │
                       ▼
              CyberTron LAN
```
⸻

## 5. Responsibilities of CYB-RTR-01

CYB-RTR-01 is responsible for the following functions:

Routing

* Default routing toward the Skinny 4G gateway.
* Routing between CyberTron networks as segmentation is introduced.

Firewall

* Stateful ingress and forwarding controls.
* WAN protection.
* Future inter-zone security policy.

NAT

* Source NAT / masquerading for CyberTron traffic leaving through the upstream 4G gateway.

DHCP

* Central DHCP services for the CyberTron LAN.
* Static DHCP reservations for infrastructure and managed endpoints.

DNS

* DNS forwarding for the CyberTron network.
* Future integration with internal DNS services when Active Directory is implemented.

Network Management

* Central network administration using RouterOS.
* Management via restricted SSH, WinBox and WebFig access.

⸻

## 6. Addressing Decision

The CyberTron LAN will retain:
```text
172.16.10.0/24

with:

Gateway: 172.16.10.1
DHCP:    172.16.10.100-199

The upstream Skinny 4G router was renumbered to:

192.168.8.1/24

This avoids overlapping subnets between the upstream WAN and CyberTron LAN.

The resulting routing boundary is:

192.168.8.0/24
       │
       │ WAN
       ▼
172.16.10.0/24
       │
       │ CyberTron LAN
       ▼
CyberTron endpoints
```
⸻

## 7. Physical Network Design

The current physical architecture is:
```text
Skinny 4G Router
        │
        │ WAN
        ▼
CYB-RTR-01 ether1
        │
        │ ether2
        ▼
TP-Link TL-SG1008D
        │
        ├── HP Administration Workstation
        ├── Dell Proxmox
        ├── ASUS Hyper-V Host
        └── Future CyberTron endpoints
```
The TL-SG1008D is intentionally treated as a simple unmanaged Layer-2 switch.

VLAN-aware switching and trunking are deferred until the flat CyberTron network is fully understood and operationally stable.

⸻

## 8. Security Design Principle

The CyberTron network will initially use a flat Layer-2 LAN with a dedicated Layer-3/security boundary at CYB-RTR-01.

This is an intentional implementation decision.

VLAN segmentation is not being introduced simultaneously with the initial router deployment because doing so would introduce multiple variables at once.

The implementation sequence is:
```text
Physical Connectivity
        ↓
IP Addressing
        ↓
Routing
        ↓
DHCP / DNS
        ↓
NAT
        ↓
Firewall
        ↓
Validation
        ↓
VLAN Segmentation
        ↓
Inter-VLAN Security Policy
```
This supports a controlled learning and troubleshooting process.

⸻

## 9. Security Hardening

The initial RouterOS management plane has been hardened by:

* disabling unused FTP;
* disabling Telnet;
* disabling API;
* disabling API-SSL;
* restricting SSH to the CyberTron LAN;
* restricting WinBox to the CyberTron LAN;
* restricting WebFig to the CyberTron LAN;
* restricting MAC-based management to the LAN;
* restricting neighbour discovery to the LAN.

The standard RouterOS stateful firewall baseline is retained as the foundation for further policy development.

⸻

## 10. Validation

The decision has been validated through practical implementation.

Testing demonstrated:

* successful CyberTron LAN DHCP;
* successful static DHCP reservations;
* successful WAN DHCP;
* successful routing to the upstream 4G gateway;
* successful source NAT;
* successful DNS forwarding;
* successful access to Proxmox;
* successful access to Windows Server;
* successful access to Ubuntu systems;
* successful access to Wazuh;
* successful Mac mini LAN integration;
* successful router reboot and configuration recovery.

A controlled router reboot confirmed that the network configuration and security controls persist after restart.

⸻

## 11. Operational Recovery

Configuration management includes:

* RouterOS configuration exports;
* RouterOS binary backups;
* downloaded off-device backups;
* removal of backup copies from limited router storage;
* controlled recovery testing.

The recovery test demonstrated that CyberTron can recover from a controlled CYB-RTR-01 reboot without manual reconfiguration.

⸻

## 12. Consequences

Positive

* Establishes a dedicated enterprise-style network boundary.
* Improves practical RouterOS knowledge.
* Provides a strong foundation for future VLAN segmentation.
* Enables future policy-based routing.
* Separates carrier/WAN responsibilities from CyberTron LAN management.
* Improves visibility and control over infrastructure addressing.
* Provides a realistic firewall and routing platform.
* Creates additional opportunities for network troubleshooting and recovery exercises.

Negative

* Adds configuration and administration overhead.
* Requires learning RouterOS.
* Introduces another component that must be backed up and maintained.
* Creates an additional dependency for internal network connectivity.

⸻

## 13. Future Extensions

The following capabilities are intentionally deferred:

* Management VLAN;
* Server VLAN;
* Client VLAN;
* Security/SIEM VLAN;
* Untrusted/IoT VLAN;
* isolated attack network;
* policy-based routing;
* multi-WAN failover;
* additional MikroTik router deployment;
* dedicated management network;
* firewall policy between security zones.

These capabilities will be introduced only after the current network baseline is considered stable.

⸻

## 14. Decision Outcome

Accepted and Implemented

CYB-RTR-01 is now the authoritative network gateway and security boundary for CyberTron Enterprise.

The Skinny 4G router remains the upstream Internet gateway.

This architecture establishes the network foundation required for the next CyberTron engineering phase: Windows Enterprise Platform, identity, security monitoring and future network segmentation.