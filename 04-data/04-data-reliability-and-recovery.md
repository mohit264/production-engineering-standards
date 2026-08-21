# Data Reliability and Recovery

> Data recovery is not the ability to restore a database. It is the ability to restore trustworthy business state within an acceptable time and loss boundary.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Data Engineering

**Applies To:** Every production project where loss, corruption, or unavailability of data can affect business operations

---

# Purpose

Data is often the most difficult part of a system to recreate.

Application code can frequently be redeployed.

Infrastructure can often be recreated.

Data may be:

- expensive to reconstruct,
- impossible to reconstruct,
- legally significant,
- operationally critical,
- historically valuable.

Therefore, data reliability must be designed explicitly.

This standard establishes baseline expectations for:

- data durability,
- backup,
- restoration,
- recovery objectives,
- replication,
- corruption recovery,
- disaster recovery,
- recovery validation,
- operational ownership.

---

# Engineering Principle

> **If the business cannot tolerate losing the data, the architecture must explicitly define how the data survives failure and how trustworthy state is restored.**

---

# 1. Availability Is Not Durability

These concepts must not be confused.

### Availability

Can the system currently serve requests?

### Durability

Will the data survive a failure?

A highly available database can still lose data if:

- writes were not durably persisted,
- replicas were not sufficiently synchronized,
- backups were unavailable,
- corruption was replicated.

Therefore:

> **Replication and availability do not automatically provide recoverability.**

---

# 2. Identify Critical Data

Not all data requires the same recovery guarantees.

Classify important datasets according to business impact.

For example:

| Data Category | Potential Impact |
|---|---|
| Transactional | Financial / operational loss |
| Customer | Business and privacy impact |
| Configuration | Service recovery impact |
| Analytical | Reporting impact |
| Cache | Usually reconstructable |
| Temporary | Usually disposable |

The classification should drive recovery requirements.

---

# 3. Recovery Objectives

Every important production workload should establish appropriate recovery objectives.

## Recovery Point Objective — RPO

RPO answers:

> **How much data can the business afford to lose?**

Example:

```text
Last acceptable recovered state
        │
        ▼
15 minutes before failure
```

This implies an RPO of approximately 15 minutes.

---

## Recovery Time Objective — RTO

RTO answers:

> **How long can the business tolerate the service or data being unavailable?**

Example:

```text
Failure
  │
  ▼
Recovery begins
  │
  ▼
Service restored
```

If restoration must complete within two hours:

```text
RTO = 2 hours
```

---

# 4. RPO and RTO Are Business Requirements

Do not select RPO and RTO merely because a technology supports them.

The project should determine:

- business impact,
- acceptable downtime,
- acceptable data loss,
- financial consequences,
- regulatory requirements.

Technology should then be selected to satisfy those requirements.

---

# 5. Backup Strategy

Important data should have an appropriate backup strategy.

The project should define:

- what is backed up,
- backup frequency,
- retention,
- storage location,
- encryption,
- access control,
- restoration procedure.

A backup policy without a restoration strategy is incomplete.

---

# 6. Backup Independence

Backups should provide meaningful protection against the failure scenarios they are intended to address.

Consider whether backups could be affected by the same event as the primary system.

For example:

```text
Primary Database
      │
      ▼
Backup
      │
      ▼
Same Failure Domain
```

This may provide limited protection.

Depending on risk, backups may need separation across:

- availability zones,
- regions,
- accounts,
- environments,
- administrative boundaries.

The appropriate level depends on the system tier.

---

# 7. Backup Retention

Backups require their own lifecycle.

Define:

- backup frequency,
- retention duration,
- archival policy,
- deletion policy.

Do not retain backups indefinitely without a business reason.

Also consider that backups may contain historical versions of sensitive data.

---

# 8. Backup Encryption

Where data sensitivity requires it, backups should be protected with appropriate encryption.

The project should understand:

- encryption at rest,
- key ownership,
- key rotation,
- key availability during recovery,
- access controls.

A backup that cannot be decrypted during an incident is not a usable backup.

---

# 9. Backup Access

Backup systems should not be treated as ordinary storage.

Access should be appropriately restricted because backups may contain:

- entire databases,
- credentials,
- personal information,
- financial information,
- historical records.

Compromise of backup storage can therefore have significant impact.

---

# 10. Restore Capability

The project must understand how data will actually be restored.

This includes:

- where backups are located,
- how they are retrieved,
- how they are restored,
- required credentials,
- required infrastructure,
- validation steps,
- expected restoration time.

"Backups exist" is not sufficient evidence of recoverability.

---

# 11. Restore Testing

Where recovery is important, restoration should be tested.

A successful backup operation proves:

> Data was copied.

A successful restore proves:

> The copied data can be turned back into usable state.

Therefore:

> **Backup success and recovery success are different signals.**

---

# 12. Recovery Validation

After restoration, the project should determine whether the recovered state is trustworthy.

Validation may include:

- database integrity checks,
- record counts,
- business invariants,
- application startup,
- representative transactions,
- reconciliation,
- consistency checks.

A database opening successfully does not prove that the business state is correct.

---

# 13. Point-in-Time Recovery

Where appropriate, systems may support recovery to a specific point in time.

This can help recover from:

- accidental deletion,
- bad deployments,
- application bugs,
- operator mistakes.

The project should understand:

- recovery granularity,
- recovery window,
- operational procedure,
- expected recovery time.

---

# 14. Accidental Deletion

Data recovery requirements should consider human error.

For example:

```text
Operator
   │
   ▼
Incorrect deletion
   │
   ▼
Replication
   │
   ▼
All replicas now contain deletion
```

Replication alone does not protect against logical mistakes.

Independent recovery mechanisms may therefore be required.

---

# 15. Corruption

Corruption can be more difficult than deletion.

For example:

```text
Incorrect Application Logic
          │
          ▼
Incorrect Data
          │
          ▼
Replication
          │
          ▼
Multiple Copies Corrupted
```

Recovery planning should therefore consider:

- corruption detection,
- historical recovery points,
- restoration from earlier snapshots,
- reconciliation.

---

# 16. Replication

Replication can improve:

- availability,
- read scalability,
- geographic resilience.

But replication introduces its own concerns:

- replication lag,
- stale reads,
- split-brain scenarios,
- conflict resolution,
- correlated failures.

Replication should therefore be designed according to its purpose.

---

# 17. Replication Is Not Backup

A replica generally reflects the current state of the source.

If the source contains:

```text
Correct Data
```

the replica may contain:

```text
Correct Data
```

If the source contains:

```text
Corrupted Data
```

the replica may also contain:

```text
Corrupted Data
```

Backups provide a different recovery capability.

---

# 18. Synchronous vs Asynchronous Replication

Replication may be:

```text
Synchronous
```

or:

```text
Asynchronous
```

The trade-offs may involve:

- latency,
- durability,
- availability,
- consistency,
- geographic distance.

The architecture should choose based on the required guarantees rather than treating one mechanism as universally superior.

---

# 19. Recovery From Partial Failure

Data systems may experience partial failures.

Examples include:

- one replica unavailable,
- network partition,
- storage degradation,
- region failure,
- failed migration,
- corrupted subset of records.

Recovery design should consider whether the system can continue operating and how state is repaired afterward.

---

# 20. Disaster Recovery

Disaster recovery addresses failures beyond ordinary component failure.

Examples include:

- regional outage,
- major infrastructure failure,
- destructive operator action,
- security incident,
- widespread data corruption.

The required disaster recovery strategy should follow the business impact of the system.

---

# 21. Recovery Architecture

Possible strategies include:

```text
Backup and Restore
```

```text
Warm Standby
```

```text
Hot Standby
```

```text
Multi-Region Operation
```

These have different:

- cost,
- complexity,
- recovery characteristics,
- operational requirements.

The project should choose intentionally.

---

# 22. Recovery Trade-offs

Higher recovery guarantees generally introduce additional:

- infrastructure,
- operational complexity,
- testing requirements,
- cost.

Therefore:

> **The most resilient architecture is not automatically the correct architecture.**

The objective is to meet the required recovery contract.

---

# 23. Recovery Dependencies

Data recovery may depend on more than the datastore.

For example:

```text
Database
   │
   ├──► Encryption Keys
   ├──► Network
   ├──► Identity
   ├──► Application Configuration
   ├──► Schemas
   └──► Application Version
```

A recovery plan must account for important dependencies.

---

# 24. Schema and Version Compatibility

A backup may contain data that requires a particular schema or application version.

Recovery should therefore consider:

- schema compatibility,
- migration history,
- application compatibility,
- rollback procedures.

Do not assume:

> "Restore database and deploy latest application."

That may not produce a compatible system.

---

# 25. Recovery Ordering

Distributed systems may contain multiple stores.

For example:

```text
Primary Database
       │
       ├──► Search Index
       ├──► Cache
       ├──► Event Stream
       └──► Analytics Store
```

Recovery should establish:

- which store is authoritative,
- what must be restored,
- what can be rebuilt,
- what must be replayed.

Rebuilding derived state is often safer than attempting to independently restore every derived copy.

---

# 26. Rebuildability

For derived data, the project should determine whether it can be recreated.

Examples:

```text
Primary Data
     │
     ▼
Search Index
```

If the index can be rebuilt from authoritative data, it may not require the same backup strategy as the primary store.

This can significantly reduce operational complexity.

---

# 27. Recovery From Event-Driven Systems

Event-driven systems may rely on:

- event retention,
- replay,
- snapshots,
- offsets,
- checkpoints.

Recovery should determine whether the system can reconstruct the required state.

The architecture should distinguish between:

```text
Authoritative Events
```

and:

```text
Derived State
```

where applicable.

---

# 28. Recovery From Data Pipeline Failure

Data pipelines may fail after partially processing data.

The system should consider:

- checkpoints,
- idempotency,
- replay,
- backfill,
- duplicate handling,
- output validation.

A failed pipeline should not silently create an incomplete dataset that appears successful.

---

# 29. Recovery and Deletion

Recovery mechanisms may preserve data longer than the primary system.

For example:

```text
Primary Data
      │
      ▼
Deleted
```

while:

```text
Backup
      │
      ▼
Still Contains Historical Copy
```

Retention and deletion requirements must therefore account for recovery systems.

---

# 30. Recovery and Security Incidents

A security incident may involve compromised or corrupted data.

Blindly restoring a backup may reintroduce:

- compromised credentials,
- malicious changes,
- vulnerable configurations,
- corrupted records.

Recovery procedures should therefore consider security validation where relevant.

---

# 31. Recovery Runbooks

Important recovery operations should have documented procedures.

A runbook should identify:

- trigger,
- owner,
- prerequisites,
- sequence,
- validation,
- escalation,
- rollback or fallback.

The procedure should be understandable by the team responsible for operating the system.

---

# 32. Recovery Automation

Where recovery procedures are repeated and deterministic, automation should be preferred.

Examples include:

- infrastructure provisioning,
- backup restoration,
- database initialization,
- validation,
- service deployment.

Automation reduces dependence on individual memory during high-stress incidents.

---

# 33. Recovery Observability

Recovery mechanisms should themselves be observable.

Monitor where appropriate:

- backup failures,
- backup age,
- replication lag,
- recovery-point availability,
- storage capacity,
- restore-test failures.

A backup system that has been silently failing for six months is not a reliable recovery mechanism.

---

# 34. Backup Monitoring

At minimum, important backup systems should provide enough visibility to determine:

- Did the backup run?
- Did it complete?
- Is the backup usable?
- Is it within the required recovery window?
- Is retention functioning?

Success should not be inferred merely from the existence of a scheduled job.

---

# 35. Recovery Objectives Must Be Measurable

A project should be able to demonstrate whether its recovery design satisfies its intended RPO and RTO.

For example:

```text
Required RTO: 60 minutes
Observed restoration time: 42 minutes
```

This provides evidence.

Without measurement, RTO is merely an aspiration.

---

# 36. Recovery Exercises

For systems where recovery is important, periodic recovery exercises should be considered.

Exercises can expose:

- missing credentials,
- broken automation,
- inaccessible backups,
- undocumented dependencies,
- unrealistic recovery estimates.

A recovery design that has never been exercised contains significant uncertainty.

---

# 37. Recovery Ownership

Every important recovery mechanism should have an owner.

Ownership includes:

- backup health,
- restore procedures,
- recovery testing,
- documentation,
- incident support.

Recovery should not depend on:

> "The person who originally configured it."

---

# 38. Recovery Evidence

Projects should retain appropriate evidence of:

- backup configuration,
- restore testing,
- recovery objectives,
- recovery exercise results,
- unresolved gaps.

The required level of evidence depends on the system tier.

---

# 39. Minimum Engineering Requirements

Every production project should:

- [ ] Identify critical datasets.
- [ ] Define appropriate RPO and RTO for important data.
- [ ] Establish an appropriate backup strategy.
- [ ] Protect backups appropriately.
- [ ] Monitor backup execution and health.
- [ ] Define restoration procedures.
- [ ] Validate restored data where recovery is important.
- [ ] Distinguish replication from backup.
- [ ] Identify important recovery dependencies.
- [ ] Define ownership of recovery operations.

Higher-tier systems may additionally require:

- [ ] Point-in-time recovery.
- [ ] Cross-failure-domain backups.
- [ ] Disaster recovery.
- [ ] Regular recovery exercises.
- [ ] Automated recovery.
- [ ] Measured RPO/RTO evidence.
- [ ] Corruption recovery strategy.

---

# Relationship With Other Standards

This document works with:

- `data-architecture.md`
- `data-lifecycle.md`
- `data-quality-and-integrity.md`

It also interacts with:

- `03-architecture/failure-domains.md`
- `05-reliability/`
- `06-security/`
- `09-platform-and-infrastructure/`
- `11-operational-readiness/`

The distinction is:

**Data Architecture**

> Where data lives and who owns it.

**Data Lifecycle**

> How data exists and changes over time.

**Data Quality and Integrity**

> Whether the data remains correct and trustworthy.

**Data Reliability and Recovery**

> How the data survives failure and how trustworthy state is restored.

---

# Final Principle

> **A backup is not a recovery strategy until the organization knows what must be recovered, how quickly, how much data may be lost, how restoration works, and whether the restored state can be trusted.**
