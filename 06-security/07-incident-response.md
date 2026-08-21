# Security Incident Response

> Security incidents are inevitable enough that production systems must be designed not only to prevent compromise, but to recognize, contain, investigate, recover from, and learn from it.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Security Engineering

**Applies To:** All production systems, with response depth proportional to system tier, exposure, data sensitivity, and business impact

---

# Purpose

Security incident response defines how the organization responds when security controls are bypassed, compromised, or suspected to have been compromised.

The objective is to establish a predictable process for:

- detection,
- triage,
- containment,
- investigation,
- eradication,
- recovery,
- communication,
- evidence preservation,
- learning.

Incident response should not begin from scratch during an incident.

---

# Engineering Principle

> **The goal of incident response is not merely to restore service. It is to contain the security impact, preserve evidence, understand what happened, restore trustworthy operation, and reduce the probability of recurrence.**

---

# 1. What Is a Security Incident?

A security incident is an event that may compromise:

- confidentiality,
- integrity,
- availability,
- identity,
- authorization,
- sensitive data,
- security controls.

Examples include:

- compromised credentials,
- unauthorized access,
- malware,
- data exposure,
- privilege escalation,
- compromised dependencies,
- malicious artifacts,
- unauthorized infrastructure changes.

Not every security event is an incident.

---

# 2. Event vs Alert vs Incident

These concepts should remain distinct.

```text
Event
  │
  ▼
Detection / Alert
  │
  ▼
Investigation
  │
  ▼
Incident
```

An event is something that happened.

An alert is a signal that something may require attention.

An incident is a confirmed or sufficiently suspected security-impacting situation requiring coordinated response.

---

# 3. Incident Severity

Incidents should be classified according to impact.

Factors may include:

- affected systems,
- affected users,
- data sensitivity,
- privilege obtained,
- duration,
- scope,
- business impact,
- regulatory implications.

Severity should determine response urgency and escalation.

---

# 4. Incident Ownership

Every security incident should have clear ownership.

The response should establish:

- incident commander,
- technical responders,
- security responders,
- business stakeholders,
- communications owner where required.

Responsibility should be explicit.

---

# 5. Detection

Incidents may originate from:

- security monitoring,
- application alerts,
- infrastructure alerts,
- vulnerability findings,
- employees,
- customers,
- external researchers,
- third parties.

All credible signals should have a defined path for investigation.

---

# 6. Initial Triage

The first objective is to establish:

- what happened,
- which system is affected,
- whether the activity is ongoing,
- what evidence exists,
- whether the incident is expanding.

Initial triage should avoid premature conclusions.

---

# 7. Preserve Evidence

Incident response should preserve relevant evidence before it disappears or is modified.

Potential evidence includes:

- logs,
- authentication records,
- audit events,
- network telemetry,
- application traces,
- system state,
- container metadata,
- deployment records,
- source-control history.

Evidence handling should follow the organization's requirements.

---

# 8. Do Not Destroy Evidence Accidentally

Containment actions can alter evidence.

For example:

```text
Compromised Host
      │
      ├── Immediate Shutdown
      │
      └── Preserve State
```

The correct action depends on the incident.

Responders should consider investigative impact before taking destructive actions when practical.

---

# 9. Establish Scope

Determine whether the incident is isolated or systemic.

Questions include:

- Which identities are affected?
- Which hosts are affected?
- Which workloads are affected?
- Which tenants are affected?
- Which data may have been accessed?
- Which credentials may have been exposed?

Scope should be continuously revised as evidence improves.

---

# 10. Containment

Containment aims to prevent further damage.

Possible actions include:

- revoke credentials,
- isolate workloads,
- block network paths,
- disable compromised accounts,
- quarantine artifacts,
- stop affected processes,
- restrict access.

Containment should be proportional to the threat and business impact.

---

# 11. Credential Compromise

When credentials are suspected to be compromised, response may include:

```text
Identify Credential
       │
       ▼
Revoke / Disable
       │
       ▼
Rotate
       │
       ▼
Identify Usage
       │
       ▼
Investigate
```

Rotation alone is insufficient if the attacker can continue using another valid credential.

---

# 12. Privileged Credential Compromise

Compromise of privileged credentials requires stronger response.

Potential actions include:

- immediate revocation,
- emergency credential rotation,
- review of privileged actions,
- investigation of persistence,
- review of newly created identities,
- review of policy changes.

The blast radius may extend beyond the initially observed system.

---

# 13. Containment vs Availability

Containment can affect production availability.

For example:

```text
Isolate System
      │
      ▼
Security Risk ↓
      +
Availability Risk ↑
```

The response team should explicitly consider both.

The safest action from a security perspective may not always be the safest action for the business.

---

# 14. Eradication

After containment, remove the cause of compromise.

Examples include:

- removing malicious code,
- replacing compromised dependencies,
- rebuilding artifacts,
- removing unauthorized identities,
- fixing vulnerable configurations,
- closing exploited attack paths.

Simply restoring service without removing the cause is insufficient.

---

# 15. Rebuild From Known-Good State

When compromise affects software artifacts or infrastructure, rebuilding from trusted inputs may be preferable to attempting to clean a potentially compromised environment.

For example:

```text
Trusted Source
      │
      ▼
Trusted Build
      │
      ▼
Known-Good Artifact
      │
      ▼
Redeploy
```

This is particularly important when system integrity is uncertain.

---

# 16. Recovery

Recovery restores normal operation after containment and eradication.

Recovery should verify:

- security controls,
- identities,
- configuration,
- artifacts,
- dependencies,
- monitoring,
- system behavior.

Recovery is not complete merely because the service is responding again.

---

# 17. Verification Before Restoration

Before returning an affected system to normal operation, establish reasonable confidence that:

- the vulnerability is addressed,
- malicious access is removed,
- compromised credentials are revoked,
- artifacts are trustworthy,
- monitoring is functioning.

---

# 18. Persistence

Attackers may establish mechanisms that survive the initial response.

Investigation should therefore consider:

- new accounts,
- altered permissions,
- scheduled tasks,
- modified workloads,
- unauthorized automation,
- persistent credentials,
- altered deployment pipelines.

---

# 19. Lateral Movement

If an attacker obtained access to one system, determine whether they accessed others.

Investigate:

- service-to-service calls,
- credential reuse,
- administrative access,
- network connections,
- identity changes.

The initially compromised component may not be the final target.

---

# 20. Data Exposure

When sensitive data may have been accessed, determine:

- which data,
- which records,
- which users,
- when access occurred,
- whether data was copied,
- whether exposure continues.

Do not assume that:

```text
Unauthorized Access
```

automatically means:

```text
Confirmed Data Exfiltration
```

The distinction should be evidence-based.

---

# 21. Data Integrity

Security incidents may affect integrity as well as confidentiality.

Investigation should consider:

- modified records,
- altered configuration,
- changed artifacts,
- manipulated transactions,
- unauthorized permissions.

Recovery may therefore require restoring known-good state rather than merely restarting services.

---

# 22. Supply Chain Incidents

If a dependency, package, container image, build tool, or artifact is compromised, identify all potentially affected systems.

The response should leverage:

- dependency inventories,
- SBOMs where available,
- artifact provenance,
- deployment records,
- image digests.

Supply-chain incidents can have a wide blast radius.

---

# 23. Build Pipeline Compromise

A compromised CI/CD system can produce trusted-looking malicious artifacts.

Response should consider:

- workflow changes,
- runner integrity,
- build credentials,
- signing keys,
- deployment credentials,
- generated artifacts.

The build system itself may need to be treated as compromised.

---

# 24. Signing-Key Compromise

If artifact-signing credentials are compromised, responders should consider:

- revoking trust,
- rotating keys,
- identifying artifacts signed by the compromised key,
- determining whether signatures can still be trusted,
- rebuilding affected artifacts.

Signing infrastructure should have a defined emergency recovery process.

---

# 25. Security Control Compromise

If an attacker disables or modifies security controls, determine:

- which controls changed,
- when they changed,
- who or what changed them,
- what activity occurred during the weakened period.

Security-policy changes can themselves be evidence of compromise.

---

# 26. Communication

Incident communication should be coordinated.

Relevant audiences may include:

- engineering,
- security,
- leadership,
- customers,
- legal,
- compliance,
- vendors,
- regulators where applicable.

Communication should be factual and evidence-based.

---

# 27. Avoid Speculation

During an incident, distinguish between:

```text
Known
```

```text
Likely
```

and:

```text
Unknown
```

Premature conclusions can lead to incorrect containment or communication decisions.

---

# 28. Incident Timeline

Maintain a timeline of significant events.

For example:

```text
10:03  Suspicious authentication detected
10:07  Account disabled
10:14  Investigation started
10:29  Additional affected workload identified
10:41  Credentials rotated
11:05  Malicious artifact identified
11:32  Known-good artifact deployed
12:10  Monitoring confirms recovery
```

A reliable timeline is valuable for both response and later analysis.

---

# 29. Decision Log

Important incident decisions should be recorded.

Examples include:

- why a system was isolated,
- why credentials were rotated,
- why service was kept online,
- why a deployment was rolled back.

This preserves the reasoning behind emergency decisions.

---

# 30. Incident Runbooks

Common incident types should have prepared runbooks.

Examples include:

- compromised credentials,
- leaked secret,
- compromised dependency,
- unauthorized access,
- data exposure,
- malicious artifact,
- infrastructure compromise.

Runbooks reduce cognitive load during high-pressure situations.

---

# 31. Runbooks Must Not Replace Judgment

Runbooks provide guidance.

They should not prevent responders from adapting to new evidence.

An incident may deviate significantly from the expected scenario.

---

# 32. Automation

Appropriate containment actions may be automated.

Examples include:

- credential revocation,
- account disabling,
- artifact quarantine,
- workload isolation.

Automation should be used carefully for actions with significant blast radius.

---

# 33. Automation Safety

Automated response should consider false positives.

An incorrect automated action could:

- disable legitimate customers,
- remove critical infrastructure,
- cause an outage,
- destroy evidence.

High-impact automation should therefore have appropriate safeguards.

---

# 34. Recovery Validation

After recovery, validate:

- authentication,
- authorization,
- network controls,
- application behavior,
- data integrity,
- monitoring,
- deployment integrity.

Security recovery should be evidence-based.

---

# 35. Post-Incident Review

After significant incidents, conduct a structured review.

Questions include:

- What happened?
- Why did it happen?
- Which controls failed?
- Which controls worked?
- Why was detection possible or impossible?
- How was containment performed?
- What delayed response?
- What should change?

---

# 36. Root Cause

Root cause analysis should go beyond:

> Which line of code was vulnerable?

Consider contributing factors such as:

- architectural assumptions,
- missing controls,
- inadequate testing,
- excessive privilege,
- poor observability,
- process failures,
- dependency management,
- configuration drift.

---

# 37. Control Failure Analysis

For every significant incident, examine the security control chain:

```text
Prevent
  │
  ▼
Detect
  │
  ▼
Contain
  │
  ▼
Eradicate
  │
  ▼
Recover
```

Identify where controls:

- succeeded,
- failed,
- were absent,
- were bypassed.

---

# 38. Security Debt

Incidents often expose existing security debt.

Examples include:

- undocumented privileges,
- unmanaged credentials,
- unsupported dependencies,
- missing telemetry,
- weak isolation.

Security debt should be captured and prioritized rather than forgotten after recovery.

---

# 39. Regression Prevention

Important incident findings should produce durable improvements.

Possible outcomes include:

- code changes,
- architecture changes,
- new tests,
- new alerts,
- tighter permissions,
- new runbooks,
- improved documentation.

The incident should make the system stronger.

---

# 40. Lessons Learned

Lessons should be translated into engineering actions.

Avoid ending with:

> "Team should be more careful."

Prefer concrete improvements such as:

> "All production deployment credentials must use workload identity rather than long-lived static credentials."

---

# 41. Incident Metrics

Useful measures may include:

- time to detect,
- time to acknowledge,
- time to contain,
- time to recover,
- number of affected systems,
- recurrence rate,
- unresolved corrective actions.

Metrics should help improve response capability.

---

# 42. Incident Exercises

Response capability should be practiced.

Possible exercises include:

- tabletop scenarios,
- credential compromise simulations,
- dependency compromise simulations,
- data-exposure scenarios,
- infrastructure compromise scenarios.

Exercises expose gaps before real incidents do.

---

# 43. Recovery Exercises

Where appropriate, teams should test whether they can actually recover from:

- compromised credentials,
- corrupted artifacts,
- unavailable security dependencies,
- compromised workloads.

A documented recovery process is weaker than a demonstrated recovery capability.

---

# 44. External Notification

Some incidents may trigger external notification requirements.

The organization should determine applicable requirements with the appropriate legal, compliance, and security stakeholders.

Engineering teams should not independently make regulatory conclusions.

---

# 45. Evidence Retention

Incident evidence should be retained according to:

- legal requirements,
- regulatory requirements,
- organizational policy,
- investigative needs.

Evidence should remain appropriately protected during retention.

---

# 46. Incident Closure

An incident should not be closed merely because service has recovered.

Closure should consider:

- containment completed,
- root cause understood sufficiently,
- required remediation completed or tracked,
- monitoring restored,
- evidence preserved,
- required communication completed.

---

# 47. Security Incident Lifecycle

The overall lifecycle is:

```text
Detect
  │
  ▼
Triage
  │
  ▼
Contain
  │
  ▼
Investigate
  │
  ▼
Eradicate
  │
  ▼
Recover
  │
  ▼
Review
  │
  ▼
Improve
```

The final step matters.

Incident response should improve the system rather than simply return it to its previous state.

---

# 48. Minimum Engineering Requirements

Every production project should:

- [ ] Have a defined security incident escalation path.
- [ ] Define incident ownership.
- [ ] Define severity criteria.
- [ ] Preserve relevant security evidence.
- [ ] Have a process for credential compromise.
- [ ] Have a process for security vulnerability containment.
- [ ] Have a recovery process.
- [ ] Maintain appropriate incident timelines.
- [ ] Perform post-incident review for significant incidents.
- [ ] Track corrective actions.
- [ ] Maintain important incident runbooks.

Higher-risk systems may additionally require:

- [ ] Formal incident-response exercises.
- [ ] Tabletop exercises.
- [ ] Automated containment.
- [ ] Dedicated forensic capability.
- [ ] Supply-chain incident runbooks.
- [ ] Signing-key compromise procedures.
- [ ] Formal recovery exercises.
- [ ] Cross-functional incident command.
- [ ] Defined external communication procedures.

---

# Relationship With Other Security Standards

This standard works with:

- `06-security/README.md`
- `06-security/identity-and-access.md`
- `06-security/secrets-and-key-management.md`
- `06-security/application-security.md`
- `06-security/supply-chain-security.md`
- `06-security/security-testing.md`
- `06-security/security-monitoring.md`

It also connects directly with:

- `05-reliability/`
- `07-delivery/`
- `08-observability/`
- `09-platform-and-infrastructure/`
- `11-operational-readiness/`

---

# Final Principle

> **The real measure of security maturity is not that a system has never been attacked. It is that when something goes wrong, the organization can detect it, contain it, understand it, recover safely, and make the system harder to compromise again.**
