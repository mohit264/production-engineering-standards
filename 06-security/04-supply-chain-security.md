# Supply Chain Security

> Production software is built from more than the code written by the team. Every dependency, tool, artifact, and build system becomes part of the trust chain.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Security Engineering

**Applies To:** All production software and its build, dependency, artifact, and deployment supply chain

---

# Purpose

Modern applications are assembled from many external components:

- open-source libraries,
- frameworks,
- container images,
- operating-system packages,
- build tools,
- CI/CD platforms,
- plugins,
- package registries,
- third-party services,
- generated artifacts.

A compromise anywhere in this chain can become a compromise of the production system.

This standard establishes the engineering baseline for understanding, protecting, and monitoring the software supply chain.

---

# Engineering Principle

> **Every component that can influence a production artifact is part of the security boundary of that artifact.**

---

# 1. The Software Supply Chain

A production artifact typically passes through multiple stages:

```text
Source
  │
  ▼
Dependencies
  │
  ▼
Build Environment
  │
  ▼
Build Process
  │
  ▼
Artifact
  │
  ▼
Registry
  │
  ▼
Deployment
  │
  ▼
Runtime
```

Each stage introduces potential trust relationships.

Security must therefore consider the entire chain rather than only application source code.

---

# 2. Source Integrity

The source repository is the starting point of the software supply chain.

Important repositories should have appropriate controls around:

- who can write code,
- who can merge changes,
- who can modify protected branches,
- who can modify build configuration,
- who can modify deployment configuration.

Source control is a security boundary.

---

# 3. Code Review

Important production changes should undergo appropriate review.

Review should consider not only:

> Does the code work?

but also:

> Does this change introduce a new security risk?

Security-sensitive changes may require stronger review depending on system tier.

---

# 4. Branch Protection

Production-bound source should not be freely modifiable by every contributor.

Where appropriate, repositories should enforce:

- protected branches,
- required reviews,
- status checks,
- controlled merge permissions.

The exact controls depend on the organization's development model.

---

# 5. Build Configuration Is Code

Build definitions can change the resulting production artifact.

Examples include:

- CI workflows,
- build scripts,
- dependency manifests,
- container definitions,
- infrastructure definitions.

Therefore:

> Build configuration should receive security protection comparable to application source.

---

# 6. Dependency Management

Applications should maintain explicit knowledge of their dependencies.

Dependencies should be:

- declared,
- versioned,
- reproducible where practical,
- reviewed,
- monitored for vulnerabilities.

Undeclared or unmanaged dependencies increase uncertainty.

---

# 7. Direct and Transitive Dependencies

An application may depend on:

```text
Application
   │
   ├── Library A
   │      │
   │      └── Library C
   │
   └── Library B
          │
          └── Library D
```

Libraries C and D are transitive dependencies.

Security review should consider the complete dependency graph rather than only the libraries explicitly selected by developers.

---

# 8. Dependency Versions

Dependency versions should be controlled intentionally.

Unbounded dependency ranges can introduce unexpected changes.

The project should balance:

- security updates,
- compatibility,
- reproducibility,
- maintenance effort.

---

# 9. Dependency Locking

Where the ecosystem supports dependency lock files, production builds should use them appropriately.

The objective is to make the dependency graph predictable.

For example:

```text
Declared Dependency
        │
        ▼
Resolved Dependency Graph
        │
        ▼
Reproducible Build
```

---

# 10. Vulnerable Dependencies

A vulnerability in a dependency does not automatically mean the application is exploitable.

Risk depends on:

- whether the vulnerable component is actually used,
- exploitability,
- exposure,
- reachable code paths,
- available mitigations,
- business impact.

Vulnerability management should therefore prioritize risk rather than raw scanner counts.

---

# 11. Dependency Updates

Dependencies should have a defined update process.

Updates may be triggered by:

- security advisories,
- compatibility requirements,
- feature needs,
- routine maintenance.

Critical security updates may require accelerated handling.

---

# 12. Abandoned Dependencies

Dependencies that are no longer maintained create increasing risk.

Projects should periodically consider:

- maintenance activity,
- community health,
- security response,
- compatibility,
- availability of alternatives.

A dependency that cannot receive security fixes may become an architectural liability.

---

# 13. Dependency Provenance

Where practical, teams should know:

- where a dependency came from,
- which version was used,
- how it entered the build,
- whether its origin can be verified.

Provenance becomes particularly important for high-risk systems.

---

# 14. Package Registries

Packages should preferably be obtained from trusted and controlled registries.

The project should consider:

- registry trust,
- package ownership,
- authentication,
- package integrity,
- availability.

Blindly consuming arbitrary package sources increases supply-chain risk.

---

# 15. Dependency Confusion

Organizations should protect against situations where a malicious package is substituted for an intended internal or trusted dependency.

Controls may include:

- controlled registries,
- package-source restrictions,
- namespace ownership,
- dependency policies.

The specific control depends on the package ecosystem.

---

# 16. Typosquatting

Attackers may create packages with names similar to legitimate dependencies.

For example:

```text
legitimate-package
legitimate_packge
legitimate-package2
```

Dependency selection should therefore be deliberate rather than based solely on name similarity.

---

# 17. Package Integrity

Where supported, package integrity mechanisms should be used.

Examples include:

- checksums,
- signatures,
- trusted registries,
- verified provenance.

The objective is to ensure:

```text
Expected Package
       │
       ▼
Actual Package
```

rather than assuming they are identical.

---

# 18. Container Images

Container images are software artifacts and therefore part of the supply chain.

The project should consider:

- image source,
- base image,
- package contents,
- image provenance,
- vulnerability status,
- image immutability.

---

# 19. Base Images

Base images introduce dependencies that may not be visible in application source.

They may contain:

- operating-system packages,
- system libraries,
- utilities,
- certificates.

Base images should therefore be selected and maintained deliberately.

---

# 20. Minimal Images

Where practical, production images should contain only what is required to run the workload.

Reducing unnecessary components can reduce:

- attack surface,
- vulnerability count,
- image size,
- maintenance burden.

Minimal does not automatically mean secure; functionality and operational requirements still matter.

---

# 21. Image Provenance

Production systems should know which source and build process produced an image.

For example:

```text
Source Commit
     │
     ▼
Build
     │
     ▼
Image Digest
```

This creates traceability between source and deployed artifact.

---

# 22. Image Digests

Where appropriate, immutable artifact references should be preferred over mutable tags for critical production deployments.

Conceptually:

```text
my-application:latest
```

does not uniquely identify an artifact.

Whereas an immutable digest identifies a specific artifact.

---

# 23. Artifact Integrity

Production artifacts should have integrity protection appropriate to risk.

The system should be able to establish:

- what artifact was built,
- when it was built,
- which source produced it,
- which build process produced it.

---

# 24. Build Environment Security

The build environment is highly privileged.

It may have access to:

- source code,
- dependencies,
- signing credentials,
- deployment credentials,
- artifact registries.

A compromised build system can therefore compromise production without directly attacking production.

---

# 25. CI/CD as a Security Boundary

CI/CD should be treated as production security infrastructure.

Important controls include:

- least-privilege credentials,
- protected workflows,
- controlled runners,
- auditability,
- secret protection,
- restricted deployment permissions.

---

# 26. Build Isolation

Where appropriate, builds should be isolated from unrelated workloads.

This can reduce the risk that:

```text
Compromised Build
      │
      ▼
Other Build / Environment
```

The required isolation depends on the threat model.

---

# 27. Build Reproducibility

Important production builds should be reproducible where practical.

Reproducibility improves:

- investigation,
- auditability,
- artifact verification,
- incident response.

The exact level of reproducibility depends on the technology ecosystem.

---

# 28. Build Inputs

Builds should have explicit inputs.

These may include:

- source revision,
- dependencies,
- compiler,
- base image,
- build configuration.

Unexpected external inputs can make artifacts difficult to trust or reproduce.

---

# 29. Network Access During Builds

Build environments should not receive unrestricted network access without justification.

Uncontrolled network access can allow:

- unexpected dependencies,
- malicious downloads,
- data exfiltration,
- build-time attacks.

Network restrictions should be balanced against legitimate build requirements.

---

# 30. Build Secrets

Build systems should receive only the secrets they require.

Secrets should not be:

- permanently embedded into artifacts,
- exposed in logs,
- available to every build,
- accessible to untrusted pull requests unless explicitly justified.

---

# 31. Pull Request Security

Code contributed by untrusted sources can create a dangerous situation when CI executes that code with privileged credentials.

The project should carefully consider:

- untrusted pull requests,
- forked repositories,
- workflow permissions,
- secret availability,
- privileged runners.

The principle is:

> Untrusted code should not automatically receive trusted credentials.

---

# 32. Artifact Registry

Artifacts should be stored in controlled repositories or registries.

The registry should provide appropriate:

- access control,
- integrity,
- retention,
- auditing,
- lifecycle management.

---

# 33. Artifact Immutability

Production artifacts should ideally be immutable.

Once an artifact is released:

```text
Artifact A
```

should not silently become:

```text
Different Artifact A
```

without changing its identity.

Immutability improves traceability and rollback.

---

# 34. Artifact Promotion

Where practical, the same artifact should move through environments rather than being rebuilt separately for each environment.

Conceptually:

```text
Build Once
    │
    ▼
Artifact
    │
    ├── Test
    │
    ├── Staging
    │
    └── Production
```

This reduces differences between tested and deployed artifacts.

---

# 35. Deployment Provenance

Production systems should be able to answer:

- Which artifact is running?
- Which source revision produced it?
- Which build produced it?
- Who or what promoted it?
- When was it deployed?

This is essential for incident investigation.

---

# 36. Software Bill of Materials

For systems where justified, maintain a machine-readable inventory of software components contained in an artifact.

An SBOM can help answer:

> What software is actually inside this production artifact?

This becomes valuable when a vulnerability is discovered in a widely used component.

---

# 37. SBOM Limitations

An SBOM is an inventory.

It does not automatically establish that software is secure.

It helps with:

- visibility,
- vulnerability response,
- dependency analysis,
- provenance.

It does not replace:

- testing,
- secure development,
- access control,
- threat modeling.

---

# 38. Artifact Signing

Where appropriate, artifacts should be cryptographically signed.

Signing can establish:

```text
Artifact
   │
   ▼
Signature
   │
   ▼
Trusted Producer
```

The verification process must also protect the trust root and signing keys.

---

# 39. Signing Key Protection

Artifact-signing keys are highly sensitive.

Compromise may allow attackers to produce artifacts that appear legitimate.

Signing keys should therefore receive appropriate:

- access control,
- protection,
- rotation,
- auditing,
- recovery procedures.

---

# 40. Verification

Signing is useful only if signatures are verified at the appropriate trust boundary.

Possible verification points include:

- artifact registry,
- deployment system,
- runtime admission layer.

The exact architecture depends on the platform.

---

# 41. Third-Party Build Actions and Plugins

Build systems often depend on:

- plugins,
- actions,
- extensions,
- scripts.

These components can execute with significant privileges.

They should therefore be treated as dependencies rather than harmless configuration.

---

# 42. Build Toolchain

The toolchain itself is part of the supply chain.

Examples include:

- compilers,
- interpreters,
- package managers,
- container builders,
- deployment tools.

Critical systems should consider the integrity and lifecycle of these components.

---

# 43. Compromised Dependency Response

When a dependency is compromised:

```text
Identify
   │
   ▼
Determine Exposure
   │
   ▼
Identify Affected Artifacts
   │
   ▼
Remediate
   │
   ▼
Rebuild
   │
   ▼
Redeploy
```

The ability to quickly identify affected artifacts is a major reason to maintain provenance and dependency inventories.

---

# 44. Vulnerability Response

When a vulnerability is announced, the project should determine:

- whether the affected component exists,
- which versions are deployed,
- whether vulnerable functionality is reachable,
- what mitigation exists,
- whether rebuild or redeployment is required.

The objective is to move from:

```text
"We might be affected."
```

to:

```text
"We know exactly where we are affected."
```

---

# 45. Dependency Risk

Not all dependencies carry equal risk.

Risk may depend on:

- privilege,
- network exposure,
- data access,
- code execution,
- maintenance status,
- update frequency.

Critical dependencies should receive proportionally stronger scrutiny.

---

# 46. Supply Chain Monitoring

The project should monitor relevant sources for:

- vulnerability disclosures,
- compromised packages,
- revoked certificates,
- malicious releases,
- abandoned dependencies,
- security advisories.

Monitoring should focus on components actually used by the system.

---

# 47. Developer Machines

Developer environments can influence production software.

Risks include:

- compromised credentials,
- malicious local tools,
- dependency manipulation,
- source-code theft.

The organization should establish appropriate endpoint and development-environment security controls.

---

# 48. Third-Party Contributors

External contributors may interact with the software supply chain.

The project should define appropriate boundaries around:

- source access,
- CI execution,
- credentials,
- deployment permissions.

Contribution should not automatically imply production trust.

---

# 49. Emergency Dependency Replacement

Critical vulnerabilities may require replacing a dependency quickly.

The project should know:

- how to identify affected components,
- how to select an alternative,
- how to test compatibility,
- how to rebuild,
- how to deploy safely.

Emergency dependency replacement is easier when the normal build process is reproducible.

---

# 50. Supply Chain and Incident Response

Supply-chain incidents should integrate with the organization's security incident process.

Potential actions include:

- artifact quarantine,
- credential rotation,
- deployment rollback,
- dependency replacement,
- forensic analysis,
- downstream notification.

---

# 51. Minimum Engineering Requirements

Every production project should:

- [ ] Know its direct dependencies.
- [ ] Track important transitive dependencies.
- [ ] Use controlled dependency sources.
- [ ] Protect source repositories.
- [ ] Protect CI/CD credentials.
- [ ] Treat build configuration as security-sensitive code.
- [ ] Protect production build pipelines.
- [ ] Know which artifact is deployed.
- [ ] Prefer immutable production artifacts.
- [ ] Maintain appropriate vulnerability-management processes.
- [ ] Protect container and package supply chains.
- [ ] Have a response process for compromised dependencies.

Higher-risk systems may additionally require:

- [ ] SBOM generation.
- [ ] Artifact signing.
- [ ] Signature verification.
- [ ] Strong provenance guarantees.
- [ ] Reproducible builds.
- [ ] Isolated build environments.
- [ ] Restricted build networking.
- [ ] Automated dependency monitoring.
- [ ] Formal supply-chain threat modeling.
- [ ] Trusted build infrastructure.

---

# Relationship With Other Security Standards

This standard works with:

- `06-security/README.md`
- `06-security/identity-and-access.md`
- `06-security/secrets-and-key-management.md`
- `06-security/application-security.md`
- `06-security/security-testing.md`
- `06-security/security-monitoring.md`
- `06-security/incident-response.md`

It also connects directly with:

- `07-delivery/`
- `03-architecture/`
- `05-reliability/`
- `08-observability/`
- `09-platform-and-infrastructure/`

---

# Final Principle

> **You cannot secure the production application while blindly trusting the process that builds it. The source, dependencies, build system, artifacts, and deployment path collectively form the software supply chain—and the entire chain must be treated as part of the security boundary.**
