# Availability and Service Levels

> Reliability requirements should be expressed as measurable expectations rather than vague promises of "high availability."

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Reliability Engineering

**Applies To:** Production systems, with requirements determined by system tier and business criticality

---

# Purpose

A production system cannot be engineered effectively around statements such as:

- "The service should always be available."
- "The application must be highly reliable."
- "Downtime should be minimal."

These statements express intent but do not provide an engineering target.

This standard establishes a framework for defining measurable service expectations around:

- availability,
- latency,
- reliability,
- service levels,
- error budgets,
- business impact.

The objective is not to maximize every metric.

The objective is to establish **appropriate and measurable reliability expectations for the business capability**.

---

# Engineering Principle

> **A reliability target is useful only when it is measurable, meaningful to the business, and achievable through deliberate engineering.**

---

# 1. Availability

Availability describes whether a required service or capability is usable when it is expected to be usable.

A simplified representation is:

```text
Availability =
Successful Service Time
────────────────────────
Required Service Time
```

The exact measurement method should be defined according to the system.

---

# 2. Availability Is Not the Same as HTTP Success

A service returning:

```text
HTTP 200
```

does not automatically mean that the business operation succeeded.

For example:

```text
API Request
    │
    ▼
HTTP 200
    │
    ▼
Business Transaction Failed
```

Where appropriate, availability should be measured against the actual user or business capability.

---

# 3. Service-Level Indicators

A **Service-Level Indicator (SLI)** is a measurable representation of service behavior.

Examples include:

- successful request rate,
- request latency,
- availability,
- queue processing latency,
- data freshness,
- transaction success rate.

The SLI should represent something meaningful to the service consumer.

---

# 4. Good SLI Characteristics

A useful SLI should be:

- measurable,
- repeatable,
- understandable,
- relevant to users,
- resistant to misleading implementation details.

For example:

```text
Database CPU
```

may be useful for diagnosis.

But:

```text
Successful checkout rate
```

may be a more meaningful reliability indicator for an e-commerce checkout capability.

---

# 5. Service-Level Objectives

A **Service-Level Objective (SLO)** defines the desired level of service measured using one or more SLIs.

Example:

```text
99.9% of valid checkout requests
should complete successfully over
the defined measurement period.
```

The exact objective depends on the system tier and business requirement.

---

# 6. Service-Level Agreements

A **Service-Level Agreement (SLA)** is a formal commitment between parties.

It may include:

- availability commitments,
- response expectations,
- support obligations,
- service credits,
- contractual consequences.

An internal engineering SLO and an external contractual SLA are not necessarily the same thing.

---

# 7. SLI → SLO → SLA

These concepts should not be treated as interchangeable.

```text
SLI
│
├── What do we measure?
│
▼
SLO
│
├── What level do we aim to achieve?
│
▼
SLA
│
└── What formal commitment do we make?
```

A project may have SLOs without having a customer-facing SLA.

---

# 8. Choosing the Right SLI

The most important question is:

> What does the consumer actually care about?

For an API:

```text
Successful Requests
```

may be appropriate.

For an asynchronous pipeline:

```text
Percentage of messages processed
within the required latency
```

may be more meaningful.

For a data product:

```text
Data freshness
```

may be the important service characteristic.

The SLI should follow the business capability.

---

# 9. Availability Targets

Availability targets should be selected deliberately.

For example:

| Target | Approximate Monthly Unavailability |
|---|---:|
| 99% | ~7.3 hours |
| 99.9% | ~43.8 minutes |
| 99.95% | ~21.9 minutes |
| 99.99% | ~4.4 minutes |
| 99.999% | ~26 seconds |

These values are illustrative.

Actual service-level calculations should use the defined measurement window and exclusions.

---

# 10. Higher Availability Is Not Automatically Better

Increasing availability requirements can introduce substantial cost and complexity.

For example, moving from:

```text
99.9%
```

to:

```text
99.99%
```

requires a significantly smaller failure budget.

The architecture may need:

- redundancy,
- automation,
- stronger isolation,
- faster detection,
- faster recovery,
- additional infrastructure.

The target should therefore reflect business value.

---

# 11. Reliability Targets by Tier

Reliability expectations should be aligned with system criticality.

Conceptually:

| Tier | Reliability Approach |
|---|---|
| Tier 1 | Explicit SLOs and strong operational evidence |
| Tier 2 | Defined SLOs for important capabilities |
| Tier 3 | Proportionate service objectives |
| Tier 4 | Basic operational expectations |

The governance domain defines the authoritative system-tier model.

---

# 12. User Journeys

A system may expose many technical endpoints but only a few truly critical business journeys.

Examples:

```text
Browse Product
Add to Cart
Checkout
Payment
Order Confirmation
```

Reliability requirements should identify the journeys whose failure has meaningful business impact.

---

# 13. Criticality

Not every operation needs the same reliability target.

For example:

```text
Checkout
```

may require stronger reliability than:

```text
Product Recommendations
```

A system should therefore consider **capability-level criticality**, not only application-level criticality.

---

# 14. Latency Objectives

Reliability includes responsiveness where latency affects usability or business correctness.

Possible latency SLIs include:

- median latency,
- p95 latency,
- p99 latency,
- time-to-first-response,
- end-to-end transaction time.

The appropriate percentile depends on the workload.

---

# 15. Tail Latency

Average latency can hide serious user impact.

For example:

```text
Average = 100 ms
```

while:

```text
p99 = 5 seconds
```

A small percentage of users may therefore experience significantly worse behavior.

Where tail latency matters, SLOs should account for it.

---

# 16. End-to-End Latency

For distributed systems:

```text
Client
  │
  ▼
API
  │
  ▼
Service
  │
  ▼
Database
  │
  ▼
Dependency
```

The user experiences the combined latency.

Individual component targets should therefore not be considered independently of the overall user journey.

---

# 17. Error Budget

An error budget represents the amount of unreliability permitted by an SLO.

For example:

```text
SLO = 99.9%

Allowed unreliability = 0.1%
```

This creates an explicit engineering budget for failure.

---

# 18. Why Error Budgets Matter

Without an error budget, organizations often fall into one of two extremes:

```text
Move Fast
    │
    ▼
Reliability Suffers
```

or:

```text
Protect Reliability
    │
    ▼
Change Becomes Extremely Slow
```

An error budget provides a mechanism for balancing:

```text
Reliability
      +
Delivery Velocity
```

---

# 19. Error Budget Consumption

The project should be able to determine how much of the allowed unreliability has been consumed.

For example:

```text
Allowed Budget
████████████████████

Consumed
██████
```

The exact policy for responding to budget consumption depends on organizational governance.

---

# 20. Reliability and Change

A healthy engineering system should allow changes while remaining within its reliability objectives.

Where reliability deteriorates significantly, teams may need to prioritize:

- reliability improvements,
- incident remediation,
- capacity improvements,
- architecture changes.

The objective is not to prohibit change indefinitely.

---

# 21. Error Budgets and Delivery

Error budgets can provide a practical relationship between reliability and engineering velocity.

When reliability is healthy:

```text
Reliability Healthy
        │
        ▼
Normal Delivery
```

When reliability is repeatedly violated:

```text
Reliability Degraded
        │
        ▼
Investigate / Stabilize
        │
        ▼
Restore Reliability
```

This should be implemented proportionally to the system tier.

---

# 22. Measurement Windows

SLOs require a defined measurement period.

Possible windows include:

- rolling 7 days,
- rolling 30 days,
- calendar month,
- business-specific period.

The chosen window should support meaningful operational decisions.

---

# 23. Availability Exclusions

Some systems may legitimately exclude certain conditions from availability calculations.

Examples might include:

- planned maintenance,
- invalid client requests,
- customer-controlled infrastructure.

However, exclusions must be explicit.

Avoid creating exclusions simply to make the service appear more reliable.

---

# 24. Planned Maintenance

Planned maintenance should be considered separately from unexpected failure.

The project should establish:

- maintenance expectations,
- communication requirements,
- acceptable downtime,
- whether maintenance is included in SLO measurement.

The goal should still be to minimize unnecessary customer impact.

---

# 25. Dependency Availability

A service's effective reliability may depend on its dependencies.

For example:

```text
Application
    │
    ▼
Payment Provider
    │
    ▼
Banking Network
```

The application cannot necessarily guarantee availability beyond dependencies it does not control.

The project should therefore distinguish:

```text
Service Reliability
```

from:

```text
End-to-End Business Reliability
```

---

# 26. Dependency Budgets

Where a service depends heavily on external systems, reliability planning should consider the dependency's behavior.

Questions include:

- What availability does the dependency provide?
- What happens during dependency degradation?
- Can the application degrade gracefully?
- Is there a fallback?
- Can work be deferred?

---

# 27. Reliability vs Correctness

A system can be available but incorrect.

For example:

```text
Service Available
       │
       ▼
Incorrect Account Balance
```

This is a reliability failure even though the endpoint responds successfully.

Reliability objectives should therefore include correctness signals where incorrect behavior has significant business impact.

---

# 28. Reliability vs Durability

Availability asks:

> Can the service be used?

Durability asks:

> Will accepted state remain available after failure?

These are related but distinct.

Data durability requirements are defined primarily in:

`04-data/`

---

# 29. Reliability vs Resilience

Reliability is the broader outcome.

Resilience describes the ability to withstand and recover from adverse conditions.

A useful distinction is:

```text
Reliability
     │
     ├── Failure Management
     ├── Capacity
     ├── Resilience
     ├── Recovery
     └── Service Objectives
```

---

# 30. Reliability Measurement

Reliability measurements should be based on meaningful evidence.

Possible sources include:

- metrics,
- traces,
- logs,
- synthetic checks,
- business events,
- incident records.

The measurement system should itself be sufficiently trustworthy for the decisions being made.

---

# 31. Synthetic Monitoring

Synthetic checks can simulate important user journeys.

For example:

```text
Login
  │
  ▼
Browse
  │
  ▼
Checkout
  │
  ▼
Confirmation
```

Synthetic checks are useful for detecting problems that may not be visible through infrastructure metrics alone.

---

# 32. Business Metrics

Where appropriate, reliability should be connected to business outcomes.

Examples include:

- successful checkout rate,
- payment completion rate,
- order processing success,
- message processing success,
- data freshness.

Business metrics should complement, not replace, technical observability.

---

# 33. SLO Ownership

Every important SLO should have an owner.

The owner should understand:

- how the SLI is calculated,
- what the target means,
- how violations are detected,
- what actions follow violations.

An SLO without ownership is unlikely to influence engineering decisions.

---

# 34. SLO Review

SLOs should be reviewed when:

- business requirements change,
- system architecture changes,
- traffic changes significantly,
- customer expectations change,
- incident history reveals a mismatch.

An SLO should not become permanent simply because it was chosen years ago.

---

# 35. Avoid Metric Gaming

Teams should not optimize measurements while degrading the actual user experience.

For example:

```text
API Availability = 99.99%
```

while:

```text
Critical Business Transactions = Failing
```

SLIs should therefore be designed to reflect meaningful service outcomes.

---

# 36. Reliability Evidence

Where a system has formal reliability objectives, teams should be able to demonstrate:

- current performance,
- historical performance,
- SLO compliance,
- major violations,
- error-budget consumption,
- significant incidents.

This allows reliability discussions to be evidence-based.

---

# 37. Reliability Review Questions

Before production, the project should be able to answer:

### Service

- What service or business capability are we measuring?

### SLI

- What exactly is measured?

### SLO

- What level of performance is required?

### Business

- Why does this target matter?

### Failure

- What happens when the SLO is violated?

### Dependencies

- Which dependencies influence the target?

### Measurement

- Can the target actually be measured?

### Ownership

- Who owns the objective?

---

# 38. Minimum Engineering Requirements

Every production project should:

- [ ] Identify important customer or business capabilities.
- [ ] Define meaningful reliability indicators where appropriate.
- [ ] Establish proportionate service objectives for important capabilities.
- [ ] Define meaningful latency expectations where latency matters.
- [ ] Identify important dependency constraints.
- [ ] Establish ownership of important reliability objectives.
- [ ] Make objective performance observable.
- [ ] Review objectives after significant changes.

Higher-tier systems may additionally require:

- [ ] Formal SLOs.
- [ ] Error budgets.
- [ ] Automated SLO reporting.
- [ ] Business-level reliability indicators.
- [ ] Synthetic monitoring.
- [ ] Formal reliability review.
- [ ] Defined response to sustained error-budget exhaustion.

---

# Relationship With Other Standards

This standard works with:

- `01-governance/`
- `05-reliability/failure-management.md`
- `05-reliability/capacity-and-scalability.md`
- `05-reliability/resilience-testing.md`
- `05-reliability/recovery-and-continuity.md`
- `08-observability/`

The distinction is:

**Failure Management**

> Defines how the system behaves when failures occur.

**Capacity and Scalability**

> Defines how the system behaves as demand approaches resource limits.

**Resilience Testing**

> Produces evidence about failure behavior.

**Recovery and Continuity**

> Defines how the system returns to service.

**Availability and Service Levels**

> Defines how reliability is measured and what level of service is expected.

---

# Final Principle

> **If reliability cannot be measured, it cannot be meaningfully managed. A production system should know what "good enough" means before an incident forces the organization to decide.**
