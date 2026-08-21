# Architecture Review

> Architecture reviews provide an objective evaluation of significant engineering decisions before they become expensive to change.

---

**Status:** Organizational Standard

**Version:** 1.0

**Classification:** Governance Standard

**Applies To:** Systems requiring architecture review as defined by the System Tiering standard.

---

# Purpose

The purpose of an architecture review is to evaluate whether a proposed solution is appropriate for the stated business requirements, engineering constraints, and operational expectations.

Architecture reviews are intended to improve engineering quality through collaborative technical evaluation.

They are not approval ceremonies.

They are not technology selection committees.

They are not gatekeeping mechanisms.

Their objective is to improve engineering decisions.

---

# Why This Standard Exists

Poor architectural decisions often become expensive organizational liabilities because they remain undiscovered until implementation or production.

Common examples include:

- Selecting technology before understanding requirements.
- Designing for hypothetical scale rather than actual demand.
- Ignoring operational complexity.
- Overlooking failure scenarios.
- Choosing architectures that exceed team capability.
- Missing security or compliance requirements.

Architecture reviews reduce these risks by introducing structured technical evaluation before significant investment occurs.

---

# Engineering Principle

> **Architectures should be evaluated against business requirements, engineering principles, and operational realities—not personal preferences or technology trends.**

---

# Objectives

Architecture reviews aim to:

- Improve engineering decision quality.
- Reduce technical and operational risk.
- Validate architectural assumptions.
- Encourage engineering discussion.
- Share organizational knowledge.
- Prevent unnecessary complexity.
- Increase confidence before implementation.

---

# When an Architecture Review is Required

The requirement for an architecture review is determined by the project's assigned system tier.

Examples include:

- New Tier 0 and Tier 1 systems.
- Significant architectural changes.
- Introduction of new platforms or technologies.
- Changes affecting availability, security, or scalability.
- Major data architecture changes.
- Significant integration changes.

Minor implementation changes should not require formal architecture reviews.

---

# Review Principles

Every architecture review should be:

## Collaborative

Architecture reviews exist to improve designs—not evaluate individuals.

---

## Business Focused

Every technical discussion should remain connected to business objectives.

---

## Evidence Based

Recommendations should be supported by engineering reasoning, operational experience, or measurable evidence.

---

## Constructive

The goal is to strengthen proposals through discussion.

---

## Technology Neutral

Reviewers should evaluate whether the selected technology satisfies requirements rather than promoting preferred technologies.

---

# Review Inputs

The review should include sufficient information to evaluate the proposed solution.

Typical inputs include:

- Business objectives
- Functional requirements
- Non-functional requirements
- Quality attributes
- System context
- Architecture diagrams
- Data flows
- Integration points
- Security considerations
- Operational expectations
- ADRs
- Risk assessment

The exact artifacts may vary depending on project complexity.

---

# Evaluation Framework

Architecture reviews should evaluate the proposal across multiple dimensions.

| Dimension | Evaluation Focus |
|------------|------------------|
| Business Alignment | Does the architecture satisfy the business need? |
| Simplicity | Is unnecessary complexity avoided? |
| Reliability | How does the system behave under failure? |
| Security | Are security principles incorporated? |
| Data | Is data ownership and lifecycle understood? |
| Scalability | Can the system support expected growth? |
| Operability | Can the system be monitored, deployed, and supported? |
| Maintainability | Can the system evolve safely? |
| Developer Experience | Does the architecture support efficient delivery? |
| Cost | Are operational and infrastructure costs understood? |

No single dimension should dominate every review.

Trade-offs should be explicitly documented.

---

# Review Outcomes

A review may conclude with one of the following outcomes.

## Approved

The proposed architecture satisfies engineering expectations.

---

## Approved with Recommendations

Implementation may proceed.

Recommendations should be considered during implementation.

---

## Revisions Required

The proposal requires changes before implementation proceeds.

---

## Deferred

Additional information or investigation is required before evaluation can continue.

---

# Responsibilities

## Architecture Author

Responsible for:

- preparing the proposal
- explaining design decisions
- documenting assumptions
- responding to feedback

---

## Reviewers

Responsible for:

- objective evaluation
- constructive discussion
- identifying risks
- documenting recommendations

---

## Engineering Leadership

Responsible for ensuring the review process remains effective, consistent, and lightweight.

---

# Review Checklist

Every review should answer the following questions.

### Business

- [ ] Is the business objective clearly understood?
- [ ] Are success criteria defined?

### Architecture

- [ ] Are major architectural decisions documented?
- [ ] Have alternatives been considered?

### Reliability

- [ ] Have failure scenarios been evaluated?
- [ ] Are recovery expectations understood?

### Security

- [ ] Have security implications been considered?

### Operations

- [ ] Can the system be monitored and supported?

### Delivery

- [ ] Can the architecture evolve safely?

### Economics

- [ ] Are cost implications understood?

---

# Common Anti-Patterns

Avoid architecture reviews that become:

- technology debates
- personal preference discussions
- approval bottlenecks
- design-by-committee
- checklist-only exercises
- meetings without documented outcomes

The quality of the discussion is more valuable than the duration of the meeting.

---

# Relationship to Other Standards

This standard defines the governance process for evaluating architectures.

It complements:

- Engineering Principles
- Decision Framework
- System Tiering
- Architecture Decision Records
- Risk Management
- Production Readiness

It does not prescribe architectural patterns or implementation technologies.

---

# References

Architecture reviews should consider applicable organizational standards together with relevant industry guidance and project-specific requirements.

Technology-specific review criteria belong within the corresponding engineering domains.

---

# Revision History

| Version | Date | Summary |
|----------|------|---------|
| 1.0 | YYYY-MM-DD | Initial version |

---

# Final Principle

> **The purpose of an architecture review is not to prove an architecture is perfect. It is to increase confidence that the chosen solution appropriately balances business value, engineering quality, operational excellence, security, and long-term maintainability.**
