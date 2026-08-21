# Deployment

> Deployment is the controlled process of changing a running environment from one known state to another.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Delivery Engineering

---

# Purpose

Deployment changes the state of an environment.

A deployment may change:

- application versions,
- infrastructure,
- configuration,
- database schemas,
- routing,
- feature availability,
- supporting services.

The objective is to make that transition:

- controlled,
- repeatable,
- observable,
- attributable,
- recoverable where possible.

---

# Engineering Principle

> **A deployment should transform a known environment state into another known state through a controlled and observable process.**

---

# 1. Deployment Is State Change

A deployment is not simply:

```text
Run a script
```

It is a state transition:

```text
Current Environment State
          │
          ▼
      Deployment
          │
          ▼
Target Environment State
```

The deployment mechanism should make this transition sufficiently predictable to operate safely.

---

# 2. Desired State

Where practical, deployment should define the desired state rather than merely executing imperative commands.

For example:

```text
Desired State:
Application = Version 5
Replicas    = 6
Configuration = X
```

The deployment system is then responsible for making the environment conform to that state.

---

# 3. Declarative vs Imperative Deployment

Two broad approaches exist.

### Imperative

```text
Execute command A
Execute command B
Execute command C
```

### Declarative

```text
Desired State
     │
     ▼
Deployment System
     │
     ▼
Actual State
```

Declarative approaches can improve repeatability and drift detection, but they are not automatically superior for every deployment problem.

The choice should reflect system characteristics.

---

# 4. Artifact Selection

Deployment should reference an identifiable artifact.

Avoid ambiguous references where production could unknowingly receive different content.

Prefer immutable artifact identity where supported.

For example:

```text
Release
   │
   ▼
Artifact Digest
   │
   ▼
Deployment
```

---

# 5. Environment Identity

The deployment system should know exactly where the artifact is being deployed.

Environment identity may include:

- development,
- test,
- staging,
- production,
- region,
- cluster,
- account,
- subscription.

Accidentally deploying to the wrong environment is a delivery failure.

---

# 6. Deployment Authorization

Production deployments should be performed by an authorized identity.

The identity may be:

- a human,
- a service account,
- an automated pipeline.

The organization should be able to determine who or what initiated the deployment.

---

# 7. Least Privilege

Deployment identities should receive only the permissions required to perform their responsibilities.

A deployment system should not automatically have unrestricted access to every infrastructure component.

Permission boundaries should limit blast radius.

---

# 8. Deployment Repeatability

Given:

```text
Same Artifact
+
Same Intended Configuration
+
Same Environment State
```

the deployment process should produce a predictable result.

Repeatability is particularly important for:

- recovery,
- disaster scenarios,
- rollback,
- environment recreation.

---

# 9. Idempotency

Where practical, deployment operations should be idempotent.

An idempotent operation can safely be retried without producing unintended additional effects.

For example:

```text
Deploy Version 5
      │
      ▼
Version 5 Running
      │
      ▼
Retry Deployment
      │
      ▼
Version 5 Running
```

Retries should not create unintended duplicate state.

---

# 10. Deployment Ordering

Some changes depend on ordering.

Examples include:

- infrastructure before application,
- schema before application,
- application compatibility before schema cleanup.

Dependencies should be explicit rather than relying on accidental execution order.

---

# 11. Backward Compatibility

During rolling deployments, multiple versions may coexist.

Therefore:

```text
Version N
    +
Version N+1
```

may temporarily operate simultaneously.

Interfaces, schemas, and protocols should support this where required.

---

# 12. Database Deployment

Database changes require special care.

A deployment may involve:

```text
Schema
   │
   ▼
Migration
   │
   ▼
Application
```

or:

```text
Application Compatibility
   │
   ▼
Schema Change
   │
   ▼
Application Cleanup
```

The correct sequence depends on compatibility requirements.

---

# 13. Configuration Deployment

Configuration can change production behavior without changing the artifact.

Configuration deployment should therefore have:

- validation,
- versioning or traceability,
- ownership,
- controlled access,
- recovery consideration.

---

# 14. Secrets

Deployment systems should obtain secrets through approved secret-management mechanisms.

Secrets should not be embedded in:

- deployment scripts,
- source repositories,
- container images,
- command history,
- logs.

---

# 15. Deployment Strategies

The appropriate deployment strategy depends on risk.

Common strategies include:

- rolling,
- blue/green,
- canary,
- recreate,
- progressive delivery.

The organization should select the strategy based on:

- failure impact,
- traffic patterns,
- rollback capability,
- architecture,
- operational maturity.

---

# 16. Rolling Deployment

A rolling deployment replaces instances incrementally.

Conceptually:

```text
Old State
A A A A

        ↓

A A A B

        ↓

A A B B

        ↓

A B B B

        ↓

B B B B
```

The system should maintain sufficient capacity and compatibility during the transition.

---

# 17. Blue/Green Deployment

Blue/green deployment maintains two environments or environment states.

Conceptually:

```text
Blue
Current Production
       │
       │
       └── Users

Green
New Version
       │
       │
       └── Validation
```

Traffic can then be moved between the two states.

This can simplify rollback but may require additional infrastructure capacity.

---

# 18. Canary Deployment

Canary deployment exposes a new version to a small portion of traffic.

Conceptually:

```text
Users
  │
  ├── Existing Version
  │       95%
  │
  └── New Version
          5%
```

The system observes the new version before increasing exposure.

---

# 19. Progressive Delivery

Progressive delivery generalizes controlled exposure.

A rollout may follow:

```text
Small Exposure
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

Promotion criteria should be based on measurable evidence.

---

# 20. Health Verification

Deployment completion should not be defined solely by process completion.

The deployment should verify appropriate health indicators.

Examples include:

- process health,
- readiness,
- error rate,
- latency,
- resource consumption,
- dependency health.

---

# 21. Startup Health vs Runtime Health

A system can successfully start and still be unhealthy.

For example:

```text
Process Started
      │
      ▼
Dependency Failure
      │
      ▼
Requests Fail
```

Deployment verification should therefore distinguish:

- startup success,
- readiness,
- runtime health.

---

# 22. Deployment Observability

A deployment should generate sufficient telemetry to determine:

- what changed,
- when,
- where,
- by whom,
- whether it succeeded,
- what happened afterward.

Deployment events should be correlated with application and infrastructure telemetry.

---

# 23. Deployment Failure

Deployment can fail before the new state becomes active.

Possible causes include:

- artifact problems,
- configuration errors,
- capacity constraints,
- infrastructure failure,
- permission failure,
- dependency failure.

The deployment system should leave the environment in a known and recoverable state where possible.

---

# 24. Partial Deployment

Distributed systems can experience partial deployment.

For example:

```text
Service A → New Version
Service B → Old Version
Service C → Old Version
```

The architecture must tolerate this state where rolling or progressive deployment is used.

---

# 25. Deployment Atomicity

Not every deployment can be atomic.

For a distributed system, changing every component simultaneously may be:

- expensive,
- impractical,
- unnecessary.

Instead, the system should be designed so intermediate states remain safe.

---

# 26. Deployment Safety

A deployment should preserve important system invariants.

Examples include:

- data integrity,
- authentication,
- authorization,
- service availability,
- compatibility.

A deployment mechanism should not trade correctness for convenience.

---

# 27. Capacity During Deployment

Deployment can temporarily increase resource requirements.

Examples include:

- old and new versions running together,
- additional infrastructure,
- duplicated workloads,
- migration jobs.

Capacity planning should account for deployment behavior.

---

# 28. Connection Draining

When replacing running instances, existing connections may need to complete.

Safe deployment may require:

```text
Stop New Traffic
       │
       ▼
Drain Existing Work
       │
       ▼
Terminate Instance
```

The exact mechanism depends on the protocol and architecture.

---

# 29. Graceful Shutdown

Applications should support controlled shutdown where practical.

This allows:

- request completion,
- message processing,
- resource cleanup,
- connection closure.

Abrupt termination can create avoidable failures during deployment.

---

# 30. Deployment Timeout

Deployment operations should have explicit timeout behavior.

A deployment should not remain indefinitely in an ambiguous state.

Timeouts should trigger:

- investigation,
- retry,
- rollback,
- recovery,

according to the deployment strategy.

---

# 31. Retry

Retries may be appropriate for transient failures.

However:

> **Retries should only be used when the operation is safe to retry.**

Repeatedly retrying a non-idempotent operation can make the failure worse.

---

# 32. Rollback

Where possible, deployments should support rollback to a known previous state.

Rollback may involve:

- previous artifact,
- previous configuration,
- traffic reversal.

Rollback should be tested rather than assumed.

---

# 33. Rollback Limitations

Rollback may not be possible when:

- data has been transformed irreversibly,
- external side effects occurred,
- schemas are incompatible,
- dependencies changed permanently.

Deployment planning should identify these limitations before production.

---

# 34. Forward Recovery

When rollback is unsafe, recovery may require moving forward.

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
   │
   ▼
Recovered State
```

The recovery strategy should be understood before high-risk changes are deployed.

---

# 35. Deployment Verification

After deployment, verify both technical and business health where appropriate.

Technical signals may include:

- latency,
- error rate,
- resource utilization.

Business signals may include:

- successful transactions,
- conversion,
- order completion,
- customer-visible errors.

The correct signals depend on the system.

---

# 36. Automatic Rollback

Automatic rollback may be appropriate when:

- failure signals are reliable,
- rollback is safe,
- thresholds are well understood.

Automatic rollback should not be introduced merely because it is technically possible.

Incorrect detection can create deployment oscillation.

---

# 37. Deployment Oscillation

If a system repeatedly performs:

```text
Deploy
  ↓
Rollback
  ↓
Deploy
  ↓
Rollback
```

the deployment controller may create additional instability.

Automatic recovery mechanisms should therefore include:

- clear thresholds,
- stabilization periods,
- bounded retries,
- human escalation.

---

# 38. Deployment and Observability

Observability is part of deployment safety.

Without appropriate telemetry:

```text
Deployment
    │
    ▼
Unknown Outcome
```

With appropriate telemetry:

```text
Deployment
    │
    ▼
Observed Behavior
    │
    ├── Healthy
    └── Unhealthy
```

Deployment confidence depends on the quality of these signals.

---

# 39. Deployment and Security

Deployment can change the system's security posture.

Examples include:

- new network exposure,
- new permissions,
- new secrets,
- new endpoints,
- new service identities.

Security-impacting deployment changes should receive appropriate validation.

---

# 40. Deployment and Reliability

Deployment is itself a reliability event.

A deployment can introduce:

- capacity changes,
- dependency changes,
- performance regressions,
- availability failures.

Reliability signals should therefore be part of deployment verification.

---

# 41. Deployment and Data

Application deployment and data deployment are often coupled but have different failure characteristics.

A deployment process should explicitly consider:

```text
Application State
       +
Data State
```

rather than assuming they can always be reversed together.

---

# 42. Deployment and Infrastructure

Infrastructure changes may be required before application deployment.

Examples include:

- compute capacity,
- networking,
- storage,
- permissions,
- service dependencies.

Infrastructure dependencies should be explicitly modeled.

---

# 43. Drift

Manual production changes can create divergence between:

```text
Declared State
```

and:

```text
Actual State
```

Deployment processes should detect and reduce meaningful drift.

---

# 44. Manual Deployment

Manual deployment should be minimized for repeatable production systems.

If manual actions are unavoidable, they should be:

- documented,
- authorized,
- attributable,
- observable,
- reconciled with the source of truth.

---

# 45. Emergency Deployment

Emergency deployments may bypass some normal process.

They should still preserve, as much as practical:

- artifact identity,
- authorization,
- traceability,
- testing,
- observability,
- recovery planning.

Emergency should mean accelerated—not invisible.

---

# 46. Deployment Auditability

The organization should be able to determine:

- what was deployed,
- where,
- when,
- by whom,
- from which artifact,
- using which release.

This evidence should remain available for an appropriate period.

---

# 47. Deployment Concurrency

Multiple deployments may occur simultaneously.

The system should define whether deployments are:

- serialized,
- independently scoped,
- coordinated.

Uncontrolled concurrent deployments can create ambiguous production state.

---

# 48. Deployment Locks

Some systems require deployment serialization.

A lock may prevent incompatible changes from being deployed simultaneously.

However, locks should not become a substitute for designing safe concurrent change.

---

# 49. Deployment Ownership

Production deployments should have clear ownership.

Ownership should cover:

- deployment execution,
- health verification,
- failure response,
- rollback/recovery.

Automation does not eliminate responsibility.

---

# 50. Minimum Engineering Requirements

Every production project should:

- [ ] Deploy identifiable artifacts.
- [ ] Define the target environment explicitly.
- [ ] Use authorized deployment identities.
- [ ] Apply least privilege.
- [ ] Make deployments repeatable where practical.
- [ ] Define deployment ordering for dependent changes.
- [ ] Verify application health after deployment.
- [ ] Produce deployment telemetry.
- [ ] Preserve deployment traceability.
- [ ] Define failure handling.
- [ ] Define rollback or forward-recovery behavior.
- [ ] Account for backward compatibility where multiple versions may coexist.
- [ ] Minimize undocumented manual production changes.

Higher-risk systems may additionally require:

- [ ] Canary deployment.
- [ ] Blue/green deployment.
- [ ] Progressive delivery.
- [ ] Automated health-based rollback.
- [ ] Deployment risk assessment.
- [ ] Strong deployment isolation.
- [ ] Formal deployment approvals.
- [ ] Automated drift detection.
- [ ] Deployment concurrency controls.
- [ ] Disaster recovery procedures for deployment infrastructure.

---

# Relationship With Other Delivery Standards

This standard works with:

- `07-delivery/README.md`
- `07-delivery/source-control.md`
- `07-delivery/ci.md`
- `07-delivery/build-and-artifacts.md`
- `07-delivery/release-management.md`
- `07-delivery/progressive-delivery.md`

It also connects directly with:

- `05-reliability/`
- `06-security/`
- `08-observability/`
- `09-platform-and-infrastructure/`
- `11-operational-readiness/`

---

# What This Standard Is Not

This standard does not prescribe:

- Kubernetes,
- Helm,
- Terraform,
- Argo CD,
- Flux,
- GitHub Actions,
- Jenkins,
- AWS,
- Azure,
- GCP.

Those are implementation choices.

The engineering contract is:

> **A deployment must move the environment toward a known target state through a controlled, observable, and appropriately recoverable process.**

---

# Final Principle

> **Deployment is not the moment we execute a command. It is the controlled transition of a live system from one state to another. Mature deployment engineering makes that transition observable, repeatable, attributable, and safe enough to recover when reality differs from expectation.**
