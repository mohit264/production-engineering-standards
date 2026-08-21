# Security

> Security is an architectural property of the system, not a checklist applied immediately before production.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Security Engineering

**Applies To:** All production systems, with depth determined by system tier, data sensitivity, threat exposure, and regulatory requirements

---

# Purpose

Security engineering exists to reduce the likelihood and impact of:

- unauthorized access,
- data exposure,
- data modification,
- privilege misuse,
- malicious activity,
- supply-chain compromise,
- service disruption,
- credential compromise.

Security should be considered throughout the system lifecycle rather than treated as a final compliance activity.

This domain establishes the security engineering baseline and provides the structure for the detailed security standards that follow.

---

# Engineering Principle

> **Security should be designed into system boundaries, identities, data flows, dependencies, and operational processes rather than added as a final control layer.**

---

# 1. Security Begins With the System

Security decisions depend on understanding:

- what the system does,
- what data it handles,
- who interacts with it,
- what it trusts,
- what it depends on,
- what could go wrong.

Security controls should therefore follow the architecture.

A control without an understood threat model may provide little meaningful protection.

---

# 2. Security Is Risk Management

Security engineering is not the elimination of every conceivable threat.

The objective is to understand:

```text
Threat
   │
   ▼
Vulnerability
   │
   ▼
Likelihood
   │
   ▼
Impact
   │
   ▼
Risk
   │
   ▼
Control
   │
   ▼
Residual Risk
```

The appropriate level of security investment depends on the consequences of compromise.

---

# 3. Security by System Tier

Security requirements should be proportional to:

- system criticality,
- data sensitivity,
- internet exposure,
- threat environment,
- regulatory obligations,
- business impact.

A low-risk internal tool should not necessarily require the same security architecture as a public financial platform.

The governance domain defines the authoritative system-tier model.

---

# 4. Security Boundaries

Every production system should identify important security boundaries.

Examples include:

- internet → application,
- user → application,
- application → database,
- service → service,
- application → third party,
- production → non-production,
- human → privileged infrastructure.

For each boundary, the project should understand:

- who is allowed across it,
- what is trusted,
- what must be authenticated,
- what must be authorized,
- what must be validated.

---

# 5. Trust Is Not Assumed

A component should not be trusted merely because it exists inside:

- a private network,
- a cloud account,
- a cluster,
- a corporate network.

Trust should be based on explicit security properties.

For example:

```text
Network Location
      ≠
Identity
      ≠
Authorization
```

Network placement alone should not become the security model.

---

# 6. Identity

Identity answers:

> Who or what is making this request?

Identities may represent:

- users,
- services,
- workloads,
- administrators,
- automated processes,
- external systems.

Identity should be explicit where authentication and authorization matter.

---

# 7. Authentication

Authentication establishes identity.

Examples include:

- passwords,
- certificates,
- tokens,
- workload identities,
- federated identity,
- multi-factor authentication.

The appropriate mechanism depends on the threat model and system context.

Authentication should not automatically imply authorization.

---

# 8. Authorization

Authorization answers:

> What is this identity allowed to do?

A request may therefore be:

```text
Authenticated
     │
     ▼
Identity Known
     │
     ▼
Authorization Check
     │
     ├── Allowed
     │
     └── Denied
```

Authorization should be enforced at the appropriate system boundary.

---

# 9. Least Privilege

Identities should receive the minimum permissions required to perform their responsibilities.

This applies to:

- users,
- services,
- workloads,
- CI/CD systems,
- administrators,
- third-party integrations.

Permissions should not become permanently broad simply because they were convenient during development.

---

# 10. Privileged Access

Privileged operations require stronger controls.

The project should identify:

- privileged identities,
- privileged operations,
- administrative boundaries,
- approval requirements where appropriate,
- monitoring requirements.

Privileged access should be limited and auditable.

---

# 11. Service-to-Service Security

Internal service communication should be treated according to actual risk.

The project should consider:

- authentication,
- authorization,
- transport protection,
- service identity,
- credential management,
- network restrictions.

"Internal" should not automatically mean "trusted."

---

# 12. Data Protection

Security must consider data throughout its lifecycle:

```text
Creation
   │
   ▼
Processing
   │
   ▼
Transmission
   │
   ▼
Storage
   │
   ▼
Backup
   │
   ▼
Archival
   │
   ▼
Deletion
```

The data domain defines detailed requirements for data handling.

Security must ensure that appropriate protection exists at each relevant stage.

---

# 13. Data Classification

Important data should be classified according to sensitivity.

Typical categories may include:

- public,
- internal,
- confidential,
- highly sensitive.

The organization should define the authoritative classification model.

Security controls should follow the classification.

---

# 14. Encryption

Where required, sensitive data should be protected:

- in transit,
- at rest,
- during appropriate processing scenarios.

Encryption should not be treated as a substitute for:

- authorization,
- access control,
- secure key management.

---

# 15. Key Management

Cryptographic keys are security-sensitive assets.

The project should understand:

- where keys are stored,
- who can access them,
- how they are rotated,
- how compromise is handled,
- how access is audited.

Application source code should not become a permanent key store.

---

# 16. Secrets Management

Secrets may include:

- passwords,
- API keys,
- access tokens,
- certificates,
- private keys,
- connection credentials.

Secrets should be managed through appropriate secret-management mechanisms.

They should not be embedded casually in:

- source code,
- container images,
- configuration repositories,
- logs,
- documentation.

---

# 17. Credential Lifecycle

Security does not end when a credential is created.

The project should consider:

```text
Create
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
Remove
```

Compromised credentials should have a defined revocation or replacement path.

---

# 18. Input Validation

External input should be treated as untrusted until validated.

Examples include:

- HTTP requests,
- uploaded files,
- messages,
- events,
- query parameters,
- headers,
- external API responses.

Validation should consider both:

- syntactic validity,
- semantic validity.

---

# 19. Output and Response Safety

Security also includes how systems expose information.

The project should consider:

- sensitive error messages,
- stack traces,
- internal identifiers,
- debugging information,
- authorization failures.

Errors should provide useful operational information without unnecessarily exposing sensitive implementation details.

---

# 20. Dependency Security

Production systems depend on:

- libraries,
- frameworks,
- operating systems,
- container images,
- cloud services,
- external APIs,
- build tools.

These dependencies become part of the system's attack surface.

Dependency security should therefore be considered an architectural concern.

---

# 21. Supply Chain Security

The software supply chain should be considered across:

```text
Source
  │
  ▼
Dependencies
  │
  ▼
Build
  │
  ▼
Artifact
  │
  ▼
Deployment
  │
  ▼
Runtime
```

Security controls should protect important boundaries in this chain.

Detailed requirements belong in the relevant delivery and security standards.

---

# 22. Vulnerability Management

Projects should have an appropriate mechanism for identifying and addressing vulnerabilities.

The process should consider:

- severity,
- exploitability,
- exposure,
- affected assets,
- available remediation,
- business impact.

Not every vulnerability requires identical response time.

Risk should drive prioritization.

---

# 23. Security Patching

Important security patches should have a defined path from discovery to deployment.

The process should account for:

- emergency patches,
- compatibility,
- testing,
- rollback,
- production exposure.

Security should not depend on an informal agreement to "patch sometime."

---

# 24. Secure Development

Development practices should reduce common security weaknesses.

Where relevant, teams should consider:

- secure coding practices,
- dependency management,
- static analysis,
- secret scanning,
- code review,
- security testing.

Controls should be proportionate to system risk.

---

# 25. Security Testing

Security testing may include:

- automated security testing,
- dependency scanning,
- static analysis,
- dynamic testing,
- penetration testing,
- threat-model validation.

The required depth should follow system risk.

---

# 26. Threat Modeling

Important systems should identify plausible threats before implementation becomes difficult to change.

A threat model should consider:

- assets,
- actors,
- trust boundaries,
- attack paths,
- abuse cases,
- mitigations.

A threat model does not need to predict every possible attack.

Its purpose is to expose important security assumptions.

---

# 27. Abuse Cases

Functional requirements describe what users should be able to do.

Security engineering should also ask:

> What happens if someone intentionally uses the capability in an unintended way?

Examples include:

- excessive requests,
- privilege escalation,
- unauthorized data access,
- malformed input,
- replay,
- enumeration.

---

# 28. Security Logging

Important security events should be observable.

Examples include:

- authentication failures,
- privilege changes,
- administrative actions,
- sensitive-data access,
- credential changes,
- policy violations.

Logging should support detection and investigation without unnecessarily exposing sensitive data.

---

# 29. Security Monitoring

Security-relevant signals should be monitored according to risk.

The objective is to detect meaningful suspicious behavior rather than simply generate large volumes of logs.

Security monitoring should therefore be connected to:

- known threats,
- important assets,
- privileged operations,
- critical boundaries.

---

# 30. Auditability

Important security actions should be attributable.

The system should be able to determine, where appropriate:

- who performed an action,
- what was performed,
- when it occurred,
- against which resource,
- whether it succeeded.

Shared administrative identities should be avoided where individual accountability is required.

---

# 31. Incident Response

A production system should have an appropriate process for responding to security incidents.

The process should address:

- detection,
- containment,
- investigation,
- eradication,
- recovery,
- communication,
- lessons learned.

Security incidents may require different procedures from ordinary reliability incidents.

---

# 32. Compromise Assumptions

Security architecture should consider that some controls may eventually fail.

Examples:

- credentials may leak,
- endpoints may be compromised,
- dependencies may be vulnerable,
- users may make mistakes.

The architecture should limit the blast radius where practical.

---

# 33. Blast Radius

Security controls should limit how far a compromise can propagate.

Examples include:

- isolated workloads,
- scoped credentials,
- separate accounts,
- network boundaries,
- authorization boundaries,
- independent failure domains.

A single compromised identity should not automatically provide unrestricted access to the entire environment.

---

# 34. Security and Availability

Security controls can affect reliability.

For example:

- authentication service outage,
- certificate expiration,
- unavailable key-management service,
- security policy misconfiguration.

Security architecture should therefore consider failure behavior.

A security control that becomes a single point of failure may create a different operational risk.

---

# 35. Security and Performance

Security controls may introduce:

- CPU overhead,
- latency,
- additional network calls,
- storage requirements.

Performance implications should be understood for critical paths.

Security should not be removed merely because it has a cost.

Instead, the trade-off should be understood and engineered.

---

# 36. Security and Developer Experience

Security controls should be designed so that secure behavior is the easiest practical path for developers.

Examples include:

- standardized authentication libraries,
- managed secrets,
- secure CI/CD defaults,
- approved dependency sources,
- reusable security controls.

The objective is:

> **Make the secure path the convenient path.**

---

# 37. Security Exceptions

Exceptions may be necessary.

A security exception should be:

- explicit,
- justified,
- risk-assessed,
- owned,
- time-bounded where appropriate.

"Legacy system" should not become a permanent explanation for an unexamined security risk.

---

# 38. Security Evidence

For systems with significant security requirements, teams should be able to demonstrate appropriate evidence.

Examples include:

- threat models,
- access reviews,
- vulnerability findings,
- penetration tests,
- security test results,
- audit logs,
- remediation records.

Evidence requirements should follow system tier and applicable obligations.

---

# 39. Privacy and Compliance

Security and privacy are related but not identical.

Security asks:

> How do we protect the system and information?

Privacy asks:

> How should personal information be collected, used, retained, shared, and deleted?

Applicable privacy and regulatory requirements should be identified separately.

Detailed privacy requirements belong in the appropriate data and governance standards.

---

# 40. Security Across Environments

Security controls should consider:

- development,
- test,
- staging,
- production.

Production should not be weakened merely to make lower environments convenient.

At the same time, non-production environments may require different controls according to their data and exposure.

---

# 41. Production Data in Non-Production

Using production data outside production introduces additional risk.

Where production data is required for testing, the project should consider:

- minimization,
- masking,
- anonymization,
- access restrictions,
- retention,
- deletion.

The safest production dataset is often the dataset that was never copied.

---

# 42. Security Architecture Review

Important systems should undergo an appropriate security architecture review.

The review should consider:

- identities,
- trust boundaries,
- data flows,
- external exposure,
- privileged access,
- dependencies,
- threat model,
- security controls.

The review depth should match the system's risk.

---

# 43. Security During Change

Security assumptions must be reconsidered when changing:

- authentication,
- authorization,
- network boundaries,
- data flows,
- dependencies,
- deployment architecture,
- infrastructure.

A change that appears functionally small may have significant security consequences.

---

# 44. Security Lifecycle

Security should follow the system through:

```text
Design
  │
  ▼
Build
  │
  ▼
Test
  │
  ▼
Deploy
  │
  ▼
Operate
  │
  ▼
Monitor
  │
  ▼
Respond
  │
  ▼
Improve
```

---

# 45. Minimum Engineering Requirements

Every production project should:

- [ ] Identify important security boundaries.
- [ ] Identify important identities and trust relationships.
- [ ] Apply appropriate authentication and authorization.
- [ ] Apply least privilege to important identities.
- [ ] Protect sensitive data appropriately.
- [ ] Manage secrets securely.
- [ ] Identify important dependencies and their security implications.
- [ ] Provide appropriate security logging and monitoring.
- [ ] Have an appropriate vulnerability-management process.
- [ ] Define an appropriate security incident response path.
- [ ] Identify and manage significant security risks.

Higher-risk systems may additionally require:

- [ ] Formal threat modeling.
- [ ] Security architecture review.
- [ ] Penetration testing.
- [ ] Advanced supply-chain controls.
- [ ] Stronger privileged-access controls.
- [ ] Formal security monitoring.
- [ ] Regular access reviews.
- [ ] Independent security assessment.

---

# Relationship With Other Domains

Security interacts with almost every engineering domain.

### Governance

Defines:

- risk classification,
- system tiers,
- compliance obligations,
- security ownership.

### Architecture

Defines:

- trust boundaries,
- system boundaries,
- dependency relationships.

### Data

Defines:

- data classification,
- protection,
- retention,
- deletion,
- recovery.

### Delivery

Defines:

- source protection,
- build security,
- artifact security,
- deployment controls.

### Reliability

Defines:

- security-control failure behavior,
- recovery,
- availability implications.

### Observability

Defines:

- security signals,
- detection,
- auditability.

---

# Domain Standards

The security domain should be expanded through focused standards rather than a single giant checklist.

Expected areas include:

- `identity-and-access.md`
- `secrets-and-key-management.md`
- `application-security.md`
- `supply-chain-security.md`
- `security-testing.md`
- `security-monitoring.md`
- `incident-response.md`
- `privacy-and-data-protection.md`

Additional standards may be introduced when justified by the organization's technology and risk profile.

---

# Final Principle

> **Good security does not mean that a system can never be compromised. It means the system makes compromise harder, limits what a compromise can reach, detects important abuse, and provides a controlled path to recovery.**
