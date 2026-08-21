# Failure Domains

> A reliable system does not assume that components will always work. It determines how failures are contained, how far they can propagate, and how the system continues operating when parts of it become unavailable.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Architecture

**Applies To:** Every production system

---

# Purpose

Failures are inevitable.

Components can:

- crash,
- become unavailable,
- become slow,
- exhaust resources,
- lose connectivity,
- return incorrect responses,
- become partially functional.

A production architecture must therefore answer:

> **When something fails, what else is allowed to fail with it?**

This standard establishes principles for identifying and designing failure domains.

---

# Why This Standard Exists

A system may contain many components while still having a single failure domain.

For example:

```text
                ┌──────────────┐
                │ Application  │
                └──────┬───────┘
                       │
                       ▼
                ┌──────────────┐
                │ Shared DB    │
                └──────────────┘
```

Multiple application instances do not provide meaningful independence if one database failure takes the entire system down.

Similarly:

```text
Service A ──► Service B ──► Service C
```

may look modular while allowing a failure in C to propagate through the entire request path.

Reliability therefore depends not only on redundancy, but on **failure isolation**.

---

# Engineering Principle

> **Every significant production system should understand its failure domains, identify critical dependency chains, and deliberately limit the propagation of failures across those boundaries.**

---

# What Is a Failure Domain?

A failure domain is a set of components that may be affected by a common failure.

Examples include:

- one process,
- one host,
- one availability zone,
- one database,
- one network segment,
- one cloud region,
- one identity provider,
- one external dependency.

The exact boundary depends on the failure being considered.

---

# Failure Domains Are Relative

A component can belong to multiple failure domains.

For example:

```text
Application Instance
        │
        ├── Process Failure Domain
        │
        ├── Host Failure Domain
        │
        ├── Availability Zone
        │
        └── Region
```

Architecture should consider the failure scope relevant to the business requirement.

---

# 1. Process Failure

A process may:

- crash,
- deadlock,
- leak resources,
- become unresponsive.

If multiple critical workloads share the same process, one process failure may affect all of them.

Where independent failure isolation is required, consider whether workloads should have separate process boundaries.

---

# 2. Host Failure

A physical or virtual host can fail because of:

- hardware failure,
- operating-system failure,
- hypervisor problems,
- resource exhaustion,
- networking issues.

If all instances of a critical workload run on one host, redundancy at the application level may not provide meaningful resilience.

---

# 3. Availability Zone Failure

Cloud infrastructure commonly provides multiple isolated locations within a region.

A workload designed to survive instance failure but not the loss of an entire availability zone has a larger failure domain than it may appear.

For systems requiring higher availability, architecture should determine whether components need distribution across independent infrastructure failure domains.

---

# 4. Region Failure

A region-level failure can affect:

- compute,
- storage,
- networking,
- managed services,
- dependencies.

Multi-region architecture introduces significant complexity.

It should therefore be considered only when:

- business continuity requires it,
- regulatory requirements demand it,
- recovery objectives justify it.

Multi-region is not automatically better architecture.

---

# 5. Dependency Failure

A system can fail because a dependency fails.

Dependencies may include:

- databases,
- queues,
- identity providers,
- payment providers,
- DNS,
- cloud services,
- third-party APIs.

The architecture should distinguish:

```text
Component Failure
```

from:

```text
Dependency Failure
```

because the recovery strategies may differ.

---

# 6. Shared Dependency Failure

A particularly dangerous dependency is one shared by many otherwise independent components.

For example:

```text
             ┌──► Service A
             │
Shared DB ───┼──► Service B
             │
             └──► Service C
```

The services may appear independent.

The shared database creates a common failure domain.

---

# 7. Failure Propagation

Failures can propagate through dependencies.

For example:

```text
Database
   │
   ▼
Service A
   │
   ▼
Service B
   │
   ▼
API
```

If Service A waits indefinitely for the database, the failure may propagate into Service B and eventually exhaust API capacity.

This is known as cascading failure.

Architecture should explicitly identify such paths.

---

# 8. Cascading Failure

A cascading failure occurs when one failure causes additional failures.

A typical pattern is:

```text
Dependency Failure
        │
        ▼
Requests Become Slow
        │
        ▼
Resources Remain Occupied
        │
        ▼
Capacity Is Exhausted
        │
        ▼
More Requests Fail
```

The original failure may be small.

The resulting outage may be large.

---

# 9. Failure Amplification

Some mechanisms amplify failures.

Examples include:

- aggressive retries,
- unbounded queues,
- synchronous dependency chains,
- connection exhaustion,
- uncontrolled fan-out.

Architecture should identify mechanisms where:

```text
Small Failure
     │
     ▼
Large Increase in Work
```

can occur.

---

# 10. Timeouts as Failure Boundaries

Timeouts limit how long a component waits for a dependency.

Without a timeout:

```text
Dependency
    │
    ▼
Caller waits
    │
    ▼
Caller resources remain occupied
    │
    ▼
Capacity decreases
```

Timeouts help bound this behavior.

Timeout values should reflect actual workload characteristics and downstream behavior.

---

# 11. Retries and Failure Amplification

Retries should be treated as part of failure architecture.

For example:

```text
Service B becomes unavailable

Service A
 ├── Request
 ├── Retry
 ├── Retry
 └── Retry
```

When many callers do this simultaneously, the failed dependency may receive even more traffic.

Retry policies should therefore consider:

- retry count,
- backoff,
- jitter,
- idempotency,
- dependency capacity,
- failure classification.

---

# 12. Circuit Breaking

A circuit breaker can prevent repeated requests to a known-failing dependency.

Conceptually:

```text
Caller
  │
  ▼
Circuit
  │
  ├── Closed ──► Dependency
  │
  └── Open ────► Fail Fast
```

Circuit breaking can reduce pressure on failing dependencies.

However, it does not make the dependency healthy.

The system must define what callers do when the circuit is open.

---

# 13. Bulkheads

A bulkhead limits how much of a shared resource one workload can consume.

Examples include limits on:

- connections,
- threads,
- workers,
- memory,
- queue capacity.

Conceptually:

```text
┌────────────────────────────────────┐
│ Application                        │
│                                    │
│ ┌────────────┐ ┌────────────┐      │
│ │ Workload A │ │ Workload B │      │
│ │ Capacity   │ │ Capacity   │      │
│ └────────────┘ └────────────┘      │
│                                    │
└────────────────────────────────────┘
```

If A becomes unhealthy, it should not automatically consume all capacity needed by B.

---

# 14. Load Shedding

When demand exceeds safe capacity, the system may need to reject or defer work.

Load shedding can protect critical operations from complete resource exhaustion.

The architecture should distinguish:

- critical work,
- important work,
- optional work.

Not all requests necessarily deserve identical treatment during overload.

---

# 15. Graceful Degradation

A system does not always need to choose between:

```text
Fully Operational
```

and:

```text
Completely Down
```

Some capabilities may degrade.

Examples:

- recommendations unavailable while checkout remains operational,
- reporting delayed while transactions continue,
- non-critical notifications delayed.

Architecture should identify where degraded operation is acceptable.

---

# 16. Dependency Classification

Important dependencies should be classified.

For each dependency, determine:

| Question | Required Understanding |
|----------|------------------------|
| Is it critical? | |
| Is it synchronous? | |
| Can it be unavailable temporarily? | |
| Can stale data be used? | |
| Can the operation be retried? | |
| Can the operation be deferred? | |
| Is there an alternative? | |
| What is the recovery strategy? | |

---

# 17. Critical Dependency

A critical dependency is one whose failure prevents an essential business capability from operating.

Examples might include:

- authoritative transaction storage,
- authentication for security-critical operations,
- payment authorization for payment completion.

Critical dependencies require explicit failure and recovery strategies.

---

# 18. Optional Dependency

An optional dependency is one whose failure should not necessarily prevent the primary business capability from operating.

For example:

```text
Checkout
   │
   ├──► Payment ───── Critical
   │
   └──► Recommendations ───── Optional
```

The architecture should prevent optional dependencies from becoming accidental critical dependencies.

---

# 19. Fan-Out

A component may depend on many downstream components.

For example:

```text
                  ┌──► B
                  ├──► C
A ────────────────┼──► D
                  ├──► E
                  └──► F
```

A single request can now fail because any of the downstream dependencies fail or become slow.

High fan-out should therefore be reviewed for:

- latency,
- failure probability,
- capacity,
- operational complexity.

---

# 20. Fan-In

Many components may depend upon the same component.

```text
A ──┐
B ──┤
C ──┼──► Shared Dependency
D ──┤
E ──┘
```

The shared dependency becomes a potentially large blast-radius component.

Fan-in should be considered when identifying critical shared dependencies.

---

# 21. Blast Radius

Blast radius describes how much of the system can be affected by a failure.

A useful architecture attempts to limit unnecessary blast radius.

Ask:

> If this component fails, what is the maximum business capability that can be affected?

This should be answered explicitly for critical components.

---

# 22. Failure Containment

Failure containment means preventing a failure from propagating unnecessarily.

Potential mechanisms include:

- timeouts,
- isolation,
- bulkheads,
- circuit breakers,
- bounded queues,
- load shedding,
- graceful degradation,
- asynchronous processing.

These are mechanisms, not universal requirements.

Use them when the failure model justifies them.

---

# 23. Recovery Boundaries

A failure boundary should also consider recovery.

Ask:

- Can the failed component restart independently?
- Can state be recovered?
- Can traffic be redirected?
- Can work be retried?
- Can failed work be replayed?
- Can the component recover without coordinated system-wide action?

Independent recovery can be as valuable as independent failure.

---

# 24. Stateful vs Stateless Components

Stateless components are often easier to replace because important state exists elsewhere.

Stateful components require additional consideration around:

- replication,
- persistence,
- recovery,
- consistency,
- failover.

This does not mean stateful systems are inherently bad.

It means their failure model must be explicitly understood.

---

# 25. Single Points of Failure

A single point of failure is a component whose failure can cause an unacceptable outage and for which there is no sufficient alternative.

Potential examples include:

- one database instance,
- one network path,
- one authentication provider,
- one deployment mechanism.

A single point of failure may be acceptable when its failure probability and business consequences are acceptable.

The important requirement is that the risk is known and intentional.

---

# 26. Redundancy

Redundancy can reduce the effect of individual failures.

Examples include:

- multiple application instances,
- replicated storage,
- multiple availability zones,
- redundant network paths.

However:

> **Redundancy without independent failure domains may provide less resilience than expected.**

Three instances on the same failed host are still one host failure domain.

---

# 27. Independence of Redundancy

When redundancy is used, determine whether the redundant instances actually fail independently.

Consider common dependencies:

```text
Instance A ─┐
Instance B ─┼──► Same Host
Instance C ─┘
```

The architecture has redundancy at one layer but a shared failure domain at another.

The meaningful question is not:

> "How many replicas do we have?"

It is:

> "What failures can the replicas survive independently?"

---

# 28. External Failure Domains

Third-party services should be treated as failure domains.

Examples include:

- payment providers,
- email providers,
- identity providers,
- maps,
- shipping providers.

For important external dependencies, determine:

- expected availability,
- timeout behavior,
- retry behavior,
- rate limits,
- fallback behavior,
- data recovery implications.

---

# 29. Failure and Data

Failure handling must account for state.

For example:

```text
Operation
   │
   ▼
State Change
   │
   X
Response Lost
```

The caller may not know whether the state change occurred.

Therefore recovery mechanisms should support determining:

- whether the operation succeeded,
- whether it can safely be retried,
- whether it requires reconciliation.

---

# 30. Failure and Asynchronous Work

Asynchronous systems introduce different failure modes.

A message may be:

- delayed,
- duplicated,
- reordered,
- rejected,
- permanently unprocessable.

The architecture should define:

- retry policy,
- dead-letter behavior,
- replay,
- idempotency,
- monitoring.

---

# 31. Failure and Capacity

Many failures are actually resource exhaustion.

Resources include:

- CPU,
- memory,
- storage,
- connections,
- threads,
- file descriptors,
- network bandwidth,
- queue capacity.

Capacity exhaustion should be considered a failure mode rather than only a performance concern.

---

# 32. Failure and Configuration

Incorrect configuration can produce system-wide failures.

Examples:

- invalid credentials,
- incorrect connection strings,
- unsafe feature flags,
- incorrect resource limits.

Critical configuration should have appropriate:

- validation,
- change controls,
- rollback mechanisms,
- observability.

---

# 33. Failure and Deployment

A deployment can create a failure domain.

Architecture should consider:

- rolling changes,
- canary deployment,
- rollback,
- compatibility,
- schema migration,
- partial deployment.

A deployment should not unnecessarily expose the entire system to the same failure simultaneously.

---

# 34. Failure and Dependency Chains

For every critical user journey, identify the dependency chain.

Example:

```text
User Request
    │
    ▼
API
    │
    ▼
Order
    │
    ▼
Payment
    │
    ▼
Database
```

Then ask:

> What happens when each dependency is unavailable?

The result should be documented for critical paths.

---

# 35. Failure Mode Classification

A useful baseline classification is:

### Fail Fast

The operation should stop quickly when a dependency is unavailable.

### Retry

The failure is likely transient and the operation can safely be retried.

### Degrade

A reduced capability remains acceptable.

### Queue

The work can be deferred.

### Fail Over

Another equivalent capability can take over.

### Reconcile

The system temporarily diverges and later corrects the state.

### Manual Intervention

The failure requires human resolution.

The correct strategy depends on business semantics.

---

# 36. Failure Testing

Important failure behavior should be tested deliberately.

Testing may include:

- process crashes,
- dependency outages,
- network failures,
- slow dependencies,
- resource exhaustion,
- duplicate messages,
- dropped responses,
- invalid configuration,
- partial deployment.

The objective is not to create chaos for its own sake.

The objective is to verify that the architecture behaves according to its intended failure contract.

---

# 37. Failure Testing Proportionality

Failure testing should be proportional to system criticality.

A low-risk internal tool may require:

- restart testing,
- backup verification,
- dependency failure checks.

A highly critical system may require substantially more extensive resilience validation.

The baseline defines the reasoning requirement; project governance determines the required depth.

---

# Failure Domain Review

For every significant production system, identify:

### Components

- [ ] What are the major failure domains?

### Dependencies

- [ ] What shared dependencies exist?
- [ ] Which dependencies are critical?

### Propagation

- [ ] How can failures propagate?
- [ ] Where can cascading failures occur?

### Isolation

- [ ] Where are failure boundaries intentionally established?
- [ ] Are bulkheads required?

### Capacity

- [ ] What happens under resource exhaustion?
- [ ] Is load shedding required?

### Recovery

- [ ] Can components recover independently?
- [ ] Can state be restored?
- [ ] Can work be retried or replayed?

### Redundancy

- [ ] Are redundant components actually independent?
- [ ] Are there hidden shared failure domains?

### Degradation

- [ ] Which capabilities can degrade?
- [ ] Which capabilities must remain available?

### External Dependencies

- [ ] What happens when third-party dependencies fail?

### Testing

- [ ] Are critical failure scenarios tested?

---

# Failure Domain Review Checklist

Before approving a production architecture:

### Failure Scope

- [ ] Major failure domains are identified.
- [ ] Blast radius is understood.
- [ ] Single points of failure are documented.

### Propagation

- [ ] Critical dependency chains are identified.
- [ ] Cascading failure risks are understood.
- [ ] Retry amplification is controlled.

### Isolation

- [ ] Appropriate timeout behavior exists.
- [ ] Resource isolation exists where justified.
- [ ] Critical workloads are protected from non-critical workloads.

### Recovery

- [ ] Recovery strategy is defined.
- [ ] Recovery objectives are appropriate.
- [ ] Recovery procedures are testable.

### Redundancy

- [ ] Redundancy is placed across meaningful failure domains.
- [ ] Shared dependencies have been considered.

### Degradation

- [ ] Graceful degradation opportunities are identified.
- [ ] Critical and optional dependencies are distinguished.

### Operations

- [ ] Failure signals are observable.
- [ ] Ownership of remediation is clear.
- [ ] Failure scenarios are tested proportionally to system criticality.

---

# Relationship to Other Standards

Failure Domains builds upon:

- System Boundaries
- Component Boundaries
- Data Architecture
- Communication Patterns
- Consistency and Concurrency
- Architecture Principles
- Engineering Quality Attributes

It provides the foundation for later decisions involving:

- resilience,
- availability,
- disaster recovery,
- scalability,
- capacity planning,
- deployment architecture,
- operational readiness.

---

# Final Principle

> **Redundancy reduces the probability that one failure causes an outage. Failure isolation limits how far that failure can travel. A resilient architecture needs both—and must understand the failure domains in which its components actually operate.**
