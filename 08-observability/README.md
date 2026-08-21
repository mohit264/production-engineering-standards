# Observability

> Observability is the engineering capability to infer the internal state and behavior of a system from the evidence it produces.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Observability Engineering

---

# Purpose

A running system has an internal state that cannot be directly observed from outside.

We can see:

- requests,
- responses,
- failures,
- latency,
- resource consumption,
- state changes.

But these are only externally visible consequences.

The internal question remains:

> **What is actually happening inside the system?**

Observability provides the evidence required to answer that question.

---

# Engineering Principle

> **A system is observable when its externally produced evidence allows engineers to explain its important internal states and behaviors.**

---

# 1. The Fundamental Problem

Consider a production request:

```text
User
 │
 ▼
Application
 │
 ▼
Database
```

The user reports:

```text
"The request is slow."
```

We can observe the symptom.

But we need to determine:

```text
Where is the time being spent?

Application?
Database?
Network?
Dependency?
Queue?
Resource contention?
```

The system's internal state is not directly visible.

That is the observability problem.

---

# 2. Visibility Is Not Observability

A dashboard can show:

```text
CPU = 80%
```

A log can show:

```text
Request failed
```

A trace can show:

```text
Request took 4.2 seconds
```

These provide visibility.

But visibility alone does not guarantee that we can explain the system's behavior.

Observability asks a stronger question:

> **Do we have enough useful evidence to reason about why the system behaved this way?**

---

# 3. Observation vs Explanation

Consider:

```text
Error Rate = 20%
```

This tells us:

```text
Something is wrong.
```

It does not necessarily tell us:

```text
Why?
```

A mature observability system should help move from:

```text
Symptom
  │
  ▼
Evidence
  │
  ▼
Hypothesis
  │
  ▼
Explanation
```

---

# 4. Observability as Evidence

A running system produces evidence.

Examples include:

- events,
- measurements,
- request records,
- state transitions,
- dependency interactions.

These signals allow engineers to infer internal behavior.

Conceptually:

```text
Running System
      │
      ▼
Evidence
      │
      ▼
Inference
      │
      ▼
Understanding
```

---

# 5. The Three Fundamental Signal Families

Three commonly used signal families provide complementary evidence:

```text
              Observability
                   │
        ┌──────────┼──────────┐
        │          │          │
        ▼          ▼          ▼
       Logs      Metrics     Traces
```

They answer different questions.

### Logs

> What happened?

### Metrics

> How much, how often, and how is it changing?

### Traces

> Where did this particular operation spend its time?

None of them completely replaces the others.

---

# 6. Logs

Logs record discrete events or observations.

Examples include:

```text
Payment request rejected
Database connection failed
User authentication succeeded
```

Logs are particularly useful when the exact event or contextual detail matters.

---

# 7. Metrics

Metrics represent measurements aggregated over time.

Examples include:

```text
Request rate
Error rate
Latency
CPU utilization
Queue depth
```

Metrics are particularly useful for understanding:

- trends,
- capacity,
- rates,
- thresholds,
- system health.

---

# 8. Traces

A trace represents the journey of an operation through a distributed system.

For example:

```text
Request
   │
   ├── Service A
   │
   ├── Service B
   │      └── Database
   │
   └── Service C
```

Traces are particularly useful for understanding:

- distributed request flow,
- latency contribution,
- dependency behavior,
- cross-service failures.

---

# 9. Signals Are Complementary

Consider:

```text
Metric:
Latency increased.

Trace:
Database call consumed most of the latency.

Log:
Database connection pool exhausted.
```

Together they provide substantially more explanatory power than any single signal.

---

# 10. Observability Is About Questions

Observability should begin with questions rather than tools.

Examples:

```text
Why did requests become slow?

Why are errors increasing?

Which dependency is failing?

Which customers are affected?

Did the latest deployment cause the regression?

Is the system running out of capacity?

What changed before the failure?
```

Instrumentation should exist to help answer meaningful operational questions.

---

# 11. Unknown Unknowns

Monitoring often focuses on known failure conditions.

For example:

```text
CPU > 90%
```

is a known condition.

But production failures may involve conditions nobody predicted.

Observability attempts to preserve enough contextual evidence to investigate behavior that was not anticipated in advance.

This is particularly important in complex distributed systems.

---

# 12. Monitoring vs Observability

Monitoring generally asks:

> **Is the system behaving according to known expectations?**

Observability asks:

> **If the system behaves unexpectedly, can we investigate why?**

They overlap, but they are not identical.

Monitoring is therefore one consumer of observability data.

---

# 13. Instrumentation

Instrumentation is the mechanism through which software produces observability evidence.

Examples include:

- application logs,
- counters,
- timers,
- spans,
- events.

Instrumentation should be intentional.

Poor instrumentation can produce enormous amounts of data while providing little useful information.

---

# 14. Instrumentation Quality

Useful instrumentation should provide:

- meaningful context,
- consistent naming,
- appropriate granularity,
- reliable timestamps,
- correlation information,
- useful dimensions.

The objective is not maximum telemetry.

The objective is useful evidence.

---

# 15. Context

A signal without context can be difficult to interpret.

Consider:

```text
ERROR: timeout
```

Compare that with evidence containing:

```text
operation
service
dependency
request identifier
duration
failure reason
```

Context increases diagnostic value.

---

# 16. Correlation

Distributed systems produce many independent signals.

A single user request may generate:

```text
Log A
Metric observation
Trace span A
Trace span B
Log B
Database event
```

Correlation allows these signals to be associated with the same operation.

Without correlation, investigation becomes a search through disconnected fragments.

---

# 17. Identity

Important telemetry should have stable identities where appropriate.

Examples include:

- request identifier,
- trace identifier,
- service identity,
- instance identity,
- deployment identity.

Identity allows evidence to be connected across system boundaries.

---

# 18. Time

Time is fundamental to operational investigation.

Signals should provide reliable timestamps and appropriate time semantics.

Without reliable time information, reconstructing event order becomes difficult.

---

# 19. Causality

Observability should help establish relationships such as:

```text
Deployment
    │
    ▼
Configuration Change
    │
    ▼
Dependency Latency
    │
    ▼
Application Errors
```

The telemetry system does not automatically prove causality.

It provides evidence from which engineers can reason about likely causes.

---

# 20. Cardinality

Telemetry dimensions can have different numbers of possible values.

For example:

```text
region
```

may have relatively few values.

While:

```text
request_id
```

may have millions.

High-cardinality data can be extremely useful for investigation but expensive to store and query.

Telemetry design should therefore balance:

```text
Diagnostic Value
       vs
Operational Cost
```

---

# 21. Signal Volume

More telemetry does not automatically mean better observability.

Consider:

```text
1 billion events
```

that contain little useful context.

The system may technically be producing enormous amounts of evidence while still being difficult to debug.

Telemetry should optimize for signal-to-noise ratio.

---

# 22. Sampling

Distributed systems may generate more telemetry than is practical to retain.

Sampling can reduce cost and volume.

Sampling strategies should preserve sufficient evidence for important investigations.

Critical events may require different treatment from routine traffic.

---

# 23. Retention

Observability data should have deliberate retention policies.

Retention should consider:

- investigation requirements,
- compliance,
- incident duration,
- storage cost,
- historical analysis.

Not every signal needs to be retained for the same duration.

---

# 24. Availability of Observability

Observability infrastructure is itself an operational dependency.

If:

```text
Production Failure
       │
       ▼
Observability Failure
```

engineers may lose the ability to understand the original failure.

Critical observability systems therefore require appropriate resilience.

---

# 25. Telemetry Failure

Applications should define what happens when telemetry infrastructure is unavailable.

Observability should generally not become a reason for the application itself to fail.

For example:

```text
Application
    │
    ├── Primary Work
    │
    └── Telemetry
             │
             X
```

A telemetry failure should not automatically become a production application failure.

---

# 26. Backpressure

Telemetry generation can become significant during incidents.

For example:

```text
Failure
  │
  ▼
More Errors
  │
  ▼
More Logs
  │
  ▼
More Telemetry
  │
  ▼
Telemetry Overload
```

Observability systems should therefore account for:

- buffering,
- sampling,
- rate limiting,
- backpressure,
- prioritization.

---

# 27. Observability During Incidents

During an incident, telemetry becomes especially valuable.

Engineers need to move quickly from:

```text
Something is wrong.
```

to:

```text
What changed?
```

then:

```text
What is affected?
```

and finally:

```text
Why?
```

Observability should support this progression.

---

# 28. Observability and Change

Production behavior should be correlated with important changes.

Examples include:

- deployments,
- configuration changes,
- infrastructure changes,
- feature activation,
- dependency changes.

A useful operational system should make these relationships discoverable.

---

# 29. Observability and Releases

Release events should be visible alongside operational telemetry.

For example:

```text
14:00  Release N
14:03  Error rate increases
14:05  Latency increases
14:07  Rollback begins
```

This allows engineers to correlate behavior with change.

---

# 30. Observability and Reliability

Reliability depends on knowing whether the system is meeting its intended behavior.

Observability provides evidence for:

- availability,
- latency,
- errors,
- saturation,
- service-level objectives.

Therefore:

> **Reliability without observability is difficult to operate.**

---

# 31. Observability and Security

Security events are also system behavior.

Examples include:

- authentication failures,
- authorization failures,
- unusual access patterns,
- privilege changes,
- suspicious requests.

Security telemetry should integrate with the broader observability strategy while respecting security and privacy requirements.

---

# 32. Observability and Data

Observability itself generates data.

That data may contain:

- user identifiers,
- request information,
- business information,
- sensitive operational details.

Telemetry must therefore follow appropriate:

- access control,
- retention,
- privacy,
- data-classification requirements.

---

# 33. Sensitive Data

Telemetry should not unnecessarily capture sensitive information.

Examples include:

- passwords,
- authentication tokens,
- private keys,
- payment information,
- personal information where not required.

"Log everything" is not an acceptable observability strategy.

---

# 34. Structured Telemetry

Machine-readable telemetry generally provides better querying and correlation than arbitrary text.

For example:

```text
event = request_failed
service = payments
operation = authorize
duration_ms = 842
dependency = database
```

Structured data allows operational systems to query evidence consistently.

---

# 35. Naming Conventions

Telemetry naming should be consistent.

Inconsistent names create fragmentation.

For example:

```text
request.duration
requestDuration
req_time
duration
```

may represent the same concept.

Standardized naming improves:

- dashboards,
- queries,
- alerts,
- cross-service analysis.

---

# 36. Semantic Consistency

Different services should use compatible meanings for shared concepts.

For example:

> What exactly does "request latency" mean?

Does it include:

- queue time?
- network time?
- application processing?
- retries?

Definitions should be explicit enough that measurements remain comparable.

---

# 37. Observability Boundaries

Every important production component should have an appropriate observability boundary.

This may include:

- service health,
- request behavior,
- dependencies,
- resource consumption,
- important business operations.

The level of instrumentation should reflect the component's importance and risk.

---

# 38. Observability of Dependencies

A system can appear unhealthy because one of its dependencies is unhealthy.

Observability should therefore help distinguish:

```text
Local Failure
```

from:

```text
Dependency Failure
```

and:

```text
Shared Infrastructure Failure
```

---

# 39. Distributed Systems

Distributed systems amplify observability challenges because a single logical operation may cross many components.

Without correlation:

```text
Service A
Service B
Service C
Database
Queue
```

produce disconnected evidence.

With correlation, the operation can be reconstructed as one logical execution.

---

# 40. Observability and Performance

Performance investigations require more than average latency.

Useful evidence may include:

- percentiles,
- distribution,
- dependency latency,
- queue time,
- resource utilization,
- concurrency.

Averages can hide severe tail behavior.

---

# 41. Observability and Capacity

Observability should provide evidence about whether the system is approaching capacity.

Examples include:

- CPU,
- memory,
- storage,
- connections,
- queue depth,
- concurrency.

Capacity signals support both operations and planning.

---

# 42. Observability and Cost

Observability has an economic cost.

Costs may arise from:

- telemetry generation,
- transport,
- storage,
- indexing,
- querying,
- retention.

The organization should optimize telemetry without destroying diagnostic capability.

---

# 43. Observability Governance

Each production system should define:

- required signals,
- naming conventions,
- ownership,
- retention,
- access,
- sensitive-data rules,
- cost expectations.

Governance should be proportional to system importance.

---

# 44. Observability Anti-Patterns

Avoid:

- logging everything,
- logging nothing useful,
- dashboards without operational questions,
- alerts without ownership,
- high-cardinality dimensions without understanding cost,
- telemetry containing secrets,
- inconsistent naming,
- relying only on averages,
- collecting telemetry that nobody uses,
- treating observability as an afterthought,
- allowing telemetry failures to take down the application.

---

# 45. Minimum Engineering Requirements

Every production project should:

- [ ] Define meaningful observability requirements.
- [ ] Produce appropriate logs, metrics, and/or traces.
- [ ] Provide sufficient context for investigation.
- [ ] Correlate distributed operations where required.
- [ ] Use consistent telemetry semantics.
- [ ] Protect telemetry containing sensitive information.
- [ ] Define retention requirements.
- [ ] Define telemetry ownership.
- [ ] Monitor important dependencies.
- [ ] Correlate important production changes with operational behavior.
- [ ] Ensure telemetry failures do not unnecessarily break primary application behavior.
- [ ] Define appropriate cost controls.

Higher-risk systems may additionally require:

- [ ] Distributed tracing.
- [ ] Structured telemetry standards.
- [ ] High-fidelity event capture.
- [ ] Advanced sampling.
- [ ] Formal telemetry schemas.
- [ ] Business-level observability.
- [ ] SLO-driven observability.
- [ ] Dedicated observability resilience.
- [ ] Formal telemetry governance.
- [ ] Cross-system correlation.

---

# Relationship With Other Standards

This standard works with:

- `08-observability/README.md`
- `08-observability/02-logs.md`
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

- Prometheus,
- Grafana,
- OpenTelemetry,
- ELK,
- Datadog,
- New Relic,
- CloudWatch,
- Azure Monitor,
- any particular observability platform.

Those are implementation choices.

The engineering contract is:

> **A production system must produce enough trustworthy evidence for engineers to understand important behavior, investigate unexpected conditions, and make informed operational decisions.**

---

# Final Principle

> **We cannot directly see the internal state of a running system. Observability is the mechanism by which the system leaves enough evidence behind for us to reconstruct what happened, understand why it happened, and decide what to do next.**
