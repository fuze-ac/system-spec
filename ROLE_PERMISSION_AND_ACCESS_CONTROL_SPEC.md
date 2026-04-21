# ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC

## Title
FUZE Role, Permission, and Access Control Specification

## Document Metadata

- Document Name: `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Version: 1.2.0
- Effective Date: 2026-04-21
- Last Updated: 2026-04-21
- Reviewed On: 2026-04-21
- Document Owner: FUZE Authorization Domain
- Approval Authority: FUZE Platform Architecture and Governance Authority
- Review Cadence: Quarterly or upon material change to workspace scope semantics, scoped grant behavior, internal operational roles, governance-sensitive controls, commercial controls, or access-evaluation policy
- Governing Layer: Platform core / role, permission, and access control
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, security engineering, support operations, audit, governance, API design, commerce/billing operators, platform operations, implementation-contract authors
- Primary Purpose: Define the canonical FUZE authorization model that turns authenticated canonical accounts operating in resolved scope into explicit, policy-bound access outcomes through durable role assignments, scoped permissions, bounded inheritance, restriction handling, and auditable control paths without collapsing identity, session, workspace structure, entitlement, wallet-aware participation, commercial truth, or governance control into one ambiguous layer
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
- Supersedes: Earlier or less explicit FUZE authorization, role, team-admin, billing-admin, support-access, and product-role writeups to the extent they conflict within this document’s scope
- Superseded By: Not yet known
- Related Decision Records: Not yet known
- Canonical Status Note: This document is the governing FUZE role, permission, and access-control specification. Downstream APIs, products, services, control-plane workflows, support tooling, and reports MUST NOT reinterpret the semantics established here.
- Implementation Status: Normative architecture baseline; downstream API, workflow, storage, event, runtime, security, and audit contracts must conform
- Approval Status: Drafted for refined-system inclusion; formal approval record not yet attached
- Change Summary:
  - tightened separation between identity, session, scope, membership, structural grant, final permission, entitlement, wallet-aware context, and reporting
  - hardened the baseline role and permission model against cross-scope leakage, self-escalation, and product-local shadow authorization
  - clarified internal operational roles, commercial controls, governance-sensitive action classes, and restriction-precedence behavior
  - strengthened implementation-contract guardrails for idempotency, replay safety, review-required outcomes, audit lineage, and degraded-mode behavior

## Purpose

This specification defines the canonical FUZE role, permission, and access-control model.

Its purpose is to make explicit:

- how FUZE turns authenticated canonical account identity plus resolved operating scope into policy-bound access outcomes
- how role assignment, permission mapping, scope prerequisites, restrictions, and policy checks interact
- how account scope, workspace scope, product scope, platform-operational scope, and governance-sensitive scope remain distinct
- how authorization remains separated from identity, authentication, sessions, workspace structure, entitlement, billing truth, wallet-aware participation, and product-local UX
- how sensitive internal actions, workspace-control actions, product actions, commercial actions, and governance-sensitive actions must be bounded and auditable
- how products, APIs, internal services, and support tooling may consume authorization truth without becoming owners of it

FUZE is a multi-product, multi-scope ecosystem. In such a platform, login success cannot be treated as authority, workspace presence cannot be treated as permission, billing participation cannot be treated as product control, and token-aware status cannot be treated as admin power. Authorization must therefore be explicit, layered, bounded, and platform-owned.

## Scope

This specification governs:

- the canonical access-control model for FUZE
- the distinction among identity, membership, role, permission, access decision, and entitlement
- authorization scope categories and their relationships
- canonical role categories and semantic baseline roles
- permission domains and mapping direction
- authorization prerequisites and baseline rule ordering
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

Those concerns are defined in adjacent or downstream specifications.

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

## Design Goals

1. Make authority explicit across account, workspace, product, platform, operational, and governance-sensitive contexts.
2. Preserve strict separation between identity and authority.
3. Preserve strict separation between collaborative scope and authority.
4. Support both individual and collaborative platform usage.
5. Support consistent authorization across shared products and shared services.
6. Keep product innovation possible without allowing product-local shadow authorization truth.
7. Preserve explicit controls around billing, credits, recovery-sensitive actions, support operations, and governance-sensitive operations.
8. Make access decisions auditable, reviewable, and explainable.
9. Support least privilege and deny-by-default as durable platform posture.
10. Remain future-safe for additional products, enterprise structures, and stricter governance controls.

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
If authority is not clearly granted, the requested action MUST be denied.

### 6. Least-Privilege Principle
Roles and permissions MUST grant only the minimum authority needed for the intended operating responsibility.

### 7. No Implicit Escalation Principle
Membership, product usage, wallet status, billing participation, session continuity, or UI selection MUST NOT silently create new authority.

### 8. Product-Consumption Principle
Products consume canonical authorization decisions or canonical role/permission truth. Products do not become owners of shared authorization semantics.

### 9. Restriction-Precedence Principle
Restriction, suspension, risk, review, or account/workspace control state MAY override otherwise valid role grants.

### 10. Governance-Separation Principle
Governance-sensitive powers require stronger, separate control pathways. They MUST NOT be inherited casually from ordinary operational or workspace roles.

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

## Truth Class Taxonomy

This specification distinguishes the following truth classes and downstream implementations MUST preserve those distinctions.

### 1. Canonical Identity Truth
The durable actor anchor is the canonical account identity represented by `account_id`.

### 2. Runtime Session Truth
A valid session is temporary authenticated runtime presence. It is a prerequisite for ordinary interactive authorization, but not authorization itself.

### 3. Collaborative Scope Truth
Workspace and organization truth define where the actor is operating. Scope is a prerequisite input to authorization, not authority by itself.

### 4. Membership Truth
Workspace membership is a structural relationship between actor and collaborative scope. Membership is often necessary, but not sufficient, for final authority.

### 5. Authorization Truth
Role assignments, permission mappings, restrictions, scope bindings, and effective-permission semantics owned by the authorization domain.

### 6. Entitlement Truth
Commercial or operational eligibility attached to an account, workspace, or other allowed subject. Entitlement may further constrain capability use, but does not become authority truth.

### 7. Wallet-Aware Context Truth
Wallet-linked participation or token-aware context may influence visibility or eligibility where policy allows, but MUST NOT replace authorization truth.

### 8. Derived Read-Model Truth
Support views, dashboards, analytics summaries, cached permission views, and UI summaries derived from canonical records.

### 9. Reporting / Public View Truth
Reports and summarized communications that describe access posture without becoming canonical mutation owners.

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

This document owns the canonical authorization structure and baseline rule set. It does not absorb deeper downstream ownership of membership lifecycle, scoped-grant modeling, evaluation internals, object-level effective permission logic, admin containment, or commercial eligibility logic.

## System Boundaries

This document governs only the following platform-owned boundaries:

- canonical role semantics
- canonical permission semantics
- authorization scope semantics
- baseline role categories and bounded inheritance posture
- canonical distinction between authorization and adjacent domains
- minimum authorization ordering
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
Owns the deeper structural model for how authorization binds to resolved scopes and nested or object-level context.

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

## Conflict Resolution Rules

When layers disagree, the platform MUST resolve authorization-related conflicts in the following order unless a higher-order policy explicitly overrides it:

1. canonical identity and session validity
2. canonical scope and membership truth
3. canonical role assignments and permission mappings
4. explicit restrictions, security posture, and higher-order policy
5. entitlement posture where the capability requires it
6. object-level or product-local narrowing facts
7. current UI state, cached permission views, and product-local convenience state
8. reports, dashboards, and other derived summaries

Specific conflict rules:

- session validity is necessary but never sufficient for allow
- workspace membership is necessary for many workspace actions but never sufficient for allow
- entitlement may further narrow capability, but MUST NOT widen authorization beyond granted permissions
- wallet-aware context MUST NOT create admin, support, finance, or governance authority
- product-local roles MUST NOT widen shared platform authority without explicit platform-owned mapping
- derived views MUST NOT override canonical role or restriction state

## Default Decision Rules

Where ambiguity exists, the following defaults apply:

1. default actor anchor: `account_id`
2. default collaborative operating scope: workspace when workspace context exists
3. default role interpretation: structural authority bundle only inside its defined scope
4. default permission interpretation: explicit action grant, not a vague status label
5. default outcome when required inputs are missing or unresolved: deny
6. default outcome for governance-sensitive or high-risk ambiguity: review-required or deny, not silent allow
7. default wallet-aware interpretation: eligibility or visibility context only, not authority
8. default product-local interpretation: subordinate to canonical shared authorization semantics
9. default commercial interpretation: entitlement may gate capability after authorization prerequisites are satisfied, but does not become permission truth
10. default correction posture for privileged mistakes: explicit audited remediation, not hidden destructive rewrite

## Roles / Actors / Entities

### End User
A canonical account acting on its own behalf or within a workspace/product scope.

### Workspace Member
A canonical account with a membership relationship in a workspace.

### Workspace Owner
The highest ordinary workspace-control role, bounded by platform policy and non-inheritance into platform, finance, support, or governance powers.

### Workspace Administrator
An elevated workspace role for operational administration inside workspace boundaries.

### Workspace Billing Manager
A role for workspace commercial and billing-related actions subject to downstream commercial policy.

### Workspace Operator
A role for shared operational and workflow actions inside workspace scope.

### Workspace Viewer
A read-oriented role where limited observation is permitted.

### Product Operator / Product Manager / Product Editor
Product-scoped roles used inside one product domain under resolved account/workspace scope.

### Support Operator
Internal role for bounded support and corrective access workflows.

### Finance Operator
Internal role for billing, credits, invoice, and commercial corrective workflows.

### Risk Reviewer
Internal role for abuse, fraud, and restriction review.

### Platform Administrator
Internal role for broad operational administration, still bounded by policy, audit, and control-plane restrictions.

### Governance-Aware Actor
A role or approval actor permitted to participate in governance-sensitive workflows.

## Ownership Model

### Authorization Domain Owns
- canonical role semantics
- canonical permission semantics
- baseline role families and their bounded meanings
- role-to-permission mapping direction
- restriction and deny posture semantics
- structural mutation rules for grants and revocations
- publication of canonical authorization mutation events
- baseline audit expectations for authorization mutation and sensitive decisions

### Authorization Domain MAY
- expose derived summaries for low-risk UX
- expose evaluation inputs to downstream effective-permission logic
- expose role and permission catalogs through explicit API contracts
- coordinate with membership, scope, entitlement, and security domains through explicit contracts

### Authorization Domain MUST NOT
- redefine identity truth
- redefine session truth
- redefine collaborative scope truth
- redefine entitlement truth
- redefine wallet-aware truth
- allow product-local systems to become owners of shared authorization truth

### Product Domains MAY
- define product-local roles and permission refinements inside product scope
- consume canonical role and permission truth
- add object-level narrowing in downstream effective-permission logic

### Product Domains MUST NOT
- replace shared authorization semantics for shared platform concerns
- create hidden global powers from product-local roles
- grant support, finance, or governance-sensitive power through product ownership alone

## Authority / Decision Model

Authorization is a layered model.

### Upstream Layers Provide
- canonical identity
- valid session/runtime state
- resolved collaborative scope
- membership and lifecycle posture
- requested action and target

### Authorization Domain Decides
- what baseline structural grants exist
- which role assignments are valid in that scope
- which permission mappings are candidates
- whether restriction state, deny posture, or higher-order structural rules suppress candidate grants

### Downstream Evaluation Domain Decides
- the final action-level effective permission after object conditions, entitlement, and higher-order policy are applied

This ordering is mandatory. Authorization defines the structural authority model. It does not absorb final object-level evaluation or commercial eligibility.

## State Model

At minimum, FUZE SHOULD support the following durable semantic state families:

### Role Assignment States
- `active`
- `pending`
- `restricted`
- `revoked`
- `expired`
- `superseded`

### Permission Mapping States
- `active`
- `deprecated`
- `disabled`
- `superseded`

### Restriction States
- `active`
- `cleared`
- `superseded`

### State Rules
- state transitions MUST be explicit, auditable, and replay-safe where retries are possible
- restrictive states outrank stale derived summaries
- deprecated mappings MUST NOT silently widen privilege
- supersession SHOULD preserve lineage instead of hiding historical authority changes
- direct overrides SHOULD remain rare, visible, and auditable

## Lifecycle / Workflow Model

### 1. Role Definition and Mapping
Canonical roles and permissions are defined through platform-owned catalogs and mapping rules. Products may extend within approved namespaces but MUST NOT replace shared role meanings.

### 2. Role Assignment
Role assignment binds a subject to a role within an explicit scope. Assignment MUST be durable, scoped, auditable, and idempotent where retries are plausible.

### 3. Role Revocation / Restriction
Role removal or restriction suppresses candidate authority and MUST propagate safely to downstream evaluation, caches, and product behavior without hidden semantic downgrade.

### 4. Owner / Admin Control Changes
Owner-transfer-related authority updates, owner-continuity protections, and privileged corrections MUST use bounded workflows with audit lineage and strong authorization.

### 5. Sensitive Internal / Governance Actions
Actions touching support correction, finance remediation, governance-sensitive control, or high-impact admin behavior MUST use explicitly classified permissions and MUST NOT flow through ordinary workspace-role inheritance.

### 6. Evaluation Handoff
After structural grant resolution, downstream effective-permission evaluation determines the final action outcome.

## Invariants

1. Authentication success is necessary but not sufficient for authorization.
2. Scope MUST be resolved before authority is applied.
3. Workspace membership is structural input, not final permission.
4. Authority exists only through explicit scoped grants or explicit policy-bound exceptions.
5. Deny-by-default applies when authority is missing, invalid, unresolved, or overridden.
6. Restriction and review posture outrank stale grants.
7. Product-local roles remain subordinate to canonical shared authorization semantics.
8. Wallet-aware context MUST NOT become shared-platform authority.
9. Workspace owner MUST NOT automatically inherit platform admin, finance, support, or governance-sensitive powers.
10. Derived views remain derived and regenerable.
11. High-impact authorization mutations MUST be auditable and replay-safe.
12. Degraded runtime conditions MUST NOT cause hidden truth substitution or privilege widening.

## Functional Rules

### Baseline Role Families
FUZE SHOULD support at minimum the following semantic role families:

- workspace owner
- workspace administrator
- workspace billing manager
- workspace operator
- workspace member
- workspace viewer
- product-scoped operator/editor/manager roles where product policy requires
- internal support operator
- internal finance operator
- internal risk reviewer
- internal platform administrator
- governance-aware roles or approval actors

These semantic families are directionally canonical. Exact names MAY evolve, but the separation they express MUST remain.

### Permission Model
Permissions are the enforceable action layer. FUZE SHOULD prefer durable, explicit, namespaced permission naming.

#### Permission Design Rules
- permission names SHOULD be durable and semantically clear
- permissions SHOULD be namespaced by domain
- permissions SHOULD describe actions, not vague status
- permissions MUST remain separate from role names
- permissions MUST be bindable to defined scopes
- permissions SHOULD support policy evolution without semantic drift

#### Representative Permission Domains
Examples include:

- `account.profile.update_self`
- `account.linked_login.manage_self`
- `account.audit.view_self`
- `account.recovery.initiate_self`
- `workspace.member.invite`
- `workspace.member.remove`
- `workspace.role.assign`
- `workspace.settings.manage`
- `workspace.archive.initiate`
- `workspace.owner.transfer`
- `billing.invoice.view`
- `billing.subscription.manage`
- `billing.payment_method.manage`
- `credits.balance.view`
- `credits.workspace.spend`
- `credits.adjustment.issue_internal`
- `product.<namespace>.workflow.approve`
- `product.<namespace>.content.edit`
- `report.internal.view`
- `report.export.execute`
- `admin.account.restrict`
- `admin.workspace.suspend`
- `admin.support_correction.execute`
- `admin.system_dashboard.view`
- `governance.payout_cycle.transition`
- `governance.control_plane.change_approve`
- `governance.registry.mapping_update`
- `governance.reserve_coordination.execute`

These examples provide semantic direction, not frozen final tables.

### Permission Inheritance and Resolution
FUZE MAY support inheritance, but inheritance MUST be bounded and explicit.

#### Required Inheritance Rules
- higher roles MAY include lower-role permissions within the same scope only where explicitly mapped
- workspace roles MAY expose product access only where product policy allows
- product roles MAY refine product capability but MUST NOT enlarge shared platform authority automatically
- platform operational roles MUST NOT automatically inherit governance-sensitive powers
- governance-sensitive permissions MUST NOT be inherited casually from workspace or ordinary admin roles
- inheritance MUST be deterministic and auditable

### Restriction, Deny, and Escalation Model
Authorization MUST support explicit deny, restricted, and review-required postures.

Representative deny conditions include:
- no valid session
- missing scope
- missing membership where required
- missing permission
- inactive or unavailable scope
- insufficient entitlement where capability requires it
- restricted or suspended account/workspace
- policy-blocked sensitive action
- governance-sensitive action attempted through ordinary role

Review-required is a legitimate first-class outcome for sensitive or ambiguous actions.

### Billing, Credits, and Commercial Access Considerations
Billing and credits are especially sensitive authorization domains.

Required rules:
- products MUST NOT redefine billing or credits authority locally
- billing-visible and billing-mutating actions MUST be separately permissioned
- spending authority and viewing authority SHOULD be distinguishable
- internal corrective finance actions MUST remain internal-only permissions
- commercial attachment MUST NOT automatically imply broad workspace admin power

### Wallet-Aware and Holder-Aware Access Considerations
Wallet-aware context may influence visibility, eligibility, or experience, but it MUST NOT replace authorization.

Allowed patterns include:
- holder-aware UI
- holder-gated features where entitlement and policy allow
- wallet linkage as one eligibility input for payout or participation logic

Disallowed patterns include:
- granting workspace admin because a wallet holds tokens
- bypassing billing or credits controls because a wallet is linked
- granting support or platform admin privileges based on token ownership
- treating token possession as sufficient authority for shared platform actions

### Internal Operational Access Model
FUZE MUST define a bounded internal operational access model with at least:
- support operator
- finance operator
- abuse/risk reviewer
- reporting operator where applicable
- platform admin

Internal access MUST remain permission-bounded, policy-constrained, and auditable.

### Governance-Sensitive Access Model
Certain actions require stronger control than ordinary admin rights.

Representative governance-sensitive action classes may include:
- payout-cycle control transitions
- reserve-related off-chain operational actions
- public registry changes affecting contract or wallet interpretation
- high-impact credits control paths
- emergency control triggers
- actions linked to multisig/timelock-governed execution paths

Such actions MUST be explicitly classified and MUST NOT be granted through ordinary workspace roles or implied by product ownership.

## Permission / Access Considerations

This document governs authorization structure, not all membership or scope lifecycle details.

Mandatory constraints:
- authorization MUST execute after scope resolution
- active workspace context is required before mutating workspace-owned resources
- current scope selector is runtime context only and MUST NOT create authority
- self-escalation MUST be blocked
- owner continuity safeguards MUST exist for owner-removal or ownership-transfer actions
- object-level checks MAY further narrow ordinary role grants
- cached permission views are non-canonical for sensitive actions

## Entitlement Considerations

Entitlement is an adjacent but separate domain.

Required rules:
- workspace or account MAY be the subject of entitlement
- valid permission does not guarantee entitlement
- valid entitlement does not guarantee permission
- entitlement checks occur after scope resolution and in coordination with permission evaluation
- product surfaces MUST NOT present entitlement as equivalent to authorization

## API / Contract Implications

The platform SHOULD expose authorization through explicit domain boundaries.

### Authorization APIs SHOULD own
- role catalog reads
- permission catalog reads
- role assignment and revocation within approved scope
- access-evaluation requests
- effective-access summaries where appropriate
- admin-safe access mutation pathways
- audit-friendly mutation results

### Boundary Rules
- identity APIs MUST NOT own role or permission truth, even if they expose derived summaries
- session APIs MUST NOT pretend session presence is equivalent to authority
- workspace APIs MUST NOT redefine permission logic, though they may expose structural membership and owner data consumed by authorization

### Contract Rules
- all mutation-capable endpoints MUST support correlation identifiers
- idempotency is required where retries are plausible, especially for role assignment, role removal, owner-transfer-related authority updates, restriction changes, and admin corrections
- control-plane and admin APIs require stronger authorization and richer audit metadata
- review-required results MUST be first-class contract outcomes where sensitive actions require escalation

## Event / Async Implications

Authorization and access mutations MUST emit durable events sufficient for audit and downstream coordination.

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
- events MUST be emitted only after canonical state transitions commit
- async delivery failure MUST NOT redefine canonical authorization truth
- consumers MAY react but MUST NOT become owners of shared authorization semantics
- retries MUST be replay-safe and idempotent
- event semantics MUST preserve distinction between structural role changes and transient evaluation outcomes

## Data Model / Storage Implications

At minimum, the platform SHOULD support the following durable semantic structures:

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
- status or removal marker
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
- role and permission truth MUST remain durable canonical entities or controlled configuration with durable versioning
- derived dashboards, caches, analytics views, and product summaries MUST NOT become write owners
- corrections SHOULD prefer explicit lineage and terminal transitions over hidden destructive rewrite
- object-level effective-permission caches are advisory only for sensitive actions

## Security / Risk / Abuse Controls

Authorization is a security boundary.

The platform MUST preserve:
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

## Boundary Violation Detection / Non-Canonical Patterns

The following patterns are explicitly non-canonical and forbidden:

- granting workspace admin because a wallet holds tokens
- bypassing billing or credits controls because a wallet is linked
- granting support or platform admin privileges based on token ownership
- treating token possession as sufficient authority for shared platform actions
- treating workspace membership as final permission
- treating entitlement as equivalent to authority
- treating session presence as equivalent to authority
- allowing product-local “super admin” shortcuts for shared platform concerns
- letting ordinary workspace roles inherit governance-sensitive control powers
- using product-local permission summaries as canonical authority for sensitive actions
- destructive correction that erases role or restriction lineage

## Audit / Traceability Requirements

FUZE MUST be able to determine:

- who assigned or removed a role
- which scope a grant applied to
- which policy or version defined a permission mapping
- why an access mutation occurred
- when a restriction overrode ordinary access
- why a sensitive action was denied or routed to review
- which internal operator executed privileged correction
- whether a visible role/permission summary is canonical or derived
- how owner, membership, billing, or workspace changes affected authority over time

Authority changes are part of the platform trust surface and MUST be reconstructable.

## Failure Handling / Edge Cases

### Valid Session, No Scope
Authorization MUST fail closed when the required scope is missing or unresolved.

### Membership Present, Permission Missing
The actor remains a valid participant in the workspace, but the action MUST be denied.

### Permission Present, Entitlement Missing
Capability use MUST be denied or marked as gated where entitlement is required, without redefining entitlement as permission truth.

### Workspace Owner Attempts Governance-Sensitive Action
Ordinary workspace ownership MUST NOT satisfy governance-sensitive authorization. The platform MUST deny or escalate.

### Product Manager Role Attempts Platform Admin Action
Product-scoped authority MUST NOT leak into platform-operational authority.

### Stale Permission Cache After Restriction
The restriction MUST outrank cached grants. Sensitive actions MUST use sufficiently fresh canonical state.

### Wallet-Linked User Attempts Finance Correction
Wallet linkage or token status MUST NOT satisfy internal finance authorization.

### Operator Mistake During Role Correction
The platform SHOULD preserve explicit corrective lineage and resulting restriction or supersession rather than hiding the mistake through destructive rewrite.

## Operational Considerations

Operational systems SHOULD support:
- observability for role mutation, restriction change, deny rates, and escalation rates
- monitoring for self-escalation attempts, wrong-scope access attempts, and product-local shadow-authorization patterns
- safe support tooling that reads derived summaries without bypassing canonical mutation boundaries
- correlation across identity, session, scope, membership, grant, entitlement, and audit systems
- runbooks for stale permission incidents, mixed-batch failures, and sensitive action review flows
- safe degraded-mode behavior that pauses unsafe automation rather than guessing

The operational model MUST preserve the principle that authorization is platform-owned truth, not frontend convenience.

## Migration / Compatibility / Supersession Considerations

- older implementations that imply login success equals authority are superseded within this scope
- older implementations that imply workspace membership alone equals permission are superseded within this scope
- product-local admin shortcuts for shared platform concerns MUST be removed or narrowed under explicit mapping
- compatibility layers MAY preserve legacy role labels temporarily, but canonical scope, deny-by-default, bounded inheritance, and restriction precedence semantics MUST remain platform-owned
- future products MUST attach to the shared authorization model rather than inventing alternate shared authority systems

## Implementation-Contract Guardrails

Downstream implementations MUST preserve all of the following:

1. `account_id` remains the durable actor anchor
2. authorization remains distinct from identity, session, scope, membership, entitlement, wallet-aware context, and reporting
3. scope resolution happens before authorization use
4. workspace membership remains prerequisite structure, not final permission
5. roles remain structural bundles and permissions remain enforceable actions
6. deny-by-default remains the baseline posture
7. restrictions and review posture remain able to override ordinary grants
8. products do not become owners of shared authorization truth
9. governance-sensitive actions remain separated from ordinary workspace or product roles
10. derived views remain derived and regenerable
11. idempotency and replay safety are preserved for mutation-capable flows
12. degraded runtime conditions do not cause hidden truth substitution or privilege widening

## Downstream Execution Staging

Recommended downstream staging order:

1. stabilize identity and session prerequisites
2. stabilize workspace and organization scope semantics
3. stabilize membership lifecycle semantics
4. stabilize canonical role and permission baseline
5. stabilize scoped-grant modeling
6. stabilize effective-permission evaluation
7. stabilize entitlement-aware gating and commercial controls
8. stabilize admin correction, containment, audit, and control-plane workflows

This ordering preserves scope-aware structural authorization before finer-grained final evaluation.

## Required Downstream Specs / Contract Layers

This specification requires compatible downstream refinement and implementation-contract work in at least the following areas:

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

## Canonical Examples / Anti-Examples

### Canonical Example 1 — Workspace Admin Inside One Workspace
A user is workspace admin in Workspace A and ordinary member in Workspace B. Their authority differs by scope while identity remains the same. This is canonical.

### Canonical Example 2 — Billing Manager Without Broad Admin
A user may manage invoices and payment methods for a workspace without automatically gaining member-removal or platform-admin powers. This is canonical.

### Canonical Example 3 — Product Role Narrower Than Workspace Role
A product editor role grants product-specific capability inside one product namespace without widening platform-operational authority. This is canonical.

### Canonical Example 4 — Governance Action Requires Stronger Path
A high-impact registry or payout control action routes through governance-aware control rather than ordinary workspace ownership. This is canonical.

### Anti-Example 1 — Login Equals Authority
A product treats successful login as permission to manage workspace settings. This is forbidden.

### Anti-Example 2 — Membership Equals Full Permission
A user who is merely a workspace member is treated as authorized to assign roles. This is forbidden.

### Anti-Example 3 — Wallet Grants Admin
A token holder is treated as workspace or platform admin because of wallet status. This is forbidden.

### Anti-Example 4 — Product Super Admin Replaces Platform Controls
A product-local super admin role silently gains shared billing, support, or governance-sensitive powers. This is forbidden.

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
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- commerce/billing/credits specifications
- product integration specifications
- control-plane and governance workflow specifications

## Explicitly Deferred Items

The following are intentionally deferred to adjacent or downstream specifications:

- exact policy-engine DSL
- exact database-level row/field enforcement strategy
- exact per-product object rule tables
- exact UI wording for each deny or review outcome
- exact break-glass staffing procedures
- exact observability dashboards and alert thresholds
- exact governance approval flow implementation

These do not weaken the canonical authorization model established here.

## Final Normative Summary

FUZE authorization is the platform-owned structure that turns authenticated identity operating in resolved scope into bounded structural authority through explicit roles, permissions, restrictions, and policy. Authentication is not authorization. Workspace membership is not final permission. Entitlement is not authority. Wallet-aware participation is not admin power. Product-local roles may refine product behavior, but they may not replace shared authorization truth. Governance-sensitive action classes must remain separated from ordinary workspace and product roles.

This document is the canonical FUZE rule set for role, permission, and access control. Downstream systems MUST preserve its separations, deny-by-default posture, bounded inheritance, restriction precedence, auditability, and anti-drift rules.
