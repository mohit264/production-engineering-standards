# Engineering Lifecycle

> Engineering governance applies throughout the entire lifecycle of a software system—from the initial business idea to the retirement of the last production instance.

---

**Status:** Foundational Standard

**Version:** 1.0

**Classification:** Organizational Standard

**Applies To:** All software systems

---

# Purpose

Software systems are not created in a single event.

They evolve through multiple phases, each introducing different engineering concerns, governance requirements, risks, and deliverables.

The purpose of this standard is to define a common engineering lifecycle that every project follows.

This lifecycle provides a consistent framework for planning, reviewing, building, operating, evolving, and eventually retiring software systems.

---

# Why This Standard Exists

Engineering problems frequently arise because governance is concentrated around deployment rather than applied across the entire lifecycle.

Common examples include:

- Architecture decisions made without understanding business requirements.
- Security introduced late in development.
- Production readiness reviewed only days before release.
- Operational documentation created after production incidents.
- Systems that remain in production long after their business value has ended.

Engineering governance should accompany the system throughout its lifecycle rather than becoming a gate near deployment.

---

# Engineering Principle

> **Engineering is a lifecycle, not a phase.**

Every engineering decision influences future decisions.

The cost of change generally increases as the system progresses through its lifecycle.

Earlier engineering decisions therefore deserve greater attention and stronger review.

---

# Lifecycle Overview

Every software system should progress through the following lifecycle.

```text
Business Need
      │
      ▼
Initiation
      │
      ▼
Discovery
      │
      ▼
Architecture
      │
      ▼
Implementation
      │
      ▼
Verification
      │
      ▼
Production Readiness
      │
      ▼
Production
      │
      ▼
Continuous Improvement
      │
      ▼
Retirement
```

Governance activities exist throughout every phase.

---

# Phase 1 — Business Need

## Objective

Identify why the system should exist.

Questions:

- What business problem are we solving?
- Who are the stakeholders?
- What value will this system deliver?
- What happens if this system is never built?

### Primary Deliverables

- Business Case
- High-Level Scope
- Stakeholder Identification

---

# Phase 2 — Project Initiation

## Objective

Establish project foundations.

Activities include:

- System Tier Assignment
- Initial Risk Assessment
- Ownership Assignment
- Governance Classification
- Success Criteria

### Primary Deliverables

- Project Charter
- Initial Tier Classification
- Project Owner
- Engineering Team

---

# Phase 3 — Discovery

## Objective

Understand requirements before selecting technology.

Activities include:

- Functional Requirements
- Non-Functional Requirements
- Security Requirements
- Operational Requirements
- Regulatory Requirements
- Data Requirements

### Primary Deliverables

- Requirements Specification
- Initial Risk Register
- Quality Attribute Prioritization

---

# Phase 4 — Architecture

## Objective

Design the solution.

Activities include:

- Architecture Reviews
- ADR Creation
- Technology Evaluation
- Failure Modeling
- Scalability Analysis
- Security Review

### Primary Deliverables

- Architecture Documentation
- Architecture Decision Records
- System Design
- Threat Model

---

# Phase 5 — Implementation

## Objective

Build the system.

Activities include:

- Development
- Code Reviews
- Automated Testing
- Static Analysis
- Security Scanning
- Infrastructure Development

### Primary Deliverables

- Source Code
- Automated Tests
- Infrastructure Definitions

---

# Phase 6 — Verification

## Objective

Validate engineering assumptions.

Activities include:

- Functional Testing
- Performance Testing
- Security Testing
- Reliability Testing
- Recovery Testing
- Operational Validation

### Primary Deliverables

- Test Results
- Performance Reports
- Security Reports
- Recovery Evidence

---

# Phase 7 — Production Readiness

## Objective

Evaluate whether the system is operationally ready.

Activities include:

- Production Readiness Review
- Operational Runbooks
- Monitoring Verification
- Alert Validation
- Backup Verification
- Deployment Validation

### Primary Deliverables

- Production Readiness Report
- Runbooks
- Dashboards
- Operational Checklist

---

# Phase 8 — Production

## Objective

Operate the system safely.

Activities include:

- Monitoring
- Incident Management
- Capacity Management
- Change Management
- Security Monitoring
- Operational Support

### Primary Deliverables

- Operational Metrics
- Incident Reports
- Capacity Reports

---

# Phase 9 — Continuous Improvement

## Objective

Improve the system through operational learning.

Activities include:

- Postmortems
- Architecture Reviews
- Technical Debt Management
- Standard Improvements
- Cost Optimization
- Performance Improvements

### Primary Deliverables

- Improvement Backlog
- Updated ADRs
- Revised Standards

---

# Phase 10 — Retirement

## Objective

Safely decommission the system.

Activities include:

- Consumer Migration
- Data Archival
- Data Deletion
- Infrastructure Decommissioning
- Secret Revocation
- Documentation Archival

### Primary Deliverables

- Retirement Plan
- Data Retention Confirmation
- Infrastructure Removal Evidence

A software system should have a planned retirement strategy before reaching end of life.

---

# Governance Across the Lifecycle

| Lifecycle Phase | Governance Activities |
|-----------------|-----------------------|
| Business Need | Business Alignment |
| Initiation | Tiering, Ownership |
| Discovery | Requirements Review |
| Architecture | Architecture Review, ADR |
| Implementation | Engineering Standards |
| Verification | Evidence Collection |
| Production Readiness | Operational Review |
| Production | Operations Governance |
| Continuous Improvement | Lessons Learned |
| Retirement | Decommissioning Review |

Governance evolves alongside the project.

---

# Common Anti-Patterns

Avoid:

- Beginning architecture before understanding requirements.
- Treating production readiness as a deployment checklist.
- Ignoring operational concerns until production.
- Never reviewing architectural decisions.
- Allowing retired systems to remain operational indefinitely.

---

# Review Checklist

Before progressing to the next lifecycle phase, verify that:

- [ ] Required deliverables are complete.
- [ ] Risks are understood.
- [ ] Required reviews have been performed.
- [ ] Evidence has been collected where applicable.
- [ ] Ownership is clearly defined.
- [ ] Governance obligations have been satisfied.

---

# Relationship to Other Standards

This lifecycle provides the context for all governance activities.

Subsequent standards—including Architecture Reviews, Production Readiness, Risk Management, ADRs, and Engineering Maturity—define the governance expectations associated with specific lifecycle phases.

---

# References

This standard aligns conceptually with industry guidance on software lifecycle management, systems engineering, and operational excellence. Technology-specific implementation guidance is intentionally excluded.

---

# Revision History

| Version | Date | Summary |
|----------|------|---------|
| 1.0 | YYYY-MM-DD | Initial version |

---

# Final Principle

> **Successful software systems are not defined solely by how they are built. They are defined by how they are conceived, governed, operated, evolved, and ultimately retired. Engineering governance should provide guidance throughout that entire journey.**
