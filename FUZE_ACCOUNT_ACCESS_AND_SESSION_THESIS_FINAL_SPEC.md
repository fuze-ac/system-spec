# FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC

## Document Metadata

- Document Name: `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
- Document Type: Refined thesis-style governing system specification
- Status: Active refined thesis spec
- Version: 2.0.0
- Effective Date: 2026-04-21
- Last Updated: 2026-04-21
- Reviewed On: 2026-04-21
- Document Owner: FUZE Platform Identity and Access Architecture
- Approval Authority: FUZE Platform Architecture and Governance Authority
- Review Cadence: Quarterly or upon material change to account identity, authentication, session, provider resolution, recovery posture, workspace/authorization ordering, or wallet-aware access posture
- Governing Layer: Platform core / public-facing thesis and interpretive design layer for account, access, and session
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, product engineering, backend engineering, security engineering, support operations, audit, governance, API design, implementation-contract authors, community-facing documentation authors
- Primary Purpose: Explain, in durable FUZE-specific and publicly understandable terms, why FUZE separates account identity, authentication access paths, and session runtime state; why that separation is foundational to a multi-product platform; and which architectural rules downstream canonical and implementation specifications MUST preserve
- Primary Upstream References:
  - `REFINED_SYSTEM_SPEC_INDEX.md`
  - `DOCS_SPEC_INDEX.md`
  - `SYSTEM_SPEC_INDEX.md`
  - `API_SPEC_INDEX.md`
  - `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
  - `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
  - `PLATFORM_ARCHITECTURE_SPEC.md`
  - `IDENTITY_AND_ACCOUNT_SPEC.md`
  - `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
  - `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
  - `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
  - `WALLET_AWARE_USER_SPEC.md`
- Primary Downstream Dependents:
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
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
  - community-facing architecture explanations and product integration guidance
- Supersedes: Earlier thesis-style account/access/session explanations that were less explicit about truth classes, ownership boundaries, provider normalization, continuity safety, session subordination, and downstream implementation guardrails
- Superseded By: Not yet known
- Related Decision Records: Not yet known
- Canonical Status Note: This document is the thesis-style interpretive source for FUZE account, access, and session design. It is explanatory in style, but its architectural rules and boundary statements are normative. Downstream documents MUST NOT reinterpret those rules.
- Implementation Status: Normative thesis and semantic foundation; deeper implementation, API, workflow, storage, and runtime contracts must conform
- Approval Status: Drafted for refined-system inclusion; formal approval record not yet attached
- Change Summary:
  - upgraded the earlier thesis into the refined-system format
  - aligned the thesis with the active refined identity, auth/session, provider, wallet-aware, and session-lifecycle specs
  - clarified truth-class separation between account identity, linked access methods, and runtime session state
  - made the account/access/session ordering rule explicit and durable
  - added conservative conflict-resolution posture and no-silent-fragmentation rules
  - added stronger implementation-contract guardrails while preserving a thesis-style, publicly understandable tone

## Purpose

This document explains, in clear but governing language, how FUZE thinks about **account**, **authentication**, and **session** as one connected platform foundation.

It exists because FUZE is not a single isolated application. FUZE is a platform ecosystem that can contain multiple products, multiple entry styles, multiple workspaces, multiple permission scopes, future partner or enterprise access paths, and wallet-aware participation context. In that environment, a platform that treats “login” as the whole user model will eventually fragment identity, overtrust providers, blur authorization boundaries, and create unsafe recovery and support behavior.

This specification therefore establishes the public thesis that FUZE MUST preserve:

> **Identity belongs to the account. Access belongs to approved linked login methods. Runtime presence belongs to the session.**

That thesis is the stable interpretive rule for the FUZE ecosystem. It explains why the canonical account exists, why many provider paths can still lead to one person-level identity, why sessions are temporary and subordinate, and why workspaces, roles, permissions, entitlements, and wallet-aware context happen **after** authentication rather than replacing it.

## Scope

This thesis governs the public and platform-wide interpretation of:

- what the FUZE account is
- what an approved access method is
- what a session is
- why those three concepts must remain separate
- why authentication must precede workspace resolution, authorization, and entitlement evaluation
- why wallet-aware context must remain attached to the account rather than replacing it
- why provider flexibility is compatible with identity strictness
- why continuity, recovery, auditability, and conservative conflict handling are foundational requirements
- which architectural meanings downstream canonical, API, and implementation-contract documents must preserve

This thesis does **not** define the full implementation detail for:

- exact provider callback handling
- exact auth challenge or MFA orchestration
- exact session token or cookie mechanics
- exact workspace membership rules
- exact authorization matrices
- exact entitlement catalogs
- exact recovery playbooks
- exact storage schemas or service topology

Those details belong in the deeper refined system specs and API specs.

## Out of Scope

This document is explicitly out of scope for:

- product-local UX copy
- marketing positioning
- exact legal identity or compliance verification
- exact cryptographic protocol detail
- exact browser/mobile storage flags
- exact control-plane UI design
- exact runbooks, staffing procedures, or support queue routing

## Design Goals

The design goals of this thesis are to:

1. give FUZE one durable human-readable account/access/session model
2. explain why the account is the identity root for the whole platform
3. explain why linked login methods are access paths rather than alternate identities
4. explain why sessions are temporary runtime trust rather than permanent identity
5. make the ordering between authentication and authorization unambiguous
6. preserve product flexibility without identity fragmentation
7. preserve wallet-aware extensibility without turning wallets into the identity root
8. preserve continuity, recovery safety, auditability, and conflict clarity
9. help downstream teams implement consistent semantics across products and services
10. reduce drift between community explanations, architecture docs, and implementation contracts

## Non-Goals

This thesis is not intended to:

- create a second canonical layer that competes with the refined canonical or implementation specs
- replace the detailed identity, auth/session, provider, wallet-aware, or session-lifecycle specs
- authorize product teams to invent product-local identity semantics
- treat sessions as if they are enough for authorization
- promote wallet-first or provider-first identity models that weaken the platform account
- weaken the conservative review posture required for ambiguous identity or provider cases

## Core Principles

### 1. One Person, One Canonical FUZE Account
FUZE SHOULD preserve one durable platform account for the actor across products, workspaces, access methods, and future platform extensions.

### 2. Many Access Methods, One Identity
Different approved login methods MAY exist, but they are paths to the account rather than replacements for it.

### 3. Session Is Temporary Runtime State
A session represents current authenticated runtime access. It is temporary, reviewable, revocable, and subordinate to account state and security posture.

### 4. Authentication Is Not Authorization
Login proves that the actor reached the account. It does not by itself prove workspace membership, role grants, permissions, entitlements, or product capability.

### 5. Continuity Matters
A person SHOULD remain the same person in FUZE even when products, providers, workspaces, or wallet-aware participation context change over time.

### 6. Review Is Better Than Guessing
When a provider flow or identity case cannot be resolved safely, FUZE MUST prefer explicit review, conflict handling, or remediation rather than silent guessing.

### 7. Products Consume the Platform Identity Foundation
Products MAY offer different entry experiences, but they MUST consume the shared platform account/access/session model.

### 8. Wallet-Aware Is Attached, Not Dominant
Wallet-aware features may enrich account context, but they MUST NOT replace canonical account identity or ordinary authentication rules.

## Canonical Definitions

### Account
The account is the canonical FUZE identity record for the actor at the platform level. It answers the question: **Who is this actor in FUZE?**

### Authentication Method
An authentication method is an approved way to prove access to the canonical account. It may be platform-native, provider-backed, or another explicitly approved access path.

### Linked Login
A linked login is the durable relationship between the account and one approved authentication method.

### Session
A session is temporary authenticated runtime state created after successful authentication and policy evaluation. It answers the question: **What already-authenticated runtime access exists right now?**

### Account Continuity
The property that the same canonical account remains reachable over time even when access methods, products, or surrounding context change.

### Provider Resolution
The backend-owned process that turns provider-backed login results into one canonical account outcome.

### Conflict / Remediation Case
An explicit path used when normal login or linking cannot be resolved safely without controlled review.

## Truth Class Taxonomy

This thesis intentionally distinguishes the following truth classes so that platform teams do not blur them:

### 1. Canonical Identity Truth
The account and its lifecycle are canonical identity truth.

### 2. Access-Path Truth
Linked authentication methods are canonical truth for access paths to the account, but they are not identity roots.

### 3. Runtime Session Truth
Sessions are canonical truth for current runtime access only.

### 4. Policy Truth
Security policies, risk policies, provider-approval policies, recovery policies, and operator-control policies constrain behavior but are not themselves the actor identity.

### 5. Provider-Input Truth
Provider claims and callback results are evidence used for controlled resolution. They are not canonical identity truth by themselves.

### 6. Wallet-Linked Context Truth
Wallet links and wallet-aware context are adjacent truths attached to the account. They do not replace the account.

### 7. Authorization Truth
Workspace membership, role assignment, permission evaluation, and entitlement decisions are downstream truths layered on top of authenticated identity.

### 8. Derived View / Reporting Truth
Dashboards, support views, exports, caches, and reports may summarize platform state, but they remain derived and must not become write owners.

## Architectural Position in the Spec Hierarchy

This thesis sits:

- below the platform constitution and top-level system-boundary documents
- alongside the public-facing architectural explanation layer of FUZE
- above the canonical and implementation-oriented identity, auth/session, provider, recovery, wallet-aware, and session-lifecycle specifications

This document establishes the interpretive thesis. The deeper refined documents translate that thesis into stricter semantic, operational, API, workflow, and storage rules.

## System Boundaries

This thesis states the platform boundaries that downstream documents must preserve:

- the **account** owns person-level platform identity
- **linked login methods** own approved access-path relationships to that account
- the **session** owns temporary runtime authenticated presence
- **workspace and organization layers** own collaborative context
- **authorization layers** own scoped permission outcomes
- **entitlement layers** own capability gating
- **wallet-aware layers** own wallet linkage and participation context
- **products** may extend UX and local profiles, but may not redefine the platform identity model

## Adjacent Boundaries

### Account vs Authentication
The account answers who the actor is. Authentication answers how access to that actor was proven.

### Authentication vs Session
Authentication is the proof and resolution process. The session is the resulting runtime state after that proof succeeds.

### Session vs Authorization
A valid session means the actor is authenticated. It does not mean the actor is entitled or authorized to do everything.

### Account vs Workspace
The account is the person layer. The workspace is the collaboration and operating-scope layer.

### Account vs Wallet
Wallets may enrich the account. They do not replace the account as canonical identity.

### Account vs Product Profile
Products may have local settings, profiles, or user-facing profile concepts. Those remain extensions of the account, not alternate platform identities.

## Conflict Resolution Rules

When multiple layers appear to disagree, FUZE MUST resolve them conservatively in the following order:

1. canonical account identity and account state
2. canonical linked-auth and provider-resolution state
3. explicit security and policy constraints
4. runtime session state
5. derived views, reports, or product-local caches

Specific thesis-level rules:

- provider profile hints MUST NOT override canonical account truth
- email similarity MUST NOT justify silent merge
- wallet linkage MUST NOT justify identity reassignment
- stale frontend session state MUST NOT override backend session invalidation
- product-local user tables MUST NOT override the platform identity model

## Default Decision Rules

Where ambiguity exists, the platform defaults are:

1. the default actor anchor is `account_id`
2. the default owner of person-level identity is the identity/account domain
3. the default meaning of linked login is “access path,” not “second identity”
4. the default meaning of session is “temporary runtime state,” not “identity” or “authorization”
5. the default meaning of wallet is “attached context,” not “primary identity”
6. the default response to ambiguous provider or duplicate-account evidence is explicit review or remediation, not silent auto-resolution
7. the default response to high-risk admin action is bounded workflow with reason codes and audit

## Roles / Actors / Entities

### End User
The human actor who interacts with FUZE through products, providers, workspaces, or wallet-aware participation.

### Canonical Account
The durable platform identity anchor for that actor.

### Linked Authentication Method
A durable access-path binding that allows the actor to reach the account.

### Session
The active runtime trust object that exists after successful authentication.

### Product Surface
A FUZE product that initiates flows and consumes outcomes but does not own the core identity model.

### Workspace / Organization Context
The operating scope in which later authorization decisions occur.

### Support / Security / Admin Functions
Privileged actors and systems that may assist with recovery or remediation, but only through explicit, audited, policy-bound pathways.

## Ownership Model

The thesis-level ownership model is:

- the **identity/account domain** owns the canonical account and person-level continuity
- the **auth/session domain** owns linked login lifecycle, session issuance, and runtime access state
- the **provider-resolution domain** owns normalization of external provider evidence into safe account outcomes
- the **wallet-aware domain** owns wallet links and wallet-aware participation context
- the **workspace/authorization domains** own scope and access decisions after authentication
- the **product domains** consume all of the above through approved boundaries and must not become hidden owners

## Authority / Decision Model

FUZE must remain platform-first in this domain.

That means:

- products may offer different approved entry paths
- the backend must normalize those paths into one shared account/access/session model
- support and admin tools may assist with complex cases
- no product, provider callback, frontend state store, reporting surface, or convenience cache may override canonical identity, linked-auth, or session truth

## State Model

At the thesis level, FUZE should be understandable as having at least these durable semantic objects:

- one canonical `account`
- one or more approved `linked_auth_method` records or equivalent access-path objects
- one or more `auth_session` records or equivalent runtime session objects
- explicit `conflict_case` or `recovery_case` records when safety requires review
- derived continuity and inspection views that summarize but do not replace canonical truth

## Lifecycle / Workflow Model

### 1. Identity Entry
The actor begins from an approved entry path such as email/password, Google, Telegram, Line, Facebook, future federation, or another approved method.

### 2. Provider or Credential Validation
The platform validates the submitted proof through owner-controlled backend processes.

### 3. Canonical Account Resolution
The platform determines whether the result maps to:
- an existing account
- a safe new-account bootstrap path
- a secure link-intent completion
- a conflict/review state
- a risk-review or denial state

### 4. Session Establishment
Only after safe account resolution and policy checks does the platform create a session.

### 5. Scope and Access Resolution
After session establishment, FUZE resolves workspace, organization, roles, permissions, entitlements, and product capability.

### 6. Ongoing Continuity
As the actor adds providers, changes products, joins workspaces, links wallets, or undergoes recovery, the platform keeps the same canonical account while re-evaluating access and security posture.

## Invariants

The following rules are durable FUZE invariants:

1. one canonical `account_id` is the durable actor anchor
2. linked login methods are access paths, not separate identities
3. sessions are temporary runtime state, not canonical identity
4. authentication happens before workspace resolution, authorization, and entitlement evaluation
5. product-specific login choice must not create product-specific identity truth
6. wallet-aware context may attach to the account but must not replace it
7. account state and security/risk posture outrank ordinary session continuation
8. no ordinary self-service mutation should strand the account without approved alternative access or recovery
9. ambiguous provider or duplicate-account cases must become explicit review cases rather than silent guesses
10. sensitive access mutations must remain auditable and policy-controlled

## Functional Rules

### Canonical Account Rule
FUZE MUST keep one platform account model for the ecosystem rather than creating separate identity roots per product or per provider.

### Linked Login Rule
A linked login MUST be understood as a durable security-sensitive relationship between the account and an approved access path.

### Provider Normalization Rule
Different providers MAY use different external protocols, but the backend MUST normalize them into one FUZE-owned account-resolution model.

### No Silent Fragmentation Rule
FUZE MUST NOT silently split, merge, duplicate, overwrite, or reassign identities when provider or login conflicts occur.

### Session Subordination Rule
A session MUST remain subordinate to account state, linked-auth state, recovery posture, and security/risk controls.

### Frontend Boundary Rule
Frontends may initiate login flows and display outcomes, but they MUST NOT become the source of truth for account identity, provider resolution, or session validity.

### Product Boundary Rule
Products may extend local UX and profile behavior, but they MUST NOT create alternate platform identity or canonical session systems.

## Permission / Access Considerations

The correct access order in FUZE is:

1. authenticate the account
2. establish the session
3. resolve workspace or organization context
4. evaluate role and permission rules
5. evaluate entitlement and capability rules
6. grant or deny the requested capability

This order is mandatory in meaning even if implementations optimize internally. No downstream system may reinterpret “logged in” to mean “fully authorized.”

## Entitlement Considerations

Entitlements are important in FUZE, but they are downstream of account and authentication truth.

The thesis-level rule is simple:

- the account is the subject anchor
- the session is the live authenticated runtime state
- entitlements decide whether the actor may use a product or feature
- entitlement truth must not redefine identity truth

## API / Contract Implications

This thesis does not define exact endpoints, but it requires that downstream APIs preserve clean domain ownership:

- identity APIs answer who the account is and what its lifecycle posture is
- auth/session APIs answer how the account is reached and what runtime sessions exist
- authorization APIs answer what the actor may do in scope
- wallet-aware APIs answer what wallet-linked context is attached
- no single API surface should blur all of these into one ambiguous “user object” that hides ownership boundaries

## Event / Async Implications

The account/access/session model is not only synchronous UX logic. It also affects platform events and async workflows.

Therefore:

- material identity, linked-login, and session changes SHOULD emit durable events after canonical commit
- async flows MUST remain deterministic, replay-safe, and auditable
- event consumers MUST treat events as downstream notifications, not as permission to redefine canonical truth
- recovery, provider correction, and security invalidation flows MUST remain explicit rather than hidden in ad hoc async side effects

## Data Model / Storage Implications

The thesis-level data-model direction is:

- keep a durable canonical account entity
- keep durable linked-auth entities or equivalent access-path records
- keep durable session entities or equivalent runtime trust records
- keep explicit review, remediation, or conflict records when ambiguity exists
- keep derived summaries, continuity views, session inspection views, and reports clearly separate from canonical write ownership

## Read Model / Projection / Reporting Rules

Read models are useful, but they are not canonical truth.

Therefore:

- support views may summarize identity and access posture
- analytics may summarize cross-product usage and continuity posture
- reporting may summarize linked providers, sessions, or wallet-aware participation
- none of those views may become hidden identity, access, or session owners
- when a report disagrees with canonical records, the canonical records win

## Security / Risk / Abuse Controls

This thesis treats account, access, and session as a security foundation rather than a UX convenience layer.

Sensitive actions include, at minimum:

- adding or removing a login method
- password reset
- changing a recovery-significant email
- linked-provider correction
- global logout or session revoke
- recovery completion
- operator-driven identity remediation
- any future wallet-auth enablement if approved

These actions require stronger care because they can change who can reach the canonical account and which runtime trust remains valid.

## Boundary Violation Detection / Non-Canonical Patterns

FUZE should treat the following as forbidden or non-canonical patterns:

- one account model per product
- one identity model for “Web2” and another unrelated model for wallet-aware behavior
- treating provider profile data as canonical identity truth
- treating login success as automatic authorization success
- allowing sessions to outrank account state or security posture
- allowing frontend state to become the source of truth for auth/session
- silently reassigning provider subjects between accounts
- treating wallet possession alone as universal platform identity
- letting support tools perform undocumented identity surgery
- letting reports, exports, dashboards, or caches become hidden write surfaces

## Audit / Traceability Requirements

FUZE MUST be able to reconstruct:

- which canonical account represented the actor
- which access methods were linked to that account
- which session or session lineage carried runtime access
- which provider or recovery events changed the trust posture
- which support, security, or admin actions affected access
- whether a visible surface is canonical or merely derived

Auditability is required because continuity, security, support, and dispute handling all depend on reconstructable truth.

## Failure Handling / Edge Cases

### Provider Outage
If one provider is unavailable, the canonical account still exists. Only one access path is impaired.

### Lost Access to One Method
If another approved method or recovery path exists, the actor should still reach the same canonical account.

### Duplicate Sign-Up Risk
The platform must explicitly decide whether to link, bootstrap, deny, or open conflict review. It must not silently merge or fragment.

### Workspace Change
Joining or leaving a workspace changes collaborative context, not canonical identity.

### Wallet Link Change
Adding or removing a wallet changes wallet-aware context, not the actor’s platform identity.

### Session Present but Account Restricted
Account restriction wins. The session must no longer behave as ordinary valid access.

### Reporting Mismatch
If a reporting surface disagrees with canonical records, the canonical records remain authoritative.

## Operational Considerations

This thesis implies several operational disciplines:

- canonical account resolution must remain backend-owned and deterministic
- continuity checks must occur before sensitive access-path removal or replacement
- security invalidation and global revoke must remain available for trust-reset events
- degraded adjacent systems must not cause the platform to invent fallback identity truth from stale derived views
- support and admin actions must remain bounded, auditable, and policy-constrained

## Migration / Compatibility / Supersession Considerations

As FUZE evolves, migrations must preserve the thesis even when implementation details change.

That means:

- new providers must plug into the shared account-resolution model
- old product-local user concepts may be supported temporarily but must not remain canonical
- wallet-aware capabilities may expand, but they must remain attached to the account
- session mechanics may evolve, but sessions must remain subordinate runtime truth
- any older interpretation that treats providers, sessions, or product-local user IDs as canonical identity is superseded within this document’s scope

## Implementation-Contract Guardrails

Although this is a thesis document, downstream implementations MUST preserve the following:

1. `account_id` remains the durable actor anchor
2. linked logins remain durable access paths rather than identity replacements
3. provider resolution remains backend-owned, conservative, and auditable
4. sessions remain distinct from identity and authorization truth
5. account-state and risk-state precedence over session continuation is preserved
6. recovery restores access to the same account rather than creating substitutes
7. product-local profiles and user abstractions remain downstream of the account
8. operator actions remain bounded, reason-coded, and audited
9. derived views remain derived and regenerable
10. ambiguous cases default to explicit review rather than silent auto-resolution

## Downstream Execution Staging

This thesis supports the following implementation sequence:

1. define the canonical account model
2. define linked-auth and provider-resolution rules
3. define session lifecycle and containment rules
4. define recovery and remediation rules
5. define workspace and authorization layering
6. define entitlement and capability-gating layering
7. define wallet-aware participation layering
8. define reporting and support read models

This order matters because downstream layers are safer when identity and access semantics are stabilized first.

## Required Downstream Specs / Contract Layers

This thesis directly requires compatible downstream refinement in:

- `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
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

## Canonical Examples / Anti-Examples

### Canonical Example 1 — Many Providers, One Person
A user first enters one FUZE product with Google, later adds Telegram in another FUZE product, and still resolves to the same `account_id`. This is canonical.

### Canonical Example 2 — Workspace Change Without Identity Change
A user joins, leaves, or switches workspaces while keeping the same account. This is canonical.

### Canonical Example 3 — Wallet-Aware Context Without Identity Replacement
A user links a wallet for holder-aware or participation-aware features while the account remains the identity root. This is canonical.

### Anti-Example 1 — Product-Local Identity Root
A product creates its own canonical `user_id` and treats it as the platform identity. This is forbidden.

### Anti-Example 2 — Silent Email-Based Merge
A provider callback returns the same email as another account and the system auto-merges without explicit controlled rules. This is forbidden.

### Anti-Example 3 — Session Treated as Full Authorization
A product assumes that because a session is valid, the actor automatically has workspace authority and product capability. This is forbidden.

### Anti-Example 4 — Wallet as Universal Identity
A product treats wallet possession alone as the whole FUZE identity model. This is forbidden.

## Dependencies / Cross-Spec Links

This specification depends on:

- `REFINED_SYSTEM_SPEC_INDEX.md`
- `DOCS_SPEC_INDEX.md`
- `SYSTEM_SPEC_INDEX.md`
- `API_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
- `WALLET_AWARE_USER_SPEC.md`

This specification directly governs or materially informs:

- `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
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
- community-facing and product-integration explanatory materials

## Explicitly Deferred Items

The following are intentionally deferred to downstream specifications:

- exact provider list and provider-specific onboarding logic
- exact credential storage, MFA, and step-up mechanisms
- exact auth challenge, callback, and replay-protection implementation detail
- exact session transport and renewal strategy
- exact workspace, role, permission, and entitlement implementation detail
- exact recovery and support workflow procedures
- exact wallet-auth implementation scope if it is adopted later
- exact admin/control-plane procedures and approvals

These deferred items do not weaken the thesis. They are deferred because they belong in more detailed and more operationally specific documents.

## Final Normative Summary

The FUZE account, access, and session thesis can be summarized as follows:

1. FUZE gives each actor one canonical platform account.
2. That account may be reached through multiple approved access methods.
3. Linked login methods are security-sensitive access paths, not separate identities.
4. Sessions are temporary authenticated runtime state.
5. Authentication happens before workspace, authorization, entitlement, and product-capability evaluation.
6. Wallet-aware context may attach to the account, but it does not replace the account.
7. Continuity is a core platform promise and must survive provider changes, product changes, and recovery events.
8. Ambiguity must default to explicit review or remediation rather than silent guessing.
9. Products consume the shared foundation; they do not redefine it.
10. Downstream implementation contracts must preserve these meanings without reinterpretation.

## Quality Gate Checklist

- [x] Canonical owner is explicit for person-level identity, access-path truth, and session truth
- [x] Mutation boundaries are explicit at thesis level
- [x] Adjacent boundaries are explicit
- [x] Truth classes are explicit
- [x] Conflict-resolution rules are explicit
- [x] Default decision rules are explicit
- [x] Non-canonical patterns are called out
- [x] Operator/admin actions are bounded and audit-sensitive
- [x] Read-model and reporting rules are explicit
- [x] Wallet-aware, workspace, authorization, and session boundaries are explicit
- [x] Failure and degraded-mode implications are explicit
- [x] Downstream implementation guardrails are explicit
- [x] Dependencies and downstream impacts are explicit
- [x] Non-goals and deferred items are explicit
- [x] The document is strong enough to guide backend, API, data, runtime, security, and product teams without inviting contradictory semantics
