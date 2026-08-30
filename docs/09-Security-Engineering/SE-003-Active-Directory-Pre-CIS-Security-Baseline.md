# CyberTron Active Directory Security Baseline — Pre-CIS Checkpoint

**Lab:** CyberTron Enterprise  
**Domain:** `ad.cybertron.test`  
**Domain Controller:** `CYB-SRV01`  
**Operating System:** Windows Server 2022  
**Checkpoint Purpose:** Record the Active Directory configuration immediately before formal CIS Benchmark assessment and remediation.

---

## 1. Purpose

This document records the security configuration of the CyberTron Active Directory environment before formal assessment against the CIS Microsoft Windows Server 2022 Benchmark.

The objective is to preserve a known-good baseline so that later CIS hardening changes can be:

- traced;
- tested;
- validated;
- compared against the previous state;
- rolled back if necessary; and
- documented as security-control evidence.

This checkpoint represents the transition from initial Active Directory deployment into structured security hardening and compliance assessment.

---

## 2. Current Active Directory Environment

### Domain

| Item | Configuration |
|---|---|
| AD DNS Domain | `ad.cybertron.test` |
| NetBIOS Domain | `CYBERTRON` |
| Domain Controller | `CYB-SRV01` |
| Server IP | `172.16.10.10` |
| Server Role | Active Directory Domain Services / DNS |
| Operating System | Windows Server 2022 |

---

## 3. Organisational Unit Structure

The following custom OU hierarchy has been created beneath the root CyberTron OU.

```text
OU=CyberTron
│
├── OU=Users
│   ├── OU=Corporate
│   └── OU=IT
│
├── OU=Privileged
│   ├── OU=Admin Accounts
│   └── OU=Admin Workstations
│
├── OU=Computers
│   ├── OU=Windows
│   └── OU=macOS
│
├── OU=Servers
│   ├── OU=Windows
│   ├── OU=Linux
│   └── OU=macOS
│
├── OU=Groups
│   ├── OU=Security
│   └── OU=Distribution
│
├── OU=Service Accounts
│
└── OU=Disabled Objects
    ├── OU=Users
    └── OU=Computers
```

Accidental-deletion protection was enabled when the custom OUs were created.

---

## 4. Administrative Identity Model

A separate standard-user and privileged-administrator identity model has been established.

### Standard Account

| Property | Value |
|---|---|
| Account | `wstynder` |
| UPN | `wstynder@ad.cybertron.test` |
| OU | `OU=IT,OU=Users,OU=CyberTron` |
| Purpose | Normal user activity |
| Administrative use | No |

### Privileged Account

| Property | Value |
|---|---|
| Account | `wstynder-admin` |
| UPN | `wstynder-admin@ad.cybertron.test` |
| OU | `OU=Admin Accounts,OU=Privileged,OU=CyberTron` |
| Purpose | Dedicated administrative identity |
| Administrative use | Yes |

The privileged identity is intentionally separated from the normal user identity.

This follows the principle that administrative credentials should not be used for routine activities such as web browsing, email, or normal workstation use.

---

## 5. Administrative Security Groups

The following Global Security groups have been created:

| Group | Scope | Type | Intended Purpose |
|---|---|---|---|
| `GG-Security-Admins` | Global | Security | Security administration |
| `GG-Server-Admins` | Global | Security | Server administration |
| `GG-SIEM-Admins` | Global | Security | SIEM administration |
| `GG-Workstation-Admins` | Global | Security | Workstation administration |

These groups provide the foundation for future role-based access control rather than assigning privileges directly to individual user accounts.

---

## 6. Domain Password Policy

The current domain password policy was configured before CIS assessment.

| Setting | Current Value |
|---|---:|
| Password complexity | Enabled |
| Password history | 24 passwords |
| Minimum password length | 12 characters |
| Minimum password age | 1 day |
| Maximum password age | 90 days |
| Reversible encryption | Disabled |

### Current assessment status

This configuration has **not yet been remediated against CIS Benchmark requirements**.

Known differences identified during initial comparison:

- CIS Level 1 recommends a minimum password length of at least **14 characters**.
- CyberTron currently requires **12 characters**.

This difference will be formally assessed and remediated during CIS Phase 1.

---

## 7. Account Lockout Policy

Current account lockout settings:

| Setting | Current Value |
|---|---:|
| Account lockout threshold | 10 failed attempts |
| Account lockout duration | 15 minutes |
| Reset account lockout counter | 15 minutes |

### Current assessment status

Known difference identified during initial CIS comparison:

- CIS Level 1 recommends **5 or fewer invalid logon attempts, but not 0**.
- CyberTron currently allows **10 attempts**.

This difference will be formally assessed during CIS Phase 1.

---

## 8. Group Policy Architecture

Separate Group Policy Objects have been created so security settings can be applied according to system role.

### Current CyberTron GPOs

```text
CyberTron - Windows Workstation Baseline
CyberTron - Windows Server Baseline
CyberTron - Privileged Workstation Baseline
CyberTron - Domain Controller Baseline
```

### Current GPO Links

#### Windows Workstations

Target:

```text
OU=Windows,OU=Computers,OU=CyberTron
```

Linked GPO:

```text
CyberTron - Windows Workstation Baseline
```

#### Windows Servers

Target:

```text
OU=Windows,OU=Servers,OU=CyberTron
```

Linked GPO:

```text
CyberTron - Windows Server Baseline
```

#### Privileged Administrative Workstations

Target:

```text
OU=Admin Workstations,OU=Privileged,OU=CyberTron
```

Linked GPO:

```text
CyberTron - Privileged Workstation Baseline
```

#### Domain Controllers

Target:

```text
OU=Domain Controllers,DC=ad,DC=cybertron,DC=test
```

Linked GPO:

```text
CyberTron - Domain Controller Baseline
```

The Microsoft `Default Domain Controllers Policy` remains linked to the Domain Controllers OU.

---

## 9. Planned Policy Ownership

CyberTron will not place every security setting into a single GPO.

The intended security-policy model is:

| GPO | Responsibility |
|---|---|
| Default Domain Policy | Domain-wide password and account-lockout policy |
| Default Domain Controllers Policy | Microsoft/CIS settings that specifically require the default DC policy |
| CyberTron - Domain Controller Baseline | Additional Domain Controller security hardening |
| CyberTron - Windows Server Baseline | Member-server security hardening |
| CyberTron - Windows Workstation Baseline | Standard workstation hardening |
| CyberTron - Privileged Workstation Baseline | Higher-security administrative workstation controls |
| CyberTron - Advanced Audit Policy | Future centralised security auditing configuration |

This design provides separation of responsibilities and makes individual security controls easier to identify, test, audit, and troubleshoot.

---

## 10. CIS Benchmark Selected

The following benchmark will be used for the first formal Windows Server security assessment:

```text
CIS Microsoft Windows Server 2022 Benchmark
Version: 5.1.0
Release date: 28 July 2026
Initial target profile: Level 1 - Domain Controller
```

The Level 1 Domain Controller profile was selected because it is intended to provide practical security improvements without unnecessarily preventing normal Domain Controller operation.

Level 2 and Next Generation Windows Security controls may be evaluated later after Level 1 is stable.

---

## 11. CIS Implementation Method

The CIS Benchmark will **not** be applied blindly.

Each control will use the following workflow:

```text
ASSESS
   ↓
IDENTIFY GAP
   ↓
UNDERSTAND RISK
   ↓
DECIDE
   ↓
REMEDIATE
   ↓
TEST
   ↓
VALIDATE
   ↓
DOCUMENT EVIDENCE
```

For each recommendation the following will be recorded:

- CIS control number;
- recommendation;
- current configuration;
- expected configuration;
- pass/fail status;
- security rationale;
- operational impact;
- remediation performed;
- validation method;
- evidence;
- exception or risk acceptance, where applicable.

---

## 12. CIS Phase Plan

### Phase 1 — Account Policies

Scope:

```text
1.1 Password Policy
1.2 Account Lockout Policy
```

Initial known findings:

| CIS Control | Control | Current | CIS L1 | Initial Result |
|---|---|---:|---:|---|
| 1.1.1 | Password history | 24 | ≥24 | PASS |
| 1.1.2 | Maximum password age | 90 days | ≤365, not 0 | PASS |
| 1.1.3 | Minimum password age | 1 day | ≥1 | PASS |
| 1.1.4 | Minimum password length | 12 | ≥14 | FAIL |
| 1.1.5 | Password complexity | Enabled | Enabled | PASS |
| 1.1.7 | Reversible encryption | Disabled | Disabled | PASS |
| 1.2.1 | Account lockout duration | 15 min | ≥15 min | PASS |
| 1.2.2 | Account lockout threshold | 10 | ≤5, not 0 | FAIL |
| 1.2.4 | Reset lockout counter | 15 min | ≥15 min | PASS |

These are preliminary findings only.

Formal assessment and evidence collection will occur during CIS Phase 1.

---

## 13. Change-Control Principle

Before significant security-hardening changes:

1. Record the existing configuration.
2. Confirm the intended CIS recommendation.
3. Review the recommendation's impact.
4. Create or confirm a working rollback point.
5. Apply the change.
6. Validate Active Directory functionality.
7. Validate the security control.
8. Record the result.
9. Document any exception.

This prevents benchmark compliance from taking priority over system availability or required functionality.

---

## 14. Recovery Checkpoint

A virtual-machine checkpoint was created after validation of the initial Active Directory configuration and before CIS hardening.

This checkpoint provides a rollback point if later security controls cause unexpected authentication, Group Policy, DNS, or Domain Controller behaviour.

The checkpoint is a lab recovery mechanism and is not considered a replacement for proper Active Directory backup.

---

## 15. Security Concepts Practised

This phase of the lab has provided practical experience with:

- Active Directory Domain Services;
- DNS-integrated Active Directory;
- organisational unit design;
- administrative tier separation;
- dedicated privileged accounts;
- role-based security groups;
- Group Policy Objects;
- Group Policy inheritance;
- password policy;
- account lockout policy;
- privileged workstation concepts;
- security baselines;
- configuration assessment;
- CIS Benchmarks;
- change control;
- compliance evidence;
- exception management;
- least privilege; and
- separation of duties.

---

## 16. Next Step

The next lab activity is:

**CIS Phase 1 — Account Policy Assessment**

The objective is to formally assess CyberTron's current Active Directory password and account-lockout policies against the CIS Microsoft Windows Server 2022 Level 1 Domain Controller recommendations before performing controlled remediation.

---

## References

- Center for Internet Security, *CIS Microsoft Windows Server 2022 Benchmark*, v5.1.0, 28 July 2026.
- Microsoft Active Directory Domain Services documentation.
- CyberTron homelab configuration and validation evidence.

> **Repository note:** The CIS Benchmark PDF is not stored in this GitHub repository. CIS licensing/terms restrict redistribution of benchmark documents through third-party sites. Obtain the benchmark directly from the Center for Internet Security.