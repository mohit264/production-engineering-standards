# Source Control

> Source control establishes the historical, reviewable, and reproducible record of engineering change.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Delivery Engineering

---

# Purpose

Source control provides the foundation from which software delivery begins.

It establishes a controlled record of:

- what changed,
- who changed it,
- when it changed,
- why it changed,
- how changes were reviewed,
- which version should be built.

Source control is therefore not merely a place to store code.

It is part of the delivery system's source of truth.

---

# Engineering Principle

> **Every production change should be traceable to an identifiable source revision and an accountable engineering change.**

---

# 1. Source of Truth

The repository should represent the intended state of the software.

Production systems should not depend on undocumented source that exists only:

- on developer machines,
- in shared folders,
- in deployment servers,
- in manually modified artifacts.

If a change matters to production, it should exist in the controlled source of truth.

---

# 2. Version History

Source control should preserve meaningful history.

History should make it possible to understand:

- what changed,
- when it changed,
- who introduced it,
- how it evolved.

History is valuable during:

- debugging,
- incident response,
- security investigations,
- rollback,
- architectural review.

---

# 3. Atomic Changes

Changes should be grouped into coherent units where practical.

A useful change should represent a logical engineering decision rather than an arbitrary collection of unrelated modifications.

This improves:

- reviewability,
- traceability,
- rollback,
- investigation.

---

# 4. Commit Quality

Commits should provide useful engineering history.

A good commit should ideally:

- represent one logical change,
- avoid unrelated modifications,
- be understandable independently,
- preserve a meaningful progression of the work.

The exact commit strategy may differ by team and repository.

---

# 5. Branching

Branches provide isolation between different lines of development.

Branching strategies should be chosen according to:

- team size,
- release model,
- deployment frequency,
- change risk,
- integration requirements.

The organization should avoid adopting a branching strategy merely because it is popular.

---

# 6. Mainline Stability

The primary integration branch should represent a trustworthy state.

Depending on the delivery model, this may mean:

- always buildable,
- continuously tested,
- deployable,
- production-ready.

The exact contract should be explicitly defined by the project.

---

# 7. Code Review

Important changes should receive appropriate review before entering protected production paths.

Review should evaluate more than syntax.

Depending on the change, reviewers may consider:

- correctness,
- architecture,
- security,
- reliability,
- performance,
- operational impact,
- maintainability.

---

# 8. Review Ownership

Code review should involve people with sufficient context to evaluate the change.

Ownership may be based on:

- component ownership,
- domain expertise,
- security sensitivity,
- architectural responsibility.

Reviewers should not become an automatic approval mechanism without understanding the change.

---

# 9. Protected Branches

Important branches should have appropriate protections.

Examples include requiring:

- review,
- passing checks,
- authenticated contributors,
- restricted force pushes,
- controlled merge mechanisms.

Protection should reflect the importance of the branch.

---

# 10. Access Control

Repository access should follow least privilege.

Access should be granted according to what an individual or automation actually needs.

Different capabilities may include:

- read,
- write,
- review,
- merge,
- administrative control.

These should not automatically be equivalent.

---

# 11. Administrative Access

Repository administration can have significant security impact.

Administrative permissions may allow changes to:

- branch protections,
- workflows,
- secrets,
- integrations,
- access control.

Administrative access should therefore be restricted and monitored.

---

# 12. Pull Requests and Change Review

Where pull requests or equivalent mechanisms are used, they should provide evidence of:

- proposed change,
- reviewer decisions,
- automated validation,
- discussion,
- final integration.

The mechanism is less important than the engineering contract it provides.

---

# 13. Automated Validation

Source changes should trigger appropriate automated checks.

Examples include:

- compilation,
- unit tests,
- static analysis,
- security scanning,
- formatting,
- dependency validation.

Automated checks should provide rapid and trustworthy feedback.

---

# 14. Secrets in Source Control

Secrets should not be committed to source repositories.

Examples include:

- passwords,
- API keys,
- access tokens,
- private keys,
- database credentials.

If a secret is accidentally committed, deleting the file is not necessarily sufficient.

The secret should be treated as potentially exposed and handled according to the secret-management standard.

---

# 15. Sensitive Configuration

Configuration containing sensitive information should be separated from source where appropriate.

Source control may contain configuration structure and defaults, but sensitive values should be supplied through approved mechanisms.

---

# 16. Dependency Definitions

Dependencies should be represented in controlled source where appropriate.

This includes:

- dependency manifests,
- lock files,
- version constraints,
- build configuration.

The objective is to make dependency selection reproducible and reviewable.

---

# 17. Build Definitions

Build and delivery definitions should themselves be version controlled.

Examples include:

- build scripts,
- pipeline definitions,
- infrastructure definitions,
- deployment configuration.

The process that creates production should not itself be an undocumented production dependency.

---

# 18. Infrastructure as Code

Where infrastructure is managed declaratively, the desired infrastructure state should be represented in source control.

This provides:

- reviewability,
- history,
- repeatability,
- change traceability.

Manual infrastructure changes should be minimized and reconciled where possible.

---

# 19. Database Migration Source

Database migrations should be version controlled when they are part of application delivery.

A production database change should have a traceable relationship to the software change that requires it.

---

# 20. Generated Files

Generated files should not automatically be committed.

The decision should consider:

- reproducibility,
- build cost,
- source-of-truth clarity,
- deployment requirements.

Generated artifacts should not create ambiguity about which representation is authoritative.

---

# 21. Large Binary Files

Large binary assets should be handled deliberately.

Consider:

- repository size,
- cloning performance,
- versioning requirements,
- artifact storage.

Source control should not become an accidental artifact repository.

---

# 22. Repository Structure

Repositories should make important engineering boundaries discoverable.

Structure should reflect the actual system rather than forcing unrelated components into arbitrary layouts.

Consistency is valuable, but clarity is more important than rigid convention.

---

# 23. Repository Documentation

Repositories should contain enough documentation to allow engineers to understand:

- what the system does,
- how to build it,
- how to test it,
- how to run it,
- how to contribute,
- how to deploy it where appropriate.

Documentation should evolve with the system.

---

# 24. Ownership

Important repositories and components should have identifiable ownership.

Ownership should make it clear who is responsible for:

- reviewing changes,
- maintaining the component,
- responding to failures,
- addressing security issues.

---

# 25. Change Traceability

The delivery chain should preserve:

```text
Change
  │
  ▼
Source Revision
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

This relationship is one of the most important properties of a mature delivery system.

---

# 26. Reproducibility

A source revision should provide enough information to reproduce the intended build as defined by the project's build contract.

Reproducibility may depend on:

- dependency versions,
- toolchain versions,
- build configuration,
- environment inputs.

Where complete reproducibility is impractical, important assumptions should be explicit.

---

# 27. Tags and Releases

Release identifiers should provide a stable reference to important production versions.

Examples include:

- tags,
- release records,
- immutable version identifiers.

The chosen mechanism should allow production state to be mapped back to source.

---

# 28. Force Rewriting History

Rewriting shared history can destroy traceability.

Protected production-related branches should generally restrict operations that can silently replace historical revisions.

Exceptions should be explicit and governed.

---

# 29. Repository Backup and Recovery

Important source repositories should have an appropriate recovery strategy.

The organization should consider:

- repository availability,
- accidental deletion,
- administrative compromise,
- corruption,
- provider outage.

Source control is itself a critical engineering dependency.

---

# 30. Auditability

Important repository actions should be attributable.

This may include:

- access changes,
- administrative changes,
- branch-policy changes,
- workflow changes,
- protected-branch overrides.

Audit requirements should reflect repository criticality.

---

# 31. Source Control and Security

Source control is part of the software supply chain.

Compromise of a repository can affect:

- source,
- dependencies,
- build definitions,
- deployment workflows,
- production credentials,
- artifacts.

Repository security should therefore align with the security standards.

---

# 32. Source Control and Delivery

Source control should provide the controlled input to CI/CD.

The delivery system should know:

```text
Which revision triggered the build?
```

and:

```text
Which revision produced the production artifact?
```

---

# 33. Source Control and Incident Response

During security or production incidents, source history can provide evidence.

Investigations may examine:

- recent changes,
- unusual contributors,
- workflow modifications,
- dependency changes,
- configuration changes,
- access changes.

Source history should therefore remain trustworthy.

---

# 34. Emergency Changes

Emergency production changes should still be captured in source control as soon as practical.

Emergency work should not create a permanent parallel development process outside the repository.

---

# 35. Local Changes vs Production Changes

A developer's local working state is not a production source of truth.

Production should not depend on:

```text
"It works on my machine."
```

The production change should exist in a controlled and reproducible source revision.

---

# 36. Source Control Policy

Each project should explicitly define:

- primary repository,
- protected branches,
- review requirements,
- merge strategy,
- ownership,
- release identification,
- access model.

The policy should be appropriate to the project's risk and delivery model.

---

# 37. Minimum Engineering Requirements

Every production project should:

- [ ] Have a defined source-of-truth repository.
- [ ] Preserve version history.
- [ ] Require appropriate review for important changes.
- [ ] Protect production-related branches or equivalent integration paths.
- [ ] Restrict repository access according to least privilege.
- [ ] Prevent secrets from being intentionally stored in source.
- [ ] Version build and delivery definitions.
- [ ] Preserve source-to-artifact traceability.
- [ ] Identify production releases with stable source references.
- [ ] Define repository ownership.
- [ ] Have an appropriate repository recovery strategy.

Higher-risk systems may additionally require:

- [ ] Strong administrative access controls.
- [ ] Repository audit monitoring.
- [ ] Signed commits or equivalent provenance controls.
- [ ] Signed release tags or artifacts.
- [ ] Independent repository backups.
- [ ] Strong branch protection.
- [ ] Formal change-approval policies.

---

# Relationship With Other Delivery Standards

This standard works with:

- `07-delivery/README.md`
- `07-delivery/ci.md`
- `07-delivery/build-and-artifacts.md`
- `07-delivery/release-management.md`
- `07-delivery/deployment.md`
- `07-delivery/progressive-delivery.md`

It also connects directly with:

- `06-security/`
- `03-architecture/`
- `04-data/`
- `05-reliability/`
- `08-observability/`

---

# What This Standard Is Not

This standard does not prescribe:

- Git workflows,
- GitHub workflows,
- GitLab workflows,
- branching models,
- pull-request tooling,
- specific repository providers.

Those are implementation choices.

The engineering contract is more fundamental:

> **Production must be traceable to controlled source.**

---

# Final Principle

> **Source control is the beginning of production traceability. If we cannot identify the exact engineering change that produced a production state, we have already lost an important part of our ability to trust, debug, secure, and recover that system.**
