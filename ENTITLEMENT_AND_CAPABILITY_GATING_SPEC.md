# ENTITLEMENT_AND_CAPABILITY_GATING_SPEC

## Title
FUZE Entitlement and Capability Gating Specification

## Document Metadata

- Document Name: `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Version: 1.1.0
- Effective Date: 2026-04-21
- Last Updated: 2026-04-21
- Reviewed On: 2026-04-21
- Document Owner: FUZE Platform Entitlement, Commerce, and Capability Architecture
- Approval Authority: FUZE Platform Architecture and Governance Authority
- Review Cadence: Quarterly or upon material change to workspace or organization semantics, authorization ordering, effective-permission evaluation, subscriptions, pricing, credits posture, rollout controls, audit requirements, or product-boundary rules
- Governing Layer: Platform core / entitlement and capability gating
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, commerce and billing, credits and ledger systems, security engineering, support operations, audit, governance, API design, platform operations, data engineering, implementation-contract authors
- Primary Purpose: Define the canonical FUZE model for how commercial eligibility, workspace- or account-attached product enablement, policy-based capability availability, credits-sensitive gating, rollout posture, and operational restriction posture determine whether a product, feature, or action class is eligible to be used without collapsing entitlement truth into identity, session, membership, authorization, effective permission, billing truth, or product-local UI state
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
  - `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
  - `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
  - `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
  - `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
  - `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md`
  - `PLATFORM_CREDITS_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - `AUTH_IDENTITY_API_SPEC.md`
  - `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
  - `WORKSPACE_ORGANIZATION_API_SPEC.md`
  - `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- Primary Downstream Dependents:
  - `PLATFORM_CREDITS_SPEC.md`
  - `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
  - `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
  - `PAYMENT_RAILS_INTEGRATION_SPEC.md`
  - `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
  - `WORKSPACE_ORGANIZATION_API_SPEC.md`
  - `ROLE_PERMISSION_ACCESS_API_SPEC.md`
  - product integration specifications
  - entitlement-aware API and worker enforcement
  - audit, reporting, support/control-plane, and reconciliation workflows
- Supersedes: Earlier or less explicit FUZE capability-access, plan-gating, product-enablement, and eligibility writeups to the extent they conflict within this document’s scope
- Superseded By: Not yet known
- Related Decision Records: Not yet known
- Canonical Status Note: This document is the canonical FUZE specification for entitlement and capability gating semantics. Downstream APIs, products, workers, dashboards, support tooling, rollout systems, pricing adapters, and credits-aware consumers MUST NOT reinterpret plan names, UI flags, billing status, credits balance, or product-local toggles as substitutes for the canonical entitlement posture defined here.
- Implementation Status: Normative architecture baseline; downstream API, service, runtime, event, audit, and control-plane contracts must conform
- Approval Status: Drafted for refined-system inclusion; formal approval record not yet attached
- Change Summary:
  - hardened the separation between eligibility truth and actor authority
  - clarified the subject model, capability taxonomy, and the distinction between product admission, plan posture, capability class, usage posture, and policy hold
  - strengthened failure-mode and freshness rules so sensitive and cost-bearing capability use cannot rely on stale or ambiguous derived views
  - expanded implementation-contract guardrails around credits-aware gating, rollout posture, exception grants, reason codes, and subject-correct evaluation

## Purpose

This specification defines the canonical FUZE entitlement and capability gating model.

Its purpose is to make explicit:

- how FUZE determines whether an account, workspace, organization, or other approved subject is eligible to access a product, feature, capacity band, automation right, or other gated capability
- how entitlement differs from identity, authentication, session validity, membership, role assignment, permission assignment, effective permission, billing truth, invoice truth, payment-rail truth, and credits-ledger truth
- how product enablement, plan posture, capability narrowing, credits-sensitive usage posture, rollout posture, operational restriction posture, and policy-based eligibility interact without being collapsed into one ambiguous switch
- how downstream access evaluation consumes entitlement posture as an input rather than redefining entitlement ownership
- how products, APIs, internal services, workers, and support/control-plane tooling must consume canonical entitlement truth without inventing hidden product-local gating systems for shared platform behavior
- how canonical data models, APIs, events, audit lineage, and operational controls must represent eligibility, degradation, suspension, restoration, policy hold, and exception posture

FUZE is platform-first and multi-product. In that environment, paid status is not authority, membership is not eligibility, credits balance is not permission, feature flags are not canonical commercial truth, and product-local toggles must not become shadow entitlement systems. A dedicated entitlement layer is therefore required so that commercial and policy eligibility remains explicit, durable, auditable, and safely consumed by the rest of the platform.

## Scope

This specification governs:

- canonical entitlement semantics for products, features, and gated capability classes
- canonical distinction between entitlement truth and adjacent identity, session, workspace, authorization, effective-permission, billing, credits, rollout, and policy domains
- approved subject classes for entitlement attachment, such as account, workspace, organization, or other explicitly approved platform subjects
- canonical states for entitlement activation, restriction, suspension, expiry, revocation, closure, exhaustion-sensitive posture, and policy hold
- the relationship among product enablement, plan/tier posture, credits-aware gating posture, usage ceiling posture, operational restrictions, rollout posture, and governance-sensitive constraints
- requirements for how effective-permission evaluation consumes entitlement posture
- API, event, data-model, audit, security, and operational implications of entitlement and capability truth
- migration constraints that prevent product-local gating drift from becoming platform truth

This specification does not define in full depth:

- canonical account identity semantics
- session issuance, refresh, or invalidation mechanics
- workspace lifecycle semantics
- exact role-to-permission mappings
- exact invoice formulas, tax rules, subscription billing math, or payment-processor behavior
- exact credits-ledger double-entry mechanics
- exact pricing tables for every plan and capability
- exact experimentation, A/B rollout, or feature-flag engine implementation
- exact user-facing copy for every entitlement failure mode

Those concerns are governed by adjacent identity, workspace, authorization, commerce, credits, API, and operational specifications and MUST remain compatible with this document.

## Out of Scope

This specification is explicitly out of scope for:

- proving who an actor is
- proving whether a session is valid
- deciding whether an actor has role-based authority to execute an action
- defining final ledger truth for charges, settlement, credits accrual, or refunds
- defining product-local UI-only visibility hints that do not claim canonical eligibility meaning
- defining every numeric quota, tier limit, or rollout percentage at this layer
- defining every support-staff workflow in full depth
- defining exact legal contract language for every subscription or enterprise arrangement

## Design Goals

1. Preserve strict separation between eligibility and authority.
2. Make product and feature enablement explicit, durable, and auditable.
3. Support both account-scoped and workspace-scoped commercial models without conceptual drift.
4. Support multiple products and multiple capability classes with one shared eligibility model.
5. Keep credits-aware gating, plan posture, and policy holds coherent without collapsing them into billing or ledger truth.
6. Support deterministic backend enforcement and safe degradation when eligibility changes.
7. Prevent product-local shadow entitlement systems from becoming hidden platform truth.
8. Support future-safe rollout, partner, enterprise, governance-sensitive, and chain-aware capability models.
9. Preserve clean API and event contracts for internal services and products.
10. Make entitlement failures explainable to internal operators and safe to surface externally in bounded form.

## Non-Goals

This specification is not intended to:

- treat paid status as the same thing as permission
- treat workspace membership as sufficient product eligibility
- treat credits balance alone as complete capability truth
- treat feature flags or rollout assignments as canonical entitlement records
- let products mint independent commercial eligibility systems for shared platform capabilities
- let support tools bypass canonical entitlement truth through hidden overrides
- let rollout posture, experiments, or UI bundles silently redefine commercial truth
- make every capability available merely because a subject is attached to a plan

## Core Principles

### 1. Eligibility-Is-Not-Authority Principle
Entitlement determines whether a subject is eligible for a product or capability class. It does not determine whether a specific actor is authorized to perform a specific action. Role and permission evaluation remain distinct and downstream.

### 2. Commercial-Truth / Capability-Truth Separation Principle
Commercial truth may justify or constrain entitlement, but invoice state, payment event state, subscription transport state, and ledger state are not themselves canonical entitlement records. Entitlement must be explicitly represented.

### 3. Scope-Attached Entitlement Principle
Every entitlement must attach to an explicit canonical subject such as an account, workspace, organization, or other approved scope-bearing subject. Contextless entitlement is forbidden.

### 4. Product-Enablement / Capability-Narrowing Principle
A broader product entitlement does not automatically imply every sensitive or premium capability inside that product. Capability classes may be narrower than product admission.

### 5. Platform-Owned Gating Principle
Entitlement and capability gating for shared platform behavior is platform-owned. Products may define product-local capability classes, but they MUST do so through approved namespaces and platform-consumable contracts.

### 6. Fail-Closed Eligibility Principle
If entitlement posture for a gated capability cannot be determined safely, the platform MUST NOT default to enablement for sensitive or cost-bearing behavior.

### 7. Freshness and Revocation Principle
High-impact eligibility checks must respect fresh enough canonical state so that suspension, exhaustion, downgrade, revocation, or policy hold can take effect without unsafe lag.

### 8. Derived-View-Is-Not-Truth Principle
UI badges, cached feature summaries, analytics exports, rollout dashboards, and product bundles are derived views. They do not replace canonical entitlement truth.

### 9. Credits-Aware but Not Credits-Collapsed Principle
Credits posture may contribute to capability eligibility where policy requires, but credits-ledger truth remains downstream and separate from the entitlement domain.

### 10. Auditability Principle
Entitlement creation, activation, suspension, downgrade, revocation, exhaustion-sensitive state changes, exception grants, and policy holds must be reconstructable.

## Canonical Definitions

### Entitlement
A durable canonical eligibility record or evaluated eligibility posture indicating that a defined subject may access a product, capability class, or usage posture subject to downstream authorization, policy, and runtime conditions.

### Capability Gate
A canonical rule or evaluated posture that determines whether a specific product, feature, action family, automation class, usage band, or other bounded capability is eligible to be used.

### Entitlement Subject
The canonical entity to which entitlement attaches, such as an account, workspace, organization, or other explicitly approved platform subject.

### Product Enablement
Eligibility for baseline access to a FUZE product or product family.

### Capability Class
A bounded family of functionality within or across products, such as premium editing, high-cost model execution, automation scheduling, export rights, billing mutation, or chain-aware participation.

### Entitlement State
The canonical lifecycle posture of an entitlement, such as `pending_activation`, `active`, `restricted`, `suspended`, `expired`, `revoked`, or `closed`.

### Eligibility Outcome
The canonical result of a capability-gating evaluation, such as `eligible`, `ineligible`, `restricted`, `suspended`, `exhausted`, `review_required`, or `policy_blocked`.

### Policy Hold
A state in which ordinary entitlement posture is temporarily suppressed or constrained due to risk, governance, compliance, abuse, billing containment, or other higher-order controls.

### Capability Restriction
A narrower suppression or limitation that affects one capability class or usage band without fully revoking broader product eligibility.

### Entitlement Source Reference
A stable reference to the upstream commercial, policy, rollout, migration, or administrative basis that justified the entitlement posture.

### Derived Eligibility View
A cache, projection, or UI/service summary of canonical entitlement posture produced for convenience rather than as source-of-truth.

## Truth Class Taxonomy

Downstream implementations MUST preserve the following truth classes.

### 1. Canonical Identity Truth
The durable account identity and actor anchor represented by `account_id`.

### 2. Runtime Session Truth
Temporary authenticated runtime presence and privileged-session or service-principal posture where relevant.

### 3. Collaborative Scope Truth
Canonical workspace, organization, and other approved subject-scope structures and lifecycle state.

### 4. Membership Truth
The durable relationship between an account and collaborative scope and its lifecycle posture.

### 5. Structural Authorization Truth
Role assignments, permission mappings, scoped grants, and scope applicability.

### 6. Effective-Permission Truth
The final evaluated allow, deny, restricted, or review-required outcome for a concrete action.

### 7. Entitlement Truth
Commercial and policy eligibility for products, capability classes, and usage posture owned by this domain.

### 8. Commerce / Billing Truth
Subscriptions, invoices, payment methods, payment events, adjustments, and other commercial basis signals that may justify but do not replace entitlement truth.

### 9. Credits / Ledger Truth
Balances, accrual, burn, restriction, settlement, and other credit-ledger semantics that may influence usage eligibility but do not replace entitlement truth.

### 10. Policy / Restriction Truth
Security, risk, governance, compliance, rollout, review, and containment rules that may narrow or suppress eligibility.

### 11. Derived Read-Model Truth
UI bundles, dashboard summaries, exports, search indexes, cached eligibility projections, and support summaries derived from canonical records.

### 12. Reporting / Public View Truth
Public, partner, or end-user-facing summaries that remain downstream presentations rather than canonical eligibility owners.

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
- `WORKSPACE_MEMBERSHIP_LIFECYCLE_SPEC.md`
- `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
- `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md`

and above:

- `PLATFORM_CREDITS_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `PAYMENT_RAILS_INTEGRATION_SPEC.md`
- `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- product integration specifications
- entitlement-aware enforcement adapters
- support/control-plane remediation workflows

This document owns entitlement and capability-gating semantics. It does not redefine identity truth, session truth, workspace truth, membership truth, role/permission truth, effective-permission truth, invoice truth, payment-rail truth, or credits-ledger truth.

## System Boundaries

This document governs only the following platform-owned boundaries:

- canonical meaning of entitlement and capability gating
- approved subject classes and scope-attachment rules for entitlement
- canonical entitlement states and state-transition posture
- distinction among baseline product enablement, premium capability enablement, credits-aware gating, rollout posture, and policy hold
- minimum evaluation rules for converting entitlement records and adjacent signals into eligibility outcomes
- safe consumption of entitlement posture by downstream authorization and product/runtime systems
- event and API contracts necessary for cross-service entitlement consistency
- canonical audit direction for entitlement mutations and high-impact eligibility decisions

It does not govern:

- identity creation or provider resolution
- session issuance, refresh, or invalidation
- workspace creation or membership lifecycle in full depth
- exact permission mapping or object-level policy evaluation
- invoice generation or payment capture
- exact credits accounting semantics
- exact experiment-assignment engine behavior
- exact BI/reporting schema for all downstream analytics consumers

## Adjacent Boundaries

### Identity Domain
Owns canonical account identity. Entitlement may attach to an account, but it does not define account truth.

### Auth / Session Domain
Owns authentication and session validity. A valid session is necessary for many runtime actions but is not the same thing as entitlement.

### Workspace / Organization Domain
Owns collaborative scope, workspace membership structure, lifecycle state, and attachment points. Workspace or organization may be the entitlement subject.

### Authorization Domain
Owns role and permission semantics. Entitlement provides eligibility input; it does not provide actor authority by itself.

### Effective-Permission Domain
Owns the final action-level decision. It must consume entitlement posture for gated capabilities without collapsing entitlement ownership into itself.

### Commerce / Billing Domain
Owns subscriptions, invoices, payment methods, adjustments, and related commercial truth. Those systems may justify entitlement changes, but they are not themselves the entitlement domain.

### Credits / Ledger Domain
Owns balances, accrual, burn, adjustment, settlement, and ledger integrity. Entitlement may coordinate with credits posture where capability policy requires it, but ledger truth remains separate.

### Product Boundary Domain
Owns which product-local semantics may exist and what must remain platform-owned. Product-local capability classes must remain bounded under this constitutional posture.

### Product Admission / Expansion Gate Domain
Owns whether a product may exist or expand within FUZE. Entitlement consumes product readiness outcomes but does not decide product admission itself.

### Security / Risk / Governance Domain
May impose policy holds, review posture, restriction, suspension, or containment that suppress otherwise valid entitlement.

### Audit / Access Traceability Domain
Owns durable traceability surfaces for eligibility mutations, evaluation lineage, and corrective actions.

## Conflict Resolution Rules

When entitlement-related layers disagree, FUZE MUST resolve conflicts in the following order unless a higher-order policy explicitly overrides it:

1. canonical subject identity and scope truth
2. canonical entitlement record and state-transition lineage
3. active policy hold, suspension, restriction, review, or governance posture
4. explicit credits-aware usage posture where the capability contract requires it
5. rollout or migration narrowing posture
6. effective eligibility outcome construction
7. derived UI, dashboard, bundle, export, or product-local cache state

Additional rules:

- a valid role or membership MUST NOT override missing entitlement for a gated capability
- a valid entitlement MUST NOT override missing permission for actor-level action execution
- canonical entitlement MUST outrank frontend plan labels and product-local plan aliases
- rollout flags MAY narrow exposure but MUST NOT silently create commercial truth
- ambiguous commercial inputs MUST resolve conservatively for cost-bearing or sensitive capability use

## Default Decision Rules

When ambiguity exists and no narrower policy-specific rule is available, the platform MUST apply the following defaults:

- default to no entitlement for cost-bearing or sensitive capability use when canonical posture cannot be determined safely
- default to no cross-subject borrowing of entitlement
- default to workspace entitlement not implying equivalent personal entitlement
- default to organization-level commercial status not automatically granting every workspace every capability
- default to explicit exception grants rather than hidden admin or support bypasses
- default to explicit policy hold or review posture when entitlement cannot be safely activated
- default to canonical entitlement reads over cached UI or bundle assumptions
- default to preserving historical lineage rather than silently rewriting mistaken entitlements

## Roles / Actors / Entities

### Entitlement Subject
The account, workspace, organization, or other approved platform subject to which entitlement attaches.

### Entitlement Consumer
A product, API surface, worker, or internal service that requests or consumes eligibility outcomes.

### Commercial Controller
The account or approved business context responsible for the upstream commercial relationship that may justify entitlement.

### Capability Owner
The platform or product domain responsible for defining a capability class and its gating contract.

### Entitlement Operator
A privileged internal actor or approved automated pathway allowed to create, adjust, suspend, or restore entitlement posture under policy.

### Policy Owner
The domain responsible for a policy hold, review requirement, restriction, or narrowing rule that affects entitlement posture.

### Evaluation Adapter
A service or module that converts canonical entitlement state and supporting context into a runtime eligibility outcome.

### Product Runtime
A user-facing or automated runtime that must honor canonical eligibility outcomes before performing gated actions.

## Ownership Model

### Entitlement and Capability Domain Owns
- canonical entitlement semantics
- entitlement subject attachment rules
- product-enable and capability-class eligibility structures
- entitlement state semantics and lifecycle posture
- canonical distinction between broader product access and narrower capability access
- eligibility outcome semantics used by downstream consumers
- event publication for entitlement mutation and eligibility-affecting changes in coordination with adjacent domains
- canonical audit references for entitlement mutation and policy hold lineage

### Entitlement and Capability Domain May
- expose entitlement read APIs and evaluation APIs
- define approved capability-gating namespaces and contracts
- consume upstream commercial, rollout, policy, risk, and credits signals through explicit references
- publish derived eligibility summaries for low-risk consumption
- coordinate with support/control-plane tooling for reviewed corrective workflows

### Entitlement and Capability Domain Must Not
- redefine invoice or payment truth
- redefine credits-ledger truth
- redefine final role/permission authority
- allow products to become canonical owners of shared entitlement semantics for shared platform capabilities
- silently widen ambiguous eligibility into enablement for sensitive or cost-bearing behavior
- treat feature flags, frontend state, or plan-label text as canonical entitlement records

### Product Domains May
- define product-local capability classes within approved namespaces
- request canonical eligibility evaluations
- cache low-risk derived eligibility summaries subject to freshness and invalidation rules
- present UX-specific messaging about unavailable capabilities

### Product Domains Must Not
- mint hidden entitlement systems for shared platform capabilities
- bypass platform-owned eligibility checks for paid, high-cost, or sensitive features
- convert local experimentation switches into canonical commercial truth
- treat membership, object ownership, or creator status as entitlement

## Authority / Decision Model

Eligibility in FUZE is a layered decision system.

### Upstream Layers Provide
- canonical subject identity and scope
- workspace or organization context where applicable
- commercial source references where applicable
- credits posture where applicable
- rollout or migration posture where applicable
- policy hold or restriction posture where applicable

### Entitlement and Capability Layer Decides
- whether the subject is eligible for baseline product enablement
- whether narrower capability classes are enabled, constrained, exhausted, suspended, or review-gated
- whether broader commercial posture produces immediate enablement or only entitlement potential pending activation
- which stable eligibility outcome and reason codes should be returned to downstream consumers

### Authorization / Effective-Permission Layers Decide
- whether the requesting actor is allowed to use the eligible capability in this scope and action context
- whether role, permission, object state, and higher-order policy still allow the exact action

This layered order is mandatory. Entitlement does not replace authorization, and authorization does not replace entitlement.

## Subject and Scope Model

Entitlement must attach to an explicit subject.

### Approved Subject Classes
At minimum, FUZE SHOULD support the following semantic subject classes:

1. **Account subject** — individual or self-service eligibility
2. **Workspace subject** — collaborative, product, or team eligibility
3. **Organization subject** — umbrella or enterprise-level eligibility where implemented distinctly
4. **Platform operational subject** — internal or platform-controlled service posture only where explicitly approved

### Subject Rules
- subject class MUST be explicit in canonical records
- subject identifier MUST be durable and canonical
- cross-subject borrowing is forbidden unless a downstream spec explicitly defines it
- account entitlement does not automatically imply workspace entitlement
- workspace entitlement does not automatically imply all member accounts individually hold equivalent personal entitlement
- organization-level entitlement may inform workspace posture only through explicit inheritance or attachment rules, not assumption

## Capability Taxonomy Model

FUZE MUST distinguish broader product enablement from narrower capability gates.

### Canonical Capability Layers

#### 1. Product Admission Layer
Whether the subject may use the product at all.

#### 2. Plan / Tier Layer
Which service band, capacity tier, or rights family the subject is eligible for.

#### 3. Capability-Class Layer
Which narrower feature families are enabled.

Representative examples include:
- premium creation or editing modes
- export rights
- automation scheduling
- high-cost model usage
- workspace-wide admin surfaces
- billing-sensitive product operations
- chain-aware participation pathways

#### 4. Usage-Posture Layer
Whether a capability remains currently usable under quotas, ceilings, credits-sensitive posture, or exhaustion rules.

#### 5. Policy / Restriction Layer
Whether a broader restriction, review requirement, suspension, or governance hold suppresses otherwise valid eligibility.

### Taxonomy Rules
- broader entitlement does not imply every narrower capability
- narrower capability grants or unlocks must remain namespaced and durable
- capability definitions must avoid alias drift
- product-local terminology may exist, but canonical contracts must reduce ambiguity

## State Model

At minimum, entitlement lifecycle MUST support the following semantic states:

- `pending_activation`
- `active`
- `restricted`
- `suspended`
- `expired`
- `revoked`
- `closed`

### State Meanings

#### Pending Activation
The upstream basis for entitlement exists, but the capability is not yet fully active for ordinary use.

#### Active
The subject is ordinarily eligible, subject to downstream authorization, policy, usage posture, and runtime checks.

#### Restricted
The entitlement remains present but one or more capability classes or usage rights are constrained.

#### Suspended
The entitlement is materially suppressed pending resolution of billing, abuse, governance, compliance, security, or operational issues.

#### Expired
The entitlement naturally ended under time-bound or term-bound policy.

#### Revoked
The entitlement was explicitly removed before ordinary expiry, usually through authoritative corrective or containment action.

#### Closed
The entitlement has ended and entered durable historical posture.

### State Rules
- state transitions MUST be explicit and auditable
- state MUST NOT be inferred only from UI switches or derived summaries
- restrictive states outrank stale active projections
- reactivation MUST preserve lineage rather than silently replacing history

## Lifecycle / Workflow Model

### Entitlement Creation
Entitlement may be created by:
- approved self-service commerce flows
- workspace or organization administration flows
- managed enterprise provisioning
- migration and normalization pathways
- reviewed internal corrective workflows
- approved promotional or policy-driven grant flows

Creation MUST define at minimum:
- entitlement identifier
- entitlement subject reference
- product or capability family reference
- initial entitlement state
- source reference
- effective dates where relevant
- reason codes where relevant
- audit and correlation references

### Entitlement Activation
Activation is the transition from justified potential access to ordinary eligible posture.

Activation rules:
- activation MUST follow explicit success conditions
- activation MUST NOT silently assign actor authority
- activation may enable only the relevant subject and capability classes
- activation SHOULD publish durable eligibility-change events

### Entitlement Narrowing / Restriction
Restriction may be applied when:
- plan posture changes
- usage posture requires temporary degradation
- credits-sensitive policy suppresses a usage band
- risk or abuse review constrains a capability class
- governance or policy requires narrower rights without full removal

### Entitlement Suspension
Suspension may be applied when:
- commercial containment is required
- abuse or security containment is required
- account or workspace restriction materially blocks continued capability use
- legal, governance, or operational hold is required

### Expiry / Revocation / Closure
Expiry, revocation, and closure MUST preserve historical lineage and reason codes. Capability use after those transitions MUST fail closed unless a separately valid successor entitlement exists.

## Invariants

1. Entitlement is never identical to role-based authority.
2. Entitlement must attach to explicit canonical subject scope.
3. Membership alone never proves entitlement.
4. Valid entitlement never overrides missing permission for actor-level action execution.
5. Missing entitlement for a gated capability must remain representable distinctly from missing permission.
6. Product-local feature flags may refine rollout behavior but must not replace canonical entitlement truth.
7. Commercial source references may justify entitlement but do not replace it.
8. Credits posture may influence usage eligibility without collapsing credits-ledger truth into entitlement truth.
9. Restrictive or suspended eligibility posture outranks stale active caches.
10. High-impact entitlement mutation without traceability is a defect.

## Functional Rules

### Product Access Rule
A product that requires entitlement MUST NOT be treated as usable merely because the actor is authenticated or belongs to the workspace.

### Capability Narrowing Rule
If a subject is entitled to a product but not to a narrower premium or sensitive capability class, the narrower capability must remain unavailable.

### Distinct Failure Rule
Missing entitlement, restricted entitlement, exhausted usage posture, and missing permission are different failure classes and must remain distinguishable internally.

### Freshness Rule
Sensitive or cost-bearing capability execution MUST use sufficiently fresh entitlement posture and MUST NOT rely solely on stale UI hints or cached feature summaries.

### Replay-Safe Mutation Rule
Entitlement creation, activation, downgrade, suspension, and revocation workflows MUST be idempotent enough to avoid duplicate or contradictory posture.

### Scope-Correctness Rule
Capability checks MUST evaluate against the correct entitled subject. Wrong-subject evaluation is unsafe evaluation.

### No Shadow Grant Rule
Products MUST NOT create hidden permanent allow-lists, hard-coded plan bypasses, or undocumented admin switches that act as canonical entitlement truth.

### Exception Discipline Rule
Promotions, temporary unlocks, migration grants, and support exceptions MUST be explicit, bounded, auditable, and terminable.

## Permission / Access Considerations

This specification does not own final actor authorization, but it imposes mandatory downstream constraints.

### Required Constraints
- effective-permission evaluation must coordinate with entitlement posture for gated capabilities
- entitlement success must never imply that every actor in the subject scope may perform every action
- missing permission must still deny the action even where entitlement exists
- policy-blocked or review-required actions remain blocked or reviewed even where entitlement exists
- support and admin tooling must not convert entitlement fixes into silent permission grants

## Entitlement Considerations

This document is the core entitlement specification. Several additional constraints remain mandatory.

### Required Separation Rules
- subscription state is not itself entitlement state
- invoice delinquency signal is not itself entitlement state, though it may justify suspension
- credits balance is not itself entitlement state, though it may justify `exhausted` or restricted usage posture for certain capability classes
- rollout flag is not itself entitlement state, though it may narrow exposure for a validly entitled subject
- workspace-level entitlement and account-level entitlement must remain distinguishable where both exist

## API / Contract Implications

The platform SHOULD expose entitlement behavior through explicit, bounded contracts.

### Entitlement APIs SHOULD Own
- entitlement subject lookup
- product and capability eligibility summary
- high-fidelity eligibility evaluation for gated actions
- entitlement mutation workflows where separately authorized
- entitlement state history and reason-code reads for internal operators where policy allows

### Authorization APIs SHOULD Own
- role and permission evaluation
- final action-level effective-permission decisions

### Commerce APIs SHOULD Own
- subscriptions
- invoices
- payment methods
- payment outcomes
- refunds and adjustments

### Credits APIs SHOULD Own
- balances
- accrual and burn
- ledger entries
- settlements

### Contract Rules
- entitlement APIs MUST return stable machine-readable outcomes and reason codes
- API consumers MUST NOT infer entitlement solely from price-plan names or frontend bundle assumptions
- async workers MUST carry enough subject, capability, and correlation context to re-check eligibility safely when required
- service-to-service calls MUST preserve actor-on-behalf-of context when entitlement and permission both matter

## Event / Async Implications

Entitlement change is a cross-service coordination event.

### Canonical Event Families
At minimum, the platform SHOULD support semantic events such as:
- `entitlement.created`
- `entitlement.activated`
- `entitlement.restricted`
- `entitlement.suspended`
- `entitlement.expired`
- `entitlement.revoked`
- `entitlement.restored`
- `capability_gating.changed`
- `usage_posture.exhausted`
- `policy_hold.applied`
- `policy_hold.released`

### Event Rules
- events MUST be published after canonical commit
- events MUST carry subject, capability, state, reason, effective-time, and correlation references
- consumers MUST treat events as synchronization signals, not as independent sources of truth
- async execution that materially depends on entitlement SHOULD revalidate before costly or sensitive action execution where freshness policy requires

## Data Model / Storage Implications

At minimum, the entitlement domain SHOULD support the following durable semantic structures:

- `entitlement_record`
- `entitlement_subject_reference`
- `capability_class_reference`
- `entitlement_source_reference`
- `entitlement_state_transition`
- `policy_hold_reference`
- `derived_eligibility_view`
- `entitlement_audit_reference`

### Data Model Rules
- canonical records MUST separate source reference from derived eligibility summary
- product-local caches may exist but MUST remain non-canonical
- subject class and subject identifier MUST be explicit
- state-transition lineage MUST be reconstructable
- temporal validity windows MUST be explicit where relevant
- capability-class namespaces MUST remain stable enough for audit, APIs, migration, and support review

## Read Model / Projection / Reporting Rules

- derived eligibility views MAY support UX and low-risk reads, but MUST remain explicitly derived
- sensitive or cost-bearing pathways MUST bypass or revalidate stale derived views against canonical posture
- product bundles and feature-summary dashboards MAY aggregate entitlement state, but MUST remain traceable back to canonical entitlement records
- public or end-user-facing explanations MAY collapse detail, but protected internal records MUST preserve richer reason families
- projection lag MUST NOT change enforcement behavior or silently extend revoked, suspended, or exhausted capability use

## Security / Risk / Abuse Controls

Entitlement and capability gating are part of the platform trust and monetization surface.

### Required Controls
- privileged entitlement mutation MUST require explicit authorization and traceability
- abuse, fraud, governance, or compliance holds MUST be able to suppress capability use without corrupting historical entitlement lineage
- sensitive internal or external capabilities SHOULD support `review_required` or `policy_blocked` posture where automatic enablement is unsafe
- products MUST NOT bypass entitlement suspension through client-side affordances or stale caches
- commercial abuse patterns that exploit delayed downgrade, delayed suspension, delayed credits exhaustion, or async race conditions MUST be treated as security defects

## Boundary Violation Detection / Non-Canonical Patterns

The following patterns are non-canonical and forbidden:

- treating paid status as universal permission
- treating workspace membership as product eligibility
- treating credits balance as canonical entitlement by itself
- using feature flags or rollout assignments as the canonical entitlement record
- creating product-local entitlement systems for shared platform capabilities
- using hidden admin or support switches as permanent eligibility truth
- letting stale bundles or dashboards continue premium capability use after canonical restriction or suspension
- silently widening organization commercial status into workspace capability use without explicit attachment rules

Implementations SHOULD detect and surface these conditions through diagnostics, audit, and operational alerts.

## Audit / Traceability Requirements

FUZE MUST be able to determine:

- why an entitlement was created
- which subject held it
- which product or capability class it covered
- what upstream commercial, policy, migration, or exception basis justified it
- when it became active, restricted, suspended, expired, revoked, restored, or closed
- which policy hold or operational restriction narrowed its behavior
- which downstream actions failed because entitlement was missing versus because permission was missing
- which operator or automated policy executed a corrective or exceptional mutation
- whether a visible eligibility summary was canonical or derived

Entitlement is a shared trust and monetization surface and must therefore be reconstructable.

## Failure Handling / Edge Cases

### Membership Exists but Entitlement Missing
The subject may remain structurally valid in scope, but gated product or capability use MUST remain unavailable.

### Entitlement Exists but Permission Missing
The actor remains ineligible to perform the action. Eligibility does not override authority.

### Workspace Entitled but Actor Outside Authorized Role
The product may be enabled for the workspace, but the actor may still be denied.

### Credits-Dependent Capability Exhausted
The subject may remain generally entitled to the product while a narrower capability class becomes temporarily exhausted or restricted.

### Subscription or Payment Ambiguity
The platform MUST preserve conservative policy. Where commercial truth is ambiguous and safe entitlement cannot be established, cost-bearing or sensitive capability use SHOULD fail closed or route to review according to policy.

### Stale Derived Eligibility View
Sensitive actions MUST re-check fresh enough canonical posture rather than trusting stale projections.

### Support Exception Needed After Wrong Downgrade
The platform MUST preserve explicit corrective lineage rather than silently rewriting history or bypassing canonical subject attachment.

### Product Requests Capability Not Defined in Canonical Namespace
This MUST be rejected or routed for platform review rather than silently creating a shadow capability gate.

### Organization-Level Contract with Workspace-Level Consumption
This is allowed only through explicit inheritance or attachment rules. Organization commercial status MUST NOT silently grant every workspace every capability by assumption.

### Rollout Flag Disagrees with Canonical Entitlement
Canonical entitlement wins for commercial truth. Rollout posture may narrow exposure temporarily but MUST NOT misrepresent underlying entitlement state.

## Operational Considerations

Operational systems SHOULD support:
- explicit entitlement read and write boundaries
- safe downgrade and suspension workflows
- observability for entitlement misses, repeated gating failures, stale-cache incidents, and policy-hold frequency
- reconciliation between canonical entitlement truth and downstream derived views
- runbooks for wrong-subject attachment, mistaken downgrade, credits-aware exhaustion disputes, and rollout/entitlement mismatch incidents
- change-management controls for capability taxonomy evolution

Eligibility posture is a platform-owned operational concern, not a frontend convenience switch.

## Migration / Compatibility / Supersession Considerations

- migrations MUST remove product-local or legacy gating systems that act like canonical entitlement without platform ownership
- compatibility layers MAY preserve old plan names or package names temporarily, but canonical entitlement semantics MUST remain explicit
- historical systems that implied paid user equals fully authorized user are superseded within this scope
- historical systems that implied workspace membership equals product eligibility are superseded within this scope
- historical systems that used credits balance alone as product-rights truth are superseded within this scope
- capability namespaces may evolve, but lineage and mapping continuity MUST be preserved
- future tightening SHOULD make eligibility more explicit and traceable, not more implicit

## Implementation-Contract Guardrails

Downstream implementations MUST preserve all of the following:

1. entitlement remains distinct from identity, session, membership, authorization, effective-permission, billing, and credits-ledger truth
2. every entitlement attaches to an explicit canonical subject
3. product enablement and narrower capability eligibility remain distinguishable
4. missing entitlement, restricted entitlement, exhausted usage posture, and missing permission remain distinct failure classes internally
5. canonical entitlement posture outranks stale bundles, rollout dashboards, and product-local caches
6. rollout or experiment posture may narrow but not silently create commercial truth
7. credits-aware gating remains coordinated with but not collapsed into credits-ledger truth
8. hidden support or admin bypasses remain forbidden as permanent entitlement mechanisms
9. degraded runtime conditions MUST NOT silently widen capability enablement for sensitive or cost-bearing actions
10. idempotency and replay safety MUST be preserved for entitlement creation, activation, suspension, revocation, restoration, and correction workflows
11. reason capture, source references, policy references, and audit lineage MUST remain explicit for high-impact eligibility changes
12. downstream teams MUST NOT optimize away subject class, capability namespace, state lineage, or failure distinctions where those elements protect security, monetization integrity, auditability, or migration safety

## Downstream Execution Staging

This specification implies the following preferred downstream implementation order:

1. stabilize subject attachment and scope rules
2. stabilize capability taxonomy and namespace rules
3. stabilize entitlement state and transition semantics
4. integrate billing, pricing, rollout, and credits-aware inputs through explicit references
5. integrate effective-permission and product-runtime enforcement
6. integrate audit, reporting, support/control-plane, and correction workflows
7. integrate derived views, dashboards, and reconciliation surfaces

## Required Downstream Specs / Contract Layers

This specification directly requires compatible downstream refinement and implementation-contract work in:

- `PLATFORM_CREDITS_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `PAYMENT_RAILS_INTEGRATION_SPEC.md`
- `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- product integration specifications
- entitlement-aware API and worker enforcement
- audit, reporting, and support/control-plane workflows

## Canonical Examples / Anti-Examples

### Canonical Example 1 — Workspace Entitled, Actor Still Denied
A workspace has valid product entitlement, but a member without the required role is denied a sensitive action. Eligibility exists; authority does not.

### Canonical Example 2 — Product Entitled, Premium Export Not Entitled
A subject may access a product generally but lacks a narrower export capability class. The export remains unavailable.

### Canonical Example 3 — Credits-Aware Restriction Without Full Product Removal
A subject remains generally entitled to a product, but a credits-dependent high-cost model capability becomes `exhausted` or restricted while the broader product remains enabled.

### Canonical Example 4 — Rollout Narrows, Does Not Replace
A validly entitled subject is temporarily narrowed by rollout posture for a newly released capability. The rollout does not become the canonical commercial truth of entitlement itself.

### Anti-Example 1 — Paid Plan Equals Admin
A product treats paid subscription status as permission to perform billing-sensitive or admin-sensitive mutations. This is forbidden.

### Anti-Example 2 — Membership Equals Product Right
A workspace member is allowed to use every premium capability because they belong to the workspace. This is forbidden.

### Anti-Example 3 — Credits Balance Equals Entitlement
A product enables gated product surfaces solely because the subject has credits. This is forbidden.

### Anti-Example 4 — Hidden Support Unlock
Support tooling silently flips a product-local switch that acts as permanent entitlement with no canonical record or bounded expiry. This is forbidden.

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
- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `ADMIN_ACCESS_CORRECTION_AND_CONTAINMENT_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
- `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`

This specification directly governs or materially informs:

- `PLATFORM_CREDITS_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `PAYMENT_RAILS_INTEGRATION_SPEC.md`
- `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- product integration specifications
- entitlement-aware enforcement adapters
- support/control-plane remediation workflows

## Explicitly Deferred Items

The following are intentionally deferred to adjacent or downstream specifications:

- exact numeric quota tables and tier limits
- exact pricing tables and commercial packaging
- exact rollout-engine implementation
- exact credits-ledger reservation and settlement mechanics
- exact invoice and refund workflow semantics
- exact end-user explanation copy for each eligibility failure
- exact support console UX for entitlement correction

These do not weaken the canonical entitlement and capability-gating model established here.

## Final Normative Summary

FUZE entitlement and capability gating is the canonical platform layer for product and feature eligibility. It answers whether a subject is eligible to use a product or capability class. It is not identity, not session validity, not membership, not actor authority, not effective permission, not invoice truth, and not credits-ledger truth.

A subject may be entitled and the actor still denied. A subject may be a workspace member and still lack entitlement. A subject may have credits and still lack product entitlement. A product may be generally enabled while a narrower capability remains restricted, exhausted, suspended, or review-gated. These distinctions are mandatory and must remain explicit in downstream systems.

This document is the canonical FUZE rule set for entitlement and capability gating semantics.

## Quality Gate Checklist

- [x] Canonical owner is explicit for every material truth family
- [x] Mutation boundaries are explicit
- [x] Adjacent boundaries are explicit
- [x] Truth classes are explicit
- [x] Conflict-resolution rules are explicit
- [x] Default decision rules are explicit
- [x] Non-canonical patterns are clearly called out
- [x] Operator override and exception paths are bounded, explicit, and auditable
- [x] Read-model, cache, and reporting rules are explicit
- [x] Failure and degraded-mode behaviors are explicit
- [x] Downstream implementation guardrails are explicit
- [x] Dependencies and downstream impacts are explicit
- [x] Non-goals and deferred items are explicit
- [x] The document is strong enough to support downstream implementation-contract work without contradictory semantics
