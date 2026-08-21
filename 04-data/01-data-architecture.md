# Data Architecture

> Data is an architectural responsibility. Every significant dataset should have clear ownership, defined access, understood consistency requirements, controlled lifecycle, and an explicit strategy for sharing and evolution.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Architecture

**Applies To:** Every software system that creates, stores, processes, or consumes data

---

# Purpose

Software systems are ultimately built around information.

Applications create data.

Services transform data.

Events distribute data.

Databases persist data.

Reports consume data.

AI and ML systems depend upon data.

As systems grow, data becomes one of the strongest sources of architectural coupling.

This standard establishes the baseline principles for designing, owning, sharing, protecting, evolving, and retiring data.

---

# Why This Standard Exists

Many architectural problems that appear to be application problems are actually data problems.

Examples include:

- multiple components modifying the same data,
- unclear ownership of authoritative information,
- inconsistent copies of data,
- tightly coupled database schemas,
- uncontrolled data replication,
- incompatible schema changes,
- inability to delete data,
- unclear retention responsibilities,
- pipelines depending on undocumented assumptions.

A system can have beautifully separated services while remaining tightly coupled through its data.

Therefore:

> **Data architecture must be considered alongside application architecture.**

---

# Engineering Principle

> **Every important dataset must have an explicit owner, authoritative source, access model, consistency expectation, lifecycle, and evolution strategy.**

---

# Data Is More Than Storage

Data architecture includes more than choosing a database.

It includes:

- data ownership,
- data modeling,
- storage,
- access,
- consistency,
- concurrency,
- replication,
- caching,
- integration,
- governance,
- security,
- retention,
- deletion,
- archival,
- recovery,
- evolution.

The database technology is only one part of the decision.

---

# 1. Establish Data Ownership

Every significant dataset should have an identifiable owner.

Ownership should answer:

- Who defines the meaning of the data?
- Who may modify it?
- Who is responsible for its correctness?
- Who is responsible for its security?
- Who defines its lifecycle?
- Who handles data-quality problems?

Ownership should not be inferred from where the data happens to be stored.

---

# 2. Identify the Authoritative Source

For every important piece of business information, identify the authoritative source.

For example:

```text
Customer Profile
      │
      ▼
Customer System
      │
      ├──► Order System
      ├──► Support System
      └──► Analytics Platform
