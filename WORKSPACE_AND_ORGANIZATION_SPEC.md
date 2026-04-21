# WORKSPACE_AND_ORGANIZATION_SPEC

## Title
FUZE Workspace and Organization Specification

## Document Metadata

- Document Name: `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Version: 1.1.0
- Effective Date: 2026-04-21
- Last Updated: 2026-04-21
- Reviewed On: 2026-04-21
- Document Owner: FUZE Platform Workspace and Organization Domain
- Approval Authority: FUZE Platform Architecture and Governance Authority
- Review Cadence: Quarterly or upon material change to collaborative scope semantics, membership structure, ownership continuity, organization hierarchy, workspace lifecycle, product attachment rules, or authorization sequencing
- Governing Layer: Platform core / collaborative scope and organization model
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, security engineering, support operations, audit, governance, API design, platform operations, reliability engineering, implementation-contract authors
- Primary Purpose: Define the canonical FUZE model for collaborative scope, including workspace and organization meaning, ownership structure, scope hierarchy, runtime scope selection, product attachment, lifecycle posture, and downstream consumption boundaries so that products, authorization systems, entitlements, support tooling, and reporting surfaces do not redefine collaborative scope inconsistently
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
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
  - `IDENTITY_AND_ACCOUNT_SPEC.md`
  - `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
  - `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
  - `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
  - `WALLET_AWARE_USER_SPEC.md`
- Primary Downstream Dependents:
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
  - `WORKSPACE_MEMBERSHIP_LIFECYCLE_SPEC.md`
  - `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
  - `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
  - `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
  - `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
  - `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
  - `WORKSPACE_ORGANIZATION_API_SPEC.md`
  - `ROLE_PERMISSION_ACCESS_API_SPEC.md`
  - product integration specifications
  - support and control-plane workflow specifications
- Supersedes: Earlier or less explicit FUZE workspace, organization, team, and collaborative-scope writeups to the extent they conflict within this document’s scope
- Superseded By: Not yet known
- Related Decision Records: Not yet known
- Canonical Status Note: This document is the governing FUZE workspace and organization specification. Downstream APIs, products, services, reports, support tooling, and control-plane workflows MUST NOT reinterpret the semantics established here.
- Implementation Status: Normative architecture baseline; downstream API, workflow, storage, event, runtime, and audit contracts must conform
- Approval Status: Drafted for refined-system inclusion; formal approval record not yet attached
- Change Summary:
  - strengthened separation between canonical account identity, runtime session state, collaborative scope truth, authorization truth, entitlement truth, wallet-aware context, and reporting truth
  - clarified workspace and organization as canonical collaborative scope structures resolved after valid identity and session, but before scoped authorization and effective-permission evaluation
  - tightened ownership continuity, scope hierarchy, current-workspace runtime selection, product-attachment rules, lifecycle posture, and support/admin correction boundaries
  - expanded implementation-contract guardrails for idempotency, audit lineage, default decision rules, conflict handling, degraded-mode behavior, and product anti-drift rules

## Purpose

This specification defines the canonical FUZE model for workspace and organization.

Its purpose is to make explicit:

- what a workspace is in FUZE and what it is not
- what an organization is in FUZE and what it is not
- how collaborative scope is represented, owned, and resolved
- how workspace and organization records relate to canonical account identity, runtime session state, membership, scoped authorization, entitlement, product context, and wallet-aware context
- how workspace ownership, organization hierarchy, lifecycle state, and product attachment must behave
- how current workspace selection differs from durable scope truth
- how products, APIs, internal services, support tooling, and reports consume workspace and organization truth without becoming owners of it

FUZE is a multi-product platform. In such a platform, collaborative scope cannot be a UI dropdown, a product-local “team” table, or a soft convention. Workspace and organization must be explicit platform concepts so that membership, role grants, credits-aware actions, billing context, product context, audit traceability, and admin correction all attach to the same durable collaborative model. fileciteturn31file0L39-L74

## Scope

This specification governs:

- canonical meaning of workspace and organization
- structural relationship between organization and workspace where both exist
- workspace ownership and organizational control structure
- runtime current-workspace selection semantics
- workspace and organization lifecycle posture
- workspace-scoped product attachment and shared-resource context
- minimum canonical entities and state posture for workspace and organization records
- boundary between collaborative scope truth and downstream membership, scoped authorization, effective permission, entitlement, wallet-aware, and reporting domains
- API, event, data-model, security, audit, operational, and migration implications of canonical collaborative scope truth

This specification does not define in full depth:

- workspace membership state transitions in full depth
- full role and permission catalogs
- full scoped grant hierarchy logic
- final action-level access evaluation rules
- entitlement formulas or billing truth
- credits-ledger truth
- wallet-link lifecycle
- exact product-specific object model details
- exact UI flows for switching workspaces or organization administration

Those concerns belong to adjacent or downstream specifications and must remain compatible with this document. fileciteturn31file0L76-L91

## Out of Scope

This specification is explicitly out of scope for:

- canonical account identity or provider resolution
- session issuance, refresh, or revocation semantics
- invitation and membership lifecycle mechanics in full depth
- exact role matrices and permission strings
- exact entitlement calculations
- product-local UI navigation logic
- exact internal queue topology for support or admin correction
- wallet custody or on-chain ownership truth
- public reporting copy or dashboard implementation details

## Design Goals

1. Make workspace the canonical collaborative operating scope for FUZE.
2. Support organization as a broader umbrella without collapsing it into workspace or identity.
3. Preserve one account across many workspaces and products without semantic drift.
4. Keep collaborative scope clearly separate from authorization, entitlement, and wallet-aware context.
5. Prevent products from inventing parallel team or scope models for shared platform behavior.
6. Support ownership continuity and safe correction without hidden privilege escalation.
7. Make runtime workspace selection useful without letting it become durable truth.
8. Support future-safe product attachment, enterprise structure, and governance-sensitive control paths.
9. Preserve auditability, correction lineage, and deterministic control behavior.
10. Make downstream API and implementation-contract work stricter and easier to align.

## Non-Goals

This specification is not intended to:

- treat workspace membership as final permission
- treat current workspace selection as durable membership or authority
- treat organization as universal permission scope by default
- let product participation become canonical collaborative truth
- let billing or entitlement systems redefine collaborative scope
- let wallet linkage become workspace ownership or admin power
- replace downstream API, schema, workflow, or runbook specifications

## Core Principles

### 1. Collaborative Scope Principle
Workspace is the primary canonical collaborative scope in FUZE. It is the operating context in which shared settings, shared resources, shared product behavior, and downstream authority evaluation occur. fileciteturn31file0L181-L200

### 2. Organization Umbrella Principle
Organization is a broader grouping structure that may contain one or more workspaces, but it does not erase workspace as the primary actionable collaborative scope. fileciteturn31file0L271-L279

### 3. Identity / Scope / Authority Separation Principle
Identity answers who the actor is. Workspace and organization answer where the actor is acting. Authorization answers what the actor may do there. These truths are connected but MUST remain distinct. fileciteturn31file0L131-L148

### 4. Scope-After-Auth Principle
Collaborative scope is resolved only after canonical account identity is established and a valid authenticated session exists, and before scoped authorization and effective-permission evaluation occur. fileciteturn30file3L58-L65 fileciteturn31file0L402-L415

### 5. Runtime Selector Is Not Durable Truth Principle
Current workspace is a runtime context selector. It helps route actions correctly but MUST NOT create membership, ownership, or authority that does not already exist. fileciteturn31file0L293-L299

### 6. Product-Consumption Principle
Products may extend behavior inside a valid workspace or organization context, but they MUST consume canonical collaborative scope truth instead of replacing it with hidden team models. fileciteturn31file0L375-L386

### 7. Ownership Continuity Principle
Workspace and organization control paths MUST preserve legitimate controlling authority and MUST NOT be strandable by ordinary self-service mistakes.

### 8. No Shadow Scope Principle
Product-local “spaces,” “teams,” “projects,” or similar constructs may exist only as subordinate product constructs. They MUST NOT silently replace canonical workspace or organization truth for shared platform behavior. fileciteturn31file0L336-L344

### 9. Restriction Precedence Principle
Workspace or organization restriction, suspension, review posture, or control-plane containment MUST outrank stale assumptions from cached membership, product-local state, or current-workspace UI selection. fileciteturn31file0L394-L399

### 10. Derived View Boundary Principle
Dashboards, support views, analytics outputs, and public summaries may describe workspace and organization state, but they MUST remain derived and correctable from canonical records.

## Canonical Definitions

### Workspace
The primary collaborative scope in FUZE. It is a durable platform object that defines a member set, shared settings, shared resources, shared product participation context, and the runtime scope an action belongs to. fileciteturn31file0L181-L200

### Organization
A broader grouping structure that may contain one or more workspaces and may provide higher-level reporting, administration, or shared commercial structure where explicitly modeled. fileciteturn31file0L271-L279

### Workspace Ownership
The durable control relationship that preserves legitimate authority over a workspace’s continued administration, membership governance, and critical settings within downstream authorization rules.

### Organization Ownership / Control
The broader controlling authority over organization-level administration and policy where such an organization structure exists distinctly from workspace ownership.

### Workspace Membership
The durable structural relationship between an account and a workspace. Membership is necessary for many actions but is not final permission. fileciteturn31file0L282-L286

### Current Workspace
The actor’s active runtime collaborative context selector. It is useful for routing actions and UX continuity but is not durable membership truth or a permission grant. fileciteturn31file0L300-L304

### Product Attachment
The explicit relationship by which a FUZE product or product-local resource family operates under a valid account, workspace, or organization scope without redefining the underlying collaborative scope.

### Scope Resolution
The process of resolving the relevant canonical collaborative scope from authenticated actor context plus explicit scope inputs before authorization and entitlement are applied. fileciteturn31file1L83-L95

### Organization / Workspace Hierarchy
The bounded parent/child relationship in which organization may act as umbrella scope while workspace remains the primary actionable collaborative operating scope. fileciteturn31file1L317-L326

### Workspace Lifecycle State
The durable state of a workspace, such as active, restricted, suspended, pending setup, archived, or closed, used to constrain downstream behavior.

## Truth Class Taxonomy

This specification distinguishes the following truth classes and downstream implementations MUST preserve those distinctions.

### 1. Canonical Identity Truth
The account record and identity-domain continuity semantics anchored by `account_id`. Workspace and organization consume identity truth but do not redefine it. fileciteturn30file3L1-L18

### 2. Runtime Session Truth
Temporary authenticated runtime presence that allows scope resolution to proceed, but does not itself define collaborative scope or authority. fileciteturn30file3L1-L18

### 3. Collaborative Scope Truth
Canonical organization and workspace records, hierarchy, ownership structure, lifecycle state, and runtime scope-selection semantics owned by the Workspace and Organization Domain.

### 4. Membership Truth
The durable relationship between an account and workspace or organization, owned by the membership lifecycle domain and consumed by downstream authorization. Membership truth is adjacent to, not identical with, collaborative scope truth. fileciteturn31file0L282-L286

### 5. Authorization Truth
Roles, permissions, scoped grants, and effective-permission outcomes owned by downstream authorization domains. Workspace scope is an input to authorization, not authorization itself. fileciteturn30file5L58-L73

### 6. Entitlement Truth
Commercial or policy eligibility for products and capability classes, which may attach to account, workspace, or organization, but remains separate from collaborative scope. fileciteturn31file2L39-L52

### 7. Wallet-Aware Context Truth
Wallet-link state and wallet-derived participation context. Wallet-aware context may be attached to an account or used by products inside workspace context, but MUST NOT replace workspace or authorization truth. fileciteturn30file6L1-L16

### 8. Derived Read-Model Truth
Support views, dashboards, search projections, analytics outputs, and UX summaries derived from canonical records.

### 9. Reporting / Public View Truth
Reports, public-facing surfaces, and summarized communication artifacts. These remain downstream presentations and MUST remain correctable from canonical truth.

## Architectural Position in the Spec Hierarchy

This document sits below:

- `REFINED_SYSTEM_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`

and above:

- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `WORKSPACE_MEMBERSHIP_LIFECYCLE_SPEC.md`
- `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`

This document owns collaborative scope semantics. It does not absorb detailed ownership of membership lifecycle, authorization grant taxonomy, effective-permission evaluation, or entitlement. fileciteturn30file3L26-L49

## System Boundaries

This document governs only the following platform-owned boundaries:

- canonical meaning of organization and workspace
- structural organization-to-workspace hierarchy
- workspace as primary collaborative operating scope
- organization as broader umbrella where modeled distinctly
- durable ownership and control-path semantics for organizations and workspaces
- current-workspace runtime context semantics
- workspace and organization lifecycle state
- product attachment to workspace or organization scope
- canonical scope identifiers and scope-resolution prerequisites
- boundary between canonical collaborative scope truth and downstream membership, authorization, entitlement, wallet-aware, and reporting layers

It does not govern:

- identity creation or provider resolution
- session issuance or refresh
- membership transition details in full depth
- role and permission catalogs
- final action-level access evaluation
- invoice or credits-ledger truth
- wallet-link lifecycle
- product-local object rules in full depth

## Adjacent Boundaries

### Identity Domain
Owns canonical account identity and continuity. Workspace and organization are downstream collaborative scope, not alternate identity roots. fileciteturn31file0L151-L178

### Auth / Session Domain
Owns authentication and session validity. Scope resolution consumes a valid session but does not redefine session semantics. fileciteturn31file0L402-L415

### Workspace Membership Lifecycle Domain
Owns whether and how an account is structurally attached to a workspace and in what lifecycle state. This document defines the workspace the membership attaches to, but not the full membership transition model.

### Authorization Domains
Own roles, permissions, scoped grants, and effective-permission outcomes. This document provides the canonical scope structures those domains bind to and evaluate within. fileciteturn31file1L203-L220

### Entitlement Domain
Owns product/capability eligibility. Entitlements may attach to workspace or organization scope, but entitlement does not become scope truth. fileciteturn31file2L230-L244

### Wallet-Aware Domain
Owns wallet-link truth and wallet-aware derived context. Wallet state may enrich product behavior but MUST NOT redefine workspace or organization semantics. fileciteturn30file6L1-L16

### Audit / Traceability Domain
Owns durable traceability requirements across access-domain actions. This document requires workspace and organization identifiers, lifecycle states, and control-path semantics to be traceable. fileciteturn30file15L39-L50

### Admin Correction / Containment Domain
Owns privileged correction and containment when ordinary self-service is insufficient or unsafe. This document defines the collaborative scope records that may need privileged correction; it does not allow admin tooling to become canonical workspace owner. fileciteturn30file13L39-L54

## Conflict Resolution Rules

When multiple layers disagree, the platform MUST resolve workspace- and organization-related disagreements in the following order unless a higher-order platform policy explicitly overrides it:

1. canonical identity-domain records for `account_id`
2. canonical workspace and organization records
3. canonical membership records
4. explicit policy, restriction, and security constraints
5. canonical scoped authorization and effective-permission records
6. entitlement posture where capability eligibility matters
7. runtime session UI state, current-workspace selector state, and product-local caches
8. derived views, dashboards, reports, or public surfaces

Specific conflict rules:

- current workspace selection MUST NOT override canonical membership or lifecycle state
- product-local “team” or “space” records MUST NOT override canonical workspace existence
- wallet presence MUST NOT override workspace membership or authorization truth fileciteturn30file6L17-L33
- entitlement presence MUST NOT redefine workspace ownership or membership
- support dashboards MUST NOT be treated as authoritative if they diverge from canonical workspace records
- reporting exports MUST NOT be used to repair canonical workspace or organization truth directly

## Default Decision Rules

Where ambiguity exists, the following defaults apply:

1. default actor anchor: `account_id`
2. default primary collaborative scope: workspace
3. default broader umbrella scope: organization only where explicitly modeled
4. default interpretation of current workspace: runtime context selector, not durable grant
5. default interpretation of membership: structural attachment, not final authority
6. default interpretation of product context: subordinate to canonical workspace or organization truth
7. default interpretation of entitlement: eligibility input, not collaborative scope owner
8. default interpretation of wallet-aware context: attached context, not scope authority
9. default resolution for ambiguous scope: explicit deny or review rather than silent fallback to broader scope
10. default resolution for ownerless-risk or control-path ambiguity: preserve continuity through review or correction rather than ordinary destructive self-service

## Roles / Actors / Entities

### Canonical Account Actor
The authenticated FUZE account attempting to operate in collaborative scope.

### Workspace
The primary actionable collaborative container for members, settings, shared resources, and workspace-scoped product activity. fileciteturn31file0L181-L200

### Organization
A broader umbrella structure that may contain one or more workspaces and may carry broader administration or reporting context. fileciteturn31file0L271-L279

### Workspace Owner / Controlling Authority
The role or bounded control path that preserves legitimate administrative continuity for a workspace.

### Organization Owner / Controlling Authority
The broader controlling authority over organization-level administration where modeled distinctly.

### Current Workspace Selector
The runtime context chosen by the actor or system for action routing. It is a selector, not a durable grant. fileciteturn31file0L300-L304

### Product Authorization Consumer
A product or internal service that reads canonical workspace and organization truth to derive downstream product behavior and authorization inputs.

### Support / Security / Admin Operator
A privileged internal actor who may inspect or correct workspace-related issues through bounded, policy-constrained, audited workflows.

## Ownership Model

### Workspace and Organization Domain Owns
- canonical workspace and organization records
- scope identifiers and parent/child hierarchy
- workspace and organization lifecycle posture
- durable ownership/control-path semantics
- current-workspace runtime selector semantics
- workspace-scoped product attachment semantics
- publication of canonical workspace/organization events
- correction lineage for workspace and organization record changes in coordination with admin and audit domains

### Workspace and Organization Domain May
- expose canonical reads for scope resolution
- validate whether a workspace belongs to an organization
- expose parent/child hierarchy metadata
- expose safe summaries for low-risk product consumption
- coordinate with membership, authorization, entitlement, and product domains through explicit contracts

### Workspace and Organization Domain MUST NOT
- redefine identity truth
- redefine session truth
- redefine membership lifecycle in full depth
- redefine role/permission semantics
- redefine final effective-permission outcomes
- redefine entitlement truth
- allow products to invent incompatible shared scope models

### Product Domains MAY
- define product-local resources and sub-scopes beneath a valid account, workspace, or organization context
- consume canonical workspace and organization truth
- persist local UX state for current workspace selection

### Product Domains MUST NOT
- replace canonical workspace existence with product-local “team” semantics
- widen product-local scope into platform-shared authority
- treat current tab or UI context as proof of workspace membership
- create hidden global scope behavior from one workspace-local concept

## Authority / Decision Model

The collaborative-scope decision model is layered.

### Upstream Layers Provide
- canonical account identity
- valid session/runtime authentication state
- explicit requested scope inputs
- product namespace or object-owner hints where relevant

### Workspace and Organization Domain Decides
- whether the referenced workspace or organization exists
- whether the hierarchy between organization and workspace is valid
- what the canonical current scope identifiers are
- whether the scope lifecycle posture permits downstream use
- whether current-workspace selection is valid as runtime context

### Membership and Authorization Domains Decide
- whether the actor is structurally attached to that scope
- which roles, permissions, scoped grants, or effective-permission outcomes apply within it

### Entitlement Domain Decides
- whether the subject is commercially or policy eligible for gated capabilities in that scope

This ordering is mandatory. Workspace and organization provide collaborative context. They do not absorb identity, membership, authority, or entitlement. fileciteturn31file0L402-L415

## State Model

At minimum, FUZE SHOULD support the following semantic states for workspace and organization records:

### Workspace States
- `pending_setup`
- `active`
- `restricted`
- `suspended`
- `archived`
- `closed`

### Organization States
- `pending_setup`
- `active`
- `restricted`
- `suspended`
- `archived`
- `closed`

### Current Workspace Selection State
Current workspace selection is not a canonical lifecycle state of the workspace itself. It is runtime selector state associated with an actor/session context and MUST remain separately represented.

### State Rules
- state transitions MUST be explicit, durable, auditable, and replay-safe where mutation retries are possible
- restrictive states MUST outrank stale derived views or cached product assumptions
- archived or closed workspaces MUST NOT remain silently usable through stale product caches
- parent/child hierarchy changes MUST preserve lineage instead of hiding history
- control-path changes affecting ownership continuity MUST remain reconstructable

## Lifecycle / Workflow Model

### 1. Scope Creation
Workspace or organization creation establishes new collaborative scope. Creation MUST define:
- canonical scope identifier
- scope category
- lifecycle state
- initial controlling authority path
- parent organization reference where applicable
- audit and correlation references

### 2. Scope Admission and Attachment
Products or product-local resources may attach only to valid canonical scope. Product attachment MUST preserve the rule that workspace and organization remain the owning collaborative context.

### 3. Current Workspace Selection
A user may switch among workspaces they already belong to. This changes runtime context only and MUST NOT create new membership or authority. fileciteturn31file0L322-L324

### 4. Scope Lifecycle Change
Restriction, suspension, archival, closure, or restoration of a workspace or organization MUST propagate safely to downstream authorization, entitlement, and product behavior without hidden semantic downgrade.

### 5. Ownership / Control-Path Change
Transfer, correction, or containment of workspace or organization control MUST preserve minimum-owner or equivalent control-path continuity and MUST use bounded workflows for sensitive cases. fileciteturn31file0L361-L370

### 6. Scope Correction
If workspace or organization truth is wrong or unsafe, correction MUST occur through explicit bounded workflows with lineage, not through hidden destructive edits.

## Invariants

1. A workspace is a durable collaborative object, not a UI tab or filter. fileciteturn31file0L185-L193
2. A single account may belong to multiple workspaces. fileciteturn31file0L287-L291
3. Workspace scope is resolved only after authentication succeeds. fileciteturn30file3L58-L65
4. Current workspace selection does not create membership or permission. fileciteturn31file0L293-L299
5. Membership is necessary for many actions but is not final authority. fileciteturn31file0L282-L286
6. Product-local collaborative models remain subordinate to canonical workspace and organization truth. fileciteturn31file0L375-L386
7. Wallet-aware context MUST NOT silently become workspace ownership, billing control, or platform admin authority. fileciteturn31file0L394-L400
8. Restriction or review posture on workspace or organization outranks stale grants and stale product assumptions.
9. Derived views remain derived and regenerable.
10. Degraded runtime conditions MUST NOT cause hidden truth substitution.

## Functional Rules

### Workspace Primary Scope Rule
FUZE MUST treat workspace as the primary collaborative operating scope for shared product usage, shared settings, shared resources, and downstream authority evaluation. fileciteturn31file0L181-L200

### Organization Umbrella Rule
Where organization exists distinctly, it MUST act as broader umbrella structure and MUST NOT erase workspace as the primary actionable scope. fileciteturn31file0L271-L279

### Runtime Selector Rule
Current-workspace state MAY be stored for convenience, but it MUST NOT be treated as canonical membership or authorization truth. fileciteturn31file0L293-L299

### Scope-Resolution Rule
Scope MUST be resolved before scoped grants or effective permission are evaluated, and ambiguous scope MUST fail closed or route to review rather than silently falling back to broader scope. fileciteturn31file1L341-L351

### Product Attachment Rule
Products MAY define product-local sub-scopes and resources, but they MUST attach those to valid canonical account, workspace, or organization scope and MUST NOT create alternate shared-scope systems. fileciteturn31file1L431-L440

### Ownership Continuity Rule
Workspace or organization control-path changes MUST preserve at least one legitimate control route or enter explicit review/correction posture instead of stranding the scope.

### Restriction Rule
If workspace or organization lifecycle posture is restricted, suspended, or under review, downstream authorization and product use MUST reflect that stronger constraint rather than stale structural grants.

### No Wallet Shortcut Rule
Wallet status or wallet-aware eligibility context MUST NOT silently become workspace authority or control. fileciteturn30file6L17-L33

## Permission / Access Considerations

This document does not own final authorization, but it imposes mandatory constraints:

- protected actions MUST reference resolvable canonical collaborative scope
- workspace or organization selection alone MUST NOT authorize anything
- valid session alone MUST NOT authorize workspace actions
- membership presence alone MUST NOT become final permission
- products MUST evaluate access only after identity, session, scope, and membership are known in the required order fileciteturn31file0L379-L393
- high-impact actions SHOULD use fresh canonical workspace and organization state, not stale UI assumptions

## Entitlement Considerations

Entitlement remains separate from collaborative scope.

Required rules:
- account-, workspace-, or organization-attached entitlement MAY affect capability eligibility, but does not redefine workspace or organization truth fileciteturn31file2L39-L52
- workspace entitlement does not create workspace membership
- organization entitlement does not automatically create cross-workspace authority
- restricted or missing entitlement is distinct from missing workspace relationship or missing permission

## API / Contract Implications

The platform SHOULD expose workspace and organization behavior through explicit contracts.

### Workspace / Organization APIs SHOULD support:
- canonical scope reads by identifier
- current-workspace selection updates as bounded runtime-context operations
- hierarchy reads for organization → workspace relationships
- lifecycle-state reads and controlled mutations
- safe product-attachment references
- machine-readable mismatch, not-found, restricted, suspended, and review-required outcomes

### Contract Rules
- mutation-capable endpoints MUST support correlation identifiers
- idempotency is required where retries are plausible, especially for creation, correction, transfer, archive, restore, and current-workspace update flows
- current-workspace update APIs MUST NOT create membership or authority as side effects
- product APIs MUST pass explicit scope identifiers rather than relying on presentation-layer assumptions
- admin/control-plane routes for ownership correction or containment require stronger authorization and audit posture

## Event / Async Implications

Workspace and organization changes are cross-service coordination events.

Representative semantic event families include:
- `workspace.created`
- `workspace.updated`
- `workspace.lifecycle_changed`
- `workspace.archived`
- `workspace.closed`
- `workspace.restored`
- `workspace.current_context_changed`
- `organization.created`
- `organization.updated`
- `organization.lifecycle_changed`
- `organization_workspace_attached`
- `organization_workspace_detached`
- `workspace_control_path_changed`

Event rules:
- canonical events MUST be emitted only after owning-domain state commits
- async failure MUST NOT redefine canonical scope truth
- event consumers MAY refresh caches or projections but MUST NOT become scope owners
- events MUST preserve enough parent/child context for downstream authorization, entitlement, and audit consumers

## Data Model / Storage Implications

At minimum, the platform SHOULD support the following durable semantic structures:

### `workspace`
Representative semantic fields:
- `workspace_id`
- lifecycle state
- current controlling authority reference
- organization reference where applicable
- canonical settings/profile metadata
- created_at / updated_at
- correction/supersession reference where applicable

### `organization`
Representative semantic fields:
- `organization_id`
- lifecycle state
- current controlling authority reference
- parent/umbrella metadata where applicable
- created_at / updated_at

### `workspace_runtime_context`
Representative semantic fields:
- `account_id`
- current workspace reference
- session or runtime-context reference where applicable
- updated_at
- selection source

### `workspace_product_attachment`
Representative semantic fields:
- product namespace
- parent scope reference
- attachment status
- created_at / updated_at
- correction/supersession reference where applicable

### Data Rules
- workspace and organization truth MUST remain durable canonical records
- current-workspace selector state MUST remain distinct from membership and grants
- derived views and product caches MUST NOT become write owners
- correction SHOULD preserve lineage rather than destructively flattening history
- parent/child scope references MUST be durable enough to support downstream resolution and audit

## Security / Risk / Abuse Controls

Workspace and organization are security-sensitive because scope confusion becomes authority confusion. FUZE MUST preserve:

- fail-closed behavior on unresolved or mismatched collaborative scope
- stronger controls for ownership transfer or control-path correction
- anti-self-escalation protections where scope changes interact with downstream authorization
- rapid suppression of stale workspace assumptions when restriction or containment is applied
- prevention of product-local shadow authority through hidden team systems
- auditability of scope creation, correction, lifecycle change, and control-path mutation

## Boundary Violation Detection / Non-Canonical Patterns

The following patterns are explicitly non-canonical and forbidden:

- product-local “team” tables acting as platform-shared collaborative truth
- current-workspace selection treated as durable permission
- workspace membership treated as full unrestricted authority
- organization assumed to grant all workspace permissions without explicit policy
- wallet status treated as workspace ownership or admin power
- entitlement state treated as collaborative-scope source-of-record
- support dashboards directly mutating workspace truth without bounded workflow and audit lineage
- destructive scope correction that erases historical lineage

Implementations SHOULD detect and surface these violations through monitoring, tests, and audit review.

## Audit / Traceability Requirements

FUZE MUST be able to determine:

- which workspace or organization a request targeted
- how current scope was resolved
- whether current-workspace selection matched canonical scope truth
- which lifecycle state applied at the time
- which control path or ownership structure existed
- how a workspace was attached to an organization or product namespace
- why a scope mismatch caused deny or review
- which operator or service executed scope correction or containment
- whether a visible summary is canonical or derived

This aligns with the broader access-traceability requirement that access-domain systems preserve actor, session, scope, target, and policy context in reconstructable form. fileciteturn30file15L39-L50

## Failure Handling / Edge Cases

### Valid Session, No Workspace
Protected workspace action MUST fail closed until a valid canonical workspace is resolved. This is consistent with the broader scope-first requirement. fileciteturn31file1L341-L351

### Workspace Selected, Membership Missing
The workspace may remain visible in stale UI state, but scope-bound action MUST be denied until structural membership and authorization inputs succeed. fileciteturn31file0L293-L299

### Product Resource Owned by Different Workspace
Canonical object-owner scope or product-attachment metadata MUST win. The platform MUST fail or reroute explicitly, not silently reuse the currently selected workspace.

### Organization Exists, Workspace Missing
Organization umbrella presence MUST NOT silently manufacture workspace authority or membership.

### Ownerless-Risk During Control-Path Change
Ordinary self-service action MUST NOT strand a workspace or organization without legitimate control path. The platform MUST block, require alternate authority, or enter explicit review/correction posture. fileciteturn31file0L361-L370

### Wallet-Aware Product Feature Inside Workspace
Wallet-aware context MAY be consumed for eligibility or experience, but MUST NOT override workspace truth, membership, or authorization. fileciteturn30file6L17-L33

### Degraded Cache or Derived View
If a derived workspace summary is stale, canonical workspace and organization records win.

## Operational Considerations

Operational systems SHOULD support:
- observability for scope creation, lifecycle change, selection update, and correction
- monitoring for repeated scope mismatch, ownerless-risk incidents, and product-local shadow-scope attempts
- safe support tooling that reads derived summaries without bypassing canonical mutation boundaries
- correlation across identity, session, workspace, membership, authorization, entitlement, and audit systems
- deterministic operator tooling for workspace correction and containment
- safe degraded-mode behavior that pauses unsafe automation rather than guessing

The operational model MUST preserve the principle that workspace and organization are shared platform truths, not product-local conveniences. fileciteturn31file0L416-L438

## Migration / Compatibility / Supersession Considerations

- migrations MUST remove implementation patterns where product-local team models act as canonical workspace truth
- older implementations that imply login success equals workspace authority, or current UI context equals collaborative truth, are superseded within this scope
- compatibility layers MAY preserve older UI labels temporarily, but canonical workspace and organization semantics MUST remain platform-owned
- future products MUST integrate with the same collaborative scope model rather than inventing parallel shared-scope systems
- state names MAY evolve, but the semantic distinctions defined here MUST be preserved

## Implementation-Contract Guardrails

Downstream implementations MUST preserve all of the following:

1. `account_id` remains the durable actor anchor
2. workspace remains the primary actionable collaborative scope
3. organization remains a broader umbrella only where explicitly modeled
4. current-workspace selection remains runtime context, not durable grant truth
5. identity, session, workspace, membership, authorization, entitlement, and wallet-aware context remain distinct truth classes
6. products do not become owners of canonical collaborative scope truth
7. scope resolution occurs before scoped authorization and effective-permission evaluation
8. owner/control-path continuity remains protected
9. derived views remain derived and regenerable
10. degraded runtime conditions do not cause hidden semantic downgrade or truth substitution
11. high-impact scope mutations remain idempotent where retries are plausible and auditable
12. downstream docs and teams MUST NOT optimize away lineage, explicit lifecycle state, or correction semantics where those elements protect continuity, security, or auditability

## Downstream Execution Staging

Recommended downstream staging order:

1. stabilize canonical identity and session boundaries
2. stabilize workspace and organization semantics
3. stabilize membership lifecycle semantics
4. stabilize role/permission and scoped-authorization binding to scope
5. stabilize effective-permission evaluation ordering
6. stabilize entitlement and product gating consumption
7. stabilize admin correction, audit, and control-plane workflows
8. build support, analytics, and reporting read models over canonical scope records

This ordering aligns with the broader FUZE rule that workspace and authorization sequencing is downstream of identity/session and upstream of entitlement-aware capability use. fileciteturn30file4L16-L31

## Required Downstream Specs / Contract Layers

This specification requires compatible downstream refinement and implementation-contract work in at least the following areas:

- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `WORKSPACE_MEMBERSHIP_LIFECYCLE_SPEC.md`
- `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- product integration specifications
- support and control-plane workflow specifications

## Canonical Examples / Anti-Examples

### Canonical Example 1 — One Account, Many Workspaces
A single FUZE account belongs to multiple workspaces and switches among them without changing identity. This is canonical. fileciteturn31file0L287-L291

### Canonical Example 2 — Runtime Switch Without New Authority
A user switches current workspace in the UI. Runtime context changes, but no new membership or role is created. This is canonical. fileciteturn31file0L322-L324

### Canonical Example 3 — Organization With Multiple Workspaces
An organization contains multiple workspaces under shared reporting or administration while each workspace remains an actionable collaborative scope. This is canonical. fileciteturn31file0L271-L279

### Canonical Example 4 — Product Operates Inside Workspace
A product provides product-local resources and UX inside a valid workspace context while consuming canonical workspace truth instead of replacing it. This is canonical. fileciteturn31file0L375-L386

### Anti-Example 1 — UI Tab As Scope Truth
A frontend-selected workspace tab is treated as proof of membership and permission. This is forbidden. fileciteturn31file0L293-L299

### Anti-Example 2 — Product-Local Team Replaces Workspace
A product invents its own shared “team” model and uses it as canonical platform scope for billing, admin, or shared authorization. This is forbidden. fileciteturn31file0L336-L344

### Anti-Example 3 — Login Equals Workspace Authority
A valid session is treated as sufficient proof that the actor may act in any selected workspace. This is forbidden. fileciteturn31file0L146-L148

### Anti-Example 4 — Wallet As Workspace Admin Shortcut
Wallet linkage or token holding is treated as workspace ownership, billing control, or platform admin authority. This is forbidden. fileciteturn31file0L394-L400

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
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
- `WALLET_AWARE_USER_SPEC.md`

This specification directly governs or materially informs:

- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `WORKSPACE_MEMBERSHIP_LIFECYCLE_SPEC.md`
- `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- product integration specifications
- support and control-plane workflow specifications

## Explicitly Deferred Items

The following are intentionally deferred to adjacent or downstream specifications:

- exact membership-state machine
- exact role and permission catalogs
- exact organization-specific commercial policy
- exact end-user switching UX
- exact queue-routing and staffing procedures for scope correction
- exact database sharding and service decomposition
- exact product-local object-scope rules
- exact public reporting wording

These deferrals do not weaken the canonical workspace and organization model established here.

## Final Normative Summary

FUZE workspace and organization is the platform capability that defines collaborative operating context after identity and session are valid, and before scoped authorization and effective permission are evaluated. Workspace is the primary actionable collaborative scope. Organization is a broader umbrella where explicitly modeled. Current workspace is runtime context, not durable authority. Membership is structural attachment, not final permission. Products may operate inside canonical scope, but they may not replace it. Wallet-aware context, entitlement posture, reporting surfaces, and support dashboards remain adjacent or derived layers, not scope truth.

This document is the canonical FUZE rule set for collaborative scope. Downstream systems MUST preserve its separations, lifecycle semantics, control-path continuity, and product anti-drift rules.
