# Progressive Delivery

> Progressive delivery is the discipline of controlling production exposure of a change while continuously evaluating evidence before increasing that exposure.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Delivery Engineering

---

# Purpose

A deployment can be technically successful while the released software is operationally unhealthy.

Progressive delivery reduces this risk by separating:

```text
Deployment
```

from:

```text
Full Exposure
```

A release can therefore move through controlled levels of production exposure.

```text
New Artifact
     │
     ▼
Limited Exposure
     │
     ▼
Observe
     │
     ▼
Increase Exposure
     │
     ▼
Observe
     │
     ▼
Full Exposure
```

---

# Engineering Principle

> **Increase production exposure only when the evidence says the system remains within acceptable operating boundaries.**

---

# 1. Why Progressive Delivery Exists

A deployment changes software.

A production system contains uncertainty that testing cannot completely eliminate.

Examples include:

- unexpected traffic patterns,
- unusual customer behavior,
- dependency failures,
- resource contention,
- data characteristics,
- regional differences,
- previously unseen interactions.

Progressive delivery limits the amount of production exposure before these unknowns are understood.

---

# 2. Deployment vs Exposure

Deployment answers:

> Is the new software present in the environment?

Progressive delivery answers:

> How much traffic, workload, or user population should experience it?

These are separate concerns.

```text
Deployment
    │
    ▼
Software Available
    │
    ▼
Exposure Decision
```

---

# 3. Exposure Is a Control Variable

Production exposure can be controlled using dimensions such as:

- percentage of traffic,
- number of instances,
- users,
- tenants,
- regions,
- geographic areas,
- customer segments,
- request types.

The appropriate dimension depends on the architecture.

---

# 4. Initial Exposure

High-risk changes should generally begin with limited exposure where practical.

The initial exposure should be large enough to generate useful evidence but small enough to limit potential impact.

This is a risk decision.

---

# 5. Canary Release

A canary release exposes a new version to a small subset of production traffic or workload.

For example:

```text
Production Traffic

Existing Version     95%
New Version           5%
```

The exact percentage is not universal.

The important property is controlled exposure.

---

# 6. Canary Selection

Canary traffic should be selected deliberately.

Possible strategies include:

- random traffic,
- geographic selection,
- tenant selection,
- customer segment,
- instance selection,
- request characteristics.

Selection should provide useful evidence without unintentionally concentrating risk.

---

# 7. Representative Traffic

A canary should ideally receive traffic representative of the behavior the new version will eventually experience.

A canary receiving only trivial traffic may provide false confidence.

Therefore, exposure design should consider:

- request types,
- customer behavior,
- traffic volume,
- geographical distribution,
- workload characteristics.

---

# 8. Progressive Exposure

A progressive rollout may use stages such as:

```text
1%
 │
 ▼
5%
 │
 ▼
10%
 │
 ▼
25%
 │
 ▼
50%
 │
 ▼
100%
```

The exact stages should be appropriate to the system.

Each stage should have a defined evaluation period and promotion criteria.

---

# 9. Promotion

Promotion means increasing exposure after evaluating the current stage.

A promotion decision should consider evidence such as:

- error rate,
- latency,
- availability,
- resource utilization,
- dependency health,
- business outcomes.

---

# 10. Halt

A rollout should be able to stop without immediately exposing additional users.

For example:

```text
10% Exposure
     │
     ▼
Observe
     │
     X
   Halt
```

Halt is useful when:

- evidence is insufficient,
- metrics are ambiguous,
- unexpected behavior appears,
- external dependencies are unstable.

---

# 11. Rollback

Rollback reduces exposure to the new version and returns traffic or workload to a previous known state where possible.

```text
New Version
    │
    ▼
Unhealthy
    │
    ▼
Reduce Exposure
    │
    ▼
Previous Version
```

Rollback should not be assumed to be universally safe.

---

# 12. Forward Recovery

Some changes cannot safely be reversed.

Progressive delivery should therefore also support forward recovery.

For example:

```text
Version N
   │
   ▼
Version N+1
   │
   X
   │
   ▼
Version N+2
```

The correct recovery strategy depends on data and compatibility constraints.

---

# 13. Automated Promotion

Promotion can be automated when the evaluation signals are:

- reliable,
- measurable,
- sufficiently fast,
- resistant to noise.

Automation should follow confidence in the signals.

---

# 14. Automated Rollback

Automated rollback can reduce exposure quickly when strong failure signals appear.

Suitable signals may include:

- severe error-rate increase,
- sustained latency regression,
- failed health checks,
- capacity exhaustion.

Automatic rollback should have safeguards against oscillation.

---

# 15. Rollout Oscillation

A poorly designed automated rollout can repeatedly alternate:

```text
Promote
   ↓
Rollback
   ↓
Promote
   ↓
Rollback
```

This can create more instability than the original release.

Controls may include:

- stabilization periods,
- bounded retries,
- minimum observation windows,
- cooldown periods,
- human escalation.

---

# 16. Evaluation Windows

Metrics should be evaluated over meaningful time windows.

An extremely short observation window may produce false conclusions.

An extremely long window may expose too many users before detecting a problem.

The window should reflect:

- traffic volume,
- failure characteristics,
- business cycle,
- system behavior.

---

# 17. Success Criteria

Every progressive rollout should define what "healthy enough" means.

Examples:

```text
Error Rate < Threshold
Latency < Threshold
Availability > Threshold
Business Metric within Expected Range
```

Thresholds should reflect service requirements rather than arbitrary numbers.

---

# 18. Failure Criteria

Failure criteria should also be explicit.

Examples include:

- sustained error increase,
- severe latency regression,
- availability degradation,
- resource exhaustion,
- security violation,
- business-impacting behavior.

A rollout should not depend entirely on someone noticing a dashboard manually.

---

# 19. Statistical Noise

Production metrics naturally contain variation.

Therefore, promotion should avoid reacting to every small fluctuation.

Evaluation should distinguish:

```text
Normal Variation
```

from:

```text
Meaningful Regression
```

The appropriate statistical method depends on the metric.

---

# 20. Baseline Comparison

Where useful, the new version should be compared against a meaningful baseline.

For example:

```text
Existing Version
       vs
New Version
```

Possible comparison dimensions include:

- error rate,
- latency,
- resource consumption,
- business success rate.

---

# 21. Control vs Treatment

Progressive delivery can provide a natural comparison between versions.

```text
Control
Existing Version

Treatment
New Version
```

This can provide stronger evidence than comparing the new version against historical averages alone.

---

# 22. Business Metrics

Technical health does not guarantee business correctness.

A release may have:

```text
HTTP 200
Low CPU
Low Memory
```

while producing:

```text
Incorrect Business Result
```

Where appropriate, progressive delivery should therefore include business-level signals.

---

# 23. Observability Dependency

Progressive delivery depends heavily on observability.

Without useful telemetry:

```text
Exposure
   │
   ▼
Unknown Outcome
```

With useful telemetry:

```text
Exposure
   │
   ▼
Evidence
   │
   ├── Healthy
   └── Unhealthy
```

Therefore:

> **Progressive delivery cannot be more mature than the observability supporting its decisions.**

---

# 24. Metrics Selection

Metrics should be:

- relevant,
- measurable,
- sufficiently sensitive,
- difficult to manipulate accidentally,
- available within the rollout decision window.

Too many metrics can make decisions ambiguous.

---

# 25. Service-Level Objectives

Where service-level objectives exist, rollout decisions should consider whether the new version threatens the relevant objectives.

For example:

```text
SLO
 │
 ▼
Error Budget
 │
 ▼
Release Decision
```

A release that consumes excessive reliability budget may require reduced exposure or a halt.

---

# 26. Error Budget

Progressive delivery can use error-budget consumption as a release signal.

This creates a connection between:

- delivery velocity,
- reliability,
- production risk.

The goal is not to eliminate all risk.

It is to make risk consumption visible.

---

# 27. Exposure Duration

Exposure percentage alone does not define risk.

Consider:

```text
5% for 2 minutes
```

versus:

```text
5% for 24 hours
```

The impact and evidence generated can be very different.

Progressive delivery should therefore consider both:

- exposure magnitude,
- exposure duration.

---

# 28. Blast Radius

Progressive delivery reduces blast radius by limiting exposure.

Blast radius may be limited through:

- traffic percentage,
- tenant selection,
- geographic scope,
- instance count,
- region.

The best control depends on where failure could propagate.

---

# 29. Tenant-Based Rollout

Multi-tenant systems may progressively expose a release by tenant.

For example:

```text
Tenant Group A
      │
      ▼
New Version

Tenant Group B
      │
      ▼
Old Version
```

Tenant selection should consider:

- workload characteristics,
- customer criticality,
- data sensitivity,
- representativeness.

---

# 30. Geographic Rollout

Distributed systems may progressively release by geography.

For example:

```text
Region A
   │
   ▼
Observe
   │
   ▼
Region B
   │
   ▼
Observe
```

Regional rollout can limit impact but may also hide region-specific problems if traffic characteristics differ.

---

# 31. Instance-Level Rollout

A rollout can initially target a subset of instances.

This is useful when:

- instances are independently observable,
- traffic can be controlled,
- the architecture supports coexistence.

Instance-level rollout should still account for load balancing behavior.

---

# 32. Dependency Compatibility

A new version may depend on another service.

Progressive delivery should consider whether:

```text
New Service
     +
Old Dependency
```

and:

```text
Old Service
     +
New Dependency
```

remain safe during transition.

This is especially important in independently deployed systems.

---

# 33. Schema Compatibility

Progressive delivery may temporarily run different application versions against the same data.

Therefore:

> **Schema compatibility is a prerequisite for many progressive rollout strategies.**

Database changes should support the coexistence period.

---

# 34. Configuration Compatibility

Configuration changes should be evaluated similarly.

A new application version may require configuration that the old version cannot understand.

Progressive delivery should therefore consider:

```text
Old Application
       +
New Configuration
```

and:

```text
New Application
       +
Old Configuration
```

where coexistence is possible.

---

# 35. Feature Flags

Feature flags can separate:

```text
Deployment
```

from:

```text
Feature Exposure
```

This can provide another control dimension for progressive delivery.

Feature flags should have:

- ownership,
- access control,
- lifecycle management,
- cleanup plans.

---

# 36. Kill Switches

High-risk features may require an operational mechanism to disable behavior quickly.

A kill switch can reduce exposure without requiring a full redeployment.

However, kill switches introduce operational complexity and should themselves be tested.

---

# 37. Dark Launch

A feature may receive production-like workload without being visible to users.

This can provide evidence about:

- performance,
- infrastructure requirements,
- compatibility,
- unexpected behavior.

The implementation must ensure that hidden execution cannot create unintended side effects.

---

# 38. Shadow Traffic

Shadow traffic sends a copy of requests to another version without using its response for the real user request.

Conceptually:

```text
User Request
     │
     ├──────────────► Existing Version
     │
     └──────────────► New Version
                         │
                         ▼
                     Observation
```

The architecture must prevent shadow execution from causing unintended mutations.

---

# 39. Progressive Infrastructure Changes

Progressive delivery is not limited to application code.

It may also apply to:

- infrastructure,
- configuration,
- networking,
- database changes.

The same principle applies:

> Limit exposure, observe consequences, increase exposure deliberately.

---

# 40. Progressive Security Changes

Security changes can also benefit from controlled rollout.

Examples include:

- authorization policy changes,
- authentication changes,
- network policy changes.

A staged rollout can expose unintended access or denial behavior before full adoption.

---

# 41. Rollout Coordination

Multiple progressive rollouts may interact.

For example:

```text
Service A → 50% New
Service B → 20% New
Database  → New
```

The system should understand whether these intermediate combinations are valid.

---

# 42. Concurrent Releases

If multiple releases are occurring simultaneously, attribution becomes harder.

A rollout should ideally make it possible to determine:

> Which change caused this behavior?

Small, independently attributable changes reduce diagnostic complexity.

---

# 43. Rollout State

The rollout system should expose its current state.

Useful states include:

```text
Pending
Running
Paused
Promoting
Completed
Failed
Rolled Back
```

The exact state model depends on implementation.

The important property is that rollout state should not be ambiguous.

---

# 44. Rollout Ownership

Someone or something must own the rollout decision.

Ownership should cover:

- promotion,
- halt,
- rollback,
- escalation.

Automation can own these decisions when the policy is explicit.

---

# 45. Human Intervention

Human intervention may be appropriate when:

- evidence is ambiguous,
- business impact is difficult to measure,
- security concerns arise,
- dependencies are unstable,
- recovery is uncertain.

Humans should receive enough evidence to make an informed decision.

---

# 46. Rollout Auditability

The system should retain:

- rollout identity,
- artifact,
- exposure levels,
- promotion decisions,
- rollback events,
- decision signals,
- initiating identity.

This supports later analysis.

---

# 47. Rollout Recovery

Progressive delivery infrastructure itself can fail.

The organization should consider what happens if:

- the rollout controller fails,
- telemetry becomes unavailable,
- traffic routing becomes unavailable,
- deployment state is lost.

The production system should have a safe failure mode.

---

# 48. Observability Failure

If the signals required for automated promotion disappear, continuing to increase exposure may be unsafe.

A mature system should define whether:

```text
No Reliable Evidence
```

means:

```text
Pause
```

rather than:

```text
Continue Automatically
```

---

# 49. Progressive Delivery and Reliability

Progressive delivery creates a feedback loop:

```text
Change
  │
  ▼
Limited Exposure
  │
  ▼
Observe
  │
  ▼
Evaluate
  │
  ├── Healthy ──► Increase Exposure
  │
  └── Unhealthy ─► Halt / Recover
```

This connects delivery directly with operational feedback.

---

# 50. Minimum Engineering Requirements

Every production project using progressive delivery should:

- [ ] Define what exposure is being controlled.
- [ ] Define meaningful rollout stages.
- [ ] Define promotion criteria.
- [ ] Define failure criteria.
- [ ] Observe production behavior during rollout.
- [ ] Provide a halt mechanism.
- [ ] Define rollback or forward-recovery behavior.
- [ ] Preserve rollout traceability.
- [ ] Ensure intermediate versions are compatible where required.
- [ ] Define behavior when required telemetry is unavailable.
- [ ] Assign rollout ownership.

Higher-risk systems may additionally require:

- [ ] Automated promotion.
- [ ] Automated rollback.
- [ ] Canary analysis.
- [ ] Control/treatment comparison.
- [ ] Business-metric evaluation.
- [ ] Tenant-based rollout.
- [ ] Geographic rollout.
- [ ] Shadow traffic.
- [ ] Feature-flag governance.
- [ ] Formal rollout policies.
- [ ] Error-budget-based promotion.
- [ ] Automated blast-radius controls.

---

# Relationship With Other Delivery Standards

This standard works with:

- `07-delivery/README.md`
- `07-delivery/source-control.md`
- `07-delivery/ci.md`
- `07-delivery/build-and-artifacts.md`
- `07-delivery/release-management.md`
- `07-delivery/deployment.md`

It also connects directly with:

- `05-reliability/`
- `06-security/`
- `08-observability/`
- `11-operational-readiness/`

---

# What This Standard Is Not

This standard does not prescribe:

- Kubernetes Deployments,
- Argo Rollouts,
- Flagger,
- service meshes,
- feature-flag platforms,
- specific cloud providers,
- a particular CI/CD system.

Those are implementation choices.

The engineering contract is:

> **Production exposure of a change should be controllable, observable, and reversible or recoverable to a degree appropriate to the risk.**

---

# Final Principle

> **A mature deployment does not ask users to absorb all of the uncertainty at once. Progressive delivery turns production itself into a controlled validation environment: expose a little, observe reality, learn, and only then expose more.**
