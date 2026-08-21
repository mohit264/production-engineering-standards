# Build and Artifacts

> A build transforms controlled source into an identifiable artifact that can be independently verified, stored, and promoted.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Delivery Engineering

---

# Purpose

This standard defines how engineering systems should transform source code into deployable artifacts.

The objective is to establish a trustworthy relationship between:

```text
Source
  │
  ▼
Build
  │
  ▼
Artifact
```

The resulting artifact should be:

- identifiable,
- traceable,
- reproducible where practical,
- independently deployable,
- protected from unintended modification.

---

# Engineering Principle

> **Build once, identify what was built, preserve its provenance, and promote that artifact rather than silently rebuilding it.**

---

# 1. What Is a Build?

A build is the controlled transformation of source and its declared inputs into an artifact.

Conceptually:

```text
Source Revision
      +
Declared Dependencies
      +
Build Configuration
      +
Toolchain
      │
      ▼
    Build
      │
      ▼
  Artifact
```

The build process should make these inputs sufficiently visible to understand what produced the artifact.

---

# 2. What Is an Artifact?

An artifact is a deployable output of the build process.

Examples include:

- container images,
- application binaries,
- packages,
- libraries,
- serverless bundles,
- deployment packages,
- infrastructure artifacts.

An artifact should have a stable identity.

---

# 3. Artifact Identity

An artifact should be uniquely identifiable.

Possible identifiers include:

- version,
- digest,
- immutable content hash,
- release identifier.

For immutable artifacts, the strongest identity is generally derived from their content.

For example:

```text
Artifact
   │
   ▼
Content Digest
   │
   ▼
Exact Artifact Identity
```

The organization should be able to distinguish one artifact from another without ambiguity.

---

# 4. Source-to-Artifact Traceability

Every production artifact should be traceable to the source revision that produced it.

The relationship should be discoverable:

```text
Production
    │
    ▼
Artifact
    │
    ▼
Build
    │
    ▼
Source Revision
```

This is essential for:

- incident investigation,
- rollback,
- vulnerability response,
- debugging,
- compliance evidence.

---

# 5. Build Inputs

Build inputs may include:

- source,
- dependencies,
- compiler,
- runtime,
- build tools,
- configuration,
- generated sources,
- environment information.

Important inputs should be explicit rather than hidden inside mutable infrastructure.

---

# 6. Dependency Resolution

Dependencies should be resolved predictably.

Where practical, builds should use:

- explicit versions,
- lock files,
- controlled registries,
- verified package integrity.

The goal is to prevent an unrelated dependency change from silently changing the production artifact.

---

# 7. Toolchain Versioning

The build toolchain can influence the resulting artifact.

Important components may include:

- compiler,
- language runtime,
- package manager,
- container builder,
- operating system,
- build plugins.

Toolchain versions should therefore be controlled where reproducibility or security requires it.

---

# 8. Reproducibility

A reproducible build produces equivalent output when given equivalent inputs.

Conceptually:

```text
Same Inputs
     │
     ▼
   Build
     │
     ▼
Equivalent Artifact
```

Complete bit-for-bit reproducibility may not always be practical.

Where it is not achieved, the reasons and remaining sources of variation should be understood.

---

# 9. Determinism

Builds should avoid unnecessary dependence on:

- current time,
- random values,
- machine-specific paths,
- mutable external resources,
- uncontrolled network state.

Deterministic builds make failures easier to investigate.

---

# 10. Build Environment

Build environments should be controlled according to risk.

Important characteristics may include:

- known operating-system version,
- known toolchain,
- isolated workspace,
- controlled network access,
- controlled credentials,
- clean build state.

A developer workstation should not be treated as the authoritative production build environment.

---

# 11. Clean Builds

Build systems should support clean builds where practical.

A clean build starts without relying on undocumented state from previous executions.

This helps identify hidden dependencies on:

- local files,
- caches,
- generated artifacts,
- installed tools,
- environment variables.

---

# 12. Caching

Build caches can significantly improve build performance.

However, caches introduce potential sources of:

- stale content,
- incorrect reuse,
- non-reproducibility,
- hidden state.

Caches should therefore be treated as optimization mechanisms rather than authoritative sources of correctness.

---

# 13. Build Isolation

Builds execute source code and dependency code.

The build environment should therefore have appropriate isolation.

Isolation requirements depend on:

- repository trust model,
- contributor access,
- build privileges,
- production credentials,
- artifact sensitivity.

---

# 14. Build Credentials

Builds should receive only the credentials they actually require.

A build that only compiles source should not automatically have access to:

- production databases,
- production infrastructure,
- unrestricted cloud administration.

Credential boundaries should minimize blast radius.

---

# 15. Network Access

Builds may require network access to:

- dependency registries,
- source repositories,
- artifact stores,
- external services.

Network access should be restricted where practical.

Unrestricted network access increases the potential impact of compromised dependencies or build scripts.

---

# 16. Build Provenance

Build provenance records how an artifact was produced.

Useful provenance may identify:

- source revision,
- builder,
- build time,
- toolchain,
- dependencies,
- build configuration,
- resulting artifact.

Provenance allows consumers of an artifact to ask:

> Where did this artifact come from?

---

# 17. Software Bill of Materials

Where appropriate, production artifacts should have a Software Bill of Materials (SBOM) or equivalent dependency inventory.

The objective is to identify what the artifact contains.

This becomes particularly valuable when:

- a dependency vulnerability is discovered,
- an emergency security response is required,
- licensing needs to be evaluated.

---

# 18. Artifact Integrity

Artifacts should be protected against unintended modification.

Possible mechanisms include:

- content digests,
- checksums,
- signatures,
- immutable registries,
- access controls.

The required mechanism depends on risk.

---

# 19. Artifact Signing

High-risk environments may require artifacts to be cryptographically signed.

Signing can provide evidence that:

- the artifact originated from an expected producer,
- the artifact was not modified after signing.

Signing should be combined with verification.

A signature that nobody verifies provides limited protection.

---

# 20. Artifact Storage

Artifacts should be stored in an appropriate artifact repository.

The repository should provide suitable:

- access control,
- retention,
- availability,
- integrity,
- auditability.

Source control should not automatically become the artifact repository.

---

# 21. Artifact Immutability

Once an artifact has been released or promoted, it should not be silently modified.

Prefer:

```text
Artifact A
```

remaining:

```text
Artifact A
```

rather than:

```text
Artifact A
      │
      ▼
Modified Artifact A
```

If a change is required, produce a new artifact.

---

# 22. Mutable Tags

Human-readable tags can be convenient:

```text
application:1.4
```

But a tag may be mutable depending on the artifact system.

Production deployment should therefore prefer an immutable identity such as:

```text
application@<digest>
```

where supported.

---

# 23. Build Once

The preferred delivery model is:

```text
Source
  │
  ▼
Build
  │
  ▼
Artifact
  │
  ├───────────────┐
  ▼               ▼
Test            Security
  │               │
  └───────┬───────┘
          ▼
      Verified
       Artifact
          │
          ▼
      Promotion
```

The artifact should not be rebuilt merely because it is moving to another environment.

---

# 24. Environment Independence

Where practical, the artifact should remain identical across environments.

Environment-specific differences should come from controlled configuration rather than rebuilding the software.

For example:

```text
Same Artifact
      │
      ├── Development Configuration
      ├── Staging Configuration
      └── Production Configuration
```

---

# 25. Build vs Deployment

Build and deployment are separate responsibilities.

Build answers:

> What artifact should exist?

Deployment answers:

> Where and how should that artifact run?

Keeping these concerns separate improves:

- traceability,
- rollback,
- testing,
- promotion,
- incident investigation.

---

# 26. Artifact Promotion

Promotion should move an already-created artifact through controlled environments.

For example:

```text
Build
 │
 ▼
Candidate
 │
 ▼
Staging
 │
 ▼
Production
```

Promotion should not implicitly create a different artifact.

---

# 27. Artifact Retention

Retention should balance:

- recovery needs,
- investigation needs,
- storage cost,
- regulatory requirements.

Important production artifacts should remain available for an appropriate period.

Retention should be deliberate rather than accidental.

---

# 28. Artifact Garbage Collection

Unused artifacts may eventually be removed.

Before deletion, consider whether an artifact is needed for:

- rollback,
- incident investigation,
- vulnerability analysis,
- compliance,
- disaster recovery.

Deletion policies should preserve required operational history.

---

# 29. Failed Builds

Failed builds should not be treated as production artifacts.

However, their evidence may still be useful for:

- debugging,
- failure analysis,
- trend analysis.

Logs and reports should have appropriate retention.

---

# 30. Build Metadata

A production artifact should expose or reference sufficient metadata to establish its origin.

Useful metadata may include:

- version,
- source revision,
- build identifier,
- build timestamp,
- dependency information,
- provenance.

Metadata should not expose sensitive credentials or secrets.

---

# 31. Artifact Validation

Before promotion, artifacts should be validated for:

- integrity,
- expected structure,
- required metadata,
- security findings,
- compatibility.

Artifact validation is distinct from source validation.

The source may have passed CI while the packaging process itself introduced an error.

---

# 32. Artifact Scanning

Depending on system risk, artifacts may be scanned for:

- known vulnerabilities,
- malicious components,
- secrets,
- policy violations,
- configuration weaknesses.

Scanning should occur at appropriate points in the delivery lifecycle.

---

# 33. Vulnerability Response

When a vulnerability is discovered in a dependency, provenance should allow the organization to determine:

```text
Which artifacts contain it?
```

and:

```text
Which production systems are running those artifacts?
```

This is one of the major operational benefits of artifact traceability.

---

# 34. Build Failure vs Artifact Failure

These are different failure classes.

### Build failure

The system cannot produce the artifact.

```text
Source
  │
  ▼
Build
  X
```

### Artifact failure

The artifact exists but fails validation or behaves incorrectly.

```text
Source
  │
  ▼
Build
  │
  ▼
Artifact
  X
```

The delivery process should distinguish these cases.

---

# 35. Build Security

Build infrastructure is part of the software supply chain.

A compromised builder may produce a malicious artifact even when the source repository is legitimate.

Therefore:

- builders require access control,
- build environments require appropriate isolation,
- build credentials require protection,
- provenance should be preserved,
- artifacts should be verified.

---

# 36. Artifact Repository Security

Artifact repositories should enforce appropriate:

- authentication,
- authorization,
- write controls,
- deletion controls,
- audit logging.

The ability to replace a production artifact can be equivalent to the ability to modify production software.

---

# 37. Artifact Naming

Artifact naming should make important identity information discoverable.

Names should avoid ambiguity.

Where appropriate, use:

- application/component,
- version,
- platform,
- architecture.

Immutable identity should still be preserved independently of human-readable naming.

---

# 38. Multi-Platform Builds

Applications supporting multiple platforms may produce different artifacts for:

- CPU architectures,
- operating systems,
- runtime environments.

The delivery system should preserve the relationship between the logical release and each platform-specific artifact.

---

# 39. Infrastructure Artifacts

Infrastructure delivery may also produce artifacts.

Examples include:

- packaged modules,
- deployment bundles,
- rendered manifests,
- infrastructure plans.

These should receive appropriate traceability and validation.

---

# 40. Database Migration Artifacts

Database migration packages or scripts should be version controlled and traceable to the application release where appropriate.

Migration artifacts should be validated independently of application binaries.

---

# 41. Generated Artifacts

Generated artifacts should have a clearly defined source of truth.

If an artifact can be deterministically recreated from source, the generated artifact does not necessarily need to become a second source of truth.

---

# 42. Build Reproducibility vs Build Availability

A build system may prioritize different properties depending on the environment.

For example:

```text
Reproducibility
       vs
Build Speed
       vs
Build Availability
```

These trade-offs should be explicit.

Caching and distributed builders can improve speed without becoming hidden correctness dependencies.

---

# 43. Delivery Evidence

The build system should retain enough evidence to answer:

- What source was built?
- Which builder built it?
- When?
- With which important dependencies?
- What artifact was produced?
- Which checks passed?
- Where is the artifact stored?

This evidence should survive long enough to support operational needs.

---

# 44. Recovery

Build and artifact systems should have recovery considerations.

Potential failures include:

- artifact registry outage,
- corrupted artifact,
- deleted artifact,
- compromised builder,
- lost build metadata.

Critical systems should define appropriate recovery mechanisms.

---

# 45. Build and Cost

Build architecture has economic consequences.

Consider:

- build frequency,
- compute time,
- concurrency,
- cache efficiency,
- storage,
- artifact retention.

Optimization should not compromise the integrity or reproducibility required by the system.

---

# 46. Minimum Engineering Requirements

Every production project should:

- [ ] Produce identifiable deployable artifacts.
- [ ] Trace artifacts to source revisions.
- [ ] Control important build inputs.
- [ ] Protect build credentials.
- [ ] Store production artifacts in an appropriate repository.
- [ ] Preserve artifact integrity.
- [ ] Prevent silent modification of released artifacts.
- [ ] Retain appropriate production artifacts.
- [ ] Separate build from deployment.
- [ ] Promote known artifacts rather than implicitly rebuilding them.
- [ ] Preserve sufficient build evidence.
- [ ] Define appropriate recovery for critical build infrastructure.

Higher-risk systems may additionally require:

- [ ] Reproducible builds.
- [ ] Artifact signing.
- [ ] Signature verification.
- [ ] Formal build provenance.
- [ ] SBOM generation.
- [ ] Isolated/ephemeral builders.
- [ ] Hermetic builds.
- [ ] Independent artifact verification.
- [ ] Strong artifact immutability guarantees.
- [ ] Recovery procedures for compromised builders.

---

# Relationship With Other Delivery Standards

This standard works with:

- `07-delivery/README.md`
- `07-delivery/source-control.md`
- `07-delivery/ci.md`
- `07-delivery/release-management.md`
- `07-delivery/deployment.md`
- `07-delivery/progressive-delivery.md`

It also connects directly with:

- `06-security/`
- `05-reliability/`
- `08-observability/`
- `09-platform-and-infrastructure/`

---

# What This Standard Is Not

This standard does not prescribe:

- Docker,
- container registries,
- Maven,
- npm,
- NuGet,
- Bazel,
- GitHub Packages,
- AWS ECR,
- Azure Container Registry,
- any specific build platform.

Those are implementation choices.

The engineering contract is:

> **A production deployment must reference a known, identifiable, and appropriately verified artifact whose origin can be traced back to controlled source.**

---

# Final Principle

> **The artifact is the boundary between building software and running software. Once we have created a trustworthy artifact, production should promote that known object—not create another version of it and hope it is equivalent.**
