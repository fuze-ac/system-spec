# KEY_MANAGEMENT_AND_USER_RECOVERY_SPEC

## Document Metadata

- Document Name: `KEY_MANAGEMENT_AND_USER_RECOVERY_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Governing Layer: Platform core / key management and user recovery
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, security engineering, platform operations, support operations, audit, governance, API design, reliability engineering
- Primary Purpose: Define the canonical FUZE model for security-sensitive secret and key material that participates in authentication, account recovery, session restoration boundaries, recovery proof handling, and operator-assisted access restoration without weakening canonical account identity, access continuity, or auditability
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
  - `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
  - `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
  - `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
  - `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - `SECRETS_CONFIG_AND_ENVIRONMENT_SPEC.md`
  - `AUTH_IDENTITY_API_SPEC.md`
  - `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- Primary Downstream Dependents:
  - `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
  - `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
  - `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
  - `AUTH_IDENTITY_API_SPEC.md`
  - `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - support and admin control-plane workflows
  - product integration specifications that surface recovery and access mutation flows

---

## Purpose

This specification defines the canonical FUZE model for key management and user recovery.

Its purpose is to make explicit:

- which secret and key classes FUZE treats as security-critical within the identity and access foundation
- how password secrets, recovery-significant secret material, short-lived recovery proofs, provider-bound recovery artifacts, and operator-mediated recovery controls must be handled
- how user recovery may rely on approved proof and challenge mechanisms without redefining canonical account identity
- how key or secret recovery differs from account recovery, session restoration, provider resolution, wallet linkage, and authorization
- how recovery-sensitive key actions are owned, constrained, rotated, invalidated, audited, and contained
- how APIs, internal services, products, support tools, and operations must consume key and recovery state without becoming owners of identity truth

This document exists because FUZE is a multi-product platform with linked access methods, continuity requirements, support-assisted remediation, and recovery-sensitive security events. In such a platform, key handling cannot be treated as an isolated cryptography note or as a generic infrastructure secret practice. It must be explicitly coordinated with canonical account identity, linked access paths, recovery posture, session containment, operator controls, and long-term auditability.

---

## Scope

This specification governs:

- platform-owned handling of user-facing authentication and recovery secret material where FUZE is the controlling system
- password-equivalent secret lifecycle where password-based access exists
- short-lived recovery challenges, verification artifacts, and reset-capable proofs
- recovery-significant secret changes and their interaction with canonical account recovery
- security-sensitive rotation, invalidation, replacement, revocation, and recovery-completion effects for access-enabling secret material
- ownership boundaries among identity, auth/session, recovery/remediation, security/risk, support/admin, and product layers for key-related actions
- minimum data-model direction for secret metadata, recovery challenge state, key versioning references, and recovery-sensitive audit events
- API, event, runtime, monitoring, and operational requirements for key-management and user-recovery workflows
- continuity-safe and takeover-resistant user recovery behavior where secret mutation is part of restoring access to the same canonical account

This specification does not define:

- general infrastructure secret management for every internal service in full depth
- treasury, multisig, vault, or on-chain private-key custody policy
- full wallet custody or wallet-signing implementation details
- exact MFA factor catalog or factor-by-factor recovery productization
- exact browser cookie flags, token transport details, or mobile secure enclave implementation
- exact legal identity, KYC, or compliance evidence policy
- exact customer-support staffing procedures or queue tooling
- exact cryptographic primitive selection beyond the requirement that approved primitives and storage models meet platform security policy

Those areas must remain compatible with this specification, but they belong to adjacent or downstream documents.

---

## Out of Scope

This specification is explicitly out of scope for:

- product-local credential systems that attempt to replace the platform identity foundation
- hidden product-side password reset logic
- using wallet possession alone as universal recovery truth unless separately approved by platform policy
- treating provider profile data, email overlap, or active session presence as interchangeable with key recovery proof
- low-level cloud KMS vendor selection as the governing architectural truth
- on-chain treasury key policy
- ad hoc support overrides that bypass owning-domain mutation boundaries
- any recovery shortcut that creates a new canonical account instead of restoring the existing one

---

## Design Goals

The design goals of the FUZE key-management and user-recovery model are:

1. Preserve access restoration to the same canonical account rather than allowing key or secret reset mechanics to redefine identity.
2. Keep key and secret handling subordinate to the canonical account, linked-auth, continuity, and recovery models.
3. Prevent secret-reset convenience from becoming silent account takeover or silent account fragmentation.
4. Make recovery-significant secret changes explicit, auditable, replay-safe, and policy-controlled.
5. Support secure reset, rotation, replacement, and invalidation flows for approved account-access methods.
6. Distinguish durable secret truth from short-lived recovery proof, session presence, and provider-return convenience data.
7. Preserve clear owner boundaries among identity, auth/session, recovery/remediation, security/risk, products, and support tooling.
8. Enable support-assisted recovery only through narrow, reviewable, and policy-referenced pathways.
9. Ensure high-impact key events drive deterministic session-containment and continuity re-evaluation outcomes.
10. Keep future provider and recovery-method expansion possible without weakening the canonical FUZE identity foundation.

---

## Non-Goals

This specification is not intended to:

- make key possession the canonical identity model
- let products define separate recovery truth for the same actor
- allow recovery completion to proceed on weak or ambiguous proof
- reduce user recovery to “email match therefore same user”
- treat active sessions as sufficient long-term recovery authority
- define every infrastructure secret or machine credential in the FUZE estate
- define every MFA, biometric, or hardware-key UX detail
- permit operator convenience to outrank takeover resistance or auditability

---

## Core Principles

### 1. Canonical Account Preservation Principle
All user-recovery and secret-reset outcomes must preserve the same canonical `account_id`. Secret replacement is not identity replacement.

### 2. Secret Material Is Access Infrastructure, Not Identity Truth
Passwords, recovery proofs, reset tokens, and similar secret material enable controlled access flows. They do not become the canonical identity object.

### 3. Recovery-Proof Separation Principle
A recovery proof or reset challenge may authorize a bounded recovery action only if policy says so. It does not by itself become durable account truth.

### 4. No Silent Rebinding Principle
A recovery-capable key, reset path, or provider-bound proof must not be silently rebound from one canonical account to another.

### 5. Continuity Before Convenience Principle
If easy reset behavior conflicts with continuity safety, FUZE must preserve continuity safety.

### 6. Explicit Containment Principle
Password reset, recovery completion, secret replacement, or compromise-sensitive correction may require targeted or global session invalidation.

### 7. Operator Narrowness Principle
Support and admin intervention in key or recovery workflows must remain narrow, reason-coded, policy-bound, and auditable.

### 8. Backend Ownership Principle
Products and frontends may initiate recovery or reset flows, but canonical secret, challenge, and recovery mutation truth remain backend-owned and domain-owned.

### 9. Derivation Boundary Principle
Visible recovery summaries, “you can recover your account” hints, and device/session views are derived outputs. They must not become mutation owners.

### 10. Conservative Proof Principle
Weak profile similarity, email overlap, or remembered-session presence must not substitute for approved recovery proof in ambiguous cases.

---

## Canonical Definitions

### User Recovery
A controlled platform process for restoring the user’s ability to reach the same canonical account when ordinary access methods are unavailable or insufficient.

### Key Management
The platform-owned lifecycle management of security-sensitive secret or key material used to authenticate, verify, reset, rotate, revoke, or recover approved account-access methods.

### Authentication Secret
A FUZE-controlled secret or secret-equivalent artifact used to validate control of an approved access path, such as a password secret or recovery-significant secret verifier.

### Recovery Challenge
A short-lived durable challenge or verification record used to authorize a bounded recovery or reset step.

### Recovery Proof
The set of policy-approved evidence or completed challenge artifacts used to decide whether a recovery-sensitive action may proceed.

### Recovery-Significant Secret
A secret or secret-derived access-enabling relationship whose replacement, removal, or reset can materially affect future ability to reach the same account.

### Secret Version
A durable version reference or epoch for a secret-controlled access method used to coordinate reset, invalidation, containment, and audit reconstruction.

### Reset Completion
A bounded action that replaces or restores a recovery-significant secret or access path under approved policy while preserving the same canonical account.

### Secret Invalidation
A durable state transition that renders prior secret-derived access no longer valid for future authentication.

### Recovery-Ready Posture
A posture in which ordinary access is insufficient but approved recovery or remediation routes still exist.

### Compromise-Sensitive Recovery
A recovery or reset path that occurs under elevated suspicion of account takeover, provider misuse, or broader security incident, and therefore may require stronger containment or review.

---

## Architectural Position in the Spec Hierarchy

This document sits below:

- `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
- `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`

and above:

- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- security-control implementation specifications
- support/admin recovery workflow specifications
- product integration specifications that expose reset and recovery surfaces

This document does not redefine canonical account identity, provider resolution in full, session lifecycle in full, workspace authorization, or wallet verification. It defines how secret and recovery-sensitive key handling must coordinate with those domains when access restoration, secret reset, or compromise remediation occurs.

---

## System Boundaries

This document governs only the following platform-owned boundaries:

- user-facing access secrets where FUZE owns the validation or replacement logic
- recovery challenge and proof handling for secret-reset and access-restoration flows
- recovery-significant secret mutation outcomes and their containment effects
- secret-version and reset-lineage semantics needed for audit, replay safety, and session invalidation
- bounded coordination between recovery decisioning and auth/session execution

It does not govern:

- provider-side secrets that FUZE never possesses
- third-party identity-provider internal credential policy
- general service-to-service secret inventory for the whole platform estate
- on-chain custody of treasury or governance keys
- wallet private-key custody by end users
- downstream authorization decisions after valid runtime access is re-established

---

## Adjacent Boundaries

### Identity and Account Domain
Owns canonical `account_id`, identity continuity meaning, recovery admissibility at the identity layer, and conflict/remediation posture.

### Auth / Session / Linked Login Domain
Owns authentication-secret verification execution, linked-method secret-affecting lifecycle, session issuance, session invalidation, and auth challenge execution behavior.

### Recovery / Conflict Domain
Owns recovery case meaning, evidence sufficiency posture, ambiguity handling, review routing, and final “may restore the same account” decision.

### Security / Risk Domain
Owns broader risk thresholds, compromise posture, stronger containment triggers, secret-compromise severity policy, and privileged operator controls.

### Support / Admin Tooling
Consumes approved reset and recovery pathways, but does not own canonical secret truth, challenge truth, or account identity truth.

### Product Domains
May expose reset and recovery entry points and display safe status, but must not implement independent recovery truth, secret mutation truth, or conflict-resolution logic.

### Wallet-Aware Domain
Owns wallet-link truth and wallet verification semantics. Wallet linkage remains subordinate to the canonical account and is not default secret-recovery truth.

---

## Roles / Actors / Entities

### End User
May initiate approved reset or recovery flows, complete challenges, and consume status or completion outcomes.

### Authenticated User Performing Sensitive Mutation
May trigger secret replacement, password reset, or link/unlink changes that require recent-auth or stronger proof.

### Support Operator
May review evidence and execute narrow, approved remediation steps through privileged bounded flows.

### Security Reviewer
May require stronger review, deny unsafe reset or recovery, or authorize broader containment.

### Internal Recovery Workflow Service
May create and progress durable challenge or reset state through policy-bound transitions.

### Identity Domain Service
Owns the canonical relationship between recovery outcome and `account_id`.

### Auth / Session Service
Executes secret replacement effects, auth-method updates, and session invalidation after approved decisions.

### Secret Storage / Verification Component
May store secret verifiers, key version metadata, challenge hashes, or equivalent approved artifacts, but must remain subordinate to domain-owned mutation boundaries.

---

## Ownership Model

### Identity Domain Owns
- the rule that recovery and reset must preserve the same canonical account
- account-level admissibility of recovery outcomes
- relationship between recovery cases and `account_id`
- conflict posture when proof could map to more than one account
- final identity-safe decision on whether access restoration may proceed

### Auth / Session / Linked Login Domain Owns
- password or equivalent secret verification execution where FUZE controls it
- secret-version lifecycle for auth-controlled methods
- auth challenge state used to complete approved secret reset
- linked auth-method mutation execution after approved recovery or remediation
- session invalidation and containment consequences of reset completion

### Recovery / Conflict Domain Owns
- durable recovery-case and conflict-case semantics
- recovery evidence references and proof posture
- review routing, rejection, expiration, and closure semantics
- policy-bound distinction between automated completion and explicit review

### Security / Risk Domain Owns
- elevated risk signals that block, delay, or constrain reset/recovery
- compromise-driven secret invalidation policy
- severity tiers for containment and privileged handling
- emergency control posture for sensitive operator action classes

### Support / Admin Domains May
- read admin-safe case detail
- append review notes and approved evidence references
- invoke policy-authorized recovery or secret-reset actions through privileged audited endpoints

### Product Domains May
- initiate public or authenticated reset/recovery flows
- show safe recovery posture summaries
- route users toward approved flows

### Product Domains Must Not
- store or verify canonical password secrets outside owner-controlled boundaries
- mutate recovery-significant secret truth directly
- bypass conflict or risk review
- treat product-local user records as recovery anchors
- create fallback identity systems when reset or recovery becomes difficult

---

## Authority / Decision Model

The decision model for key-management and user-recovery is conservative and layered.

### Automated secret-reset or recovery completion is allowed only when:
- the target canonical account is unambiguous
- the recovery or reset path is policy-approved for the account posture
- proof satisfies the relevant challenge and risk requirements
- no active conflict, elevated-risk, or operator-review block exists
- the resulting continuity posture remains acceptable or is explicitly transitioned through approved recovery state

### Explicit review is required when:
- multiple candidate accounts appear plausible
- a provider-linked or password-linked recovery outcome collides with another account’s state
- a secret-reset request follows recent compromise indicators
- a recovery-significant contact or proof channel is disputed
- support alleges prior misbinding, mistaken merge, or unsafe secret replacement
- the requested change would otherwise strand, ambiguously reassign, or silently overwrite access truth

### Operator remediation is permitted only when:
- policy explicitly allows the action class
- stronger authorization and privileged-session rules are satisfied
- reason code, policy reference, and affected account references are recorded
- resulting continuity, session-containment, and secret-version effects are explicit

---

## Secret and Recovery Material Taxonomy

The platform must distinguish at least the following semantic categories:

### 1. Authentication Secrets
Examples include:
- password verifier inputs and stored password-derived verifiers
- secret-backed local account-access material where FUZE owns the verifier

These are durable access-enabling artifacts and require lifecycle controls.

### 2. Recovery Challenges
Examples include:
- reset request tokens
- short-lived verification codes
- bounded challenge references tied to a recovery or reset case
- proof-completion artifacts for sensitive mutation steps

These are short-lived and must not become durable identity truth.

### 3. Secret Metadata and Versioning
Examples include:
- current secret epoch or version
- last reset time
- reset-required markers
- invalidation reason codes
- compromise flags

These support replay safety, containment, and audit reconstruction.

### 4. Provider-Adjacent Recovery Signals
Examples include:
- approved provider callback evidence used within an already-approved recovery path
- provider-held reachability hints
- provider subject stability references

These may support recovery but must not silently override the canonical account and conflict model.

### 5. Operator Recovery References
Examples include:
- support case references
- review notes
- policy references
- remediation action IDs

These are administrative lineage artifacts, not replacements for secret truth or identity truth.

---

## State Model

### Secret-Controlled Access States
At minimum, the platform must support semantic states such as:

- `active`
- `pending_reset`
- `reset_in_progress`
- `reset_blocked_review`
- `invalidated`
- `disabled`
- `removed`

These exact names may vary, but the semantic distinctions must remain expressible.

### Recovery Challenge States
At minimum, the platform must support semantic states such as:

- `issued`
- `delivered_or_presented`
- `completed`
- `expired`
- `revoked`
- `consumed`
- `blocked_review`

### Secret Posture States
The platform should support at least the following semantic postures:

- `normal`
- `rotation_recommended`
- `reset_required`
- `recovery_only`
- `under_review`
- `blocked`

### State Rules
- state transitions must be explicit, auditable, and replay-safe
- terminal or terminal-equivalent states for consumed, expired, revoked, or invalidated artifacts must be durable
- stale read models must not redefine canonical challenge or secret truth
- secret metadata changes must preserve lineage rather than silently erasing prior security-significant history

---

## Lifecycle / Workflow Model

### 1. Recovery or Reset Initiation
Recovery-sensitive flows begin through approved public, authenticated, or operator-mediated entry points.

Required properties:
- initiation must be idempotent where policy requires
- public initiation must respect anti-enumeration rules
- initiation must create or reference durable case or challenge state
- initiation must not silently mutate canonical identity or linked-method ownership

### 2. Proof and Challenge Establishment
The platform establishes the approved proof path.

Required properties:
- proof requirements must be policy-bound and sensitivity-aware
- challenge artifacts must be short-lived, scope-bound, and durable enough for review or audit
- weak profile similarity is not sufficient proof
- proof may support progression, denial, or escalation to review

### 3. Conflict and Risk Evaluation
Before secret mutation or reset completion, the platform must evaluate:
- whether the target account is unambiguous
- whether the proof path collides with another account or disputed state
- whether continuity would be stranded or ambiguously reassigned
- whether compromise signals require stronger containment or explicit review

### 4. Approved Mutation Execution
If the reset or recovery step is approved, the platform may:
- replace a password or equivalent secret verifier
- re-enable an approved access path
- mark prior secret versions invalid
- require recent-auth or fresh login for subsequent sensitive actions
- create deterministic session-containment effects

### 5. Post-Completion Containment
After completion, the platform must evaluate:
- whether all prior sessions tied to the old secret or affected auth lineage must be globally invalidated
- whether targeted containment is sufficient
- whether continuity posture changes from `recovery_only` or `under_review` to a healthier state
- whether pending related recovery or conflict cases must be closed or superseded

### 6. Rejection / Expiration / Cancellation
If proof is insufficient, policy disallows the route, or the challenge expires, the platform must terminate the flow explicitly without hidden mutation.

---

## Invariants

1. Secret reset or recovery completion may restore access only to the same canonical `account_id` unless an exceptional, separately approved remediation process says otherwise.
2. Recovery-significant secret mutation must not silently create a new account.
3. Secret proof, email overlap, provider-return convenience data, or active-session presence alone must not justify hidden reassignment when ambiguity exists.
4. Sessions are runtime state, not durable secret recovery truth.
5. Products may consume reset and recovery outcomes but may not own them.
6. Recovery-significant secret changes must be auditable, reason-coded, and reconstructable.
7. Account continuity, workspace continuity, wallet-aware continuity, billing continuity, and audit continuity remain attached to the canonical account and must not be orphaned by reset or recovery.
8. Secret invalidation, risk posture, account state, and approved recovery posture outrank ordinary session continuation.
9. Recovery challenge artifacts must be scope-bound, expiry-bound, and safe against replay.
10. Operator convenience must not outrank takeover resistance.

---

## Functional Rules

### Password and Secret Verifier Rules
- FUZE-controlled password or equivalent access secrets must be stored and validated only through approved backend-owned mechanisms
- plaintext secret storage is forbidden
- replacement of a recovery-significant secret must create a new valid secret version or equivalent lineage marker
- prior secret-derived authentication should become unusable according to policy after reset completion
- products must not store shadow password truth

### Recovery Challenge Rules
- challenge issuance must be bounded by rate limits, anti-abuse controls, and anti-enumeration rules
- challenge state must be durable enough to enforce one-time or bounded-use semantics
- expired, consumed, or revoked challenges must not be accepted
- challenge completion must be bound to the intended action scope and account context
- replay-safe consumption is mandatory

### Reset Completion Rules
- completion must target the same account
- completion must not silently detach products, workspaces, wallets, billing continuity, or audit lineage from the account
- completion must explicitly record resulting secret version, reason code, and containment consequences
- completion after elevated risk may require stronger containment, forced re-authentication, or fresh trusted session establishment

### Conflict Rules
The platform must treat at least the following as explicit conflict or review conditions:
- proof could plausibly restore access to more than one canonical account
- a provider-backed recovery hint collides with another account’s stable provider subject
- a recovery-significant contact or secret reset path is disputed
- prior operator remediation, merge, or recovery history makes automatic continuation unsafe
- the account is already in `under_review`, `blocked`, or compromise-sensitive posture

### Resolution Rules
If conflict or ambiguity exists, the platform must not:
- silently merge accounts
- silently overwrite provider ownership
- silently reassign recovery-significant contact or access truth
- silently continue reset completion against ambiguous state

Instead, it must:
- create or continue durable review/remediation state
- freeze unsafe automation
- preserve continuity and audit lineage
- require support/admin/security review where policy demands

### Sensitive Mutation Rules
The following actions are recovery-sensitive or secret-sensitive and require stronger control:
- password set or reset
- primary or recovery-significant email change
- linked provider add or remove when continuity is affected
- provider disablement or restoration when recovery posture is affected
- account recovery completion
- duplicate-account merge or remediation affecting access paths
- support-led access correction
- global logout or forced session reset after trust-reset events

### Evidence Rules
- provider-stable subject is stronger than mutable profile data
- email may be used as hint, contact, or review signal, but not as sole canonical match key in ambiguous cases
- challenge-completion artifacts are stronger than unverifiable profile overlap, but remain subordinate to conflict and risk policy
- evidence storage must preserve lineage and reviewability
- insufficient proof must lead to explicit denial or review, not silent permissiveness

---

## Permission / Access Considerations

Key-management and user-recovery actions are high-sensitivity actions.

Required control expectations:
- privileged support/admin actions must require stronger authorization than ordinary user self-service actions
- privileged actions must run through privileged session classes or equivalent stronger control paths
- admin mutation requests must include reason code, policy reference, and actor confirmation
- internal service transitions must include service identity and scoped authority
- recent-auth or step-up proof should be required for authenticated sensitive secret mutations where policy requires
- support tools must not bypass owning-domain mutation boundaries

Successful recovery restores ability to authenticate the account. It does not bypass later workspace, role, permission, or entitlement evaluation.

---

## Entitlement Considerations

Key management and user recovery do not redefine entitlement truth.

Required rules:
- account-scoped or workspace-scoped entitlements remain attached to the canonical account and/or workspace according to their owning domains
- secret reset or recovery completion must not mint new entitlement state as a side effect unless explicitly owned and triggered by another domain
- remediation must not lose track of billing, credits, or product continuity because those relationships remain attached to the same account

---

## API / Contract Implications

The platform should expose secret-reset and user-recovery behavior through explicit boundaries.

### Identity APIs should support:
- recovery initiation and case-reference creation using safe response semantics
- account recovery posture reads that affect admissibility of reset or restoration
- conflict/remediation references and statuses
- admin-safe review and approved completion operations

### Session / Linked-Login APIs should support:
- password or secret reset initiation and completion
- linked-method mutation blocks with explicit continuity, review, or conflict reasons
- targeted and global session invalidation after approved reset or recovery events
- continuity-aware reset results and containment outputs

### API Rules
- all mutation-capable endpoints must require correlation identifiers
- idempotency is mandatory for recovery initiation, challenge consumption, reset completion requests, admin remediation actions, and session-containment actions where retries are plausible
- errors must distinguish validation failure, conflict, under review, policy denied, expired challenge, replay rejected, and dependency degraded without exposing unsafe internal detail
- async or review-required flows should return operation or case references rather than implying synchronous finality
- public responses must preserve anti-enumeration posture

---

## Event / Async Implications

Key-management and user-recovery behavior must emit durable domain events sufficient for audit, monitoring, and downstream coordination.

Representative semantic events include:
- `identity.recovery_initiated`
- `identity.recovery_status_changed`
- `identity.recovery_completed`
- `identity.recovery_rejected`
- `identity.secret_reset_initiated`
- `identity.secret_reset_completed`
- `identity.secret_invalidated`
- `identity.recovery_challenge_issued`
- `identity.recovery_challenge_consumed`
- `identity.conflict_detected`
- `identity.conflict_resolved`
- `auth.session_global_invalidated`
- `auth.session_targeted_invalidated`

Async workflow rules:
- challenge issuance, delivery, review routing, and completion may be orchestrated asynchronously, but canonical state must remain explicit and durable
- retries must be replay-safe and idempotent
- terminal events must not be emitted multiple times with contradictory meaning
- review workflows must preserve clear ownership between identity decisioning, recovery-case progression, and auth/session execution

---

## Data Model / Storage Implications

At minimum, the platform should support the following durable semantic structures.

### `account_secret_state`
Representative semantic fields:
- `account_id`
- secret class
- secret status
- current secret version or epoch
- last_rotated_at or last_reset_at
- reset_required_flag
- invalidation_reason_code
- compromise_marker where applicable

### `recovery_challenge`
Representative semantic fields:
- `challenge_id`
- related account or recovery-case reference where safe
- challenge type
- challenge status
- issued_at
- expires_at
- consumed_at
- correlation_id
- policy_reference

### `account_secret_mutation`
Representative semantic fields:
- `mutation_id`
- `account_id`
- mutation type
- prior version reference
- resulting version reference
- actor type
- reason code
- policy reference
- executed_at
- containment_effect

### `account_recovery_evidence_reference`
Representative semantic fields:
- evidence reference ID
- recovery case or challenge reference
- evidence type
- evidence status
- artifact hash or artifact reference
- collected_at
- collected_by_actor_type

### `account_access_remediation_action`
Representative semantic fields:
- remediation action ID
- related case reference
- action type
- action state
- actor reference
- reason code
- policy reference
- executed_at
- resulting continuity effect
- resulting session containment effect

### Data Rules
- secret and challenge state must be durable source-of-truth entities or durable state machines, not transient frontend-only artifacts
- stored secret verifiers and challenge artifacts must be minimized, hashed or equivalent protected form where appropriate, and access-controlled
- derived views may summarize posture, but must not own mutation
- destructive rewrite of prior security-significant truth should be avoided in favor of explicit lineage and terminal transitions
- evidence references should preserve traceability without overloading canonical account records
- internal machine secrets for service infrastructure belong to adjacent platform secret-management specs, but any overlap with user-recovery flows must preserve these semantics

---

## Security / Risk / Abuse Controls

Key-management and user-recovery are identity-and-access security controls.

The platform must preserve:
- anti-enumeration behavior where public exposure would weaken security
- anti-replay handling for recovery-sensitive completions and challenge consumption
- rate limits and abuse controls on initiation and challenge attempts
- stronger operator controls for privileged remediation
- recent-auth or step-up requirements where policy requires
- explicit containment after compromise-sensitive reset or recovery
- bounded exposure of internal decision reasons on public surfaces
- strong audit lineage for support or admin intervention
- denial or review posture when risk signals make ordinary continuation unsafe

Risk-aware rules:
- a suspected compromise may justify broader containment than ordinary reset
- elevated risk may require review even when proof would otherwise seem sufficient
- support convenience must not outrank takeover resistance
- secret-compromise posture must be able to invalidate prior trust deterministically

---

## Audit / Traceability Requirements

FUZE must be able to determine:

- when and why a secret-reset, recovery, or remediation case was created
- which canonical account was involved or which candidate accounts were considered
- which secret version or recovery challenge was affected
- which evidence types were collected and what decision they supported
- why a reset or recovery was completed, rejected, expired, blocked, or routed to review
- which auth methods and sessions were affected by completion or remediation
- which operator or service executed a privileged action
- how continuity posture changed before and after the action
- whether a visible recovery summary is canonical or derived

Auditability is required because recovery and secret-reset mistakes are high-impact, difficult to reconstruct later, and central to platform trust.

---

## Failure Handling / Edge Cases

### Lost Password, Other Method Still Exists
The platform may restore access through another approved path without redefining identity. Secret reset may be unnecessary if continuity posture remains acceptable.

### Last Viable Access Secret Lost
The platform must route toward approved recovery or remediation posture. It must not silently bootstrap a new account to avoid friction.

### Provider Subject Already Linked Elsewhere
The platform must raise explicit conflict/remediation state and must not attach the subject to a second account through recovery convenience.

### Same Email Returned by Different Provider
Email overlap alone must not force secret reset or account merge. The platform must use conservative resolution and may require review.

### Recovery Completes After Suspected Compromise
The platform may globally invalidate prior sessions, close affected challenges, and require fresh trusted session establishment.

### Stale Session Exists During Reset
A stale or remembered session must not be treated as sufficient proof for identity rewrite or secret replacement. Policy may use it as one signal, not as sole authority.

### Support Error During Remediation
The platform must preserve correction lineage and superseding actions rather than silently rewriting history.

### Wallet Link Remains but Ordinary Access Is Lost
Wallet-aware context remains historically attached to the account, but wallet linkage alone does not become default recovery truth unless separately approved by policy.

### Replay of Challenge Consumption
Consumed, revoked, or expired challenges must fail deterministically and audibly without duplicating reset effects.

### Partial Reset Completion Followed by Downstream Failure
The platform must preserve a reconstructable terminal or compensating state rather than leaving hidden ambiguity about whether the old or new secret version is canonical.

---

## Operational Considerations

Operational systems should support:
- bounded review queues for recovery, secret-reset, and conflict cases
- correlation across public, internal, admin, and session-containment actions
- secure secret-verifier storage and controlled access to secret metadata
- visibility into challenge aging, expiration, delivery failures, and unresolved high-severity cases
- deterministic operator tooling with narrow action classes
- monitoring for repeated replay, repeated reset attempts, unusual recovery concentration, or suspicious operator patterns
- support playbooks that reference policy without bypassing domain ownership
- operational metrics for reset-success rate, challenge-failure rate, review routing rate, session-containment frequency, and abuse blocking outcomes

The operational model must preserve the principle that recovery and reset are structured platform workflows, not ad hoc support edits.

---

## Migration / Compatibility / Supersession Considerations

- migrations must not silently weaken the requirement that recovery and reset restore access to the same canonical account
- compatibility layers may preserve older product entry flows temporarily, but must not preserve product-local secret ownership or hidden reset logic
- older documents or implementations that imply email-only matching, implicit merge, implicit provider reassignment, active-session-overrides, or support-side silent rewrites are superseded within this scope
- new providers and new recovery methods must plug into the same key-management, continuity, conflict, and containment model rather than creating one-off exceptions
- state names may evolve, but the semantic distinctions defined here must be preserved

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
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
- `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `SECRETS_CONFIG_AND_ENVIRONMENT_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`

This specification directly governs or materially informs:

- `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- support/admin workflow specifications
- product integration specifications

---

## Explicitly Deferred Items

The following are intentionally deferred to adjacent or downstream specifications:

- exact password hashing algorithm, parameter tuning, and rotation cadence
- exact MFA factor matrix and factor-recovery UX
- exact browser/mobile transport and client-side secure storage details
- exact cloud KMS, HSM, or enclave vendor architecture
- exact legal-identity or KYC evidence policies
- exact wallet-auth-as-recovery policy if ever approved
- exact service split, queue topology, or database partitioning for challenge processing
- exact human support queue routing and staffing procedures

These do not weaken the canonical key-management and user-recovery model established here.

---

## Final Normative Summary

FUZE key management and user recovery is the platform capability that controls how access-enabling secret material is created, validated, reset, replaced, invalidated, and reviewed without weakening canonical account identity. Secret handling is part of the identity and access foundation, but secret material is not itself the identity root. Recovery and reset restore access to the same canonical account; they do not create a new identity path, silently merge accounts, silently reassign provider ownership, or silently orphan cross-platform continuity.

Recovery-significant secret changes are high-sensitivity workflows. They must be policy-controlled, continuity-safe, backend-owned, auditable, replay-safe, and coordinated with recovery-case decisioning, linked-auth state, session containment, and account-state precedence. Products may surface and consume these flows, but they may not redefine them. This document is the canonical FUZE rule set for user-facing key and secret lifecycle behavior where recovery, reset, and access restoration intersect with the long-term FUZE identity foundation.
