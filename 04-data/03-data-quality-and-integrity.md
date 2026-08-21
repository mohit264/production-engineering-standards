# Data Quality and Integrity

> A system is not healthy merely because its services are running. If the data it produces is incomplete, contradictory, stale, or incorrect, the system is already failing.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Data Engineering

**Applies To:** Every project where data influences business behavior, reporting, integrations, analytics, or automated decisions

---

# Purpose

Data quality and data integrity address two related but different questions:

> **Is the data valid and trustworthy?**

and:

> **Does the system prevent invalid states from being created or propagated?**

A production system can be:

- available,
- fast,
- observable,
- secure,

and still produce incorrect business outcomes because its data is wrong.

This standard establishes baseline expectations for preventing, detecting, containing, and correcting data quality and integrity problems.

---

# Engineering Principle

> **Important business invariants should be explicitly defined, protected at the strongest appropriate boundary, and continuously observable where practical.**

---

# 1. Data Quality vs Data Integrity

These concepts should not be treated as interchangeable.

### Data Integrity

Concerns whether data remains structurally and logically correct.

Examples:

- unique identifiers remain unique,
- required relationships remain valid,
- an order cannot reference a nonexistent customer,
- a transaction cannot be recorded twice when uniqueness is required.

### Data Quality

Concerns whether the data is useful and trustworthy for its intended purpose.

Examples:

- customer email is valid,
- analytics data arrives on time,
- required fields are complete,
- measurements remain within expected ranges.

A system can have strong database integrity while still producing poor-quality business data.

---

# 2. Define Business Invariants

The first step is identifying what must always remain true.

Examples:

```text
An order must belong to an existing customer.

A payment cannot be successfully captured twice.

An inventory quantity cannot become negative
when the business invariant forbids negative inventory.

A completed order cannot silently return to an arbitrary earlier state.
```

These rules should be written down before deciding how they will be enforced.

---

# 3. Protect Invariants at the Strongest Appropriate Boundary

If a rule can be safely enforced by the data store, consider enforcing it there.

Examples include:

- uniqueness,
- referential integrity,
- non-null requirements,
- valid ranges.

Application validation may still be required for business rules that the datastore cannot reasonably enforce.

The principle is:

> **Do not rely on a weaker layer when a stronger appropriate enforcement mechanism exists.**

---

# 4. Validation

Validation should occur at appropriate boundaries.

Potential validation points include:

```text
External Input
      │
      ▼
API Boundary
      │
      ▼
Application Logic
      │
      ▼
Persistence Boundary
```

Validation should not assume that every caller is trustworthy.

Internal services, scripts, migrations, administrative tools, and batch jobs can also introduce invalid data.

---

# 5. Required Data

For important fields, determine whether the value is:

- mandatory,
- optional,
- conditionally required.

Avoid representing missing information ambiguously.

For example, these may have different meanings:

```text
NULL
""
"unknown"
"not applicable"
"0"
```

The data model should preserve those distinctions where the business meaning requires them.

---

# 6. Uniqueness

Where a business identifier must be unique, uniqueness should be enforced appropriately.

Application logic such as:

```text
Check whether value exists
        │
        ▼
Insert value
```

can fail under concurrency because another operation may insert the same value between those steps.

Where appropriate, enforce uniqueness atomically at the persistence boundary.

---

# 7. Referential Integrity

Relationships between entities should remain valid.

For example:

```text
Order
  │
  ▼
Customer
```

An order should not reference a customer that does not exist when the domain requires that relationship.

The appropriate mechanism may be:

- database constraints,
- application validation,
- service-level contracts,
- reconciliation.

The correct choice depends on the architecture.

---

# 8. State Integrity

Many business entities have lifecycle states.

For example:

```text
Created
   │
   ▼
Confirmed
   │
   ▼
Completed
```

The system should define which transitions are valid.

Avoid allowing arbitrary state changes such as:

```text
Completed ─────────► Created
```

unless the business explicitly supports that transition.

---

# 9. State Machines

Where an entity has meaningful lifecycle states, consider explicitly modeling:

- valid states,
- valid transitions,
- invalid transitions,
- terminal states,
- recovery transitions.

This makes business correctness easier to reason about.

---

# 10. Idempotency

Distributed systems may deliver the same operation more than once.

For operations where duplication would be harmful, the system should define idempotency semantics.

For example:

```text
Request ID: 12345

First attempt  ──► Process
Second attempt ──► Recognize existing operation
```

Idempotency is particularly important for:

- payments,
- order creation,
- message processing,
- external integrations,
- retries.

---

# 11. Duplicate Data

Duplicates may be either:

- valid,
- invalid,
- temporarily expected.

The architecture should distinguish these cases.

For example, two identical business events may represent:

```text
Two legitimate transactions
```

or:

```text
One transaction processed twice
```

The system must use business semantics rather than visual similarity to determine correctness.

---

# 12. Concurrency

Multiple actors may modify the same data simultaneously.

For important concurrent operations, determine:

- what can happen concurrently,
- which operations conflict,
- what must be serialized,
- what consistency is required.

Possible mechanisms include:

- transactions,
- optimistic concurrency,
- pessimistic locking,
- version checks,
- atomic operations.

The mechanism should follow the invariant being protected.

---

# 13. Lost Updates

Consider:

```text
Initial Value = 10

Actor A reads 10
Actor B reads 10

Actor A writes 11
Actor B writes 11
```

The final value may be:

```text
11
```

even though two updates occurred.

If the business expected:

```text
12
```

the system has lost an update.

Projects should identify where this class of failure matters.

---

# 14. Versioning

Version information can help detect concurrent modifications.

Conceptually:

```text
Read version 7

        │
        ▼

Update only if version = 7

        │
        ▼

Version becomes 8
```

If another writer already changed the record to version 8, the update can be rejected and handled explicitly.

---

# 15. Stale Data

Data may become stale because of:

- caching,
- replication,
- asynchronous processing,
- delayed events,
- failed synchronization.

For important data, define the maximum acceptable staleness.

Examples:

```text
Payment Status
→ Near-real-time requirement

Product Recommendation
→ Minutes or hours may be acceptable
```

The correct tolerance depends on the business capability.

---

# 16. Data Freshness

Freshness should be measurable where it matters.

Potential signals include:

- last successful update,
- event processing delay,
- replication lag,
- pipeline delay,
- age of latest record.

A pipeline that is running but producing yesterday's data may still be operationally unhealthy.

---

# 17. Completeness

Completeness concerns whether the expected data exists.

Examples:

- expected records are missing,
- required fields are empty,
- a batch contains fewer records than expected.

Where completeness matters, define appropriate expectations.

These may be:

- exact counts,
- minimum thresholds,
- percentage-based expectations,
- reconciliation against a source.

---

# 18. Validity

Values should conform to their intended semantics.

Examples:

```text
Age < 0
Invalid

Currency = "XYZ"
Potentially Invalid

Order Status = "BANANA"
Invalid
```

Validation rules should be derived from the domain rather than arbitrary assumptions.

---

# 19. Accuracy

Accuracy asks whether stored data represents reality correctly.

For example:

```text
System says:
Inventory = 100

Physical reality:
Inventory = 73
```

The database may be internally consistent while still being wrong.

Accuracy may require:

- reconciliation,
- external verification,
- domain controls,
- human review.

---

# 20. Consistency Across Representations

When the same business information exists in multiple places, discrepancies may occur.

For example:

```text
Primary Store ──► Search Index
       │
       └────────► Analytics Store
```

The project should define:

- authoritative source,
- synchronization mechanism,
- acceptable delay,
- reconciliation strategy.

Do not assume replication automatically means correctness.

---

# 21. Reconciliation

Reconciliation compares independent representations of state to detect divergence.

For example:

```text
Source State
     │
     ├──────── Compare ────────┐
     │                         │
     ▼                         ▼
System A                    System B
```

Reconciliation is particularly useful when:

- systems are eventually consistent,
- integrations can fail,
- events can be lost,
- external systems are authoritative.

---

# 22. Data Corruption

Data corruption can occur because of:

- application defects,
- incorrect migrations,
- serialization errors,
- concurrency bugs,
- manual intervention,
- infrastructure failures,
- integration defects.

The architecture should consider how corruption would be:

1. prevented,
2. detected,
3. contained,
4. corrected.

---

# 23. Detection

Not every integrity violation can be prevented.

Therefore systems should also detect anomalies.

Potential mechanisms include:

- validation,
- database constraints,
- reconciliation,
- checksums,
- anomaly detection,
- audit trails,
- business-rule monitoring.

Detection should focus on failures that matter to the business.

---

# 24. Correction

Detection without correction can leave a system permanently inconsistent.

For important data problems, define whether correction can occur through:

- automatic repair,
- replay,
- reconciliation,
- compensating transactions,
- controlled manual intervention.

Correction procedures should be auditable where appropriate.

---

# 25. Data Quality Gates

For data pipelines and critical integrations, quality gates may prevent bad data from progressing downstream.

For example:

```text
Ingestion
   │
   ▼
Validation
   │
   ├── Invalid ──► Quarantine
   │
   ▼
Accepted Data
   │
   ▼
Processing
```

Quality gates should be proportional to the consequences of incorrect data.

---

# 26. Quarantine

When data cannot safely be processed, quarantine may be preferable to silently accepting it.

Quarantine should provide enough information to determine:

- what failed,
- why it failed,
- when it failed,
- where it originated,
- whether it can be corrected and replayed.

---

# 27. Poison Data

A malformed record can repeatedly fail processing.

For example:

```text
Message
   │
   ▼
Consumer
   │
   X
   │
   ▼
Retry
   │
   X
   │
   ▼
Retry
```

Without appropriate handling, one bad record can block an entire stream or queue.

Systems processing asynchronous data should define behavior for permanently unprocessable records.

---

# 28. Data Lineage

For important derived data, the project should understand where the data came from.

A simplified lineage model:

```text
Source
   │
   ▼
Transformation
   │
   ▼
Derived Dataset
   │
   ▼
Consumer
```

Lineage becomes particularly important for:

- analytics,
- regulatory reporting,
- machine learning,
- financial data,
- business-critical dashboards.

---

# 29. Data Provenance

Provenance answers questions such as:

- Where did this value originate?
- Which transformation produced it?
- When was it produced?
- Which version of the logic was used?

The required depth depends on the business and regulatory context.

---

# 30. Schema Validation

Data exchanged between systems should have an explicit contract where appropriate.

The contract may define:

- fields,
- types,
- required values,
- allowed values,
- version,
- compatibility rules.

This is especially important for:

- APIs,
- events,
- files,
- data pipelines.

---

# 31. Schema Evolution

Schemas change.

A change should consider:

- existing producers,
- existing consumers,
- historical data,
- rolling deployments,
- compatibility.

Avoid assuming that all consumers will upgrade simultaneously.

---

# 32. Backward Compatibility

When changing a data contract, determine whether existing consumers can continue operating.

Potential strategies include:

- additive changes,
- versioned contracts,
- compatibility windows,
- migration phases.

The correct strategy depends on the communication architecture.

---

# 33. Data Quality in Batch Processing

Batch systems should consider:

- expected input volume,
- missing input,
- duplicate input,
- partial processing,
- failed records,
- output completeness.

A batch job that exits successfully after processing only half the expected data is a data failure even if its process exit code is zero.

---

# 34. Data Quality in Streaming Systems

Streaming systems should consider:

- duplicate events,
- out-of-order events,
- delayed events,
- missing events,
- schema changes,
- consumer lag.

Correctness should be defined according to the business meaning of the stream.

---

# 35. Data Quality in AI / ML Systems

Where applicable, AI/ML systems introduce additional dimensions.

Consider:

- training data quality,
- labeling quality,
- feature validity,
- data drift,
- distribution changes,
- dataset lineage,
- evaluation-data integrity.

A model can behave incorrectly even when the inference service is technically healthy.

---

# 36. Data Drift

A dataset may remain structurally valid while its statistical properties change.

For example:

```text
Training Distribution
        │
        ▼
Production Distribution
        │
        X
 Significant Shift
```

Where model behavior depends on data distribution, projects should determine whether drift monitoring is required.

---

# 37. Human-Generated Data

Human input can introduce:

- typos,
- inconsistent formats,
- ambiguous values,
- intentional manipulation.

Validation should therefore be designed around the actual source of data.

Do not assume human-generated input is inherently trustworthy.

---

# 38. External Data

External systems can produce incorrect or unexpected data.

Integrations should consider:

- schema changes,
- missing fields,
- invalid values,
- duplicate messages,
- delayed responses,
- inconsistent semantics.

External data should not automatically be treated as authoritative merely because it comes from another system.

---

# 39. Manual Data Changes

Production data should not be changed casually through ad-hoc commands.

Where manual intervention is necessary, establish appropriate controls around:

- authorization,
- review,
- auditability,
- rollback,
- validation.

Manual intervention should be treated as a controlled operational activity.

---

# 40. Data Quality Observability

Important quality dimensions should be observable.

Depending on the system, this may include:

- freshness,
- completeness,
- validity,
- duplication,
- reconciliation differences,
- failed validations,
- rejected records.

The objective is to detect meaningful data failures before they become business failures.

---

# 41. Data Quality Alerts

Alerts should focus on actionable conditions.

Good alert:

```text
Payment reconciliation mismatch exceeds defined threshold.
```

Poor alert:

```text
Database table changed.
```

Alerts should indicate a condition that requires investigation or action.

---

# 42. Quality Thresholds

Thresholds should reflect business consequences.

For example:

```text
99.9% complete
```

may be acceptable for one analytical dataset but unacceptable for:

```text
Financial settlement records
```

There is no universal quality threshold.

---

# 43. Data Quality Ownership

Someone must own important quality dimensions.

Ownership may belong to:

- application teams,
- data engineering teams,
- domain teams,
- platform teams,
- business owners.

Technical ownership and business ownership may be different.

Both should be understood where relevant.

---

# 44. Data Quality Incident

A data-quality incident should be treated as an engineering incident when it materially affects the business.

Examples include:

- incorrect customer balances,
- missing transactions,
- duplicate payments,
- incorrect reports,
- corrupted datasets.

Incident response should determine:

- affected data,
- affected users,
- root cause,
- correction,
- prevention.

---

# 45. Quality vs Availability

A system that returns incorrect data may be worse than a system that temporarily refuses to respond.

For example:

```text
Incorrect Payment Status
```

may cause more harm than:

```text
Payment Status Temporarily Unavailable
```

Architecture should therefore explicitly consider whether:

> **Failing safely is preferable to returning incorrect data.**

---

# 46. Minimum Engineering Requirements

Every production project should:

- [ ] Identify important business invariants.
- [ ] Define validation requirements.
- [ ] Protect critical integrity constraints.
- [ ] Define concurrency behavior where relevant.
- [ ] Identify duplicate-processing risks.
- [ ] Define freshness expectations for important data.
- [ ] Identify important derived representations.
- [ ] Define reconciliation requirements where divergence is possible.
- [ ] Define handling for invalid or unprocessable data.
- [ ] Establish appropriate data-quality observability.

Additional requirements should be determined by:

- system tier,
- business criticality,
- data sensitivity,
- workload characteristics,
- regulatory obligations.

---

# Relationship With Other Standards

This document works with:

- `data-architecture.md`
- `data-lifecycle.md`
- `data-reliability-and-recovery.md`

It also depends on concepts defined in:

- `03-architecture/consistency-and-concurrency.md`
- `03-architecture/failure-domains.md`
- `06-security/`
- `08-observability/`

The distinction is important:

**Data Architecture**

> What data exists, who owns it, and how it is structured.

**Data Lifecycle**

> What happens to the data over time.

**Data Quality and Integrity**

> Whether the data remains correct and trustworthy.

**Data Reliability and Recovery**

> How the data survives failures and can be restored.

---

# Final Principle

> **A system that is available but produces incorrect data is not a healthy production system. Protect important invariants, measure meaningful quality, detect divergence, and make correction part of the architecture rather than an emergency afterthought.**
