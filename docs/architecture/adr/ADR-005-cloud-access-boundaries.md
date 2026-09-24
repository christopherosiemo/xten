# ADR-005 — Separate cloud collection and execution authority

**Status: PROPOSED**
**Date: 2026-09-24**

## Context

XTEN initially advises and collects evidence. A customer trust relationship for billing/operational reads must never enable resource mutation. Provider access, revocation and confused-deputy risks vary by customer and provider.

## Decision

Collector authority is READ ONLY for supported collection actions and only within an authorised CloudConnection/CloudScope and versioned support matrix. AWS direction is customer cross-account assumed roles with short-lived credentials, a unique customer-connection external identifier for confused-deputy protection, explicit least-privilege policy and revocation checks before future collection; an AWS external ID is an identifier/control input, not a password or secret. Do not use permanent client AWS access keys. Azure and Google Cloud adapter designs must support short-lived federation rather than assume downloaded long-lived secrets; exact scopes await M01-T02. The M19 execution plane is separate, absent/inactive now, and would need a distinct identity/credentials, narrow action allowlist, target-bound expiring approval and audit. Collection roles, jobs and workers cannot inherit execution authority.

## Alternatives considered

Permanent shared customer access keys and generic write-capable collection roles were rejected because rotation/revocation and blast radius are unacceptable. Combining collection and execution credentials was rejected because a read grant could then become production authority. A live execution component in M01 was rejected as out of scope.

## Consequences

Connections require customer-specific authorisation evidence, scoped credential reference, revocation state and real connector tests later. Supporting a provider never implies every service, region, agreement or action.

## Security implications

Trust policy and external ID must bind the intended customer connection; least privilege and short credential lifetime are defence layers, not substitutes for tenant checks. Revocation stops queued/future collection safely and is audited. Execution is a new M19 security review.

## Financial/data-integrity implications

Provider data collected through read scopes remains versioned evidence. A recommendation or approved estimate is not authority to mutate infrastructure or evidence of realised benefit.

## Operational implications

Credential rotation, role assumption failures, rate limits and revocation need visible collection state and retry policy. Azure/GCP federation and exact permissions await provider matrix work.

## Requirements/milestones affected

M01, M05, M12, M14, M15, M19; REQ-005, REQ-006, REQ-026, REQ-027, REQ-031, REQ-035, REQ-041.

## Conditions that would justify revisiting the decision

Provider-native supported access patterns or threat-model findings require a safer scoped mechanism; any later execution design is a distinct M19 decision and cannot be inferred from this ADR.

This ADR is proposed M01-T01 architecture work, not a human acceptance decision. See the [architecture index](../README.md), [system architecture](../SYSTEM_ARCHITECTURE.md) and [acceptance register](../../ACCEPTANCE.md).
