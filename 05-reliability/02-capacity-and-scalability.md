# Capacity and Scalability

> A system is reliable only while it can continue operating within the demand and resource boundaries it was designed to handle.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Reliability Engineering

**Applies To:** Production systems, with depth determined by system tier and workload characteristics

---

# Purpose

Many production failures are not caused by broken software.

They are caused by insufficient capacity.

Examples include:

- CPU exhaustion,
- memory exhaustion,
- connection pool exhaustion,
- thread exhaustion,
- queue growth,
- storage exhaustion,
- database capacity limits,
- API quotas,
- network saturation,
- unexpected traffic spikes.

Capacity is therefore not merely a performance concern.

> **Capacity is a reliability concern.**

This standard establishes baseline expectations for understanding, measuring, planning, and controlling system capacity.

---

# Engineering Principle

> **A production system should have known resource boundaries and deliberate behavior when demand approaches or exceeds those boundaries.**

A system should not discover its capacity limit for the first time during a production incident.

---

# 1. Capacity vs Scalability

These concepts are related but different.

### Capacity

The amount of workload a system can handle under defined conditions.

Examples:

```text
500 requests/second
10,000 messages/minute
2 TB storage
1,000 concurrent connections
```

### Scalability

The ability of the system to increase capacity as demand increases.

A system can have high capacity without being highly scalable.

Likewise, a system may be scalable but poorly configured for its current workload.

---

# 2. Define the Workload

Capacity cannot be measured without understanding the workload.

Relevant characteristics may include:

- requests per second,
- concurrent users,
- message rate,
- batch size,
- transaction volume,
- payload size,
- read/write ratio,
- query complexity,
- storage growth.

The project should identify the workload dimensions that actually drive resource consumption.

---

# 3. Peak Demand

Average traffic is rarely sufficient for capacity planning.

Consider:

```text
Average Traffic
       │
       ▼
Peak Traffic
       │
       ▼
Capacity Requirement
```

Peak demand may arise from:

- business events,
- campaigns,
- seasonal traffic,
- scheduled jobs,
- customer behavior,
- downstream recovery,
- unexpected events.

The appropriate peak model depends on the business.

---

# 4. Capacity Headroom

A system should not normally operate continuously at its absolute resource limit.

For example:

```text
100% ───────────── Hard Resource Limit
       │
       │
 80% ───────────── Planned Operating Boundary
       │
       │
  0% ─────────────
```

Headroom provides space for:

- traffic variation,
- temporary spikes,
- recovery activity,
- deployment effects,
- background workloads.

The appropriate headroom depends on workload characteristics and system tier.

---

# 5. Resource Exhaustion

Every important resource should have a known exhaustion behavior.

Potential resources include:

- CPU,
- memory,
- disk,
- network,
- database connections,
- worker threads,
- file descriptors,
- queue capacity,
- API quotas.

For each important resource, determine:

> What happens when this resource is exhausted?

---

# 6. CPU Saturation

High CPU utilization may indicate:

- insufficient compute,
- inefficient code,
- unexpected workload,
- runaway processes,
- expensive queries.

The project should distinguish between:

```text
High CPU
```

and:

```text
CPU-induced user impact
```

A CPU percentage alone does not define system health.

---

# 7. Memory Saturation

Memory exhaustion can result in:

- process termination,
- garbage-collection pressure,
- swapping,
- degraded latency,
- operating-system instability.

Where memory is an important constraint, teams should understand:

- normal consumption,
- growth behavior,
- maximum safe usage,
- failure behavior.

---

# 8. Connection Exhaustion

Systems frequently depend on finite connection pools.

For example:

```text
Application
     │
     ▼
Connection Pool
     │
     ▼
Database
```

If all connections are occupied:

```text
New Request
     │
     X
Connection Unavailable
```

This may appear to users as a database outage even when the database itself is healthy.

---

# 9. Queue Capacity

Queues can absorb temporary demand spikes.

They can also hide a capacity problem.

For example:

```text
Producer Rate  >  Consumer Rate
        │
        ▼
Queue Growth
        │
        ▼
Increasing Delay
        │
        ▼
Storage / Retention Limit
```

The project should monitor queue behavior and define acceptable backlog.

---

# 10. Queue Age

Queue depth alone may not indicate user impact.

Consider:

```text
Queue = 10,000 messages
```

This could be healthy if consumers process thousands per second.

Conversely:

```text
Queue = 100 messages
```

could be severe if each message has been waiting for hours.

Where asynchronous processing matters, consider measuring:

- queue depth,
- oldest message age,
- processing rate,
- arrival rate.

---

# 11. Storage Capacity

Storage exhaustion can cause unexpected system failures.

Potential consequences include:

- database failure,
- inability to write logs,
- failed deployments,
- failed backups,
- corrupted temporary state.

Projects should understand:

- current usage,
- growth rate,
- retention,
- alert thresholds,
- expansion mechanism.

---

# 12. Database Capacity

Databases can become constrained by:

- CPU,
- memory,
- storage,
- connections,
- I/O,
- locks,
- query complexity,
- transaction volume.

Database capacity should therefore be evaluated using the actual workload rather than a single utilization metric.

---

# 13. Network Capacity

Network constraints may occur because of:

- bandwidth,
- connection limits,
- packet processing,
- network device capacity,
- provider quotas.

Network saturation can manifest as:

- increased latency,
- packet loss,
- timeouts,
- retries.

This can then trigger cascading failures.

---

# 14. Dependency Quotas

External services may impose limits such as:

- requests per second,
- requests per minute,
- concurrent requests,
- storage limits,
- API quotas.

These limits are part of the system's effective capacity.

A service that can generate 10,000 requests/second but whose dependency allows only 1,000 requests/second does not have an effective downstream capacity of 10,000 requests/second.

---

# 15. Capacity Bottlenecks

A system's effective capacity is often constrained by its bottleneck.

For example:

```text
API
 │
 ▼
Application
 │
 ▼
Database
 │
 ▼
External Service
```

If the external service can process only:

```text
500 operations/sec
```

that constraint may dominate the entire workflow.

Capacity analysis should therefore consider the complete request path.

---

# 16. Little's Law

For systems involving queues, Little's Law provides a useful relationship:

```text
L = λW
```

Where:

- `L` = average number of items in the system,
- `λ` = arrival rate,
- `W` = average time spent in the system.

This relationship helps explain why increasing latency can increase the amount of work simultaneously occupying system resources.

It is particularly useful when reasoning about:

- request concurrency,
- queues,
- workers,
- throughput,
- latency.

---

# 17. Concurrency

Throughput and concurrency are not interchangeable.

A system may have:

```text
High Throughput
```

while maintaining relatively low concurrency if operations complete quickly.

Conversely, slow operations can create:

```text
High Concurrency
```

even at moderate request rates.

Capacity planning should therefore consider both.

---

# 18. Rate Limiting

Rate limiting can protect a system from demand exceeding its safe processing capacity.

Potential strategies include:

- per-user limits,
- per-client limits,
- global limits,
- endpoint-specific limits,
- dependency-aware limits.

Rate limits should reflect business requirements rather than arbitrary numbers.

---

# 19. Admission Control

When capacity is limited, the system may need to decide which work is accepted.

For example:

```text
Incoming Work
      │
      ▼
Admission Control
      │
 ┌────┴─────┐
 ▼          ▼
Accept     Reject
```

Admission control is particularly useful when accepting unlimited work would cause a larger system failure.

---

# 20. Priority

Not all workloads have equal business value.

Where appropriate, workloads can be classified by priority.

For example:

```text
Critical Transaction
       │
       ▼
Highest Priority

Background Analytics
       │
       ▼
Lower Priority
```

Capacity pressure can then be handled according to business importance.

---

# 21. Graceful Degradation Under Load

When capacity becomes constrained, systems may reduce functionality rather than fail completely.

Possible strategies include:

- disable optional features,
- reduce response size,
- serve cached data,
- defer background processing,
- reject low-priority work.

The degradation strategy must preserve critical business correctness.

---

# 22. Autoscaling

Autoscaling can adjust capacity based on demand.

Possible scaling signals include:

- CPU,
- memory,
- request rate,
- queue depth,
- latency,
- custom business metrics.

Autoscaling should not be treated as a universal solution.

A bottleneck may exist in a non-scalable dependency.

---

# 23. Scaling Lag

Scaling is not instantaneous.

For example:

```text
Traffic Increase
      │
      ▼
Metric Changes
      │
      ▼
Scaling Decision
      │
      ▼
New Capacity Starts
      │
      ▼
Capacity Available
```

The project should understand this delay.

If traffic can increase faster than the system can scale, additional headroom or another protection mechanism may be required.

---

# 24. Scale-Up vs Scale-Out

Capacity may be increased by:

### Scale Up

Increase resources assigned to an existing instance.

### Scale Out

Add additional instances.

The appropriate strategy depends on:

- workload characteristics,
- application architecture,
- state management,
- cost,
- dependency constraints.

---

# 25. Stateful Scaling

Scaling stateful systems can be more difficult than scaling stateless services.

The project should consider:

- state location,
- partitioning,
- replication,
- consistency,
- synchronization,
- storage capacity.

Do not assume that adding application instances automatically increases total system capacity.

---

# 26. Capacity of Dependencies

Scaling one component may increase pressure on another.

For example:

```text
Application Instances
        │
        ▼
More Requests
        │
        ▼
Database
        │
        ▼
Database Saturation
```

Autoscaling the application may therefore make the system less reliable.

Scaling decisions should consider downstream capacity.

---

# 27. Load Testing

Where capacity is important, representative load testing should be considered.

Testing should attempt to establish:

- throughput,
- latency,
- resource utilization,
- saturation point,
- failure behavior.

The objective is not merely:

> "Can the application handle X requests?"

It is:

> **"How does the system behave as demand approaches and exceeds its safe operating boundary?"**

---

# 28. Capacity Testing

A useful capacity test should identify:

```text
Normal
   │
   ▼
Increasing Load
   │
   ▼
Safe Operating Region
   │
   ▼
Saturation
   │
   ▼
Failure / Degradation
```

This helps establish meaningful operating boundaries.

---

# 29. Performance vs Capacity

Performance asks:

> How quickly does the system perform a unit of work?

Capacity asks:

> How much work can the system sustain?

A system can be:

```text
Fast but low-capacity
```

or:

```text
Slower but highly scalable
```

Both dimensions should be considered.

---

# 30. Capacity Planning

Capacity planning should consider expected future demand.

Relevant inputs may include:

- historical traffic,
- business growth,
- planned launches,
- seasonal patterns,
- customer growth,
- data growth.

Planning should distinguish:

```text
Expected Growth
```

from:

```text
Unbounded Growth
```

No system can economically guarantee unlimited capacity.

---

# 31. Capacity Forecasting

Forecasting should identify when capacity thresholds may be reached.

For example:

```text
Current Storage
      │
      ▼
Growth Rate
      │
      ▼
Projected Capacity Date
```

This provides time for:

- scaling,
- optimization,
- migration,
- procurement,
- architectural changes.

---

# 32. Capacity During Recovery

Recovery itself may require additional capacity.

Examples:

- rebuilding indexes,
- replaying events,
- restoring databases,
- processing queued work,
- resynchronizing replicas.

A system that has just recovered from an outage may experience unusually high workload.

Recovery capacity should therefore be considered separately from normal capacity.

---

# 33. Recovery Storms

When a dependency or system component returns after an outage, accumulated work may suddenly be released.

For example:

```text
Outage
  │
  ▼
Backlog Accumulates
  │
  ▼
Dependency Recovers
  │
  ▼
Backlog Released
  │
  ▼
Traffic Spike
```

Recovery mechanisms should avoid overwhelming the recovered component.

---

# 34. Cost and Capacity

Additional capacity has a cost.

Capacity planning should therefore consider:

- infrastructure cost,
- scaling frequency,
- idle capacity,
- peak capacity,
- operational complexity.

The objective is not maximum resource allocation.

It is an appropriate balance between:

```text
Cost
+
Performance
+
Reliability
```

---

# 35. Capacity Observability

Important capacity indicators should be observable.

Depending on the workload, this may include:

- utilization,
- saturation,
- queue depth,
- queue age,
- request rate,
- concurrency,
- storage growth,
- connection usage,
- quota consumption.

Observability should focus on indicators that help predict or explain failure.

---

# 36. Capacity Alerts

Alerts should ideally identify approaching risk before complete exhaustion.

For example:

```text
Storage > 80%
```

may be more useful than:

```text
Storage = 100%
```

The appropriate threshold depends on:

- rate of consumption,
- recovery time,
- scaling time,
- business impact.

---

# 37. Capacity Runbooks

For important capacity constraints, operational guidance should identify:

- how to confirm the condition,
- immediate mitigation,
- scaling mechanism,
- safe limits,
- escalation path.

A capacity alert without an operational response may only tell engineers that failure is approaching.

---

# 38. Capacity Ownership

Capacity should have an owner.

Ownership may include:

- monitoring,
- forecasting,
- scaling configuration,
- load testing,
- optimization,
- quota management.

Ownership should not be assumed to belong automatically to the infrastructure team.

Application behavior often determines capacity.

---

# 39. Minimum Engineering Requirements

Every production project should:

- [ ] Identify major workload drivers.
- [ ] Identify important resource limits.
- [ ] Understand expected and peak demand.
- [ ] Identify major capacity bottlenecks.
- [ ] Define behavior when important resources approach exhaustion.
- [ ] Monitor meaningful capacity indicators.
- [ ] Establish appropriate capacity headroom.
- [ ] Consider dependency and quota limits.
- [ ] Define ownership of capacity management.

Higher-tier or high-scale systems may additionally require:

- [ ] Formal capacity models.
- [ ] Load testing.
- [ ] Capacity forecasting.
- [ ] Autoscaling.
- [ ] Admission control.
- [ ] Priority-based load shedding.
- [ ] Recovery-capacity testing.
- [ ] Regular capacity reviews.

---

# Relationship With Other Standards

This standard works with:

- `05-reliability/failure-management.md`
- `04-data/data-reliability-and-recovery.md`
- `07-delivery/`
- `08-observability/`
- `09-platform-and-infrastructure/`

The distinction is:

**Failure Management**

> What should happen when something fails?

**Capacity and Scalability**

> How much demand can the system sustain, and what happens as capacity is approached?

**Observability**

> How do we know that capacity or reliability is deteriorating?

**Platform and Infrastructure**

> What technical mechanisms provide and scale the required resources?

---

# Final Principle

> **Capacity is a reliability boundary. A system becomes unreliable not only when something breaks, but also when demand exceeds the resources available to serve it safely.**
