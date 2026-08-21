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
```

to:

```text
Network Call
```

changes the failure model.

Changing:

```text
Synchronous Request
```

to:

```text
Asynchronous Event
```

changes the temporal relationship between components.

Changing:

```text
Direct Dependency
```

to:

```text
Message Broker
```

introduces new infrastructure and operational responsibilities.

Therefore:

> **Communication style is an architectural decision.**

---

# Engineering Principle

> **Choose the simplest communication mechanism that satisfies the required business semantics, performance, reliability, coupling, and operational characteristics.**

---

# Communication Is a Contract

Communication between components should define expectations around:

- meaning,
- inputs,
- outputs,
- errors,
- timing,
- availability,
- compatibility,
- security,
- ownership.

The protocol is only one part of the contract.

---

# Communication Models

The primary communication models are:

```text
Synchronous
    │
    ├── Request / Response
    │
    └── Direct Invocation

Asynchronous
    │
    ├── Queue
    │
    ├── Event
    │
    └── Stream
```

These models solve different problems.

---

# 1. In-Process Communication

The simplest communication mechanism is often a direct call within the same process.

```text
┌─────────────────────────────────────┐
│ Application                         │
│                                     │
│ Component A ─────► Component B      │
│                                     │
└─────────────────────────────────────┘
```

Advantages may include:

- low latency,
- simple debugging,
- simple failure handling,
- shared execution context.

This can be appropriate when components do not require independent deployment or scaling.

---

# 2. Synchronous Request / Response

A synchronous interaction means the caller waits for a response.

```text
Client
  │
  │ Request
  ▼
Service
  │
  │ Response
  ▼
Client
```

Useful when:

- the caller needs an immediate result,
- the operation is naturally request/response,
- the latency requirement is compatible with synchronous execution.

Examples include:

- retrieving customer information,
- validating a request,
- submitting an operation requiring immediate acknowledgement.

---

# Synchronous Communication Creates Temporal Coupling

With synchronous communication:

```text
A ─────► B
```

A depends upon B being able to respond.

If B is:

- unavailable,
- slow,
- overloaded,

A may also become:

- slow,
- unavailable,
- overloaded.

This creates temporal coupling.

Therefore synchronous dependencies should be evaluated as potential failure propagation paths.

---

# 3. Asynchronous Communication

Asynchronous communication allows the sender and receiver to operate at different times.

```text
Producer
    │
    ▼
Message Infrastructure
    │
    ▼
Consumer
```

Useful when:

- immediate response is unnecessary,
- work can happen later,
- workloads should be decoupled,
- buffering is valuable,
- consumers may process at different rates.

---

# Asynchronous Communication Creates Temporal Decoupling

The producer does not necessarily require the consumer to be immediately available.

This can provide:

- buffering,
- workload smoothing,
- independent processing,
- improved resilience.

However, asynchronous communication introduces additional complexity.

---

# 4. Queues

A queue generally represents work that needs to be processed.

Conceptually:

```text
Producer
    │
    ▼
┌──────────────┐
│ Work Queue   │
└──────┬───────┘
       │
       ▼
    Consumer
```

A queue is appropriate when the semantic meaning is:

> "This work needs to be performed."

Examples:

- process an image,
- generate an invoice,
- send a notification,
- execute a background job.

---

# 5. Events

An event represents a fact that has already happened.

For example:

> `OrderPlaced`

The producer is communicating:

> "This happened."

rather than:

> "Please do this."

This distinction matters.

Events can enable multiple consumers to react independently.

```text
                 ┌──► Consumer A
                 │
Producer ─► Event├──► Consumer B
                 │
                 └──► Consumer C
```

---

# Commands vs Events

The semantic distinction should be explicit.

## Command

A request for an action.

Examples:

- `CreateOrder`
- `ProcessPayment`
- `GenerateInvoice`

## Event

A statement of fact.

Examples:

- `OrderCreated`
- `PaymentProcessed`
- `InvoiceGenerated`

Confusing commands and events can create unclear ownership and unintended coupling.

---

# 6. Publish / Subscribe

Publish/subscribe allows multiple consumers to receive a message or event.

Useful when consumers should be able to evolve independently.

However, adding consumers increases the producer's indirect impact.

Therefore the event contract becomes an important architectural boundary.

---

# 7. Streaming

Streaming is appropriate when data arrives continuously or when consumers need ordered sequences of records over time.

Examples include:

- telemetry,
- transaction streams,
- click streams,
- operational events.

Streaming systems introduce additional concerns around:

- ordering,
- partitioning,
- replay,
- retention,
- consumer progress,
- backpressure.

Streaming should not be introduced merely because a message broker is already available.

---

# 8. Communication Direction

Architecture should make communication direction explicit.

For example:

```text
Ordering
   │
   ▼
Payment
```

means Ordering depends on Payment.

Bidirectional dependencies should be treated carefully:

```text
Ordering ─────► Payment
    ▲              │
    └──────────────┘
```

Cycles can make:

- deployment,
- testing,
- reasoning,
- failure analysis

more difficult.

---

# 9. Avoid Chatty Communication

A boundary becomes problematic when a single business operation requires excessive communication.

For example:

```text
A
│
├──► B
│     └──► C
│           └──► D
│
└──► E
```

Potential consequences include:

- increased latency,
- more failure points,
- complex debugging,
- increased infrastructure load.

Prefer communication that reflects meaningful business operations rather than exposing internal implementation details.

---

# 10. Communication and Failure

Every remote communication can fail.

Failure modes include:

- timeout,
- connection failure,
- server failure,
- overload,
- malformed response,
- duplicate request,
- delayed response,
- partial success.

Architectural communication design should explicitly consider these cases.

---

# 11. Timeouts

Remote calls should have explicit timeout expectations.

An indefinite wait can consume:

- threads,
- connections,
- memory,
- request capacity.

Timeout values should be based on actual business and system behavior rather than arbitrary defaults.

---

# 12. Retries

Retries can improve resilience against transient failures.

But retries can also amplify load.

For example:

```text
Caller
 │
 ├── Request ──► Service
 │
 ├── Retry ────► Service
 │
 └── Retry ────► Service
```

If many callers retry simultaneously, an overloaded service can become even more overloaded.

Retries should therefore consider:

- transient vs permanent failure,
- backoff,
- retry limits,
- jitter,
- idempotency,
- downstream capacity.

---

# 13. Idempotency

A retry can result in the same operation being received more than once.

Therefore operations that may be retried should define appropriate duplicate-handling semantics.

For example:

```text
Request
   │
   ├──► Processing
   │
   └──► Retry
          │
          ▼
      Same Operation
```

The system should determine whether duplicate execution is:

- safe,
- detectable,
- preventable,
- or compensatable.

---

# 14. Ordering

Not every communication system guarantees ordering.

Where ordering matters, explicitly define:

- what must be ordered,
- within what scope,
- how ordering is established,
- what happens when messages arrive out of order.

Do not assume global ordering unless the system explicitly provides it.

---

# 15. Delivery Semantics

Messaging architectures should explicitly understand delivery expectations.

Common conceptual models include:

- at-most-once,
- at-least-once,
- effectively-once through application design,
- exactly-once semantics where genuinely provided and required.

Teams should avoid using "exactly once" as a vague synonym for "we handle duplicates."

The actual guarantees of the communication infrastructure and application must be understood.

---

# 16. Backpressure

Consumers may process messages more slowly than producers generate them.

This creates a rate mismatch.

Architecture should define what happens when:

```text
Producer Rate > Consumer Processing Rate
```

Possible mechanisms include:

- buffering,
- throttling,
- load shedding,
- scaling consumers,
- prioritization,
- rejecting work.

Backpressure is particularly important for high-volume systems.

---

# 17. Dead-Letter Handling

Messages that cannot be successfully processed may require separate handling.

A dead-letter mechanism can help isolate problematic messages.

However, dead-lettering should not become a silent place where failures disappear.

The system should define:

- why messages are dead-lettered,
- how they are monitored,
- how they are investigated,
- whether they can be replayed,
- who owns remediation.

---

# 18. Replay

Some asynchronous systems allow messages or events to be replayed.

Replay can support:

- rebuilding derived state,
- recovering consumers,
- correcting processing logic,
- backfilling data.

Replay requires careful consideration of:

- idempotency,
- event retention,
- schema compatibility,
- side effects.

---

# 19. Communication Contracts

Every important communication contract should define appropriate expectations around:

### Request / Event

- structure,
- semantics,
- identifiers,
- timestamps.

### Response

- successful result,
- errors,
- partial results.

### Compatibility

- supported versions,
- evolution strategy,
- deprecation.

### Reliability

- timeout,
- retry expectations,
- delivery semantics.

### Security

- authentication,
- authorization,
- confidentiality.

The level of formality should be proportional to risk.

---

# 20. Synchronous vs Asynchronous Decision

Use synchronous communication when:

- the caller requires an immediate answer,
- the operation is naturally interactive,
- latency is acceptable,
- dependency availability is acceptable.

Consider asynchronous communication when:

- immediate completion is unnecessary,
- work can be deferred,
- buffering is valuable,
- producer and consumer should operate independently,
- workloads have different processing rates.

Neither model is universally better.

---

# 21. Communication Decision Matrix

| Question | Synchronous | Asynchronous |
|----------|-------------|--------------|
| Immediate response required? | Strong fit | Poor fit |
| Temporal decoupling needed? | Poor fit | Strong fit |
| Simple request/response? | Strong fit | Usually unnecessary |
| Work can be delayed? | Possible | Strong fit |
| Buffering required? | Weak | Strong |
| Simple debugging required? | Stronger | More complex |
| Consumer availability independent? | Weak | Strong |
| Replay required? | Usually unnecessary | Often useful |

This table is guidance rather than a mandatory rule.

---

# 22. Communication and Security

Communication boundaries should explicitly consider:

- authentication,
- authorization,
- encryption,
- identity propagation,
- credential management,
- trust boundaries.

Internal network communication should not automatically be treated as trusted.

---

# 23. Communication and Observability

Distributed communication should be observable.

Important signals may include:

- request count,
- latency,
- errors,
- timeouts,
- retries,
- queue depth,
- consumer lag,
- dropped messages.

Correlation identifiers should be considered where they materially improve troubleshooting.

---

# 24. Communication and Capacity

Communication infrastructure has finite capacity.

Consider:

- requests per second,
- message rates,
- payload size,
- connection limits,
- queue depth,
- consumer throughput,
- network bandwidth.

Capacity assumptions should be documented for critical communication paths.

---

# 25. Communication and Data Ownership

Communication should not accidentally transfer data ownership.

For example:

```text
Customer System
      │
      │ Customer Information
      ▼
Ordering System
```

Receiving customer information does not automatically make Ordering the owner of that information.

The architecture should distinguish:

- authoritative data,
- copied data,
- derived data.

---

# 26. Communication and Consistency

Communication style influences consistency.

Synchronous interactions can make immediate coordination easier.

Asynchronous interactions naturally introduce temporal separation.

When asynchronous communication is used, explicitly determine:

- what may become temporarily stale,
- how long staleness is acceptable,
- how correctness is maintained,
- how failures are reconciled.

---

# 27. Avoid Communication for Accidental Abstraction

A component should not become a network service merely to enforce conceptual separation.

If two components:

- always deploy together,
- always scale together,
- always fail together,
- always change together,

introducing a network boundary may create complexity without meaningful independence.

---

# 28. Avoid Infrastructure-Driven Architecture

Do not select communication patterns merely because an organization already has:

- a message broker,
- an API gateway,
- a streaming platform,
- a service mesh.

Infrastructure availability does not establish architectural necessity.

Business and system requirements should drive communication choices.

---

# 29. Communication Failure Checklist

For every important remote dependency, answer:

- [ ] What happens if the dependency is unavailable?
- [ ] What happens if it is slow?
- [ ] What happens if the request times out?
- [ ] Can the request safely be retried?
- [ ] Can duplicate processing occur?
- [ ] What happens when responses arrive late?
- [ ] What happens when messages arrive out of order?
- [ ] What happens when processing permanently fails?
- [ ] Can work be replayed?
- [ ] How is the failure observed?
- [ ] Who owns remediation?

---

# Communication Review Checklist

Before approving a significant communication mechanism:

### Semantics

- [ ] Is the interaction a command, query, event, or stream?
- [ ] Is the communication model appropriate?

### Coupling

- [ ] Does the interaction create temporal coupling?
- [ ] Does it create deployment or schema coupling?

### Failure

- [ ] Are timeout and failure behaviors understood?
- [ ] Are retries safe?
- [ ] Is idempotency addressed where required?

### Performance

- [ ] Are latency requirements understood?
- [ ] Are throughput requirements understood?
- [ ] Is backpressure addressed where necessary?

### Messaging

Where applicable:

- [ ] Are delivery semantics understood?
- [ ] Is ordering understood?
- [ ] Is replay behavior defined?
- [ ] Is dead-letter handling defined?

### Security

- [ ] Are trust boundaries understood?
- [ ] Is authentication defined?
- [ ] Is authorization defined?

### Operations

- [ ] Is communication observable?
- [ ] Can failures be diagnosed?
- [ ] Can the communication infrastructure be operated reliably?

### Economics

- [ ] Is the infrastructure and operational complexity justified?

---

# Relationship to Other Standards

Communication Patterns builds upon:

- System Boundaries
- Component Boundaries
- Data Architecture
- Architecture Principles
- Engineering Quality Attributes
- Engineering Constraints

It provides the foundation for later decisions involving:

- consistency,
- concurrency,
- distributed transactions,
- event-driven architectures,
- resilience,
- scalability.

---

# Final Principle

> **Communication is not merely how components exchange information. It defines how they depend upon one another in time, failure, data, and operation. Choose communication mechanisms according to those dependencies—not according to architectural fashion.**
