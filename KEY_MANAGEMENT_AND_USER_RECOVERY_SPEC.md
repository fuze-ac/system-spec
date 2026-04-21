# KEY_MANAGEMENT_AND_USER_RECOVERY_SPEC

## Title
FUZE Key Management and User Recovery Specification

## Document Metadata

- Document Name: `KEY_MANAGEMENT_AND_USER_RECOVERY_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Version: 1.2.0
- Effective Date: 2026-04-21
- Last Updated: 2026-04-21
- Reviewed On: 2026-04-21
- Document Owner: FUZE Platform Identity and Access Architecture with secret-lifecycle execution delegated to the Auth / Session domain and supporting security controls delegated to the Security / Risk domain
- Approval Authority: FUZE Platform Architecture and Governance Authority
- Review Cadence: Quarterly or upon material change to password-backed access, recovery proof policy, session-containment policy, compromise-response policy, provider-resolution policy, or support/admin remediation controls
- Governing Layer: Platform core / key management and user recovery
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, security engineering, product engineering, platform operations, support operations, audit, governance, API design, reliability engineering, implementation-contract authors
- Primary Purpose: Define the canonical FUZE model for security-sensitive secret and key material that participates in authentication, secret reset, account recovery support, recovery proof handling, session-containment consequences, and operator-assisted access restoration without weakening canonical account identity, access continuity, or auditability
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
  - product integration specifications that surface reset, recovery, access-mutation, or compromise-response flows
- Supersedes: Earlier or less explicit FUZE key-management, password-reset, reset-token, and user-recovery writeups to the extent they conflict within this document’s scope
- Superseded By: Not yet known
- Related Decision Records: Not yet known
- Canonical Status Note: This document is the governing FUZE key-management and user-recovery specification. Downstream APIs, products, workflows, support tooling, security tooling, and operational playbooks MUST NOT reinterpret the semantics established here.
- Implementation Status: Normative architecture baseline; downstream API, workflow, storage, event, runtime, support, and audit contracts must conform
- Approval Status: Drafted for refined-system inclusion; formal approval record not yet attached
- Change Summary:
  - aligned key-management and reset semantics with the current canonical account/access/session, session-security, and account-recovery refinements
  - tightened separation between secret truth, recovery-proof truth, session truth, provider-input truth, and canonical identity truth
  - strengthened secret-versioning, challenge lifecycle, conflict/default decision rules, containment effects, and operator-control requirements
  - clarified non-goals and boundary exclusions so this spec does not collapse into infrastructure-secret management, provider-owned credentials, or wallet custody policy
  - expanded implementation-contract guardrails for idempotency, replay safety, audit lineage, degraded-mode handling, and downstream mutation discipline

## Purpose

This specification defines the canonical FUZE model for key management and user recovery.

Its purpose is to make explicit:

- which secret and key classes FUZE treats as security-critical within the identity and access foundation
- how password secrets, reset-capable proof artifacts, short-lived recovery challenges, recovery-significant secret material, and operator-mediated recovery controls must be handled
- how user recovery may rely on approved secret-reset and proof mechanisms without redefining canonical account identity
- how key or secret recovery differs from account recovery, session restoration, provider resolution, wallet linkage, and authorization
- how recovery-sensitive key actions are owned, constrained, rotated, invalidated, audited, and contained
- how APIs, internal services, products, support tools, and operations must consume key and recovery state without becoming owners of identity truth

This document exists because FUZE is a multi-product platform with linked access methods, continuity requirements, support-assisted remediation, and recovery-sensitive security events. In such a platform, key handling cannot be treated as an isolated cryptography note or as a generic infrastructure-secret practice. It must be explicitly coordinated with canonical account identity, linked access paths, recovery posture, session containment, operator controls, and long-term auditability.

## Scope

This specification governs:

- platform-owned handling of user-facing authentication and recovery secret material where FUZE is the controlling system
- password-equivalent secret lifecycle where password-based access exists
- short-lived recovery challenges, verification artifacts, and reset-capable proofs
- recovery-significant secret changes and their interaction with canonical account recovery
- security-sensitive rotation, invalidation, replacement, revocation, and recovery-completion effects for access-enabling secret material
- secret-version and reset-lineage semantics needed for replay safety, auditability, and session containment
- ownership boundaries among identity, auth/session, recovery/remediation, security/risk, support/admin, and product layers for key-related actions
- minimum data-model direction for secret metadata, challenge state, version epochs, and recovery-sensitive audit events
- API, event, runtime, monitoring, and operational requirements for key-management and user-recovery workflows
- continuity-safe and takeover-resistant user recovery behavior where secret mutation is part of restoring access to the same canonical account

This specification does not define in full depth:

- general infrastructure-secret management for every internal service
- exact cryptographic primitive selection or KDF parameterization beyond compliance with approved security policy
- exact browser cookie flags, token transport details, or mobile secure enclave implementation
- full MFA factor catalog or factor-by-factor recovery productization
- full provider-protocol mechanics or provider-owned credential policy
- exact legal identity, KYC, or compliance evidence policy
- exact support queue tooling or staffing procedures
- exact hardware security module or KMS vendor selection
- wallet private-key custody, treasury custody, governance key custody, or chain-signing policy

These areas belong to adjacent or downstream specifications and must remain compatible with this document.

## Out of Scope

This specification is explicitly out of scope for:

- product-local credential systems that attempt to replace the platform identity foundation
- hidden product-side password reset logic
- using wallet possession alone as universal recovery truth unless separately approved by platform policy
- treating provider profile data, email overlap, or active session presence as interchangeable with key recovery proof
- low-level cloud KMS vendor selection as the governing architectural truth
- on-chain treasury key policy
- end-user wallet private-key custody
- ad hoc support overrides that bypass owning-domain mutation boundaries
- any recovery shortcut that creates a new canonical account instead of restoring the existing one

## Design Goals

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

## Non-Goals

This specification is not intended to:

- make key possession the canonical identity model
- let products define separate recovery truth for the same actor
- allow recovery completion to proceed on weak or ambiguous proof
- reduce user recovery to “email match therefore same user”
- treat active sessions as sufficient long-term recovery authority
- define every infrastructure secret or machine credential in the FUZE estate
- define every MFA, passkey, biometric, or hardware-key UX detail
- permit operator convenience to outrank takeover resistance or auditability
- replace downstream API, schema, runbook, or security-control specifications

## Core Principles

### 1. Canonical Account Preservation Principle
All user-recovery and secret-reset outcomes MUST preserve the same canonical `account_id`. Secret replacement is not identity replacement.

### 2. Secret Material Is Access Infrastructure, Not Identity Truth
Passwords, recovery proofs, reset tokens, and similar secret material enable controlled access flows. They do not become the canonical identity object.

### 3. Recovery-Proof Separation Principle
A recovery proof or reset challenge MAY authorize a bounded recovery action only if policy says so. It does not by itself become durable account truth.

### 4. No Silent Rebinding Principle
A recovery-capable key, reset path, or provider-bound proof MUST NOT be silently rebound from one canonical account to another.

### 5. Continuity Before Convenience Principle
If easy reset behavior conflicts with continuity safety, FUZE MUST preserve continuity safety.

### 6. Explicit Containment Principle
Password reset, recovery completion, secret replacement, or compromise-sensitive correction MAY require targeted or global session invalidation.

### 7. Operator Narrowness Principle
Support and admin intervention in key or recovery workflows MUST remain narrow, reason-coded, policy-bound, and auditable.

### 8. Backend Ownership Principle
Products and frontends MAY initiate recovery or reset flows, but canonical secret, challenge, and recovery mutation truth remain backend-owned and domain-owned.

### 9. Derivation Boundary Principle
Visible recovery summaries, “you can recover your account” hints, and device/session views are derived outputs. They MUST NOT become mutation owners.

### 10. Conservative Proof Principle
Weak profile similarity, email overlap, or remembered-session presence MUST NOT substitute for approved recovery proof in ambiguous cases.

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

## Truth Class Taxonomy

This specification distinguishes the following truth classes and downstream implementations MUST preserve those distinctions.

### 1. Canonical Identity Truth
The canonical account record, account lifecycle, and identity-domain continuity semantics anchored by `account_id`.

### 2. Auth-Link Truth
The durable approved mapping between a canonical account and its linked authentication methods or provider-backed access paths.

### 3. Secret / Key-State Truth
Durable secret state, version epochs, invalidation posture, reset-required posture, and challenge or reset lineage owned by the key-management domain.

### 4. Recovery / Conflict Case Truth
Durable recovery-case and conflict-case state, including review posture and approved restoration outcomes, owned by the recovery domain.

### 5. Runtime Session Truth
Temporary authenticated runtime presence, session lineage, revocation state, and security invalidation state.

### 6. Policy Truth
Recovery policy, security policy, continuity policy, operator-control policy, secret-handling policy, and higher-order platform rules.

### 7. Provider-Input Truth
Validated external inputs supplied by providers or approved adapters. These are evidence inputs, not canonical identity or secret truth.

### 8. Wallet-Aware Context Truth
Wallet-link state and wallet-derived participation context. This remains attached context, not default secret-recovery truth.

### 9. Derived Read-Model Truth
Support views, dashboards, reset summaries, review queues, search projections, and convenience views derived from canonical secret or case records.

### 10. Reporting / Public View Truth
Exports and user-facing status summaries that describe reset or recovery posture without becoming mutation owners.

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

This document does not redefine canonical account identity, provider resolution in full, session lifecycle in full, workspace authorization, or wallet verification. It defines how secret and recovery-sensitive key handling MUST coordinate with those domains when access restoration, secret reset, or compromise remediation occurs.

## System Boundaries

This document governs only the following platform-owned boundaries:

- user-facing access secrets where FUZE owns validation or replacement logic
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
May expose reset and recovery entry points and display safe status, but MUST NOT implement independent recovery truth, secret mutation truth, or conflict-resolution logic.

### Wallet-Aware Domain
Owns wallet-link truth and wallet verification semantics. Wallet linkage remains subordinate to the canonical account and is not default secret-recovery truth.

## Conflict Resolution Rules

When multiple layers disagree, the platform MUST resolve key-management and reset-related disagreements in the following order unless a higher-order platform policy explicitly overrides it:

1. canonical identity-domain records and restriction state
2. canonical recovery-case or conflict-case truth
3. canonical secret / key-state records and linked-auth records
4. explicit policy and security constraints
5. validated provider-input or challenge evidence interpreted through approved rules
6. runtime session or client state
7. derived views, dashboards, queues, reports, or product-local caches

Specific conflict rules:

- email overlap MUST NOT justify secret reset against ambiguous identity state
- provider callback success MUST NOT justify secret mutation when recovery conflict exists
- stale active session state MUST NOT override reset blocks or conflict posture
- wallet presence MUST NOT justify account ownership rewrite or reset completion
- support tooling views MUST NOT be treated as authoritative if they diverge from canonical case or secret-state records
- product-local user records MUST NOT become recovery anchors when they diverge from canonical account truth

## Default Decision Rules

Where ambiguity exists, the following defaults apply:

1. default actor anchor: `account_id`
2. default recovery goal: restore access to the same canonical account, not a replacement account
3. default owner of recovery and admissibility meaning: Identity and Account Domain
4. default owner of secret lifecycle and reset mutation execution: Auth / Session Domain under approved policy
5. default interpretation of challenge completion: bounded evidence, not standalone durable identity truth
6. default interpretation of email or contact overlap: hint or review signal, not sole canonical matching proof
7. default interpretation of active session presence: temporary runtime signal, not durable reset authority
8. default interpretation of wallet linkage: attached context, not universal reset proof
9. default resolution for ambiguous reset or secret replacement: explicit review or denial, not silent automation
10. default posture under degraded evidence or high-risk uncertainty: fail closed for high-impact access actions

## Roles / Actors / Entities

### End User
May initiate approved reset or recovery flows, complete challenges, and consume status or completion outcomes.

### Authenticated User Performing Sensitive Mutation
May trigger secret replacement, password reset, or link/unlink changes that require recent-auth or stronger proof.

### Support Operator
May review evidence and execute narrow approved remediation steps through privileged bounded flows.

### Security Reviewer
May require stronger review, deny unsafe reset or recovery, or authorize broader containment.

### Internal Recovery Workflow Service
May create and progress durable challenge or reset state through policy-bound transitions.

### Identity Domain Service
Owns the canonical relationship between recovery outcome and `account_id`.

### Auth / Session Service
Executes secret replacement effects, auth-method updates, and session invalidation after approved decisions.

### Secret Storage / Verification Component
May store secret verifiers, version metadata, challenge hashes, or equivalent approved artifacts, but MUST remain subordinate to domain-owned mutation boundaries.

### Canonical Entities
- `account_secret_state`
- `account_recovery_challenge`
- `account_secret_reset_operation`
- `account_recovery_evidence_reference`
- `account_access_remediation_action`
- derived support/read models
- event and trace references linked to mutation outcomes

## Ownership Model

### Identity Domain MUST Own
- the rule that recovery and reset MUST preserve the same canonical account
- account-level admissibility of recovery outcomes
- relationship between recovery cases and `account_id`
- conflict posture when proof could map to more than one account
- final identity-safe decision on whether access restoration may proceed

### Auth / Session Domain MUST Own
- secret verification and approved secret replacement execution
- password or password-equivalent lifecycle execution
- reset completion effects on auth paths
- session invalidation and containment execution
- auth challenge lifecycle where linked-auth flows require it

### Recovery / Conflict Domain MUST Own
- case-state meaning and review posture
- evidence sufficiency posture
- remediation routing when automation becomes unsafe
- supersession and closure of recovery or conflict cases

### Security / Risk Domain MUST Own
- compromise severity posture
- policy thresholds for stronger proof or stronger containment
- security-led invalidation and operator constraints

### Support / Admin Domains MAY
- read admin-safe case detail
- add review notes and evidence references
- execute approved remediation actions through privileged audited endpoints

### Support / Admin Domains MUST NOT
- create canonical secret truth outside owner domains
- silently merge or reassign accounts
- bypass blocked or under-review decisions
- rewrite historical secret state destructively in ways that erase lineage

### Product Domains MAY
- initiate user-facing reset and recovery flows
- display status and guidance
- route users toward approved flows

### Product Domains MUST NOT
- own canonical secret or reset truth
- perform direct secret mutation without backend owner-domain mediation
- treat product-local state as reset authority
- bypass continuity, conflict, or containment rules

## Authority / Decision Model

The decision model for key-management and user-recovery actions is conservative and layered.

### Automated secret reset or replacement is allowed only when:
- the target canonical account is unambiguous
- the evidence meets policy for the requested reset path
- no conflict, collision, or elevated risk posture blocks progression
- resulting auth-method and session effects are deterministic
- reset completion preserves continuity to the same account

### Explicit review is required when:
- proof could plausibly restore access to more than one canonical account
- a provider-backed recovery hint collides with another account’s stable provider subject
- a recovery-significant contact or reset path is disputed
- prior operator remediation or conflict history makes automatic continuation unsafe
- the account is already in under-review, blocked, or compromise-sensitive posture
- dependencies are degraded such that safe automation cannot be trusted

### Operator remediation is permitted only when:
- policy explicitly permits the action class
- stronger authorization is present
- reason code, policy reference, and trace/correlation ID are recorded
- resulting continuity effect and session-containment effect are explicit
- the action remains bounded and reconstructable

### Denial is required when:
- evidence is insufficient for the requested action
- ambiguity remains unresolved and policy does not permit progression
- the requested action would violate same-account restoration rules
- the requested correction would silently steal or overwrite another account’s access path
- security posture requires fail-closed behavior

## State Model

### Secret State
At minimum, the platform SHOULD support semantic secret states such as:

- `active`
- `reset_required`
- `reset_in_progress`
- `invalidated`
- `compromised_review`
- `superseded`

### Recovery Challenge State
At minimum, the platform SHOULD support semantic challenge states such as:

- `issued`
- `consumed`
- `expired`
- `revoked`
- `superseded`

### Secret Reset Operation State
At minimum, the platform SHOULD support semantic reset-operation states such as:

- `initiated`
- `verification_pending`
- `under_review`
- `approved_for_completion`
- `completed`
- `rejected`
- `expired`
- `cancelled`
- `superseded`

### State Rules
- state transitions MUST be explicit, durable, monotonic where appropriate, and auditable
- terminal states MUST be durable and replay-safe
- derived views MUST NOT become mutation owners
- stale read models MUST NOT redefine canonical secret or reset truth
- supersession MUST preserve linkage to prior lineage instead of hiding history

## Lifecycle / Workflow Model

### 1. Reset / Recovery Initiation
Reset or recovery begins when the user or an authorized operator initiates an approved path for a known or safely discoverable canonical account context.

Required properties:
- initiation MUST be idempotent where policy requires
- public initiation flows MUST preserve anti-enumeration posture
- initiation MUST create or reference durable case or reset state
- initiation MUST NOT silently mutate canonical identity or linked-auth ownership

### 2. Challenge Issuance and Verification
The platform issues or verifies bounded challenges appropriate to the requested path.

Required properties:
- challenges MUST be short-lived and policy-bounded
- challenge artifacts MUST be durable enough for review and audit
- challenge issuance and challenge consumption MUST be replay-safe
- consumed, expired, or revoked challenges MUST NOT silently become valid again

### 3. Evidence Evaluation
The platform evaluates whether available proof is sufficient for the requested reset or recovery-sensitive mutation.

Required properties:
- proof MUST be policy-bound and sensitivity-aware
- weak profile similarity is not sufficient proof
- evidence MAY support progression, denial, or escalation to review
- evidence references MUST preserve lineage rather than overwriting prior proof posture

### 4. Conflict Detection
During reset and recovery flows, the platform MUST evaluate whether the intended result is unambiguous.

The platform MUST detect, at minimum:
- provider subject already linked elsewhere
- multiple plausible candidate accounts
- disputed recovery-significant contact or access path
- prior remediation history that makes automatic continuation unsafe
- signals of possible compromise, abuse, or replay
- degraded dependencies that reduce confidence in safe automation

### 5. Completion
Reset completion replaces or restores the secret or access path while preserving the same canonical account.

Completion MUST:
- target the same account
- explicitly record resulting secret version or epoch
- explicitly record reason code and containment consequences
- preserve downstream account anchoring across products, workspaces, wallets, and audit lineage
- require fresh session issuance rather than trusting stale session state where policy requires

### 6. Post-Completion Containment
After completion, the platform MUST evaluate whether stale sessions, stale challenges, stale auth paths, or related remediation cases require targeted invalidation, global invalidation, supersession, or closure.

### 7. Rejection / Expiration
If proof is insufficient, policy disallows the route, the challenge expires, the operation expires, or risk posture blocks safe progression, the platform MUST terminate the path explicitly without hidden mutation.

### 8. Corrective Remediation
When a prior mistake or contested secret/access-path ownership requires correction, the platform MUST use explicit remediation rather than ordinary reset semantics. Corrective remediation MUST preserve lineage, reason codes, resulting continuity effect, and resulting session-containment effect.

## Invariants

1. A reset or recovery outcome may restore access only to the same canonical `account_id`.
2. Secret replacement MUST NOT create a new account as a shortcut.
3. Challenges and proofs are bounded evidence, not canonical identity truth.
4. Conflict or ambiguity MUST produce explicit case state rather than silent guesswork.
5. Provider callback success, email similarity, or profile similarity alone MUST NOT justify reset completion against ambiguous identity state.
6. Sessions are not canonical identity truth and do not by themselves authorize identity rewrite.
7. Key-management and recovery mutations MUST be auditable, reason-coded where applicable, and reconstructable.
8. Product surfaces MAY consume outcomes but MAY NOT own secret or reset truth.
9. High-impact secret events MUST drive deterministic session-containment or forced re-auth posture where policy requires.
10. Controlled reassignment, if ever approved, is exceptional and never implicit.
11. Degraded runtime conditions MUST NOT cause hidden semantic downgrade or truth substitution.
12. Retries and repeated callbacks MUST NOT create duplicate or contradictory durable outcomes.

## Functional Rules

### Secret Handling Rules
- secret verifiers MUST be stored and processed according to approved security policy
- plaintext secret recovery or reversible secret storage for user-facing authentication secrets is forbidden
- secret mutation MUST occur only through owner-domain boundaries
- direct product-side secret writes are forbidden

### Recovery Challenge Rules
- challenges MUST be single-purpose or purpose-bounded
- challenge validity MUST be finite
- consumed challenges MUST become unusable for future completion
- challenge status MUST be durable enough to reject replay and support audit reconstruction
- expired or revoked challenges MUST NOT be silently reactivated

### Reset Completion Rules
- completion MUST target the same account
- completion MUST NOT silently detach products, workspaces, wallets, billing continuity, or audit lineage from the account
- completion MUST explicitly record resulting secret version, reason code, and containment consequences
- completion after elevated risk MAY require stronger containment, forced re-authentication, or fresh trusted session establishment

### Conflict Rules
The platform MUST treat at least the following as explicit conflict or review conditions:
- proof could plausibly restore access to more than one canonical account
- a provider-backed recovery hint collides with another account’s stable provider subject
- a recovery-significant contact or secret reset path is disputed
- prior operator remediation, merge, or recovery history makes automatic continuation unsafe
- the account is already in under-review, blocked, or compromise-sensitive posture

### Resolution Rules
If conflict or ambiguity exists, the platform MUST NOT:
- silently merge accounts
- silently overwrite provider ownership
- silently reassign recovery-significant contact or access truth
- silently continue reset completion against ambiguous state

Instead, it MUST:
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
- email MAY be used as hint, contact, or review signal, but not as sole canonical match key in ambiguous cases
- challenge-completion artifacts are stronger than unverifiable profile overlap, but remain subordinate to conflict and risk policy
- evidence storage MUST preserve lineage and reviewability
- insufficient proof MUST lead to explicit denial or review, not silent permissiveness

## Permission / Access Considerations

Key-management and user-recovery actions are high-sensitivity actions.

Required control expectations:
- privileged support/admin actions MUST require stronger authorization than ordinary user self-service actions
- privileged actions MUST run through privileged session classes or equivalent stronger control paths
- admin mutation requests MUST include reason code, policy reference, and actor confirmation
- internal service transitions MUST include service identity and scoped authority
- recent-auth or step-up proof SHOULD be required for authenticated sensitive secret mutations where policy requires
- support tools MUST NOT bypass owning-domain mutation boundaries

Successful recovery restores ability to authenticate the account. It does not bypass later workspace, role, permission, or entitlement evaluation.

## Entitlement Considerations

Key management and user recovery do not redefine entitlement truth.

Required rules:
- account-scoped or workspace-scoped entitlements remain attached to the canonical account and/or workspace according to their owning domains
- secret reset or recovery completion MUST NOT mint new entitlement state as a side effect unless explicitly owned and triggered by another domain
- remediation MUST NOT lose track of billing, credits, payout eligibility, or product continuity because those relationships remain attached to the same account

## API / Contract Implications

The platform SHOULD expose secret-reset and user-recovery behavior through explicit boundaries.

### Identity APIs SHOULD support:
- recovery initiation and case-reference creation using safe response semantics
- account recovery posture reads that affect admissibility of reset or restoration
- conflict/remediation references and statuses
- admin-safe review and approved completion operations

### Session / Linked-Login APIs SHOULD support:
- password or secret reset initiation and completion
- linked-method mutation blocks with explicit continuity, review, or conflict reasons
- targeted and global session invalidation after approved reset or recovery events
- continuity-aware reset results and containment outputs

### API Rules
- all mutation-capable endpoints MUST require correlation identifiers
- idempotency is mandatory for recovery initiation, challenge consumption, reset completion requests, admin remediation actions, and session-containment actions where retries are plausible
- errors MUST distinguish validation failure, conflict, under review, policy denied, expired challenge, replay rejected, and dependency degraded without exposing unsafe internal detail
- async or review-required flows SHOULD return operation or case references rather than implying synchronous finality
- public responses MUST preserve anti-enumeration posture

## Event / Async Implications

Key-management and user-recovery behavior MUST emit durable domain events sufficient for audit, monitoring, and downstream coordination.

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
- challenge issuance, delivery, review routing, and completion MAY be orchestrated asynchronously, but canonical state MUST remain explicit and durable
- retries MUST be replay-safe and idempotent
- terminal events MUST NOT be emitted multiple times with contradictory meaning
- review workflows MUST preserve clear ownership between identity decisioning, recovery-case progression, and auth/session execution

## Data Model / Storage Implications

At minimum, the platform SHOULD support the following durable semantic structures.

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

### `account_recovery_challenge`
Representative semantic fields:
- `challenge_id`
- `account_id` or safely anchored case reference
- challenge purpose
- challenge status
- issued_at
- expires_at
- consumed_at
- revoked_at
- correlation_id
- delivery/reference metadata where policy allows

### `account_secret_reset_operation`
Representative semantic fields:
- `reset_operation_id`
- `account_id`
- reset type
- operation status
- initiated_by_actor_type
- policy_reference
- correlation_id
- resulting_secret_version
- resulting_session_containment_effect
- created_at
- closed_at

### `account_recovery_evidence_reference`
Representative semantic fields:
- `evidence_reference_id`
- related case or operation reference
- evidence type
- evidence status
- artifact reference or hash
- collected_at
- collected_by_actor_type

### Data Rules
- secret and reset state MUST be durable source-of-truth entities or durable state machines, not transient frontend-only artifacts
- derived views MAY summarize posture, but MUST NOT own mutation
- destructive rewrite of prior truth SHOULD be avoided in favor of explicit lineage and terminal transitions
- evidence references SHOULD preserve traceability without overloading canonical account records
- stale or partial projections MUST NOT be used as justification for corrective identity mutation

## Read Model / Projection / Reporting Rules

The platform MAY maintain:
- reset and recovery queue views
- current challenge summaries
- user-facing safe status summaries
- analytics and operational dashboards
- audit-friendly exports
- search and lookup projections for authorized internal users

These models MUST remain derived. They MUST NOT:
- redefine canonical secret or reset ownership
- become the only evidence of a reset or challenge decision
- drive destructive canonical mutations directly
- out-rank owner-domain records when disagreements occur
- hide staleness when canonical containment or reset-state changes have already occurred

If a derived model becomes stale, canonical case, secret, and identity records win.

## Security / Risk / Abuse Controls

Key management and user recovery are identity-and-access security controls.

The platform MUST preserve:
- anti-enumeration behavior where public exposure would weaken security
- anti-replay handling for challenge consumption and reset completion
- stronger operator controls for privileged remediation
- recent-auth or step-up requirements where policy requires
- explicit containment after compromise-sensitive reset or recovery
- bounded exposure of internal decision reasons on public surfaces
- strong audit lineage for support or admin interventions
- denial or review posture when risk signals make ordinary continuation unsafe

Risk-aware rules:
- suspected compromise MAY justify broader containment than ordinary reset
- elevated risk MAY require review even when evidence would otherwise seem sufficient
- support convenience MUST NOT outrank takeover resistance

## Boundary Violation Detection / Non-Canonical Patterns

The following patterns are explicitly non-canonical and forbidden:

- product-local reset or recovery truth outside the identity/auth domains
- silent automated account merge during reset or recovery
- silent creation of a new account instead of restoring the old one
- using email overlap alone as sufficient recovery proof in ambiguous cases
- using wallet linkage alone as default recovery proof
- treating stale session presence as durable authority for secret rewrite
- support-tool direct mutation of canonical secret or provider ownership without bounded workflow, reason code, and audit lineage
- destructive remediation that erases historical case or secret-version lineage
- public error surfaces that leak sensitive internal account existence or remediation detail beyond policy

Implementations SHOULD detect and surface these violations through monitoring, tests, and audit review.

## Audit / Traceability Requirements

FUZE MUST be able to determine:
- when and why a reset or recovery operation was initiated
- which canonical account and secret class were involved
- which challenge or evidence types were collected and what decision they supported
- why an operation was completed, rejected, expired, superseded, or routed to review
- which auth methods and sessions were affected by completion or remediation
- which operator or service executed a privileged action
- how secret version changed before and after the action
- which policy version constrained the action
- whether a visible reset summary is canonical or derived

Auditability is required because key-management and reset mistakes are high-impact, difficult to reconstruct later, and central to platform trust.

## Failure Handling / Edge Cases

### Lost Password, Other Method Still Exists
The platform MAY restore access through another approved path without redefining identity. Secret reset MAY still be unnecessary if continuity posture remains acceptable.

### Last Viable Access Path Lost
The platform MUST route toward approved recovery or remediation posture. It MUST NOT silently bootstrap a new account to avoid friction.

### Provider Subject Already Linked Elsewhere
The platform MUST raise explicit conflict or remediation state and MUST NOT attach reset authority to a second account.

### Same Email Returned by Different Provider
Email overlap alone MUST NOT force reset completion. The platform MUST use conservative resolution and MAY require review.

### Challenge Replay Attempt
Consumed, expired, or revoked challenges MUST be rejected without duplicate side effects.

### Recovery Completes After Suspected Compromise
The platform MAY globally invalidate prior sessions, close affected challenges, and require fresh trusted session establishment.

### Support Error During Remediation
The platform MUST preserve correction lineage and superseding actions rather than silently rewriting history.

### Stale Session Exists During Reset
A stale or remembered session MUST NOT be treated as sufficient proof for identity rewrite. Policy MAY use it as one signal, not as sole authority.

### Wallet Link Remains but Normal Access Is Lost
Wallet-aware context remains historically attached to the account, but wallet linkage alone does not become default recovery truth unless separately approved by policy.

## Operational Considerations

Operational systems SHOULD support:
- bounded review queues for reset and recovery operations
- correlation across public, internal, and admin actions
- rate limits and abuse controls on public reset initiation
- visibility into challenge issuance, consumption, expiration, and replay rejection
- deterministic operator tooling with narrow action classes
- monitoring for repeated replay, repeated collision, or suspicious reset patterns
- support playbooks that reference policy without bypassing domain ownership
- safe degraded-mode behavior that pauses unsafe automation rather than guessing

The operational model MUST preserve the principle that reset and secret recovery are structured platform workflows, not ad hoc customer-support edits.

## Migration / Compatibility / Supersession Considerations

- migrations MUST NOT silently weaken the requirement that reset and recovery preserve the same canonical account
- compatibility layers MAY preserve older product entry flows temporarily, but MUST NOT preserve hidden product-local recovery ownership
- older documents or implementations that imply email-only matching, implicit merge, implicit provider reassignment, or support-side silent rewrites are superseded within this scope
- new providers and new reset methods MUST plug into the same recovery/conflict decision model rather than creating one-off exceptions
- state names MAY evolve, but the semantic distinctions defined here MUST be preserved

Migration plans MUST include replay-safe mapping, lineage preservation, explicit rollback posture, and correction handling for ambiguous historical states.

## Implementation-Contract Guardrails

Downstream implementations MUST preserve the following guardrails:

1. reset and recovery preserve the same canonical account rather than creating substitutes
2. conflict and ambiguity become explicit case state rather than hidden guesses
3. identity-domain ownership of recovery admissibility and meaning is preserved
4. auth/session execution remains subordinate to approved recovery and remediation decisions
5. session containment after trust-reset events remains deterministic and auditable
6. evidence, provider input, session presence, wallet context, and secret state remain distinct truth classes
7. products and frontends do not become owners of secret or reset truth
8. operator or support overrides remain bounded, reason-coded, policy-constrained, and audited
9. derived views remain derived and regenerable
10. degraded dependencies do not cause hidden semantic downgrade or truth substitution
11. retries and repeated callbacks do not create duplicate or contradictory durable outcomes
12. downstream docs and teams MUST NOT optimize away challenge durability, version lineage, explicit review posture, or containment semantics where those elements protect continuity, security, or auditability

These guardrails are mandatory and MUST NOT be optimized away for convenience.

## Downstream Execution Staging

Recommended downstream staging order:

1. stabilize canonical identity and account ownership semantics
2. stabilize linked-auth and provider-resolution rules that create recovery or conflict posture
3. stabilize secret-state, challenge-state, and reset-operation data models and APIs
4. stabilize session-containment and auth-method mutation execution
5. stabilize support/admin remediation controls and security escalation policy
6. integrate workspace, authorization, entitlement, and wallet-aware downstream continuity consumers
7. build support, analytics, and reporting read models over canonical case and secret records

This ordering preserves identity stability before narrower runtime or UX layers specialize behavior.

## Required Downstream Specs / Contract Layers

This specification requires compatible downstream refinement and implementation-contract work in at least the following areas:

- `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- support and admin control-plane workflows
- product integration specifications that surface recovery and access-mutation flows

## Canonical Examples / Anti-Examples

### Canonical Example 1 — Same Account, New Password
A user loses their password-backed access path, completes approved recovery proof, rotates the password, and re-enters the same account. Prior sessions are globally invalidated according to policy. This is canonical.

### Canonical Example 2 — Challenge Replay Rejected
A recovery challenge is consumed once, then replayed later. The second attempt is rejected without duplicate reset completion or duplicate containment side effects. This is canonical.

### Canonical Example 3 — Provider Collision Routed to Review
A provider-backed recovery hint collides with another account’s stable provider subject. The platform opens a conflict case and routes to review rather than auto-attaching reset authority. This is canonical.

### Canonical Example 4 — Support-Led Corrective Action
A prior mistake is confirmed through review. A bounded remediation action is executed with reason code, policy reference, session-containment effect, and audit lineage preserved. This is canonical.

### Anti-Example 1 — Silent Email-Based Reset Completion
A reset flow sees email overlap with another account and auto-completes secret replacement. This is forbidden.

### Anti-Example 2 — New Account as Recovery Shortcut
A product silently creates a new account when it cannot safely recover the existing one. This is forbidden.

### Anti-Example 3 — Session as Durable Reset Proof
A remembered session on one device is treated as sufficient proof to rewrite secret ownership. This is forbidden.

### Anti-Example 4 — Support Dashboard as Canonical Truth
A support tool view shows a likely correction and directly mutates secret or identity ownership without the bounded remediation workflow. This is forbidden.

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
- support and admin control-plane workflows
- product integration specifications that surface recovery and access mutation flows

## Explicitly Deferred Items

The following are intentionally deferred to adjacent or downstream specifications:

- exact KDF, hash, or enclave implementation detail
- exact MFA factor matrix and factor recovery UX
- exact legal-identity or KYC evidence policies
- exact HSM/KMS deployment topology
- exact support queue tooling and reviewer assignment logic
- exact wallet-auth-as-recovery policy if ever approved
- exact service split, queue topology, or schema partitioning

These deferrals do not weaken the canonical key-management and user-recovery model established here.

## Final Normative Summary

FUZE key management and user recovery is the platform capability that controls security-sensitive secret material and reset-capable proof handling in support of restoring access to the same canonical account. Secret reset and recovery do not create identity. They operate underneath the canonical account model, underneath recovery-case admissibility, and alongside session-containment and audit-lineage requirements.

Key-management and reset actions are high-sensitivity workflows. They MUST be policy-controlled, continuity-safe, backend-owned, auditable, replay-safe, and coordinated with linked-auth state, provider-link state, session containment, and account-state precedence. Products may surface and consume these flows, but they may not redefine them. This document is the canonical FUZE rule set for key-management, reset-capable proof handling, and user-recovery secret semantics.

## Quality Gate Checklist

- [x] canonical owner is explicit for every material key-management and recovery-related truth family
- [x] mutation boundaries are explicit
- [x] adjacent boundaries are explicit
- [x] truth classes are explicit
- [x] conflict-resolution rules are explicit where needed
- [x] default decision rules are explicit where ambiguity could arise
- [x] non-canonical patterns are called out clearly
- [x] operator/admin override paths are bounded and audited
- [x] read-model, cache, reporting, and projection rules are explicit
- [x] wallet and adjacent domain responsibilities are explicit where relevant
- [x] failure and degraded-mode behaviors are explicit
- [x] downstream implementation guardrails are explicit
- [x] dependencies and downstream impacts are explicit
- [x] non-goals and deferred items are explicit
- [x] the document is strong enough to support backend, API, data, runtime, security, support, and audit implementation without inventing contradictory semantics
