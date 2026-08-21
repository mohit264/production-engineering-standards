# Security

> Security is the discipline of protecting the system's trust boundaries, identities, data, software supply chain, and operational state against misuse and compromise.

---

**Status:** Engineering Governance

**Version:** 1.0

**Classification:** Security Architecture

---

# Purpose

This directory defines the engineering standards used to design, build, operate, and continuously improve secure systems.

Security is not treated as a single technology or a separate phase of development.

It is a property that must be considered across:

- architecture,
- identity,
- application behavior,
- data,
- dependencies,
- delivery,
- infrastructure,
- observability,
- operations.

The standards in this directory establish the security expectations that apply across those areas.

---

# Security Philosophy

The security model is based on a simple principle:

> **Do not assume trust where trust can be explicitly established, and do not depend on a security control that cannot be verified.**

This leads to several engineering behaviors:

- establish explicit trust boundaries,
- authenticate identities,
- authorize every protected capability,
- minimize privileges,
- protect secrets and cryptographic material,
- treat external input as untrusted,
- secure the software supply chain,
- test security assumptions,
- monitor important security boundaries,
- prepare for compromise,
- continuously improve after incidents.

---

# Security Is a System Property

Security cannot be isolated inside the security directory.

A vulnerability may originate in:

```text
Architecture
     │
     ├── Identity
     │
     ├── Application
     │
     ├── Data
     │
     ├── Dependencies
     │
     ├── Delivery
     │
     ├── Infrastructure
     │
     └── Operations
```

Therefore, these standards should be read together with the corresponding engineering domains.

---

# Security Standards

## 1. Identity and Access

**File:**

`identity-and-access.md`

Defines how systems establish identity and control access to capabilities and resources.

Core concerns include:

- authentication,
- authorization,
- least privilege,
- service identities,
- role boundaries,
- resource-level authorization,
- tenant isolation,
- privileged access.

### Core Principle

> **Authentication establishes who or what is acting; authorization establishes what that identity is allowed to do.**

---

## 2. Secrets and Key Management

**File:**

`secrets-and-key-management.md`

Defines how sensitive credentials and cryptographic material are created, stored, accessed, rotated, and retired.

Core concerns include:

- passwords,
- API credentials,
- tokens,
- certificates,
- encryption keys,
- secret rotation,
- access control,
- key lifecycle.

### Core Principle

> **Sensitive credentials should exist only where they are required, for only as long as they are required.**

---

## 3. Application Security

**File:**

`application-security.md`

Defines security expectations for application behavior.

Core concerns include:

- input validation,
- authorization,
- injection,
- session security,
- token validation,
- file handling,
- SSRF,
- business-logic abuse,
- resource exhaustion,
- secure error handling.

### Core Principle

> **Every externally influenced value should be treated as untrusted until the application establishes that it is safe for the operation being performed.**

---

## 4. Supply Chain Security

**File:**

`supply-chain-security.md`

Defines security expectations for everything that contributes to a production artifact.

Core concerns include:

- dependencies,
- package registries,
- container images,
- build systems,
- CI/CD,
- artifact integrity,
- provenance,
- SBOMs,
- artifact signing,
- compromised dependencies.

### Core Principle

> **Every component that can influence a production artifact is part of the security boundary of that artifact.**

---

## 5. Security Testing

**File:**

`security-testing.md`

Defines how security assumptions and controls are tested.

Core concerns include:

- security unit tests,
- authorization testing,
- API testing,
- dependency scanning,
- secret scanning,
- dynamic testing,
- penetration testing,
- abuse cases,
- security regression testing,
- vulnerability triage.

### Core Principle

> **Security controls are assumptions until they are tested.**

---

## 6. Security Monitoring

**File:**

`security-monitoring.md`

Defines how security-relevant activity is observed in production.

Core concerns include:

- authentication events,
- authorization failures,
- privileged activity,
- policy changes,
- suspicious behavior,
- security alerts,
- detection coverage,
- event integrity,
- investigation support.

### Core Principle

> **A security control that cannot be observed cannot be reliably trusted in production.**

---

## 7. Security Incident Response

**File:**

`incident-response.md`

Defines how the organization responds when preventive security controls are bypassed, compromised, or suspected to have failed.

Core concerns include:

- detection,
- triage,
- containment,
- investigation,
- evidence preservation,
- eradication,
- recovery,
- communication,
- post-incident review,
- corrective actions.

### Core Principle

> **The organization must be able to detect compromise, contain it, understand it, recover safely, and make the system harder to compromise again.**

---

# Security Control Lifecycle

The standards form a continuous lifecycle:

```text
          ┌──────────────────────┐
          │ Identity & Access    │
          └──────────┬───────────┘
                     │
                     ▼
          ┌──────────────────────┐
          │ Protect Application  │
          │ & Data               │
          └──────────┬───────────┘
                     │
                     ▼
          ┌──────────────────────┐
          │ Secure Supply Chain  │
          └──────────┬───────────┘
                     │
                     ▼
          ┌──────────────────────┐
          │ Test Security        │
          │ Assumptions          │
          └──────────┬───────────┘
                     │
                     ▼
          ┌──────────────────────┐
          │ Monitor Production   │
          └──────────┬───────────┘
                     │
                     ▼
          ┌──────────────────────┐
          │ Respond to Incidents │
          └──────────┬───────────┘
                     │
                     ▼
          ┌──────────────────────┐
          │ Improve the System   │
          └──────────┬───────────┘
                     │
                     └───────────────►
                             
                         Continuous Cycle
```

This is not a linear development process.

Security continuously feeds back into architecture and engineering.

---

# Security Boundaries

Security architecture should explicitly identify important trust boundaries.

Examples include:

- user → application,
- application → database,
- service → service,
- workload → infrastructure,
- application → third-party service,
- developer → source repository,
- source → build system,
- build system → artifact registry,
- artifact → production environment.

Each boundary should answer:

1. Who or what is communicating?
2. What is trusted?
3. What is authenticated?
4. What is authorized?
5. What data crosses the boundary?
6. What happens when the security mechanism fails?
7. What evidence is produced?

---

# Least Privilege

Privileges should be intentionally scoped.

The desired relationship is:

```text
Required Capability
       │
       ▼
Required Permission
       │
       ▼
Required Identity
```

Avoid granting broad permissions simply because they are convenient.

Privilege should be reviewed as systems evolve.

---

# Defense in Depth

Important security properties should not depend on one control when multiple independent controls are practical.

For example:

```text
Authentication
      +
Authorization
      +
Input Validation
      +
Network Controls
      +
Monitoring
      +
Incident Response
```

Defense in depth does not mean adding arbitrary layers.

Each layer should reduce a meaningful risk.

---

# Security and Architecture

Security decisions should be made alongside architectural decisions.

For significant systems, consider security implications of:

- trust boundaries,
- data flows,
- identities,
- privileges,
- external exposure,
- dependencies,
- failure modes,
- operational access.

Security should not be added only after the architecture has been finalized.

---

# Security and Data

Security controls must align with data sensitivity.

Consider:

- what data exists,
- where it is stored,
- who can access it,
- how it moves,
- how long it is retained,
- how it is deleted.

Data classification should influence security requirements.

---

# Security and Reliability

Security controls can affect availability.

Examples include:

- authentication dependencies,
- authorization services,
- secret stores,
- certificate infrastructure,
- security gateways.

For important security dependencies, explicitly define failure behavior.

The system should understand the trade-off between:

```text
Security
```

and:

```text
Availability
```

rather than discovering the trade-off during an outage.

---

# Security and Delivery

The delivery system is part of the security boundary.

Security therefore applies to:

- source repositories,
- pull requests,
- build systems,
- CI/CD workflows,
- artifact registries,
- deployment credentials,
- release processes.

A secure application built by an insecure pipeline is not a secure production system.

---

# Security and Observability

Security events should integrate with the broader observability model.

Security telemetry may include:

- logs,
- metrics,
- traces,
- audit events,
- identity events,
- infrastructure events.

However:

> **Security telemetry must itself be protected against unauthorized access and unnecessary data exposure.**

---

# Risk-Based Security

Not every system requires the same security controls.

Security requirements should reflect:

- business criticality,
- internet exposure,
- data sensitivity,
- privilege,
- threat landscape,
- regulatory requirements,
- customer impact,
- architectural complexity.

The objective is not:

> Maximum security everywhere.

The objective is:

> **Appropriate security for the risk.**

---

# Security Exceptions

When a required control cannot be implemented, the exception should be explicit.

An exception should identify:

- affected system,
- requirement,
- reason,
- risk,
- compensating control,
- owner,
- review date.

Exceptions should be temporary engineering decisions, not invisible permanent gaps.

---

# Security Review Triggers

A security review should be considered when introducing:

- new external exposure,
- new authentication mechanisms,
- new authorization models,
- sensitive data,
- privileged functionality,
- new third-party integrations,
- file uploads,
- arbitrary outbound requests,
- new execution capabilities,
- significant dependency changes,
- major infrastructure changes.

Review depth should be proportional to risk.

---

# Security Maturity

Security maturity should progress from:

```text
Reactive
   │
   ▼
Defined
   │
   ▼
Repeatable
   │
   ▼
Measured
   │
   ▼
Adaptive
```

A mature organization does not merely have security documents.

It can demonstrate that:

- controls exist,
- controls are tested,
- important activity is visible,
- incidents can be handled,
- lessons become engineering improvements.

---

# Minimum Security Baseline

Every production system should establish appropriate controls for:

- identity,
- authorization,
- secrets,
- application security,
- dependency security,
- security testing,
- security monitoring,
- incident response.

The exact implementation depends on the system's risk profile.

---

# Relationship With Other Engineering Domains

Security is intentionally cross-cutting.

This directory should be used alongside:

- `01-governance/`
- `02-engineering-maturity/`
- `03-architecture/`
- `04-data/`
- `05-reliability/`
- `07-delivery/`
- `08-observability/`
- `09-platform-and-infrastructure/`
- `10-cost-and-economics/`
- `11-operational-readiness/`

Security requirements should influence decisions within each of these domains.

---

# What This Directory Is Not

This directory is not:

- a compliance checklist,
- a penetration-testing manual,
- a collection of vendor-specific configurations,
- a list of security products,
- a substitute for threat modeling,
- a substitute for secure engineering.

It defines engineering expectations.

Implementation details belong in the appropriate technology-specific standards and system designs.

---

# Final Principle

> **Security is not a feature added to a system. It is the continuous discipline of controlling trust, limiting privilege, protecting important assets, verifying security assumptions, observing what happens in production, and responding when those assumptions fail.**
