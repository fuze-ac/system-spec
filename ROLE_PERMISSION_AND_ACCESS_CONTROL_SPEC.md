# ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC

## Document Metadata

- Document Name: `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Governing Layer: Platform core / role, permission, and access control
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, security, support operations, audit, governance, API design, commerce/billing, operations
- Primary Purpose: Define the canonical FUZE authorization model that turns authenticated canonical accounts operating in resolved scope into explicit, policy-bound allow/deny outcomes through durable role assignments, scoped permissions, bounded inheritance, restriction handling, and auditability without collapsing identity, workspace structure, entitlement, billing truth, product-local policy, or governance control into one ambiguous layer
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
  - `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
  - `WALLET_AWARE_USER_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - `AUTH_IDENTITY_API_SPEC.md`
  - `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- Primary Downstream Dependents:
  - `WORKSPACE_MEMBERSHIP_LIFECYCLE_SPEC.md`
  - `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
  - `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
  - `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
  - `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
  - `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
  - `WORKSPACE_ORGANIZATION_API_SPEC.md`
  - `ROLE_PERMISSION_ACCESS_API_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - commerce/billing/credits specifications
  - product integration specifications
  - control-plane and governance workflow specifications

---

## Purpose

This specification defines the canonical FUZE role, permission, and access-control model.

Its purpose is to make explicit:

- how FUZE turns authenticated canonical account identity plus resolved operating scope into allowed or denied actions
- how role assignment, permission mapping, scope resolution prerequisites, and policy checks interact
- how account scope, workspace scope, product scope, platform-operational scope, and governance-sensitive scope remain distinct
- how authorization must remain separated from identity, authentication, sessions, workspace structure, entitlement, billing truth, wallet-aware participation, and product-local UX
- how sensitive internal actions, workspace control actions, product actions, commercial actions, and governance-aware control actions must be bounded and auditable
- how products, APIs, internal services, and support tools may consume authorization truth without becoming owners of it

This document exists because FUZE is a multi-product, multi-scope ecosystem. In such a platform, login success cannot be treated as authority, workspace presence cannot be treated as permission, billing participation cannot be treated as product control, and token-aware status cannot be treated as admin power. Authorization must therefore be explicit, layered, bounded, and platform-owned.

---

## Scope

This specification governs:

- the canonical access-control model for FUZE
- the distinction among identity, membership, role, permission, access decision, and entitlement
- authorization scopes and their relationships
- canonical role categories and semantic baseline roles
- permission domains and mapping direction
- access-decision prerequisites and rule ordering
- bounded inheritance and restriction behavior
- internal operational and governance-sensitive access boundaries
- audit, data-model, API, event, security, and operational implications of authorization truth

This specification does not define:

- canonical account identity semantics in full depth
- authentication-method lifecycle or session lifecycle
- full workspace/organization lifecycle semantics
- exact membership invitation or onboarding mechanics
- exact policy-engine representation or DSL
- exact effective-permission evaluation algorithm by object type
- exact entitlement or commercial eligibility algorithm
- exact admin containment workflow implementation
- exact product-specific role tables for every product

Those are defined in adjacent or downstream specifications.

---

## Out of Scope

This specification is explicitly out of scope for:

- identity bootstrap and provider-resolution semantics
- login, session issuance, refresh, or revocation semantics
- workspace creation/closure structure in full depth
- product-local UI-only visibility rules that do not represent canonical authorization
- exact pricing, seat, billing, or credits algorithms
- exact wallet verification mechanics
- exact break-glass tooling implementation
- exact query-language syntax for policy evaluation

---

## Design Goals

The design goals of the FUZE role, permission, and access-control model are:

1. make authority explicit across account, workspace, product, platform, and governance-sensitive contexts
2. preserve strict separation between identity and authority
3. preserve strict separation between collaborative scope and authority
4. support both individual and collaborative platform usage
5. support consistent authorization across shared products and shared services
6. keep product innovation possible without allowing product-local shadow authorization truth
7. preserve explicit control around billing, credits, recovery-sensitive actions, support operations, and governance-sensitive operations
8. make access decisions auditable, reviewable, and explainable
9. support least privilege and deny-by-default as durable platform posture
10. remain future-safe for additional products, enterprise structures, and stricter governance controls

---

## Non-Goals

This specification is not intended to:

- treat session existence as authorization success
- treat workspace membership as sufficient permission by itself
- treat entitlement as equivalent to authority
- treat wallet ownership or token status as a substitute for authorization
- make workspace owner equivalent to platform administrator
- allow product-local “super admin” shortcuts for shared platform concerns
- define every object-level permission for every product at this layer
- collapse governance-sensitive operations into ordinary admin rights

---

## Core Principles

### 1. Authentication Is Not Authorization Principle
Authentication proves access to the canonical account. It does not prove workspace membership, scope authority, role assignment, permission grant, entitlement, or policy clearance.

### 2. Scope-Then-Authority Principle
Authorization occurs only after the relevant operating scope has been resolved. Scope is not optional context; it is a prerequisite input.

### 3. Explicit Grant Principle
Authority exists only where it has been explicitly granted through canonical role and permission structures or explicitly approved policy rules.

### 4. Role-Manages / Permission-Enforces Principle
Roles are the human-manageable bundle layer. Permissions are the enforceable action layer. Final enforcement must resolve to concrete permission decisions.

### 5. Deny-by-Default Principle
If authority is not clearly granted, the requested action must be denied.

### 6. Least-Privilege Principle
Roles and permissions must grant only the minimum authority needed for the intended operating responsibility.

### 7. No Implicit Escalation Principle
Membership, product usage, wallet status, billing participation, session continuity, or UI selection must not silently create new authority.

### 8. Product-Consumption Principle
Products consume canonical authorization decisions or canonical role/permission truth. Products do not become owners of shared authorization semantics.

### 9. Restriction-Precedence Principle
Restriction, suspension, risk, review, or account/workspace control state may override otherwise valid role grants.

### 10. Governance-Separation Principle
Governance-sensitive powers require stronger, separate control pathways. They must not be inherited casually from ordinary operational or workspace roles.

---

## Canonical Definitions

### Authorization
The platform process of determining whether a canonical account may perform a requested action in a defined scope under current policy and state.

### Role
A named bundle of authority assigned within a defined scope.

### Permission
A concrete action right or action class that can be evaluated and enforced.

### Scope
The domain context in which a role or permission applies, such as account, workspace, product, operational, or governance-sensitive scope.

### Access Decision
The output of evaluating identity, session validity, scope, membership, role assignment, permission mapping, entitlement inputs where relevant, object state, restriction state, and policy constraints.

### Membership
The structural relationship between a canonical account and a workspace. Membership is not itself final authority.

### Entitlement
A commercial or operational eligibility state that may be required in addition to permission before capability use is allowed.

### Effective Permission
The resolved action-level authorization outcome after roles, permissions, restrictions, entitlement inputs, object conditions, and policy are evaluated.

### Restriction
A state that suppresses ordinary authority even where prior grants exist.

### Governance-Sensitive Action
An action that requires stronger control, approval, or policy than ordinary operational administration.

---

## Architectural Position in the Spec Hierarchy

This document sits below:

- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`

and above:

- `WORKSPACE_MEMBERSHIP_LIFECYCLE_SPEC.md`
- `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`

This document owns the canonical authorization structure and baseline rule set. It does not absorb the deeper downstream ownership of membership lifecycle, evaluation internals, object-level effective permission logic, admin containment, or commercial eligibility logic.

---

## System Boundaries

This document governs only the following platform-owned boundaries:

- canonical role semantics
- canonical permission semantics
- authorization scope semantics
- baseline role categories and bounded inheritance posture
- canonical distinction between authorization and adjacent domains
- minimum access-decision ordering
- restriction and deny semantics
- auditability expectations for access mutations and sensitive access decisions
- structural data-model direction for role and permission truth

It does not govern:

- identity bootstrap
- session lifecycle
- workspace existence and ownership structure in full depth
- exact product-specific permission tables
- exact per-object policy expressions
- pricing and commercial truth
- wallet or token verification truth
- governance workflow mechanics in full depth

---

## Adjacent Boundaries

### Identity Domain
Owns canonical account identity. Authorization consumes identity truth but does not define it.

### Auth / Session Domain
Owns authentication and session validity. Authorization depends on session validity but does not own it.

### Workspace / Organization Domain
Owns workspace existence, ownership relationships, lifecycle, and membership structure. Authorization consumes resolved scope and membership truth but does not replace them.

### Membership Lifecycle Domain
Owns invitation, activation, restriction, removal, and membership-state transitions. This specification assumes that membership state is canonical input.

### Scoped Authorization Model Domain
Owns the deeper structural model for how authorization binds to resolved scopes and possibly nested or object-level context.

### Effective Permission Domain
Owns detailed action-level decision logic, object conditions, and final policy-resolution semantics beyond the canonical baseline established here.

### Entitlement / Capability Gating Domain
Owns commercial and operational eligibility gating. Authorization may still deny a capability even when role/permission is valid if entitlement is absent.

### Security / Risk Domain
Owns elevated risk, abuse, review, and emergency restriction controls that can override ordinary authority.

### Governance / Control-Plane Domain
Owns stronger approval, dual-control, or governance workflows for governance-sensitive actions.

### Product Domains
May define product-local roles and object-level rules on top of platform truth, but may not replace shared authorization semantics.

---

## Roles / Actors / Entities

### End User
A canonical account acting on its own behalf or within a workspace/product scope.

### Workspace Member
A canonical account with a membership relationship in a workspace.

### Workspace Owner
The highest ordinary workspace-control role, bounded by platform policy and non-inheritance into platform/governance powers.

### Workspace Administrator
An elevated workspace role for operational administration inside workspace boundaries.

### Workspace Billing Manager
A role for workspace commercial and billing-related actions subject to downstream policy.

### Workspace Operator
A role for shared operational and workflow actions.

### Workspace Viewer
A read-oriented role where limited observation is permitted.

### Product Operator / Product Manager / Product Editor
Product-scoped roles used inside one product domain under resolved account/workspace scope.

### Support Operator
Internal role for bounded support and corrective action workflows.

### Finance Operator
Internal role for billing/credits/commercial corrective workflows.

### Risk Reviewer
Internal role for abuse, fraud, and restriction review.

### Platform Administrator
Internal role for broad operational administration, still bounded by policy.

### Governance-Aware Actor
A role or approval actor permitted to participate in governance-sensitive workflows.

---

## Ownership Model

### Authorization Domain Owns
- canonical role definitions at the platform layer
- canonical permission naming direction at the platform layer
- role-to-permission baseline mapping structures
- scope applicability semantics
- inheritance constraints
- access-decision baseline ordering
- restriction and deny semantics at the authorization layer
- canonical authorization audit references in coordination with audit domain

### Authorization Domain May
- expose canonical role catalogs
- expose permission catalogs and mapping summaries
- resolve baseline grants for downstream evaluation
- publish domain events after canonical access mutations
- coordinate with downstream policy/effective-permission engines

### Authorization Domain Must Not
- redefine account identity
- redefine workspace or membership truth
- redefine entitlement truth
- define billing or credits ledger truth
- define wallet-link truth
- bypass security/risk overrides
- allow products to become canonical owners of shared authorization

### Product Domains May
- consume canonical role/permission truth
- define product-local roles and permissions within approved namespaces
- add object-level checks within product boundaries
- surface user-visible explanations of access state

### Product Domains Must Not
- define hidden shared platform roles
- bypass workspace or platform authorization for shared concerns
- treat entitlement as complete authorization
- grant platform-level admin power through product-local roles
- treat token status as admin or workspace-control authority

---

## Authority / Decision Model

Authorization is a layered decision system.

### Identity/Auth Layer Provides
- canonical account identity
- valid session/runtime presence
- risk and recovery-related restrictions where applicable

### Workspace Layer Provides
- resolved collaborative scope
- membership presence and status
- workspace lifecycle state

### Authorization Layer Decides
- which role grants apply in the resolved scope
- which permissions are potentially granted
- whether inheritance is valid
- whether restriction rules suppress ordinary grants
- whether the requested action is permitted structurally

### Entitlement / Capability Layer Decides
- whether the commercially or operationally required capability is enabled

### Security / Governance Layers May Override
- whether the action must be denied, restricted, escalated, or routed for review

This layered model is mandatory. No single layer may be treated as sufficient for all access decisions.

---

## Scope Model

Roles and permissions in FUZE must always be tied to explicit scope. Scope must never be implicit.

### 1. Account Scope
Authority tied to the user’s own canonical account.

Representative examples:
- update own profile
- manage own linked logins
- view own receipts
- link own wallet where allowed
- view own audit history where policy allows

### 2. Workspace Scope
Authority tied to collaborative scope.

Representative examples:
- invite member
- remove member
- assign workspace role
- manage shared settings
- spend workspace credits where separately allowed
- manage workspace billing surface where separately allowed

### 3. Product Scope
Authority tied to a product capability within an account or workspace context.

Representative examples:
- manage QTB watchlists
- operate Botmad workflows
- edit HerHelp-generated assets
- manage AIE content
- manage ZAGA utility configuration

### 4. Platform Operational Scope
Authority tied to internal platform operations.

Representative examples:
- restrict account
- suspend workspace
- review abuse case
- execute support correction
- inspect system operations dashboards

### 5. Governance-Sensitive Scope
Authority tied to actions requiring stronger control-plane or governance-aware control.

Representative examples:
- initiate payout-cycle operational transition
- approve high-impact credits control path
- update public registry interpretation under policy
- perform reserve-related operational coordination
- trigger emergency control posture

### Scope Rules
- scope must be resolved before authorization
- scope selection does not create authority
- the same actor may hold different roles in different scopes
- higher-risk scopes require stronger control and may use separate approval flows
- cross-scope assumptions are disallowed unless explicitly defined

---

## Canonical Role Categories

FUZE must support role categories across multiple levels.

### Account-Level Roles
These govern self-service and account-bound actions.

Representative categories:
- account self-service
- restricted account posture
- support-reviewed account posture

These are not substitutes for workspace or platform roles.

### Workspace-Level Roles
These govern collaborative operation.

Minimum semantic baseline:
- workspace owner
- workspace admin
- workspace billing manager
- workspace operator
- workspace member
- workspace viewer

### Product-Level Roles
These extend platform/workspace structure inside a product.

Representative categories:
- product operator
- product manager
- product editor
- product reviewer
- product viewer

All product roles must remain namespaced and product-bounded.

### Platform Operational Roles
These govern internal operational access.

Minimum semantic baseline:
- support operator
- finance operator
- risk reviewer
- reporting operator
- platform admin

### Governance-Aware Roles
These govern stronger control paths.

Representative categories:
- governance approver
- control-plane reviewer
- payout-cycle operator
- registry maintainer
- emergency operations actor

Not every deployment requires every exact role at launch, but the semantic categories must remain expressible.

---

## Baseline Role Semantics

### Workspace Owner
Highest ordinary workspace authority.

May typically:
- manage membership
- manage workspace settings
- assign ordinary workspace roles subject to policy
- manage shared workspace resources
- initiate archive or closure flows where policy allows

Must not automatically:
- become platform admin
- receive governance-sensitive authority
- bypass entitlement, billing, or product-safety rules
- bypass security/risk controls

### Workspace Admin
Operational administrator inside the workspace.

May typically:
- invite members
- manage many settings
- administer product access within workspace rules
- view usage and reporting where allowed

Must not automatically:
- transfer ownership unless separately allowed
- perform every billing-sensitive action
- perform governance-sensitive actions

### Workspace Billing Manager
Commercial role tied to workspace billing and commercial surfaces.

May typically:
- view billing summary
- view invoices and receipts
- manage subscriptions where supported
- manage payment methods where supported
- view credits usage where policy allows

Must not automatically:
- become workspace owner
- gain product or governance authority unrelated to billing

### Workspace Operator
Operational role for shared workflows and product operations.

### Workspace Member
Normal active participant whose authority remains bounded by workspace role, product permissions, and entitlements.

### Workspace Viewer
Read-only or limited-observation role.

### Internal Operational Roles
Support, finance, risk, reporting, and platform admin roles are bounded internal roles and must remain explicitly permission-scoped.

### Governance-Aware Roles
These are never implied by workspace status, product ownership, or ordinary admin convenience.

---

## Permission Model

Permissions are the enforceable action layer. FUZE should prefer durable, explicit, namespaced permission naming.

### Permission Design Rules
- permission names should be durable and semantically clear
- permissions should be namespaced by domain
- permissions should describe actions, not vague status
- permissions should remain separate from role names
- permissions should be bindable to defined scopes
- permissions should support policy evolution without semantic drift

### Representative Permission Domains

#### Identity / Account Permissions
Examples:
- `account.profile.update_self`
- `account.linked_login.manage_self`
- `account.audit.view_self`
- `account.recovery.initiate_self`

#### Workspace Permissions
Examples:
- `workspace.member.invite`
- `workspace.member.remove`
- `workspace.role.assign`
- `workspace.settings.manage`
- `workspace.archive.initiate`
- `workspace.owner.transfer`

#### Billing / Commerce / Credits Permissions
Examples:
- `billing.invoice.view`
- `billing.subscription.manage`
- `billing.payment_method.manage`
- `credits.balance.view`
- `credits.workspace.spend`
- `credits.adjustment.issue_internal`

#### Product Permissions
Examples:
- `product.qtb.signals.manage`
- `product.botmad.workflow.approve`
- `product.herhelp.app.manage`
- `product.aie.content.edit`
- `product.zaga.utility.manage`

#### Reporting Permissions
Examples:
- `report.internal.view`
- `report.export.execute`
- `report.transparency_inputs.view`
- `report.payout_ledger.view`

#### Platform Operational Permissions
Examples:
- `admin.account.restrict`
- `admin.workspace.suspend`
- `admin.support_correction.execute`
- `admin.system_dashboard.view`

#### Governance-Sensitive Permissions
Examples:
- `governance.payout_cycle.transition`
- `governance.control_plane.change_approve`
- `governance.registry.mapping_update`
- `governance.reserve_coordination.execute`

These examples are semantic direction, not frozen final tables.

---

## Access Decision Model

The canonical FUZE access-decision flow must remain explicit even if optimized internally.

1. resolve canonical account from valid session
2. verify session and account state still permit access evaluation
3. resolve requested scope
4. resolve workspace membership where workspace context exists
5. verify scope state and membership state are active enough for the action
6. resolve assigned roles within the relevant scope
7. derive candidate permissions from role assignment and bounded inheritance
8. check whether required permission is granted
9. check relevant entitlement or commercial gating if the capability requires it
10. apply object-state, restriction, security, and policy checks
11. return `allow`, `deny`, or `escalate/review_required`

### Access Model Rules
- authentication success is necessary but not sufficient
- valid session is necessary but not sufficient
- workspace membership is necessary for many workspace actions but not sufficient
- entitlement is necessary for some capabilities but not sufficient if permission is absent
- policy or restriction may deny an action even when ordinary role/permission appears to allow it
- review-required is a valid first-class outcome for sensitive actions

---

## Distinction Between Role, Permission, and Entitlement

FUZE must preserve the following distinctions:

### Role
Defines structural authority bundle in a scope.

### Permission
Defines the precise action allowed.

### Entitlement
Defines whether the account or workspace is commercially or operationally eligible to use a product or feature.

### Required Separation Rules
- a role is not an entitlement
- an entitlement is not a permission
- a permission is not a product plan
- a workspace owner is not automatically a billing-approved actor for every commercial action
- a paid plan does not automatically grant all high-risk actions to all members

This separation is essential to avoiding authorization drift.

---

## Permission Inheritance and Resolution

FUZE may support inheritance, but inheritance must be bounded and explicit.

### Required Inheritance Rules
- higher roles may include lower-role permissions within the same scope only where explicitly mapped
- workspace roles may expose product access only where product policy allows
- product roles may refine capability inside the product but may not enlarge platform authority automatically
- platform operational roles do not automatically inherit governance-sensitive powers
- governance-sensitive permissions must not be inherited casually from workspace or ordinary admin roles
- inheritance must be deterministic and auditable

### Example
A workspace owner may inherit:
- workspace admin permissions
- workspace member permissions
- workspace viewer permissions

A workspace owner must not automatically inherit:
- support operator privileges
- finance remediation privileges
- governance registry-modification privileges
- payout-cycle control privileges

### Direct Overrides
Direct permission overrides should remain rare, explicit, visible, and auditable. The platform should prefer stable role-based grants for ordinary behavior.

---

## Restriction, Deny, and Escalation Model

Authorization must support explicit `deny`, `restricted`, and `review-required` outcomes.

### Representative Deny Scenarios
- no valid session
- missing membership
- missing permission
- inactive or unavailable scope
- insufficient entitlement
- restricted or suspended account
- restricted or suspended workspace
- policy-blocked sensitive action
- governance-sensitive action attempted through ordinary role

### Restriction Rules
- restricted actors or scopes may remain visible and auditable
- restrictions suppress ordinary action rights until reinstatement or review completion
- restriction state outranks stale cached grants

### Escalation Rules
An access decision may return review-required when:
- policy requires higher approval
- the action is governance-sensitive
- security/risk signals elevate the request
- admin correction or containment workflow is required
- entitlement or object-state ambiguity requires explicit handling

Deny is not an implementation inconvenience. It is a core platform state.

---

## Billing, Credits, and Commercial Access Considerations

Billing and credits are especially sensitive authorization domains.

### Required Rules
- products must not redefine billing or credits authority locally
- billing-visible and billing-mutating actions must be separately permissioned
- spending authority and viewing authority should be distinguishable
- internal corrective finance actions must remain internal-only permissions
- commercial attachment does not automatically imply broad workspace admin power
- commercial entitlement evaluation remains downstream, but authorization must provide the bounded actor permission layer

Billing and credits are a shared trust surface and therefore require platform-controlled authorization.

---

## Wallet-Aware and Holder-Aware Access Considerations

Wallet-aware context may influence visibility, eligibility, or experience, but it must not replace authorization.

### Allowed Patterns
- show holder-aware UI
- enable holder-gated features where entitlement/policy allows
- use wallet linkage as one eligibility input for payout or participation logic

### Disallowed Patterns
- grant workspace admin because a wallet holds tokens
- bypass billing or credits controls because a wallet is linked
- grant support or platform admin privileges based on token ownership
- treat token possession as sufficient authority for shared platform actions

Wallet-aware participation is adjacent to authorization, not a replacement for it.

---

## Internal Operational Access Model

FUZE must define a bounded internal operational access model.

### Minimum Internal Role Families
- support operator
- finance operator
- abuse/risk reviewer
- reporting operator
- platform admin

### Internal Access Rules
- each role must be permission-bounded
- support roles must not automatically gain finance or governance-sensitive powers
- platform admin remains bounded by policy, audit, and control-plane restrictions
- privileged internal actions require durable audit records
- stronger control or dual approval may be required for certain action classes
- operational convenience must not outrank authorization structure

---

## Governance-Sensitive Access Model

Certain actions require stronger control than ordinary admin rights.

### Governance-Sensitive Action Classes May Include
- payout-cycle control transitions
- reserve-related off-chain operational actions
- public registry changes affecting contract or wallet interpretation
- high-impact credits control paths
- emergency-control triggers
- actions linked to multisig/timelock-governed execution paths

### Required Rules
- governance-sensitive actions must be explicitly classified
- such actions must not be granted through ordinary workspace roles
- such actions must not be assumed from product ownership
- such actions should route through governance-aware or control-plane workflows
- such actions should support pending/approved/rejected semantics where appropriate
- such actions must be auditable and policy-referenced

This separation is required for FUZE’s long-term trust model.

---

## Permission / Access Considerations

This document governs authorization structure, not all membership or scope lifecycle details.

### Mandatory Constraints
- authorization must execute after scope resolution
- active workspace context is required before mutating workspace-owned resources
- current scope selector is runtime context only and must not create authority
- self-escalation must be blocked
- owner continuity safeguards must exist for owner-removal or ownership-transfer actions
- object-level checks may further narrow ordinary role grants
- cached permission views are non-canonical for sensitive actions

---

## Entitlement Considerations

Entitlement is an adjacent but separate domain.

### Required Rules
- workspace or account may be the subject of entitlement
- valid permission does not guarantee entitlement
- valid entitlement does not guarantee permission
- entitlement checks occur after scope resolution and in coordination with permission evaluation
- product surfaces must not present entitlement as equivalent to authorization

This document establishes the structural separation; downstream entitlement specs define detailed gating behavior.

---

## API / Contract Implications

The platform should expose authorization through explicit domain boundaries.

### Authorization APIs Should Own
- role catalog reads
- permission catalog reads
- role assignment and revocation within approved scope
- access-evaluation requests
- effective access summaries where appropriate
- admin-safe access mutation pathways
- audit-friendly access mutation results

### Identity APIs Should Not
- own role or permission truth, even if they expose derived summaries

### Session APIs Should Not
- pretend session presence is equivalent to authority

### Workspace APIs Should Not
- redefine permission logic, though they may expose structural membership and owner data consumed by authorization

### API Rules
- all mutation-capable endpoints must support correlation identifiers
- idempotency is required where retries are plausible, especially for role assignment, role removal, owner-transfer-related authority updates, restriction changes, and admin corrections
- control-plane/admin APIs require stronger authorization and richer audit metadata
- review-required results must be first-class contract outcomes where sensitive actions require escalation

---

## Event / Async Implications

Authorization and access mutations must emit durable events sufficient for audit and downstream coordination.

Representative semantic events include:
- `authz.role_assigned`
- `authz.role_revoked`
- `authz.permission_mapping_changed`
- `authz.scope_restriction_changed`
- `authz.access_denied_logged`
- `authz.owner_transfer_authority_changed`
- `authz.admin_override_requested`
- `authz.admin_override_completed`
- `authz.governance_action_requested`
- `authz.governance_action_approved`
- `authz.governance_action_rejected`

### Event Rules
- events must be emitted only after canonical state transitions commit
- async delivery failure must not redefine canonical authorization truth
- consumers may react but may not become owners of shared authorization semantics
- retries must be replay-safe and idempotent
- event semantics must preserve distinction between structural role changes and transient evaluation outcomes

---

## Data Model / Storage Implications

At minimum, the platform should support the following durable semantic structures.

### `role_assignment`
Representative semantic fields:
- `assignment_id`
- `subject_account_id`
- role name
- scope type
- scope identifier
- assigned_by
- assigned_at
- expires_at where applicable
- status / removal marker
- correlation reference

### `role_definition`
Representative semantic fields:
- role name
- scope applicability
- semantic category
- inheritance references
- status
- policy version reference

### `permission_mapping`
Representative semantic fields:
- role name
- permission name
- scope applicability
- mapping status
- policy/config version
- effective date markers where applicable

### `access_restriction`
Representative semantic fields:
- subject reference
- scope reference
- restriction type
- reason code
- policy reference
- applied_by
- applied_at
- cleared_at where applicable

### `access_audit_reference`
Representative semantic fields:
- action ID
- actor
- target
- scope
- requested action
- result
- reason/policy context
- timestamp
- correlation reference

### Data Rules
- role and permission truth must remain durable canonical entities or controlled configuration with durable versioning
- derived dashboards, caches, analytics views, and product summaries must not become write owners
- corrections should prefer explicit lineage and terminal transitions over hidden destructive rewrite
- object-level effective permission caches are advisory only for sensitive actions

---

## Security / Risk / Abuse Controls

Authorization is a security boundary.

The platform must preserve:
- deny-by-default posture
- anti-self-escalation safeguards
- explicit restriction precedence
- stronger control for high-sensitivity mutations
- bounded internal operational access
- separated governance-sensitive control paths
- auditability for access mutation and privileged actions
- resistance to stale-cache authorization mistakes for sensitive actions
- product inability to bypass canonical access controls through local assumptions

Weak authorization structure would directly undermine account safety, workspace safety, billing controls, credits integrity, product trust, and governance trust.

---

## Audit / Traceability Requirements

FUZE must be able to determine:

- who assigned or removed a role
- which scope a grant applied to
- which policy or version defined the permission mapping
- why an access mutation occurred
- when a restriction overrode ordinary access
- why a sensitive action was denied or routed to review
- which internal operator executed privileged correction
- whether a visible role/permission summary is canonical or derived
- how owner, membership, billing, or workspace changes affected authority over time

Authority changes are part of the platform trust surface and must be reconstructable.

---

## Failure Handling / Edge Cases

### User Authenticated but No Workspace Selected
Workspace-scoped actions must be denied until valid workspace context is resolved.

### User Has Membership but Product Not Enabled
Product action must be denied until entitlement exists.

### Role Removed While Session Remains Active
Subsequent authorization checks must reflect updated role state. Session validity does not preserve old authority.

### Workspace Owner Removed or Suspended
Controlled reassignment, restriction, or review must occur. Ownership may not remain logically orphaned.

### Product Cache Holds Stale Permission State
Fresh canonical authorization checks must prevail for sensitive actions.

### Internal Admin Attempts Governance-Sensitive Action Without Proper Scope
The action must be denied or routed into governance-aware control flow.

### Holder Rank Suggests Feature Visibility but Role Missing
Feature may be visible or appear eligible, but the action must still satisfy authorization.

### Entitlement Exists but Permission Missing
Access remains denied.

### Permission Exists but Scope State Restricted
Access remains denied or escalated according to policy.

### Admin Correction Needed After Bad Role Grant
The platform must preserve explicit corrective lineage rather than silently rewriting history.

---

## Operational Considerations

Operational systems should support:
- safe role-assignment and revocation workflows
- observability for repeated denies, escalation volume, and stale-cache risk
- clear control-plane tooling for privileged corrective actions
- correlation across identity, session, workspace, authorization, commerce, and audit actions
- replay-safe mutation handling for grants and revocations
- support runbooks that reference policy without bypassing canonical mutation boundaries
- reporting on ownerless-risk conditions, self-escalation attempts, and governance-sensitive request volumes

Authorization changes are canonical platform mutations, not product-local convenience updates.

---

## Migration / Compatibility / Supersession Considerations

- migrations must not silently move shared authorization ownership into product-local systems
- compatibility layers may preserve older product roles temporarily, but canonical scope and authorization rules must remain platform-owned
- role naming cleanup should prefer durable namespaces and reduced aliasing
- older implementations or documents that imply login success equals authority, workspace selection equals permission, entitlement equals permission, or token ownership equals admin power are superseded within this scope
- inheritance models may evolve, but bounded inheritance and governance separation must remain intact

---

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
- `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
- `WALLET_AWARE_USER_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`

This specification directly governs or materially informs:

- `WORKSPACE_MEMBERSHIP_LIFECYCLE_SPEC.md`
- `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- commerce/billing/credits specs
- product integration specifications
- control-plane and governance workflow specifications

---

## Explicitly Deferred Items

The following are intentionally deferred to adjacent or downstream specifications:

- exact policy engine language and evaluation DSL
- exact object-level effective permission algorithm
- exact role-to-permission tables by product
- exact seat-based authority interactions
- exact admin containment and break-glass workflow design
- exact governance approval routing
- exact UI explanation model for deny/review outcomes

These do not weaken the canonical authorization model established here.

---

## Final Normative Summary

FUZE uses a layered authorization model in which authentication identifies the canonical account, workspace or other scope resolution establishes where the actor is operating, roles provide the bounded structural authority model, permissions provide the enforceable action model, entitlements provide adjacent eligibility inputs, and security/governance controls may further constrain the final outcome. Login success is not authority. Workspace presence is not permission. Entitlement is not permission. Wallet-aware status is not admin power. Product-local policy is not shared platform truth.

Authorization must therefore remain explicit, scope-aware, permission-enforced, deny-by-default, least-privilege, auditable, and platform-owned. Products may extend the model inside their own domains, but they may not replace it. This document is the canonical FUZE rule set for role, permission, and access control.
