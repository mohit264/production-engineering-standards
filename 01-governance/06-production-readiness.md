# Production Readiness

> Production Readiness is the engineering evaluation of whether a software system can be safely deployed, operated, supported, recovered, and evolved within its intended production environment.

---

**Status:** Organizational Standard

**Version:** 1.0

**Classification:** Governance Standard

**Applies To:** Production systems as determined by the System Tiering standard.

---

# Purpose

The purpose of a Production Readiness Review (PRR) is to determine whether a system is operationally prepared for production use.

A successful deployment does not imply a production-ready system.

Production readiness evaluates the complete operational capability of a system rather than its ability to execute application code.

---

# Why This Standard Exists

Many production incidents occur because engineering teams validate software functionality but overlook operational readiness.

Examples include:

- Applications without monitoring.
- Services without runbooks.
- Missing backup verification.
- Undefined recovery procedures.
- Unknown ownership.
- Missing alerting.
- Untested rollback procedures.
- Capacity assumptions never validated.

Production readiness reduces operational risk before production deployment.

---

# Engineering Principle

> **Production readiness is demonstrated through operational capability, not successful deployment.**

A production system must be capable of being:

- operated,
- monitored,
- supported,
- recovered,
- maintained,
- evolved,

throughout its lifecycle.

---

# Objectives

Production readiness aims to:

- Reduce production risk.
- Improve operational confidence.
- Validate engineering assumptions.
- Verify operational ownership.
- Ensure recovery capability.
- Improve long-term maintainability.

---

# When a Production Readiness Review Is Required

A review should be performed when:

- A new production system is introduced.
- A major architectural change occurs.
- A new customer-facing capability is released.
- A significant infrastructure change is introduced.
- The assigned system tier requires formal review.

The level of review should be proportional to the assigned system tier.

---

# Evaluation Principles

Production readiness evaluates engineering maturity across multiple dimensions.

No single dimension determines readiness.

Trade-offs should be documented and consciously accepted.

---

# Evaluation Areas

## Business Readiness

Questions include:

- Is the business objective clearly defined?
- Are stakeholders identified?
- Is operational ownership assigned?
- Are success criteria documented?

---

## Architecture Readiness

Questions include:

- Has the architecture been reviewed?
- Are ADRs complete?
- Are architectural assumptions documented?
- Have trade-offs been evaluated?

---

## Reliability Readiness

Questions include:

- Are SLOs defined?
- Has capacity planning been performed?
- Have failure scenarios been evaluated?
- Are recovery procedures documented?

---

## Security Readiness

Questions include:

- Has security review been completed?
- Are identities managed?
- Are secrets protected?
- Are compliance requirements satisfied?

---

## Data Readiness

Questions include:

- Are backups configured?
- Have restores been tested?
- Is data retention defined?
- Is data deletion supported where required?

---

## Operational Readiness

Questions include:

- Are dashboards available?
- Are alerts configured?
- Are runbooks documented?
- Is on-call ownership assigned?
- Are dependencies understood?

---

## Deployment Readiness

Questions include:

- Can deployments be safely performed?
- Can deployments be rolled back?
- Is deployment automated?
- Are releases traceable?

---

## Observability Readiness

Questions include:

- Are metrics available?
- Are logs structured?
- Are traces available where appropriate?
- Can failures be diagnosed?

---

## Support Readiness

Questions include:

- Are operational procedures documented?
- Is escalation defined?
- Are contact paths known?
- Are incident responsibilities assigned?

---

# Review Outcomes

A Production Readiness Review may result in one of the following outcomes.

## Approved

The system is considered operationally ready.

---

## Approved with Conditions

The system may proceed to production.

Documented follow-up actions are required.

---

## Deferred

Additional engineering work is required before production deployment.

---

## Rejected

The operational risk exceeds the organization's acceptable tolerance.

Deployment should not proceed.

---

# Required Evidence

Production readiness should be supported by evidence.

Examples include:

- Architecture review outcomes
- ADRs
- Load testing reports
- Security assessments
- Backup verification
- Restore testing
- Operational dashboards
- Alert validation
- Runbooks
- Deployment validation
- Capacity analysis
- Recovery testing

Evidence should be current and traceable.

---

# Responsibilities

## Engineering Team

Responsible for demonstrating production readiness.

---

## Reviewers

Responsible for evaluating readiness objectively.

---

## Platform Teams

Responsible for validating platform capabilities where applicable.

---

## Engineering Leadership

Responsible for accepting residual engineering risk.

---

# Common Anti-Patterns

Avoid:

- Treating production readiness as a deployment checklist.
- Performing reviews only days before release.
- Assuming cloud platforms provide operational maturity automatically.
- Approving systems without evidence.
- Ignoring operational ownership.
- Deferring documentation until after production.

---

# Production Readiness Checklist

## Business

- [ ] Business objectives documented.
- [ ] Ownership assigned.

---

## Architecture

- [ ] Architecture reviewed.
- [ ] ADRs completed.

---

## Reliability

- [ ] SLOs defined.
- [ ] Capacity evaluated.
- [ ] Recovery documented.

---

## Security

- [ ] Security review completed.
- [ ] Secrets managed.

---

## Data

- [ ] Backup verified.
- [ ] Restore tested.

---

## Operations

- [ ] Monitoring configured.
- [ ] Alerting validated.
- [ ] Runbooks available.
- [ ] Escalation documented.

---

## Deployment

- [ ] Deployment validated.
- [ ] Rollback verified.

---

## Observability

- [ ] Metrics available.
- [ ] Logging configured.
- [ ] Tracing implemented where appropriate.

---

# Relationship to Other Standards

Production Readiness builds upon:

- System Tiering
- Engineering Lifecycle
- Architecture Review
- Architecture Decision Records
- Reliability Standards
- Security Standards
- Observability Standards
- Operations Standards
- Evidence Framework

This standard integrates the outputs of multiple engineering disciplines into a single operational assessment.

---

# References

This standard aligns with established production engineering practices, operational readiness reviews, and software delivery governance. Organization-specific implementation should reference applicable regulatory requirements and operational policies.

---

# Revision History

| Version | Date | Summary |
|---------|------|---------|
| 1.0 | YYYY-MM-DD | Initial version |

---

# Final Principle

> **Production is not the destination of software development—it is the beginning of software operations. A system is production-ready only when it can be reliably operated, monitored, secured, recovered, and continuously improved throughout its lifecycle.**
