# WALLET_AWARE_USER_SPEC

## Document Metadata

- Document Name: `WALLET_AWARE_USER_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Governing Layer: Platform core / wallet-aware user and participation context
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, security, support operations, audit, governance, API design, reporting, chain-adjacent services
- Primary Purpose: Define the canonical FUZE wallet-aware user model, including how canonical accounts relate to wallets, how wallet links are verified and governed, how holder-aware and eligibility-aware context is derived, and how products consume wallet-aware context without redefining account identity, token truth, authorization, credits, or payout semantics
- Primary Upstream References:
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
  - `SYSTEM_SPEC_INDEX.md`
  - `API_SPEC_INDEX.md`
  - `DOCS_SPEC_INDEX.md`
- Primary Downstream Dependents:
  - `WALLET_AWARE_USER_API_SPEC.md`
  - `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
  - `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
  - `ETHEREUM_TOKEN_LAYER_SPEC.md`
  - `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
  - `WORKSPACE_AND_ORGANIZATION_SPEC.md`
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - product integration specifications

---

## Purpose

This specification defines the canonical wallet-aware user architecture of the FUZE platform.

Its purpose is to establish:

- how a canonical FUZE account relates to one or more wallets
- how wallet links are represented, verified, and lifecycle-controlled
- how wallet-aware, holder-aware, token-aware, and eligibility-aware platform behavior is derived
- how wallet-aware context interacts with identity, sessions, workspaces, authorization, product capabilities, reporting, and participation logic
- how wallet-linked behavior must remain distinct from token truth, credits truth, payout truth, and governance truth
- how products and reporting surfaces may consume wallet-aware context without inventing product-local wallet identity systems
- how conflict handling, reassignment, unlinking, invalidation, and support/admin corrections must preserve trust and auditability

This document exists because FUZE is both a SaaS platform ecosystem and a Web3-aware participation environment. The platform therefore must connect canonical account identity to on-chain participation context in a way that is structurally clear, security-aware, and consistent with the broader FUZE separation among identity, authentication, wallet linkage, token balances, Platform Credits, and profit-participation payout logic.

---

## Scope

This specification governs:

- the distinction between account identity and wallet-linked participation context
- the canonical wallet-link model for FUZE accounts
- multi-wallet support and wallet preference handling
- wallet-link lifecycle states and mutation boundaries
- wallet ownership proof requirements
- holder-aware and token-aware derived context
- wallet use in selected privileges, ecosystem recognition, product behavior, and eligibility-related systems
- ownership boundaries between wallet-link records and canonical chain truth
- wallet-related security, audit, conflict, and review rules
- wallet-aware behavior across products and workspaces
- wallet-aware data model requirements
- edge cases, correction posture, and failure-handling principles

This specification does not redefine:

- canonical account identity semantics
- auth/session mechanics
- workspace lifecycle semantics
- role and permission truth
- Ethereum token balance truth
- Platform Credits semantics
- payout execution contract semantics
- detailed snapshot and eligibility algorithm rules

Those are refined in downstream specifications.

---

## Out of Scope

This specification is explicitly out of scope for:

- exact technical wallet-signature or proof method details
- exact chain-indexing implementation
- exact snapshot proof format
- exact public registry publication schema
- exact payout-claim mapping mechanics
- exact support tooling UI
- exact multi-chain expansion detail beyond currently approved scope
- product-specific token-benefit rules beyond platform wallet-aware inputs

---

## Design Goals

The design goals of the FUZE wallet-aware user model are:

1. let one canonical FUZE account connect to zero, one, or many wallets in a controlled way
2. preserve the distinction between platform identity and on-chain ownership
3. support holder-aware and token-aware product experience across FUZE
4. support Ethereum-based eligibility and participation logic without making wallets the sole identity model
5. keep wallet-link state auditable, secure, correction-capable, and conflict-safe
6. support future products and future participation logic without redefining the wallet model
7. make products consume wallet-aware context through shared platform rules rather than ad hoc chain assumptions
8. preserve clear separation among wallet linkage, token truth, authorization, entitlements, credits, and payout meaning
9. support historical continuity when wallet configurations change over time
10. keep the platform explainable to users, operators, auditors, and downstream systems

---

## Non-Goals

This specification is not intended to:

- make wallet addresses the canonical user identity of FUZE
- treat token balance reads as substitutes for platform authorization
- define arbitrary wallet-based admin or governance privileges outside approved policy
- make every product require a wallet link
- collapse wallet linkage into auth/session behavior
- define every chain-integration detail of balance reads or snapshot generation
- allow products to keep hidden wallet identity systems outside the shared platform model
- imply that one “primary wallet” always determines all participation or payout outcomes
- let wallet-aware context silently redefine token, credits, payout, or reporting truth

---

## Core Principles

### 1. Wallet-Aware Account Principle
A wallet is a linked participation artifact associated with a canonical FUZE account, while canonical token ownership truth remains on-chain and platform identity truth remains in the account system.

### 2. Account-First Principle
The account remains the canonical user identity. Wallet links attach participation context to that identity but do not replace it.

### 3. Chain-Truth Separation Principle
Wallet-link tables do not own token balances, token transfers, or contract truth. Ethereum remains canonical for token state.

### 4. Multi-Wallet Principle
One account may have multiple linked wallets, and the platform must not collapse all meaning into the first linked wallet forever.

### 5. Proof-Based Link Principle
A wallet link must be based on acceptable proof of control in the context of the canonical authenticated account.

### 6. Holder-Context-Is-Derived Principle
Holder-aware context is derived from active wallet links, chain truth, and policy. It is not an independent absolute truth source.

### 7. Authorization Separation Principle
Wallet-aware context may influence selected product visibility or ecosystem privileges, but it must not replace workspace membership, role/permission, support/admin authority, or general platform authorization.

### 8. Explicit Conflict Principle
The platform must not silently overwrite wallet ownership mappings, silently merge accounts, or silently reassign contested wallets.

---

## Canonical Definitions

### Account
The canonical FUZE platform identity.

### Wallet
A blockchain address or wallet-controlled address that may be linked to a FUZE account for participation-aware platform behavior.

### Wallet Link
A platform record representing the relationship between a canonical account and a wallet address.

### Wallet Ownership Proof
The verification event or mechanism proving that the actor controlling the canonical account also controlled the wallet at the time of linking.

### Primary Wallet
A wallet designated by platform policy or user choice as the preferred wallet for selected display or selected non-critical policy use where applicable.

### Secondary Wallet
Any additional linked wallet associated with the same account.

### Holder-Aware Context
Derived platform context based on wallet linkage and token-related state, such as holder rank, participation tier, or feature eligibility.

### Token-Aware Context
Derived product- or platform-facing context built using wallet links plus on-chain token state, without becoming canonical token truth.

### Eligibility Context
Derived state used in payout, privilege, or policy decisions based on wallet-link records, relevant chain truth, and cycle-specific rules.

### Wallet Conflict
A case in which one wallet appears to be claimed by multiple accounts, appears misbound, or requires controlled reassignment or security review.

### Wallet Invalidation
A terminal or near-terminal state in which a wallet link’s original trust basis is no longer acceptable.

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
- product integration specifications

This document defines the wallet-aware user domain. It does not absorb canonical account identity, auth/session issuance, role/permission evaluation, token contract truth, or payout execution truth.

---

## Canonical Wallet Model

FUZE must use a **wallet-aware account model**, not a wallet-only identity model.

Under this model:

- one account may have zero, one, or multiple linked wallets
- one active wallet address may point to one canonical account at a time unless a formal controlled migration process exists
- wallet linking is a controlled and auditable action
- wallet balances and token events are not stored as primary truth in the wallet-link table
- wallet-aware context is derived from the combination of wallet linkage and on-chain reads or snapshot pipelines

This structure allows FUZE to remain both SaaS-native and Web3-aware without weakening either side.

---

## Why Wallets Are Separate from Accounts

FUZE keeps wallets separate from accounts because they solve different problems.

### Accounts solve:
- who the user is in the platform
- how the user accesses products
- what workspaces the user belongs to
- what commercial and audit history follows the user
- how linked login methods and product usage persist over time

### Wallets solve:
- what on-chain addresses the account has proven control over
- what token-aware participation context may apply
- what Ethereum-based balances or snapshots may be relevant
- what holder-based privileges or payout eligibility may be derived

If FUZE collapsed accounts and wallets into one concept, it would weaken platform continuity, make non-wallet product access harder, and create ambiguity when wallets change but the platform account should remain the same.

For these reasons, wallets are linked participation artifacts, not the core identity system.

---

## Domain Ownership and Authority

The canonical owner of wallet-aware linkage and wallet-aware context is the **Wallet-Aware User Domain**.

This domain owns:

- wallet-link records
- wallet-link lifecycle state
- wallet ownership proof acceptance state
- preferred-wallet metadata where supported
- wallet conflict and remediation posture
- wallet-aware derived context read models
- wallet-related audit lineage generation in coordination with the audit domain
- account-to-wallet mapping used by downstream holder-aware and eligibility-aware systems

### The Wallet-Aware Domain may:
- create wallet-link records
- update wallet-link lifecycle state
- mark preferred wallet metadata
- expose wallet-aware context read models
- coordinate conflict-review flows
- publish wallet-aware events after canonical commits

### The Wallet-Aware Domain may not:
- redefine canonical account identity
- redefine session truth
- redefine workspace or role/permission truth
- redefine Ethereum token balances
- redefine Platform Credits truth
- redefine payout execution truth
- let products bypass wallet-link lifecycle, proof, or conflict rules

### Product domains may:
- read wallet-aware context
- display holder-aware UX
- request token-aware features according to platform policy
- use wallet-aware inputs in product logic where approved

### Product domains may not:
- create hidden wallet-link records outside the platform domain
- redefine the meaning of token ownership
- override wallet-link conflict decisions
- treat product-local wallet state as canonical platform linkage

### Reporting domains may:
- derive summaries and visibility layers from wallet-aware context and chain data
- support transparency and payout-related displays

### Reporting domains may not:
- redefine wallet-link truth
- redefine Ethereum token truth
- redefine eligibility pipeline truth

---

## Relationship to Identity and Authentication

Wallets are attached to accounts, not the other way around.

The identity/account model requires that `account_id` remains the durable internal anchor for cross-product identity continuity, while wallet-aware participation stays attached rather than becoming canonical identity truth.

This means:

- the account remains the person-layer identity
- linked login methods remain access paths
- sessions remain temporary runtime presence
- wallet links attach participation-aware context
- products must not treat wallet address alone as the universal identity model

Wallet-aware access providers may exist in the future only if explicitly approved, but even then they must normalize into the same canonical account model rather than replacing it.

---

## Wallet Link Lifecycle

At minimum, a wallet link should support the following lifecycle states:

- `pending_verification`
- `active`
- `restricted`
- `unlinked`
- `superseded`
- `invalidated`

### Pending Verification
A wallet-link request exists but proof of control is not yet accepted.

### Active
The wallet is successfully linked to the account and may be used for wallet-aware platform behavior.

### Restricted
The wallet remains linked in records but has temporary limits due to security review, conflict handling, policy concerns, or support review.

### Unlinked
The wallet is intentionally detached from active platform use for the account, while preserving audit history.

### Superseded
The wallet remains part of history but is no longer the preferred wallet for contexts where a primary designation or policy replacement exists.

### Invalidated
A wallet link is marked unusable because its original trust assumption is no longer acceptable, such as support-detected misbinding or a security remediation event.

Transitions must be auditable and owner-controlled.

---

## Multi-Wallet Support

FUZE must support multi-wallet linkage.

### Reasons multi-wallet support is required
- users may separate operational and long-term holding wallets
- users may rotate wallet usage over time
- users may use different wallets in different products or contexts
- holder eligibility or wallet-aware UX may require a richer model than one-address-only behavior
- the same account may need both active and legacy linked addresses for continuity or reporting

### Multi-wallet principles
1. one account may have multiple linked wallets
2. one active wallet address may not belong to more than one canonical account at the same time unless a formal controlled migration process exists
3. platform logic must not assume that the first linked wallet is the only meaningful wallet forever
4. products must consume wallet context through platform APIs or shared models rather than keeping independent wallet lists

---

## Wallet Linking Rules

Wallet linking is a sensitive account action and must be controlled accordingly.

### A wallet link may be created only when:
- the actor is authenticated as the canonical account
- the platform receives acceptable wallet-ownership proof
- the target wallet is not already actively linked to another account unless a controlled migration/review path is in progress
- policy checks and risk checks pass

### Wallet linking must produce:
- a canonical wallet-link record
- link metadata
- verification metadata
- audit events
- conflict or review state where needed

### Wallet linking must not:
- silently replace another account’s wallet link
- silently merge accounts
- silently infer wallet ownership from public chain observation alone
- bypass support or review flows in conflict cases

---

## Wallet Unlinking Rules

Wallet unlinking is also a sensitive action.

### A wallet may be unlinked when:
- the requesting actor is authorized to modify the account
- platform policy allows unlinking in the present state
- unlinking does not create policy-breaking inconsistencies for active features, billing, pending participation workflows, or other trust-sensitive processes, or those impacts are handled explicitly

### Wallet unlinking principles
- unlinking affects wallet-aware participation context, not canonical account identity
- unlinking must preserve audit history
- unlinking may require warning or explicit acknowledgment when participation status, rank visibility, or payout-related future context may be affected
- unlinking should not mutate historical payout-cycle facts that were already determined under older linked state

### Safety rule
If a wallet is currently required for an in-progress sensitive process, such as recovery review or unresolved eligibility dispute, unlinking may be temporarily restricted.

---

## Wallet Ownership Proof

A wallet link must be supported by wallet ownership proof.

The exact technical mechanism may vary by chain and wallet environment, but the semantic requirement is constant:

> the platform must have a valid basis to believe that the actor controlling the canonical account also controlled the wallet at the time of linking.

The platform must not treat passive chain observation as sufficient wallet-link proof.

### Ownership-proof requirements
- proof must be tied to the target wallet
- proof must be tied to the authenticated account context or linking session
- proof outcome must be auditable
- proof method should be replay-resistant or otherwise policy-safe

---

## Primary Wallet and Wallet Preferences

FUZE may support the concept of a **primary wallet** for user experience and selected policy use.

A primary wallet may be useful for:
- preferred display
- default holder-aware context
- preferred wallet for selected product interfaces
- selected eligibility reference where policy allows one preferred wallet in non-critical UX

However, the platform must not assume that “primary wallet” always equals:
- exclusive wallet
- only relevant wallet
- sole payout basis without explicit policy
- full identity replacement

Primary-wallet behavior is a convenience and policy construct, not a replacement for the full wallet-link model.

---

## Holder-Aware Context

Wallet-aware linkage exists so the platform can derive **holder-aware context**.

Holder-aware context may include:
- token-holder status
- participation tier or rank
- wallet-linked ecosystem privileges
- product feature visibility based on token-linked context
- payout-eligibility preparation inputs
- cross-product unlock context where approved

This context is derived, not absolute. It depends on:
- active wallet links
- token state on Ethereum
- policy-defined thresholds
- exclusion rules
- snapshot logic where required

Products may consume holder-aware context through the platform, but they must not define their own incompatible interpretations of what token ownership means.

---

## Token-Aware Context Versus Authorization

FUZE must preserve the distinction between token-aware context and authorization.

### Token-aware context may influence:
- visibility of holder benefits
- participation rank
- eligibility for selected features
- payout-related status
- ecosystem recognition or unlock logic

### Token-aware context must not automatically grant:
- workspace ownership
- billing authority
- support/admin access
- unrestricted product actions
- governance-sensitive operational control
- permissions unrelated to the published token role

The token is the ecosystem participation asset. It is not a replacement for the platform role and permission model.

---

## Relationship to Ethereum Token Truth

The wallet-aware user layer does not own token balances.

Ethereum remains the canonical source of truth for:
- FUZE token balances
- token transfer history
- wallet-address token state
- holder-level snapshot inputs

The wallet-aware layer owns:
- account-to-wallet mapping
- wallet-link lifecycle
- account-side interpretation context
- derived holder-aware state cached or materialized for platform use

The platform may derive user context from token state, but it must not pretend that the wallet-link table is the primary source of token truth.

---

## Relationship to Snapshot and Eligibility Logic

The wallet-aware user layer is a critical input into eligibility-related systems.

When FUZE needs to determine eligibility for stablecoin profit participation or selected token-aware platform logic, the system may combine:
- active wallet-link relationships
- account-side eligibility rules where applicable
- Ethereum token balance snapshots
- policy-defined exclusion or inclusion logic
- cycle-specific participation rules

The wallet-aware layer does not replace the snapshot pipeline. It helps connect snapshot logic to canonical platform accounts and user-facing context.

This distinction matters because:
- payout eligibility may depend on chain truth plus policy
- wallet links may change over time
- historical cycle eligibility may need to remain stable even when future wallet configuration changes

The wallet-aware layer is therefore an input and mapping layer, not the sole owner of payout eligibility truth.

---

## Relationship to Workspaces and Products

Wallet links are attached to accounts, not directly to workspaces.

However, workspace-aware and product-aware behavior may depend on the wallet-aware context of the participating account.

### Examples
- a workspace member may have holder rank that unlocks selected ecosystem privileges
- a product may show token-aware configuration or benefits based on the account’s linked wallet context
- selected workspace-scoped reporting may reference the wallet-linked participation context of members where policy allows

### Important rule
Workspace ownership and product authority still flow through role and permission systems, not wallet ownership alone.

Wallet-aware behavior may enrich product experience, but it must not rewrite workspace or product authorization rules.

---

## Wallet Conflict Handling

Conflicts must be handled explicitly.

### Common conflict cases
- wallet already linked to another active account
- support claim that wallet was linked to wrong account
- duplicate linking attempts from different accounts
- security review of suspicious wallet-link change
- historical wallet link needs preservation during controlled reassignment

### Required conflict behavior
The platform must not silently overwrite wallet ownership mappings.

Instead, it must support:
- explicit conflict state
- review or support intervention
- controlled migration or unlink/relink flow
- strong audit visibility

Wallet-link integrity is part of platform trust and must be treated accordingly.

---

## Wallet-Related Security Principles

Wallet linkage is security-sensitive even though it is not the same as account auth.

The platform must support:
- proof-based linking
- conflict detection
- unlink safeguards
- audit trails for wallet changes
- support/admin review pathways for sensitive cases
- clear separation between wallet presence and account authority
- recovery-safe handling when wallet links affect ecosystem privileges

The platform must also avoid over-centralizing trust in wallet linkage. Wallets are important, but they are one layer of the ecosystem, not the entire access model.

---

## Canonical Entity Model

At minimum, the wallet-aware user model must support the following durable semantic structures.

### Wallet Link
Representative semantic fields:
- `wallet_link_id`
- `account_id`
- `chain_identifier` or approved chain-family identifier
- `wallet_address`
- `status`
- `created_at`
- `updated_at`
- verification metadata reference
- primary-wallet flag or preference indicator where supported

### Wallet Verification Challenge
Representative semantic fields:
- challenge ID
- account reference
- wallet address reference
- chain identifier
- challenge state
- expiry
- proof method metadata
- correlation reference

### Wallet Conflict / Remediation Case
Representative semantic fields:
- conflict case ID
- wallet address reference
- chain identifier
- affected account references
- conflict type
- case state
- reviewer/operator reference where applicable
- resulting action reference

### Derived Wallet-Aware Context
At minimum, the platform should be able to materialize or compute:
- linked wallet count
- primary wallet indicator where relevant
- holder-aware status
- token-aware rank inputs
- selected product-facing privilege flags
- payout-related mapping references where needed
- freshness state of derived context

### Wallet Audit Events
Representative semantic events:
- wallet link requested
- wallet linked
- wallet set primary
- wallet unlinked
- wallet restricted
- wallet invalidated
- wallet conflict reviewed
- wallet migration/reassignment event where allowed

---

## Read / Write Rights

### Wallet-Aware Domain may:
- create wallet-link records
- update wallet-link lifecycle state
- mark preferred-wallet metadata
- expose wallet-aware context read models
- coordinate conflict-review flows

### Product domains may:
- read wallet-aware context
- display holder-aware UX
- request token-aware features according to platform policy
- use wallet-aware inputs in product logic where approved

### Product domains may not:
- create hidden wallet-link records outside the platform domain
- redefine the meaning of token ownership
- override wallet-link conflict decisions
- treat product-local wallet state as canonical platform linkage

### Reporting domains may:
- derive summaries and visibility layers from wallet-aware context and chain data
- support transparency and payout-related displays

### Reporting domains may not:
- redefine wallet truth
- redefine chain truth

---

## Integration Points

The wallet-aware user layer integrates with:
- identity and account domain
- auth/session domain
- role and access control domain
- workspace and organization domain
- Ethereum token read domain
- snapshot and eligibility pipeline
- profit participation system
- reporting and transparency systems
- product domain services
- support/control-plane workflows

Every integration must preserve the distinction between account identity, wallet linkage, and chain truth.

---

## State Mutation Rules

### Deterministic Mutation Rule
Canonical wallet-link truth may be changed only through explicit owner-controlled mutation boundaries, including approved synchronous commands, governed async flows, or approved admin/control pathways.

### Product Mutation Rule
Products may not directly mutate canonical wallet-link lifecycle, conflict state, or preferred-wallet truth.

### Frontend Boundary Rule
Frontend applications may initiate wallet-link flows and render outcomes, but they must not own canonical truth for:
- wallet-link durability
- proof acceptance
- conflict resolution
- invalidation state
- controlled reassignment

### Reporting Rule
Reporting, analytics, and support views may summarize wallet-aware context but may not redefine wallet-link truth or token truth.

---

## Failure Handling and Edge Cases

### Account has no linked wallet
The account remains a valid FUZE identity and may use products that do not require wallet-aware context.

### User changes preferred wallet
Primary-wallet preference may change, but the account and other wallet links remain intact unless explicitly modified.

### Wallet linked, but chain reads temporarily degraded
The wallet link remains valid as a platform record. Token-aware derived context may enter degraded-read state, but canonical token meaning remains on Ethereum.

### Wallet unlinked after a prior payout snapshot
Historical payout-cycle facts remain anchored to the cycle logic and do not disappear because current wallet configuration changed later.

### Wallet linked to wrong account by support error
The platform must support audited corrective handling, including possible restriction, unlink, reassignment, and support review.

### Product caches stale holder-aware state
Cached product state is non-canonical. Fresh platform context and Ethereum truth must prevail for sensitive decisions.

### User has many wallets with uneven balances
Policy must determine how product-level derived context and payout-related mapping interpret those wallets. Products must not improvise incompatible rules.

---

## Permission / Access Considerations

This specification does not replace access-control specs, but it imposes the following rules:

- wallet linkage does not imply universal read access
- token-aware context does not imply workspace ownership
- token-aware context does not imply support/admin authority
- product authority still flows through authorization and entitlement where applicable
- privileged wallet-review or correction actions require stronger authorization and audit posture than ordinary user flows

---

## Data Model / Storage Implications

This specification requires the following data-model discipline:

- `wallet_link` is canonical off-chain account-to-wallet linkage truth
- verification challenge records must remain durable short-lived entities, not frontend-only transient state
- conflict/remediation records must be durable and auditable
- derived holder-context summaries must remain separate from canonical wallet-link truth
- snapshot-derived summaries are not authoritative eligibility pipeline truth
- projections and convenience views must not become write owners
- wallet-aware corrections should prefer explicit lineage and state transitions over hidden destructive rewrites

---

## Audit / Traceability Requirements

FUZE must be able to determine:

- which account a wallet was or is linked to
- when a wallet was linked, unlinked, restricted, invalidated, or reassigned
- what proof or verification process supported the link
- how conflicts and support/admin decisions were resolved
- whether a visible holder-aware or eligibility-facing view is canonical or derived
- how wallet-aware context influenced downstream product or payout-facing behavior

Auditability is a first-class property of the wallet-aware system.

---

## Security / Risk / Abuse Controls

This wallet-aware model is also a security boundary.

If wallet-aware ownership is weak:
- products can create shadow wallet identity systems
- token ownership can be misrepresented
- wallet misbinding can become hard to correct
- support actions can silently rewrite trust-bearing mappings
- derived holder-aware views can be mistaken for canonical token truth
- ecosystem privileges can drift away from platform policy

All downstream security, risk, abuse-prevention, and monitoring specs must preserve this wallet-aware boundary.

---

## Migration / Compatibility / Supersession Considerations

- migrations must not silently transfer wallet-aware ownership out of the wallet-aware domain
- compatibility layers may support older or narrower product behavior temporarily, but canonical wallet-link truth must remain platform-owned
- renamed or split wallet-aware entities must preserve continuity of authority and auditability
- if older implementations or documents imply looser product-local wallet ownership, this refined specification supersedes them within its scope
- future wallet-auth or multi-chain expansion must occur through the shared wallet-aware and identity/access model rather than product-local exceptions

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

This specification directly governs or materially informs:

- `WALLET_AWARE_USER_API_SPEC.md`
- `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `ETHEREUM_TOKEN_LAYER_SPEC.md`
- `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- product integration specs

---

## Explicitly Deferred Items

The following are intentionally deferred to downstream specifications:

- exact technical wallet-proof method
- exact multi-chain support boundaries if extended beyond current scope
- exact primary-wallet policy behavior
- exact payout-cycle mapping from wallet to claimant identity where relevant
- exact support tooling for wallet conflict and reassignment workflows
- exact privacy boundaries for wallet visibility in product and reporting surfaces

These do not weaken the canonical wallet-aware account model established here.

---

## Final Normative Summary

The FUZE wallet-aware user layer connects canonical platform accounts to on-chain participation context through controlled wallet links. Accounts remain the canonical user identity. Wallet links represent verified account-to-wallet relationships. Ethereum remains the source of truth for token balances and token events. The platform derives holder-aware and eligibility-aware context from the combination of wallet linkage, chain truth, and policy. This structure allows FUZE to support token-aware features, cross-product ecosystem privileges, and payout eligibility logic without collapsing account identity, token ownership, platform authorization, credits semantics, and payout meaning into one ambiguous system.

Any downstream design that weakens this separation is inconsistent with the FUZE platform model.
