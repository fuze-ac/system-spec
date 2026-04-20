# ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC

## Document Metadata

- Document Name: `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Governing Layer: Platform core / access evaluation and effective permission
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, security, support operations, audit, governance, API design, operations
- Primary Purpose: Define the canonical FUZE model for evaluating access requests and producing effective permission outcomes from identity, session, scope, membership, role grants, scoped authorization, object state, restriction state, entitlement posture, and higher-order policy without collapsing those upstream domains into one ambiguous layer
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
  - `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
  - `WORKSPACE_AND_ORGANIZATION_SPEC.md`
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
  - `WORKSPACE_MEMBERSHIP_LIFECYCLE_SPEC.md`
  - `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
  - `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - `AUTH_IDENTITY_API_SPEC.md`
  - `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
  - `WORKSPACE_ORGANIZATION_API_SPEC.md`
  - `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- Primary Downstream Dependents:
  - `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
  - `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
  - `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
  - `WORKSPACE_ORGANIZATION_API_SPEC.md`
  - `ROLE_PERMISSION_ACCESS_API_SPEC.md`
  - product integration specifications
  - internal service authorization adapters
  - control-plane workflow specifications
  - audit and observability specifications

---

## Purpose

This specification defines the canonical FUZE access-evaluation and effective-permission model.

Its purpose is to make explicit:

- how FUZE turns a concrete access request into a final effective-permission outcome
- how identity, session validity, scope resolution, membership state, role grants, scoped grants, restrictions, object conditions, entitlement posture, and higher-order policy interact
- how FUZE distinguishes structural grant availability from final action permission
- how evaluation outcomes must remain deterministic, auditable, and fail-closed
- how products and internal services must consume canonical evaluation outcomes without inventing weaker local shortcuts
- how APIs, events, data models, audit records, and runtime systems should represent allow, deny, restricted, and review-required outcomes

This document exists because the surrounding refined FUZE access library already establishes that login is not authorization, scope must be resolved before authorization, and roles/permissions are only the structural grant model. A separate canonical layer is therefore required to define how those inputs become the final answer to a real request. That layer is access evaluation and effective permission.

---

## Scope

This specification governs:

- canonical access-evaluation ordering
- the semantic difference between candidate grants and effective permission
- evaluation inputs and normalization requirements
- allow/deny/review-required outcome model
- restriction, suspension, lifecycle-state, and policy precedence rules
- object-state and parent-scope conditions as evaluation inputs
- interaction between permission evaluation and entitlement gating at the evaluation layer
- evaluation caching, invalidation, and stale-read constraints
- API, event, data-model, audit, security, and operational implications of effective-permission outcomes

This specification does not define:

- canonical account identity in full depth
- session lifecycle in full depth
- workspace or membership lifecycle in full depth
- role and permission catalog design in full depth
- scoped grant structure in full depth
- exact entitlement formulas or commercial policy tables
- exact UI wording for access explanations
- exact policy-engine DSL or storage engine implementation

Those concerns remain in adjacent or upstream specifications.

---

## Out of Scope

This specification is explicitly out of scope for:

- login and session issuance mechanics
- invitation and membership correction mechanics
- exact role naming and exact permission namespace taxonomy
- exact pricing and subscription formulas
- exact row-level security implementation per database technology
- exact frontend feature-flag rendering behavior unless it claims canonical permission truth
- exact break-glass or privileged operator staffing procedures

---

## Design Goals

The design goals of the FUZE access-evaluation and effective-permission model are:

1. produce deterministic, explicit, and auditable access outcomes
2. preserve strict separation between structural grants and final action permission
3. prevent stale or partial context from becoming unsafe authorization
4. fail closed when required inputs are missing, invalid, or ambiguous
5. support one actor operating safely across many scopes and products
6. support object-sensitive and policy-sensitive evaluation without creating hidden product-local truth
7. keep restriction, suspension, review, and risk posture structurally stronger than stale grants
8. support future-safe commercial, product, and governance-sensitive policy layers
9. make authorization reasoning explainable to internal operators and implementable by services
10. keep effective permission platform-owned even when products define product-local objects and rules

---

## Non-Goals

This specification is not intended to:

- treat role assignment as the same thing as final permission
- allow products to answer sensitive permission questions from UI state alone
- treat workspace membership as sufficient final authority
- treat entitlement as equivalent to permission
- treat object ownership or creator status as universal override
- define every product object rule exhaustively at this layer
- permit cached summaries to outrank fresh canonical evaluation for sensitive actions

---

## Core Principles

### 1. Effective-Permission Principle
A final permission outcome is not the same thing as a role, a permission string, a scope, a membership, or an entitlement. It is the evaluated result of those inputs under current state and policy.

### 2. Structural-Grant / Final-Outcome Separation Principle
Roles and scoped grants define what authority may be considered. Access evaluation determines what is actually allowed now.

### 3. Fail-Closed Principle
If required identity, session, scope, membership, object, restriction, or policy inputs are missing or invalid, the request must not default to allow.

### 4. Restriction-Precedence Principle
Restriction, suspension, review posture, or lifecycle-state blocks outrank ordinary grants.

### 5. Scope-First Principle
Evaluation must occur against the correct canonical scope. Wrong-scope evaluation is unsafe evaluation.

### 6. Fresh-State Principle
Sensitive access decisions must rely on sufficiently fresh canonical state, not stale caches or UI assumptions.

### 7. Narrowest-Applicable-Rule Principle
Where multiple rules apply, the evaluation should prefer the narrowest relevant object and scope facts, while still respecting stronger parent-scope restrictions and broader policy-deny conditions.

### 8. Product-Consumption Principle
Products may supply product-local object facts and rules, but final effective-permission behavior remains subordinate to platform-owned identity, scope, membership, restriction, and authorization layers.

### 9. Review-Is-First-Class Principle
For ambiguous, high-risk, or governance-sensitive actions, `review_required` is a legitimate canonical result and not merely an implementation fallback.

### 10. Auditability Principle
Every high-impact allow, deny, restriction, and review outcome must be reconstructable from canonical inputs and policy references.

---

## Canonical Definitions

### Access Evaluation
The platform process of resolving whether a requested action is permitted under current identity, session, scope, membership, grant, object, entitlement, restriction, and policy conditions.

### Candidate Permission
A permission that appears structurally available from one or more valid grants before final policy and state checks complete.

### Effective Permission
The final action-level authorization outcome after all relevant conditions are evaluated.

### Evaluation Context
The normalized set of inputs used to evaluate a request, including actor, scope, request intent, target object, object owner scope, membership state, grants, restrictions, entitlement posture, and policy references.

### Evaluation Outcome
The canonical result of access evaluation, such as:
- `allow`
- `deny`
- `review_required`
- `restricted`
- `scope_unresolved`
- `entitlement_missing`
- `policy_blocked`

These exact names may vary in implementation, but the semantic distinctions must remain expressible.

### Policy Deny
A deny outcome caused by a governing rule even when structural grants might otherwise suggest allow.

### Restriction Override
A higher-priority condition that suppresses a candidate permission.

### Object Condition
A property of the target object, object family, or object owner scope that materially affects access evaluation.

### Parent-Scope Constraint
A broader workspace, organization, operational, or governance condition that constrains otherwise matching narrower grants.

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
- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `WORKSPACE_MEMBERSHIP_LIFECYCLE_SPEC.md`
- `SCOPED_AUTHORIZATION_MODEL_SPEC.md`

and above:

- `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- product integration specifications

This document owns final evaluation structure and effective-permission semantics. It does not redefine identity, sessions, workspace truth, membership truth, role catalogs, or scoped grant structure.

---

## System Boundaries

This document governs only the following platform-owned boundaries:

- canonical access-evaluation sequence
- structural distinction between candidate grants and final permission
- evaluation outcome semantics
- precedence rules among grants, restrictions, object conditions, entitlements, and policy
- constraints for caches and derived access summaries
- canonical audit direction for effective-permission outcomes
- service-facing API and event expectations for evaluated permission results

It does not govern:

- account identity creation
- session issuance and refresh
- workspace creation and lifecycle in full depth
- membership invitation and removal mechanics
- exact grant applicability structures in full depth
- commercial plan truth
- exact product object schema design
- exact governance workflow graph

---

## Adjacent Boundaries

### Identity Domain
Owns canonical actor identity and account state. Access evaluation consumes this truth but does not define it.

### Auth / Session Domain
Owns session validity, recent-auth posture, privileged-session posture, and containment/invalidation state. Access evaluation consumes this truth.

### Workspace / Organization Domain
Owns collaborative scope records and lifecycle state. Access evaluation consumes resolved scope and scope state.

### Membership Lifecycle Domain
Owns membership existence and state. Access evaluation uses membership as a prerequisite input for many workspace-scoped actions.

### Role / Permission Domain
Owns role and permission catalogs and baseline structural grants. Access evaluation uses these as candidate-input sources.

### Scoped Authorization Domain
Owns how grants bind to scope. Access evaluation uses those bindings after scope resolution.

### Entitlement / Capability Domain
Owns commercial and product capability eligibility. Access evaluation must coordinate with it but must not collapse into it.

### Security / Risk Domain
May impose deny, restriction, review, or containment that overrides otherwise matching grants.

### Product Domains
May provide product-local object facts, namespaced rules, and product capability semantics, but may not replace platform-owned evaluation ordering or final effective-permission truth.

### Audit / Traceability Domain
Owns durable access traceability surfaces informed by evaluation lineage and outcomes.

---

## Roles / Actors / Entities

### Actor
The canonical FUZE account or approved internal service principal attempting an action.

### Requesting Session
The current runtime session or privileged session context attached to the actor.

### Target Scope
The canonical scope in which the action is being attempted.

### Target Object
The resource, object family, workflow, report, setting, or control-plane action being evaluated.

### Candidate Grant Set
The set of structurally relevant grants that survive basic scope applicability checks.

### Evaluator
The policy-aware service or module that computes effective permission.

### Policy Owner
The domain that owns a deny, review, or narrowing rule used during evaluation.

### Authorization Consumer
A product, API layer, worker, or internal service that requests or consumes evaluation outcomes.

---

## Ownership Model

### Effective-Permission Domain Owns
- canonical access-evaluation ordering
- normalization of evaluation context
- candidate-grant to final-outcome transition rules
- canonical outcome semantics
- precedence rules between grants, restrictions, entitlements, object conditions, and policy
- stale-evaluation safety requirements
- publication of effective-permission-related events and audit references in coordination with adjacent domains

### Effective-Permission Domain May
- expose evaluation APIs
- expose explainable machine-readable outcome codes
- request supporting context from identity, workspace, membership, grant, product, entitlement, and risk domains
- publish policy-reference metadata with outcomes
- support deterministic cacheable read models for low-risk scenarios under safety limits

### Effective-Permission Domain Must Not
- redefine upstream identity, session, workspace, or membership truth
- redefine grant ownership
- redefine commercial plan truth
- rely on product-local hidden state as the sole source for sensitive access decisions
- silently widen ambiguous results into allow

### Product Domains May
- request effective-permission decisions
- supply product-local object attributes and product rule modules through approved contracts
- cache low-risk derived summaries subject to freshness and invalidation rules

### Product Domains Must Not
- treat derived permission badges as canonical for sensitive mutation
- bypass platform-owned evaluation ordering
- silently keep access alive after revocation, suspension, removal, or containment
- convert entitlement or subscription state into local permission truth

---

## Authority / Decision Model

Access evaluation is the final structural stage before action execution.

### Upstream Layers Provide
- canonical actor identity
- valid session or service principal state
- resolved scope
- membership state where applicable
- valid grants bound to that scope
- object owner scope and object metadata where applicable
- restriction, suspension, or review posture
- entitlement and capability posture where applicable
- relevant higher-order policy rules

### Effective-Permission Layer Decides
- whether candidate grants survive the full set of current conditions
- whether policy denial or restriction overrides ordinary grants
- whether the target object and action family satisfy object-level prerequisites
- whether the actor should be allowed, denied, or routed to review for this exact action now

The output of this layer is the canonical effective-permission answer for the request.

---

## Evaluation Context Model

Every significant access evaluation must normalize an evaluation context.

### Required Context Components
At minimum, the evaluation context should support:

- actor identifier
- actor type
- requesting session identifier or service-principal identifier
- requested action identifier
- resolved scope category and scope identifier
- parent scope references where applicable
- membership status where applicable
- workspace/organization lifecycle state where applicable
- target object identifier and object family where applicable
- object owner scope reference where applicable
- candidate grants
- restriction / suspension / review markers
- entitlement posture where relevant
- policy version or policy reference
- correlation identifier
- request timestamp

### Context Rules
- missing required context must fail closed
- object owner scope must come from canonical object metadata, not caller assumption
- multiple conflicting scope facts must not auto-resolve to broader authority
- stale context older than policy-safe thresholds must not be used for sensitive mutations

---

## Canonical Evaluation Pipeline

FUZE access evaluation must follow an explicit canonical order. Internal optimizations may exist, but they must not violate the semantic order.

### 1. Actor and Runtime Validation
Confirm the actor and requesting session or service principal are valid enough for evaluation.

Required checks may include:
- canonical account existence
- session validity
- privileged-session posture where required
- account restriction or containment posture

If invalid, the request must be denied or restricted before grant evaluation continues.

### 2. Scope Resolution Validation
Confirm the intended scope is resolved canonically.

Required checks may include:
- workspace or organization existence
- scope ID validity
- parent scope validity
- object owner scope consistency
- client/server scope mismatch detection

If unresolved or mismatched, the request must fail closed.

### 3. Membership and Structural Relation Validation
Where workspace or organization context exists, confirm the actor’s structural relation is active enough for the request class.

Required checks may include:
- membership status
- owner/admin relation where required
- self vs other actor relation
- object creator/provenance relation where policy uses it

### 4. Candidate Grant Derivation
Determine the structurally applicable candidate grants.

Required checks may include:
- scope-bound role assignments
- permission bindings
- explicit inheritance
- direct narrow overrides where separately approved

### 5. Restriction and Lifecycle-State Filtering
Suppress candidate grants that are invalidated by stronger state.

Required checks may include:
- restricted or suspended account
- restricted or suspended membership
- restricted or suspended workspace
- removed or left membership
- expired or revoked grant
- review posture
- control-plane containment

### 6. Object and Action Preconditions
Evaluate object-sensitive and action-sensitive prerequisites.

Required checks may include:
- object existence
- object owner scope
- object state, status, or lock posture
- parent resource relationship
- immutable/finalized state
- action-specific preconditions such as recent-auth or dual approval requirement markers

### 7. Entitlement and Capability Coordination
For capabilities requiring commercial or policy eligibility, evaluate whether entitlement is present.

Required checks may include:
- workspace or account entitlement
- plan tier
- credits posture where relevant
- product enablement posture
- feature gate posture where relevant

### 8. Higher-Order Policy Evaluation
Apply security, governance, abuse, or control-plane rules that may still deny or escalate the action.

Required checks may include:
- governance-sensitive classification
- support/admin containment state
- risk-score or review posture
- action-specific approval requirements
- explicit policy deny conditions

### 9. Final Outcome Construction
Return one canonical effective-permission result and accompanying reason codes or policy references.

Representative outcomes:
- `allow`
- `deny`
- `restricted`
- `review_required`
- `entitlement_missing`
- `scope_unresolved`
- `policy_blocked`

---

## Effective Permission Semantics

Effective permission must remain a first-class semantic concept, not a loose synonym for “has role.”

### Allow
The actor may perform the action now under current policy and state.

### Deny
The actor may not perform the action now. Deny may result from missing grant, wrong scope, invalid object state, missing membership, or other disqualifying conditions.

### Restricted
A stronger platform restriction suppresses ordinary action regardless of ordinary grants.

### Review Required
The action is not allowed to proceed automatically and requires explicit review or higher-trust workflow.

### Policy Blocked
A governing rule forbids the action in the current posture even though candidate grants may exist.

### Entitlement Missing
The actor or scope lacks required commercial or capability eligibility.

### Scope Unresolved
The request did not produce one safe canonical scope for evaluation.

These outcomes must remain distinguishable for implementation, audit, and operational clarity.

---

## Precedence Rules

When multiple conditions apply, FUZE must preserve a stable precedence model.

### Highest-Priority Deny / Restriction Conditions
Examples include:
- invalid or contained session
- restricted account
- restricted privileged pathway
- unresolved or mismatched scope
- removed or suspended membership for a workspace-scoped action
- restricted or suspended workspace
- explicit governance or security block
- immutable object state preventing the action

### Middle-Layer Structural Conditions
Examples include:
- missing role
- missing permission
- invalid scope applicability
- expired assignment
- non-matching object ownership rule

### Commercial / Capability Conditions
Examples include:
- missing entitlement
- missing product enablement
- insufficient credits posture where capability policy requires it

### Precedence Rules
- stronger deny or restriction conditions must short-circuit lower-granularity allow conditions where appropriate
- product-local allow rules must never override platform-level deny
- object-level allow cannot override parent-scope restriction
- entitlement success cannot override missing grant
- candidate grant presence cannot override review-required policy

---

## Relationship to Scoped Authorization

Scoped authorization answers whether a grant structurally applies to a scope.

Effective permission answers whether that structurally applicable grant actually allows this exact action now.

### Required Separation Rules
- a valid scoped grant may still fail effective-permission evaluation
- an invalid scoped grant must not enter the candidate set
- object-sensitive rules may narrow a structurally valid grant
- restriction, entitlement, or policy may suppress a structurally valid grant
- effective-permission logic must not silently redefine scoped authorization categories

---

## Relationship to Membership and Workspace State

Many FUZE actions depend on membership and workspace posture.

### Required Rules
- workspace membership is necessary input for many workspace-scoped actions
- non-active membership must not yield ordinary effective permission
- workspace restriction or suspension may suppress otherwise matching grants
- owner continuity and owner-removal safeguards may influence evaluation outcomes for ownership-sensitive actions
- workspace lifecycle state may block actions such as invite, billing mutation, or product operations depending on policy

---

## Relationship to Entitlement and Capability Gating

Entitlement is an adjacent domain, but it is load-bearing at evaluation time for many capabilities.

### Required Rules
- entitlement checks must not be skipped for capabilities that require them
- entitlement does not replace grant-based permission
- a valid grant with missing entitlement may produce `entitlement_missing` or `deny`, depending on product/API posture
- product UI may choose softer messaging, but canonical backend evaluation must preserve the distinction

---

## Relationship to Product and Object-Level Rules

Products may define product-local objects and finer-grained rules, but they must plug into the shared evaluation model.

### Allowed
- product-local object-state inputs
- product namespaced action rules
- object-owner-scope checks
- product-local read optimization caches for low-risk use cases

### Not Allowed
- bypassing platform membership or restriction checks
- treating product object ownership as workspace ownership override without policy
- silently granting broader permissions because the actor created an object
- inventing alternate effective-permission semantics for shared platform concerns

---

## Functional Rules

### Sensitive Mutation Rule
Sensitive mutations must use fresh canonical evaluation context and must not rely solely on cached UI or client-side access summaries.

### Read vs Mutation Distinction Rule
Read access and mutation access may differ materially even within the same scope and object family.

### Self vs Other Rule
Actions affecting the actor’s own profile, settings, or linked methods must remain distinct from actions affecting other actors.

### Parent-Scope Guard Rule
Actions on child objects must still respect parent workspace, organization, or operational restrictions.

### Review Escalation Rule
Governance-sensitive, abuse-sensitive, or correction-sensitive actions may require explicit review even when structural grants are present.

### Service-to-Service Rule
Internal services acting on behalf of users must carry sufficient actor and scope context or use approved service-principal semantics. Headless internal power is disallowed unless explicitly granted and audited.

### Batch and Async Rule
Bulk or async actions must preserve per-item or per-target evaluation safety where mixed permission outcomes are possible.

---

## Permission / Access Considerations

This document is itself the core effective-permission spec, but several mandatory constraints must remain clear.

### Required Constraints
- sensitive actions must evaluate against fresh enough canonical state
- restriction and containment must suppress stale grants rapidly
- allow must be the result of explicit success, not the absence of an obvious error
- review-required is not a soft allow
- service-side enforcement is authoritative
- frontend permission hints are advisory only
- cross-scope fallback is forbidden

---

## API / Contract Implications

The platform should expose effective-permission behavior through explicit APIs and contracts.

### Authorization Evaluation APIs Should Support
- explicit evaluation requests for actor + action + scope + object
- batch evaluation with per-target results where safe
- machine-readable outcome codes
- policy and reason references where appropriate
- freshness and cacheability metadata
- control-plane review-required outcomes for sensitive actions

### API Rules
- mutation-capable APIs must enforce effective-permission server-side
- all evaluation and mutation routes should support correlation identifiers
- idempotency is required for mutation endpoints, not for read-only evaluation queries by default
- APIs should distinguish missing grant, restricted state, unresolved scope, missing entitlement, and review-required where doing so is safe
- public APIs may intentionally collapse some reasons to protect security posture, but internal canonical traces must preserve the richer distinctions

---

## Event / Async Implications

Effective-permission and access-evaluation systems should emit durable events where high-value for audit and operations.

Representative semantic events include:
- `authz.evaluation_completed`
- `authz.evaluation_denied`
- `authz.review_required`
- `authz.restriction_override_applied`
- `authz.entitlement_block_applied`
- `authz.policy_block_applied`

### Event Rules
- events must not leak unsafe details on public channels
- canonical events should be emitted after evaluation or after the resulting access mutation, depending on action class
- async consumers may use outcomes for observability, audit, or cache refresh, but may not redefine canonical permission truth
- high-volume low-risk reads need not emit one event per evaluation if alternate audit aggregation exists

---

## Data Model / Storage Implications

At minimum, the platform should support the following durable semantic structures.

### `effective_permission_audit`
Representative semantic fields:
- evaluation ID
- actor reference
- session or service-principal reference
- requested action
- scope reference
- target object reference where applicable
- candidate grant summary
- resulting outcome
- reason code(s)
- policy reference(s)
- timestamp
- correlation reference

### `access_restriction_snapshot`
Representative semantic fields:
- subject reference
- scope reference
- restriction type
- active marker
- reason code
- policy reference
- applied_at
- cleared_at where applicable

### `entitlement_evaluation_reference`
Representative semantic fields:
- subject reference
- capability reference
- entitlement result
- supporting plan/credits reference
- evaluated_at
- correlation reference

### `object_access_context`
Representative semantic fields:
- object reference
- object owner scope
- object state
- parent object reference where applicable
- finalized/locked markers
- last updated timestamp

### Data Rules
- durable audit surfaces must exist for high-impact evaluation outcomes
- derived UI permission summaries must not become write owners or sole truth for sensitive actions
- policy versions or references should be reconstructable for non-trivial outcomes
- evaluation caches must remain invalidation-aware and subordinate to canonical state

---

## Security / Risk / Abuse Controls

Effective permission is one of FUZE’s core security boundaries.

The platform must preserve:
- server-side authoritative enforcement
- fail-closed behavior on missing or ambiguous inputs
- restriction precedence over stale grants
- rapid suppression after containment, membership removal, or workspace suspension
- stronger handling for privileged, billing-sensitive, credits-sensitive, and governance-sensitive actions
- protection against confused-deputy behavior in internal service calls
- resistance to stale permission summaries being reused for sensitive mutations
- durable evidence for abuse analysis and incident review

---

## Audit / Traceability Requirements

FUZE must be able to determine:

- who requested the action
- through which session or service principal
- in which scope
- against which object or action family
- which candidate grants were considered
- which stronger restrictions or lifecycle states applied
- whether entitlement affected the final outcome
- why the final result was allow, deny, restricted, or review-required
- which policy version or rule family shaped the outcome
- whether a visible permission summary was canonical or derived

High-impact permission outcomes are part of the platform trust surface and must be reconstructable.

---

## Failure Handling / Edge Cases

### Valid Session, Missing Membership
Workspace-scoped actions must deny even when UI still shows the workspace.

### Valid Membership, Missing Permission
The actor remains a valid participant but the requested action must deny.

### Permission Present, Workspace Suspended
The suspension must win and ordinary action must not continue.

### Permission Present, Entitlement Missing
The action must deny or return `entitlement_missing` according to contract posture.

### Product Object Moved to Another Workspace
Object owner scope must be re-evaluated and stale cached permission conclusions must not survive silently.

### Bulk Operation Across Mixed Targets
The platform must not silently treat the whole batch as allowed if some targets fail evaluation.

### Privileged Session Lost Mid-Flow
The action must re-evaluate and fail or re-prompt where policy requires stronger runtime posture.

### Support / Admin Correction Action
The action may require `review_required` or privileged containment logic even when general support role exists.

### Governance-Sensitive Operation Requested by Ordinary Workspace Owner
Ordinary workspace power is insufficient. The request must deny or route to a governance-aware control path.

### Stale Feature Flag Shows Button
Frontend visibility must not be mistaken for allow. Server-side evaluation remains authoritative.

---

## Operational Considerations

Operational systems should support:
- observability for denies, review-required rates, stale-cache risk, and restriction overrides
- correlation across identity, session, membership, scoped grant, entitlement, and object-state changes
- safe caching only for low-risk read scenarios
- cache invalidation hooks on membership mutation, role mutation, workspace suspension, entitlement change, and session containment
- clear diagnostics for why high-impact requests failed without exposing unsafe detail to end users
- runbooks for stale permission incidents, mixed-batch failures, wrong-scope object requests, and policy regression analysis

The operational model must preserve the principle that final permission is a canonical evaluated result, not a frontend convenience.

---

## Migration / Compatibility / Supersession Considerations

- migrations must remove any implementation pattern where role presence alone is treated as final allow
- older implementations that imply login success equals feature access, or cached UI state equals final permission, are superseded within this scope
- compatibility layers may preserve legacy permission names temporarily, but final evaluation ordering and fail-closed behavior must remain intact
- products adopting finer-grained object rules must still attach to the shared evaluation model rather than building parallel authorization engines
- entitlement-aware evaluation should become stricter over time, not looser

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
- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `WORKSPACE_MEMBERSHIP_LIFECYCLE_SPEC.md`
- `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
- `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`

This specification directly governs or materially informs:

- `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- product integration specifications
- internal service authorization adapters
- control-plane workflow specifications

---

## Explicitly Deferred Items

The following are intentionally deferred to adjacent or downstream specifications:

- exact policy-engine implementation language
- exact database-level row/field enforcement strategy
- exact per-product object rule tables
- exact end-user explanation copy for each deny reason
- exact observability dashboards and alert thresholds
- exact governance approval flow implementation

These do not weaken the canonical effective-permission model established here.

---

## Final Normative Summary

FUZE effective permission is the final evaluated answer to a concrete access request. It is not the same thing as identity, session, scope, membership, role, permission string, or entitlement. Those are all inputs. The platform must evaluate them in explicit order, apply stronger restrictions and policy-deny conditions before weaker inferred allows, and return a canonical outcome that is deterministic, auditable, and fail-closed.

This document is the canonical FUZE rule set for turning structural authorization inputs into final action-level allow, deny, restricted, or review-required outcomes. Products may supply object-level facts and product-local rules, but they may not replace the shared evaluation model.
