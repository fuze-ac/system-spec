# AUTH_SESSION_AND_LINKED_LOGIN_SPEC

## Document Metadata

- Document Name: `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Governing Layer: Platform core / authentication, session, and linked login
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, security, support operations, audit, governance, API design, operations
- Primary Purpose: Define the canonical FUZE model for authentication methods, linked-login relationships, session lifecycle, access continuity, and auth-sensitive controls while preserving the separation between account identity, authentication, sessions, workspaces, authorization, wallet-aware participation, and product-local behavior
- Primary Upstream References:
  - `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
  - `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
  - `PLATFORM_ARCHITECTURE_SPEC.md`
  - `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
  - `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
  - `IDENTITY_AND_ACCOUNT_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
  - `SYSTEM_SPEC_INDEX.md`
  - `API_SPEC_INDEX.md`
  - `DOCS_SPEC_INDEX.md`
- Primary Downstream Dependents:
  - `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
  - `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
  - `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
  - `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
  - `WORKSPACE_AND_ORGANIZATION_SPEC.md`
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
  - `WALLET_AWARE_USER_SPEC.md`
  - `AUTH_IDENTITY_API_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - product integration specifications

---

## Purpose

This specification defines the canonical authentication, session, and linked-login architecture of the FUZE platform.

Its purpose is to make explicit:

- how a canonical FUZE account is reached through one or more approved authentication methods
- how linked login methods are modeled as durable access paths rather than alternate identities
- how sessions are created, continued, inspected, refreshed, revoked, invalidated, and terminated
- how provider-based access must normalize into one backend-owned account resolution model
- how access continuity must be protected when providers are added, removed, disabled, or recovered
- how products, frontends, support tools, and internal services must consume auth/session truth without redefining identity or authorization
- how auditability, risk controls, re-authentication, and administrative intervention must behave for auth-sensitive actions

This document exists because FUZE is a multi-product platform. A coherent multi-product platform cannot allow products, providers, or frontends to become separate owners of access truth. Authentication and session behavior must remain platform-owned, auditable, continuity-safe, and consistent across the ecosystem.

---

## Scope

This specification governs:

- the distinction between canonical account identity, linked authentication methods, and sessions
- the approved linked-login model
- authentication method lifecycle and provider-link rules
- session classes, session lifecycle, and continuation rules
- login initiation and completion semantics
- linked method add/remove/disable behavior
- access continuity protection
- auth-sensitive recovery-aligned behavior
- the relationship between auth/session and workspace, authorization, entitlement, wallet-aware participation, and product access
- canonical entities and state models for linked auth and sessions
- audit, risk, and security requirements for auth/session behavior
- boundary rules for products, frontends, internal services, admin tools, and reporting

This specification does not define:

- canonical account identity semantics in full depth
- detailed provider-resolution heuristics for ambiguous cases
- detailed recovery and duplicate-account remediation procedures
- exact workspace scope resolution mechanics
- exact role and permission evaluation rules
- exact wallet verification or wallet-aware participation rules
- exact MFA implementation or cryptographic secret storage details
- exact cookie, token, or browser transport implementation details

Those are refined in downstream specifications.

---

## Out of Scope

This specification is explicitly out of scope for:

- product-local login models
- exact provider SDK or OAuth library selection
- exact password complexity rules
- exact browser storage implementation details
- legal identity, KYC, or compliance verification
- exact support playbooks and staffing procedures
- exact repository or deployment topology
- exact admin UI design

---

## Design Goals

The design goals of the FUZE auth/session/linked-login model are:

1. allow one canonical account to be reached through multiple approved access methods
2. keep authentication mechanics separate from canonical identity truth
3. support secure and durable cross-product access continuity
4. allow users to add or change access methods without fragmenting account ownership
5. support revocation, containment, recovery, and recovery-safe re-entry
6. make session behavior auditable, policy-bound, and security-aware
7. prevent products from inventing separate login systems or canonical auth truth
8. support future provider expansion without changing canonical identity rules
9. preserve backend-owned truth for auth state and session validity
10. maintain clean separation between authentication and later authorization layers

---

## Non-Goals

This specification is not intended to:

- make each provider a separate identity
- let products define standalone platform access models
- treat sessions as durable identity truth
- support uncontrolled provider linking or silent account merges
- make wallet signature the universal login path unless separately approved
- let frontend state become the source of truth for session validity
- collapse recovery, support review, or risk containment into ordinary product flows
- define full MFA architecture unless later promoted as a formal platform capability

---

## Core Principles

### 1. Canonical Access Principle
One canonical account may be reached through one or more approved authentication methods, and each successful authentication event may issue one or more controlled sessions, but neither the authentication method nor the session becomes the canonical identity.

### 2. Identity Versus Access Principle
Identity belongs to the account. Access belongs to linked authentication methods. Runtime presence belongs to the session.

### 3. Durable Link Principle
A linked login method is a durable, security-sensitive relationship between a canonical account and an approved provider-backed or platform-backed access path.

### 4. Session Subordination Principle
Session continuation is subordinate to account state, linked-method state, recovery posture, and security/risk controls.

### 5. No Silent Fragmentation Principle
The platform must not silently create duplicate identities, silently merge accounts, silently overwrite provider links, or silently strand access continuity.

### 6. Product Consumption Principle
Products consume authenticated account context and later scoped authorization outcomes, but they do not redefine auth/session truth.

### 7. Continuity Protection Principle
No ordinary self-service mutation should leave the account without another viable access path, an approved recovery path, or an operator-reviewed remediation path.

### 8. Explicit Normalization Principle
Different provider flows may vary externally, but the backend must normalize them into one canonical account-access and session-resolution model.

---

## Canonical Definitions

### Authentication Method
An approved access path by which an actor proves control over the canonical account.

### Linked Login
The durable relationship between a canonical account and one approved authentication method.

### Linked Authentication Method
The canonical durable entity that represents a provider-backed or platform-backed access-path binding to an account.

### Session
Temporary authenticated runtime state created after successful authentication and policy evaluation.

### Session Lineage
The durable session-history or refresh/rotation lineage that allows review, renewal control, revocation, and security response.

### Access Continuity
The property that the account remains reachable through at least one viable approved access path or approved recovery/remediation path.

### Sensitive Auth Action
An action that changes the security posture of account access.

### Auth Challenge
A short-lived durable challenge entity used to track provider-init, provider-completion, re-auth, or other controlled access verification flows.

### Conflict State
A state in which provider completion or link mutation cannot safely resolve without explicit review, user confirmation, or support/admin intervention.

### Security Invalidation
A higher-severity session termination state caused by risk, compromise suspicion, account restriction, recovery completion, or equivalent platform safety action.

---

## Architectural Position in the Spec Hierarchy

This document sits below:

- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`

and above:

- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
- `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`

This document defines the auth/session/linked-login domain. It does not absorb the downstream ownership of canonical account identity, workspace resolution, authorization, or wallet-aware participation.

---

## Canonical Rule Set

The permanent FUZE rule set for this domain is:

1. there is one canonical account for the actor
2. there may be multiple approved ways to access that account
3. linked login methods are access paths, not separate identities
4. sessions are temporary authenticated runtime state
5. authentication happens before workspace, role, permission, entitlement, and product capability evaluation
6. account status and security/risk controls override ordinary session continuation
7. frontend applications may initiate flows, but backend domains own auth truth
8. provider flexibility is allowed; identity fragmentation is not

---

## Domain Ownership and Authority

The canonical owner of auth/session/linked-login behavior is the **Identity / Access Domain**, split conceptually with the identity domain as follows:

### Identity Domain Owns
- canonical account truth
- account lifecycle state
- provider-to-account resolution outputs
- recovery posture at the identity layer
- identity conflict/remediation state
- account-level continuity meaning

### Auth / Session / Linked Login Domain Owns
- session issuance
- session inspection and listing
- session refresh or rotation semantics
- targeted revocation and global revoke
- linked-login add/remove/disable/reenable lifecycle
- auth challenge lifecycle
- current auth posture summary
- access continuity checks at auth mutation time
- auth-sensitive audit emission in coordination with the audit domain

### Authorization Domain Owns
- workspace scope resolution
- organization scope resolution
- role and permission evaluation
- capability grant rules after authentication

### Product Domains May
- consume authenticated account context
- initiate login or link flows through approved boundaries
- render product-appropriate login UX
- create product-local convenience views of auth state

### Product Domains Must Not
- issue product-local canonical sessions
- treat raw provider identity as authenticated FUZE identity
- directly mutate linked-auth durability or session truth
- redefine continuity rules
- bypass auth/session safety controls

---

## Approved Access Model

FUZE supports a multi-provider linked-login model.

### Approved Current-Scope Methods
- email + password
- Google
- Telegram

### Future Methods
Future providers may be added only by explicit platform approval and must follow the same canonical account-linking, provider-subject, continuity, and session rules.

### Canonical Rule
A linked login method does not create a separate identity. It becomes an access path to the same canonical account.

### Allowed Patterns
- account created with email/password, later linked with Google
- account created with Google, later linked with Telegram
- account with multiple linked providers for resilience and user convenience
- different products exposing different approved provider subsets while still resolving to the same backend-owned account model

### Disallowed Patterns
- a provider or product silently creating an unrelated second account when a safe link or conflict flow should occur
- a frontend or product treating provider profile data as canonical identity
- a product-specific provider onboarding path that bypasses the shared account/access/session model

---

## Provider Subject Model

Each provider-backed method must use a provider-scoped stable subject identifier as the durable mapping key.

### Provider Subject Rules
1. each active provider subject may be linked to exactly one canonical account unless an operator-managed remediation flow is underway
2. the platform should store a normalized provider subject key
3. email may be used as a hint, recovery contact, or review signal, but should not be the sole canonical matching key for provider login resolution
4. provider uniqueness must be transactionally enforced
5. provider profile cache is convenience data only and must not replace linked-auth truth

### Examples
- OIDC-style providers: issuer + subject
- messaging providers: provider-specific stable subject identifier
- wallet-based auth if approved later: normalized chain + address or equivalent approved wallet subject key

---

## Linked Authentication Method Model

At minimum, each linked authentication method must support:

- linked canonical account ID
- provider type
- provider-scoped subject identifier
- verification state
- created time
- last-used time where available
- method status
- risk or restriction markers where applicable

### Linked Method Semantics
A linked auth method MUST be treated as:
- an approved access path
- a durable relationship to the account
- a security-sensitive asset

A linked auth method MUST NOT be treated as:
- a separate account
- a second identity root
- an authorization source of truth
- a workspace membership record
- a wallet ownership record

---

## Linked Auth Method Lifecycle

The platform should support explicit linked-method lifecycle states.

### Minimum Semantic States
- `pending_verification`
- `active`
- `disabled`
- `removed`
- `blocked_conflict`
- `blocked_risk_review`

### Lifecycle Notes
- new provider link may begin in `pending_verification`
- only `active` methods can serve as normal login paths
- `disabled` methods may remain historically attached but unusable
- `removed` methods are not active access paths
- `blocked_conflict` and `blocked_risk_review` prevent use until resolution
- lifecycle transitions must be durable and auditable

---

## Session Model

Sessions are temporary authenticated runtime access records tied to a canonical account.

### A Session Must Represent
- authenticated canonical account
- auth method used or session lineage reference
- issuance time
- expiry time or validity window
- current revocation or invalidation state
- client or device metadata where policy allows
- risk or restriction flags where applicable

### Sessions Are Not
- canonical identity
- proof of long-term ownership
- substitutes for linked-login relationships
- authorization truth
- wallet or product ownership truth

### Session Classes
FUZE may support multiple session classes, such as:
- web interactive session
- mobile/app session
- refresh-capable lineage
- privileged admin session
- support-review session

The semantic separation matters even if some classes share infrastructure.

---

## Session Lifecycle

The canonical session lifecycle is:

1. `issued`
2. `active`
3. `rotated`
4. `expired`
5. `revoked`
6. `invalidated_security`
7. `logged_out`

### Lifecycle Notes
- `issued` transitions quickly to `active` after issuance completes
- `rotated` indicates replaced lineage where supported
- `expired` is passive end-of-life
- `revoked` is intentional targeted or global disablement
- `invalidated_security` is higher-severity invalidation caused by risk, compromise, account restriction, recovery completion, or similar events
- `logged_out` represents normal voluntary closure

These transitions must be auditable and monotonic toward terminal states.

---

## Login Flow Principles

All login flows must resolve into the same platform identity model.

### Email + Password Flow
The user proves access through a stored secret and verification checks. If successful, the platform resolves the canonical account and issues session state.

### Provider Flow
The user authenticates through an approved provider. The provider subject is matched to a linked authentication method. If a match exists, the platform resolves the canonical account and issues session state.

### First-Time Provider Sign-In
If the provider subject is not linked yet, the platform must decide between:
- new account creation
- link-to-existing-account flow
- conflict or review state

This decision must be explicit, backend-owned, and controlled.

### Login Completion Principle
No login flow may let the frontend or product claim authenticated platform identity without backend verification, account resolution, and session establishment.

---

## Session Issuance Rules

Session issuance occurs only after successful authentication and post-auth policy checks.

### Required Issuance Checks
At minimum, the platform must verify:
- linked provider mapping or valid credential match
- account status allows access
- auth flow is complete and not in unresolved conflict state
- session-policy checks pass
- risk-based restriction checks pass
- provider proof integrity or challenge-binding checks pass where applicable

### Session Issuance Outputs
A valid session should provide enough semantic information to:
- identify the canonical account
- support later workspace resolution
- support product authorization and entitlement evaluation
- support audit traceability
- support later invalidation or revocation

The exact transport shape is implementation-specific, but these semantics are mandatory.

---

## Session Refresh and Renewal Rules

If FUZE supports session refresh or rolling validity, refresh must remain subordinate to account and risk rules.

### Refresh Principles
- refresh extends access; it does not redefine identity
- refresh must fail if the account becomes restricted or suspended
- refresh must fail if the auth lineage is invalidated by a high-risk event
- refresh lineage must be revocable
- refresh behavior must remain auditable at a meaningful level

The exact mechanism may vary, but the behavioral semantics must remain consistent.

---

## Session Revocation and Invalidation

FUZE must support both targeted and broad revocation.

### Targeted Revocation
Revoke one or more specific session lineages due to:
- logout on one device
- device compromise suspicion
- user-requested cleanup
- operator-driven targeted containment

### Global Revocation
Revoke all revocable sessions for an account due to:
- password reset
- recovery completion
- suspicious account takeover event
- account suspension or major restriction
- sensitive provider-linking correction
- support-initiated security reset

### Invalidation Precedence
If an account is suspended or materially restricted, active sessions must not continue granting normal access indefinitely. Account-state and security-state authority override routine session continuation.

---

## Linked Login Addition Rules

Adding a linked login method is a sensitive account action.

A linked login may be added only when:
- the user is already authenticated into the canonical account or is in another explicitly approved secure flow
- the provider-specific identity passes verification and uniqueness checks
- linking does not conflict with an already-linked active account elsewhere
- continuity, risk, and recent-auth conditions pass

### Required Protections
- re-authentication or equivalent step-up confirmation may be required
- uniqueness checks must be enforced
- provider ownership must be verified
- challenge-binding verification must be enforced where applicable
- link-add actions must be audited

### Result of Success
The account gains an additional access path, but the canonical account ID does not change.

---

## Linked Login Removal Rules

Removing a linked login method is also a sensitive account action.

A linked method may be removed only if:
- the requesting actor is authorized to modify the account
- the platform determines that account access resilience is preserved
- or the removal is part of a controlled recovery, remediation, or operator-reviewed flow

### Safety Rule
The system must not allow removal of the final viable access path if that would leave the account inaccessible without an approved recovery mechanism or replacement path already in force.

### Removal Examples
- removing Google while keeping email/password
- removing Telegram while keeping Google
- disabling a method after a compromised-provider concern

Provider unlink events must be durable, auditable, and reason-coded.

---

## Access Continuity Rules

Access continuity is a first-class design concern in FUZE.

The platform must allow a user to retain access to the same account even if:
- they prefer a new login method
- they lose access to one provider
- they change primary email
- they operate across multiple products
- they need to add a backup access path

### Continuity Implications
- workspace membership remains tied to the account
- wallet links remain tied to the account
- billing and credits continuity remain tied to the account and/or workspace, not to one provider
- product history remains tied to the account
- audit lineage remains tied to the account even when access methods change

### Continuity Surface
FUZE should expose a first-class continuity summary or posture view so user surfaces, products, support tools, and admin tools can make consistent decisions about stranding risk.

---

## Relationship to Workspace and Authorization

Authentication gives access to the canonical account. It does not by itself determine all workspace or product rights.

After successful authentication:

1. account identity is resolved
2. session is issued
3. workspace or organization context may be selected or resolved
4. product entitlements are checked
5. role and access control is applied

### Separation Rule
Successful authentication does not by itself prove:
- workspace membership
- organization scope
- role assignment
- permission grant
- product entitlement

Products must rely on platform auth/session results plus later scoped authorization context, not on provider identity alone.

---

## Relationship to Wallet-Aware Participation

Authentication methods are not the same as wallet links.

A user may authenticate with:
- email/password
- Google
- Telegram
- future approved methods

and separately have:
- one or more linked wallets
- holder-aware privileges
- snapshot-related eligibility context

Authentication proves access to the platform account. Wallet links attach participation context to that account. Products must not assume that provider login is equivalent to wallet ownership.

---

## Relationship to Recovery and Conflict Handling

Recovery is one of the hardest parts of a linked-login platform and must preserve the canonical account model.

### Recovery Principles in This Domain
- recovery restores access to the same canonical account
- recovery must not accidentally create duplicate accounts
- recovery must not orphan linked providers, workspaces, credits context, or wallet context
- recovery actions must be auditable
- support-assisted recovery must be treated as highly sensitive
- recovery completion may require global session revocation or stronger containment actions

### Conflict Handling Principles in This Domain
Common conflict cases include:
- provider login expected to map to an existing account but does not resolve cleanly
- provider subject already linked elsewhere
- attempted provider link collides with another active account
- provider return data suggests ambiguity

The platform must not silently merge, silently overwrite, or silently split identity. Instead, it must resolve through:
- explicit account-link flow
- explicit conflict state
- explicit review or support flow
- controlled merge/remediation where separately approved

Identity integrity is more important than login convenience in these cases.

---

## Sensitive Authentication Actions

At minimum, the following actions are security-sensitive:

- password set or reset
- primary or recovery-significant email change
- linked provider add
- linked provider remove
- global logout or forced session reset
- account recovery completion
- duplicate-account merge affecting login paths
- security review restriction affecting access methods
- wallet-auth enablement if supported
- operator-driven identity remediation

### Sensitive Action Requirements
Sensitive actions require:
- audit events
- stricter authorization or re-verification
- support/admin review where applicable
- explicit failure handling
- idempotent mutation safety where the API contract requires it

---

## Canonical Entities and Data Ownership

### Durable Canonical Entities
- `linked_auth_method`
- `auth_session`
- `session_refresh_lineage` where refresh-capable sessions exist
- `auth_challenge`
- `auth_security_action`
- auth-sensitive domain events and audit references

### Owner Notes
- `account` remains owned by the identity domain
- `linked_auth_method` and `auth_session` are owned by the identity/access domain
- derived summary views do not become source-of-truth

### Minimum Data Semantics

#### linked_auth_method
- `linked_method_id`
- `account_id`
- `provider_type`
- `provider_subject_id`
- `status`
- `verification_state`
- `created_at`
- `last_used_at`
- risk or block markers where applicable

#### auth_session
- `session_id`
- `account_id`
- `auth_method_reference`
- `issued_at`
- `expires_at` or validity rule
- `revoked_at` or revocation state
- client/device metadata where allowed
- risk markers where applicable

#### auth_challenge
- `challenge_id`
- challenge type or mode
- linked account or pending link context where appropriate
- provider reference
- state
- expiry
- correlation reference

#### auth_event / audit record
- event type
- account ID
- session or linked-method reference
- actor type
- timestamp
- action result
- correlation or trace reference
- reason code where applicable

---

## State Mutation Rules

### Deterministic Mutation Rule
Canonical auth/session truth may be changed only through explicit owner-controlled mutation boundaries, including approved synchronous commands, governed async flows, or approved admin/control pathways.

### Product Mutation Rule
Products may not directly mutate canonical linked-auth durability, session validity, or continuity posture.

### Frontend Boundary Rule
Frontend applications may initiate login and link flows and render results, but they must not own canonical truth for:
- provider identity resolution
- linked-method durability
- session validity
- recovery completion
- operator remediation

### Reporting Rule
Reporting, support dashboards, and analytics may summarize auth/session state but may not redefine it.

---

## Audit and Traceability Requirements

The auth/session/linked-login system must generate durable audit records for at least:

- successful login
- failed login where policy requires
- session issued
- session revoked
- logout current
- revoke all
- linked login added
- linked login removed
- linked method disable/reenable if supported
- password reset initiated/completed
- recovery completed
- suspicious-auth restriction event
- admin or support auth-related intervention
- conflict/review transitions where security relevance exists

Audit output must be strong enough to support security review, support investigation, and platform integrity.

---

## Security / Risk / Abuse Controls

This domain is security-critical.

The platform must preserve:
- provider uniqueness
- controlled provider linking
- safe unlink behavior
- session revocability
- account-state authority over sessions
- risk override over ordinary session continuation
- high-sensitivity treatment for recovery-complete and access-reset actions
- privileged-path separation for support/admin actions
- backend validation of provider completion and challenge integrity

No product may bypass these controls by issuing product-local login state that behaves like canonical platform access.

---

## Failure Handling and Edge Cases

### Provider Outage
If a provider is unavailable, the canonical account still exists. Only that access path is degraded.

### Last Linked Method Removal Attempt
The platform must block or reroute the action if it would strand the account without a safe recovery or approved replacement path.

### Suspended Account With Active Session
Account-state restriction must override routine session continuity.

### Stale Session After Password Reset
Global invalidation or policy-equivalent session reset must occur.

### Product Receives Provider Identity But No Valid FUZE Session
The product must not treat raw provider identity as authenticated platform identity.

### Duplicate Provider Subject Detected
The platform must enter controlled conflict handling, not silent overwrite.

### Recovery Completes After Suspected Compromise
The platform may revoke all prior sessions and require fresh trusted login state.

### Replay of Sensitive Mutation
Idempotency and terminal-state semantics must prevent duplicate linked-method or session-side effects.

### Degraded Read Model
A stale session listing or continuity summary view does not change canonical linked-auth or session truth.

---

## API / Contract Implications

The API surface should separate domain ownership clearly.

### Identity Domain APIs Should Own
- canonical account truth
- durable provider-to-account resolution outputs
- account lifecycle state
- recovery posture at the identity level

### Session and Linked-Login APIs Should Own
- login initiation and completion
- session issuance, inspection, refresh or rotation
- targeted revocation
- global revocation
- linked method listing
- linked method begin-link / complete-link / remove
- access continuity summary

### Authorization APIs Should Own
- workspace scope resolution
- role and permission evaluation
- capability grant rules

No API surface should blur these boundaries.

---

## Data Model / Storage Implications

This specification requires the following data-model discipline:

- `linked_auth_method` must remain a durable source-of-truth entity
- `auth_session` must remain a durable source-of-truth entity for active/revoked/expired session lineage
- `session_refresh_lineage` must remain explicit if refresh-capable sessions exist
- `auth_challenge` must remain a durable short-lived entity rather than implicit transient frontend-only state
- continuity and access-summary views are derived, not canonical
- provider metadata caches are caches only
- auth/session corrections should prefer explicit lineage and state transitions over hidden destructive rewrites

---

## Migration / Compatibility / Supersession Considerations

- migrations must not silently transfer auth/session ownership out of the identity/access domain
- compatibility layers may preserve older flows temporarily, but canonical semantics must remain the same
- provider additions must occur through the shared account/access/session model rather than product-local exceptions
- renamed or split auth/session entities must preserve continuity of authority and auditability
- if older implementations or documents imply looser frontend- or product-owned auth truth, this refined specification supersedes them within its scope

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
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`

This specification directly governs or materially informs:

- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
- `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `WALLET_AWARE_USER_SPEC.md`
- product integration specs

---

## Explicitly Deferred Items

The following are intentionally deferred to downstream specifications:

- exact session transport mechanism
- exact refresh-token and rotation implementation detail
- exact MFA productization
- exact provider onboarding sequence details
- exact support tooling for linked-login conflict resolution
- exact password complexity and secret-storage rules
- exact admin/control-plane workflow procedures

These do not weaken the canonical auth/session/linked-login model established here.

---

## Final Normative Summary

FUZE uses a canonical access model in which one account may be reached through multiple approved linked login methods, and successful authentication may issue controlled sessions for runtime access. The account remains the canonical identity. Authentication methods are access paths. Sessions are temporary authenticated state. Account status, linked-method status, recovery posture, and security/risk controls override ordinary session continuation.

This separation allows FUZE to support provider flexibility, cross-product continuity, secure recovery, auditable access changes, and long-term platform coherence without fragmenting user identity across products or providers. Any downstream design that weakens this rule is inconsistent with the FUZE identity foundation.
