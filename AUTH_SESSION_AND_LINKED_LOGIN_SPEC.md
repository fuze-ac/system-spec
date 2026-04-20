# AUTH_SESSION_AND_LINKED_LOGIN_SPEC

## Purpose

This document defines the canonical authentication, session, and linked-login architecture of the FUZE platform. Its purpose is to establish how canonical FUZE accounts are accessed, how sessions are created and managed, how multiple login methods are linked to one account, how provider-based authentication interacts with platform identity, and how sensitive authentication-related actions are governed and audited.

This specification exists to preserve a critical distinction in FUZE: **identity is the canonical account**, while authentication methods and sessions are controlled access mechanisms to that account. FUZE is a multi-product platform, so account access must remain durable, provider-flexible, auditable, and consistent across the ecosystem.

---

## Scope

This specification covers:

- the distinction between account identity, authentication methods, and sessions
- the supported linked-login model
- login and session lifecycle behavior
- session issuance, renewal, revocation, and invalidation rules
- linked-provider add/remove logic
- account access continuity across products
- authentication-related security controls
- support and recovery implications for linked login methods
- audit and control requirements for sensitive auth/session actions

This specification does not redefine canonical account identity or workspace ownership. Those are refined in:

- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `WALLET_AWARE_USER_SPEC.md`

---

## Design Goals

The design goals of the FUZE auth, session, and linked-login model are:

1. to allow one canonical account to be accessed through multiple approved login methods
2. to keep authentication mechanics separate from identity truth
3. to support secure and durable cross-product access continuity
4. to let users add or change access methods without fragmenting account ownership
5. to support account security, revocation, and recovery in a controlled way
6. to make session behavior auditable and policy-driven
7. to prevent products from inventing separate authentication systems
8. to support future provider expansion without changing canonical identity rules

---

## Non-Goals

This specification is not intended to:

- make each authentication provider a separate user identity
- let products define their own standalone login models
- treat sessions as permanent identity truth
- support uncontrolled provider linking or silent account merges
- make wallet signature alone the universal login path unless later approved explicitly
- define full MFA architecture unless later added as a formal platform capability
- define legal identity verification or KYC logic

---

## Canonical Access Principle

The primary access principle of FUZE is:

> one canonical account may be reached through one or more approved authentication methods, and each successful authentication event may issue one or more controlled sessions, but neither the authentication method nor the session becomes the canonical identity.

This means:

- the account is the owner of identity
- the linked login method is the owner of provider access mapping
- the session is the owner of temporary authenticated runtime access
- products consume authenticated account context but do not redefine it

This principle is mandatory across the ecosystem.

---

## Core Concepts

### Account
The canonical FUZE identity object. It is defined in `IDENTITY_AND_ACCOUNT_SPEC.md`.

### Authentication Method
An approved method by which a user may prove access to the account. Examples include:
- email + password
- Google
- Telegram
- future approved providers

### Linked Login
A persisted binding between a canonical FUZE account and a provider-specific authentication method.

### Session
A temporary authenticated runtime context issued after successful authentication or refresh according to platform policy.

### Primary Access Path
The set of linked methods the account can currently use to regain access.

### Sensitive Auth Action
Any action that changes the security posture of account access, such as:
- adding a login provider
- removing a login provider
- password reset
- primary email change
- forced logout of sessions
- recovery override
- provider unlink after risk review

---

## Supported Linked Login Model

FUZE supports a multi-provider linked-login model.

### Approved access methods at current scope

- email + password
- Google
- Telegram

Future providers may be added only by explicit platform approval and must follow the same canonical account-linking rules.

### Canonical rule

A linked login method does not create a separate identity. It becomes an access path to the same canonical account.

### Allowed patterns

- account created with email/password, later linked with Google
- account created with Google, later linked with Telegram
- account with multiple linked providers for resilience and user convenience

### Disallowed pattern

A product or auth provider must not silently create an unrelated second account for the same intended user context when a proper linking or resolution path should occur.

---

## Authentication Method Model

At minimum, each linked authentication method must support:

- linked account ID
- provider type
- provider-scoped subject identifier
- verification state
- created_at timestamp
- last_used_at timestamp where available
- status (active, disabled, pending, removed, etc.)
- risk markers where applicable

### Provider uniqueness rules

Each provider-specific subject identifier must be unique within that provider type at the platform level.

Examples:
- one Google subject ID cannot be linked to two different active FUZE accounts
- one Telegram user identity cannot be linked to two different active FUZE accounts without explicit controlled migration or merge logic

---

## Session Model

Sessions are temporary authenticated runtime access records tied to a canonical account.

### A session must represent:

- the authenticated account
- the auth method used or session lineage
- issuance time
- expiry time or validity window
- revocation state
- device/client metadata where policy allows
- risk flags where applicable

### Sessions are not:

- canonical identity
- proof of long-term ownership
- substitutes for linked-login relationships

### Session classes

FUZE may support multiple session classes, such as:

- web interactive session
- mobile/app session
- long-lived refresh-capable session lineage
- privileged admin session
- support-review session

The semantic separation matters even if some are implemented through the same session infrastructure.

---

## Canonical Session Lifecycle

The canonical session lifecycle is:

1. **issued**
2. **active**
3. **refreshed** or rotated where supported
4. **expired**
5. **revoked**
6. **invalidated by security event**
7. **closed by logout**

A session may end due to:
- explicit logout
- expiry
- account restriction/suspension
- password reset or provider-security event
- forced administrative or support revocation
- suspicious-activity triggered invalidation
- global account-access reset

These transitions must be auditable.

---

## Login Flow Principles

All login flows must resolve into the same platform identity model.

### Email + Password Flow
The user proves access through a stored secret and verification checks. If successful, the platform issues session state for the canonical linked account.

### Google Flow
The user authenticates via Google. The provider subject is matched to a linked authentication method. If a match exists, the platform resolves the canonical account and issues session state.

### Telegram Flow
The user authenticates via Telegram according to the approved provider flow. The Telegram identity is matched to a linked authentication method. If a match exists, the platform resolves the canonical account and issues session state.

### First-time provider sign-in
If the provider subject is not linked yet, the platform must decide between:
- new account creation
- link-to-existing-account flow
- conflict or review state

This decision must be explicit and controlled.

---

## Linked Login Addition Rules

Adding a linked login method is a sensitive account action.

### A linked login may be added when:

- the user is already authenticated into the canonical account, and
- the provider-specific identity passes verification and uniqueness checks, and
- linking does not conflict with an already-linked active account elsewhere

### Required protections

- re-authentication or equivalent confirmation may be required for sensitive link additions
- uniqueness checks must be enforced
- provider ownership must be verified
- link-add actions must be audited

### Result of success

The account gains an additional access path, but the canonical account ID does not change.

---

## Linked Login Removal Rules

Removing a linked login method is also a sensitive account action.

### Removal requirements

A linked method may be removed only if:

- the requesting actor is authorized to modify the account, and
- the platform determines that account access resilience is preserved, or
- the removal is part of a controlled recovery/support flow

### Safety rule

The system must not allow removal of the final viable access path if that would leave the account inaccessible without an approved recovery mechanism.

### Removal examples

- removing Google while keeping email/password
- removing Telegram while keeping Google
- disabling an auth method after a compromised-provider concern

### Removal audit

Provider unlink events must be logged with:
- acting account
- removed provider type
- timestamp
- reason or action context
- support/admin actor if applicable

---

## Account Access Continuity Rules

One of the most important functions of linked login is access continuity.

FUZE must allow a user to retain access to the same account even if:

- they prefer a new login method
- they lose access to one provider
- they change primary email
- they operate across multiple products
- they need to add a backup access path

The account must remain the stable anchor.

### Continuity implications

- credits continuity must remain tied to the account/workspace, not to one login provider
- workspace membership must remain tied to the account
- wallet links must remain tied to the account
- product history must remain tied to the account
- audit trails must remain tied to the account even when access methods change

---

## Session Issuance Rules

Session issuance occurs only after successful authentication and post-auth policy checks.

### Required issuance checks

At minimum, the platform must verify:

- linked provider mapping or valid email/password match
- account status allows access
- auth flow is complete and not in conflict state
- session-policy checks pass
- risk-based restriction checks pass where applicable

### Session issuance outputs

A valid session should provide enough information to:

- identify the canonical account
- resolve workspace access context
- authorize product access
- support audit traceability
- support session invalidation later

The exact token/cookie/session transport shape is implementation-specific, but the semantics above are mandatory.

---

## Session Refresh and Renewal Rules

If FUZE supports session refresh or rolling validity, refresh must remain subordinate to the canonical account and risk rules.

### Refresh principles

- refresh extends access; it does not redefine identity
- refresh must fail if the account becomes restricted or suspended
- refresh must fail if the auth lineage is invalidated by a high-risk event
- refresh lineage must be revocable
- refresh behavior must remain auditable at a meaningful level

The platform may use:
- short-lived access session + longer refresh mechanism
- rolling session renewal
- other equivalent secure models

The exact mechanism may vary, but the behavioral semantics must remain consistent.

---

## Session Revocation and Invalidation

FUZE must support both targeted and broad revocation.

### Targeted revocation
Revoke one device/session lineage due to:
- logout on one device
- device compromise suspicion
- user-requested session cleanup

### Global revocation
Revoke all active sessions for an account due to:
- password reset
- recovery flow completion
- suspicious account takeover event
- account suspension
- sensitive provider-linking correction
- support-initiated security reset

### Invalidation precedence

If an account is suspended or materially restricted, active sessions must not continue granting normal access indefinitely. Session invalidation must reflect account-state authority.

---

## Sensitive Authentication Actions

The following actions are classified as sensitive:

- password set or reset
- primary email change
- linked provider add
- linked provider remove
- global logout / forced session reset
- account recovery completion
- duplicate-account merge affecting login paths
- security review restriction affecting access methods

Sensitive actions require:
- audit events
- stricter authorization or re-verification
- support/admin review where applicable
- explicit failure handling

---

## Relationship to Workspace and Product Access

Authentication gives access to the canonical account. It does not by itself determine all workspace or product rights.

After successful authentication:

1. account identity is resolved
2. session is issued
3. workspace context may be selected or resolved
4. product entitlements are checked
5. role and access control is applied

This separation matters because:
- one account may belong to multiple workspaces
- one account may have different product rights in different contexts
- a successful login does not imply unrestricted product access

Products must rely on platform auth/session results plus platform authorization context, not on provider identity alone.

---

## Relationship to Wallet-Aware Participation

Authentication methods are not the same as wallet links.

A user may authenticate with:
- email/password
- Google
- Telegram

and separately have:
- one or more linked wallets
- holder-aware privileges
- snapshot-related eligibility context

Wallet linking is an adjacent domain. Authentication proves access to the platform account; wallet links attach participation context to that account.

Products must not assume that a Google login or Telegram login is equivalent to wallet ownership.

---

## Account Recovery and Linked Login Recovery Implications

Recovery is one of the hardest parts of a linked-login platform and must preserve the canonical account model.

### Recovery principles

- recovery restores access to the same canonical account
- recovery must not accidentally create duplicate accounts
- recovery must not orphan linked providers, credits, workspaces, or wallet context
- recovery actions must be auditable
- support-assisted recovery must be treated as highly sensitive

### Recovery examples

- user lost email access but still has Google linked
- user lost Google access but still has Telegram linked
- user lost all routine access and requires support-assisted recovery under policy

Recovery flows are refined operationally elsewhere, but they must remain consistent with this spec.

---

## Duplicate Account and Conflict Handling

Because multiple providers exist, conflict handling is mandatory.

### Common conflict cases

- user signs in with Google expecting an existing email-based account
- Telegram login arrives for a provider subject that should map to an existing account
- provider subject already linked elsewhere
- attempted provider link collides with another active account

### Required behavior

The platform must not silently merge, silently overwrite, or silently split identity.

Instead, it must resolve through:
- explicit account-link flow
- explicit conflict state
- explicit support/review flow
- controlled merge process where approved

Identity integrity is more important than login convenience in these cases.

---

## Audit Requirements

The auth/session/linked-login system must generate audit records for at least:

- successful login
- failed login where policy requires logging
- session issued
- session revoked
- global logout
- linked login added
- linked login removed
- password reset initiated/completed
- recovery completed
- suspicious-auth restriction event
- admin or support auth-related intervention

Audit output must be strong enough to support security review, support investigation, and platform integrity.

---

## Minimum Data Model Requirements

At minimum, the auth/session/linked-login model must support:

### Linked Authentication Method
- account_id
- provider_type
- provider_subject_id
- status
- verification_state
- created_at
- last_used_at

### Session
- session_id
- account_id
- auth_method_reference
- issued_at
- expires_at or validity rule
- revoked_at or revocation state
- client/device metadata where allowed
- risk markers where applicable

### Auth Event / Audit Record
- event_type
- account_id
- provider or session reference
- actor type (user/support/admin/system)
- timestamp
- action result
- correlation or trace reference where applicable

---

## Security and Control Implications

This domain is security-critical.

The platform must preserve:

- provider uniqueness
- controlled provider linking
- safe unlink behavior
- session revocability
- account-state authority over sessions
- auditability of auth changes
- privileged-path separation for support/admin actions

No product may bypass these controls by issuing product-local login state that behaves like canonical platform access.

---

## Edge Cases and Failure Handling

### Provider outage
If Google or Telegram is unavailable, the canonical account still exists. Only that access path is degraded.

### Last linked method removal attempt
The platform must block or reroute the action if it would strand the account without a safe recovery path.

### Suspended account with active session
Account-state restriction must override routine session continuity.

### Stale session after password reset
Global invalidation or policy-equivalent session reset must occur.

### Product receives provider identity but no valid FUZE session
The product must not treat raw provider identity as authenticated platform identity.

### Duplicate provider subject detected
The platform must enter controlled conflict handling, not silent overwrite.

### Recovery completes after suspected compromise
The platform may revoke all prior sessions and require fresh trusted login state.

---

## Open Items

The following items are intentionally refined in downstream or operational specifications:

- exact session transport mechanism (cookie, token, hybrid, etc.)
- exact refresh-token/rotation strategy
- exact MFA policy if enabled later
- exact provider onboarding sequence details
- exact support tooling for linked-login conflict resolution
- exact password complexity and secret storage rules

These do not weaken the canonical auth/session/linked-login model defined here.

---

## Closing Summary

FUZE uses a canonical access model in which one account may be reached through multiple approved linked login methods, and successful authentication may issue controlled sessions for runtime access. The account remains the canonical identity. Authentication methods are access paths. Sessions are temporary authenticated state. This separation allows FUZE to support provider flexibility, cross-product continuity, secure recovery, auditable access changes, and long-term platform coherence without fragmenting user identity across products or providers.
