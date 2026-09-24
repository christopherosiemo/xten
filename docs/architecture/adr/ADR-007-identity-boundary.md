# ADR-007 — External authentication and XTEN decision rights

**Status: PROPOSED**
**Date: 2026-09-24**

## Context

XTEN serves consultancy staff and multiple client roles. Authentication of a person is different from client membership, role, approval rights and cloud execution authority. Building a password service would add unnecessary security scope.

## Decision

Delegate primary authentication to a standards-based external identity provider through OIDC/OAuth-compatible integration; do not build an XTEN password database. XTEN stores provider issuer/subject mapping, tenant Memberships, application Roles, decision-right policies and audit history. Server validates token issuer/audience/signature/expiry and resolves current tenant membership for each request. Business approval requires an explicit right and target/version/transition check; cloud execution authority is separate and inactive until M19. A single User may have memberships in multiple tenants, selected explicitly per operation. Enterprise SSO/SAML may be supplied by the future IdP, but no commercial IdP is selected now.

## Alternatives considered

XTEN-managed passwords were rejected because they enlarge credential and recovery risk. Trusting IdP group claims as the sole tenant/approval model was rejected because engagement-specific rights and revocation must be controlled by XTEN. Equating login or client-admin status with execution was rejected.

## Consequences

Identity-provider availability and claim changes need session/refresh policy and fail-closed behaviour. Tenant invitations, revocation and decision rights are XTEN workflows and must be tested separately.

## Security implications

Authentication, tenant membership, application role, business approval authority and cloud execution authority are distinct. Token/subject mapping is validated, sessions and role grants can be revoked, and audit omits raw tokens. Support impersonation, if ever allowed, needs a separate reviewed design.

## Financial/data-integrity implications

Only an explicit finance/decision right can approve a baseline or financial policy change. A report viewer or authenticated user cannot manufacture an approval or measured benefit.

## Operational implications

IdP outage, key rotation, subject reassignment, invitation lifecycle and deprovisioning need observable fail-closed handling; provider selection and exact session controls remain M01/M02 work.

## Requirements/milestones affected

M01, M02, M03, M09, M11, M19; REQ-003, REQ-004, REQ-020, REQ-022, REQ-027, REQ-029, REQ-041.

## Conditions that would justify revisiting the decision

Enterprise requirements or threat-model evidence demand different federation/session controls; any change must preserve XTEN-owned tenant rights and independent execution authority.

This ADR is proposed M01-T01 architecture work, not a human acceptance decision. See the [architecture index](../README.md), [system architecture](../SYSTEM_ARCHITECTURE.md) and [acceptance register](../../ACCEPTANCE.md).
