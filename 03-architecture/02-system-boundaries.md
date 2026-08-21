# System Boundaries

> Architecture begins by deciding where responsibility, ownership, trust, data, failure, and change should stop.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Architecture

**Applies To:** Every software system

---

# Purpose

Every software system has boundaries.

Some are obvious:

- an application boundary,
- a database boundary,
- a network boundary.

Others are less visible:

- responsibility,
- ownership,
- trust,
- data,
- deployment,
- failure,
- organizational boundaries.

Architectural quality depends heavily on whether these boundaries are placed intentionally.

This standard establishes how engineering teams should identify, evaluate, and evolve system boundaries.

---

# Why This Standard Exists

Many architectural problems are actually boundary problems.

Examples:

- Two teams modify the same responsibility.
- Multiple services own the same data.
- A failure in one component affects unrelated capabilities.
- A supposedly independent service cannot be deployed independently.
- Security-sensitive functionality crosses an unclear trust boundary.
- A component is separated technically but remains tightly coupled operationally.

Creating more components does not automatically create better boundaries.

A boundary is useful only when it creates meaningful independence.

---

# Engineering Principle

> **A boundary is valuable when it establishes meaningful ownership, responsibility, or independence while keeping the cost of crossing that boundary acceptable.**

---

# What Is a System Boundary?

A system boundary separates one area of responsibility from another.

A boundary determines:

- what belongs inside,
- what belongs outside,
- who owns it,
- how interaction occurs,
- what assumptions cross the boundary,
- what failures can cross it,
- how changes propagate across it.

A boundary is therefore both a **structural decision** and a **contract**.

---

# Boundaries Are Multidimensional

There is rarely a single boundary in a software system.

Consider:

```text
                     SYSTEM
┌──────────────────────────────────────────────┐
│                                              │
│   Business Boundary                          │
│   ┌──────────────────────────────────────┐   │
│   │                                      │   │
│   │   Responsibility Boundary             │   │
│   │                                      │   │
│   │   ┌──────────────────────────────┐   │   │
│   │   │                              │   │   │
│   │   │   Data Ownership Boundary    │   │   │
│   │   │                              │   │   │
│   │   └──────────────────────────────┘   │   │
│   │                                      │   │
│   └──────────────────────────────────────┘   │
│                                              │
│   Trust Boundary                             │
│                                              │
│   Deployment Boundary                        │
│                                              │
│   Failure Boundary                           │
│                                              │
└──────────────────────────────────────────────┘
```

These boundaries may coincide.

They do not have to.

---

# 1. Business Boundaries

Business capabilities are often useful starting points for identifying architectural boundaries.

Examples:

- Customer Management
- Catalog
- Ordering
- Payments
- Fulfillment
- Reporting

The goal is not to turn every business capability into a service.

The goal is to understand meaningful responsibilities.

---

# 2. Responsibility Boundaries

A responsibility boundary defines what a component or subsystem is responsible for.

A good responsibility boundary makes it possible to answer:

> What does this component own?

and:

> What does it deliberately not own?

Ambiguous responsibility is a major source of architectural coupling.

---

# 3. Ownership Boundaries

Every significant capability should have clear ownership.

Ownership includes responsibility for:

- implementation,
- deployment,
- operation,
- incidents,
- security,
- lifecycle,
- change.

Technical separation without ownership separation may provide little practical benefit.

---

# 4. Data Boundaries

Data boundaries define who owns and controls a dataset.

Important questions include:

- Who is the authoritative owner?
- Who can modify the data?
- Who can read it?
- How is it shared?
- What consistency is required?
- What happens when the owner is unavailable?

Data boundaries are often more important than code boundaries.

---

# 5. Trust Boundaries

A trust boundary separates areas with different security assumptions.

Examples include:

- Internet and application,
- application and privileged infrastructure,
- users and administrative systems,
- one security domain and another.

Crossing a trust boundary should require explicit security controls appropriate to the risk.

---

# 6. Deployment Boundaries

A deployment boundary determines what can be released independently.

A useful deployment boundary can allow:

- independent releases,
- independent rollback,
- controlled blast radius,
- separate scaling.

However, deployment separation creates operational overhead.

Therefore:

> A deployment boundary should exist only when independent deployment provides meaningful value.

---

# 7. Scaling Boundaries

A scaling boundary allows one workload to scale independently from another.

This can be valuable when workloads have significantly different:

- traffic patterns,
- resource requirements,
- performance requirements.

But independent scaling often requires additional infrastructure and operational complexity.

---

# 8. Failure Boundaries

A failure boundary determines how far a failure can propagate.

Questions include:

- If this component fails, what else fails?
- If it becomes slow, what happens?
- Can callers continue operating?
- Can the system degrade gracefully?
- Can the component recover independently?

Failure isolation should be considered when establishing architectural boundaries.

---

# 9. Change Boundaries

A change boundary determines how far a change propagates.

If changing one capability requires coordinated changes across many unrelated components, the architecture contains significant change coupling.

Useful boundaries allow related functionality to evolve together while unrelated functionality remains unaffected.

---

# Boundary Alignment

Some boundaries benefit from alignment.

For example:

```text
Business Responsibility
        │
        ▼
Ownership
        │
        ▼
Data Ownership
        │
        ▼
Change Boundary
        │
        ▼
Deployment Boundary
```

Strong alignment can create meaningful independence.

However, forcing every boundary to align can also introduce unnecessary complexity.

Architecture should align boundaries deliberately, not mechanically.

---

# The Cost of Crossing a Boundary

Every boundary introduces a cost.

Crossing a boundary may require:

- communication,
- serialization,
- authentication,
- authorization,
- network calls,
- retries,
- monitoring,
- error handling,
- contract management,
- operational coordination.

Therefore:

> **Boundaries are not free.**

The benefits of separation must justify the cost of crossing the boundary.

---

# In-Process vs Distributed Boundaries

A responsibility boundary does not necessarily require a network boundary.

For example:

```text
Modular Monolith

┌──────────────────────────────────────┐
│ Application                          │
│                                      │
│  ┌──────────┐    ┌──────────────┐   │
│  │ Catalog  │    │ Ordering     │   │
│  │ Module   │    │ Module       │   │
│  └──────────┘    └──────────────┘   │
│                                      │
└──────────────────────────────────────┘
```

The modules may have strong logical boundaries while remaining in one deployable unit.

This can provide:

- simpler deployment,
- simpler debugging,
- simpler local communication,

while preserving the possibility of future separation if justified.

---

# Distributed Boundary

A network boundary introduces substantially more responsibility.

```text
┌──────────────────┐
│ Ordering Service │
└────────┬─────────┘
         │
      Network
         │
┌────────▼─────────┐
│ Payment Service  │
└──────────────────┘
```

Now the architecture must account for:

- latency,
- timeouts,
- retries,
- partial failure,
- authentication,
- versioning,
- observability,
- service discovery,
- deployment coordination.

Therefore:

> **A distributed boundary should be introduced because the independence it provides is valuable—not simply because the responsibility is conceptually separate.**

---

# Cohesion and Coupling

Boundary design should balance two forces.

## Cohesion

Responsibilities inside a boundary should have meaningful relationships.

High cohesion generally makes systems easier to understand and evolve.

---

## Coupling

Dependencies across boundaries should be intentional and manageable.

Excessive coupling makes independent evolution difficult.

---

# A Useful Boundary Test

For a proposed boundary, ask:

### Responsibility

- Does the boundary contain a meaningful responsibility?

### Ownership

- Can one team or organizational unit reasonably own it?

### Data

- Does it have coherent data ownership?

### Change

- Do its responsibilities tend to change together?

### Failure

- Would isolating failure provide value?

### Scaling

- Would independent scaling provide value?

### Security

- Does it represent a meaningful trust boundary?

### Deployment

- Would independent deployment provide value?

### Cost

- Is the benefit worth the cost of crossing the boundary?

If most answers are "no", the boundary may be artificial.

---

# Boundary Smells

The following patterns should trigger architectural review.

## Shared Mutable Data

Multiple components directly modify the same authoritative dataset.

Potential consequences:

- hidden coupling,
- inconsistent business rules,
- unclear ownership.

---

## Distributed Monolith

Components are technically separate but require coordinated:

- deployments,
- releases,
- changes,
- availability.

The system has distributed complexity without meaningful independence.

---

## Chatty Boundaries

A component requires many cross-boundary calls to perform a simple operation.

Potential consequences:

- increased latency,
- increased failure probability,
- difficult troubleshooting.

---

## Artificial Decomposition

A responsibility is separated purely because a pattern recommends separation.

The resulting boundary creates complexity without meaningful independence.

---

## Ownership Ambiguity

Multiple teams believe they own the same capability.

This often results in:

- delayed decisions,
- conflicting changes,
- unclear incident responsibility.

---

## Boundary Leakage

Internal implementation details cross the boundary.

Examples:

- exposing internal database structures,
- sharing internal domain models,
- allowing callers to depend on implementation-specific behavior.

Boundary leakage increases coupling.

---

# Boundary Evolution

Boundaries should evolve with evidence.

A system may begin as:

```text
Single Application
```

and later evolve into:

```text
Modular Application
```

and eventually:

```text
Multiple Independently Operated Components
```

There is no requirement to start with the most distributed architecture.

A boundary should become more independent when evidence demonstrates that independence provides sufficient value.

---

# When to Split a Boundary

Consider stronger separation when:

- responsibilities evolve independently,
- ownership is clearly separate,
- scaling requirements differ significantly,
- failure isolation is valuable,
- security requirements differ,
- deployment independence provides substantial value,
- organizational boundaries require independence.

---

# When Not to Split

Avoid separation when:

- responsibilities always change together,
- data is tightly coupled,
- communication is extremely frequent,
- independent scaling is unnecessary,
- independent deployment provides little value,
- operational capability is insufficient,
- the boundary exists only to follow a fashionable architecture.

---

# Boundary Review Checklist

Before introducing a significant architectural boundary:

### Problem

- [ ] What problem does the boundary solve?

### Responsibility

- [ ] Is the responsibility cohesive?

### Ownership

- [ ] Is ownership clear?

### Data

- [ ] Is data ownership clear?

### Change

- [ ] Can the boundary evolve independently?

### Failure

- [ ] Does the boundary provide useful failure isolation?

### Scaling

- [ ] Is independent scaling valuable?

### Security

- [ ] Does the boundary represent a meaningful trust boundary?

### Deployment

- [ ] Is independent deployment valuable?

### Operations

- [ ] Can the organization operate the boundary?

### Cost

- [ ] Is the cost of crossing the boundary justified?

---

# Relationship to Other Standards

System Boundaries builds upon:

- Architecture Philosophy
- Architecture Principles
- Engineering Quality Attributes
- Engineering Constraints
- Trade-off Analysis

It provides the foundation for subsequent architecture decisions involving:

- component boundaries,
- data architecture,
- communication,
- consistency,
- failure domains,
- scalability,
- architectural patterns.

---

# References

Boundary-oriented architecture draws upon established software architecture concepts including cohesion, coupling, information hiding, domain boundaries, distributed systems design, and organizational architecture.

Specific architectural boundaries should always be evaluated against the business and engineering context of the system.

---

# Revision History

| Version | Date | Summary |
|---------|------|---------|
| 1.0 | YYYY-MM-DD | Initial version |

---

# Final Principle

> **A boundary should create meaningful independence. If separating two responsibilities does not improve ownership, changeability, scalability, security, or failure isolation enough to justify the cost, the boundary may be solving the wrong problem.**
