# AUTH_SESSION_AND_LINKED_LOGIN_SPEC

## Title
AUTH_SESSION_AND_LINKED_LOGIN_SPEC

## Document Metadata

- Document Name: `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Version: 1.0.0
- Effective Date: 2026-04-21
- Last Updated: 2026-04-21
- Reviewed On: 2026-04-21
- Document Owner: FUZE Platform Identity / Access Domain
- Approval Authority: FUZE Platform Architecture and Governance Authority
- Review Cadence: Quarterly or upon material authentication, session, provider-linking, recovery-model, or security-control change
- Governing Layer: Platform core / authentication, session, and linked login
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, security engineering, support operations, audit, governance, API design, platform operations, reliability engineering, reporting
- Primary Purpose: Define the canonical FUZE model for authentication methods, linked-login relationships, auth challenge handling, session issuance boundaries, access continuity protections, and auth-sensitive controls while preserving strict separation between account identity, authentication, sessions, workspace scope, authorization, wallet-aware participation, and product-local behavior
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
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
- Primary Downstream Dependents:
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
  - product integration specifications
- Supersedes: Earlier non-refined or less explicit auth/session/login writeups to the extent they conflict within this document's scope
- Superseded By: Not yet known
- Related Decision Records: Not yet known
- Canonical Status Note: This document is the governing FUZE auth/session/linked-login system specification. Downstream implementations, product flows, adapters, and operator tools MUST NOT redefine the semantics established here.
- Implementation Status: Normative architecture baseline; downstream API, workflow, service, storage, and runtime contracts must conform
- Approval Status: Drafted for refined-system inclusion; formal approval record not yet attached
- Change Summary:
  - normalized auth-versus-identity ownership boundaries
  - tightened linked-login, auth-challenge, and session semantics
  - clarified continuity-preserving mutation rules and default conflict posture
  - strengthened audit, risk, operator, and implementation-contract guardrails

---

## Purpose

This specification defines the canonical authentication, session, and linked-login architecture of the FUZE platform.

Its purpose is to make explicit:

- how one canonical FUZE account may be reached through one or more approved authentication methods
- how linked login methods are modeled as durable access paths rather than alternate identities
- how authentication completion, auth challenge validation, session issuance, session continuation, and auth-sensitive mutation must behave
- how provider-backed access must normalize into one backend-owned account resolution model
- how access continuity must be protected when methods are added, removed, disabled, corrected, or recovered
- how products, frontends, internal services, admin tools, and support flows must consume auth/session truth without redefining identity or authorization
- how auditability, risk controls, re-authentication, and administrative intervention must behave for auth-sensitive actions

This document exists because FUZE is a multi-product platform. A coherent multi-product platform cannot allow products, providers, or frontends to become separate owners of access truth. Authentication and session behavior MUST remain platform-owned, auditable, continuity-safe, and consistent across the ecosystem.

---

## Scope

This specification governs:

- the distinction between canonical account identity, linked authentication methods, auth challenges, and sessions
- the approved linked-login model
- authentication method lifecycle and provider-link rules at the parent auth/session layer
- auth challenge lifecycle for login, link, re-verification, and other approved access verification flows
- session issuance preconditions and parent session semantics
- linked method add, remove, disable, restore, and review-sensitive behavior
- access continuity protection for ordinary and sensitive auth mutations
- auth-sensitive recovery-aligned behavior
- the relationship between auth/session and workspace scope, authorization, entitlement, wallet-aware participation, and product access
- canonical entities and state models for linked auth, auth challenges, and sessions
- audit, risk, and security requirements for auth/session behavior
- boundary rules for products, frontends, internal services, admin tools, reports, and projections

This specification does not define in full depth:

- canonical account identity semantics
- detailed provider-resolution heuristics for ambiguous cases
- detailed session token transport mechanics
- detailed recovery and duplicate-account remediation procedures
- exact workspace scope resolution mechanics
- exact role and permission evaluation rules
- exact wallet verification or wallet-aware participation rules
- exact MFA implementation or cryptographic secret-storage detail
- exact cookie, token, or browser transport implementation detail

Those belong in adjacent and downstream specifications.

---

## Out of Scope

This specification is explicitly out of scope for:

- product-local login models
- exact provider SDK or OAuth library selection
- exact password complexity or secret-storage standards
- exact browser-storage implementation detail
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
- replace downstream API, schema, or runbook specifications

---

## Core Principles

### 1. Canonical Access Principle
One canonical account MAY be reached through one or more approved authentication methods, and each successful authentication event MAY issue one or more controlled sessions, but neither the authentication method nor the session becomes canonical identity.

### 2. Identity Versus Access Principle
Identity belongs to the account. Access belongs to linked authentication methods. Runtime presence belongs to the session.

### 3. Durable Link Principle
A linked login method is a durable, security-sensitive relationship between a canonical account and an approved provider-backed or platform-backed access path.

### 4. Session Subordination Principle
Session continuation is subordinate to account state, linked-method state, recovery posture, and security/risk controls.

### 5. No Silent Fragmentation Principle
FUZE MUST NOT silently create duplicate identities, silently merge accounts, silently overwrite provider links, or silently strand access continuity.

### 6. Product Consumption Principle
Products consume authenticated account context and later scoped authorization outcomes, but they do not redefine auth/session truth.

### 7. Continuity Protection Principle
No ordinary self-service mutation SHOULD leave the account without another viable access path, an approved recovery path, or an operator-reviewed remediation path.

### 8. Explicit Normalization Principle
Different provider flows MAY vary externally, but the backend MUST normalize them into one canonical account-access and session-resolution model.

### 9. Auth-Before-Authorization Principle
Authentication proves access to the account. It does not itself prove workspace membership, role grant, permission grant, entitlement, or product capability.

### 10. Review-Over-Guessing Principle
When access safety cannot be determined confidently, the platform MUST fail closed into explicit denial, review, conflict, or remediation posture rather than silently guessing.

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
A short-lived durable challenge entity used to track provider-init, provider-completion, re-auth, link verification, or other controlled access verification flows.

### Conflict State
A state in which provider completion or link mutation cannot safely resolve without explicit review, user confirmation, or support/admin intervention.

### Security Invalidation
A higher-severity session termination state caused by risk, compromise suspicion, account restriction, recovery completion, or equivalent platform safety action.

---

## Truth Class Taxonomy

This domain MUST distinguish the following truth classes:

### Canonical Identity Truth
`account` and identity-domain lifecycle semantics remain canonical identity truth and are not redefined here.

### Auth-Link Truth
`linked_auth_method` and approved provider-subject bindings are canonical auth-link truth for this domain.

### Runtime Session Truth
`auth_session`, refresh lineage, revocation state, and session invalidation state are canonical runtime truth for this domain.

### Auth-Challenge Truth
`auth_challenge` and equivalent owner-controlled verification state are canonical short-lived challenge truth for this domain.

### Policy Truth
Access-method approval policy, risk policy, re-auth requirements, unlink safety rules, session policy, and admin-control policy are policy truth.

### Provider-Input Truth
Provider claims, callback payloads, issuer-subject pairs, messaging-provider subjects, and similar external inputs are evidence and mapping inputs, not canonical account truth.

### Derived Read-Model Truth
Continuity summaries, active-session lists, support views, analytics views, and product convenience summaries are derived views. They MAY summarize canonical auth/session truth but MUST NOT become write owners.

### Reporting Truth
Exports and analytical summaries MAY reflect auth/session state but MUST remain downstream and correctable from canonical truth.

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

This document defines the auth/session/linked-login domain. It does not absorb downstream ownership of canonical account identity, workspace resolution, authorization, or wallet-aware participation.

---

## System Boundaries

This specification governs the auth/session/linked-login domain and its mandatory interaction with adjacent domains.

The auth/session/linked-login domain MUST own:

- login initiation and completion semantics after account resolution rules are applied
- linked-login add, remove, disable, restore, and review-sensitive lifecycle behavior
- auth challenge lifecycle
- session issuance, inspection, refresh or rotation semantics, logout, targeted revocation, global revocation, and security invalidation
- current auth posture summary and continuity checks at auth mutation time
- auth-sensitive audit emission in coordination with the audit domain

This domain MUST NOT own:

- canonical account identity truth
- workspace membership truth
- organization scope truth
- role, permission, or entitlement truth
- wallet-link truth or chain ownership truth
- product-local profile truth
- reporting truth

---

## Adjacent Boundaries

### Identity and Account
`IDENTITY_AND_ACCOUNT_SPEC.md` governs the canonical account, identity lifecycle, and identity-domain ownership. This document governs how approved access paths and runtime sessions reach that account without redefining it.

### Provider Resolution and Linking
`FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md` governs provider normalization, mapping outcomes, collisions, and provider-link correction. This document governs the durable access-path and session semantics that consume those outputs.

### Session Lifecycle and Security
`FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md` governs detailed session lifecycle and security posture. This document establishes the parent auth/session rule set and separation boundaries that session lifecycle work must preserve.

### Recovery and Conflict Handling
`FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md` governs high-sensitivity restoration and remediation flows. This document governs ordinary and sensitive auth/session behavior that must defer to recovery and conflict state when trust is contested.

### Workspace and Authorization
Authentication proves account access. It does not itself prove workspace membership, organization scope, role assignment, permission grant, or entitlement.

### Wallet-Aware Participation
Authentication methods and linked logins are not the same as wallet links. Wallet-aware participation remains attached context, not canonical identity or ordinary session truth.

---

## Conflict Resolution Rules

When multiple layers disagree, the platform MUST resolve auth/session conflicts in the following order unless a higher-order platform policy explicitly states otherwise:

1. canonical identity-domain records and restriction state
2. canonical linked-auth and session-domain records
3. explicit policy and security constraints
4. validated provider-input evidence within approved resolution rules
5. runtime client state
6. derived views, dashboards, or product-local caches

Specific conflict rules:

- provider profile data MUST NOT override canonical account ownership
- email similarity MUST NOT justify silent merge or silent relink
- stale frontend session state MUST NOT override backend session invalidation
- wallet presence MUST NOT justify automatic login resolution or identity reassignment
- product-local login state MUST NOT override shared auth/session truth
- support tooling views MUST NOT be treated as authoritative if they diverge from canonical records

---

## Default Decision Rules

Where ambiguity exists, the following defaults apply:

1. default actor anchor: `account_id`
2. default owner of durable identity semantics: Identity and Account Domain
3. default owner of durable auth-link and runtime session semantics: Auth / Session / Linked Login Domain
4. default interpretation of provider subject: evidence for approved mapping, not a second identity root
5. default interpretation of email: hint, contact, or review signal, not sole canonical matching key
6. default interpretation of session: temporary runtime access, not durable identity or authorization truth
7. default interpretation of wallet: attached participation context, not universal login proof unless separately approved
8. default resolution for ambiguous provider collision: explicit conflict/review, not silent overwrite
9. default resolution for removal of last viable access path: block ordinary self-service mutation and require approved replacement, recovery, or operator-reviewed remediation
10. default resolution for high-risk admin action: require reason code, audit, policy reference, and bounded workflow

---

## Roles / Actors / Entities

### End User
The actor attempting login, session continuation, logout, linked-login management, or access restoration.

### Identity / Access Domain
The platform owner of this auth/session/linked-login rule set in coordination with the identity domain.

### Identity Domain
Owns canonical account truth, account lifecycle state, provider-to-account resolution outputs, identity conflict/remediation state, and account-level continuity meaning.

### Auth / Session / Linked Login Domain
Owns session issuance, session inspection, refresh or rotation semantics, targeted revocation, global revocation, linked-login lifecycle, auth challenge lifecycle, auth posture summary, and auth mutation safety checks.

### Security / Risk Function
Owns heightened review posture, compromise handling, risk overrides, and stronger evidence requirements where access safety is contested.

### Support / Operations Function
May assist with reviewable auth, recovery, or remediation actions under stronger authorization, reason-code, and audit requirements. Support does not become canonical identity owner.

### Products and Frontends
May initiate approved flows and consume approved outputs, but MUST NOT become owners of canonical auth/session truth.

### Canonical Entities
- `linked_auth_method`
- `auth_session`
- `session_refresh_lineage` where refresh-capable sessions exist
- `auth_challenge`
- `auth_security_action`
- auth-sensitive domain events and audit references

---

## Ownership Model

### Identity Domain Owns
- canonical account truth
- account lifecycle state
- provider-to-account resolution outputs
- recovery posture at the identity layer
- identity conflict/remediation state
- account-level continuity meaning

### Auth / Session / Linked Login Domain Owns
- linked-auth durability and lifecycle at the auth domain boundary
- session issuance
- session inspection and listing
- session refresh or rotation semantics
- targeted revocation and global revoke
- auth challenge lifecycle
- current auth posture summary
- continuity checks at auth mutation time
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

## Authority / Decision Model

The auth/session domain MAY:

- issue and terminate sessions
- manage auth challenge state
- add, remove, disable, or restore linked methods through approved flows
- enforce continuity checks before sensitive auth mutations
- reject issuance or mutation when conflict, recovery, or risk posture blocks continuation
- publish auth/session events after canonical commits

Products MAY:

- initiate approved login and link flows
- present login choices and session-aware UX
- request logout and selected session-management actions through approved interfaces

Products MUST NOT:

- create alternate canonical session systems
- accept provider callback completion as sufficient platform-auth truth without backend issuance
- persist product-local auth truth that outranks backend records
- auto-merge or auto-reassign linked providers

Admin and support surfaces MAY trigger reviewable actions only through bounded control-plane pathways. They MUST NOT bypass domain validation, policy, reason-code capture, audit, or idempotency requirements.

---

## State Model

### Linked Authentication Method States
At minimum, the platform SHOULD support:

- `pending_verification`
- `active`
- `disabled`
- `removed`
- `blocked_conflict`
- `blocked_risk_review`

Only `active` methods may serve as ordinary login paths.

### Session States
At minimum, the platform SHOULD support:

- `issued`
- `active`
- `rotated`
- `expired`
- `revoked`
- `invalidated_security`
- `logged_out`

These transitions MUST be auditable and monotonic toward terminal states.

### Auth Challenge States
At minimum, the platform SHOULD support:

- `created`
- `presented`
- `completed`
- `expired`
- `failed`
- `cancelled`
- `replayed_denied`

Challenge completion MUST be terminal and replay-safe.

### Conflict and Review States
Conflict, review, and remediation posture MUST be represented explicitly through durable owner-controlled state rather than hidden manual notes, transient client flags, or undocumented support behavior.

---

## Lifecycle / Workflow Model

### Login Entry Flow
1. actor begins an approved credential or provider flow
2. provider or credential proof is validated through approved boundaries
3. canonical account resolution is determined through the shared backend-owned model
4. unresolved conflict, recovery, or risk posture is evaluated
5. if allowed, session issuance occurs
6. post-auth workspace, authorization, entitlement, and product-capability evaluation occurs downstream

### First-Time Provider Flow
If a provider subject is not yet linked, the platform MUST explicitly decide among:

- new account bootstrap
- secure link-to-existing-account flow
- conflict/review state

The decision MUST be backend-owned, conservative, and auditable.

### Linked Method Addition Flow
A linked login method MAY be added only when:

- the actor is already authenticated into the canonical account or is in another explicitly approved secure flow
- provider-specific identity passes verification and uniqueness checks
- linking does not collide with another active account elsewhere
- continuity, risk, and recent-auth conditions pass

### Linked Method Removal Flow
A linked method MAY be removed only when:

- the actor is authorized to modify the account
- access resilience is preserved
- or the removal is part of a controlled recovery, remediation, or operator-reviewed flow

The platform MUST block ordinary self-service removal of the final viable access path when doing so would strand the account without approved recovery or replacement.

### Session Continuation Flow
Session continuation remains subordinate to account state, linked-method state, recovery posture, and security/risk controls.

### Recovery-Aligned Flow
Recovery restores access to the same canonical account. It MAY require global revocation, stronger re-authentication, or denial of new issuance until trust posture is coherent.

---

## Invariants

1. there is one canonical account for the actor
2. there may be multiple approved ways to access that account
3. linked login methods are access paths, not separate identities
4. sessions are temporary authenticated runtime state
5. authentication happens before workspace, role, permission, entitlement, and product capability evaluation
6. account status and security/risk controls override ordinary session continuation
7. provider flexibility is allowed; identity fragmentation is not
8. products and frontends MAY initiate flows, but backend domains own auth truth
9. reports, dashboards, exports, and caches MUST NOT become auth/session write owners
10. sensitive auth actions MUST remain auditable, reason-coded where applicable, and policy-bound
11. auth challenge completion MUST be replay-safe and idempotent where side effects are possible
12. no downstream implementation MAY optimize away explicit continuity checks for access-path removal or replacement

---

## Functional Rules

### Approved Method Rule
Every authentication method MUST be explicitly approved by FUZE policy and implementation scope before production use.

### Durable Link Rule
Each active authentication method MUST be durably linked to exactly one canonical account unless an operator-managed remediation flow is underway.

### Provider Subject Rule
Each provider-backed method MUST use a provider-scoped stable subject identifier as the durable mapping key.

The platform SHOULD store a normalized provider subject key.

Examples include:
- OIDC-style providers: issuer + subject
- messaging providers: provider-specific stable subject identifier
- wallet-based auth if approved later: normalized chain + address or equivalent approved wallet subject key

### Email Non-Canonical Rule
Email MAY be used as a hint, recovery contact, or review signal, but SHOULD NOT be the sole canonical matching key for provider login resolution.

### Linked Method Semantics Rule
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

### Auth Challenge Rule
Any login, link, re-verification, or callback-sensitive flow that can be replayed, misbound, or completed out of order MUST use owner-controlled short-lived challenge state or an equivalent durable correlation mechanism.

### Session Issuance Rule
A session MAY be issued only after successful authentication and post-auth policy checks. At minimum, the platform MUST verify:
- canonical account resolution succeeded
- the auth flow completed successfully
- the linked method or credential path is valid for use
- account status allows issuance
- unresolved conflict or remediation posture is not blocking issuance
- risk-state and policy checks allow issuance
- challenge integrity or provider proof integrity is valid where applicable

### Session Continuation Rule
A session MUST NOT continue as ordinary valid runtime access when any of the following become true:
- the account is suspended or materially restricted
- the linked auth method is disabled, removed, or blocked in a policy-relevant way
- recovery completion requires prior trust to be discarded
- password reset or access reset requires containment
- a major compromise or takeover suspicion exists
- an operator-driven security intervention invalidates the session lineage

### Browser Session Direction Rule
For browser-based surfaces, FUZE SHOULD prefer secure cookie-based session transport with strong server-side validation rather than exposing long-lived bearer secrets to browser storage.

---

## Permission / Access Considerations

Successful authentication proves account access. It does not by itself prove:

- workspace membership
- organization scope
- role assignment
- permission grant
- entitlement grant
- product capability

After successful authentication:

1. account identity is resolved
2. session is issued
3. workspace or organization context may be selected or resolved
4. product entitlements are checked
5. role and access control is applied

No downstream design may collapse these steps into a single ambiguous login result.

---

## Entitlement Considerations

Entitlements remain downstream of auth/session truth.

- entitlement systems MUST consume canonical account identity and required scope context
- entitlement systems MUST NOT use raw provider identity as entitlement subject truth
- identity correction or recovery MUST preserve entitlement subject continuity unless a controlled policy states otherwise
- auth/session views MAY summarize entitlement-adjacent state for UX purposes but MUST NOT become entitlement owners

---

## API / Contract Implications

The API surface MUST separate domain ownership clearly.

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
- linked method begin-link, complete-link, remove, disable, and restore
- access continuity summary
- auth challenge state where exposed by contract

### Authorization APIs Should Own
- workspace scope resolution
- organization scope resolution
- role evaluation
- permission evaluation
- capability grants

Sensitive auth/session APIs MUST require, where relevant:

- explicit actor identity
- correlation IDs or trace IDs
- idempotency for side-effecting mutations vulnerable to replay
- reason codes for privileged mutations
- auditable state transitions
- policy-version or policy-reference capture where applicable

---

## Event / Async Implications

The auth/session domain SHOULD publish durable events after canonical commits for material actions such as:

- login initiated
- login completed
- login denied
- linked login added, removed, disabled, or restored
- session issued
- session refreshed or rotated
- session revoked
- session invalidated for security
- logout current
- revoke all
- auth challenge expired or failed
- sensitive auth action initiated or completed

Async auth/session workflows MUST remain deterministic, auditable, idempotent, and replay-safe.

Event consumers MUST treat these events as downstream notifications, not as permission to redefine canonical auth/session truth.

---

## Data Model / Storage Implications

This specification requires the following data-model discipline:

- `linked_auth_method` MUST remain a durable source-of-truth entity
- `auth_session` MUST remain a durable source-of-truth entity for active, revoked, expired, and invalidated lineage
- `session_refresh_lineage` MUST remain explicit if refresh-capable sessions exist
- `auth_challenge` MUST remain a durable short-lived entity rather than implicit transient frontend-only state
- continuity and access-summary views are derived, not canonical
- provider metadata caches are convenience data only and MUST NOT replace linked-auth truth
- auth/session corrections SHOULD prefer explicit lineage and state transitions over hidden destructive rewrites

Representative semantic fields include:

### linked_auth_method
- `linked_method_id`
- `account_id`
- `provider_type`
- `provider_subject_id`
- `status`
- `verification_state`
- `created_at`
- `last_used_at`
- risk or block markers where applicable

### auth_session
- `session_id`
- `account_id`
- `auth_method_reference`
- `session_class`
- `state`
- `issued_at`
- `expires_at` or validity policy reference
- `revoked_at` or invalidation timestamp where applicable
- lineage reference
- client or device metadata where policy allows
- risk markers where applicable

### auth_challenge
- `challenge_id`
- challenge type or mode
- linked account or pending link context where appropriate
- provider reference
- state
- expiry
- correlation reference

---

## Read Model / Projection / Reporting Rules

Read models, support views, analytics views, exports, dashboards, and search projections MAY summarize auth/session state only if they preserve the following rules:

- they must be clearly marked as derived
- they must be regenerable from canonical truth
- they must not become mutation owners
- they must not invent auth/session states that cannot map back to canonical semantics
- they must not silently coalesce multiple accounts or method bindings into one access identity
- they must not hide conflict, review, containment, or remediation posture where that posture materially affects interpretation

---

## Security / Risk / Abuse Controls

This domain is security-critical.

The platform MUST preserve:

- provider uniqueness
- controlled provider linking
- safe unlink behavior
- session revocability
- account-state authority over sessions
- risk override over ordinary session continuation
- high-sensitivity treatment for recovery-complete and access-reset actions
- privileged-path separation for support/admin actions
- backend validation of provider completion and challenge integrity

FUZE SHOULD support recent-auth or stronger verification for high-risk actions.

Security and risk controls MAY suspend, restrict, or invalidate ordinary session continuation when additional protection is required.

---

## Boundary Violation Detection / Non-Canonical Patterns

The following patterns are non-canonical and forbidden unless a future approved spec explicitly and narrowly permits them:

- one account model per product
- one identity model for Web2 flows and another unrelated model for wallet-aware flows
- using provider profile data as canonical identity truth
- treating login success as automatic authorization success
- allowing sessions to outrank account or risk state
- allowing frontend logic to become source of truth for auth state
- adding new providers through product-local one-off exceptions that bypass the shared model
- silently reassigning provider subjects between accounts
- using reports, exports, or support dashboards as hidden auth/session write surfaces
- letting support operators mutate auth/session state outside bounded control-plane paths
- using email-match heuristics as an implicit merge engine

---

## Audit / Traceability Requirements

Every material auth/session mutation MUST preserve durable traceability.

At minimum, sensitive actions SHOULD record or emit:

- actor identity or initiator type
- target account reference
- linked-method or session reference where relevant
- action type
- reason code where privileged or security-sensitive
- correlation / trace identifier
- policy version or policy reference where applicable
- resulting lifecycle transition
- timestamp and operator lineage where applicable

Audit evidence MUST be durable enough to reconstruct why issuance, denial, invalidation, link, unlink, disablement, restoration, or conflict escalation occurred.

---

## Failure Handling / Edge Cases

### Replayed Callback or Challenge Completion
The platform MUST reject or safely absorb replay without duplicate side effects.

### Provider Subject Already Linked Elsewhere
The platform MUST enter conflict/review posture, not silently overwrite.

### Same Email Across Different Providers
This is a review signal, not proof of the same canonical account.

### Attempted Removal of Final Viable Access Path
The platform MUST block ordinary self-service mutation unless approved recovery or controlled replacement exists.

### Stale Frontend Session After Backend Invalidation
Backend invalidation wins. Products MUST treat stale frontend state as non-authoritative.

### Recovery Completion After Active Sessions Exist
The platform SHOULD globally revoke or security-invalidate prior sessions according to recovery policy.

### Support or Admin Correction Request
Privileged correction MUST remain bounded, reason-coded, audited, and policy-constrained.

---

## Operational Considerations

Operational implementations SHOULD support:

- explicit monitoring for auth challenge failure, replay, and timeout patterns
- observability for login success, denial, review, and invalidation paths
- replay-safe mutation handlers for callback and session refresh endpoints
- durable metrics for provider-link collisions and continuity-blocked actions
- security alerting for abnormal provider or session patterns
- support tooling that reads derived summaries without bypassing canonical mutation boundaries

Operational convenience MUST NOT weaken canonical auth/session semantics.

---

## Migration / Compatibility / Supersession Considerations

Legacy login paths, product-local session assumptions, or older provider-link conventions MUST be migrated toward the model defined here.

Migration plans MUST preserve:

- stable `account_id` anchoring
- durable auth-link lineage
- explicit session invalidation behavior during cutover when trust may change
- compatibility adapters only as temporary boundaries, not permanent semantic forks
- full audit lineage for correction and remediation actions during migration

No migration may silently merge identities, silently strand access continuity, or silently transfer provider ownership across accounts.

---

## Implementation-Contract Guardrails

Downstream implementation contracts MUST preserve all of the following where relevant:

- account identity remains distinct from authentication method and session state
- only the owner domain may mutate canonical linked-auth, auth-challenge, and session truth
- continuity checks remain explicit for access-path addition, removal, replacement, disablement, and restoration
- side-effecting auth/session mutations are replay-safe and idempotent where API contracts permit retries
- privileged actions require reason codes, audit lineage, and policy-bounded authorization
- session continuation remains subordinate to account, auth-method, recovery, and risk posture
- provider-input data is treated as evidence, not canonical account truth
- read models and support views remain derived and non-authoritative

Downstream implementations MUST NOT optimize away explicit review posture, auth challenge durability, or security invalidation semantics for convenience.

---

## Downstream Execution Staging

Recommended downstream staging order:

1. establish canonical auth/session API contracts
2. finalize provider-resolution and continuity-compatible mutation flows
3. finalize detailed session lifecycle and security rules
4. finalize recovery/conflict handling contracts that depend on auth/session truth
5. propagate product integrations and operator tooling against the canonical APIs

This ordering preserves identity and access stability before narrower implementations specialize behavior.

---

## Required Downstream Specs / Contract Layers

This specification requires compatible downstream refinement in at least the following areas:

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
- product integration specifications

---

## Canonical Examples / Anti-Examples

### Canonical Example 1
A user signs into one FUZE product with Google, later adds Telegram in another FUZE product, and still resolves to the same `account_id`. This is canonical.

### Canonical Example 2
A user removes one linked login only after another verified method and recovery path remain available. This is canonical.

### Canonical Example 3
A password reset or recovery completion invalidates prior sessions and requires fresh authenticated runtime state. This is canonical.

### Anti-Example 1
A product creates a product-local `user_id` and treats it as the platform’s identity root. This is forbidden.

### Anti-Example 2
A provider callback with a matching email silently merges two previously separate accounts. This is forbidden.

### Anti-Example 3
A frontend continues trusting a stale browser session after backend invalidation. This is forbidden.

### Anti-Example 4
A support tool directly edits linked-method or session records without reason code, policy reference, and durable audit lineage. This is forbidden.

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
- product integration specifications

---

## Explicitly Deferred Items

The following are intentionally deferred to adjacent or downstream specifications:

- exact provider list and provider-specific resolution heuristics
- exact session token model and runtime session schema
- exact password, MFA, and step-up implementation detail
- exact support workflow procedures for recovery, merge, and remediation
- exact PII minimization and retention rules
- exact browser/mobile transport flags and infrastructure details
- exact admin console workflow design
- exact legal-identity or compliance-specific identity overlays

These deferrals do not weaken the canonical auth/session/linked-login model established here.

---

## Final Normative Summary

FUZE authentication, linked login, and session behavior are platform-owned foundations. One canonical account anchors identity. Multiple approved linked authentication methods may reach that account. Sessions represent temporary authenticated runtime presence only after successful authentication and policy checks. Authentication must remain separate from identity, workspace scope, authorization, entitlement, wallet-aware participation, and product-local behavior. Access-path changes must preserve continuity or fail closed into review or remediation. Products, frontends, support tools, and derived views may initiate or consume auth/session behavior, but they MUST NOT redefine canonical access truth.

This document is the canonical FUZE rule set for auth/session/linked-login semantics. Downstream APIs, storage models, events, products, and operator workflows must preserve the ownership boundaries, invariants, conflict rules, and safety constraints defined here.

---

## Quality Gate Checklist

- Canonical owner explicit for every material truth family: Yes
- Mutation boundaries explicit: Yes
- Adjacent boundaries explicit: Yes
- Truth classes explicit: Yes
- Conflict-resolution rules explicit where needed: Yes
- Default decision rules explicit where ambiguity could arise: Yes
- Non-canonical patterns called out clearly: Yes
- Operator/admin override paths bounded and audited: Yes
- Read-model, cache, reporting, and projection rules explicit: Yes
- Failure and degraded-mode behaviors explicit: Yes
- Downstream implementation guardrails explicit: Yes
- Dependencies and downstream impacts explicit: Yes
- Non-goals and deferred items explicit: Yes
- Strong enough for backend/API/data/runtime implementation without contradictory semantics: Yes
