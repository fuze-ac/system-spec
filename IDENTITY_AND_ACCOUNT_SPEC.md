# IDENTITY_AND_ACCOUNT_SPEC

## Purpose

This document defines the canonical identity and account architecture of the FUZE platform. Its purpose is to establish the source-of-truth model for user identity, account lifecycle, identity continuity, linked authentication methods, security-sensitive account behavior, and the relationship between account identity, workspace membership, wallet-aware participation, and product access across the FUZE ecosystem.

This specification is foundational because FUZE is a multi-product platform, not a collection of isolated applications. Identity must therefore function as a shared platform capability rather than a product-local feature.

---

## Scope

This specification covers:

- the canonical identity model for FUZE
- account lifecycle and account states
- user identity versus authentication method distinction
- linked login method model
- account continuity across products
- identity ownership and authority boundaries
- account-level security and recovery rules
- relationship between identity, sessions, workspaces, wallets, and product entitlements
- identity-related edge cases and failure handling
- implementation rules for downstream services and products

This specification does not define detailed session issuance logic, wallet-link details, or workspace semantics beyond the identity boundary. Those are refined in:

- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `WALLET_AWARE_USER_SPEC.md`

---

## Design Goals

The design goals of the FUZE identity and account system are:

1. to create one canonical user identity across the FUZE ecosystem
2. to preserve account continuity across all current and future products
3. to separate identity truth from authentication method mechanics
4. to support multiple linked login methods without fragmenting user identity
5. to support business, workspace, and wallet-aware participation contexts without collapsing them into the same concept
6. to make identity durable enough for product expansion, billing continuity, auditability, and governance-aware actions
7. to make account security, recovery, and ownership changes explicit and controlled
8. to prevent products from redefining platform identity semantics

---

## Non-Goals

This specification is not intended to:

- define product-local profile models as canonical identity
- define every authentication provider integration detail
- make wallet addresses the sole identity model of the platform
- treat sessions as identity truth
- treat workspaces as substitutes for user accounts
- allow product domains to create parallel user identity systems
- define full KYC, legal identity, or compliance verification frameworks unless later added explicitly

---

## Canonical Identity Principle

The primary identity principle of FUZE is:

> one human or system actor should map to one canonical FUZE account identity, which may be accessed through multiple linked authentication methods and may participate across multiple products, workspaces, and wallet contexts.

This means:

- the account is the canonical identity container
- login methods are access paths to the account
- sessions are temporary access state, not identity truth
- wallets are participation-linked artifacts, not the full identity model
- products consume identity; they do not redefine it

This principle is required for platform coherence.

---

## Canonical Identity Model

FUZE identity is centered on the **Account**.

The account is the canonical platform identity object. It represents the persistent user-level identity that may:

- authenticate through one or more linked login methods
- belong to or create workspaces
- hold Platform Credits directly or through workspace context
- link one or more wallets
- access multiple products
- accumulate audit, billing, and usage history
- participate in token-aware features and payout-related contexts through linked wallet relationships

The account is therefore the root user object of the ecosystem.

### Identity Object Hierarchy

The canonical identity hierarchy is:

1. **Account**
2. **Linked Authentication Method(s)**
3. **Session(s)**
4. **Workspace Membership(s)**
5. **Wallet Link(s)**
6. **Product Access / Product Profile Extensions**

This hierarchy must be preserved in all downstream architecture.

---

## Core Identity Concepts

### Account
The permanent canonical identity object for a user or actor in FUZE.

### Linked Authentication Method
A method by which the account may be accessed, such as email/password, Google, Telegram, or future approved providers.

### Session
A temporary authenticated access state issued after successful authentication. Sessions are not the canonical identity.

### Workspace Membership
A relationship between an account and an organization/workspace context.

### Wallet Link
A relationship between an account and a wallet address or wallet set used for token-aware and participation-aware platform logic.

### Product Profile Extension
A product-local representation derived from the platform account for product-specific UX or settings, without becoming a new canonical identity.

---

## Account Lifecycle

The account lifecycle in FUZE follows the stages below.

### 1. Created
The account is created through an approved identity-entry path.

### 2. Active
The account is active and may authenticate, join workspaces, consume services, link wallets, and use platform products according to policy.

### 3. Restricted
The account remains present but may be limited due to abuse controls, unresolved security issues, payment risk, policy violations, or other platform-enforced restrictions.

### 4. Suspended
The account is suspended from normal platform use pending review, recovery, compliance, abuse handling, or administrative action.

### 5. Closed / Deactivated
The account is no longer available for normal use, subject to platform retention, legal, audit, and recovery policies.

### 6. Merged (Exceptional Case)
An account may be merged into another canonical account under controlled support/admin procedures if duplicate identities must be resolved safely.

### 7. Deleted / Hard-Removed (Exceptional and Policy-Bound)
If hard deletion is supported in limited cases, it must follow data-retention, audit, and legal policy and should be treated as an exceptional path rather than ordinary lifecycle behavior.

Account lifecycle transitions must be auditable.

---

## Canonical Account States

At minimum, the platform must support the following account status states:

- `pending_setup`
- `active`
- `restricted`
- `suspended`
- `deactivated`
- `merged`
- `closed`

Additional implementation states may exist, but these semantic states must remain expressible.

---

## Identity Entry Paths

FUZE must support multiple entry paths into the same canonical account system.

### Approved identity entry paths include:

- email + password
- Google
- Telegram
- future approved linked-login providers

The purpose of multiple entry paths is access flexibility, not identity fragmentation.

Each entry path must ultimately resolve to a canonical account object. A user who later adds Google to an email-created account should still have one account, not two identities.

---

## Linked Authentication Model

Authentication methods are linked to the account.

### Rules

1. An account may have multiple linked authentication methods.
2. A linked method does not create a new canonical account if it is attached to an existing one.
3. A login method must have uniqueness rules appropriate to its provider.
4. Provider-specific identifiers must be stored separately from the canonical account ID.
5. Account recovery or support operations must be able to inspect linked methods without redefining identity ownership.

### Examples

- one account with email/password + Google
- one account with Google + Telegram
- one account with email/password + Telegram + future provider

This model is essential to account continuity.

---

## Identity Versus Authentication Distinction

The system must preserve the distinction between identity and authentication.

### Identity answers:
- who is this actor in FUZE?
- what account owns this platform history?
- what workspaces and products is this actor connected to?
- what wallet links and audit trails are associated with this account?

### Authentication answers:
- how did this actor prove access right now?
- which provider or secret was used?
- should a session be issued?

The system must not confuse the two.

This distinction matters because:
- accounts persist across provider changes
- sessions expire
- login methods may be added or removed
- product access should survive changes in login preference
- wallet participation context should remain tied to the account, not to one auth provider

---

## Identity Ownership and Authority

The canonical owner of identity in FUZE is the **Platform Identity Domain**.

This domain owns:

- account creation rules
- account uniqueness rules
- canonical account identifiers
- account status transitions
- linked-auth relationships
- identity merge rules
- identity deactivation rules
- identity ownership semantics across the ecosystem

Products may read and extend from identity, but may not redefine it.

### Products may not:

- create alternative canonical user IDs for platform actors
- treat a product-local user table as the source of truth for platform identity
- reinterpret wallet addresses as the full identity model
- bypass identity lifecycle rules

---

## Account Continuity Across Products

FUZE is a multi-product platform. Therefore, account continuity is mandatory.

A single canonical account must be able to:

- log into multiple FUZE products
- maintain billing continuity across products
- preserve workspace membership across products
- preserve wallet links across products
- maintain audit continuity across products
- maintain credits continuity across products where account-scoped
- preserve participation-related context across products

This continuity is one of the strongest reasons identity must be platform-owned.

### Product Rule

Products may create product-specific settings or product-specific local profiles, but these must remain children or extensions of the canonical platform account, not replacements for it.

---

## Relationship to Workspace and Organization Layer

The account exists independently of any one workspace.

An account may:

- create a workspace
- join one or more workspaces
- leave a workspace while retaining platform identity
- belong to multiple collaborative contexts
- have personal and workspace-scoped balances or entitlements depending on policy

The account is therefore the human or actor identity layer. The workspace is a collaboration and billing context layered on top.

The identity layer must not collapse into workspace ownership. Likewise, workspace existence must not become required for canonical account identity to exist.

---

## Relationship to Wallet-Aware User Layer

The account is also distinct from wallet identity.

A wallet link is a relationship attached to an account, not the entire account itself.

This distinction matters because:

- an account may have multiple wallets
- wallets may change over time
- token balance truth comes from Ethereum, not from the account table
- the same account may use products that do not require wallet context
- the same account may need both traditional SaaS continuity and Web3 participation continuity

The identity model must therefore support wallet-aware participation without becoming wallet-only identity.

---

## Relationship to Platform Credits and Billing

The account is one of the possible holders of Platform Credits and one of the owners of billing history, subject to workspace-level ownership rules.

This means the identity layer must support:

- account-scoped billing history
- account-scoped credits where applicable
- account-level payment method history where supported
- account-level receipt visibility
- account-level usage traceability

However, the account does not own billing policy itself. That belongs to platform billing and credits domains.

The account is the identity anchor through which those relationships are expressed.

---

## Relationship to Product Access and Entitlements

The identity layer provides the subject against which entitlements are evaluated.

This means:
- products do not grant access to an anonymous provider identity
- they grant access to a canonical FUZE account or to a workspace in which the account participates
- product-specific privileges, access levels, and paid unlocks should ultimately resolve to the canonical account/workspace model

This is necessary for coherent access control across products.

---

## Account Security Model

The account system must support a platform-grade security model.

### Minimum security concerns include:

- authentication provider validation
- credential protection where passwords exist
- session invalidation hooks
- linked-method addition/removal controls
- suspicious-login handling
- account restriction and suspension controls
- recovery-channel validation
- sensitive change verification
- auditability of high-impact identity actions

### High-impact identity actions include:

- primary email change
- password reset
- linked-provider add/remove
- wallet link changes where policy treats them as sensitive
- account merge
- account recovery ownership transfer
- account deactivation/reactivation

These actions must be auditable and policy-controlled.

---

## Account Recovery Principles

Account recovery must be supported without weakening the identity model.

### Recovery principles:

1. Recovery restores access to the canonical account; it does not create a new account.
2. Recovery must verify enough proof to reduce unauthorized takeover risk.
3. Recovery flows must be auditable.
4. Recovery flows must not silently orphan linked workspaces, credits, or wallet contexts.
5. Recovery methods may vary by linked login paths, but the restored identity must remain the same canonical account.

Support-assisted recovery should be treated as a sensitive action and must generate audit events.

---

## Duplicate Account Prevention and Merge Policy

Because FUZE supports multiple login methods, duplicate-account risk must be handled explicitly.

### Prevention goals:

- reduce accidental duplicate accounts
- detect conflicts between provider identities and existing accounts
- support controlled linking when the user legitimately owns multiple auth paths

### Merge policy principles:

- merging accounts is exceptional, not normal
- merges must be support/admin-controlled or policy-controlled
- merges must protect audit continuity
- merges must handle linked methods, credits, workspace membership, wallet links, and product entitlements carefully
- merged-from identities must remain traceable in audit records

Products must not implement their own hidden merge logic.

---

## Identity Data Model Requirements

At minimum, the identity system must be able to represent:

### Account
- platform account ID
- status
- creation time
- update time
- canonical profile metadata
- security / lifecycle markers

### Linked Authentication Method
- provider type
- provider-scoped identifier
- linked account ID
- verification state
- creation time
- disable/remove state where supported

### Identity Audit Events
- account creation
- linked method added
- linked method removed
- account restricted/suspended
- recovery executed
- merge executed
- deactivation or closure action

Additional fields are defined downstream, but these semantic structures are required.

---

## Identity Read / Write Rights

### The Identity Domain may:
- create accounts
- update account lifecycle state
- attach and detach linked auth methods
- expose canonical identity read models
- coordinate identity merges and recovery flows

### Product domains may:
- read canonical account identity
- attach product-local profile extensions
- attach product-local preferences
- request access checks against identity/workspace state

### Product domains may not:
- mutate canonical identity lifecycle directly
- redefine account status semantics
- attach provider identities outside the identity domain
- create hidden alternate account IDs for platform actors

### Reporting domains may:
- derive and present identity-related metrics or summaries
- join identity information into reports where policy allows

Reporting domains may not redefine identity truth.

---

## Identity Integration Points

The identity and account system integrates with:

- auth/session service
- workspace and organization service
- role and access control service
- wallet-aware participation service
- billing and credits services
- audit log service
- product domain services
- control-plane / support operations
- reporting services

Every integration must treat the identity domain as canonical for account truth.

---

## Edge Cases and Failure Handling

### Login provider outage
If a provider is temporarily unavailable, the canonical account still exists. Only that access path is impaired.

### User loses access to one linked method
The user may still access the same canonical account through another linked method if policy allows.

### Duplicate sign-up attempt via another provider
The system must detect and resolve whether the actor should link to an existing account or create a new one under explicit rules.

### Workspace removed but account remains valid
The account remains canonical and active unless separately restricted.

### Wallet unlinked or changed
The account remains canonical. Wallet-aware participation context changes, not account identity itself.

### Product-local profile corruption
The platform account remains canonical even if a product extension profile is broken or missing.

### Suspicious security event
The account may be restricted or suspended without deleting the underlying identity.

### Reporting mismatch
If a reporting surface shows stale identity information, the platform identity domain remains canonical.

---

## Open Items

The following areas are intentionally refined in downstream specifications:

- exact provider list and provider-specific linking rules
- exact session token model
- exact password and MFA policy where applicable
- exact support workflow for merges and recovery
- exact PII minimization and retention rules
- exact user-profile versus account-profile field boundaries

These do not weaken the canonical identity model established here.

---

## Closing Summary

FUZE identity is built around one canonical account model that persists across products, linked login methods, workspaces, wallets, billing contexts, and product entitlements. The account is the root identity object of the ecosystem. Authentication methods are access paths to the account, not separate identities. Sessions are temporary access state, not identity truth. Wallets are linked participation artifacts, not the full identity model. Products may extend platform identity, but they may not redefine it. This structure allows FUZE to behave as one coherent platform ecosystem rather than a disconnected set of applications.
