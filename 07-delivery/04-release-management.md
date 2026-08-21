# Release Management

> Release management is the discipline of deciding what becomes available to users, when it becomes available, and under what conditions.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Delivery Engineering

---

# Purpose

A build produces an artifact.

A release gives that artifact meaning in the context of the production system.

Release management establishes the controlled relationship between:

```text
Artifact
   │
   ▼
Release
   │
   ▼
Production Availability
```

It defines how organizations:

- identify releases,
- determine release readiness,
- coordinate changes,
- communicate important changes,
- control release exposure,
- manage release risk.

---

# Engineering Principle

> **A release is an intentional decision to make a known change available, not merely the existence of a newly built artifact.**

---

# 1. Artifact vs Release

An artifact and a release are related but different.

### Artifact

The deployable object produced by the build process.

### Release

The intentional decision to make a particular artifact available through the production system.

Therefore:

```text
Build
  │
  ▼
Artifact
  │
  │  may exist without being released
  │
  ▼
Release Decision
  │
  ▼
Production Availability
```

This distinction enables better control over production change.

---

# 2. Release Identity

Every production release should have an unambiguous identity.

A release should be traceable to:

- artifact,
- source revision,
- release metadata,
- deployment event.

The relationship should remain discoverable after the release has completed.

---

# 3. Release Readiness

A release should satisfy the conditions appropriate to its risk.

Readiness may consider:

- required CI checks,
- security validation,
- artifact integrity,
- compatibility,
- database changes,
- operational readiness,
- monitoring,
- rollback or recovery capability.

Not every release requires the same level of ceremony.

---

# 4. Release Candidate

A release candidate is an artifact that has passed the validation required to be considered for release.

Conceptually:

```text
Artifact
   │
   ▼
Validation
   │
   ▼
Release Candidate
   │
   ▼
Release Decision
```

A release candidate should remain identifiable.

---

# 5. Release Decision

The decision to release should be based on evidence appropriate to the system.

Evidence may include:

- test results,
- security results,
- operational readiness,
- compatibility validation,
- business readiness,
- risk assessment.

The objective is not to eliminate uncertainty.

It is to make the remaining uncertainty explicit and acceptable.

---

# 6. Release Frequency

Release frequency should be determined by system needs and engineering capability.

Possible models include:

- scheduled releases,
- continuous releases,
- release trains,
- event-driven releases,
- emergency releases.

There is no universally correct release cadence.

---

# 7. Continuous Delivery

Continuous delivery means that a validated artifact can move through the release process whenever the organization chooses to release it.

This provides:

```text
Validated Artifact
       │
       ▼
Production-Ready State
       │
       ▼
Release When Appropriate
```

Continuous delivery does not require automatic production deployment.

---

# 8. Continuous Deployment

Continuous deployment automatically promotes eligible changes to production.

This is appropriate only when the system has sufficient:

- automated validation,
- observability,
- operational maturity,
- rollback or recovery capability.

Automation should follow confidence.

---

# 9. Release Scope

A release should clearly identify what it changes.

Scope may include:

- application behavior,
- APIs,
- infrastructure,
- configuration,
- database schema,
- data migrations,
- dependencies.

A release should not hide significant changes inside an otherwise generic version number.

---

# 10. Release Notes

Significant releases should communicate meaningful changes.

Release information may include:

- features,
- fixes,
- security changes,
- breaking changes,
- operational changes,
- migration requirements,
- known limitations.

Release notes should serve the people who need to understand the impact of the release.

---

# 11. Breaking Changes

Breaking changes require explicit identification.

Examples include:

- incompatible API changes,
- removed functionality,
- schema incompatibility,
- changed authentication behavior,
- changed operational contracts.

Consumers should receive appropriate notice before a breaking change reaches them.

---

# 12. Compatibility

Release management should consider compatibility across versions.

During rolling deployments, different versions may temporarily coexist.

Therefore, releases should consider:

```text
Current Version
      +
New Version
```

and whether both can safely operate together.

---

# 13. Database Releases

Database changes require special release planning.

Consider:

- schema compatibility,
- migration ordering,
- data transformation,
- application compatibility,
- rollback limitations.

A database migration may create irreversible state even when application deployment itself is reversible.

---

# 14. Release Ordering

When multiple components change together, release ordering should be explicit.

Examples include:

```text
Database
   │
   ▼
Backend
   │
   ▼
Frontend
```

or:

```text
Backend Compatibility
   │
   ▼
Frontend
   │
   ▼
Backend Cleanup
```

The correct ordering depends on the compatibility model.

---

# 15. Release Dependencies

A release may depend on:

- another service,
- database changes,
- infrastructure,
- configuration,
- third-party systems.

Important dependencies should be identified before release.

---

# 16. Configuration Changes

Configuration changes can alter production behavior without changing application code.

Therefore, release management should account for significant configuration changes as production changes.

Configuration should have:

- traceability,
- validation,
- ownership,
- rollback or recovery consideration.

---

# 17. Feature Activation

A feature may be deployed without immediately being exposed to users.

This allows:

```text
Code Deployment
      │
      ▼
Feature Available
      │
      ▼
Feature Activated
```

This separation can reduce release risk.

---

# 18. Feature Flags

Feature flags may control exposure independently from deployment.

They should have:

- clear ownership,
- defined purpose,
- lifecycle expectations,
- access control,
- cleanup expectations.

Permanent accumulation of feature flags increases system complexity.

---

# 19. Progressive Release

High-risk changes may be introduced gradually.

For example:

```text
Release
  │
  ▼
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
Full Availability
```

The release process should define what evidence permits increased exposure.

---

# 20. Release Gates

A release gate establishes a condition that must be satisfied before proceeding.

Examples include:

- required tests passed,
- security scan passed,
- approval obtained,
- monitoring available,
- migration completed.

Gates should correspond to meaningful risk controls.

---

# 21. Approval

Some releases require explicit human approval.

Approval may be appropriate when:

- change risk is high,
- regulatory requirements exist,
- operational impact is significant,
- automated validation cannot establish sufficient confidence.

Approval should not become a ritual disconnected from actual risk.

---

# 22. Separation of Duties

For high-risk releases, it may be appropriate to separate:

- author,
- reviewer,
- approver,
- deployer.

The exact model should reflect the organization's risk profile.

---

# 23. Release Windows

Some systems may require controlled release windows.

Reasons may include:

- business operations,
- support availability,
- maintenance constraints,
- dependency schedules,
- customer impact.

Release windows should be based on actual operational needs.

---

# 24. Emergency Releases

Emergency releases may be required to address:

- security vulnerabilities,
- production incidents,
- critical defects.

Emergency does not mean undocumented.

The organization should preserve as much as practical:

- source traceability,
- testing,
- authorization,
- artifact identity,
- deployment evidence.

---

# 25. Rollback

A release strategy should define how an unsafe release can be reversed where possible.

Possible mechanisms include:

- previous artifact,
- traffic reversal,
- feature deactivation,
- configuration rollback.

Rollback should be validated rather than assumed.

---

# 26. Forward Recovery

Some changes cannot be safely rolled back.

Examples include:

- irreversible data migrations,
- external side effects,
- destructive schema changes.

For these releases, the strategy should emphasize:

- compatibility,
- backups,
- staged migration,
- forward-fix capability,
- recovery procedures.

---

# 27. Release Failure

A release may fail before or after production exposure.

The release process should distinguish:

```text
Release Preparation Failure
```

from:

```text
Deployment Failure
```

and:

```text
Runtime Failure
```

Each may require a different response.

---

# 28. Release Verification

Release completion should not be determined solely by deployment success.

Verification should consider appropriate signals such as:

- service health,
- error rate,
- latency,
- resource behavior,
- dependency health,
- business metrics.

A successful command does not necessarily mean a successful release.

---

# 29. Release Observability

Release events should be visible in the operational observability system.

Useful information includes:

- release identifier,
- artifact identity,
- deployment time,
- environment,
- initiating identity,
- release scope.

This allows teams to correlate behavior changes with releases.

---

# 30. Release Auditability

Important release decisions should be attributable.

The organization should be able to determine:

- what was released,
- when,
- by whom or by which automation,
- from which artifact,
- under which approval or policy.

---

# 31. Release Provenance

Release provenance should connect:

```text
Source
   │
   ▼
Build
   │
   ▼
Artifact
   │
   ▼
Release
   │
   ▼
Deployment
```

This chain is critical for:

- incident investigation,
- vulnerability response,
- rollback,
- operational analysis.

---

# 32. Release Communication

Communication requirements should depend on impact.

Potential audiences include:

- engineering,
- operations,
- support,
- security,
- product,
- customers.

Not every release requires organization-wide communication.

---

# 33. Customer Impact

Releases should consider changes to:

- user behavior,
- performance,
- availability,
- APIs,
- pricing,
- data handling,
- permissions.

Customer-visible impact should not be discovered accidentally after deployment.

---

# 34. Operational Readiness

Before significant releases, verify that operations can support the new state.

Consider:

- dashboards,
- alerts,
- runbooks,
- capacity,
- support procedures,
- recovery procedures.

A release that cannot be operated safely is not production-ready.

---

# 35. Security Readiness

Security-sensitive releases should verify:

- required security checks,
- authorization behavior,
- secret configuration,
- dependency status,
- exposure changes,
- monitoring.

Security should be part of release readiness rather than a separate afterthought.

---

# 36. Release Risk

Release risk can depend on:

- change size,
- architectural scope,
- customer exposure,
- data sensitivity,
- reversibility,
- dependency count,
- operational complexity.

Higher risk should lead to stronger controls.

---

# 37. Release Risk Reduction

Risk can be reduced by:

- smaller changes,
- progressive exposure,
- stronger validation,
- feature flags,
- better observability,
- reversible deployments,
- backward compatibility.

The goal is not merely to assess risk.

It is to reduce it.

---

# 38. Release Batching

Large batches of unrelated changes increase uncertainty.

Where practical, releases should contain coherent changes.

Smaller releases generally improve:

- diagnosis,
- rollback,
- attribution,
- learning.

However, batching may be appropriate when changes are tightly coupled.

---

# 39. Release Frequency and Batch Size

Release frequency and release size are related.

In general:

```text
Smaller Changes
      +
Frequent Releases
      =
Lower Change Attribution Cost
```

This is not an absolute rule.

The architecture and operational model determine the appropriate balance.

---

# 40. Release and Reliability

A release is a controlled modification of production state.

Therefore, release management should align with reliability engineering.

Important questions include:

- What can fail?
- How will we detect it?
- How much exposure occurs before detection?
- Can we reverse it?
- If not, how do we recover?

---

# 41. Release and Security

A release can change the security posture of the system.

Examples include:

- new endpoints,
- new permissions,
- new dependencies,
- new external integrations,
- new infrastructure exposure.

Security impact should therefore be part of release assessment.

---

# 42. Release and Data

Data changes may outlive the release that created them.

Release management should therefore consider:

```text
Application Lifecycle
        vs
Data Lifecycle
```

These are not necessarily the same.

---

# 43. Release and Cost

A release may alter:

- compute consumption,
- storage,
- network traffic,
- third-party usage,
- infrastructure requirements.

Significant cost changes should be identified where practical.

---

# 44. Release Records

Important releases should leave a durable record.

A release record may include:

- release identifier,
- artifact,
- source revision,
- scope,
- validation evidence,
- approval,
- deployment information,
- outcome.

The required level of detail should reflect system risk.

---

# 45. Release Retention

Release records should remain available long enough to support:

- incident investigation,
- rollback,
- auditing,
- vulnerability response,
- operational analysis.

Retention requirements should be explicit.

---

# 46. Release Governance

Each project should define:

- release ownership,
- readiness criteria,
- approval requirements,
- release identification,
- rollback/recovery expectations,
- communication requirements.

Governance should scale with risk.

---

# 47. Exceptions

If a release bypasses a normal control, the exception should be explicit.

It should identify:

- what was bypassed,
- why,
- risk introduced,
- who accepted the risk,
- required follow-up.

Repeated exceptions should trigger process improvement.

---

# 48. Release Metrics

Useful release metrics may include:

- release frequency,
- lead time,
- change failure rate,
- rollback rate,
- release duration,
- time to recovery,
- percentage of releases requiring manual intervention.

Metrics should improve decision-making rather than become vanity targets.

---

# 49. Release Anti-Patterns

Avoid:

- treating every artifact as automatically releasable,
- rebuilding artifacts during release,
- releasing without observable health signals,
- relying entirely on manual approval,
- enormous release batches,
- undocumented emergency releases,
- permanent feature flags,
- assuming rollback is always possible,
- treating deployment success as release success,
- hiding configuration and database changes from release scope.

---

# 50. Minimum Engineering Requirements

Every production project should:

- [ ] Distinguish artifacts from releases.
- [ ] Define release readiness criteria.
- [ ] Give production releases an unambiguous identity.
- [ ] Preserve source-to-artifact-to-release traceability.
- [ ] Identify significant release scope.
- [ ] Validate operational readiness for significant changes.
- [ ] Define appropriate rollback or recovery behavior.
- [ ] Verify production health after release.
- [ ] Record important release events.
- [ ] Make important release actions attributable.
- [ ] Define emergency release handling.

Higher-risk systems may additionally require:

- [ ] Progressive delivery.
- [ ] Automated release gates.
- [ ] Formal approval workflows.
- [ ] Separation of duties.
- [ ] Feature flag governance.
- [ ] Automated rollback.
- [ ] Release risk scoring.
- [ ] Formal release evidence.
- [ ] Customer-impact assessment.
- [ ] Predefined emergency release procedures.

---

# Relationship With Other Delivery Standards

This standard works with:

- `07-delivery/README.md`
- `07-delivery/source-control.md`
- `07-delivery/ci.md`
- `07-delivery/build-and-artifacts.md`
- `07-delivery/deployment.md`
- `07-delivery/progressive-delivery.md`

It also connects directly with:

- `05-reliability/`
- `06-security/`
- `08-observability/`
- `11-operational-readiness/`

---

# What This Standard Is Not

This standard does not prescribe:

- GitHub Releases,
- semantic versioning,
- release trains,
- change-management software,
- manual CAB processes,
- a specific deployment platform.

Those are implementation choices.

The engineering contract is:

> **A release is a deliberate, traceable decision to make a known artifact available under explicitly understood conditions.**

---

# Final Principle

> **Building software creates an artifact. Releasing software creates a production decision. The discipline of release management exists to make that decision deliberate, observable, traceable, and proportional to the risk of the change.**
