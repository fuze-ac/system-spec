# FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC

## Title
FUZE Account Access and Session Canonical Final Specification

## Document Metadata

- Document Name: `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined canonical spec
- Version: 2.0.0
- Effective Date: 2026-04-21
- Last Updated: 2026-04-21
- Reviewed On: 2026-04-21
- Document Owner: FUZE Platform Identity and Access Architecture
- Approval Authority: FUZE Platform Architecture and Governance Authority
- Review Cadence: Quarterly or upon material change to account identity, linked authentication, session semantics, provider resolution, recovery posture, workspace/authorization ordering, wallet-aware access posture, or security containment behavior
- Governing Layer: Platform core / canonical cross-domain rule set for account, access, and session
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, security engineering, product engineering, API design, support operations, audit, governance, reliability engineering, implementation-contract authors, community-facing architecture writers
- Primary Purpose: Define the canonical FUZE platform rule set for how durable account identity, approved access paths, and temporary authenticated sessions interact across products and domains so that downstream specs and implementations cannot reinterpret identity, authentication, session, workspace, authorization, wallet-aware context, or reporting behavior inconsistently
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
  - `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
  - `IDENTITY_AND_ACCOUNT_SPEC.md`
  - `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
  - `WALLET_AWARE_USER_SPEC.md`
- Primary Downstream Dependents:
  - `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
  - `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
  - `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
  - `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
  - `KEY_MANAGEMENT_AND_USER_RECOVERY_SPEC.md`
  - `WORKSPACE_AND_ORGANIZATION_SPEC.md`
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
  - `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
  - `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
  - `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
  - `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
  - `AUTH_IDENTITY_API_SPEC.md`
  - `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
  - product integration specifications
  - support and control-plane workflows
- Supersedes: Earlier thesis-style or less explicit account/access/session writeups to the extent they conflict within this document’s scope
- Superseded By: Not yet known
- Related Decision Records: Not yet known
- Canonical Status Note: This document is the governing FUZE canonical account/access/session specification. Downstream documents, APIs, products, services, reports, support tooling, and control-plane workflows MUST NOT redefine the semantics established here.
- Implementation Status: Normative architecture baseline; downstream API, workflow, storage, event, runtime, and audit contracts must conform
- Approval Status: Drafted for refined-system inclusion; formal approval record not yet attached
- Change Summary:
  - upgraded the prior canonical draft into the full refined-system format
  - aligned the canonical layer with the active thesis, identity, auth/session, wallet-aware, authorization, entitlement, audit, and chain-boundary refinements
  - made truth classes, ownership boundaries, conflict rules, and default decision rules explicit
  - strengthened implementation-contract guardrails, projection/reporting limits, operator constraints, and deterministic failure posture

## Purpose

This specification defines the canonical FUZE rule set for how **account identity**, **approved access paths**, and **authenticated session runtime state** fit together.

Its purpose is to establish one stable cross-domain interpretation that all downstream specifications and implementations MUST preserve:

- identity is anchored by the canonical account
- authentication methods are controlled access paths to that account
- sessions are temporary runtime state created only after successful authentication and policy checks
- workspace, authorization, entitlement, wallet-aware participation, and product behavior are downstream layers that consume this foundation but do not redefine it

This document is intentionally positioned between the more explanatory thesis layer and the deeper domain and API implementation layers. It is more normative than the thesis and more cross-domain than the implementation specs.

## Scope

This specification governs:

- the canonical semantic relationship between account, access path, and session
- the platform ordering of authentication, session establishment, workspace resolution, authorization evaluation, and entitlement evaluation
- the truth-class separation between identity truth, access-path truth, runtime session truth, provider input, wallet-aware context, and derived/reporting views
- canonical ownership and mutation boundaries for identity and access-related truth families
- cross-product continuity rules for a person moving across providers, products, workspaces, and wallet-aware surfaces
- required invariants for anti-fragmentation, anti-silent-merge, and session subordination to account and risk posture
- conflict-resolution and default decision rules where domains or truth layers disagree
- minimum canonical expectations for data-model shape, API layering, events, auditability, idempotency, replay handling, and operator behavior
- implementation guardrails that downstream contracts must preserve

## Out of Scope

This specification does not define:

- provider-specific SDK or protocol implementation detail
- exact credential storage, MFA, or step-up mechanism design
- exact session transport, cookie, token, or client-cache implementation detail
- exact workspace membership algorithms or role catalogs
- exact entitlement catalogs, pricing, or billing rules
- exact wallet verification challenge design
- exact support-tool UX or admin-console workflow design
- exact database topology or service decomposition
- exact legal identity, KYC, or compliance overlays unless later explicitly introduced

Those topics belong in adjacent or downstream specifications.

## Design Goals

1. preserve one canonical person-level identity anchor across the FUZE ecosystem
2. separate identity, authentication, session, workspace, authorization, entitlement, and wallet-aware participation into durable layers
3. allow product-specific login entry choices without allowing product-specific identity truth
4. preserve continuity when providers, products, workspaces, and wallet links change over time
5. make conflict, recovery, remediation, and containment deterministic, explicit, and auditable
6. prevent reporting, caches, surfaces, provider claims, or product-local tables from becoming hidden truth owners
7. create a canonical architecture strong enough to constrain downstream APIs, data models, events, and runtime behavior

## Non-Goals

This specification is not intended to:

- treat providers as separate identities
- treat sessions as durable identity truth
- treat valid login as equivalent to workspace access, permission, or entitlement
- treat wallet possession as a universal identity replacement
- permit products to create shadow platform user roots
- let reports, exports, support dashboards, or search projections become canonical owners
- replace downstream API, schema, workflow, runbook, or control-plane specs

## Core Principles

### 1. Account Root Principle
`account_id` is the canonical durable actor anchor of the FUZE platform.

### 2. Access Path Principle
Authentication methods and linked logins are approved access paths to the canonical account, not alternate identities.

### 3. Session Subordination Principle
A session is temporary authenticated runtime state. Session validity is always subordinate to account state, auth-path state, recovery posture, and security/risk posture.

### 4. Downstream Layering Principle
Workspace scope, authorization, entitlement, wallet-aware participation, and product-local behavior are downstream consumers of canonical identity and session state. They do not redefine account/access/session semantics.

### 5. Backend Normalization Principle
External provider outputs, client-side state, and product entry flows must normalize into FUZE-owned backend semantics before becoming platform truth.

### 6. Continuity Before Convenience Principle
When convenience and continuity conflict, FUZE MUST preserve continuity, auditability, and anti-fragmentation safety.

### 7. Explicit Remediation Principle
Ambiguous or contested cases MUST resolve through explicit reviewable pathways rather than silent merge, silent reassignment, or silent fragmentation.

### 8. Derived View Discipline Principle
Read models, analytics, support views, exports, and public-facing explanations may summarize canonical truth but MUST remain derived and regenerable.

## Canonical Definitions

### Account
The canonical FUZE identity record for a person or actor. It answers: **who is this actor in FUZE?**

### Access Path
An approved mechanism by which an actor proves control over the canonical account.

### Linked Login
The durable relationship between a canonical account and one approved access path.

### Session
Temporary authenticated runtime state created after successful authentication and required policy evaluation. It answers: **what authenticated runtime presence does this actor currently have?**

### Access Continuity
The property that the same actor remains attached to the same canonical account over time even as login preference, provider mix, products, workspaces, or wallet-aware context change.

### Authorization Context
The post-authentication operating context in which workspace scope, role, permission, object conditions, and entitlement are evaluated.

### Provider Input
Externally supplied login evidence such as issuer-subject pairs, provider claims, callback payloads, or wallet-auth proofs. Provider input is evidence, not canonical identity truth.

### Conflict / Remediation Case
An explicit bounded platform state used when the platform cannot safely determine a single canonical outcome automatically.

## Truth Class Taxonomy

This document distinguishes the following truth classes and requires downstream specs to preserve those distinctions.

### 1. Canonical Identity Truth
The account record, its lifecycle, and the identity-domain continuity semantics anchored by `account_id`.

### 2. Access-Path Truth
The durable approved mapping between the canonical account and an authentication method or provider-backed subject.

### 3. Runtime Session Truth
The active or historical session state representing temporary authenticated runtime presence.

### 4. Policy Truth
Security policy, provider-approval policy, recovery policy, operator-control policy, entitlement policy, and higher-order platform rules that constrain behavior.

### 5. Provider-Input Truth
Validated external inputs supplied by providers or wallet-auth flows. These are inputs to FUZE-owned normalization and resolution logic.

### 6. Authorization Truth
Workspace scope, membership, role assignment, permission structure, and effective-permission evaluation owned by downstream authorization domains.

### 7. Wallet-Aware Context Truth
Wallet-link state and wallet-derived participation or eligibility context. This is attached context, not identity root.

### 8. Derived Read-Model Truth
Support views, dashboards, analytics models, search projections, exports, and public-facing explanatory surfaces generated from canonical records.

### 9. Reporting / Public View Truth
Reports, public registries, and summarized communication surfaces. They are downstream presentations and must remain correctable from canonical truth.

## Architectural Position in the Spec Hierarchy

This document sits below:

- `REFINED_SYSTEM_SPEC_INDEX.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`

and above:

- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
- `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
- `KEY_MANAGEMENT_AND_USER_RECOVERY_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`

This document is the governing canonical layer for the account/access/session relationship. It does not absorb the detailed ownership of the downstream domains listed above.

## System Boundaries

This specification governs the following semantic boundaries:

- **identity boundary**: account identity is canonical and durable
- **access boundary**: approved auth methods are durable access paths to the account
- **session boundary**: sessions represent temporary runtime presence only
- **workspace boundary**: workspace and organization context are resolved only after authentication succeeds
- **authorization boundary**: permissions are evaluated only after scope is known
- **entitlement boundary**: product eligibility is evaluated after identity, session, and scope are known
- **wallet boundary**: wallet-aware participation may attach to the account but does not replace it
- **reporting boundary**: reports and public surfaces do not become owners of underlying truth

## Adjacent Boundaries

### Identity and Account Domain
Owns canonical account semantics, continuity meaning, identity lifecycle, and the canonical actor anchor.

### Auth / Session / Linked Login Domain
Owns auth-path lifecycle, auth challenges, session issuance, session continuation, revocation, and runtime access mechanics.

### Workspace / Organization Domain
Owns collaborative operating context and membership structures.

### Authorization Domain
Owns roles, permissions, scoped grants, and effective-permission evaluation.

### Entitlement Domain
Owns product and capability eligibility rules.

### Wallet-Aware Domain
Owns wallet-link truth and wallet-derived participation context.

### Audit / Traceability Domain
Owns durable reconstructability across identity, session, scope, and privileged-control actions.

### Reporting / Public Surfaces
May present derived interpretations but MUST NOT own mutation or semantics for canonical truth.

## Conflict Resolution Rules

When truth layers disagree, the platform MUST resolve using the following precedence unless a higher-order approved policy explicitly states otherwise:

1. canonical account/access records owned by FUZE backend domains
2. explicit security, policy, restriction, recovery, or remediation decisions
3. validated provider-input evidence processed through approved normalization rules
4. runtime session state
5. derived views, reports, caches, support displays, and public-facing surfaces

The following conflict rules are mandatory:

- provider profile data MUST NOT override canonical account ownership
- email similarity MUST NOT justify silent account merge
- wallet-link state MUST NOT reassign canonical identity
- valid session presence MUST NOT override suspended, restricted, or recovered account posture
- workspace membership MUST NOT become a substitute for identity
- authorization allow state MUST NOT be inferred solely from authentication success
- product-local user tables MUST NOT override `account_id`
- reporting mismatches MUST resolve in favor of canonical backend records

## Default Decision Rules

When ambiguity exists and no more specific downstream rule applies, the following defaults govern:

1. the default durable actor anchor is `account_id`
2. the default owner of person-level identity semantics is the identity/account domain
3. the default owner of runtime authenticated presence is the auth/session domain
4. the default owner of workspace scope is the workspace/organization domain
5. the default owner of permission semantics is the authorization domain
6. the default owner of capability eligibility is the entitlement domain
7. the default interpretation of provider input is evidence, not final truth
8. the default interpretation of email is contact and hint, not sole canonical mapping key
9. the default interpretation of wallet state is attached context, not identity root
10. the default resolution for contested identity or provider evidence is explicit review rather than silent automation
11. the default resolution for privileged corrections is reason-coded, policy-bound, and audited execution
12. the default runtime posture under uncertainty is fail-closed for high-impact access and non-destructive for canonical identity storage

## Roles / Actors / Entities

- **Human actor**: a natural person using FUZE products
- **System actor**: an internal service or automation acting under explicit service identity and policy scope
- **Canonical account**: the platform identity anchor
- **Linked authentication method**: a durable access-path relationship
- **Session**: temporary runtime authenticated state
- **Provider subject**: stable provider-scoped external subject used for mapping
- **Recovery case**: controlled process for restoring access to the same account
- **Conflict/remediation case**: explicit case for contested or unsafe automatic resolution
- **Product profile extension**: downstream local profile or UX extension derived from the canonical account

## Ownership Model

### Canonical Owners
- identity semantics and the durable actor anchor: Identity and Account Domain
- linked-login and auth challenge semantics: Auth / Session / Linked Login Domain, subject to canonical identity constraints
- runtime session lifecycle: Auth / Session / Linked Login Domain
- workspace scope: Workspace / Organization Domain
- permissions and effective access: Authorization Domain
- capability eligibility: Entitlement Domain
- wallet-link truth: Wallet-Aware User Domain
- cross-domain reconstructability: Audit / Traceability Domain

### Mutation Ownership
Only owner-controlled backend pathways may mutate canonical identity or access-path truth. Products, frontends, reports, public surfaces, analytics systems, and general support dashboards MUST NOT mutate canonical truth directly.

### Shared Constraint
Where multiple domains participate in one workflow, the upstream owner of each truth family remains authoritative. Workflow orchestration MUST NOT erase ownership boundaries.

## Authority / Decision Model

### Identity Domain Authority
The identity domain may:
- define the canonical account anchor
- define identity continuity semantics
- participate in or own identity conflict and remediation decisions
- expose canonical identity reads

### Auth / Session Domain Authority
The auth/session domain may:
- validate access proofs
- manage linked-login lifecycle according to identity and continuity constraints
- issue, inspect, rotate, revoke, and invalidate sessions
- enforce session containment after recovery, restriction, or security events

### Product Authority Limits
Products may:
- initiate approved login flows
- request authentication outcomes
- consume account/session/scope/authorization outcomes
- maintain product-local derived profiles

Products must not:
- invent alternate platform actor roots
- treat provider or wallet state as canonical identity by default
- decide provider-account resolution unilaterally
- treat valid session state as equivalent to final access entitlement

### Operator Authority Limits
Operator and support actions must be bounded, policy-constrained, reason-coded, attributable, correlation-linked, and auditable. No operator path may silently bypass canonical domain validation.

## State Model

This document requires the platform model to remain able to express, at minimum, the following durable entities or equivalent owner-governed representations:

- `account`
- `account_auth_method` or equivalent linked-login entity
- `auth_session`
- `identity_conflict_case`
- `account_recovery_case`
- `session_lineage` or equivalent rotation lineage where applicable
- `identity_operation_request` or equivalent privileged mutation record

### Minimum Semantic State Families

**Account state** must distinguish at least:
- pending setup
- active
- restricted
- suspended
- deactivated / closed
- merged (only for explicit remediation)

**Linked-login state** must distinguish at least:
- pending verification or pending link completion
- active
- disabled
- removed
- review-required or conflict-bound where applicable

**Session state** must distinguish at least:
- issued
- active
- refreshed / rotated where supported
- revoked
- expired
- invalidated / contained

Hidden manual notes or ad hoc support flags are non-canonical substitutes for explicit state.

## Lifecycle / Workflow Model

### Canonical Entry Flow
1. actor initiates approved access flow
2. provider or auth evidence is validated
3. backend resolves the evidence to one canonical account outcome
4. session issuance is allowed only if identity, auth-path, and policy conditions pass
5. downstream workspace, authorization, and entitlement evaluation occur after valid authenticated runtime state exists

### Continuity Flow
Provider changes, recovery actions, wallet-link changes, or product changes must preserve the same canonical account whenever the actor is the same actor under approved continuity rules.

### Recovery Flow
Recovery restores access to the same canonical account. It must not create a silent substitute identity.

### Restriction / Suspension Flow
Restriction, suspension, or higher-order security posture may limit or terminate otherwise active sessions and access paths.

### Conflict / Remediation Flow
If the platform cannot safely choose one canonical outcome, it must create an explicit reviewable case instead of mutating truth silently.

## Invariants

1. each actor MUST resolve to exactly one canonical `account_id` except during explicit operator-managed remediation or review
2. products MUST NOT create alternative canonical identity layers
3. linked authentication methods MUST be modeled as access paths rather than identities
4. sessions MUST be treated as temporary runtime truth and MUST NOT be treated as durable identity truth
5. provider outputs MUST be normalized by backend-owned rules before they affect canonical account or access-path truth
6. authentication MUST occur before workspace, authorization, and entitlement evaluation
7. account-state, auth-path-state, recovery-state, and risk-state precedence over session continuation MUST be preserved
8. wallet-aware context MAY attach to the account but MUST NOT replace canonical account identity
9. derived views, public surfaces, and reports MUST NOT become mutation owners
10. ambiguous cases MUST default to explicit review rather than silent merge, silent reassignment, or silent fragmentation

## Functional Rules

### Canonical Actor Rule
The platform MUST use `account_id` as the only durable internal anchor for cross-product actor continuity.

### Linked Access Rule
Each approved login method MUST be represented as a durable path to one canonical account, not as a second identity record.

### Provider Mapping Rule
Provider-backed methods MUST use stable provider-scoped subject identifiers as mapping keys under FUZE-owned normalization logic.

### Email Rule
Email MAY be used as a signal, contact, or review hint, but MUST NOT be treated as sufficient by itself for silent canonical account merge or reassignment.

### Session Rule
A session MUST only exist after successful authentication and required policy checks. Session existence alone proves neither workspace authority nor product entitlement.

### Ordering Rule
FUZE services MUST preserve the following evaluation ordering:
1. resolve canonical account outcome
2. validate allowed access-path outcome
3. establish session or deny issuance
4. resolve workspace or organization context where needed
5. evaluate authorization
6. evaluate entitlement or capability gating
7. execute or deny requested action

### Frontend Rule
Frontends may initiate flows and render outcomes, but canonical truth for identity, provider resolution, linked-login durability, recovery completion, and session validity must remain backend-owned.

### Product Flexibility Rule
Different products may expose different approved entry providers, but all must converge into the same canonical account/access/session model.

### Degraded-Mode Rule
When adjacent services or derived systems are degraded, FUZE must preserve canonical backend semantics and must not invent fallback identity truth from stale caches or convenience surfaces.

## Permission / Access Considerations

This canonical layer does not replace the authorization model, but it binds it through the following rules:

- login success is not final authorization
- valid session presence is not final permission
- workspace scope must be known before scoped permission evaluation
- permission evaluation must remain distinct from entitlement evaluation
- privileged identity or session mutation paths require stronger authorization than ordinary product actions

## Entitlement Considerations

Entitlement is downstream of account, access-path, session, and often workspace context.

- entitlement systems must consume canonical subject anchors rather than invent them
- entitlement state must not be written into identity or session truth as a substitute
- loss of entitlement does not redefine canonical identity
- identity recovery should preserve entitlement subject continuity unless a higher-order policy requires otherwise

## API / Contract Implications

Downstream API specifications MUST preserve the following boundaries:

- identity APIs own canonical account semantics and identity-domain outcomes
- auth/session APIs own linked-login orchestration, auth challenges, session issuance, refresh, revoke, inspect, and containment
- workspace APIs own scope and membership context
- authorization APIs own grant structure and action evaluation
- entitlement APIs own eligibility and capability gating
- wallet-aware APIs own wallet-link truth and wallet-derived context

Sensitive API paths MUST require, where relevant:

- actor attribution
- correlation identifiers
- idempotency for repeatable side effects
- explicit reason codes for privileged mutations
- audit lineage emission or durable references
- policy-version or policy-reference capture where a policy materially influenced the outcome

## Event / Async Implications

Material account/access/session actions SHOULD emit durable post-commit events or equivalent audited notifications, including where applicable:

- account created or activated
- linked login added, disabled, removed, restored, or review-blocked
- session issued, rotated, revoked, invalidated, expired, or globally contained
- recovery initiated, approved, completed, rejected, or cancelled
- conflict/remediation case opened, escalated, resolved, or reversed

Async workflows affecting canonical truth MUST be deterministic, idempotent, replay-safe, and correlation-linked. Consumers of these events MUST treat them as downstream notifications, not as alternate truth owners.

## Data Model / Storage Implications

This canonical layer requires the following data discipline:

- `account` remains the durable actor anchor
- linked-login records remain explicit and durable
- runtime session records remain distinct from account identity records
- wallet links remain distinct from canonical identity
- recovery and conflict records remain explicit rather than implicit
- lineage for security-sensitive state changes should be preserved rather than hidden by destructive overwrite
- projections and read models must remain regenerable from canonical state

## Read Model / Projection / Reporting Rules

Read models, projections, support views, exports, analytics tables, and public reports are allowed only if they preserve the following rules:

- they are clearly derived from canonical sources
- they are not direct mutation owners
- they can be regenerated or reconciled from owner truth
- they do not silently coalesce multiple accounts into one actor without governed remediation
- they do not suppress active conflict, restriction, or remediation posture where that posture materially affects interpretation
- public-facing simplifications must not imply stronger truth ownership than they actually have

## Security / Risk / Abuse Controls

The account/access/session foundation is security-critical. At minimum, the platform MUST preserve:

- server-side authoritative validation of identity and session state
- step-up or recent-auth posture for high-risk mutations where policy requires it
- targeted containment and global containment capabilities
- account-state and risk-state precedence over ordinary session continuation
- continuity-aware controls for adding, removing, or replacing access paths
- bounded operator control paths for security-sensitive corrections
- prevention of frontend-local or product-local shadow identity/session truth

## Boundary Violation Detection / Non-Canonical Patterns

The following are non-canonical and forbidden unless later explicitly approved in a narrower governing document:

- per-product platform identity roots
- silent email-based account merge
- silent reassignment of provider subjects between accounts
- session treated as permanent identity or as final authorization
- wallet possession treated as full platform identity
- reports or support dashboards treated as authoritative mutation surfaces
- provider callbacks directly mutating account truth without normalized backend resolution
- entitlement flags used as identity or session substitutes
- operator-side undocumented “identity surgery” outside policy-bound pathways

The platform SHOULD detect and surface reviewable evidence for likely boundary violations, including:

- one provider subject appearing on multiple accounts
- apparent duplicate platform identities created from product-local roots
- mismatches between canonical truth and derived support/reporting surfaces
- privileged mutations lacking reason codes, correlation IDs, or audit lineage

## Audit / Traceability Requirements

FUZE must be able to reconstruct, at minimum:

- which canonical account represented the actor
- which access path was used or attempted
- which session lineage existed and why it ended
- what policy, restriction, recovery, or security event influenced issuance or continuation
- which privileged actor or system performed sensitive corrections
- how identity, access-path, session, scope, and later authorization outcomes connected in one attributable chain

Operator and admin actions affecting this domain MUST be reason-coded, actor-attributed, timestamped, correlation-linked, and durably auditable.

## Failure Handling / Edge Cases

### Provider Outage
The canonical account still exists. Only one access path is impaired.

### Lost Access to One Method
If another approved method or recovery path exists, access should continue to the same canonical account.

### Duplicate Sign-Up or Ambiguous Provider Result
The platform must explicitly choose among link, bootstrap, deny, or review-required outcomes. It must not silently merge or silently fragment.

### Workspace Change
Workspace join/leave/suspension changes operating context, not canonical identity.

### Wallet Link Change
Wallet change alters attached wallet-aware context, not canonical identity.

### Session Present but Account Restricted
Restriction or higher-order security posture wins. The session must not behave as ordinarily valid access.

### Reporting Mismatch
Canonical records remain authoritative. Derived views must be corrected or regenerated.

### Degraded Adjacent Services
The platform must fail closed for high-risk access decisions, preserve durable canonical truth, and avoid destructive fallback assumptions.

## Operational Considerations

Implementations must preserve:

- deterministic backend account-resolution behavior
- explicit correlation across identity, access-path, session, and privileged-control actions
- replay-safe handling of provider callbacks, recovery steps, and containment requests
- durable auditability even during degraded runtime conditions
- safe support and operator workflows that never bypass owner validation rules
- controlled caching only where stale data cannot mutate or overrule canonical truth

## Migration / Compatibility / Supersession Considerations

- future provider expansion must attach to the shared canonical account/access/session model
- legacy product-local user abstractions may be temporarily supported but must not remain canonical
- legacy flows implying login success equals full platform access are superseded
- legacy flows implying providers, sessions, or wallets are alternate identity roots are superseded
- migrations must preserve lineage, continuity, auditability, and deterministic mapping outcomes
- compatibility layers may preserve transport or UX differences temporarily, but they must not weaken canonical semantics

## Implementation-Contract Guardrails

Downstream implementations MUST preserve all of the following:

1. `account_id` remains the durable actor anchor
2. linked logins remain access paths rather than identities
3. provider resolution remains backend-owned, conservative, deterministic, and auditable
4. sessions remain distinct from identity, workspace, authorization, entitlement, and wallet truth
5. account-state, recovery-state, and risk-state precedence over session continuation is preserved
6. ambiguous cases default to explicit review rather than silent auto-resolution
7. product-local user abstractions remain downstream of canonical account identity
8. operator actions remain bounded, reason-coded, policy-constrained, and audited
9. derived views remain derived and regenerable
10. degraded runtime conditions must not cause hidden semantic downgrades or truth substitution
11. idempotency and replay safety must be preserved for side-effecting account/access/session workflows
12. downstream docs and teams must not optimize away explicit state, lineage, or reason capture where those elements protect continuity, security, financial integrity, or auditability

## Downstream Execution Staging

This canonical layer implies the following preferred execution order for downstream implementation-contract work:

1. stabilize account identity semantics
2. stabilize linked-login and provider-resolution rules
3. stabilize session lifecycle and containment rules
4. stabilize recovery and remediation rules
5. integrate workspace and authorization sequencing
6. integrate entitlement and capability gating
7. integrate wallet-aware participation layers
8. build support, reporting, and public read models

## Required Downstream Specs / Contract Layers

This specification directly requires compatible downstream refinement and implementation-contract work in:

- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
- `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
- `KEY_MANAGEMENT_AND_USER_RECOVERY_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`

## Canonical Examples / Anti-Examples

### Canonical Example 1 — Many Providers, One Person
A user first enters one FUZE product with Google, later adds Telegram in another FUZE product, and still resolves to the same `account_id`.

### Canonical Example 2 — Workspace Change Without Identity Change
A user joins, leaves, or switches workspaces while keeping the same account.

### Canonical Example 3 — Wallet-Aware Context Without Identity Replacement
A user links a wallet for holder-aware or participation-aware features while the account remains the identity root.

### Canonical Example 4 — Security Containment Without Identity Loss
A user’s account is temporarily restricted after a trust event, sessions are invalidated, and later access is restored to the same canonical account after review.

### Anti-Example 1 — Product-Local Identity Root
A product creates its own canonical `user_id` and treats it as the platform identity.

### Anti-Example 2 — Silent Email Merge
A provider callback returns a matching email and the platform silently merges two accounts.

### Anti-Example 3 — Session Equals Authorization
A product assumes a valid session means the actor automatically has workspace authority and product capability.

### Anti-Example 4 — Wallet as Universal Identity
A product treats wallet possession alone as the complete FUZE identity model.

### Anti-Example 5 — Reporting Surface as Truth Owner
A support dashboard is used to directly correct canonical account state without reason codes, audit lineage, and owner validation.

## Dependencies / Cross-Spec Links

This specification depends on:

- `REFINED_SYSTEM_SPEC_INDEX.md`
- `DOCS_SPEC_INDEX.md`
- `SYSTEM_SPEC_INDEX.md`
- `API_SPEC_INDEX.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `WALLET_AWARE_USER_SPEC.md`

This specification directly governs or materially informs:

- `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
- `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
- `KEY_MANAGEMENT_AND_USER_RECOVERY_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- community-facing and product-integration architecture materials

## Explicitly Deferred Items

The following are intentionally deferred to adjacent or downstream specifications:

- exact provider inventory and per-provider onboarding logic
- exact credential, MFA, and recent-auth implementation design
- exact session transport and client-storage implementation
- exact recovery proof mechanics and support procedures
- exact workspace membership and role-catalog implementation detail
- exact entitlement catalogs and billing integrations
- exact wallet-auth scope if adopted for broader login use
- exact admin-console workflow screens and approval UX
- exact observability dashboards and alert thresholds

## Final Normative Summary

FUZE MUST operate one canonical account-centered identity model for the entire platform.

That model requires all downstream layers to preserve the following:

- one durable `account_id` as the actor anchor
- linked logins as access paths rather than identities
- sessions as temporary authenticated runtime state
- authentication before scope, authorization, and entitlement evaluation
- account, recovery, and risk posture overriding ordinary session continuation when required
- wallet-aware context remaining attached rather than identity-rooting
- reports, support views, and public surfaces remaining derived rather than canonical
- explicit, auditable handling of recovery, conflict, containment, and privileged correction

Any downstream implementation, API, product, workflow, or support/control surface that weakens these rules is non-compliant with the FUZE canonical account/access/session architecture.

## Quality Gate Checklist

- [x] canonical owner is explicit for each material truth family
- [x] mutation boundaries are explicit
- [x] adjacent boundaries are explicit
- [x] truth classes are explicit
- [x] conflict-resolution rules are explicit where needed
- [x] default decision rules are explicit where ambiguity could arise
- [x] non-canonical patterns are called out clearly
- [x] operator/admin override paths are bounded and audited
- [x] read-model, cache, reporting, and projection rules are explicit
- [x] on-chain versus off-chain adjacency is explicit where relevant
- [x] failure and degraded-mode behavior is explicit
- [x] downstream implementation guardrails are explicit
- [x] dependencies and downstream impacts are explicit
- [x] non-goals and deferred items are explicit
- [x] the document is strong enough to support backend, API, data, runtime, support, and audit implementation without inventing contradictory semantics
