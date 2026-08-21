# Application Security

> An application is secure when its trust boundaries, inputs, authorization decisions, data handling, and failure behavior are deliberately designed to resist misuse.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Security Engineering

**Applies To:** All production applications, with depth proportional to system tier, exposure, data sensitivity, and threat profile

---

# Purpose

Application security addresses vulnerabilities that arise from application behavior rather than only from infrastructure configuration.

This includes:

- untrusted input,
- broken authorization,
- insecure data handling,
- injection,
- unsafe deserialization,
- insecure file handling,
- session weaknesses,
- business-logic abuse,
- excessive resource consumption,
- insecure error handling.

The objective is not to create a checklist of vulnerabilities.

The objective is to establish engineering practices that make insecure application behavior harder to introduce and easier to detect.

---

# Engineering Principle

> **Every externally influenced value should be treated as untrusted until the application has established that it is safe for the operation being performed.**

---

# 1. Application Trust Boundaries

The application should identify where untrusted or less-trusted data enters the system.

Examples include:

- HTTP requests,
- API clients,
- uploaded files,
- message queues,
- event streams,
- third-party APIs,
- browser input,
- command-line input.

Each boundary should define:

- what is trusted,
- what is validated,
- what is authenticated,
- what is authorized.

---

# 2. Input Is Untrusted

Applications should assume external input may be:

- malformed,
- incomplete,
- unexpectedly large,
- intentionally malicious,
- semantically invalid.

Validation should therefore happen before the input reaches security-sensitive operations.

---

# 3. Validation

Validation should establish both:

### Structural validity

Is the input shaped correctly?

### Semantic validity

Does the input make sense for the operation?

For example:

```text
age = "25"
```

may be structurally valid.

But:

```text
age = -500
```

may be semantically invalid.

Both dimensions matter.

---

# 4. Allowlist Validation

Where practical, validation should define what is permitted rather than attempting to enumerate every possible malicious value.

For example:

```text
Allowed:
A-Z
a-z
0-9
-
_
```

may be preferable to attempting to identify every dangerous character.

The appropriate validation model depends on the input.

---

# 5. Validation Does Not Replace Authorization

A value can be:

```text
Valid
```

but still:

```text
Unauthorized
```

For example, a valid account ID does not prove that the current user is allowed to access that account.

Validation and authorization solve different problems.

---

# 6. Authorization at the Resource Boundary

Security-sensitive operations should verify authorization close to the protected resource or capability.

For example:

```text
Request
  │
  ▼
Authenticate
  │
  ▼
Validate Input
  │
  ▼
Authorize Resource
  │
  ▼
Perform Operation
```

Authentication alone is insufficient.

---

# 7. Object-Level Authorization

Whenever an operation references a resource, the application should establish that the caller is authorized for that specific resource.

Examples include:

- account,
- order,
- document,
- tenant,
- invoice,
- customer record.

This protects against unauthorized resource access caused by manipulating identifiers.

---

# 8. Tenant Isolation

Multi-tenant applications require explicit isolation.

The application should not assume that:

```text
Authenticated User
```

implies:

```text
Access to Every Tenant Resource
```

Tenant context should be established and enforced consistently.

---

# 9. Server-Side Authorization

Security decisions should not depend exclusively on client-controlled information.

For example, hiding an administrative button in a UI does not provide authorization.

The server must independently enforce the permission.

---

# 10. Business Logic Security

Security vulnerabilities can exist even when:

- authentication works,
- authorization works,
- input validation works.

The business workflow itself may still be exploitable.

Examples include:

- bypassing approval steps,
- replaying transactions,
- applying discounts repeatedly,
- submitting an operation out of sequence,
- exceeding business limits.

Security review should therefore consider business behavior.

---

# 11. State Transitions

Important business operations should define valid state transitions.

For example:

```text
Created
   │
   ▼
Approved
   │
   ▼
Completed
```

The application should prevent unauthorized transitions such as:

```text
Created
   │
   X
   ▼
Completed
```

when approval is required.

---

# 12. Injection

Injection occurs when untrusted data changes the meaning of an operation.

Potential targets include:

- SQL,
- operating-system commands,
- template engines,
- expression languages,
- interpreters,
- query languages.

Applications should use appropriate structured APIs and parameterization rather than constructing executable statements from raw input.

---

# 13. Database Queries

Database queries should use parameterized mechanisms where supported.

Avoid constructing queries through unsafe string concatenation.

Conceptually:

```text
Untrusted Input
      │
      X
      │
Query Construction
```

should become:

```text
Query Structure
      +
Bound Parameters
```

This preserves the distinction between code and data.

---

# 14. Command Execution

Applications that invoke operating-system commands should treat command execution as a high-risk boundary.

Where possible:

- avoid shell invocation,
- use structured APIs,
- restrict allowed operations,
- validate arguments,
- run with least privilege.

Command execution should be considered carefully during threat modeling.

---

# 15. Output Encoding

Data should be encoded appropriately for the context in which it is rendered.

Examples include:

- HTML,
- JavaScript,
- URLs,
- JSON,
- SQL,
- shell commands.

Encoding for one context does not automatically make data safe for another.

---

# 16. Cross-Site Scripting

Applications rendering untrusted content should protect against script execution in unintended contexts.

Controls may include:

- contextual output encoding,
- safe templating,
- content-security controls,
- input constraints where appropriate.

The application should not rely solely on filtering a few known dangerous strings.

---

# 17. Cross-Site Request Forgery

State-changing browser operations should consider whether an attacker could cause an authenticated user's browser to perform an unintended action.

Where applicable, use appropriate mechanisms such as:

- anti-CSRF tokens,
- same-site cookie controls,
- origin validation.

The correct control depends on the application's authentication model.

---

# 18. Authentication Boundaries

Applications should not implement authentication differently for every feature without strong justification.

Authentication should use established mechanisms and should integrate with the organization's identity model.

Custom authentication logic increases security risk.

---

# 19. Session Security

Applications should protect session state against:

- theft,
- fixation,
- unauthorized reuse,
- excessive lifetime.

Session identifiers should be treated as credentials.

---

# 20. Cookies

Where cookies are used for authentication or sensitive state, appropriate controls should be considered, including:

- Secure,
- HttpOnly,
- SameSite,
- appropriate expiration.

The exact configuration should follow the application's architecture and browser interaction model.

---

# 21. Token Validation

Applications accepting security tokens should validate the relevant security properties.

Depending on the token type, this may include:

- issuer,
- audience,
- signature,
- expiration,
- not-before constraints,
- scopes or claims.

A token should not be trusted merely because it is syntactically valid.

---

# 22. Replay Protection

Operations with financial, security, or other high-impact consequences should consider replay attacks.

Possible mechanisms include:

- unique request identifiers,
- timestamps,
- nonce values,
- idempotency keys,
- server-side state.

The appropriate mechanism depends on the operation.

---

# 23. Idempotency

Idempotency can protect important operations from unintended repeated execution.

For example:

```text
Payment Request
     │
     ▼
Request ID
     │
     ▼
Already Processed?
     │
     ├── Yes → Return Existing Result
     │
     └── No  → Process
```

This is both a reliability and security concern for important state-changing operations.

---

# 24. File Uploads

File uploads create a significant trust boundary.

Applications should consider:

- file size,
- file type,
- file name,
- storage location,
- content inspection,
- execution risk,
- access control.

Do not trust the client-provided file extension alone.

---

# 25. File Names

Uploaded file names may contain unexpected characters or path manipulation attempts.

Applications should avoid directly using user-provided names as filesystem paths.

Prefer generated identifiers where appropriate.

---

# 26. Path Traversal

Applications should prevent user-controlled values from escaping intended directories.

For example:

```text
Expected:
 /uploads/<file>

Unexpected:
 /uploads/../../sensitive-file
```

Filesystem access should use safe path handling and explicit boundaries.

---

# 27. File Execution

Uploaded content should not automatically become executable code.

Storage architecture should separate:

- uploaded content,
- executable application code.

Where practical, uploaded files should be stored in locations where execution is not permitted.

---

# 28. Deserialization

Deserialization of untrusted data can introduce severe security risk depending on the technology.

Applications should:

- prefer safe data formats,
- avoid unsafe native-object deserialization,
- restrict allowed types,
- validate input.

Untrusted serialized data should never be assumed safe merely because it came from an expected endpoint.

---

# 29. SSRF

Applications that retrieve URLs or resources based on user-controlled input should consider server-side request forgery.

Potential targets may include:

- internal services,
- metadata endpoints,
- administrative interfaces,
- private network resources.

The application should explicitly restrict where server-side requests may go.

---

# 30. Outbound Requests

External requests should consider:

- destination validation,
- DNS behavior,
- redirects,
- timeouts,
- authentication,
- response size.

An application should not blindly follow arbitrary redirects to untrusted destinations.

---

# 31. Dependency Responses Are Also Untrusted

Applications should not assume that external services always return safe or expected data.

Third-party responses should be validated before being used in security-sensitive operations.

Trust should not be transferred automatically:

```text
External System
       │
       ▼
Application
       │
       X
Implicit Trust
```

---

# 32. Error Handling

Errors should be useful to operators without unnecessarily exposing:

- credentials,
- internal topology,
- stack traces,
- sensitive records,
- security configuration.

Detailed diagnostics should generally be available through controlled operational channels rather than returned directly to untrusted clients.

---

# 33. Exception Handling

Applications should avoid:

- silently swallowing security-relevant failures,
- returning inconsistent authorization behavior,
- exposing implementation details.

Failures should produce predictable security behavior.

---

# 34. Resource Exhaustion

Security includes protecting resources from intentional or accidental exhaustion.

Examples include:

- CPU,
- memory,
- database connections,
- threads,
- file descriptors,
- queue capacity,
- request concurrency.

Potential controls include:

- rate limits,
- quotas,
- request-size limits,
- timeouts,
- concurrency limits.

---

# 35. Rate Limiting

Rate limits can protect against:

- brute-force attacks,
- abusive clients,
- accidental overload,
- expensive operations.

Rate limiting should be applied where the business and threat model justify it.

Not every endpoint requires identical limits.

---

# 36. Expensive Operations

Operations with disproportionate computational cost should receive special consideration.

Examples include:

- password verification,
- large searches,
- report generation,
- file processing,
- cryptographic operations.

The system should prevent attackers from converting one request into disproportionate resource consumption.

---

# 37. Secrets in Application Behavior

Applications should avoid exposing secrets through:

- API responses,
- logs,
- exceptions,
- telemetry,
- URLs,
- client-side configuration.

Sensitive values should remain within the smallest necessary trust boundary.

---

# 38. Sensitive Data in URLs

Sensitive information should generally not be placed in URLs because URLs can propagate into:

- browser history,
- logs,
- monitoring systems,
- referrer information,
- analytics.

Use appropriate request mechanisms instead.

---

# 39. Sensitive Data Minimization

Applications should collect and process only the data necessary for the business operation.

Minimization reduces:

- attack surface,
- breach impact,
- storage requirements,
- privacy exposure.

The best protected sensitive data is often data the system never collected.

---

# 40. Secure Defaults

Applications should default to the safer behavior.

Examples include:

```text
No Permission → Deny

Invalid Input → Reject

Unknown Capability → Disabled

Missing Security Configuration → Fail Safely
```

Defaults should not silently weaken security.

---

# 41. Fail Securely

When a security decision cannot be made safely, the system should avoid granting unintended access.

For example:

```text
Authorization Service Unavailable
          │
          ▼
Do Not Assume Permission
```

The exact behavior must also consider availability requirements.

Security and availability trade-offs should be explicitly designed.

---

# 42. Security Headers

Web applications should consider appropriate browser security controls.

Depending on the application, these may include:

- Content-Security-Policy,
- Strict-Transport-Security,
- frame restrictions,
- content-type protections,
- referrer controls.

The configuration should reflect the application's actual browser behavior.

---

# 43. TLS

Sensitive application traffic should use appropriate transport security.

The application should avoid:

- obsolete protocols,
- insecure cipher configurations,
- certificate-validation bypasses.

Certificate validation should not be disabled merely to make development environments work.

---

# 44. Caching

Caching can accidentally expose data across authorization boundaries.

Applications should consider:

- cache keys,
- tenant boundaries,
- user-specific content,
- sensitive responses,
- invalidation.

A cache should not become an authorization bypass.

---

# 45. Concurrency

Concurrent operations can introduce security vulnerabilities when state changes are not atomic.

Examples include:

```text
Check Balance
     │
     ▼
Withdraw
```

If another operation modifies the balance between these steps, the security or business rule may be bypassed.

Critical state transitions should use appropriate concurrency controls.

---

# 46. Race Conditions

Security-sensitive operations should consider time-of-check vs time-of-use problems.

Conceptually:

```text
Check
 │
 X
 │
Use
```

The state may change between the two operations.

Where this matters, authorization and state transitions should be designed atomically or otherwise protected.

---

# 47. Auditability

Important security-sensitive actions should produce sufficient audit information to answer:

- who performed the action,
- what was changed,
- which resource was affected,
- when it happened,
- whether it succeeded.

Audit information should not itself expose secrets.

---

# 48. Security Testing in Development

Applications should incorporate security checks into the development lifecycle where practical.

Potential controls include:

- dependency analysis,
- static analysis,
- secret scanning,
- unit tests for authorization,
- integration tests,
- dynamic testing.

Security testing should focus on meaningful risks rather than maximizing scanner output.

---

# 49. Negative Security Tests

Security tests should explicitly verify that prohibited actions fail.

Examples:

```text
User A → Resource A → ALLOWED

User A → Resource B → DENIED

Normal User → Admin Operation → DENIED
```

A security control is incomplete if only the successful path is tested.

---

# 50. Threat Modeling Feedback

Application security findings should feed back into threat models.

If testing reveals:

```text
Unexpected Attack Path
```

the architecture should be reconsidered where necessary.

Security testing is therefore not merely a release gate.

It is an input into design improvement.

---

# 51. Security and Observability

Security controls should produce appropriate operational signals.

Examples include:

- authentication failures,
- authorization denials,
- suspicious request patterns,
- privilege changes,
- abnormal resource consumption.

These signals should integrate with the observability and security-monitoring model.

---

# 52. Security and Reliability

Security controls should not introduce avoidable reliability failures.

Examples include:

- unavailable authentication dependencies,
- expired certificates,
- overloaded authorization services,
- unavailable key-management systems.

Important security dependencies should have understood failure behavior.

---

# 53. Security and Performance

Security controls should be evaluated against critical-path performance.

Examples include:

- authorization checks,
- cryptographic operations,
- token validation,
- malware scanning,
- policy evaluation.

Performance concerns should lead to better engineering, not silent removal of necessary security controls.

---

# 54. Security Review Triggers

A security review should be considered when introducing:

- new external exposure,
- new authentication mechanisms,
- new authorization models,
- sensitive data,
- privileged functionality,
- file uploads,
- arbitrary outbound requests,
- code execution,
- new third-party integrations.

The review depth should match the risk.

---

# 55. Minimum Engineering Requirements

Every production application should:

- [ ] Treat external input as untrusted.
- [ ] Validate important inputs.
- [ ] Enforce authorization server-side.
- [ ] Protect resource-level authorization.
- [ ] Protect tenant boundaries where applicable.
- [ ] Use parameterized database access.
- [ ] Avoid unsafe command execution.
- [ ] Protect authentication sessions and tokens.
- [ ] Prevent secrets from entering logs and responses.
- [ ] Handle uploaded files safely.
- [ ] Protect against inappropriate resource exhaustion.
- [ ] Handle errors without unnecessary information disclosure.
- [ ] Provide appropriate security audit information.
- [ ] Test important negative authorization paths.

Higher-risk applications may additionally require:

- [ ] Formal threat modeling.
- [ ] Security architecture review.
- [ ] Dedicated penetration testing.
- [ ] Advanced abuse-case testing.
- [ ] Automated security testing.
- [ ] Formal API security testing.
- [ ] Specialized testing for multi-tenancy.
- [ ] Security review of critical business workflows.

---

# Relationship With Other Security Standards

This standard works with:

- `06-security/README.md`
- `06-security/identity-and-access.md`
- `06-security/secrets-and-key-management.md`
- `06-security/supply-chain-security.md`
- `06-security/security-testing.md`
- `06-security/security-monitoring.md`
- `06-security/incident-response.md`

It also connects with:

- `03-architecture/`
- `04-data/`
- `05-reliability/`
- `07-delivery/`
- `08-observability/`

---

# Final Principle

> **Application security is not primarily about adding security tools. It is about ensuring that every trust boundary, input, identity, state transition, and sensitive operation has an explicit security decision behind it.**
