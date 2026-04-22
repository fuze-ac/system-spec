# ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC

## Title
FUZE Access Evaluation and Effective Permission Specification

## Document Metadata

- Document Name: `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Version: 1.1.0
- Effective Date: 2026-04-21
- Last Updated: 2026-04-21
- Reviewed On: 2026-04-21
- Document Owner: FUZE Platform Authorization Architecture
- Approval Authority: FUZE Platform Architecture and Governance Authority
- Review Cadence: Quarterly or upon material change to identity/session posture, workspace or organization semantics, membership lifecycle, role and permission structure, scoped authorization rules, entitlement posture, audit traceability requirements, privileged correction pathways, or higher-order security/risk policy
- Governing Layer: Platform core / access evaluation and effective permission
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, security engineering, support operations, audit, governance, API design, platform operations, implementation-contract authors
- Primary Purpose: Define the canonical FUZE model for evaluating concrete access requests and producing final effective-permission outcomes from identity, session, scope, membership, grants, object state, restrictions, entitlement posture, and higher-order policy without collapsing those adjacent truth domains into one ambiguous permission layer
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
  - `WORKSPACE_MEMBERSHIP_LIFECYCLE_SPEC.md`
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
  - `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
  - `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
  - `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
  - `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
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
- Supersedes: Earlier or less explicit FUZE permission-evaluation, action-authorization, and access-decision writeups to the extent they conflict within this document’s scope
- Superseded By: Not yet known
- Related Decision Records: Not yet known
- Canonical Status Note: This document is the canonical FUZE specification for final access-evaluation ordering and effective-permission outcome semantics. Downstream APIs, services, products, workers, dashboards, and support tooling MUST NOT reinterpret effective permission in ways that conflict with this document.
- Implementation Status: Normative architecture baseline; downstream API, service, runtime, event, audit, and control-plane contracts must conform
- Approval Status: Drafted for refined-system inclusion; formal approval record not yet attached
- Change Summary:
  - strengthened separation between structural grants and final evaluated outcomes
  - made outcome precedence and fail-closed handling explicit across identity, session, scope, membership, restriction, object, entitlement, and policy layers
  - tightened cache freshness, degraded-mode, and batch-evaluation guardrails
  - expanded explainability, traceability, and privileged/remediation-aware decision requirements

## Purpose

This specification defines the canonical FUZE access-evaluation and effective-permission model.

Its purpose is to make explicit:

- how FUZE turns a concrete access request into one final effective-permission outcome
- how identity, session posture, scope resolution, membership state, role and permission structure, scoped grants, object conditions, entitlement posture, restriction posture, and higher-order policy interact
- how candidate structural authority differs from final action-level permission
- how evaluation outcomes remain deterministic, auditable, fail-closed, and safe under degraded conditions
- how products and internal services must consume effective-permission outcomes without inventing weaker product-local shortcuts
- how APIs, events, storage, audit records, and runtime enforcement must preserve the canonical meaning of `allow`, `deny`, `restricted`, `review_required`, `entitlement_missing`, `scope_unresolved`, and related outcomes

FUZE already defines that login is not authorization, workspace selection is not authority, membership is not final permission, and scoped grants are only structural inputs. A separate canonical layer is therefore required to define how those inputs become the final answer to a real request. That layer is access evaluation and effective permission.

## Scope

This specification governs:

- canonical access-evaluation ordering
- the semantic difference between candidate structural authority and final effective permission
- required evaluation context and normalization rules
- final-outcome semantics and reason-family expectations
- precedence rules across grants, restrictions, lifecycle state, object state, entitlement posture, and higher-order policy
- evaluation constraints for object-sensitive, action-sensitive, and workflow-sensitive operations
- access-evaluation caching, invalidation, freshness, and degraded-mode rules
- API, event, data-model, audit, security, and operational implications of effective-permission outcomes
- implementation-contract guardrails for products, APIs, workers, and control-plane tools that consume evaluated permission

This specification does not define in full depth:

- canonical account identity semantics
- session issuance, refresh, or revocation semantics
- workspace or organization lifecycle semantics
- membership invitation, activation, restriction, or removal mechanics
- role and permission catalog taxonomy in full depth
- scoped-grant storage and scope-resolution structure in full depth
- entitlement formulas, billing logic, or credits-ledger mechanics
- exact policy-engine DSL or database technology

Those concerns belong to adjacent or upstream specifications and MUST remain compatible with this document.

## Out of Scope

This specification is explicitly out of scope for:

- login and session-issuance mechanics
- invitation and membership-correction procedures
- exact role naming and permission namespace design
- exact pricing, subscription, invoicing, and payment-rail behavior
- exact database-specific row-level enforcement implementation
- exact frontend explanation copy for every denial or block
- exact break-glass staffing procedures or approval rosters

## Design Goals

1. Produce deterministic, explicit, and auditable access outcomes.
2. Preserve strict separation between structural authority and final action permission.
3. Prevent stale, partial, or ambiguous context from becoming unsafe authorization.
4. Fail closed when required inputs are missing, invalid, stale beyond policy, or contradictory.
5. Support one actor operating safely across many workspaces, products, and object hierarchies.
6. Support object-sensitive, workflow-sensitive, and governance-sensitive evaluation without creating shadow product truth.
7. Keep restriction, suspension, review posture, and containment stronger than stale or ordinary grants.
8. Support future-safe entitlement, commercial, governance, risk, and platform-control layers.
9. Make authorization reasoning explainable to internal reviewers and implementable by services.
10. Keep effective permission platform-owned even when products define product-local objects and rule modules.

## Non-Goals

This specification is not intended to:

- treat role assignment as the same thing as final permission
- treat membership as sufficient final authority
- treat entitlement as equivalent to permission
- treat workspace selection or UI context as canonical authority
- treat object ownership or creator status as a universal override
- define every product object rule exhaustively at this layer
- permit cached summaries to outrank fresh canonical evaluation for sensitive actions
- let products invent alternate final-permission semantics for shared platform concerns

## Core Principles

### 1. Effective-Permission Principle
A final permission outcome is not the same thing as a role, permission string, scope, membership, entitlement, or UI state. It is the evaluated result of those inputs under current canonical state and policy.

### 2. Structural-Grant / Final-Outcome Separation Principle
Roles and scoped grants define what authority may be considered. Access evaluation determines what is actually allowed now.

### 3. Fail-Closed Principle
If required identity, session, scope, membership, object, restriction, entitlement, or policy inputs are missing, invalid, stale beyond policy, or contradictory, the request MUST NOT default to allow.

### 4. Restriction-Precedence Principle
Restriction, suspension, containment, review posture, lifecycle-state blocks, and policy-deny conditions outrank ordinary grants.

### 5. Scope-First Principle
Evaluation MUST occur against the correct canonical scope. Wrong-scope evaluation is unsafe evaluation.

### 6. Fresh-State Principle
Sensitive access decisions MUST use sufficiently fresh canonical state and MUST NOT rely on stale caches, UI badges, or optimistic client assumptions.

### 7. Narrowest-Applicable-Rule Principle
Where multiple rules apply, evaluation SHOULD use the narrowest relevant scope and object facts while still respecting stronger parent-scope restrictions and broader policy-deny conditions.

### 8. Product-Consumption Principle
Products may supply product-local object facts and approved rule modules, but final effective-permission behavior remains subordinate to platform-owned identity, session, scope, membership, restriction, entitlement, and policy layers.

### 9. Review-Is-First-Class Principle
`review_required` is a canonical outcome, not an implementation failure or soft allow. High-risk ambiguity MUST route to explicit review rather than permissive fallback.

### 10. Auditability Principle
High-impact allow, deny, restricted, review-required, and policy-blocked outcomes MUST be reconstructable from canonical inputs and policy references.

## Canonical Definitions

### Access Evaluation
The platform process of determining whether a requested action is permitted under current identity, session, scope, membership, grant, object, entitlement, restriction, and policy conditions.

### Candidate Grant
A structurally applicable role-derived or permission-derived authority signal that survives scope applicability checks before final state and policy checks complete.

### Effective Permission
The final action-level authorization outcome after all relevant conditions are evaluated.

### Evaluation Context
The normalized set of inputs used to evaluate a request, including actor, runtime posture, scope, target, object owner scope, membership state, candidate grants, restriction posture, entitlement posture, policy references, and timestamps.

### Evaluation Outcome
The canonical result of access evaluation, such as:
- `allow`
- `deny`
- `restricted`
- `review_required`
- `entitlement_missing`
- `scope_unresolved`
- `policy_blocked`

Implementation naming may vary, but these semantic distinctions MUST remain representable.

### Policy Deny
A deny outcome caused by a governing rule even when candidate grants might otherwise suggest allow.

### Restriction Override
A stronger condition that suppresses candidate structural authority.

### Object Condition
A property of the target object, object family, or object owner scope that materially affects access evaluation.

### Parent-Scope Constraint
A broader workspace, organization, operational, or governance condition that constrains otherwise matching narrower grants or object-local allowances.

## Truth Class Taxonomy

Downstream implementations MUST preserve the following truth classes.

### 1. Canonical Identity Truth
The durable actor anchor represented by `account_id`.

### 2. Runtime Session Truth
Temporary authenticated runtime posture or approved service-principal posture. This is prerequisite runtime state, not permission truth.

### 3. Collaborative Scope Truth
Canonical workspace and organization records, hierarchy, and lifecycle state. These define where evaluation occurs, not whether the action is allowed.

### 4. Membership Truth
The durable structural relation between an account and collaborative scope. Membership is often necessary input, but not final permission.

### 5. Structural Authorization Truth
Role assignments, permission mappings, scope bindings, and candidate structural authority available after scope resolution.

### 6. Effective-Permission Truth
The final evaluated allow, deny, restricted, review-required, or analogous outcome owned by this domain.

### 7. Entitlement Truth
Commercial or policy eligibility for products and capability classes. Entitlement can suppress or block capability use but is not actor authority by itself.

### 8. Policy / Restriction Truth
Security, risk, containment, governance, lifecycle, and other higher-order rules that may suppress or escalate action.

### 9. Object / Runtime Context Truth
Canonical object owner scope, object state, parent relations, lock/finalization posture, and action-specific preconditions required for evaluation.

### 10. Derived Read-Model Truth
UI badges, support summaries, analytics projections, permission dashboards, and cached access summaries derived from canonical records.

### 11. Reporting / Public View Truth
Public, partner, or operator-facing summaries and reports that describe outcomes without becoming canonical mutation or evaluation owners.

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
- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `WORKSPACE_MEMBERSHIP_LIFECYCLE_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `SCOPED_AUTHORIZATION_MODEL_SPEC.md`

and above:

- `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- product integration specifications
- internal service authorization adapters
- control-plane workflow specifications

This document owns final evaluation structure and effective-permission semantics. It does not redefine identity, sessions, workspace truth, membership truth, role catalogs, scoped-grant structure, entitlement ownership, or audit ownership.

## System Boundaries

This document governs only the following platform-owned boundaries:

- canonical access-evaluation sequence
- structural distinction between candidate grants and final permission
- canonical effective-permission outcome semantics
- precedence rules among grants, restrictions, object conditions, entitlements, and policy
- stale-read, caching, and degraded-mode safety constraints
- canonical audit direction for evaluated outcomes
- service-facing API and event expectations for effective-permission results

It does not govern:

- identity creation or provider resolution
- session issuance and refresh
- workspace or organization creation in full depth
- membership invitation or removal workflows
- grant-binding structures in full depth
- commercial truth or plan truth
- exact product object schema design
- exact governance approval graph implementation

## Adjacent Boundaries

### Identity Domain
Owns canonical actor identity and account state. Access evaluation consumes identity truth but does not define it.

### Auth / Session Domain
Owns session validity, recent-auth posture, privileged-session posture, invalidation, and containment state. Access evaluation consumes this truth.

### Workspace / Organization Domain
Owns collaborative scope records and lifecycle state. Access evaluation consumes resolved scope and scope state.

### Membership Lifecycle Domain
Owns membership existence and lifecycle posture. Access evaluation uses membership as a prerequisite input for many workspace-scoped actions.

### Role / Permission Domain
Owns role and permission catalogs and baseline structural grants. Access evaluation uses these as candidate-input sources.

### Scoped Authorization Domain
Owns how grants bind to scope and whether a grant structurally applies to a scope. Access evaluation uses those bindings after scope resolution.

### Entitlement / Capability Domain
Owns commercial and policy eligibility. Access evaluation coordinates with this domain but must not collapse into it.

### Security / Risk Domain
May impose deny, restriction, review, containment, or escalation that overrides otherwise matching grants.

### Product Domains
May provide product-local object facts, namespaced rules, and product capability semantics, but MUST NOT replace platform-owned evaluation ordering or final effective-permission truth.

### Audit / Traceability Domain
Owns durable access traceability surfaces informed by evaluation lineage and outcomes.

### Admin Correction / Containment Domain
Owns privileged remediation workflows. Access evaluation consumes containment and reviewed-remediation posture but does not own those control flows.

## Conflict Resolution Rules

When inputs disagree, FUZE MUST resolve access-evaluation disagreements in the following order unless a higher-order policy explicitly overrides it:

1. canonical identity and actor validity
2. runtime session or approved service-principal validity
3. canonical scope resolution and object owner scope
4. collaborative scope lifecycle and membership posture
5. structural candidate-grant applicability
6. restrictions, suspension, containment, and lifecycle suppressors
7. object and action preconditions
8. entitlement and capability posture
9. higher-order policy deny, review, or governance escalation
10. final effective-permission outcome construction
11. derived summaries, caches, dashboards, and UI state

Additional rules:

- canonical object owner scope MUST outrank caller-supplied scope hints
- parent-scope restriction MUST outrank narrower object-local allow
- entitlement success MUST NOT override missing grant
- candidate grant presence MUST NOT override review-required policy
- ambiguous or conflicting context MUST resolve to deny, restricted, or review-required, never permissive fallback

## Default Decision Rules

When ambiguity exists and no narrower policy-specific rule is available, the platform MUST apply the following defaults:

- default to deny for unresolved, stale, or structurally incomplete context
- default to `scope_unresolved` where one safe canonical scope cannot be produced
- default to `restricted` where a stronger restriction or containment posture applies
- default to `review_required` for ambiguous high-risk or governance-sensitive pathways
- default to no cross-scope fallback
- default to server-side evaluation over client claims or cached hints
- default to fresh canonical reads for sensitive mutation and high-cost execution
- default to per-target outcomes in batch workflows where mixed results are possible
- default to preserving reason families and audit lineage even when public-facing messages are collapsed

## Roles / Actors / Entities

### Actor
The canonical FUZE account or approved internal service principal attempting an action.

### Requesting Session
The current runtime session, privileged session, or approved service-principal context attached to the actor.

### Target Scope
The canonical scope in which the action is being attempted.

### Target Object
The resource, object family, workflow, report, setting, or control-plane action being evaluated.

### Candidate Grant Set
The set of structurally relevant grants that survive scope applicability checks.

### Evaluator
The policy-aware service or module that computes effective permission.

### Policy Owner
The domain that owns a deny, review, restriction, or narrowing rule used during evaluation.

### Authorization Consumer
A product, API layer, worker, or internal service that requests or consumes evaluation outcomes.

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
- expose machine-readable outcome codes and explainability metadata
- request supporting context from identity, workspace, membership, grant, product, entitlement, and risk domains
- publish policy-reference metadata with outcomes
- support deterministic cacheable read models for low-risk scenarios under safety limits

### Effective-Permission Domain Must Not
- redefine upstream identity, session, workspace, membership, or grant ownership
- redefine commercial plan truth or entitlement ownership
- rely on product-local hidden state as the sole source for sensitive access decisions
- silently widen ambiguous, stale, or partial results into allow
- let derived permission badges or caches become the canonical answer for sensitive mutation

### Product Domains May
- request effective-permission decisions
- supply product-local object attributes and approved namespaced rule modules
- cache low-risk derived summaries subject to freshness and invalidation rules

### Product Domains Must Not
- treat derived permission badges as canonical for sensitive mutation
- bypass platform-owned evaluation ordering
- silently keep access alive after revocation, suspension, removal, containment, or restriction
- convert entitlement, subscription, feature-flag, or rollout state into local permission truth

## Authority / Decision Model

Access evaluation is the final structural stage before action execution.

### Upstream Layers Provide
- canonical actor identity
- valid session or approved service-principal posture
- resolved scope
- membership state where applicable
- valid grants bound to that scope
- object owner scope and object metadata where applicable
- restriction, suspension, containment, or review posture
- entitlement and capability posture where applicable
- relevant higher-order policy rules

### Effective-Permission Layer Decides
- whether candidate grants survive the full set of current conditions
- whether policy denial or restriction overrides ordinary grants
- whether the target object and action family satisfy object-level and workflow-level prerequisites
- whether the actor should be allowed, denied, restricted, or routed to review for this exact action now

The output of this layer is the canonical effective-permission answer for the request.

## Evaluation Context Model

Every significant access evaluation MUST normalize an evaluation context.

### Required Context Components
At minimum, the evaluation context SHOULD support:

- actor identifier
- actor type
- session identifier or service-principal identifier
- requested action identifier
- resolved scope category and scope identifier
- parent scope references where applicable
- membership status where applicable
- workspace or organization lifecycle state where applicable
- target object identifier and object family where applicable
- object owner scope reference where applicable
- candidate grants
- restriction, suspension, containment, or review markers
- entitlement posture where relevant
- policy version or policy reference
- correlation identifier
- request timestamp
- freshness metadata for any cached or derived inputs used

### Context Rules
- missing required context MUST fail closed
- object owner scope MUST come from canonical object metadata, not caller assumption
- conflicting scope facts MUST NOT auto-resolve to broader authority
- stale context beyond policy-safe thresholds MUST NOT be used for sensitive mutations or high-cost execution
- explainability metadata MUST preserve enough structure for internal reconstruction even if end-user messaging is collapsed

## Canonical Evaluation Pipeline

FUZE access evaluation MUST follow an explicit canonical order. Internal optimizations may exist, but they MUST NOT violate the semantic order.

### 1. Actor and Runtime Validation
Confirm the actor and requesting session or service principal are valid enough for evaluation.

Representative checks include:
- canonical account existence
- session validity
- privileged-session posture where required
- account restriction or containment posture

If invalid, the request MUST be denied or restricted before grant evaluation continues.

### 2. Scope Resolution Validation
Confirm the intended scope is resolved canonically.

Representative checks include:
- workspace or organization existence
- scope ID validity
- parent scope validity
- object owner scope consistency
- client/server scope mismatch detection

If unresolved or mismatched, the request MUST fail closed.

### 3. Membership and Structural Relation Validation
Where workspace or organization context exists, confirm the actor’s structural relation is active enough for the request class.

Representative checks include:
- membership status
- owner/admin relation where required
- self vs other actor relation where policy distinguishes them
- object provenance relation where policy uses it

### 4. Candidate Grant Derivation
Determine the structurally applicable candidate grants.

Representative checks include:
- scope-bound role assignments
- permission mappings
- explicit inheritance
- direct narrow overrides where separately approved

### 5. Restriction and Lifecycle-State Filtering
Suppress candidate grants that are invalidated by stronger state.

Representative checks include:
- restricted or suspended account
- restricted or suspended membership
- restricted or suspended workspace
- removed or left membership
- expired or revoked grant
- review posture
- control-plane containment

### 6. Object and Action Preconditions
Evaluate object-sensitive and action-sensitive prerequisites.

Representative checks include:
- object existence
- object owner scope
- object state, status, or lock posture
- parent resource relationship
- finalized or immutable state
- action-specific preconditions such as recent-auth, dual approval, or workflow guard markers

### 7. Entitlement and Capability Coordination
For capabilities requiring commercial or policy eligibility, evaluate whether entitlement is present and usable.

Representative checks include:
- workspace or account entitlement
- plan or tier posture
- credits-sensitive posture where relevant
- product enablement posture
- feature or capability gate posture where relevant

### 8. Higher-Order Policy Evaluation
Apply security, governance, abuse, or control-plane rules that may still deny or escalate the action.

Representative checks include:
- governance-sensitive classification
- support/admin containment state
- risk score or review posture
- action-specific approval requirements
- explicit policy deny conditions

### 9. Final Outcome Construction
Return one canonical effective-permission result and accompanying reason families or policy references.

Representative outcomes:
- `allow`
- `deny`
- `restricted`
- `review_required`
- `entitlement_missing`
- `scope_unresolved`
- `policy_blocked`

## Effective Permission Semantics

Effective permission MUST remain a first-class semantic concept, not a loose synonym for “has role.”

### Allow
The actor may perform the action now under current policy and state.

### Deny
The actor may not perform the action now. Deny may result from missing grant, wrong scope, invalid object state, missing membership, or other disqualifying conditions.

### Restricted
A stronger platform restriction suppresses ordinary action regardless of otherwise matching grants.

### Review Required
The action is not allowed to proceed automatically and requires explicit review or a higher-trust workflow.

### Policy Blocked
A governing rule forbids the action in the current posture even though candidate grants may exist.

### Entitlement Missing
The actor or evaluation subject lacks required commercial or capability eligibility.

### Scope Unresolved
The request did not produce one safe canonical scope for evaluation.

These outcomes MUST remain distinguishable for implementation, audit, and operational clarity.

## Precedence Rules

When multiple conditions apply, FUZE MUST preserve a stable precedence model.

### Highest-Priority Deny / Restriction Conditions
Representative examples include:
- invalid or contained session
- restricted account
- restricted privileged pathway
- unresolved or mismatched scope
- removed or suspended membership for a workspace-scoped action
- restricted or suspended workspace
- explicit governance or security block
- immutable object state preventing the action

### Middle-Layer Structural Conditions
Representative examples include:
- missing role
- missing permission
- invalid scope applicability
- expired assignment
- non-matching object ownership rule

### Commercial / Capability Conditions
Representative examples include:
- missing entitlement
- missing product enablement
- insufficient credits posture where capability policy requires it

### Precedence Rules
- stronger deny or restriction conditions MUST short-circuit lower-granularity allow conditions where appropriate
- product-local allow rules MUST NEVER override platform-level deny
- object-level allow MUST NOT override parent-scope restriction
- entitlement success MUST NOT override missing grant
- candidate grant presence MUST NOT override review-required policy
- `review_required` MUST NOT be treated as soft allow in runtime execution paths

## Relationship to Scoped Authorization

Scoped authorization answers whether a grant structurally applies to a scope. Effective permission answers whether that structurally applicable grant actually allows this exact action now.

### Required Separation Rules
- a valid scoped grant may still fail effective-permission evaluation
- an invalid scoped grant MUST NOT enter the candidate set
- object-sensitive rules may narrow a structurally valid grant
- restriction, entitlement, or policy may suppress a structurally valid grant
- effective-permission logic MUST NOT silently redefine scoped-authorization categories

## Relationship to Membership and Workspace State

Many FUZE actions depend on membership and workspace posture.

### Required Rules
- workspace membership is necessary input for many workspace-scoped actions
- non-active membership MUST NOT yield ordinary effective permission
- workspace restriction or suspension may suppress otherwise matching grants
- owner continuity and owner-removal safeguards may influence evaluation outcomes for ownership-sensitive actions
- workspace lifecycle state may block invites, billing mutation, product operations, or exports depending on policy

## Relationship to Entitlement and Capability Gating

Entitlement is an adjacent domain, but it is load-bearing at evaluation time for many capabilities.

### Required Rules
- entitlement checks MUST NOT be skipped for capabilities that require them
- entitlement does not replace grant-based permission
- a valid grant with missing entitlement may produce `entitlement_missing`, `deny`, or `restricted` depending on product/API posture and policy
- product UI may choose softer messaging, but canonical backend evaluation MUST preserve the distinction

## Relationship to Product and Object-Level Rules

Products may define product-local objects and finer-grained rules, but they MUST plug into the shared evaluation model.

### Allowed
- product-local object-state inputs
- product-namespaced action rules
- object-owner-scope checks
- product-local read optimizations for low-risk use cases

### Not Allowed
- bypassing platform membership or restriction checks
- treating product object ownership as workspace ownership override without policy
- silently granting broader permissions because the actor created an object
- inventing alternate effective-permission semantics for shared platform concerns

## Invariants

The following invariants are mandatory:

1. final effective permission is not identical to candidate grant presence
2. evaluation occurs against one canonical scope
3. unresolved or mismatched scope never silently widens to allow
4. non-active membership cannot support ordinary workspace-scoped effective permission
5. restriction, containment, and stronger lifecycle-state posture outrank stale grants
6. entitlement success never overrides missing permission
7. object-local allow never overrides stronger parent-scope block
8. review-required is not executable allow
9. derived permission summaries remain derived and subordinate
10. sensitive mutation requires fresh enough canonical context

## Functional Rules

### Sensitive Mutation Rule
Sensitive mutations MUST use fresh canonical evaluation context and MUST NOT rely solely on cached UI or client-side access summaries.

### Read vs Mutation Distinction Rule
Read access and mutation access may differ materially even within the same scope and object family.

### Self vs Other Rule
Actions affecting the actor’s own profile, settings, or linked methods MUST remain distinct from actions affecting other actors.

### Parent-Scope Guard Rule
Actions on child objects MUST still respect parent workspace, organization, or operational restrictions.

### Review Escalation Rule
Governance-sensitive, abuse-sensitive, correction-sensitive, or trace-deficient actions MAY require explicit review even when structural grants are present.

### Service-to-Service Rule
Internal services acting on behalf of users MUST carry sufficient actor and scope context or use approved service-principal semantics. Headless internal power is forbidden unless explicitly granted and audited.

### Batch and Async Rule
Bulk or async actions MUST preserve per-item or per-target evaluation safety where mixed permission outcomes are possible. Aggregated success MUST NOT hide denied or restricted targets.

## Permission / Access Considerations

This document is itself the core effective-permission specification, but several mandatory constraints remain explicit.

### Required Constraints
- sensitive actions MUST evaluate against fresh enough canonical state
- restriction and containment MUST suppress stale grants rapidly
- allow MUST be the result of explicit success, not the absence of an obvious error
- review-required is not a soft allow
- service-side enforcement is authoritative
- frontend permission hints are advisory only
- cross-scope fallback is forbidden

## Entitlement Considerations

- entitlement posture may attach to account, workspace, organization, or another approved subject, but it is not actor authority truth
- effective-permission evaluation MUST consume entitlement posture for gated capabilities
- entitlement-derived denial or degradation MUST remain distinguishable internally from missing permission
- entitlement caches MUST remain subordinate to canonical eligibility state and invalidation rules

## API / Contract Implications

The platform SHOULD expose effective-permission behavior through explicit APIs and contracts.

### Authorization Evaluation APIs SHOULD Support
- explicit evaluation requests for actor + action + scope + object
- batch evaluation with per-target results where safe
- machine-readable outcome codes
- policy and reason references where appropriate
- freshness and cacheability metadata
- review-required outcomes for sensitive actions

### API Rules
- mutation-capable APIs MUST enforce effective permission server-side
- evaluation and mutation routes MUST support correlation identifiers
- idempotency is required for mutation endpoints, not read-only evaluation queries by default
- APIs SHOULD distinguish missing grant, restricted state, unresolved scope, missing entitlement, and review-required where doing so is safe
- public APIs MAY intentionally collapse some reasons to protect security posture, but internal canonical traces MUST preserve richer distinctions

## Event / Async Implications

Effective-permission and access-evaluation systems SHOULD emit durable events where high value for audit and operations.

Representative semantic events include:
- `authz.evaluation_completed`
- `authz.evaluation_denied`
- `authz.review_required`
- `authz.restriction_override_applied`
- `authz.entitlement_block_applied`
- `authz.policy_block_applied`

### Event Rules
- events MUST NOT leak unsafe detail on public channels
- canonical events SHOULD be emitted after evaluation or after the resulting access mutation, depending on action class
- async consumers MAY use outcomes for observability, audit, or cache refresh, but MUST NOT redefine canonical permission truth
- high-volume low-risk reads need not emit one event per evaluation if alternate audit aggregation exists and policy permits it
- retries and async follow-up MUST preserve causality linkage to the initiating evaluation

## Data Model / Storage Implications

At minimum, the platform SHOULD support the following durable semantic structures.

### `effective_permission_audit`
Representative semantic fields:
- evaluation_id
- actor_reference
- session_or_service_principal_reference
- requested_action
- scope_reference
- target_object_reference where applicable
- candidate_grant_summary
- resulting_outcome
- reason_codes
- policy_references
- timestamp
- correlation_reference

### `access_restriction_snapshot`
Representative semantic fields:
- subject_reference
- scope_reference
- restriction_type
- active_marker
- reason_code
- policy_reference
- applied_at
- cleared_at where applicable

### `entitlement_evaluation_reference`
Representative semantic fields:
- subject_reference
- capability_reference
- entitlement_result
- supporting_plan_or_credits_reference
- evaluated_at
- correlation_reference

### `object_access_context`
Representative semantic fields:
- object_reference
- object_owner_scope
- object_state
- parent_object_reference where applicable
- finalized_or_locked_markers
- last_updated_timestamp

### Data Rules
- durable audit surfaces MUST exist for high-impact evaluation outcomes
- derived UI permission summaries MUST NOT become write owners or sole truth for sensitive actions
- policy versions or references SHOULD be reconstructable for non-trivial outcomes
- evaluation caches MUST remain invalidation-aware and subordinate to canonical state
- correction or containment events MUST link back to prior evaluation lineage rather than erasing it

## Read Model / Projection / Reporting Rules

- derived permission summaries MAY support UX and low-risk read paths, but they MUST remain explicitly derived
- low-risk read models MAY cache allowability summaries within bounded freshness windows
- sensitive mutation pathways MUST bypass or revalidate derived summaries against canonical state
- reports and dashboards MAY aggregate effective-permission outcomes, but they MUST remain traceable to canonical evaluation lineage
- public or end-user-facing explanations MAY collapse detail, but internal protected records MUST preserve fuller reason families

## Security / Risk / Abuse Controls

Effective permission is one of FUZE’s core security boundaries.

The platform MUST preserve:
- server-side authoritative enforcement
- fail-closed behavior on missing, stale, contradictory, or ambiguous inputs
- restriction precedence over stale grants
- rapid suppression after containment, membership removal, workspace suspension, grant revocation, or entitlement suspension
- stronger handling for privileged, billing-sensitive, credits-sensitive, governance-sensitive, and admin-sensitive actions
- protection against confused-deputy behavior in internal service calls
- resistance to stale permission summaries being reused for sensitive mutations
- durable evidence for abuse analysis, incident review, and user-support reconstruction

## Boundary Violation Detection / Non-Canonical Patterns

The following patterns are non-canonical and forbidden:

- granting access because the UI still shows a badge
- treating membership as final permission
- treating entitlement as permission
- allowing cross-scope fallback when object owner scope does not match requested scope
- allowing product-local creator status to bypass platform restriction or workspace admin checks
- treating `review_required` as executable success
- using stale cached authorization summaries for high-impact mutation
- omitting reason families or policy references for high-impact denied, restricted, or reviewed outcomes

Implementations SHOULD detect and surface these conditions through diagnostics, audit, and operational alerts.

## Audit / Traceability Requirements

FUZE MUST be able to determine:

- who requested the action
- through which session or service principal
- in which scope
- against which object or action family
- which candidate grants were considered
- which stronger restrictions, lifecycle states, or policy conditions suppressed those grants
- whether entitlement posture contributed to the final result
- what final effective-permission outcome was returned
- which correlation, policy, and causality references explain the result

High-impact outcomes MUST remain reconstructable even when user-facing explanations are intentionally simplified.

## Failure Handling / Edge Cases

### Valid Session, No Resolved Scope
The request MUST fail closed with `scope_unresolved` or equivalent.

### Workspace Selected, Membership Missing
The selected workspace may remain visible in UI state, but ordinary workspace-scoped action MUST be denied or restricted.

### Product Object Belongs to Different Workspace Than Requested
Canonical object owner scope MUST win. The request MUST fail or explicitly reroute; silent fallback is forbidden.

### Candidate Grant Exists, Workspace Suspended
The stronger workspace lifecycle restriction suppresses the candidate grant. The outcome MUST NOT be `allow`.

### Candidate Grant Exists, Entitlement Missing
The outcome MUST remain distinguishable from missing grant and MUST NOT be collapsed into allow.

### Ambiguous High-Risk Action
The evaluator MUST prefer `review_required`, `restricted`, or explicit deny over permissive fallback.

### Batch Operation With Mixed Results
The platform MUST preserve per-target outcomes rather than flattening them into one broad success signal.

### Async Worker Acting on Behalf of User
The worker MUST carry attributable actor and scope context or operate under approved service-principal semantics. Otherwise the action is non-compliant.

## Operational Considerations

Operational systems SHOULD support:
- diagnostics for scope mismatch, stale context, and unresolved-scope failures
- observability for repeated denied/restricted/reviewed outcomes by action family
- tracing for evaluation latency and stale-context fallback prevention
- control-plane tools that can inspect protected reason families without becoming alternate truth owners
- cache invalidation and freshness signaling when membership, grants, restrictions, or entitlement posture changes
- runbooks for permission-badge drift, wrong-scope product requests, stale grant suppression, and mixed-outcome batch handling

## Migration / Compatibility / Supersession Considerations

- older implementations that equate role presence, workspace membership, workspace selection, or product-local admin state with final permission are superseded within this scope
- compatibility layers MAY preserve legacy response shapes temporarily, but they MUST preserve canonical internal outcome distinctions
- migration MUST eliminate unsafe reliance on stale UI permission summaries for sensitive mutation
- products with local object-rule systems MUST integrate those rules beneath the shared evaluation pipeline rather than replacing it
- legacy “boolean allow only” interfaces SHOULD migrate toward richer internal outcome semantics even if public surfaces remain simplified

## Implementation-Contract Guardrails

Downstream implementations MUST preserve all of the following:

1. final effective permission remains distinct from structural candidate grants
2. server-side evaluation remains authoritative for protected actions
3. identity, session, scope, membership, entitlement, and policy remain separate truth classes
4. stale or ambiguous context never silently widens to allow
5. review-required remains first-class and non-executable without the required workflow
6. parent-scope and restriction precedence are preserved
7. entitlement success never substitutes for permission
8. derived summaries remain derived and regenerable
9. degraded runtime conditions MUST NOT cause hidden semantic downgrades
10. idempotency and replay safety MUST be preserved for mutation workflows that depend on evaluation results
11. traceability and reason capture MUST remain explicit for high-impact outcomes
12. downstream teams MUST NOT optimize away outcome distinctions, freshness metadata, or policy references where those protect security, auditability, or financial integrity

## Downstream Execution Staging

This specification implies the following preferred downstream implementation order:

1. stabilize identity and session posture inputs
2. stabilize workspace, membership, and scope-resolution inputs
3. stabilize role, permission, and scoped-grant inputs
4. implement canonical evaluation pipeline and outcome semantics
5. integrate object-sensitive rules and product adapters
6. integrate entitlement and capability gating
7. integrate privileged correction, containment, and review pathways
8. integrate audit, observability, dashboards, and safe derived views

## Required Downstream Specs / Contract Layers

This specification directly requires compatible downstream refinement and implementation-contract work in:

- `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- product integration specifications
- internal service authorization adapters
- control-plane workflow specifications
- audit and observability specifications

## Canonical Examples / Anti-Examples

### Canonical Example 1 — Workspace Admin, Suspended Workspace
An actor has a structurally valid workspace-admin grant. The workspace is suspended. The final outcome is not `allow`; the stronger workspace restriction suppresses the grant.

### Canonical Example 2 — Valid Grant, Missing Premium Capability Entitlement
An actor has the correct structural authority for a premium export action. The workspace lacks the required capability entitlement. The evaluator returns `entitlement_missing` or equivalent bounded denial semantics, not `allow`.

### Canonical Example 3 — Product Object in Wrong Scope
An actor requests mutation against an object they can manage in workspace A, but the target object is owned by workspace B. The evaluator uses canonical object owner scope and denies or routes appropriately. It does not silently fall back.

### Canonical Example 4 — Mixed Batch Outcomes
A user bulk-edits ten objects. Eight targets are allowed, one is restricted, and one requires review. The system preserves per-target outcomes instead of flattening everything into success.

### Anti-Example 1 — UI Badge As Permission
A frontend shows “Admin” from a stale cached summary, and the backend accepts a sensitive mutation without fresh evaluation. This is forbidden.

### Anti-Example 2 — Membership Equals Final Permission
A user is an active workspace member and is therefore allowed to perform every workspace action. This is forbidden.

### Anti-Example 3 — Entitlement Equals Permission
A workspace is on a paid plan and therefore any member may perform billing-sensitive or admin-sensitive actions. This is forbidden.

### Anti-Example 4 — Review Required Treated As Allow
A governance-sensitive action returns `review_required`, but the product executes the action anyway and opens a review ticket afterward. This is forbidden.

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
- `WORKSPACE_MEMBERSHIP_LIFECYCLE_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
- `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
- `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`

This specification directly governs or materially informs:

- `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- product integration specifications
- internal service authorization adapters
- control-plane workflow specifications
- audit and observability specifications

## Explicitly Deferred Items

The following are intentionally deferred to adjacent or downstream specifications:

- exact policy-engine implementation language
- exact product object-rule tables
- exact numeric freshness thresholds by action class
- exact public error wording for each denial or review case
- exact storage technology for evaluation evidence
- exact observability dashboard design
- exact reviewer routing workflow for every review-required pathway

These do not weaken the canonical effective-permission model established here.

## Final Normative Summary

FUZE effective permission is the canonical final authorization answer for a concrete request. It is downstream of identity, session, scope, membership, structural grants, object conditions, entitlement posture, restrictions, and higher-order policy. It is not equivalent to role presence, membership, entitlement, or product UI state.

Every protected action MUST be evaluated against one canonical scope and sufficiently fresh canonical context. Restriction, containment, non-active membership, parent-scope block, object-state block, entitlement deficiency, and higher-order policy MAY suppress structurally valid grants. Products, APIs, workers, and dashboards MUST consume this model rather than replace it with local shortcuts.

This document is the canonical FUZE rule set for final access evaluation and effective-permission semantics.

## Quality Gate Checklist

- [x] Canonical owner is explicit for every material truth family
- [x] Mutation boundaries are explicit
- [x] Adjacent boundaries are explicit
- [x] Truth classes are explicit
- [x] Conflict-resolution rules are explicit
- [x] Default decision rules are explicit
- [x] Non-canonical patterns are clearly called out
- [x] Operator and privileged-review/correction impacts are bounded and auditable
- [x] Read-model, cache, and reporting rules are explicit
- [x] Failure and degraded-mode behaviors are explicit
- [x] Downstream implementation guardrails are explicit
- [x] Dependencies and downstream impacts are explicit
- [x] Non-goals and deferred items are explicit
- [x] The document is strong enough to support downstream implementation-contract work without contradictory semantics
