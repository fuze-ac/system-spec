# SCOPED_AUTHORIZATION_MODEL_SPEC

## Document Metadata

- Document Name: `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Governing Layer: Platform core / scoped authorization model
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, security, support operations, audit, governance, API design, operations
- Primary Purpose: Define the canonical FUZE model for how authorization grants bind to explicit scope, how scope is resolved and inherited, how scoped grants relate to workspace membership and product context, and how the platform prevents ambiguous or cross-scope authority leakage without collapsing identity, workspace truth, role/permission truth, entitlement, or object-level evaluation into one ambiguous layer
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
  - `WORKSPACE_AND_ORGANIZATION_SPEC.md`
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
  - `WORKSPACE_MEMBERSHIP_LIFECYCLE_SPEC.md`
  - `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - `AUTH_IDENTITY_API_SPEC.md`
  - `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
  - `WORKSPACE_ORGANIZATION_API_SPEC.md`
  - `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- Primary Downstream Dependents:
  - `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
  - `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
  - `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
  - `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
  - `WORKSPACE_ORGANIZATION_API_SPEC.md`
  - `ROLE_PERMISSION_ACCESS_API_SPEC.md`
  - product integration specifications
  - internal service authorization adapters
  - audit and control-plane workflow specifications

---

## Purpose

This specification defines the canonical FUZE scoped authorization model.

Its purpose is to make explicit:

- how roles, permissions, and grants bind to explicit scope rather than floating as global authority
- how FUZE distinguishes account scope, workspace scope, product scope, platform-operational scope, governance-sensitive scope, and narrower object or sub-resource scope
- how scope resolution interacts with identity, session validity, workspace membership, workspace lifecycle, and product context
- how grants may inherit, narrow, or compose across scope boundaries without causing implicit escalation
- how products consume scope-aware authorization truth without redefining collaborative scope or canonical grants
- how APIs, events, data models, audit trails, and runtime behavior must treat scope as a first-class authorization input

This document exists because the refined workspace and authorization specs already establish that workspace truth provides operating context and that authorization is scope-aware, deny-by-default, and downstream of scope resolution. That leaves a necessary middle layer: the explicit model for how authority is attached to scope, how scope is represented, and how the platform prevents cross-scope confusion. This document is that middle layer.

---

## Scope

This specification governs:

- canonical scope categories used in authorization
- the structural relationship between scope and role/permission grants
- scope resolution prerequisites and outputs
- grant attachment to account, workspace, product, internal-operational, governance-sensitive, and narrower sub-scopes
- bounded inheritance and narrowing rules across scope hierarchies
- how role assignments and direct grants reference scope
- how object or sub-resource context participates structurally in authorization without absorbing final effective-permission logic
- how products and internal services must consume scoped authorization truth
- API, event, data-model, audit, security, and operational implications of scoped grants

This specification does not define:

- canonical account identity semantics in full depth
- workspace existence or membership lifecycle in full depth
- full role and permission catalogs in full depth
- final object-level allow/deny decision logic for every action
- final entitlement or commercial gating logic
- exact policy language or evaluation engine DSL
- exact UI explanation logic for why a decision was allowed or denied

Those concerns belong in adjacent or downstream specifications.

---

## Out of Scope

This specification is explicitly out of scope for:

- login, session issuance, refresh, or revocation semantics
- invitation and membership-state mutation mechanics
- exact product permission tables
- exact entitlement formulas
- exact admin break-glass implementation
- exact row-level or field-level enforcement semantics for every object type
- exact caching topology or deployment topology

---

## Design Goals

The design goals of the FUZE scoped authorization model are:

1. make scope explicit for every meaningful authorization grant
2. preserve strict separation between identity, scope, and authority
3. prevent scope ambiguity from becoming authority leakage
4. support one account operating across many workspaces, products, and internal contexts
5. support future-safe hierarchical and nested scope without creating hidden global power
6. support product-specific extension while preserving platform-owned scope truth
7. keep authorization explainable, auditable, and structurally deterministic
8. prevent role and permission grants from drifting into global or contextless semantics
9. support strong safety around internal, commercial, and governance-sensitive actions
10. give downstream effective-permission logic a clean structural input model

---

## Non-Goals

This specification is not intended to:

- treat current UI context as authoritative scope by itself
- treat workspace membership as unrestricted workspace-wide power
- treat product participation as platform-wide admin capability
- grant authority globally where only one workspace or one product scope was intended
- define every final allow/deny rule
- collapse entitlement or billing into scope truth
- allow products to invent incompatible scope models for shared platform behavior

---

## Core Principles

### 1. Explicit Scope Principle
Every durable grant in FUZE must bind to an explicit scope. Contextless authority is disallowed except where a clearly defined global platform role is explicitly owned and justified.

### 2. Scope-Resolution-Before-Grant-Use Principle
A grant cannot be applied until the relevant scope has been resolved from canonical inputs.

### 3. Scope-Is-Not-Identity Principle
Scope answers where an actor is operating. Identity answers who the actor is. These must remain separate.

### 4. Scope-Is-Not-Permission Principle
Scope provides the domain in which authority may be evaluated. Scope does not by itself grant authority.

### 5. Narrowest Valid Scope Principle
Authority should be granted at the narrowest scope that satisfies the operating need.

### 6. No Cross-Scope Leakage Principle
A grant for one workspace, product, object set, or internal operational area must not silently apply to another unrelated scope.

### 7. Bounded Inheritance Principle
Inheritance across scope levels is allowed only where explicitly modeled and policy-safe. It must never be assumed casually.

### 8. Runtime-Selector-Is-Not-Grant Principle
Active workspace selection, active product tab, or launch context may help resolve intended scope but may not create a grant.

### 9. Restriction-Precedence Principle
Restriction, suspension, review, or lifecycle-state constraints on the relevant scope outrank ordinary grants.

### 10. Product-Consumption Principle
Products consume platform-owned scope truth and scoped grants. They may extend it within approved namespaces, but they may not replace it.

---

## Canonical Definitions

### Scope
A defined operational context in which a grant may apply.

### Scoped Grant
A role assignment, permission grant, or equivalent authorization binding that explicitly references a scope and applies only within that scope or an explicitly modeled descendant of it.

### Scope Category
A named class of scope such as account, workspace, product, operational, governance-sensitive, or object/sub-resource scope.

### Scope Identifier
The durable identifier or composite reference that identifies the target scope instance.

### Scope Resolution
The process of turning canonical runtime inputs into a concrete scope reference against which grants may be evaluated.

### Scope Context
The set of resolved structural facts needed to evaluate grants, such as workspace ID, product namespace, object owner scope, membership state, workspace lifecycle state, and actor relation.

### Scope Hierarchy
The structural parent/child or umbrella/actionable relationship among scopes, such as organization → workspace or workspace → product-within-workspace.

### Scope Inheritance
The controlled ability for a broader-scope grant to satisfy authority requirements in a narrower scope where policy explicitly allows it.

### Scope Narrowing
The process of limiting a broader role or permission to a more specific resource set, product area, object family, or action family.

### Unscoped Grant
A grant with no explicit scope reference. Unscoped grants are disallowed for ordinary behavior and must be treated as exceptional platform-level constructs if they exist at all.

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
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `WORKSPACE_MEMBERSHIP_LIFECYCLE_SPEC.md`

and above:

- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`

This document owns the structural model that binds grants to scope. It does not absorb the ownership of membership lifecycle, general role/permission taxonomy, or final action-level evaluation semantics.

---

## System Boundaries

This document governs only the following platform-owned boundaries:

- canonical scope categories for authorization
- structural grant-to-scope binding rules
- scope hierarchy and narrowing semantics
- canonical scope resolution prerequisites and outputs
- grant portability restrictions across scopes
- auditability of scope-bound grant mutation
- scope-aware API and event expectations

It does not govern:

- the existence of workspaces or organizations in full depth
- detailed membership mutation semantics
- exact product-local permission maps
- detailed commercial entitlement rules
- full object-level effective-permission evaluation
- exact governance approval mechanics

---

## Adjacent Boundaries

### Identity Domain
Owns canonical account identity. Scoped authorization consumes identity but does not redefine it.

### Auth / Session Domain
Owns session validity and runtime authentication state. Scope resolution may depend on valid session context but does not own session semantics.

### Workspace / Organization Domain
Owns workspace and organization existence, ownership relationships, and active collaborative scope primitives. Scoped authorization consumes those canonical scope facts.

### Membership Lifecycle Domain
Owns whether the actor is structurally attached to the workspace and in what membership state. Scoped authorization consumes that input.

### Role / Permission Authorization Domain
Owns the baseline role and permission model. Scoped authorization binds that model to explicit scope.

### Effective Permission Domain
Owns the final action-level evaluation logic after scope context, grants, restrictions, object conditions, and entitlements are brought together.

### Entitlement / Capability Domain
Owns commercial and capability eligibility. Scoped authorization provides one input layer, not the final capability answer.

### Security / Risk Domain
May impose restriction or review that overrides otherwise valid scoped grants.

### Product Domains
May define product-local sub-scope and namespaced grants within platform rules, but may not redefine shared scope categories or global semantics.

### Governance / Control-Plane Domain
Owns stronger approval pathways for governance-sensitive actions and may require special scope classes or approval-state overlays.

---

## Roles / Actors / Entities

### Canonical Account Actor
The authenticated FUZE account attempting to act.

### Scope Subject
The scope instance under which the actor attempts to operate, such as an account, workspace, product-within-workspace, or internal operational domain.

### Scope Owner
The domain owner of the scope record itself. For example, workspace records are owned by the workspace domain.

### Scoped Role Holder
An actor with a role assignment bound to a specific scope.

### Scoped Permission Holder
An actor with a permission grant or derived permission set bound to a specific scope.

### Scope Resolver
The platform service or boundary that resolves the intended scope from canonical runtime inputs.

### Product Authorization Consumer
A product or internal service that reads scoped grants and scope context to perform or request evaluation.

### Control-Plane Reviewer
A privileged actor who may approve, constrain, or correct scope-bound grants in high-sensitivity cases.

---

## Ownership Model

### Scoped Authorization Domain Owns
- canonical authorization scope categories
- structural binding rules between grants and scope
- scope reference format and applicability semantics
- inheritance and narrowing rules between scope layers
- audit lineage for scope-bound grant mutation in coordination with the audit domain
- publication of scope-aware grant events

### Scoped Authorization Domain May
- expose canonical scope applicability metadata
- validate whether a grant can bind to a requested scope
- publish scoped-grant summaries for downstream consumers
- coordinate with effective-permission, entitlement, and product domains through explicit contracts

### Scoped Authorization Domain Must Not
- redefine identity truth
- redefine workspace truth
- redefine membership lifecycle
- own final effective-permission outcomes for every object and action
- allow products to invent incompatible shared scope models
- treat UI selection or cached context as canonical scope truth

### Product Domains May
- define product-local scope extensions under approved namespaces
- bind product roles to product-scoped resources nested inside account or workspace scope
- consume scoped-grant reads

### Product Domains Must Not
- define global shared scope categories independently
- treat product-local context as a substitute for canonical workspace scope
- allow product-local scope to silently widen into platform or workspace-wide authority

---

## Authority / Decision Model

The scoped-authorization decision model is layered.

### Upstream Layers Provide
- canonical account identity
- session/runtime validity
- workspace or organization context where relevant
- membership presence and status
- workspace or organization lifecycle state
- product namespace or object ownership hints where relevant

### Scoped Authorization Layer Decides
- what scope the request is operating in
- whether the relevant grant can attach to that scope category
- whether a broader grant may apply to a narrower scope
- whether a narrower grant is insufficient for a broader action
- whether a scope mismatch requires deny or review rather than fallback

### Effective Permission Layer Decides
- whether the resolved scoped grant plus object, restriction, and entitlement conditions yields final allow or deny

This separation is required. Scoped authorization is the structural binding layer, not the full evaluation engine.

---

## Canonical Scope Categories

FUZE must support at least the following scope categories.

### 1. Account Scope
Authority that applies only to the actor’s own account domain.

Representative uses:
- managing own linked access methods
- viewing own receipts
- initiating account recovery for self where policy allows
- reading own audit summaries where policy allows

### 2. Workspace Scope
Authority that applies to a specific workspace.

Representative uses:
- inviting members
- removing members
- assigning workspace roles
- editing shared settings
- managing workspace billing surfaces
- operating shared workflows

### 3. Organization Scope
Authority that applies at a broader organizational umbrella where that model exists distinctly.

Representative uses:
- reporting across contained workspaces
- higher-level administration across multiple workspaces
- managed provisioning or policy-setting across child workspaces where policy allows

### 4. Product Scope
Authority that applies to one product namespace within an account or workspace context.

Representative uses:
- operating a Botmad workflow in one workspace
- editing AIE content in one workspace
- managing HerHelp assets for one account or workspace
- configuring ZAGA or QTB features inside a bounded product context

### 5. Internal Operational Scope
Authority that applies to internal support, finance, risk, reporting, or operational domains.

Representative uses:
- executing a support correction
- reviewing abuse cases
- viewing internal system operations data
- performing finance-side corrective actions

### 6. Governance-Sensitive Scope
Authority that applies to higher-trust operations requiring stronger control pathways.

Representative uses:
- approving registry-affecting changes
- executing payout-cycle operational transitions
- performing high-impact credits control actions
- triggering emergency control posture

### 7. Object / Sub-Resource Scope
Authority that applies to a bounded object family, object container, or sub-resource nested under a broader scope.

Representative uses:
- managing one project
- editing one workflow collection
- administering one automation namespace
- viewing one reporting dataset

Object scope remains subordinate to the broader account/workspace/product structure and must not become an alternate shared-scope system.

---

## Scope Hierarchy Model

FUZE should support a bounded hierarchy of scope, not a flat or global-only model.

### Representative Structural Hierarchy

1. **Account**
2. **Organization** (optional umbrella)
3. **Workspace**
4. **Product Within Workspace or Account**
5. **Object / Container / Sub-Resource**

Not every path is required for every feature, but the model must keep these semantic distinctions expressible.

### Hierarchy Rules
- organization may contain one or more workspaces where implemented distinctly
- workspace is the primary actionable collaborative scope
- product scope must attach to a valid parent scope, usually account or workspace
- object/sub-resource scope must attach to a valid parent scope and inherit no more than policy allows
- broader scope does not imply unrestricted authority in all descendants unless explicitly modeled
- narrower scope never implies authority in its parent scope

---

## Scope Resolution Model

Scope resolution is a platform-controlled process.

### Scope Resolution Inputs May Include
- canonical account identity from session
- requested action
- explicit workspace identifier
- explicit organization identifier
- explicit product namespace
- object identifier and object owner scope
- membership state
- workspace lifecycle state
- request route family or control-plane context
- privileged approval context where applicable

### Scope Resolution Outputs Must Include At Minimum
- resolved scope category
- resolved scope identifier(s)
- parent scope reference(s) where applicable
- actor relation to scope (for example member, owner, admin, self, operator)
- scope validity status
- any structural mismatch or ambiguity indicator

### Scope Resolution Rules
- scope must be resolved before evaluating scoped grants
- ambiguous scope must not silently fall back to a broader scope
- runtime UI selection is an input hint, not canonical proof
- product routes must not silently invent workspace scope when canonical workspace context is missing
- object owner scope must be derived from canonical object metadata, not user assumption
- missing or conflicting scope inputs must produce deny or review-required, not silent permissiveness

---

## Grant Binding Model

Every durable authorization grant must bind to one explicit scope category and one explicit scope identifier or scope-pattern construct approved by policy.

### Allowed Structural Forms
- role assignment to one account scope
- role assignment to one workspace scope
- role assignment to one organization scope
- role assignment to one product scope under a parent workspace or account
- internal operational role assignment under one operational domain
- governance-sensitive role assignment under one governance-sensitive domain
- object-scoped grant under an approved parent scope

### Disallowed Structural Forms
- implicit grant with no scope
- workspace-global semantics inferred from product-local grant without explicit policy
- product-wide semantics inferred from one object grant
- organization-wide semantics inferred from one workspace grant
- authority widened by frontend-selected context alone

### Grant Binding Rules
- scope applicability must be validated at grant-creation time
- invalid scope-to-role combinations must be rejected
- product-scoped grants must include product namespace and parent scope reference
- internal and governance-sensitive grants must remain separately classified
- expired, revoked, or restricted grants must not remain structurally valid through stale caches

---

## Inheritance and Narrowing Model

Scoped authorization may support inheritance, but it must be explicit and bounded.

### Permitted Examples
- a workspace owner role may satisfy certain workspace-subscope requirements where policy explicitly allows
- an organization-level administrative grant may apply to a child workspace only where organization/workspace hierarchy and policy explicitly allow it
- a product manager grant for a workspace-specific product scope may apply to bounded object scopes inside that product

### Disallowed Examples
- workspace member automatically inherits product admin across all products
- product admin automatically inherits workspace ownership or billing authority
- workspace owner automatically inherits platform admin or governance-sensitive authority
- one workspace grant automatically applies to another workspace
- one object grant automatically expands to all sibling objects without explicit policy

### Narrowing Rules
- grants may be narrowed by object owner scope, product namespace, workflow family, or container path
- narrowed grants must remain auditable and machine-checkable
- direct per-object overrides should remain rare and visible
- narrowing may reduce authority but must not silently widen it

---

## Relationship to Membership and Workspace Truth

Scoped authorization depends on workspace and membership truth but does not own them.

### Required Rules
- workspace scope exists only if canonical workspace truth exists
- workspace-scoped grants are meaningful only when the actor’s relation to the workspace is valid enough for downstream use
- membership state is a structural input, not the full authorization answer
- removed, suspended, or restricted membership may invalidate otherwise matching scoped grants
- active workspace selection is runtime context only and must not substitute for membership or scope truth

---

## Relationship to Entitlement and Capability Gating

Scoped authorization is not the same as entitlement.

### Required Rules
- a valid scoped grant may still be insufficient if the capability is not entitled
- entitlement without scoped grant is insufficient for protected action
- entitlement checks occur after or alongside scoped-grant applicability, depending on downstream evaluation design
- products must not confuse product plan or billing status with scope grant

---

## Relationship to Product and Object-Level Policy

Products may refine scope beneath the shared platform model.

### Allowed
- product-specific sub-scopes under a valid parent scope
- object-specific permissions within a valid product or workspace scope
- product-local role namespaced under product ownership

### Not Allowed
- alternate workspace systems for shared behavior
- global product-admin roles that silently act across all workspaces
- product-local overrides that bypass shared platform restriction or governance rules
- object-owner assumptions that ignore canonical resource scope metadata

---

## Permission / Access Considerations

This document defines structure, not the final evaluation algorithm, but it imposes mandatory constraints.

### Required Constraints
- all sensitive actions must reference a resolvable scope
- scope mismatch must fail closed
- broader scope grants may apply to narrower scopes only through explicit policy
- narrower grants must not satisfy broader actions
- role and permission catalogs must declare scope applicability
- caches and derived views are advisory only for sensitive scope checks
- review-required is a valid outcome when scope mapping is ambiguous or cross-domain sensitive

---

## API / Contract Implications

The platform should expose scope-aware authorization through explicit contracts.

### Authorization APIs Should Support
- scope applicability metadata for roles and permissions
- explicit scope resolution reads
- grant creation with required scope references
- grant revocation by scoped assignment ID
- scope-aware authorization evaluation requests
- machine-readable scope mismatch and ambiguity outcomes

### API Rules
- all mutation-capable endpoints must support correlation identifiers
- idempotency is required where retries are plausible, especially for scoped role assignment, scoped role revocation, and scoped corrective actions
- control-plane routes for broader or governance-sensitive scope classes require stronger authorization
- API responses must distinguish unsupported scope, unresolved scope, unauthorized scope, restricted scope, and review-required scope
- products must pass explicit scope identifiers rather than relying on presentation-layer assumptions

---

## Event / Async Implications

Scoped authorization changes must emit durable events sufficient for downstream audit and synchronization.

Representative semantic events include:
- `authz.scope_resolved`
- `authz.scope_resolution_failed`
- `authz.scoped_role_assigned`
- `authz.scoped_role_revoked`
- `authz.scoped_grant_corrected`
- `authz.scope_restriction_applied`
- `authz.scope_hierarchy_changed`

### Event Rules
- canonical events must be emitted only after owning-domain state commits
- async delivery failure must not redefine canonical scope or grant truth
- event consumers may refresh caches or projections but may not become scope owners
- events must preserve enough parent/child reference context to support downstream evaluation and audit

---

## Data Model / Storage Implications

At minimum, the platform should support the following durable semantic structures.

### `authorization_scope`
Representative semantic fields:
- `scope_id`
- scope category
- parent scope reference where applicable
- owner-domain reference
- status
- created_at / updated_at
- namespace or product reference where applicable

### `scoped_role_assignment`
Representative semantic fields:
- `assignment_id`
- `account_id`
- role name
- scope category
- scope reference
- parent scope reference where applicable
- assigned_by
- assigned_at
- expires_at where applicable
- status
- correction/supersession reference where applicable

### `scoped_permission_binding`
Representative semantic fields:
- permission name
- scope applicability category
- parent/child inheritance metadata
- narrowing metadata
- status
- policy version reference

### `scope_resolution_audit`
Representative semantic fields:
- action/request reference
- actor account reference
- requested scope inputs
- resolved scope outputs
- ambiguity indicator
- result status
- timestamp
- correlation reference

### Data Rules
- scope and scoped-grant truth must remain durable canonical records or controlled canonical configuration
- derived views and product caches must not become write owners
- correction should preserve lineage rather than destructively flattening scope history
- object owner scope references must be durable enough to support correct downstream evaluation

---

## Security / Risk / Abuse Controls

Scoped authorization is a security boundary because scope confusion is authority confusion.

The platform must preserve:
- fail-closed behavior on unresolved or mismatched scope
- no implicit widening from runtime context or product-local assumptions
- stronger controls around broader-scope and governance-sensitive assignments
- anti-self-escalation protections when scope assignment affects membership, billing, or control-plane authority
- rapid suppression of grants when the relevant scope is restricted, suspended, or structurally invalid
- auditability for scope-binding and scope-correction mutations

---

## Audit / Traceability Requirements

FUZE must be able to determine:

- which scope a role or permission grant was bound to
- who created, changed, or revoked that scoped grant
- what parent scope relation existed at the time
- why a scope mismatch caused deny or review
- how a product or object scope was attached to its parent workspace or account
- whether a broader grant was legitimately inherited into a narrower context
- whether a visible scope summary is canonical or derived
- how a current scoped-grant state was reached from prior states

Scope mistakes are high-impact because they silently widen or misroute authority if not reconstructable.

---

## Failure Handling / Edge Cases

### Valid Session, No Scope
Protected workspace or product action must fail closed until scope is resolved.

### Workspace Selected, Membership Missing
The selected workspace may be visible in UI history or stale state, but scope-bound authority must be denied.

### Product Object Belongs to Different Workspace Than Requested
Canonical object owner scope must win; the request must fail or reroute explicitly.

### Organization Admin Acts on Child Workspace
Only explicitly modeled organization-to-workspace inheritance may allow this. It must not be assumed.

### Product Admin Attempts Workspace Billing Action
The grant is mismatched in scope and domain; action must be denied unless separately granted.

### Workspace Owner Attempts Governance-Sensitive Action
Ordinary workspace scope is insufficient. The action must route through governance-sensitive scope and control.

### Membership Removed But Scoped Grant Cache Persists
Canonical membership and scope validity must suppress use of stale scoped grants.

### Object Migrated Between Scopes
Grant applicability must be re-evaluated against the new canonical owner scope; stale object-scope assumptions must not persist silently.

### Wrong Scope Passed by Client
Server-side scope resolution must detect mismatch and fail closed.

---

## Operational Considerations

Operational systems should support:
- clear diagnostics for scope mismatch and unresolved-scope failures
- observability for repeated scope ambiguity, stale parent-scope references, and cross-scope misuse attempts
- control-plane tooling for correcting misbound grants
- replay-safe mutation handling for scoped assignments
- cache invalidation or refresh mechanisms when parent scope, membership, or workspace state changes
- runbooks for wrong-scope product mutations, object-scope migration, inherited-scope disputes, and stale-grant suppression

The operational model must preserve the principle that scope is canonical authorization structure, not a presentation-layer convenience.

---

## Migration / Compatibility / Supersession Considerations

- migrations must remove contextless or ambiguously scoped grants where they exist
- older implementations that imply workspace selection equals authority, or that product-local admin acts across all workspaces, are superseded within this scope
- compatibility layers may preserve legacy role names temporarily, but explicit scope binding must become canonical
- organization/workspace models that were previously flattened may be preserved temporarily, but grants must still remain explicit about the target scope they bind to
- object-scope and product-scope adoption should prefer explicit parent-scope references over implicit inference

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
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `WORKSPACE_MEMBERSHIP_LIFECYCLE_SPEC.md`
- `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`

This specification directly governs or materially informs:

- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- product integration specifications
- internal service authorization adapters
- audit and control-plane workflow specifications

---

## Explicitly Deferred Items

The following are intentionally deferred to adjacent or downstream specifications:

- exact policy DSL for scope-aware evaluation
- exact object-level allow/deny formulas
- exact entitlement join logic
- exact UI explanation wording for scope mismatch
- exact cache invalidation SLA and propagation topology
- exact governance approval graph for broader-scope or governance-sensitive assignments

These do not weaken the canonical scoped authorization model established here.

---

## Final Normative Summary

FUZE scoped authorization is the structural model that binds authority to explicit operating context. Identity tells FUZE who the actor is. Workspace and related domain records tell FUZE where the actor is acting. Scoped grants tell FUZE what authority may even be considered in that context. Final effective-permission logic then determines whether the exact action is allowed under current policy, object state, restriction state, and entitlement posture.

No grant in FUZE should behave like a contextless global power by accident. Workspace selection is not a grant. Product context is not a substitute for workspace truth. Membership is not final authority. Entitlement is not permission. Product-local scope may refine platform scope, but it may not replace it. This document is the canonical FUZE rule set for how authorization binds to scope.
