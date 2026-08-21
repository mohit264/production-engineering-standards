# System Tiering

> System tiering classifies software systems according to their business criticality and operational risk, enabling engineering governance to apply an appropriate level of rigor without introducing unnecessary process.

---

**Status:** Foundational Standard

**Version:** 1.0

**Classification:** Organizational Standard

**Applies To:** All software systems

---

# Purpose

Not every software system carries the same level of business risk.

A public payment platform should not follow the same engineering process as an internal reporting tool.

Applying identical engineering controls to every project results in either:

- insufficient governance for critical systems, or
- excessive governance for low-risk systems.

System tiering ensures that engineering practices are proportional to business impact and operational risk.

---

# Engineering Principle

> **Engineering governance should be proportional to risk.**

Higher-risk systems require stronger engineering controls.

Lower-risk systems should minimize unnecessary governance while maintaining appropriate engineering quality.

---

# Objectives

System tiering enables organizations to:

- Apply engineering effort where it delivers the greatest value.
- Standardize governance expectations.
- Improve consistency across teams.
- Reduce unnecessary bureaucracy.
- Allocate engineering investment appropriately.
- Prioritize operational excellence for business-critical systems.

---

# Tier Classification

Every production system shall be assigned a single governance tier.

The assigned tier determines the minimum engineering expectations for that system.

---

# Tier 0 — Mission Critical

## Description

Failure causes immediate and severe business impact.

These systems are fundamental to business continuity.

---

## Examples

- Payment Processing
- Identity & Authentication
- Banking Core Systems
- Trading Platforms
- Emergency Services
- Healthcare Life-Critical Systems
- Critical Infrastructure Control Systems

---

## Characteristics

- Revenue critical
- Customer trust critical
- High availability required
- Strict recovery objectives
- Strong security requirements
- Formal change management
- Executive visibility

---

# Tier 1 — Business Critical

## Description

Failure significantly impacts customers or major business operations.

---

## Examples

- Customer-facing APIs
- SaaS Platforms
- E-Commerce
- Order Management
- Customer Portals
- Mobile Applications

---

## Characteristics

- Customer-facing
- Revenue impacting
- Moderate to high availability
- Strong operational maturity
- High engineering standards

---

# Tier 2 — Business Supporting

## Description

Supports internal business operations but does not directly affect customers.

---

## Examples

- CRM Systems
- HR Platforms
- Reporting Systems
- Internal Portals
- Workflow Automation
- Finance Applications

---

## Characteristics

- Internal users
- Limited customer impact
- Moderate availability requirements
- Simplified operational controls

---

# Tier 3 — Internal Productivity

## Description

Supports engineering or business productivity.

Operational failure causes inconvenience rather than significant business impact.

---

## Examples

- Internal dashboards
- Development tooling
- Knowledge portals
- Documentation platforms
- Team utilities

---

## Characteristics

- Internal use
- Low operational risk
- Lightweight governance
- Fast iteration

---

# Tier 4 — Experimental

## Description

Temporary or experimental systems created to validate ideas.

---

## Examples

- Proof of Concepts
- Research projects
- Hackathon submissions
- Technical experiments
- Innovation prototypes

---

## Characteristics

- Short-lived
- Minimal business dependency
- Limited governance
- Rapid experimentation

Experimental systems must not silently evolve into production systems without reassessment.

---

# Tier Assessment Criteria

System classification should consider multiple factors.

| Criterion | Questions |
|------------|-----------|
| Business Impact | What happens if the system fails? |
| Customer Impact | Are customers directly affected? |
| Financial Impact | Does failure affect revenue or contractual obligations? |
| Operational Impact | Does failure interrupt critical business processes? |
| Security Impact | Does the system process sensitive information? |
| Regulatory Impact | Are compliance obligations involved? |
| Recovery Expectations | How quickly must service be restored? |
| Availability Expectations | How much downtime is acceptable? |

Tier assignment should consider the overall risk profile rather than a single factor.

---

# Governance Requirements by Tier

| Engineering Practice | Tier 0 | Tier 1 | Tier 2 | Tier 3 | Tier 4 |
|----------------------|:------:|:------:|:------:|:------:|:------:|
| Architecture Review | ✓ | ✓ | Risk Based | Optional | No |
| ADR Required | ✓ | ✓ | Recommended | Optional | No |
| Production Readiness Review | ✓ | ✓ | ✓ | Optional | No |
| Threat Modeling | ✓ | ✓ | Risk Based | Optional | No |
| Disaster Recovery Plan | ✓ | ✓ | Risk Based | No | No |
| Restore Testing | ✓ | ✓ | Recommended | Optional | No |
| Load Testing | ✓ | ✓ | Risk Based | Optional | No |
| SLO Definition | ✓ | ✓ | Recommended | Optional | No |
| Operational Runbooks | ✓ | ✓ | Recommended | Optional | No |
| On-call Support | ✓ | Risk Based | Optional | No | No |
| Post-Incident Review | ✓ | ✓ | Risk Based | Optional | No |

---

# Tier Assignment Process

Every new project should be assigned a governance tier during project initiation.

The initial tier should be reviewed whenever:

- business requirements change,
- customer exposure changes,
- regulatory obligations change,
- architecture changes significantly,
- operational risk changes.

Tier assignment is not permanent.

---

# Escalation Rules

Projects must be re-evaluated when:

- Internal tools become customer-facing.
- Experimental systems enter production.
- Customer adoption grows significantly.
- Regulatory requirements change.
- Business criticality increases.
- Significant architectural changes occur.

Engineering governance must evolve with business reality.

---

# Common Anti-Patterns

Avoid:

### Applying identical governance to every project

Results in unnecessary process for low-risk systems.

---

### Ignoring system criticality

Results in insufficient engineering rigor for critical systems.

---

### Never revisiting classifications

Business importance changes over time.

Tier assignments should evolve accordingly.

---

# Decision Checklist

Before assigning a tier, confirm:

- [ ] Business impact understood.
- [ ] Customer impact evaluated.
- [ ] Financial impact assessed.
- [ ] Security implications identified.
- [ ] Regulatory obligations reviewed.
- [ ] Recovery expectations documented.
- [ ] Availability expectations defined.
- [ ] Operational ownership identified.

---

# Relationship to Other Standards

System tiering influences the application of all engineering standards within this repository.

Subsequent governance documents—including Architecture Reviews, Production Readiness, Risk Management, Exception Management, and Engineering Maturity—use the assigned system tier to determine the minimum required level of engineering rigor.

---

# Final Principle

> **Every software system deserves good engineering. Not every software system requires the same engineering investment. Effective governance aligns engineering rigor with business risk.**
