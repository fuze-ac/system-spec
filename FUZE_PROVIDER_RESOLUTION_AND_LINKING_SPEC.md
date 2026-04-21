# FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC

## Title
FUZE Provider Resolution and Linking Specification

## Document Metadata

- Document Name: `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Version: 1.1.0
- Effective Date: 2026-04-21
- Last Updated: 2026-04-21
- Reviewed On: 2026-04-21
- Document Owner: FUZE Platform Identity and Access Architecture / Provider Resolution Domain
- Approval Authority: FUZE Platform Architecture and Governance Authority
- Review Cadence: Quarterly or upon material change to provider onboarding, provider-subject mapping, account-resolution policy, auth-link lifecycle, recovery posture, session-security posture, or wallet-auth scope
- Governing Layer: Platform core / provider resolution and linked provider mapping
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, security engineering, support operations, audit, governance, API design, implementation-contract authors, platform operations
- Primary Purpose: Define the canonical FUZE model for resolving external authentication providers into canonical platform accounts and for linking, unlinking, disabling, restoring, reviewing, and remediating provider-backed access paths without fragmenting identity, weakening continuity, or bypassing backend-owned platform truth
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
  - `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
  - `IDENTITY_AND_ACCOUNT_SPEC.md`
  - `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
  - `WALLET_AWARE_USER_SPEC.md`
- Primary Downstream Dependents:
  - `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
  - `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
  - `AUTH_IDENTITY_API_SPEC.md`
  - `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - `WORKSPACE_AND_ORGANIZATION_SPEC.md`
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
  - `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
  - product integration specifications
  - support and control-plane workflows
- Supersedes: Earlier non-refined or less explicit provider-resolution, social-login, messaging-login, account-matching, and provider-linking writeups to the extent they conflict within this document’s scope
- Superseded By: Not yet known
- Related Decision Records: Not yet known
- Canonical Status Note: This document is the governing FUZE provider-resolution and provider-linking system specification. Downstream APIs, workflows, services, products, reports, support tooling, and control-plane systems MUST NOT redefine the semantics established here.
- Implementation Status: Normative architecture baseline; downstream API, workflow, storage, event, runtime, audit, and operator contracts must conform
- Approval Status: Drafted for refined-system inclusion; formal approval record not yet attached
- Change Summary:
  - normalized provider-resolution ownership against the current refined identity, canonical account/access/session, continuity, wallet-aware, and recovery specifications
  - tightened truth classes, ownership boundaries, conflict/default decision rules, and continuity-sensitive mutation requirements
  - clarified provider subject uniqueness, evidence handling, provider-claims limitations, and prohibited matching shortcuts
  - strengthened implementation-contract guardrails, idempotency/replay requirements, audit lineage, operator constraints, and non-canonical pattern handling

## Purpose

This specification defines the canonical FUZE provider-resolution and provider-linking architecture.

Its purpose is to make explicit:

- how external provider identities normalize into one FUZE-owned account-resolution model
- how provider-scoped subjects are treated as durable access-path bindings rather than alternate identities
- how sign-in, sign-up, link, unlink, disable, restore, review, and remediation flows must behave
- how the platform chooses among existing-account resolution, controlled bootstrap, controlled link completion, review/remediation posture, or denial
- how continuity, auditability, security posture, and anti-fragmentation rules constrain provider-linked behavior
- how provider-linked changes interact with sessions, recovery, workspaces, authorization, wallet-aware context, and downstream product behavior
- how products, frontends, APIs, support tools, and reporting surfaces consume provider-related truth without becoming owners of it

This document exists because FUZE supports multiple provider-backed entry paths and must preserve one canonical account model across products. Provider resolution is therefore a high-risk boundary for accidental duplicate accounts, silent merge, silent reassignment, unsafe linking, and hidden product-local identity drift. The provider-resolution model must remain explicit, conservative, backend-owned, deterministic where required, and auditable.

## Scope

This specification governs:

- provider-scoped subject mapping to canonical FUZE accounts
- provider-backed access-path resolution for sign-in and bootstrap
- provider-backed link creation, activation, disablement, removal, restoration, and supersession
- provider uniqueness and provider-subject ownership rules
- safe handling of provider claims, email hints, profile data, and provider metadata snapshots
- provider conflict detection, review posture, and remediation posture
- continuity-preserving rules for provider-related mutations
- provider-related audit, security, and operator-control requirements
- provider-resolution entity model, state model, and truth-class taxonomy
- API-boundary implications for provider start, callback, completion, link, unlink, review, and remediation flows
- read-model, support-view, reporting, and product-consumption constraints for provider-derived data

This specification does not define in full depth:

- the canonical account identity model
- the full linked-auth and session lifecycle model
- the full recovery playbook
- the full workspace, authorization, or entitlement model
- the full wallet-aware participation model
- exact provider SDK or transport implementation details
- exact OAuth, OIDC, messaging-provider, or wallet-auth protocol mechanics
- exact MFA or step-up mechanism catalogs
- exact support queue staffing or admin-console UI behavior

Those areas are governed by adjacent specifications.

## Out of Scope

This specification is explicitly out of scope for:

- product-local login UX design beyond canonical behavior constraints
- exact callback signature-validation or SDK wiring details
- exact browser or mobile token/session transport
- exact password storage or credential-secret handling
- exact case-management user interface design
- exact legal-identity, KYC, or compliance verification overlays unless later explicitly adopted
- exact multi-chain wallet-auth behavior if provider-auth by wallet is later approved
- exact privacy-policy wording or external legal disclosures

## Design Goals

1. Normalize many provider flows into one canonical account-resolution outcome model.
2. Prevent provider-backed identity fragmentation across all FUZE products.
3. Treat provider-subject bindings as durable, auditable, security-sensitive access assets.
4. Preserve continuity when providers are added, removed, disabled, superseded, or recovered.
5. Permit controlled bootstrap only when no safe existing-account resolution applies.
6. Support safe existing-account resolution without unsafe matching shortcuts.
7. Make ambiguity explicit through conflict/review posture rather than silent system guesses.
8. Keep products and frontends as initiators and consumers, not owners, of provider-resolution truth.
9. Preserve clear separation among provider input, canonical account identity, linked access paths, runtime sessions, wallet-aware context, authorization, and derived/public views.
10. Make provider-related support and security interventions replay-safe, auditable, policy-controlled, and reason-coded.

## Non-Goals

This specification is not intended to:

- make each provider its own platform identity model
- let products maintain separate provider-link truth
- use provider profile data as canonical identity keys
- silently merge accounts because two providers appear similar
- silently create a second account when safe linking or explicit review is required
- silently move a provider subject from one account to another
- let provider callbacks define authorization, workspace scope, entitlement, or wallet ownership
- allow operator convenience to bypass continuity, audit, or policy boundaries
- replace downstream API, schema, workflow, runbook, or security-control specifications

## Core Principles

### 1. Canonical Account Resolution Principle
Every validated provider flow MUST resolve into one canonical FUZE account outcome, not a product-local or provider-local identity outcome.

### 2. Provider Subject Principle
A provider-backed access path is anchored by a provider-scoped stable subject identifier, not by mutable profile hints.

### 3. Durable Link Principle
A provider link is a durable relationship between one canonical account and one provider subject under approved lifecycle rules.

### 4. No Silent Guessing Principle
When provider completion does not resolve safely, the platform MUST enter explicit review, conflict, bootstrap, or denial posture rather than silently guessing.

### 5. No Silent Reassignment Principle
A provider subject MUST NOT be silently moved from one canonical account to another.

### 6. Continuity Before Convenience Principle
Removing, replacing, disabling, or correcting provider links MUST preserve continuity even if that introduces friction.

### 7. Backend Normalization Principle
Different providers may use different external protocols, but the backend MUST normalize them into one FUZE-owned outcome model.

### 8. Products Consume, They Do Not Own Principle
Products may expose provider choices and consume provider-resolution outcomes, but they MUST NOT own provider mapping truth.

### 9. Evidence, Not Identity Principle
Provider outputs are evidence inputs into FUZE-owned rules. They do not become canonical account identity by themselves.

### 10. Conflict Is Explicit Principle
Collisions, ambiguity, takeover-sensitive signals, and contested provider claims MUST become explicit platform states with durable lineage.

## Canonical Definitions

### Provider
An approved external identity or login source used to authenticate access to a FUZE account.

### Provider Subject
The stable provider-scoped identifier for the actor inside that provider ecosystem.

### Normalized Provider Subject Key
The platform-approved canonical representation of provider identity used for durable uniqueness checks, typically including provider namespace plus stable subject identifier and, where required, issuer or chain scope.

### Provider Resolution
The backend-owned process that maps a validated provider result to one canonical FUZE account outcome.

### Provider Link
The durable relationship between a canonical account and one provider-backed authentication method.

### Provider Bootstrap
A controlled flow in which a provider result leads to creation of a new canonical account because no safe existing-account resolution applies.

### Provider Link Intent
A controlled flow in which an already authenticated account intentionally links an additional provider-backed access path.

### Provider Conflict
A state in which the platform cannot safely resolve a provider result to exactly one canonical account outcome and one correct provider-link state.

### Provider Review State
A controlled posture in which provider resolution or linking is paused for explicit confirmation, security review, or support/admin remediation.

### Provider Claims Snapshot
A cached, non-canonical snapshot of provider profile claims captured for convenience, review, troubleshooting, or audit support.

### Provider Resolution Outcome
The canonical result of a provider flow, such as:
- `resolved_existing_account`
- `bootstrap_required`
- `link_completed`
- `conflict_review_required`
- `risk_review_required`
- `resolution_denied`

### Provider Link Status
The durable lifecycle status of a provider link, such as:
- `pending`
- `active`
- `disabled`
- `restricted`
- `superseded`
- `removed`
- `review_required`

## Truth Class Taxonomy

This specification distinguishes the following truth classes and downstream implementations MUST preserve those distinctions.

### 1. Canonical Identity Truth
The account record, account lifecycle, and identity-domain continuity semantics anchored by `account_id`.

### 2. Provider-Link Truth
The durable approved mapping between a canonical account and a normalized provider subject key.

### 3. Auth / Session Runtime Truth
Auth challenge state, auth mutation posture, and session state representing temporary authenticated runtime access after resolution succeeds.

### 4. Policy Truth
Provider-approval policy, security policy, recovery policy, continuity policy, operator-control policy, and higher-order platform rules that constrain behavior.

### 5. Provider-Input Truth
Validated external inputs supplied by providers or approved adapters, such as issuer-subject pairs, claims, callback payloads, and signed assertions.

### 6. Conflict / Remediation Truth
Durable conflict cases, review states, remediation decisions, containment posture, and final correction outcomes.

### 7. Wallet-Aware Context Truth
Wallet-link state and wallet-derived participation context. Wallet-aware truth is adjacent context, not provider-resolution truth.

### 8. Authorization / Entitlement Truth
Workspace scope, membership, role assignment, permission evaluation, and product capability gating, owned by downstream domains.

### 9. Derived Read-Model Truth
Support views, dashboards, search projections, summaries, and convenience models derived from canonical records.

### 10. Reporting / Public View Truth
Exports, reports, public explanations, and registry/publication outputs. These remain downstream and correctable from canonical truth.

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

and above:

- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- product integration specifications

This document does not redefine canonical account identity or full session lifecycle in depth. It defines the provider-resolution and provider-linking rules those domains must preserve.

## System Boundaries

This specification governs the provider-resolution and provider-linking domain and its mandatory interaction with adjacent domains.

The provider-resolution domain MUST govern:

- provider normalization into backend-owned account-resolution outcomes
- normalized provider-subject uniqueness rules
- existing-account resolution, bootstrap, explicit link completion, and denial/review routing
- provider-related conflict detection and durable conflict state
- the provider-side portion of continuity-sensitive link correction and reassignment posture
- canonical rules for when provider claims may inform review versus when they are forbidden from deciding identity
- provider-related audit emission in coordination with audit domains

This specification MUST NOT govern in full:

- canonical account identity lifecycle ownership
- workspace membership truth
- organization scope truth
- role, permission, or entitlement truth
- wallet-link truth or chain ownership truth
- full session lifecycle truth
- product-local onboarding UX decisions except where they would violate canonical provider rules
- reporting truth

## Adjacent Boundaries

### Identity and Account
`IDENTITY_AND_ACCOUNT_SPEC.md` governs the canonical account, identity lifecycle, and account-level anti-fragmentation rules. This document governs how provider-backed evidence is resolved into approved account outcomes without redefining the account.

### Auth / Session / Linked Login
`AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md` governs linked-auth mutation semantics, auth challenge lifecycle, and session issuance after resolution succeeds. This document governs the provider-side mapping and resolution rules that feed that domain.

### Account Access Continuity
`FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md` governs the continuity rule that ordinary mutations must not strand the account. This document defines the provider-linked conditions and outcomes that continuity enforcement must respect.

### Recovery and Conflict Handling
`FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md` governs recovery cases, conflict cases, containment, and operator-reviewed remediation. This document defines the provider-resolution and provider-subject semantics those flows must preserve.

### Wallet-Aware Participation
`WALLET_AWARE_USER_SPEC.md` governs wallet linkage and participation-aware context. This document MUST NOT treat wallet presence as universal provider or identity proof unless explicitly approved through the identity/auth stack.

### Workspace, Authorization, and Entitlements
Workspace membership, role assignment, permission evaluation, and product capability are resolved after canonical account resolution and authenticated session issuance. Provider success MUST NOT be treated as direct authorization or entitlement.

## Conflict Resolution Rules

When layers disagree, the platform MUST resolve provider-related conflicts in the following order unless a higher-order platform policy explicitly overrides it:

1. canonical identity-domain records and restriction state
2. canonical provider-link and linked-auth records
3. explicit policy and security constraints
4. validated provider-input evidence interpreted through approved normalization rules
5. runtime product/client state
6. derived views, dashboards, support summaries, or reports

Specific conflict rules:

- provider profile data MUST NOT override canonical account ownership
- email similarity MUST NOT justify silent merge, silent relink, or silent reassignment
- wallet presence MUST NOT justify automatic provider resolution or identity reassignment
- stale product-local provider state MUST NOT override canonical backend records
- support tooling views MUST NOT be treated as authoritative if they diverge from canonical records
- operator preference MUST NOT override the uniqueness of a normalized provider subject key without explicit remediation workflow, reason code, and policy authorization
- reporting exports MUST NOT be used to repair provider-link truth directly
- a provider callback for a subject already bound to another account MUST default to explicit conflict/review or denial, not silent overwrite

## Default Decision Rules

Where ambiguity exists, the following defaults apply:

1. default actor anchor: `account_id`
2. default durable provider binding key: normalized provider subject key, not email or display name
3. default owner of provider-to-account resolution outputs: Identity and Account Domain
4. default owner of provider-link lifecycle and post-resolution auth behavior: Auth / Session / Linked Login Domain
5. default interpretation of provider email: hint, contact field, or review signal, not sole canonical matching key
6. default interpretation of provider claims snapshots: convenience and review support only, not write-owner truth
7. default resolution for ambiguous provider collision: explicit conflict/review, not silent overwrite
8. default resolution for last-viable-provider removal: block ordinary self-service unless approved replacement, recovery, or operator-reviewed remediation exists
9. default resolution for disputed reassignment request: preserve existing canonical ownership and open remediation rather than auto-transfer
10. default resolution for degraded provider callbacks or missing required claims: deny completion or route to review rather than guess
11. default interpretation of wallet-auth-like evidence if separately introduced: provider input subject to the same identity and continuity protections, not a shortcut around them
12. default interpretation of product-local provider aliases: non-canonical presentation data only

## Roles / Actors / Entities

### End User
May initiate sign-in, bootstrap, and link-intent flows; may confirm approved steps; may not directly mutate canonical provider-link truth outside approved platform workflows.

### Identity and Account Domain
Owns canonical account identity, provider-to-account resolution outputs, anti-fragmentation rules, and identity-safe bootstrap or conflict-routing decisions.

### Auth / Session / Linked Login Domain
Owns provider-link lifecycle, auth challenge lifecycle, link completion orchestration, and session issuance after successful provider resolution.

### Recovery / Remediation Domain
Owns provider-related conflict cases, support/security review posture, corrective reassignment if ever approved, and containment or restoration behavior.

### Security / Risk Function
Owns elevated review posture, compromise handling, anomaly interpretation, stronger evidence requirements, and security-led containment decisions.

### Support / Operations Function
May assist with provider review and remediation under stronger authorization, reason-code, and audit requirements. Support does not become canonical identity owner.

### Products and Frontends
May initiate provider flows, render provider choices and outcomes, and consume resulting authenticated account context. They MUST NOT own provider mapping truth or merge/reassign accounts.

### Provider Adapter / Integration Layer
Validates and normalizes external provider outputs into approved internal provider-input structures. It does not own canonical provider-link truth.

### Provider Link Entity
Durable source-of-truth record linking an account to a normalized provider subject key under lifecycle rules.

### Provider Challenge Entity
Durable short-lived record for provider-init, callback correlation, link-intent verification, or step-up requirements.

### Provider Conflict Case Entity
Durable source-of-truth record for ambiguity, collision, duplicate-account risk, or contested reassignment involving provider evidence.

## Ownership Model

### Identity Domain MUST Own
- canonical account identity
- provider-to-account resolution outputs
- bootstrap decisions that create a new account
- account-level anti-fragmentation rules
- final identity-safe decision that a provider result maps to an existing account, requires bootstrap, or requires review

### Auth / Session / Linked Login Domain MUST Own
- provider-link lifecycle state
- provider challenge lifecycle
- link-intent and link-completion execution
- post-resolution session issuance or denial
- auth-side continuity checks during link mutation

### Recovery / Conflict Domain MUST Own
- provider-related conflict/remediation case lifecycle
- manual reassignment review if explicitly authorized by policy
- containment, denial, and restoration posture when ordinary resolution is unsafe

### Products / Frontends MAY
- expose approved provider choices
- initiate provider-auth requests
- initiate link-intent flows for already authenticated users
- display resulting outcomes and guidance
- consume resulting authenticated account context and downstream authorization outcomes

### Products / Frontends MUST NOT
- directly write provider-link truth
- create canonical provider-to-user mapping tables
- decide account merge or provider reassignment
- treat provider profile data as canonical identity truth
- bypass review, recovery, or continuity constraints through custom logic

## Authority / Decision Model

The following decision model is normative:

1. validate provider transport and authenticity through approved adapter logic
2. normalize provider input into a provider namespace plus normalized provider subject key and allowed claims structure
3. determine whether the normalized provider subject key already has an active or review-constrained binding
4. if yes, resolve through the existing canonical account path unless a blocking policy or review state applies
5. if no binding exists, determine whether the flow is:
   - an already authenticated link-intent flow
   - a safe bootstrap flow
   - a case requiring explicit review or denial
6. perform continuity, conflict, restriction, and policy checks before mutating durable state
7. issue sessions only after successful resolution and post-auth policy checks
8. emit durable audit lineage and owner-emitted events for the resulting outcome

Products, support tools, and reports MAY observe this process. They MUST NOT replace it.

## State Model

### Provider Resolution Outcome States
- `resolved_existing_account`
- `bootstrap_required`
- `link_completed`
- `conflict_review_required`
- `risk_review_required`
- `resolution_denied`

### Provider Link Lifecycle States
- `pending`
- `active`
- `disabled`
- `restricted`
- `review_required`
- `superseded`
- `removed`

### Provider Conflict / Review States
- `open`
- `awaiting_evidence`
- `security_review`
- `support_review`
- `remediation_authorized`
- `resolved`
- `denied`
- `contained`

State transitions MUST be explicit, durable, and reconstructable. Downstream systems MUST NOT infer destructive transitions from missing rows or overwritten caches.

## Lifecycle / Workflow Model

### 1. Existing-Account Provider Sign-In
A validated provider result whose normalized provider subject key is already durably bound to a canonical account SHOULD resolve to that same account, subject to restriction, recovery, and risk posture checks. Session issuance occurs only after those checks succeed.

### 2. Controlled Bootstrap
A validated provider result with no existing durable binding MAY create a new canonical account only when bootstrap policy allows it and when no higher-confidence safe existing-account resolution path applies. Bootstrap MUST be explicit and auditable.

### 3. Authenticated Link-Intent Flow
An already authenticated actor MAY initiate link intent for an additional provider. The platform MUST verify the current actor, validate provider proof, confirm no conflicting existing ownership, evaluate continuity/risk posture, and then create the durable provider link if safe.

### 4. Unlink / Disable / Restore Flow
Provider-link removal or disablement MUST preserve continuity. Ordinary self-service unlink of the last viable access path MUST be blocked unless an approved replacement, recovery path, or operator-reviewed remediation exists.

### 5. Supersession / Correction Flow
If policy allows a provider-link correction, the correction MUST occur through explicit remediation with durable lineage. The platform MUST prefer supersession records and bounded state transitions over hidden destructive rewrites.

### 6. Conflict / Review Flow
If provider evidence does not safely resolve to exactly one account outcome, the platform MUST open explicit conflict or review posture and MUST NOT auto-complete sign-in, merge, or relink.

### 7. Recovery-Aligned Flow
If provider access becomes unusable or disputed, the platform MUST defer to recovery/remediation workflows that restore access to the same canonical account without redefining identity ownership.

## Invariants

1. one normalized provider subject key MUST NOT be simultaneously active on more than one canonical account
2. one canonical account MAY have multiple approved provider-backed access paths
3. provider-backed access paths are access paths, not alternate identities
4. email, display name, avatar, or profile similarity MUST NOT serve as sole canonical matching proof
5. provider success MUST NOT equal authorization, entitlement, or wallet ownership success
6. provider-link mutations MUST remain continuity-aware, replay-safe where APIs permit retries, and auditable
7. ambiguous provider outcomes MUST fail into explicit review or denial rather than silent guesswork
8. products and frontends MAY initiate flows but MUST NOT own provider-link truth
9. derived views remain derived and regenerable
10. operator actions on provider ownership remain bounded, reason-coded, policy-constrained, and audited

## Functional Rules

### Approved Provider Rule
Every provider MUST be explicitly approved by FUZE policy and implementation scope before production use.

### Normalized Subject Rule
Every provider-backed method MUST use a platform-approved normalized provider subject key as the durable mapping key.

Examples include:
- OIDC-style providers: issuer plus subject
- messaging providers: provider-specific stable subject identifier
- wallet-auth if separately approved later: normalized chain plus address or other explicitly approved wallet subject key

### Email Non-Canonical Rule
Email MAY be used as a hint, contact field, or review signal, but MUST NOT be the sole canonical matching key for provider-resolution decisions.

### Existing Binding Rule
If a normalized provider subject key is already durably bound to a canonical account, subsequent validated provider completions for that same subject MUST resolve to that same account unless an explicit remediation workflow authorizes otherwise.

### Bootstrap Rule
Bootstrap MUST be explicit. The platform MUST NOT silently convert an ambiguous existing-account case into a new-account creation merely because no quick safe match is available.

### Link-Intent Rule
New provider linkage for an already authenticated user MUST require:
- current actor authorization to modify the account
- provider proof validation
- no conflicting existing durable ownership
- continuity posture evaluation
- policy and risk approval
- durable audit emission

### Unlink Rule
A provider link MAY be removed only when:
- the actor is authorized to modify the account
- access resilience is preserved
- or the action is part of a controlled recovery, remediation, or operator-reviewed flow

### Reassignment Rule
Ordinary automated reassignment of a provider subject from one account to another is forbidden. Any permitted reassignment MUST be a bounded remediation action with explicit reason code, operator or policy authorization, containment where needed, and durable lineage.

### Session Issuance Rule
A session MAY be issued only after:
- canonical account resolution succeeded
- the provider flow completed successfully
- the provider link or linkable state is valid for use
- unresolved conflict or remediation posture is not blocking issuance
- account status, recovery posture, and risk posture allow issuance

### Derived View Rule
Provider summaries, support views, analytics outputs, and provider-claims caches MAY summarize canonical state but MUST NOT become shadow write owners.

## Permission / Access Considerations

Successful provider completion proves only that approved provider-backed authentication reached an account outcome under FUZE rules. It does not by itself prove:

- workspace membership
- organization scope
- role assignment
- permission grant
- entitlement or capability
- wallet ownership or holder status
- public-registry eligibility

After successful account resolution and session issuance, downstream workspace, authorization, and entitlement domains MUST independently evaluate access.

## Entitlement Considerations

Products MAY use provider choice or provider-linked state as a product-experience input only where explicitly approved. Provider linkage itself MUST NOT become a hidden entitlement system. Any product that gates capability based on provider origin MUST do so through explicit policy and downstream entitlement rules, not through ad hoc provider-link semantics.

## API / Contract Implications

The platform SHOULD expose provider-related behavior through explicit API boundaries.

### Identity APIs SHOULD support:
- provider-auth request initiation
- provider callback normalization and resolution
- bootstrap-eligible outcome responses
- conflict/remediation references
- deterministic machine-readable resolution outcomes

### Session / Linked-Login APIs SHOULD support:
- provider link listing
- link-intent challenge creation
- link completion
- unlink, disable, restore, and supersession flows
- continuity-aware mutation results

### Admin / Support APIs SHOULD:
- preserve stronger authorization for remediation
- require reason codes and policy references
- expose audit-friendly action summaries
- avoid direct destructive rewrites of canonical provider-link records

### Products and Frontends MUST NOT:
- directly resolve provider subjects into canonical identity without backend mediation
- bypass link/resolve conflict handling
- treat provider profile data as source-of-truth identity
- infer success merely from callback reception without canonical backend outcome

## Event / Async Implications

Provider flows often involve callbacks, retries, asynchronous verification, or multi-step orchestration. Downstream event and workflow implementations MUST preserve:

- idempotent handling of repeated callbacks where API contracts permit retries
- terminal-state semantics that prevent duplicate side effects
- durable correlation identifiers across request, callback, link mutation, and session issuance
- explicit distinction between provider-input receipt and canonical provider-resolution success
- explicit event families for provider-resolution result, provider-link mutation, conflict/review creation, and remediation completion
- replay-safe consumers that do not recreate or overwrite provider ownership incorrectly
- audit lineage tying async execution to canonical owner-domain outcomes

## Data Model / Storage Implications

This specification requires the following data-model discipline:

- provider-subject bindings MUST remain durable source-of-truth entities
- provider challenges MUST remain explicit short-lived durable records
- provider claims snapshots are convenience caches only
- provider conflict/remediation records MUST be durable and auditable
- derived provider summaries MUST remain separate from canonical link truth
- replay and idempotency controls MUST remain durable mutation-safety artifacts where retries are supported
- provider-related corrections SHOULD prefer explicit lineage and state transitions over hidden destructive rewrites
- normalized provider subject uniqueness MUST be enforceable at the authoritative storage boundary

Representative canonical entities include:
- `provider_link`
- `provider_challenge`
- `provider_conflict_case`
- `provider_claims_snapshot`
- `provider_mutation_audit_reference`

Downstream schemas MAY refine names, but not the semantics.

## Read Model / Projection / Reporting Rules

The platform MAY maintain:

- provider-link summaries for support tooling
- provider choice display models for products
- provider-activity dashboards for operations
- analytics and exports for review or adoption analysis
- public-safe explanations of supported providers where appropriate

These models MUST remain derived. They MUST NOT:

- redefine provider ownership
- become the only evidence of provider-link status
- drive destructive canonical mutations directly
- out-rank owner-domain records when disagreements occur

If a derived model becomes stale, canonical owner-domain records win.

## Security / Risk / Abuse Controls

Provider resolution and linking are security-sensitive platform behaviors. The platform MUST support:

- provider authenticity validation through approved adapters
- stronger review posture for suspicious or takeover-sensitive flows
- risk-aware denial or review routing before session issuance
- containment options when provider correction changes the trust boundary
- targeted or global session invalidation when provider remediation requires it
- abuse monitoring for repeated collision, replay, enumeration, or callback-anomaly patterns
- policy-bounded step-up or recent-auth requirements for sensitive provider-link mutations where applicable

Security controls MAY constrain provider flows, but resulting business state MUST still be written through the correct owner domains.

## Boundary Violation Detection / Non-Canonical Patterns

The following patterns are explicitly non-canonical and forbidden:

- a product keeping its own canonical provider-to-user mapping table
- email-only account matching during provider callback resolution
- silently overwriting an existing provider subject binding
- creating a new account when a safer explicit review state is required
- letting wallet linkage implicitly satisfy provider resolution
- treating provider claims caches as canonical truth
- letting support tooling mutate provider-link ownership without reason code, policy reference, and audit lineage
- treating a successful provider callback as equivalent to authorization success
- hiding provider correction in destructive data edits without preserved lineage

Implementations SHOULD detect and surface these violations through monitoring, tests, and audit review.

## Audit / Traceability Requirements

The platform MUST be able to determine:

- which provider was used
- which normalized provider subject key was involved
- which canonical account outcome was chosen
- why bootstrap, review, denial, or existing-account resolution was selected
- who or what initiated provider-link mutation
- which policy version constrained the action
- whether continuity checks passed or blocked the mutation
- whether remediation or containment occurred
- which operator performed privileged correction
- which sessions were issued, denied, or invalidated because of provider-related actions
- how historical provider ownership changed over time where policy permits correction

Provider-linked changes are part of the FUZE trust surface and MUST be reconstructable.

## Failure Handling / Edge Cases

### Same Email Across Different Providers
This is a review signal, not proof of the same canonical account.

### Provider Subject Already Bound to Another Account
The platform MUST preserve existing canonical ownership and route the new attempt into denial or explicit conflict/remediation posture.

### Callback Replayed
Idempotency and terminal-state semantics MUST prevent duplicate account creation, duplicate link creation, or duplicate session issuance.

### Authenticated User Attempts to Link an Already-Owned Subject
The platform MUST deny the action or open explicit remediation. It MUST NOT steal the binding.

### Removal of Final Viable Provider
The platform MUST block ordinary self-service mutation unless approved replacement, recovery, or operator-reviewed remediation exists.

### Provider Becomes Temporarily Unavailable
The platform MAY deny completion, defer issuance, or preserve existing active sessions according to policy, but MUST NOT invent new account-matching semantics as a workaround.

### Recovery Completion After Provider Dispute
The platform SHOULD globally revoke or security-invalidate prior sessions according to recovery and security policy.

### Wallet Evidence Suggests Same Person
Wallet evidence MAY inform separate wallet-aware workflows, but MUST NOT by itself resolve provider identity ownership.

### Historical Data Imported from Legacy Product
Migration MUST preserve normalized subject uniqueness, lineage, and explicit remediation posture for ambiguous mappings. No silent merge or silent reassignment is allowed.

## Operational Considerations

Operational implementations SHOULD support:

- observability for provider-login success, denial, review, and callback failures
- explicit monitoring for replay, collision, enumeration, and anomaly patterns
- support tooling that reads derived summaries without bypassing canonical mutation boundaries
- safe remediation workflows for policy-approved correction paths
- correlation across identity, provider, auth, session, security, and audit systems
- replay-safe mutation handlers and trace identifiers across async boundaries
- metrics for continuity-blocked provider actions and collision/review rates

Operational convenience MUST NOT weaken canonical provider semantics.

## Migration / Compatibility / Supersession Considerations

- migrations MUST NOT silently weaken provider-subject uniqueness or continuity semantics
- new providers MUST be onboarded through the shared resolution model, not product-local exceptions
- compatibility layers MAY preserve older flows temporarily, but canonical provider semantics MUST remain platform-owned
- legacy data with weak provider matching MUST be normalized conservatively and routed through remediation where ambiguity remains
- if older documents or implementations imply email-only matching, silent reassignment, or looser provider ownership, this refined specification supersedes those interpretations within its scope

Migration plans MUST include replay-safe mapping, preserved audit lineage, explicit rollback posture, and conflict queues for unresolved historical ambiguity.

## Implementation-Contract Guardrails

Downstream implementations MUST preserve all of the following:

1. provider resolution remains backend-owned, conservative, deterministic where required, and auditable
2. the normalized provider subject key remains the durable uniqueness anchor
3. provider profile data and email remain evidence inputs, not canonical account truth
4. bootstrap remains explicit and MUST NOT hide unresolved ambiguity
5. provider-link mutations remain continuity-aware, policy-bounded, and replay-safe where APIs permit retries
6. ambiguous collisions default to explicit review, denial, or remediation rather than silent overwrite
7. products and frontends do not become owners of provider-link truth
8. sessions are issued only after successful canonical resolution and post-auth policy checks
9. operator corrections remain bounded, reason-coded, policy-constrained, and audited
10. derived views remain derived and regenerable
11. degraded runtime conditions must not cause hidden semantic downgrades or truth substitution
12. downstream docs and teams MUST NOT optimize away explicit review posture, challenge durability, or correction lineage where those elements protect continuity, security, or auditability

These guardrails are mandatory and MUST NOT be optimized away for convenience.

## Downstream Execution Staging

Recommended downstream staging order:

1. stabilize canonical identity and account ownership semantics
2. stabilize provider-resolution outcomes and normalized subject-key rules
3. stabilize linked-login and session API contracts that consume provider outcomes
4. stabilize session lifecycle and security containment behavior
5. stabilize recovery and remediation flows for contested provider cases
6. integrate workspace, authorization, and entitlement sequencing
7. integrate wallet-aware or public-trust surfaces that consume downstream outputs
8. build reporting, analytics, and support read models

This ordering preserves identity and access stability before narrower implementations specialize behavior.

## Required Downstream Specs / Contract Layers

This specification requires compatible downstream refinement and implementation-contract work in at least the following areas:

- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- product integration specifications
- support and control-plane workflow specifications

## Canonical Examples / Anti-Examples

### Canonical Example 1 — Existing Subject, Same Account
A user signs into one FUZE product with Google. Later they use another FUZE product that also supports Google. The same normalized provider subject key resolves to the same `account_id`. This is canonical.

### Canonical Example 2 — Explicit Authenticated Link Intent
A user signs in with one approved method and then intentionally links Telegram while already authenticated. The platform validates the Telegram subject, confirms no conflicting owner, evaluates continuity posture, and creates a new provider link on the same account. This is canonical.

### Canonical Example 3 — Conflict Routed to Review
A provider callback contains profile signals that resemble an existing person, but no safe durable subject binding exists and email similarity is ambiguous. The platform opens review posture rather than auto-linking or auto-bootstrapping. This is canonical.

### Canonical Example 4 — Provider Correction Through Remediation
A legacy mapping import reveals a historically incorrect provider binding. The platform opens a remediation case, preserves audit lineage, applies bounded correction, and invalidates affected sessions where required. This is canonical.

### Anti-Example 1 — Silent Email Merge
A provider callback returns an email that matches another account and the platform silently merges accounts. This is forbidden.

### Anti-Example 2 — Product-Local Mapping Table
A product keeps its own provider-to-user table and treats it as canonical. This is forbidden.

### Anti-Example 3 — Silent Provider Theft
A user who is authenticated on one account attempts to link a provider subject already bound to another account and the system silently steals the binding. This is forbidden.

### Anti-Example 4 — Provider Success Equals Authorization
A product assumes a successful provider callback automatically grants workspace access or premium capability. This is forbidden.

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
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
- `WALLET_AWARE_USER_SPEC.md`

This specification directly governs or materially informs:

- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- product integration specifications
- support and control-plane workflows

## Explicitly Deferred Items

The following are intentionally deferred to adjacent or downstream specifications:

- exact provider list and onboarding sequence by product
- exact OIDC, OAuth, messaging-provider, or wallet-auth transport mechanics
- exact challenge payload shape and storage implementation detail
- exact recent-auth, step-up, and MFA requirements by mutation type
- exact support queue routing and approval workflow details
- exact provider-specific UI copy and user-journey design
- exact privacy retention and redaction policy wording
- exact provider adapter deployment topology

These deferrals do not weaken the canonical provider-resolution and linking model established here.

## Final Normative Summary

FUZE provider resolution and linking is the platform capability that turns many external provider flows into one coherent canonical account-access system. A provider subject is a durable access-path binding, not a separate identity. Provider completion MUST resolve into one explicit backend-owned outcome: existing-account resolution, controlled bootstrap, controlled link completion, review/remediation posture, or denial. The platform MUST NOT silently split, merge, overwrite, or reassign account identity because of provider behavior, email similarity, product entry differences, or convenience shortcuts.

Provider-linked changes are continuity-sensitive, security-sensitive, and audit-sensitive. They MUST preserve canonical account continuity, preserve uniqueness of normalized provider-subject ownership, and route ambiguity into explicit conflict or remediation posture. Products may initiate and consume provider flows, but they MUST NOT own provider-resolution truth.

## Quality Gate Checklist

- [x] canonical owner is explicit for every material provider-related truth family
- [x] mutation boundaries are explicit
- [x] adjacent boundaries are explicit
- [x] truth classes are explicit
- [x] conflict-resolution rules are explicit where needed
- [x] default decision rules are explicit where ambiguity could arise
- [x] non-canonical patterns are called out clearly
- [x] operator and admin override paths are bounded and audited
- [x] read-model, cache, reporting, and projection rules are explicit
- [x] wallet and adjacent domain responsibilities are explicit where relevant
- [x] failure and degraded-mode behaviors are explicit
- [x] downstream implementation guardrails are explicit
- [x] dependencies and downstream impacts are explicit
- [x] non-goals and deferred items are explicit
- [x] the document is strong enough to support backend, API, data, runtime, support, and audit implementation without inventing contradictory semantics
