# ADR-009 — Active Directory Domain and DNS Architecture

- **ADR ID:** ADR-009
- **Architecture Domain:** Identity
- **Date:** 2026-08-29
- **Status:** Accepted
- **Decision Owner:** CyberTron Enterprise

---

## 1. Context

CyberTron Enterprise requires a central identity platform to support the next phase of enterprise infrastructure development.

The environment currently contains:

- Windows Server;
- Windows endpoints;
- Linux servers and endpoints;
- macOS;
- Proxmox;
- Hyper-V;
- MikroTik network infrastructure;
- Wazuh security monitoring.

Authentication is currently primarily local to individual systems.

This limits the ability to practise and implement:

- central identity management;
- role-based access control;
- privileged administration;
- Group Policy;
- enterprise authentication;
- central security auditing;
- identity lifecycle management;
- Microsoft enterprise administration.

CyberTron therefore requires an Active Directory Domain Services architecture.

---

## 2. Decision

CyberTron Enterprise will implement a single Active Directory forest containing a single Active Directory domain.

The forest and domain will be:

```text
ad.cybertron.test
```

The NetBIOS domain name will be:

```text
CYBERTRON
```

The first domain controller will be:

```text
CYB-SRV01
172.16.10.10
```

CYB-SRV01 will provide:

```text
Active Directory Domain Services
Active Directory-integrated DNS
Global Catalog
Kerberos authentication
LDAP directory services
Group Policy infrastructure
```

---

## 3. Namespace Decision

CyberTron does not currently own a public Internet DNS domain.

A reserved testing namespace will therefore be used.

Namespace family:

```text
cybertron.test
│
├── ad.cybertron.test
├── dev.cybertron.test
└── lab.cybertron.test
```

Only:

```text
ad.cybertron.test
```

will initially be an Active Directory domain.

The `dev` and `lab` namespaces are reserved for future DNS and application use.

They are not additional AD domains.

---

## 4. Rationale for `.test`

The `.test` top-level domain is appropriate for the CyberTron lab because it is reserved for testing purposes and avoids dependence on a public namespace that CyberTron does not own.

The design avoids:

```text
cybertron.local
```

because `.local` is commonly associated with multicast DNS and may introduce unnecessary naming ambiguity in a mixed Windows, Linux and macOS environment.

The design also avoids using a public DNS namespace owned by another organisation.

---

## 5. Rationale for `ad.cybertron.test`

The Active Directory namespace will use:

```text
ad.cybertron.test
```

rather than:

```text
cybertron.test
```

to explicitly identify the namespace as the enterprise identity namespace.

This preserves conceptual namespace separation:

```text
ad.cybertron.test   → Identity
dev.cybertron.test  → Development
lab.cybertron.test  → Lab services
```

while maintaining a single Active Directory domain.

---

## 6. Forest Topology

CyberTron will initially operate:

```text
1 Forest
1 Domain
1 Domain Controller
```

Architecture:

```text
CyberTron Enterprise
        │
        ▼
ad.cybertron.test
        │
        ▼
CYB-SRV01
```

Additional domains and forests will not be introduced without a documented architectural requirement.

---

## 7. Domain Controller

The first domain controller will be:

```text
Hostname: CYB-SRV01
IPv4:     172.16.10.10
```

CYB-SRV01 currently exists as a Windows Server 2022 Hyper-V virtual machine.

A known-good pre-AD DS baseline and Hyper-V checkpoint have been created before implementation.

---

## 8. Static Addressing

CYB-SRV01 currently receives:

```text
172.16.10.10
```

through a MikroTik DHCP reservation.

Before domain-controller promotion, the address will be configured statically within Windows.

Target network configuration:

```text
IPv4:    172.16.10.10
Prefix:  /24
Gateway: 172.16.10.1
```

The existing Hyper-V static MAC remains:

```text
00:15:5D:BE:61:01
```

---

## 9. DNS Decision

Active Directory-integrated DNS will be installed on CYB-SRV01.

CYB-SRV01 will become authoritative for:

```text
ad.cybertron.test
```

Domain clients will use:

```text
172.16.10.10
```

as their primary DNS resolver.

---

## 10. DNS Forwarding

CYB-SRV01 will resolve the Active Directory namespace directly.

Queries outside the authoritative internal namespace will be forwarded upstream.

Initial architecture:

```text
Domain Client
     │
     ▼
CYB-SRV01
172.16.10.10
     │
     ▼
CYB-RTR-01
172.16.10.1
     │
     ▼
Upstream DNS
```

---

## 11. DHCP Decision

DHCP will remain on:

```text
CYB-RTR-01
```

Active Directory implementation will not move DHCP to Windows Server.

After Active Directory DNS is validated, the MikroTik DHCP configuration will provide:

```text
Gateway: 172.16.10.1
DNS:     172.16.10.10
```

to CyberTron clients.

This separates network address management from identity services.

---

## 12. Domain Membership Decision

All appropriate CyberTron operating systems will use the central Active Directory identity plane.

This includes:

```text
Windows
Linux
macOS
```

Windows systems will be directly joined to the Active Directory domain.

Linux and macOS systems will be integrated using the appropriate platform mechanisms.

Infrastructure appliances may use Active Directory or LDAP authentication where supported but are not assumed to behave as Windows domain members.

---

## 13. Organisational Unit Decision

CyberTron will use an educational OU structure designed around:

- policy application;
- administration;
- security;
- system role;
- identity lifecycle.

Initial structure:

```text
ad.cybertron.test
│
├── Domain Controllers
│
└── CyberTron
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

The OU structure will not simply reproduce a fictional organisational chart.

---

## 14. Privileged Identity Decision

Standard and privileged identities will be separated.

Initial model:

```text
Standard:
CYBERTRON\wst

Privileged:
CYBERTRON\wst-admin
```

The standard identity will be used for ordinary work.

The privileged identity will be used only when elevated administrative access is required.

---

## 15. Access-Control Decision

Permissions will be assigned primarily through Active Directory security groups.

Direct permission assignment to individual user identities will be avoided where practical.

Future role groups may include:

```text
Server Administrators
Workstation Administrators
Network Administrators
Security Administrators
SIEM Administrators
```

This supports:

- least privilege;
- role-based access control;
- separation of duties;
- auditable access assignment.

---

## 16. Group Policy Decision

Group Policy will be implemented incrementally.

The initial strategy will include purpose-specific GPOs rather than placing all configuration into the Default Domain Policy.

Initial planned policy areas:

```text
Domain Account Policy
Domain Controller Baseline
Windows Server Baseline
Windows Workstation Baseline
Privileged Workstation Baseline
Advanced Audit Policy
```

The Default Domain Policy will primarily retain settings that genuinely require domain-wide scope.

---

## 17. Security Monitoring Decision

Active Directory security telemetry will later be integrated with Wazuh.

Relevant event categories will include:

```text
Authentication
Account management
Group membership changes
Kerberos
Privilege use
Policy changes
Directory changes
Administrative activity
```

Audit configuration will be implemented deliberately through Group Policy.

---

## 18. Time Synchronisation Decision

The domain's PDC Emulator will provide the authoritative domain time hierarchy.

CYB-SRV01 will initially hold this role.

Target architecture:

```text
External Time Source
        │
        ▼
CYB-SRV01
PDC Emulator
        │
        ▼
Domain Members
```

Detailed NTP configuration will occur after domain promotion.

---

## 19. Alternatives Considered

### Alternative A — Continue Using Local Accounts

Rejected.

Local-only authentication does not support the enterprise identity, Group Policy, central auditing and access-control learning objectives of CyberTron.

---

### Alternative B — Use `cybertron.local`

Rejected.

Although historically common in Active Directory deployments, `.local` is associated with multicast DNS and is undesirable for a new mixed-platform CyberTron environment.

---

### Alternative C — Use a Public Domain Not Owned by CyberTron

Rejected.

CyberTron should not build internal identity infrastructure using a public namespace controlled by another organisation.

---

### Alternative D — Purchase a Public Domain Before AD Deployment

Deferred.

A public domain would provide advantages for future Microsoft 365 and Microsoft Entra integration, but purchasing one is not required to achieve the current Active Directory learning objectives.

A verified public namespace may later be added as an alternative UPN suffix.

---

### Alternative E — Multiple Domains

Rejected.

CyberTron's current scale and learning objectives do not justify multiple AD domains.

Additional domains would introduce unnecessary:

- administrative complexity;
- DNS complexity;
- replication complexity;
- policy complexity.

---

### Alternative F — Multiple Forests

Rejected.

No current isolation, acquisition, regulatory or trust requirement justifies multiple forests.

---

### Alternative G — Move DHCP to Windows Server

Rejected for the initial implementation.

The existing MikroTik DHCP service is stable and provides useful separation between:

```text
Network services
```

and:

```text
Identity services
```

DHCP may be revisited in a later project if a specific learning requirement justifies Windows DHCP.

---

## 20. Consequences

### Positive

The decision provides:

- central identity;
- central authentication;
- central authorisation;
- Group Policy;
- enterprise DNS;
- privileged-account separation;
- mixed-platform identity learning;
- security monitoring opportunities;
- future Microsoft ecosystem integration.

### Negative

The initial architecture creates dependency on a single domain controller.

If CYB-SRV01 is unavailable, the environment may lose:

- Active Directory authentication;
- internal AD DNS;
- Group Policy processing;
- directory services.

This risk is accepted during the initial implementation phase.

---

## 21. Risks and Mitigations

| Risk | Mitigation |
|---|---|
| Single domain controller | Future second DC |
| Single AD DNS server | Future second AD-integrated DNS server |
| AD misconfiguration | PRE-ADDS Hyper-V checkpoint |
| DNS failure | Documented DNS architecture and validation |
| Privilege misuse | Separate standard/admin identities |
| Excessive Domain Admin use | Group-based delegated administration |
| Evaluation OS lifecycle | Track Windows Server evaluation period |
| Flat LAN | Host firewalls and future VLAN segmentation |

---

## 22. Implementation Preconditions

Before installing Active Directory Domain Services:

- CYB-SRV01 foundation baseline must be complete;
- GitHub documentation must be committed;
- PRE-ADDS Hyper-V checkpoint must exist;
- CYB-SRV01 must be healthy;
- no reboot must be pending;
- network connectivity must be verified;
- CYB-SRV01 must be configured with static IPv4 addressing.

---

## 23. Implementation Sequence

```text
ADR accepted
      │
      ▼
Verify PRE-ADDS checkpoint
      │
      ▼
Configure static IPv4
      │
      ▼
Install AD DS + DNS
      │
      ▼
Create ad.cybertron.test
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
Implement OU structure
      │
      ▼
Create standard and privileged identities
      │
      ▼
Implement initial GPO strategy
      │
      ▼
Update MikroTik DHCP DNS option
      │
      ▼
Begin domain-member onboarding
```

---

## 24. Future Decisions

Separate ADRs may be created for:

- second domain controller;
- Microsoft Entra integration;
- Microsoft 365 integration;
- PKI / Active Directory Certificate Services;
- privileged access architecture;
- enterprise Group Policy baseline;
- Windows LAPS;
- VLAN segmentation;
- Active Directory backup and disaster recovery.

---

## 25. Decision Outcome

**Accepted**

CyberTron Enterprise will implement:

```text
Forest:          ad.cybertron.test
Domain:          ad.cybertron.test
NetBIOS:         CYBERTRON
First DC:        CYB-SRV01
DC IPv4:         172.16.10.10
DNS:             Active Directory-integrated
DHCP:            CYB-RTR-01
Identity Model:  Standard + Privileged
Platform Scope:  Windows + Linux + macOS
```

---

## 26. Related Documentation

- `Active-Directory-Architecture-v0.1.md`
- `ADR-006-Identity-and-Access-Management.md`
- `ADR-008-MikroTik-Router-Implementation.md`
- `Network-Architecture-v0.2.md`
- `BL-009-CYB-SRV01-Foundation-Baseline.md`
- `Windows-Server-Inventory.md`
- `Network-Inventory.md`

---

## 27. Review Trigger

Review this ADR if:

- CyberTron acquires a public DNS domain;
- a second domain controller is introduced;
- Microsoft Entra integration begins;
- Microsoft 365 identity integration begins;
- multiple domains or forests are proposed;
- the DNS architecture materially changes;
- the DHCP architecture materially changes.