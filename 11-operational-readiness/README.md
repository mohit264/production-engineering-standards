# Operational Readiness

> Operational readiness is the discipline of proving that a system can be safely operated, supported, recovered, changed, and eventually retired in production.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Operational Engineering

---

# Purpose

A system can be:

* correctly designed,
* successfully implemented,
* successfully tested,
* successfully deployed,

and still be **unready for production**.

Why?

Because production introduces responsibilities that development does not.

Once a system is live, someone must be able to answer:

```text
Who owns it?

How do we know it is healthy?

What happens when it fails?

Who responds?

How do we recover it?

How do we restore its data?

How do we safely change it?

How do we know when it should be retired?
```

Operational readiness establishes the engineering conditions required to answer those questions **before the system becomes someone else's emergency**.

---

# Engineering Principle

> **A system is production-ready only when the organization can operate it intentionally under both normal conditions and expected failure conditions.**

"Works in production" is not the same as "is ready to be operated in production."

---

# 1. The Fundamental Problem

Software engineering often focuses on:

```text
Build
  │
  ▼
Test
  │
  ▼
Deploy
```

But production continues:

```text
Deploy
  │
  ▼
Operate
  │
  ├── Observe
  ├── Respond
  ├── Recover
  ├── Change
  ├── Scale
  └── Eventually Retire
```

Operational readiness exists at the boundary between **delivery** and **responsibility**.

---

# 2. Production Is a Responsibility

Deployment does not transfer responsibility to "production."

A production system needs accountable humans and teams.

At minimum, we should know:

```text
Service
   │
   ├── Business Owner
   ├── Engineering Owner
   ├── Operational Owner
   └── Escalation Path
```

Ownership must be explicit.

---

# 3. Ownership Is More Than a Name

An owner should have the authority and capability to:

* investigate failures,
* make operational decisions,
* coordinate response,
* approve appropriate changes,
* escalate when necessary,
* retire the system when appropriate.

An ownership label without operational responsibility is not meaningful ownership.

---

# 4. Operational Contract

Before production, the team should be able to state:

```text
What does this system promise?

What does healthy mean?

What failures are acceptable?

What failures require immediate action?

Who responds?

How quickly must we respond?

How do we recover?
```

This forms the system's operational contract.

---

# 5. Service Level Expectations

Operational readiness should establish measurable expectations where appropriate.

These may include:

```text
Availability
Latency
Throughput
Recovery time
Recovery point
Capacity
```

These expectations should come from actual business and system requirements rather than arbitrary numbers.

---

# 6. SLOs, SLAs, and Internal Targets

These concepts should remain distinct.

### SLA

A commitment made to a customer or external party.

### SLO

A reliability objective used to operate the service.

### Internal Target

An engineering target that may support implementation or capacity decisions.

For example:

```text
External commitment
        │
        ▼
SLA

Operational objective
        │
        ▼
SLO

Engineering implementation target
        │
        ▼
Internal target
```

Not every internal target should become an SLA.

---

# 7. Health Definition

The team should define what "healthy" means.

Health may include:

```text
Requests succeeding
Latency within target
Dependencies available
Queues within acceptable bounds
Data processing current
Capacity available
Critical background jobs functioning
```

Health should represent the service's actual ability to fulfill its purpose.

---

# 8. Observability Readiness

A production system should have sufficient observability to support operation.

This connects directly with `08-observability/`.

The team should be able to determine:

```text
Is the system healthy?

What is failing?

Who is affected?

When did it begin?

What changed?

Where should we investigate?
```

Observability is therefore a prerequisite for effective operations.

---

# 9. Alert Readiness

Important failure conditions should have appropriate alerts.

But operational readiness should ask a deeper question:

> **Can the on-call engineer do something useful when the alert fires?**

A production alert without an actionable response path is incomplete operational readiness.

---

# 10. Runbooks

Important operational conditions should have documented response procedures.

A runbook may describe:

```text
Detection
Diagnosis
Mitigation
Recovery
Verification
Escalation
```

Runbooks should be written for the engineer who encounters the problem during an incident—not for the engineer who originally designed the system.

---

# 11. Runbook Quality

A runbook should be:

* discoverable,
* understandable,
* current,
* actionable,
* owned.

A document that merely says:

```text
"Check the logs."
```

is rarely a sufficient runbook.

---

# 12. Incident Response

Incidents are inevitable in sufficiently complex systems.

Operational readiness therefore requires a response model.

The model should establish:

```text
Detection
   │
   ▼
Triage
   │
   ▼
Mitigation
   │
   ▼
Recovery
   │
   ▼
Verification
   │
   ▼
Learning
```

The objective during an incident is first to restore safe service.

Detailed root-cause analysis can follow afterward when appropriate.

---

# 13. Incident Roles

For significant incidents, responsibilities may need to be separated.

For example:

```text
Incident Commander
Technical Lead
Communications Lead
Subject Matter Experts
```

The exact organizational structure can vary.

The principle is:

> **Do not assume the person debugging the system should simultaneously coordinate the entire incident.**

---

# 14. Escalation

Operational readiness should define what happens when the primary responder cannot resolve the problem.

For example:

```text
Primary Owner
      │
      ▼
Secondary Owner
      │
      ▼
Specialist Team
      │
      ▼
Leadership / Vendor
```

Escalation should be defined before the incident.

---

# 15. On-Call

If a system requires rapid response, an on-call model should exist.

On-call readiness includes:

```text
Coverage
Contact mechanism
Escalation
Access
Runbooks
Alert routing
Handover
```

An on-call rotation without the necessary access or documentation is not operational readiness.

---

# 16. Access Readiness

Engineers responding to incidents need appropriate access.

They may require access to:

```text
Logs
Metrics
Traces
Deployment systems
Infrastructure
Databases
Cloud resources
Incident systems
```

Access should follow least privilege while still allowing effective incident response.

---

# 17. Break-Glass Access

Some systems may require emergency access beyond normal privileges.

A break-glass mechanism should provide:

```text
Emergency access
+
Strong authentication
+
Auditing
+
Controlled duration
```

Emergency access should be designed before it is needed.

---

# 18. Dependency Readiness

A system rarely operates alone.

Dependencies may include:

```text
Database
Identity provider
Payment service
Messaging system
External API
Storage
Network
Platform service
```

For each critical dependency, the team should understand:

```text
What happens if it is slow?

What happens if it is unavailable?

What happens if it returns invalid data?

What happens if connectivity is lost?
```

---

# 19. Dependency Failure

A production-ready system should define appropriate behavior when dependencies fail.

Possible strategies include:

```text
Timeout
Retry
Backoff
Circuit breaking
Fallback
Degraded operation
Queueing
Fail closed
Fail open
```

The correct strategy depends on the dependency and business semantics.

---

# 20. Timeouts

Every remote dependency should have an intentional timeout where applicable.

Without timeouts:

```text
Dependency stalls
      │
      ▼
Request waits
      │
      ▼
Resources accumulate
      │
      ▼
Service degrades
```

Timeouts are therefore both reliability and capacity controls.

---

# 21. Retry Readiness

Retries can improve resilience against transient failures.

But uncontrolled retries can amplify failures.

For example:

```text
Service A
   │
   ▼
Service B
```

If A aggressively retries B during B's outage, the retry traffic can increase B's load.

Operational readiness should therefore include intentional retry behavior.

---

# 22. Capacity Readiness

A system should have enough capacity for:

* expected workload,
* anticipated growth,
* reasonable peaks,
* expected failure scenarios.

Capacity should not be based solely on average demand.

---

# 23. Scaling Readiness

The team should understand how the system scales.

Questions include:

```text
What scales?

What triggers scaling?

How quickly can capacity increase?

What is the maximum useful scale?

What becomes the bottleneck first?
```

Automatic scaling does not remove the need to understand capacity behavior.

---

# 24. Failure Testing

Production readiness should not depend entirely on assumptions.

Where appropriate, teams should test:

```text
Dependency failure
Instance failure
Network failure
Storage failure
Capacity exhaustion
Deployment failure
Credential failure
```

The goal is not to create chaos for its own sake.

The goal is to verify that the expected recovery behavior actually exists.

---

# 25. Backup

If data matters, backup requirements should be explicit.

The team should know:

```text
What is backed up?

How frequently?

Where is it stored?

How long is it retained?

Who owns it?
```

A backup that has never been successfully restored is only an assumption.

---

# 26. Restore

Restore capability is more important than backup existence.

The operational contract should define:

```text
How do we restore?

How long does restoration take?

How much data could be lost?

How do we verify restoration?
```

This connects directly to recovery objectives.

---

# 27. Recovery Point Objective

RPO answers:

> **How much data loss can the business tolerate?**

For example:

```text
RPO = 15 minutes
```

means the recovery strategy should aim to lose no more than approximately 15 minutes of accepted data, subject to the actual system design.

---

# 28. Recovery Time Objective

RTO answers:

> **How quickly must the service be restored?**

For example:

```text
RTO = 1 hour
```

means the recovery architecture should support restoration within that operational target.

RTO and RPO are business and architecture decisions, not merely infrastructure settings.

---

# 29. Disaster Recovery

Disaster recovery addresses failures beyond normal component-level incidents.

Examples include:

```text
Region failure
Major infrastructure loss
Large-scale data corruption
Security compromise
Organizational dependency failure
```

The appropriate recovery strategy depends on business criticality.

---

# 30. Recovery Is an Architecture Property

Recovery should not be designed as a document alone.

For example:

```text
Recovery requirement
        │
        ▼
Architecture
        │
        ├── Replication
        ├── Backup
        ├── Failover
        └── Restoration
```

If the architecture cannot support the recovery objective, a runbook cannot magically create the required capability.

---

# 31. Deployment Readiness

A system should have a controlled mechanism for introducing changes.

The team should understand:

```text
How is a release created?

How is it validated?

How is it promoted?

How is it rolled back?

Who can authorize emergency changes?
```

This connects operational readiness with `07-delivery/`.

---

# 32. Rollback

Every significant production change should have a recovery strategy.

Rollback may mean:

```text
Previous application version
Previous configuration
Previous model
Previous infrastructure state
```

But rollback is not universally possible.

For irreversible changes, forward recovery must be designed instead.

---

# 33. Database Changes

Database changes deserve special attention.

A deployment may be reversible while a schema migration is not.

For example:

```text
Application v2
     +
Schema v2
```

may not safely return to:

```text
Application v1
```

Operational readiness should therefore explicitly consider migration compatibility.

---

# 34. Configuration Changes

Configuration can affect production behavior without changing application binaries.

Operational readiness should therefore include:

```text
Configuration ownership
Validation
Versioning
Change history
Rollback
Emergency procedures
```

---

# 35. Security Operations

A production system must be able to respond to security events.

Operational readiness should consider:

```text
Credential compromise
Unauthorized access
Suspicious activity
Vulnerability discovery
Certificate expiry
Secret rotation
```

Security response should connect with `03-security/`.

---

# 36. Certificate and Credential Lifecycle

Operational failures can occur when credentials expire.

Important assets may include:

```text
Certificates
API credentials
Service identities
Tokens
Signing keys
```

The system should define:

```text
Ownership
Expiration
Rotation
Monitoring
Emergency replacement
```

---

# 37. Operational Documentation

Documentation should cover the information required to operate the system.

Useful categories include:

```text
Architecture
Dependencies
Deployment
Configuration
Monitoring
Alerts
Runbooks
Recovery
Escalation
Ownership
```

Documentation should be maintained as part of the system lifecycle.

---

# 38. Operational Acceptance Criteria

A useful production-readiness review can ask:

```text
Ownership defined?
        │
        ▼
Observability ready?
        │
        ▼
Alerts actionable?
        │
        ▼
Runbooks available?
        │
        ▼
Dependencies understood?
        │
        ▼
Recovery tested?
        │
        ▼
Access verified?
        │
        ▼
Deployment and rollback understood?
        │
        ▼
System ready?
```

The exact checklist should reflect the system's criticality.

---

# 39. Production Readiness Review

A Production Readiness Review should not become a bureaucratic approval ceremony.

Its purpose is to expose unanswered operational questions before they become incidents.

The review should ask:

> **What would make operating this system unexpectedly difficult?**

That question often reveals more than a checklist alone.

---

# 40. Operational Readiness Is Continuous

Readiness is not a one-time gate.

Systems change.

Therefore:

```text
Architecture changes
      │
      ▼
Dependencies change
      │
      ▼
Operational assumptions change
      │
      ▼
Readiness must be reassessed
```

A system can become operationally unready even after years of successful operation.

---

# 41. Operational Readiness After Incidents

Incidents provide evidence about operational assumptions.

After a significant incident, ask:

```text
Did we detect it?

Did we know who owned it?

Did the runbook work?

Did we have the required access?

Could we recover?

Was recovery fast enough?

What assumption was wrong?
```

These answers should feed improvements back into the system.

---

# 42. Operational Readiness and Observability

`08-observability/` provides the ability to see and understand system behavior.

`11-operational-readiness/` asks whether that information is sufficient for operating the system.

The distinction is:

```text
Observability
    │
    ▼
Can we understand what is happening?

Operational Readiness
    │
    ▼
Can we responsibly operate when it happens?
```

---

# 43. Operational Readiness and Platform

`09-platform-and-infrastructure/` provides infrastructure capabilities.

Operational readiness asks:

```text
Can we depend on them?

Can we recover from their failure?

Do we understand their limits?

Do we have appropriate ownership?
```

Platform capability and operational responsibility therefore remain distinct.

---

# 44. Operational Readiness and AI

For AI systems, operational readiness additionally considers:

```text
Model availability
Model degradation
Data drift
Evaluation failure
Provider dependency
Inference cost
Fallback behavior
Safety failures
```

An AI system should have an operational response to model failure just as a conventional service should have a response to database failure.

---

# 45. Graceful Degradation

Some systems should continue providing reduced functionality when dependencies fail.

For example:

```text
Full functionality
       │
       ▼
Dependency failure
       │
       ▼
Reduced functionality
```

The acceptable degraded mode should be explicitly designed.

---

# 46. Fail-Safe Behavior

Some systems should stop rather than continue under unsafe conditions.

Examples may include:

```text
Security decision
Financial transaction
Safety-sensitive operation
Data integrity failure
```

The correct behavior depends on the system's risk model.

---

# 47. Operational Cost

Operations consume resources.

These include:

```text
On-call time
Incident response
Infrastructure
Monitoring
Backups
Support
Maintenance
```

A system that requires disproportionate operational effort may have an architectural problem.

Operational cost should therefore influence architecture decisions.

---

# 48. Operational Simplicity

A useful principle is:

> **Prefer architectures whose normal operation and failure recovery are understandable by the team responsible for them.**

Complexity can be justified.

But every additional component introduces:

```text
Failure modes
Dependencies
Operational knowledge
Maintenance
Cost
```

---

# 49. Decommissioning

Operational readiness includes the end of the system's lifecycle.

Before retirement, teams should determine:

```text
What depends on it?

What data must be retained?

What credentials must be revoked?

What infrastructure must be removed?

What monitoring and alerts must be retired?

Who approves decommissioning?
```

Leaving obsolete systems running creates unnecessary operational and security risk.

---

# 50. Minimum Engineering Requirements

Every production system should:

* [ ] Have explicit ownership.
* [ ] Define its operational expectations.
* [ ] Define what healthy means.
* [ ] Have appropriate observability.
* [ ] Have actionable alerts for important failures.
* [ ] Provide required runbooks.
* [ ] Define incident response and escalation.
* [ ] Ensure on-call coverage where required.
* [ ] Verify operational access.
* [ ] Identify critical dependencies.
* [ ] Define dependency failure behavior.
* [ ] Understand capacity and scaling behavior.
* [ ] Define backup and restore requirements where applicable.
* [ ] Define RTO and RPO where applicable.
* [ ] Have a production change and rollback strategy.
* [ ] Address security operational requirements.
* [ ] Document important operational procedures.
* [ ] Reassess readiness after significant changes.
* [ ] Have a retirement strategy.

Higher-criticality systems may additionally require:

* [ ] Formal Production Readiness Review.
* [ ] Tested disaster recovery.
* [ ] Recovery exercises.
* [ ] Break-glass access.
* [ ] Multi-region or equivalent recovery architecture.
* [ ] Formal incident-management roles.
* [ ] Dependency failure testing.
* [ ] Capacity and resilience exercises.
* [ ] Automated operational checks.
* [ ] Periodic operational maturity reviews.

---

# Relationship With Other Standards

This standard integrates the preceding engineering domains:

* `03-security/`
* `04-data/`
* `05-reliability/`
* `07-delivery/`
* `08-observability/`
* `09-platform-and-infrastructure/`
* `10-ai-and-data-engineering/`

Operational readiness is therefore not an isolated capability.

It is where the guarantees established by those domains become an operational responsibility.

---

# What This Standard Is Not

This standard does not prescribe:

* a specific incident-management platform,
* a specific on-call provider,
* a specific backup product,
* a specific disaster-recovery technology,
* a specific cloud provider,
* a specific deployment platform.

Those remain architecture and implementation decisions.

The engineering contract is:

> **Before a system becomes a production responsibility, the organization must be able to operate it, observe it, respond to its failures, recover it within defined expectations, change it safely, and eventually retire it deliberately.**

---

# Final Principle

> **Production readiness is not the moment software starts running. It is the point at which the organization is prepared to take responsibility for everything that happens after it starts running.**
