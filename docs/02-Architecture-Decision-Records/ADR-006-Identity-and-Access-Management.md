---
title: ADR-006 - Identity and Access Management
version: 0.1
status: Accepted
classification: Internal
author: Wayne Stynder
created: 2026-07-19
last_updated: 2026-07-19
---

# ADR-006 — Identity and Access Management

## Purpose

Define the enterprise identity strategy for CyberTron.

---

## Scope

This ADR covers:

- Active Directory
- Domain naming
- Authentication
- Administrative model

This ADR excludes Microsoft Entra ID and Intune, which will be introduced in later phases.

---

## Audience

- Windows Administrators
- Infrastructure Engineers
- Security Engineers

---

# Status

**Accepted**

---

# Context

CyberTron requires centralised identity management to support enterprise administration, Group Policy, PowerShell automation and future Microsoft 365 integration.

---

# Decision

Windows Server 2022 shall provide:

- Active Directory Domain Services
- DNS
- Group Policy

The domain shall become the authoritative identity provider for CyberTron-managed Windows systems.

---

# Decision Evolution

## Initial Design

Standalone Windows workstations.

---

## Revised Design

Windows Server introduced as a Hyper-V VM.

---

## Final Decision

Identity services shall be centralised within Active Directory.

Future cloud identity services will integrate with, rather than replace, the on-premises directory.

---

# Design Principles

- Single source of identity
- Least privilege
- Role-based administration
- PowerShell-first administration
- Central policy management

---

# Future Services

- Microsoft Entra ID
- Microsoft Intune
- Conditional Access
- Windows LAPS
- Certificate Services
- Windows Hello for Business

---

# Consequences

## Positive

- Enterprise administration experience
- Realistic identity management
- PowerShell automation
- Group Policy

## Negative

- Additional complexity
- Domain administration overhead

---

# Related ADRs

- ADR-001
- ADR-003
