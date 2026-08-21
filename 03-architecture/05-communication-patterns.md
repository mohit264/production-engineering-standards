# Communication Patterns

> Communication between components is an architectural dependency. The choice between synchronous and asynchronous communication should be driven by business semantics, latency requirements, failure behavior, coupling, and operational capability—not by architectural fashion.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Architecture

**Applies To:** Every system containing multiple interacting components

---

# Purpose

Components rarely operate in isolation.

They communicate to:

- request information,
- execute operations,
- notify other components,
- distribute facts,
- coordinate work,
- trigger asynchronous processing.

The communication mechanism chosen between components directly influences:

- coupling,
- latency,
- availability,
- failure propagation,
- scalability,
- observability,
- deployment independence.

This standard establishes principles for selecting and designing communication mechanisms.

---

# Why This Standard Exists

Communication is often treated as an implementation detail.

It is not.

Changing:

```text
Function Call
