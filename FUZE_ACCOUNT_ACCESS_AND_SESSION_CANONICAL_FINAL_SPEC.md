# FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC

## Document Metadata

- Document Name: `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
- Document Type: Canonical platform system specification
- Status: Active refined canonical spec
- Governing Layer: Platform core / canonical public system design for account, access, and session
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, security, support operations, audit, governance, API design, community-facing platform documentation
- Primary Purpose: Define the canonical FUZE platform rule set for account identity, approved access methods, linked login, and authenticated session behavior in a formal and stable way that remains readable while binding downstream implementation specs
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
  - `WALLET_AWARE_USER_SPEC.md`
- Primary Downstream Dependents:
  - `IDENTITY_AND_ACCOUNT_SPEC.md`
  - `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
  - `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
  - `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
  - `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
  - `AUTH_IDENTITY_API_SPEC.md`
  - `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
  - `WORKSPACE_AND_ORGANIZATION_SPEC.md`
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
  - `WALLET_AWARE_USER_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - product integration specifications

---

## Purpose

This document defines the canonical FUZE platform rule set for **account**, **authentication**, and **session**.

It is the formal bridge between:

- the more public, explanatory thesis layer, and
- the deeper domain and API specifications that implement FUZE identity and access in production.

This document exists to keep the platform model stable as FUZE grows across products, providers, workspaces, wallet-aware participation, and future enterprise or partner contexts.

It is intentionally more normative than the thesis document and more cross-domain than the implementation specs. It is the canonical “public system design” statement that all downstream identity, access, session, authorization, workspace, wallet-aware, and API designs must respect.

---

## Scope

This specification defines the platform rules for:

- canonical account identity
- approved authentication methods and linked login relationships
- authenticated session creation, continuation, and termination
- cross-product continuity of a person across FUZE services
- boundary separation between authentication and later authorization layers
- continuity and anti-fragmentation rules
- public-safe design requirements for account, access, and session behavior
- recommended ownership boundaries for identity, session, and authorization APIs
- minimum canonical data-model direction for the core account/access/session layer

This specification does **not** define the full implementation detail for:

- workspace membership resolution
- role and permission evaluation
- security and risk operations in full depth
- wallet subsystem behavior in full depth
- internal service-to-service contracts in full depth
- exact session transport mechanisms
- exact provider-resolution heuristics in ambiguous cases
- exact support workflow procedures
- exact MFA productization

Those areas belong in downstream specifications.

---

## Out of Scope

This document is explicitly out of scope for:

- product-local user model design
- exact provider SDK or OAuth implementation detail
- exact browser, mobile, or token transport implementation
- exact support tooling and admin UI design
- exact legal-identity, KYC, or compliance models
- exact contract or chain-integration implementation
- exact role matrices or entitlement catalogs
- exact database topology or service split

---

## Design Thesis

FUZE is a multi-product ecosystem, not a single isolated application.

Because of that, FUZE must treat identity as a platform capability, not as a product-local feature.

The platform design thesis is:

> A person should have one canonical FUZE account. That account may be accessed through one or more approved authentication methods. A session is then created as temporary authenticated runtime state. Workspace scope, role, permission, and product capability are resolved only after that authentication succeeds.

This thesis exists to preserve continuity for users who may:

- enter FUZE through different products
- use different login methods across those products
- change login preference over time
- join or leave workspaces
- later connect wallet-aware participation context
- continue using the platform without fragmenting identity

---

## Normative Language

The key words **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY** in this document describe platform requirements and design expectations.

---

## Core Definitions

### Account
An **account** is the canonical FUZE identity record for a person or actor at the platform level.

The account answers the question:

**Who is this actor in FUZE?**

The account is the durable anchor for continuity across:

- products
- workspaces and organizations
- audit history
- account lifecycle state
- recovery posture
- wallet-aware participation context
- future platform relationships

### Authentication Method
An **authentication method** is an approved access path that allows the actor to prove control over the canonical account.

Examples may include:

- email/password
- Google
- Telegram
- Line
- Facebook
- wallet-based proof, if explicitly approved by policy and implementation scope
- future federation or enterprise identity methods

An authentication method is **not** a separate identity.

### Linked Login
A **linked login** is the durable relationship between a FUZE account and one approved authentication method.

### Session
A **session** is temporary authenticated runtime state created after successful authentication and policy evaluation.

The session answers the question:

**What access does this already-authenticated actor have right now?**

A session is not canonical identity truth.

### Authorization Context
An **authorization context** is the post-authentication scope in which the platform evaluates workspace, organization, role, permission, entitlement, and product rules.

This layer is separate from account identity and session establishment.

### Access Continuity
The property that the same canonical account remains reachable over time even as providers, products, workspaces, wallets, and user preferences change.

### Conflict / Remediation Case
An explicit platform case used when provider completion, account association, or continuity handling cannot be safely resolved automatically.

---

## Canonical Platform Rule

FUZE MUST preserve the following platform rule:

> Identity belongs to the account. Access belongs to linked authentication methods. Runtime presence belongs to the session.

This rule exists to prevent long-term platform errors such as:

- product-local identity fragmentation
- over-trusting provider identity as canonical identity
- treating sessions as durable identity truth
- confusing workspace membership with identity
- confusing wallet linkage with account identity
- inconsistent support and recovery handling

This rule is non-negotiable.

---

## Platform Invariants

### 1. Canonical Identity Invariant
Each actor MUST resolve to exactly one canonical `account_id` at the platform level, except during explicit operator-managed remediation such as controlled merge or conflict review.

Products MUST NOT create an alternative canonical identity layer.

### 2. Linked Access Invariant
Each approved login method MUST be modeled as an access path to the canonical account, not as a second account.

### 3. Session Invariant
A session MUST be treated as temporary authenticated runtime state and MUST NOT be treated as the permanent identity source of truth.

### 4. Authorization Ordering Invariant
FUZE services MUST preserve the following sequence:

1. authenticate account
2. establish session
3. resolve workspace or organization context
4. evaluate role and permission rules
5. evaluate product entitlements or capability rules
6. grant or deny requested capability

### 5. No Silent Fragmentation Invariant
The platform MUST NOT silently:

- create duplicate identities for the same actor
- merge accounts without explicit controlled rules
- overwrite provider links into a different account
- reassign an access method in a way that breaks audit continuity

### 6. Account State Precedence Invariant
Account state, risk state, and authentication-method state MUST take precedence over session continuation.

### 7. Product Boundary Invariant
Product-specific login choice MUST NOT create product-specific identity truth.

### 8. Wallet Boundary Invariant
Wallet-aware context MAY attach to an account, but MUST NOT replace canonical account identity.

---

## Supported Platform Model

FUZE is designed to support different entry patterns across different products while preserving one shared account model.

Examples include:

- one product may expose Google, Line, or Facebook
- another product may expose Telegram or wallet-aware entry
- another product may expose email/password and Google
- a future enterprise-facing product may expose federation or SSO

This product flexibility is allowed only if each approved provider flow resolves back into the same canonical account and session architecture.

Product-specific login choice MUST NOT create product-specific identity truth.

---

## Identity Model Requirements

### Canonical Account Requirement
The platform MUST use `account_id` as the only durable internal anchor for cross-product identity continuity.

Secondary identifiers such as provider subjects, email addresses, wallet addresses, usernames, and product-local profile references MUST be treated as attached relationships or attributes, not as replacements for `account_id`.

### Account Lifecycle Requirement
Account lifecycle state SHOULD remain explicit and durable.

The detailed lifecycle model belongs in `IDENTITY_AND_ACCOUNT_SPEC.md` and the related identity APIs.

### Product Profile Boundary Requirement
Products MAY maintain product-local profile data. However, product-local profile data MUST remain downstream of the canonical account and MUST NOT redefine platform identity.

### Wallet Boundary Requirement
Wallet-aware participation or wallet-aware access context MAY attach to an account, but wallet linkage MUST NOT automatically become canonical identity truth.

The detailed wallet boundary belongs in `WALLET_AWARE_USER_SPEC.md`.

---

## Authentication Method Requirements

### Approved Method Requirement
Every authentication method MUST be explicitly approved by FUZE policy and implementation scope before production use.

### Durable Link Requirement
Each active authentication method MUST be durably linked to exactly one canonical account unless an operator-managed remediation flow is underway.

### Provider Subject Requirement
Each provider-backed method MUST use a provider-scoped stable subject identifier as the durable mapping key.

The platform SHOULD store a normalized provider subject key.

Examples:
- OIDC-style providers: issuer + subject
- messaging providers: provider-specific stable subject identifier
- wallet-based auth: normalized chain + address or equivalent approved wallet subject key

### Email Non-Canonical Requirement
Email MAY be used as a hint, recovery contact, or review signal, but SHOULD NOT be the sole canonical matching key for provider login resolution.

### Linked Login Semantics Requirement
A linked login MUST be treated as:
- an approved access path
- a durable relationship to the account
- a security-sensitive asset

A linked login MUST NOT be treated as:
- a separate account
- a second identity root
- an authorization source of truth
- a workspace membership record

---

## Account Continuity Requirements

### Continuity Principle
FUZE SHOULD preserve the experience that a person remains the same person in FUZE even when their preferred login method, product usage, workspace relationship, or wallet-aware participation changes over time.

### Access Continuity Requirement
No self-service mutation SHOULD leave an account stranded without:
- another viable active authentication method, or
- an approved recovery path, or
- an operator-reviewed remediation path

### Continuity Check Requirement
The platform SHOULD evaluate continuity posture before sensitive auth mutations, including:
- linking a new provider
- removing a linked provider
- changing a recovery-significant email
- setting or resetting password credentials
- enabling or disabling wallet-based auth if supported
- completing account recovery

### Continuity Surface Requirement
FUZE SHOULD define a first-class continuity view or continuity summary model so products and support tools can make consistent decisions.

Detailed continuity behavior belongs in:
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`

---

## Session Requirements

### Session Purpose Requirement
A session MUST represent temporary authenticated runtime state and MUST be revocable, reviewable, and policy-bound.

### Session Lifecycle Requirement
FUZE SHOULD support explicit session lifecycle states such as:
- issued
- active
- refreshed or rotated
- revoked
- expired
- invalidated for security reason

Detailed lifecycle and API behavior belongs in:
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`

### Session Continuation Requirement
Session continuation MUST remain subordinate to:
- account status
- authentication-method status
- security and risk controls
- recovery events
- platform revocation events

### Browser Session Transport Requirement
For browser-based surfaces, FUZE SHOULD prefer secure cookie-based session transport with strong server-side validation rather than exposing long-lived bearer secrets to browser storage.

### Session Audit Requirement
Security-significant session events SHOULD generate durable audit records.

Detailed session-security and audit behavior belongs in:
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`

---

## Authorization Boundary Requirements

### Authentication vs Authorization Separation
Authentication and authorization MUST remain separate concerns.

Successful authentication proves account access. It does not by itself prove:
- workspace membership
- organization scope
- role assignment
- permission grant
- product entitlement

### Workspace Resolution Requirement
After authentication and session establishment, the platform MUST resolve workspace or organization context before granting scoped product actions.

### Role and Permission Requirement
Role and permission evaluation MUST happen after workspace or organizational context is resolved.

These detailed rules belong in:
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- related authorization APIs

---

## Provider Flexibility Requirements

### Product Flexibility Requirement
Different FUZE products MAY expose different approved login providers if this improves product fit, onboarding flow, or user trust.

### Canonical Resolution Requirement
All provider flows MUST normalize into one backend-owned account resolution model.

### Frontend Boundary Requirement
Frontend applications MAY initiate login flows and present results, but MUST NOT own canonical truth for:
- account identity
- provider identity resolution
- session validity
- recovery completion
- operator remediation

### Future Provider Onboarding Requirement
New providers SHOULD be onboarded only through the shared account/access/session model rather than through product-local special cases.

Detailed provider onboarding contracts belong in:
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`

---

## Security and Risk Requirements

### Security-Sensitive Mutation Requirement
The following actions SHOULD be treated as security-sensitive:
- add auth method
- remove auth method
- change recovery-significant email
- password reset
- recovery completion
- global session revoke
- wallet-auth enablement if supported
- operator-driven identity remediation

### Step-Up Requirement
FUZE SHOULD support recent-auth or stronger verification for high-risk actions.

### Risk Override Requirement
Security and risk controls MAY suspend, restrict, or invalidate ordinary session continuation when the platform determines that additional protection is required.

Detailed controls belong in:
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`

---

## Recommended Data-Model Direction

This canonical document does not define the final schema. However, the canonical model SHOULD include durable representations for:

- `account`
- `account_auth_method` or equivalent canonical auth-link entity
- `auth_session`
- session lineage or refresh lineage
- continuity summary or posture view
- conflict or remediation case where identity resolution is ambiguous

Recommended naming direction:
- prefer one durable canonical auth-link name across the platform
- prefer provider subject fields that are stable and explicit
- prefer explicit session lineage and invalidation reason fields
- prefer explicit conflict-case objects instead of implicit hidden remediation logic

The detailed data model belongs in:
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`

---

## Recommended API Boundary Direction

The API surface SHOULD separate domain ownership clearly.

### Identity Domain
The identity domain SHOULD own:
- canonical account truth
- durable auth-method relationships at the identity layer
- account lifecycle state
- recovery posture
- provider-to-account resolution outputs

### Session Domain
The session domain SHOULD own:
- session issuance
- session inspection
- refresh or rotation
- targeted revocation
- global revocation
- runtime auth state

### Authorization Domain
The authorization domain SHOULD own:
- workspace scope resolution
- organization scope resolution
- role evaluation
- permission evaluation
- capability grant rules

No API surface should blur these boundaries.

---

## Community-Facing Interpretation

For the FUZE community, this model should feel simple even though the platform underneath is sophisticated.

The intended meaning is:

- you have one FUZE identity
- you may use different approved login methods over time
- you may use different FUZE products over time
- you may join workspaces or organizations over time
- you may later connect wallet-aware context over time
- and FUZE should still recognize you as the same person within the platform

That continuity is one of the core promises of the FUZE system design.

---

## Anti-Patterns FUZE Should Avoid

FUZE SHOULD avoid the following platform mistakes:

- one account model per product
- one identity model for Web2 flows and another unrelated model for wallet-aware flows
- using provider profile data as canonical identity truth
- treating login success as automatic authorization success
- allowing sessions to outrank account or risk state
- allowing frontend logic to become source of truth for auth state
- adding new providers through one-off product exceptions that bypass the shared model

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
- `WALLET_AWARE_USER_SPEC.md`

This specification directly governs or materially informs:

- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
- `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `WALLET_AWARE_USER_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- product integration specifications

---

## Explicitly Deferred Items

The following are intentionally deferred to downstream specifications:

- exact provider list and provider-specific onboarding logic
- exact session transport and token model
- exact password, MFA, and recent-auth implementation detail
- exact workspace membership resolution logic
- exact support workflow for recovery and remediation
- exact wallet-auth implementation scope if adopted
- exact admin and control-plane procedures

These do not weaken the canonical account/access/session model established here.

---

## Summary Rule Set

The FUZE account, access, and session core can be summarized as follows:

1. There is one canonical account for the actor.
2. There may be multiple approved ways to access that account.
3. Linked login methods are access paths, not separate identities.
4. Sessions are temporary authenticated runtime state.
5. Authentication happens before workspace, role, permission, and product capability evaluation.
6. Account status and security controls override ordinary session continuity.
7. Product flexibility is allowed, identity fragmentation is not.
8. Wallet-aware context may attach to an account, but must not replace canonical account identity.

---

## Final Statement

FUZE should continue building its platform on a single clear promise:

> One person, one canonical FUZE account, many approved access paths, and policy-controlled sessions across many products.

That promise gives FUZE the right foundation for a multi-product future.

It is flexible enough to support product-specific login choices.
It is strict enough to prevent identity fragmentation.
And it is clear enough for the FUZE community to understand how the platform stays coherent as it grows.
