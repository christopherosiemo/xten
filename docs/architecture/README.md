# XTEN architecture index

M01-T01 defines the proposed core architecture and domain language for XTEN. The accepted M00 [product contract](../PRODUCT.md), [roadmap](../ROADMAP.md), [acceptance register](../ACCEPTANCE.md) and [status](../STATUS.md) remain authoritative for product scope. These M01 documents are design work for review: M01 is IN_PROGRESS and is not accepted.

## Document map

- [System architecture](SYSTEM_ARCHITECTURE.md): context, components, trust zones, flows, storage, failures and scaling.
- [Domain model](DOMAIN_MODEL.md): entity meanings, ownership, relationships, lifecycle and invariants.
- [ADR-001 — Modular architecture](adr/ADR-001-modular-architecture.md)
- [ADR-002 — Tenant isolation](adr/ADR-002-tenant-isolation.md)
- [ADR-003 — FinOps data storage](adr/ADR-003-finops-data-storage.md)
- [ADR-004 — Financial arithmetic](adr/ADR-004-financial-arithmetic.md)
- [ADR-005 — Cloud access boundaries](adr/ADR-005-cloud-access-boundaries.md)
- [ADR-006 — Background processing](adr/ADR-006-background-processing.md)
- [ADR-007 — Identity boundary](adr/ADR-007-identity-boundary.md)

## Status vocabulary and traceability

PROPOSED means a decision is drafted; READY_FOR_REVIEW means its evidence and dependencies have been assembled for review; ACCEPTED means designated human reviewers approved it; SUPERSEDED means a later recorded decision replaces it. Every ADR here is PROPOSED. Completing this task makes M01-T01 READY_FOR_REVIEW, not M01 ACCEPTED. Implementation work must cite relevant accepted ADRs and [acceptance requirements](../ACCEPTANCE.md), and record any divergence for human review.

Outstanding M01 work: planned M01-T02 financial semantics and finite support matrix; M01-T03 threat model, data classification and abuse cases; M01-T04 measurable operational, security and performance targets, traceability and final M01 review preparation. These task numbers are planning labels, subject to later task issuance. No implementation setup, product test or provider connection is authorised here.
