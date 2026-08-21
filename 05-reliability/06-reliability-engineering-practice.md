# Reliability Engineering Practice

> Reliability is not a one-time architecture decision. It is an ongoing engineering discipline.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Reliability Engineering

**Applies To:** All production projects, with rigor proportional to system tier and business criticality

---

# Purpose

The preceding reliability standards define:

- how systems should behave during failure,
- how capacity should be managed,
- how resilience should be tested,
- how systems should recover,
- how reliability should be measured.

Those practices are only effective if reliability remains part of normal engineering work.

This standard defines how teams incorporate reliability into:

- architecture,
- planning,
- development,
- delivery,
- operations,
- incident response,
- continuous improvement.

The objective is to prevent reliability from becoming a document that is reviewed once and then forgotten.

---

# Engineering Principle

> **Reliability should be continuously engineered, measured, reviewed, and improved throughout the system lifecycle.**

---

# 1. Reliability Is a Lifecycle Responsibility

Reliability should be considered across the entire lifecycle:

```text
Plan
 │
 ▼
Design
 │
 ▼
Build
 │
 ▼
Test
 │
 ▼
Deploy
 │
 ▼
Operate
 │
 ▼
Measure
 │
 ▼
Learn
 │
 └──────────────► Improve
```

Reliability is therefore not exclusively an operations responsibility.

---

# 2. Reliability Ownership

Every production system should have clear ownership for reliability.

Ownership should cover:

- reliability objectives,
- important failure modes,
- operational readiness,
- incident response,
- recovery,
- capacity,
- reliability improvements.

Ownership does not mean one person performs every reliability activity.

It means responsibility is explicit.

---

# 3. Shared Responsibility

Reliability may involve:

- developers,
- architects,
- platform engineers,
- security engineers,
- data engineers,
- operations,
- product owners.

Each group contributes different capabilities.

The project should avoid creating an artificial boundary such as:

> "Reliability belongs to the operations team."

Application architecture strongly influences reliability.

---

# 4. Reliability During Architecture

Important architectural decisions should consider:

- failure domains,
- dependencies,
- state,
- capacity,
- recovery,
- availability,
- operational complexity.

Architecture reviews should ask:

> What happens when this component fails?

and:

> How does the system recover?

---

# 5. Reliability During Design

Design should identify:

- critical paths,
- non-critical paths,
- dependency boundaries,
- resource limits,
- retry semantics,
- timeout behavior,
- degradation behavior.

Reliability decisions should be documented where they materially affect system behavior.

---

# 6. Reliability During Development

Developers should consider failure behavior when implementing:

- external calls,
- asynchronous processing,
- database operations,
- distributed workflows,
- caching,
- retries,
- background jobs.

Code should not silently inherit unsafe defaults.

---

# 7. Reliability During Code Review

Where relevant, code reviews should consider:

- timeout behavior,
- retry behavior,
- duplicate execution,
- resource consumption,
- failure propagation,
- error handling,
- observability.

Not every change requires a formal reliability review.

The depth should be proportional to the change risk.

---

# 8. Reliability During Testing

Testing should validate important reliability assumptions.

Potential techniques include:

- integration testing,
- load testing,
- failure testing,
- recovery testing,
- resilience testing.

The required depth depends on:

- system tier,
- failure impact,
- architectural complexity,
- change risk.

---

# 9. Reliability During Deployment

Production changes should consider:

- deployment risk,
- rollback,
- health validation,
- migration compatibility,
- configuration changes,
- dependency changes.

A deployment is itself a potential reliability event.

---

# 10. Reliability During Operations

While operating the system, teams should continuously understand:

- service health,
- capacity,
- dependency health,
- reliability objectives,
- incidents,
- recovery readiness.

Observability provides the evidence required for these decisions.

---

# 11. Reliability Signals

Important reliability signals may include:

### Availability

Is the service usable?

### Latency

Is it responding within the required time?

### Errors

Are requests or business operations failing?

### Saturation

Are important resources approaching limits?

### Correctness

Is the system producing the correct outcome?

### Freshness

Where applicable, is information sufficiently current?

The appropriate signals depend on the system.

---

# 12. Reliability Reviews

Reliability should be periodically reviewed for systems where the business impact justifies it.

A review may consider:

- SLO performance,
- incidents,
- capacity,
- failure modes,
- recovery testing,
- technical debt,
- dependency changes,
- upcoming business demand.

The review depth should be proportional to system criticality.

---

# 13. Reliability Review Questions

A review should ask:

### Are we meeting our reliability objectives?

If not:

- Why?
- Is the issue recurring?

### Have important failure modes changed?

New dependencies or architecture changes may introduce new risks.

### Has demand changed?

Capacity assumptions may no longer be valid.

### Has recovery been tested?

Recovery procedures may become stale.

### Has the business changed?

The required reliability level may have changed.

---

# 14. Reliability Debt

Reliability debt is accumulated engineering work that increases future operational risk.

Examples include:

- undocumented recovery procedures,
- missing timeouts,
- excessive retries,
- untested failover,
- obsolete runbooks,
- insufficient capacity headroom,
- weak observability,
- known single points of failure.

Reliability debt should be visible rather than hidden.

---

# 15. Reliability Debt Prioritization

Reliability debt should be prioritized based on:

```text
Business Impact
      ×
Likelihood
      ×
Exposure
      ×
Recovery Difficulty
```

The exact prioritization model may differ between organizations.

The principle is to focus engineering effort where risk is greatest.

---

# 16. Incident-Driven Improvement

Incidents provide evidence about how the system actually behaves.

After significant incidents, teams should determine:

- what failed,
- why it failed,
- why the failure propagated,
- why detection worked or failed,
- why recovery worked or failed,
- what assumptions were incorrect.

The resulting improvements should feed back into engineering work.

---

# 17. Near Misses

Not every important reliability lesson comes from an outage.

Examples include:

- an almost exhausted database,
- a failed deployment caught before broad rollout,
- an unexpectedly growing queue,
- a backup restoration that nearly failed.

Near misses should be treated as opportunities to identify weaknesses before they become incidents.

---

# 18. Blameless Learning

Reliability analysis should focus on system conditions rather than individual blame.

Humans operate within:

- tooling,
- processes,
- architecture,
- permissions,
- operational pressure,
- organizational constraints.

The goal is to improve the system so that the same failure is less likely to recur.

---

# 19. Reliability Improvement Work

Reliability findings should become actionable engineering work.

Possible actions include:

- architecture changes,
- automation,
- testing,
- observability improvements,
- capacity improvements,
- dependency changes,
- runbook updates,
- configuration changes.

---

# 20. Risk Acceptance

Not every reliability risk must be eliminated.

Some risks may be:

- low impact,
- expensive to eliminate,
- temporary,
- accepted by the business.

Accepted risks should be explicit.

For important risks, record:

- risk,
- impact,
- rationale,
- owner,
- review date where appropriate.

---

# 21. Reliability and Business Change

Business changes can alter reliability requirements.

Examples include:

- new customer segments,
- increased transaction volume,
- new geographic markets,
- new regulatory requirements,
- new revenue-critical workflows.

Reliability objectives should therefore be reconsidered when business criticality changes.

---

# 22. Reliability and Architecture Change

Architecture changes may invalidate previous reliability assumptions.

Examples include:

- introducing a new dependency,
- moving from synchronous to asynchronous processing,
- changing database technology,
- introducing caching,
- changing deployment topology,
- moving regions.

Significant architecture changes should therefore trigger a reliability review where appropriate.

---

# 23. Reliability and Dependency Change

Replacing or adding a dependency can change:

- availability,
- latency,
- quota,
- failure behavior,
- recovery,
- operational ownership.

Dependency changes should therefore consider the dependency's effect on the overall reliability contract.

---

# 24. Reliability and Capacity Growth

When demand grows, teams should reassess:

- capacity,
- scaling behavior,
- resource limits,
- dependency limits,
- recovery time,
- failure modes.

A system that was reliable at one scale may not remain reliable at another.

---

# 25. Reliability and Delivery Velocity

Reliability engineering should support safe delivery rather than unnecessarily slowing delivery.

Useful organizational signals include:

- deployment frequency,
- lead time for changes,
- change failure rate,
- recovery time.

A mature engineering organization aims for:

> **High delivery velocity with controlled reliability risk.**

---

# 26. Reliability Automation

Repeated reliability activities should be automated where practical.

Examples include:

- SLO calculation,
- capacity alerts,
- backup verification,
- recovery checks,
- health validation,
- resilience tests,
- dependency monitoring.

Automation should reduce both operational effort and human error.

---

# 27. Reliability Documentation

Important reliability knowledge should be discoverable.

This may include:

- architecture decisions,
- failure modes,
- SLOs,
- recovery procedures,
- capacity assumptions,
- dependency contracts,
- resilience tests.

Documentation should remain close to the system it describes where practical.

---

# 28. Reliability Documentation Must Age

Documentation becomes unreliable when the system changes.

Teams should therefore review important reliability documentation after:

- architecture changes,
- major incidents,
- recovery exercises,
- dependency changes,
- deployment changes.

---

# 29. Reliability Maturity

Reliability practices can mature over time.

A simple progression is:

```text
Reactive
   │
   ▼
Documented
   │
   ▼
Measured
   │
   ▼
Tested
   │
   ▼
Automated
   │
   ▼
Continuously Improved
```

A project does not need to begin at the highest maturity level.

The required maturity should correspond to business criticality.

---

# 30. Tier-Appropriate Reliability

The baseline should not force identical engineering investment on every system.

For example:

### Lower-risk system

May require:

- basic monitoring,
- defined recovery,
- reasonable capacity,
- documented ownership.

### High-criticality system

May additionally require:

- formal SLOs,
- error budgets,
- tested disaster recovery,
- resilience experiments,
- advanced capacity planning,
- stronger failure isolation.

The governance tier determines the required depth.

---

# 31. Reliability Review Evidence

A mature project should be able to provide evidence appropriate to its tier.

Examples include:

- SLO reports,
- incident history,
- recovery test results,
- capacity tests,
- resilience test results,
- backup restoration evidence,
- architecture decisions.

The objective is to move from:

> "We believe this is reliable."

to:

> **"Here is the evidence supporting our reliability assumptions."**

---

# 32. Reliability Baseline Checklist

Every production project should be able to answer:

- [ ] Who owns reliability?
- [ ] What are the important business capabilities?
- [ ] What are the important failure modes?
- [ ] What are the critical dependencies?
- [ ] What happens when those dependencies fail?
- [ ] What are the important capacity limits?
- [ ] How is failure detected?
- [ ] How is the system recovered?
- [ ] How is recovery validated?
- [ ] What evidence supports these assumptions?

Higher-tier systems should additionally establish appropriate:

- [ ] SLOs.
- [ ] Error budgets.
- [ ] Recovery exercises.
- [ ] Resilience testing.
- [ ] Capacity forecasting.
- [ ] Failure-domain analysis.
- [ ] Automated reliability controls.

---

# Relationship With Other Standards

This standard brings the reliability domain together with:

- `05-reliability/failure-management.md`
- `05-reliability/capacity-and-scalability.md`
- `05-reliability/resilience-testing.md`
- `05-reliability/recovery-and-continuity.md`
- `05-reliability/availability-and-service-levels.md`

It also connects reliability with:

- `01-governance/`
- `03-architecture/`
- `04-data/`
- `07-delivery/`
- `08-observability/`
- `09-platform-and-infrastructure/`
- `11-operational-readiness/`

---

# Final Principle

> **Reliability is not a feature delivered at the end of a project. It is a property continuously produced by architecture, engineering decisions, operations, measurement, and learning.**
