# WORKSPACE_MEMBERSHIP_LIFECYCLE_SPEC

## Title
FUZE Workspace Membership Lifecycle Specification

## Document Metadata

- Document Name: `WORKSPACE_MEMBERSHIP_LIFECYCLE_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Version: 1.2.0
- Effective Date: 2026-04-21
- Last Updated: 2026-04-21
- Reviewed On: 2026-04-21
- Document Owner: FUZE Platform Workspace Membership Domain
- Approval Authority: FUZE Platform Architecture and Governance Authority
- Review Cadence: Quarterly or upon material change to workspace semantics, organization semantics, invitation and admission behavior, authorization ordering, owner continuity posture, privileged correction workflows, entitlement coupling, or audit/traceability requirements
- Governing Layer: Platform core / workspace membership lifecycle
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, security engineering, support operations, audit, governance, API design, platform operations, reliability engineering, implementation-contract authors
- Primary Purpose: Define the canonical FUZE lifecycle for workspace membership from invitation and admission through activation, restriction, suspension, removal, leave, reinstatement, correction, and historical retention without collapsing identity, workspace structure, authorization, entitlement, billing truth, or product-local participation into one ambiguous layer
- Primary Upstream References:
  - `REFINED_SYSTEM_SPEC_INDEX.md`
  - `DOCS_SPEC_INDEX.md`
  - `SYSTEM_SPEC_INDEX.md`
  - `API_SPEC_INDEX.md`
  - `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
  - `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
  - `PLATFORM_ARCHITECTURE_SPEC.md`
  - `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
  - `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
  - `IDENTITY_AND_ACCOUNT_SPEC.md`
  - `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
  - `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
  - `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
  - `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
  - `WORKSPACE_AND_ORGANIZATION_SPEC.md`
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
  - `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - `AUTH_IDENTITY_API_SPEC.md`
  - `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
  - `WORKSPACE_ORGANIZATION_API_SPEC.md`
- Primary Downstream Dependents:
  - `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
  - `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
  - `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
  - `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
  - `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
  - `WORKSPACE_ORGANIZATION_API_SPEC.md`
  - `ROLE_PERMISSION_ACCESS_API_SPEC.md`
  - product integration specifications
  - support and control-plane workflow specifications
  - audit, security, and reporting specifications
- Supersedes: Earlier or less explicit FUZE membership, invitation, team-member, collaborator, workspace-user, and provisioning writeups to the extent they conflict within this document’s scope
- Superseded By: Not yet known
- Related Decision Records: Not yet known
- Canonical Status Note: This document is the governing FUZE workspace membership lifecycle specification. Downstream APIs, services, products, control-plane tools, support workflows, and reports MUST NOT reinterpret the lifecycle, truth ownership, transition ordering, or correction rules established here.
- Implementation Status: Normative architecture baseline; downstream API, event, storage, runtime, audit, security, and control-plane contracts must conform
- Approval Status: Drafted for refined-system inclusion; formal approval record not yet attached
- Change Summary:
  - normalized the document to the refined system-spec metadata structure
  - tightened the separation between invitation truth, membership truth, authorization truth, entitlement truth, and derived reporting truth
  - clarified owner-continuity safeguards, wrong-account acceptance handling, reinstatement/correction lineage, and default decision rules for contested transitions
  - strengthened implementation-contract guardrails around idempotency, replay safety, auditability, read-model constraints, and degraded-mode behavior
  - aligned this specification more explicitly with the refined workspace, authorization, effective-permission, and audit/traceability layers

## Purpose

This specification defines the canonical FUZE model for workspace membership lifecycle.

Its purpose is to make explicit:

- what workspace membership is and what it is not
- how invitation, admission, acceptance, activation, restriction, suspension, removal, leave, reinstatement, and correction are related but not conflated
- how membership binds canonical account identity to canonical workspace scope
- how membership state influences downstream authorization, entitlement consumption, product participation, billing-adjacent calculations, and audit traceability without becoming those domains
- how owner continuity, control-path continuity, and resource continuity must be preserved during membership mutation
- how APIs, events, storage, audit records, security controls, and operations must coordinate around membership lifecycle changes

FUZE is a multi-product collaborative platform. In such a platform, membership cannot be treated as a login side effect, a transient UI state, a product-local participant table, or an informal list of collaborators. Membership is a durable platform-owned relationship between a canonical account and a canonical workspace. It therefore carries security, operational, commercial, and audit implications that downstream systems must consume without redefining.

## Scope

This specification governs:

- the canonical meaning of workspace membership
- invitation-to-membership transition rules
- membership state semantics and transition ordering
- join, accept, activate, restrict, suspend, remove, leave, re-invite, reinstate, and corrective workflows
- owner-continuity and minimum-control-path implications of membership mutation
- interaction between membership lifecycle and downstream role assignment, scoped authorization, effective permission, entitlement consumption, product participation, and audit lineage
- API, event, data-model, security, operational, and migration implications of membership lifecycle truth
- historical retention and reconstructability requirements for membership-related records

This specification does not define in full depth:

- canonical account identity creation or provider resolution
- session issuance, refresh, or revocation semantics
- full workspace or organization lifecycle semantics
- detailed role catalogs or permission tables
- final action-level effective-permission algorithms
- exact entitlement, seat, invoice, or pricing formulas
- exact invite transport implementation and UX copy
- exact enterprise directory-sync implementation details
- exact reviewer staffing procedures or ticket-routing logic

Those concerns belong to adjacent or downstream specifications and must remain compatible with this document.

## Out of Scope

This specification is explicitly out of scope for:

- canonical account creation and identity bootstrap
- login method lifecycle and linked-provider lifecycle in full depth
- session runtime truth as a standalone domain
- product-local collaboration records that do not claim canonical workspace-membership meaning
- exact object-level access evaluation
- exact commercial counting formulas for seat billing or invoice generation
- exact legal/compliance acceptance flows unless later brought into scope explicitly
- exact UI implementation for invite emails, acceptance pages, or membership lists

## Design Goals

1. Make workspace membership a durable, explicit, platform-owned relationship.
2. Preserve strict separation between canonical account identity and collaborative membership.
3. Preserve strict separation between workspace scope, membership truth, authorization truth, entitlement truth, and reporting truth.
4. Support the full practical lifecycle from invitation through exit and remediation.
5. Prevent wrong-account admission, duplicate membership semantics, and hidden identity creation shortcuts.
6. Preserve owner continuity, control-path continuity, and shared-resource continuity during membership mutation.
7. Support restriction, suspension, removal, and correction without destructive rewrite.
8. Make downstream API, storage, runtime, and audit implementations deterministic and easier to align.
9. Support future-safe enterprise provisioning, policy tightening, and product expansion.
10. Prevent product-local systems from becoming shadow owners of canonical workspace membership.

## Non-Goals

This specification is not intended to:

- treat membership as final permission by itself
- treat invitation delivery as active membership
- treat current workspace selection as proof of membership
- let product-local participant lists replace canonical membership
- define every downstream authorization rule that consumes membership
- define every commercial consequence of member-count changes
- permit destructive rewrite of high-impact historical membership lineage

## Core Principles

### 1. Identity / Membership Separation Principle
The account is the canonical actor identity. Membership is the canonical account-to-workspace relationship. These truths are adjacent and connected, but they MUST NOT be collapsed.

### 2. Membership Is Durable Structure Principle
Membership is a durable platform record, not a transient frontend state, not a convenience cache, and not a soft convention.

### 3. Invitation / Membership Distinction Principle
An invitation is a pending admission artifact. It is not itself active membership.

### 4. Activation Before Authority Principle
Until membership becomes active enough for downstream use, workspace-scoped authority MUST NOT be inferred.

### 5. Membership Before Authorization Principle
Membership state is a prerequisite input for many workspace-scoped authorization paths, but membership does not by itself equal permission.

### 6. Restriction Precedence Principle
Restricted, suspended, removed, or otherwise non-active membership states suppress ordinary participation even where stale grants, caches, or product-local views suggest otherwise.

### 7. Continuity Preservation Principle
Membership mutations MUST preserve workspace continuity, owner continuity, resource continuity, and audit lineage.

### 8. No Hidden Identity Creation Principle
Invitation acceptance, provisioning, reinstatement, or correction MUST bind to canonical account identity and MUST NOT silently create duplicate, replacement, or shadow identity.

### 9. Explicit Remediation Principle
Wrong-account acceptance, mistaken removal, stranded ownership, ambiguous admission, and unsafe historical drift MUST route through explicit corrective workflows.

### 10. Product-Consumption Principle
Products consume canonical membership truth. They do not redefine it.

## Canonical Definitions

### Workspace Membership
The durable canonical relationship between a canonical account and a canonical workspace.

### Invitation
A pending admission artifact intended to allow a qualified actor to join a workspace through controlled binding to canonical account identity.

### Admission
The platform-owned process by which the system creates or validates the path from invitation or provisioning intent toward canonical workspace membership.

### Membership Activation
The transition by which a pending or invited relationship becomes active enough to support ordinary downstream collaboration and authorization input.

### Membership Restriction
A state in which membership still exists structurally and historically but ordinary participation is partially or fully constrained.

### Membership Suspension
A stronger operational, security, abuse, or policy limitation that suppresses ordinary collaboration while preserving record continuity and future review or reinstatement possibilities.

### Membership Removal
A state transition in which an account is no longer an active member of the workspace due to authorized actor action or policy-driven removal.

### Membership Leave
A state transition initiated by the member’s own exit from the workspace where policy allows.

### Membership Reinstatement
A controlled transition restoring active participation from a restricted or suspended posture, or from another explicitly permitted recoverable state.

### Membership Correction
An explicit remediation workflow used when prior admission, acceptance, removal, or ownership-sensitive mutation was wrong, unsafe, or incomplete.

### Membership Provenance
The durable lineage showing how the membership was created, invited, accepted, provisioned, activated, restricted, removed, left, or corrected.

## Truth Class Taxonomy

Downstream implementations MUST preserve the following truth distinctions.

### 1. Canonical Identity Truth
The durable actor anchor represented by `account_id`. Membership consumes identity truth but does not define it.

### 2. Runtime Session Truth
Temporary authenticated runtime presence. Sessions may be prerequisites for interactive membership mutations or acceptance, but session truth is not membership truth.

### 3. Collaborative Scope Truth
Canonical workspace and organization records, hierarchy, ownership structure, and lifecycle posture owned by the workspace/organization domain.

### 4. Membership Truth
The durable relationship between canonical account identity and workspace scope, including invitation linkage, membership state, and transition lineage.

### 5. Authorization Truth
Roles, permissions, scoped grants, and effective-permission outcomes consumed after scope and membership are resolved.

### 6. Entitlement Truth
Commercial or policy eligibility for products or capability classes. Entitlement may use membership as an input but MUST NOT redefine it.

### 7. Security / Restriction Truth
Security, abuse, review, containment, and higher-order policy posture that may constrain or override ordinary membership flows.

### 8. Derived Read-Model Truth
Support views, dashboards, search indexes, analytics summaries, member lists cached by products, and UX summaries derived from canonical records.

### 9. Reporting / Public View Truth
Reports, exported lists, public or customer-facing surfaces, and summarized communication artifacts. These remain downstream presentations, not membership mutation owners.

## Architectural Position in the Spec Hierarchy

This document sits below:

- `REFINED_SYSTEM_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`

and above:

- `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- product integration specifications

This document owns membership lifecycle structure, transition semantics, mutation guardrails, and reconstruction requirements. It does not absorb full ownership of workspace existence, final access evaluation, entitlement logic, billing formulas, or generic reporting.

## System Boundaries

This document governs only the following platform-owned boundaries:

- canonical workspace-membership records
- invitation-to-membership transitions
- membership state semantics
- lifecycle transitions for invite, join, activation, restriction, suspension, removal, leave, reinstatement, and correction
- owner-safety interactions and minimum-control-path safeguards for membership mutation
- durable provenance and audit lineage for membership mutation
- membership prerequisites for downstream authorization and product participation
- canonical separation between membership truth and downstream derived views

It does not govern:

- account identity truth
- session runtime truth
- workspace structural existence in full depth
- role and permission definitions in full depth
- exact commercial seat and invoice calculations
- product-local participation constructs unless they claim canonical membership meaning

## Adjacent Boundaries

### Identity Domain
Owns canonical account identity and identity continuity. Membership always points to canonical account identity and may never replace it.

### Auth / Session Domain
Owns login and session runtime truth. Sessions do not create or replace membership.

### Workspace / Organization Domain
Owns workspace existence, organization hierarchy, ownership relationships, and collaborative scope truth. Membership lifecycle attaches to those canonical scopes.

### Authorization Domain
Consumes membership state as prerequisite input for many workspace-scoped actions but does not own membership mutation semantics.

### Effective-Permission Domain
Determines final action-level allow, deny, restricted, review-required, or other outcomes after scope, membership, grants, object conditions, entitlement, and policy are evaluated.

### Entitlement / Capability Domain
May use membership or seat posture as an input, but does not own canonical membership truth.

### Commerce / Billing Domain
May count active, billable, suspended, or otherwise classified members for downstream commercial purposes, but does not redefine membership semantics.

### Security / Risk Domain
May require restriction, suspension, review, or containment of a membership and may override ordinary flows where security or abuse posture requires.

### Product Domains
May read membership truth and maintain product-local participation records or caches under workspace scope, but may not replace canonical membership or invent hidden shared-platform variants.

### Support / Control-Plane Domain
May execute approved corrective or containment flows, but only through policy-bound, auditable pathways and without becoming the owner of membership truth.

## Conflict Resolution Rules

When layers disagree, FUZE MUST resolve workspace-membership disagreements in the following order unless a higher-order platform policy explicitly overrides it:

1. canonical identity-domain records for `account_id`
2. canonical workspace and organization records
3. canonical invitation and membership records
4. explicit security, risk, restriction, and policy constraints
5. canonical role/scoped-authorization/effective-permission records
6. entitlement posture where capability eligibility matters
7. current-workspace selector state, UI state, and product-local caches
8. derived views, dashboards, reports, or exports

Specific conflict rules:

- invitation state MUST NOT override canonical membership state once membership state exists
- current workspace selection MUST NOT override canonical membership or workspace lifecycle state
- product-local participant lists MUST NOT override canonical membership truth
- session presence MUST NOT preserve membership after removal or leave
- entitlement presence MUST NOT redefine membership ownership or lifecycle state
- support dashboards and exports MUST NOT be treated as authoritative if they diverge from canonical membership records
- later correction MUST supersede earlier mistaken state through explicit lineage rather than by silent history deletion

## Default Decision Rules

Where ambiguity exists, the following defaults apply:

1. default actor anchor: `account_id`
2. default collaborative subject: workspace, not product-local participant list
3. default interpretation of invitation: pending admission artifact, not active membership
4. default interpretation of membership: structural relationship, not final authority
5. default outcome for unresolved account-binding ambiguity: block activation and route to review or correction
6. default outcome for unsafe owner-adjacent removal or leave: preserve control-path continuity and block or route to review
7. default outcome for stale or conflicting cache state: canonical membership truth wins
8. default correction posture for privileged mistakes: explicit audited remediation, not destructive rewrite
9. default handling of missing required context for high-impact mutation: fail closed or `review_required`, not silent success
10. default interpretation of product-local state: subordinate to canonical workspace-membership truth

## Roles / Actors / Entities

### Invited Actor
The person or entity intended to bind an invitation to a canonical account.

### Candidate Account
A canonical account being evaluated for binding to invitation acceptance, provisioning outcome, or corrective remediation.

### Active Member
A canonical account whose membership is sufficiently active for ordinary collaboration and downstream authorization evaluation.

### Restricted Member
A canonical account whose membership remains structurally present but operationally constrained.

### Suspended Member
A canonical account whose membership is materially blocked pending review, abuse handling, or other containment logic.

### Removed Former Member
A canonical account with historical membership lineage but no current ordinary participation.

### Leaving Member
A member executing self-service departure where policy allows.

### Workspace Owner
The highest ordinary workspace-control member whose continuity requires stronger safeguards during membership mutation.

### Workspace Administrator
An elevated actor allowed to perform certain membership mutations subject to authorization and safety rules.

### Support / Admin Operator
A privileged internal actor allowed to perform correction, forced removal, suspension, reinstatement, or containment under stronger controls.

### Provisioning Adapter
An approved system path that creates or updates invitation or membership state from sanctioned provisioning or migration workflows without redefining canonical membership semantics.

## Ownership Model

### Workspace Membership Domain Owns

- canonical membership-record truth
- invitation-to-membership linkage
- membership state semantics
- membership transition rules
- provenance and lifecycle lineage
- owner-safety and minimum-control-path enforcement during membership mutation
- publication of membership lifecycle events
- durable membership audit references in coordination with the audit domain

### Workspace Membership Domain May

- create invitation records
- create pending or active membership records through approved flows
- restrict, suspend, remove, reinstate, or correct memberships
- expose canonical membership reads and transition summaries
- coordinate with authorization, entitlement, commerce, audit, and product systems through explicit contracts

### Workspace Membership Domain MUST NOT

- redefine canonical account identity
- issue sessions or define session validity
- decide final effective permissions
- own commercial plan truth
- allow products to bypass canonical membership mutation through hidden writes
- let generic reporting systems become mutation owners

### Product Domains MAY

- consume canonical membership reads
- react to membership events
- maintain product-local participation caches or materialized views
- define product admission flows that eventually call canonical membership APIs

### Product Domains MUST NOT

- create authoritative workspace membership by local side effect
- treat a product invitation as canonical workspace membership unless routed through platform membership APIs
- silently preserve access after canonical membership removal where workspace-scoped access depends on membership
- treat cache freshness or local participation state as stronger than canonical membership truth

## Authority / Decision Model

Membership decisions are layered.

### Workspace Membership Domain Decides

- whether an invitation exists
- whether an invitation may bind to a canonical account
- what the membership status is
- whether a transition is structurally valid
- whether owner-safety or minimum-control-path constraints block the transition

### Authorization and Effective-Permission Domains Decide

- what an active, restricted, or suspended member may actually do after membership state is resolved

### Security / Risk Domain May Override

- whether a membership must be suspended, reviewed, blocked, or contained

### Commerce / Entitlement Domains May Consume

- whether a member counts for seat or capability policy

### Support / Control-Plane Domain May Assist

- only through reason-coded, policy-bound workflows
- without becoming the owner of membership truth

This ordering is mandatory. Membership is structural truth. It does not absorb final authorization, entitlement, or commercial truth.

## Canonical State Model

Membership lifecycle MUST distinguish invitation state from membership state.

### Invitation States
At minimum, FUZE SHOULD support semantic invitation states such as:

- `created`
- `sent`
- `accepted`
- `declined`
- `expired`
- `cancelled`

### Membership States
At minimum, FUZE MUST support semantic membership states such as:

- `invited`
- `pending_acceptance`
- `active`
- `restricted`
- `suspended`
- `removed`
- `left`
- `declined`
- `expired`

Exact implementation names MAY vary, but these semantic meanings MUST remain expressible.

### State Meaning Summary

#### Invited
Admission intent exists but the workspace relationship is not yet active.

#### Pending Acceptance
A provisional bridge exists while the platform resolves canonical account binding, acceptance, provisioning completion, or other admission checks.

#### Active
The account is a current member and may serve as downstream authorization input subject to role, entitlement, and restriction checks.

#### Restricted
The membership exists but is constrained. Some ordinary actions may be blocked while historical lineage remains intact.

#### Suspended
The membership is materially blocked pending review, security handling, abuse handling, policy intervention, or remediation.

#### Removed
The membership has been ended by another authorized actor or policy pathway. Historical lineage remains.

#### Left
The membership ended through member-initiated departure. Historical lineage remains.

#### Declined
The invitation or admission offer was explicitly declined and did not become active membership.

#### Expired
The invitation or provisional admission path aged out without successful activation.

### State Rules

- invitation state and membership state MUST NOT be conflated
- terminal invitation outcomes MUST NOT silently mutate active membership
- removed, left, declined, and expired states MUST preserve historical lineage
- state transitions MUST be explicit, durable, auditable, and replay-safe
- stale cached reads MUST NOT override canonical membership state
- one canonical active membership for the same account/workspace pair MUST exist at a time

## Lifecycle / Workflow Model

### 1. Invitation Issuance
An authorized actor or approved provisioning path may issue an invitation to join a workspace.

Required properties:
- invite issuance MUST be authorized
- invite issuance MUST bind to a target workspace and intended admission posture where applicable
- invite issuance MUST create durable invitation lineage
- invite issuance MUST NOT by itself create active membership
- invite issuance MUST be idempotent or superseding-safe where retries are plausible

### 2. Invitation Delivery / Presentation
An invitation may be delivered through approved channels or surfaced through first-party product interfaces.

Required properties:
- delivery mechanics MUST NOT redefine canonical invitation truth
- public-safe responses SHOULD avoid unsafe workspace or membership enumeration
- delivery failure MUST NOT imply membership creation

### 3. Acceptance / Identity Binding
The invited actor attempts to accept the invitation.

Required properties:
- acceptance MUST bind to canonical account identity
- acceptance MUST NOT silently create duplicate identity when a canonical account already exists
- ambiguity MUST route through identity-resolution, recovery, or correction logic
- successful acceptance MAY produce `pending_acceptance` before final activation if additional checks are needed

### 4. Activation
Membership becomes active after required admission conditions are satisfied.

Required properties:
- activation MUST be explicit
- activation MUST record timestamps, actor provenance, and correlation references
- activation MAY trigger downstream authorization refresh, entitlement refresh, onboarding, notification, and product provisioning side effects
- activation MUST NOT bypass workspace restriction, owner-safety, or broader security posture

### 5. Restriction / Suspension
Membership may be restricted or suspended due to policy, security, abuse, workspace state, commercial-policy coupling where separately approved, or operational remediation.

Required properties:
- restriction and suspension MUST be reason-coded
- membership lineage MUST remain intact
- downstream access SHOULD reflect the new state quickly and deterministically
- reinstatement MUST be separate and explicit

### 6. Removal
An authorized actor or policy pathway removes the member.

Required properties:
- removal MUST be auditable
- removal MUST preserve historical lineage
- removal MUST enforce owner-safety and minimum-control-path checks
- workspace-owned resources MUST remain owned by the workspace or governed by canonical transfer rules
- downstream product access dependent on membership MUST be revoked or suppressed

### 7. Leave
A member leaves the workspace through self-service where policy allows.

Required properties:
- self-leave MUST be disallowed where it would violate owner continuity or strand required control
- leave MUST preserve historical lineage
- leave MUST NOT silently reassign workspace-owned resources to the leaving account

### 8. Re-Invite / Rejoin
A former member may be re-invited or reactivated through approved flows.

Required properties:
- rejoin MUST preserve prior historical lineage
- stale invites MUST NOT silently reactivate removed or left membership without an explicit valid transition
- reinstatement versus new admission MUST remain distinguishable where reconstruction matters

### 9. Correction / Reinstatement
Support or authorized actors may correct mistaken membership states.

Required properties:
- corrections MUST be explicit and auditable
- reinstatement MUST NOT erase prior mistake history
- wrong-account acceptance and mistaken removal MUST use remediation lineage rather than destructive rewrite
- correction SHOULD publish explicit superseding events and trace references

## Invariants

1. Membership always points to canonical account identity.
2. Invitation is not active membership.
3. Active membership is necessary for many workspace-scoped actions but is not sufficient for final authorization.
4. Session presence does not create membership.
5. Current workspace selection does not create membership.
6. Product-local participation does not replace canonical membership.
7. Membership removal or leave does not destroy canonical account identity.
8. Workspace-owned resources remain workspace-owned through membership mutation unless a separate canonical transfer flow governs otherwise.
9. Minimum-owner or control-path safety must block unsafe owner-adjacent exits or removals.
10. Membership history must remain auditable and reconstructable.
11. Degraded runtime conditions must not widen membership or revive inactive membership by implication.
12. Derived views remain derived and correctable from canonical records.

## Functional Rules

### Admission Rules
- only authorized actors or approved provisioning flows may admit members
- admission MUST bind to canonical workspace scope
- admission MUST bind to canonical account identity or a controlled identity-resolution flow
- invitation acceptance MUST NOT silently attach the wrong account
- duplicate active memberships for the same account/workspace pair are forbidden

### Activation Rules
- only active membership may feed ordinary downstream workspace authorization
- `invited`, `pending_acceptance`, `declined`, and `expired` are not equivalent to `active`
- activation MAY require successful invite acceptance, provisioning completion, workspace readiness, and policy-required checks

### Restriction / Suspension Rules
- restricted or suspended memberships remain visible to canonical systems for audit and remediation
- restriction or suspension suppresses ordinary collaboration and may suppress downstream permissions regardless of stale grants
- reinstatement MUST be explicit and auditable

### Removal Rules
- authorized removal MUST be explicit and auditable
- removal MUST revoke or suppress membership-dependent downstream access
- removal MUST NOT silently erase provenance, ownership history, or audit lineage
- owner removal MUST obey owner continuity safeguards

### Leave Rules
- self-leave is allowed only where policy permits
- self-leave MUST be blocked if it would leave the workspace without a legitimate control path where policy requires continuity
- self-leave MUST preserve historical membership lineage

### Rejoin Rules
- former members MAY be re-invited through explicit new invitation or approved reinstatement flows
- rejoin SHOULD preserve prior historical lineage while creating a new activation episode or explicit reactivation marker
- stale invites MUST NOT silently reactivate removed or left membership

### Correction Rules
- wrong-account acceptance, mistaken removal, mistaken suspension, and historical data drift MUST use explicit correction workflows
- corrections SHOULD preserve both original event lineage and superseding correction lineage
- hidden destructive rewrite is forbidden for high-impact membership history except where required by a separately approved data-protection deletion policy, and even then only to the minimum extent required

## Permission / Access Considerations

This specification does not define authorization in full depth, but it imposes mandatory constraints on downstream access design.

### Required Constraints
- membership state MUST be resolved before workspace-scoped authorization
- only membership states deemed sufficiently active may supply ordinary authorization input
- restricted or suspended membership MUST suppress ordinary authority even if historical role assignments still exist
- removed and left memberships MUST no longer produce ordinary active workspace access
- role assignments attached to non-active memberships MUST NOT be treated as sufficient authority
- current workspace selection is runtime context only and MUST NOT bypass membership checks
- sensitive mutations SHOULD use fresh canonical membership state rather than stale client-side or cached summaries

## Entitlement Considerations

Membership and entitlement are related but distinct.

### Rules
- active membership MAY be necessary for workspace-scoped entitlement consumption but is not by itself sufficient
- entitlement MAY attach to workspace, account, seat, or product policy depending on downstream domain
- member count and billable-member calculations are downstream concerns, but they MUST consume canonical membership truth rather than redefining it
- restricted or suspended membership MAY or MAY NOT count commercially depending on downstream policy; this document does not decide that commercial question
- entitlement success MUST NOT reactivate or widen inactive membership

## API / Contract Implications

The platform SHOULD expose membership lifecycle through explicit contracts.

### Workspace / Membership APIs SHOULD support
- invitation issuance
- invitation cancellation
- invitation acceptance and decline
- membership listing and point reads
- membership activation-state reads
- membership restriction, suspension, reinstatement, removal, and leave flows
- owner-safe mutation checks
- correction and remediation endpoints for privileged workflows
- machine-readable conflict, review-required, wrong-account-risk, and owner-safety outcomes

### API Rules
- all mutation-capable endpoints MUST support correlation identifiers
- idempotency is required where retries are plausible, especially for invite, accept, decline, activate, restrict, suspend, remove, leave, reinstate, and corrective actions
- public-safe responses SHOULD avoid leaking unsafe details about workspace existence or membership presence
- admin and control-plane routes require stronger authorization and richer audit context
- `review_required` or equivalent outcomes are valid for ambiguous or safety-sensitive mutation requests
- downstream APIs MUST NOT collapse invitation truth and active-membership truth into one generic status without preserving the distinction semantically

## Event / Async Implications

Membership lifecycle MUST emit durable events sufficient for downstream authorization, products, audit, and operations.

Representative semantic events include:

- `workspace.member_invited`
- `workspace.member_invitation_cancelled`
- `workspace.member_invitation_declined`
- `workspace.member_invitation_expired`
- `workspace.member_joined`
- `workspace.member_activated`
- `workspace.member_restricted`
- `workspace.member_suspended`
- `workspace.member_reinstated`
- `workspace.member_removed`
- `workspace.member_left`
- `workspace.member_corrected`

### Event Rules
- events MUST be emitted only after canonical state transitions commit
- retries MUST be replay-safe and idempotent
- downstream consumers MAY react but MUST NOT redefine membership truth
- async delivery failure MUST NOT imply that membership mutation did not occur
- corrective events MUST preserve lineage rather than pretending earlier events never happened
- cache invalidation, downstream authorization refresh, and product-participation suppression SHOULD preserve causality linkage to the membership mutation that triggered them

## Data Model / Storage Implications

At minimum, FUZE SHOULD support the following durable semantic structures.

### `workspace_membership`
Representative semantic fields:
- `membership_id`
- `workspace_id`
- `account_id`
- membership status
- current role-binding references where applicable
- created_at
- invited_at
- accepted_at
- activated_at
- restricted_at
- suspended_at
- removed_at
- left_at
- provenance reference
- correction or review markers
- correlation reference

### `workspace_invitation`
Representative semantic fields:
- `invitation_id`
- target workspace reference
- inviter account reference
- intended recipient artifact or identity reference where allowed
- invitation state
- sent_at
- expires_at
- accepted_by_account reference where known
- cancelled_at
- decline marker
- correlation reference

### `workspace_membership_transition`
Representative semantic fields:
- `transition_id`
- `membership_id`
- prior state
- resulting state
- actor reference
- reason code
- policy reference
- executed_at
- correction or supersession reference where applicable
- correlation reference

### `workspace_membership_audit_reference`
Representative semantic fields:
- audit action ID
- membership reference
- actor reference
- action type
- reason code
- timestamp
- correlation reference

### Data Rules
- membership and invitation truth MUST remain durable source-of-truth records
- derived dashboards, analytics views, caches, and product mirrors MUST NOT become write owners
- corrections SHOULD preserve lineage rather than silently rewriting history
- one canonical active membership record per account/workspace relationship SHOULD exist at a time, with history captured through transitions, episodes, or supersession lineage
- storage compaction or migration MUST NOT flatten meaningfully distinct lifecycle events into irreversible generic status if that would weaken auditability or correction safety

## Read Model / Projection / Reporting Rules

Derived membership views are allowed, but they are subordinate.

### Allowed Derived Views
- product-facing active-member lists
- support-facing membership summaries
- analytics/member-count dashboards
- invite-conversion and churn reports
- search indexes and cached participant lookups

### Required Constraints
- derived views MUST identify themselves as derived, not canonical
- derived views MUST be regenerable from canonical invitation, membership, and transition truth
- sensitive mutation paths MUST re-check fresh canonical state rather than relying solely on derived views
- public or customer-facing summaries MAY collapse detail, but internal protected systems MUST preserve richer lineage
- stale derived projections MUST NOT be allowed to widen access or hide removal, suspension, or correction state

## Security / Risk / Abuse Controls

Workspace membership is a security-sensitive collaborative boundary.

FUZE MUST preserve:

- stronger checks for owner-adjacent removal or leave actions
- protection against wrong-account acceptance
- anti-enumeration behavior where public invitation flows could leak workspace existence or membership patterns
- replay-safe acceptance and mutation handling
- stronger operator controls for forced removal, suspension, reinstatement, and correction
- rapid suppression of downstream access after suspension or removal
- risk-aware routing to review when abuse, takeover, social-engineering risk, or policy ambiguity exists

## Boundary Violation Detection / Non-Canonical Patterns

The following patterns are non-canonical and forbidden unless a higher-order approved specification explicitly creates a bounded exception:

- treating successful login as automatic workspace membership
- treating current workspace selection as proof of membership
- using product-local participant lists as canonical shared-platform membership
- silently creating a new account during invite acceptance when a canonical account already exists
- mutating membership through support tooling without reason codes, policy references, and audit lineage
- destroying removal or correction history to make the timeline look clean
- counting a derived dashboard as authoritative over canonical membership records
- allowing stale caches to preserve workspace-scoped access after removal or suspension
- routing governance-sensitive or owner-sensitive membership mutations through ordinary low-trust paths

## Audit / Traceability Requirements

FUZE MUST be able to determine:

- who invited whom to which workspace
- which canonical account accepted an invitation
- when membership became active
- why a member was restricted, suspended, removed, left, or reinstated
- whether owner-safety logic blocked a mutation
- which operator executed corrective action
- whether visible member summaries were canonical or derived
- how a current membership state was reached from prior states
- which policy references and correlation identifiers were associated with a high-impact mutation

Membership mutation is high-impact because it affects collaborative access, product participation, billing-adjacent calculations, audit continuity, and support remediation.

## Failure Handling / Edge Cases

### Invite Delivered but Never Accepted
Invitation may expire without creating active membership. No hidden active membership may remain.

### Invite Accepted by Wrong Account
The platform MUST preserve explicit remediation and correction pathways. Membership MUST end up attached to the correct canonical account or the flow MUST fail safely.

### Canonical Account Already Exists
Acceptance or provisioning MUST bind to the existing account rather than silently creating another identity.

### Workspace Owner Attempts Self-Leave as Last Controller
The platform MUST block the action or route it through explicit ownership transfer or remediation.

### Member Removed While Session Still Exists
Downstream workspace access MUST reflect removal quickly. Session presence does not preserve workspace participation.

### Member Suspended During Active Product Use
Membership-dependent product actions MUST be suppressed according to downstream enforcement timing and policy.

### Former Member Reinvited
A new invitation or explicit reinstatement flow MAY occur, but prior history MUST remain visible and distinct.

### Workspace Restricted or Suspended
Membership may remain historically present while ordinary collaboration is suppressed by stronger scope posture.

### Product Cache Still Shows Member After Removal
Canonical membership truth prevails. Product caches MUST refresh or deny on sensitive checks.

### Support Corrects Mistaken Removal
The platform MUST create explicit correction lineage rather than silently deleting the removal event.

### Async Side Effects Partially Fail
The committed canonical membership transition remains authoritative. Downstream retries MUST be idempotent and linked causally to the original mutation.

## Operational Considerations

Operational systems SHOULD support:

- observability for invite aging, acceptance failures, wrong-account acceptance risk, repeated removal/reinvite churn, and owner-orphan risk
- clear control-plane workflows for suspension, removal, reinstatement, and correction
- correlation across identity, session, workspace, authorization, entitlement, and product events
- idempotent retry behavior for sensitive lifecycle transitions
- runbooks for wrong-account acceptance, stranded ownership, forced suspension, large-scale workspace offboarding, and membership correction
- metrics for invite conversion, membership churn, suspension volume, reinstatement volume, correction frequency, and owner-orphan prevention

The operational model MUST preserve the principle that membership mutation is canonical platform behavior, not a product-local patch.

## Migration / Compatibility / Supersession Considerations

- migrations MUST NOT move canonical membership truth into product-local stores
- compatibility layers MAY mirror older “team” or collaborator constructs temporarily, but canonical membership ownership MUST remain platform-owned
- older implementations or documents that imply login side effects create membership, or that product sharing alone is canonical membership, are superseded within this scope
- if prior systems conflated invitation state and membership state, migrations MUST separate them explicitly
- membership-history cleanup SHOULD prefer lineage preservation over destructive flattening
- transition naming MAY evolve, but the semantic distinctions defined here MUST remain preserved through migration

## Implementation-Contract Guardrails

Downstream implementation layers MUST preserve at minimum:

- canonical distinction between invitation truth and membership truth
- canonical binding of membership to `account_id` and `workspace_id`
- explicit state transitions rather than implied status mutation by side effect
- idempotency and replay safety for high-impact mutations
- reason-coded and audit-linked privileged correction paths
- owner-continuity safeguards for owner-adjacent removals and self-leave
- canonical read revalidation for sensitive actions even if low-risk derived views exist
- explicit suppression or revocation propagation to downstream systems after removal or suspension
- explicit handling of wrong-account acceptance and stale-invite scenarios
- conflict handling that prefers canonical membership truth over caches, exports, or UI state

Downstream implementations MUST NOT optimize away:

- transition lineage needed for reconstruction
- correlation identifiers needed for cross-service tracing
- policy references for review-required or privileged correction outcomes
- distinct outcome semantics for remove vs leave vs suspend vs restricted where those differences matter to access, audit, or reporting

## Downstream Execution Staging

Recommended downstream execution order:

1. resolve canonical actor identity
2. validate session or service-principal posture where required
3. resolve canonical workspace scope
4. resolve invitation and membership state
5. enforce owner-safety, policy, and security constraints for mutations
6. commit canonical membership mutation
7. publish events and trace references
8. invalidate or refresh downstream authorization, product, and reporting projections
9. reconcile async side effects without changing the committed canonical truth silently

## Required Downstream Specs / Contract Layers

This specification directly governs or materially informs:

- `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- product integration specifications
- support and control-plane workflow specifications
- audit and reporting specifications

## Canonical Examples / Anti-Examples

### Canonical Example: Standard Invitation Acceptance
A workspace admin issues an invite to a valid target. The invited actor authenticates into the correct canonical account, accepts the invitation, passes any required policy checks, and the system explicitly transitions membership from `invited` or `pending_acceptance` to `active` with durable lineage.

### Canonical Example: Wrong-Account Acceptance Block
An invite is opened while authenticated as the wrong canonical account. The system does not silently activate membership. It blocks, routes to identity-resolution or correction logic, and preserves the attempt for audit.

### Canonical Example: Owner-Safe Self-Leave Block
A workspace owner attempts self-leave while no alternate control path exists. The system blocks the action and requires ownership transfer or privileged remediation.

### Anti-Example: Product Team Table Creates Membership
A product-specific collaboration feature adds someone to a local participant table and treats them as a workspace member without calling canonical membership APIs. This is forbidden.

### Anti-Example: Removal Hidden by Cache
A product cache continues showing active access after a canonical membership removal. Sensitive actions still succeed because the cache was trusted over canonical truth. This is forbidden.

### Anti-Example: Correction by History Deletion
An operator discovers a mistaken removal and edits the current row to appear as if removal never occurred, without a superseding correction record. This is forbidden.

## Dependencies / Cross-Spec Links

This specification depends on:

- `REFINED_SYSTEM_SPEC_INDEX.md`
- `DOCS_SPEC_INDEX.md`
- `SYSTEM_SPEC_INDEX.md`
- `API_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
- `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`

This specification directly informs:

- `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- product integration specifications
- control-plane workflows
- support tooling
- audit, anomaly-detection, and reporting pipelines

## Explicitly Deferred Items

The following are intentionally deferred to adjacent or downstream specifications:

- exact API schema shapes and field-level validation rules
- exact event payload schemas and transport topology
- exact database schema, indexing, and sharding strategy
- exact enterprise provisioning connector behavior and mapping syntax
- exact end-user messaging copy for invite, deny, or review-required outcomes
- exact seat-billing formulas and invoice policy treatment of non-active memberships
- exact observability dashboard layouts and alert thresholds
- exact control-plane staffing, approval, or queue-routing procedures

These deferrals do not weaken the canonical lifecycle semantics established here.

## Final Normative Summary

FUZE workspace membership is the durable canonical relationship between a canonical account and a canonical workspace. It is not login, not session, not workspace selection, not product-local sharing, not final permission, and not billing truth. Invitation truth and membership truth must remain distinct. Active membership is a prerequisite structural input for many workspace-scoped actions, but final authority remains downstream in authorization and effective-permission evaluation.

Membership lifecycle transitions must be explicit, auditable, replay-safe, owner-continuity-safe, and correction-capable. Wrong-account acceptance, mistaken removal, stale invite reactivation, and cache-vs-canonical disagreement must fail safely in favor of canonical truth and explicit remediation. Products and internal tools may consume membership truth and derive views from it, but they must not redefine it.

This document is the canonical FUZE rule set for governing workspace membership lifecycle semantics, mutation guardrails, reconstruction requirements, and implementation-contract boundaries.

## Quality Gate Checklist

- Canonical owner is explicit for invitation and membership truth: **Yes**
- Mutation boundaries are explicit: **Yes**
- Adjacent boundaries are explicit: **Yes**
- Truth classes are explicit: **Yes**
- Conflict-resolution rules are explicit where needed: **Yes**
- Default decision rules are explicit where ambiguity could arise: **Yes**
- Non-canonical patterns are called out clearly: **Yes**
- Operator/admin override paths are bounded and audited: **Yes**
- Read-model, cache, reporting, and projection rules are explicit: **Yes**
- Failure and degraded-mode behaviors are explicit: **Yes**
- Downstream implementation guardrails are explicit: **Yes**
- Dependencies and downstream impacts are explicit: **Yes**
- Non-goals and deferred items are explicit: **Yes**
- Strong enough for backend/API/data/runtime teams to implement without inventing contradictory semantics: **Yes**
