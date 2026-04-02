# SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC

## Purpose

This document defines the canonical system overview and system boundaries of the FUZE platform. Its purpose is to establish the top-level source-of-truth description of what FUZE is, what the FUZE platform owns, what products on top of FUZE own, what on-chain contracts own, what external systems own, and how responsibility is separated across the ecosystem.

This file is intentionally foundational. It should be read before domain-specific implementation documents because it establishes the primary architectural frame used by all other FUZE specification files.

---

## Scope

This specification covers:

- the top-level purpose of the FUZE platform
- the system goals that define FUZE as a platform company
- non-goals that FUZE should not be treated as responsible for
- the platform-first architectural principle
- the relationship between FUZE platform and FUZE products
- the boundary between platform, product, and on-chain systems
- the boundary between FUZE-owned systems and third-party dependencies
- the canonical ownership model for the major layers of the ecosystem
- the top-level rules for how future specifications must interpret system responsibilities

This specification does not define deep implementation details of any one subsystem. Those belong in dedicated files such as `PLATFORM_ARCHITECTURE_SPEC.md`, `IDENTITY_AND_ACCOUNT_SPEC.md`, `PLATFORM_CREDITS_SPEC.md`, `CHAIN_ARCHITECTURE_SPEC.md`, and related documents.

---

## Design Goals

The design goals of FUZE system boundaries are:

1. to make it explicit what FUZE is building as a shared platform
2. to prevent product-level duplication of core platform concerns
3. to preserve clear separation between token, credits, and payout systems
4. to preserve clear separation between on-chain truth and off-chain application logic
5. to make ownership and responsibility auditable across the ecosystem
6. to support multi-product expansion without weakening platform coherence
7. to support production-grade engineering decisions through explicit architectural boundaries
8. to ensure future specifications remain consistent with one canonical model

---

## Non-Goals

The FUZE platform is not intended to be:

- a single-product application
- a generic token project whose architecture is centered primarily around token narrative
- a fully on-chain product environment where all application logic is forced into smart contracts
- a monolithic backend in which every product owns its own copy of core systems
- a governance model where token voting directly controls all day-to-day product or platform operations
- a treasury structure where reserve categories are merged into opaque omnibus wallets
- an architecture where Platform Credits, FUZE token, and stablecoin payouts are treated as interchangeable assets

These non-goals are important because many system-level mistakes begin when the platform is interpreted through the wrong model.

---

## System Goals

FUZE is designed to achieve the following system-level outcomes:

- operate as the central platform layer behind a growing ecosystem of AI-powered SaaS products
- provide one shared operating environment for identity, account continuity, commerce, credits, AI orchestration, workflow execution, auditability, and governance-aware controls
- enable multiple products to launch and scale without rebuilding foundational services independently
- provide one internal economic layer through Platform Credits
- preserve Ethereum as the canonical token participation layer
- preserve Base as the operational layer for credits and stablecoin payout execution
- support a policy-defined stablecoin profit participation model without blurring profit participation with product usage
- make the platform easier to understand as it grows, not harder
- support long-term public credibility through structural transparency

These goals define FUZE as a platform company first.

---

## Canonical Platform Definition

FUZE is a **transparency-first SaaS platform ecosystem** and the **central platform layer** behind a growing ecosystem of AI-powered SaaS products.

At the system-design level, FUZE must be understood as:

- a shared operating layer
- a shared commercial layer
- a shared participation-awareness layer
- a shared transparency layer
- a shared governance and control layer

FUZE is not the same thing as any one product in the ecosystem. It is the system that sits beneath products and provides the reusable capabilities they depend on.

The current canonical product ecosystem on top of FUZE includes:

- QTB — Quant AI Trade Brain
- AIMM — AI Market Maker
- ZAGA — Token Utility OS
- AIE — Event Intelligence
- HerHelp — AI-assisted business that starts with sheet-to-app — an AI SaaS for SMEs
- Botmad — AI desktop employee
- and more

The existence of multiple products does not change the definition of FUZE. It strengthens the need for clear platform boundaries.

---

## Platform-First Architecture Principle

The primary architectural principle of FUZE is:

> shared platform capabilities must be owned once at the platform layer and reused across products unless there is a clear product-specific reason not to do so.

This principle exists to prevent the ecosystem from turning into a loose portfolio of semi-independent applications.

Under this principle:

- identity must be platform-owned
- credits and internal commercial logic must be platform-owned
- wallet-aware participation context must be platform-owned
- AI orchestration primitives must be platform-owned
- workflow and automation primitives must be platform-owned
- transparency and audit infrastructure must be platform-owned
- governance and control-plane behavior must be platform-owned

Products may extend, configure, and consume these capabilities, but they should not redefine their core behavior independently unless explicitly permitted by platform policy.

This principle is one of the main structural safeguards against platform fragmentation.

---

## Top-Level System Layers

The FUZE ecosystem is divided into the following top-level layers:

### 1. FUZE Platform Layer
The shared off-chain application and service layer that provides identity, workspaces, credits, billing, AI orchestration, automation, auditability, reporting, and governance-aware controls.

### 2. FUZE Product Layer
The product-specific application domains that sit on top of FUZE and consume platform services while owning product-specific features, data models, and workflows.

### 3. On-Chain Participation and Financial Layer
The set of token, credits-ledger, payout, and reserve contracts that provide on-chain truth for participation, internal credits recording, reserve separation, and payout execution.

### 4. External Dependency Layer
Third-party systems such as Stripe, app-store billing systems, Telegram Stars, external chains, notification providers, infrastructure vendors, and any other external dependency not owned by FUZE.

These layers interact, but they do not share ownership. Each layer has a different source of truth and a different responsibility model.

---

## Canonical Asset Separation

The following asset separation is mandatory across the system:

### FUZE token
- ecosystem participation asset
- canonical chain: Ethereum
- used for holder identity, eligibility, status, and ecosystem participation logic

### Platform Credits
- internal consumption asset
- canonical operational chain: Base
- used for subscriptions, usage billing, feature unlocks, AI actions, and platform services

### Stablecoin payouts
- profit participation payout asset
- payout execution chain: Base
- used for funded claim cycles under the stablecoin profit participation model

These three are not interchangeable.

All future specifications must preserve this separation.

---

## Canonical Chain Separation

The following chain-role model is mandatory:

### Ethereum mainnet
Ethereum is the canonical token layer. It is the source of truth for FUZE token balances, holder recognition, and snapshot-derived eligibility inputs.

### Base
Base is the operational commerce layer for Platform Credits and the payout execution layer for stablecoin profit participation claims.

This chain separation exists because participation truth and operational execution do not have the same requirements.

---

## System Boundary Model

The FUZE system boundary model is summarized below.

### FUZE platform owns:
- user identity and account continuity
- session and linked-login logic
- workspace and organization models
- wallet-linking and wallet-aware participation context
- Platform Credits issuance and internal balance logic
- subscriptions and usage billing
- invoicing and receipt logic where applicable
- payment verification and normalization flows
- AI orchestration primitives
- workflow and automation primitives
- audit logging and activity records
- transparency reporting surfaces
- product integration contracts and shared service contracts
- platform-admin and governance-aware control plane behavior

### FUZE products own:
- product-specific domain logic
- product-specific user workflows
- product-specific data models beyond shared platform entities
- product-specific monetization configuration within platform rules
- product-specific AI prompts, workflows, actions, and product experiences
- product-specific reporting needs beyond shared platform reporting
- product-specific interfaces and user-facing experiences

### On-chain contracts own:
- canonical FUZE token balance truth on Ethereum
- Platform Credits on-chain ledger truth on Base
- stablecoin payout pool and claim execution logic on Base
- token allocation vault logic
- reserve and vesting contract constraints
- contract-level governance and timelock restrictions
- contract-level event emission for public visibility

### External systems own:
- external fiat payment processing
- external app-store purchase authorization
- external Telegram Stars authorization
- third-party notification delivery
- external infrastructure runtime dependencies
- external market and blockchain data not persisted as FUZE-owned truth unless ingested and normalized under FUZE policy

This model is the foundation for all deeper ownership documents.

---

## Ownership and Responsibilities

### Platform Ownership

The FUZE platform team is responsible for designing and maintaining the shared operating environment of the ecosystem. Platform ownership includes:

- platform schemas and shared services
- commercial infrastructure
- shared event model
- audit and transparency infrastructure
- shared APIs where platform concerns are involved
- integration standards used by products
- on-chain/off-chain coordination flows where platform logic participates

Platform ownership must not be silently delegated to products.

### Product Ownership

Each product team or product domain is responsible for:

- product-specific business logic
- product-specific interface design
- product-specific data that is not declared platform-owned
- product-specific flows using shared platform services
- product-specific roadmap evolution inside platform constraints

Product ownership does not include redefining platform primitives.

### On-Chain Ownership

On-chain contract logic is the source of truth only for the categories it explicitly governs.

For example:
- Ethereum token balance truth belongs to the token contract layer
- credits ledger truth belongs to the credits contract layer on Base
- payout execution truth belongs to the payout contract layer on Base
- reserve constraints belong to the respective vault contracts

On-chain truth must not be overstated. Contracts do not own all platform logic. They own only the categories explicitly committed to chain.

### External Ownership

Third-party systems remain third-party systems. Even when FUZE depends on them operationally, they are not FUZE-owned sources of truth unless the platform explicitly normalizes and records the relevant state under FUZE-controlled models.

This is especially important for:
- Stripe payment state
- Telegram Stars state
- Apple and Google billing state
- external blockchain RPC or indexing behavior
- external market data feeds

---

## Product-to-Platform Relationship

Products in the FUZE ecosystem should be treated as **platform consumers and platform extenders**, not as independent platforms.

A product may:

- call shared APIs
- use shared identity
- use shared credits
- use shared billing
- attach product-specific entitlements
- define product-specific workflows
- define product-specific AI execution patterns
- store product-owned domain data
- render product-owned interfaces

A product may not:

- redefine user identity independently
- create a separate internal spending asset outside Platform Credits without explicit platform-level approval
- redefine the canonical meaning of FUZE token holdings
- bypass shared billing rules where platform monetization applies
- redefine reserve or payout logic
- create hidden governance pathways that conflict with platform control rules

This relationship is essential to preserving platform coherence.

---

## Boundary Between Platform and Product Data

As a top-level rule:

### Platform-owned data includes:
- users
- accounts
- sessions
- linked login methods
- workspaces and organizations
- roles and memberships where platform-wide
- wallet links
- credits balances and ledger entries
- billing records
- invoices and receipts
- platform-wide entitlements
- platform-wide audit events
- transparency and payout reporting records

### Product-owned data includes:
- product-specific configurations
- product-specific workflows
- product-specific content or analytical objects
- product-specific usage outputs
- product-specific feature flags or internal models
- product-specific AI artifacts unless promoted to shared platform use

If there is ambiguity, future specs must make ownership explicit rather than assume it.

---

## Boundary Between Off-Chain and On-Chain Logic

FUZE intentionally uses a mixed architecture. Not all truth belongs on-chain, and not all truth belongs off-chain.

### Off-chain is authoritative for:
- application identity and account state
- sessions and auth flows
- workspace membership and access control
- payment verification workflows
- product configuration and product workflows
- billing logic orchestration
- profit calculation and accounting workflows before payout funding
- most AI orchestration and automation logic
- reporting generation and public transparency presentation

### On-chain is authoritative for:
- FUZE token balance truth on Ethereum
- credits-ledger truth committed to Base
- funded payout pool and claim execution truth on Base
- reserve and vesting contract rules
- contract-level governance permissions and event history

Off-chain logic must not pretend to own on-chain truth.  
On-chain contracts must not be expected to own application domains they were never designed to represent.

---

## Boundary Between Revenue, Credits, and Profit

This boundary is mandatory and must be preserved in all future documents.

### Credits are not revenue by themselves
Credits represent internal purchasing power after verified payment or approved issuance.

### Revenue is not profit by itself
Revenue must be reconciled with costs, reversals, refunds, operational expenses, and accounting policy before profit can be determined.

### Profit is not payout until funded
Stablecoin profit participation becomes real only after the payout cycle is finalized, funded, and opened under the payout architecture.

This boundary protects the system against economic confusion.

---

## Boundary Between Treasury, Foundation, and Other Reserves

Reserve categories must remain distinct.

### Treasury Reserve
Strategic platform reserve for long-horizon execution flexibility.

### Foundation
Long-term stewardship structure with protected principal logic.

### Holder Incentives
Community and holder-facing incentive programs.

### Ecosystem Partnership
Growth and partnership deployment.

### Liquidity Operations
Liquidity and market-operations-related deployment.

### Transparency / Stability
Trust-preserving and stability-oriented reserve logic.

No future specification should collapse these categories into a generic treasury concept.

---

## External Dependencies and Trust Boundaries

The FUZE platform depends on external systems, but each dependency must be treated as crossing a trust boundary.

Major dependency classes include:

- blockchain RPC and indexing providers
- payment processors
- app-store billing providers
- Telegram Stars or mini-app environment dependencies
- AI model providers
- cloud infrastructure providers
- notification providers
- external analytics or data services

For each such dependency, later specs must define:

- what FUZE trusts that dependency to do
- what FUZE verifies independently
- what FUZE persists as internal truth
- what failures can occur if the dependency degrades
- what fallback or retry strategy applies

This rule prevents hidden coupling from becoming invisible system risk.

---

## Canonical Responsibility Matrix

| Layer / Concern | FUZE Platform | Product Layer | On-Chain Contracts | External Systems |
|---|---|---|---|---|
| Identity and sessions | Owns | Uses | Does not own | May support auth providers |
| Workspaces and roles | Owns | Uses / extends | Does not own | Does not own |
| Wallet linking | Owns mapping | Uses context | Token contracts provide balances only | Wallet software external |
| FUZE token balances | Reads / interprets | Reads if needed | Owns canonical truth | RPC/indexers external access path |
| Platform Credits | Owns policy/orchestration | Uses for monetization | Owns committed ledger truth on Base | Payment rails trigger source events |
| Billing and subscriptions | Owns | Configures product rules | Does not own | Payment providers execute external flows |
| Product domain workflows | Provides primitives | Owns | Does not own | May depend on external APIs |
| Profit calculation | Owns accounting/policy | Does not own directly | Does not own | External accounting inputs may exist |
| Payout claims | Orchestrates cycle creation | Does not own directly | Owns funded claim execution | External wallets interact |
| Reserve constraints | Reads/reporting | Does not own | Owns vault rules | Does not own |
| Transparency reports | Owns reporting layer | May contribute product inputs | Provides event truth where relevant | Does not own |

This matrix is high-level and is refined in later specifications.

---

## Key Architectural Rules

All future FUZE specifications must comply with the following rules:

1. FUZE is platform-first.
2. Products consume and extend platform services; they do not redefine them.
3. FUZE token, Platform Credits, and stablecoin payouts must remain separate.
4. Ethereum is the canonical token layer.
5. Base is the credits and payout execution layer.
6. Credits do not automatically equal profit.
7. Profit participation is cycle-based and policy-defined.
8. Reserve categories must remain visible and separate.
9. Governance must be bounded by architecture, policy, multisig, and timelock discipline.
10. Transparency must be implemented through structure and reporting, not only messaging.
11. Off-chain and on-chain ownership must remain explicit.
12. External dependencies must be treated as trust boundaries, not invisible assumptions.

---

## Edge Cases and Failure Handling Principles

This foundational document does not define subsystem-specific incident procedures, but it does establish the following top-level failure-handling principles:

- failure in one product must not redefine shared platform truth
- temporary external payment-provider failure must not corrupt internal credits truth
- degraded indexing or RPC access must not change canonical token meaning
- payout-cycle preparation failure must not be treated as funded payout truth
- governance delay or timelock delay is preferable to opaque high-impact execution
- unresolved ambiguity must be surfaced explicitly rather than silently absorbed into implementation

These principles should be inherited by deeper operational specifications.

---

## Open Items

At the system-overview level, the following areas may still require deeper formalization in later specs:

- exact product service decomposition across platform and product runtime boundaries
- exact microservice versus modular-monolith deployment decisions
- exact schema ownership mapping for every shared and product-specific entity
- exact external-provider trust contracts and fallback models
- exact event contract between platform services and product services

These are not unresolved conceptual ambiguities. They are later-stage elaborations of the boundary model established here.

---

## Closing Summary

FUZE is the central platform layer behind a growing ecosystem of AI-powered SaaS products. This system overview establishes that FUZE must be designed as a platform-first architecture with explicit ownership boundaries across the platform layer, product layer, on-chain contracts, and external dependencies. It also establishes the non-negotiable separations between FUZE token, Platform Credits, and stablecoin payouts; between Ethereum and Base chain roles; and between treasury, Foundation, incentives, liquidity, and transparency-related reserve categories.

This document is the top-level architectural source of truth for how FUZE should be interpreted and built. All future specifications must remain consistent with these boundaries.
