# CHAIN_ARCHITECTURE_SPEC

## Purpose

This document defines the canonical chain architecture of the FUZE platform. Its purpose is to establish how FUZE separates token participation, internal platform commerce, and profit-participation execution across different chain environments while preserving one coherent ecosystem model.

This specification is foundational because chain choice in FUZE is not a cosmetic deployment detail. Chain roles are part of the platform’s economic, governance, transparency, and operational architecture. The system must preserve canonical token truth, support low-cost internal credits activity, and enable practical stablecoin payout execution without collapsing these roles into one ambiguous on-chain layer.

---

## Scope

This specification covers:

- the canonical layered chain model of FUZE
- the role of Ethereum as the canonical token layer
- the role of Base as the Platform Credits layer
- the role of Base as the stablecoin payout execution layer
- how holder truth, credits, and payouts connect across layers
- the policy and systems that connect on-chain and off-chain responsibilities
- why token, credits, and payout layers are intentionally separated
- why sold credits do not automatically equal distributable profit
- security, operational, and transparency implications of the layered model

This specification does not define the full details of tokenomics, smart contract implementation internals, or every off-chain accounting rule. Those are refined in:

- `ETHEREUM_TOKEN_LAYER_SPEC.md`
- `BASE_PLATFORM_CREDITS_LAYER_SPEC.md`
- `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md`
- `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`

---

## Design Goals

The design goals of the FUZE chain architecture are:

1. to preserve one canonical token participation layer
2. to support low-cost, high-frequency internal platform commerce
3. to enable practical stablecoin payout execution for eligible holders
4. to maintain clear economic separation between token, credits, and payouts
5. to reduce chain-role confusion across products, holders, and operators
6. to make cross-layer behavior auditable and explainable
7. to support long-term platform scale without forcing every function onto one chain
8. to align technical architecture with the economic meaning of each layer

---

## Non-Goals

This specification is not intended to:

- force all FUZE functions onto one chain for narrative simplicity
- make the FUZE token the default internal spending unit of products
- make Platform Credits the same thing as token ownership
- make payout execution the same thing as token transfer
- require holders to move the FUZE token to Base in order to participate in stablecoin payouts
- define chain choice independently from economic role
- blur accounting, payout funding, and internal credits issuance into one on-chain meaning

---

## Canonical Chain Principle

The primary chain principle of FUZE is:

> each major economic role in the ecosystem should live on the chain environment best suited to that role, while cross-layer connection should be handled through explicit policy, verification, snapshots, and controlled contract interaction rather than through conceptual collapse.

This means:

- token participation truth should have one canonical layer
- internal product commerce should use a practical operational layer
- payout execution should use a cost-efficient claim layer
- cross-layer linkage should be explicit and auditable
- the user-facing ecosystem should remain coherent even when chain roles are separated

This principle is one of the clearest expressions of FUZE’s platform-first and clarity-first architecture.

---

## Why FUZE Uses a Layered Chain Model

FUZE uses a layered chain model because the ecosystem contains multiple economic and operational functions that do not share the same requirements.

The FUZE token is the ecosystem participation asset. It requires a canonical and credible holder-truth environment. Platform Credits are the internal consumption unit of the platform. They require lower-cost, higher-frequency issuance, deduction, and adjustment behavior. Stablecoin payout execution requires a practical claim environment so that funded payout cycles can be used efficiently by eligible holders.

If these roles were forced into one undifferentiated chain environment, the platform would inherit one or more weaknesses:

- token operations would become mixed with operational commerce
- product usage would become more expensive or more cumbersome than necessary
- payout execution could become inefficient for users
- economic roles would become harder to explain
- platform scale would be constrained by the wrong execution environment

The layered chain model solves this by matching each function to the chain role that best fits its purpose.

### Canonical FUZE chain model

- **FUZE token remains on Ethereum mainnet**
- **FUZE Platform Credits operate on Base**
- **stablecoin profit participation executes on Base**
- **Ethereum FUZE-holder snapshots remain the eligibility source for stablecoin profit participation**

This structure is not a compromise. It is a deliberate architecture that aligns technical execution with economic meaning.

---

## Core Layer Roles

The FUZE chain architecture is best understood through three core layers.

### 1. Token Participation Layer
This is the layer where the FUZE token exists as the ecosystem participation asset.

### 2. Internal Commerce Layer
This is the layer where Platform Credits are issued, stored, and consumed for subscriptions, usage billing, and internal platform value flow.

### 3. Payout Execution Layer
This is the layer where funded stablecoin payout cycles become claimable by eligible holders.

These layers are connected, but they are not the same thing.

---

## Ethereum as the Canonical Token Layer

Ethereum mainnet serves as the canonical token layer of the FUZE ecosystem.

This means Ethereum is the source of truth for:

- FUZE token contract identity
- total supply and token distribution state
- holder balances
- holder ownership context
- snapshot-derived eligibility input for profit participation
- holder-aware ecosystem status and rank logic where applicable

### Why Ethereum is used for token truth

Ethereum is used because the FUZE token is the ecosystem participation asset, not the internal commerce unit of the platform. The token layer benefits from a strong canonical environment for ownership, supply, and long-term external credibility.

The purpose of Ethereum in FUZE is not to optimize frequent product-level commerce. Its role is to anchor the participation asset. This makes the answer to “who holds FUZE?” structurally clear.

### Ethereum layer principles

- Ethereum is canonical for token balances and holder truth
- Ethereum balances drive snapshot-based eligibility inputs
- Ethereum token ownership does not automatically equal product entitlement
- Ethereum is not the default execution environment for high-frequency platform commerce
- Ethereum is not the direct claim environment for stablecoin payouts

This role clarity protects the integrity of the token.

---

## Base as the Platform Credits Layer

Base serves as the Platform Credits layer of FUZE.

This means Base is the operational chain environment where the internal consumption economy of the platform is recorded and managed. Platform Credits are issued on Base after verified payment and are then used across products for subscriptions, usage billing, feature unlocks, premium actions, and related internal platform commerce.

### Why Base is used for credits

The Platform Credits layer must support more frequent operational actions than the token layer, including:

- issuance after verified payment
- spend and deduction
- reservation and settlement patterns
- reversals and adjustments
- subscription-linked deductions
- usage-based product activity
- workspace and account balance operations

These functions require a lower-cost and more practical execution environment than Ethereum mainnet.

### Base layer principles for credits

- Base is the operational commerce layer for Platform Credits
- Credits are recorded on a shared ledger contract or credit-ledger architecture on Base
- Credits are internal platform purchasing power, not token ownership
- credits issuance follows verified payment or approved policy issuance rules
- product usage settles through credits logic rather than through raw token transfer

This makes Base the correct home for the internal economic layer of FUZE.

---

## Base as the Payout Execution Layer

Base also serves as the payout execution layer for stablecoin profit participation.

This means the profit participation system is funded and claimed on Base through the designated payout contract architecture, while holder eligibility is still derived from Ethereum FUZE balances through the snapshot framework.

### Why Base is used for payout execution

Stablecoin payout cycles require:

- contract funding
- practical holder claim behavior
- affordable gas costs
- cycle-specific payout handling
- on-chain execution visibility for claim operations

Base provides a more practical claim environment than Ethereum mainnet for these functions. This allows the profit participation system to be operationally usable without changing the role of the token layer.

### Payout layer principles

- Base is used for payout execution, not for canonical holder truth
- stablecoin claims occur on Base after cycle funding
- holders do not need the token itself to live on Base for eligibility to work
- payout execution is separate from Platform Credits logic even though both live on Base
- payout execution is separate from token transfer logic

This preserves both practicality and clarity.

---

## How the Layers Connect

The layers in FUZE are connected through policy, system logic, and controlled contract behavior rather than through the assumption that one chain must own every role.

### 1. External payment to Base credits
External payments enter through approved payment rails. After verification and normalization, value is converted into Platform Credits and issued on Base.

### 2. Ethereum holder truth to Base payout eligibility
The platform derives a holder dataset from Ethereum FUZE balances according to the published snapshot and exclusion policy. That dataset becomes the eligibility input for Base payout execution.

### 3. Treasury/accounting to Base payout funding
Treasury and accounting determine the policy-defined distributable profit for the payout cycle. Once finalized, the stablecoin pool is funded into the Base payout contract.

### 4. Platform usage to accounting/profit logic
Platform Credits are used for internal commerce. Product usage, fees, refunds, costs, and related platform economics are processed through accounting before any profit-participation funding decision occurs.

### Connection principle

The chain layers connect through:
- verified payment normalization
- ledger issuance
- snapshot generation
- eligibility datasets
- payout funding logic
- treasury/accounting policy
- public contract structure and reporting

They do not connect through conceptual shortcuts such as “token equals credits” or “credits equal profit.”

---

## Why Credits and FUZE Token Are Separate

Platform Credits and the FUZE token are separate because they serve different economic roles.

### FUZE token
The FUZE token is the ecosystem participation asset. It is connected to holder alignment, eligibility, privileges, and future governance direction.

### Platform Credits
Platform Credits are the internal consumption asset. They are used for subscriptions, usage billing, premium features, AI actions, and daily platform commerce across products.

### Why this separation is required

If token and credits were merged into one unit, several problems would emerge:

- internal product billing would become harder to standardize
- platform usage would become entangled with token participation logic
- treasury and profit interpretation would become less clear
- payout expectations could become confused with ordinary product spending
- user understanding of what is spent versus what is held would weaken

FUZE avoids this by assigning bounded roles to each layer:

- token = participation
- credits = internal consumption
- stablecoin payouts = distributable profit execution

This separation is one of the strongest trust-preserving design choices in the ecosystem.

---

## Why Sold Credits Do Not Automatically Equal Profit

One of the most important economic clarifications in the FUZE architecture is that sold Platform Credits do not automatically equal distributable profit.

### What sold credits mean
When credits are sold, this means verified value has entered the platform and has been transformed into internal purchasing power. It does not yet mean the platform has realized distributable profit.

### Why not
Before distributable profit can be determined, the following may still affect the platform’s economic outcome:

- payment processor fees
- channel costs
- stablecoin settlement costs where relevant
- AI and infrastructure costs
- support and service-delivery costs
- refunds and chargebacks
- product operating costs
- treasury and accounting policy treatment
- timing differences between issuance, usage, and recognized platform results

### Architectural consequence

The correct flow is:

1. payment is verified
2. credits are issued
3. product value is consumed
4. revenue and costs are reconciled through treasury/accounting
5. policy-defined distributable profit is finalized
6. stablecoins are funded to payout execution
7. eligible holders claim

This distinction is necessary to protect the credibility of the profit participation model. Platform Credits represent internal consumption, not automatic holder payout rights.

---

## Snapshot-Based Cross-Layer Eligibility

The layered architecture depends on a snapshot-based eligibility system that connects Ethereum holder truth to Base payout execution.

### Snapshot role
The snapshot pipeline determines:

- which Ethereum addresses are eligible
- which balances count
- which addresses are excluded
- how cycle-specific entitlement should be constructed

### Why snapshots are needed
Snapshots allow FUZE to:

- keep the token on Ethereum
- keep payout claims on Base
- avoid requiring token migration
- preserve cycle-specific auditability
- publish clearer payout-cycle logic

### Snapshot principle
Ethereum remains the canonical participation layer, and Base remains the practical payout execution layer. The snapshot system is the bridge that keeps these roles connected without collapsing them.

---

## Operational and Economic Separation Principles

The chain architecture of FUZE depends on several separation principles.

### Separation 1: Token truth vs product commerce
Token balances define participation. Credits define product spending.

### Separation 2: Product commerce vs distributable profit
Platform Credits support usage and access. Distributable profit is determined only after accounting and policy reconciliation.

### Separation 3: Canonical chain truth vs operational execution layer
Ethereum defines token truth. Base handles internal credits and payout execution.

### Separation 4: Internal ledger vs public participation asset
The internal commerce ledger should not be mistaken for the public participation token layer.

### Separation 5: Payout claim execution vs ecosystem participation identity
Stablecoin payout claims are executed on Base, but participation identity is still anchored to Ethereum holder truth.

These separation principles are part of what makes FUZE intelligible.

---

## On-Chain and Off-Chain Coordination

The layered chain model requires explicit coordination between on-chain and off-chain systems.

### On-chain roles include:
- Ethereum token contract state
- Base Platform Credits ledger/contract state
- Base payout contract state
- on-chain funding and claim execution

### Off-chain roles include:
- payment verification
- external rail normalization
- accounting treatment
- treasury reconciliation
- snapshot preparation logic
- eligibility exclusion application
- reporting generation
- support and correction workflows

### Coordination principle

On-chain systems provide enforceable contract state for token truth, credits state, and payout execution. Off-chain systems provide verification, accounting, classification, policy interpretation, and reporting. Neither side alone is sufficient for the full platform model.

This is one reason FUZE must preserve clear responsibility boundaries between technical layers and economic meaning.

---

## Security and Trust Implications of the Layered Model

The layered chain model improves security and trust in several ways.

### 1. Reduced role confusion
Each chain and contract environment has a bounded purpose.

### 2. Better economic clarity
Users, holders, and partners can understand what is held, what is spent, and what is paid out.

### 3. Better operational practicality
High-frequency internal platform commerce is not forced onto the canonical token layer.

### 4. Better payout usability
Holders can claim stablecoin payouts on a lower-cost environment.

### 5. Better transparency
The architecture itself helps explain the system’s economic flows.

### 6. Better governance structure
Different roles can be governed with role-appropriate policies and contract logic.

The layered model is not complexity for its own sake. It is controlled separation of concerns.

---

## Risks of the Layered Model

The layered architecture also introduces risks that must be handled intentionally.

### 1. Cross-layer coordination risk
The system must keep Ethereum holder truth, Base credits state, and Base payout execution aligned.

### 2. Snapshot and eligibility risk
Improper snapshot logic or exclusion handling could weaken trust in payout cycles.

### 3. Operational reconciliation risk
Credits issuance, accounting treatment, and payout funding must remain internally consistent.

### 4. User understanding risk
If the ecosystem is not explained clearly, participants may confuse token, credits, and payouts.

### 5. Technical dependency risk
The system depends on more than one chain environment and on the correctness of the connection logic between them.

These risks do not invalidate the model. They reinforce the need for strong reporting, clear policy, careful contract design, and explicit cross-layer system definitions.

---

## Transparency Requirements for the Chain Architecture

Because the chain architecture is one of FUZE’s core structural claims, it must be transparent enough to explain.

At minimum, the ecosystem should be able to make clear:

- which chain hosts the token
- which chain hosts Platform Credits
- which chain hosts payout execution
- how eligibility is determined
- how funding flows into payout cycles
- why credits are not the same as the token
- why sold credits are not the same as profit
- which contracts or registries correspond to each role

This clarity is essential because the chain model is part of the public trust surface of FUZE.

---

## Minimum Architectural Entities

At minimum, the chain architecture should recognize the following entities:

### Ethereum token entities
- FUZE token contract
- canonical holder-balance view
- snapshot reference inputs

### Base credits entities
- Platform Credits ledger/contract
- issuance and spend references
- account/workspace credit state

### Base payout entities
- stablecoin payout contract
- funded payout cycle state
- claimable entitlement records or proofs where relevant

### Cross-layer coordination entities
- snapshot dataset references
- exclusion policy references
- payout-cycle identifiers
- funding references
- reporting references

These are minimum conceptual entities. Detailed schemas and contracts are refined in downstream specifications.

---

## Open Items

The following areas are intentionally refined in downstream specifications:

- exact Base credit-ledger contract design
- exact payout-contract entitlement encoding model
- exact snapshot materialization and proof format
- exact reporting and registry schemas
- exact off-chain accounting interfaces
- exact correction and reconciliation procedures across layers

These do not weaken the canonical layered chain model established here.

---

## Closing Summary

The FUZE chain architecture is intentionally layered so that different economic roles can live in environments suited to their purpose. Ethereum mainnet is the canonical participation and token-truth layer. Base is the operational commerce layer for Platform Credits and the execution layer for stablecoin payout claims. These layers are connected through verified payment normalization, snapshot-based eligibility, treasury/accounting funding logic, and explicit policy boundaries rather than through conceptual collapse. This design preserves token credibility, enables practical platform commerce, and supports a clearer and more disciplined relationship between product usage, internal balances, and holder profit participation.
