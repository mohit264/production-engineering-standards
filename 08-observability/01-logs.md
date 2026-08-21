# Logs

> A log is a durable record of an event or observation produced by a running system.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Observability Engineering

---

# Purpose

A running system continuously changes state.

Requests arrive.

Operations succeed or fail.

Dependencies respond.

State transitions occur.

Configuration changes.

Background work starts and finishes.

Many of these events disappear once they happen.

If the system leaves no useful record, an engineer investigating the system later has to reconstruct the past from incomplete evidence.

Logs exist to preserve that evidence.

---

# Engineering Principle

> **A log should preserve enough contextual information about an important event to make that event understandable after it has happened.**

---

# 1. The Fundamental Problem

Consider a production failure:

```text
10:42
Request failed
```

An engineer discovers the failure five minutes later.

The system has already moved on.

The original execution no longer exists.

The question becomes:

> **What happened at 10:42?**

Without historical evidence:

```text
Past Event
    │
    ▼
Gone
```

With useful logging:

```text
Past Event
    │
    ▼
Recorded Evidence
    │
    ▼
Later Investigation
```

This is the fundamental reason logs exist.

---

# 2. Logs Preserve History

Metrics describe aggregated behavior.

Traces describe execution paths.

Logs preserve individual events and contextual facts.

For example:

```text
2026-08-21 10:42:13
Payment authorization failed
customer=...
dependency=...
reason=...
```

The log creates a historical record that can be examined after the original event has disappeared.

---

# 3. What Should Be Logged?

A system should log events that are useful for:

- diagnosis,
- operational investigation,
- security investigation,
- auditing,
- business troubleshooting,
- understanding important state transitions.

Not every internal operation deserves a log entry.

---

# 4. Logging Is Evidence Collection

Logging should begin with the questions engineers may need to answer.

Examples:

```text
What happened?

When did it happen?

Which operation was involved?

Which component produced it?

What was the outcome?

What caused the failure?

Which request was affected?

Which dependency was involved?
```

The log should preserve evidence relevant to these questions.

---

# 5. Event vs Message

A weak log may say:

```text
Something went wrong.
```

A useful log represents an event:

```text
payment_authorization_failed
```

with contextual information such as:

```text
operation
customer context
dependency
failure reason
request identity
timestamp
```

The second representation is much easier to query and reason about.

---

# 6. Structured Logging

Logs should generally be structured when they are consumed by machines.

For example:

```text
{
  "event": "payment_authorization_failed",
  "service": "payments",
  "operation": "authorize",
  "dependency": "bank",
  "duration_ms": 842,
  "error_type": "timeout"
}
```

Structured data enables:

- filtering,
- aggregation,
- correlation,
- searching,
- automated analysis.

---

# 7. Human Readability

Structured logging does not mean humans should be ignored.

Engineers still need to understand individual events quickly.

Good logging therefore balances:

```text
Machine Queryability
        +
Human Comprehension
```

---

# 8. Context

A log without context has limited diagnostic value.

Compare:

```text
ERROR timeout
```

with:

```text
event=dependency_timeout
service=orders
operation=create_order
dependency=inventory
duration_ms=3200
```

The second provides substantially more information for investigation.

---

# 9. Event Identity

Important events should have a recognizable event identity.

For example:

```text
event = order_created
event = payment_failed
event = cache_connection_lost
event = configuration_loaded
```

Consistent event identities improve querying and analysis.

---

# 10. Timestamp

Every log event should have an accurate timestamp.

The timestamp allows investigators to:

- establish ordering,
- correlate events,
- compare components,
- reconstruct incidents.

Time synchronization across systems is therefore important.

---

# 11. Ordering

Distributed systems make event ordering difficult.

Two systems may produce:

```text
Event A
Event B
```

with timestamps that do not perfectly represent causal order.

Logs should therefore not be interpreted as proof of causality merely because one timestamp precedes another.

Where necessary, correlation and distributed tracing should provide additional context.

---

# 12. Correlation

A single operation may generate logs across multiple components.

For example:

```text
Request
  │
  ├── API
  │
  ├── Orders
  │
  ├── Payments
  │
  └── Database
```

Each component may produce its own logs.

A shared correlation identity allows these events to be associated with the same logical operation.

---

# 13. Request Identity

A request identifier can help connect events belonging to one request.

For example:

```text
request_id = abc123
```

The identifier may appear in:

- API logs,
- service logs,
- dependency logs,
- error records.

The exact propagation mechanism depends on the architecture.

---

# 14. Trace Identity

In distributed systems, trace identity can connect logs with distributed traces.

This allows an engineer to move between:

```text
Log
  ↕
Trace
```

and obtain both:

- detailed event context,
- distributed execution context.

---

# 15. Service Identity

Logs should identify the component that produced them.

Useful identity may include:

- service,
- application,
- process,
- instance,
- environment.

Without component identity, centralized logs can become difficult to interpret.

---

# 16. Environment Identity

Logs should distinguish environments where appropriate.

For example:

```text
environment=production
```

should not be confused with:

```text
environment=staging
```

Environment identity is particularly important when logs from multiple environments share infrastructure.

---

# 17. Severity

Logs often have severity classifications.

Common levels include:

```text
DEBUG
INFO
WARN
ERROR
FATAL
```

The exact taxonomy may differ.

The important requirement is that severity has consistent meaning.

---

# 18. Severity Is Not Importance Alone

A log level should communicate operational significance.

For example:

```text
INFO
```

should not mean:

> "The developer thought this was interesting."

Likewise:

```text
ERROR
```

should not mean:

> "An exception happened somewhere."

Severity should reflect how the event should be interpreted operationally.

---

# 19. Error Logging

Errors should contain enough context to understand:

- what operation failed,
- why it failed,
- which component was involved,
- whether retry occurred,
- whether the failure affected the request.

Avoid logging only:

```text
Exception occurred.
```

without useful context.

---

# 20. Stack Traces

Stack traces can be valuable during debugging.

However, they should be:

- captured intentionally,
- associated with the relevant event,
- protected from sensitive information,
- stored at appropriate severity.

Stack traces should not become the entire diagnostic strategy.

---

# 21. Duplicate Logging

A single failure may be logged at multiple layers:

```text
Database
   │
Service
   │
API
```

This can create multiple records for one underlying failure.

Excessive duplication increases noise and cost.

Teams should establish clear ownership for logging important failures.

---

# 22. Logging at Boundaries

Useful logging often occurs at meaningful boundaries.

Examples include:

- request received,
- request completed,
- external dependency called,
- message consumed,
- message published,
- transaction committed,
- transaction failed.

Boundary logging provides useful context without recording every internal instruction.

---

# 23. Business Events

Some business events deserve explicit logging or event recording.

Examples:

```text
order_created
payment_completed
subscription_cancelled
account_suspended
```

These events may be useful for operational investigation and business troubleshooting.

They should not automatically be treated as an audit record unless the required guarantees are actually provided.

---

# 24. Logs vs Audit Records

A log and an audit record are not necessarily equivalent.

Operational logs may be:

- sampled,
- aggregated,
- retained for limited periods,
- inaccessible to normal users.

Audit records may require stronger guarantees around:

- completeness,
- immutability,
- access,
- retention,
- attribution.

If a requirement is legally or operationally an audit requirement, ordinary application logs may not be sufficient.

---

# 25. Sensitive Data

Logs are frequently copied into centralized systems.

This increases their exposure.

Never log secrets unnecessarily.

Examples include:

- passwords,
- access tokens,
- private keys,
- session credentials,
- payment secrets.

---

# 26. Personal Data

Personal information should be logged only when there is a legitimate operational requirement.

Where possible:

- minimize the data,
- mask sensitive values,
- tokenize identifiers,
- restrict access,
- define retention.

Observability should not become an uncontrolled data collection mechanism.

---

# 27. Logging Secrets by Accident

A common failure occurs when applications serialize an entire request or object.

For example:

```text
log(request)
```

may accidentally capture:

```text
password
token
credit_card
```

Structured logging does not automatically make this safe.

Applications should explicitly control which fields are logged.

---

# 28. Cardinality

Some fields have extremely large numbers of possible values.

Examples:

```text
request_id
user_id
session_id
```

These can be valuable for investigation.

However, high-cardinality fields can increase storage, indexing, and query costs.

The decision should balance diagnostic value against operational cost.

---

# 29. Log Volume

Logging every event may appear attractive:

```text
More Logs = More Information
```

But the relationship is not linear.

Eventually:

```text
More Logs
   │
   ▼
More Noise
   │
   ▼
Harder Investigation
```

Logging should maximize useful evidence rather than raw volume.

---

# 30. Logging During Failure

Failure conditions can generate dramatically more logs.

For example:

```text
Dependency Failure
       │
       ▼
Every Request Fails
       │
       ▼
Every Request Logs Error
       │
       ▼
Telemetry Explosion
```

Systems should prevent failure amplification through uncontrolled logging.

Possible controls include:

- rate limiting,
- sampling,
- aggregation,
- deduplication.

---

# 31. Rate Limiting

High-frequency events may need rate limiting.

For example, instead of recording millions of identical events:

```text
database_timeout
```

the system may preserve:

- representative events,
- counts,
- first occurrence,
- last occurrence,
- aggregate information.

The appropriate approach depends on investigation requirements.

---

# 32. Sampling

Sampling can reduce log volume.

However, sampling error events indiscriminately may remove exactly the evidence needed during an incident.

Important events may require higher retention or different sampling policies.

---

# 33. Logging Performance

Logging consumes resources.

Costs may include:

- CPU,
- memory,
- disk,
- network,
- serialization,
- storage.

Logging should therefore be designed so that observability does not materially damage application performance.

---

# 34. Asynchronous Logging

Applications may buffer or asynchronously transmit logs.

This can reduce application-path overhead.

However, it introduces trade-offs:

- buffering,
- delayed delivery,
- possible loss,
- memory consumption,
- shutdown behavior.

The appropriate model depends on reliability requirements.

---

# 35. Log Loss

Logs can be lost because of:

- process crashes,
- network failures,
- collector failures,
- disk exhaustion,
- buffering limits.

The required durability depends on the purpose of the log.

Not every diagnostic log requires guaranteed delivery.

---

# 36. Logging Pipeline

A typical architecture may look like:

```text
Application
     │
     ▼
Log Generation
     │
     ▼
Collection
     │
     ▼
Transport
     │
     ▼
Storage
     │
     ▼
Query / Analysis
```

Each stage can fail independently.

---

# 37. Collection

A collection mechanism gathers logs from applications and infrastructure.

Collection should avoid requiring every application to understand the entire storage system.

This separation can simplify application design.

---

# 38. Centralization

Centralized logging allows engineers to search evidence across multiple components.

This is particularly valuable in distributed systems.

However, centralization also creates:

- infrastructure dependency,
- cost,
- access-control requirements,
- concentration of sensitive information.

---

# 39. Local Logs

Local logs can still be useful.

They may provide:

- immediate diagnostic evidence,
- startup information,
- crash information,
- fallback evidence when centralized collection is unavailable.

Local retention should be controlled to prevent disk exhaustion.

---

# 40. Log Storage

Storage design should consider:

- retention,
- indexing,
- query performance,
- durability,
- cost,
- access control.

Different classes of logs may require different storage characteristics.

---

# 41. Retention

Not every log needs permanent retention.

Retention should reflect:

- operational usefulness,
- incident investigation timelines,
- compliance requirements,
- storage cost,
- sensitivity.

Retention policies should be explicit.

---

# 42. Access Control

Logs may contain sensitive operational information.

Access should therefore be controlled according to:

- role,
- environment,
- data sensitivity,
- organizational policy.

Production logs should not automatically be visible to everyone.

---

# 43. Encryption

Logs may travel across networks and remain stored for significant periods.

Appropriate encryption should protect:

- log transport,
- log storage.

The exact controls depend on system requirements.

---

# 44. Queryability

A log system is useful only if engineers can efficiently find relevant evidence.

Queryability depends on:

- structured fields,
- consistent naming,
- indexing,
- timestamps,
- correlation identities.

Poorly structured logs increase investigation time.

---

# 45. Search by Time

Time should be a primary query dimension.

During an incident, investigators often know:

```text
Something changed around 14:32.
```

They should be able to efficiently narrow the evidence to that period.

---

# 46. Search by Correlation

Engineers should be able to retrieve events associated with a logical operation where appropriate.

For example:

```text
trace_id = xyz
```

or:

```text
request_id = abc
```

This can dramatically reduce investigation complexity.

---

# 47. Log Schemas

Structured logs should have stable semantics.

For important events, teams may define schemas containing fields such as:

```text
timestamp
event
service
environment
severity
trace_id
request_id
operation
outcome
error
```

Not every event requires every field.

The schema should reflect the event's purpose.

---

# 48. Schema Evolution

Log schemas evolve as systems evolve.

Changes should preserve enough compatibility for existing:

- queries,
- dashboards,
- alerts,
- operational workflows.

Breaking telemetry schemas can silently damage observability.

---

# 49. Log Naming

Event names should be:

- descriptive,
- consistent,
- stable,
- searchable.

Prefer:

```text
payment_authorization_failed
```

over:

```text
something_bad_happened
```

---

# 50. Logging Governance

Each production system should define:

- logging conventions,
- required contextual fields,
- severity semantics,
- sensitive-data rules,
- retention,
- access,
- ownership,
- cost expectations.

Governance should be proportional to system importance.

---

# 51. Minimum Engineering Requirements

Every production project should:

- [ ] Define what operational events require logging.
- [ ] Use consistent severity semantics.
- [ ] Include sufficient contextual information.
- [ ] Identify the producing service/component.
- [ ] Include reliable timestamps.
- [ ] Support correlation where distributed investigation requires it.
- [ ] Avoid logging secrets.
- [ ] Minimize unnecessary personal or sensitive data.
- [ ] Define retention.
- [ ] Control access to production logs.
- [ ] Prevent uncontrolled log amplification.
- [ ] Ensure logging does not materially compromise application reliability.
- [ ] Provide a practical mechanism for querying important logs.

Higher-risk systems may additionally require:

- [ ] Formal log schemas.
- [ ] Centralized log collection.
- [ ] Strong correlation with traces.
- [ ] Advanced sampling.
- [ ] Tamper-resistant audit logging.
- [ ] Formal log classification.
- [ ] Cross-service logging standards.
- [ ] Automated sensitive-data detection.
- [ ] Log pipeline resilience.
- [ ] Cost monitoring.

---

# Relationship With Other Standards

This standard works with:

- `08-observability/README.md`
- `08-observability/03-metrics.md`
- `08-observability/04-traces.md`
- `08-observability/05-alerting.md`
- `08-observability/06-observability-governance.md`

It also connects directly with:

- `05-reliability/`
- `06-security/`
- `07-delivery/`
- `11-operational-readiness/`

---

# What This Standard Is Not

This standard does not prescribe:

- Elasticsearch,
- Logstash,
- Kibana,
- Loki,
- Fluent Bit,
- Fluentd,
- CloudWatch Logs,
- Azure Monitor Logs,
- Splunk,
- Datadog.

Those are implementation choices.

The engineering contract is:

> **Important operational events must leave behind sufficient, contextual, searchable evidence to support investigation without creating unnecessary security, reliability, or cost problems.**

---

# Final Principle

> **A running system forgets almost everything that happened unless we deliberately preserve evidence. Logs are that historical memory—but useful memory is not a transcript of everything. It is carefully chosen evidence that allows us to reconstruct important events when we need to understand the past.**
