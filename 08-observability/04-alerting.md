# Alerting

> An alert is a decision that an observed condition requires attention or action.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Observability Engineering

---

# Purpose

Observability produces evidence.

Metrics change.

Logs appear.

Traces reveal execution behavior.

But evidence alone does not tell us whether somebody should act.

For example:

```text
CPU = 90%
```

is a measurement.

It is not automatically an incident.

Similarly:

```text
Error rate = 5%
```

is a measurement.

The engineering question is:

> **When does an observed condition become important enough to require action?**

Alerting exists to answer that question.

---

# Engineering Principle

> **An alert should represent a condition that requires a defined action by a defined owner within an appropriate timeframe.**

---

# 1. The Fundamental Problem

Imagine a production system generating thousands of telemetry signals:

```text
Metrics
Logs
Traces
Events
```

Something unusual happens.

Without alerting:

```text
System changes
      │
      ▼
Telemetry records change
      │
      ▼
Nobody notices
```

The system may remain degraded until a customer reports the problem.

Alerting introduces a decision mechanism:

```text
Observed Condition
       │
       ▼
Evaluation
       │
       ▼
Action Required?
       │
       ├── No → Continue observing
       │
       └── Yes
             │
             ▼
           Alert
```

---

# 2. Metric Is Not an Alert

A metric answers:

> **What is happening?**

An alert answers:

> **Does this require action?**

For example:

```text
CPU = 92%
```

is a metric.

An alert might be:

```text
Production service has sustained CPU saturation
for 10 minutes and capacity is affecting request latency.
```

The second statement contains operational meaning.

---

# 3. Alert Is Not a Notification

These concepts should remain separate.

```text
Signal
  │
  ▼
Alert Decision
  │
  ▼
Notification
  │
  ▼
Human / Automation
```

A notification is merely a delivery mechanism.

An alert represents the decision that action may be required.

---

# 4. Alerting Begins With Action

Before creating an alert, ask:

> **What will someone do when this alert fires?**

If the answer is:

```text
Nothing.
```

the alert probably should not exist.

Alerts should exist because they trigger a meaningful response.

---

# 5. The Alert Contract

Every important alert should define:

* what condition triggers it,
* why the condition matters,
* who owns the response,
* what action is expected,
* how quickly action is required,
* how the alert resolves.

Conceptually:

```text
Condition
   +
Meaning
   +
Owner
   +
Action
   +
Urgency
```

---

# 6. Symptoms vs Causes

A common mistake is alerting on every possible cause.

For example:

```text
CPU high
Memory high
Thread pool high
Database connections high
Queue depth high
```

All may occur during the same incident.

This can create many alerts for one underlying problem.

A useful alerting system should distinguish between:

```text
Root Cause
```

and:

```text
Observable User Impact
```

where practical.

---

# 7. Alert on User-Relevant Failure

A strong principle is:

> **Prefer alerts that represent meaningful service impact over alerts that merely represent internal activity.**

For example:

```text
Error rate affecting requests
```

may be more actionable than:

```text
CPU > 80%
```

CPU may be a useful diagnostic signal without necessarily indicating an incident.

---

# 8. Thresholds

Some alerts are naturally threshold-based.

For example:

```text
queue_depth > 10,000
```

or:

```text
disk_free < 10%
```

Thresholds work well when the boundary has clear operational meaning.

---

# 9. Thresholds Need Context

A threshold without context can create noise.

Consider:

```text
CPU > 80%
```

If CPU regularly reaches 90% while the service remains healthy, the alert is not useful.

The threshold should therefore reflect actual system behavior and operational consequences.

---

# 10. Duration

Transient conditions should not necessarily generate alerts.

For example:

```text
CPU > threshold
```

for:

```text
10 seconds
```

may be harmless.

While:

```text
CPU > threshold
```

for:

```text
20 minutes
```

may indicate sustained saturation.

Alert conditions should therefore consider time where appropriate.

---

# 11. Rate of Change

Sometimes the speed of change matters more than the absolute value.

For example:

```text
Error rate:
0.1%
0.2%
0.4%
0.8%
1.6%
3.2%
```

The rapid increase may be more important than the current absolute value.

Alerting should therefore consider both:

* state,
* behavior over time.

---

# 12. Static vs Dynamic Conditions

Some systems have stable operating boundaries.

Others have highly variable workloads.

A fixed threshold may work well for:

```text
disk capacity
```

but poorly for:

```text
request volume
```

Dynamic or baseline-based alerting may be more appropriate where behavior varies naturally.

---

# 13. Alert Severity

Alerts may have different levels of urgency.

For example:

```text
Critical
Warning
Informational
```

The exact taxonomy is organizational.

The important principle is:

> **Severity should communicate the urgency and expected response.**

---

# 14. Severity Must Have Meaning

A `Critical` alert should not simply mean:

```text
This metric is high.
```

It should mean something operationally meaningful, such as:

```text
Immediate intervention is required.
```

If every alert is critical, severity loses meaning.

---

# 15. Urgency

Different conditions require different response times.

For example:

```text
Customer-facing outage
      → immediate response

Capacity approaching limit
      → planned response

Long-term trend
      → engineering review
```

Alert urgency should reflect the required response window.

---

# 16. Ownership

Every actionable alert should have an owner.

An alert without ownership creates:

```text
Alert
  │
  ▼
Everyone assumes someone else will respond
```

Ownership should be explicit.

Possible ownership boundaries include:

* service team,
* platform team,
* security team,
* database team,
* on-call engineer.

---

# 17. Alert Routing

Different alerts may require different destinations.

For example:

```text
Application failure
      → Application on-call

Infrastructure failure
      → Platform on-call

Security event
      → Security response

Capacity warning
      → Engineering planning
```

Routing should reflect ownership and urgency.

---

# 18. Notification Channels

Possible notification mechanisms include:

* paging,
* email,
* chat,
* ticketing,
* incident-management systems.

The mechanism is secondary.

The important requirement is that the right owner receives the right signal with the right urgency.

---

# 19. Paging

Paging should be reserved for conditions requiring prompt human response.

A page that does not require immediate action creates unnecessary interruption.

This is one of the primary sources of alert fatigue.

---

# 20. Alert Fatigue

Suppose an engineer receives:

```text
100 alerts/day
```

and only one requires meaningful action.

Eventually the engineer learns:

```text
Alerts are usually noise.
```

This is dangerous.

Alerting systems should optimize for:

```text
Actionable Signal
```

rather than:

```text
Maximum Detection
```

---

# 21. Alert Quality

A useful alert should answer:

```text
What happened?

Why does it matter?

How urgent is it?

Who owns it?

What should I do?
```

An alert such as:

```text
CPU HIGH
```

is often insufficient.

---

# 22. Alert Context

An alert should provide enough context to begin investigation.

Useful information may include:

* affected service,
* environment,
* time,
* measured condition,
* current value,
* expected boundary,
* affected scope,
* relevant links or references.

---

# 23. Alert Runbooks

Important alerts should have an associated response procedure.

For example:

```text
Alert
  │
  ▼
Runbook
  │
  ▼
Investigation
  │
  ▼
Mitigation
```

A runbook reduces dependence on individual memory during stressful incidents.

---

# 24. Alerts Should Be Testable

An alert that has never been tested is an assumption.

Teams should periodically verify:

* condition evaluation,
* routing,
* notification,
* ownership,
* escalation,
* runbook accessibility.

Alerting infrastructure should be treated as production infrastructure.

---

# 25. False Positives

A false positive occurs when an alert fires but meaningful action is not required.

Excessive false positives create:

```text
Noise
  │
  ▼
Alert Fatigue
  │
  ▼
Reduced Trust
```

Alert thresholds and conditions should therefore be continuously evaluated.

---

# 26. False Negatives

The opposite problem is more dangerous.

A false negative occurs when a meaningful failure does not generate an alert.

For example:

```text
Customer-facing outage
       │
       ▼
No alert
```

The organization discovers the problem only after customer reports.

Alerting should therefore balance:

```text
Sensitivity
     vs
Noise
```

---

# 27. Alert Evaluation

Alert conditions may be evaluated using:

* thresholds,
* rates,
* ratios,
* durations,
* percentiles,
* absence of expected signals,
* SLO conditions,
* composite conditions.

The evaluation method should match the failure mode.

---

# 28. Absence as a Signal

Sometimes the important condition is:

> **Something expected stopped happening.**

Examples:

```text
No successful transactions
No heartbeat
No messages consumed
No telemetry received
```

Absence can therefore be an important alerting condition.

But missing telemetry must be distinguished from actual system inactivity where possible.

---

# 29. Composite Alerts

A single symptom can be ambiguous.

For example:

```text
CPU = 90%
```

may be normal.

But:

```text
CPU = 90%
AND
latency increasing
AND
error rate increasing
```

may represent a meaningful incident.

Composite conditions can improve signal quality when designed carefully.

---

# 30. SLO-Based Alerting

Reliability objectives can provide stronger alerting foundations than arbitrary infrastructure thresholds.

For example:

```text
Service SLO
    │
    ▼
Error Budget Consumption
    │
    ▼
Alert
```

This ties alerting to user-visible reliability rather than merely infrastructure state.

---

# 31. Burn Rate

A service may consume its reliability budget slowly or rapidly.

For example:

```text
Normal consumption
        vs
Rapid consumption
```

Rapid consumption can indicate an incident even before a long-term availability target is technically violated.

Burn-rate-based alerting can therefore detect significant reliability degradation earlier.

---

# 32. Alert Deduplication

One incident can produce many identical alert evaluations.

For example:

```text
1 failure
  │
  ├── Service alert
  ├── Dependency alert
  ├── CPU alert
  ├── Latency alert
  └── Error alert
```

Alert systems should reduce unnecessary duplication where possible.

---

# 33. Alert Grouping

Related alerts may be grouped into a single operational event.

For example:

```text
Database outage
   │
   ├── Service A affected
   ├── Service B affected
   └── Service C affected
```

Instead of treating these as three unrelated incidents, the system can expose the common failure context.

---

# 34. Alert Suppression

Some alerts may be temporarily suppressed when they are known consequences of another condition.

For example:

```text
Planned maintenance
      │
      ▼
Expected service unavailable
```

Alert suppression should be controlled carefully.

Permanent suppression can hide real failures.

---

# 35. Maintenance Windows

Planned changes may intentionally create conditions that normally trigger alerts.

Maintenance mechanisms can prevent unnecessary notifications.

However:

> **Maintenance suppression must never become a way to hide unexpected failures.**

---

# 36. Escalation

If the primary owner does not respond within the required timeframe, escalation may be necessary.

Conceptually:

```text
Alert
  │
  ▼
Primary Owner
  │
  │ no response
  ▼
Secondary Owner
  │
  │ no response
  ▼
Escalation
```

Escalation should reflect actual organizational responsibility.

---

# 37. Acknowledgement

An acknowledgement means:

> **Someone has accepted responsibility for responding.**

It does not necessarily mean:

```text
Problem fixed.
```

These states should remain distinct.

---

# 38. Alert Lifecycle

An actionable alert typically moves through states such as:

```text
Normal
  │
  ▼
Condition Detected
  │
  ▼
Alert Active
  │
  ▼
Acknowledged
  │
  ▼
Mitigation
  │
  ▼
Resolved
```

The exact lifecycle depends on the incident-management system.

---

# 39. Auto-Resolution

Some alerts can resolve automatically when the underlying condition disappears.

For example:

```text
Error rate > threshold
       │
       ▼
Alert active
       │
       ▼
Error rate returns to normal
       │
       ▼
Resolved
```

Auto-resolution is useful when the underlying condition is objectively measurable.

---

# 40. Alert Persistence

An alert should not necessarily disappear immediately after a short transient recovery.

The required behavior depends on the condition and operational process.

The important principle is that alert lifecycle semantics should be explicit.

---

# 41. Alert Ownership During Incidents

Ownership may change during an incident.

For example:

```text
Application team
      │
      ▼
Platform dependency identified
      │
      ▼
Platform team engaged
```

Alerting should support collaboration rather than create organizational silos.

---

# 42. Alerting and Incident Management

Alerting detects conditions.

Incident management coordinates response.

They are related but different responsibilities.

```text
Observability
      │
      ▼
Alert
      │
      ▼
Incident Management
      │
      ▼
Response
```

Not every alert needs to become a formal incident.

---

# 43. Alerting and Automation

Some conditions can trigger automated actions.

For example:

```text
Queue depth increasing
       │
       ▼
Automated scaling
```

Automation can reduce response time.

But automated remediation should be introduced only when:

* the condition is sufficiently understood,
* the action is safe,
* failure modes are understood,
* rollback is possible.

---

# 44. Alerting Does Not Equal Automation

An alert can trigger:

```text
Human response
```

or:

```text
Automated response
```

The decision should depend on the risk and confidence associated with the condition.

---

# 45. Alert Security

Alert systems may contain sensitive information.

Examples include:

* customer impact,
* infrastructure details,
* security events,
* internal topology.

Access should therefore be controlled appropriately.

---

# 46. Alert Cost

Alerting has an organizational cost.

Every alert can consume:

* engineer attention,
* on-call time,
* incident-management capacity.

The cost is therefore not merely infrastructure cost.

It is also cognitive cost.

---

# 47. Alert Review

Alerts should periodically be reviewed.

Questions include:

```text
Did this alert lead to action?

Was it noisy?

Was it useful?

Was the owner correct?

Was the threshold appropriate?

Did the runbook help?

Could another alert represent the same failure better?
```

Unused or noisy alerts should be removed or redesigned.

---

# 48. Alert Ownership Is a Lifecycle Responsibility

Ownership should include:

* creation,
* review,
* tuning,
* testing,
* response,
* retirement.

An alert without ongoing ownership becomes operational debt.

---

# 49. Minimum Engineering Requirements

Every production project should:

* [ ] Define actionable alert conditions.
* [ ] Assign an owner to each important alert.
* [ ] Define severity and urgency.
* [ ] Define expected response.
* [ ] Provide sufficient alert context.
* [ ] Avoid paging for non-urgent conditions.
* [ ] Provide runbooks for important alerts.
* [ ] Test alert delivery and routing.
* [ ] Monitor false-positive and false-negative behavior.
* [ ] Define alert lifecycle semantics.
* [ ] Review alert quality periodically.
* [ ] Control alert duplication and noise.
* [ ] Protect sensitive alert information.

Higher-risk systems may additionally require:

* [ ] SLO-based alerting.
* [ ] Error-budget burn-rate alerts.
* [ ] Alert grouping and correlation.
* [ ] Automated escalation.
* [ ] Formal on-call ownership.
* [ ] Automated remediation.
* [ ] Alert-quality dashboards.
* [ ] Synthetic failure testing.
* [ ] Formal alert governance.
* [ ] Alert dependency mapping.

---

# Relationship With Other Standards

This standard works with:

* `08-observability/README.md`
* `08-observability/02-logs.md`
* `08-observability/03-metrics.md`
* `08-observability/04-traces.md`
* `08-observability/06-observability-governance.md`

It also connects directly with:

* `05-reliability/`
* `06-security/`
* `07-delivery/`
* `11-operational-readiness/`

---

# What This Standard Is Not

This standard does not prescribe:

* PagerDuty,
* Opsgenie,
* Alertmanager,
* CloudWatch Alarms,
* Azure Monitor Alerts,
* Datadog,
* Slack,
* email,
* any particular incident-management or notification platform.

Those are implementation choices.

The engineering contract is:

> **An alert must represent a meaningful condition that requires action, have an accountable owner, communicate appropriate urgency, and provide enough context for the expected response.**

---

# Final Principle

> **Telemetry tells us what the system is doing. Alerting decides when that behavior matters enough to interrupt someone. Good alerting does not maximize the number of things we detect—it maximizes the number of important conditions that lead to the right action.**
