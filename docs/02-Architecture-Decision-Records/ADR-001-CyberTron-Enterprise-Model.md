---
title: ADR-001 - CyberTron Enterprise Model
version: 0.1
status: Accepted
classification: Private
author: Wayne Stynder
created: 2026-07-19
last_updated: 2026-07-19
related_documents:
  - ../01-Architecture/CyberTron-Enterprise-Infrastructure-v0.1.md
---

# ADR-001 — Adopt the CyberTron Enterprise Model

## Status

**Accepted**

---

# Context

The primary objective of this project is to develop practical enterprise IT infrastructure, cybersecurity, systems administration and documentation skills using repurposed hardware within a realistic Small-to-Medium Business (SMB) environment.

Rather than constructing an isolated "homelab" consisting of unrelated virtual machines and experiments, the project will model a fictional but realistic cybersecurity consultancy known as **CyberTron**.

CyberTron represents a one-person cybersecurity startup providing professional services to SMB clients.

This enterprise model provides realistic business requirements against which infrastructure, operational procedures, documentation, automation, security controls and governance can be designed.

---

# Decision

The project shall be designed, implemented and operated as the complete IT environment for **CyberTron**, rather than as a generic homelab.

All future technical decisions shall support this enterprise model.

Infrastructure, documentation and operational processes shall be developed as though CyberTron were an operational business expected to grow from a single consultant to a small professional services organisation.

---

# Business Profile

| Item | Value |
|------|-------|
| Company | CyberTron |
| Industry | Cybersecurity Consulting |
| Current Staff | 1 |
| Target Growth | 5–30 Employees |
| Primary Clients | Small & Medium Businesses |

---

# Business Services

CyberTron will provide services including:

- Security Assessments
- Security Awareness Training
- Network Hardening
- Vulnerability Management
- Security Documentation
- Cyber Insurance Readiness
- Microsoft 365 Administration
- Incident Response
- Governance, Risk and Compliance (future)

---

# Rationale

Designing the infrastructure around a realistic business provides several advantages.

## 1. Context-Driven Learning

Every technology introduced into the environment must satisfy an identified business requirement rather than being installed solely for experimentation.

## 2. Enterprise Thinking

The project encourages infrastructure planning before implementation.

Design decisions become architectural decisions rather than isolated technical exercises.

## 3. Portfolio Quality

Documentation demonstrates engineering judgement, operational planning and design methodology rather than simply showing completed technical tasks.

## 4. Professional Documentation

The repository becomes the internal engineering documentation for CyberTron rather than a collection of personal notes.

## 5. Future Growth

The architecture is intentionally scalable.

Infrastructure decisions should remain appropriate as the business expands beyond a single employee.

---

# Consequences

## Positive

- Consistent architectural direction.
- Realistic enterprise design decisions.
- Strong portfolio demonstrating systems thinking.
- Natural integration of Windows, Linux, networking and cybersecurity.
- Supports future learning in Microsoft 365, Azure, GRC and enterprise operations.

## Negative

- Additional planning and documentation effort before implementation.
- Greater emphasis on documentation discipline.
- Infrastructure changes require architecture updates rather than ad-hoc modifications.

These trade-offs are considered acceptable because documentation and planning are core learning objectives of the project.

---

# Architectural Principles

Future decisions shall align with the following principles:

1. Documentation First
2. Security by Design
3. Simplicity
4. Enterprise Alignment
5. Budget Conscious
6. Reproducibility
7. Continuous Improvement

---

# Repository Philosophy

The GitHub repository is the authoritative source for:

- Architecture
- Architecture Decision Records
- Build Logs
- Runbooks
- Asset Inventory
- Lessons Learned
- Automation Scripts
- Security Documentation

Every significant technical change shall be reflected in the repository.

---

# Success Criteria

CyberTron Enterprise will be considered successful when it demonstrates:

- Enterprise infrastructure design
- Windows administration
- Linux administration
- Network engineering
- Virtualisation
- Security monitoring
- Automation
- Professional documentation
- Repeatable operational procedures

while remaining maintainable by a single engineer.

---

# Alternatives Considered

## Generic Homelab

Rejected because it lacks business context and encourages disconnected experimentation.

## Certification-Focused Lab

Rejected because it optimises for completing course objectives rather than building an integrated enterprise environment.

## Pure Cybersecurity Lab

Rejected because modern cybersecurity professionals require broad understanding of enterprise infrastructure, identity, networking and systems administration.

---

# Related ADRs

- ADR-002 — Linux Infrastructure Platform
- ADR-003 — Windows Enterprise Platform
- ADR-004 — Storage Architecture
- ADR-005 — Network Segmentation Strategy

---

> **Design Principle**

> *Every technical component deployed within CyberTron must solve a business requirement, support a learning objective, or improve the operational capability of the enterprise. Technologies are not introduced solely because they are popular or interesting.*
