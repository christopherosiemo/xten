# Product contract

This is the M00 specification for **XTEN — FinOps Delivery OS**. No application capabilities are implemented. See the [roadmap](ROADMAP.md), [acceptance register](ACCEPTANCE.md) and [status](STATUS.md).

## Purpose and intended customer

XTEN supports secure, evidence-led FinOps delivery from initial customer onboarding through verified outcomes and ongoing governance. It is not merely a dashboard, chatbot, cost-report generator or autonomous resource-deletion tool.

The intended customer range is £1,000–£1,000,000 in monthly cloud expenditure. The initial managed-service focus is AWS-led customers spending approximately £20,000–£200,000 monthly. These are planning assumptions, not eligibility guarantees or promised customer savings. Currency in this market description does not imply that customer financial data may be silently converted to GBP.

## Consultancy delivery model

A consultancy operates a multi-client service with explicit engagements, entitlements, delivery owners and human review. Authorised collection feeds a validated financial baseline. Analysts propose evidenced opportunities; authorised reviewers and client decision-makers approve, reject or defer them. Engineers coordinate client implementation or perform only separately authorised supported actions. Observed outcomes feed financial verification, client deliverables and recurring governance.

The service is initially read-only/advisory. Customer authorisation, scope, data handling, retention, reporting obligations and decision rights must be recorded per engagement. Consultancy workload and engagement economics are part of the product, alongside client value. Client implementation can be coordinated without granting XTEN execution privileges.

## Personas

| Persona | Intended responsibilities and boundaries |
| --- | --- |
| Operator | Manage consultancy engagements, onboarding, entitlements, workload and support within assigned client permissions; no implicit execution authority. |
| Analyst | Validate and reconcile data, map allocation, establish baselines and prepare evidenced recommendations and reporting. |
| Engineer | Assess technical risk and dependencies, plan changes, coordinate or carry out separately authorised supported implementation, and record outcomes. |
| Client administrator | Manage the client's authorised users, access scope, connection lifecycle and engagement configuration within that tenant. |
| Client finance user | Review billing, allocation, budgets, baselines and measured benefit; exercise financial decision rights only where explicitly granted. |
| Client engineering user | Validate ownership and technical assumptions, assess risks, coordinate changes and provide implementation evidence. |
| Executive viewer | View permitted executive summaries, outcomes and governance information without implicit approval or change rights. |

Personas describe experiences, not a final permission model. M01 must specify explicit roles, delegation and separation of duties; M03 must verify tenant-scoped enforcement.

## Complete client lifecycle

Onboard → authorise access → connect → validate → reconcile → map ownership and allocation → establish baseline → identify and review opportunities → approve → implement or coordinate client implementation → observe → verify → report → govern → offboard.

Each stage needs an accountable owner, recorded scope, auditable evidence and an explicit handoff. Validation or reconciliation gaps remain visible and may block downstream conclusions. Rejected and deferred opportunities retain reasons and history. Offboarding includes stopping collection, revoking access, delivering agreed exports and applying the retention/deletion policy, including the treatment of backups and audit evidence.

## Mandatory release capabilities

These stable capability labels define mandatory full-release scope; they do not claim current implementation or complete test coverage.

| ID | Required capability | Principal milestones |
| --- | --- | --- |
| A | Multi-client delivery console, onboarding, permissions and audit. | M03, M18 |
| B | Client leadership, finance and engineering experiences. | M11 |
| C | Source preservation, lineage, data quality and reproducible reporting. | M04, M06, M11 |
| D | Currency-aware exact financial calculations and invoice reconciliation. | M04, M06 |
| E | Ownership, application mapping and shared-cost allocation. | M07 |
| F | Evidence-backed recommendations with confidence, exclusions, risk, estimated benefit, dependencies, owners and overlap relationships. | M08 |
| G | Review, approval, implementation and rejection/deferment workflows. | M09 |
| H | Savings measurement distinguishing potential savings, implemented run-rate reduction, realised benefit, cost avoidance and reclaimed capacity. These categories must not be added indiscriminately; units, periods, baselines and overlap must be explicit. | M10 |
| I | Executive brief, financial workbook, opportunity register, implementation roadmap, governance pack and evidence appendix. | M11 |
| J | Forecasting, budgets, anomalies, commitment analysis, unit economics and governance assessments. | M13 |
| K | AWS, Azure and Google Cloud within an explicit versioned support matrix, with AWS implemented first. | M01, M05, M14, M15 |
| L | Kubernetes allocation through OpenCost-compatible inputs and explicitly defined AI/SaaS and business-activity imports. | M16 |
| M | Controlled evidence-grounded AI assistance. | M17 |
| N | Selected ticketing/change integrations with scoped authorisation and auditable delivery. | M09, M18 |
| O | Separate, explicitly authorised execution for narrowly supported actions; initially the service is read-only/advisory. | M19 |
| P | Service entitlements, delivery workload, engagement economics, reporting branding, operational support and complete offboarding. | M11, M18 |
| Q | Deployment, monitoring, recovery, upgrades, security validation, workload testing and real-world acceptance. | M02, M12, M20, M21 |

M01 must establish a finite, versioned release support matrix and measurable performance, recovery and workload requirements before affected capabilities can be accepted. The matrix must name supported provider services, regions, billing/agreement types, data sources and versions, import schemas, integration choices and execution actions, with limitations, exclusions and validation obligations. Supporting a cloud provider does not mean supporting every service, region, agreement type or action. Support additions require review and evidence.

## Intermediate AWS pilot scope

M12 is a restricted real-world AWS pilot, bounded by the AWS subset agreed in M01 and an explicitly authorised engagement. It exercises onboarding, scoped read-only collection, reconciliation, allocation, baseline, reviewed opportunities, approvals, coordinated client implementation, observation, verification and client reporting. It requires real authorised data and an observed implementation outcome; mocked tests alone cannot satisfy it. A measured outcome may show no positive savings and must still be reported honestly.

Pilot acceptance covers only its recorded subset and limitations. It does not complete Azure, Google Cloud, extended inputs, enterprise operations, controlled execution or the full product. The pilot does not authorise XTEN to change production resources; client-led implementation or a separately authorised human process supplies implementation evidence.

## Explicit exclusions and change-control rules

Native integrations for every SaaS vendor, unrestricted autonomous production changes and resale of the platform to other consultancies are not required for the first full release. Unsupported matrix entries must be surfaced explicitly, not implied by broad provider labels. Guaranteed savings, unrestricted AI authority and silent financial estimates are outside the contract.

Changes to mandatory scope, support, architecture, financial policy, security boundaries or acceptance criteria require a recorded proposal with rationale, affected requirements, risks, evidence needs and migration impact. The designated human product/technical reviewers must approve the change, with client, security or financial review where applicable. Update the contract, versioned matrix, roadmap, acceptance register and status together as affected. An agent must not silently remove requirements or weaken gates to report completion. Reviewer appointments and policy details are outstanding decisions in [STATUS](STATUS.md).

## Proposed technical direction

- Modular application with clear domain boundaries.
- React/TypeScript frontend and Python/FastAPI backend.
- PostgreSQL transactional storage.
- Exact monetary arithmetic with explicit currency, conversion and rounding policy.
- Object storage for preserved source evidence and columnar analytical data.
- Durable separate workers for collection and longer-running processing.
- Established identity-provider integration.
- Provider-independent financial core with versioned source adapters.
- Deterministic calculations, with AI restricted to evidence-grounded assistance.
- Separate read and execution security boundaries.

These are starting decisions for M01 architecture review, not permission to scaffold the stack now. Hosting, vendors, versions, thresholds and final designs remain to be reviewed; no dependency installation or infrastructure provisioning is authorised by this document.

## Definition of product acceptance

100% means all mandatory release requirements are satisfied with appropriate engineering and real-world evidence for the full agreed, versioned scope. It does not mean zero possible defects, positive savings for every customer, passing mock tests alone or the coding agent declaring itself finished.

Acceptance requires traceable requirement evidence, reproducible financial results, demonstrated security and operational controls, and recorded review by designated human product, engineering, security, financial and representative client stakeholders as applicable. M21 requires the full release evidence; M12 is only the pilot gate. The initial [acceptance register](ACCEPTANCE.md) must be expanded through design review and does not assert full requirements coverage or security certification.
