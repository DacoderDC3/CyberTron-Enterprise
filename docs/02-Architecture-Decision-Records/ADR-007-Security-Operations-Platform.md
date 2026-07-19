---
title: ADR-007 - Security Operations Platform
version: 0.1
status: Accepted
classification: Internal
author: Wayne Stynder
created: 2026-07-19
last_updated: 2026-07-19
---

# ADR-007 — Security Operations Platform

## Purpose

Define the strategic approach to monitoring, detection and security operations within CyberTron Enterprise.

---

## Scope

This ADR covers:

- Security monitoring
- Logging
- Detection
- Incident response
- Security tooling

This ADR does not define individual detection rules or playbooks.

---

## Audience

- SOC Analysts
- Security Engineers
- Infrastructure Engineers

---

# Status

**Accepted**

---

# Context

CyberTron Enterprise is intended to provide practical experience across Blue Team operations, Windows administration and enterprise security.

The platform must support detection, investigation and response while remaining achievable on repurposed hardware.

---

# Decision

CyberTron will implement a layered security architecture.

| Layer | Platform |
|---------|----------|
| Endpoint | Microsoft Defender |
| Host Monitoring | Wazuh |
| Network | pfSense |
| Future Network Monitoring | Security Onion |
| Future Log Analytics | Splunk Evaluation |

Security capabilities will be introduced progressively as the enterprise matures.

---

# Decision Evolution

## Initial Design

Single Wazuh deployment.

---

## Revised Design

Considered replacing Wazuh.

---

## Final Decision

Retain Wazuh as the primary endpoint monitoring platform while evaluating complementary network monitoring platforms in future phases.

Each platform will have a defined role rather than overlapping functionality.

---

# Security Principles

- Defence in Depth
- Least Privilege
- Monitoring by Default
- Secure Configuration
- Continuous Improvement

---

# Security Objectives

Develop practical experience in:

- Detection Engineering
- Incident Response
- Threat Hunting
- Log Analysis
- Windows Security
- Linux Security
- Enterprise Monitoring

---

# Future Capabilities

- Security Onion
- Splunk
- Microsoft Sentinel
- SOAR concepts
- Threat Intelligence
- Sigma Rules
- MITRE ATT&CK mapping

---

# Consequences

## Positive

- Layered security
- Enterprise SOC experience
- Clear technology responsibilities
- Progressive capability growth

## Negative

- Multiple monitoring platforms
- Additional administration effort
- Higher infrastructure requirements over time

---

# Related ADRs

- ADR-001
- ADR-002
- ADR-003
- ADR-005
- ADR-006
