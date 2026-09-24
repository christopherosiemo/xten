# Roadmap

This is a plan, not implementation status. [STATUS](STATUS.md) records actual progress; [PRODUCT](PRODUCT.md) defines mandatory scope and [ACCEPTANCE](ACCEPTANCE.md) defines initial verification requirements. IDs and names below are stable. Dependencies are prerequisite gates, not effort estimates or deadlines. Ranges include both endpoints.

Security and operational checks begin with architecture and each affected implementation. M20 is the final broad assessment, not permission to defer security. M01 must define the finite versioned support matrix and measurable performance, recovery and workload requirements before affected capabilities can be accepted. All gates require designated human review; the implementing agent can submit work as `READY_FOR_REVIEW` but cannot accept a milestone.

## M00 — Product contract and repository working rules

- **Objective:** Establish the product boundary, delivery contract and durable repository rules.
- **Dependencies:** None; inspect the existing repository before edits.
- **Expected evidence:** Reviewed specification, initial acceptance register, repository observations and documentation validation.
- **Acceptance gate:** Designated human reviewer approves the contract and working rules; agent completion alone is insufficient.

## M01 — Architecture, threat model and finite support matrix

- **Objective:** Review domain boundaries, financial policies, threat model and the finite versioned release support matrix. Define measurable performance, recovery and workload requirements.
- **Dependencies:** M00.
- **Expected evidence:** Architecture decisions, trust/data-flow diagrams, threats and mitigations, support matrix and numeric workload/performance/recovery targets with measurement methods.
- **Acceptance gate:** Human architecture, security and financial review approves scope and measurable targets before affected capabilities can be accepted.

## M02 — Runnable engineering foundation and automated checks

- **Objective:** Establish a reproducible development setup, automated checks and early security/operational foundations.
- **Dependencies:** M01.
- **Expected evidence:** Clean-checkout setup transcript, pinned dependency inventory, automated check results and initial deployment/monitoring procedures.
- **Acceptance gate:** Documented setup reproduces; required checks run; initial secret handling, dependency and operational checks are reviewed.

## M03 — Identity, tenant isolation and client onboarding

- **Objective:** Implement identity, tenant-scoped roles, audit and client onboarding.
- **Dependencies:** M02.
- **Expected evidence:** Authentication/authorisation tests, cross-tenant API/storage/job/export probes and onboarding/audit walkthroughs.
- **Acceptance gate:** Unauthorised requests fail; tenant boundaries and onboarding are demonstrated for the supported roles.

## M04 — Financial data model and reference test cases

- **Objective:** Define source identity, exact monetary calculations, currencies and cost-basis semantics.
- **Dependencies:** M01, M02.
- **Expected evidence:** Versioned schemas, rounding/conversion policies and independently reviewed synthetic financial reference cases.
- **Acceptance gate:** Financial reviewers approve deterministic expected results and separation of billed and effective/amortised costs.

## M05 — AWS read-only connection and collection

- **Objective:** Collect the supported AWS source data through explicitly authorised scoped access.
- **Dependencies:** M03, M04.
- **Expected evidence:** Authorised AWS connector results, permission boundary checks, source provenance and revocation/retry evidence.
- **Acceptance gate:** Real AWS access is read-only and limited to the agreed matrix; revocation safely stops collection; mock results are labelled separately.

## M06 — Billing normalisation, reconciliation and data quality

- **Objective:** Normalise billing without losing source identity; reconcile invoices and surface quality limitations.
- **Dependencies:** M04, M05.
- **Expected evidence:** Replay/correction/late-data cases, legitimate duplicate cases, invoice reconciliations and data-quality reports.
- **Acceptance gate:** Imports do not inflate totals or discard legitimate records; differences are explained; missing values remain unknown.

## M07 — Business context, ownership, allocation and cost exploration

- **Objective:** Map ownership and applications, allocate shared costs and support traceable exploration.
- **Dependencies:** M03, M06.
- **Expected evidence:** Versioned mappings, allocation cases, totals reconciliation and user exploration evidence.
- **Acceptance gate:** Allocated totals reconcile to source amounts; unallocated/unknown ownership remains visible; client access stays scoped.

## M08 — Evidence-backed recommendation engine

- **Objective:** Produce inspectable opportunities with confidence, exclusions, risk, benefit, dependencies, owners and overlaps.
- **Dependencies:** M06, M07.
- **Expected evidence:** Reviewed recommendation cases, source/assumption links, commitment-aware estimates and overlap tests.
- **Acceptance gate:** Reviewers can reproduce estimates and inspect evidence; unknowns are disclosed and overlaps are not double counted.

## M09 — Review, approvals and implementation workflow

- **Objective:** Implement reviewed approval, rejection, deferment and client implementation coordination, including selected ticketing/change integration.
- **Dependencies:** M03, M08.
- **Expected evidence:** State-transition tests, role checks, audit trails, separate implementation records and authorised integration results.
- **Acceptance gate:** Invalid transitions fail; scope and decision rights persist; selected integration delivery is auditable and safe under retries.

## M10 — Savings ledger and financial verification

- **Objective:** Measure distinct benefit categories against versioned agreed baselines.
- **Dependencies:** M04, M06, M09.
- **Expected evidence:** Baseline approvals, observation windows, implementation evidence, financial reference cases and ledger reconciliation.
- **Acceptance gate:** Potential savings, run-rate reduction, realised benefit, avoidance and capacity remain distinct; financial review verifies attribution and uncertainty.

## M11 — Client portal and report deliverables

- **Objective:** Deliver leadership, finance and engineering experiences plus all six client deliverables and branding.
- **Dependencies:** M03, M07, M09, M10.
- **Expected evidence:** Role-based walkthroughs, executive brief, financial workbook, opportunity register, implementation roadmap, governance pack and evidence appendix.
- **Acceptance gate:** All deliverables reproduce stored results and lineage; authorised client reviewers can use their intended experiences.

## M12 — Restricted real-world AWS pilot acceptance

- **Objective:** Validate the agreed restricted AWS delivery lifecycle with an authorised customer.
- **Dependencies:** M05–M11, plus applicable security/operational controls from M01–M03.
- **Expected evidence:** Real authorised data, agreed baseline, reviewed opportunity, approval, observed implementation outcome, verified reporting and client feedback.
- **Acceptance gate:** Human reviewers accept the recorded pilot subset with real data and an observed implementation outcome; this does not accept the full product.

## M13 — Forecasting, commitments, unit economics and governance

- **Objective:** Add forecasting, budgets, anomalies, commitment analysis, unit economics and governance assessments.
- **Dependencies:** M07, M08, M10, M12.
- **Expected evidence:** Reference scenarios, forecast backtesting, anomaly cases, commitment models, unit definitions and governance reviews.
- **Acceptance gate:** Agreed methods and measurable criteria are met; assumptions, uncertainty and missing business inputs remain explicit.

## M14 — Azure integration and provider-specific validation

- **Objective:** Integrate Azure within its reviewed support-matrix entries.
- **Dependencies:** M06, M12.
- **Expected evidence:** Authorised Azure connector tests, provider-specific billing/reconciliation cases, source lineage and revocation evidence.
- **Acceptance gate:** Supported Azure cases satisfy the financial, isolation and access requirements with real connector evidence.

## M15 — Google Cloud integration and provider-specific validation

- **Objective:** Integrate Google Cloud within its reviewed support-matrix entries.
- **Dependencies:** M06, M12; reuse shared-core lessons from M14 where applicable.
- **Expected evidence:** Authorised Google Cloud connector tests, provider-specific billing/reconciliation cases, source lineage and revocation evidence.
- **Acceptance gate:** Supported Google Cloud cases satisfy the financial, isolation and access requirements with real connector evidence.

## M16 — Kubernetes allocation and extended cost/business inputs

- **Objective:** Support OpenCost-compatible allocation and explicit AI/SaaS and business-activity import schemas.
- **Dependencies:** M07, M10, M13.
- **Expected evidence:** Versioned import contracts, authorised representative input results, allocation reconciliations and missing/duplicate input cases.
- **Acceptance gate:** Supported inputs reconcile without silent double counting; ownership, business units and limitations are inspectable.

## M17 — Evidence-grounded AI assistance and evaluations

- **Objective:** Provide restricted assistance grounded in authorised evidence.
- **Dependencies:** M08, M09, M11.
- **Expected evidence:** Grounding, unsupported-answer, prompt-injection, tenant-boundary and authority-boundary evaluation results.
- **Acceptance gate:** AI cannot invent authoritative financial figures, bypass tenant controls or authorise changes; agreed evaluation criteria are met.

## M18 — Enterprise service operations and commercial readiness

- **Objective:** Complete entitlements, delivery workload, engagement economics, support, branding, integrations and offboarding.
- **Dependencies:** M09, M11, M13–M17.
- **Expected evidence:** Service-role walkthroughs, entitlement checks, workload/economics records, support procedures, integration tests and offboarding exercises.
- **Acceptance gate:** Human operational review confirms the agreed service can be delivered and supported, with complete scoped offboarding.

## M19 — Separately authorised controlled execution

- **Objective:** Add narrowly supported actions behind a separate execution security boundary.
- **Dependencies:** M09, M10, M18 and action-specific M01 security requirements.
- **Expected evidence:** Action allowlist, separate credentials/roles, bound approvals, expiry/tamper/replay tests, audit and rollback/failure drills.
- **Acceptance gate:** Only explicitly authorised supported actions execute; expired or altered approvals fail; action-specific safety and recovery evidence is reviewed.

## M20 — Security, recovery, scale and operational hardening

- **Objective:** Perform final broad security and operational assessment across the agreed release scope.
- **Dependencies:** M13–M19 and all applicable earlier controls.
- **Expected evidence:** Security review, measured workload/performance tests, exercised restore/recovery, monitoring, deployment and upgrade/rollback records.
- **Acceptance gate:** Agreed M01 thresholds are met; risks are reviewed; critical release blockers are resolved; recovery is exercised rather than merely documented.

## M21 — Full production acceptance

- **Objective:** Accept the complete agreed release scope across capabilities A–Q.
- **Dependencies:** M00–M20 accepted by designated human reviewers.
- **Expected evidence:** Versioned scope-to-evidence register, engineering results, real-world client outcomes, operational exercises and human acceptance decisions.
- **Acceptance gate:** Every mandatory release requirement has appropriate engineering and real-world evidence; full-scope human acceptance is recorded.

M12 requires real authorised data and an observed implementation outcome for its restricted AWS scope. M21 requires evidence for the full agreed release, including the other providers and mandatory later capabilities. Neither a pilot nor passing mocked tests substitutes for full production acceptance.
