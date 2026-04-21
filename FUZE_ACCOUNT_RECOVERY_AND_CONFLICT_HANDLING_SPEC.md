# FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC

## Title
FUZE Account Recovery and Conflict Handling Specification

## Document Metadata

- Document Name: `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Version: 1.1.0
- Effective Date: 2026-04-21
- Last Updated: 2026-04-21
- Reviewed On: 2026-04-21
- Document Owner: FUZE Platform Identity and Account Domain with recovery-execution coordination delegated to the Auth / Session and Recovery Operations domains under policy control
- Approval Authority: FUZE Platform Architecture and Governance Authority
- Review Cadence: Quarterly or upon material change to identity recovery policy, provider-linking behavior, continuity policy, session-containment policy, support/admin remediation controls, or security-risk posture
- Governing Layer: Platform core / account recovery and conflict handling
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, security engineering, support operations, audit, governance, API design, platform operations, reliability engineering, implementation-contract authors
- Primary Purpose: Define the canonical FUZE model for restoring access to the same canonical account and for handling identity, provider, linked-access, and ambiguity conflicts without silent merge, silent fragmentation, silent reassignment, or unsafe takeover
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
  - `KEY_MANAGEMENT_AND_USER_RECOVERY_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
  - `AUTH_IDENTITY_API_SPEC.md`
  - `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
  - `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
- Primary Downstream Dependents:
  - `KEY_MANAGEMENT_AND_USER_RECOVERY_SPEC.md`
  - `AUTH_IDENTITY_API_SPEC.md`
  - `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - `WORKSPACE_AND_ORGANIZATION_SPEC.md`
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
  - `AUDIT_LOG_AND_ACTIVITY_SPEC.md`
  - support and admin control-plane workflow specifications
  - product integration specifications that expose recovery, appeal, correction, or ambiguity-handling entry points
- Supersedes: Earlier or less explicit FUZE recovery, support-led correction, ambiguity-handling, and duplicate-account writeups to the extent they conflict within this document’s scope
- Superseded By: Not yet known
- Related Decision Records: Not yet known
- Canonical Status Note: This document is the governing FUZE account recovery and conflict-handling specification. Downstream APIs, workflows, products, support tooling, security tooling, and operational playbooks MUST NOT reinterpret the semantics established here.
- Implementation Status: Normative architecture baseline; downstream API, workflow, storage, event, runtime, support, and audit contracts must conform
- Approval Status: Drafted for refined-system inclusion; formal approval record not yet attached
- Change Summary:
  - strengthened same-account restoration rules and explicit prohibition on recovery as substitute identity creation
  - tightened truth-class separation among identity, auth-link, recovery-case, session, provider-input, wallet-aware, authorization, and reporting domains
  - clarified conflict classes, case-state semantics, default decision rules, controlled reassignment posture, and degraded-mode behavior
  - strengthened session-containment, operator-remediation, idempotency, replay-safety, and audit-lineage requirements
  - aligned recovery and conflict handling with current refined identity, auth/session, provider-resolution, session-security, and key-management specifications

## Purpose

This specification defines the canonical FUZE rules for account recovery and conflict handling.

Its purpose is to make explicit:

- how FUZE restores access to the same canonical account when ordinary access paths fail, degrade, or become unsafe
- how the platform handles ambiguity, duplicate-account risk, provider collisions, contested access-path ownership, and support-led correction without silently merging, silently splitting, or silently reassigning identity
- how recovery, conflict review, remediation, continuity preservation, and session containment must interact
- how recovery-sensitive and conflict-sensitive actions are owned, authorized, audited, reason-coded, and policy-constrained
- how downstream APIs, services, products, support tools, security tooling, and admin surfaces must consume recovery and conflict state without becoming owners of canonical identity truth

Recovery and conflict handling are not convenience features. In FUZE they are trust-preservation mechanisms at the identity boundary. They exist to preserve the actor’s path back to the same canonical `account_id` while preventing takeover, hidden account drift, unsafe merge behavior, or operator-side silent rewrites.

## Scope

This specification governs:

- restoration of access to an existing canonical account
- recovery-case creation, progression, review, completion, rejection, expiration, supersession, and closure
- conflict-case creation and controlled handling for provider collisions, duplicate-account risk, ambiguity, contested ownership, and remediation-sensitive inconsistencies
- the relationship between recovery posture, linked-auth state, provider-link state, session containment, continuity posture, account restriction state, and security posture
- support-reviewed, admin-reviewed, or security-reviewed remediation actions affecting account access
- risk-aware containment and session invalidation following recovery or corrective identity-access actions
- minimum canonical entity, state, event, and lineage requirements for recovery and conflict handling
- API, event, runtime, monitoring, and operational boundary requirements for user, internal, support, and admin flows
- read-model, projection, and reporting rules for recovery/conflict state

This specification does not define in full depth:

- canonical account identity semantics in general
- detailed provider-resolution heuristics for all auth flows
- detailed session token transport and low-level browser mechanics
- the full key-management or secret-recovery architecture
- exact MFA factor matrix or challenge catalog
- detailed workspace role or workspace membership logic
- detailed wallet verification protocol
- exact cloud, queue, or storage implementation for evidence handling
- exact case-management UI design or staffing procedure
- exact legal identity, KYC, or external compliance verification model

These areas belong to adjacent or downstream specifications and must remain compatible with this document.

## Out of Scope

This document is explicitly out of scope for:

- product-local fallback identity systems
- hidden automated account merge logic
- ordinary login flows when no recovery or conflict posture exists
- provider SDK implementation detail
- browser token transport detail
- non-FUZE external account systems acting as canonical identity truth
- treasury, credits, payouts, governance, or other unrelated correction workflows except where account continuity impact must be preserved
- replacing identity-domain ownership with support-tool ownership
- using wallet possession alone as default recovery truth unless separately approved by higher-order policy

## Design Goals

1. Restore access to the same canonical account rather than creating replacement identity paths.
2. Preserve identity integrity over convenience when evidence is incomplete, weak, or ambiguous.
3. Prevent silent fragmentation, silent merge, silent reassignment, and support-driven hidden rewrites.
4. Preserve continuity across products, workspaces, wallet-aware context, billing relationships, audit lineage, and future platform surfaces.
5. Ensure recovery and remediation actions are explicit, policy-controlled, auditable, reviewable, and replay-safe.
6. Keep recovery and conflict handling backend-owned and domain-owned rather than frontend-owned or product-owned.
7. Preserve clear separation among identity, authentication, session, authorization, workspace scope, entitlement, and wallet-aware context.
8. Ensure recovery and conflict resolution remain safe under retries, partial failures, asynchronous review workflows, and degraded dependencies.
9. Define deterministic containment effects for stale or compromised sessions after trust-reset events.
10. Make downstream implementation and API contract design stricter, not more ambiguous.

## Non-Goals

This specification is not intended to:

- guarantee fully automated recovery in every case
- optimize solely for minimum user friction at the expense of takeover resistance
- treat email similarity, profile similarity, provider callback success, or stale session presence as sufficient proof for merge or reassignment
- let support or admin operators bypass policy, audit, continuity, or security rules
- redefine workspace authorization, entitlement, or wallet-aware behavior as recovery truth
- let currently active sessions stand in for durable future access continuity except where policy explicitly permits a narrow temporary role
- replace downstream API, schema, runbook, or queue-topology specifications

## Core Principles

### 1. Same-Account Restoration Principle
Recovery restores access to the same canonical `account_id`. Recovery MUST NOT create a new canonical account as a shortcut.

### 2. No-Silent-Merge Principle
The platform MUST NOT silently merge accounts because recovery evidence, provider signals, or support intuition appears similar to another identity record.

### 3. No-Silent-Reassignment Principle
A linked access path, provider subject, or recovery-significant account relationship MUST NOT be silently moved from one account to another.

### 4. Continuity Before Convenience Principle
If convenience and continuity integrity conflict, FUZE MUST preserve continuity integrity.

### 5. Explicit Review Principle
If automated certainty is insufficient, the platform MUST enter explicit review or remediation posture rather than guessing.

### 6. Session Containment Principle
Recovery completion, compromise remediation, password reset, provider correction, and certain corrective access actions MAY require targeted or global session invalidation.

### 7. Audit Lineage Principle
Recovery and conflict handling MUST be reconstructable through durable case state, reason-coded decisions, evidence references, trace identifiers, and emitted events.

### 8. Backend Ownership Principle
Products and frontends MAY initiate and consume recovery flows, but canonical recovery truth and conflict-resolution truth remain backend-owned and domain-owned.

### 9. Authorization Separation Principle
Recovery restores account access. It does not automatically restore or grant workspace role, permission, entitlement, or product capability if downstream controls have changed.

### 10. Conservative Remediation Principle
Operator or support intervention MUST remain narrow, bounded, policy-referenced, and auditable. Remediation is an exception path, not a second identity model.

## Canonical Definitions

### Recovery
A controlled process that restores ability to reach an existing canonical FUZE account after ordinary access paths are unavailable, degraded, blocked, or insufficient.

### Recovery Case
A durable platform case that tracks recovery initiation, verification, review, transitions, and outcome for a specific canonical account or safely anchored candidate account context.

### Conflict Case
A durable platform case used when identity or access-path evidence cannot safely resolve to one unambiguous account-access outcome.

### Recovery-Significant Attribute
An attribute or relationship whose change can materially affect ability to regain access to the same account, such as a primary recovery email, a linked auth method, or an approved recovery route.

### Recovery Posture
The platform-evaluated state describing whether recovery is unnecessary, available, in progress, under review, blocked, completed recently, rejected, or expired.

### Conflict Posture
A state in which the platform cannot safely continue ordinary automated resolution due to ambiguity, collision, duplicate-account risk, provider collision, contested ownership, or remediation-sensitive inconsistency.

### Remediation
A controlled corrective action that resolves or contains a recovery or conflict case without silently rewriting canonical identity truth.

### Recovery Evidence
The set of verification artifacts, completed challenges, support references, or policy-approved proof inputs used to decide a recovery case.

### Recovery Completion
A terminal or near-terminal action that restores account reachability and may trigger auth-method changes, continuity-posture changes, and session containment.

### Controlled Reassignment
An explicitly approved and auditable remediation action that changes ownership of an access-path binding only through policy-authorized review. This is exceptional and never implicit.

### Stranded Account
A canonical account with no presently viable ordinary access path and no currently usable approved recovery or remediation route.

### Under Review
A posture indicating that automated flow has paused and explicit reviewer or policy-controlled operator decision is required.

## Truth Class Taxonomy

This specification distinguishes the following truth classes and downstream implementations MUST preserve those distinctions.

### 1. Canonical Identity Truth
The canonical account record, account lifecycle, and identity-domain continuity semantics anchored by `account_id`.

### 2. Auth-Link Truth
The durable approved mapping between a canonical account and its linked authentication methods or provider-backed access paths.

### 3. Recovery / Conflict Case Truth
Durable recovery-case and conflict-case state, including case transitions, review posture, remediation references, and completion or rejection outcomes.

### 4. Runtime Session Truth
Temporary authenticated runtime presence, session lineage, revocation state, and security invalidation state.

### 5. Policy Truth
Recovery policy, security policy, continuity policy, operator-control policy, approval thresholds, evidence sufficiency rules, and higher-order platform rules.

### 6. Provider-Input Truth
Validated external inputs supplied by providers or approved adapters, such as issuer-subject pairs, callback claims, or provider metadata. These are evidence inputs, not identity truth.

### 7. Wallet-Aware Context Truth
Wallet-link state and wallet-derived participation context. This remains adjacent attached context, not default recovery truth.

### 8. Authorization / Entitlement Truth
Workspace scope, membership, roles, permissions, product capabilities, and entitlements evaluated downstream after valid account access is restored.

### 9. Derived Read-Model Truth
Support views, dashboards, review queues, summaries, search projections, and convenience views derived from canonical recovery or conflict records.

### 10. Reporting / Public View Truth
Exports, user-facing status messages, and reports that summarize recovery/conflict posture without becoming mutation owners.

## Architectural Position in the Spec Hierarchy

This document sits below:

- `REFINED_SYSTEM_SPEC_INDEX.md`
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

and above:

- `KEY_MANAGEMENT_AND_USER_RECOVERY_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- support/admin workflow specifications
- product integration specifications that surface account-recovery, appeal, correction, or ambiguity-handling entry points

This document does not redefine canonical account identity, provider-resolution ownership, session lifecycle in full, workspace authorization, or wallet verification in full. It defines how those domains coordinate when access restoration, ambiguity handling, or corrective identity-access action occurs.

## System Boundaries

This document governs only the following platform-owned boundaries:

- recovery initiation into canonical account-restoration workflows
- controlled review and remediation when ordinary auth resolution becomes unsafe
- recovery-case and conflict-case state as durable platform truth
- containment effects on auth methods and sessions after trust-reset events
- continuity-safe handling of ambiguous account-access situations
- evidence sufficiency posture for recovery and conflict decisioning
- controlled operator and support actions that affect account access restoration or corrective resolution
- the boundary between canonical case truth and derived support/reporting views

It does not govern:

- routine login when no recovery or conflict posture exists
- standalone authorization decisions after valid authenticated runtime state exists
- product-local onboarding preferences except insofar as they must not break recovery and continuity rules
- wallet-only remediation except where wallet-link continuity must be preserved while account access is restored
- unrelated commercial or governance correction workflows outside the account-access boundary

## Adjacent Boundaries

### Identity and Account Domain
Owns canonical `account_id`, account lifecycle state, identity conflict semantics, and identity-level recovery meaning. Recovery must preserve that ownership.

### Auth / Session / Linked Login Domain
Owns linked auth-method lifecycle, auth challenge state, session issuance, session invalidation, and linked-login mutation execution. This specification tells that domain when recovery or conflict posture constrains ordinary behavior.

### Provider Resolution and Linking Domain
Owns provider normalization, provider-subject uniqueness, provider-link collision handling, and provider correction semantics. This specification consumes those outputs when restoration or remediation is required.

### Session Lifecycle and Security Domain
Owns detailed session issuance, lineage, revocation, privileged-session posture, and security invalidation execution. This specification governs when recovery or remediation must trigger containment.

### Key Management and User Recovery Domain
Owns secret and recovery-proof mechanics, secret-reset lineage, and reset-capable proof handling. This specification owns the identity and case meaning of the resulting restoration decision.

### Workspace / Authorization / Entitlement Domains
Own downstream workspace membership, role, permission, and capability evaluation after valid account access is restored. Recovery does not replace those checks.

### Wallet-Aware Domain
Owns wallet-link truth and wallet verification semantics while remaining subordinate to canonical account continuity. Wallet linkage alone does not become default recovery truth unless separately approved by higher-order policy.

### Security / Risk Domain
Owns broader risk policy, severity posture, compromise-response policy, containment triggers, and high-sensitivity operator controls.

### Support / Admin Tooling
Consumes approved recovery and remediation pathways but does not own canonical identity truth, case truth, or auth-link truth.

## Conflict Resolution Rules

When multiple layers disagree, the platform MUST resolve recovery-related and conflict-related disagreements in the following order unless a higher-order platform policy explicitly overrides it:

1. canonical identity-domain records and restriction state
2. canonical recovery-case or conflict-case truth
3. canonical linked-auth and provider-link records
4. explicit policy and security constraints
5. validated provider-input and proof evidence interpreted through approved rules
6. runtime session or client state
7. derived views, dashboards, queues, reports, or product-local caches

Specific conflict rules:

- email similarity MUST NOT justify silent merge or reassignment
- provider callback success MUST NOT justify silent merge, reassignment, or ordinary session issuance when recovery conflict exists
- stale active session state MUST NOT override recovery or remediation posture
- wallet presence MUST NOT justify account ownership rewrite
- support tooling views MUST NOT be treated as authoritative if they diverge from canonical case or identity records
- product-local user records MUST NOT become recovery anchors when they diverge from canonical account truth
- reporting exports MUST NOT drive direct corrective mutation of identity or access-path ownership

## Default Decision Rules

Where ambiguity exists, the following defaults apply:

1. default actor anchor: `account_id`
2. default recovery goal: restore access to the same canonical account, not a replacement account
3. default owner of recovery and conflict-case meaning: Identity and Account Domain
4. default owner of auth-method mutation and session-containment execution: Auth / Session Domain
5. default interpretation of provider subject: durable evidence stronger than mutable profile data, but still subject to remediation policy
6. default interpretation of email or contact overlap: hint or review signal, not sole canonical matching proof
7. default interpretation of active session presence: temporary runtime signal, not durable recovery proof
8. default interpretation of wallet linkage: attached context, not universal recovery proof
9. default resolution for ambiguous recovery or collision: explicit review or denial, not silent automation
10. default resolution for last viable access path loss: enter recovery or remediation posture, not silent bootstrap of a new account
11. default resolution for disputed reassignment request: preserve existing canonical ownership and open remediation rather than auto-transfer
12. default posture under degraded evidence or high-risk uncertainty: fail closed for high-impact access actions

## Roles / Actors / Entities

### End User
May initiate recovery, respond to approved recovery challenges, provide policy-approved evidence, review safe status, and consume completion or denial outcomes.

### Authenticated User Performing Sensitive Mutation
May trigger access-path changes, password reset, or other recovery-adjacent actions that require recent-auth or stronger proof.

### Support Operator
May review cases and execute narrow approved remediation actions through privileged bounded flows. Support does not become identity owner.

### Security Reviewer
May require stronger review, deny unsafe restoration, require broader containment, or authorize compromise-sensitive remediation.

### Internal Workflow / Review Service
May create and progress durable case state through policy-bound transitions and asynchronous orchestration.

### Identity Domain Service
Owns canonical recovery-case and conflict-case meaning, recovery admissibility, and final same-account restoration decision.

### Auth / Session Service
Executes auth-method changes and session containment under approved identity or security decisions.

### Product Surface
May expose recovery entry points and status views but MUST NOT decide canonical outcomes or own case state.

### Canonical Entities
- `account_recovery_case`
- `identity_conflict_case`
- `account_recovery_evidence_reference`
- `account_access_remediation_action`
- derived review/read models
- event and trace references linked to case and mutation outcomes

## Ownership Model

### Identity Domain MUST Own
- canonical recovery-case and conflict-case semantics
- determination that the intended restoration targets the same canonical account
- case-state transitions for completion, rejection, review, supersession, and closure
- duplicate-account and ambiguity posture at the identity layer
- recovery and remediation decision meaning
- continuity-preservation meaning when restoration or correction occurs

### Auth / Session Domain MUST Own
- linked auth-method mutation execution after approved decisions
- session invalidation and containment execution
- auth-challenge or recent-auth execution behavior where required
- post-recovery re-entry session issuance according to session-policy rules

### Provider Resolution Domain MUST Own
- provider-subject uniqueness rules
- provider collision semantics
- provider-link correction constraints used by recovery and remediation flows

### Security / Risk Domain MUST Own
- elevated risk signals that block or constrain recovery
- compromise-response policy that requires additional containment or review
- severity-based operator controls and escalation thresholds

### Support / Admin Domains MAY
- read admin-safe case detail
- add review notes and evidence references
- execute approved remediation actions through privileged audited endpoints

### Support / Admin Domains MUST NOT
- create canonical recovery truth outside the identity domain
- silently merge or reassign accounts
- bypass blocked or under-review decisions
- rewrite history through destructive edits that erase lineage

### Product Domains MAY
- initiate user-facing recovery flows
- display status and guidance
- route users toward approved flows

### Product Domains MUST NOT
- create recovery truth outside the identity domain
- merge or reassign accounts
- override blocked or under-review decisions
- treat product-local user records as recovery anchors

## Authority / Decision Model

The decision model for recovery and conflict handling is conservative and layered.

### Automated decisions are allowed only when:
- the target canonical account is unambiguous
- the evidence meets policy for the requested recovery path
- continuity can be preserved without creating ambiguity
- no conflict, collision, or elevated risk posture blocks progression
- resulting auth-method and session effects are deterministic

### Explicit review is required when:
- a provider subject collides with another account
- provider-email overlap is suggestive but not sufficient
- multiple candidate accounts appear plausible
- a support case alleges prior misbinding or duplicate-account creation
- a recovery-significant attribute conflicts with another account’s state
- restoration would otherwise strand, overwrite, or ambiguously reassign access truth
- elevated risk or compromise signals exist
- required dependencies are degraded such that safe automation cannot be trusted

### Operator remediation is permitted only when:
- policy explicitly permits the action class
- stronger authorization is present
- reason code, policy reference, and trace/correlation ID are recorded
- resulting continuity, session containment, and audit effects are explicit
- the action remains bounded and reconstructable

### Denial is required when:
- evidence is insufficient for the requested action
- ambiguity remains unresolved and policy does not permit progression
- the requested action would violate same-account restoration rules
- the requested correction would silently steal or overwrite another account’s access path
- security posture requires fail-closed behavior

## State Model

### Recovery Case States
At minimum, the platform MUST support semantic recovery states such as:

- `initiated`
- `verification_pending`
- `under_review`
- `awaiting_user_action`
- `approved_for_completion`
- `completed`
- `rejected`
- `expired`
- `cancelled`
- `superseded`

Exact names MAY vary, but the semantics must remain distinguishable.

### Conflict Case States
At minimum, the platform MUST support semantic conflict states such as:

- `detected`
- `triage_pending`
- `under_review`
- `awaiting_evidence`
- `remediation_planned`
- `resolved`
- `rejected`
- `superseded`
- `closed_without_change`

### Recovery Posture States
The platform SHOULD support at least the following semantic postures:

- `not_needed`
- `available`
- `recovery_only`
- `under_review`
- `blocked`
- `completed_recently`

### State Rules
- state transitions MUST be explicit, durable, monotonic where appropriate, and auditable
- terminal states MUST be durable and replay-safe
- derived views MUST NOT become mutation owners
- stale read models MUST NOT redefine canonical case truth
- supersession MUST preserve linkage to prior case lineage instead of hiding history
- case closure MUST preserve the reason and effective outcome of the prior progression

## Lifecycle / Workflow Model

### 1. Recovery Initiation
Recovery begins when the user or an authorized operator initiates a platform-approved recovery path for a known or safely discoverable canonical account context.

Required properties:
- initiation MUST be idempotent where policy requires
- anti-enumeration rules MUST be respected on public surfaces
- initiation MUST create or reference durable case state
- initiation MUST NOT silently mutate canonical identity or linked-auth ownership

### 2. Verification and Evidence Collection
The platform gathers proof appropriate to the requested recovery path.

Required properties:
- proof MUST be policy-bound and bounded by sensitivity
- verification artifacts MUST be durable enough for review and audit
- weak profile similarity is not sufficient proof
- evidence MAY support progression, denial, or escalation to review
- evidence references MUST preserve lineage rather than overwriting prior proof posture

### 3. Conflict Detection
During recovery and related access correction flows, the platform MUST evaluate whether the intended result is unambiguous.

The platform MUST detect, at minimum:
- provider subject already linked elsewhere
- multiple plausible candidate accounts
- duplicate-account indications
- recovery-significant email or contact overlap that is insufficient for automatic resolution
- link or unlink actions colliding with an active review case
- signals of possible compromise, abuse, or replay
- dependencies degraded such that safe automation loses confidence

### 4. Review / Remediation Routing
If the platform cannot safely complete ordinary restoration, it MUST route to explicit review.

Review routing MUST:
- preserve case lineage
- freeze unsafe automated progression
- avoid silently creating a new account or silently attaching an access path elsewhere
- retain enough context for support/security/operator review
- preserve current canonical ownership while review remains unresolved

### 5. Completion
Recovery completion restores access to the same canonical account.

Completion MAY include:
- reactivating or replacing an approved access path
- setting or rotating password credentials where applicable
- restoring account reachability
- closing or superseding stale recovery cases
- triggering targeted or global session containment
- recalculating continuity posture
- requiring fresh session issuance rather than continuing stale trust

### 6. Post-Completion Containment and Review
After completion, the platform MUST evaluate whether stale sessions, stale auth paths, open challenges, or related remediation cases require containment, invalidation, supersession, or closure.

### 7. Rejection / Expiration
If proof is insufficient, policy disallows the route, the case expires, or risk posture blocks safe progression, the platform MUST terminate the case explicitly without hidden mutation.

### 8. Corrective Remediation
When a prior mistake or contested ownership requires correction, the platform MUST use explicit remediation rather than ordinary recovery semantics. Corrective remediation MUST preserve lineage, reason codes, resulting continuity effect, and resulting session-containment effect.

## Invariants

1. One recovery outcome may restore access only to the same canonical `account_id` unless an explicit policy-controlled merge or remediation process outside ordinary recovery has been approved.
2. Recovery MUST NOT create a new account as a substitute for restoring the existing one.
3. Conflict or ambiguity MUST produce explicit case state rather than silent guesswork.
4. Provider callback success, email similarity, or profile similarity alone MUST NOT justify merge or reassignment.
5. Sessions are not canonical identity truth and do not by themselves authorize identity rewrite.
6. Active sessions MAY support temporary runtime continuity but do not replace durable future access continuity for sensitive decisions.
7. Recovery and conflict mutations MUST be auditable, reason-coded where applicable, and reconstructable.
8. Product surfaces MAY consume outcomes but MAY NOT own recovery truth.
9. Workspace, entitlement, and wallet-aware state remain attached to the canonical account and MUST NOT be orphaned by recovery or remediation.
10. Account state, auth-method state, recovery posture, and risk posture outrank ordinary session continuation.
11. Controlled reassignment, if ever approved, is exceptional and never implicit.
12. Degraded runtime conditions MUST NOT cause hidden semantic downgrade or truth substitution.

## Functional Rules

### Recovery Admission Rules
- recovery MAY be initiated only through approved recovery paths
- public initiation flows MUST avoid exposing whether a target account exists unless policy explicitly allows it
- any initiation with mutation side effects MUST be idempotent
- initiation MUST NOT bypass existing under-review or blocked posture without explicit supersession logic

### Recovery Restoration Rules
- restored access MUST target the same account
- restoration MUST NOT silently detach products, workspaces, wallet links, billing continuity, or audit history from the account
- restoration MUST NOT silently replace a linked provider with another provider on a different account
- restoration MUST preserve downstream subject anchoring on the same canonical account

### Conflict Rules
The platform MUST treat at least the following as explicit conflict conditions:
- provider subject already linked to another active or review-constrained account
- ambiguous candidate accounts during provider or recovery correlation
- duplicate-account risk where the same actor may have created multiple accounts through different flows
- recovery-significant email or contact overlap that is not sufficient proof
- support allegation of misbinding or prior mistaken merge/remediation
- contested access-path ownership or contested correction request

### Resolution Rules
If conflict exists, the platform MUST NOT:
- silently merge accounts
- silently overwrite provider ownership
- silently create a second account to avoid surfacing the conflict
- silently continue auth issuance against ambiguous identity state
- silently erase prior corrective history

Instead, it MUST:
- create durable conflict/remediation state
- enter review posture
- preserve continuity and audit lineage
- require support/admin/security decision where policy demands

### Sensitive Mutation Rules
The following actions are recovery-sensitive or conflict-sensitive and require stronger control:
- password set or reset
- primary or recovery-significant email change
- linked provider add or remove when continuity is affected
- provider disablement or restoration
- account recovery completion
- duplicate-account merge or account-side remediation affecting access paths
- support-led access correction
- global logout or forced session reset after trust-reset events

### Evidence Rules
- provider-stable subject is stronger than mutable profile data
- email may be used as hint, contact, or review signal, but not as sole canonical matching key in ambiguous cases
- evidence storage MUST preserve lineage and reviewability
- insufficient proof MUST lead to explicit denial or review, not silent permissiveness
- evidence references MAY support progression, denial, or escalation, but MUST NOT become alternate identity truth

## Permission / Access Considerations

Recovery and remediation are high-sensitivity actions.

Required control expectations:
- privileged support/admin actions MUST require stronger authorization than ordinary user self-service actions
- admin mutation requests MUST include reason code, policy reference, actor confirmation, and trace/correlation identifiers
- internal service transitions MUST include service identity and scoped authority
- recent-auth or step-up proof SHOULD be required for authenticated sensitive access mutations where policy requires
- support tools MUST NOT bypass owning-domain mutation boundaries

Successful recovery restores ability to authenticate the account. It does not bypass later workspace or permission evaluation.

## Entitlement Considerations

Recovery does not redefine commercial or product entitlement truth.

Required rules:
- account-scoped or workspace-scoped entitlements remain attached to the canonical account and/or workspace according to their owning domains
- recovery completion MUST NOT mint new entitlement state as a side effect unless separately owned and explicitly triggered by another domain
- remediation MUST NOT lose track of billing, credits, payout eligibility, or product continuity because those relationships remain attached to the same canonical account

## API / Contract Implications

The platform SHOULD expose recovery and conflict behavior through explicit boundaries.

### Identity APIs SHOULD support:
- recovery initiation
- recovery-case status retrieval using safe response semantics
- admin-safe recovery resolution operations
- conflict and remediation references and statuses
- account restriction and recovery posture reads

### Session / Linked-Login APIs SHOULD support:
- recovery-safe auth-method changes
- linked-method removal blocks with explicit continuity or conflict reasons
- session revoke / revoke-all following recovery completion or compromise correction
- conflict-safe completion of provider login and provider link flows

### API Rules
- all mutation-capable endpoints MUST require correlation identifiers
- idempotency is mandatory for recovery initiation, provider-resolution side effects, unlink/remediation requests, and admin remediation actions where retries are plausible
- errors MUST distinguish bounded categories such as validation failure, conflict, under review, policy denied, dependency degraded, and risk blocked without exposing unsafe internal detail
- async or review-required flows SHOULD return operation or case references rather than implying synchronous finality
- downstream APIs MUST NOT compress recovery, conflict, and restoration outcomes into one ambiguous “try again later” state

## Event / Async Implications

Recovery and conflict handling MUST emit durable domain events sufficient for audit, monitoring, and downstream coordination.

Representative semantic events include:
- `identity.recovery_initiated`
- `identity.recovery_status_changed`
- `identity.recovery_completed`
- `identity.recovery_rejected`
- `identity.recovery_expired`
- `identity.conflict_detected`
- `identity.conflict_resolved`
- `identity.auth_method_remediation_applied`
- `auth.session_global_invalidated`
- `auth.session_targeted_invalidated`

Async workflow rules:
- case transitions MAY be orchestrated asynchronously, but canonical case state MUST remain explicit and durable
- retries MUST be replay-safe and idempotent
- terminal events MUST NOT be emitted multiple times with contradictory meaning
- review workflows MUST preserve clear ownership between identity decisioning and auth/session execution
- consumers MUST treat these events as downstream notifications, not alternate mutation ownership

## Data Model / Storage Implications

At minimum, the platform SHOULD support the following durable semantic structures.

### `account_recovery_case`
Representative semantic fields:
- `recovery_case_id`
- `account_id`
- `recovery_type`
- `recovery_status`
- `initiated_by_actor_type`
- `initiated_at`
- `expires_at`
- `closed_at`
- `resolution_code`
- `review_reference`
- `support_case_reference`
- `policy_reference`
- `correlation_id`

### `identity_conflict_case`
Representative semantic fields:
- `conflict_case_id`
- related provider subject or identity hint reference
- candidate account references
- case type
- case state
- severity or review tier
- reviewer or operator reference
- resulting action reference
- created_at
- closed_at

### `account_recovery_evidence_reference`
Representative semantic fields:
- `evidence_reference_id`
- `recovery_case_id`
- evidence type
- evidence status
- artifact reference or hash
- collected_at
- collected_by_actor_type

### `account_access_remediation_action`
Representative semantic fields:
- `remediation_action_id`
- related case reference
- action type
- action state
- actor reference
- reason code
- policy reference
- executed_at
- resulting continuity effect
- resulting session-containment effect

### Data Rules
- recovery and conflict state MUST be durable source-of-truth entities or durable state machines, not transient frontend-only artifacts
- derived views MAY summarize case posture, but MUST NOT own case mutation
- destructive rewrite of prior truth SHOULD be avoided in favor of explicit lineage and terminal transitions
- evidence references SHOULD preserve traceability without overloading canonical account records
- stale or partial projections MUST NOT be used as justification for corrective identity mutation

## Read Model / Projection / Reporting Rules

The platform MAY maintain:
- support-review queue views
- current case summaries
- user-facing safe status summaries
- analytics and operational dashboards
- audit-friendly exports
- search and lookup projections for authorized internal users

These models MUST remain derived. They MUST NOT:
- redefine canonical case ownership
- become the only evidence of a recovery or conflict decision
- drive destructive canonical mutations directly
- out-rank owner-domain records when disagreements occur
- hide staleness when canonical containment or case-state changes have already occurred

If a derived model becomes stale, canonical case and identity records win.

## Security / Risk / Abuse Controls

Recovery and conflict handling are identity-and-access security controls.

The platform MUST preserve:
- anti-enumeration behavior where public exposure would weaken security
- anti-replay handling for recovery-sensitive and provider-sensitive completions
- stricter operator controls for privileged remediation
- recent-auth or step-up requirements where policy requires
- explicit containment after compromise-sensitive recovery or correction
- bounded exposure of internal decision reasons on public surfaces
- strong audit lineage for support or admin interventions
- denial or review posture when risk signals make ordinary continuation unsafe

Risk-aware rules:
- suspected compromise MAY justify broader containment than ordinary recovery
- elevated risk MAY require review even when evidence would otherwise seem sufficient
- support convenience MUST NOT outrank takeover resistance

## Boundary Violation Detection / Non-Canonical Patterns

The following patterns are explicitly non-canonical and forbidden:

- product-local recovery truth outside the identity domain
- silent automated account merge during recovery or provider correction
- silent creation of a new account instead of restoring the old one
- using email overlap alone as sufficient recovery proof in ambiguous cases
- using wallet linkage alone as default recovery proof
- treating stale session presence as durable authority for identity rewrite
- support-tool direct mutation of canonical identity or provider ownership without bounded workflow, reason code, and audit lineage
- destructive remediation that erases historical case or action lineage
- public error surfaces that leak sensitive internal account existence or remediation detail beyond policy

Implementations SHOULD detect and surface these violations through monitoring, tests, and audit review.

## Audit / Traceability Requirements

FUZE MUST be able to determine:
- when and why a recovery case or conflict case was created
- which canonical account was involved or which candidate accounts were considered
- which evidence types were collected and what decision they supported
- why a case was completed, rejected, expired, superseded, or routed to review
- which auth methods and sessions were affected by completion or remediation
- which operator or service executed a privileged action
- how continuity posture changed before and after the action
- which policy version constrained the action
- whether a visible case summary is canonical or derived

Auditability is required because recovery errors and conflict-resolution mistakes are high-impact, difficult to reconstruct later, and central to platform trust.

## Failure Handling / Edge Cases

### Lost Provider, Other Method Still Exists
The platform MAY restore access through another approved path without redefining identity. Recovery MAY still be unnecessary if continuity posture remains acceptable.

### Last Viable Access Path Lost
The platform MUST route toward approved recovery or remediation posture. It MUST NOT silently bootstrap a new account to avoid friction.

### Provider Subject Already Linked Elsewhere
The platform MUST raise explicit conflict or remediation state and MUST NOT attach the subject to a second account.

### Same Email Returned by Different Provider
Email overlap alone MUST NOT force merge. The platform MUST use conservative resolution and MAY require review.

### Duplicate Account Suspected Across Products
The platform MUST NOT let product-local convenience drive hidden merge logic. Duplicate-account handling MUST stay explicit and auditable.

### Recovery Completes After Suspected Compromise
The platform MAY globally invalidate prior sessions, close affected challenges, and require fresh trusted session establishment.

### Support Error During Remediation
The platform MUST preserve correction lineage and superseding actions rather than silently rewriting history.

### Stale Session Exists During Recovery
A stale or remembered session MUST NOT be treated as sufficient proof for identity rewrite. Policy MAY use it as one signal, not as sole authority.

### Product Switch With Different Provider Set
The user MUST be routed to the same account where safe, or to explicit conflict or recovery posture if safe automatic resolution is unavailable.

### Wallet Link Remains but Normal Access Is Lost
Wallet-aware context remains historically attached to the account, but wallet linkage alone does not become default recovery truth unless separately approved by policy.

## Operational Considerations

Operational systems SHOULD support:
- bounded review queues for recovery and conflict cases
- correlation across public, internal, and admin actions
- rate limits and abuse controls on public recovery initiation
- visibility into case aging, expiration, and unresolved high-severity cases
- deterministic operator tooling with narrow action classes
- monitoring for repeated replay, repeated collision, or suspicious recovery patterns
- support playbooks that reference policy without bypassing domain ownership
- safe degraded-mode behavior that pauses unsafe automation rather than guessing

The operational model MUST preserve the principle that recovery and remediation are structured platform workflows, not ad hoc customer-support edits.

## Migration / Compatibility / Supersession Considerations

- migrations MUST NOT silently weaken the requirement that recovery restores the same canonical account
- compatibility layers MAY preserve older product entry flows temporarily, but MUST NOT preserve hidden product-local recovery ownership
- older documents or implementations that imply email-only matching, implicit merge, implicit provider reassignment, or support-side silent rewrites are superseded within this scope
- new providers and new recovery methods MUST plug into the same recovery/conflict decision model rather than creating one-off exceptions
- case-state names MAY evolve, but the semantic distinctions defined here MUST be preserved

Migration plans MUST include replay-safe mapping, lineage preservation, explicit rollback posture, and correction handling for ambiguous historical states.

## Implementation-Contract Guardrails

Downstream implementations MUST preserve the following guardrails:

1. recovery restores the same canonical account rather than creating substitutes
2. conflict and ambiguity become explicit case state rather than hidden guesses
3. identity-domain ownership of recovery and conflict meaning is preserved
4. auth/session execution remains subordinate to approved recovery and remediation decisions
5. session containment after trust-reset events remains deterministic and auditable
6. evidence, provider input, session presence, and wallet context remain distinct truth classes
7. products and frontends do not become owners of recovery truth
8. operator or support overrides remain bounded, reason-coded, policy-constrained, and audited
9. derived views remain derived and regenerable
10. degraded dependencies do not cause hidden semantic downgrade or truth substitution
11. retries and repeated callbacks do not create duplicate or contradictory durable outcomes
12. downstream docs and teams MUST NOT optimize away lineage, explicit review posture, or conflict-case semantics where those elements protect continuity, security, or auditability

These guardrails are mandatory and MUST NOT be optimized away for convenience.

## Downstream Execution Staging

Recommended downstream staging order:

1. stabilize canonical identity and account ownership semantics
2. stabilize linked-auth and provider-resolution rules that create conflict posture
3. stabilize recovery-case and conflict-case data models and APIs
4. stabilize session-containment and auth-method mutation execution
5. stabilize secret-reset and proof-handling coordination in the key-management layer
6. integrate workspace, authorization, entitlement, and wallet-aware downstream continuity consumers
7. build support, analytics, and reporting read models over canonical case records

This ordering preserves identity stability before narrower runtime or UX layers specialize behavior.

## Required Downstream Specs / Contract Layers

This specification requires compatible downstream refinement and implementation-contract work in at least the following areas:

- `KEY_MANAGEMENT_AND_USER_RECOVERY_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `AUDIT_LOG_AND_ACTIVITY_SPEC.md`
- support and admin control-plane workflow specifications
- product integration specifications that surface recovery, remediation, or ambiguity-handling entry points

## Canonical Examples / Anti-Examples

### Canonical Example 1 — Same Account, New Password
A user loses their ordinary password-backed access path, completes approved recovery proof, rotates the password, and re-enters the same account. Prior sessions are globally invalidated according to policy. This is canonical.

### Canonical Example 2 — Provider Collision Routed to Review
A provider subject needed for recovery is already linked elsewhere. The platform opens a conflict case, preserves existing ownership, and routes to review rather than auto-attaching it. This is canonical.

### Canonical Example 3 — Duplicate-Account Suspicion Across Products
Signals suggest the same person may have created two accounts through different product paths. The platform opens explicit remediation posture and preserves lineage rather than silently merging. This is canonical.

### Canonical Example 4 — Support-Led Corrective Action
A prior mistake is confirmed through review. A bounded remediation action is executed with reason code, policy reference, session-containment effect, and audit lineage preserved. This is canonical.

### Anti-Example 1 — Silent Email-Based Merge
A recovery flow sees an email overlap with another account and auto-merges the two. This is forbidden.

### Anti-Example 2 — New Account as Recovery Shortcut
A product silently creates a new account when it cannot safely recover the existing one. This is forbidden.

### Anti-Example 3 — Session as Durable Recovery Proof
A remembered session on one device is treated as sufficient proof to rewrite ownership of an access path. This is forbidden.

### Anti-Example 4 — Support Dashboard as Canonical Truth
A support tool view shows a likely correction and directly mutates identity ownership without the bounded remediation workflow. This is forbidden.

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
- `KEY_MANAGEMENT_AND_USER_RECOVERY_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`

This specification directly governs or materially informs:

- `KEY_MANAGEMENT_AND_USER_RECOVERY_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `AUDIT_LOG_AND_ACTIVITY_SPEC.md`
- support/admin workflow specifications
- product integration specifications

## Explicitly Deferred Items

The following are intentionally deferred to adjacent or downstream specifications:

- exact key escrow, hardware-backed secret recovery, and cryptographic secret lifecycle
- exact MFA factor matrix and factor recovery UX
- exact legal-identity or KYC evidence policies
- exact document or evidence storage implementation details
- exact support queue tooling and reviewer assignment logic
- exact wallet-auth-as-recovery policy if ever approved
- exact service split, queue topology, or schema partitioning

These deferrals do not weaken the canonical recovery and conflict-handling model established here.

## Final Normative Summary

FUZE account recovery and conflict handling is the platform capability that preserves the user’s ability to return to the same canonical account when ordinary access is lost or when identity or access ambiguity prevents safe automation. Recovery restores access to the same account; it does not create a new identity path. Conflict handling makes ambiguity explicit; it does not silently merge, silently split, silently overwrite, or silently reassign identity.

Recovery and remediation are high-sensitivity workflows. They MUST be policy-controlled, continuity-safe, backend-owned, auditable, replay-safe, and coordinated with linked-auth state, provider-link state, session containment, and account-state precedence. Products may surface and consume these flows, but they may not redefine them. This document is the canonical FUZE rule set for recovery, ambiguity handling, and corrective identity-access restoration.

## Quality Gate Checklist

- [x] canonical owner is explicit for every material recovery-related truth family
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
