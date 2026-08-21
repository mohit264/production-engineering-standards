# Delivery

> Delivery is the engineering discipline of turning a change in source into a trustworthy production state.

---

**Status:** Engineering Governance

**Version:** 1.0

**Classification:** Delivery Engineering

---

# Purpose

This directory defines the engineering standards for designing, building, validating, releasing, deploying, and promoting software into production.

The purpose of delivery engineering is not simply to automate deployments.

It is to establish a trustworthy path between:

```text
Engineering Change
        │
        ▼
Production State
```

That path must preserve:

- correctness,
- security,
- traceability,
- repeatability,
- reliability,
- reversibility.

---

# Delivery Philosophy

The central principle is:

> **A production deployment should be the controlled promotion of a known change, not an act of manual execution.**

A mature delivery system should make it possible to answer:

- What changed?
- Who changed it?
- Which source produced the artifact?
- Which tests passed?
- Which artifact was deployed?
- Where was it deployed?
- Who approved the promotion where approval is required?
- Can we reproduce the artifact?
- Can we safely recover?

---

# Delivery Is a System

Delivery is not synonymous with CI/CD.

The delivery system includes:

```text
Source
  │
  ▼
Change Validation
  │
  ▼
Build
  │
  ▼
Artifact
  │
  ▼
Verification
  │
  ▼
Release
  │
  ▼
Deployment
  │
  ▼
Production
  │
  ▼
Observation
  │
  ▼
Promotion / Rollback
```

Each stage creates evidence for the next.

---

# 1. Source of Truth

The delivery process should begin from a controlled source of truth.

Source control should provide:

- version history,
- authorship,
- change traceability,
- review history,
- reproducible references.

A production system should be able to identify the source revision from which it was produced.

---

# 2. Change Validation

Changes should be validated before they become production candidates.

Validation may include:

- formatting,
- linting,
- static analysis,
- unit tests,
- integration tests,
- security checks,
- dependency checks,
- policy validation.

The exact checks should reflect system risk.

---

# 3. Build

The build process converts source into a deployable artifact.

A build should ideally be:

- reproducible,
- automated,
- traceable,
- isolated,
- deterministic where practical.

The build environment itself is part of the delivery security boundary.

---

# 4. Artifact

The artifact is the object that will eventually run in production.

Examples include:

- container images,
- packages,
- binaries,
- serverless bundles,
- infrastructure plans.

The artifact should have an unambiguous identity.

For example:

```text
Source Revision
      │
      ▼
Build
      │
      ▼
Artifact Digest / Version
```

Production should run the identified artifact rather than rebuilding implicitly during deployment.

---

# 5. Artifact Promotion

A mature delivery system separates:

```text
Build
```

from:

```text
Promotion
```

The same verified artifact should be promoted across environments where practical.

Conceptually:

```text
Build Once
    │
    ▼
Verified Artifact
    │
    ├── Development
    │
    ├── Staging
    │
    └── Production
```

This reduces the possibility that different environments run subtly different builds.

---

# 6. Environments

Environments should have explicit purposes.

For example:

- development,
- testing,
- staging,
- production.

The exact environment model may differ by organization.

The important principle is that environment boundaries should be intentional rather than accidental.

---

# 7. Environment Configuration

Configuration should be separated from the application artifact where appropriate.

This allows the same artifact to operate under different environment-specific settings without rebuilding it.

However:

> Configuration is part of system behavior and must therefore be versioned, validated, and governed appropriately.

---

# 8. Continuous Integration

Continuous integration provides rapid feedback about changes.

A healthy CI system should:

- validate changes automatically,
- run appropriate tests,
- produce build artifacts,
- expose failures clearly,
- prevent known-invalid changes from progressing.

CI should optimize for trustworthy feedback, not merely fast pipelines.

---

# 9. Continuous Delivery

Continuous delivery means that changes can reach a production-ready state through an automated, repeatable process.

This does not necessarily mean:

```text
Every Change → Automatic Production
```

It means:

```text
Every Validated Change
        │
        ▼
Can Reach Production Through
a Controlled Repeatable Path
```

---

# 10. Continuous Deployment

Continuous deployment is a further step in which validated changes are automatically deployed to production.

Whether this is appropriate depends on:

- risk,
- test coverage,
- business requirements,
- operational maturity,
- rollback capability.

Automation should follow confidence, not fashion.

---

# 11. Release

A release is the intentional act of making a change available to users or production systems.

Release management should distinguish between:

- artifact creation,
- deployment,
- exposure,
- activation.

This distinction enables safer rollout strategies.

---

# 12. Deployment

Deployment changes the production state.

A deployment mechanism should be:

- repeatable,
- observable,
- auditable,
- reversible where practical.

Manual production changes should be minimized because they reduce reproducibility and traceability.

---

# 13. Deployment Strategies

Different systems may require different rollout strategies.

Examples include:

- rolling deployment,
- blue/green deployment,
- canary deployment,
- progressive delivery,
- feature flags.

The correct strategy depends on:

- failure impact,
- traffic characteristics,
- rollback capability,
- architecture,
- operational maturity.

---

# 14. Progressive Delivery

High-risk changes may be exposed gradually.

Conceptually:

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
Full Rollout
```

The important principle is that increased exposure should follow evidence.

---

# 15. Feature Flags

Feature flags can separate:

```text
Code Deployment
```

from:

```text
Feature Activation
```

This can reduce deployment risk.

However, feature flags introduce operational complexity and should have:

- owners,
- lifecycle expectations,
- cleanup plans,
- appropriate access controls.

---

# 16. Database Changes

Database changes require special consideration because application rollback does not automatically imply database rollback.

Delivery standards should consider:

- backward compatibility,
- schema evolution,
- data migration,
- rollback strategy,
- migration ordering.

Database changes should be treated as part of the production change.

---

# 17. Backward Compatibility

During rolling or progressive deployments, multiple application versions may coexist.

Therefore, interfaces and schemas may need to support:

```text
Version N
    +
Version N+1
```

simultaneously.

Compatibility requirements should be explicit.

---

# 18. Infrastructure Delivery

Infrastructure changes should use controlled delivery mechanisms where practical.

Examples include:

- infrastructure-as-code,
- declarative configuration,
- automated validation,
- planned changes,
- controlled promotion.

Infrastructure should not depend on undocumented manual configuration.

---

# 19. Policy Validation

Delivery pipelines should validate important policies before production.

Examples include:

- security policies,
- infrastructure policies,
- configuration rules,
- compliance requirements,
- resource constraints.

Policy validation should happen as early as practical.

---

# 20. Security in Delivery

The delivery system is part of the security boundary.

Delivery should therefore incorporate:

- identity controls,
- least privilege,
- secret management,
- dependency validation,
- artifact integrity,
- provenance,
- auditability.

A compromised delivery pipeline can compromise every system it deploys.

---

# 21. Supply Chain Integrity

Production artifacts should have sufficient provenance to establish:

```text
Where did this artifact come from?
```

Where appropriate, delivery should preserve relationships between:

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
Deployment
```

---

# 22. Deployment Identity

Deployments should be attributable.

The organization should be able to determine:

- which system initiated the deployment,
- which identity authorized it,
- which artifact was deployed,
- when it occurred.

Avoid anonymous production changes.

---

# 23. Approvals

Some production changes may require explicit approval.

Approval requirements should be based on risk.

Approvals should not become meaningless ceremony.

The objective is to introduce deliberate control where automated validation alone is insufficient.

---

# 24. Separation of Duties

High-impact changes may require separation between:

- author,
- reviewer,
- deployer,
- approver.

The exact model should reflect risk and organizational context.

---

# 25. Immutable Production Artifacts

Where practical, production should deploy immutable artifacts.

This reduces the possibility that:

```text
Artifact A
```

quietly becomes:

```text
Artifact A + Manual Modification
```

after validation.

---

# 26. Deployment Observability

Every important deployment should produce sufficient telemetry to answer:

- when it started,
- what changed,
- where it went,
- whether it succeeded,
- what happened afterward.

Deployment events should integrate with operational observability.

---

# 27. Health Verification

Deployment completion does not necessarily mean successful deployment.

After deployment, verify appropriate signals such as:

- application health,
- error rate,
- latency,
- resource behavior,
- dependency health,
- business metrics.

A deployment should be considered successful based on system behavior, not merely command completion.

---

# 28. Automated Rollback

Where practical, the delivery system should be capable of reversing unsafe changes.

Rollback may mean:

- restoring a previous artifact,
- reverting traffic,
- disabling a feature,
- restoring configuration.

Rollback strategy must consider data and state changes.

---

# 29. Rollback Is Not Always Possible

Some changes cannot safely be rolled back.

Examples include:

- irreversible data transformations,
- external side effects,
- destructive migrations.

For these changes, delivery must instead emphasize:

- validation,
- compatibility,
- backups,
- forward recovery,
- controlled rollout.

---

# 30. Failure Handling

A delivery system should define what happens when a stage fails.

For example:

```text
Build Failure
     │
     ▼
No Release

Test Failure
     │
     ▼
No Promotion

Deployment Failure
     │
     ▼
Contain / Roll Back / Recover
```

Failure should produce a known state rather than an ambiguous one.

---

# 31. Pipeline Reliability

The delivery system itself is production infrastructure for engineering.

It should therefore consider:

- availability,
- dependency failures,
- credential failures,
- queueing,
- concurrency,
- recovery.

A broken delivery pipeline can become a significant operational bottleneck.

---

# 32. Pipeline Security

CI/CD systems should protect against:

- unauthorized workflow changes,
- credential theft,
- malicious dependencies,
- artifact substitution,
- privilege escalation,
- untrusted code execution.

Pipeline permissions should follow least privilege.

---

# 33. Secrets in Pipelines

Sensitive credentials should not be embedded directly into:

- source code,
- pipeline definitions,
- logs,
- artifacts.

Pipelines should obtain secrets through approved mechanisms.

---

# 34. Build Isolation

Builds may execute untrusted or semi-trusted source code.

Build environments should therefore be appropriately isolated.

The required isolation depends on:

- contributor model,
- repository exposure,
- build privileges,
- artifact sensitivity.

---

# 35. Dependency Reproducibility

Builds should use controlled dependency resolution where practical.

Important considerations include:

- version constraints,
- lock files,
- package integrity,
- registry availability,
- dependency provenance.

The goal is to reduce unexpected changes entering the build.

---

# 36. Delivery Traceability

A production system should allow the organization to trace:

```text
Production
    │
    ▼
Deployment
    │
    ▼
Artifact
    │
    ▼
Build
    │
    ▼
Source Change
```

This traceability is fundamental to debugging, security investigation, and rollback.

---

# 37. Change Failure Rate

Delivery maturity should be measured not merely by deployment frequency.

Useful measures may include:

- deployment frequency,
- lead time,
- change failure rate,
- recovery time,
- rollback frequency.

Metrics should be interpreted in context.

---

# 38. Delivery and Reliability

Fast delivery without reliability is not engineering maturity.

Delivery systems should enable teams to make changes:

- frequently,
- safely,
- observably,
- reversibly where possible.

The objective is controlled change velocity.

---

# 39. Delivery and Security

Security should be integrated into the delivery lifecycle.

Examples include:

```text
Source
  │
  ├── Security Analysis
  │
  ▼
Build
  │
  ├── Dependency Validation
  │
  ▼
Artifact
  │
  ├── Artifact Verification
  │
  ▼
Deployment
  │
  └── Runtime Verification
```

Security should not be a final manual gate added immediately before production.

---

# 40. Delivery and Observability

A deployment without observability creates uncertainty.

Important deployments should correlate with:

- logs,
- metrics,
- traces,
- alerts,
- business indicators.

This allows teams to determine whether a change actually caused an observed behavior change.

---

# 41. Delivery and Operations

The delivery process must align with operational readiness.

Before deploying significant changes, consider:

- monitoring,
- alerting,
- runbooks,
- capacity,
- rollback,
- support readiness.

A technically deployable system is not necessarily operationally ready.

---

# 42. Emergency Changes

Production incidents may require emergency changes.

Emergency delivery should still preserve as much as practical:

- traceability,
- authorization,
- testing,
- observability,
- rollback capability.

Emergency does not mean uncontrolled.

---

# 43. Manual Changes

Manual production changes should be minimized.

When unavoidable, they should be:

- authorized,
- recorded,
- observable,
- reversible where possible,
- reconciled back into the source of truth.

Otherwise, the production environment gradually diverges from the engineering system.

---

# 44. Drift

Production state should remain aligned with its declared source of truth.

Drift may occur when:

- configuration is changed manually,
- infrastructure is modified outside the pipeline,
- artifacts are patched in place,
- undocumented operational changes accumulate.

Delivery processes should detect and reduce meaningful drift.

---

# 45. Delivery Governance

Delivery standards should define:

- what must be automated,
- what requires approval,
- what evidence is required,
- what can block promotion,
- how exceptions are handled.

Governance should control risk without creating unnecessary bureaucracy.

---

# 46. Minimum Engineering Requirements

Every production system should:

- [ ] Have a controlled source of truth.
- [ ] Automatically validate important changes.
- [ ] Produce identifiable build artifacts.
- [ ] Maintain source-to-artifact traceability.
- [ ] Deploy known artifacts.
- [ ] Make production changes attributable.
- [ ] Validate deployment health.
- [ ] Define failure handling.
- [ ] Have a rollback or recovery strategy appropriate to the change.
- [ ] Protect delivery credentials.
- [ ] Minimize undocumented manual production changes.
- [ ] Integrate security validation into delivery.
- [ ] Integrate deployment events with observability.

Higher-risk systems may additionally require:

- [ ] Progressive delivery.
- [ ] Automated rollback.
- [ ] Artifact signing and verification.
- [ ] Formal provenance.
- [ ] Separation of duties.
- [ ] Independent production approval.
- [ ] Automated policy enforcement.
- [ ] Deployment risk scoring.
- [ ] Disaster recovery of the delivery system.
- [ ] Formal release evidence.

---

# Relationship With Other Engineering Domains

This domain works with:

- `01-governance/`
- `02-engineering-maturity/`
- `03-architecture/`
- `04-data/`
- `05-reliability/`
- `06-security/`
- `08-observability/`
- `09-platform-and-infrastructure/`
- `10-cost-and-economics/`
- `11-operational-readiness/`

Delivery should not be designed independently from these domains.

---

# What This Directory Is Not

This directory is not:

- a Jenkins manual,
- a GitHub Actions manual,
- a Kubernetes deployment guide,
- a cloud-provider deployment guide,
- a collection of pipeline YAML files.

Those belong in implementation-specific documentation.

This directory defines the engineering principles and expectations that delivery implementations should satisfy.

---

# Final Principle

> **The measure of delivery maturity is not how many deployment pipelines exist. It is how confidently the organization can move a known change into production, understand its effects, and recover when the change behaves differently from what was expected.**
