# Identity and Access

> Authentication establishes who is acting. Authorization determines what that identity is allowed to do.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Security Engineering

**Applies To:** All production systems, with controls proportional to system tier, data sensitivity, and privilege risk

---

# Purpose

Identity and access management establishes the security boundary around people, services, workloads, and systems.

A production system should be able to answer:

- Who is requesting access?
- How was that identity established?
- What is the identity allowed to do?
- Who granted that permission?
- How long should the permission exist?
- How can the permission be revoked?
- Can the action be attributed to an individual or workload?

This standard defines the engineering baseline for authentication, authorization, privileged access, service identities, and access lifecycle.

---

# Engineering Principle

> **Every meaningful access decision should be based on an explicit identity, explicit authorization, and the minimum privilege required to perform the operation.**

---

# 1. Identity vs Authentication vs Authorization

These concepts must remain distinct.

### Identity

Represents the actor.

Examples:

- human user,
- service,
- workload,
- administrator,
- external system.

### Authentication

Establishes that the actor controls or represents that identity.

### Authorization

Determines what that authenticated identity may do.

Conceptually:

```text
Actor
  │
  ▼
Identity
  │
  ▼
Authentication
  │
  ▼
Authenticated Identity
  │
  ▼
Authorization
  │
  ├── Allowed
  │
  └── Denied
```

---

# 2. Human and Workload Identity

Human identities and workload identities should be treated differently.

### Human identity

Represents a person.

Examples:

- employee,
- administrator,
- customer,
- support engineer.

### Workload identity

Represents software acting on behalf of a system.

Examples:

- application service,
- scheduled job,
- CI/CD pipeline,
- background worker.

A workload should not normally authenticate as an individual human.

---

# 3. Identity Ownership

Every important identity should have an owner.

The owner should understand:

- why the identity exists,
- what it can access,
- where it is used,
- how it is revoked,
- when it should expire.

Unknown identities are security debt.

---

# 4. Authentication

Authentication mechanisms should be appropriate to the risk.

Possible mechanisms include:

- passwords,
- multi-factor authentication,
- certificates,
- access tokens,
- federated identity,
- workload identity,
- cryptographic credentials.

The project should avoid inventing custom authentication mechanisms when established mechanisms are available.

---

# 5. Centralized Identity

Where practical, organizations should prefer established identity providers rather than implementing independent identity stores for every application.

Benefits may include:

- centralized lifecycle management,
- consistent authentication policy,
- centralized revocation,
- stronger auditing,
- reduced credential duplication.

The appropriate architecture depends on the system and organizational environment.

---

# 6. Passwords

If passwords are used, the system should follow appropriate password-security practices.

Passwords should not be:

- stored in plaintext,
- logged,
- embedded in source code,
- transmitted unnecessarily,
- reused as service credentials.

Password storage should use an appropriate password-hashing mechanism rather than reversible encryption.

---

# 7. Multi-Factor Authentication

MFA should be considered for:

- privileged access,
- administrative access,
- sensitive applications,
- high-risk operations.

The required MFA strength should follow the threat model and organizational security policy.

---

# 8. Session Management

Authenticated sessions should have an explicit lifecycle.

The system should consider:

- session expiration,
- inactivity,
- renewal,
- revocation,
- logout,
- credential compromise.

Long-lived sessions increase the impact of credential theft.

---

# 9. Token Management

Where tokens are used, the project should understand:

- lifetime,
- audience,
- issuer,
- scope,
- revocation behavior,
- renewal mechanism.

Tokens should provide only the permissions required for their intended use.

---

# 10. Authorization

Authorization should be enforced at the point where access to a protected capability or resource is granted.

A request should not be considered safe merely because it passed authentication.

For example:

```text
Authenticated User
       │
       ▼
Request for Resource
       │
       ▼
Authorization Decision
       │
       ├── Allowed
       │
       └── Denied
```

---

# 11. Default Deny

Where appropriate, authorization should follow a deny-by-default model.

Conceptually:

```text
No Explicit Permission
        │
        ▼
      DENY
```

Access should be granted through explicit policy rather than relying on accidental permissions.

---

# 12. Least Privilege

An identity should receive only the permissions required to perform its responsibilities.

For example:

```text
Application
   │
   ├── Read customer profile
   │
   ├── Create order
   │
   └── No administrative access
```

Broad permissions should require explicit justification.

---

# 13. Permission Scope

Permissions should be scoped where practical by:

- resource,
- action,
- environment,
- tenant,
- service,
- data classification.

A permission such as:

```text
Access Everything
```

should not be the default.

---

# 14. Role-Based Access Control

Role-based access control can simplify authorization where permissions naturally map to organizational or application roles.

Examples:

- viewer,
- operator,
- administrator.

Roles should not become a mechanism for accumulating unrelated privileges indefinitely.

---

# 15. Attribute-Based Authorization

Some systems require authorization decisions based on attributes.

Examples include:

- resource ownership,
- department,
- tenant,
- geographic restrictions,
- data classification,
- transaction value.

Where required, authorization should incorporate the attributes necessary to enforce the business security policy.

---

# 16. Resource Ownership

Where resources belong to users or tenants, authorization should verify ownership explicitly.

For example:

```text
User A
  │
  └── Resource A

User B
  │
  └── Resource B
```

Authentication alone must not allow User A to access Resource B.

---

# 17. Multi-Tenant Authorization

Multi-tenant systems require explicit tenant isolation.

Every relevant operation should establish:

- current tenant,
- resource tenant,
- authorization relationship.

A common security failure is:

```text
Authenticated
      │
      ▼
Authorized for Application
      │
      X
      │
      ▼
Unauthorized Tenant Data
```

Tenant isolation should therefore be treated as a first-class security requirement.

---

# 18. Service Identity

Services should have identities that allow the system to distinguish:

```text
Service A
Service B
Service C
```

rather than treating all internal workloads as one trusted identity.

This enables:

- scoped permissions,
- attribution,
- isolation,
- credential rotation,
- policy enforcement.

---

# 19. Workload Identity

Where the platform supports workload identity, it should generally be preferred over distributing long-lived static credentials.

The goal is to establish:

```text
Workload
   │
   ▼
Verified Identity
   │
   ▼
Short-Lived / Scoped Credential
   │
   ▼
Resource Access
```

The exact implementation depends on the platform.

---

# 20. Service-to-Service Authorization

A service should not automatically receive access to another service simply because network connectivity exists.

The receiving service should be able to determine:

- caller identity,
- requested operation,
- resource,
- authorization.

Network reachability and authorization are separate concerns.

---

# 21. Privileged Identities

Privileged identities can perform high-impact operations.

Examples include:

- changing authorization policy,
- accessing sensitive data,
- modifying production infrastructure,
- rotating security credentials.

These identities require stronger controls.

---

# 22. Privileged Access Management

Where justified by risk, privileged access should include controls such as:

- MFA,
- time-limited access,
- approval,
- session auditing,
- separate administrative identities.

The objective is to reduce both unauthorized use and accidental misuse.

---

# 23. Separate Administrative Identity

Where appropriate, individuals should use separate identities for:

```text
Normal Work
```

and:

```text
Privileged Administration
```

This reduces the exposure of administrative privileges during ordinary activity.

---

# 24. Just-in-Time Access

Long-lived administrative permissions increase risk.

Where practical, privileged access should be:

- granted when needed,
- limited in scope,
- time-bound,
- automatically revoked.

This is especially valuable for high-impact production environments.

---

# 25. Access Reviews

Important permissions should be reviewed periodically.

The review should identify:

- unused permissions,
- excessive privileges,
- departed users,
- obsolete service accounts,
- unexpected access paths.

The review frequency should be proportional to risk.

---

# 26. Joiner, Mover, Leaver Lifecycle

Human access should follow the employment or organizational lifecycle.

### Joiner

Provide appropriate initial access.

### Mover

Adjust access when responsibilities change.

### Leaver

Remove access when the person no longer requires it.

The most important principle is:

> Access should follow current responsibility, not historical responsibility.

---

# 27. Service Identity Lifecycle

Workload identities also require lifecycle management.

Consider:

```text
Create
  │
  ▼
Deploy
  │
  ▼
Operate
  │
  ▼
Rotate / Update
  │
  ▼
Retire
  │
  ▼
Revoke
```

Unused service identities should not remain permanently active.

---

# 28. Credential Rotation

Credentials should have an appropriate rotation strategy.

Rotation may be:

- periodic,
- event-driven,
- triggered by suspected compromise.

Rotation should be designed so that it does not create unnecessary service outages.

---

# 29. Credential Revocation

The project should have a practical mechanism to revoke compromised credentials.

Questions include:

- How quickly can a credential be revoked?
- What systems depend on it?
- Can replacement credentials be deployed safely?
- Can compromised credentials remain active after rotation?

A credential that cannot be revoked promptly increases security risk.

---

# 30. Secrets Are Not Identity Architecture

A secret may authenticate an identity.

It should not become the identity model itself.

For example:

```text
"production-password-123"
```

does not provide useful identity semantics.

Prefer mechanisms that allow the system to establish:

```text
Which workload is acting?
```

and:

```text
What is that workload allowed to do?
```

---

# 31. Access Through Automation

Automation should use dedicated identities.

Examples include:

- CI/CD pipelines,
- deployment tools,
- infrastructure automation,
- scheduled jobs.

Automation should not depend on a developer's personal credential.

---

# 32. CI/CD Permissions

Deployment systems often have significant privileges.

The project should explicitly determine:

- what the pipeline can deploy,
- where it can deploy,
- what infrastructure it can modify,
- which secrets it can access.

CI/CD should receive only the permissions necessary for its responsibilities.

---

# 33. Break-Glass Access

Critical systems may require emergency access when normal authentication or authorization paths are unavailable.

Break-glass access should be:

- strongly protected,
- tightly controlled,
- auditable,
- limited to emergencies,
- reviewed after use.

Emergency access should not become the normal operating model.

---

# 34. Authorization Changes

Changes to important authorization policies should be controlled.

Where appropriate:

- review changes,
- audit changes,
- validate policies,
- test critical access paths.

A single incorrect policy change can expose significant amounts of data.

---

# 35. Authorization Testing

Security testing should verify both:

### Positive cases

Authorized users can perform allowed operations.

### Negative cases

Unauthorized users cannot perform restricted operations.

Negative testing is particularly important for:

- tenant isolation,
- administrative functions,
- sensitive resources,
- privilege boundaries.

---

# 36. Access Logging

Important access events should be attributable.

Where appropriate, record:

- identity,
- action,
- resource,
- timestamp,
- result.

Avoid logging secrets or sensitive authentication material.

---

# 37. Access Anomalies

Where risk justifies it, systems should be able to detect suspicious access patterns.

Examples include:

- unusual privilege escalation,
- unexpected geographic access,
- abnormal service behavior,
- repeated authorization failures,
- access outside expected patterns.

Detection should be based on meaningful risk signals rather than indiscriminate alerting.

---

# 38. External Identities

External users and organizations require explicit trust boundaries.

The project should establish:

- how external identities are authenticated,
- what they can access,
- how their access expires,
- how access is revoked.

External access should not inherit internal trust automatically.

---

# 39. Machine-to-Machine Access

Machine-to-machine communication should use explicit service identities where practical.

Avoid:

```text
Service A
   │
   ▼
Shared Global Credential
   │
   ▼
Every Service
```

Prefer scoped identities:

```text
Service A ──► Credential A ──► Allowed Resources
Service B ──► Credential B ──► Allowed Resources
```

This limits blast radius.

---

# 40. Access and Environment Separation

Production access should be separated appropriately from:

- development,
- test,
- staging.

A developer should not automatically receive production privileges merely because they can modify application code.

---

# 41. Access and Data Classification

Access requirements should follow data sensitivity.

For example:

```text
Public Data
     │
     ▼
Broad Access

Sensitive Data
     │
     ▼
Restricted Access
```

Data classification and access policy should remain connected.

---

# 42. Access and Recovery

Recovery procedures may require privileged access.

The project should verify that recovery identities:

- exist,
- are protected,
- are documented,
- remain available during an outage.

Recovery should not depend on an inaccessible administrator.

---

# 43. Access and Observability

Authorization failures and important privileged actions should be observable.

Security and operational monitoring should be able to distinguish:

```text
Expected Access
```

from:

```text
Unexpected Access
```

---

# 44. Access and Availability

Identity infrastructure can become a dependency.

For critical systems, consider:

- identity-provider availability,
- token validation dependencies,
- certificate expiry,
- key-management availability,
- emergency access.

Security controls should not accidentally create an unrecognized single point of failure.

---

# 45. Avoid Custom Authorization Logic

Authorization is security-critical.

Where possible, use:

- established policy mechanisms,
- centralized authorization patterns,
- well-tested libraries,
- standard identity protocols.

Custom authorization code should receive appropriate security scrutiny.

---

# 46. Access Policy as Code

Where authorization policies are complex, managing them as version-controlled artifacts can improve:

- reviewability,
- auditability,
- reproducibility,
- change tracking.

The exact mechanism depends on the platform and architecture.

---

# 47. Access Documentation

Important access models should be documented.

Documentation should explain:

- important roles,
- privileged operations,
- service identities,
- authorization boundaries,
- emergency access.

Documentation should not contain actual secrets.

---

# 48. Access Risk

Important access risks should be explicitly considered.

Examples include:

- excessive privilege,
- shared accounts,
- long-lived credentials,
- weak tenant isolation,
- undocumented administrative access,
- unrevoked identities.

Risks should be prioritized according to impact and exposure.

---

# 49. Minimum Engineering Requirements

Every production project should:

- [ ] Identify important human and workload identities.
- [ ] Separate authentication from authorization.
- [ ] Apply least privilege.
- [ ] Protect privileged access.
- [ ] Secure service-to-service access.
- [ ] Manage credentials through an appropriate lifecycle.
- [ ] Provide a mechanism for credential revocation.
- [ ] Avoid embedding credentials in source code.
- [ ] Test important authorization boundaries.
- [ ] Provide appropriate access auditing.
- [ ] Define how access is removed when no longer required.

Higher-risk systems may additionally require:

- [ ] Centralized identity federation.
- [ ] Strong MFA for privileged access.
- [ ] Just-in-time privileged access.
- [ ] Formal access reviews.
- [ ] Workload identity.
- [ ] Policy-as-code.
- [ ] Advanced authorization testing.
- [ ] Break-glass procedures.
- [ ] Automated identity lifecycle management.

---

# Relationship With Other Security Standards

This standard works with:

- `06-security/README.md`
- `06-security/secrets-and-key-management.md`
- `06-security/application-security.md`
- `06-security/supply-chain-security.md`
- `06-security/security-testing.md`
- `06-security/security-monitoring.md`
- `06-security/incident-response.md`

It also connects directly with:

- `01-governance/`
- `03-architecture/`
- `04-data/`
- `07-delivery/`
- `08-observability/`
- `09-platform-and-infrastructure/`

---

# Final Principle

> **The strongest access model is not the one that gives everyone the permissions they might need. It is the one that gives each identity exactly what it needs, for exactly as long as it needs it, and makes that access observable and revocable.**
