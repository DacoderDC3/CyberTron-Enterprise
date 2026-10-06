# CIS Phase 01 — Account Policy Assessment

**Environment:** CyberTron Enterprise Homelab  
**Domain:** `ad.cybertron.test`  
**Domain Controller:** `CYB-SRV01`  
**Operating System:** Windows Server 2022  
**Benchmark:** CIS Microsoft Windows Server 2022 Benchmark v5.1.0  
**Profile:** Level 1 — Domain Controller  
**Assessment Scope:** CIS Section 1 — Account Policies  
**Assessment Status:** Complete  
**Final Result:** 9/9 applicable controls compliant  
**Recovery Checkpoint:** `CIS-PH01-Account-Policy-Compliant`

---

## 1. Purpose

This document records the first formal CIS security assessment performed against the CyberTron Active Directory environment.

The assessment covers:

```text
1.1 Password Policy
1.2 Account Lockout Policy
```

The purpose was not simply to apply the CIS Benchmark.

The assessment followed the CyberTron security-engineering workflow:

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

This provides evidence not only that security settings exist, but that:

- the original configuration was measured;
- gaps were identified against an external benchmark;
- applicability was considered;
- remediation decisions were made;
- changes were controlled;
- effective configuration was verified; and
- Active Directory functionality was validated after remediation.

---

## 2. Assessment Target

| Property | Value |
|---|---|
| Domain | `ad.cybertron.test` |
| NetBIOS domain | `CYBERTRON` |
| Domain Controller | `CYB-SRV01` |
| Domain Controller IP | `172.16.10.10` |
| Operating System | Windows Server 2022 |
| Benchmark | CIS Microsoft Windows Server 2022 Benchmark v5.1.0 |
| Profile | Level 1 — Domain Controller |

---

## 3. Assessment Method

The effective Active Directory domain policy was queried directly rather than relying on previously documented values.

Primary assessment command:

```powershell
Get-ADDefaultDomainPasswordPolicy
```

A concise view was also obtained using:

```powershell
Get-ADDefaultDomainPasswordPolicy | Select-Object ComplexityEnabled,PasswordHistoryCount,MaxPasswordAge,MinPasswordAge,MinPasswordLength,ReversibleEncryptionEnabled,LockoutThreshold,LockoutDuration,LockoutObservationWindow
```

The Default Domain Policy was validated using:

```powershell
Get-GPO -Name "Default Domain Policy" | Select-Object DisplayName,GpoStatus,ModificationTime
```

and:

```powershell
Get-GPInheritance -Target "DC=ad,DC=cybertron,DC=test"
```

This confirmed that the Default Domain Policy remained enabled and linked at the domain root.

---

## 4. Important Baseline Correction

The pre-CIS documentation previously recorded:

```text
Maximum password age: 90 days
```

The live assessment showed that the actual effective configuration was:

```text
MaxPasswordAge : 00:00:00
```

A value of zero means passwords did not expire.

The live configuration was therefore treated as authoritative evidence.

The previous documentation was not used to override or reinterpret the observed system state.

This demonstrated an important security-assessment principle:

> Documentation describes the expected state; technical evidence establishes the actual state.

---

## 5. Initial Assessment Results

### 5.1 Password Policy

| CIS | Control | Initial CyberTron State | CIS Requirement | Initial Result |
|---|---|---:|---:|---|
| 1.1.1 | Enforce password history | 24 | 24 or more | PASS |
| 1.1.2 | Maximum password age | 0 / Never | 365 or fewer days, but not 0 | **FAIL** |
| 1.1.3 | Minimum password age | 1 day | 1 or more days | PASS |
| 1.1.4 | Minimum password length | 12 characters | 14 or more characters | **FAIL** |
| 1.1.5 | Password must meet complexity requirements | Enabled | Enabled | PASS |
| 1.1.6 | Relax minimum password length limits | Not configured | Member Server profile only | **N/A** |
| 1.1.7 | Store passwords using reversible encryption | Disabled | Disabled | PASS |

### 5.2 Account Lockout Policy

| CIS | Control | Initial CyberTron State | CIS Requirement | Initial Result |
|---|---|---:|---:|---|
| 1.2.1 | Account lockout duration | 15 minutes | 15 or more minutes | PASS |
| 1.2.2 | Account lockout threshold | 10 attempts | 5 or fewer, but not 0 | **FAIL** |
| 1.2.3 | Allow Administrator account lockout | N/A | Member Server profile only | **N/A** |
| 1.2.4 | Reset account lockout counter after | 15 minutes | 15 or more minutes | PASS |

---

## 6. Initial Findings

Three applicable CIS Level 1 Domain Controller findings required remediation.

### Finding CIS-01-01

**Control:** CIS 1.1.2 — Maximum password age

**Observed:**

```text
00:00:00
```

This represented a maximum password age of zero, meaning passwords did not expire.

**Required:**

```text
365 or fewer days, but not 0
```

**Decision:** Remediate.

**Selected CyberTron value:**

```text
90 days
```

The selected value meets the CIS requirement while retaining the previously intended CyberTron policy.

---

### Finding CIS-01-02

**Control:** CIS 1.1.4 — Minimum password length

**Observed:**

```text
12 characters
```

**Required:**

```text
14 or more characters
```

**Decision:** Remediate.

**Selected CyberTron value:**

```text
14 characters
```

Longer passwords increase resistance to password guessing and offline password-cracking attacks.

The minimum CIS-compliant value was selected rather than introducing a more aggressive requirement during the initial baseline.

---

### Finding CIS-01-03

**Control:** CIS 1.2.2 — Account lockout threshold

**Observed:**

```text
10 invalid logon attempts
```

**Required:**

```text
5 or fewer invalid logon attempts, but not 0
```

**Decision:** Remediate.

**Selected CyberTron value:**

```text
5 invalid logon attempts
```

A threshold of five was selected rather than a lower value because account lockout represents a balance between:

- resistance to online password guessing; and
- account availability.

A very low threshold can increase accidental lockouts and can provide an attacker with a denial-of-service mechanism.

---

## 7. Operational Lockout Observation

Immediately before this assessment, the privileged account:

```text
CYBERTRON\wstynder-admin
```

was unintentionally locked after repeated incorrect password attempts.

The account was subsequently recovered using the bootstrap Domain Administrator identity.

The following commands were used to investigate the condition:

```powershell
Get-ADUser "wstynder-admin" -Properties LockedOut,Enabled,BadPwdCount,LastBadPasswordAttempt
```

and:

```powershell
Search-ADAccount -LockedOut -UsersOnly
```

After recovery, validation showed:

```text
Enabled      : True
LockedOut    : False
BadPwdCount  : 0
```

This provided a practical demonstration of the operational effect of account-lockout controls.

It also demonstrated why a lower lockout threshold is not automatically better.

Security controls can affect both:

```text
CONFIDENTIALITY / INTEGRITY
            and
        AVAILABILITY
```

The final CyberTron threshold of five represents the CIS Level 1 requirement while avoiding an unnecessarily aggressive setting.

---

## 8. Remediation

The applicable domain Account Policy findings were remediated through Active Directory domain policy.

The resulting configuration was set to:

```text
Maximum password age : 90 days
Minimum password length : 14 characters
Account lockout threshold : 5 attempts
```

The remaining compliant settings were preserved.

---

## 9. Post-Remediation Effective Policy

The effective policy was re-queried using:

```powershell
Get-ADDefaultDomainPasswordPolicy | Select-Object ComplexityEnabled,PasswordHistoryCount,MaxPasswordAge,MinPasswordAge,MinPasswordLength,ReversibleEncryptionEnabled,LockoutThreshold,LockoutDuration,LockoutObservationWindow
```

The validated state was:

```text
ComplexityEnabled              : True
PasswordHistoryCount           : 24
MaxPasswordAge                 : 90.00:00:00
MinPasswordAge                 : 1.00:00:00
MinPasswordLength              : 14
ReversibleEncryptionEnabled    : False
LockoutThreshold               : 5
LockoutDuration                : 00:15:00
LockoutObservationWindow       : 00:15:00
```

---

## 10. Final CIS Compliance Matrix

### Password Policy

| CIS | Control | Final State | Requirement | Final Result |
|---|---|---:|---:|---|
| 1.1.1 | Enforce password history | 24 | ≥24 | **PASS** |
| 1.1.2 | Maximum password age | 90 days | ≤365 days and not 0 | **PASS — Remediated** |
| 1.1.3 | Minimum password age | 1 day | ≥1 day | **PASS** |
| 1.1.4 | Minimum password length | 14 | ≥14 | **PASS — Remediated** |
| 1.1.5 | Password complexity | Enabled | Enabled | **PASS** |
| 1.1.6 | Relax minimum password length limits | N/A | Member Server only | **N/A** |
| 1.1.7 | Reversible encryption | Disabled | Disabled | **PASS** |

### Account Lockout Policy

| CIS | Control | Final State | Requirement | Final Result |
|---|---|---:|---:|---|
| 1.2.1 | Account lockout duration | 15 min | ≥15 min | **PASS** |
| 1.2.2 | Account lockout threshold | 5 | ≤5 and not 0 | **PASS — Remediated** |
| 1.2.3 | Allow Administrator account lockout | N/A | Member Server only | **N/A** |
| 1.2.4 | Reset account lockout counter | 15 min | ≥15 min | **PASS** |

### Final applicable-control result

```text
Applicable controls: 9
PASS:                9
FAIL:                0

Not Applicable:      2
```

**CIS Phase 01 Account Policy compliance: 100% of applicable controls.**

This percentage refers only to the controls assessed in this Phase 01 scope and does not imply overall CIS Windows Server 2022 compliance.

---

## 11. Profile Applicability Correction

During the assessment, CIS control 1.1.6:

```text
Relax minimum password length limits
```

was initially treated as applicable to the Domain Controller profile.

Review of the CIS Microsoft Windows Server 2022 Benchmark v5.1.0 confirmed that this recommendation is applicable to:

```text
Level 1 - Member Server
```

and is not part of the Level 1 Domain Controller profile.

The setting was therefore classified:

```text
N/A — Member Server profile only
```

rather than PASS or FAIL for CYB-SRV01.

This correction demonstrates an important compliance principle:

> A technically desirable security setting must not be reported as a benchmark requirement unless it is applicable to the assessed profile.

Similarly, CIS 1.2.3 is explicitly Member Server only and was classified N/A.

---

## 12. Group Policy Validation

Group Policy application to CYB-SRV01 was validated using:

```powershell
gpresult /r /scope computer
```

The Domain Controller reported the expected applied Group Policy Objects:

```text
Default Domain Controllers Policy
CyberTron - Domain Controller Baseline
Default Domain Policy
```

This confirmed that:

- CYB-SRV01 remained in the Domain Controllers OU;
- the CyberTron Domain Controller baseline was being processed;
- the Default Domain Controllers Policy remained applied; and
- the Default Domain Policy remained inherited.

---

## 13. Post-Change Domain Controller Health Validation

Security remediation was followed by functional testing.

The objective was to ensure that achieving benchmark compliance had not adversely affected Active Directory.

### 13.1 Domain Controller diagnostics

The following command was executed:

```powershell
dcdiag /test:Advertising /test:Services /test:DNS
```

Results:

```text
Connectivity : PASS
Advertising  : PASS
Services     : PASS
DNS          : PASS
```

The `ad.cybertron.test` domain DNS test also passed.

---

### 13.2 LDAP service discovery

The following query was performed:

```powershell
Resolve-DnsName -Type SRV _ldap._tcp.dc._msdcs.ad.cybertron.test
```

Result:

```text
Service      : LDAP
Target       : cyb-srv01.ad.cybertron.test
Port         : 389
IPv4Address  : 172.16.10.10
```

This confirmed that Active Directory LDAP service discovery remained operational.

---

### 13.3 Domain Controller discovery

The following command was executed:

```powershell
Get-ADDomainController -Discover
```

The result successfully discovered:

```text
Domain       : ad.cybertron.test
Forest       : ad.cybertron.test
HostName     : CYB-SRV01.ad.cybertron.test
IPv4Address  : 172.16.10.10
Name         : CYB-SRV01
Site         : Default-First-Site-Name
```

Domain Controller discovery therefore remained functional after remediation.

---

## 14. Control Validation Summary

The completed validation chain was:

```text
CIS REQUIREMENT
       ↓
BASELINE ASSESSMENT
       ↓
GAP IDENTIFIED
       ↓
RISK / IMPACT CONSIDERED
       ↓
REMEDIATION APPROVED
       ↓
CONFIGURATION CHANGED
       ↓
EFFECTIVE POLICY VERIFIED
       ↓
GROUP POLICY VERIFIED
       ↓
AD / DNS FUNCTIONAL TESTS
       ↓
CONTROL EVIDENCE RECORDED
```

Successful validation included:

```text
Effective Account Policy       PASS
Group Policy processing        PASS
Domain Controller advertising  PASS
Domain Controller services     PASS
DNS                             PASS
LDAP service discovery         PASS
Domain Controller discovery    PASS
```

---

## 15. Exceptions

No exceptions or risk acceptances were required for the applicable CIS Phase 01 controls.

The following controls were classified as not applicable rather than exceptions:

```text
1.1.6 — Member Server profile only
1.2.3 — Member Server profile only
```

An N/A classification indicates that the control is outside the scope of the selected CIS Level 1 Domain Controller profile.

It does not represent a failed control or accepted risk.

---

## 16. Recovery and Change Control

A Hyper-V checkpoint was created after successful remediation and validation.

Checkpoint:

```text
CIS-PH01-Account-Policy-Compliant
```

The checkpoint represents the following known-good state:

```text
AD DS operational
        +
DNS operational
        +
CIS Phase 01 remediation
        +
GPO validation
        +
DC health validation
```

This provides a lab recovery boundary before subsequent Domain Controller hardening.

The Hyper-V checkpoint is a lab recovery mechanism and is not considered a substitute for an Active Directory-aware backup strategy.

---

## 17. Lessons Learned

### 17.1 Live evidence takes precedence over documentation

The pre-CIS documentation stated that maximum password age was 90 days.

The live assessment demonstrated:

```text
MaxPasswordAge = 0
```

The assessment therefore used the observed technical state rather than assuming the documentation was correct.

---

### 17.2 Compliance requires applicability analysis

A control can be technically useful without being applicable to the selected benchmark profile.

CIS 1.1.6 was initially treated as a Domain Controller requirement but source verification established that it belongs to the Member Server profile.

The assessment record was corrected rather than claiming an unsupported PASS.

---

### 17.3 Security and availability can conflict

The `wstynder-admin` lockout demonstrated that account-lockout controls have an operational cost.

Reducing the threshold improves resistance to online password guessing but increases the possibility of:

- accidental lockout;
- administrative disruption; and
- deliberate account-lockout denial-of-service.

Controls therefore require risk-based implementation rather than assuming that a numerically stronger setting is always better.

---

### 17.4 Compliance does not prove functionality

A server can satisfy a configuration benchmark and still fail operationally.

For that reason, CyberTron validated:

```text
DC advertising
DC services
DNS
LDAP discovery
DC discovery
```

after remediation.

Technical compliance and functional validation are treated as separate requirements.

---

### 17.5 Benchmark implementation is change management

The exercise demonstrated that benchmark hardening is not simply:

```text
FAIL → change setting → PASS
```

The preferred process is:

```text
Requirement
    ↓
Evidence
    ↓
Gap
    ↓
Risk
    ↓
Decision
    ↓
Change
    ↓
Validation
    ↓
Evidence
```

This process provides a stronger basis for security engineering, audit, and GRC work.

---

## 18. Phase 01 Closure

CIS Phase 01 — Account Policy Assessment is complete.

Final result:

```text
Applicable CIS controls assessed : 9
Compliant                         : 9
Non-compliant                     : 0
Not applicable                    : 2
Exceptions                        : 0
```

All identified applicable findings were remediated.

Post-remediation Active Directory and DNS functionality was successfully validated.

The environment has been checkpointed at:

```text
CIS-PH01-Account-Policy-Compliant
```

---

## 19. Next Phase

The next stage will proceed into the Domain Controller-specific CIS controls in controlled groups rather than attempting to apply the entire benchmark simultaneously.

The same assessment method will continue to be used:

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

---

## References

- Center for Internet Security, *CIS Microsoft Windows Server 2022 Benchmark*, Version 5.1.0.
- Microsoft Active Directory Domain Services.
- CyberTron Active Directory Security Baseline — Pre-CIS Checkpoint.
- CyberTron laboratory validation evidence.

> **Repository note:** The CIS Benchmark itself is not redistributed through this repository. The repository records CyberTron's assessment, implementation decisions, configuration evidence, and validation results.