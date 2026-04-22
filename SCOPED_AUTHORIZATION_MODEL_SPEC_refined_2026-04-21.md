# SCOPED_AUTHORIZATION_MODEL_SPEC

## Title
FUZE Scoped Authorization Model Specification

## Document Metadata

- Document Name: `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Version: 1.1.0
- Effective Date: 2026-04-21
- Last Updated: 2026-04-21
- Reviewed On: 2026-04-21
- Document Owner: FUZE Platform Identity and Access Architecture
- Approval Authority: FUZE Platform Architecture and Governance Authority
- Review Cadence: Quarterly or upon material change to workspace semantics, organization hierarchy, membership lifecycle, role and permission taxonomy, effective-permission evaluation, entitlement posture, audit traceability posture, or privileged correction boundaries
- Governing Layer: Platform core / scoped authorization model
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, security engineering, support operations, audit, governance, API design, platform operations, implementation-contract authors
- Primary Purpose: Define the canonical FUZE model for how roles, permissions, and direct grants bind to explicit scope; how scope is resolved, inherited, narrowed, and validated; and how scoped authorization remains distinct from identity, workspace truth, membership truth, effective permission, entitlement, and product-local state
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
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
  - `WORKSPACE_AND_ORGANIZATION_SPEC.md`
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
  - `WORKSPACE_MEMBERSHIP_LIFECYCLE_SPEC.md`
  - `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
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
- Supersedes: Earlier or less explicit FUZE scoped-grant, scope-resolution, or workspace-context authorization writeups to the extent they conflict within this document’s scope
- Superseded By: Not yet known
- Related Decision Records: Not yet known
- Canonical Status Note: This document is the canonical FUZE specification for authorization scope binding and scope resolution semantics. Downstream APIs, services, products, caches, reports, and control-plane tools MUST NOT reinterpret the rules established here.
- Implementation Status: Normative architecture baseline; downstream API, workflow, storage, event, runtime, and audit contracts must conform
- Approval Status: Drafted for refined-system inclusion; formal approval record not yet attached
- Change Summary:
  - strengthened truth-class separation between collaborative scope truth, membership truth, scoped authorization truth, effective-permission truth, and entitlement truth
  - clarified hierarchy, scope-resolution, and inheritance rules for account, organization, workspace, product, internal operational, governance-sensitive, and object scopes
  - tightened anti-drift rules preventing product-local context, current workspace selection, or stale caches from widening authority
  - expanded implementation-contract guardrails for idempotency, replay safety, privileged correction lineage, traceability, and degraded-mode behavior

## Purpose

This specification defines the canonical FUZE scoped authorization model.

Its purpose is to make explicit:

- how FUZE binds authorization grants to explicit scope instead of allowing contextless or implicitly global authority
- how scope resolution converts canonical runtime inputs into a valid authorization context
- how grants may inherit into narrower contexts only through explicit policy-safe rules
- how scope binding, narrowing, and hierarchy interact with workspace truth, membership state, product context, and object ownership context
- how products and internal services consume scoped authorization truth without redefining collaborative scope or final permission semantics
- how APIs, events, storage, audit lineage, security controls, and operational workflows must preserve scope-aware authorization behavior

FUZE is a multi-workspace, multi-product platform. In that platform, authorization cannot safely rest on UI selection, stale local context, or product-specific assumptions. It must bind to explicit platform-owned scope. This document is the canonical rule set for that middle layer between baseline role/permission structure and final effective-permission evaluation.

## Scope

This specification governs:

- canonical scope categories used by the authorization system
- structural binding rules between roles, permissions, grants, and scope
- scope resolution prerequisites, outputs, and legality rules
- bounded inheritance and narrowing across scope hierarchy
- grant portability restrictions across workspace, organization, product, operational, governance-sensitive, and object-level contexts
- required interactions among scope truth, membership truth, and downstream effective-permission evaluation
- scoped-grant mutation rules, lineage requirements, and cache invalidation requirements
- API, event, data-model, security, audit, migration, and operational implications of scoped authorization

This specification does not define in full depth:

- canonical account identity semantics
- workspace and organization lifecycle semantics in full depth
- membership invitation and activation flows in full depth
- full role and permission catalog design
- final object-level allow/deny evaluation for every action
- entitlement or commercial eligibility formulas
- exact policy-engine DSL or storage implementation

Those concerns belong to adjacent or downstream specifications and MUST remain compatible with this document.

## Out of Scope

This specification is explicitly out of scope for:

- login, session issuance, refresh, or revocation semantics
- invitation and membership-state mutation mechanics in full depth
- exact product permission tables
- exact entitlement formulas
- exact admin break-glass staffing procedures
- exact row-level or field-level enforcement implementation for every service
- exact cache topology or deployment topology

## Design Goals

1. Make scope explicit for every meaningful authorization grant.
2. Preserve strict separation between identity, scope, membership, authorization, effective permission, and entitlement.
3. Prevent scope ambiguity from becoming authority leakage.
4. Support one canonical account operating across many workspaces, organizations, products, and internal domains.
5. Support future-safe hierarchy and narrowing without hidden global power.
6. Preserve strong auditability and reconstruction for scope-bound grant changes.
7. Prevent product-local authorization drift from replacing platform-owned scope semantics.
8. Keep governance-sensitive and internal-operational authority separately modeled and tightly controlled.
9. Provide a deterministic structural model for downstream effective-permission evaluation.
10. Make implementation-contract work stricter, clearer, and safer.

## Non-Goals

This specification is not intended to:

- treat current workspace selection as canonical authority
- treat workspace membership as unrestricted workspace-wide power
- treat product participation as platform-wide admin capability
- define every final allow or deny rule
- collapse entitlement or billing posture into scope truth
- let products invent incompatible shared scope categories for shared platform behavior
- allow cached summaries to substitute for fresh canonical scope checks on sensitive actions

## Core Principles

### 1. Explicit Scope Principle
Every durable authorization grant in FUZE MUST bind to one explicit scope category and one explicit scope reference or approved scope-pattern construct. Contextless authority is disallowed except where a narrowly defined platform-global role is explicitly owned, justified, and separately governed.

### 2. Scope-Resolution-Before-Grant-Use Principle
A grant MUST NOT be evaluated until the relevant scope has been resolved from canonical inputs.

### 3. Scope-Is-Not-Identity Principle
Identity answers who the actor is. Scope answers where the actor is operating. These truths are connected but MUST remain distinct.

### 4. Scope-Is-Not-Permission Principle
Scope defines the domain in which authority may be considered. Scope itself MUST NOT be treated as permission.

### 5. Membership-Is-Not-Scope Principle
Membership is a structural relationship between an account and collaborative scope. It is an input to scoped authorization, not a substitute for scope resolution and not final authority.

### 6. Narrowest Valid Scope Principle
Authority SHOULD be granted at the narrowest scope that satisfies the operating need, unless broader scope is explicitly required and policy-approved.

### 7. No Cross-Scope Leakage Principle
A grant for one workspace, organization, product namespace, object family, or operational domain MUST NOT silently apply to another unrelated scope.

### 8. Bounded Inheritance Principle
Inheritance across scope levels is allowed only where explicitly modeled, policy-safe, and auditable. It MUST NEVER be assumed casually.

### 9. Runtime Selector Is Not Grant Principle
Current workspace selection, active product tab, route context, or frontend state may assist scope resolution but MUST NOT create authority.

### 10. Restriction Precedence Principle
Restriction, suspension, review posture, containment, or lifecycle-state blocks affecting the relevant scope or its parent MUST outrank ordinary grants.

### 11. Product-Consumption Principle
Products may extend authorization behavior beneath a valid parent scope, but they MUST consume platform-owned scope truth instead of replacing it.

### 12. Reconstruction Principle
Scope-bound grants and scope-resolution outcomes MUST remain reconstructable from canonical records, policy references, and audit lineage.

## Canonical Definitions

### Scope
A defined operational context in which a grant may apply.

### Scope Category
A named class of scope such as account, organization, workspace, product, internal operational, governance-sensitive, or object/sub-resource scope.

### Scope Identifier
The durable identifier or composite reference that identifies the scope instance.

### Scope Resolution
The platform-controlled process that turns canonical runtime inputs into a concrete scope reference and structural scope context.

### Scope Context
The resolved structural facts needed for downstream evaluation, such as workspace ID, product namespace, object owner scope, actor relation, workspace lifecycle state, and ambiguity indicators.

### Scoped Grant
A role assignment, permission grant, or equivalent authorization binding that explicitly references scope and applies only within that scope or an explicitly modeled descendant of it.

### Scope Hierarchy
The structural parent/child relationship among scopes, such as organization to workspace or workspace to product-within-workspace.

### Scope Inheritance
The controlled ability for a broader grant to satisfy authority requirements in a narrower scope where policy explicitly allows it.

### Scope Narrowing
The process of limiting a broader role or grant to a more specific product namespace, object family, workflow family, container path, or bounded action family.

### Unscoped Grant
A grant with no explicit scope reference. Unscoped grants are non-canonical and forbidden for ordinary behavior.

### Scope Applicability
The rule family that defines which role or permission types are valid for which scope categories.

### Scope Mismatch
A condition in which the requested action, target object, or claimed context does not match the canonical resolved scope required for safe authorization.

## Truth Class Taxonomy

Downstream implementations MUST preserve the following truth classes.

### 1. Canonical Identity Truth
The durable actor anchor represented by `account_id`.

### 2. Runtime Session Truth
Temporary authenticated runtime presence and session posture. Session validity is prerequisite runtime state, not authorization.

### 3. Collaborative Scope Truth
Canonical organization and workspace records, hierarchy, ownership structure, lifecycle state, and runtime scope-selection semantics owned by the Workspace and Organization Domain.

### 4. Membership Truth
The durable relationship between an account and collaborative scope, owned by the membership lifecycle domain. Membership informs scoped authorization but does not replace it.

### 5. Scoped Authorization Truth
Canonical scope categories, scope resolution outputs, structural grant-to-scope bindings, inheritance metadata, and scoped-grant lifecycle state owned by this domain.

### 6. Effective-Permission Truth
The final evaluated allow, deny, restricted, or review-required outcome owned by the effective-permission domain. Scoped authorization supplies structural inputs but does not own the final action answer.

### 7. Entitlement Truth
Commercial or policy eligibility for products and capabilities, owned by the entitlement domain. Entitlement may constrain or block use even when scoped grants exist.

### 8. Policy / Restriction Truth
Security, risk, containment, governance, and other higher-order rules that can suppress or constrain otherwise valid structural grants.

### 9. Derived Read-Model Truth
Support views, dashboards, cached grant summaries, search projections, and UX summaries derived from canonical records.

### 10. Reporting / Public View Truth
Reports, public or partner-facing summaries, and aggregated communication artifacts. These remain downstream presentations and MUST remain correctable from canonical truth.

## Architectural Position in the Spec Hierarchy

This document sits below:

- `REFINED_SYSTEM_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
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
- product integration specifications
- internal service authorization adapters

This document owns the structural model that binds grants to scope. It does not absorb the ownership of membership lifecycle, workspace existence, general role and permission taxonomy, or final action-level evaluation semantics.

## System Boundaries

This document governs only the following platform-owned boundaries:

- canonical scope categories for authorization
- structural grant-to-scope binding rules
- scope hierarchy, inheritance, and narrowing semantics
- canonical scope-resolution prerequisites and outputs
- grant portability restrictions across scope boundaries
- auditability and lineage of scope-bound grant mutation
- scope-aware API and event expectations
- correction and supersession rules for misbound grants

It does not govern:

- workspace or organization existence in full depth
- detailed membership mutation semantics
- exact product-local permission maps
- detailed commercial entitlement rules
- final object-level effective-permission evaluation
- exact governance approval graph implementation

## Adjacent Boundaries

### Identity Domain
Owns canonical account identity. Scoped authorization consumes identity but does not redefine it.

### Auth / Session Domain
Owns session validity and runtime authentication state. Scope resolution may depend on valid session context but does not own session semantics.

### Workspace / Organization Domain
Owns organization and workspace existence, structural hierarchy, collaborative scope truth, and lifecycle state. Scoped authorization consumes those facts.

### Membership Lifecycle Domain
Owns whether the actor is structurally attached to a workspace or organization and in what state. Scoped authorization consumes that input but does not own membership mutation.

### Role / Permission Domain
Owns the baseline role and permission model. Scoped authorization binds that model to explicit scope.

### Effective Permission Domain
Owns final action-level evaluation logic after scope context, grants, restrictions, object conditions, entitlement posture, and policy are brought together.

### Entitlement / Capability Domain
Owns commercial and capability eligibility. Scoped authorization provides one structural input layer and MUST NOT collapse into entitlement.

### Security / Risk Domain
May impose restriction, containment, suspension, or review that overrides otherwise valid scoped grants.

### Admin Correction / Containment Domain
May execute policy-bound, audited corrective flows when scope or grant state is wrong, unsafe, or insufficiently recoverable through ordinary paths. It does not become the canonical owner of scope truth.

### Product Domains
May define approved product-local sub-scopes and namespaced grants beneath valid parent scope, but MUST NOT redefine shared scope categories or global semantics.

### Governance / Control-Plane Domain
Owns stronger approval pathways for governance-sensitive actions and may require special scope classes or approval overlays.

## Conflict Resolution Rules

When multiple layers disagree, FUZE MUST resolve scope-related disagreements in the following order unless a higher-order policy explicitly overrides it:

1. canonical identity truth for the actor
2. canonical workspace or organization truth, including lifecycle posture
3. canonical membership truth
4. canonical scope-resolution rules and scope ownership metadata
5. explicit restriction, risk, containment, and governance posture
6. scoped-grant validity and applicability
7. effective-permission evaluation
8. entitlement posture
9. derived views, caches, UI state, and reports

Additional rules:

- canonical owner scope metadata for the target object MUST outrank client-supplied scope hints
- explicit parent-scope restrictions MUST outrank broader inferred allows
- ambiguous scope MUST resolve to deny or review-required, never silent fallback
- stale caches MUST NOT outrank fresh canonical scope or grant truth
- privileged correction MUST preserve lineage rather than flattening prior mistaken state

## Default Decision Rules

When ambiguity exists and no narrower policy-specific rule is available, the platform MUST apply the following defaults:

- default to no grant portability across scopes
- default to no inheritance from broader to narrower scope unless explicitly declared
- default to no escalation from product scope to workspace scope, from workspace scope to organization scope, or from ordinary operational scope to governance-sensitive scope
- default to server-side scope resolution over client-supplied context
- default to deny or review-required for unresolved, mismatched, or structurally ambiguous scope
- default to preserving existing canonical control relationships during correction rather than risking orphaned ownership
- default to canonical parent-scope attachment for product and object scopes
- default to explicit reason codes and audit lineage for every privileged scope correction
- default to derived-view invalidation on any authoritative scope or scoped-grant mutation

## Roles / Actors / Entities

### Canonical Account Actor
The authenticated FUZE account attempting to act.

### Scope Subject
The scope instance under which the actor attempts to operate, such as an account, workspace, product-within-workspace, internal operational domain, or governance-sensitive domain.

### Scope Owner
The domain owner of the scope record itself.

### Scope Resolver
The platform service or boundary that resolves the intended scope from canonical runtime inputs and target metadata.

### Scoped Role Holder
An actor with a role assignment bound to a specific scope.

### Scoped Permission Holder
An actor with a permission grant or derived permission set bound to a specific scope.

### Product Authorization Consumer
A product or internal service that reads scoped grants and scope context to request or perform authorization evaluation.

### Control-Plane Reviewer
A privileged actor who may approve, constrain, or correct scope-bound grants in high-sensitivity cases.

### Object Owner Scope
The canonical scope to which a product object, container, or sub-resource belongs for authorization purposes.

## Ownership Model

### Scoped Authorization Domain Owns
- canonical authorization scope categories
- structural binding rules between grants and scope
- scope-reference format and applicability semantics
- inheritance and narrowing rules between scope layers
- scope-resolution outputs and mismatch semantics
- audit lineage for scope-bound grant mutation in coordination with the audit domain
- publication of scope-aware grant events after canonical commit

### Scoped Authorization Domain May
- expose canonical scope applicability metadata
- validate whether a requested grant may bind to the requested scope
- publish scoped-grant summaries and scope-resolution summaries for downstream consumers
- coordinate with effective-permission, entitlement, security, and product domains through explicit contracts

### Scoped Authorization Domain Must Not
- redefine identity truth
- redefine workspace or organization truth
- redefine membership lifecycle
- own final effective-permission outcomes
- own commercial eligibility truth
- allow products to invent incompatible shared scope models
- treat UI selection, route state, or cached context as canonical scope truth

## Authority / Decision Model

The scoped-authorization decision model is layered.

### Upstream Layers Provide
- canonical account identity
- runtime session validity
- workspace or organization context where relevant
- membership presence and status
- workspace or organization lifecycle state
- product namespace or object ownership hints where relevant
- restriction, containment, or privileged approval context where relevant

### Scoped Authorization Layer Decides
- what scope the request is operating in
- whether the relevant grant may attach to that scope category
- whether a broader grant may apply to a narrower context
- whether a narrower grant is insufficient for a broader action
- whether a scope mismatch requires deny or review instead of fallback
- whether the requested grant mutation is structurally legal

### Effective Permission Layer Decides
- whether the resolved scoped grant plus object conditions, restriction state, policy state, and entitlement posture yields the final outcome

This separation is mandatory. Scoped authorization is the structural binding layer, not the full evaluation engine.

## Canonical Scope Categories

FUZE MUST support at least the following scope categories.

### 1. Account Scope
Authority that applies only within the actor’s own account domain.

Representative uses:
- managing own linked access methods
- viewing own receipts
- initiating self-service account-recovery steps where policy allows
- reading own audit summaries where policy allows

### 2. Organization Scope
Authority that applies to a broader organizational umbrella where that model exists distinctly.

Representative uses:
- reporting across contained workspaces
- managed provisioning or policy-setting across child workspaces where policy allows
- higher-level administration over explicitly modeled child workspaces

### 3. Workspace Scope
Authority that applies to one specific workspace, which remains the primary actionable collaborative scope.

Representative uses:
- inviting or removing members
- assigning workspace roles
- editing shared settings
- operating shared workspace workflows
- managing workspace-scoped product surfaces

### 4. Product Scope
Authority that applies to one product namespace within a valid account or workspace context.

Representative uses:
- operating a product workflow in one workspace
- administering product-local content inside one workspace
- configuring a bounded product feature set under one account or workspace parent scope

### 5. Internal Operational Scope
Authority that applies to internal support, finance, risk, trust, reporting, or other operational domains.

Representative uses:
- support correction execution
- abuse review
- finance-side corrective actions
- operational diagnostics and restricted support tooling

### 6. Governance-Sensitive Scope
Authority that applies to higher-trust operations requiring stronger control pathways.

Representative uses:
- registry-affecting changes
- payout-cycle operational transitions
- high-impact credits control actions
- emergency control posture actions

### 7. Object / Sub-Resource Scope
Authority that applies to a bounded object family, object container, or sub-resource nested under a broader parent scope.

Representative uses:
- managing one project
- editing one workflow collection
- administering one automation namespace
- viewing one reporting dataset

Object scope remains subordinate to broader account, workspace, organization, or product structure and MUST NOT become an alternate shared-scope system.

## Scope Hierarchy Model

FUZE MUST support a bounded hierarchy of scope rather than a flat or global-only model.

### Representative Structural Hierarchy

1. **Account**
2. **Organization** (optional umbrella)
3. **Workspace**
4. **Product Within Workspace or Account**
5. **Object / Container / Sub-Resource**

Not every path is required for every feature, but the model MUST keep these distinctions expressible.

### Hierarchy Rules
- organization may contain one or more workspaces where implemented distinctly
- workspace remains the primary actionable collaborative scope
- product scope MUST attach to a valid parent scope, usually account or workspace
- object or sub-resource scope MUST attach to a valid parent scope
- broader scope does not imply unrestricted authority in all descendants unless explicitly modeled
- narrower scope NEVER implies authority in its parent scope
- internal operational and governance-sensitive scopes remain separately classified even when their actions affect workspace or product state

## Scope Resolution Model

Scope resolution is a platform-controlled process.

### Scope Resolution Inputs May Include
- canonical account identity from session
- requested action or route family
- explicit workspace identifier
- explicit organization identifier
- explicit product namespace
- object identifier and object owner scope
- membership state
- workspace or organization lifecycle state
- request route family or control-plane context
- privileged approval context where applicable

### Scope Resolution Outputs Must Include At Minimum
- resolved scope category
- resolved scope identifier
- parent scope reference where applicable
- actor relation to scope
- scope validity status
- structural mismatch and ambiguity indicators
- correlation identifier or equivalent trace linkage

### Scope Resolution Rules
- scope MUST be resolved before evaluating scoped grants
- ambiguous scope MUST NOT silently fall back to a broader scope
- runtime UI selection is an input hint, not canonical proof
- product routes MUST NOT silently invent workspace scope when canonical workspace context is missing
- object owner scope MUST be derived from canonical object metadata, not user assumption
- missing or conflicting inputs MUST produce deny or review-required, not silent permissiveness
- server-side resolution MUST win over client-supplied scope claims
- resolution results MUST be reconstructable for sensitive actions

## Grant Binding Model

Every durable authorization grant MUST bind to one explicit scope category and one explicit scope identifier or approved scope-pattern construct.

### Allowed Structural Forms
- role assignment to one account scope
- role assignment to one organization scope
- role assignment to one workspace scope
- role assignment to one product scope under a parent workspace or account
- internal operational role assignment under one operational domain
- governance-sensitive role assignment under one governance-sensitive domain
- object-scoped grant under an approved parent scope

### Disallowed Structural Forms
- implicit grant with no scope
- workspace-wide semantics inferred from one product-local grant without explicit policy
- product-wide semantics inferred from one object grant
- organization-wide semantics inferred from one workspace grant
- authority widened by frontend-selected context alone
- global shared-platform semantics inferred from workspace-local role names

### Grant Binding Rules
- scope applicability MUST be validated at grant-creation time
- invalid scope-to-role combinations MUST be rejected
- product-scoped grants MUST include product namespace and parent scope reference
- internal operational and governance-sensitive grants MUST remain separately classified
- expired, revoked, restricted, or superseded grants MUST NOT remain structurally valid through stale caches
- direct grants outside approved catalogs or approved exception pathways are non-canonical and forbidden

## Inheritance and Narrowing Model

Scoped authorization MAY support inheritance, but it MUST be explicit and bounded.

### Permitted Examples
- a workspace-owner role may satisfy certain workspace-subscope requirements where policy explicitly allows
- an organization-level administrative grant may apply to a child workspace only where organization/workspace hierarchy and policy explicitly allow it
- a product-manager grant for one workspace-specific product scope may apply to bounded object scopes inside that product where policy declares that relation

### Disallowed Examples
- workspace membership automatically inheriting product admin across all products
- product admin automatically inheriting workspace ownership or billing authority
- workspace owner automatically inheriting platform admin or governance-sensitive authority
- one workspace grant automatically applying to another workspace
- one object grant automatically expanding to all sibling objects without explicit policy

### Narrowing Rules
- grants MAY be narrowed by object owner scope, product namespace, workflow family, container path, or bounded action family
- narrowed grants MUST remain auditable and machine-checkable
- direct per-object overrides SHOULD remain rare, visible, and policy-constrained
- narrowing MAY reduce authority but MUST NOT silently widen it

## Relationship to Membership and Workspace Truth

Scoped authorization depends on workspace and membership truth but does not own them.

### Required Rules
- workspace scope exists only if canonical workspace truth exists
- workspace-scoped grants are meaningful only when the actor’s relation to that workspace is valid enough for downstream use
- membership state is a structural input, not the final authorization answer
- removed, suspended, restricted, or containment-affected membership MAY invalidate otherwise matching scoped grants
- active workspace selection is runtime context only and MUST NOT substitute for membership or scope truth
- workspace or organization lifecycle restrictions MAY suppress scoped-grant usability even where grant records still exist

## Relationship to Effective Permission

Scoped authorization is upstream of effective permission and MUST remain distinct from it.

### Required Rules
- scoped authorization determines candidate structural applicability, not final action outcome
- effective permission consumes resolved scope context plus scoped grants, restrictions, object conditions, policy posture, and entitlement posture
- products MUST NOT answer high-impact permission questions from scoped-grant presence alone
- cacheable scoped-grant summaries MUST NOT be treated as final allow for sensitive actions

## Relationship to Entitlement and Capability Gating

Scoped authorization is not the same as entitlement.

### Required Rules
- a valid scoped grant may still be insufficient if the capability is not entitled
- entitlement without a valid scoped grant is insufficient for protected action
- products MUST NOT confuse plan status, billing status, or feature rollout assignment with scoped authorization
- entitlement-aware blocks MAY suppress use of structurally valid grants downstream, but they do not redefine scope truth

## Relationship to Product and Object-Level Policy

Products may refine authorization beneath the shared platform model.

### Allowed
- product-specific sub-scopes under a valid parent scope
- object-specific permissions within a valid product or workspace scope
- product-local role namespaced under product ownership
- product-local object-condition checks that feed downstream effective-permission evaluation

### Not Allowed
- alternate workspace systems for shared behavior
- global product-admin roles that silently act across all workspaces
- product-local overrides that bypass platform restriction, containment, or governance rules
- object-owner assumptions that ignore canonical object scope metadata

## Invariants

The following invariants are mandatory:

1. every durable grant has explicit scope
2. scope is resolved before grant evaluation
3. workspace selection is not authority
4. membership is not final permission
5. narrower scope never implies broader authority
6. broader scope applies to narrower scope only through explicit policy
7. object owner scope outranks client-supplied scope hints
8. stale caches cannot preserve authority after canonical invalidation
9. privileged correction preserves lineage rather than erasing history
10. products cannot redefine shared scope semantics

## Functional Rules

- all sensitive actions MUST reference a resolvable scope
- scope mismatch MUST fail closed
- machine-readable mismatch, unresolved-scope, restricted-scope, and review-required outcomes MUST exist in downstream contracts
- role and permission catalogs MUST declare scope applicability
- broader-scope grants may apply to narrower scopes only through explicit inheritance metadata
- narrower grants MUST NOT satisfy broader actions
- control-plane or governance-sensitive routes MUST require explicitly modeled scope classes and stronger authorization posture
- derived views and caches are advisory only for sensitive scope checks

## Permission / Access Considerations

This document defines structural authorization rules but imposes mandatory constraints for downstream access evaluation:

- candidate grants MUST be derivable from valid scope-bound grants
- final allow MUST remain downstream of effective-permission evaluation
- higher-order restrictions MAY suppress scoped grants without deleting them
- review-required is a valid canonical outcome where scope mapping is ambiguous, cross-domain sensitive, or pending privileged correction
- current-workspace UI state MUST NOT resurrect invalidated scope-bound access

## Entitlement Considerations

- entitlement state may attach to account, workspace, organization, or approved subject, but it is not authority truth
- scoped authorization MUST provide structurally correct input to entitlement-aware evaluation
- capability-gating layers MAY narrow usable authority but MUST NOT mutate scope truth by side effect
- entitlement caches MUST be invalidated or refreshed when scope changes alter subject attachment

## API / Contract Implications

The platform MUST expose scope-aware authorization through explicit contracts.

### Authorization APIs SHOULD Support
- scope applicability metadata for roles and permissions
- explicit scope-resolution reads
- grant creation with required scope references
- grant revocation by scoped assignment ID
- scope-aware authorization evaluation requests
- machine-readable scope mismatch and ambiguity outcomes

### API Rules
- all mutation-capable endpoints MUST support correlation identifiers
- idempotency is required where retries are plausible, especially for scoped role assignment, scoped role revocation, and scoped corrective actions
- control-plane routes for broader or governance-sensitive scope classes require stronger authorization posture
- API responses MUST distinguish unsupported scope, unresolved scope, unauthorized scope, restricted scope, and review-required scope
- products MUST pass explicit scope identifiers rather than relying on presentation-layer assumptions
- scope-correction and supersession flows MUST preserve prior assignment lineage

## Event / Async Implications

Scoped authorization changes MUST emit durable events sufficient for downstream audit, synchronization, and cache invalidation.

Representative semantic events include:
- `authz.scope_resolved`
- `authz.scope_resolution_failed`
- `authz.scoped_role_assigned`
- `authz.scoped_role_revoked`
- `authz.scoped_grant_corrected`
- `authz.scope_restriction_applied`
- `authz.scope_hierarchy_changed`

### Event Rules
- canonical events MUST be emitted only after owning-domain state commits
- async delivery failure MUST NOT redefine canonical scope or grant truth
- event consumers MAY refresh caches or projections but MUST NOT become scope owners
- events MUST preserve enough parent and child reference context to support downstream evaluation and audit
- event ordering guarantees for grant mutation and revocation MUST be strong enough to prevent stale-authority continuation

## Data Model / Storage Implications

At minimum, the platform SHOULD support the following durable semantic structures.

### `authorization_scope`
Representative semantic fields:
- `scope_id`
- `scope_category`
- `parent_scope_id`
- `owner_domain`
- `status`
- `namespace`
- `created_at`
- `updated_at`

### `scoped_role_assignment`
Representative semantic fields:
- `assignment_id`
- `account_id`
- `role_name`
- `scope_category`
- `scope_reference`
- `parent_scope_reference`
- `assigned_by`
- `assigned_at`
- `expires_at`
- `status`
- `correction_reference`
- `supersedes_assignment_id`

### `scoped_permission_binding`
Representative semantic fields:
- `permission_name`
- `scope_applicability_category`
- `inheritance_metadata`
- `narrowing_metadata`
- `status`
- `policy_version_reference`

### `scope_resolution_audit`
Representative semantic fields:
- `action_reference`
- `actor_account_id`
- `requested_scope_inputs`
- `resolved_scope_outputs`
- `ambiguity_indicator`
- `result_status`
- `timestamp`
- `correlation_id`

### Data Rules
- scope and scoped-grant truth MUST remain durable canonical records or controlled canonical configuration
- derived views and product caches MUST NOT become write owners
- correction MUST preserve lineage rather than destructively flattening scope history
- object owner scope references MUST be durable enough to support correct downstream evaluation
- replayed mutation requests MUST NOT create duplicated effective assignments

## Read Model / Projection / Reporting Rules

- support views MAY expose scope summaries, but MUST distinguish canonical records from derived summaries
- cached scope summaries MAY accelerate low-risk reads, but MUST NOT become the source of truth for sensitive actions
- reports and analytics MAY aggregate scope-bound grants, but MUST remain traceable back to canonical records
- public or partner-facing summaries MUST NOT expose privileged scope internals and MUST remain correctable from canonical truth
- projection lag MUST NOT silently widen authority

## Security / Risk / Abuse Controls

Scoped authorization is a security boundary because scope confusion is authority confusion.

The platform MUST preserve:
- fail-closed behavior on unresolved or mismatched scope
- no implicit widening from runtime context or product-local assumptions
- stronger controls around broader-scope and governance-sensitive assignments
- anti-self-escalation protections when scope assignment affects membership, billing, or control-plane authority
- rapid suppression of grants when the relevant scope is restricted, suspended, or structurally invalid
- auditability for scope-binding and scope-correction mutations
- explicit approval pathways for high-trust scope classes where policy requires them

## Boundary Violation Detection / Non-Canonical Patterns

The following patterns are non-canonical and forbidden:

- unscoped roles used for ordinary workspace or product behavior
- frontend state treated as authority
- product-local “team” models replacing canonical workspace scope for shared behavior
- workspace-wide admin semantics inferred from one product-local grant
- organization-wide semantics inferred from one workspace grant
- object owner heuristics replacing canonical object-scope metadata
- destructive correction that erases prior grant lineage
- stale cache continuation after membership removal, workspace suspension, or scoped-grant revocation

Implementations SHOULD detect and surface these conditions through diagnostics, audit, and operational alerts.

## Audit / Traceability Requirements

FUZE MUST be able to determine:

- which scope a role or permission grant was bound to
- who created, changed, revoked, or corrected that scoped grant
- what parent scope relation existed at the time
- why a scope mismatch caused deny or review
- how product or object scope attached to its parent workspace or account
- whether broader grant inheritance into narrower context was legitimate
- whether a visible scope summary is canonical or derived
- how current scoped-grant state was reached from prior states

Scope mistakes are high-impact because they silently widen or misroute authority if not reconstructable.

## Failure Handling / Edge Cases

### Valid Session, No Scope
Protected workspace or product action MUST fail closed until scope is resolved.

### Workspace Selected, Membership Missing
The selected workspace may appear in UI history or stale runtime state, but scope-bound authority MUST be denied.

### Product Object Belongs to Different Workspace Than Requested
Canonical object owner scope MUST win. The request MUST fail or reroute explicitly.

### Organization Admin Acts on Child Workspace
Only explicitly modeled organization-to-workspace inheritance may allow this. It MUST NOT be assumed.

### Product Admin Attempts Workspace Billing Action
The grant is mismatched in scope and domain. The action MUST be denied unless separately granted.

### Workspace Owner Attempts Governance-Sensitive Action
Ordinary workspace scope is insufficient. The action MUST route through governance-sensitive scope and stronger control.

### Membership Removed But Scoped Grant Cache Persists
Canonical membership and scope validity MUST suppress use of stale scoped grants.

### Object Migrated Between Scopes
Grant applicability MUST be re-evaluated against the new canonical owner scope. Stale object-scope assumptions MUST NOT persist silently.

### Wrong Scope Passed by Client
Server-side scope resolution MUST detect mismatch and fail closed.

## Operational Considerations

Operational systems SHOULD support:
- diagnostics for scope mismatch and unresolved-scope failures
- observability for repeated ambiguity, stale parent-scope references, and cross-scope misuse attempts
- control-plane tooling for correcting misbound grants
- replay-safe mutation handling for scoped assignments
- cache invalidation or refresh mechanisms when parent scope, membership, workspace state, or restriction posture changes
- runbooks for wrong-scope product mutations, object-scope migration, inherited-scope disputes, and stale-grant suppression

The operational model MUST preserve the principle that scope is canonical authorization structure, not presentation-layer convenience.

## Migration / Compatibility / Supersession Considerations

- migrations MUST remove contextless or ambiguously scoped grants where they exist
- older implementations that imply workspace selection equals authority, or that product-local admin acts across all workspaces, are superseded within this scope
- compatibility layers MAY preserve legacy role names temporarily, but explicit scope binding MUST become canonical
- flattened organization/workspace models MAY be preserved temporarily, but grants MUST remain explicit about the scope they bind to
- object-scope and product-scope adoption SHOULD prefer explicit parent-scope references over implicit inference
- migrations MUST not silently convert product-local participation into workspace-wide scope authority

## Implementation-Contract Guardrails

Downstream implementations MUST preserve all of the following:

1. every durable grant remains explicitly scoped
2. scope resolution stays server-owned, deterministic, and auditable
3. session state remains distinct from scope truth
4. membership remains structural input, not final permission
5. effective permission remains downstream of scoped authorization
6. entitlement remains separate from scoped authorization truth
7. product-local abstractions remain subordinate to canonical parent scope
8. privileged correction remains bounded, reason-coded, policy-constrained, and audited
9. derived views remain derived and regenerable
10. degraded runtime conditions MUST NOT cause hidden semantic downgrades or truth substitution
11. idempotency and replay safety MUST be preserved for side-effecting scope and grant workflows
12. downstream teams MUST NOT optimize away explicit state, lineage, or reason capture where those elements protect security, financial integrity, auditability, or control continuity

## Downstream Execution Staging

This specification implies the following preferred execution order for downstream implementation-contract work:

1. stabilize workspace and organization scope semantics
2. stabilize membership lifecycle semantics
3. stabilize baseline role and permission applicability metadata
4. implement scoped grant structures and scope-resolution services
5. integrate final effective-permission evaluation
6. integrate entitlement and capability gating
7. integrate privileged correction and containment pathways
8. build reporting, support, audit, and derived-read surfaces

## Required Downstream Specs / Contract Layers

This specification directly requires compatible downstream refinement and implementation-contract work in:

- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- product integration specifications
- internal service authorization adapters
- control-plane workflow specifications

## Canonical Examples / Anti-Examples

### Canonical Example 1 — Workspace Role Bound to One Workspace
An actor is assigned a workspace-admin role for workspace A. The grant authorizes workspace-admin actions only in workspace A and any explicitly modeled descendants. It does not authorize actions in workspace B.

### Canonical Example 2 — Product Scope Under Workspace
An actor receives a product-manager role for a named product namespace within workspace A. The grant may authorize bounded product actions within that namespace and workspace. It does not create workspace ownership, billing authority, or authority across other product namespaces unless policy explicitly allows it.

### Canonical Example 3 — Organization Grant Narrowed to Child Workspace
An organization-level operations role may authorize certain actions in child workspaces only where hierarchy and policy explicitly allow that inheritance and the target workspace remains within the same organization.

### Canonical Example 4 — Object Scope Attached to Parent Product Scope
A bounded object-admin grant for one automation namespace inside a workspace product context permits actions for that namespace only. It does not widen into full product admin or full workspace admin.

### Anti-Example 1 — Current Workspace as Authority
A client remembers the last selected workspace and sends a mutation request without fresh canonical scope confirmation. The platform treats the remembered workspace as authoritative and permits the action. This is forbidden.

### Anti-Example 2 — Product Admin Implies Workspace Billing
A product-local admin role is treated as sufficient to mutate workspace billing settings. This is forbidden unless a separately valid workspace or governance-sensitive grant exists.

### Anti-Example 3 — Membership Equals Final Permission
An actor is an active workspace member and is therefore treated as authorized for every workspace action. This is forbidden.

### Anti-Example 4 — One Workspace Grant Applies to Another
A workspace-owner assignment in workspace A is used to manage roles in workspace B. This is forbidden.

### Anti-Example 5 — Silent Grant Rewrite
A misbound grant is corrected by destructive overwrite with no preserved lineage to the prior mistaken state. This is forbidden.

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
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `WORKSPACE_MEMBERSHIP_LIFECYCLE_SPEC.md`
- `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`

This specification directly governs or materially informs:

- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- product integration specifications
- internal service authorization adapters
- control-plane workflow specifications

## Explicitly Deferred Items

The following are intentionally deferred to adjacent or downstream specifications:

- exact policy-engine implementation language
- exact database-level row and field enforcement strategy
- exact per-product object rule tables
- exact end-user explanation copy for each deny reason
- exact observability dashboards and alert thresholds
- exact governance approval workflow implementation
- exact control-plane UI layout for correction and scope review

These do not weaken the canonical scoped-authorization model established here.

## Final Normative Summary

FUZE scoped authorization is the canonical structural layer that binds grants to explicit scope. It is not identity, not workspace truth, not membership truth, not final effective permission, and not entitlement. It resolves where the actor is operating, determines which grants can structurally apply there, constrains inheritance and narrowing, and supplies deterministic, auditable input to downstream effective-permission evaluation.

Every durable grant MUST be explicitly scoped. Every sensitive action MUST resolve canonical scope before grant use. Broader authority MUST NOT leak into narrower or unrelated contexts without explicit policy. Products and derived views MUST consume this model rather than replacing it.

This document is the canonical FUZE rule set for scope-bound authorization structure.

## Quality Gate Checklist

- [x] Canonical owner is explicit for every material truth family
- [x] Mutation boundaries are explicit
- [x] Adjacent boundaries are explicit
- [x] Truth classes are explicit
- [x] Conflict-resolution rules are explicit
- [x] Default decision rules are explicit
- [x] Non-canonical patterns are clearly called out
- [x] Operator and privileged-correction paths are bounded and audited
- [x] Read-model, cache, and reporting rules are explicit
- [x] Failure and degraded-mode behaviors are explicit
- [x] Downstream implementation guardrails are explicit
- [x] Dependencies and downstream impacts are explicit
- [x] Non-goals and deferred items are explicit
- [x] The document is strong enough to support downstream implementation-contract work without contradictory semantics
