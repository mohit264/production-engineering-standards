# Data Architecture

> Data is not merely an implementation detail. It represents business state, carries ownership, creates dependencies, and determines what the system can reliably know, change, recover, and forget.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Architecture

**Applies To:** Every system that creates, stores, processes, exchanges, or derives business data

---

# Purpose

Every production system depends on data.

Data may exist as:

- transactional records,
- configuration,
- cached state,
- files,
- events,
- logs,
- analytical datasets,
- machine-learning datasets,
- derived views.

Poor data architecture can create problems that are difficult to correct later:

- unclear ownership,
- inconsistent state,
- accidental coupling,
- uncontrolled duplication,
- difficult migrations,
- data loss,
- privacy violations,
- impossible deletion,
- unreliable recovery.

This standard establishes baseline principles for designing and operating data within a system.

---

# Why This Standard Exists

A system can have excellent application architecture and still fail because its data architecture is unclear.

Consider:

```text
Service A ──┐
Service B ──┼──► Shared Database
Service C ──┘
```

The database may appear to simplify the architecture.

But now:

- all services depend on its schema,
- schema changes affect multiple teams,
- ownership becomes ambiguous,
- independent deployment becomes harder,
- one failure can affect multiple capabilities.

Therefore:

> **Data ownership and data boundaries are architectural decisions.**

---

# Engineering Principle

> **Every important dataset must have a clear owner, defined semantics, known lifecycle, appropriate consistency guarantees, controlled access, and an explicit recovery and deletion strategy.**

---

# 1. Identify the Data

Before selecting a database or storage technology, identify what the system actually needs to store.

For each significant dataset, determine:

- What does it represent?
- Who creates it?
- Who owns it?
- Who reads it?
- Who modifies it?
- How long must it exist?
- How accurate must it be?
- How quickly must it be available?
- Can it be recreated?
- Can it be deleted?

Technology selection should follow these requirements.

---

# 2. Data Classification

Data should be classified according to its importance and sensitivity.

A project may define categories such as:

```text
Public
Internal
Confidential
Restricted
```

The exact classification scheme may vary by organization.

The important requirement is that classification drives appropriate:

- access controls,
- encryption,
- retention,
- logging,
- handling,
- deletion.

---

# 3. Authoritative Data

For important business data, identify the authoritative source.

For example:

```text
Customer Profile
      │
      ▼
Customer System
```

Other systems may maintain:

- caches,
- replicas,
- projections,
- search indexes,
- analytical copies.

These should not accidentally become competing authorities.

---

# 4. Data Ownership

Every significant dataset should have a clear owner.

Ownership means responsibility for:

- correctness,
- schema,
- lifecycle,
- access,
- changes,
- recovery,
- retention,
- deletion.

A dataset without a clear owner becomes difficult to govern.

---

# 5. Ownership vs Access

A system may access data without owning it.

For example:

```text
Customer System
      │
      │ Customer Data
      ▼
Order System
```

Order System may consume customer information.

That does not necessarily mean Order System owns the customer record.

Architecture should distinguish:

```text
Who owns the data?
```

from:

```text
Who is allowed to use the data?
```

---

# 6. Shared Databases

A shared database may be appropriate in some architectures.

It should not automatically be treated as an architectural failure.

However, shared databases introduce coupling through:

- schema,
- transactions,
- indexes,
- operational capacity,
- migrations.

If multiple components share storage, the resulting coupling should be understood and intentional.

---

# 7. Database per Service Is Not a Universal Rule

Database-per-service can improve ownership boundaries.

But it can also introduce:

- distributed transactions,
- duplicated data,
- operational complexity,
- synchronization challenges.

Therefore:

> **Choose data boundaries according to business ownership and consistency requirements, not architecture fashion.**

---

# 8. Data Duplication

Duplication is sometimes useful.

Examples include:

- read models,
- caches,
- search indexes,
- analytical copies,
- materialized views.

Duplication becomes dangerous when teams do not know:

- which copy is authoritative,
- how copies are updated,
- how stale they may become,
- how conflicts are handled.

Every important duplicate should have a defined purpose.

---

# 9. Derived Data

Derived data is computed from other data.

Examples:

```text
Orders
   │
   ▼
Revenue Summary
```

or:

```text
Transactions
   │
   ▼
Search Index
```

For derived data, determine:

- source of truth,
- derivation mechanism,
- freshness requirement,
- rebuild strategy,
- recovery strategy.

A derived dataset should ideally be reproducible when practical.

---

# 10. Cache Architecture

A cache is not automatically a source of truth.

Architecture should explicitly define:

- what is cached,
- why it is cached,
- acceptable staleness,
- expiration,
- invalidation,
- behavior on cache failure.

A common principle is:

> **If the cache disappears, the system should behave according to the intended cache-miss contract.**

---

# 11. Cache Failure

A cache failure can become dangerous if all requests immediately fall back to an underlying system.

For example:

```text
Normal:

Requests ──► Cache ──► Database

Cache Failure:

Requests ─────────────► Database
```

The database may suddenly receive traffic far beyond its expected capacity.

Therefore cache architecture should consider:

- cache stampede,
- fallback load,
- connection limits,
- request coalescing,
- degraded behavior.

---

# 12. Data Consistency

For each important dataset, determine the required consistency model.

Questions include:

- Must reads reflect the latest committed state?
- Can stale data be tolerated?
- For how long?
- Which operations require authoritative reads?
- What happens when replicas disagree?

Consistency requirements should be defined by business behavior.

---

# 13. Transactions

Transactions should protect meaningful business invariants.

For example:

```text
Debit Account
+
Record Ledger Entry
```

may require atomicity.

Do not create transactions merely because multiple statements happen to be executed together.

The transaction boundary should correspond to a correctness boundary.

---

# 14. Transaction Scope

Large transactions can create:

- lock contention,
- long-running operations,
- reduced throughput,
- difficult failure behavior.

Transaction boundaries should therefore be as small as practical while preserving the required invariant.

---

# 15. Distributed Transactions

A business operation may span multiple data stores.

For example:

```text
Order DB
     │
     ▼
Payment System
     │
     ▼
Inventory System
```

A single atomic transaction across all systems may not be practical.

Where distributed transactions are avoided, architecture may instead use:

- explicit workflow state,
- idempotency,
- events,
- compensating actions,
- reconciliation.

The correct mechanism depends on the business invariant.

---

# 16. Schema Design

Schema should reflect the semantics of the data.

Important considerations may include:

- identifiers,
- relationships,
- constraints,
- uniqueness,
- nullability,
- lifecycle state,
- timestamps,
- versioning.

Schema design should prevent invalid states where the storage system can enforce them reliably.

---

# 17. Constraints

Where correctness depends on a constraint, enforce it at the strongest appropriate layer.

Examples:

- uniqueness,
- referential integrity,
- valid ranges,
- required fields.

Application checks alone may be insufficient when multiple actors can modify the data concurrently.

---

# 18. Identifiers

Important entities should have stable identifiers.

An identifier strategy should consider:

- uniqueness,
- scope,
- generation,
- predictability,
- exposure,
- migration.

Identifiers should not accidentally expose sensitive business information.

---

# 19. Temporal Data

Where business behavior depends on time, timestamps should have clear semantics.

For example:

```text
created_at
updated_at
effective_at
expires_at
processed_at
```

These represent different concepts.

Do not use one timestamp field to represent several unrelated meanings.

---

# 20. Time and Distributed Systems

Distributed systems may have:

- clock skew,
- delayed messages,
- different processing times.

Therefore timestamps should not automatically be treated as a perfect ordering mechanism.

Where ordering matters, use explicit versioning or ordering mechanisms appropriate to the system.

---

# 21. Data Lifecycle

Every important dataset should have a lifecycle.

A useful model is:

```text
Create
  │
  ▼
Active
  │
  ▼
Archived
  │
  ▼
Deleted
```

Not every dataset needs every stage.

The lifecycle should be defined according to business, legal, operational, and technical requirements.

---

# 22. Retention

Retention defines how long data should remain available.

Retention should consider:

- business requirements,
- legal requirements,
- regulatory requirements,
- operational requirements,
- storage cost,
- privacy requirements.

Do not retain data indefinitely simply because storage is inexpensive.

---

# 23. Data Deletion

Deletion should be designed from the beginning.

The system should be able to answer:

> **If a user or organization must be removed, where does their data exist?**

This includes considering:

- primary databases,
- replicas,
- caches,
- search indexes,
- object storage,
- analytical systems,
- event stores,
- backups,
- derived datasets.

Deletion is a data architecture concern, not merely a database operation.

---

# 24. Right to Be Forgotten

Where applicable, privacy requirements may require deletion or anonymization of personal data.

Architecture should determine:

- what must be deleted,
- what can be anonymized,
- how derived copies are handled,
- how deletion propagates,
- how backups are treated,
- how deletion is verified.

This should be designed before immutable or replicated architectures make deletion difficult.

---

# 25. Sensitive Data

Sensitive data should have explicit handling requirements.

Consider:

- encryption at rest,
- encryption in transit,
- access control,
- masking,
- tokenization,
- auditability,
- retention,
- deletion.

Sensitive data should not appear unnecessarily in:

- logs,
- traces,
- error messages,
- URLs,
- analytics events.

---

# 26. Data Minimization

Collect and retain only the data required for legitimate system purposes.

Ask:

```text
Do we need this data?
        │
        ▼
Do we need it for this long?
        │
        ▼
Do all components need access?
```

Reducing unnecessary data reduces:

- privacy risk,
- security exposure,
- storage cost,
- operational complexity.

---

# 27. Data Access

Access should follow least privilege.

For important datasets, define:

- who can read,
- who can write,
- who can delete,
- who can administer.

Application-level authorization does not eliminate the need for appropriate infrastructure-level access controls.

---

# 28. Data Access Patterns

Storage technology should be selected according to access patterns.

Examples:

| Requirement | Potential Consideration |
|---|---|
| Transactional updates | Relational / transactional store |
| Document-oriented access | Document store |
| Key-based lookup | Key-value store |
| Search | Search index |
| Large objects | Object storage |
| Analytical queries | Analytical platform |
| Time-series data | Time-oriented storage |

This is guidance, not a mandatory technology mapping.

The workload determines the appropriate technology.

---

# 29. Polyglot Persistence

Different workloads may legitimately require different storage technologies.

For example:

```text
Transactional DB
       │
       ├──► Search Index
       │
       ├──► Cache
       │
       └──► Analytics Store
```

Polyglot persistence increases operational complexity.

Therefore each additional datastore should have a clear reason to exist.

---

# 30. Operational Ownership

Every production datastore should have an owner responsible for:

- capacity,
- availability,
- backup,
- recovery,
- security,
- upgrades,
- monitoring,
- cost.

Using a managed service does not eliminate ownership.

It changes the operational responsibilities.

---

# 31. Backup

Backups should exist for data whose loss would be unacceptable.

Backup design should define:

- what is backed up,
- frequency,
- retention,
- storage location,
- encryption,
- access controls.

A backup that cannot be restored is not a reliable recovery mechanism.

---

# 32. Restore Testing

Backup success does not prove recovery capability.

Restore procedures should be tested periodically where the business impact justifies it.

Testing should verify:

- backup integrity,
- restore process,
- restoration time,
- application compatibility,
- data correctness.

---

# 33. Recovery Objectives

Data architecture should align with recovery objectives.

Important concepts include:

### Recovery Point Objective

How much data loss is acceptable?

### Recovery Time Objective

How quickly must service/data be restored?

These objectives should drive:

- backup frequency,
- replication,
- failover,
- recovery architecture.

---

# 34. Data Migration

Schema and data migrations should be treated as production changes.

A migration plan should consider:

- compatibility,
- deployment ordering,
- rollback,
- data volume,
- locking,
- performance,
- partial completion.

Large data migrations should not assume that development-scale behavior represents production behavior.

---

# 35. Expand and Contract

Where systems require rolling deployments, schema changes may need to remain compatible across multiple application versions.

A common strategy is:

```text
Expand
   │
   ▼
Deploy Compatible Application
   │
   ▼
Migrate Data
   │
   ▼
Remove Old Structure
```

The exact process depends on the datastore and workload.

The underlying principle is:

> **Do not require all application instances to change simultaneously unless the deployment model guarantees that condition.**

---

# 36. Data Corruption

Architecture should consider not only data loss but incorrect data.

Potential causes include:

- application defects,
- incorrect migrations,
- duplicate processing,
- manual changes,
- integration failures,
- serialization problems.

Detection mechanisms may include:

- constraints,
- validation,
- reconciliation,
- checksums,
- audit records,
- anomaly detection.

---

# 37. Auditability

For important business data, determine whether changes need to be attributable.

Where required, capture:

- what changed,
- when,
- by whom or what system,
- previous state where appropriate,
- reason for change where required.

Audit data should itself have appropriate security and retention controls.

---

# 38. Event Data

Events may become an important historical record.

If events are retained, architecture should define:

- event ownership,
- schema evolution,
- retention,
- replay behavior,
- privacy implications,
- deletion strategy.

An immutable event store can make privacy deletion particularly challenging.

This must be considered before adopting the model.

---

# 39. Analytical Data

Operational and analytical workloads often have different requirements.

Operational systems may prioritize:

- transactional correctness,
- low-latency writes,
- request-driven access.

Analytical systems may prioritize:

- large-scale scans,
- aggregation,
- historical retention,
- flexible queries.

Avoid forcing one datastore to serve fundamentally incompatible workloads without understanding the consequences.

---

# 40. Data Pipelines

Data pipelines should explicitly define:

- source,
- transformation,
- destination,
- freshness requirement,
- failure behavior,
- replay/backfill strategy.

For example:

```text
Source
  │
  ▼
Ingestion
  │
  ▼
Transformation
  │
  ▼
Storage
  │
  ▼
Consumers
```

Each boundary is a potential failure and correctness boundary.

---

# 41. Data Quality

Data quality should be treated as an engineering concern.

Where appropriate, define expectations around:

- completeness,
- validity,
- uniqueness,
- consistency,
- freshness,
- accuracy.

A pipeline that is operationally "green" but producing incorrect data is still failing.

---

# 42. Data Drift

For systems involving analytics or machine learning, input data may change over time.

Architecture should consider whether changes in:

- distribution,
- schema,
- volume,
- categories,
- business behavior

can affect downstream correctness.

Where relevant, establish detection and response mechanisms.

---

# 43. Backfill

Data systems should consider how historical data can be reprocessed.

Backfill may be required after:

- pipeline failure,
- transformation defect,
- schema change,
- new business logic.

Backfill architecture should address:

- idempotency,
- duplicate effects,
- resource consumption,
- historical correctness,
- downstream impact.

---

# 44. Data Observability

Important datasets should have appropriate visibility into:

- freshness,
- volume,
- errors,
- anomalies,
- pipeline health,
- replication lag.

The appropriate level depends on the business importance of the data.

---

# 45. Data Cost

Data architecture has economic consequences.

Consider:

- storage,
- replication,
- backups,
- network transfer,
- query processing,
- retention,
- indexing.

Long-term data retention should have an explicit business justification.

---

# 46. Data Portability

For important systems, understand how data can be:

- exported,
- migrated,
- restored,
- transferred between systems.

Avoid creating unnecessary dependence on proprietary features when portability is an important business requirement.

---

# 47. Data Residency

Where legal, regulatory, or contractual requirements apply, determine where data may be stored and processed.

This may influence:

- region selection,
- replication,
- backups,
- analytics,
- third-party processing.

Data residency should be treated as an architectural constraint where applicable.

---

# 48. Data Architecture Decision Record

Significant data architecture decisions should document:

- dataset,
- owner,
- authoritative source,
- storage technology,
- consistency requirement,
- retention,
- backup,
- recovery,
- deletion,
- security classification,
- access model.

The depth of documentation should be proportional to risk.

---

# Data Architecture Review Checklist

### Ownership

- [ ] Every important dataset has an owner.
- [ ] Authoritative sources are identified.
- [ ] Consumers are distinguished from owners.

### Correctness

- [ ] Important invariants are defined.
- [ ] Appropriate constraints exist.
- [ ] Transaction boundaries are understood.
- [ ] Concurrency behavior is defined.

### Lifecycle

- [ ] Creation is understood.
- [ ] Retention is defined.
- [ ] Archival requirements are defined where applicable.
- [ ] Deletion is defined.

### Security and Privacy

- [ ] Data classification exists.
- [ ] Sensitive data handling is defined.
- [ ] Access follows least privilege.
- [ ] Privacy requirements are addressed.

### Resilience

- [ ] Backups exist where required.
- [ ] Restore procedures are tested.
- [ ] Recovery objectives are defined.
- [ ] Data corruption scenarios are considered.

### Evolution

- [ ] Schema migration strategy exists.
- [ ] Compatibility during deployment is considered.
- [ ] Large migrations are tested appropriately.

### Distributed Data

- [ ] Replicas and derived copies are identified.
- [ ] Consistency requirements are understood.
- [ ] Synchronization failures are considered.
- [ ] Reconciliation exists where required.

### Data Engineering / AI

Where applicable:

- [ ] Data quality requirements exist.
- [ ] Pipeline failure behavior is defined.
- [ ] Backfill is possible.
- [ ] Data drift is considered.
- [ ] Model/data lineage is understood.

### Operations

- [ ] Data observability is appropriate.
- [ ] Capacity and cost are understood.
- [ ] Ownership of operational responsibilities is clear.

---

# Relationship to Other Standards

Data Architecture builds upon:

- Architecture Principles
- System Boundaries
- Component Boundaries
- Communication Patterns
- Consistency and Concurrency
- Failure Domains

It provides the foundation for later decisions involving:

- persistence,
- caching,
- event-driven architecture,
- analytics,
- data platforms,
- privacy engineering,
- disaster recovery,
- AI/ML systems.

---

# Final Principle

> **Good data architecture is not about choosing the right database. It is about making ownership, correctness, lifecycle, consistency, security, recovery, and deletion explicit before the system becomes dependent on the data.**
