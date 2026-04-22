# IDENTITY_AND_ACCOUNT_SPEC

## Document Metadata

- Document Name: `IDENTITY_AND_ACCOUNT_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Version: 1.0.0
- Effective Date: 2026-04-21
- Last Updated: 2026-04-21
- Reviewed On: 2026-04-21
- Document Owner: FUZE Platform Identity and Account Domain
- Approval Authority: FUZE Platform Architecture and Governance Authority
- Review Cadence: Quarterly or upon material identity-model change
- Governing Layer: Platform core / identity and account
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, security engineering, support operations, audit, governance, API design, reporting, platform operations
- Primary Purpose: Define the canonical identity and account architecture of FUZE, including what the account is, what the identity domain owns, how canonical identity continuity works across products and providers, how account lifecycle and recovery must behave, and how identity must remain distinct from authentication, sessions, workspaces, wallets, authorization, and product-local profiles
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
- Primary Downstream Dependents:
  - `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
  - `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
  - `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
  - `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
  - `KEY_MANAGEMENT_AND_USER_RECOVERY_SPEC.md`
  - `WORKSPACE_AND_ORGANIZATION_SPEC.md`
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
  - `WALLET_AWARE_USER_SPEC.md`
  - `AUTH_IDENTITY_API_SPEC.md`
  - `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - product integration specifications
- Supersedes: Earlier non-refined or less explicit identity/account writeups to the extent they conflict within this document’s scope
- Superseded By: Not yet known
- Related Decision Records: Not yet known
- Canonical Status Note: This document is the governing FUZE identity-domain system specification for canonical account identity and identity-domain ownership. Downstream specs MUST NOT redefine the semantics established here.
- Implementation Status: Normative architecture baseline; downstream API, workflow, and service contracts must conform
- Approval Status: Drafted for refined-system inclusion; formal approval record not yet attached
- Change Summary:
  - normalized identity-domain ownership and mutation boundaries
  - tightened truth-class taxonomy and conflict-resolution rules
  - clarified identity versus auth/session versus workspace versus wallet boundaries
  - strengthened lifecycle, recovery, remediation, and audit requirements
  - added explicit implementation-contract guardrails and non-canonical patterns

---

## Purpose

This specification defines the canonical identity and account architecture of the FUZE platform.

Its purpose is to establish the durable source-of-truth model for identity across the FUZE ecosystem by answering the following questions clearly and normatively:

- what is the canonical identity of a person or actor in FUZE
- what does the identity and account domain own
- how canonical identity remains continuous across products, providers, workspaces, wallets, and future platform capabilities
- how account lifecycle, recovery, merge/remediation, and restriction behave
- what identity is, and what is explicitly not identity
- how downstream APIs, products, workspaces, reports, and security controls must consume identity without redefining it

This document is foundational because FUZE is a multi-product platform. Identity therefore cannot be product-local, provider-local, wallet-local, reporting-owned, or frontend-owned. It must be one shared platform capability with durable lifecycle rules, explicit ownership, strong auditability, and constrained correction paths.

---

## Scope

This specification governs:

- the canonical identity model for FUZE
- the definition of the account as the durable identity anchor
- account lifecycle and canonical account states
- account continuity across products and provider changes
- identity-domain ownership, authority, and mutation boundaries
- the distinction between identity, authentication methods, sessions, workspaces, wallets, authorization, entitlements, and product-local profiles
- duplicate-account prevention, merge/remediation posture, and recovery posture
- identity-level security-sensitive actions
- the minimum canonical entity and state-model requirements for identity
- downstream integration rules for products, APIs, reporting, control-plane operations, and security systems

This specification does not define:

- detailed session issuance, refresh, rotation, or revocation mechanics
- detailed workspace membership or role semantics
- detailed wallet verification mechanics
- detailed provider-specific protocol implementation
- detailed MFA orchestration
- detailed support workflow procedures
- detailed legal-identity, KYC, or compliance frameworks unless later added explicitly
- exact database topology, service split, or token transport model

Those areas belong in downstream specifications.

---

## Out of Scope

This specification is explicitly out of scope for:

- product-local profile schemas
- UI login-experience design
- exact OAuth, OIDC, or messaging-provider callback implementation detail
- exact password or credential-storage implementation detail
- exact role matrices and permission catalogs
- exact wallet-signature challenge mechanics
- exact retention, redaction, or privacy-policy text
- exact public profile rendering rules beyond ownership constraints
- marketing or community education language beyond architectural meaning

---

## Design Goals

The design goals of the FUZE identity and account model are:

1. create one canonical actor identity across the FUZE ecosystem
2. preserve continuity across all current and future products
3. separate identity truth from authentication method mechanics
4. support multiple approved linked login methods without fragmenting identity
5. support workspaces, wallet-aware participation, billing, credits, and entitlements without collapsing them into one ambiguous identity layer
6. make identity durable enough for auditability, commercial continuity, security controls, and governance-aware actions
7. make recovery, restriction, merge/remediation, and closure explicit and controlled
8. prevent products from redefining platform identity semantics
9. preserve future provider flexibility without changing the core identity rule
10. keep identity easy to reason about across APIs, reports, support operations, and security workflows

---

## Non-Goals

This specification is not intended to:

- define product-local user tables as canonical platform identity
- treat login providers as separate user identities
- treat sessions as durable identity truth
- treat workspaces as replacements for user identity
- make wallet addresses the sole identity model
- let products create hidden alternate canonical actor IDs
- let reports, dashboards, exports, caches, or search indexes become identity owners
- define a generalized legal-person identity framework beyond the current platform account model

---

## Core Principles

### 1. Canonical Account Principle
One human or system actor MUST map to one canonical FUZE account identity, except during explicit operator-managed remediation or conflict review.

### 2. Identity Versus Access Principle
Identity belongs to the account. Access belongs to linked authentication methods. Runtime presence belongs to the session.

### 3. Product Consumption Principle
Products consume canonical identity and MAY extend it locally, but they MUST NOT redefine it.

### 4. Workspace Separation Principle
An account is the actor identity. A workspace is a collaborative and operational context layered on top of the account, not a replacement for it.

### 5. Wallet Boundary Principle
Wallets MAY be linked to an account for participation-aware behavior, but wallet linkage does not become canonical identity truth.

### 6. Continuity Principle
A person SHOULD remain the same person in FUZE even when their preferred login method, workspace relationship, product usage, or wallet-aware participation changes over time.

### 7. Controlled Recovery Principle
Recovery restores access to the same canonical account. It MUST NOT silently create a second account or orphan linked platform relationships.

### 8. Explicit Remediation Principle
Conflict, duplicate-account, or merge cases MUST be explicit, auditable, and policy-controlled rather than hidden inside product logic, provider callbacks, or support shortcuts.

---

## Canonical Definitions

### Account
The durable canonical FUZE identity record for a person or actor at the platform level.

### Canonical Identity
The authoritative answer to the question: “Who is this actor in FUZE?”

### Linked Authentication Method
A durable relationship between a canonical account and one approved authentication method or provider-specific subject.

### Authentication Method
An approved access path by which the actor proves control over the canonical account.

### Session
Temporary authenticated runtime state issued after successful authentication and policy evaluation. A session is not canonical identity truth.

### Workspace Membership
A relationship between an account and a workspace or organization context.

### Wallet Link
A relationship between an account and one or more wallets used for token-aware or participation-aware behavior.

### Product Profile Extension
A product-local representation derived from the canonical account for product UX or settings, without becoming a new canonical identity.

### Account Continuity
The property that FUZE can continue recognizing the same actor across provider changes, product usage changes, workspace changes, and wallet-link changes.

### Identity Conflict
A case in which available provider, email, wallet, support, or historical evidence does not safely and automatically resolve to a unique canonical account.

### Recovery Case
A controlled process for restoring access to a canonical account without redefining identity ownership.

### Identity Remediation
A bounded operator-managed or workflow-managed corrective process used when identity continuity, provider binding, recovery, or duplicate-account handling cannot safely be resolved through ordinary user flows.

---

## Truth Class Taxonomy

This domain MUST distinguish the following truth classes.

### 1. Canonical Identity Truth
The `account` and its identity-domain-governed lifecycle, uniqueness, and continuity semantics are canonical identity truth.

### 2. Policy Truth
Approval policies, risk rules, provider onboarding policy, recovery policy, and admin-control policy are policy truth. They constrain identity actions but are not themselves the identity record.

### 3. Runtime Truth
Session state, recent-auth posture, active authentication state, and transient login workflow state are runtime truth. Runtime truth is subordinate to canonical identity truth and policy truth.

### 4. Storage Truth
Durable identity-domain records such as account, linked auth method, recovery case, conflict case, and identity-operation lineage are storage truth for the identity domain.

### 5. Provider-Input Truth
Provider claims, callback payloads, issuer-subject pairs, messaging-provider subjects, and similar external inputs are provider inputs. They are evidence and mapping inputs, not canonical identity truth.

### 6. Derived Read-Model Truth
Read models, support views, analytics views, public profile projections, and search projections are derived. They MAY summarize canonical identity state but MUST NOT become the write owner.

### 7. Reporting Truth
Reporting exports and analytical summaries may reflect identity-related counts or attributes but MUST remain downstream and correctable from canonical identity truth.

### 8. Wallet-Linked Context Truth
Wallet links are canonical within the wallet-aware domain, but they remain attached participation context, not canonical identity truth.

### 9. Authorization Truth
Workspace membership, organization scope, role assignment, permission evaluation, and entitlement state are separate truths owned by adjacent domains. Identity supplies the subject anchor.

---

## Architectural Position in the Spec Hierarchy

This document sits below:

- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`

and above:

- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
- `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
- `KEY_MANAGEMENT_AND_USER_RECOVERY_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `WALLET_AWARE_USER_SPEC.md`

This document defines the identity domain and the account model. It does not absorb downstream ownership for sessions, workspaces, authorization, or wallet-link execution.

---

## System Boundaries

The identity and account domain MUST own:

- canonical account creation
- canonical account identifiers
- account uniqueness and anti-fragmentation rules
- account lifecycle states and lifecycle transitions
- linked-auth durability at the identity layer
- identity conflict and remediation state
- recovery-case ownership at the identity layer
- canonical identity read models
- merge, closure, deactivation, and restriction semantics for identity-domain truth
- identity-related audit lineage generation in coordination with the audit domain

The identity and account domain MUST NOT own:

- session issuance and active-session runtime truth
- workspace membership truth
- role or permission truth
- entitlement truth
- wallet-link truth or token-balance truth
- billing, credits, payout, or governance truth
- product-local profile truth beyond any fields explicitly designated as identity-domain-owned

---

## Adjacent Boundaries

### Identity versus Authentication
Identity answers who the actor is. Authentication answers how the actor proved access. Authentication MUST NOT become canonical identity.

### Identity versus Session
Sessions are temporary runtime state. They are revocable, expirable, and subordinate to account state, auth-method state, and security/risk posture.

### Identity versus Workspace
An account exists independently of any one workspace. Workspace existence MUST NOT be required for canonical account identity to exist.

### Identity versus Authorization and Entitlements
Authorization, role evaluation, permission evaluation, and entitlements are resolved after authentication succeeds and after account and workspace context are known. The identity layer provides the subject; it does not own those downstream decisions.

### Identity versus Wallet
A wallet link is attached to an account. Token-balance truth comes from chain truth, not from the account record. Wallet-aware participation MAY influence product behavior but MUST NOT replace identity.

### Identity versus Product Profiles
Products MAY maintain product-local profile data, settings, or local extensions. These remain downstream of the canonical account and MUST NOT redefine platform identity.

### Identity versus Reporting and Support Views
Reporting, analytics, support views, and search indexes MAY summarize identity state. They MUST NOT become identity mutation owners or hidden sources of truth.

---

## Conflict Resolution Rules

When multiple layers disagree, the platform MUST resolve conflicts in the following order unless a higher-order platform policy explicitly states otherwise:

1. canonical identity-domain records
2. explicit policy and security constraints
3. validated provider-input evidence within approved resolution rules
4. runtime session state
5. derived views, projections, dashboards, or product-local caches

Specific conflict rules:

- provider profile data MUST NOT override canonical account ownership
- email similarity MUST NOT justify silent account merge
- wallet linkage MUST NOT justify identity reassignment
- stale session presence MUST NOT override account suspension or restriction
- product-local user tables MUST NOT override canonical account identity
- support tooling displays MUST NOT be treated as authoritative if they diverge from canonical identity records

---

## Default Decision Rules

Where ambiguity exists, the following defaults apply:

1. default owner of person-level identity semantics: Identity and Account Domain
2. default actor anchor: `account_id`
3. default interpretation of provider subject: evidence for mapping, not canonical identity
4. default interpretation of email: contact and hint, not sole canonical resolution key
5. default interpretation of session: temporary runtime access, not durable identity
6. default interpretation of wallet: linked participation context, not identity root
7. default interpretation of product-local profile: extension artifact, not platform identity
8. default interpretation of reporting mismatch: canonical identity domain remains authoritative
9. default resolution for ambiguous duplicate-account evidence: create explicit conflict/remediation path rather than silently merging or silently fragmenting
10. default resolution for high-risk admin action: require reason code, audit, and policy-bound workflow

---

## Roles / Actors / Entities

### Human Actor
A natural person using one or more FUZE products.

### System Actor
An internal service or automated workflow acting under explicit service identity and policy scope.

### Platform Identity and Account Domain
The canonical owner of identity semantics, account records, identity lifecycle, durable linked-auth relationships at the identity layer, recovery posture, and conflict/remediation posture.

### Auth / Session Domain
The owner of session issuance, runtime authenticated state, refresh/rotation, revocation, and related runtime access mechanics.

### Workspace / Organization Domain
The owner of collaborative scope, workspace membership, organization structure, and related context.

### Role / Permission / Access-Control Domain
The owner of authorization truth after identity and context resolution.

### Wallet-Aware User Domain
The owner of wallet-link truth and wallet-aware participation context.

### Product Domains
Consumers of canonical identity that MAY create product-local extensions but MUST NOT redefine identity semantics.

### Support / Admin Control Plane
Privileged operational surfaces that MAY initiate policy-bound corrective actions but MUST NOT become identity truth owners.

---

## Ownership Model

### Canonical Owner
The canonical owner of identity in FUZE is the Platform Identity and Account Domain.

### Domain-Owned Truth Families
The identity domain owns:

- `account`
- identity-level lifecycle state
- identity uniqueness rules
- durable linked-auth relationship ownership at the identity layer
- identity conflict cases
- recovery cases at the identity layer
- merge and closure semantics
- canonical identity read models

### Non-Owned Adjacent Truth Families
The identity domain does not own:

- `auth_session`
- workspace membership records
- role and permission records
- wallet-link records
- token balances
- commercial ledgers
- payout records

### Mutation Ownership
Only owner-controlled domain pathways MAY mutate canonical identity truth. Products, frontends, reports, caches, search indexes, and general-purpose support tooling MUST NOT mutate identity truth directly.

---

## Authority / Decision Model

### Identity Domain Authority
The identity domain MAY:

- create accounts
- mutate account lifecycle state through explicit owner-controlled pathways
- attach and detach linked auth methods where the identity domain is the canonical owner of the durable relationship
- expose canonical identity reads
- coordinate recovery and merge/remediation flows
- publish identity-domain events after canonical commits

### Product Authority Limits
Products MAY:

- read canonical identity
- create product-local profile extensions
- create product-local preferences
- request access checks against canonical identity and workspace state

Products MUST NOT:

- create alternate platform user IDs
- treat product-local user tables as platform identity truth
- attach provider identities outside owner-controlled identity boundaries
- reinterpret wallet addresses as full identity
- mutate canonical account lifecycle directly

### Admin / Operator Authority Limits
Admin and support systems MAY trigger reviewable identity actions through bounded control-plane pathways. They MUST NOT bypass domain validation, audit, reason-code, policy, or idempotency requirements.

---

## State Model

### Canonical Entities
At minimum, the canonical model SHOULD include durable representations for:

- `account`
- `account_auth_method` or equivalent canonical auth-link entity
- `account_recovery_case`
- `identity_conflict_case`
- `identity_operation_request`
- `identity_audit_lineage_reference` or equivalent audit relationship

### Minimum Expressible Account States
At minimum, the platform MUST support the semantic states:

- `pending_setup`
- `active`
- `restricted`
- `suspended`
- `deactivated`
- `merged`
- `closed`

Implementation detail MAY include more granular internal states, but these meanings must remain expressible.

### Linked Auth States
At minimum, linked auth methods SHOULD support states such as:

- `pending_verification`
- `active`
- `disabled`
- `removed`

### Recovery / Conflict States
Recovery and conflict/remediation records MUST have explicit durable states. Hidden manual notes, one-off support fields, or implicit transient logic are non-canonical.

---

## Lifecycle / Workflow Model

### Account Lifecycle
1. pending bootstrap or pending setup
2. active
3. restricted
4. suspended
5. deactivated or closed
6. merged as an exceptional remediation outcome

### Identity Entry Flow
An actor enters through an approved identity-entry path, such as email/password, Google, Telegram, Line, Facebook, future federation, or future approved wallet-based proof. Every entry path MUST ultimately resolve to a canonical account object.

### Linked Access Flow
A linked authentication method is attached to the account as a durable access relationship. It is a security-sensitive asset, not a second identity.

### Recovery Flow
Recovery restores access to the same canonical account. It MUST verify sufficient proof, remain auditable, and preserve workspaces, wallet links, product relationships, commercial continuity, and audit lineage.

### Conflict / Remediation Flow
If evidence does not safely resolve to a unique canonical account, the platform MUST open an explicit reviewable conflict/remediation path. It MUST NOT silently merge or silently fragment.

### Restriction / Suspension Flow
Identity restriction or suspension MAY occur due to security, abuse, payment risk, policy, or controlled administrative action. Such state MUST take precedence over ordinary session continuation.

---

## Invariants

1. each actor MUST resolve to exactly one canonical `account_id` except during explicit operator-managed remediation
2. products MUST NOT create alternative canonical identity layers
3. linked auth methods MUST be modeled as access paths to the canonical account
4. sessions MUST be treated as temporary authenticated runtime state and MUST NOT be treated as permanent identity truth
5. provider flows MUST normalize into one backend-owned account-resolution model
6. product-specific login choice MUST NOT create product-specific identity truth
7. wallet-aware context MAY attach to the account but MUST NOT replace canonical account identity
8. account state, risk state, and auth-method state MUST take precedence over session continuation
9. lifecycle transitions MUST be durable and auditable
10. reports, dashboards, exports, caches, and projections MUST NOT become identity owners

---

## Functional Rules

### Canonical Identity Rule
The platform MUST use `account_id` as the only durable internal anchor for cross-product identity continuity.

Secondary identifiers such as provider subjects, email addresses, wallet addresses, usernames, and product-local profile references MUST be treated as attached relationships or attributes, not as replacements for `account_id`.

### Identity Entry Rule
Every approved entry path MUST resolve into the shared identity model rather than creating product-local identity variants.

### Provider Subject Rule
Each provider-backed method MUST use a provider-scoped stable subject identifier as the durable mapping key.

### Email Non-Canonical Rule
Email MAY be used as a hint, recovery contact, or review signal, but SHOULD NOT be the sole canonical matching key for provider login resolution.

### No Silent Fragmentation Rule
The platform MUST NOT silently:

- create duplicate identities for the same actor
- merge accounts without explicit controlled rules
- overwrite provider links into a different account
- reassign an access method in a way that breaks audit continuity

### Product Extension Rule
Product-local settings and profiles MUST remain child or extension artifacts of the canonical account, not replacements for it.

### Frontend Boundary Rule
Frontend applications MAY initiate identity flows and render outcomes, but they MUST NOT own canonical truth for:

- account identity
- provider identity resolution
- recovery completion
- restriction state
- linked-provider durability

---

## Permission / Access Considerations

This specification does not replace the authorization model, but it imposes the following rules:

- successful authentication does not prove workspace membership
- successful authentication does not prove role or permission grants
- successful authentication does not prove entitlement
- account identity is the subject against which later scoped access decisions are made
- identity-domain admin and control actions require stronger authorization and audit posture than ordinary user flows
- internal service access to identity mutation paths must be explicit, least-privileged, and policy-scoped

---

## Entitlement Considerations

Identity anchors entitlement evaluation but does not own entitlement semantics.

- entitlement systems MUST consume canonical account identity and any required workspace context
- entitlement systems MUST NOT redefine identity ownership
- identity correction or recovery MUST preserve entitlement subject continuity unless a controlled policy says otherwise
- product entitlements derived from identity and workspace context remain downstream and reversible from canonical truth

---

## API / Contract Implications

Downstream API specifications MUST preserve the following:

- identity APIs own canonical account creation, lifecycle state, provider-link durability at the identity layer, recovery posture, and provider-to-account resolution outputs
- session APIs own session issuance, refresh, revocation, and runtime authenticated state
- authorization APIs own workspace scope resolution, role evaluation, permission evaluation, and capability grants
- wallet-aware APIs own wallet-link truth and wallet verification

No API surface may blur these boundaries.

Sensitive identity APIs MUST require:

- explicit actor identity
- correlation IDs
- idempotency where side effects could repeat
- reason codes for privileged mutations
- auditable state transitions
- policy-version or policy-reference capture where applicable

---

## Event / Async Implications

The identity domain SHOULD publish durable events after canonical commits for material actions such as:

- account created
- account activated
- account restricted or suspended
- linked auth method added, disabled, restored, or removed
- recovery initiated, approved, completed, rejected, or cancelled
- conflict detected, escalated, or resolved
- account merged, closed, or deactivated

Event consumers MUST treat these events as downstream notifications, not as permission to redefine canonical identity truth.

Async identity workflows MUST remain deterministic, auditable, idempotent, and replay-safe.

---

## Data Model / Storage Implications

This specification requires the following identity data-model discipline:

- `account` is canonical identity truth
- linked auth methods must remain durable canonical identity-domain relationships
- sessions must remain distinct from account records unless explicitly owned by a downstream auth/session schema
- wallet links must remain distinct from account identity
- product profile extensions must remain downstream derived or child records
- recovery, conflict, and remediation records must be durable and auditable
- projections and safe read models must not become write owners
- identity-related corrections should prefer explicit lineage and state transitions over hidden destructive rewrites

---

## Read Model / Projection / Reporting Rules

Read models, support views, analytics views, exports, dashboards, and search projections MAY summarize identity state. They are allowed only if they preserve the following rules:

- they must be clearly marked as derived
- they must be regenerable from canonical truth
- they must not become mutation owners
- they must not invent identity states that cannot map back to canonical semantics
- they must not silently coalesce multiple accounts into one identity without explicit governed rules
- they must not hide conflict or remediation posture where that posture materially affects interpretation

---

## Security / Risk / Abuse Controls

This identity model is also a security boundary.

At minimum, the following actions SHOULD be treated as security-sensitive:

- primary email change
- password reset
- linked-provider add or remove
- wallet-auth enablement if supported
- recovery completion
- account merge
- account deactivation or reactivation
- operator-driven identity remediation
- global session revoke request triggered from identity-side recovery or restriction events

Security and risk controls MAY suspend, restrict, or invalidate ordinary session continuation when the platform determines that additional protection is required. Identity-domain decisions and session-domain containment MUST remain coordinated but distinct.

---

## Boundary Violation Detection / Non-Canonical Patterns

The following patterns are non-canonical and forbidden unless a future approved spec explicitly and narrowly permits them:

- one account model per product
- one identity model for Web2 flows and another unrelated model for wallet-aware flows
- using provider profile data as canonical identity truth
- treating login success as automatic authorization success
- allowing sessions to outrank account or risk state
- allowing frontend logic to become source of truth for auth or identity state
- adding new providers through product-local one-off exceptions that bypass the shared model
- silently reassigning provider subjects between accounts
- using reports, exports, or support dashboards as hidden identity write surfaces
- letting support operators perform undocumented identity surgery outside policy-bound pathways

Boundary-violation detection SHOULD include alerting or reviewable evidence when:

- a provider subject appears linked to multiple accounts
- a product attempts to create alternate actor IDs that appear to mirror account identity
- derived views materially diverge from canonical records
- recovery or remediation actions occur without reason codes or audit lineage

---

## Audit / Traceability Requirements

FUZE MUST be able to determine:

- which canonical account represents the actor
- which auth methods are or were linked to that account
- which lifecycle transitions occurred and why
- how recovery, restriction, or merge/remediation was executed
- which products, workspaces, and wallet links were associated to the account over time
- whether a visible identity view is canonical or derived
- how provider-resolution and support decisions affected continuity

Auditability is a first-class property of the identity system, not an afterthought.

Identity-sensitive operator actions MUST be:

- reason-coded
- actor-attributed
- timestamped
- correlation-linked
- policy-referenced where applicable
- non-repudiably recorded in durable audit lineage

---

## Failure Handling / Edge Cases

### Provider Outage
If a provider is temporarily unavailable, the canonical account still exists. Only that access path is impaired.

### User Loses One Linked Method
The user MAY still access the same canonical account through another viable linked method if policy allows.

### Duplicate Sign-Up Attempt via Another Provider
The platform must determine whether to link to an existing account, create a new account, or open an explicit conflict/remediation path. It must not silently merge or silently fragment.

### Workspace Removed but Account Remains Valid
The account remains canonical and active unless separately restricted.

### Wallet Unlinked or Changed
The account remains canonical. Wallet-aware participation context changes, not account identity.

### Product-Local Profile Corruption
The canonical platform account remains authoritative even if a product extension profile is broken or missing.

### Suspicious Security Event
The account MAY be restricted or suspended without deleting the underlying identity.

### Reporting Mismatch
If a reporting surface shows stale or wrong identity information, the platform identity domain remains canonical.

### Provider Subject Already Linked Elsewhere
This MUST be treated as a conflict/remediation condition, not silently reassigned.

### Recovery Changes Access Paths
Recovery MAY restore access or replace viable auth paths according to policy, but it must preserve the same canonical account and the related audit lineage.

---

## Operational Considerations

Operational implementations MUST preserve:

- high-confidence account-resolution determinism
- strong correlation and request tracing for sensitive mutations
- replay safety for provider-callback and recovery transitions
- operator workflow containment for high-risk identity changes
- audit durability even during degraded runtime conditions
- safe fallback behavior when adjacent domains are unavailable

If a downstream service needed for enrichment is degraded, the identity domain MUST still preserve canonical identity semantics and MUST avoid inventing fallback identity truth from derived or stale sources.

---

## Migration / Compatibility / Supersession Considerations

- migrations must not silently transfer canonical identity ownership out of the identity domain
- compatibility layers may support older product-local user concepts temporarily, but canonical identity must remain platform-owned
- renamed or split identity-related entities must preserve continuity of authority and auditability
- if older implementations or documents imply looser product-local identity ownership, this refined specification supersedes them within its scope
- future provider expansion must occur through the shared identity model rather than product-local special cases

Migration plans MUST include replay-safe mapping, lineage preservation, and explicit rollback posture where account resolution or auth-link migration could affect continuity.

---

## Implementation-Contract Guardrails

Downstream implementations MUST preserve the following guardrails:

1. `account_id` remains the durable canonical actor anchor
2. identity mutations occur only through owner-controlled domain pathways
3. provider resolution is deterministic, auditable, and idempotent
4. sessions remain distinct from identity truth
5. account-state and risk-state precedence over session continuity is preserved
6. recovery restores access to the same canonical account rather than creating substitutes
7. product-local user abstractions remain downstream of the canonical account
8. admin or support overrides are bounded, reason-coded, policy-constrained, and audited
9. derived views remain derived and regenerable
10. ambiguous duplicate or merge cases default to explicit review rather than silent auto-resolution

These guardrails are mandatory and MUST NOT be optimized away for convenience.

---

## Downstream Execution Staging

Typical downstream staging SHOULD follow this order:

1. identity-domain canonical model and mutation boundaries
2. identity API contracts
3. provider-resolution and linked-login workflow contracts
4. session lifecycle and containment behavior
5. recovery and remediation workflows
6. workspace and authorization integrations
7. wallet-aware integrations
8. reporting, analytics, and support read models

This sequencing exists to keep identity semantics stable before downstream adaptation layers are finalized.

---

## Required Downstream Specs / Contract Layers

This specification requires compatible downstream refinement in at least the following areas:

- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
- `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
- `KEY_MANAGEMENT_AND_USER_RECOVERY_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `WALLET_AWARE_USER_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`

---

## Canonical Examples / Anti-Examples

### Canonical Example 1
A user signs into one FUZE product with Google, later adds Telegram in another FUZE product, and still resolves to the same `account_id`. This is canonical.

### Canonical Example 2
A user leaves one workspace but retains platform identity and can later join another workspace. This is canonical.

### Canonical Example 3
A user links a wallet for holder-aware participation without replacing their canonical account identity. This is canonical.

### Anti-Example 1
A product creates a product-local `user_id` and treats it as the platform’s identity root. This is forbidden.

### Anti-Example 2
A provider callback with a matching email silently merges two previously separate accounts. This is forbidden.

### Anti-Example 3
A support tool directly edits identity records without reason codes, policy reference, and durable audit lineage. This is forbidden.

### Anti-Example 4
A stale reporting table is treated as authoritative when it disagrees with canonical identity records. This is forbidden.

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
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`

This specification directly governs or materially informs:

- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
- `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
- `KEY_MANAGEMENT_AND_USER_RECOVERY_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `WALLET_AWARE_USER_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- product integration specs

---

## Explicitly Deferred Items

The following are intentionally deferred to downstream specifications:

- exact provider list and provider-specific resolution heuristics
- exact session token model and runtime session schema
- exact password, MFA, and step-up implementation detail
- exact support workflow procedures for recovery, merge, and remediation
- exact PII minimization and retention rules
- exact user-profile versus account-profile field boundaries
- exact admin console workflow design
- exact legal-identity or compliance-specific identity overlays

These do not weaken the canonical identity and account model established here.

---

## Final Normative Summary

FUZE MUST operate one canonical account-centered identity model for the entire platform.

That model requires:

- one canonical `account_id` as the durable actor anchor
- linked authentication methods as access paths rather than identities
- sessions as temporary runtime presence rather than durable identity truth
- workspaces, authorization, entitlements, wallets, and products as adjacent layers rather than replacements for identity
- explicit and auditable handling of recovery, conflict, merge, restriction, and closure
- strict prohibition on silent fragmentation, silent merge, and hidden alternate identity roots

Any downstream implementation, API, workflow, product, or support surface that weakens these rules is non-compliant with the FUZE canonical identity architecture.

---

## Quality Gate Checklist

- [x] canonical owner is explicit for every material identity truth family
- [x] mutation boundaries are explicit
- [x] adjacent boundaries are explicit
- [x] truth classes are explicit
- [x] conflict-resolution rules are explicit where needed
- [x] default decision rules are explicit where ambiguity could arise
- [x] non-canonical patterns are called out clearly
- [x] operator and admin override paths are bounded and audited
- [x] read-model, cache, reporting, and projection rules are explicit
- [x] wallet and adjacent domain responsibilities are explicit where relevant
- [x] failure and degraded-mode behaviors are explicit
- [x] downstream implementation guardrails are explicit
- [x] dependencies and downstream impacts are explicit
- [x] non-goals and deferred items are explicit
- [x] the document is strong enough to support backend, API, data, runtime, support, and audit implementation without inventing contradictory semantics
