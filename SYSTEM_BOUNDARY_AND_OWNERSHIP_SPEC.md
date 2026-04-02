# SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC

## Purpose

This document defines the canonical boundary and ownership model of the FUZE ecosystem. Its purpose is to convert the high-level platform thesis into an explicit responsibility framework that engineering, product, operations, governance, and reporting functions can use as source of truth. It establishes who owns which layer, which service, which data, which decisions, and which categories of truth across the platform, product, on-chain, and external dependency domains.

This specification exists because a multi-product ecosystem becomes fragile when ownership is implied instead of declared. In FUZE, platform trust, implementation speed, data correctness, and governance quality all depend on explicit boundaries.

---

## Scope

This specification covers:

- the canonical ownership model for FUZE as a platform-first architecture
- responsibility boundaries between platform, product, on-chain contracts, and external providers
- ownership boundaries for data, business rules, execution flows, and reporting outputs
- the distinction between canonical truth, derived truth, and presentation layers
- the distinction between operational authority and governance-controlled authority
- the ownership model for shared services versus product extensions
- the boundary model for economic systems including token, credits, and payouts
- the escalation rules for ambiguous ownership cases

This file does not define the full implementation details of specific domains. Those are refined in downstream specifications such as:

- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `SERVICE_TOPOLOGY_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `GOVERNANCE_MODEL_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`

---

## Design Goals

The design goals of the FUZE boundary and ownership model are:

1. to make ownership explicit across all major layers of the ecosystem
2. to prevent duplicated authority across platform and product systems
3. to preserve one clear source of truth per responsibility category
4. to keep token, credits, payouts, treasury, and platform commerce clearly separated
5. to reduce ambiguity in implementation, reporting, and governance
6. to allow products to move quickly without undermining platform coherence
7. to support future scale by making new product integration predictable
8. to strengthen transparency and auditability through explicit role mapping

---

## Non-Goals

This specification is not intended to:

- define team org charts or human reporting lines
- define deployment topology in detail
- replace detailed API, data-model, or smart-contract specs
- create soft or shared ownership where one explicit owner is required
- allow overlapping economic ownership across token, credits, or payout systems
- collapse platform and product responsibilities for convenience

If there is ambiguity between “shared collaboration” and “shared ownership,” this document prefers explicit ownership with collaboration rules rather than joint ownership.

---

## Canonical Ownership Principle

The primary ownership principle of FUZE is:

> every meaningful system concern must have one canonical owner, one canonical source of truth, and one explicit boundary for how other layers may interact with it.

This principle applies to:

- identities
- workspaces
- wallets
- credits
- billing
- product entitlements
- token balances
- payouts
- reserve categories
- reporting outputs
- governance-sensitive actions

A downstream system may consume, derive, cache, display, or enrich a category of truth without owning it.

This principle is one of the most important controls against architectural drift.

---

## Canonical System Layers

The FUZE ecosystem is divided into four primary ownership layers.

### 1. Platform Layer
The shared off-chain application and service layer that owns common operating capabilities across all products.

### 2. Product Layer
The product-specific domain layer that owns category-specific features, objects, workflows, and user experiences on top of the platform.

### 3. On-Chain Layer
The smart-contract layer that owns explicitly committed chain truth such as token balances, credits ledger commitments, payout execution state, and reserve constraints.

### 4. External Dependency Layer
The third-party systems that FUZE depends on operationally but does not own, such as payment processors, AI providers, app stores, wallets, RPC providers, and infrastructure vendors.

Each layer has a different ownership model and a different kind of truth.

---

## Ownership Categories

All ownership decisions in FUZE must be classified into one of the following categories.

### 1. Canonical Ownership
The system is the authoritative source of truth and policy for the category.

### 2. Derived Ownership
The system may compute, aggregate, or summarize information from canonical sources, but it does not redefine the underlying truth.

### 3. Presentation Ownership
The system owns the rendering, formatting, and user-facing representation of information, but not the underlying source truth.

### 4. Execution Ownership
The system owns the actual execution of a process or state transition, but not necessarily the business-policy definition of that transition.

### 5. Governance Ownership
The system or control path owns the authority to approve or restrict a high-impact action.

These categories must not be conflated.

For example, a reporting surface may own presentation of payout history, while the payout contract owns execution truth and the platform owns cycle orchestration logic.

---

## Top-Level Boundary Model

### Platform Owns
The FUZE platform canonically owns:

- users and account identity
- linked authentication methods
- sessions and security state
- organizations and workspaces
- workspace membership and role relationships
- wallet-link mapping to accounts
- billing policy and commercial orchestration
- Platform Credits issuance policy and internal consumption rules
- credits balance coordination and ledger orchestration
- subscription and usage-billing orchestration
- shared AI orchestration primitives
- shared workflow and automation primitives
- platform-wide audit events
- transparency reporting pipelines
- payout-cycle orchestration logic before on-chain funding
- platform-admin control pathways
- shared integration contracts with external providers

### Products Own
Each product canonically owns:

- product-specific domain entities
- product-specific workflows and UX
- product-specific feature configuration
- product-specific AI behavior on top of shared orchestration interfaces
- product-specific usage semantics
- product-specific entitlement mapping within shared billing and credits rules
- product-specific reporting views derived from platform and product truth

### On-Chain Contracts Own
On-chain contracts canonically own:

- FUZE token balance truth on Ethereum
- contract-level supply and token event truth on Ethereum
- Base credits-ledger commitments where credits are formally written on-chain
- funded payout pool and claim execution truth on Base
- reserve and vesting constraints in vault contracts
- contract-level authorization and governance restrictions where implemented on-chain
- contract event history relevant to transparency and audit pipelines

### External Dependencies Own
External providers own:

- raw external payment processing outcomes
- app-store purchase validation state
- Telegram Stars payment rail state
- external wallet client behavior
- external RPC/indexing access paths
- external AI model execution environments
- underlying infrastructure/runtime guarantees provided by third-party vendors

FUZE may normalize external state into FUZE-owned truth, but normalization does not transfer canonical ownership of the external system itself.

---

## Platform Boundary Rules

The platform layer exists to own shared infrastructure and shared operating logic.

### The platform must own all concerns that are:

- cross-product by nature
- economically shared across products
- identity-defining across products
- required for transparency or reporting across products
- required for governance-aware control across products
- foundational to expansion into future products

### The platform must not own:

- product-specific category intelligence logic unless explicitly elevated to a shared service
- product-specific user-facing flows that are not reusable platform capabilities
- product-specific objects that do not need cross-product continuity
- arbitrary content or data that belongs exclusively to one product domain

The platform is therefore the shared operating system, not the owner of every product behavior.

---

## Product Boundary Rules

Products are extension domains, not separate platforms.

### Products may own:

- domain models unique to the product category
- product-specific interaction patterns
- product-specific heuristics and analysis logic
- product-specific automation recipes built on shared workflow primitives
- product-specific display, dashboard, and user-experience models

### Products may not own:

- alternate user identity systems
- alternate workspace systems
- alternate internal spending assets outside approved Platform Credits
- canonical token participation semantics
- independent payout architectures
- hidden reserve logic
- governance-sensitive control paths that bypass the platform or on-chain governance model

This boundary ensures that products remain differentiated without becoming fragmented systems.

---

## On-Chain Boundary Rules

On-chain contracts are authoritative only for the truths explicitly committed to contract design.

### On-chain is canonical for:

- FUZE token balances on Ethereum
- credits commitment state on Base where the platform writes formal ledger events or balances on-chain
- stablecoin payout funding and claim execution on Base
- vesting and reserve constraints encoded into contracts
- contract-level governance permissions and time-locked state where relevant

### On-chain is not canonical for:

- user account identity
- workspace membership
- off-chain billing policy
- AI execution policy
- product domain logic
- product usage semantics
- revenue recognition and accounting before payout funding
- internal admin workflows

This distinction is critical. The presence of smart contracts does not make them the owner of every business concept.

---

## External Dependency Boundary Rules

External dependencies must be treated as trust-boundary systems.

FUZE may depend on these systems operationally, but it must distinguish:

- what the provider does
- what FUZE verifies
- what FUZE records as internal truth
- what FUZE can reconstruct if the provider becomes unavailable
- what FUZE cannot treat as independently authoritative forever

### Examples

#### Stripe
Stripe owns raw payment-processor state. FUZE owns verified commercial interpretation after normalization.

#### Apple / Google Billing
Store platforms own raw purchase and refund source events. FUZE owns post-verification entitlement and credits outcomes inside platform policy.

#### Telegram Stars
Telegram owns the Stars transaction environment. FUZE owns the verified conversion into internal credits only after policy and verification.

#### AI providers
The model vendor owns model execution infrastructure. FUZE owns orchestration, prompt policy, usage accounting, and product-facing interpretation of outputs.

#### RPC/indexing providers
The provider owns transport and data access service. FUZE does not outsource canonical token meaning to an indexer; it uses indexers as access paths to chain truth.

---

## Ownership of Economic Systems

The ownership model for FUZE economic systems is non-negotiable.

### FUZE token
- canonical owner of token balance truth: Ethereum token contract layer
- canonical owner of token participation policy interpretation: platform policy layer
- product layers may consume token-aware context but may not redefine token meaning

### Platform Credits
- canonical owner of credits policy and orchestration: platform layer
- canonical owner of credits ledger commitment on-chain: Base credits contract layer where applicable
- product layers may spend and configure use cases within platform rules but may not redefine the credits system

### Stablecoin payouts
- canonical owner of payout policy and cycle orchestration: platform layer
- canonical owner of funded claim execution truth: Base payout contract layer
- products do not own payout rights or payout execution

### Revenue and profit
- canonical owner of revenue interpretation and accounting logic before payout funding: platform financial/accounting domain
- credits issuance does not itself own profit definition
- treasury and policy determine distributable profit prior to payout-cycle funding

This separation is required for economic clarity and reporting integrity.

---

## Ownership of Governance-Sensitive Actions

Not all actions in FUZE are equal. Some are ordinary runtime actions. Others are governance-sensitive.

### Ordinary runtime actions
Examples:
- user sign-in
- dashboard fetches
- ordinary credits spending for approved usage
- product-specific content generation
- non-sensitive workflow execution

These can be owned by ordinary platform or product runtime services under authorization rules.

### Governance-sensitive actions
Examples:
- reserve or vault-related operational actions
- changes to payout-cycle controls
- material changes to credits policy behavior
- changes to contract registry or governance-critical endpoints
- adjustments to sensitive permissions
- on-chain actions controlled by multisig or timelock domains

These actions require explicit governance-aware ownership and may not be executed through ordinary runtime convenience.

This boundary matters because operational convenience must not silently become governance authority.

---

## Canonical Truth Model

Every meaningful FUZE category must map to one canonical truth owner.

### Canonical truth examples

| Category | Canonical Owner |
|---|---|
| User identity | Platform identity domain |
| Session state | Platform auth/session domain |
| Workspace membership | Platform workspace domain |
| Wallet-to-account link | Platform wallet-aware domain |
| FUZE token balances | Ethereum token contract layer |
| Platform Credits policy | Platform credits domain |
| Platform Credits on-chain commitment | Base credits contract layer |
| Subscription state | Platform commerce/billing domain |
| Product-specific domain object | Relevant product domain |
| Payout-cycle orchestration metadata | Platform payout orchestration domain |
| Payout claim execution state | Base payout contract layer |
| Vault constraint rules | Relevant smart contract vault layer |
| Public transparency report rendering | Platform transparency/reporting domain |

If a future spec cannot identify the canonical owner of a category, that spec is incomplete.

---

## Derived Truth Model

Some systems may derive views from canonical sources without becoming the owner.

### Valid derived-truth examples

- dashboard summaries derived from platform billing records
- product insight views derived from product data and platform entitlements
- investor reports derived from accounting, vault, payout, and product metrics
- public payout pages derived from payout-cycle orchestration records and on-chain claim state
- product holder-tier displays derived from platform wallet links and Ethereum token balances

Derived systems must not mutate canonical meaning. They may summarize, join, enrich, or display.

---

## Presentation Ownership Model

Presentation layers may own:

- formatting
- navigation structure
- chart composition
- grouping logic
- text explanations
- UX decisions for surfacing data

They do not own:

- the meaning of balances
- the meaning of payout eligibility
- the meaning of reserve categories
- the meaning of token participation
- the meaning of billing state

Presentation ownership is important because FUZE includes transparency surfaces that must present complicated architecture clearly without silently redefining it.

---

## Data Ownership Boundaries

### Platform-owned data
Platform-owned data includes:

- users
- accounts
- sessions
- auth providers / linked methods
- organizations and workspaces
- memberships and roles where platform-wide
- wallet links
- credits balances and ledger entries
- billing records
- invoices and receipts
- platform-wide entitlements
- audit events
- payout-cycle metadata
- transparency records
- integration provider records

### Product-owned data
Product-owned data includes:

- QTB analytical outputs and signals
- AIMM market-operation workflow objects
- ZAGA token-utility operating objects
- AIE event intelligence entities and recommendation objects
- HerHelp business-app generation objects and business workflow artifacts
- Botmad work-execution flows, agent tasks, and desktop-operation artifacts
- other product-specific domain models not promoted to platform scope

### On-chain-owned records
On-chain-owned records include:

- token transfer/event truth
- on-chain balance truth where explicitly implemented
- claim-state truth for payout execution
- reserve lock and vesting rule truth
- governance event truth where contract-controlled

These categories must remain separated in data design.

---

## Decision Ownership Rules

Every decision class in FUZE must have a boundary owner.

### Platform-level decisions
Owned by platform architecture and policy domains.

Examples:
- credits class model
- payment normalization rules
- subscription framework
- wallet-link semantics
- entitlement framework
- payout-cycle orchestration design
- shared control-plane behavior

### Product-level decisions
Owned by product domains within platform constraints.

Examples:
- product-specific UX
- product-specific AI usage patterns
- product-specific object models
- product-specific workflow logic
- product-specific monetization configuration under platform billing rules

### Governance-level decisions
Owned by governance structures, policy, and where relevant contract-governed approvals.

Examples:
- reserve action policy
- governance-sensitive role changes
- timelock-protected system changes
- vault action restrictions
- selected future DAO-lite participation areas

### External-provider decisions
Owned by external providers, but accepted by FUZE only after verification or normalization where required.

---

## Boundary Escalation Rules

When ownership ambiguity appears, the following escalation rules apply.

### Rule 1
If the concern is shared across more than one product, default ownership belongs to the platform unless a formal exception is declared.

### Rule 2
If the concern affects token, credits, payouts, reserves, or governance-sensitive actions, it must be explicitly reviewed as a platform-and-governance concern rather than left to product interpretation.

### Rule 3
If the concern involves canonical chain state, ownership belongs to the relevant contract domain for the state itself and to the platform for business-policy interpretation unless explicitly specified otherwise.

### Rule 4
If the concern is only a visualization or summary, ownership is presentation ownership, not canonical ownership.

### Rule 5
If the concern crosses external trust boundaries, FUZE must specify what it verifies and what it merely receives.

These escalation rules are intended to reduce silent architectural drift.

---

## Canonical Ownership Matrix

| Concern | Canonical Owner | Secondary Consumers | Notes |
|---|---|---|---|
| User identity | Platform | All products | Shared across ecosystem |
| Auth/session state | Platform | All products | Product cannot redefine |
| Workspace/org state | Platform | All products | Shared collaborative context |
| Wallet links | Platform | Products, reporting, payouts | Mapping layer, not token truth |
| Token balances | Ethereum contract layer | Platform, products, reporting | Canonical participation truth |
| Credits policy | Platform | All products | Internal economic rules |
| Credits on-chain commitment | Base credits contract layer | Platform, reporting | Chain-committed state |
| Billing state | Platform | Products | Product config allowed within rules |
| Product-specific domain objects | Product | Platform reporting where needed | Domain-owned |
| Payout-cycle orchestration | Platform | Reporting, governance | Policy-driven off-chain coordination |
| Claim execution state | Base payout contract layer | Platform, reporting | Canonical claim truth |
| Reserve lock/vesting rules | Relevant vault contracts | Platform reporting, governance | Contract-constrained |
| Public report rendering | Platform reporting domain | Public users, investors | Presentation layer |

This matrix is normative unless a downstream spec explicitly narrows it with consistent detail.

---

## Security Implications of Ownership Boundaries

Clear ownership is a security requirement, not just an architecture convenience.

Without explicit ownership:

- products may overreach into platform truth
- external-provider failures may corrupt internal state interpretation
- payout rights may be misrepresented
- credits logic may diverge across products
- governance-sensitive actions may be executed through the wrong pathways
- reporting layers may silently redefine canonical truth

For this reason, all security-relevant downstream specifications must preserve the ownership model declared here.

---

## Transparency Implications of Ownership Boundaries

FUZE is transparency-first, so ownership boundaries must also support public intelligibility.

The platform must be able to explain:

- which system owns balances
- which system owns payout claims
- which system owns reserve constraints
- which system owns product-specific metrics
- which system owns public reporting presentation
- which system is authoritative when platform and chain views differ temporarily due to sync lag

This is one of the ways ownership clarity improves public trust.

---

## Edge Cases and Failure Handling Principles

### Product attempts to redefine shared billing behavior
The platform remains canonical owner of billing and credits policy. Product-specific deviation requires explicit platform approval and spec change.

### Reporting surface disagrees with chain state
Chain state remains canonical for explicitly on-chain truths. Reporting is derived and must reconcile rather than overwrite.

### External payment provider sends contradictory signals
External provider remains source of raw event input. FUZE must apply verification and internal normalization rules before mutating credits or entitlements.

### Async process partially completes across platform and chain domains
Canonical ownership remains split by category. Partial completion must be recorded as incomplete orchestration rather than treated as fully completed truth.

### Product stores cached holder information
Cache is non-canonical. Ethereum token layer remains canonical source of token balance truth.

### Governance-sensitive action initiated through normal product interface
The action must be rerouted through the appropriate control-plane and governance pathway or rejected.

---

## Open Items

The following items are intentionally deferred to downstream specs:

- exact entity-by-entity ownership maps for every platform and product schema
- exact command/event ownership for async workflows
- exact service boundaries for modular-monolith versus service-oriented deployment
- exact approval graph for governance-sensitive off-chain actions
- exact ownership of intermediate reconciliation artifacts in financial reporting

These do not weaken the ownership model established here. They refine it.

---

## Closing Summary

FUZE is a platform-first ecosystem, and platform-first ecosystems require explicit ownership. This specification establishes that every major category of truth, control, and execution in FUZE must have one canonical owner and one explicit boundary. The platform owns shared operating logic. Products own product-specific domains. On-chain contracts own only the truths explicitly committed to chain. External systems remain external and must be treated as trust boundaries. The separation between token, credits, payouts, reserves, reporting, and governance-sensitive actions is therefore not optional. It is the ownership foundation that allows FUZE to scale with clarity, security, and transparency.
