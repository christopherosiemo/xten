# ADR-004 — Exact and versioned financial arithmetic

**Status: PROPOSED**
**Date: 2026-09-24**

## Context

Cloud bills contain fractional charges, credits, commitments, multiple currencies and several cost bases. Approximate arithmetic, implicit conversion or an LLM-computed total would make reconciliation and claimed benefit unauditable.

## Decision

Authoritative money uses database NUMERIC/DECIMAL and application decimal arithmetic; binary floating point is prohibited for authoritative values. A monetary fact carries exact amount, currency, cost basis/type, period/context and source/calculation version. Preserve original provider values and negative charges/credits. A derived amount records calculation and policy versions plus rounding stage/mode/precision once approved. NULL/missing is unknown, not zero. Mixed currencies cannot be added without an explicit versioned FX policy and rate provenance; billed versus effective/amortised bases and unmatched periods cannot be silently combined. Deterministic code owns reconciliation, allocation, recommendations and savings calculations. AI may explain evidence but cannot supply authoritative numbers, approvals or execution.

## Alternatives considered

Binary float was rejected for authoritative money because rounding is representation-dependent. Silent FX/default cost-basis conversion was rejected because it changes financial meaning. LLM-derived authoritative totals were rejected because they are not deterministic, reproducible financial evidence.

## Consequences

Financial policies and reference cases must be designed and independently reviewed before implementation. Output types and APIs must expose currency, basis, period and version; reports need manifest-bound computation inputs.

## Security implications

Financial policy changes and baseline approvals are decision-right protected and audited. AI/tool interfaces cannot write authoritative calculations or approve changes.

## Financial/data-integrity implications

M01-T02 must specify currency codes, rate source/date/version, conversion timing, rounding precision/mode/stage, residual allocation, invoice tolerances, commitment/billed/effective semantics, negative values and time aggregation. No universal rounding rule is invented here.

## Operational implications

Calculation versions and policy migrations need reproducible backfill and discrepancy handling. Recalculation creates new derived/report versions rather than silently rewriting published results.

## Requirements/milestones affected

M01, M04, M06, M07, M08, M10, M11, M13; REQ-011, REQ-012, REQ-013, REQ-014, REQ-015, REQ-016, REQ-019, REQ-022, REQ-023, REQ-024.

## Conditions that would justify revisiting the decision

Independent financial review or provider evidence reveals a cost basis/policy distinction missing from this model, or reference cases fail; revise with versioned migration and published-report impact.

This ADR is proposed M01-T01 architecture work, not a human acceptance decision. See the [architecture index](../README.md), [system architecture](../SYSTEM_ARCHITECTURE.md) and [acceptance register](../../ACCEPTANCE.md).
