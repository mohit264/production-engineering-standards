# Consistency and Concurrency

> Correctness in a multi-component system depends on explicitly defining what state may change concurrently, what consistency is required, and what the system should do when multiple actors disagree about the state.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Architecture

**Applies To:** Every system where multiple actors, processes, components, or workflows can observe or modify shared state

---

# Purpose

Software systems frequently have multiple actors operating at the same time.

These actors may include:

- users,
- application instances,
- background workers,
- scheduled jobs,
- external systems,
- event consumers,
- administrators,
- automated agents.

When multiple actors interact with the same state, the system must define what constitutes correct behavior.

This standard establishes baseline principles for reasoning about:

- concurrent changes,
- consistency,
- atomicity,
- isolation,
- ordering,
- optimistic concurrency,
- pessimistic concurrency,
- distributed coordination,
- stale data,
- conflicts,
- reconciliation.

---

# Why This Standard Exists

Concurrency bugs are often difficult to reproduce because the system may appear correct under normal execution.

Consider:

```text
Actor A ─────► State
Actor B ─────► State
```

If both actors modify the same state at approximately the same time, the final result may depend on timing.

Without explicit concurrency semantics, the system may produce:

- lost updates,
- duplicate operations,
- inconsistent state,
- incorrect balances,
- stale decisions,
- conflicting records.

Therefore:

> **Concurrency behavior must be designed, not discovered accidentally in production.**

---

# Engineering Principle

> **For every important piece of mutable state, explicitly define who may change it concurrently, what consistency is required, how conflicts are detected, and how correctness is restored when concurrent operations interact.**

---

# 1. Define the Correctness Requirement First

Do not begin with:

> "Which locking mechanism should we use?"

Begin with:

> "What must remain true when concurrent operations occur?"

For example:

```text
Account balance must never become negative.
```

or:

```text
An order must not be paid twice.
```

or:

```text
A product's inventory must not be oversold beyond the permitted business tolerance.
```

The concurrency mechanism should protect the required invariant.

---

# 2. Identify Shared Mutable State

Not all shared data creates the same concurrency problem.

Identify state that can be:

- read by multiple actors,
- modified by multiple actors,
- modified while being read,
- derived from other mutable state.

Examples include:

- account balances,
- inventory,
- order status,
- configuration,
- quotas,
- counters,
- workflow state.

---

# 3. Atomicity

Atomicity means that a defined operation behaves as an indivisible unit with respect to the relevant correctness guarantee.

For example:

```text
Debit Account
+
Create Ledger Entry
```

may need to be treated as one atomic operation depending on the business invariant.

Atomicity should be defined at the level required by the business operation.

---

# 4. Isolation

Isolation concerns how concurrent operations observe one another's intermediate state.

Different systems provide different isolation guarantees.

Architecture should explicitly understand whether concurrent operations can observe:

- uncommitted changes,
- inconsistent reads,
- changes made during a transaction,
- newly inserted matching records.

The required isolation level should be driven by correctness requirements rather than habit.

---

# 5. Consistency

Consistency is not synonymous with:

> "All copies always contain exactly the same value."

In distributed systems, consistency describes the guarantees made about how state is observed and updated.

Examples include:

- strong consistency,
- eventual consistency,
- session-oriented guarantees,
- application-defined consistency.

The architecture should define what users and components are allowed to observe.

---

# 6. Strong Consistency

Strong consistency may be appropriate when an operation requires the latest authoritative state before proceeding.

Examples may include:

- certain financial operations,
- uniqueness constraints,
- inventory allocation,
- security authorization decisions.

Strong consistency can introduce coordination and latency costs.

It should therefore be used where its correctness benefit justifies those costs.

---

# 7. Eventual Consistency

Eventual consistency allows different representations of state to temporarily disagree while the system converges.

For example:

```text
Authoritative State
       │
       ├──────► Read Model A
       │
       └──────► Read Model B
```

A and B may temporarily differ.

This can be acceptable when:

- immediate consistency is unnecessary,
- stale data is tolerable,
- asynchronous processing provides useful decoupling.

The acceptable staleness window should be understood where it affects business behavior.

---

# 8. Stale Data

A system using replicated or cached data should explicitly consider stale reads.

Ask:

- How old can the data be?
- Is stale data acceptable?
- Which decisions may use stale data?
- Which decisions require authoritative state?
- What happens when stale information leads to a conflict?

Stale data is not automatically a defect.

Unexpected stale data is.

---

# 9. Lost Updates

A lost update occurs when one valid change unintentionally overwrites another.

Example:

```text
Initial Value = 10

Actor A reads 10
Actor B reads 10

Actor A writes 11
Actor B writes 12

Final Value = 12
```

Actor A's change has disappeared.

Architecture should explicitly prevent or detect lost updates where they violate correctness.

---

# 10. Optimistic Concurrency

Optimistic concurrency assumes conflicts are relatively uncommon.

The system allows an actor to work with a known version of the state and verifies that the state has not changed before committing.

Conceptually:

```text
Read Version 7
      │
      ▼
Modify Locally
      │
      ▼
Write Only If Version = 7
```

If another actor has already changed the state:

```text
Version = 8
```

the write fails or requires conflict handling.

Optimistic concurrency can be useful when:

- contention is relatively low,
- conflicts are recoverable,
- holding locks would be expensive.

---

# 11. Pessimistic Concurrency

Pessimistic concurrency coordinates access before modification.

Conceptually:

```text
Acquire Coordination
        │
        ▼
Modify State
        │
        ▼
Release Coordination
```

This may reduce conflicting modifications but can introduce:

- blocking,
- contention,
- deadlocks,
- reduced throughput.

Pessimistic approaches should therefore be used where their coordination guarantees justify their cost.

---

# 12. Locks

Locks can protect shared state from concurrent modification.

However, locking should answer:

- What exactly is being protected?
- How long is the lock held?
- What happens if the holder fails?
- Can another operation wait indefinitely?
- Can locks be acquired in conflicting orders?

A lock without bounded behavior can become a failure mechanism.

---

# 13. Deadlocks

Deadlocks can occur when actors wait for resources held by one another.

Example:

```text
Actor A
  │
  ├── holds Resource 1
  │
  └── waits for Resource 2


Actor B
  │
  ├── holds Resource 2
  │
  └── waits for Resource 1
```

Neither can proceed.

Where multiple locks or coordination mechanisms exist, architecture should consider:

- lock ordering,
- timeout,
- deadlock detection,
- recovery.

---

# 14. Idempotency

Idempotency is particularly important when operations may be retried.

Consider:

```text
Client
  │
  │ Create Payment
  ▼
Server
  │
  │ Processed
  │
  X Response Lost
  │
  ▼
Client Retries
```

Without appropriate semantics, the same business operation may execute twice.

For operations where duplicates are harmful, architecture should define an idempotency strategy.

---

# 15. Uniqueness

Some correctness requirements are fundamentally uniqueness constraints.

Examples:

- one active subscription per account,
- one identifier per entity,
- one payment for a specific operation.

Where uniqueness matters, do not rely solely on application-level checks such as:

```text
if not exists:
    create
```

when concurrent actors can execute the check simultaneously.

The authoritative consistency mechanism must enforce the invariant.

---

# 16. Check-Then-Act Races

A common concurrency bug is:

```text
Check Condition
      │
      ▼
Perform Action
```

Another actor may change the state between these operations.

For example:

```text
A: Check inventory = 1
B: Check inventory = 1

A: Reserve item
B: Reserve item
```

The system must ensure the entire business invariant is protected.

---

# 17. Compare-and-Swap Semantics

Some systems use a pattern equivalent to:

```text
Update State
WHERE CurrentVersion = ExpectedVersion
```

This provides a lightweight mechanism for detecting concurrent modification.

The important architectural concept is:

> "Only modify this state if it is still the state I previously observed."

---

# 18. Concurrency Boundaries

Not every part of a system needs the same concurrency guarantees.

Identify the state where coordination is actually required.

For example:

```text
┌──────────────────────────────┐
│ Application                  │
│                              │
│ Read-heavy operations        │
│        │                     │
│        ▼                     │
│ ┌────────────────────────┐   │
│ │ Inventory Allocation   │   │
│ │ Strong Coordination    │   │
│ └────────────────────────┘   │
│                              │
└──────────────────────────────┘
```

Avoid applying expensive coordination mechanisms to unrelated workloads.

---

# 19. Distributed Concurrency

Concurrency becomes more difficult when actors operate across machines.

Now there may be:

- network latency,
- partial failure,
- duplicated requests,
- delayed messages,
- unavailable coordinators,
- inconsistent observations.

A mechanism that works safely inside one process may not provide the same guarantees across a network.

---

# 20. Do Not Assume Local Locks Provide Distributed Safety

A process-local lock protects state within that process.

It does not automatically protect state shared by multiple instances.

For example:

```text
Instance A              Instance B
     │                       │
 Local Lock              Local Lock
     │                       │
     └────────┬──────────────┘
              ▼
        Shared State
```

Both instances can hold their own local locks simultaneously.

The coordination mechanism must exist at the scope where the state is shared.

---

# 21. Distributed Coordination

When multiple independent actors must coordinate around shared state, the architecture may require a distributed coordination mechanism.

Examples include:

- transactional storage,
- compare-and-swap semantics,
- distributed locking,
- consensus-backed coordination,
- ownership leases.

The mechanism should be selected according to the required correctness guarantee.

---

# 22. Coordination Has a Cost

Distributed coordination can introduce:

- latency,
- availability dependencies,
- contention,
- operational complexity.

Therefore:

> **Do not coordinate globally when local correctness is sufficient.**

Prefer the smallest coordination scope that preserves the required invariant.

---

# 23. Ordering

Some business processes depend on event or operation ordering.

For example:

```text
Create Account
      │
      ▼
Activate Account
      │
      ▼
Suspend Account
```

If operations are processed out of order, the resulting state may be incorrect.

Architecture should explicitly identify where ordering matters and where it does not.

---

# 24. Exactly-Once Claims

"Exactly once" should be treated carefully.

A system may provide exactly-once behavior at one layer while still producing duplicate effects at another layer.

For example:

```text
Message Delivery
        │
        ▼
Application Processing
        │
        ▼
External Side Effect
```

Exactly-once processing of the message does not automatically guarantee exactly-once execution of an external side effect.

The architecture should define the actual business guarantee required.

---

# 25. Reconciliation

Some distributed systems cannot guarantee immediate agreement.

Instead, they rely on reconciliation.

Conceptually:

```text
Observed State
      │
      ▼
Compare With
Desired / Authoritative State
      │
      ▼
Correct Difference
```

Reconciliation can be useful when temporary divergence is acceptable.

The architecture should define:

- source of truth,
- correction mechanism,
- convergence expectations,
- conflict handling.

---

# 26. Conflict Resolution

When concurrent changes conflict, the architecture should define what happens.

Possible strategies include:

- reject one change,
- retry,
- merge,
- last-write-wins,
- domain-specific resolution,
- manual intervention.

There is no universally correct conflict-resolution strategy.

Business semantics must determine the appropriate behavior.

---

# 27. Versioning State

Version numbers, timestamps, or equivalent mechanisms can help detect concurrent modification.

For example:

```text
Entity
├── Data
└── Version = 42
```

An update may require:

```text
Expected Version = 42
```

If the actual version has changed, the operation must handle the conflict.

---

# 28. Time Is Not Automatically a Safe Conflict Resolver

Using timestamps to decide which update wins can be dangerous.

Distributed systems may have:

- clock skew,
- delayed messages,
- different processing times.

If ordering matters, use a mechanism that actually provides the required ordering or version semantics.

---

# 29. Concurrency and Communication

Communication choices influence concurrency behavior.

Synchronous communication may coordinate immediate state transitions.

Asynchronous communication may introduce:

- delayed processing,
- duplicate delivery,
- reordering,
- temporary divergence.

Therefore concurrency requirements must be evaluated together with communication architecture.

---

# 30. Concurrency and Data Architecture

Concurrency requirements should be derived from data ownership.

For each important dataset, identify:

```text
Who owns it?
     │
     ▼
Who can modify it?
     │
     ▼
Can multiple actors modify it?
     │
     ▼
What invariant must remain true?
     │
     ▼
What coordination is required?
```

This prevents concurrency mechanisms from being selected in isolation.

---

# 31. Concurrency in Distributed Workflows

A business workflow may span multiple components.

For example:

```text
Order
  │
  ▼
Payment
  │
  ▼
Inventory
  │
  ▼
Fulfillment
```

The entire workflow may not be safely wrapped in one distributed transaction.

Instead, the architecture may need:

- explicit state transitions,
- idempotent operations,
- compensating actions,
- events,
- reconciliation.

The appropriate mechanism depends on the business invariant.

---

# 32. Avoid Global Coordination

Global coordination can become a scalability and availability bottleneck.

Prefer:

- local ownership,
- partitioned state,
- bounded coordination,
- asynchronous convergence

where the business semantics allow it.

Global coordination should be introduced only when the correctness requirement truly demands it.

---

# 33. Concurrency Testing

Important concurrency behavior should be tested deliberately.

Tests may include:

- simultaneous updates,
- duplicate requests,
- retries,
- conflicting writes,
- delayed messages,
- out-of-order messages,
- worker crashes,
- partial completion.

A concurrency mechanism that is never exercised under contention should not be assumed to be correct.

---

# 34. Load and Contention Testing

Performance testing should consider contention, not only throughput.

Measure where relevant:

- lock contention,
- transaction conflicts,
- queue contention,
- database contention,
- retry amplification,
- latency under concurrent load.

A system that works correctly with ten concurrent operations may behave very differently with ten thousand.

---

# 35. Concurrency Failure Checklist

For every important mutable state, answer:

- [ ] Who can modify it?
- [ ] Can multiple actors modify it concurrently?
- [ ] What invariant must remain true?
- [ ] What happens when two actors modify it simultaneously?
- [ ] Can updates be lost?
- [ ] Can duplicate operations occur?
- [ ] Can operations arrive out of order?
- [ ] What consistency level is required?
- [ ] Is optimistic concurrency sufficient?
- [ ] Is pessimistic coordination required?
- [ ] What happens when coordination fails?
- [ ] How are conflicts detected?
- [ ] How are conflicts resolved?
- [ ] How is correctness verified?

---

# Consistency and Concurrency Review Checklist

### Correctness

- [ ] Important invariants are explicitly identified.
- [ ] Concurrent behavior is defined.
- [ ] Lost updates are addressed.
- [ ] Duplicate operations are addressed where relevant.

### Consistency

- [ ] Required consistency level is documented.
- [ ] Staleness tolerance is understood.
- [ ] Authoritative state is identified.

### Concurrency

- [ ] Shared mutable state is identified.
- [ ] Coordination scope is appropriate.
- [ ] Locking or optimistic concurrency is justified.
- [ ] Deadlock behavior is considered where applicable.

### Distributed Systems

- [ ] Network failure is considered.
- [ ] Duplicate delivery is considered.
- [ ] Ordering requirements are understood.
- [ ] Distributed coordination is justified.

### Recovery

- [ ] Failed operations can be retried or recovered.
- [ ] Conflict resolution is defined.
- [ ] Reconciliation exists where temporary divergence is expected.

### Testing

- [ ] Concurrent behavior is tested.
- [ ] Contention is tested where significant.
- [ ] Failure scenarios are exercised.

---

# Relationship to Other Standards

Consistency and Concurrency builds upon:

- System Boundaries
- Component Boundaries
- Data Architecture
- Communication Patterns
- Architecture Principles
- Engineering Quality Attributes

It provides the foundation for later decisions involving:

- distributed transactions,
- resilience,
- failure domains,
- event-driven systems,
- scalability,
- state management.

---

# Final Principle

> **Concurrency is not primarily a locking problem. It is a correctness problem. First define what must remain true when multiple actors operate simultaneously; then choose the smallest coordination and consistency mechanism capable of preserving that truth.**
