# WALLET_AWARE_USER_SPEC

## Purpose

This document defines the canonical wallet-aware user architecture of the FUZE platform. Its purpose is to establish how platform accounts relate to wallet addresses, how wallet links are represented and controlled, how token-aware and holder-aware platform behavior is derived, how wallet state interacts with participation and eligibility logic, and how products should consume wallet-related context without redefining identity or token truth.

This specification is critical because FUZE is both a SaaS platform ecosystem and a Web3-native participation environment. The platform must therefore support wallet-aware behavior in a way that is structurally clear, secure, and compatible with the broader separation between:
- account identity,
- authentication,
- wallet linkage,
- token balances,
- Platform Credits,
- and stablecoin profit participation.

The wallet-aware user layer is what allows FUZE to connect canonical platform identity to ecosystem participation without collapsing these roles into one ambiguous model.

---

## Scope

This specification covers:

- the distinction between account identity and wallet identity
- the wallet-link model for FUZE accounts
- multi-wallet support
- wallet lifecycle and wallet-link states
- holder-aware and token-aware derived context
- wallet use in eligibility, privileges, and ecosystem participation logic
- ownership boundaries between platform wallet records and canonical chain truth
- wallet-related security and control rules
- wallet-aware behavior across products
- edge cases and failure-handling principles

This specification does not redefine token balance truth, auth/session mechanics, or workspace lifecycle semantics. Those are refined in:

- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `ETHEREUM_TOKEN_LAYER_SPEC.md`
- `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`

---

## Design Goals

The design goals of the wallet-aware user model are:

1. to let one canonical FUZE account connect to one or more wallets in a controlled way
2. to preserve the distinction between platform identity and on-chain ownership
3. to support holder-aware and token-aware user experience across products
4. to support Ethereum-based eligibility and participation logic without making wallets the sole identity model
5. to keep wallet state auditable, unlinkable under policy, and secure against misbinding
6. to support future products and future participation logic without redefining the wallet model
7. to make products consume wallet-aware context through platform rules rather than through ad hoc chain assumptions

---

## Non-Goals

This specification is not intended to:

- make wallet addresses the canonical user identity of FUZE
- treat token balance reads as substitutes for platform authorization
- define arbitrary wallet-based admin or governance privileges outside approved policy
- make every product require a wallet link
- collapse wallet linkage into auth/session behavior
- define every chain-integration detail of balance reads or snapshot generation
- allow products to keep their own hidden wallet identity systems outside the platform model

---

## Canonical Wallet-Aware Principle

The primary principle of the FUZE wallet-aware layer is:

> a wallet is a linked participation artifact associated with a canonical FUZE account, while canonical token ownership truth remains on-chain and platform identity truth remains in the account system.

This means:

- the account remains the canonical user identity
- the wallet link records the relationship between account and wallet
- the chain remains the source of truth for token balances and token events
- products may consume wallet-aware context, but they may not redefine token truth or account identity

This principle is mandatory across the ecosystem.

---

## Core Concepts

### Account
The canonical FUZE platform identity.

### Wallet
A blockchain address or wallet-controlled address that may be linked to a FUZE account for participation-aware platform behavior.

### Wallet Link
A platform record representing the relationship between a canonical account and a wallet address.

### Wallet Ownership Proof
The verification event or mechanism proving that the actor controlling the canonical account also controlled the wallet at the time of linking.

### Primary Wallet
A wallet designated by platform policy or user choice as the main wallet for selected user experience or eligibility preferences where applicable.

### Secondary Wallet
Any additional linked wallet associated with the same account.

### Holder-Aware Context
Derived platform context based on wallet linkage and token-related state, such as holder rank, participation tier, or feature eligibility.

### Eligibility Context
Derived state used in payout, privilege, or policy decisions based on wallet-link records and relevant chain truth.

---

## Canonical Wallet Model

FUZE must use a **wallet-aware account model**, not a wallet-only identity model.

Under this model:

- one account may have zero, one, or multiple linked wallets
- one wallet link points to one canonical account at a time
- wallet linking is a controlled and auditable action
- wallet balances and token events are not stored as primary truth in the wallet-link table itself
- wallet-aware context is derived from the combination of wallet linkage and on-chain reads or snapshot pipelines

This structure is what allows FUZE to remain both SaaS-native and Web3-native without weakening either side.

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
The wallet remains part of history but is no longer the preferred wallet for context where a primary designation or policy replacement exists.

### Invalidated
A wallet link is marked unusable because its original trust assumption is no longer acceptable, such as support-detected misbinding or a security remediation event.

Transitions must be auditable.

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

This model gives FUZE the flexibility needed for serious Web3-aware platform behavior.

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
- unlinking does not create policy-breaking inconsistencies for active features, billing, or pending participation workflows, or those impacts are handled explicitly

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

The implementation details may be defined downstream, but the platform must preserve proof-based linking semantics.

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

This distinction is essential to FUZE’s architectural integrity.

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

This separation is one of the most important boundary rules in FUZE. The platform may derive user context from token state, but it must not pretend that the wallet-link table is the primary source of token truth.

---

## Relationship to Snapshot and Eligibility Logic

The wallet-aware user layer is also a critical input into eligibility-related systems.

When FUZE needs to determine eligibility for stablecoin profit participation or selected token-aware platform logic, the system may combine:

- active wallet-link relationships
- account-side eligibility rules
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

## Minimum Data Model Requirements

At minimum, the wallet-aware user model must support:

### Wallet Link
- wallet_link_id
- account_id
- chain identifier
- wallet_address
- status
- created_at
- updated_at
- verification metadata reference
- primary_wallet flag or preference indicator where supported

### Wallet Audit Event
- wallet link requested
- wallet linked
- wallet set primary
- wallet unlinked
- wallet restricted
- wallet invalidated
- wallet conflict reviewed
- wallet migration/reassignment event where allowed

### Derived Wallet-Aware Context
At a minimum, the platform should be able to materialize or compute:
- linked wallet count
- primary wallet indicator where relevant
- holder-aware status
- token-aware rank inputs
- selected product-facing privilege flags
- payout-related mapping references where needed

---

## Read / Write Rights

### Wallet-Aware Domain may:
- create wallet-link records
- update wallet-link lifecycle state
- mark preferred wallet metadata
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

Reporting domains may not redefine wallet truth or chain truth.

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

## Edge Cases and Failure Handling

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

## Open Items

The following areas are intentionally refined in downstream specifications:

- exact technical wallet-proof method
- exact multi-chain support boundaries if extended beyond current scope
- exact primary-wallet policy behavior
- exact payout-cycle mapping from wallet to claimant identity where relevant
- exact support tooling for wallet conflict and reassignment workflows
- exact privacy boundaries for wallet visibility in product and reporting surfaces

These do not weaken the canonical wallet-aware account model established here.

---

## Closing Summary

The FUZE wallet-aware user layer connects canonical platform accounts to on-chain participation context through controlled wallet links. Accounts remain the canonical user identity. Wallet links represent verified account-to-wallet relationships. Ethereum remains the source of truth for token balances and token events. The platform derives holder-aware and eligibility-aware context from the combination of wallet linkage, chain truth, and policy. This structure allows FUZE to support token-aware features, cross-product ecosystem privileges, and payout eligibility logic without collapsing account identity, token ownership, and platform authorization into one ambiguous system.
