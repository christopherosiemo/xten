# ADR-006 — Durable scoped background processing

**Status: PROPOSED**
**Date: 2026-09-24**

## Context

Collection, ingestion, normalisation, large analysis and report generation can exceed HTTP lifetimes and fail mid-operation. Distributed queue delivery and provider callbacks can duplicate or reorder work.

## Decision

Use durable background jobs and a managed queue for long work. A persisted job carries job ID, tenant ID, requested operation and scope, payload/schema version, idempotency key where applicable, attempt count, creation time, status, correlation/trace ID and result/error reference. Messages carry references, not secrets or financial truth. Workers reload current tenant, connection and operation authority; leases/checkpoints support crash recovery. Assume at-least-once delivery and define per-operation idempotency, including provider/delivery-aware source identity and immutable versioned outputs. For a state change that must initiate work/event, commit state plus an outbox record in one PostgreSQL transaction; a dispatcher retries delivery and consumers deduplicate. Never claim exactly-once distributed delivery or rely on process memory for durable workflow state.

## Alternatives considered

In-process background tasks were rejected for durable work because restarts lose intent. Uncoupled DB write then queue publish was rejected because failure between them loses work. A claim of exactly-once queue execution was rejected; side effects must tolerate retries.

## Consequences

Each job type requires retry budget, backoff, poison/failed state, idempotency and compensation policy. Operations must expose partial/failed status rather than silently marking success.

## Security implications

Tenant and operation scope are mandatory in durable state and checked at every attempt. Queue payloads omit credentials and sensitive raw data. Support replay requires authorisation and audit.

## Financial/data-integrity implications

Retries cannot inflate totals or publish duplicate benefit. Legitimate identical-valued source rows remain distinct when source identity differs; report publication binds immutable input versions.

## Operational implications

Monitor queue age, attempts, dead letters, outbox lag, worker crashes and provider throttling with sanitised logs. Run restore/replay drills later under agreed M01 targets.

## Requirements/milestones affected

M01, M02, M05, M06, M08, M09, M11, M20; REQ-004, REQ-006, REQ-008, REQ-009, REQ-010, REQ-024, REQ-028, REQ-042.

## Conditions that would justify revisiting the decision

An operation's reliability or ordering requirements exceed the chosen queue/outbox semantics; any replacement must retain durable tenant scope, transactional intent and proven idempotency.

This ADR is proposed M01-T01 architecture work, not a human acceptance decision. See the [architecture index](../README.md), [system architecture](../SYSTEM_ARCHITECTURE.md) and [acceptance register](../../ACCEPTANCE.md).
