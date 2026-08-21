# Resilience Testing

> A resilience mechanism that has never been exercised is an assumption, not evidence.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Reliability Engineering

**Applies To:** Production systems, with depth determined by system tier, business criticality, and failure impact

---

# Purpose

Reliability engineering cannot depend entirely on architectural intent.

A system may claim to have:

- retries,
- timeouts,
- failover,
- backups,
- autoscaling,
- graceful degradation,
- recovery procedures,

while still failing unexpectedly when those mechanisms are exercised.

Resilience testing provides evidence about how a system behaves under adverse conditions.

The objective is not to deliberately break production systems without purpose.

The objective is:

> **To reduce uncertainty about important failure modes before those failures occur in production.**

---

# Engineering Principle

> **Test the failure behavior that matters to the business, at a level proportional to the consequences of failure.**

Not every application requires chaos engineering.

Not every failure requires a dedicated experiment.

But important reliability assumptions should be tested where practical.

---

# 1. What Is Resilience Testing?

Resilience testing evaluates how a system behaves when conditions become abnormal.

Examples include:

- dependency unavailable,
- dependency slow,
- process crashes,
- node becomes unavailable,
- network communication fails,
- storage becomes unavailable,
- queue processing stops,
- traffic increases,
- resources approach exhaustion,
- deployment introduces failure,
- recovery occurs after an outage.

The purpose is to understand:

```text
Failure
   │
   ▼
System Behavior
   │
   ▼
Impact
   │
   ▼
Recovery
```

---

# 2. Resilience Testing vs Functional Testing

Functional testing asks:

> Does the system behave correctly under expected conditions?

Resilience testing asks:

> Does the system behave acceptably when expected assumptions stop being true?

Both are necessary.

A service can pass every functional test and still fail catastrophically when its database becomes slow.

---

# 3. Resilience Testing vs Load Testing

Load testing primarily evaluates behavior under increasing demand.

Resilience testing evaluates behavior under adverse conditions.

There can be overlap.

For example:

```text
High Load
   +
Dependency Latency
```

may be a meaningful resilience scenario.

The project should select testing techniques according to the risk being investigated.

---

# 4. Test the Contract

Resilience tests should validate an explicit expectation.

For example:

```text
Expectation:

If the recommendation service becomes unavailable,
checkout must remain functional.
```

A useful test then becomes:

```text
Recommendation Service
        │
        X
        │
        ▼
Checkout
        │
        ▼
Should Continue
```

Without a defined expected behavior, a resilience experiment produces observations but not necessarily useful engineering evidence.

---

# 5. Failure Hypothesis

Important resilience tests should begin with a hypothesis.

A useful structure is:

```text
If [failure condition] occurs,

then [expected system behavior]

because [architectural mechanism].
```

Example:

```text
If the payment provider becomes temporarily unavailable,

then the order should remain in a recoverable pending state,

because payment processing is asynchronous and idempotent.
```

---

# 6. Define the Blast Radius

Before introducing a failure, establish:

- what is being affected,
- what must remain unaffected,
- which users may be impacted,
- how the experiment will be stopped.

Testing should not create uncontrolled production impact.

---

# 7. Test Environment

Resilience tests can be performed in different environments:

- local development,
- test environment,
- staging,
- dedicated resilience environment,
- production.

The appropriate environment depends on:

- realism required,
- risk,
- system tier,
- failure scenario.

Higher-risk experiments require stronger controls.

---

# 8. Production Testing

Some failures are difficult to reproduce outside production.

Examples include:

- real traffic patterns,
- real dependency behavior,
- real infrastructure topology,
- real-scale resource interactions.

Production experiments may therefore be justified for mature systems.

However, they require:

- explicit scope,
- safeguards,
- abort criteria,
- observability,
- ownership,
- communication.

---

# 9. Dependency Failure Testing

Critical dependencies should be tested where appropriate.

Potential scenarios include:

```text
Dependency unavailable
Dependency slow
Dependency returns errors
Dependency returns malformed data
Dependency rate-limits requests
Dependency recovers
```

The project should verify that the calling system behaves according to its intended contract.

---

# 10. Timeout Testing

A dependency may become slow without becoming completely unavailable.

Test scenarios should consider:

```text
Normal Response
      │
      ▼
Increasing Latency
      │
      ▼
Timeout Boundary
      │
      ▼
Recovery
```

Verify:

- timeout occurs as expected,
- resources are released,
- retries behave correctly,
- users receive appropriate behavior,
- recovery does not create a traffic spike.

---

# 11. Retry Testing

Retry mechanisms should be exercised.

Verify:

- transient failures are retried,
- permanent failures are not retried indefinitely,
- retry limits are respected,
- backoff operates correctly,
- jitter prevents synchronization where relevant,
- retries do not overwhelm the dependency.

A retry mechanism that has never been exercised under failure may behave differently from its intended design.

---

# 12. Idempotency Testing

Where operations are retryable, verify duplicate execution behavior.

Example:

```text
Request ID = 123

Attempt 1
    │
    ▼
Success

Attempt 2
    │
    ▼
Duplicate Request
```

The test should establish whether the resulting business state remains correct.

This is especially important for:

- payments,
- orders,
- message consumers,
- external integrations.

---

# 13. Process Failure

Processes may terminate unexpectedly.

Where relevant, test:

- process crash,
- restart,
- in-flight request behavior,
- in-flight message behavior,
- state recovery,
- duplicate processing.

The objective is to understand whether restart produces:

```text
Clean Recovery
```

or:

```text
Inconsistent State
```

---

# 14. Instance Failure

If a system is expected to survive instance failure, exercise that assumption.

Verify:

- traffic reroutes appropriately,
- remaining capacity is sufficient,
- state remains accessible,
- monitoring detects the failure,
- recovery occurs within the required objective.

Do not infer high availability solely from the existence of multiple instances.

---

# 15. Failure-Domain Testing

Where resilience depends on failure-domain separation, test the actual boundary where practical.

Examples include:

- availability zone failure,
- host failure,
- network segment failure,
- regional dependency failure.

The objective is to validate that supposedly independent components are actually independent enough for the intended reliability contract.

---

# 16. Network Failure

Distributed systems should be tested against relevant network failures.

Potential scenarios include:

- latency,
- packet loss,
- connection failure,
- temporary partition,
- DNS failure,
- unavailable route.

The purpose is to verify assumptions around:

- timeouts,
- retries,
- connection pools,
- failover,
- recovery.

---

# 17. Queue Failure

For asynchronous systems, consider testing:

- producer failure,
- consumer failure,
- queue unavailable,
- delayed processing,
- duplicate delivery,
- poison messages,
- consumer recovery.

Verify that the resulting system state remains consistent with the business contract.

---

# 18. Backlog Testing

A queue may accumulate work during an outage.

Test:

```text
Consumer Failure
      │
      ▼
Backlog Growth
      │
      ▼
Consumer Recovery
      │
      ▼
Backlog Processing
```

Observe:

- processing rate,
- recovery duration,
- resource consumption,
- downstream pressure,
- duplicate handling.

---

# 19. Capacity Failure Testing

Where capacity is important, test what happens near resource limits.

Examples include:

- CPU saturation,
- memory pressure,
- connection exhaustion,
- storage exhaustion,
- queue growth,
- API quota exhaustion.

The objective is to determine whether the system:

- degrades gracefully,
- rejects work safely,
- cascades into failure,
- recovers automatically.

---

# 20. Load and Stress Testing

Load testing should establish how the system behaves as demand increases.

A useful progression is:

```text
Normal Load
    │
    ▼
Increasing Load
    │
    ▼
Peak Expected Load
    │
    ▼
Beyond Expected Load
    │
    ▼
Saturation
```

The purpose is to understand both capacity and failure behavior.

---

# 21. Recovery Testing

Recovery should be tested independently from failure detection.

Verify:

- failure is detected,
- recovery begins,
- required resources become available,
- state is reconstructed,
- dependent systems recover,
- normal operation resumes.

A system that detects failure but cannot recover automatically may still require significant operational intervention.

---

# 22. Recovery Time Measurement

Where RTO is defined, measure actual recovery time.

For example:

```text
Failure
  │
  ├── Detection
  │
  ├── Mitigation
  │
  ├── Recovery
  │
  └── Validation
```

The measured duration should be compared against the required RTO.

This turns a recovery objective into an engineering claim supported by evidence.

---

# 23. Data Recovery Testing

Data recovery should validate more than infrastructure restoration.

Verify:

- data can be restored,
- schema is compatible,
- application can consume restored data,
- important invariants remain valid,
- derived state can be rebuilt where required.

Detailed requirements are defined in:

`04-data/data-reliability-and-recovery.md`

---

# 24. Disaster Recovery Testing

For systems requiring disaster recovery, the recovery architecture should be exercised periodically.

Testing may include:

- region failure simulation,
- restoration from backup,
- standby activation,
- traffic redirection,
- data recovery,
- dependency reconfiguration.

The appropriate scope depends on the system's disaster recovery contract.

---

# 25. Failover Testing

If a system claims automatic failover, test it.

Verify:

```text
Primary
   │
   X
   │
   ▼
Secondary
```

Questions to answer include:

- Was failover triggered?
- How long did it take?
- Was state preserved?
- Were requests lost?
- Were duplicate operations created?
- Did clients recover correctly?

---

# 26. Failback Testing

Recovery is not complete merely because traffic moved to a secondary system.

The architecture may also need to return to the intended operating state.

Where applicable, test:

```text
Primary
   X
   │
   ▼
Secondary

      │
      │ Recovery
      ▼

Primary
```

Failback can introduce different failure modes from failover.

---

# 27. Degradation Testing

If the system has fallback behavior, verify that it actually works.

For example:

```text
Primary Recommendation Service
          │
          X
          │
          ▼
Cached Recommendation
```

Test both:

- fallback activation,
- return to normal behavior.

Fallback paths are often less exercised than primary paths.

---

# 28. Cascading Failure Testing

Where practical, test whether a local failure propagates.

Example:

```text
Dependency Slow
      │
      ▼
Application Threads Occupied
      │
      ▼
Connection Pool Exhaustion
      │
      ▼
Request Failures
```

The purpose is to identify missing containment mechanisms.

---

# 29. Recovery Storm Testing

Recovery can itself generate excessive load.

Test scenarios where:

- many queued messages become available,
- many clients reconnect,
- caches repopulate,
- replicas resynchronize,
- services restart simultaneously.

The goal is to verify that recovery does not create another outage.

---

# 30. Configuration Failure Testing

Where configuration is critical, test invalid or missing configuration.

Examples include:

- invalid endpoint,
- unavailable credential,
- incompatible feature flag,
- incorrect timeout,
- invalid connection settings.

Verify that failures are:

- detected early,
- observable,
- safely handled.

---

# 31. Deployment Failure Testing

A production deployment process should be tested against failure.

Examples:

- application startup failure,
- failed health checks,
- incompatible schema,
- elevated error rate,
- latency regression.

Verify that the delivery mechanism can:

- stop rollout,
- prevent further exposure,
- roll back or mitigate,
- preserve data integrity.

---

# 32. Schema Migration Testing

Database migrations can create difficult recovery scenarios.

Where applicable, test:

- migration failure,
- partial migration,
- rollback limitations,
- application compatibility,
- deployment ordering.

The project should explicitly understand whether a migration is:

```text
Reversible
```

or:

```text
Forward-only
```

and design deployment accordingly.

---

# 33. Observability Validation

A resilience test should also verify observability.

During the experiment, determine:

- Did the system detect the failure?
- Did the right metrics change?
- Did logs provide useful evidence?
- Did traces reveal the dependency failure?
- Did alerts fire appropriately?

A system that survives failure but cannot explain what happened remains operationally difficult.

---

# 34. User Impact Validation

Technical metrics should be correlated with user impact.

For example:

```text
Dependency Failure
        │
        ▼
Error Rate
        │
        ▼
Affected Requests
        │
        ▼
Affected Users
        │
        ▼
Business Impact
```

Where practical, resilience testing should determine whether the expected business behavior actually occurred.

---

# 35. Abort Criteria

Experiments should have explicit stop conditions.

Examples include:

- unexpected user impact,
- error rate above threshold,
- resource exhaustion,
- data integrity risk,
- uncontrolled failure propagation.

The team should know:

> When do we stop the experiment?

before starting it.

---

# 36. Safety Controls

Higher-risk experiments should have appropriate safeguards.

These may include:

- limited scope,
- traffic limits,
- isolated test accounts,
- feature flags,
- rollback mechanisms,
- automated aborts,
- operator supervision.

Controls should be proportional to the experiment's risk.

---

# 37. Production Experimentation

Production resilience testing should be treated as an engineering activity, not an ad-hoc stunt.

Before execution, establish:

- hypothesis,
- scope,
- expected behavior,
- observability,
- abort conditions,
- ownership,
- communication.

After execution, document the result.

---

# 38. Evidence

A resilience test should produce useful evidence.

Capture where appropriate:

- test scenario,
- system version,
- environment,
- failure introduced,
- expected behavior,
- observed behavior,
- impact,
- recovery time,
- unexpected findings,
- corrective actions.

Evidence should be retained according to the project's governance requirements.

---

# 39. Failed Experiments Are Valuable

A resilience test that disproves an assumption is not necessarily a failure.

For example:

```text
Expected:

Dependency failure
       ↓
Graceful fallback

Observed:

Dependency failure
       ↓
Request timeout
       ↓
Thread exhaustion
       ↓
Service outage
```

The experiment has identified a reliability defect before an equivalent production incident occurred.

---

# 40. Test Frequency

Testing frequency should depend on:

- system tier,
- change frequency,
- architecture complexity,
- failure impact,
- historical incidents.

A system that changes frequently may require more frequent validation than a rarely changed system.

---

# 41. Test After Significant Architectural Change

Resilience assumptions may become invalid after changes such as:

- new dependencies,
- database migration,
- architecture redesign,
- scaling model changes,
- deployment changes,
- regional changes.

Important resilience tests should therefore be reconsidered after significant architectural changes.

---

# 42. Resilience Test Ownership

Each important test should have an owner responsible for:

- maintaining the scenario,
- validating results,
- updating assumptions,
- tracking remediation.

A resilience test suite that nobody owns will eventually become stale.

---

# 43. Remediation

When a resilience test exposes a weakness, the result should feed into engineering work.

Possible outcomes include:

- architecture change,
- configuration change,
- automation,
- monitoring improvement,
- documentation,
- accepted risk.

Not every finding requires immediate remediation.

Risk acceptance should be explicit.

---

# 44. Minimum Engineering Requirements

Every production project should:

- [ ] Identify important resilience assumptions.
- [ ] Define expected behavior for important failure scenarios.
- [ ] Test critical recovery mechanisms where practical.
- [ ] Validate important timeout and retry behavior.
- [ ] Validate important backup/restore mechanisms.
- [ ] Verify observability during failure scenarios.
- [ ] Document significant findings.
- [ ] Assign ownership for remediation.

Higher-tier systems may additionally require:

- [ ] Formal resilience test plans.
- [ ] Regular failure-injection testing.
- [ ] Disaster recovery exercises.
- [ ] Failover and failback testing.
- [ ] Capacity and saturation testing.
- [ ] Production resilience experiments.
- [ ] Automated resilience validation.
- [ ] Measured RTO/RPO evidence.

---

# Relationship With Other Standards

This standard works with:

- `05-reliability/failure-management.md`
- `05-reliability/capacity-and-scalability.md`
- `04-data/data-reliability-and-recovery.md`
- `07-delivery/`
- `08-observability/`
- `11-operational-readiness/`

The distinction is:

**Failure Management**

> Defines how the system should behave when failures occur.

**Capacity and Scalability**

> Defines how the system behaves as workload and resource pressure increase.

**Resilience Testing**

> Provides evidence that the intended behavior actually occurs.

---

# Final Principle

> **Reliability is an engineering claim. Resilience testing is one of the ways we gather evidence that the claim is true.**
