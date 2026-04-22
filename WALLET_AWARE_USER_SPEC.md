# WALLET_AWARE_USER_SPEC

## Title
WALLET_AWARE_USER_SPEC

## Document Metadata

- Document Name: `WALLET_AWARE_USER_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Version: 1.1.0
- Effective Date: 2026-04-21
- Last Updated: 2026-04-21
- Reviewed On: 2026-04-21
- Document Owner: FUZE Platform Wallet-Aware User Domain
- Approval Authority: FUZE Platform Architecture and Governance Authority
- Review Cadence: Quarterly or upon material wallet-linking, chain-boundary, eligibility, public-registry, payout, or security-model change
- Governing Layer: Platform core / wallet-aware user and participation context
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, security engineering, support operations, audit, governance, API design, reporting, chain-adjacent services, transparency/public-registry systems, payout and eligibility systems
- Primary Purpose: Define the canonical FUZE wallet-aware user model, including how canonical accounts relate to wallets, how wallet links are verified and lifecycle-governed, how wallet-aware, holder-aware, token-aware, and eligibility-aware context is derived, and how downstream products and systems consume wallet-aware context without redefining canonical account identity, chain truth, authorization, credits semantics, payout semantics, or public-reporting semantics
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
  - `IDENTITY_AND_ACCOUNT_SPEC.md`
  - `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
  - `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
  - `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
  - `WORKSPACE_AND_ORGANIZATION_SPEC.md`
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- Primary Downstream Dependents:
  - `WALLET_AWARE_USER_API_SPEC.md`
  - `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
  - `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
  - `ETHEREUM_TOKEN_LAYER_SPEC.md`
  - `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
  - `TRANSPARENCY_REPORTING_SPEC.md`
  - `WORKSPACE_AND_ORGANIZATION_SPEC.md`
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - product integration specifications
  - public transparency and reporting specifications
- Supersedes: Earlier non-refined or less explicit wallet-aware, holder-aware, and wallet-linked user writeups to the extent they conflict within this document’s scope
- Superseded By: Not yet known
- Related Decision Records: Not yet known
- Canonical Status Note: This document is the governing FUZE wallet-aware user system specification. Downstream implementations, products, APIs, reports, registries, and control-plane tools MUST NOT redefine the semantics established here.
- Implementation Status: Normative architecture baseline; downstream API, workflow, service, storage, eligibility, and reporting contracts must conform
- Approval Status: Drafted for refined-system inclusion; formal approval record not yet attached
- Change Summary:
  - tightened account-rooted wallet semantics against refined identity and auth/session documents
  - clarified truth-class separation between wallet-link truth, chain truth, eligibility truth, authorization truth, and public/reporting truth
  - strengthened conflict, reassignment, unlink, and remediation rules for contested wallets
  - hardened implementation-contract guardrails for idempotency, audit lineage, and operator-controlled actions
  - clarified registry, transparency, and snapshot interactions as downstream derived layers rather than canonical wallet-link ownership

---

## Purpose

This specification defines the canonical wallet-aware user architecture of the FUZE platform.

Its purpose is to make explicit:

- how a canonical FUZE account relates to one or more wallets
- how wallet links are represented, verified, bounded, and lifecycle-controlled
- how wallet-aware, holder-aware, token-aware, and eligibility-aware behavior is derived without replacing the account model
- how wallet-aware context interacts with identity, sessions, workspaces, authorization, product capabilities, transparency surfaces, reporting, and participation logic
- how wallet-link truth must remain distinct from chain truth, credits truth, payout truth, role truth, workspace truth, and public publication truth
- how products, reporting surfaces, transparency layers, and chain-adjacent systems may consume wallet-aware context without inventing product-local wallet identity systems
- how conflict handling, reassignment, unlinking, invalidation, and support or operator corrections must preserve trust, continuity, and auditability

This document exists because FUZE is both a SaaS platform ecosystem and a Web3-aware participation environment. The platform therefore must connect canonical account identity to on-chain participation context in a way that is structurally clear, security-aware, implementation-usable, and consistent with the broader FUZE separation among identity, authentication, wallet linkage, token balances, Platform Credits, eligibility pipelines, public registries, and payout logic.

---

## Scope

This specification governs:

- the distinction between canonical account identity and wallet-linked participation context
- the canonical wallet-link model for FUZE accounts
- wallet-link lifecycle states, mutation boundaries, and conflict states
- proof-of-control requirements for wallet linking
- multi-wallet support, preference metadata, and wallet-history continuity
- wallet use in holder-aware product behavior, ecosystem recognition, public-registry outputs, and eligibility-related systems
- ownership boundaries between wallet-link records and chain truth
- wallet-related security, audit, review, conflict, and remediation rules
- wallet-aware behavior across products and workspace-scoped experiences
- wallet-aware data-model requirements
- read-model, projection, and reporting boundaries for wallet-aware context
- edge cases, degraded-mode behavior, and migration or supersession posture

This specification does not redefine:

- canonical account identity semantics
- auth/session mechanics
- workspace lifecycle or workspace membership semantics
- role, permission, or entitlement truth
- Ethereum token balance truth
- Platform Credits semantics
- payout execution contract semantics
- detailed snapshot and eligibility algorithm rules
- public registry schema details
- exact wallet-auth implementation scope if separately approved later

Those belong in adjacent or downstream specifications.

---

## Out of Scope

This specification is explicitly out of scope for:

- exact wallet-signature or proof-method implementation detail
- exact chain-indexing infrastructure design
- exact snapshot-proof format or eligibility formulas
- exact public-registry publication schema
- exact payout-claim or claim-routing mechanics
- exact support tooling UX
- exact multi-chain expansion detail beyond approved scope
- product-specific token-benefit formulas beyond platform wallet-aware inputs
- generalized legal-identity or KYC semantics

---

## Design Goals

The design goals of the FUZE wallet-aware user model are:

1. allow one canonical FUZE account to link to zero, one, or many wallets in a controlled way
2. preserve the distinction between platform identity and on-chain ownership or control
3. support holder-aware and token-aware experience across FUZE without turning wallets into the sole identity layer
4. support Ethereum-based participation, transparency, and eligibility logic without collapsing chain truth into account storage
5. keep wallet-link state auditable, secure, correction-capable, conflict-safe, and replay-safe
6. support future products and future participation logic without redefining the wallet model
7. make products consume wallet-aware context through shared platform rules rather than ad hoc chain assumptions
8. preserve separation among wallet linkage, token truth, authorization, entitlements, credits, registry publication, and payout meaning
9. support historical continuity when wallet configurations change over time
10. remain strong enough to support downstream implementation-contract work without forcing downstream teams to invent semantics

---

## Non-Goals

This specification is not intended to:

- make wallet addresses the canonical user identity of FUZE
- treat token balance reads as substitutes for platform authorization
- define arbitrary wallet-based admin, operator, or governance privileges outside approved policy
- require every FUZE product to support or require wallet linkage
- collapse wallet linkage into ordinary auth/session behavior
- define every chain-integration detail of balance reads, indexers, or snapshot generation
- allow products to keep hidden wallet identity systems outside the shared platform model
- imply that one preferred wallet always determines every participation or payout outcome
- let wallet-aware context silently redefine token, credits, payout, registry, or reporting truth
- let public registry outputs override private canonical wallet-link state

---

## Core Principles

### 1. Wallet-Aware Account Principle
A wallet is a linked participation artifact associated with a canonical FUZE account, while canonical token ownership truth remains on-chain and canonical person-level identity truth remains in the account system.

### 2. Account-First Principle
The account remains the canonical actor anchor. Wallet links attach participation context to that anchor but do not replace it.

### 3. Chain-Truth Separation Principle
Wallet-link records do not own token balances, token transfers, or contract truth. Ethereum remains canonical for Ethereum token state. Public registries and snapshot outputs remain downstream interpretations.

### 4. Multi-Wallet Principle
One account may have multiple linked wallets, and the platform must not collapse all meaning into the first linked wallet forever.

### 5. Proof-Based Link Principle
A wallet link must be based on acceptable proof of control in the context of an authenticated canonical account or another explicitly approved secure flow.

### 6. Derived Holder Context Principle
Holder-aware, token-aware, and eligibility-aware context is derived from active wallet links, chain truth, and policy. It is not an independent absolute truth source.

### 7. Authorization Separation Principle
Wallet-aware context may influence selected product visibility or ecosystem privileges, but it must not replace workspace membership, role or permission truth, support or admin authority, or general platform authorization.

### 8. Explicit Conflict Principle
The platform must not silently overwrite wallet mappings, silently merge accounts, or silently reassign contested wallets.

### 9. Publication Separation Principle
Public registry, transparency, and reporting outputs may publish wallet-related representations, but they remain downstream publication artifacts and must not become the owner of wallet-link truth.

---

## Canonical Definitions

### Account
The canonical FUZE platform identity record.

### Wallet
A blockchain address or wallet-controlled address that may be linked to a FUZE account for participation-aware behavior.

### Wallet Link
A platform-owned record representing the relationship between a canonical account and a wallet address.

### Wallet Ownership Proof
The verification event or approved mechanism proving that the actor controlling the canonical account also controlled the wallet at the time of linking.

### Preferred Wallet
A wallet designated by user choice or policy as the preferred wallet for selected display or non-critical product behavior where applicable.

### Secondary Wallet
Any additional linked wallet associated with the same account.

### Holder-Aware Context
Derived platform context based on wallet linkage and token-related state, such as holder tier, participation rank, or wallet-linked product visibility.

### Token-Aware Context
Derived product- or platform-facing context built using wallet links plus on-chain token state, without becoming canonical token truth.

### Eligibility Context
Derived state used in payout, privilege, or policy decisions based on wallet-link records, relevant chain truth, and cycle-specific rules.

### Wallet Conflict
A case in which one wallet appears to be claimed by multiple accounts, appears misbound, or requires controlled reassignment or security review.

### Wallet Invalidation
A state in which a wallet link’s original trust basis is no longer acceptable and the link cannot continue as ordinary active wallet-aware context.

---

## Truth Class Taxonomy

This domain MUST distinguish the following truth classes:

### 1. Canonical Identity Truth
`account` and identity-domain lifecycle semantics remain canonical identity truth and are not redefined here.

### 2. Wallet-Link Truth
`wallet_link` and its lifecycle state are canonical wallet-aware domain truth for account-to-wallet association.

### 3. Provider-Input or Proof-Input Truth
Wallet signatures, challenge responses, provider claims, and external chain observations are evidence inputs. They are not by themselves canonical wallet-link truth until accepted by the wallet-aware owner domain.

### 4. Chain Truth
On-chain balances, transfers, and contract state remain chain truth. They are external to wallet-link storage truth even when consumed by FUZE.

### 5. Policy Truth
Wallet-link approval policy, proof-acceptance policy, conflict-remediation policy, eligibility policy, and public-publication policy are policy truth. Policy constrains wallet-aware behavior but is not itself the wallet-link record.

### 6. Runtime Truth
Linking-session state, challenge lifecycle, replay windows, and current request posture are runtime truth. Runtime truth is subordinate to canonical identity truth, wallet-link truth, and policy truth.

### 7. Eligibility Truth
Cycle-specific eligibility datasets, snapshot outcomes, and payout-preparation datasets are separate downstream truths. They may consume wallet-link truth and chain truth but do not own wallet-link truth.

### 8. Public Registry or Publication Truth
Public wallet registries, public profile association surfaces, transparency reports, and public exports are downstream publication truth. They may represent wallet-aware state but remain derived and correctable from canonical owner domains.

### 9. Derived Read-Model Truth
Support views, product summaries, holder-rank caches, analytics views, and search projections are derived views. They MUST be regenerable and MUST NOT become write owners.

### 10. Authorization Truth
Workspace scope, role assignment, permission evaluation, and entitlement grant are separate truths owned by adjacent domains. Wallet-aware context may be an input, but it is not the owner.

---

## Architectural Position in the Spec Hierarchy

This document sits below:

- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`

and above:

- `WALLET_AWARE_USER_API_SPEC.md`
- `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `ETHEREUM_TOKEN_LAYER_SPEC.md`
- `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`
- product integration specifications

This document defines the wallet-aware user domain. It does not absorb canonical account identity, auth/session issuance, role/permission evaluation, token contract truth, payout execution truth, or public registry ownership.

---

## System Boundaries

The wallet-aware user domain MUST own:

- wallet-link records
- wallet-link lifecycle state
- wallet proof acceptance outcomes
- account-to-wallet mapping
- preferred-wallet metadata where approved
- wallet conflict and remediation posture at the wallet-link layer
- wallet-aware derived context read models owned by the wallet-aware domain
- wallet-aware audit lineage generation in coordination with the audit domain
- wallet-aware domain events after canonical commits

The wallet-aware user domain MUST NOT own:

- canonical account identity truth
- session issuance or runtime session truth
- workspace membership truth
- role, permission, or entitlement truth
- Ethereum token balances or contract state
- payout-ledger truth or payout-execution truth
- public registry publication ownership
- product-local wallet convenience tables
- transparency-reporting ownership

---

## Adjacent Boundaries

### Identity and Account
`IDENTITY_AND_ACCOUNT_SPEC.md` governs canonical account identity. This document governs wallet-aware participation attached to that account. Wallet linkage must never replace `account_id` as the actor anchor.

### Authentication and Session
`AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md` governs authentication methods, linked login, and sessions. Wallet links are not ordinary linked login methods unless separately approved. Wallet linkage therefore does not automatically imply auth or session authority.

### Access Continuity and Recovery
`FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md` and `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md` govern same-account continuity, recovery, and conservative remediation. Wallet corrections or contested-wallet cases must preserve the same continuity posture and explicit-review bias.

### Workspace and Authorization
`WORKSPACE_AND_ORGANIZATION_SPEC.md` and `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md` govern collaborative scope and authorization. Wallet-aware context may enrich experience or eligibility inputs, but it cannot replace those domains.

### On-Chain / Off-Chain Responsibility
`ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md` governs which truths belong on-chain and which remain off-chain. Wallet-link records are off-chain platform truth; token balances remain on-chain truth; eligibility and reporting remain downstream derived truth.

### Public Registry and Transparency
Public registry and transparency surfaces consume wallet-aware outputs through approved publication boundaries. They do not become the owner of the underlying wallet link.

---

## Conflict Resolution Rules

When multiple layers disagree, the platform MUST resolve wallet-aware conflicts in the following order unless a higher-order platform policy explicitly states otherwise:

1. canonical identity-domain records for `account_id`
2. canonical wallet-aware domain records for wallet-link ownership and lifecycle
3. explicit policy and security constraints
4. accepted proof or validated external evidence within approved resolution rules
5. chain observations used as supporting evidence
6. eligibility datasets, public registries, reports, dashboards, or product-local caches

Specific conflict rules:

- chain balance presence MUST NOT by itself create or move a wallet link
- public registry display MUST NOT override canonical wallet-link ownership
- wallet presence MUST NOT override workspace membership or authorization truth
- product-local wallet tables MUST NOT override platform wallet-link records
- support or operator displays MUST NOT be treated as authoritative if they diverge from canonical wallet-link records
- snapshot or payout artifacts MUST NOT retroactively rewrite wallet-link history

---

## Default Decision Rules

Where ambiguity exists, the following defaults apply:

1. default actor anchor: `account_id`
2. default owner of canonical wallet-link semantics: Wallet-Aware User Domain
3. default interpretation of wallet proof: evidence for link creation or mutation, not automatic durable truth until accepted
4. default interpretation of chain observation: supporting evidence, not canonical account mapping
5. default interpretation of preferred wallet: convenience and policy metadata, not exclusive ownership meaning
6. default interpretation of eligibility output: downstream derived cycle truth, not wallet-link ownership truth
7. default interpretation of public registry output: publication artifact, not canonical source-of-record
8. default resolution for ambiguous contested wallet: explicit conflict or review posture, not silent reassignment
9. default resolution for high-risk operator action: require reason code, audit, policy reference, and bounded workflow
10. default resolution under degraded conditions: fail closed on sensitive wallet-link mutation if safe ownership cannot be determined

---

## Roles / Actors / Entities

### End User
The canonical account holder linking, unlinking, reviewing, or consuming wallet-aware context.

### Wallet-Aware User Domain
The canonical owner of wallet-link truth, wallet lifecycle, proof acceptance, wallet conflict state, and wallet-aware derived context.

### Identity Domain
The owner of canonical account identity and actor anchoring.

### Auth / Session Domain
The owner of authentication and runtime session trust required to initiate ordinary wallet-link mutation flows.

### Security / Risk Function
The owner of heightened review posture, compromise handling, suspicious-link investigations, and stronger evidence requirements.

### Support / Operations Function
May assist with policy-bound reviewable wallet correction or containment actions. Support does not become the owner of canonical wallet-link truth.

### Snapshot / Eligibility Systems
Downstream consumers that combine wallet-link truth and chain truth to produce cycle-specific eligibility or participation outputs.

### Public Registry / Transparency Systems
Downstream publication consumers that present approved wallet-related outputs without owning the source wallet-link record.

### Product Domains
Consumers of wallet-aware outputs for product behavior. They may not own cross-product wallet-link truth.

### Canonical Entities
- `wallet_link`
- `wallet_verification_challenge`
- `wallet_conflict_case`
- `wallet_mutation_request`
- `wallet_audit_lineage_reference` or equivalent
- approved wallet-aware domain events and derived wallet-aware summary models

---

## Ownership Model

### Canonical Owner
The canonical owner of wallet-aware account-to-wallet association is the Wallet-Aware User Domain.

### Domain-Owned Truth Families
The wallet-aware domain owns:

- `wallet_link`
- wallet-link lifecycle state
- proof-acceptance outcomes
- preferred-wallet metadata where approved
- wallet conflict and wallet-reassignment case posture at the wallet-aware layer
- wallet-aware read models owned by the wallet-aware domain

### Non-Owned Adjacent Truth Families
The wallet-aware domain does not own:

- `account`
- `auth_session`
- workspace membership records
- role and permission records
- token balances
- commercial ledgers
- payout records
- public registry publication records
- reporting exports

### Mutation Ownership
Only owner-controlled domain pathways may mutate canonical wallet-link truth. Products, frontends, reports, caches, search indexes, eligibility datasets, registry publishers, and general-purpose support tooling MUST NOT mutate wallet-link truth directly.

---

## Authority / Decision Model

### Wallet-Aware Domain Authority
The wallet-aware domain MAY:

- create wallet-link records
- transition wallet-link lifecycle state
- accept or reject wallet proofs under approved policy
- set or clear preferred-wallet metadata where approved
- initiate or resolve wallet conflict states through approved workflows
- expose wallet-aware reads
- publish wallet-aware events after canonical commits

### Product Authority Limits
Products MAY:

- read wallet-aware context
- initiate approved wallet-link requests
- display holder-aware UX
- request token-aware behavior through approved platform interfaces

Products MUST NOT:

- create hidden cross-product wallet-link records
- reinterpret chain observation as canonical platform linkage
- override wallet-conflict decisions
- treat product-local wallet state as canonical platform mapping

### Operator Authority Limits
Operator and support systems MAY trigger reviewable wallet actions only through bounded control-plane pathways. They MUST NOT bypass domain validation, proof policy, reason-code capture, audit, policy-reference capture, or idempotency requirements.

---

## State Model

### Canonical Wallet Link States
At minimum, the platform MUST support the semantic states:

- `pending_verification`
- `active`
- `restricted`
- `unlinked`
- `superseded`
- `invalidated`
- `blocked_conflict`

Only `active` wallet links may serve as ordinary wallet-aware participation context.

### Challenge or Proof States
Wallet verification challenge records SHOULD support states such as:

- `issued`
- `completed`
- `expired`
- `failed`
- `cancelled`

### Conflict or Review States
Wallet conflict and review posture MUST be represented explicitly through durable owner-controlled state rather than hidden support notes, spreadsheets, or transient client flags.

---

## Lifecycle / Workflow Model

### Wallet Link Request Flow
1. actor is resolved to a canonical authenticated account or another explicitly approved secure flow
2. wallet-link request is created
3. wallet-proof challenge or equivalent approved mechanism is issued
4. proof is validated and replay or uniqueness checks are applied
5. collision, policy, and risk checks are performed
6. if accepted, canonical wallet-link commit occurs
7. wallet-aware domain events and derived summary refresh occur
8. downstream eligibility, reporting, registry, and product consumers update from owner-approved outputs

### Wallet Unlink Flow
1. actor requests unlink through an approved boundary
2. authorization, recent-auth, policy, and continuity impacts are evaluated
3. if no blocking review condition exists, wallet-link state transitions to `unlinked` or another approved terminal state
4. historical lineage is preserved
5. downstream consumers receive updated wallet-aware outputs

### Wallet Correction or Reassignment Flow
If a wallet appears misbound, duplicated, or contested:

1. explicit conflict case is opened
2. ordinary self-service mutation is blocked where required
3. stronger evidence, support review, or operator review is required
4. canonical state transitions are performed only through approved remediation pathways
5. history remains attributable and auditable
6. downstream publication and eligibility layers are refreshed without rewriting prior justified cycle truth unless a governing downstream policy explicitly requires correction

### Historical Continuity Flow
When preferred wallet changes or legacy wallets remain relevant for continuity, the system preserves historical lineage rather than destructively overwriting ownership history.

---

## Invariants

1. `account_id` remains the durable actor anchor
2. wallet links attach to accounts; accounts do not derive identity from wallet links
3. one active wallet address MUST NOT belong to more than one canonical account at the same time unless a controlled remediation posture explicitly represents the conflict
4. chain truth remains external to wallet-link storage truth
5. wallet-aware context MAY influence product behavior but MUST NOT replace workspace, role, permission, or entitlement truth
6. public registry and reporting surfaces MUST remain derived and correctable from owner-domain truth
7. wallet-link lifecycle transitions MUST be durable and auditable
8. replayable or duplicated proof inputs MUST have deterministic idempotent handling
9. products and reports MUST NOT become wallet-link write owners
10. degraded runtime conditions MUST NOT justify bypassing proof, uniqueness, or audit requirements

---

## Functional Rules

### Canonical Mapping Rule
The platform MUST use `account_id` as the only durable internal anchor for wallet-aware participation continuity.

### Link Creation Rule
A wallet link may be created only when:

- the actor is authenticated as the canonical account or another explicitly approved secure flow exists
- the platform receives acceptable wallet-ownership proof
- the target wallet is not already actively linked to another account unless a controlled review or remediation path is underway
- policy checks and risk checks pass

### Proof Acceptance Rule
The platform MUST NOT treat passive chain observation, public social claims, or product-local assertions as sufficient proof of wallet-link ownership.

### Unlink Safety Rule
Unlinking MAY be blocked or restricted when the wallet is involved in an unresolved conflict, security review, recovery-sensitive case, or other trust-sensitive process where removal would create ambiguity or safety loss.

### Preferred Wallet Rule
Preferred-wallet designation MAY support display and selected non-critical product behavior, but it MUST NOT be treated as exclusive ownership meaning or as a universal payout or eligibility determinant unless an explicit downstream policy says so.

### Authorization Boundary Rule
Wallet-aware context MUST NOT automatically grant workspace ownership, billing authority, support or admin access, governance-sensitive operations, or unrestricted product control.

### Registry Publication Rule
Public wallet-registry outputs MAY represent wallet-aware association only through downstream publication pathways and MUST remain explicitly derived from owner-domain and policy-approved inputs.

### Historical Cycle Rule
Changes to wallet-link state SHOULD affect future wallet-aware context. They MUST NOT silently rewrite historical snapshot, payout, or registry artifacts whose governing downstream policy requires historical stability, except through an explicit correction pathway owned by the appropriate downstream domain.

---

## Permission / Access Considerations

This specification does not replace the authorization model, but it imposes the following rules:

- successful wallet linking does not prove workspace membership
- successful wallet linking does not prove role or permission grants
- successful wallet linking does not prove billing authority
- successful wallet linking does not prove entitlement unless an entitlement domain explicitly uses wallet-aware inputs under its own rules
- wallet-aware admin and control actions require stronger authorization and audit posture than ordinary user actions
- internal service access to wallet mutation paths must be explicit, least-privileged, and policy-scoped

---

## Entitlement Considerations

Entitlements remain downstream of wallet-aware truth.

- entitlement systems MAY consume canonical account identity, workspace context, and wallet-aware context
- entitlement systems MUST NOT redefine wallet-link ownership
- wallet correction or recovery MUST preserve subject continuity and explicit correction lineage
- product capability gating that uses wallet-aware context remains downstream and reversible from canonical owner truth

---

## API / Contract Implications

Downstream API specifications MUST preserve the following:

- identity APIs own canonical account truth
- session and linked-login APIs own authentication and session truth
- wallet-aware APIs own wallet-link truth and wallet verification outcomes
- authorization APIs own workspace, role, permission, and capability evaluation
- eligibility APIs own snapshot and cycle-specific eligibility computation
- registry and transparency APIs own publication or export logic, not wallet-link ownership

Sensitive wallet-aware APIs MUST require, where relevant:

- explicit actor identity
- correlation or trace IDs
- idempotency for side-effecting mutations vulnerable to replay
- reason codes for privileged mutations
- auditable state transitions
- policy-version or policy-reference capture where applicable

No API surface may blur these boundaries.

---

## Event / Async Implications

The wallet-aware domain SHOULD publish durable events after canonical commits for material actions such as:

- wallet link requested
- wallet challenge issued
- wallet proof completed or failed
- wallet link activated
- wallet link restricted
- wallet link unlinked
- preferred wallet changed
- wallet conflict opened, escalated, resolved, or cancelled
- wallet invalidated
- wallet reassignment completed through approved remediation

Async wallet-aware workflows MUST remain deterministic, auditable, idempotent, and replay-safe.

Event consumers MUST treat these events as downstream notifications, not as permission to redefine canonical wallet-link truth.

---

## Data Model / Storage Implications

This specification requires the following data-model discipline:

- `wallet_link` MUST remain a durable source-of-truth entity
- `wallet_verification_challenge` MUST remain a durable short-lived entity rather than transient frontend-only state
- `wallet_conflict_case` or equivalent remediation entity MUST remain explicit and durable
- preferred-wallet metadata MUST remain attached to wallet-link truth rather than product-local settings when platform-owned
- chain balances and token events remain external or separately owned data, not fields masquerading as wallet-link source truth
- eligibility outputs, registry outputs, and reporting exports are derived, not canonical
- wallet-aware corrections SHOULD prefer explicit lineage and state transitions over hidden destructive rewrites

Representative semantic fields include:

### wallet_link
- `wallet_link_id`
- `account_id`
- `chain_identifier`
- `wallet_address`
- `status`
- `proof_method_type`
- `proof_verified_at`
- `linked_at`
- `updated_at`
- `preferred_flag` where approved
- `restriction_reason_code` where applicable
- audit and trace references

### wallet_verification_challenge
- `wallet_challenge_id`
- `account_id`
- `chain_identifier`
- `wallet_address`
- `challenge_state`
- `issued_at`
- `expires_at`
- `completed_at`
- correlation reference
- proof metadata reference

### wallet_conflict_case
- `wallet_conflict_case_id`
- `wallet_address`
- `chain_identifier`
- candidate account references
- conflict reason code
- current case state
- `opened_at`
- `resolved_at`
- resolution policy reference
- audit and trace references

---

## Read Model / Projection / Reporting Rules

Read models, support views, analytics views, exports, dashboards, search projections, holder-rank caches, and publication tables MAY summarize wallet-aware state. They are allowed only if they preserve the following rules:

- they must be clearly marked as derived
- they must be regenerable from canonical truth
- they must not become mutation owners
- they must not invent wallet-link states that cannot map back to canonical semantics
- they must not silently coalesce multiple accounts into one wallet identity without explicit governed rules
- they must show provenance or source lineage where ambiguity is material
- public-facing projections must pass through approved publication policy when disclosure is sensitive

---

## Security / Risk / Abuse Controls

Wallet linkage is security-sensitive even though it is not identical to account authentication.

The platform MUST support:

- proof-based wallet linking
- replay-aware or otherwise policy-safe proof handling
- collision detection for wallet reuse
- unlink safeguards
- stronger recent-auth or equivalent confirmation for sensitive wallet mutations where policy requires
- audit trails for wallet changes
- support and operator review pathways for sensitive cases
- rate limiting, abuse detection, and suspicious-link monitoring
- containment or restriction posture when takeover or misbinding risk exists

The platform MUST NOT over-trust wallet linkage as a universal authority signal. Wallet linkage is one layer of participation context, not the entire access model.

---

## Boundary Violation Detection / Non-Canonical Patterns

The following patterns are non-canonical and forbidden unless a narrower governing spec explicitly authorizes a bounded exception:

- a product creates its own cross-product wallet table and treats it as the source-of-record
- chain balance reads directly mutate canonical wallet-link ownership
- public registry outputs are treated as the source-of-record for wallet linkage
- payout or snapshot datasets are used to rewrite wallet-link history without an owner-domain correction flow
- support operators directly edit wallet-link ownership in storage without reason code, policy reference, and durable audit lineage
- a wallet is treated as universal authorization proof for workspace, billing, admin, or governance-sensitive actions
- frontend-only linking state is treated as authoritative without canonical backend commit

---

## Audit / Traceability Requirements

Every material wallet-aware mutation MUST produce durable lineage sufficient to answer:

- who initiated the action
- which canonical account was affected
- which wallet and chain were involved
- what proof or evidence basis was accepted
- what policy version or rule set was used
- what prior and resulting states were
- whether a conflict, review, or operator override occurred
- which trace or correlation identifiers tie the workflow together

Privileged wallet corrections, restrictions, reassignments, or invalidations MUST be reason-coded, attributable, reviewable, and policy-constrained.

---

## Failure Handling / Edge Cases

The platform MUST define deterministic behavior for at least the following cases:

### Duplicate Proof Submission
Repeated proof completion for the same pending request must resolve idempotently rather than creating duplicate active wallet links.

### Wallet Already Linked Elsewhere
Ordinary self-service mutation must fail closed into conflict or review posture rather than silently reassigning the wallet.

### Proof Expired
Expired proof must not activate the wallet link and must require a new approved challenge or request.

### Degraded Chain Observation
Loss of chain-indexing freshness must not invalidate canonical wallet-link truth by itself. It may degrade derived holder-aware context and must be labeled accordingly.

### Historical Correction Need
If a prior wallet link is later shown to have been misbound, correction must preserve explicit case lineage, avoid silent destructive rewrites, and respect downstream historical-cycle correction policy.

### Last Wallet Removed
Removing the last wallet is allowed unless a governing downstream policy requires an active wallet for a pending trust-sensitive workflow. In that case, the unlink path may be blocked or deferred explicitly.

### Multi-Wallet Ambiguity
If downstream product logic assumes one wallet while multiple active wallets exist, the product must defer to approved preferred-wallet policy or an explicit downstream rule rather than inventing hidden tie-breakers.

---

## Operational Considerations

Operational implementations SHOULD provide:

- clear owner-domain APIs for wallet-link lifecycle
- observability for challenge issuance, proof completion, activation, collision, conflict, and operator intervention
- alerting for suspicious wallet-link mutation patterns
- replay and retry instrumentation
- safe backfill and rebuild procedures for derived wallet-aware read models
- explicit degraded-mode indicators when chain-derived context is stale or unavailable
- bounded operator tooling that surfaces provenance, case state, and policy references

---

## Migration / Compatibility / Supersession Considerations

- migrations MUST NOT silently transfer wallet-aware ownership out of the wallet-aware domain
- compatibility layers MAY preserve older product behavior temporarily, but canonical wallet-link truth must remain platform-owned
- renamed or split wallet-aware entities MUST preserve continuity of authority and auditability
- if older implementations or documents imply looser product-local wallet ownership, weaker derived-state boundaries, or publication-owned wallet truth, this refined specification supersedes them within its scope
- future wallet-auth or multi-chain expansion MUST occur through the shared identity/access and wallet-aware model rather than product-local exceptions

---

## Implementation-Contract Guardrails

Downstream implementation-contract layers MUST preserve the following:

1. `account_id` remains the durable actor anchor
2. `wallet_link` remains the canonical wallet-aware source-of-truth entity
3. chain truth remains external to wallet-link storage truth
4. eligibility, payout, public registry, and reporting outputs remain distinguishable from wallet-link truth
5. idempotent handling exists for proof callbacks, retries, duplicate jobs, and replayable external input
6. sensitive operator actions require reason codes, policy references, and trace identifiers
7. blocked or ambiguous mutations produce deterministic outcomes
8. stale derived context is labeled rather than silently treated as current canonical truth
9. products and reports consume owner-domain APIs and events instead of direct convenience-table writes
10. degraded runtime conditions do not justify bypassing proof, uniqueness, or audit requirements

---

## Downstream Execution Staging

The expected downstream staging model is:

1. canonical account context is resolved upstream
2. wallet-link request and proof flow is handled by wallet-aware APIs and services
3. canonical wallet-link commit is completed by the wallet-aware owner domain
4. downstream derived summary refresh and event publication occur
5. eligibility, reporting, public registry, and product consumers update from owner-approved inputs
6. any payout, transparency, or public artifact uses the appropriate downstream owner dataset rather than reinterpreting wallet-link truth locally

---

## Required Downstream Specs / Contract Layers

This specification requires compatible downstream refinement in at least the following areas:

- `WALLET_AWARE_USER_API_SPEC.md`
- `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `ETHEREUM_TOKEN_LAYER_SPEC.md`
- `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- product integration specifications
- public transparency and reporting specifications

---

## Canonical Examples / Anti-Examples

### Canonical Example 1
A user signs into FUZE through an approved auth method, links an Ethereum wallet through an approved proof flow, and the platform records an active wallet link under the same `account_id`. This is canonical.

### Canonical Example 2
A user links multiple wallets. One is marked preferred for profile display, while eligibility for a given cycle is computed by a downstream snapshot dataset using all active approved wallets. This is canonical.

### Canonical Example 3
A public registry displays a wallet as associated with a public-facing profile or ecosystem surface, but the registry remains a derived publication artifact and does not own wallet-link truth. This is canonical.

### Anti-Example 1
A product creates its own wallet table and treats that table as the source-of-record for cross-product wallet linkage. This is forbidden.

### Anti-Example 2
A chain balance read is used to grant admin rights without downstream authorization and policy evaluation. This is forbidden.

### Anti-Example 3
A support operator reassigns a contested wallet to a different account by direct table edit without reason code, policy reference, and durable audit lineage. This is forbidden.

### Anti-Example 4
A payout report or snapshot artifact is used to retroactively rewrite canonical wallet-link history. This is forbidden.

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
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
- `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
- `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`

This specification directly governs or materially informs:

- `WALLET_AWARE_USER_API_SPEC.md`
- `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `ETHEREUM_TOKEN_LAYER_SPEC.md`
- `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- product integration specifications
- public transparency and reporting specifications

---

## Explicitly Deferred Items

The following are intentionally deferred to adjacent or downstream specifications:

- exact cryptographic challenge formats and wallet-signature payload structure
- exact chain-indexer architecture and freshness thresholds
- exact snapshot-cycle formulas and exclusion lists
- exact public-publication schema and disclosure policy implementation detail
- exact payout-claim routing and stablecoin execution detail
- exact UI journeys for wallet linking, unlinking, or conflict review
- exact operator staffing and approval-chain workflow procedures
- exact multi-chain rollout sequence if additional chains are later approved

These do not weaken the canonical wallet-aware model established here.

---

## Final Normative Summary

FUZE uses an account-rooted wallet-aware model in which wallet links attach participation context to a canonical account without replacing canonical identity, chain truth, authorization truth, or public/publication truth. The wallet-aware domain owns wallet-link records, proof acceptance, lifecycle state, and wallet-aware read models. Ethereum remains the owner of Ethereum token state. Snapshot and eligibility systems own cycle-specific eligibility outputs. Public registry and transparency systems own publication outputs. Products consume wallet-aware context but must not create parallel wallet identity systems.

This separation is what allows FUZE to bridge the SaaS side and the Web3 side coherently. It preserves cross-product identity continuity, safe wallet-link mutation, conservative conflict handling, clean on-chain/off-chain ownership boundaries, and reliable downstream implementation-contract work. Any downstream design that weakens these rules is inconsistent with the FUZE wallet-aware foundation.

---

## Quality Gate Checklist

- Canonical owner is explicit for wallet-link truth, chain truth, eligibility truth, and publication truth.
- Mutation boundaries are explicit.
- Adjacent boundaries are explicit.
- Truth classes are explicit.
- Conflict-resolution rules are explicit.
- Default decision rules are explicit.
- Non-canonical patterns are called out.
- Operator override paths are bounded and auditable.
- Read-model, cache, reporting, and publication rules are explicit.
- On-chain versus off-chain responsibilities are explicit.
- Failure and degraded-mode behaviors are explicit.
- Downstream implementation guardrails are explicit.
- Dependencies and downstream impacts are explicit.
- Non-goals and deferred items are explicit.
- The document is strong enough to guide backend, API, data, runtime, reporting, and governance-aligned downstream implementation without inventing contradictory semantics.
