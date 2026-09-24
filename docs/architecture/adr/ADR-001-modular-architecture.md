# ADR-001 — Modular application architecture

**Status: PROPOSED**
**Date: 2026-09-24**

## Context

XTEN needs one coherent financial and tenant model across onboarding, collection, recommendations, approvals and reports. Different runtime loads justify separate web, API and worker processes, but early distributed service boundaries would add network failure and consistency problems before module contracts are established.

## Decision

Start with a modular monolith: one coherently versioned server domain, separately runnable React/TypeScript web, Python/FastAPI API and Python workers. Internal modules own identity/access, tenancy, engagement, connections, ingestion, financial core, allocation, recommendations, workflow, measurement, reporting and audit. Each owns its invariants and exposes typed/versioned commands, queries or events; UI handlers orchestrate through application services, while provider SDK calls stay behind adapters. Shared deployment/code evolution does not imply shared process memory for workflow state. Future extraction requires a new ADR, contract, tenant boundary and failure analysis.

## Alternatives considered

An initial microservice fleet was rejected because cross-service financial consistency and operational overhead would precede evidence of independent scaling needs. A giant unstructured application was rejected because it obscures ownership and invariants. Domain logic in UI handlers and provider SDK calls spread throughout the domain were rejected because they bypass authorisation, lineage and adapter versioning.

## Consequences

Module interfaces and ownership need review discipline; some internal calls remain in-process. Separate process deployments can scale web, API and workers independently while preserving one domain model. Later extraction remains possible, not predetermined.

## Security implications

Tenant authorisation, audit and secret access are shared guardrails invoked by modules, not optional per-handler helpers. Module boundaries do not replace database/object/job isolation.

## Financial/data-integrity implications

One financial core owns exact money, calculation versions and policy contracts; provider adapters cannot redefine monetary truth or silently coerce cost bases.

## Operational implications

A coordinated release train and versioned worker/job contracts are needed during rolling upgrades. Separate API/worker health and deployment controls are still required.

## Requirements/milestones affected

M01, M02, M03, M04, M06, M20; REQ-002, REQ-004, REQ-007, REQ-012, REQ-042.

## Conditions that would justify revisiting the decision

Measured scaling or team ownership proves a module requires independent deployment, and an extraction design preserves tenant, lineage and financial invariants with explicit failure/rollback evidence.

This ADR is proposed M01-T01 architecture work, not a human acceptance decision. See the [architecture index](../README.md), [system architecture](../SYSTEM_ARCHITECTURE.md) and [acceptance register](../../ACCEPTANCE.md).
