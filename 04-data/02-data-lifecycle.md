# Data Lifecycle

> Data should have an intentional beginning, useful lifetime, controlled retention, and deliberate end.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Data Engineering

**Applies To:** Every project that creates, stores, processes, exchanges, or derives persistent data

---

# Purpose

Data rarely exists forever.

It is:

- created,
- modified,
- consumed,
- replicated,
- transformed,
- archived,
- retained,
- eventually deleted.

If these stages are not designed deliberately, data tends to accumulate indefinitely.

That creates:

- unnecessary cost,
- security exposure,
- privacy risk,
- operational complexity,
- compliance risk,
- difficult migrations,
- unclear ownership.

This standard establishes the baseline for designing the lifecycle of important data.

---

# Engineering Principle

> **Every important dataset should have an intentional lifecycle from creation through eventual disposal, with ownership, retention, access, archival, and deletion decisions made explicitly.**

---

# 1. Identify the Dataset

Before defining its lifecycle, identify what the data represents.

For each significant dataset, document:

- business meaning,
- owner,
- producer,
- consumers,
- sensitivity,
- authoritative source,
- lifecycle requirements.

Do not define lifecycle rules for a technical table without understanding the business data represented by it.

---

# 2. Lifecycle Stages

A useful generic model is:

```text
Created
   │
   ▼
Active
   │
   ├──────────────► Modified
   │
   ▼
Inactive
   │
   ▼
Archived
   │
   ▼
Deleted
```

Not every dataset requires every stage.

For example:

- temporary data may go directly from active to deletion,
- analytical data may be retained for years,
- transactional data may require archival,
- cached data may expire automatically.

The lifecycle must reflect actual business requirements.

---

# 3. Data Creation

The lifecycle begins when data is created.

Creation should have defined semantics.

Determine:

- who or what creates it,
- what makes it valid,
- required fields,
- initial state,
- ownership,
- timestamp semantics,
- identifier generation.

Where data creation represents a business event, the system should make the business meaning explicit.

---

# 4. Data Validity

Data should not become part of authoritative state merely because a record was technically written.

Where appropriate, define validation for:

- required fields,
- valid values,
- relationships,
- uniqueness,
- business constraints.

Validation should occur at appropriate boundaries.

---

# 5. Active Data

Active data is data currently required for normal business operations.

Active data generally requires:

- normal availability,
- appropriate access controls,
- monitoring,
- integrity protection,
- defined modification semantics.

The project should understand which data is operationally critical and which is merely retained for reference.

---

# 6. Data Modification

Data changes over time.

The project should define:

- who may modify it,
- what changes are permitted,
- whether history is required,
- whether changes must be auditable,
- how concurrent modifications are handled.

Where historical state matters, overwriting the current value may not be sufficient.

---

# 7. Historical Data

Some domains require knowledge of previous states.

Examples include:

- financial records,
- contractual information,
- configuration changes,
- audit history.

For such data, determine whether the system needs:

- change history,
- versioning,
- append-only records,
- audit records,
- snapshots.

Do not introduce historical storage merely because it is technically possible.

---

# 8. Inactive Data

Data may remain valid but no longer be actively used.

Examples:

- completed orders,
- closed accounts,
- expired configurations,
- historical transactions.

Inactive data should have an explicit lifecycle state where the business domain requires one.

Do not infer inactivity solely from age when business semantics matter.

---

# 9. Retention

Retention defines how long data should remain available.

Retention decisions should consider:

- business needs,
- legal requirements,
- regulatory obligations,
- contractual requirements,
- operational requirements,
- privacy requirements,
- cost.

The default should not be:

> "Keep everything forever."

---

# 10. Retention Classification

Projects should classify important data according to retention requirements.

For example:

| Category | Typical Requirement |
|---|---|
| Temporary | Delete after processing |
| Operational | Retain while actively required |
| Historical | Retain for defined period |
| Regulatory | Retain according to obligation |
| Audit | Retain according to governance requirement |
| Derived | Retain only while useful/reproducible |

The exact categories should be defined by the organization or project.

---

# 11. Time-Based Retention

Some datasets can be retained according to age.

For example:

```text
Data < 30 days
    │
    ▼
Active

Data 30–365 days
    │
    ▼
Archive

Data > 365 days
    │
    ▼
Delete
```

The actual values must come from business and regulatory requirements.

They should never be copied blindly between projects.

---

# 12. Event-Based Retention

Not all retention decisions are time-based.

A dataset may become eligible for archival or deletion when a business event occurs.

Examples:

- account closed,
- contract terminated,
- order completed,
- project cancelled.

The lifecycle should support business semantics where required.

---

# 13. Archival

Archival moves data away from primary operational systems while preserving it for future use.

Potential reasons include:

- reduced operational storage,
- performance,
- long-term retention,
- compliance,
- historical analysis.

Archival should define:

- what is archived,
- when,
- where,
- how it is retrieved,
- how it is protected,
- when it can be deleted.

---

# 14. Archive Is Not Delete

Archiving does not mean deletion.

An archived record may still be:

- sensitive,
- regulated,
- subject to access controls,
- subject to retention rules,
- subject to deletion requirements.

Moving data to cheaper storage does not remove its governance obligations.

---

# 15. Retrieval From Archive

If archived data may be required, the project should understand:

- how it is located,
- how it is retrieved,
- expected retrieval time,
- required permissions,
- integrity validation.

An archive that technically exists but cannot be practically retrieved is not a useful recovery mechanism.

---

# 16. Deletion

Deletion is the final lifecycle stage for data that no longer needs to exist.

The project should define:

- deletion trigger,
- authorization,
- scope,
- execution mechanism,
- verification,
- handling of dependent data.

Deletion should be intentional rather than an accidental consequence of storage failure.

---

# 17. Logical vs Physical Deletion

Some systems distinguish between:

```text
Logical Deletion
```

and:

```text
Physical Deletion
```

Logical deletion may mark data as inactive without immediately removing it.

Physical deletion removes the stored representation.

The choice depends on:

- business requirements,
- audit requirements,
- privacy requirements,
- storage architecture.

Logical deletion must not be treated as equivalent to privacy deletion when actual erasure is required.

---

# 18. Cascading Deletion

Deleting one entity may affect related data.

For example:

```text
Customer
   │
   ├── Orders
   ├── Preferences
   ├── Notifications
   └── Analytics Records
```

The project must understand whether related data should be:

- deleted,
- anonymized,
- retained independently,
- transformed.

Do not assume database-level cascading deletion automatically represents the correct business behavior.

---

# 19. Derived Data

Data may exist in multiple derived forms.

For example:

```text
Authoritative Data
       │
       ├──► Cache
       ├──► Search Index
       ├──► Reporting Store
       └──► Analytics Dataset
```

Lifecycle management must account for these representations.

A deletion or retention decision is incomplete if important derived copies remain indefinitely.

---

# 20. Event Stores

Event-driven systems introduce additional lifecycle considerations.

Events may contain:

- personal information,
- business state,
- historical records,
- identifiers.

If events are immutable, deletion can become difficult.

Before adopting long-lived event storage, determine:

- retention,
- privacy requirements,
- deletion strategy,
- compaction,
- anonymization,
- downstream propagation.

---

# 21. Backups and Retention

Backups are themselves data.

Therefore they require their own lifecycle.

For example:

```text
Primary Data
     │
     ▼
Backup
     │
     ▼
Backup Retention
     │
     ▼
Backup Expiration
     │
     ▼
Deletion
```

A production deletion strategy must consider whether old backups continue to contain the deleted data and what the applicable requirements are.

---

# 22. Replicas and Copies

Replicas are also part of the data lifecycle.

The project should understand:

- how replicas are created,
- how changes propagate,
- how stale data behaves,
- how deleted data propagates,
- what happens when replication fails.

A replica should not accidentally become a permanent uncontrolled copy.

---

# 23. Caches

Caches usually have shorter lifecycles than authoritative data.

The project should define:

- expiration,
- invalidation,
- maximum acceptable staleness,
- behavior after cache loss.

A cache should not silently extend the effective lifetime of sensitive data beyond the intended retention policy.

---

# 24. Temporary Data

Temporary data should have an explicit cleanup mechanism.

Examples include:

- temporary files,
- processing artifacts,
- staging tables,
- intermediate datasets,
- transient messages.

Temporary data without cleanup can become permanent operational debt.

---

# 25. Data Lifecycle and Privacy

Privacy requirements may impose explicit lifecycle constraints.

The project should determine:

- what personal data exists,
- why it is retained,
- how long it is retained,
- who can access it,
- when it must be deleted,
- how deletion propagates.

Data minimization and deletion should be considered during design, not after implementation.

---

# 26. Data Lifecycle and Security

Lifecycle changes may alter the security requirements of data.

For example:

```text
Active Data
     │
     ▼
Archived Data
     │
     ▼
Deleted Data
```

Each stage may require different:

- access controls,
- encryption,
- monitoring,
- storage mechanisms.

Moving data to an archive does not remove its security requirements.

---

# 27. Lifecycle Automation

Where lifecycle decisions are deterministic, automation should be preferred.

Examples include:

- automated expiration,
- archival jobs,
- retention policies,
- scheduled cleanup,
- object lifecycle policies.

Manual cleanup is more likely to be:

- forgotten,
- inconsistent,
- difficult to audit,
- difficult to scale.

---

# 28. Lifecycle Safety

Automated deletion is powerful and dangerous.

Before enabling automated deletion, establish:

- correct eligibility criteria,
- safeguards,
- authorization,
- monitoring,
- failure handling,
- auditability,
- recovery strategy where applicable.

A lifecycle automation bug can cause large-scale irreversible data loss.

---

# 29. Data Lifecycle and Recovery

Recovery architecture must account for lifecycle state.

Restoring an old backup may resurrect data that should no longer exist.

Therefore recovery procedures should consider:

- retention policies,
- deletion requirements,
- current lifecycle state,
- legal constraints,
- downstream consistency.

Restoration is not simply:

> "Put yesterday's database back."

---

# 30. Data Lifecycle and Migration

Migration can temporarily create multiple copies of the same dataset.

For example:

```text
Old Store
   │
   ├──────────────► Migration
   │
   ▼
New Store
```

During migration, both representations may exist.

The project should define:

- which is authoritative,
- synchronization,
- migration completion,
- validation,
- rollback,
- old-data cleanup.

Migration should include a plan for eventually removing obsolete data structures.

---

# 31. Lifecycle Observability

Important lifecycle operations should be observable.

Depending on risk, monitor:

- records approaching expiration,
- archival failures,
- deletion failures,
- unexpected data growth,
- replication lag,
- cleanup failures.

Lifecycle automation without visibility can silently fail for months.

---

# 32. Lifecycle Ownership

Someone must own the lifecycle.

Ownership should include:

- defining policies,
- implementing automation,
- reviewing failures,
- handling exceptions,
- validating compliance.

A policy without an owner is not an operational control.

---

# 33. Lifecycle Exceptions

Some data may require exceptional retention.

Examples include:

- legal holds,
- investigations,
- regulatory requirements,
- contractual obligations.

Exceptions should be:

- explicit,
- authorized,
- traceable,
- time-bounded where appropriate.

---

# 34. Data Lifecycle Review

For each significant dataset, the project should be able to answer:

| Question | Answer |
|---|---|
| What is this data? | |
| Who owns it? | |
| Where is the authoritative copy? | |
| When is it created? | |
| How is it modified? | |
| When does it become inactive? | |
| How long is it retained? | |
| Is it archived? | |
| Where are copies stored? | |
| When is it deleted? | |
| How is deletion verified? | |
| What happens to backups? | |
| What happens to derived data? | |

---

# Minimum Engineering Requirements

Every production project should:

- [ ] Identify lifecycle stages for important datasets.
- [ ] Define retention requirements.
- [ ] Identify data ownership.
- [ ] Identify authoritative sources.
- [ ] Identify important derived copies.
- [ ] Define deletion behavior where applicable.
- [ ] Consider backups and replicas as part of the lifecycle.
- [ ] Automate deterministic lifecycle operations where practical.
- [ ] Protect automated deletion with appropriate safeguards.
- [ ] Define ownership for lifecycle operations.

Additional controls should be applied according to:

- system tier,
- data sensitivity,
- regulatory requirements,
- business criticality,
- scale.

---

# Relationship With Other Data Standards

This document works with:

- `data-architecture.md`
- `data-quality-and-integrity.md`
- `data-reliability-and-recovery.md`

It also interacts with:

- `06-security/`
- `08-observability/`
- `11-operational-readiness/`

The lifecycle defines **what should happen to data over time**.

The other standards define how the system maintains correctness, protects the data, observes lifecycle operations, and recovers from failure.

---

# Final Principle

> **Data that has no defined end tends to become permanent. Permanent data creates permanent responsibility. Design the lifecycle before the data begins accumulating.**
