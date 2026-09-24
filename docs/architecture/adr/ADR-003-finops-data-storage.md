# ADR-003 — Split control, evidence and analytical storage

**Status: PROPOSED**
**Date: 2026-09-24**

## Context

Source billing exports can be large, corrected and delivered repeatedly. Transactional workflow state and high-volume analytical scans have different durability and query needs. XTEN must reproduce reports without rewriting their inputs.

## Decision

Use PostgreSQL for tenant/control metadata, identities/memberships, policies, workflows, lineage indexes, recommendation/measurement/report metadata, audit and outbox. Use tenant-qualified S3-compatible object storage for immutable/versioned source artifacts, provider exports, normalised high-volume analytical files and generated report artifacts where appropriate. Store artifact hash, provider/delivery identity, capture time, schema/adapter version and provenance metadata in a tenant-scoped index. Represent normalised large billing datasets as columnar files such as Parquet, with conceptual partitions by tenant, provider/dataset, billing period and version. A replaceable analytical-query adapter accepts authorised tenant/dataset/version and typed query intent; domain code does not depend on one warehouse vendor. SourceRecordIdentity is provider/delivery-aware; replay classification is not a rule to drop identical rows. Corrections append evidence and derived versions; published snapshots bind exact versions.

## Alternatives considered

Putting every raw line item only in PostgreSQL was rejected as a mandatory design because it couples transactional load to analytical volume. Mutable single-table source-to-report storage was rejected because corrections would destroy provenance. Choosing a warehouse vendor now was deferred pending workload and support-matrix evidence.

## Consequences

A lineage index and object integrity checks are necessary. Storage lifecycles, retention, backfills and query adapter performance need measurable review. Cross-store publication must stage artifacts and commit metadata atomically at the control plane boundary with safe retry/cleanup.

## Security implications

Object paths are not access controls; scoped service identities, server-side tenant authorisation, encryption/key policy and audited retrieval are required. No secrets or real client data belong in the repository.

## Financial/data-integrity implications

Preserve billed/effective cost bases, currency, provider-specific fields and raw value provenance. Duplicate deliveries must not inflate totals, while legitimately identical records remain. Report results reproduce source and policy versions.

## Operational implications

Monitor object/metadata consistency, failed uploads, manifest/hash mismatch, late delivery, partition growth and query costs. Retention/residency and chosen analytical engine await later M01 decisions.

## Requirements/milestones affected

M01, M04, M05, M06, M07, M11, M16; REQ-007, REQ-008, REQ-009, REQ-010, REQ-013, REQ-024.

## Conditions that would justify revisiting the decision

Measured dataset sizes or query patterns justify another storage/query split, provided immutability, tenant scope, lineage and report reproducibility remain demonstrable.

This ADR is proposed M01-T01 architecture work, not a human acceptance decision. See the [architecture index](../README.md), [system architecture](../SYSTEM_ARCHITECTURE.md) and [acceptance register](../../ACCEPTANCE.md).
