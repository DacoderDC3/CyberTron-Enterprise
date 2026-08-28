# CyberTron Enterprise — Active Directory Architecture

- **Document Type:** Enterprise Architecture
- **Architecture Domain:** Identity
- **Version:** 0.1
- **Date:** 2026-08-29
- **Status:** Approved for Initial Implementation
- **Environment:** CyberTron Enterprise
- **Primary Platform:** Microsoft Active Directory Domain Services

---

## 1. Purpose

This document defines the initial Active Directory Domain Services architecture for CyberTron Enterprise.

The architecture establishes the enterprise identity foundation that will support:

- centralised authentication;
- centralised authorisation;
- Windows domain membership;
- Linux identity integration;
- macOS identity integration;
- role-based access control;
- privileged administration;
- Group Policy;
- Active Directory-integrated DNS;
- security auditing;
- future Microsoft 365 and Microsoft Entra integration.

The design intentionally begins with a simple architecture appropriate to the current CyberTron environment while providing a foundation that can evolve as the enterprise lab becomes more sophisticated.

---

## 2. Architecture Principles

The Active Directory design follows the following principles:

1. **Keep the initial forest simple.**
2. **Use a single authoritative enterprise identity plane.**
3. **Separate standard and privileged identities.**
4. **Use groups rather than individual users for access assignment.**
5. **Design Organisational Units around administration and policy requirements rather than reproducing an organisational chart.**
6. **Treat DNS as a core Active Directory dependency.**
7. **Use least privilege for administrative access.**
8. **Maintain clear separation between infrastructure, servers, endpoints and privileged systems.**
9. **Support Windows, Linux and macOS identity integration.**
10. **Design for future Microsoft Entra ID and Microsoft 365 integration without unnecessarily complicating the initial deployment.**

---

## 3. Forest Architecture

CyberTron Enterprise will initially operate:

```text
One Forest
    │
    └── One Domain
```

Forest root:

```text
ad.cybertron.test
```

Domain:

```text
ad.cybertron.test
```

NetBIOS domain name:

```text
CYBERTRON
```

The architecture therefore becomes:

```text
CyberTron Enterprise
        │
        ▼
Active Directory Forest
ad.cybertron.test
        │
        ▼
Active Directory Domain
ad.cybertron.test
        │
        ▼
CYBERTRON
```

No child domains or additional forests are required for the initial CyberTron environment.

---

## 4. Namespace Architecture

CyberTron uses the reserved `.test` namespace for internal lab infrastructure.

The namespace family is:

```text
cybertron.test
│
├── ad.cybertron.test
├── dev.cybertron.test
└── lab.cybertron.test
```

### 4.1 Active Directory Namespace

```text
ad.cybertron.test
```

Purpose:

- Active Directory Domain Services;
- Active Directory-integrated DNS;
- Kerberos service discovery;
- LDAP service discovery;
- domain-member registration;
- internal identity infrastructure.

### 4.2 Development Namespace

```text
dev.cybertron.test
```

Status:

```text
Reserved for future use
```

Potential future purposes include:

- development systems;
- application testing;
- automation;
- DevSecOps exercises;
- development DNS services.

### 4.3 Lab Services Namespace

```text
lab.cybertron.test
```

Status:

```text
Reserved for future use
```

Potential future purposes include:

- security tooling;
- temporary lab services;
- training systems;
- application testing;
- isolated exercises.

The `dev` and `lab` namespaces are not separate Active Directory domains.

The initial Active Directory architecture remains:

```text
Single Forest
Single Domain
```

---

## 5. Domain Controller Architecture

The first CyberTron domain controller will be:

```text
Hostname: CYB-SRV01
IPv4:     172.16.10.10
```

CYB-SRV01 will initially provide:

```text
Active Directory Domain Services
DNS Server
Global Catalog
Group Policy infrastructure
Kerberos authentication
LDAP directory services
```

Architecture:

```text
                ad.cybertron.test
                       │
                       ▼
                   CYB-SRV01
                  172.16.10.10
                       │
          ┌────────────┼────────────┐
          │            │            │
         AD DS        DNS      Global Catalog
          │            │            │
          └────────────┼────────────┘
                       │
                       ▼
               CyberTron Systems
```

---

## 6. Initial Domain Controller Resilience

The initial implementation will use one domain controller.

This is acceptable for the initial learning and implementation phase but represents a known single point of failure.

Initial state:

```text
CYB-SRV01
    │
    ├── AD DS
    ├── DNS
    └── Global Catalog
```

Future architecture should introduce a second domain controller:

```text
             ad.cybertron.test
                    │
          ┌─────────┴─────────┐
          │                   │
          ▼                   ▼
     CYB-SRV01           Future DC
       AD DS                AD DS
        DNS                  DNS
         GC                   GC
```

A second domain controller will provide practical experience with:

- Active Directory replication;
- DNS replication;
- domain-controller redundancy;
- FSMO roles;
- domain-controller failure;
- recovery testing.

---

## 7. Domain Functional Architecture

CYB-SRV01 runs Windows Server 2022.

The initial forest and domain functional level will use the supported Windows Server 2016 Active Directory functional level.

The initial architecture therefore uses:

```text
Forest Functional Level: Windows Server 2016
Domain Functional Level: Windows Server 2016
```

---

## 8. Organisational Unit Architecture

CyberTron OUs are designed primarily around:

- administrative boundaries;
- security boundaries;
- Group Policy targeting;
- system role;
- lifecycle management.

They are not intended to reproduce the fictional company's organisational chart.

Initial structure:

```text
ad.cybertron.test
│
├── Domain Controllers
│   └── CYB-SRV01
│
└── CyberTron
    │
    ├── Users
    │   ├── Corporate
    │   └── IT
    │
    ├── Privileged
    │   ├── Admin Accounts
    │   └── Admin Workstations
    │
    ├── Computers
    │   ├── Windows
    │   ├── Linux
    │   └── macOS
    │
    ├── Servers
    │   ├── Windows
    │   ├── Linux
    │   └── Security
    │
    ├── Groups
    │   ├── Security
    │   └── Distribution
    │
    ├── Service Accounts
    │
    └── Disabled Objects
        ├── Users
        └── Computers
```

---

## 9. Domain Controllers OU

Active Directory automatically creates the:

```text
Domain Controllers
```

OU.

Domain controllers will remain within this dedicated OU.

CYB-SRV01 will therefore reside at:

```text
ad.cybertron.test
└── Domain Controllers
    └── CYB-SRV01
```

A separate CyberTron Domain Controllers OU will not be created.

This preserves the standard Active Directory domain-controller administrative model and provides an appropriate location for domain-controller-specific Group Policy.

---

## 10. Users OU

The initial user structure is:

```text
CyberTron
└── Users
    ├── Corporate
    └── IT
```

### Corporate

Contains standard business user identities.

### IT

Contains standard user identities belonging to personnel performing technical roles.

Administrative privileges are not assigned to these standard user identities merely because the user belongs to IT.

Privileged identities are maintained separately.

---

## 11. Privileged OU

Privileged identities and systems are separated from ordinary user and endpoint objects.

```text
CyberTron
└── Privileged
    ├── Admin Accounts
    └── Admin Workstations
```

### Admin Accounts

Contains dedicated privileged identities used for administrative operations.

### Admin Workstations

Contains systems specifically designated for privileged administration.

This structure supports future implementation of:

- privileged access workstations;
- restricted administrative logon;
- privileged Group Policy;
- enhanced auditing;
- separate security baselines.

---

## 12. Computers OU

User endpoint systems are organised by operating-system family:

```text
CyberTron
└── Computers
    ├── Windows
    ├── Linux
    └── macOS
```

This allows operating-system-specific management and policy.

Windows endpoints may receive Group Policy directly.

Linux and macOS systems will use Active Directory for identity integration but will not be treated as if Windows Group Policy provides identical management capabilities.

---

## 13. Servers OU

Non-domain-controller servers are separated from endpoint systems:

```text
CyberTron
└── Servers
    ├── Windows
    ├── Linux
    └── Security
```

### Windows

Windows member servers.

### Linux

Linux infrastructure servers.

### Security

Systems primarily providing cybersecurity monitoring, detection or security services.

Examples may include:

```text
Wazuh
Security Onion
future SOC infrastructure
```

---

## 14. Groups OU

CyberTron will use groups as the primary mechanism for assigning permissions.

```text
CyberTron
└── Groups
    ├── Security
    └── Distribution
```

Security groups will be used for:

- resource access;
- administrative delegation;
- role-based access control;
- application permissions;
- server administration;
- security-tool administration.

Direct permission assignment to individual users should be avoided where practical.

---

## 15. Service Accounts OU

Service identities will be separated from human identities:

```text
CyberTron
└── Service Accounts
```

This OU will support future learning involving:

- traditional service accounts;
- service-account lifecycle management;
- least privilege;
- password management;
- Group Managed Service Accounts;
- service authentication.

---

## 16. Disabled Objects OU

CyberTron will preserve disabled identities temporarily rather than immediately deleting them.

```text
CyberTron
└── Disabled Objects
    ├── Users
    └── Computers
```

This supports:

- controlled offboarding;
- investigation;
- recovery from accidental disablement;
- identity lifecycle management;
- auditability.

---

## 17. Identity Model

CyberTron will separate standard and privileged identities.

Initial model:

```text
Standard identity:
wst

Privileged identity:
wst-admin
```

Example domain identities:

```text
CYBERTRON\wst
CYBERTRON\wst-admin
```

UPNs:

```text
wst@ad.cybertron.test
wst-admin@ad.cybertron.test
```

The standard identity will be used for ordinary activity.

The privileged identity will be used only when administrative rights are required.

---

## 18. Future Public UPN

CyberTron does not currently own a public DNS domain.

If a public domain is acquired later, for example:

```text
cybertronsecurity.co.nz
```

it may be added as an alternative Active Directory UPN suffix.

This could allow identities such as:

```text
wst@cybertronsecurity.co.nz
```

without renaming the Active Directory forest.

This will support future Microsoft Entra ID and Microsoft 365 identity integration.

---

## 19. Administrative Access Model

Administrative permissions will be assigned through groups rather than directly to user accounts.

Potential future administrative roles include:

```text
Server Administrators
Workstation Administrators
Network Administrators
Security Administrators
SIEM Administrators
```

The exact group architecture will be implemented incrementally as services are introduced.

Domain Admin membership will be kept deliberately limited.

Domain Admin will not be treated as the default mechanism for ordinary administrative work.

---

## 20. Domain Membership Strategy

All appropriate CyberTron endpoint and server operating systems will use the central Active Directory identity plane.

### Windows

Windows systems will be directly joined to:

```text
ad.cybertron.test
```

### Linux

Linux systems will integrate with Active Directory using technologies such as:

```text
Kerberos
SSSD
realmd
LDAP
```

as appropriate.

### macOS

macOS will be integrated with the CyberTron Active Directory environment for central identity learning and testing.

### Infrastructure Appliances

Network and virtualisation infrastructure may use Active Directory or LDAP authentication where supported, but are not assumed to behave as Windows domain members.

Examples include:

```text
CYB-RTR-01
Proxmox
```

---

## 21. DNS Architecture

Active Directory depends on DNS for service discovery.

CYB-SRV01 will become the authoritative DNS server for:

```text
ad.cybertron.test
```

Domain members will use CYB-SRV01 as their primary internal DNS resolver.

Architecture:

```text
CyberTron Domain Members
          │
          │ DNS
          ▼
      CYB-SRV01
     172.16.10.10
          │
          ├── ad.cybertron.test
          │
          ├── LDAP service records
          │
          ├── Kerberos service records
          │
          ├── domain-controller records
          │
          └── internal host records
          │
          │ unresolved external query
          ▼
      DNS Forwarder
          │
          ▼
      CYB-RTR-01
     172.16.10.1
          │
          ▼
    Upstream DNS
```

---

## 22. DNS Client Architecture

Current CyberTron clients receive:

```text
DNS: 172.16.10.1
```

from CYB-RTR-01.

After Active Directory DNS is operational, CyberTron DHCP will provide:

```text
Default Gateway: 172.16.10.1
DNS Server:       172.16.10.10
```

This ensures domain members query Active Directory DNS first.

Domain members must not use public DNS servers directly as their primary resolver because Active Directory service discovery depends on internal DNS records.

---

## 23. DNS Forwarding

CYB-SRV01 will resolve the internal Active Directory namespace directly.

Queries for external names will be forwarded upstream.

Initial forwarding architecture:

```text
CYB-SRV01
    │
    ▼
CYB-RTR-01
172.16.10.1
    │
    ▼
Upstream DNS
```

This preserves the MikroTik's role as the network's upstream DNS forwarding point while ensuring Active Directory clients query AD DNS first.

---

## 24. Static Addressing Requirement

A domain controller must have stable network addressing.

Before CYB-SRV01 is promoted, its current DHCP-reservation-based configuration will be replaced with a static IPv4 configuration inside Windows.

Target configuration:

```text
IPv4 Address: 172.16.10.10
Prefix:       /24
Gateway:      172.16.10.1
```

The DNS client configuration will be changed as part of the AD DS/DNS implementation sequence.

The existing static Hyper-V MAC will remain:

```text
00:15:5D:BE:61:01
```

---

## 25. DHCP Architecture

DHCP will remain on:

```text
CYB-RTR-01
172.16.10.1
```

Active Directory Domain Services will not initially take over DHCP.

Responsibilities therefore remain separated:

```text
CYB-RTR-01
├── DHCP
├── Gateway
├── Firewall
└── NAT

CYB-SRV01
├── AD DS
├── DNS
├── Kerberos
├── LDAP
└── Group Policy
```

This provides clear infrastructure role separation while retaining the existing stable MikroTik DHCP service.

---

## 26. Initial Group Policy Architecture

Group Policy will be introduced gradually.

The initial strategy will avoid creating large numbers of overlapping GPOs.

Planned baseline GPO categories include:

```text
Default Domain Policy
        │
        └── Domain-wide account and Kerberos policy only

Domain Controller Baseline
        │
        └── Domain-controller-specific security controls

Windows Server Baseline
        │
        └── Member-server security configuration

Windows Workstation Baseline
        │
        └── Windows endpoint security configuration

Privileged Workstation Baseline
        │
        └── Enhanced controls for administrative systems

Advanced Audit Policy
        │
        └── Security telemetry and audit configuration
```

---

## 27. Default Domain Policy Principle

The Default Domain Policy will not become a general-purpose configuration container.

It should primarily contain domain-level settings that genuinely require domain scope, including:

- password policy;
- account lockout policy;
- Kerberos policy.

Other settings should normally be implemented through purpose-specific GPOs.

---

## 28. Security Logging

CyberTron will use Windows Advanced Audit Policy to increase useful security telemetry.

Potential future auditing includes:

```text
Process Creation
Account Management
Logon / Logoff
Credential Validation
Kerberos
Policy Change
Privilege Use
Directory Service Changes
PowerShell activity
```

Audit policy will be implemented deliberately through Group Policy rather than enabling every audit category indiscriminately.

Security events will later be forwarded or collected by Wazuh.

---

## 29. Security Monitoring Integration

Future architecture:

```text
Active Directory
       │
       ├── Authentication Events
       ├── Account Changes
       ├── Group Changes
       ├── Kerberos Events
       ├── Policy Changes
       └── Administrative Activity
                    │
                    ▼
                  Wazuh
                    │
                    ▼
             Security Monitoring
```

This will provide practical experience correlating identity activity with security monitoring.

---

## 30. Time Architecture

Kerberos authentication depends on reliable time synchronisation.

The initial domain controller holding the PDC Emulator role will become the authoritative time reference for the domain hierarchy.

Target architecture:

```text
External Reliable Time Source
            │
            ▼
     PDC Emulator
       CYB-SRV01
            │
            ▼
     AD Domain Hierarchy
            │
      ┌─────┼─────┐
      │     │     │
   Windows Linux macOS
```

Detailed time configuration will be implemented after domain promotion.

---

## 31. Security Boundaries

The initial Active Directory deployment will operate on the existing:

```text
172.16.10.0/24
```

CyberTron LAN.

Network segmentation is deliberately deferred.

Future architecture will introduce:

```text
Management VLAN
Server VLAN
Client VLAN
Security VLAN
Lab VLAN
```

Active Directory architecture must remain compatible with this future segmentation.

---

## 32. Initial Implementation Sequence

The approved implementation sequence is:

```text
Architecture approved
        │
        ▼
PRE-ADDS checkpoint
        │
        ▼
Configure CYB-SRV01 static IPv4
        │
        ▼
Install AD DS role
        │
        ▼
Install DNS role
        │
        ▼
Create ad.cybertron.test forest
        │
        ▼
Promote CYB-SRV01
        │
        ▼
Validate AD DS
        │
        ▼
Validate DNS
        │
        ▼
Create OU structure
        │
        ▼
Create identity model
        │
        ▼
Configure initial GPOs
        │
        ▼
Change DHCP DNS option
        │
        ▼
Join Windows systems
        │
        ▼
Integrate Linux systems
        │
        ▼
Integrate macOS
```

---

## 33. Out of Scope for Initial Implementation

The following are deliberately deferred:

```text
Second domain controller
Microsoft Entra Connect
Hybrid Microsoft Entra ID
Microsoft 365 integration
Certificate Services / PKI
Federation Services
Read-Only Domain Controllers
Multiple AD domains
Multiple forests
Trust relationships
Privileged Access Management platform
Production-grade disaster recovery
VLAN segmentation
```

These may be introduced through later CyberTron projects.

---

## 34. Risks

### Single Domain Controller

Failure of CYB-SRV01 will temporarily remove:

- domain authentication;
- AD DNS;
- Group Policy processing;
- directory services.

Mitigation:

```text
Hyper-V checkpoint before implementation
Host-level backup
Future second domain controller
Documented recovery procedures
```

### Single DNS Server

The initial DNS architecture depends on CYB-SRV01.

Mitigation:

```text
Future second AD-integrated DNS server
```

### Evaluation Operating System

CYB-SRV01 currently uses Windows Server 2022 Standard Evaluation.

The evaluation lifecycle must be monitored.

### Flat Network

The initial domain operates without VLAN security boundaries.

Mitigation:

```text
Host firewalls
Restricted administrative access
Future segmentation project
```

---

## 35. Future Architecture Evolution

The Active Directory environment is expected to evolve toward:

```text
                    Microsoft 365
                         │
                  Microsoft Entra ID
                         │
                  Hybrid Identity
                         │
                 ad.cybertron.test
                         │
             ┌───────────┴───────────┐
             │                       │
         CYB-SRV01              Second DC
             │                       │
             └───────────┬───────────┘
                         │
                    AD DNS / GPO
                         │
             ┌───────────┼───────────┐
             │           │           │
          Windows      Linux       macOS
```

This evolution will occur only when supported by a defined business or learning requirement.

---

## 36. Architecture Status

**Approved for Initial Implementation**

The next implementation activity is:

```text
CYB-SRV01
Pre-AD DS Network Preparation
```

No Active Directory role installation should occur until the associated architecture decision has been recorded.

---

## 37. Related Documentation

- `ADR-006-Identity-and-Access-Management.md`
- `ADR-009-Active-Directory-Domain-and-DNS-Architecture.md`
- `ADR-008-MikroTik-Router-Implementation.md`
- `Network-Architecture-v0.2.md`
- `BL-009-CYB-SRV01-Foundation-Baseline.md`
- `Windows-Server-Inventory.md`
- `Network-Inventory.md`

---

## 38. Document Control

| Version | Date | Change |
|---|---|---|
| 0.1 | 2026-08-29 | Initial Active Directory architecture approved |