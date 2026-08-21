# 05 — Reliability Engineering Baseline

> Reliability is the ability of a system to continue delivering its intended behavior under expected and unexpected conditions.

---

**Status:** Engineering Baseline

**Version:** 1.0

**Applies To:** All production systems

---

# Purpose

The Reliability Engineering Baseline defines the minimum expectations for designing, operating, and evolving systems that can withstand failures without producing unacceptable business impact.

Reliability is not the absence of failure.

Production systems will experience:

- component failures,
- network failures,
- dependency failures,
- capacity exhaustion,
- software defects,
- configuration errors,
- deployment failures,
- data corruption,
- human mistakes,
- regional or infrastructure failures.

The engineering objective is therefore not:

> "Prevent every failure."

It is:

> **Understand how the system fails, limit the impact, detect meaningful failures, recover safely, and continuously reduce systemic risk.**

---

# Core Principle

> **Every production system should have an explicit reliability contract appropriate to its business criticality.**

The required level of reliability depends on:

- business impact,
- system tier,
- availability requirements,
- recovery requirements,
- data criticality,
- dependency characteristics,
- regulatory requirements,
- cost of failure.

A small internal application and a payment platform should not have identical reliability requirements.

They should, however, follow the same engineering reasoning.

---

# Reliability Is a System Property

Reliability cannot be delegated entirely to infrastructure.

Consider:

```text
                 ┌─────────────────┐
                 │   Application   │
                 └────────┬────────┘
                          │
             ┌────────────┼────────────┐
             ▼            ▼            ▼
        Database       Network      Dependency
             │            │            │
             └────────────┼────────────┘
                          ▼
                       Users
```

A system may have reliable individual components and still be unreliable as a whole.

Reliability must therefore be evaluated across:

- application behavior,
- infrastructure,
- data,
- dependencies,
- communication paths,
- deployment mechanisms,
- operational procedures.

---

# Reliability and Business Outcomes

Technical availability is not always equivalent to business reliability.

For example:

```text
HTTP 200
```

does not necessarily mean:

```text
Business transaction succeeded correctly
```

Likewise:

```text
Service unavailable
```

may be preferable to:

```text
Service returned incorrect financial state
```

Reliability engineering must therefore consider **correctness as well as availability**.

---

# Reliability Contract

Every production system should establish an appropriate reliability contract.

Depending on the system, this may include:

- availability,
- latency,
- throughput,
- durability,
- recovery time,
- recovery point,
- correctness,
- acceptable degradation,
- dependency behavior.

The contract should be measurable where practical.

---

# Failure Is Expected

Reliability engineering begins by accepting that failure will occur.

The project should identify meaningful failure scenarios such as:

- process crash,
- host failure,
- storage failure,
- network partition,
- dependency outage,
- database failure,
- capacity exhaustion,
- deployment failure,
- configuration error,
- data corruption,
- regional outage.

Not every theoretical failure requires mitigation.

The project should prioritize failures according to business impact.

---

# Failure Domains

Systems should understand the boundaries within which failures can occur.

Examples include:

- process,
- host,
- availability zone,
- region,
- account,
- network segment,
- dependency,
- operational team.

The architecture should avoid unintentionally placing critical components inside the same failure domain when independence is required.

---

# Failure Containment

A failure should not automatically become a system-wide failure.

The architecture should consider:

- isolation,
- bulkheads,
- bounded resources,
- timeouts,
- admission control,
- load shedding,
- dependency isolation.

The objective is to prevent:

```text
Local Failure
      │
      ▼
Failure Propagation
      │
      ▼
System-Wide Failure
```

where such propagation is avoidable.

---

# Dependency Reliability

Every external dependency introduces additional failure modes.

Dependencies may include:

- databases,
- APIs,
- message brokers,
- identity providers,
- payment providers,
- cloud services,
- internal services.

For important dependencies, the project should understand:

- expected availability,
- latency behavior,
- failure modes,
- timeout behavior,
- retry behavior,
- fallback behavior,
- ownership.

---

# Timeouts

Distributed calls should not wait indefinitely.

A missing timeout can cause:

```text
Dependency Slow
      │
      ▼
Request Waits
      │
      ▼
Worker Occupied
      │
      ▼
More Requests Queue
      │
      ▼
Capacity Exhaustion
```

Timeouts should therefore be deliberate.

They should reflect the actual business and technical interaction.

---

# Retries

Retries can improve resilience against transient failures.

They can also amplify failures.

For example:

```text
Dependency Fails
       │
       ▼
100 Requests Retry
       │
       ▼
200 Requests
       │
       ▼
400 Requests
```

Retries should therefore consider:

- whether the operation is safe to retry,
- maximum attempts,
- backoff,
- jitter,
- timeout budgets,
- dependency capacity.

Retries are not automatically a reliability improvement.

---

# Idempotency

Retryable operations should have explicit idempotency semantics where duplicate execution could cause harm.

This is particularly important for:

- financial operations,
- order creation,
- message processing,
- external APIs,
- asynchronous workflows.

Reliability and data integrity are therefore closely connected.

---

# Graceful Degradation

When the full system cannot operate, it may still be possible to provide reduced functionality.

Examples:

```text
Primary Recommendation Service
          │
          ▼
Unavailable
          │
          ▼
Fallback Recommendation
```

or:

```text
Real-Time Analytics
        │
        ▼
Unavailable
        │
        ▼
Last Known Data
```

Degradation should be intentional and should not violate important business invariants.

---

# Fail-Safe Behavior

For some operations, returning no result is safer than returning incorrect information.

The project should determine where failure should result in:

- rejection,
- fallback,
- stale data,
- partial response,
- read-only mode,
- complete unavailability.

The correct behavior depends on the business capability.

---

# Capacity and Reliability

Capacity problems are reliability problems.

A system may fail because:

- CPU is exhausted,
- memory is exhausted,
- connection pools are exhausted,
- database connections are exhausted,
- queues grow without bound,
- storage reaches capacity,
- API quotas are exceeded.

Capacity planning should therefore be part of reliability engineering.

---

# Resource Boundaries

Important resources should have appropriate bounds.

Examples include:

- request concurrency,
- queue depth,
- connection pools,
- memory,
- storage,
- thread pools,
- worker counts.

Unbounded resource consumption can transform a small problem into a cascading failure.

---

# Backpressure

When producers can generate work faster than consumers can process it, the system needs a strategy.

Possible strategies include:

- throttling,
- queueing,
- rejection,
- load shedding,
- rate limiting.

The project should understand what happens when demand exceeds processing capacity.

---

# Load Shedding

When the system cannot safely process all incoming work, rejecting some work may protect the system as a whole.

The decision should be based on business priority.

For example:

```text
Critical Operations
        │
        ▼
Preserve

Non-Critical Operations
        │
        ▼
Defer / Reject
```

Load shedding should be explicit rather than emerging accidentally from resource exhaustion.

---

# State Recovery

A system may recover technically while remaining logically inconsistent.

Recovery should therefore consider:

- state reconstruction,
- replay,
- reconciliation,
- duplicate processing,
- partial operations,
- stale caches.

This connects directly with the `04-data` domain.

---

# Recovery Objectives

Reliability requirements should establish appropriate:

### RTO

How quickly the system must recover.

### RPO

How much data loss is acceptable.

These should be derived from business impact rather than selected arbitrarily.

Detailed data recovery requirements are defined in:

`04-data/data-reliability-and-recovery.md`

---

# Observability

A reliable system must make meaningful failures detectable.

Reliability depends on the ability to determine:

- what failed,
- where it failed,
- how much is affected,
- whether the system is recovering,
- whether users are impacted.

The detailed observability baseline is defined in:

`08-observability/`

---

# Incident Response

Reliability includes the ability to respond when preventive controls fail.

Projects should define appropriate:

- incident ownership,
- escalation,
- communication,
- mitigation,
- recovery,
- post-incident analysis.

The required rigor depends on the system tier.

---

# Change Reliability

Deployments and configuration changes are common sources of production failure.

Reliability therefore includes:

- safe deployment,
- rollback,
- progressive delivery where appropriate,
- configuration management,
- migration safety,
- change validation.

Detailed delivery practices are defined in:

`07-delivery/`

---

# Testing for Reliability

Testing should include failure behavior where appropriate.

Potential techniques include:

- unit testing,
- integration testing,
- failure injection,
- load testing,
- resilience testing,
- recovery testing,
- disaster recovery exercises.

The objective is not to test every theoretical failure.

It is to reduce uncertainty around important failure modes.

---

# Reliability and Developer Velocity

Reliability should not become a reason to make every change painfully slow.

A mature engineering organization aims for:

> **Fast, safe change.**

Reliability should therefore be evaluated alongside delivery performance.

Relevant engineering signals may include:

- deployment frequency,
- lead time for changes,
- change failure rate,
- recovery time.

The detailed delivery baseline defines how these measurements are applied.

---

# Reliability and Cost

Reliability has a cost.

Higher guarantees may require:

- redundancy,
- additional infrastructure,
- replication,
- standby capacity,
- specialized operational processes,
- testing,
- automation.

The correct question is not:

> "How do we make this infinitely reliable?"

It is:

> **"What level of reliability does the business require, and what engineering investment is justified?"**

---

# Reliability Tiers

Reliability requirements should be aligned with system tier.

A conceptual model:

| Tier | Typical Characteristics | Reliability Approach |
|---|---|---|
| Tier 1 | Business-critical / customer-critical | Strong availability, recovery, failure isolation |
| Tier 2 | Important production capability | Defined availability and recovery objectives |
| Tier 3 | Internal / moderate impact | Proportionate resilience and recovery |
| Tier 4 | Low-risk / experimental | Minimal production guarantees |

The organization's governance model defines the authoritative tiering criteria.

---

# Reliability Review Questions

Before production, teams should be able to answer:

### Failure

- What are the most important failure modes?
- What happens when each critical dependency fails?

### Containment

- Can one component failure cascade into others?
- Are resources appropriately bounded?

### Recovery

- How does the system recover?
- What state must be reconstructed?

### Dependencies

- Which dependencies are critical?
- What happens when they become slow or unavailable?

### Capacity

- What happens under expected peak load?
- What happens beyond capacity?

### Operations

- How will operators detect and mitigate failure?

### Change

- How can a bad deployment be reversed?

### Business Impact

- What is the maximum acceptable impact?

---

# Minimum Engineering Requirements

Every production project should:

- [ ] Identify meaningful failure modes.
- [ ] Identify critical dependencies.
- [ ] Define appropriate timeout behavior.
- [ ] Define retry behavior where applicable.
- [ ] Consider idempotency for retryable operations.
- [ ] Establish appropriate resource limits.
- [ ] Define behavior under capacity pressure.
- [ ] Define recovery expectations.
- [ ] Make important failures observable.
- [ ] Establish an operational owner.

Higher-tier systems may additionally require:

- [ ] Failure-domain isolation.
- [ ] Graceful degradation.
- [ ] Load shedding.
- [ ] Advanced disaster recovery.
- [ ] Failure-injection testing.
- [ ] Regular recovery exercises.
- [ ] Formal reliability objectives and error budgets.

---

# Relationship With Other Domains

Reliability is intentionally cross-cutting.

```text
01 Governance
      │
      ▼
Defines applicability and risk

03 Architecture
      │
      ▼
Defines boundaries and failure relationships

04 Data
      │
      ▼
Defines data durability and recovery

05 Reliability
      │
      ▼
Defines system resilience and failure behavior

06 Security
      │
      ▼
Defines protection against security failures

07 Delivery
      │
      ▼
Defines safe change

08 Observability
      │
      ▼
Makes failure detectable

11 Operational Readiness
      │
      ▼
Makes recovery executable
```

No single domain creates reliability by itself.

---

# Final Principle

> **Reliable systems are not systems that never fail. They are systems whose failures are understood, contained, observable, recoverable, and proportionate to the consequences the business is willing to accept.**
