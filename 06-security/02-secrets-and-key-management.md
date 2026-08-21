# Secrets and Key Management

> A secret is not merely configuration. It is a security credential with a lifecycle, an owner, and a blast radius.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Security Engineering

**Applies To:** All production systems, with controls proportional to system tier, data sensitivity, and credential risk

---

# Purpose

Production systems depend on security-sensitive material such as:

- passwords,
- API keys,
- access tokens,
- certificates,
- private keys,
- encryption keys,
- database credentials,
- signing keys.

Poor management of this material can allow an attacker to bypass otherwise strong security controls.

This standard establishes the engineering baseline for:

- secret storage,
- secret distribution,
- cryptographic key management,
- rotation,
- revocation,
- access control,
- exposure prevention,
- recovery.

---

# Engineering Principle

> **Secrets and cryptographic keys should have an explicit lifecycle, controlled access, limited scope, and a practical path to rotation and revocation.**

---

# 1. Secret vs Key

These terms should not be treated as interchangeable.

A **secret** is sensitive information used to authenticate or authorize access.

Examples:

- database password,
- API token,
- OAuth client secret.

A **cryptographic key** is material used by a cryptographic algorithm.

Examples:

- encryption key,
- signing key,
- private key.

Both require strong lifecycle management, but cryptographic keys often have additional requirements.

---

# 2. Secret Lifecycle

Every important secret should have a lifecycle:

```text
Create
  │
  ▼
Store
  │
  ▼
Distribute
  │
  ▼
Use
  │
  ▼
Rotate
  │
  ▼
Revoke
  │
  ▼
Destroy
```

A secret without a defined lifecycle becomes long-lived security debt.

---

# 3. Secret Ownership

Every important secret should have an owner.

The owner should know:

- why the secret exists,
- which system uses it,
- who can access it,
- when it should be rotated,
- how it can be revoked.

Unknown secrets should be treated as a security risk.

---

# 4. Secrets Must Not Live in Source Code

Credentials should not be embedded in:

- application source,
- configuration committed to Git,
- infrastructure code,
- scripts,
- documentation.

Examples of unsafe patterns include:

```text
DB_PASSWORD = "production-password"
```

or:

```text
API_KEY = "abcdef..."
```

Once committed to source control, assume the secret may have been exposed.

Removing it from the latest revision does not necessarily remove it from historical copies.

---

# 5. Secrets Must Not Be Treated as Ordinary Configuration

Configuration describes system behavior.

Secrets provide access or cryptographic capability.

For example:

```text
PORT=8080
```

is ordinary configuration.

Whereas:

```text
DATABASE_PASSWORD=...
```

is security-sensitive material.

The two should therefore have different handling requirements.

---

# 6. Centralized Secret Management

Production secrets should generally be stored in an appropriate secret-management system rather than distributed through ad-hoc mechanisms.

A suitable system should provide capabilities such as:

- access control,
- encryption,
- auditing,
- versioning,
- rotation support,
- controlled retrieval.

The exact technology should be selected according to the platform and organizational requirements.

---

# 7. Secret Distribution

A secret-management system is useful only if secrets can be delivered securely to the workload.

The architecture should consider:

- how the workload authenticates,
- how the secret is retrieved,
- when it is retrieved,
- where it exists in memory,
- whether it is persisted locally.

The preferred design is to avoid unnecessary copies.

---

# 8. Workload Identity

Where supported, workloads should authenticate to the secret-management system using workload identity rather than another long-lived secret.

This avoids creating:

```text
Secret A
   │
   ▼
Access Secret Store
   │
   ▼
Secret B
```

The system should instead establish workload identity through an appropriate trusted mechanism.

---

# 9. Secret Scope

Secrets should have the smallest practical scope.

For example:

```text
Service A
   │
   └── Credential A
          │
          └── Database A
```

is generally preferable to:

```text
Every Service
      │
      ▼
Global Credential
      │
      ▼
Everything
```

Scope limits the blast radius of compromise.

---

# 10. Environment Separation

Production credentials should be separated from:

- development,
- test,
- staging.

A development workload should not normally possess production credentials.

This protects production even when a lower environment is compromised.

---

# 11. Secret Access Control

Access to secrets should follow least privilege.

The system should be able to answer:

- which identity can read the secret,
- which identity can modify it,
- which identity can rotate it,
- which identity can delete it.

Read access and administrative access should not automatically be equivalent.

---

# 12. Secret Access Auditing

Important secret access should be auditable.

Where appropriate, capture:

- identity,
- secret or secret class,
- action,
- timestamp,
- result.

Do not log the secret value itself.

---

# 13. Secret Exposure Through Logs

Secrets must not appear in:

- application logs,
- error messages,
- stack traces,
- telemetry,
- metrics,
- tracing attributes.

Particular care should be taken with:

- HTTP headers,
- query strings,
- connection strings,
- authorization tokens.

---

# 14. Secret Exposure Through Diagnostics

Debugging mechanisms can accidentally expose secrets.

Examples include:

- environment dumps,
- request dumps,
- memory diagnostics,
- configuration snapshots,
- crash reports.

Production diagnostic mechanisms should therefore be reviewed for secret exposure.

---

# 15. Secret Exposure Through Artifacts

Secrets should not be unintentionally embedded into:

- container images,
- compiled artifacts,
- build logs,
- deployment packages,
- generated configuration files.

The build process should be designed so that secrets do not become permanent artifact contents.

---

# 16. Secret Exposure Through Version Control

Repositories may contain historical copies of accidentally committed secrets.

When a secret is exposed through source control:

1. Treat it as compromised.
2. Revoke or rotate it.
3. Investigate exposure.
4. Remove the secret from the appropriate repository history where required.
5. Determine whether downstream systems also received the secret.

Simply deleting the file is not sufficient.

---

# 17. Rotation

Secrets should have an appropriate rotation strategy.

Rotation may be:

- scheduled,
- event-driven,
- triggered by personnel changes,
- triggered by suspected compromise,
- triggered by system changes.

Rotation frequency should be based on risk and operational feasibility.

---

# 18. Rotation Without Downtime

Rotation should ideally avoid unnecessary service interruption.

A common pattern is:

```text
Credential A
    │
    ▼
Introduce Credential B
    │
    ▼
Consumers transition
    │
    ▼
Credential A revoked
```

The exact implementation depends on the system.

---

# 19. Emergency Rotation

The project should have a practical procedure for rapidly replacing compromised credentials.

Questions include:

- Who can initiate rotation?
- How quickly can it occur?
- How are dependent services updated?
- Can old credentials be revoked immediately?
- How is the incident investigated?

Emergency rotation should be tested for important systems.

---

# 20. Revocation

Rotation and revocation are related but different.

**Rotation** replaces a credential.

**Revocation** invalidates a credential.

The project should understand how quickly compromised credentials can be made unusable.

---

# 21. Credential Expiration

Where appropriate, credentials should have an expiration mechanism.

Long-lived credentials increase the time available for misuse after compromise.

Short-lived credentials can reduce that exposure.

The system should balance:

```text
Security
   │
   +
Operational Complexity
```

---

# 22. Static Credentials

Static credentials should be avoided where an appropriate dynamic identity mechanism exists.

For example, prefer:

```text
Workload Identity
      │
      ▼
Short-Lived Credential
```

over:

```text
Permanent API Key
```

when the platform supports the stronger model.

---

# 23. Certificates

Certificates have their own lifecycle.

The project should consider:

- issuance,
- trust,
- expiration,
- renewal,
- revocation,
- private-key protection.

Certificate expiration should not become an avoidable production outage.

---

# 24. Private Keys

Private keys require stronger protection than ordinary configuration values.

They should:

- have restricted access,
- avoid unnecessary copying,
- be protected at rest,
- have defined rotation procedures,
- be revoked or replaced after compromise.

Private keys should never be committed to source control.

---

# 25. Cryptographic Key Hierarchy

Where encryption is used at scale, keys may be organized hierarchically.

For example:

```text
Root / Master Key
       │
       ▼
Key Encryption Key
       │
       ▼
Data Encryption Key
       │
       ▼
Encrypted Data
```

The exact hierarchy depends on the cryptographic architecture.

The important principle is to avoid unnecessary exposure of highly privileged keys.

---

# 26. Key Separation

Different cryptographic purposes should generally use appropriately separated keys.

For example:

```text
Encryption Key
      ≠
Signing Key
      ≠
Authentication Credential
```

Reusing one key for unrelated purposes increases blast radius and complicates lifecycle management.

---

# 27. Encryption at Rest

Where sensitive data requires encryption at rest, the project should understand:

- what is encrypted,
- which keys are used,
- who can decrypt,
- how keys are protected,
- what happens if keys become unavailable.

Encryption should be implemented using established cryptographic mechanisms.

Custom cryptography should generally be avoided.

---

# 28. Encryption in Transit

Sensitive communication should use appropriate transport protection.

The architecture should consider:

- endpoint authentication,
- certificate validation,
- protocol versions,
- key management,
- trust establishment.

Encryption without authentication may protect confidentiality while failing to establish who is communicating.

---

# 29. Signing Keys

Systems that sign:

- tokens,
- artifacts,
- messages,
- software packages,

should protect signing keys carefully.

A compromised signing key may allow an attacker to create artifacts that appear legitimate.

---

# 30. Key Rotation

Cryptographic keys should have a defined rotation strategy where appropriate.

Rotation should consider:

- existing encrypted data,
- old-key readability,
- new-key usage,
- key versioning,
- rollback,
- revocation.

Key rotation is often more complex than simply generating a new key.

---

# 31. Re-encryption

Changing an encryption key does not necessarily mean every existing object must immediately be decrypted and encrypted again.

Depending on the architecture, systems may support:

```text
Old Key
   │
   ▼
Encrypted Data
   │
   ▼
Key Rewrap / Re-encryption Strategy
   │
   ▼
New Key
```

The appropriate strategy depends on the cryptographic design and data lifecycle.

---

# 32. Key Backup

Important cryptographic keys may require carefully controlled backup.

However, backup creates another copy of the key and therefore another security boundary.

The project should balance:

- recoverability,
- confidentiality,
- availability.

Key backup should not be treated like ordinary file backup.

---

# 33. Key Recovery

The project should determine what happens if:

- the key-management service is unavailable,
- a key is accidentally deleted,
- credentials are lost,
- a recovery event occurs.

For critical systems, key recovery should be tested.

---

# 34. Secret Store Availability

A secret-management system becomes a dependency.

Critical workloads should understand what happens if:

```text
Application
     │
     ▼
Secret Store
     │
     X
   Failure
```

Questions include:

- Are secrets fetched at startup only?
- Are they cached in memory?
- Can the application continue operating temporarily?
- What happens during rotation?

The architecture should make this behavior explicit.

---

# 35. Secret Caching

Caching secrets can improve availability but increases exposure duration.

The project should consider:

- cache lifetime,
- memory exposure,
- rotation propagation,
- revocation behavior.

Caching should be intentional.

---

# 36. Secrets in Containers

Secrets should not be baked permanently into container images.

A container image should remain reusable across environments.

Prefer:

```text
Immutable Image
      +
Runtime Secret Injection
```

rather than:

```text
Production Secret
      │
      ▼
Container Image
```

---

# 37. Secrets in CI/CD

CI/CD systems frequently handle sensitive credentials.

The project should ensure:

- secrets are scoped,
- secrets are masked where possible,
- build logs do not expose them,
- pipelines receive only required credentials,
- credentials do not become build artifacts.

---

# 38. Secret Scanning

Source repositories and build pipelines should use appropriate mechanisms to detect accidental secret exposure.

Scanning should be treated as a preventive control.

It is not a substitute for:

> **Immediate rotation when a real secret is exposed.**

---

# 39. Secret Detection Limitations

Automated scanners may produce:

- false positives,
- false negatives.

They should therefore be combined with:

- secure development practices,
- access controls,
- review,
- monitoring.

No scanner can prove that a repository contains no secrets.

---

# 40. Third-Party Secrets

External integrations may require credentials.

Examples include:

- payment providers,
- messaging services,
- SaaS APIs,
- cloud services.

Third-party credentials should have:

- explicit ownership,
- scoped permissions,
- lifecycle management,
- rotation procedures.

---

# 41. Secret Sharing

Secrets should not be shared casually through:

- chat,
- email,
- tickets,
- documents,
- screenshots.

If a credential must be transferred, use an approved secure mechanism.

---

# 42. Secret Recovery

Recovery procedures should not require engineers to search through:

- personal notes,
- old emails,
- chat messages,
- source repositories.

Important credentials should be recoverable through controlled organizational mechanisms.

---

# 43. Decommissioning

When a service is retired:

- revoke its credentials,
- remove unused keys,
- disable identities,
- remove secret-store entries where appropriate,
- review dependent integrations.

Retired systems should not retain active credentials indefinitely.

---

# 44. Compromise Response

If a secret or key is suspected to be compromised:

```text
Detect
  │
  ▼
Contain
  │
  ▼
Revoke / Rotate
  │
  ▼
Investigate
  │
  ▼
Recover
  │
  ▼
Improve
```

The exact response depends on the credential and potential impact.

---

# 45. Secret Inventory

Important production projects should maintain enough information to answer:

- What important secrets exist?
- Who owns them?
- Which workloads use them?
- Where are they stored?
- How are they rotated?
- How are they revoked?

The inventory need not expose the secret values.

---

# 46. Security Boundaries

Secrets should not cross security boundaries unnecessarily.

For example:

```text
Development
     X
     │
     │ production credential
     X
Production
```

The architecture should minimize unnecessary secret propagation.

---

# 47. Minimum Engineering Requirements

Every production project should:

- [ ] Keep secrets out of source code.
- [ ] Use an appropriate secret-management mechanism.
- [ ] Restrict secret access using least privilege.
- [ ] Separate production secrets from lower environments.
- [ ] Prevent secrets from appearing in logs and telemetry.
- [ ] Define ownership for important secrets.
- [ ] Define rotation and revocation procedures.
- [ ] Protect private keys appropriately.
- [ ] Avoid embedding secrets in immutable artifacts.
- [ ] Provide a response path for exposed credentials.
- [ ] Remove credentials associated with retired systems.

Higher-risk systems may additionally require:

- [ ] Workload identity.
- [ ] Short-lived credentials.
- [ ] Automated rotation.
- [ ] Formal key hierarchy.
- [ ] Hardware-backed key protection where justified.
- [ ] Formal key-recovery procedures.
- [ ] Automated secret scanning.
- [ ] Secret-access monitoring.
- [ ] Tested emergency credential rotation.

---

# Relationship With Other Security Standards

This standard works with:

- `06-security/README.md`
- `06-security/identity-and-access.md`
- `06-security/application-security.md`
- `06-security/supply-chain-security.md`
- `06-security/security-testing.md`
- `06-security/security-monitoring.md`
- `06-security/incident-response.md`

It also connects with:

- `04-data/`
- `07-delivery/`
- `08-observability/`
- `09-platform-and-infrastructure/`
- `11-operational-readiness/`

---

# Final Principle

> **The security of a credential depends not only on keeping its value secret, but also on controlling who can use it, limiting what it can do, knowing when it should change, and being able to revoke it when trust is lost.**
