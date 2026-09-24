# ADR-002 — Defence-in-depth tenant isolation

**Status: PROPOSED**
**Date: 2026-09-24**

## Context

A consultancy serves multiple cloud customers. A single missing tenant predicate, mis-scoped worker, cache entry or export could disclose another customer's data. PostgreSQL RLS alone cannot secure object storage, queues or privileged roles.

## Decision

Require explicit tenant_id on every tenant-owned transactional record and on every job, dataset, object namespace, cache key if introduced, report and export. Application authorisation checks active membership, role, engagement and operation scope before access. PostgreSQL RLS adds row enforcement with policies bound to validated request/worker tenant context; runtime DB roles are non-owner, non-superuser and have no BYPASSRLS. Schema ownership/migrations use a distinct privileged identity, never the application runtime role. Enable RLS and expect FORCE ROW LEVEL SECURITY on tenant-owned tables to protect against accidental owner-based application access; FORCE does not constrain superusers or BYPASSRLS, so migration/maintenance paths are separate, approved and audited. Object keys are tenant-qualified, but server-side authorisation and scoped service identities still govern reads/writes. Workers revalidate tenant and operation; reports/exports and any caches are tenant-bound. Support/admin access is explicit, time-limited and audited. External IDs are standard non-sequential identifiers.

## Alternatives considered

Relying only on application WHERE clauses was rejected because one missed path can leak data. RLS alone was rejected because privileged roles, non-database stores, support tools and queues remain. A single database role that owns tables was rejected because owners normally bypass RLS unless FORCE applies.

## Consequences

All data and job contracts carry tenant scope; cross-tenant administrative operations require an exceptional design and audit. Integration tests must probe APIs, SQL/RLS, object paths, jobs, caches, exports and support access independently.

## Security implications

Fail closed when tenant context is absent or cannot be validated. Test grants, connection-pool context reset, RLS policies, owner/BYPASSRLS behaviour, signed URLs and support tooling. FORCE is expected, with documented exceptions only for separately controlled privileged maintenance.

## Financial/data-integrity implications

Cost rows, allocations, evidence and published results cannot be reassigned across tenants. Cross-tenant aggregates require explicit authorised policy and must not mix customer financial facts by accident.

## Operational implications

Migrations use a distinct privileged principal and reviewed runbook; support access is time-bound with reason and trace. Monitoring must avoid leaking tenant payloads.

## Requirements/milestones affected

M01, M02, M03, M05, M06, M11, M20; REQ-003, REQ-004, REQ-007, REQ-024, REQ-029.

## Conditions that would justify revisiting the decision

The isolation threat model or real tests show a boundary is insufficient, or a proposed tenancy/storage model changes; any revision needs equivalent or stronger cross-surface verification.

This ADR is proposed M01-T01 architecture work, not a human acceptance decision. See the [architecture index](../README.md), [system architecture](../SYSTEM_ARCHITECTURE.md) and [acceptance register](../../ACCEPTANCE.md).
