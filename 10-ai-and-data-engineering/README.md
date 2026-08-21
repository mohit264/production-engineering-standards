# AI and Data Engineering

> AI and data engineering turn data and computational capabilities into analytical, machine-learning, and AI-driven systems that can be built, evaluated, deployed, and operated reliably.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** AI and Data Engineering

---

# Purpose

The organization already has a data architecture.

That architecture establishes the foundations for:

* data ownership,
* data storage,
* data movement,
* data contracts,
* data governance,
* data lifecycle.

But having data available does not automatically create intelligence.

A second engineering problem emerges:

> **How do we reliably transform data into analytical, machine-learning, and AI capabilities that behave predictably in production?**

This domain addresses that problem.

---

# Engineering Principle

> **AI and data capabilities should be engineered as production systems, not treated as experiments that happen to reach production.**

This means considering:

```text
Data
  +
Computation
  +
Models
  +
Evaluation
  +
Serving
  +
Observability
  +
Governance
```

as one engineering lifecycle.

---

# 1. Relationship With Data Architecture

This domain does not replace `04-data`.

The distinction is important.

```text
04-data
   │
   ├── What data exists?
   ├── Where does it live?
   ├── Who owns it?
   ├── How does it move?
   └── What guarantees does it provide?
             │
             ▼
10-ai-and-data-engineering
   │
   ├── How is data transformed?
   ├── How is it used for analytics?
   ├── How are models built?
   ├── How are AI systems evaluated?
   ├── How are models served?
   └── How are AI systems operated?
```

The first establishes the data foundation.

The second turns that foundation into computational capabilities.

---

# 2. The Fundamental Problem

Suppose an organization has excellent data.

It may still fail to produce a useful AI system.

Why?

Because a production AI capability requires much more than data.

It requires:

```text
Reliable data
      +
Appropriate computation
      +
A useful model
      +
Evaluation
      +
Serving
      +
Operational feedback
```

The engineering challenge is therefore not simply:

> "Can we train a model?"

It is:

> **Can we repeatedly produce a useful computational capability whose behavior remains measurable and controllable in production?**

---

# 3. Data Engineering

Data engineering transforms raw or operational data into forms suitable for downstream use.

Typical responsibilities include:

```text
Ingestion
Transformation
Validation
Enrichment
Aggregation
Scheduling
Lineage
Quality
```

The implementation may vary.

The engineering responsibility remains the same:

> **Make data reliably available in the form required by downstream consumers.**

---

# 4. Data Pipelines

A pipeline represents a repeatable transformation process.

Conceptually:

```text
Source Data
    │
    ▼
Ingestion
    │
    ▼
Transformation
    │
    ▼
Validation
    │
    ▼
Curated Data
```

The pipeline should make its assumptions explicit.

---

# 5. Batch and Streaming

Data processing generally falls somewhere along a spectrum between:

```text
Batch
```

and:

```text
Continuous / Streaming
```

The choice should follow the business requirement.

For example:

```text
Daily financial reporting
        → batch may be sufficient

Fraud detection
        → near-real-time processing may be required
```

Real-time processing should not be introduced merely because it is technically attractive.

---

# 6. Data Quality

AI and analytical systems inherit weaknesses in their inputs.

Poor data can produce:

```text
Incorrect analysis
Incorrect predictions
Incorrect recommendations
Incorrect decisions
```

Data quality therefore becomes part of AI system reliability.

Important dimensions may include:

```text
Accuracy
Completeness
Consistency
Timeliness
Validity
Uniqueness
```

---

# 7. Data Validation

Important pipelines should validate assumptions about incoming data.

For example:

```text
Expected:
customer_id exists

Observed:
customer_id missing
```

The system must define what happens next.

Possible outcomes include:

```text
Reject
Quarantine
Repair
Continue with explicit degradation
```

The appropriate behavior depends on the data contract.

---

# 8. Data Lineage

When an analytical result or model prediction is questioned, engineers may need to determine:

> **Where did this data come from?**

Lineage connects:

```text
Source
  │
  ▼
Transformation
  │
  ▼
Dataset
  │
  ▼
Model / Analysis
  │
  ▼
Output
```

Lineage improves debugging, governance, and reproducibility.

---

# 9. Reproducibility

A model or analysis should ideally be reproducible from known inputs and known computational conditions.

Relevant factors may include:

```text
Dataset version
Code version
Configuration
Dependencies
Model version
Training parameters
Randomness
```

Without reproducibility, investigating a changed result becomes difficult.

---

# 10. Feature Engineering

Machine-learning systems often transform raw data into representations useful for prediction.

For example:

```text
Raw events
     │
     ▼
Derived features
     │
     ▼
Model
```

Features should have:

* defined semantics,
* known ownership,
* appropriate freshness,
* controlled lifecycle.

---

# 11. Training Data

Training data is not merely another dataset.

It directly influences model behavior.

Therefore teams should understand:

```text
Where did the data come from?

When was it collected?

How was it transformed?

What population does it represent?

What assumptions exist?

What biases may exist?
```

Training data should be treated as a governed engineering artifact.

---

# 12. Training and Serving

Training and production serving have different requirements.

```text
Training
 ├── Large computation
 ├── Historical data
 ├── Experimentation
 └── Iteration

Serving
 ├── Predictable latency
 ├── Availability
 ├── Current inputs
 └── Operational reliability
```

The two environments should therefore not be assumed to have identical architecture.

---

# 13. Machine Learning as an Engineering Lifecycle

A production ML capability can be viewed as:

```text
Problem
  │
  ▼
Data
  │
  ▼
Experiment
  │
  ▼
Training
  │
  ▼
Evaluation
  │
  ▼
Deployment
  │
  ▼
Serving
  │
  ▼
Monitoring
  │
  ▼
Feedback
  │
  └──────────► Retraining / Improvement
```

The model is only one component of the lifecycle.

---

# 14. Model Evaluation

A model should not be promoted merely because it produces predictions.

The important question is:

> **Does it perform adequately for the intended use case?**

Evaluation may consider:

```text
Accuracy
Precision
Recall
Latency
Cost
Robustness
Fairness
Safety
Domain-specific outcomes
```

The correct evaluation criteria depend on the problem.

---

# 15. Offline Evaluation

Before production deployment, models can be evaluated against known datasets.

This allows controlled comparison between:

```text
Model A
Model B
Model C
```

Offline evaluation is useful but insufficient by itself.

A model can perform well on historical data and still behave poorly in production.

---

# 16. Online Evaluation

Production behavior provides additional evidence.

Relevant signals may include:

```text
Prediction quality
User outcomes
Latency
Error rate
Cost
Drift
```

Where ground truth is available, production outcomes should feed back into evaluation.

---

# 17. Model Versioning

Models should be identifiable.

For example:

```text
Model v1
Model v2
Model v3
```

This allows teams to determine:

```text
Which model produced this result?
```

Model versioning should connect with the broader software and data lifecycle.

---

# 18. Model Registry

Organizations may maintain a controlled inventory of model artifacts.

A model registry can capture information such as:

```text
Model version
Training data reference
Evaluation results
Owner
Deployment status
Approval status
```

The implementation is optional.

The governance requirement is traceability.

---

# 19. Model Serving

A trained model becomes useful only when consumers can invoke it.

Serving may expose capabilities through:

```text
API
Batch processing
Streaming
Embedded inference
Edge deployment
```

The serving architecture should reflect the workload.

---

# 20. Inference

Inference is the process of using a trained model to produce an output.

Conceptually:

```text
Input
  │
  ▼
Preprocessing
  │
  ▼
Model
  │
  ▼
Postprocessing
  │
  ▼
Prediction / Response
```

Every stage can affect system behavior.

---

# 21. Inference Reliability

Production inference should be treated like any other production dependency.

It requires consideration of:

```text
Availability
Latency
Capacity
Failure handling
Timeouts
Resource limits
Cost
```

AI does not remove ordinary distributed-system concerns.

---

# 22. Generative AI

Generative AI introduces additional system components.

A production generative AI system may involve:

```text
User Input
    │
    ▼
Context Construction
    │
    ├── Retrieved Information
    ├── Conversation State
    └── Application Data
    │
    ▼
Model
    │
    ▼
Generated Output
    │
    ▼
Validation / Policy
    │
    ▼
Application Response
```

The model is therefore only one part of the system.

---

# 23. Retrieval-Augmented Systems

When external knowledge is required, an AI system may retrieve relevant information before generation.

Conceptually:

```text
Query
  │
  ▼
Retrieval
  │
  ▼
Relevant Context
  │
  ▼
Model
  │
  ▼
Response
```

The retrieval system introduces its own engineering concerns:

```text
Index freshness
Retrieval quality
Latency
Access control
Data lineage
```

---

# 24. Context Is an Engineering Resource

AI systems often depend on context.

Context can come from:

```text
Documents
Databases
APIs
Conversation history
Application state
Tools
```

Context must be selected, transformed, and governed.

More context is not automatically better.

---

# 25. Tool-Using AI Systems

Some AI systems can invoke external tools.

Conceptually:

```text
Model
  │
  ├── Tool A
  ├── Tool B
  └── Tool C
```

This introduces a new reliability boundary.

The system must control:

```text
Which tools are available?

What arguments are permitted?

What authority does the tool have?

What happens when the tool fails?

How is the action audited?
```

---

# 26. AI Is a Distributed System

A production AI application may depend on:

```text
Application
Model
Data
Retrieval
Vector index
External APIs
Tools
Identity
Observability
```

Each dependency introduces:

* latency,
* failure modes,
* security boundaries,
* cost,
* operational complexity.

AI architecture should therefore use the same systems-thinking principles applied to other distributed systems.

---

# 27. Evaluation Is a First-Class Capability

Traditional software often has relatively deterministic expected behavior.

Generative systems can produce multiple valid outputs.

Therefore evaluation becomes more complex.

Evaluation may need to consider:

```text
Correctness
Relevance
Grounding
Safety
Consistency
Instruction adherence
Latency
Cost
```

The evaluation strategy should be designed alongside the system rather than added after deployment.

---

# 28. AI Safety

AI systems can produce harmful or incorrect outputs.

Engineering controls may include:

```text
Input validation
Output validation
Policy enforcement
Access control
Tool restrictions
Human review
Auditability
```

The appropriate controls depend on the system's risk.

---

# 29. Human-in-the-Loop

Some AI decisions should not be fully automated.

For higher-risk operations, a human may need to review the output before action.

Conceptually:

```text
AI Output
    │
    ▼
Risk Assessment
    │
    ├── Low risk → Automated path
    │
    └── High risk → Human review
```

The boundary should be explicit.

---

# 30. AI Observability

AI systems require both traditional system telemetry and AI-specific signals.

Traditional signals include:

```text
Latency
Availability
Errors
Resource usage
```

AI-specific signals may include:

```text
Token usage
Model latency
Retrieval quality
Model response characteristics
Evaluation scores
Safety violations
```

AI observability should connect with `08-observability/`.

---

# 31. AI Cost

AI systems can have unusual cost structures.

For example:

```text
Model inference
Token consumption
GPU time
Embedding generation
Retrieval infrastructure
External API calls
Storage
```

Cost should therefore be treated as an architectural signal.

A technically successful AI system may still be economically unsustainable.

---

# 32. Model Drift

Production behavior can change because the environment changes.

Examples include:

```text
User behavior changes
Data distribution changes
Business rules change
External conditions change
```

A model that was effective during training may degrade over time.

This creates the need for ongoing evaluation.

---

# 33. Data Drift

The input distribution itself may change.

For example:

```text
Training:
90% customer type A

Production:
40% customer type A
60% customer type B
```

Such changes may affect model behavior.

Data drift should therefore be considered when evaluating production models.

---

# 34. Concept Drift

The relationship between inputs and outcomes may change.

For example:

```text
Historical relationship
        ↓
Business environment changes
        ↓
Relationship changes
```

A model may therefore degrade even when the input format remains valid.

---

# 35. Retraining

Retraining should not automatically occur whenever a metric changes.

A controlled retraining process should establish:

```text
Why retrain?

What data should be used?

How will the new model be evaluated?

What constitutes improvement?

How will deployment occur?

How can we roll back?
```

Retraining is an engineering change.

---

# 36. Model Rollback

A model deployment should have a recovery strategy.

For example:

```text
Model v2
   │
   ▼
Production
   │
   ▼
Unexpected degradation
   │
   ▼
Model v1
```

Rollback should be considered before deployment, not invented during an incident.

---

# 37. AI Governance

AI systems may require governance around:

```text
Data usage
Model provenance
Access
Safety
Evaluation
Human oversight
Auditability
Regulatory requirements
```

The degree of governance should correspond to system risk.

---

# 38. Reproducibility Across AI Systems

For important AI outputs, teams may need to identify:

```text
Model version
Prompt / instruction configuration
Retrieved context
Tool calls
Relevant data version
Application version
```

The exact requirements depend on the system.

The underlying principle is:

> **Important AI behavior should be explainable in terms of the system configuration that produced it.**

---

# 39. AI Supply Chain

AI systems may depend on externally sourced components:

```text
Models
Datasets
Libraries
Embeddings
Tools
Third-party APIs
```

These dependencies introduce supply-chain risks.

Teams should understand:

```text
Origin
Version
License
Security
Behavior
Trust boundary
```

where relevant.

---

# 40. Production Readiness

An AI capability should not be considered production-ready merely because:

```text
The model works.
```

Production readiness should consider:

```text
Data
Evaluation
Serving
Security
Observability
Cost
Failure handling
Ownership
Rollback
Governance
```

---

# 41. Build vs Buy

AI capabilities can be:

```text
Built internally
Consumed from a managed service
Consumed from an external model provider
Combined from multiple services
```

The decision should consider:

```text
Capability
Cost
Control
Latency
Security
Data requirements
Operational burden
Vendor dependency
```

There is no universal answer.

---

# 42. Avoid AI as an Architectural Exception

AI systems should not bypass normal engineering disciplines.

They still require:

```text
Identity
Security
Reliability
Testing
Observability
Cost management
Change management
Ownership
```

AI introduces additional concerns.

It does not remove existing ones.

---

# 43. Minimum Engineering Requirements

Every production AI or ML capability should:

* [ ] Define the business or engineering problem it solves.
* [ ] Identify the data dependencies.
* [ ] Establish data-quality expectations.
* [ ] Version important models and configurations.
* [ ] Define evaluation criteria before production use.
* [ ] Establish production ownership.
* [ ] Define serving reliability expectations.
* [ ] Monitor relevant system behavior.
* [ ] Monitor relevant model or AI behavior.
* [ ] Define failure and fallback behavior.
* [ ] Define rollback or model replacement strategy.
* [ ] Consider security and access boundaries.
* [ ] Consider cost.
* [ ] Define appropriate governance for the system's risk.

Higher-risk or higher-maturity systems may additionally require:

* [ ] Formal model evaluation pipelines.
* [ ] Model registries.
* [ ] Dataset lineage.
* [ ] Automated drift detection.
* [ ] Continuous evaluation.
* [ ] Human-in-the-loop controls.
* [ ] AI safety testing.
* [ ] Model supply-chain controls.
* [ ] Formal model approval processes.
* [ ] Automated rollback.
* [ ] AI-specific cost attribution.

---

# Relationship With Other Standards

This standard connects directly with:

* `04-data/`
* `06-security/`
* `07-delivery/`
* `08-observability/`
* `09-platform-and-infrastructure/`
* `11-operational-readiness/`

It should be read as the engineering layer that turns the organization's data and computational foundations into analytical, ML, and AI capabilities.

---

# What This Standard Is Not

This standard does not prescribe:

* a particular ML framework,
* a particular LLM,
* a particular cloud provider,
* a particular vector database,
* a particular orchestration platform,
* a particular MLOps platform,
* a particular AI provider.

Those are architecture and implementation decisions.

The engineering contract is:

> **AI and data capabilities must be treated as production engineering systems with explicit data dependencies, evaluation criteria, operational ownership, reliability controls, observability, security, cost awareness, and lifecycle management.**

---

# Final Principle

> **A model is not a production system. An AI capability becomes an engineering system only when data, computation, models, evaluation, serving, safety, observability, and operational ownership are designed as one lifecycle.**
