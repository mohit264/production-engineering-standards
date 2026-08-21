# Metrics

> A metric is a measured value representing some property of a system, workload, or business process over time.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Observability Engineering

---

# Purpose

A running system produces enormous numbers of individual events.

For example:

```text
Request 1 → 120 ms
Request 2 → 140 ms
Request 3 → 110 ms
...
Request 1,000,000 → 950 ms
```

Individual events are useful for investigation.

But operating a production system requires understanding the behavior of the population:

```text
How many requests?

How many failed?

How fast are they?

Is demand increasing?

Are resources approaching capacity?
```

Metrics provide a compact representation of this behavior over time.

---

# Engineering Principle

> **Metrics should turn important system behavior into measurable signals that can be compared, aggregated, alerted on, and understood over time.**

---

# 1. The Fundamental Problem

Suppose a service receives one million requests.

Logging every request may preserve detailed evidence, but an operator often needs a faster question:

> **Is the service healthy right now?**

A metric can answer:

```text
Requests/sec = 18,420
Error rate   = 0.8%
P95 latency  = 240 ms
```

Instead of inspecting a million individual events.

Metrics therefore provide a different form of evidence from logs.

---

# 2. Logs vs Metrics

Logs preserve individual events.

Metrics summarize behavior.

```text
Logs
 │
 ├── Request A
 ├── Request B
 ├── Request C
 └── ...
 
Metrics
 │
 ├── Request Rate
 ├── Error Rate
 └── Latency Distribution
```

Neither replaces the other.

A metric may tell us:

> Errors increased.

Logs may help answer:

> Which operations failed and why?

---

# 3. Metrics as Measurements

A metric should represent something measurable.

Examples:

```text
request_count
request_duration
active_connections
queue_depth
cpu_utilization
memory_usage
```

The meaning of each measurement should be explicit.

---

# 4. Measurement Requires a Definition

Consider:

```text
latency = 200 ms
```

What does that mean?

Possibilities include:

- average latency,
- median latency,
- P95 latency,
- maximum latency,
- server processing time,
- end-to-end request time.

A metric without a precise definition can create false confidence.

---

# 5. Metric Types

Different measurements answer different questions.

Common conceptual types include:

- counters,
- gauges,
- histograms,
- distributions.

The exact implementation terminology may vary between platforms.

---

# 6. Counters

A counter represents a quantity that generally increases as events occur.

Examples:

```text
requests_total
errors_total
messages_processed_total
```

The raw value is often less useful than its rate of change.

For example:

```text
requests_total
       │
       ▼
requests / second
```

---

# 7. Gauges

A gauge represents a value that can increase or decrease.

Examples:

```text
active_connections
queue_depth
memory_usage
temperature
```

A gauge answers:

> **What is the current measured state?**

---

# 8. Histograms

Some measurements represent distributions rather than a single value.

Latency is a classic example.

Suppose:

```text
90% requests < 200 ms
95% requests < 400 ms
99% requests < 2 s
```

This provides much more information than:

```text
Average = 180 ms
```

---

# 9. Why Averages Can Mislead

Consider ten requests:

```text
100
100
100
100
100
100
100
100
100
2000 ms
```

The average may appear reasonable.

But one request experienced severe latency.

At scale, a small percentage of extremely slow requests can represent significant user impact.

Metrics should therefore preserve useful distribution information where appropriate.

---

# 10. Percentiles

Percentiles describe the distribution of observations.

For example:

```text
P50 = 100 ms
P95 = 400 ms
P99 = 900 ms
```

These answer different questions.

P99 being unhealthy does not necessarily mean P50 is unhealthy.

The appropriate percentile depends on the service requirement.

---

# 11. Rates

Many operational questions concern change over time.

Examples:

```text
Requests/sec
Errors/sec
Messages/sec
Transactions/minute
```

Rates make raw counters operationally meaningful.

---

# 12. Error Rate

Error rate is often more useful than raw error count.

For example:

```text
Errors = 10,000
```

may sound severe.

But if:

```text
Total Requests = 100,000,000
```

the interpretation differs from:

```text
Errors = 10,000
Total Requests = 20,000
```

Metrics should provide enough context to interpret values correctly.

---

# 13. Saturation

A system may be healthy while demand is low but approaching failure as demand increases.

Saturation metrics indicate how close a resource is to its useful capacity.

Examples include:

- CPU utilization,
- memory pressure,
- connection pool usage,
- queue depth,
- thread pool utilization,
- storage capacity.

---

# 14. The Four Useful Dimensions

A practical starting point for service health is:

```text
Traffic
Errors
Latency
Saturation
```

These provide different perspectives on system behavior.

They are not a universal complete model, but they are useful foundations.

---

# 15. Business Metrics

Not all important metrics describe infrastructure.

Examples include:

```text
Orders completed
Payments successful
Subscriptions activated
Messages delivered
```

A system may be technically healthy while business outcomes are failing.

Business metrics therefore complement technical metrics.

---

# 16. System Metrics vs Business Metrics

Consider:

```text
CPU       = 30%
Memory    = 40%
Errors    = 0.1%
```

Everything looks healthy.

But:

```text
Successful checkout = -35%
```

The business system is clearly not healthy.

Operational observability should therefore include the metrics necessary to understand meaningful system outcomes.

---

# 17. Metric Dimensions

Metrics often need dimensions.

For example:

```text
request_count
service=payments
region=ap-south-1
status=success
```

Dimensions allow the same metric to be analyzed across different populations.

---

# 18. Cardinality

Dimensions can create enormous numbers of unique combinations.

Consider:

```text
service
region
endpoint
tenant
user
request_id
```

Combining these dimensions can produce extremely high cardinality.

High cardinality can increase:

- storage,
- indexing,
- query cost,
- memory usage,
- operational complexity.

---

# 19. Cardinality Is a Design Decision

A useful question is:

> **Will this dimension help us answer an important operational question?**

If not, it may not belong in the metric.

Avoid adding dimensions merely because the data is available.

---

# 20. Metric Naming

Metric names should communicate meaning.

Prefer:

```text
http_requests_total
```

over:

```text
counter1
```

Naming should be:

- consistent,
- stable,
- descriptive,
- understandable.

---

# 21. Units

Metrics should have explicit units where applicable.

Examples:

```text
duration_ms
bytes
requests_per_second
seconds
```

A value such as:

```text
duration = 10
```

is ambiguous without knowing whether it represents:

```text
10 ms
10 s
10 μs
```

---

# 22. Semantic Consistency

Different services should measure similar concepts consistently.

If one service reports:

```text
request_latency = time spent inside application
```

while another reports:

```text
request_latency = end-to-end client latency
```

the metrics cannot safely be compared.

Metric definitions therefore matter as much as metric values.

---

# 23. Metric Collection

A typical metric path may look like:

```text
Application
     │
     ▼
Metric Instrumentation
     │
     ▼
Collection
     │
     ▼
Storage
     │
     ▼
Query / Visualization
```

Each stage has operational considerations.

---

# 24. Pull vs Push

Metrics can be collected using different models.

### Pull

A collector periodically retrieves metrics.

```text
Collector
    │
    ▼
Application
```

### Push

The application or agent sends metrics.

```text
Application
    │
    ▼
Collector
```

Neither model is universally correct.

The architecture should determine the appropriate approach.

---

# 25. Collection Interval

Metrics are generally observed at intervals.

For example:

```text
10:00
10:01
10:02
10:03
```

The interval affects:

- detection speed,
- storage volume,
- resolution,
- cost.

Faster collection is not automatically better.

---

# 26. Resolution

A metric sampled every second can reveal behavior that a metric sampled every minute may hide.

But higher resolution increases cost.

The appropriate resolution depends on:

- failure speed,
- operational requirements,
- query requirements,
- cost.

---

# 27. Aggregation

Metrics are often aggregated.

Examples include:

```text
sum
average
minimum
maximum
percentile
rate
```

Aggregation reduces data volume while preserving useful information.

However, inappropriate aggregation can destroy important details.

---

# 28. Aggregation Can Hide Failures

Consider:

```text
Region A → healthy
Region B → failing
```

A global average may look healthy.

Therefore, metrics should be aggregated at dimensions that preserve meaningful failure boundaries.

---

# 29. Time Series

A metric is commonly represented as a time series:

```text
Time
 │
 ├── 10:00 → 100
 ├── 10:01 → 120
 ├── 10:02 → 150
 └── 10:03 → 900
```

The shape of the series can reveal:

- trends,
- spikes,
- regressions,
- periodic behavior.

---

# 30. Trends

Metrics are particularly useful for understanding change over time.

Examples:

```text
Traffic increasing
Memory gradually increasing
Latency degrading
Storage approaching capacity
```

A single measurement often cannot reveal these patterns.

---

# 31. Baselines

A metric becomes more useful when compared against a meaningful baseline.

Examples:

```text
Current traffic
     vs
Typical traffic
```

or:

```text
Current latency
     vs
Previous release
```

Baselines should be chosen carefully.

Historical averages can be misleading when system behavior changes.

---

# 32. Anomaly Detection

Metrics can support detection of unusual behavior.

For example:

```text
Normal
  │
  │
  │
  └─────── Spike
```

Anomaly detection may be:

- threshold-based,
- statistical,
- seasonal,
- model-based.

The detection mechanism should reflect the nature of the metric.

---

# 33. Thresholds

A threshold defines a boundary that may require attention.

For example:

```text
queue_depth > 10,000
```

Thresholds are useful when the system has meaningful boundaries.

But arbitrary thresholds can produce noise.

---

# 34. Alertability

Not every metric should generate an alert.

A metric may exist for:

- dashboards,
- capacity planning,
- investigation,
- reporting.

Alerts should be reserved for conditions that require action.

---

# 35. Metric vs Alert

A metric answers:

> What is happening?

An alert answers:

> Does someone need to act?

These are different concerns.

A healthy observability architecture should avoid turning every metric into an alert.

---

# 36. SLO Metrics

Service-level objectives require measurements that represent user-relevant reliability.

Examples include:

```text
Availability
Latency
Successful requests
```

Metrics should support the calculations required by the chosen SLOs.

---

# 37. Error Budgets

Error budgets translate reliability objectives into measurable allowance.

For example:

```text
Target Availability = 99.9%
```

The remaining allowable failure becomes an operational budget.

Metrics provide the evidence required to understand how quickly that budget is being consumed.

---

# 38. Metrics During Incidents

Metrics are particularly useful during incidents because they compress large volumes of behavior into recognizable signals.

An engineer may observe:

```text
14:02 → normal
14:05 → latency increases
14:07 → errors increase
14:09 → CPU increases
```

This timeline provides a starting point for investigation.

---

# 39. Correlation With Changes

Metrics should be correlated with significant system changes.

For example:

```text
Deployment
     │
     ▼
Latency Increase
     │
     ▼
Error Increase
```

Correlation does not prove causation.

But it provides useful evidence for investigation.

---

# 40. Metrics and Logs

Metrics can identify:

```text
Something changed.
```

Logs can provide:

```text
Individual evidence about what happened.
```

A common investigation pattern is:

```text
Metric
  │
  ▼
Identify abnormal period
  │
  ▼
Search logs
  │
  ▼
Investigate events
```

---

# 41. Metrics and Traces

Metrics may indicate:

```text
Latency increased.
```

Traces can help identify:

```text
Which operation or dependency contributed to that latency?
```

The signals therefore complement each other.

---

# 42. Metrics and Infrastructure

Infrastructure metrics may include:

- CPU,
- memory,
- disk,
- network,
- connections,
- queue depth.

These metrics help determine whether application behavior is influenced by infrastructure constraints.

---

# 43. Metrics and Dependencies

Dependencies should have measurable health where appropriate.

Examples include:

```text
Database latency
Cache hit rate
External API errors
Queue backlog
```

Without dependency metrics, local symptoms may be difficult to explain.

---

# 44. Metric Reliability

Metrics themselves can be wrong.

Possible causes include:

- instrumentation bugs,
- incorrect units,
- missing samples,
- collector failures,
- clock problems,
- aggregation errors.

Operational decisions should consider the reliability of the measurement system itself.

---

# 45. Missing Metrics

Missing data should not automatically be interpreted as:

```text
Healthy
```

It may mean:

```text
Application stopped
Collector failed
Network failed
Instrumentation failed
```

The system should distinguish absence of evidence from evidence of health where possible.

---

# 46. Staleness

A metric may exist but no longer represent current system state.

For example:

```text
Last sample = 30 minutes ago
```

A dashboard displaying that value without indicating staleness can create false confidence.

---

# 47. Metric Availability

Critical operational metrics should have appropriate availability.

If the metric needed to determine service health disappears during an incident, operational response becomes harder.

Observability infrastructure should therefore be designed with appropriate resilience.

---

# 48. Metric Cost

Metrics consume resources through:

- instrumentation,
- collection,
- transport,
- storage,
- indexing,
- querying.

High-cardinality metrics can be particularly expensive.

Metric design should therefore include cost considerations from the beginning.

---

# 49. Metric Lifecycle

Metrics should not live forever simply because they were once useful.

Teams should periodically evaluate:

- who uses the metric,
- what decision it supports,
- whether it remains meaningful,
- whether its cost is justified.

Unused telemetry creates operational clutter.

---

# 50. Minimum Engineering Requirements

Every production project should:

- [ ] Define important system and business measurements.
- [ ] Give metrics precise semantics.
- [ ] Define units where applicable.
- [ ] Use consistent naming.
- [ ] Control metric cardinality.
- [ ] Measure meaningful service health dimensions.
- [ ] Measure important dependencies.
- [ ] Preserve useful distributions for latency-sensitive systems.
- [ ] Define appropriate collection resolution.
- [ ] Distinguish metrics from alerts.
- [ ] Account for missing or stale measurements.
- [ ] Protect metric infrastructure appropriately.
- [ ] Define metric ownership.
- [ ] Consider telemetry cost.

Higher-risk systems may additionally require:

- [ ] Formal metric schemas.
- [ ] SLO-aligned metrics.
- [ ] Error-budget tracking.
- [ ] Advanced anomaly detection.
- [ ] High-resolution telemetry.
- [ ] Business-level metrics.
- [ ] Automated metric-quality validation.
- [ ] Formal cardinality governance.
- [ ] Cross-service metric standards.

---

# Relationship With Other Standards

This standard works with:

- `08-observability/README.md`
- `08-observability/02-logs.md`
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
- CloudWatch,
- Azure Monitor,
- Datadog,
- InfluxDB,
- VictoriaMetrics,
- a particular metrics protocol.

Those are implementation choices.

The engineering contract is:

> **Important system behavior must be represented by measurements whose meaning, accuracy, granularity, and cost are understood well enough to support operational decisions.**

---

# Final Principle

> **Logs help us remember individual events. Metrics help us understand the behavior of the whole system over time. A mature metric is not merely a number—it is a well-defined measurement whose meaning remains trustworthy when someone has to make an operational decision under pressure.**
