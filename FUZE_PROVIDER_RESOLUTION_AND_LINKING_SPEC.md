# FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC

## Document Metadata

- Document Name: `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Governing Layer: Platform core / provider resolution and linked provider mapping
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, security, support operations, audit, governance, API design, operations
- Primary Purpose: Define the canonical FUZE model for resolving external authentication providers into canonical platform accounts and for linking, unlinking, disabling, reviewing, and remediating provider-backed access paths without fragmenting identity or breaking access continuity
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
  - `IDENTITY_AND_ACCOUNT_SPEC.md`
  - `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
  - `WALLET_AWARE_USER_SPEC.md`
  - `AUTH_IDENTITY_API_SPEC.md`
  - `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- Primary Downstream Dependents:
  - `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
  - `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
  - `AUTH_IDENTITY_API_SPEC.md`
  - `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - `WORKSPACE_AND_ORGANIZATION_SPEC.md`
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
  - product integration specifications

---

## Purpose

This specification defines the canonical FUZE provider-resolution and provider-linking model.

Its purpose is to make explicit:

- how external provider identities are normalized into canonical FUZE account outcomes
- how provider-scoped subjects are treated as durable access-path bindings rather than alternate identities
- how sign-in, sign-up, link, unlink, disable, restore, and remediation flows must behave
- how the platform decides among account resolution outcomes such as:
  - existing-account resolution
  - new-account bootstrap
  - controlled link completion
  - conflict / review state
- how continuity, auditability, and anti-fragmentation rules constrain provider-related behavior
- how provider-linked access changes interact with sessions, recovery, workspaces, wallet-aware context, and downstream product behavior
- how products, frontends, and admin/support tools must consume provider-related truth without becoming owners of it

This document exists because FUZE supports multiple provider-backed access paths and product-specific login entry shapes. In such a platform, provider resolution is one of the highest-risk places for identity fragmentation, duplicate accounts, silent reassignment, unsafe linking, and support complexity. The provider-resolution model must therefore be explicit, conservative, and platform-owned.

---

## Scope

This specification governs:

- provider-scoped subject mapping to canonical FUZE accounts
- provider-backed linked login creation, activation, disablement, removal, and restoration
- provider resolution outcomes for sign-in, bootstrap, link, review, and remediation
- provider uniqueness and provider-subject ownership rules
- safe use of provider claims, email hints, and profile snapshots
- provider conflict detection and review posture
- continuity-preserving rules for provider changes
- provider-related audit and security requirements
- provider-resolution entity model and state model
- API-boundary implications for provider start, callback, completion, link, and remediation flows

This specification does not define:

- the canonical account identity model in full depth
- the full session lifecycle model
- the full recovery playbook
- the full workspace and authorization model
- the full wallet-aware participation model
- exact provider SDK implementation details
- exact OIDC / OAuth / messaging-provider technical transport details

Those are refined in downstream specifications.

---

## Out of Scope

This specification is explicitly out of scope for:

- product-local social-login UX design
- exact provider cryptographic callback validation mechanics
- exact password storage or MFA implementation
- exact support tooling UI
- exact browser/mobile token transport
- exact legal or compliance identity verification
- exact chain wallet-auth implementation if adopted later

---

## Design Goals

The design goals of the FUZE provider-resolution and linking model are:

1. Normalize many provider flows into one canonical account-access outcome model.
2. Prevent provider-backed identity fragmentation across FUZE products.
3. Treat provider-subject bindings as durable, auditable, security-sensitive access assets.
4. Preserve continuity when providers are added, removed, disabled, or recovered.
5. Support new-account bootstrap only when safe and intentional.
6. Support existing-account resolution without unsafe matching shortcuts.
7. Make ambiguity explicit through conflict/review posture rather than silent guesses.
8. Keep products and frontends as initiators and consumers, not owners, of provider resolution truth.
9. Preserve clear separation between provider identity, canonical account identity, session state, wallet links, and authorization.
10. Make provider-related support and security interventions replay-safe, auditable, and policy-controlled.

---

## Non-Goals

This specification is not intended to:

- make each provider its own platform identity
- let products maintain separate provider-link truth
- use provider profile fields as canonical identity keys
- silently merge accounts because two providers appear similar
- silently create second accounts when safe linking or review is required
- allow provider unlink or reassignment without continuity and conflict checks
- treat sessions as a substitute for correct provider-account mapping
- let provider callbacks directly define product authorization

---

## Core Principles

### 1. Canonical Account Resolution Principle
Every provider flow must resolve into a canonical FUZE account outcome, not a product-local or provider-local identity outcome.

### 2. Provider Subject Principle
A provider-backed access method is anchored by a provider-scoped stable subject identifier, not by mutable profile hints.

### 3. Durable Link Principle
A provider link is a durable relationship between one canonical account and one provider subject.

### 4. No Silent Guessing Principle
When provider completion does not resolve safely, the platform must enter explicit review, conflict, or bootstrap posture rather than silently guessing.

### 5. No Silent Reassignment Principle
A provider subject must not be silently moved from one account to another.

### 6. Continuity Before Convenience Principle
Removing or replacing provider links must preserve account continuity even if that adds friction.

### 7. Backend-Normalization Principle
Different providers may have different external protocols, but the backend must normalize them into one FUZE-owned outcome model.

### 8. Products Consume, They Do Not Own Principle
Products may expose provider choices and consume provider-resolution results, but they may not own provider mapping truth.

---

## Canonical Definitions

### Provider
An approved external identity or login source used to authenticate access to a FUZE account.

### Provider Subject
The stable provider-scoped identifier for the actor inside that provider ecosystem.

### Provider Resolution
The backend-owned process that maps a validated provider result to one canonical FUZE account outcome.

### Provider Link
The durable relationship between a canonical account and one provider-backed authentication method.

### Provider Bootstrap
A controlled flow in which a provider result leads to creation of a new canonical account because no safe existing-account resolution applies.

### Provider Link Intent
A controlled flow in which an already authenticated account is intentionally linking an additional provider-backed access path.

### Provider Conflict
A state in which the platform cannot safely resolve a provider result to exactly one canonical account and one correct provider-link outcome.

### Provider Review State
A controlled posture in which provider resolution or linking is paused for explicit confirmation, security review, or support/admin remediation.

### Provider Claims Snapshot
A cached, non-canonical snapshot of provider profile claims captured for convenience, review, or audit support.

### Provider Resolution Outcome
The canonical result of a provider flow, such as:
- resolved_existing_account
- bootstrap_required
- link_completed
- conflict_review_required
- risk_review_required

---

## Architectural Position in the Spec Hierarchy

This document sits below:

- `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`

and above:

- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`

This document does not redefine canonical account identity or session lifecycle in full. It defines the provider resolution and provider-linking rules those domains must preserve.

---

## Canonical Provider-Resolution Rule

FUZE MUST preserve the following rule:

> A validated provider result must resolve to exactly one canonical account outcome through backend-owned rules. The platform must not silently split, merge, overwrite, or reassign account identity because of provider completion.

This is the central provider-resolution rule of the platform.

---

## What Provider Resolution Must Accomplish

A correct provider-resolution system must answer four questions safely:

1. Which provider is this result from?
2. Which provider subject does it represent?
3. Does that subject already map to a linked access path on a canonical FUZE account?
4. If not, is the correct next step:
   - bootstrap a new canonical account,
   - link to an already authenticated account,
   - open a conflict/review state,
   - or reject the flow?

The platform must answer those questions consistently across products.

---

## Canonical Ownership and Authority

### Identity Domain Owns
- canonical account identity
- provider-to-account resolution outputs
- identity conflict/remediation state
- provider-linked account bootstrap decisions
- account-side anti-fragmentation rules

### Auth / Session / Linked Login Domain Owns
- provider-link lifecycle state
- provider-auth challenge lifecycle
- provider start and completion orchestration
- session establishment after successful provider resolution
- auth-side conflict and continuity checks during link mutation

### Recovery / Remediation Domain Owns
- controlled restoration when provider state becomes unusable
- support-reviewed corrective transitions
- operator-reviewed provider reassignment only where policy explicitly allows

### Products May
- initiate provider login flows
- initiate link-intent flows for already authenticated users
- display provider choices and outcomes
- consume resulting authenticated account context

### Products Must Not
- directly write provider-link truth
- directly decide account merge or reassignment
- use provider profile data as canonical identity truth
- create product-local provider-to-user mapping tables that act like canonical identity linkage

---

## Provider Subject Rules

FUZE MUST treat the provider subject as the durable provider-side binding key.

### Required rules
1. Each provider-backed auth method must store a provider-scoped stable subject identifier.
2. The provider subject must be normalized into a canonical backend representation.
3. One active provider subject must map to at most one canonical account at a time unless an explicit controlled remediation process is underway.
4. Provider uniqueness must be transactionally enforced.
5. Provider claims snapshots must not replace provider subject truth.

### Examples
- OIDC-style providers: issuer + subject
- messaging providers: provider-specific stable subject
- future wallet-auth providers: normalized chain + wallet subject, only if separately approved

---

## Use of Email and Other Profile Hints

Email, display name, avatar, phone, or other provider-returned profile data may be useful, but they are not sufficient by themselves to define canonical identity resolution.

### Rules
- email may be used as a hint, review signal, or contact reference
- profile fields may be used to enrich review context
- mutable profile fields must not become the sole canonical matching key
- two providers returning the same email must not automatically force account merge
- missing or changed provider email must not by itself justify silent account split

This conservative rule protects against provider drift, mutable claims, shared emails, and support ambiguity.

---

## Canonical Provider Resolution Outcomes

FUZE SHOULD normalize provider completion into a small set of canonical outcomes.

### 1. `resolved_existing_account`
The provider subject already maps to a linked method on a canonical account, and the platform may continue toward session issuance.

### 2. `bootstrap_required`
The provider subject is not yet linked and no safe existing-account resolution applies, so the platform may begin or complete new-account bootstrap according to policy.

### 3. `link_completed`
The provider flow was intentionally initiated from an already authenticated account as a link-intent flow, and the provider subject has been safely attached to that same account.

### 4. `conflict_review_required`
The provider completion cannot be resolved safely because a collision, ambiguity, or anti-fragmentation risk exists.

### 5. `risk_review_required`
The provider completion is syntactically valid but cannot proceed without security or operator review.

### 6. `resolution_denied`
The provider flow is invalid, replayed, blocked by policy, or otherwise not allowed to proceed.

These outcomes make provider behavior more consistent across products, APIs, and support operations.

---

## Provider Flow Types

FUZE should distinguish at least the following provider flow types.

### Sign-In Flow
The actor is trying to authenticate with a provider in order to reach an existing or new canonical account.

### Bootstrap Flow
The actor is entering FUZE through a provider and no prior safe link exists, so the platform may create a new canonical account.

### Link-Intent Flow
The actor is already authenticated into a canonical account and is intentionally adding a provider-backed access path.

### Re-Verification / Step-Up Flow
The actor uses provider validation in support of a sensitive auth action.

### Review / Remediation Flow
The provider result is being handled under support/security/operator posture rather than ordinary user self-service.

Each flow type should use different preconditions and guardrails.

---

## Existing-Account Resolution Rules

When a provider subject is already linked to an account:

- the platform should resolve to that same canonical account
- the provider flow should not create a new account
- account continuity rules remain in force
- session issuance may proceed only after ordinary account-state, risk-state, and policy checks pass

If the account is restricted or suspended, provider success does not override account state.

---

## Bootstrap Rules

New-account bootstrap through a provider may occur only when:

- no active provider-subject link already exists
- no safer link-intent flow is in force
- no explicit conflict state requires review
- policy allows new-account bootstrap from that provider path
- the bootstrap result will create exactly one canonical account

Bootstrap must not be used as a convenience fallback when the correct action is conflict handling or safe link-to-existing-account flow.

---

## Link-Intent Rules

Provider linking is a sensitive action and must be explicitly differentiated from ordinary sign-in.

A provider may be linked to an existing account only when:

- the actor is already authenticated into the target canonical account or is in another explicitly approved secure linking flow
- the provider result passes validation
- the provider subject is not already actively linked elsewhere
- continuity, risk, and recent-auth conditions pass
- the link action is durable and auditable

A successful link-intent flow increases access resilience but does not change the canonical account ID.

---

## Provider Unlink / Disable / Restore Rules

### Unlink
A provider link may be removed only if:
- the requester is authorized,
- the action does not strand the account without another viable access path or approved recovery/remediation path,
- and the action is auditable.

### Disable
A provider link may be disabled under:
- compromise suspicion,
- provider policy problems,
- operator review,
- risk containment,
- or support-approved remediation.

Disabled methods remain historically attached but unusable for ordinary login.

### Restore
A disabled method may be restored only through approved owner-controlled pathways and only if no conflicting mapping or risk condition remains.

Unlinking, disabling, and restoring are all continuity-sensitive actions.

---

## Continuity Constraints on Provider Changes

This spec inherits and applies the continuity rule that ordinary self-service mutation must not strand the account.

Therefore:

- removing the last viable provider must fail unless another viable method or approved recovery path exists
- replacing one provider with another is safe only when the replacement becomes viable before the old path is removed, or when a controlled migration flow guarantees continuity
- disabling a provider for risk reasons requires immediate continuity recalculation
- provider correction during support remediation must preserve identity continuity and audit lineage

Provider convenience must never outrank continuity integrity.

---

## Conflict Rules

Provider conflicts must be explicit.

### Common conflict cases
- provider subject already linked to another active account
- user appears to have created multiple accounts through different provider paths
- provider completion returns data suggesting an existing relationship, but safe proof is insufficient
- provider-email overlap suggests possible account match, but not safe enough for automatic merge
- a link-intent flow collides with an already linked subject

### Required behavior
The platform must not:
- silently overwrite the older mapping
- silently attach the provider to a second account
- silently merge accounts
- silently create a second account if that would hide the underlying conflict

Instead, it must:
- raise explicit conflict or review posture
- create durable remediation state
- preserve audit lineage
- require support/admin/security decision where needed

---

## Review and Remediation Rules

Provider-related remediation should be rare, explicit, and durable.

### Review may be required when:
- provider claims do not safely match existing account evidence
- a provider subject is already in use elsewhere
- a support case alleges misbinding
- a previously linked provider must be migrated
- replay or abuse is suspected
- account continuity would otherwise be broken

### Remediation actions may include:
- provider disablement
- unlink + replacement flow
- controlled reassignment with durable case records
- recovery-case escalation
- support-approved merge/remediation in coordination with identity and recovery domains

These actions require stronger authorization, reason codes, and audit outputs.

---

## Provider-Related Security Principles

Provider resolution is a security-sensitive boundary.

The platform must preserve:
- provider subject uniqueness
- anti-replay callback handling
- challenge-binding integrity
- recent-auth or step-up protection for sensitive linking/unlinking
- continuity checks for last-access-path removal
- account-state precedence over provider success
- operator review for ambiguous cases
- durable audit lineage for all sensitive mutations

A provider callback may prove something about the provider flow. It does not by itself override the canonical account model or platform risk controls.

---

## Provider Challenge and Callback Model

The platform SHOULD use durable short-lived challenge state for provider start and completion flows.

At minimum, provider challenge state should support:
- challenge creation
- provider intent (`login`, `link_provider`, `reverify`)
- expiry
- correlation to return payload
- replay protection
- completion terminalization

Provider callback completion must be idempotent and replay-safe.

---

## Canonical Entity Model

At minimum, the provider-resolution and linking model should support the following durable semantic structures.

### Provider Auth Request / Challenge
Representative semantic fields:
- `provider_auth_request_id`
- `provider_type`
- `intent`
- `request_state`
- `account_reference_if_authenticated`
- `created_at`
- `expires_at`
- `correlation_id`
- anti-replay reference

### Linked Provider Method
Representative semantic fields:
- `auth_method_id`
- `account_id`
- `provider_type`
- `provider_subject_id`
- `provider_email_snapshot`
- `provider_claims_snapshot_hash`
- `status`
- `verification_state`
- `linked_at`
- `last_used_at`
- risk markers where applicable

### Provider Conflict / Remediation Case
Representative semantic fields:
- `provider_case_id`
- `provider_type`
- `provider_subject_id`
- candidate account references
- case type
- case state
- reviewer/operator reference
- resulting action reference
- created_at / closed_at

### Provider Resolution Event
Representative semantic events:
- provider auth requested
- provider auth resolved
- provider link completed
- provider link removed
- provider link disabled
- provider conflict detected
- provider remediation completed
- provider resolution denied

---

## Read / Write Rights

### Identity Domain may:
- decide canonical account outcomes
- own conflict/remediation identity implications
- expose provider-to-account resolution results

### Auth / Session Domain may:
- manage provider challenges
- create and update linked provider method state
- coordinate session handoff after successful resolution
- enforce auth-side continuity and risk checks

### Product Domains may:
- initiate provider flows
- read resulting account-auth context
- display link/unlink status

### Product Domains must not:
- write provider-link durability
- override provider conflict outcomes
- invent alternate provider resolution rules
- treat provider callback output as canonical account truth

### Reporting / Support Views may:
- summarize provider-linked state and review status
- expose safe operational context

### Reporting / Support Views must not:
- redefine provider-link truth
- bypass mutation boundaries

---

## State Mutation Rules

### Deterministic Mutation Rule
Provider-link and provider-resolution truth may be changed only through explicit owner-controlled mutation boundaries.

### Pre-Commit Resolution Rule
A provider flow must reach a canonical normalized outcome before it can create account bootstrap, activate a provider link, or hand off session establishment.

### Uniqueness Rule
Provider subject uniqueness must be checked transactionally before activation.

### Continuity Rule
Provider removal or replacement must pass continuity evaluation before commit.

### Review Rule
Ambiguous provider outcomes must transition into explicit review/remediation posture rather than silent mutation.

### Audit Rule
Sensitive provider mutations and conflict outcomes must be auditable and reason-coded.

---

## Failure Handling and Edge Cases

### Provider Callback Replayed
The platform must treat replayed completion as idempotent or reject it safely, without creating duplicate side effects.

### Provider Subject Already Linked Elsewhere
The platform must enter conflict/review posture, not silently overwrite.

### Provider Returns Same Email as Another Account
This is a review signal, not proof of identical identity.

### Product Starts Provider Flow for Wrong Account Context
The backend must validate link intent against the authenticated account context and reject or reroute unsafe outcomes.

### Last Viable Provider Removal Attempt
The platform must block or reroute the action if it would strand the account without an approved alternative.

### Provider Compromised or Disabled
The account remains canonical. Continuity posture may degrade, and sessions may be invalidated as policy requires.

### Bootstrap Triggered When Existing Safe Link Should Have Been Used
This is a provider-resolution error and must be corrected explicitly, not hidden by later product logic.

### Support Requests Reassignment of Provider
This must use explicit remediation records and stronger authorization; it must not be treated as a casual edit.

---

## Audit / Traceability Requirements

FUZE must be able to determine:

- which provider subject was linked to which account at what time
- which provider resolution outcome occurred for a given provider flow
- whether a provider result created bootstrap, resolved an existing account, linked a method, or entered conflict
- why a provider link was removed, disabled, restored, or remediated
- how continuity checks affected provider mutations
- which operator or policy reference governed a remediation action
- whether a visible provider summary is canonical or derived

Provider resolution errors are high-impact and must be reconstructable.

---

## API / Contract Implications

The platform SHOULD expose provider-related behavior through explicit API boundaries.

### Identity APIs should support:
- provider auth request initiation
- provider callback resolution
- account bootstrap and identity-side resolution outcomes
- conflict/remediation references

### Session / Linked-Login APIs should support:
- provider link listing
- link-intent challenge creation
- link completion
- unlink / disable / restore flows
- continuity-aware mutation results

### Product APIs should not:
- directly resolve provider subjects into canonical identity without backend mediation
- bypass link/resolve conflict handling
- treat provider profile data as source-of-truth identity

### Admin / Support APIs should:
- preserve stronger authorization for provider remediation
- require reason codes and policy references
- expose audit-friendly action summaries

---

## Data Model / Storage Implications

This specification requires the following data-model discipline:

- provider-subject bindings must remain durable source-of-truth entities
- provider auth requests/challenges must remain explicit short-lived durable records
- provider claims snapshots are convenience caches only
- provider conflict/remediation records must be durable and auditable
- derived provider summaries must remain separate from canonical link truth
- replay and idempotency controls must remain durable mutation-safety artifacts
- provider-related corrections should prefer explicit lineage and terminal state transitions over hidden destructive rewrites

---

## Migration / Compatibility / Supersession Considerations

- migrations must not silently weaken provider subject uniqueness or continuity semantics
- new providers must be onboarded through the shared resolution model, not through product-local special cases
- compatibility layers may preserve older flows temporarily, but canonical provider-link semantics must remain platform-owned
- if older documents or implementations imply email-only matching or looser provider reassignment, this refined specification supersedes those interpretations within its scope

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
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
- `WALLET_AWARE_USER_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`

This specification directly governs or materially informs:

- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- product integration specifications

---

## Explicitly Deferred Items

The following are intentionally deferred to downstream specifications:

- exact provider list and onboarding sequence by product
- exact OIDC / OAuth / messaging-provider callback mechanics
- exact challenge storage model
- exact support workflow and approval routing for provider reassignment
- exact wallet-auth provider behavior if later adopted
- exact recent-auth and MFA requirements per mutation type

These do not weaken the canonical provider-resolution and linking model established here.

---

## Final Normative Summary

FUZE provider resolution and linking is the platform capability that turns many external provider flows into one coherent canonical account-access system. A provider subject is a durable access-path binding, not a separate identity. Provider completion must resolve into one explicit backend-owned outcome: existing-account resolution, controlled bootstrap, controlled link completion, review/remediation posture, or denial. The platform must not silently split, merge, overwrite, or reassign account identity because of provider behavior or product entry differences.

Provider-linked changes are continuity-sensitive, security-sensitive, and audit-sensitive. They must preserve canonical account continuity, preserve uniqueness of provider-subject ownership, and route ambiguity into explicit conflict or remediation posture. Products may initiate and consume provider flows, but they may not own provider-resolution truth.

This document is the canonical provider-resolution and linking rule set for the FUZE identity and access foundation.
