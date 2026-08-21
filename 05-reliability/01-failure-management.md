# Failure Management

> Production engineering begins by assuming that things will fail and deciding what the system should do when they do.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Reliability Engineering

**Applies To:** Production systems, with depth determined by system tier and business criticality

---

# Purpose

Failures are inevitable in production systems.

A process can crash.

A database can become unavailable.

A network can partition.

A dependency can become slow.

A deployment can introduce a defect.

An operator can make a mistake.

A system that does not explicitly define its failure behavior will still have failure behavior. It will simply emerge accidentally from:

- timeouts,
- exhausted resources,
- retries,
- queues,
- connection pools,
- default library behavior,
- user behavior.

This standard establishes a baseline for deliberately designing how systems:

1. identify failures,
2. contain failures,
3. respond to failures,
4. recover from failures,
5. communicate failures,
6. learn from failures.

---

# Engineering Principle

> **Every important failure mode should have an intentional system behavior proportional to its business impact.**

Not every failure needs elaborate engineering.

The objective is not to eliminate every possible failure.

The objective is to ensure that important failures do not produce unacceptable or unexpected consequences.

---

# 1. Failure Classification

Projects should identify meaningful failure categories.

Typical categories include:

- process failure,
- host failure,
- storage failure,
- network failure,
- dependency failure,
- capacity failure,
- configuration failure,
- deployment failure,
- data failure,
- security failure,
- human error,
- regional or infrastructure failure.

The project should prioritize them according to business impact.

---

# 2. Failure Modes

For important components, determine:

| Question | Example |
|---|---|
| What can fail? | Payment provider |
| How does it fail? | Timeout |
| How will we detect it? | Request timeout |
| What happens next? | Retry or defer |
| Can the operation be retried? | Depends on idempotency |
| What does the user see? | Payment pending |
| How does the system recover? | Reconciliation |
| What is the business impact? | Delayed order |

The objective is to turn unknown failure behavior into explicit engineering decisions.

---

# 3. Failure Domains

A failure may affect a particular scope.

For example:

```text
Process
   │
   ▼
Host
   │
   ▼
Availability Zone
   │
   ▼
Region
```

The project should understand the failure domains of critical components.

Where independent failure is required, critical components should not unintentionally share the same failure domain.

---

# 4. Single Points of Failure

A component is a potential single point of failure when its failure can prevent the system from providing a required capability.

Examples may include:

- one database instance,
- one network path,
- one deployment mechanism,
- one external dependency,
- one operator-controlled credential,
- one region.

Not every single point of failure must be eliminated.

The project should determine whether the associated risk is acceptable.

---

# 5. Failure Containment

A failure should remain within the smallest practical boundary.

For example:

```text
Dependency Failure
        │
        ▼
Calling Component
        │
        X
        │
        ▼
Other Independent Components
```

The objective is to prevent:

```text
Local Failure
      │
      ▼
Resource Exhaustion
      │
      ▼
Cascading Failure
      │
      ▼
System-Wide Outage
```

---

# 6. Timeouts

Distributed operations should have deliberate timeout behavior.

Without a timeout:

```text
Dependency
    │
    ▼
No Response
    │
    ▼
Request Waits
    │
    ▼
Resource Remains Occupied
    │
    ▼
Capacity Decreases
```

Timeouts should be selected according to:

- expected operation duration,
- user experience,
- downstream behavior,
- resource constraints,
- retry strategy.

Avoid copying arbitrary timeout values across unrelated systems.

---

# 7. Timeout Budgets

A request may traverse multiple components.

For example:

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
```

The total latency budget must account for the entire operation.

A downstream component should not independently consume more time than the upstream request can tolerate.

Timeouts should therefore be considered as a system rather than isolated configuration values.

---

# 8. Retry Policy

Retries should be used only when they improve the expected outcome.

Before retrying, determine:

- Is the failure likely transient?
- Is the operation safe to repeat?
- How many attempts are appropriate?
- How much additional load will retries generate?
- What happens if the dependency remains unavailable?

---

# 9. Backoff

Immediate retries can overload an already failing dependency.

A retry strategy should generally consider increasing the interval between attempts.

Conceptually:

```text
Attempt 1
   │
   ▼
Wait
   │
   ▼
Attempt 2
   │
   ▼
Longer Wait
   │
   ▼
Attempt 3
```

The exact strategy depends on the workload.

---

# 10. Jitter

If many clients retry at the same time, synchronized retries can create another traffic spike.

For example:

```text
1000 Clients
     │
     ▼
Failure
     │
     ▼
All Retry Together
     │
     ▼
Dependency Overloaded
```

Jitter can spread retry attempts over time.

Where retry storms are plausible, jitter should be considered.

---

# 11. Retry Limits

Retries should be bounded.

Unlimited retries can convert a transient failure into a persistent resource-consumption problem.

The project should establish:

- maximum attempts,
- maximum retry duration,
- total timeout budget,
- terminal failure behavior.

---

# 12. Retryable vs Non-Retryable Failures

Not every failure should trigger a retry.

Examples of potentially transient failures:

- temporary network interruption,
- service overload,
- temporary dependency unavailability.

Examples of potentially permanent failures:

- invalid request,
- authentication failure,
- schema violation,
- authorization failure.

Retry behavior should follow failure semantics.

---

# 13. Idempotency

Retries can result in duplicate execution.

For operations where duplication is harmful, define idempotency.

Example:

```text
Request
ID = 8472

First execution
       │
       ▼
Operation succeeds

Retry
       │
       ▼
Recognize ID = 8472
       │
       ▼
Do not execute business operation again
```

Idempotency requirements should be defined for important retryable operations.

---

# 14. Circuit Breaking

Repeated calls to a failing dependency can waste local resources and increase dependency load.

Where appropriate, a system may temporarily stop sending requests to a known failing dependency.

Conceptually:

```text
Dependency Healthy
       │
       ▼
Normal Calls
       │
       ▼
Repeated Failures
       │
       ▼
Stop / Reduce Calls
       │
       ▼
Periodic Recovery Check
       │
       ▼
Resume
```

Circuit breaking should not be added automatically to every dependency.

It should solve a demonstrated failure mode.

---

# 15. Bulkheads

A shared resource can allow one workload to consume capacity needed by another.

For example:

```text
                    Shared Pool
                 ┌───────────────┐
Workload A ─────►│               │
Workload B ─────►│               │
Workload C ─────►│               │
                 └───────────────┘
```

If Workload A consumes all capacity, B and C may also fail.

Isolation can reduce this coupling.

The appropriate isolation boundary depends on the system.

---

# 16. Resource Limits

Important resources should have sensible limits.

Examples include:

- request concurrency,
- connection pools,
- worker threads,
- queue depth,
- memory,
- storage,
- batch size.

Unbounded growth is often a precursor to cascading failure.

---

# 17. Backpressure

When incoming work exceeds processing capacity, the system should have an explicit response.

Possible behaviors include:

- queue,
- throttle,
- reject,
- defer,
- prioritize,
- shed load.

The correct choice depends on the business capability.

---

# 18. Load Shedding

When capacity is exhausted, protecting the entire system may require rejecting lower-priority work.

For example:

```text
                    Capacity Limit
                          │
             ┌────────────┴────────────┐
             ▼                         ▼
      Critical Work              Optional Work
             │                         │
             ▼                         ▼
          Preserve                  Reject
```

Load shedding should be deliberate and observable.

---

# 19. Graceful Degradation

A system may be able to continue providing reduced functionality.

Examples:

- serve cached information,
- disable recommendations,
- switch to read-only mode,
- defer non-critical processing,
- reduce response detail.

Degradation must not violate critical business invariants.

---

# 20. Fail-Open vs Fail-Closed

Some failures require an explicit decision about whether the system should:

```text
Fail Open
```

or:

```text
Fail Closed
```

For example, if an authorization dependency is unavailable, allowing access may be unacceptable for a sensitive operation.

For another low-risk capability, temporary access may be acceptable.

The decision must follow the security and business requirements.

---

# 21. Partial Failure

Distributed systems can fail partially.

For example:

```text
Service A ───► Service B
                  │
                  X
                  │
              Unavailable
```

Service A may still be healthy.

The system should determine:

- what functionality remains available,
- what becomes unavailable,
- whether fallback is possible,
- how recovery is detected.

---

# 22. Dependency Failure

For every critical dependency, determine:

- expected failure modes,
- timeout,
- retry policy,
- fallback,
- user-visible behavior,
- recovery behavior.

Do not assume dependencies are always available merely because they have high availability.

---

# 23. Dependency Failure Classification

A dependency can be:

```text
Available
Slow
Unavailable
Incorrect
Degraded
Recovering
```

Treating all failures as:

```text
HTTP 500
```

may hide important distinctions.

The appropriate response may differ significantly.

---

# 24. Slow Is a Failure Mode

A dependency does not need to be completely unavailable to cause failure.

Consider:

```text
Normal latency: 100 ms

Dependency becomes slow:
5 seconds
```

Even though requests eventually succeed, the system may experience:

- thread exhaustion,
- connection exhaustion,
- queue growth,
- user-visible latency,
- cascading timeouts.

Slow dependencies should therefore be treated as a meaningful failure mode.

---

# 25. Failure Propagation

Projects should understand how failures move through the architecture.

For example:

```text
Dependency Latency
       │
       ▼
Request Latency
       │
       ▼
Worker Occupancy
       │
       ▼
Queue Growth
       │
       ▼
Capacity Exhaustion
```

This analysis helps identify where containment mechanisms are required.

---

# 26. Queue Failures

For asynchronous systems, consider:

- queue unavailability,
- message duplication,
- message loss,
- delayed processing,
- poison messages,
- consumer failure,
- producer failure.

The system should define what constitutes successful processing.

---

# 27. Poison Messages

A message may repeatedly fail processing.

Without isolation:

```text
Message
   │
   ▼
Consumer
   X
   │
   ▼
Retry
   │
   X
   │
   ▼
Retry Forever
```

Where appropriate, systems should provide mechanisms such as:

- bounded retries,
- quarantine,
- dead-letter handling,
- manual review.

The exact mechanism depends on the messaging technology and business requirements.

---

# 28. Partial Operations

A business operation may span multiple components.

For example:

```text
Create Order
     │
     ├──► Reserve Inventory
     │
     ├──► Authorize Payment
     │
     └──► Send Confirmation
```

One step may succeed while another fails.

The architecture must define how the business state behaves in such situations.

Possible strategies include:

- transactions,
- compensation,
- retries,
- reconciliation,
- pending states.

The correct approach depends on the business semantics.

---

# 29. Compensation

When an operation cannot be atomically rolled back across components, a compensating action may restore business consistency.

For example:

```text
Reserve Inventory
       │
       ▼
Payment Fails
       │
       ▼
Release Inventory
```

Compensation is a business operation, not merely a technical rollback.

It should therefore be designed and tested explicitly.

---

# 30. Recovery

Recovery should answer:

> What does the system do after the failure condition disappears?

Recovery may involve:

- automatic retry,
- replay,
- reconciliation,
- resynchronization,
- cache rebuild,
- service restart,
- operator intervention.

A system that detects failure but cannot recover is only partially resilient.

---

# 31. Recovery Safety

Recovery mechanisms can introduce new failures.

For example:

```text
System Recovers
      │
      ▼
Retries Everything
      │
      ▼
Traffic Spike
      │
      ▼
System Fails Again
```

Recovery should therefore consider:

- rate limits,
- backoff,
- ordering,
- duplicate operations,
- dependency capacity.

---

# 32. Thundering Herd

When many clients or workers react to the same recovery event simultaneously, the system can experience a sudden load spike.

Examples include:

- cache expiration,
- service restart,
- dependency recovery,
- queue consumer restart.

Where relevant, recovery mechanisms should avoid synchronized bursts.

---

# 33. Cache Failure

Caches should generally be treated as non-authoritative unless explicitly designed otherwise.

The project should define:

- behavior when cache is unavailable,
- behavior when cache is stale,
- rebuild strategy,
- invalidation strategy.

A cache outage should not automatically become a database outage.

---

# 34. Configuration Failure

Configuration can be a production failure source.

Examples include:

- invalid connection strings,
- incorrect feature flags,
- missing environment variables,
- incorrect limits,
- incompatible settings.

Important configuration should therefore be:

- validated,
- versioned,
- reviewed,
- observable,
- recoverable.

---

# 35. Deployment Failure

A deployment may introduce:

- incorrect code,
- schema incompatibility,
- configuration errors,
- performance regressions.

Production systems should have an appropriate mechanism for detecting and mitigating bad changes.

Possible mechanisms include:

- rollback,
- progressive rollout,
- feature flags,
- health checks,
- automated validation.

---

# 36. Human Error

Operators are part of the system.

Important operational actions should therefore consider:

- authorization,
- confirmation,
- auditability,
- safe defaults,
- blast-radius reduction,
- recovery.

The goal is not to assume operators never make mistakes.

The goal is to make mistakes less catastrophic.

---

# 37. Failure Detection

Failures should be detectable through meaningful signals.

Potential signals include:

- error rate,
- latency,
- saturation,
- queue depth,
- dependency health,
- reconciliation failures,
- business metrics.

The appropriate signals are defined further in:

`08-observability/`.

---

# 38. Alerting

Not every failure signal should page an engineer.

Alerts should distinguish between:

- informational conditions,
- warnings,
- actionable incidents.

A useful alert should help answer:

> What is wrong, how severe is it, and what action is expected?

---

# 39. Business-Level Failure Detection

Technical metrics may not detect every important failure.

For example:

```text
HTTP Success = 99.99%
```

while:

```text
Orders successfully completed = 80%
```

The system should therefore consider business-level correctness signals where appropriate.

---

# 40. Incident Response

When a significant failure occurs, the organization should have an appropriate incident process.

This should establish:

- ownership,
- severity,
- communication,
- mitigation,
- recovery,
- escalation.

The required process should scale with system criticality.

---

# 41. Post-Incident Learning

After significant incidents, teams should determine:

- what happened,
- why it happened,
- why it was not prevented,
- why it was not detected earlier,
- how impact was contained,
- how recovery occurred,
- what should change.

The objective is learning and systemic improvement, not blame.

---

# 42. Reliability Evidence

Reliability claims should be supported by evidence where appropriate.

Examples include:

- recovery test results,
- load-test results,
- failure-injection results,
- measured availability,
- incident history,
- backup restoration tests.

"Designed to survive failure" is not the same as demonstrating that it survives failure.

---

# 43. Minimum Engineering Requirements

Every production project should:

- [ ] Identify important failure modes.
- [ ] Identify critical failure domains.
- [ ] Define timeout behavior for distributed calls.
- [ ] Define retry behavior where applicable.
- [ ] Define idempotency for important retryable operations.
- [ ] Establish resource boundaries.
- [ ] Define behavior under dependency failure.
- [ ] Consider failure containment and cascading failure.
- [ ] Define recovery behavior.
- [ ] Make important failures observable.
- [ ] Assign operational ownership.

Higher-tier systems may additionally require:

- [ ] Failure-domain isolation.
- [ ] Circuit breaking.
- [ ] Bulkheads.
- [ ] Load shedding.
- [ ] Graceful degradation.
- [ ] Failure-injection testing.
- [ ] Disaster recovery exercises.
- [ ] Formal reliability objectives.

---

# Relationship With Other Domains

This standard is part of the broader reliability domain.

It depends on and interacts with:

- `03-architecture/`
- `04-data/`
- `06-security/`
- `07-delivery/`
- `08-observability/`
- `11-operational-readiness/`

The distinction is:

**Architecture**

> Defines the system boundaries and relationships.

**Data**

> Protects the correctness, lifecycle, and recoverability of data.

**Reliability**

> Defines how the system behaves when components, dependencies, resources, or processes fail.

**Observability**

> Makes failures and their impact detectable.

**Operations**

> Makes mitigation and recovery executable.

---

# Final Principle

> **Failure behavior is part of the architecture. If the system has no deliberate answer to "what happens when this fails?", then the system already has an answer — it is simply an accidental one.**
