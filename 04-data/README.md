# 04 — Data Engineering Baseline

> Data is a system responsibility, not merely a persistence concern.

---

**Status:** Engineering Baseline

**Version:** 1.0

**Applies To:** All projects that create, store, process, exchange, derive, or consume data

---

# Purpose

The Data Engineering Baseline defines the minimum engineering expectations for how a project designs, manages, protects, evolves, and recovers its data.

It exists because data decisions have consequences far beyond the database.

A data decision can affect:

- application correctness,
- system availability,
- scalability,
- security,
- privacy,
- compliance,
- recoverability,
- operational cost,
- analytics,
- integrations,
- future architectural change.

Therefore, data architecture must be considered an engineering concern from the beginning of a project.

---

# What This Domain Covers

The `04-data` domain establishes engineering guidance for:

- data ownership,
- authoritative sources,
- persistence,
- data lifecycle,
- data consistency,
- data integrity,
- data quality,
- data evolution,
- data recovery,
- data retention,
- data deletion,
- derived data,
- caching,
- replication,
- analytical data,
- data pipelines.

It does **not** prescribe a particular database, cloud platform, vendor, or architectural style.

---

# Core Principle

> **Every important dataset must have a clear owner, defined meaning, known lifecycle, appropriate consistency and integrity guarantees, controlled access, and a deliberate recovery and deletion strategy.**

---

# Why Data Has Its Own Domain

Data cuts across the entire system.

Consider a typical production system:

```text
                ┌───────────────┐
                │   Users /     │
                │   Consumers   │
                └───────┬───────┘
                        │
                        ▼
                ┌───────────────┐
                │ Application   │
                │ Components    │
                └───────┬───────┘
                        │
              ┌─────────┴─────────┐
              ▼                   ▼
       ┌──────────────┐    ┌──────────────┐
       │ Authoritative│    │ Derived Data │
       │ Data         │    │ / Analytics  │
       └──────────────┘    └──────────────┘
              │                   │
              └─────────┬─────────┘
                        ▼
                 Recovery / Backup
```

The database is only one part of this picture.

Data may exist simultaneously in:

- primary stores,
- replicas,
- caches,
- search indexes,
- event streams,
- object storage,
- analytics platforms,
- logs,
- backups,
- derived datasets.

A project therefore needs to understand the **entire data lifecycle**, not just where the primary database lives.

---

# Relationship With Architecture

The `03-architecture` domain establishes the broader system architecture.

It answers questions such as:

- What are the system boundaries?
- What are the component boundaries?
- How do components communicate?
- What happens when components operate concurrently?
- How do failures propagate?

The `04-data` domain builds upon those decisions.

For example:

```text
03 — Architecture
        │
        ├── System Boundaries
        ├── Component Boundaries
        ├── Communication
        ├── Consistency
        └── Failure Domains
                    │
                    ▼
04 — Data
        │
        ├── Ownership
        ├── Persistence
        ├── Lifecycle
        ├── Integrity
        ├── Quality
        └── Recovery
```

This separation prevents database decisions from being made independently of system architecture.

---

# Data Is Not Automatically the Source of Truth

A system may contain multiple representations of the same business information.

For example:

```text
                 ┌──► Cache
                 │
Authoritative ───┼──► Search Index
State            │
                 ├──► Read Model
                 │
                 └──► Analytics
```

These representations may legitimately exist.

However, the architecture must identify:

> **Which representation is authoritative?**

Without that distinction, the system can develop competing versions of reality.

---

# Data Ownership

Every important dataset should have an identifiable owner.

Ownership includes responsibility for:

- correctness,
- schema,
- access,
- lifecycle,
- retention,
- recovery,
- deletion,
- evolution.

A component consuming data does not necessarily own that data.

The project should explicitly distinguish:

```text
Data Owner
```

from:

```text
Data Consumer
```

---

# Data Lifecycle

Data should have a defined lifecycle.

A typical lifecycle may look like:

```text
Created
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

Not every dataset requires every stage.

The important requirement is that the lifecycle is intentional.

For important datasets, the project should be able to answer:

- When is it created?
- When is it modified?
- When does it become inactive?
- How long is it retained?
- When can it be deleted?
- What happens to derived copies?

---

# Data Correctness

Data correctness is a business concern.

The project should identify important invariants.

Examples:

```text
An order cannot be both Cancelled and Fulfilled.

A payment cannot be successfully captured twice.

A unique identifier cannot belong to two entities.

Inventory cannot be reduced below the permitted business boundary.
```

Where possible, important invariants should be enforced by the strongest appropriate mechanism.

---

# Data Consistency

Not every dataset requires the same consistency guarantees.

A project should explicitly determine whether data requires:

- strong consistency,
- eventual consistency,
- bounded staleness,
- read-after-write behavior,
- domain-specific consistency.

The appropriate choice depends on business requirements.

Consistency should therefore be a deliberate architecture decision rather than a default assumption.

---

# Data Quality

Operational correctness and data quality are related but different concerns.

A pipeline can be:

```text
Operationally Healthy
```

while producing:

```text
Incorrect Data
```

Where relevant, projects should establish expectations around:

- completeness,
- validity,
- uniqueness,
- accuracy,
- freshness,
- consistency.

For data-intensive systems, these expectations should be observable.

---

# Data Evolution

Data structures change over time.

The baseline therefore expects projects to consider:

- schema evolution,
- compatibility,
- migrations,
- versioning,
- rolling deployments,
- historical data,
- backward compatibility.

A production system should not assume that all components can change simultaneously.

---

# Data Recovery

Data that cannot be recovered when required is a production risk.

Projects should determine:

- what data must be backed up,
- how frequently,
- how long backups are retained,
- where backups are stored,
- how restoration works,
- how restoration is validated.

A successful backup job does not prove that recovery works.

Where the business impact justifies it:

> **Restore procedures must be tested.**

---

# Data Deletion

Deletion must be considered as carefully as creation.

A project should know where important data exists.

For example:

```text
User Data
   │
   ├──► Primary Database
   ├──► Replica
   ├──► Cache
   ├──► Search Index
   ├──► Analytics
   ├──► Object Storage
   └──► Backup
```

If data must be deleted, the project must understand how deletion propagates across the relevant representations.

This becomes especially important when privacy or regulatory requirements apply.

---

# Data Security

Security is governed primarily by the `06-security` domain.

However, data architecture must provide the information required by security engineering.

This includes:

- data classification,
- ownership,
- sensitivity,
- access patterns,
- retention,
- deletion requirements,
- data flows.

Therefore:

> **`04-data` defines what the data requires; `06-security` defines the security controls that protect it.**

---

# Data and Privacy

Privacy requirements can create architectural constraints.

Examples include:

- data minimization,
- retention limits,
- deletion,
- anonymization,
- regional storage,
- restricted access.

Privacy should not be treated as a feature added after the data model has been finalized.

---

# Data and Reliability

Data architecture is tightly coupled with reliability.

A failure may result in:

- data loss,
- duplicate writes,
- partial updates,
- stale replicas,
- corrupted derived data,
- failed migrations.

The `05-reliability` domain addresses the broader reliability engineering practices.

The `04-data` domain identifies the **data-specific consequences and requirements**.

---

# Data and AI / Machine Learning

AI and ML systems introduce additional data concerns.

Where applicable, projects should consider:

- training data lineage,
- dataset versioning,
- data quality,
- feature consistency,
- data drift,
- pipeline failures,
- backfills,
- reproducibility.

These concerns may be expanded in:

`10-ai-and-data-engineering/`

The `04-data` baseline remains the foundational data contract.

---

# Data Technology Selection

This baseline does not mandate:

- PostgreSQL,
- MySQL,
- MongoDB,
- DynamoDB,
- Cassandra,
- Redis,
- Kafka,
- Snowflake,
- any particular cloud provider.

Technology should follow requirements.

The project should be able to explain:

> **Why is this data technology appropriate for this workload?**

rather than:

> **Which database does the organization normally use?**

---

# Polyglot Persistence

Multiple data technologies may be appropriate for different workloads.

For example:

```text
                 ┌──► Transactional Store
                 │
Business Data ───┼──► Search
                 │
                 ├──► Cache
                 │
                 └──► Analytics
```

Each additional datastore introduces:

- operational cost,
- synchronization concerns,
- security responsibilities,
- backup requirements,
- monitoring requirements.

Therefore:

> **Every additional datastore should have a clear architectural reason to exist.**

---

# Project-Level Application

This baseline is mandatory across projects.

However, implementation depth is determined by:

- system tier,
- business criticality,
- data sensitivity,
- regulatory requirements,
- scale,
- recovery objectives,
- workload characteristics.

A small internal application and a payment platform do not require identical data architecture.

They require the same **engineering reasoning**, applied at different levels of rigor.

---

# Minimum Project Questions

Every project should be able to answer the following.

### Ownership

- Who owns each important dataset?
- What is the authoritative source?

### Correctness

- What invariants must remain true?
- What consistency guarantees are required?

### Lifecycle

- When is data created?
- How long is it retained?
- When is it deleted?

### Security

- What data is sensitive?
- Who can access it?

### Recovery

- What data must be recoverable?
- What are the recovery objectives?
- Has restoration been tested where required?

### Evolution

- How will the schema change?
- How will migrations be performed?

### Derived Data

- What copies or projections exist?
- How are they rebuilt?

### Operations

- How is data health observed?
- Who owns operational issues?

---

# Documents in This Domain

The `04-data` domain currently contains:

```text
04-data/
├── README.md
├── data-architecture.md
├── data-lifecycle.md
├── data-quality-and-integrity.md
└── data-reliability-and-recovery.md
```

The documents are intentionally separated so that teams can consume and apply the relevant engineering concerns without turning the baseline into one large governance document.

---

# Relationship With Project Governance

Project Governance determines:

- which requirements apply,
- what evidence is required,
- who approves exceptions,
- how compliance is demonstrated.

The Data domain defines the engineering expectations.

Therefore:

```text
Project Governance
        │
        ▼
Determines Applicability
        │
        ▼
04 — Data Engineering Baseline
        │
        ▼
Project Architecture Profile
        │
        ▼
Implementation Evidence
```

---

# Exceptions

A project may require an exception from a baseline requirement.

Exceptions must not silently bypass the baseline.

The project should document:

- requirement being deviated from,
- reason,
- risk introduced,
- compensating controls,
- owner,
- approval,
- review or expiry date where appropriate.

The objective is not bureaucratic compliance.

The objective is **conscious engineering trade-offs**.

---

# Final Principle

> **A production system should always know what its important data means, who owns it, where the authoritative state lives, how it changes, how long it exists, how it is protected, how it is recovered, and how it can eventually be removed.**

Data architecture is therefore not a database selection exercise.

It is the engineering discipline that makes the system's relationship with its data explicit.
