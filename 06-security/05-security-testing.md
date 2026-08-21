# Security Testing

> Security controls are assumptions until they are tested.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Security Engineering

**Applies To:** All production systems, with testing depth proportional to system tier, exposure, data sensitivity, and threat profile

---

# Purpose

Security testing provides evidence that security properties expected by the architecture and engineering standards actually hold in the running system.

Security testing should identify:

- vulnerabilities,
- broken authorization,
- insecure configurations,
- unintended trust paths,
- exposed secrets,
- vulnerable dependencies,
- unsafe application behavior,
- exploitable business logic.

The objective is not to produce a large number of scanner findings.

The objective is to establish confidence that important security boundaries behave as designed.

---

# Engineering Principle

> **Security testing should attempt to prove that important security assumptions hold—and deliberately attempt to break them when they do not.**

---

# 1. Security Testing Is Not One Test

Security testing exists at multiple layers.

```text
Source
  │
  ├── Static Analysis
  │
  ├── Dependency Analysis
  │
  ├── Secret Detection
  │
  ▼
Build Artifact
  │
  ├── Artifact Scanning
  │
  └── Configuration Validation
  │
  ▼
Running System
  │
  ├── Dynamic Testing
  ├── Authorization Testing
  ├── API Testing
  └── Penetration Testing
```

No single technique provides complete coverage.

---

# 2. Security Testing and Risk

Testing depth should correspond to risk.

Consider:

- system exposure,
- data sensitivity,
- privilege level,
- business impact,
- threat model,
- change frequency.

A public payment system and an internal reporting utility should not necessarily have identical security-testing programs.

---

# 3. Security Requirements Become Test Cases

Important security requirements should be testable.

For example:

> A customer can access only resources belonging to their tenant.

should produce tests such as:

```text
Customer A → Tenant A → ALLOWED

Customer A → Tenant B → DENIED
```

A requirement that cannot be tested is difficult to verify.

---

# 4. Positive and Negative Testing

Security testing must include both.

### Positive

Verify that legitimate behavior works.

### Negative

Verify that prohibited behavior fails.

For example:

```text
Authorized User
      │
      ▼
Protected Resource
      │
      ▼
ALLOWED


Unauthorized User
      │
      ▼
Protected Resource
      │
      ▼
DENIED
```

Negative testing is particularly important for authorization.

---

# 5. Security Unit Tests

Security-sensitive logic should have focused unit tests where appropriate.

Examples include:

- permission evaluation,
- input validation,
- token validation,
- policy evaluation,
- security-sensitive state transitions.

Tests should cover both expected and adversarial inputs.

---

# 6. Authorization Tests

Authorization tests should verify:

- allowed operations,
- denied operations,
- role boundaries,
- resource ownership,
- tenant isolation,
- administrative boundaries.

These tests should be treated as part of the application's correctness suite.

---

# 7. Authentication Tests

Authentication mechanisms should be tested for:

- invalid credentials,
- expired credentials,
- revoked credentials,
- malformed tokens,
- incorrect audiences,
- incorrect issuers,
- session expiration.

The exact tests depend on the authentication architecture.

---

# 8. Privilege Boundary Testing

Important privilege boundaries should be explicitly tested.

Examples:

```text
Normal User
     │
     X
     │
Administrative Operation
```

and:

```text
Service A
     │
     X
     │
Service B Administrative API
```

A successful authentication should never implicitly imply unrestricted privilege.

---

# 9. Multi-Tenant Testing

Multi-tenant applications require dedicated isolation tests.

Tests should attempt to access:

- another tenant's records,
- another tenant's files,
- another tenant's API resources,
- another tenant's cached information.

Tenant isolation should be tested through realistic application paths.

---

# 10. API Security Testing

APIs should be tested for:

- authentication,
- authorization,
- input validation,
- rate limits,
- resource ownership,
- malformed requests,
- excessive payloads,
- unexpected HTTP methods,
- error disclosure.

API testing should include both expected clients and adversarial behavior.

---

# 11. Input Validation Testing

Inputs should be tested with:

- malformed values,
- boundary values,
- unexpected types,
- excessive sizes,
- invalid encodings,
- unexpected characters,
- missing fields.

The goal is to establish that invalid input cannot produce unintended behavior.

---

# 12. Injection Testing

Where relevant, test for injection into:

- databases,
- operating-system commands,
- templates,
- expressions,
- query languages.

Testing should verify that input remains data rather than becoming executable instructions.

---

# 13. File Upload Testing

Applications supporting uploads should test:

- unexpected file types,
- oversized files,
- malformed files,
- dangerous filenames,
- path traversal,
- executable content,
- authorization failures.

The test should verify both application behavior and storage behavior.

---

# 14. SSRF Testing

Applications that make server-side requests should test whether user-controlled input can reach unintended destinations.

Potential targets include:

- internal services,
- private network addresses,
- metadata endpoints,
- administrative interfaces.

Testing should verify that destination restrictions actually hold.

---

# 15. Session Testing

Session mechanisms should be tested for:

- fixation,
- expiration,
- logout behavior,
- concurrent sessions,
- credential changes,
- revocation.

The expected session lifecycle should be explicit.

---

# 16. Token Testing

Security tokens should be tested for:

- expired tokens,
- malformed tokens,
- invalid signatures,
- incorrect issuer,
- incorrect audience,
- insufficient scope,
- altered claims.

The application should reject tokens that fail required validation.

---

# 17. Replay Testing

High-impact operations should be tested for replay.

For example:

```text
Request A
   │
   ▼
Processed

Request A
   │
   ▼
Should not unintentionally execute again
```

The expected behavior should be defined by the business operation.

---

# 18. Rate-Limit Testing

Where rate limiting is part of the security design, test:

- threshold behavior,
- burst behavior,
- recovery,
- client identification,
- distributed requests.

The goal is to verify that the protection works under realistic load.

---

# 19. Resource Exhaustion Testing

Security testing should consider attacks that attempt to consume excessive:

- CPU,
- memory,
- storage,
- connections,
- threads,
- queue capacity.

Tests should establish that the system degrades safely rather than failing unpredictably.

---

# 20. Static Application Security Testing

Static analysis can identify potentially dangerous code patterns before execution.

It may identify:

- injection risks,
- unsafe APIs,
- insecure cryptography,
- suspicious data flows,
- hard-coded credentials.

Static analysis should be treated as evidence, not absolute truth.

---

# 21. Dependency Scanning

Dependency scanning should identify known vulnerabilities in:

- direct dependencies,
- transitive dependencies,
- operating-system packages,
- container components.

Findings should be evaluated based on actual risk and applicability.

---

# 22. Secret Scanning

Repositories and appropriate build inputs should be scanned for accidentally committed credentials.

Detection should include relevant:

- API keys,
- passwords,
- tokens,
- private keys,
- cloud credentials.

A discovered real credential should trigger rotation rather than merely deletion.

---

# 23. Container Scanning

Where containers are used, images may be scanned for:

- vulnerable packages,
- insecure configurations,
- known malicious components.

The project should distinguish:

```text
Vulnerability Exists
```

from:

```text
Vulnerability Is Exploitable In This System
```

Risk-based triage remains necessary.

---

# 24. Infrastructure Security Testing

Infrastructure configuration should be tested where appropriate.

Examples include:

- network exposure,
- overly broad permissions,
- insecure storage configuration,
- public endpoints,
- weak encryption settings.

Infrastructure security testing should align with the architecture.

---

# 25. Configuration Testing

Security-sensitive configuration should be validated.

Examples include:

- authentication requirements,
- authorization defaults,
- TLS configuration,
- security headers,
- network exposure,
- storage permissions.

Configuration drift should also be considered.

---

# 26. Dynamic Application Security Testing

Running applications can be tested from the outside to identify behavioral vulnerabilities.

Dynamic testing may identify:

- authentication weaknesses,
- authorization flaws,
- injection,
- session issues,
- insecure configurations,
- unexpected exposed functionality.

The test environment should be representative enough to produce meaningful results.

---

# 27. Penetration Testing

Penetration testing attempts to discover exploitable attack paths using attacker-oriented techniques.

It may be appropriate for:

- externally exposed systems,
- sensitive systems,
- major architectural changes,
- high-risk business capabilities.

Penetration testing should complement—not replace—continuous security engineering.

---

# 28. Threat-Led Testing

Testing should be informed by the system's threat model.

For example:

```text
Threat
  │
  ▼
Attack Path
  │
  ▼
Security Control
  │
  ▼
Test
```

This focuses testing effort on realistic attack paths.

---

# 29. Abuse Cases

Functional requirements describe what users should be able to do.

Abuse cases describe what an attacker may attempt.

Examples:

- access another user's data,
- bypass approval,
- replay a transaction,
- exhaust resources,
- escalate privileges.

Important abuse cases should become security tests.

---

# 30. Business Logic Testing

Automated scanners may not discover business-logic vulnerabilities.

Testing should therefore consider workflows such as:

```text
Create
  │
  ▼
Approve
  │
  ▼
Execute
  │
  ▼
Complete
```

Tests should attempt invalid sequences and unauthorized state transitions.

---

# 31. Race Condition Testing

Security-sensitive operations should be tested under concurrency where race conditions are plausible.

Examples include:

- duplicate transactions,
- simultaneous approvals,
- concurrent withdrawals,
- concurrent privilege changes.

The test should verify that business and authorization invariants remain intact.

---

# 32. Security Regression Tests

When a security defect is fixed, the system should normally gain a regression test appropriate to the defect.

This prevents the same vulnerability from silently returning.

Conceptually:

```text
Security Defect
      │
      ▼
Fix
      │
      ▼
Regression Test
      │
      ▼
Future Protection
```

---

# 33. Test Data

Security testing should use appropriately controlled data.

Avoid using unnecessary production-sensitive data in test environments.

Where possible, use:

- synthetic data,
- anonymized data,
- purpose-built test identities.

---

# 34. Production Testing

Security testing against production requires explicit consideration of risk.

Tests that may:

- modify data,
- consume significant resources,
- trigger alerts,
- affect customers,

should be carefully controlled.

Not every penetration technique belongs in production.

---

# 35. Testing Authentication Dependencies

Where authentication depends on external identity infrastructure, failure scenarios should be tested where appropriate.

Examples include:

- identity provider unavailable,
- token validation failure,
- certificate expiry,
- network partition.

The system should have an intentional security behavior under dependency failure.

---

# 36. Testing Authorization Dependencies

Similarly, authorization infrastructure may fail.

The system should establish whether failure results in:

```text
Fail Closed
```

or, for narrowly justified cases:

```text
Continue With Explicitly Defined Limited Behavior
```

This is an architecture decision, not an accidental implementation detail.

---

# 37. Security Testing in CI/CD

Appropriate security tests should be integrated into the delivery pipeline.

Potential stages include:

```text
Commit
  │
  ▼
Static / Secret Checks
  │
  ▼
Build
  │
  ▼
Dependency / Artifact Checks
  │
  ▼
Integration Tests
  │
  ▼
Security Tests
  │
  ▼
Release
```

The exact pipeline should reflect risk and delivery requirements.

---

# 38. Security Gates

Not every security finding should block every deployment.

Security gates should be based on defined criteria such as:

- severity,
- exploitability,
- exposure,
- affected asset,
- available mitigation.

This avoids both:

```text
Ignore Everything
```

and:

```text
Block Every Scanner Finding
```

---

# 39. Vulnerability Triage

Security findings should be classified and prioritized.

Consider:

- severity,
- exploitability,
- exposure,
- affected data,
- privilege required,
- compensating controls.

The objective is to focus engineering effort where it reduces meaningful risk.

---

# 40. False Positives

Security tools will produce false positives.

Teams should be able to:

- investigate,
- document,
- suppress where justified,
- periodically review suppressions.

A suppression should not become permanent unexplained technical debt.

---

# 41. Security Test Evidence

Important security tests should produce evidence that can be reviewed.

Evidence may include:

- test results,
- reports,
- logs,
- findings,
- remediation records.

Evidence should be retained according to organizational requirements.

---

# 42. Findings and Remediation

A security finding should have a lifecycle:

```text
Discovered
   │
   ▼
Triaged
   │
   ▼
Assigned
   │
   ▼
Remediated
   │
   ▼
Verified
   │
   ▼
Closed
```

A vulnerability should not be considered resolved merely because a developer claims it was fixed.

---

# 43. Security Exceptions

Sometimes a vulnerability cannot immediately be remediated.

An exception should identify:

- affected system,
- risk,
- justification,
- compensating controls,
- owner,
- expiration or review date.

Exceptions should not become permanent alternatives to remediation.

---

# 44. Security Testing Frequency

Testing frequency should reflect risk.

Possible triggers include:

- every change,
- every build,
- every release,
- periodic assessment,
- major architecture change,
- new external exposure.

Higher-risk systems require stronger and more frequent validation.

---

# 45. Independent Testing

For high-impact systems, some security assessments should be performed independently of the implementation team where appropriate.

Independent review can expose assumptions shared by the original designers.

---

# 46. Security Testing After Major Changes

Security testing should be reconsidered when introducing:

- new authentication,
- new authorization,
- new external APIs,
- new data classes,
- new network exposure,
- new privileged capabilities,
- major dependency changes,
- major infrastructure changes.

---

# 47. Security Testing and Observability

Testing should verify that security-relevant events are observable.

For example:

```text
Attack Attempt
      │
      ▼
Security Control
      │
      ├── Block
      │
      └── Record
```

A system that blocks attacks but produces no useful signal may still have operational security weaknesses.

---

# 48. Security Testing and Incident Response

Testing should inform incident-response readiness.

If a test discovers:

```text
Compromised Credential
```

the team should know:

- how to revoke it,
- how to identify usage,
- how to investigate,
- how to recover.

Testing therefore exposes both technical and operational gaps.

---

# 49. Security Testing and Reliability

Security testing should avoid creating false confidence about availability.

A security control may be correct while introducing:

- excessive latency,
- dependency failures,
- resource exhaustion,
- operational complexity.

Security and reliability should therefore be evaluated together for critical controls.

---

# 50. Minimum Engineering Requirements

Every production project should:

- [ ] Test important authentication behavior.
- [ ] Test important authorization boundaries.
- [ ] Test negative security paths.
- [ ] Test tenant isolation where applicable.
- [ ] Test important input-validation boundaries.
- [ ] Scan appropriate dependencies.
- [ ] Scan repositories for accidental secrets.
- [ ] Test security-sensitive APIs.
- [ ] Maintain security regression tests for important defects.
- [ ] Define vulnerability triage and remediation.
- [ ] Review important security findings before release.

Higher-risk systems may additionally require:

- [ ] Dynamic application security testing.
- [ ] Penetration testing.
- [ ] Independent security assessment.
- [ ] Threat-led testing.
- [ ] Business-logic abuse testing.
- [ ] Race-condition testing.
- [ ] Container and infrastructure security testing.
- [ ] Formal security testing evidence.
- [ ] Continuous security monitoring in delivery pipelines.

---

# Relationship With Other Security Standards

This standard works with:

- `06-security/README.md`
- `06-security/identity-and-access.md`
- `06-security/secrets-and-key-management.md`
- `06-security/application-security.md`
- `06-security/supply-chain-security.md`
- `06-security/security-monitoring.md`
- `06-security/incident-response.md`

It also connects with:

- `03-architecture/`
- `05-reliability/`
- `07-delivery/`
- `08-observability/`
- `11-operational-readiness/`

---

# Final Principle

> **Security is not established by declaring that a control exists. It is established by repeatedly attempting to violate the control and obtaining evidence that the system behaves as intended.**
