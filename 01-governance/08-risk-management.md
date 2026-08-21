# Engineering Risk Management

> Engineering Risk Management provides a structured approach to identifying, assessing, prioritizing, mitigating, accepting, monitoring, and continuously reviewing engineering risks throughout the software lifecycle.

---

**Status:** Organizational Standard

**Version:** 1.0

**Classification:** Governance Standard

**Applies To:** All software systems

---

# Purpose

Every engineering decision introduces uncertainty.

Some uncertainty affects cost.

Some affects schedule.

Some affects maintainability.

Some affects production reliability.

Some affects customer trust.

Engineering Risk Management ensures these risks are identified early, evaluated objectively, and managed throughout the system lifecycle.

Risk cannot be eliminated.

It can only be understood, reduced, transferred, accepted, or continuously monitored.

---

# Why This Standard Exists

Many production failures are not caused by unknown risks.

They are caused by **known risks that were never documented, discussed, or mitigated.**

Examples include:

- Single points of failure.
- Untested disaster recovery.
- Capacity assumptions.
- Security vulnerabilities.
- Vendor lock-in.
- Operational knowledge concentrated in one engineer.
- Technology adoption without organizational expertise.

Risk becomes dangerous when it remains invisible.

---

# Engineering Principle

> **Every engineering decision creates risk. Good engineering makes those risks explicit before they become production incidents.**

---

# Objectives

Engineering Risk Management aims to:

- Identify engineering risks early.
- Reduce operational surprises.
- Improve architectural decision quality.
- Prioritize engineering investment.
- Increase production confidence.
- Support informed business decisions.

---

# Engineering Risk Categories

Engineering risks typically fall into one or more categories.

## Architecture Risk

Examples:

- inappropriate architecture
- excessive complexity
- scalability assumptions
- distributed system complexity

---

## Reliability Risk

Examples:

- single points of failure
- insufficient redundancy
- poor recovery capability
- inadequate capacity

---

## Security Risk

Examples:

- weak authentication
- excessive privileges
- exposed secrets
- vulnerable dependencies

---

## Operational Risk

Examples:

- missing monitoring
- inadequate runbooks
- poor alerting
- insufficient ownership

---

## Data Risk

Examples:

- data loss
- backup failures
- corruption
- privacy violations
- retention failures

---

## Delivery Risk

Examples:

- unsafe deployments
- rollback failure
- manual release processes
- inadequate testing

---

## Platform Risk

Examples:

- vendor dependency
- unsupported technology
- platform limitations
- infrastructure maturity

---

## Knowledge Risk

Examples:

- undocumented systems
- key-person dependency
- missing ADRs
- inadequate onboarding

---

# Risk Assessment

Every identified engineering risk should be evaluated using a consistent approach.

Recommended assessment dimensions:

| Dimension | Questions |
|-----------|-----------|
| Probability | How likely is the risk? |
| Impact | What happens if it occurs? |
| Detectability | How quickly can it be detected? |
| Recoverability | How difficult is recovery? |
| Business Exposure | What is the business consequence? |

---

# Risk Levels

Organizations may define their own scoring model.

A simple qualitative model is often sufficient.

| Level | Description |
|--------|-------------|
| Critical | Immediate action required |
| High | Mitigation required before production |
| Medium | Planned mitigation |
| Low | Acceptable with monitoring |

The chosen methodology should remain consistent across projects.

---

# Risk Treatment Strategies

Every identified risk should have an explicit treatment strategy.

## Avoid

Change the design to eliminate the risk.

Example:

Replace a single-instance database with a managed high-availability service.

---

## Reduce

Implement controls that reduce probability or impact.

Example:

Introduce automated failover testing.

---

## Transfer

Transfer responsibility to another party.

Example:

Use a managed cloud service with defined service commitments.

Transferring responsibility does not eliminate accountability.

---

## Accept

Accept the remaining risk after informed evaluation.

Accepted risks should be:

- documented
- approved
- periodically reviewed

---

## Monitor

Some risks cannot currently be eliminated.

Instead:

- monitor indicators
- define review triggers
- reassess periodically

---

# Risk Register

Every significant project should maintain an Engineering Risk Register.

Each entry should include:

- Risk Identifier
- Description
- Category
- Probability
- Impact
- Overall Rating
- Mitigation Strategy
- Owner
- Review Date
- Current Status

The Risk Register should evolve throughout the project lifecycle.

---

# Relationship to Other Standards

Engineering Risk Management interacts with multiple governance standards.

```text
Architecture Review
        │
        ▼
Identify Risks
        │
        ▼
ADR
        │
        ▼
Document Decisions
        │
        ▼
Risk Register
        │
        ▼
Mitigation
        │
        ▼
Evidence
        │
        ▼
Production Readiness
```

Risk management is continuous—not a one-time exercise.

---

# Common Engineering Risks

Examples include:

- Architectural complexity exceeds team capability.
- Capacity assumptions remain unvalidated.
- Disaster recovery has never been tested.
- Platform dependencies become unsupported.
- Monitoring coverage is incomplete.
- Secrets management relies on manual processes.
- Security reviews occur after implementation.
- Operational ownership is unclear.

---

# Common Anti-Patterns

Avoid:

- Treating risk as purely a project management concern.
- Recording risks without owners.
- Never reviewing accepted risks.
- Hiding technical debt.
- Assuming cloud services eliminate engineering risk.
- Confusing "unlikely" with "impossible."

---

# Review Checklist

Before production, verify that:

- [ ] Significant engineering risks have been identified.
- [ ] Risk owners are assigned.
- [ ] Mitigation plans exist.
- [ ] Accepted risks are documented.
- [ ] Risk treatment decisions are approved.
- [ ] Review dates are scheduled.
- [ ] Risks are reflected in production readiness.

---

# Relationship to Other Standards

This standard supports:

- System Tiering
- Architecture Reviews
- Architecture Decision Records
- Production Readiness
- Evidence Framework
- Reliability Standards
- Security Standards

Risk Management provides the organizational context for prioritizing engineering effort.

---

# References

Engineering risk management should align with organizational governance policies and recognized risk management practices. The implementation approach should be appropriate to the organization's size, maturity, and regulatory environment.

---

# Revision History

| Version | Date | Summary |
|---------|------|---------|
| 1.0 | YYYY-MM-DD | Initial version |

---

# Final Principle

> **Engineering excellence is not achieved by eliminating every risk. It is achieved by understanding risks, making informed trade-offs, and ensuring that every accepted risk is visible, owned, and consciously managed.**
