# BASE_PLATFORM_CREDITS_LAYER_SPEC

## Purpose

This document defines the canonical Base-based Platform Credits layer of the FUZE ecosystem. Its purpose is to establish why Base is used as the operational chain for Platform Credits, what the Base credits layer owns, how it relates to off-chain payment normalization and platform billing systems, how it connects to product consumption across the ecosystem, and how it remains distinct from both the Ethereum token layer and the Base payout execution layer.

This specification is foundational because Platform Credits are the internal economic layer of FUZE. They are not a minor billing convenience. They are the shared internal unit through which subscriptions, usage billing, premium actions, AI-driven product activity, workspace consumption, and related product value flows become economically coherent across multiple applications. The Base Platform Credits layer is therefore one of the most important operational and economic components in the entire platform architecture.

---

## Scope

This specification covers:

- the canonical role of Base in the Platform Credits architecture
- why Base is used as the credits execution layer
- what the Base credits layer owns and what it does not own
- how verified payment becomes credits on Base
- how account and workspace balances are represented on the Base layer
- how credits issuance, spend, reservation, reversal, and adjustment relate to Base
- how Base credits interact with subscriptions, usage billing, AI actions, and product monetization
- the relationship between the Base credits layer and the Ethereum token layer
- the relationship between the Base credits layer and the Base payout execution layer
- transparency, security, and operational expectations for the Base credits layer

This specification does not define every detailed billing rule, every refund edge case, or every product-specific credits behavior. Those are refined in:

- `CHAIN_ARCHITECTURE_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `PAYMENT_RAILS_INTEGRATION_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `REFUND_REVERSAL_AND_ADJUSTMENT_SPEC.md`
- `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md`

---

## Design Goals

The design goals of the Base Platform Credits layer are:

1. to provide a low-cost operational chain for the internal commerce layer of FUZE
2. to support frequent issuance, deduction, and adjustment of Platform Credits in a practical way
3. to maintain one coherent internal spending system across products
4. to preserve strong separation between token participation and internal consumption
5. to make credits activity auditable, structured, and compatible with transparent reporting
6. to support both account-scoped and workspace-scoped credits balances
7. to enable subscriptions, usage billing, premium AI actions, and product feature unlocks through one shared on-chain operational layer
8. to avoid forcing high-frequency internal platform commerce onto the Ethereum token layer

---

## Non-Goals

This specification is not intended to:

- make Base the canonical source of FUZE token ownership truth
- make Platform Credits equivalent to the FUZE token
- make the Base credits layer the same thing as the stablecoin payout execution layer
- require every external payment rail to settle directly on Base in raw form
- replace off-chain payment verification, accounting, or billing logic with naive on-chain assumptions
- define Platform Credits as freely transferable market assets
- collapse product revenue, internal balances, and distributable profit into one chain meaning

---

## Canonical Layer Principle

The primary principle of the Base Platform Credits layer is:

> Base is the operational chain environment where verified platform value is normalized into FUZE Platform Credits and then consumed across products under one shared internal commerce model.

This means:

- Base is where Platform Credits are issued and recorded as internal spending power
- Base is where credits balances are represented for accounts and workspaces under platform policy
- Base supports spend, reservation, reversal, and adjustment behavior for the internal economic layer
- Base does not replace Ethereum as the participation and token-truth layer
- Base credits operations remain distinct from Base stablecoin payout execution even though both live on the same chain

This principle is essential because it gives Base a bounded and intelligible role in the FUZE ecosystem.

---

## Why Base Is Used for Platform Credits

Base is used for Platform Credits because the internal commerce layer of FUZE must support more frequent operational activity than the token participation layer.

Platform Credits are expected to power:

- subscription activation and renewal
- usage-based billing
- premium feature unlocks
- AI task billing
- report generation
- workspace consumption flows
- reversals, adjustments, and release behavior
- product-specific premium actions across multiple applications

These activities create a different operational profile from token holding. They are more frequent, more product-coupled, and more likely to require repeated mutation of balances, reservations, or settlement state.

Ethereum mainnet is used as the canonical token layer because it is the participation layer of the ecosystem. That role benefits from strong holder-truth semantics and public credibility. But it is not the most suitable place for frequent internal product commerce. Forcing daily or high-frequency operational credits activity onto Ethereum would make the internal economy less practical and less scalable.

Base gives FUZE a more suitable operational environment for:

- lower-cost credits issuance
- repeated deduction and settlement
- practical user and workspace interaction
- integration with a shared on-chain credits ledger
- a more usable internal economic system across products

Base is therefore chosen because it fits the role of the internal consumption layer more effectively than Ethereum mainnet.

---

## Canonical Role of the Base Credits Layer

The Base credits layer exists to serve as the on-chain execution and recording layer for FUZE Platform Credits.

This means the Base credits layer is responsible for the on-chain representation of:

- credits issuance after verified value enters the platform
- credits balances by owner scope
- credits spend and deduction behavior
- credits reservations and later settlement where applicable
- credits reversals, releases, and adjustments under policy
- shared credits visibility across multiple platform products

The Base credits layer is not simply a wallet balance display. It is the execution layer of the internal platform economy.

### Strategic meaning

In a multi-product platform, the internal commerce layer is a major long-term strategic asset. The Base credits layer is what allows that asset to become technically enforceable, operationally coherent, and publicly understandable enough to support long-term trust.

This matters because FUZE is not just building products. It is building an internal economic operating system that those products can share.

---

## What the Base Credits Layer Owns

The Base credits layer owns the on-chain operational meaning of:

- Platform Credits issuance records
- Platform Credits spend and deduction records
- credits reservations where adopted by the billing model
- credits releases and reversals where reflected on-chain
- scoped balances for accounts and workspaces
- commitment state for the internal consumption economy
- shared credits state that products may consume for commercial enforcement

### Clarification of “owns”

To say the Base credits layer owns these meanings does not mean Base alone explains the full commercial system. Off-chain systems still verify payments, interpret policy, apply accounting treatment, and determine product billing logic. But when the question becomes “what is the on-chain state of the internal credits economy?” the answer belongs to the Base credits layer.

This gives the platform a clean place to anchor its internal commerce state.

---

## What the Base Credits Layer Does Not Own

The Base credits layer does **not** own:

- canonical FUZE token balances
- token-holder participation truth
- raw external payment verification
- final platform accounting profit determination
- stablecoin payout entitlement logic
- token-governance meaning
- unrestricted market transfer behavior for credits
- product authorization logic by itself without entitlement and policy layers
- general-purpose treasury meaning

This distinction is important because the Base credits layer is powerful but bounded. It supports internal platform commerce. It is not the universal answer to every economic question in FUZE.

---

## Relationship to External Payment Rails

The Base credits layer is downstream from the external payment rail system.

Approved payment rails may include:

- fiat through Stripe
- supported stablecoins
- FUZE token input where policy allows
- Telegram Stars
- Apple In-App Purchase
- Google Play Billing

### Canonical flow

The correct flow is:

1. external payment event occurs
2. payment is verified and normalized under platform policy
3. appropriate credits amount and class are determined
4. Platform Credits are issued on Base
5. those credits become available for internal ecosystem usage

### Why this matters

The Base credits layer does not assume that every raw payment event on every rail should automatically create on-chain spending power. Verification and normalization are required first. This preserves the integrity of the internal economy.

The Base credits layer is therefore not a raw payment-rail mirror. It is the normalized internal spending layer that sits after verification and policy processing.

---

## Payment -> Credits -> Base Ledger Flow

The Platform Credits layer on Base is easiest to understand through the standard internal value flow:

### 1. Value enters the platform
The user or workspace pays through an approved external payment rail.

### 2. Value is verified
The payment rail is normalized and verified off-chain according to the payment integration and billing rules.

### 3. Credits are calculated
The platform determines the correct credits class, quantity, and destination scope.

### 4. Credits are issued on Base
The internal consumption unit is created or assigned through the Base credits layer.

### 5. Credits are consumed across products
Products then use these credits for subscriptions, usage actions, premium features, AI tasks, reports, and related platform services.

This flow is important because it shows that Base is the internal commerce execution layer, not the direct raw input layer for every external asset.

---

## Account-Scoped and Workspace-Scoped Credits on Base

The Base credits layer must support both account-scoped and workspace-scoped balances.

### Account-scoped usage
Used when a personal user is the commercial owner of credits and product consumption.

Examples include:
- individual subscriptions
- personal premium analysis
- solo AI generation
- personal usage-based actions

### Workspace-scoped usage
Used when a team, organization, or collaborative operating context is the commercial owner of credits and product consumption.

Examples include:
- workspace-funded AI actions
- shared business software usage
- team report generation
- operator actions billed to a workspace
- multi-user product environments

### Scope principle

The Base credits layer must preserve clarity about which owner scope holds the credits and which scope is being charged. This is essential for commercial correctness, support clarity, and product consistency across the ecosystem.

A shared on-chain credits layer is only useful if ownership scope remains explicit.

---

## Credit Classes on Base

The Base credits layer should support distinct credit classes, reflecting the wider Platform Credits architecture.

At minimum, it should be able to represent:

- paid credits
- bonus credits
- restricted credits
- policy-defined future classes if added under governance

### Why classes matter on Base

The on-chain operational layer must preserve the semantic differences between:
- credits purchased directly,
- credits issued as rewards or compensation,
- and credits restricted to defined contexts.

Without class-aware credits logic, the platform would lose flexibility around:
- expiry policy
- restricted usage
- support compensation
- spending priority
- reward-campaign controls

Base is therefore not only the place where balances live. It is also the place where the class-sensitive internal economy becomes operationally structured.

---

## Spend and Settlement Behavior on Base

The Base credits layer must support spend and settlement behavior that aligns with the shared billing and ledger model of FUZE.

### Spend behavior may include:

- direct immediate spend
- reservation before high-cost action
- partial settlement after job completion
- release of unused reserved amount
- policy-driven deductions for subscription or usage events
- explicit reversal or adjustment entries where applicable

### Examples

- a QTB premium analysis request may deduct credits
- a HerHelp generation job may reserve credits, then settle final usage after completion
- a Botmad workflow scan may deduct credits according to premium action rules
- a workspace subscription renewal may consume workspace-held paid credits

### Settlement principle

The Base layer should support credits state transitions that are legible and reconcilable. Spend should not appear as unstructured balance mutation with no reason or lineage. The Base credits layer works best when linked clearly to the credits ledger and settlement architecture.

---

## Relationship to the Credits Ledger

The Base credits layer and the canonical credits ledger are closely connected but not conceptually identical.

### Credits ledger owns:
- semantic business record of credits mutation
- reason codes and mutation lineage
- reservation and release logic
- linkage to billing and payment reference
- the append-oriented truth of the internal credits economy

### Base credits layer owns:
- on-chain operational representation of the credits economy
- committed credits state
- chain-visible issuance and mutation record where applicable
- operational environment for the internal spending unit

### Principle

The platform ledger explains why credits changed.  
The Base layer records the operational on-chain state of those credits.

This distinction is important because on-chain state alone is not enough to explain the full economic history of the platform, but it is still a critical part of the internal commerce architecture.

---

## Relationship to Product Consumption

The Base credits layer exists so that products can operate inside one shared internal consumption economy.

Products such as:

- QTB
- AIMM
- ZAGA
- AIE
- HerHelp
- Botmad
- and future products

should be able to consume Platform Credits without each creating a separate internal asset model.

### Product integration implications

Products may differ in:
- pricing logic
- included versus premium actions
- subscription structure
- action-based metering
- workspace versus account billing model

But they should share:
- the same core credits unit
- the same general issuance logic
- the same general deduction model
- the same internal economic vocabulary

This is one of the core reasons the Base credits layer exists. It allows product diversity without internal economic fragmentation.

---

## Relationship to Subscriptions and Usage Billing

The Base credits layer is tightly connected to the platform subscriptions and usage billing architecture.

### Subscriptions
Recurring plans may activate, renew, downgrade, or lapse based on credits-backed logic under policy.

### Usage billing
Metered actions may deduct or reserve credits when the product value is consumed.

### Premium AI actions
AI-heavy or advanced actions may consume credits directly or after reservation and settlement.

### Workspace billing
A workspace may use a shared Base-held credits balance to fund product activity by authorized members.

### Important boundary

The Base credits layer is not the same as the billing-policy layer. Billing determines what should be charged and under what rules. The Base credits layer is where the internal consumption unit is represented and consumed operationally after those rules are applied.

---

## Relationship to the Ethereum Token Layer

The Base Platform Credits layer is intentionally separate from the Ethereum token layer.

### Ethereum token layer
Owns:
- canonical token truth
- holder balances
- participation identity
- snapshot source for eligibility

### Base credits layer
Owns:
- internal spending unit
- product-consumption balances
- subscriptions and usage-linked commercial state
- on-chain credits execution

### Why this matters

A user may hold FUZE on Ethereum and have no Platform Credits on Base.  
A user or workspace may purchase and spend Platform Credits on Base without holding FUZE on Ethereum.  
A product may charge Platform Credits for usage without that usage meaning anything about token-holder rank or payout eligibility.

This separation is necessary because FUZE is designed with distinct roles for participation and consumption.

---

## Relationship to the Base Payout Execution Layer

The Base credits layer is also separate from the Base payout execution layer, even though both live on Base.

### Base credits layer
Represents internal platform spending power.

### Base payout execution layer
Represents stablecoin profit participation claims after cycle funding.

### Why separation is necessary

If the internal commerce layer and the payout layer were blurred together, the ecosystem would inherit multiple problems:
- internal spending could be misread as holder reward rights
- sold credits could be confused with distributable profit
- payout assets could be confused with spendable product balances
- treasury and accounting logic would become harder to explain

### Principle

Shared chain does not mean shared economic meaning.  
Base hosts more than one bounded layer in FUZE.  
The credits layer and the payout layer remain separate even though they share the same execution environment.

This is one of the clearest examples of FUZE’s role-separation architecture.

---

## Transferability and Internal Movement Rules

The Base credits layer should preserve the non-market nature of Platform Credits.

Platform Credits are intended to be:

- account-bound
- workspace-bound
- transferable only through tightly controlled internal pathways
- unsuitable for open speculative circulation

### Controlled internal movement may include:
- account to workspace allocation
- workspace reassignment under policy
- support-driven migration
- internal correction or rebinding of ownership scope

### Disallowed default behavior includes:
- unrestricted peer-to-peer transfer
- open-market liquidity behavior
- treating Platform Credits as a public speculative token

This rule is critical because the internal spending layer must remain clearly distinct from the ecosystem participation token.

---

## Transparency Expectations for the Base Credits Layer

Because Platform Credits are a core part of the internal economy of FUZE, the Base credits layer should support meaningful transparency.

At minimum, the ecosystem should be able to explain:

- that Platform Credits live on Base
- that credits are the internal consumption unit
- that credits are distinct from the FUZE token
- that credits are distinct from stablecoin payout claims
- how verified payments turn into credits
- how credits balances are used across products
- how reversals, restrictions, and classes affect the internal economy

This transparency matters because the Base credits layer is one of the most distinctive structural components of the platform. If it is not explained clearly, users may confuse spending power with participation or profit.

---

## Governance and Administrative Expectations

The Base credits layer should be governed with role-appropriate control discipline.

### Governance-sensitive actions may include:

- allowed issuance pathways
- credit-class policy changes
- restricted-usage logic changes
- controlled migration or rebinding operations
- pause or emergency controls where necessary
- contract or ledger-role updates where applicable

### Governance principles

- administrative control should be explicit
- sensitive actions should remain policy-bounded
- role separation should exist between commercial policy, accounting interpretation, and on-chain execution control
- updates or control changes should be auditable and compatible with shared governance expectations

The Base credits layer is a high-trust operational layer. Its control model must reflect that importance.

---

## Security Principles for the Base Credits Layer

The Base credits layer should follow strong security and operational integrity principles.

### Key principles include:

#### Internal Asset Clarity
The layer should clearly represent Platform Credits and not drift into ambiguous token-like behavior.

#### Mutation Discipline
Credits issuance, spend, reversal, and adjustment should occur only through defined pathways.

#### Scope Integrity
Account and workspace ownership must remain explicit.

#### Commercial Safety
Retry, correction, and reversal behavior must not create silent duplication or ambiguous balances.

#### Separation from Payout Layer
Even though both layers live on Base, their roles must remain distinct.

#### Observability
Operators should be able to monitor issuance volume, spend patterns, failures, reversals, and reconciliation issues.

These principles matter because internal economic systems often fail through ambiguity rather than through visible spectacular collapse. FUZE is designed to avoid that.

---

## Risks and Failure Considerations

The Base credits layer introduces several important risks that must be managed.

### 1. Credits-Meaning Confusion
Users may confuse Platform Credits with the FUZE token or with profit-participation rights if the architecture is not explained clearly.

### 2. Commercial Mutation Risk
If issuance, reversal, or spend logic is too flexible or poorly controlled, the internal economy may lose coherence.

### 3. Cross-Scope Billing Risk
If account and workspace ownership are not handled carefully, credits may be charged to the wrong commercial context.

### 4. Reconciliation Risk
The Base credits layer must stay consistent with off-chain payment verification, credits ledger records, and reporting systems.

### 5. Cross-Layer Confusion
Because Base also hosts the payout execution layer, the platform must explain clearly that the two roles are separate.

These risks reinforce the need for strong documentation, strong control logic, and strong transparency around the Base credits architecture.

---

## Minimum Architectural Entities

At minimum, the Base Platform Credits layer should recognize the following entities:

### Base Credits Contract / Ledger Entities
- Platform Credits contract or ledger identity
- credit class state
- scoped balance state
- issuance and deduction references

### Ownership and Scope Entities
- account-scoped credit owner
- workspace-scoped credit owner
- ownership rebinding or migration references where relevant

### Settlement and Mutation Entities
- spend references
- reservation references where applicable
- reversal and release references
- adjustment references

### Coordination Entities
- payment verification linkage
- billing and subscription linkage
- reporting references
- governance/control references

These are minimum conceptual entities. More detailed schemas and contract models are refined in downstream specifications.

---

## Open Items

The following areas are intentionally refined in downstream specifications:

- exact shared credit-ledger contract structure on Base
- exact scope representation model for accounts and workspaces
- exact reservation and release mechanics
- exact restricted-credit enforcement mechanics
- exact reporting schema for Base credits activity
- exact emergency and pause controls for credits-layer operations

These do not weaken the canonical Base Platform Credits layer model established here.

---

## Closing Summary

The Base Platform Credits layer is the operational internal commerce layer of the FUZE ecosystem. It is where verified external value becomes Platform Credits, where account- and workspace-scoped balances are represented, and where internal product consumption is executed across subscriptions, usage billing, premium features, AI actions, and related platform services. It is intentionally separate from the Ethereum token layer and intentionally separate from the Base payout execution layer. By giving Platform Credits a low-cost, bounded, and clearly explained on-chain home on Base, FUZE creates one of the most important structural foundations of its multi-product platform economy.
