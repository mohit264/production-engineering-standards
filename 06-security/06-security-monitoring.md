# Security Monitoring

> Security controls prevent and restrict. Security monitoring tells us when something unusual, dangerous, or unexpected is happening.

---

**Status:** Engineering Standard

**Version:** 1.0

**Classification:** Security Engineering

**Applies To:** All production systems, with monitoring depth proportional to system tier, exposure, data sensitivity, and threat profile

---

# Purpose

Security monitoring provides visibility into security-relevant activity occurring within production systems.

It should help answer:

- Who accessed the system?
- What did they attempt to do?
- Was the operation allowed or denied?
- Did a security boundary behave unexpectedly?
- Are credentials being abused?
- Are privileged operations occurring?
- Is an attack underway?
- Is a security control failing?

The objective is not to collect every possible event.

The objective is to collect enough meaningful evidence to detect, investigate, and respond to security-relevant behavior.

---

# Engineering Principle

> **Security monitoring should produce actionable evidence about important security boundaries, identities, privileges, and abnormal behavior without becoming an indiscriminate collection of noise.**

---

# 1. Monitoring vs Logging

Logging records events.

Security monitoring interprets events for security significance.

For example:

```text
Application Log
      │
      ▼
"Login failed"
```

becomes security-relevant when correlated with:

```text
100 failed logins
      +
same account
      +
same source
      +
short time window
```

Monitoring therefore depends on both telemetry and interpretation.

---

# 2. Security-Relevant Events

Events worth monitoring may include:

- authentication failures,
- successful privileged authentication,
- authorization failures,
- privilege changes,
- account creation,
- account deletion,
- credential changes,
- secret access,
- security-policy changes,
- administrative actions,
- suspicious network activity.

The exact event set should be risk-driven.

---

# 3. Identity Visibility

Security monitoring should preserve enough identity information to establish:

- who acted,
- which workload acted,
- which service acted,
- which tenant was involved where applicable.

Anonymous or ambiguous events are difficult to investigate.

---

# 4. Authentication Monitoring

Important authentication events may include:

- successful authentication,
- failed authentication,
- repeated failures,
- MFA failures,
- account lockouts,
- unusual authentication patterns,
- authentication from unexpected contexts.

Monitoring should focus on meaningful anomalies rather than simply counting every login.

---

# 5. Authorization Monitoring

Authorization failures can reveal:

- accidental misuse,
- application defects,
- privilege escalation attempts,
- resource enumeration,
- tenant-isolation attacks.

Important authorization denials should therefore be observable.

---

# 6. Privileged Activity

Privileged operations deserve stronger monitoring.

Examples include:

- changing access policies,
- creating administrative identities,
- changing security configuration,
- modifying production infrastructure,
- accessing sensitive data.

The system should provide sufficient evidence to determine:

```text
Who
  +
What
  +
When
  +
Result
```

---

# 7. Identity Lifecycle Events

Monitor important changes to identities.

Examples include:

- identity creation,
- role changes,
- privilege elevation,
- account disablement,
- account deletion,
- service-account creation.

Unexpected identity changes can represent a significant security event.

---

# 8. Credential Events

Where appropriate, monitor:

- credential creation,
- credential rotation,
- credential revocation,
- failed credential use,
- unexpected credential usage.

The monitoring system should never record the actual secret value.

---

# 9. Secret Access

Access to particularly sensitive secrets may warrant monitoring.

The objective is to identify:

- unexpected identities,
- unexpected workloads,
- unusual access frequency,
- access outside normal operational patterns.

Monitoring should capture access metadata rather than secret contents.

---

# 10. Key-Management Events

Cryptographic key operations may be security-sensitive.

Depending on the system, monitor:

- key creation,
- key rotation,
- key deletion,
- key access,
- policy changes.

The exact level of monitoring should follow the importance of the keys.

---

# 11. Administrative Changes

Security-relevant configuration changes should be observable.

Examples include:

- firewall changes,
- authorization-policy changes,
- identity-provider configuration,
- secret-store policies,
- network exposure,
- security-control configuration.

A security control that can silently change is difficult to trust.

---

# 12. Application Security Events

Applications should produce useful security signals for important events.

Examples include:

- repeated invalid requests,
- authorization failures,
- suspicious resource access,
- rejected uploads,
- invalid tokens,
- unusual API behavior.

Application telemetry should remain consistent with the organization's observability model.

---

# 13. Sensitive Data Protection

Security telemetry must itself be protected.

Logs and monitoring systems may contain:

- user identifiers,
- resource identifiers,
- request metadata,
- security events.

They should not unnecessarily contain:

- passwords,
- access tokens,
- private keys,
- secret values.

Security monitoring must not become a secondary data-leak channel.

---

# 14. Event Integrity

Security-relevant logs should have appropriate protection against:

- unauthorized modification,
- deletion,
- tampering.

The required controls depend on the risk and regulatory environment.

For high-impact systems, stronger integrity guarantees may be required.

---

# 15. Event Time

Security investigations depend heavily on time.

Security events should therefore have reliable timestamps.

Distributed systems should consider:

- clock synchronization,
- timezone handling,
- event ordering,
- ingestion delay.

An investigation becomes difficult when event timelines cannot be trusted.

---

# 16. Correlation

Individual events may be harmless.

A sequence of events may reveal an attack.

For example:

```text
Failed Authentication
        │
        ▼
Successful Authentication
        │
        ▼
Privilege Change
        │
        ▼
Sensitive Resource Access
```

Security monitoring should correlate events where doing so provides meaningful detection value.

---

# 17. Baselines

Monitoring may use expected behavior as a reference.

Examples include:

- normal authentication frequency,
- normal service-to-service calls,
- normal administrative activity,
- normal data-access patterns.

Anomaly detection should be based on meaningful baselines rather than assumptions that all unusual behavior is malicious.

---

# 18. Detection Rules

Security detections should represent meaningful risks.

Examples:

```text
Repeated Authentication Failures
```

or:

```text
Unexpected Privilege Elevation
```

or:

```text
Service Accessing Resource Outside Expected Scope
```

Rules should have clear intent.

---

# 19. Alert Quality

A security alert should answer:

- What happened?
- Why is it suspicious?
- Which identity is involved?
- Which system is affected?
- What evidence exists?
- What should the operator investigate?

Alerts without useful context create operational noise.

---

# 20. Alert Fatigue

Too many low-value alerts cause important events to be ignored.

Therefore:

> Detection quality is more important than detection quantity.

Alert thresholds should be tuned according to real operational experience.

---

# 21. Severity

Security events should have meaningful severity.

A possible model is:

```text
Informational
      │
      ▼
Low
      │
      ▼
Medium
      │
      ▼
High
      │
      ▼
Critical
```

Severity should reflect potential impact and urgency.

---

# 22. Detection Confidence

Not every detection represents a confirmed attack.

Monitoring systems should distinguish where useful between:

- observation,
- suspicious activity,
- probable attack,
- confirmed incident.

This prevents premature conclusions while still enabling rapid response.

---

# 23. Detection Coverage

Security monitoring should be evaluated against important threats.

For each significant threat, ask:

```text
Threat
  │
  ▼
Attack Path
  │
  ▼
Detection Signal?
  │
  ├── Yes
  │
  └── No → Identify Visibility Gap
```

This connects monitoring to threat modeling.

---

# 24. Security Monitoring and Threat Modeling

Threat models should identify which events matter enough to monitor.

For example:

```text
Threat:
Privilege Escalation

Security Control:
Authorization

Monitoring Signal:
Unexpected Privilege Change
```

This creates a traceable relationship between:

```text
Threat → Control → Detection
```

---

# 25. Network Security Monitoring

Where relevant, monitor:

- unexpected inbound exposure,
- unexpected outbound communication,
- unusual connection patterns,
- access to restricted network zones.

Network monitoring should complement—not replace—application-level authorization.

---

# 26. Service-to-Service Monitoring

Unexpected service communication can reveal:

- compromised workloads,
- misconfiguration,
- privilege escalation,
- lateral movement.

Important service boundaries should therefore produce enough telemetry to establish communication patterns.

---

# 27. Data Access Monitoring

Sensitive data access may require stronger monitoring.

Examples include:

- unusual bulk reads,
- unexpected administrative access,
- cross-tenant access attempts,
- unusual export behavior.

Monitoring requirements should follow data classification.

---

# 28. Data Exfiltration Signals

Potential indicators may include:

- unusual transfer volume,
- unexpected destinations,
- abnormal export frequency,
- unusual access patterns.

Detection should be based on the system's actual data flows.

---

# 29. Security Monitoring of CI/CD

Build and deployment systems should also be monitored.

Important events may include:

- workflow changes,
- credential changes,
- unusual deployments,
- unexpected artifact changes,
- privilege escalation,
- unusual build behavior.

CI/CD is part of the security boundary.

---

# 30. Supply-Chain Monitoring

Security monitoring should incorporate relevant signals from the software supply chain.

Examples include:

- newly disclosed dependency vulnerabilities,
- compromised packages,
- unexpected artifact changes,
- signing failures,
- provenance violations.

The exact monitoring model depends on the supply-chain architecture.

---

# 31. Container Monitoring

Where containers are used, security monitoring may include:

- unexpected image execution,
- unusual process behavior,
- privilege escalation,
- unexpected network connections,
- runtime policy violations.

Container monitoring should complement image and build-time security controls.

---

# 32. Infrastructure Monitoring

Infrastructure security events may include:

- firewall changes,
- security-group changes,
- IAM changes,
- storage-policy changes,
- public exposure,
- administrative access.

Infrastructure changes should be attributable.

---

# 33. Monitoring Control Failures

Security controls themselves can fail.

Examples include:

- authentication service unavailable,
- certificate validation failures,
- authorization service errors,
- secret-store failures,
- policy evaluation failures.

Such failures should be visible because they may create security or availability consequences.

---

# 34. Fail-Open Detection

Where a system is intentionally designed to fail open under specific conditions, that behavior should be observable.

For example:

```text
Security Dependency Failure
          │
          ▼
Fallback Behavior
          │
          ▼
Security Monitoring Event
```

Unexpected fallback behavior should receive attention.

---

# 35. Monitoring Administrative Interfaces

Administrative interfaces should receive stronger monitoring than ordinary application activity where appropriate.

Examples include:

- control planes,
- management consoles,
- privileged APIs,
- infrastructure management interfaces.

Administrative activity should be attributable and reviewable.

---

# 36. Monitoring Changes to Security Policy

Security-policy changes should be especially visible.

Examples include:

- granting a role,
- modifying an access policy,
- opening a network port,
- changing a secret policy,
- disabling a security control.

These changes can alter the security boundary itself.

---

# 37. Detection of Credential Abuse

Credential abuse may appear as:

- unusual source,
- unusual frequency,
- unusual resource access,
- unusual time,
- unusual privilege usage.

Detection should combine multiple signals where possible.

---

# 38. Detection of Lateral Movement

Compromised identities or workloads may attempt to access additional systems.

Potential signals include:

```text
Expected:
Service A → Service B

Unexpected:
Service A → Service C
Service A → Service D
Service A → Administrative Service
```

Monitoring should focus on meaningful deviations from expected communication patterns.

---

# 39. Detection of Privilege Escalation

Potential indicators include:

- unexpected role changes,
- administrative token issuance,
- privileged API access,
- new service identities,
- policy modifications.

Privilege changes should be attributable and observable.

---

# 40. Monitoring High-Risk Operations

Some business operations deserve stronger security visibility.

Examples include:

- financial transactions,
- credential changes,
- account recovery,
- bulk exports,
- administrative actions.

The monitoring depth should follow business impact.

---

# 41. Security Dashboards

Dashboards may provide visibility into:

- authentication failures,
- privileged activity,
- security incidents,
- important vulnerabilities,
- suspicious access,
- security-control health.

Dashboards should support decisions rather than simply display large volumes of metrics.

---

# 42. Security Metrics

Useful security metrics may include:

- time to detect,
- time to respond,
- unresolved high-risk findings,
- privileged-access events,
- authentication failure patterns,
- vulnerability exposure,
- credential rotation status.

Metrics should measure meaningful outcomes rather than activity alone.

---

# 43. Detection Testing

Security detections should themselves be tested.

A detection should answer:

> If the event we care about occurs, will we actually notice?

Possible techniques include:

- controlled test events,
- simulated attacks,
- tabletop exercises,
- detection validation.

---

# 44. Detection Regression

When an important detection is added, it should not silently disappear during future system changes.

Detection logic should therefore be treated as maintainable engineering.

---

# 45. Monitoring Retention

Security events may need to be retained long enough to support:

- investigation,
- incident response,
- operational analysis,
- compliance requirements.

Retention should be based on actual requirements.

Longer retention also increases:

- storage cost,
- privacy exposure,
- access-control requirements.

---

# 46. Access to Security Logs

Security logs are themselves sensitive.

Access should be restricted according to need.

The ability to:

```text
Generate Security Event
```

should not automatically imply the ability to:

```text
Delete or Modify Security Evidence
```

---

# 47. Security Monitoring Availability

Critical monitoring infrastructure should itself be reliable enough to detect important events.

The architecture should consider:

- telemetry loss,
- collector failure,
- ingestion delays,
- storage exhaustion.

A monitoring system that silently stops receiving events creates a dangerous blind spot.

---

# 48. Monitoring Gaps

The organization should be able to identify important areas where security events are not visible.

Examples:

```text
Critical Asset
      │
      ▼
No Security Telemetry
      │
      ▼
Visibility Gap
```

Unknown visibility gaps are themselves a security risk.

---

# 49. Investigation Support

Security monitoring should provide enough context to reconstruct important events.

Useful context may include:

- identity,
- source,
- destination,
- resource,
- action,
- timestamp,
- result,
- request or correlation identifier.

The objective is to connect related events across system boundaries.

---

# 50. Security Monitoring and Incident Response

Monitoring exists partly to initiate incident response.

Conceptually:

```text
Security Signal
      │
      ▼
Detection
      │
      ▼
Triage
      │
      ▼
Incident Response
```

Detection and response should therefore be designed together.

---

# 51. Security Monitoring and Privacy

Monitoring can itself create privacy concerns.

The project should avoid collecting unnecessary personal information.

Security monitoring should balance:

- detection capability,
- data minimization,
- access control,
- retention.

---

# 52. Minimum Engineering Requirements

Every production project should:

- [ ] Monitor important authentication events.
- [ ] Monitor important authorization failures.
- [ ] Monitor privileged operations.
- [ ] Monitor security-policy changes.
- [ ] Protect security logs from unauthorized modification.
- [ ] Prevent secrets from entering security telemetry.
- [ ] Provide reliable timestamps.
- [ ] Define meaningful security alerts.
- [ ] Establish severity for important alerts.
- [ ] Provide enough context for investigation.
- [ ] Monitor important security-control failures.
- [ ] Test important security detections.

Higher-risk systems may additionally require:

- [ ] Centralized security event correlation.
- [ ] Behavioral anomaly detection.
- [ ] Network security monitoring.
- [ ] Data-access monitoring.
- [ ] Supply-chain monitoring.
- [ ] Runtime container monitoring.
- [ ] Detection engineering and regression testing.
- [ ] Security dashboards.
- [ ] Formal detection-coverage analysis.
- [ ] Continuous detection validation.

---

# Relationship With Other Security Standards

This standard works with:

- `06-security/README.md`
- `06-security/identity-and-access.md`
- `06-security/secrets-and-key-management.md`
- `06-security/application-security.md`
- `06-security/supply-chain-security.md`
- `06-security/security-testing.md`
- `06-security/incident-response.md`

It also connects directly with:

- `05-reliability/`
- `07-delivery/`
- `08-observability/`
- `09-platform-and-infrastructure/`
- `11-operational-readiness/`

---

# Final Principle

> **A security control that cannot be observed cannot be reliably trusted in production. Prevention reduces the probability of compromise; monitoring provides the evidence needed when prevention is bypassed, misconfigured, or fails.**
