# XTEN system architecture

**Status:** PROPOSED · **Date:** 2026-09-24 · **Scope:** M01-T01. This design refines the accepted [M00 product contract](../PRODUCT.md) and must be reviewed with the [ADRs](README.md) before implementation. No component described here is implemented.

## 1. Scope and architectural goals

XTEN is a multi-tenant SaaS control plane for consultancy-led FinOps engagements. The first production deployment is intended to be hosted on AWS, while customer connections and the canonical financial/domain model remain provider-independent. The initial server shape is a modular monolith with separately runnable web, API and worker processes. One coherent domain model and versioned internal contracts govern them.

The architecture must preserve tenant isolation, source provenance, deterministic money, human decision rights, durable work and reproducible reports. The read-only/advisory service is the initial authority envelope. Later execution is a new security boundary, not a property of collection. The service design must support the full lifecycle in [PRODUCT](../PRODUCT.md); a restricted AWS pilot cannot stand for full product acceptance.

## 2. M01-T01 non-goals

This task does not finish the finite provider support matrix, provider-by-provider field mappings, complete threat model, data classification or retention/residency policy, numeric SLO/RPO/RTO/performance targets, final hosting region, dependency versions, application scaffolding, or any real integration. M01 work must complete those decisions before REQ-001 or affected capabilities can be accepted. No warehouse or identity vendor is selected here.

## 3. System context

| Actor or external system | Relationship and boundary |
| --- | --- |
| XTEN operator | Runs assigned consultancy engagements and support within separately granted tenant authority. |
| XTEN analyst | Interprets reconciled data and prepares evidenced recommendations; cannot make facts authoritative by assertion. |
| XTEN engineer | Assesses change risk and coordinates implementation; collector access does not confer execution rights. |
| Client users | Administrators, finance users, engineering users and executive viewers act within their own tenant memberships and explicit decision rights. |
| External identity provider | Authenticates human identities through OIDC/OAuth-compatible integration; XTEN decides application membership and authority. |
| Customer cloud environments | Supply explicitly authorised billing and operational evidence via scoped provider adapters and temporary read credentials. |
| Future ticket/change integrations | Receive selected, authorised workflow records; integration side effects must be idempotent and audited. |
| XTEN control plane | Owns engagements, memberships, connection definitions, workflows, financial policy, audit and publication decisions. |
| XTEN data/collection processes | Fetch, preserve, normalise and analyse tenant evidence through scoped durable jobs; they have no generic infrastructure write capability. |
| Future execution plane | M19 boundary only; inactive and unimplemented in this design, with separate identity, credentials and approval binding. |
| Report/export consumers | Receive immutable, tenant-authorised published artifacts; links or filenames are not themselves authority. |

Trust crosses the public network, identity boundary, XTEN application boundary, tenant storage boundary and provider/customer cloud boundary. Diagram arrows show proposed logical flows, not deployed services.

```text
Users → Web → API/control modules → PostgreSQL (state, lineage, outbox, audit)
                     │                   ↓
                     ├→ durable queue → workers → provider read adapters → customer clouds
                     │                   └→ immutable evidence / columnar files → query adapter
                     └→ reporting → published snapshot / export → authorised consumer
External IdP → authentication boundary → XTEN membership/decision rights
Secrets manager → scoped worker/provider credentials
Future execution plane [INACTIVE; separate M19 authority]  × collector credentials
```

## 4. Logical component model

Each row states positive and forbidden access, trust assumption, and interaction mode. An API, worker or query engine must never acquire authority merely because it can address a storage path or message.

| Component | Responsibility | May access | Must not access | Principal trust assumption and interaction |
| --- | --- | --- | --- | --- |
| Web client | Render role-scoped workflows and reports, submit user intent. | User-visible tenant data returned by API; ephemeral session state. | Provider credentials, cross-tenant raw objects, privileged DB connections. | Browser is untrusted; synchronous API calls require server-side reauthorisation. |
| Application/API | Validate requests, orchestrate use cases and publish tenant-scoped responses. | Authorised transactional records, lineage metadata, scoped object/query handles through modules. | Unscoped tenant data or execution credentials. | Authenticated calls are still untrusted input; synchronous request boundary, asynchronous work via outbox. |
| Authentication integration | Validate IdP tokens/session claims and map external subject. | Token metadata, IdP keys/discovery, subject mapping. | Password database or cloud execution credentials. | Trust only validated issuer/audience/signature/expiry; synchronous login/token validation; refresh metadata separately. |
| Tenant authorisation module | Resolve membership, tenant scope, role and decision rights per operation. | Membership and policy state, engagement decision rules. | Authority from a bare tenant ID, URL, storage path or audit event. | Fail closed on absent/expired membership; synchronous guard on API and worker operations. |
| Core domain modules | Own tenancy, engagement, connections, ingestion, allocation, recommendations, workflow, measurement and reporting invariants. | Their tenant-scoped records and versioned contracts. | Provider SDK internals, arbitrary cross-tenant tables, raw credentials. | Module boundaries are enforced by APIs and review; sync domain commands, async jobs for long work. |
| Financial-calculation module | Reconcile, allocate and measure with exact, versioned policies. | Explicit currency/cost-basis inputs, canonical datasets, policy versions. | Binary float as authoritative money, LLM output as financial fact, implicit FX. | Deterministic pure calculations where possible; sync small calculations, async large batches. |
| Provider adapters | Translate selected versioned provider exports/operations into canonical inputs. | Supported provider read responses, scoped temporary credentials, source metadata. | Domain workflow writes, generic customer write APIs, other tenants' credentials. | Untrusted provider data and service-specific semantics; worker-invoked async collection. |
| Ingestion coordinator | Register deliveries, evidence identities, validation and stage transitions. | Tenant/source metadata, artifact hashes, dataset versions, outbox. | Silent mutation of prior evidence or publication snapshots. | Transactions bind stage state to outbox; async worker orchestration. |
| Collection workers | Fetch supported data using connection and CloudScope. | Own job scope, temporary read credential, tenant-qualified evidence destination. | Broad customer write permissions, execution-plane identity, unrelated tenant jobs. | Recheck revocation and lease/tenant scope on each attempt; async durable jobs. |
| Durable queue | Deliver scoped job references with retries. | Job identity, tenant/operation scope, opaque references and timing. | Raw secrets or authoritative financial results in message payloads. | Delivery is at least once; consumers revalidate state and idempotency. |
| PostgreSQL transactional database | Hold control state, memberships, policies, lineage indexes, workflow, result metadata, audit and outbox. | Explicitly tenant-scoped transactional rows via restricted roles. | Sole copy of high-volume raw exports or an unrestricted runtime owner role. | Database grants plus RLS and application checks; synchronous transactions, async outbox dispatch. |
| Object/evidence storage | Preserve source artifacts, provider exports, canonical/columnar files and published artifacts. | Tenant-qualified immutable/versioned objects with hashes and provenance. | Cross-tenant listing/download by path possession alone; plaintext secrets. | Server-mediated authorisation and scoped service identity; async writes/reads, signed retrieval only after authorisation. |
| Analytical query abstraction | Query versioned columnar datasets and return tenant-constrained typed results. | Registered tenant partitions and approved dataset versions. | Arbitrary buckets, unrestricted SQL or other tenant partitions. | Query vendor is replaceable; server binds tenant, dataset/version and permitted query shape; sync preview or async large query. |
| Reporting engine | Render deterministic report snapshots from approved versions. | Result versions, lineage manifest, template, tenant branding and authorised evidence references. | Live mutable values when reproducing a publication; another tenant's template/data. | Async generation and atomic publication; retrieval is synchronous and separately authorised. |
| Audit subsystem | Record security/finance decisions and system actions with actor, tenant, scope and correlation. | Minimal event metadata and safe references. | Secrets, duplicated raw sensitive payloads, authority grants from event presence. | Append-oriented durable record; transactionally coupled to state changes where needed, async export to monitoring. |
| Secrets-management boundary | Supply scoped short-lived service/provider material to authorised components. | Secret references, policies, rotation metadata and key service. | Source code, ordinary application tables or user-facing logs. | Separate service identity and least privilege; access at use time, with audit. |
| Observability boundary | Correlate health, traces, job failures and tenant-safe operational metrics. | Sanitised events, trace IDs, aggregate counters. | Credentials, raw customer records, cross-tenant payloads in public logs. | Operational telemetry is not a financial source of truth; async collection and controlled support access. |
| Future execution plane | Reserve M19 action gateway with distinct credentials, approvals and allowlist. | Nothing active in M01; future bound approval and action context only after M19 design. | Collection credentials or implied authority from recommendations. | INACTIVE / NOT IMPLEMENTED; no runtime process, queue consumer or credential is provisioned. |

Internal modules exchange typed/versioned domain contracts rather than reaching into another module's private tables. Ownership of a transaction is explicit; workflows coordinate cross-module steps via commands and events. Extraction into separate services later would require a new ADR, new failure/consistency analysis and preserved tenant/financial invariants.

## 5. Deployment-process model

The preferred first implementation is a React/TypeScript web experience, Python/FastAPI API, Python workers, PostgreSQL, S3-compatible object storage, a durable managed queue, managed secrets/key services and a replaceable analytical query adapter. These are architecture choices for later review; M02 pins versions and proves a clean setup. AWS hosting of XTEN does not make AWS the only customer provider. No exact SKU, region, deployment platform or vendor for identity/query is chosen.

Web serving and API execution should be separately scalable because browser assets and authenticated domain requests have different release and trust surfaces. Workers run separately because collection and large analysis can outlive HTTP requests, need bounded provider permissions and need retry/crash isolation. The outbox dispatcher may be a dedicated process or a bounded worker role, but its transactions and replay semantics remain explicit. Reporting may run in workers behind the same domain contracts. The logical modular monolith can share a codebase/release train without sharing process memory as workflow state.

PostgreSQL stores transaction/control truth. Object storage holds immutable source and analytical files. A replaceable query engine reads registered, tenant-scoped columnar datasets through an adapter; domain modules never embed vendor SQL as their contract. The queue holds work references, while durable job and idempotency state live in transactional storage. Managed secrets and keys issue narrowly scoped service access; no process receives all customer provider credentials.

## 6. Trust zones

| Zone | Boundary and enforcement expectation |
| --- | --- |
| Public/untrusted client network | Treat requests, files and browser state as hostile; API validates authentication, input and operation scope. |
| Authenticated XTEN user | IdP authentication identifies a subject, not tenant membership, approval or execution authority. |
| Application/control plane | Authorisation module and domain invariants gate every command; state changes and audit/outbox are transactional. |
| Tenant data | Explicit tenant IDs, grants and PostgreSQL RLS; tenant-qualified object namespaces with server-side authorisation; tenant-bound reports/exports and cache keys. |
| Worker | Scoped job payload and service role; reload current authorisation/revocation; no cross-tenant ambient context or process-memory workflow state. |
| Provider/customer cloud | Untrusted external data and separately authorised short-lived read access; adapter enforces finite support scope. |
| Secrets | Distinct access policies, secret references and rotation; no raw secrets in DB rows, jobs, source files or logs. |
| Administrator/migration | Distinct privileged identities for schema ownership, data repair and break-glass; human approval, time-bound use and audit; not the API runtime role. |
| Future execution authority | Separate M19 identity/credential, approval and action allowlist boundary; absent in initial deployments. |

The runtime DB identity is a non-owner role, not a superuser or BYPASSRLS role. Tenant-owned PostgreSQL tables use explicit tenant IDs and RLS as additional defence; FORCE ROW LEVEL SECURITY is expected on them so accidental owner-based application access cannot bypass policies. Owner/migration identities remain separately privileged and controlled, so FORCE is not sufficient for privileged-role isolation. The threat model must verify role grants, session tenant context, connection pooling and support/admin access; RLS is never the sole control. External identifiers use standard non-sequential UUID/ULID-class IDs without a custom cryptographic scheme.

## 7. Primary data flows

Each flow is scoped to a resolved tenant and correlation ID. Tenant IDs supplied by clients are selectors to validate, never proof of authority. Audit records are durable references, not grants.

### A. User authentication and tenant selection

1. The browser redirects to the external IdP; API validates returned OIDC/OAuth-compatible claims, maps provider subject to User identity and establishes a session. Token validation/audit records authentication outcome without storing token secrets.
2. The user selects a tenant; the API checks active Membership, Role/decision rights and engagement scope before returning tenant data. Denials and tenant switches generate tenant-aware audit events.
3. Subsequent API calls carry server-validated tenant context; workers and reports receive explicit tenant scope, never implicit browser choice.

### B. Client onboarding

1. An authorised operator proposes a Tenant and Engagement; tenancy module creates non-sequential IDs and records service entitlement, decision owners, consent/data-handling status and audit event.
2. A client administrator is invited through identity integration; memberships are activated only after explicit tenant acceptance and role checks. CloudConnection remains inactive pending client authorisation.
3. Onboarding records and audit point to the responsible tenant; no provider access or collection job is created merely by opening an engagement.

### C. Cloud collection

1. An authorised client administrator records CloudConnection, CloudScope, read purpose and permission proof for one tenant. A scoped job is written with state/outbox; audit records the grant.
2. Worker rechecks tenant, connection state, supported scope and revocation, obtains short-lived read-only provider credentials through secrets boundary, and calls the versioned adapter. AWS direction is cross-account assumed role with a customer-specific external ID for confused-deputy protection; the ID is not treated as a password.
3. Fetched objects receive tenant-qualified immutable storage keys, hashes and CollectionRun provenance; failure/revocation is audited and stops future reads. No collector receives customer infrastructure write authority.

### D. Source ingestion

1. Delivery manifest/source objects are registered as new SourceArtifact versions under tenant and connection; validate size, format, hash and delivery identity without accepting arbitrary storage paths.
2. Ingestion records SourceRecordIdentity using provider/delivery semantics so replay is idempotent while distinct identical-valued records survive.
3. Transactional dataset/outbox state links source and parser version; partial failure leaves a visible quality state and retryable operation. Audit notes ingestion and any correction relationship.

### E. Normalisation

1. A tenant-scoped job selects immutable source versions and a versioned provider adapter/FOCUS-aligned mapping. The adapter preserves raw fields needed for reconciliation and unsupported semantics.
2. Financial core creates a new CanonicalCostDataset version with currency, cost basis, period, record identity and lineage; invalid/missing inputs become findings, not zeros.
3. Quality checks and reconciliation results are recorded; canonical version is published for downstream use only after its declared validation gate. Corrections create new versions and do not mutate historical report inputs.

### F. Analysis and recommendation creation

1. An authorised analyst selects tenant, approved canonical/baseline and mapping/allocation versions; a durable analysis job records those inputs.
2. Deterministic calculations produce versioned derived results, commitment-aware estimates and quality limits. RecommendationEvidence links the source/calculation/assumptions; overlaps and unknown ownership remain explicit.
3. Reviewer actions create Recommendation states and audit. AI may assist with wording or evidence navigation but cannot create authoritative numbers or approval.

### G. Approval/workflow change

1. API validates tenant membership, decision right, current Recommendation and transition preconditions, including stale versions.
2. A transaction records Approval or rejection/deferment reason, separate ImplementationRecord intent and audit/outbox event. No estimate becomes an implementation fact by status change.
3. Selected future ticket/change integration workers may deliver idempotent scoped updates; denied or failed transitions leave an auditable failure and do not create cloud execution authority.

### H. Report publication

1. An authorised reviewer selects tenant, approved result/baseline, source and policy versions, mapping/allocation versions, template version and intended recipients.
2. Reporting job renders candidate artifact and manifest; it verifies tenant, reconciled values, hash and access classification before atomic publication of an immutable ReportSnapshot with publication identity/time.
3. Transactional report metadata, audit and outbox link the exact evidence set. Later data corrections produce a new report version or amendment, never silently overwrite the published one.

### I. Report retrieval

1. Client user requests a specific ReportSnapshot or Export; API rechecks current tenant membership, role, engagement and retention access.
2. API returns a scoped short-lived retrieval handle or streams the immutable artifact; object path knowledge alone cannot bypass authorisation.
3. Access audit records actor, tenant, artifact/version and result while omitting report contents and secrets.

### J. Future execution request boundary

1. Initial product rejects any execution request, even if a recommendation is approved; no active execution job, worker, credential or route exists.
2. M19 must define a separate action allowlist, target/scope-bound approval, expiry/replay protection, distinct service identity and audit/rollback before enabling any route.
3. Collector tokens, cloud read roles, job permissions and audit records can never be upgraded into execution authority by the control plane.

## 8. Data lifecycle and lineage

- **SOURCE:** Immutable logical evidence, including provider deliveries, manifests and raw values. A correction or late delivery adds a new artifact/delivery version with hash, origin, capture time and supersession relationship; original bytes remain addressable subject to a later approved retention policy.
- **CANONICAL:** Versioned, FOCUS 1.4-aligned normalisation of supported source semantics, with exact money, provider-specific extensions, record identity, parser version and quality/reconciliation findings. FOCUS alignment is a reference model, not a claim that any provider export is identical or conformant.
- **DERIVED:** Versioned allocations, baselines, opportunities, forecasts and measurements tied to canonical dataset, policy, mapping and calculation versions. These are recomputable results; missing data remains unknown.
- **PUBLISHED:** Immutable ReportSnapshot/Export manifests and artifacts bind source, canonical and derived versions, policy/calculation/mapping/template versions, publication identity and time. A later correction requires a new publication or explicit amendment.

Lineage is a tenant-scoped directed graph of versioned artifacts/records/results; every derived or published amount can trace to source evidence and transformation policy. Source and publication identities cannot be reassigned between tenants. Data deletion/retention, residency, legal holds, backup treatment and cryptographic destruction remain open M01 policy decisions; the design must support tenant-scoped offboarding and prevent broken published evidence chains until policy resolves them. Evidence retention is not a license to store real customer data in this repository.

Authoritative financial values use PostgreSQL NUMERIC/DECIMAL and application decimal arithmetic. Binary floating point is prohibited for authoritative money. Every amount retains currency, cost basis/type, period and source/calculation version where applicable; cross-currency totals require an explicit versioned FX policy. M01-T02 must define exact rounding and conversion rules before financial calculations can be accepted.

## 9. Failure model

| Failure | Required behaviour and visible evidence |
| --- | --- |
| Retry or duplicate delivery | Stable provider/delivery-aware identity and idempotency key make retries safe; identical financial values with distinct source identities remain distinct. Record retry/duplicate classification. |
| Partial ingestion | Persist stage/checkpoint and quality finding; never mark incomplete totals as complete or publish them as final. Retry from durable state or quarantine invalid input. |
| Worker crash | Lease expires; another worker resumes the durable job with tenant scope and idempotency. Transactional state decides completion, not process memory. |
| Out-of-order or corrected data | Add versioned evidence and recompute affected canonical/derived views; mark stale reports and publish explicit revisions when approved. |
| Unavailable provider API | Bounded retries and backoff, then visible blocked/failed CollectionRun; previous source remains labelled with age. No invented data. |
| Stale data | Show last successful collection, coverage and quality; suppress or qualify recommendations/financial conclusions as policy requires. |
| Report-generation failure | Candidate is not published; retry from bound input versions; retain failure/audit and avoid partially visible exports. |
| Permission revocation | Recheck at dispatch/use; stop future collection, revoke cached temporary access where possible, fail pending jobs safely and audit. |
| Dependency outage | API fails closed for authority checks; queue/DB/object/query outages leave durable pending state and visible health signals, not false success. |
| Outbox/queue split | Outbox and state commit together; dispatcher retries publish; consumer deduplicates. At-least-once delivery, not exactly-once execution. |

Error references and telemetry avoid customer payloads and secrets. Numeric recovery and availability targets remain an M01-T04 decision; M20 must exercise restore.

## 10. Security architecture assumptions for later threat modelling

The threat model must test malicious tenants/users, compromised worker or support identities, forged or stale IdP claims, tampered provider deliveries, confused-deputy cloud role use, cross-tenant object/query/report access, leaked signed URLs, prompt injection, outbox replay, report tampering and privilege escalation through migrations. Expected defences are server-side authorisation, tenant-scoped DB/RLS and object/query policies, narrowly scoped service identities, short-lived read credentials, secret isolation, immutable evidence hashes, audit and fail-closed state transitions. No claim of security certification follows from documenting these controls.

Admin/migration identities can bypass normal runtime boundaries and therefore require separate credentials, approval, logging and environment access. Audit records are append-oriented evidence of decisions, never a source of authority. Support tools must use tenant-scoped, time-limited access and log the reason. Exact data classification, retention, key policy, penetration-test scope and threat mitigations belong to M01-T03.

## 11. Scaling model

Size by tenants, connected accounts/subscriptions/projects, billing rows and correction frequency, resource inventory, metric cardinality, users/concurrent queries, queued jobs, and report generations/artifact size. Scale API and worker replicas independently within the modular architecture; partition analytical files by tenant, provider/dataset and billing period/version as appropriate, retaining tenant-bound query authorization. Bound queue concurrency and provider rates per tenant/connection; avoid one large tenant starving others. Keep transaction tables indexed for control-plane access and move high-volume scans to columnar datasets/query adapter.

Customer spend is not a sufficient proxy for processing load: a small spend account can have many line items, resources or metrics, while a large commitment can produce few records. M01-T04 must define measurable workload shapes, latency/throughput, recovery and operational targets and how to test them before affected capabilities are accepted.

## 12. Open decisions reserved for later M01 tasks

- M01-T02: finite, versioned AWS/Azure/Google support entries, pilot subset, provider source and field mappings, FOCUS 1.4 alignment/differences, exact currency/rounding/FX and reconciliation policies, commitment semantics and import contracts.
- M01-T03: complete threat model, data classification, retention/deletion/residency and backup/legal-hold treatment, privileged support access, key/secret policy and abuse-case mitigations.
- M01-T04: numerical SLO/RPO/RTO, workload/performance/recovery/security targets, test environment and measurement method, architecture-to-requirement traceability and final M01 review package.
- Owner decisions before M02 proprietary code: repository visibility/licensing, branch protection/review policy, hosting region and commercial/operational approvals. No setting or provider account is changed by M01-T01.

## Authoritative external baselines checked on 2026-09-24

- [FOCUS Specification 1.4](https://focus.finops.org/docs/specification/v1-4/) is the initial FOCUS-aligned reference. Provider exports and FOCUS-aligned canonical data may differ; adapter-specific gaps and original fields remain explicit.
- [AWS Data Exports CUR 2.0 guidance](https://docs.aws.amazon.com/cur/latest/userguide/dataexports-migrate.html) is the AWS detailed billing direction. [AWS export delivery guidance](https://docs.aws.amazon.com/cur/latest/userguide/dataexports-export-delivery.html) describes manifests and delivery partitioning; exact supported configuration awaits M01-T02.
- [AWS third-party cross-account role guidance](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_common-scenarios_third-party.html) and [confused-deputy guidance](https://docs.aws.amazon.com/IAM/latest/UserGuide/confused-deputy.html) support the assumed-role and customer-specific external-ID direction.
- [Microsoft Entra workload identity federation](https://learn.microsoft.com/entra/workload-id/workload-identity-federation) and [Google Cloud Workload Identity Federation](https://docs.cloud.google.com/iam/docs/workload-identity-federation) show short-lived federated approaches to consider for future provider adapters; supported scopes remain open.
- [OpenCost specification](https://opencost.io/docs/specification/) is the vendor-neutral conceptual baseline for later Kubernetes allocation, not an implemented input in M01.
- [PostgreSQL row security documentation](https://www.postgresql.org/docs/current/ddl-rowsecurity.html) describes owner and BYPASSRLS behaviour and FORCE ROW LEVEL SECURITY; the chosen PostgreSQL version and runtime grants must be verified in M02.

These references must be rechecked against authoritative documentation and versioned support entries at implementation time; they are architecture baselines, not conformance or connector evidence.
