# Engineering Constraints

> Every engineering system is designed within constraints. Engineering excellence is not the absence of constraints, but the ability to make sound decisions while understanding, exposing, and deliberately managing them.

---

**Status:** Foundational Standard

**Version:** 1.0

**Classification:** Core Engineering

**Applies To:** Every engineering decision

---

# Purpose

Engineering decisions do not happen in a vacuum.

Every system is constrained by some combination of:

- business objectives,
- time,
- budget,
- people,
- technology,
- regulation,
- organizational structure,
- physical limitations,
- operational capability.

These constraints shape the solution space available to engineers.

The purpose of this standard is to establish constraints as explicit engineering inputs rather than treating them as obstacles discovered after architectural decisions have already been made.

---

# Why This Standard Exists

Engineering discussions often begin with:

> "What is the best architecture?"

That is usually the wrong question.

A better question is:

> "What solution best satisfies our requirements within our constraints?"

An architecture that is technically excellent but impossible to operate, unaffordable, or incompatible with regulatory requirements is not a successful engineering solution.

Constraints therefore belong at the beginning of engineering reasoning.

---

# Engineering Principle

> **A technically sound solution that violates fundamental constraints is not a sound engineering solution.**

---

# What Is an Engineering Constraint?

An engineering constraint is a condition that limits the feasible solution space.

A constraint may be:

- imposed externally,
- created by the business,
- created by existing systems,
- created by organizational capability,
- or created by physical reality.

Constraints should be distinguished from preferences.

---

# Constraint Categories

## 1. Business Constraints

Business constraints originate from organizational objectives and commercial realities.

Examples:

- budget,
- market deadlines,
- contractual commitments,
- revenue requirements,
- business continuity requirements,
- geographic availability.

Questions:

- What business deadline exists?
- What budget is available?
- What commercial commitments must be honored?

---

## 2. Product Constraints

Product requirements can constrain architecture.

Examples:

- required customer experience,
- supported workflows,
- backward compatibility,
- feature deadlines,
- customer-specific requirements.

Engineering should understand which product requirements are fixed and which are negotiable.

---

## 3. Time Constraints

Time is one of the most common engineering constraints.

Examples:

- regulatory deadlines,
- product launches,
- contractual commitments,
- migration deadlines,
- incident remediation windows.

Time constraints may justify temporary engineering decisions, but those decisions should be explicitly recorded.

---

## 4. Financial Constraints

Engineering operates within financial limits.

Examples:

- infrastructure budgets,
- licensing costs,
- engineering headcount,
- operational expenditure,
- third-party service costs.

Financial constraints should consider lifecycle cost rather than only initial expenditure.

---

## 5. Organizational Constraints

Architecture is influenced by the organization building and operating the system.

Examples:

- team size,
- team skills,
- ownership boundaries,
- support model,
- organizational structure,
- engineering maturity.

A solution that requires capabilities the organization cannot realistically provide may create operational risk.

---

## 6. Technical Constraints

Existing technology can constrain future engineering decisions.

Examples:

- legacy applications,
- existing databases,
- required protocols,
- existing APIs,
- supported platforms,
- integration requirements.

Technical constraints should be explicitly identified rather than hidden inside architectural assumptions.

---

## 7. Regulatory and Compliance Constraints

Some constraints exist outside the organization's control.

Examples:

- data residency,
- privacy requirements,
- financial regulations,
- audit requirements,
- retention obligations,
- industry-specific controls.

These constraints may directly determine architectural boundaries.

---

## 8. Security Constraints

Security requirements can restrict architectural and implementation choices.

Examples:

- identity requirements,
- encryption requirements,
- network isolation,
- privileged access restrictions,
- secrets management,
- authentication mechanisms.

Security constraints should be identified before architecture is finalized.

---

## 9. Operational Constraints

A system must be operable by the organization responsible for it.

Examples:

- support hours,
- on-call capability,
- monitoring maturity,
- deployment capability,
- incident response capability,
- disaster recovery capability.

An architecture should not require operational capabilities that the organization cannot reliably provide.

---

## 10. Physical Constraints

Some constraints are imposed by physical reality.

Examples:

- network latency,
- bandwidth,
- storage capacity,
- compute capacity,
- geographic distance,
- hardware limitations.

These constraints cannot be eliminated through better software design.

They can only be managed.

---

## 11. Dependency Constraints

Systems frequently depend upon external capabilities.

Examples:

- third-party APIs,
- identity providers,
- payment providers,
- cloud services,
- shared organizational platforms.

Dependencies introduce constraints around:

- availability,
- latency,
- contracts,
- rate limits,
- pricing,
- compatibility,
- failure behavior.

---

# Hard vs Soft Constraints

Not every constraint has equal authority.

## Hard Constraint

A condition that cannot reasonably be violated.

Examples:

- legal requirement,
- mandatory security control,
- contractual obligation.

---

## Soft Constraint

A condition that can be changed when sufficient value exists.

Examples:

- preferred technology,
- team preference,
- internal convention.

Distinguishing hard and soft constraints prevents preferences from being mistaken for engineering laws.

---

# Constraints vs Preferences

This distinction is essential.

Consider:

> "The team prefers PostgreSQL."

That is a preference.

Consider:

> "The existing platform requires PostgreSQL."

That may be a constraint.

Engineering decisions should not treat preferences as immutable requirements.

---

# Constraints Can Change

Constraints are not necessarily permanent.

Business priorities change.

Budgets change.

Organizations evolve.

Technology improves.

Regulations change.

Therefore every important constraint should have an owner and, where appropriate, a review point.

---

# Constraint Hierarchy

When constraints conflict, engineers should establish their relative authority.

A typical hierarchy may be:

```text
Legal / Regulatory
        │
        ▼
Security / Safety
        │
        ▼
Business Obligations
        │
        ▼
Customer Requirements
        │
        ▼
Operational Capability
        │
        ▼
Technical Preferences
```

This hierarchy is illustrative rather than universal.

Each organization should define its own constraint precedence according to its risk profile.

---

# Constraints and Architecture

Architecture should be designed after understanding the relevant constraints.

A useful reasoning sequence is:

```text
Business Objective
        │
        ▼
Requirements
        │
        ▼
Constraints
        │
        ▼
Quality Attributes
        │
        ▼
Candidate Architectures
        │
        ▼
Trade-off Analysis
        │
        ▼
Decision
```

Constraints narrow the solution space.

Quality attributes determine what should be optimized within that space.

---

# Constraints and Technical Debt

Constraints frequently lead to intentional technical debt.

For example:

A business deadline may require a simpler implementation today.

That can be acceptable when:

- the constraint is real,
- the decision is explicit,
- the resulting debt is documented,
- the future cost is understood.

Technical debt should not be justified by vague statements such as:

> "We didn't have time."

The actual constraint should be documented.

---

# Constraint Register

Significant projects should maintain a Constraint Register.

A useful register may contain:

| Field | Description |
|-------|-------------|
| Constraint | What limits the solution? |
| Category | Business, technical, regulatory, etc. |
| Type | Hard or soft |
| Source | Where did the constraint originate? |
| Owner | Who controls the constraint? |
| Impact | How does it affect engineering? |
| Review Date | When should it be reconsidered? |
| Status | Active, changed, or removed |

The register makes hidden assumptions visible.

---

# Common Anti-Patterns

Avoid:

- Treating preferences as hard constraints.
- Discovering critical constraints after architecture is selected.
- Ignoring organizational capability.
- Assuming constraints are permanent.
- Designing systems that exceed operational capability.
- Using "best practice" as a reason to ignore legitimate business constraints.
- Allowing temporary constraints to become permanent without review.

---

# Review Checklist

Before making a significant engineering decision, verify:

- [ ] Business constraints are understood.
- [ ] Financial constraints are understood.
- [ ] Time constraints are understood.
- [ ] Organizational constraints are understood.
- [ ] Technical constraints are documented.
- [ ] Security constraints are identified.
- [ ] Regulatory constraints are identified.
- [ ] Operational constraints are understood.
- [ ] Hard and soft constraints are distinguished.
- [ ] Preferences are not being treated as constraints.
- [ ] Important constraints have owners.
- [ ] Constraints that may change have review points.

---

# Relationship to Other Standards

Engineering Constraints builds upon:

- Engineering Values
- Engineering Principles
- Systems Thinking

It provides the constraints within which:

- Engineering Quality Attributes are prioritized,
- Trade-offs are evaluated,
- Engineering Decisions are made,
- Architecture is designed.

The resulting relationship is:

```text
Values
   │
   ▼
Principles
   │
   ▼
Systems Thinking
   │
   ▼
Constraints ─────────┐
   │                 │
   ▼                 │
Quality Attributes   │
   │                 │
   ▼                 │
Trade-offs ◄─────────┘
   │
   ▼
Decision
   │
   ▼
Architecture
```

---

# References

Constraints are a fundamental concept in systems engineering, software architecture, product development, and engineering management.

Organizations should adapt constraint categories and precedence according to their business, regulatory, security, and operational context.

---

# Revision History

| Version | Date | Summary |
|---------|------|---------|
| 1.0 | YYYY-MM-DD | Initial version |

---

# Final Principle

> **Engineering does not happen in an unconstrained world. Great engineering begins by making constraints visible, distinguishing requirements from preferences, and designing deliberately within the reality that exists.**
