# PLATFORM_CREDITS_SPEC

## Purpose

This document defines the canonical Platform Credits architecture of FUZE. Its purpose is to establish the source-of-truth specification for what a FUZE Platform Credit is, how credits are issued, recorded, spent, reversed, expired, scoped, reported, and governed across the platform ecosystem.

This specification is foundational because Platform Credits are the internal economic layer of FUZE. They sit between external payment rails and internal product consumption, and they allow multiple products, multiple payment inputs, multiple pricing styles, and multiple usage patterns to operate inside one coherent commercial framework.

This document is therefore not just a billing specification. It is one of the core economic architecture specifications of FUZE.

---

## Scope

This specification covers:

- the canonical definition of Platform Credits
- the role of credits inside the FUZE economic architecture
- credit classes
- issuance paths
- balance ownership and scope
- credit-ledger principles
- spend rules
- refund, reversal, and adjustment rules
- expiry policy
- account-bound versus workspace-bound behavior
- transfer restrictions
- audit, reporting, and governance implications
- boundary rules between credits, token, revenue, and profit participation

This specification does not define the full payment-verification logic, invoice detail, chain contract bytecode, or complete payout accounting treatment. Those are refined in:

- `PAYMENT_RAILS_INTEGRATION_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `INVOICING_AND_RECEIPTS_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `REFUND_REVERSAL_AND_ADJUSTMENT_SPEC.md`
- `BASE_PLATFORM_CREDITS_LAYER_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`

---

## Design Goals

The design goals of the Platform Credits system are:

1. to provide one shared internal economic unit across the FUZE ecosystem
2. to normalize many external payment rails into one internal consumption system
3. to support subscriptions, usage billing, AI actions, feature unlocks, and cross-product monetization through one unified balance model
4. to preserve strict separation between credits, FUZE token, and stablecoin payouts
5. to support transparent and auditable issuance, spending, reversal, and reporting
6. to support both account-scoped and workspace-scoped commercial behavior
7. to make the platform easier to expand commercially as new products are added
8. to provide a policy-driven economic system that remains operationally practical on Base

---

## Non-Goals

This specification is not intended to:

- make Platform Credits the same thing as the FUZE token
- make credits a freely tradable market asset
- make credits a governance asset
- make credits a direct equity-like or profit-right instrument
- allow products to create independent hidden balance systems outside the credits model
- blur credit issuance into automatic profit or payout entitlement
- define an unconstrained discretionary mint system

These non-goals are necessary to preserve economic clarity.

---

## Canonical Credits Principle

The primary principle of the FUZE credits system is:

> Platform Credits are the internal consumption unit of the FUZE ecosystem, issued under platform policy after verified value intake or approved platform action, and used to consume products and services across the platform without replacing the FUZE token or the stablecoin payout system.

This means:

- credits are internal platform purchasing power
- credits are not the token
- credits are not stablecoin profit participation
- credits are not general treasury assets
- credits are the shared unit through which products meter and collect internal consumption value

This principle must remain invariant across all products and integrations.

---

## Canonical Definition of a Credit

A **FUZE Platform Credit** is a platform-denominated internal balance unit that represents eligible purchasing power inside the FUZE ecosystem.

A Platform Credit is:

- tied to a canonical account or workspace context
- issued after verified payment or approved non-payment issuance
- usable under defined product and platform policies
- recorded in the platform credits ledger model
- committed to an operational on-chain credits ledger on Base where applicable
- separate from both FUZE token ownership and stablecoin payout claims

A Platform Credit is not:

- a public market token
- a holder-participation asset
- a governance right
- a reserve bucket
- a stablecoin claim
- a direct measure of distributable profit

This explicit definition must be preserved in user-facing explanations, product logic, and downstream specs.

---

## Why Platform Credits Exist

Platform Credits exist because FUZE is a multi-product platform ecosystem with:

- multiple payment inputs
- multiple pricing models
- multiple products
- multiple usage patterns
- AI-driven metered actions
- account and workspace commercial contexts
- a need for internal economic consistency

Without credits, each product would tend to develop its own internal commercial logic. That would fragment the platform economically even if the identity and product layers were shared.

Credits solve that problem by creating one internal commercial language.

They allow FUZE to:

- accept external value through many payment rails
- convert verified value into one internal unit
- meter usage and subscriptions consistently
- support cross-product balances
- track internal consumption coherently
- preserve a clean distinction between consumption and participation

Credits therefore exist because a platform company requires an internal economic layer, not merely many disconnected product billing systems.

---

## Payment -> Credits -> Usage -> Ledger Model

The canonical credits flow is:

1. **External payment or approved issuance event occurs**
2. **Platform verifies and normalizes the event**
3. **Credits are issued under policy**
4. **Credits are recorded in the platform ledger model**
5. **Credits are committed to the Base credits layer where applicable**
6. **Products consume credits through platform charging logic**
7. **Spend, reversal, reporting, and reconciliation flows update the economic state**

This model is important because it preserves clean separation between:

- external money in
- internal purchasing power
- product consumption
- revenue recognition
- profit determination
- payout execution

Each of these is related, but they are not the same thing.

---

## Supported Payment Inputs

Platform Credits are designed to accept value from the following approved input categories:

- fiat / Stripe
- stablecoins such as USDT under approved rails
- FUZE token under approved conversion policy
- Telegram Stars
- Apple In-App Purchase
- Google Play Billing
- future approved payment rails

These are input rails, not the internal economic layer itself.

A key architectural rule is:

> payment rail diversity at the edge must not create internal economic fragmentation at the center.

All approved inputs must therefore normalize into the Platform Credits system rather than causing product-specific private balance systems to emerge.

---

## Credit Classes

FUZE credits must support multiple credit classes because credits do not all enter the ecosystem under the same economic conditions.

### 1. Paid Credits

Paid Credits are issued after a verified payment event.

They are the strongest and most standard credit class because they represent direct user-purchased platform value.

Paid Credits should generally be:

- non-expiring by default
- broadly spendable according to product and platform rules
- directly tied to verified payment source and issuance records

### 2. Bonus Credits

Bonus Credits are issued through approved non-payment reward paths.

Examples may include:

- promotional rewards
- referral programs
- loyalty rewards
- service recovery credits
- ecosystem campaigns
- holder incentive-linked platform campaigns where appropriate

Bonus Credits may be:

- expiring
- use-limited
- restricted by product or campaign policy

### 3. Restricted Credits

Restricted Credits are issued for bounded contexts.

Examples may include:

- one-product-only credits
- one-feature-only credits
- duration-bound campaign credits
- enterprise-allocated use-specific balances
- internal operational or compensation credits with scope restrictions

Restricted Credits allow FUZE to support specialized commercial structures without breaking the general credits model.

### Credit Class Rule

Every credit balance entry must be class-identifiable so the system can apply class-aware spend, reversal, expiry, and reporting rules.

---

## Balance Scopes

Credits must support scope-aware balance ownership.

### 1. Account-Scoped Credits

Credits owned by an individual canonical account.

These are appropriate for:

- individual users
- personal product usage
- individual purchases
- self-service access patterns

### 2. Workspace-Scoped Credits

Credits owned by a workspace context.

These are appropriate for:

- team subscriptions
- shared product usage
- business or organization-level balances
- multi-user operational environments
- workspace-level usage billing

### Scope Principles

- account-scoped and workspace-scoped credits must remain distinguishable
- products must know which scope is being charged
- billing and reporting must preserve scope context
- transfers or reallocation between scopes, if allowed, must be explicit and auditable

This scope distinction is essential for team-oriented products and future enterprise behavior.

---

## Canonical Credits Ledger Model

Platform Credits require one canonical ledger model.

The credits ledger must support at least the following economic state categories:

- issuance
- spend
- reversal
- refund-related correction
- adjustment
- expiry
- migration or scope reassignment where approved
- on-chain commitment status where applicable

The ledger is the canonical platform record of how credits move through the ecosystem.

### Ledger Principles

1. Every credits mutation must be reason-coded.
2. Every credits mutation must be attributable to a source action or policy event.
3. Every credits mutation must be auditable.
4. Derived reporting views may summarize ledger behavior, but may not redefine it.
5. On-chain commitment state does not eliminate the need for a platform-side canonical ledger model.

The credits ledger is refined further in `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`, but the canonical requirement is established here.

---

## Issuance Rules

Credits may only be issued through approved issuance paths.

### 1. Paid Issuance

Issued after verified payment intake.

Requirements:

- approved payment rail
- successful verification
- normalization into platform value model
- issuance reason and source tracking
- scope assignment to account or workspace

This is the primary issuance path.

### 2. Reward Issuance

Issued through approved non-payment reward policy.

Examples:

- promotions
- referrals
- service recovery
- holder incentive campaigns mapped into credits, if approved
- ecosystem growth programs

Requirements:

- policy-defined program
- bounded eligibility
- explicit class assignment
- audit trail

### 3. Adjustment Issuance

Issued for correction or platform-controlled operational reasons.

Examples:

- finance correction
- enterprise settlement adjustment
- post-support remediation
- operational reconciliation fix

Requirements:

- restricted authority
- strong audit trail
- reason code
- approval path where required

### Issuance Prohibitions

Credits must not be issued through:

- arbitrary operator discretion without audit
- product-local hidden balance creation
- unsupported payment assumptions
- token holding alone without approved issuance policy
- reporting-side or UI-side convenience mutation

---

## Spend Rules

Credits may be spent only through approved platform or product actions.

### Valid spend examples

- subscription activation or renewal
- seat activation
- usage-based deduction
- AI action metering
- premium feature unlock
- report generation
- workflow execution
- add-on purchase
- product-scoped service usage

### Spend evaluation requirements

At minimum, a valid spend action must check:

1. sufficient balance exists
2. the selected balance scope is valid
3. the credit class is eligible for the requested use
4. the product or feature is properly mapped to a charge reason
5. entitlement and policy checks pass
6. the spend event can be recorded and reconciled

### Spend-order policy

If multiple credit classes exist in one scope, the platform may define deterministic spend priority rules, such as:

- expiring bonus credits first
- restricted credits if specifically matched to the action
- then paid credits

If spend-order logic exists, it must be explicit and deterministic.

### Product rule

Products may define what they charge for, but not invent incompatible deduction semantics outside the platform credits system.

---

## Reservation and Consumption Model

Where product workflows require it, FUZE may support a two-step commercial model:

1. **reservation / authorization of credits**
2. **final consumption / settlement of credits**

This may be useful for:

- long-running AI jobs
- batch processing
- queued or asynchronous work
- workflows where a charge is known to be likely but final settlement occurs later
- support of cancellation or failure-aware refund logic

If reservation is supported, the model must preserve:

- clear distinction between reserved and finally spent credits
- release behavior when work fails or is canceled
- full audit visibility
- deterministic settlement transitions

This should be implemented only where it improves commercial correctness.

---

## Refund, Reversal, and Adjustment Rules

Credits must support explicit correction logic.

### Refund-related reversal

If an external payment is refunded and associated credits remain unused, the unused portion may be reversed or burned according to policy.

### Partial-use rule

If some credits have already been consumed, only the unused portion should normally be directly refundable through credits reversal tied to the original payment path. Consumed platform value should not be treated as unused balance.

### Channel-aware correction

Credits issued from Apple, Google, Telegram Stars, or other rails may need rail-specific correction handling after external refund or revocation events.

### Chargeback / fraud handling

If value intake is later invalidated, the platform may:

- remove unused credits
- create negative-balance logic or debt state where policy allows
- restrict account or workspace access
- escalate to fraud review

### Support-issued compensation

Compensation credits should be modeled as explicit issuance events, not as silent hidden ledger edits.

### Adjustment rule

Every adjustment must preserve economic traceability. There must never be a “mystery balance change” in the credits system.

---

## Expiry Policy

FUZE must support class-aware expiry rules.

### Paid Credits

Paid Credits should be non-expiring by default.

This is the recommended trust-preserving standard.

### Bonus Credits

Bonus Credits may expire where campaign or reward policy justifies it.

### Restricted Credits

Restricted Credits may be expiring or non-expiring depending on their use case, but the rule must be explicit.

### Expiry Principles

- expiry must be visible in policy and user-facing expectations
- expiry must be deterministic
- expiry must be auditable
- expiry processing must be reflected in ledger state
- expiry should not silently apply to paid credits unless a future policy explicitly changes the standard

The platform should favor clarity and trust over hidden balance erosion.

---

## Account-Bound and Transferability Rules

Platform Credits are account-bound or workspace-bound internal assets.

### Default rule

Credits are not freely transferable market assets.

### Why

This protects the economic role of credits as:

- internal purchasing power
- non-speculative platform unit
- controlled billing and usage instrument
- non-governance asset
- non-public market token

### Controlled internal movement may be allowed

Examples may include:

- account-to-workspace reallocation under approved rules
- enterprise or admin migration
- support-approved correction
- controlled scope reassignment during billing changes or workspace restructuring

### Prohibited behavior

Credits should not behave like:

- public exchange tokens
- peer-to-peer resale assets
- informal over-the-counter transferable balances
- shadow token markets for product consumption

This rule is essential to keeping the token and credits layers distinct.

---

## Relationship to FUZE Token

The Platform Credits system must remain fully distinct from the FUZE token.

### FUZE token is:
- the ecosystem participation asset
- canonical on Ethereum
- used for holder alignment, eligibility, ecosystem status, and future governance direction

### Platform Credits are:
- the internal consumption asset
- operational on Base
- used for subscriptions, usage, AI actions, product access, and internal platform spending

### Architectural rule

No product, UI, report, or operator should imply that credits and token are the same asset class.

This distinction must remain visible in:

- UX
- accounting
- reporting
- pricing logic
- governance logic
- contract architecture

---

## Relationship to Revenue and Profit

Credits must not be confused with revenue or profit.

### Credits are not revenue by themselves

Credits represent internal spendable platform value after issuance. They are not identical to realized recognized revenue.

### Revenue is not profit by itself

Revenue must be reconciled against:

- channel fees
- infrastructure costs
- AI costs
- refunds
- chargebacks
- operating expenses
- treasury and accounting policy

### Profit is not payout until funded

Only after policy-defined distributable profit is finalized and stablecoins are funded into the payout system does stablecoin profit participation become real claimable value.

This separation is one of the strongest safeguards in FUZE’s economic architecture.

---

## Relationship to Products

Products consume the Platform Credits system, but do not own it.

### Products may:

- define chargeable actions
- define usage meters
- define product-specific spend reasons
- define product-specific entitlement rules on top of platform billing
- present credit balances and spend history in product UX where appropriate

### Products may not:

- create hidden product-local credit balances
- redefine paid/bonus/restricted classes independently
- override platform spend policy
- create alternate settlement semantics outside approved platform patterns
- mutate credits without going through the credits domain

This rule is one of the most important commercial integrity controls in the FUZE platform.

---

## Relationship to Base Credits Layer

Base is the operational chain for Platform Credits.

This means the credits system must support:

- on-chain commitment of credits state where policy requires
- clear relation between platform ledger truth and Base execution/commitment truth
- event visibility for committed credits changes where applicable
- reconciliation between platform-side and chain-side records

However, Base commitment does not remove the need for the platform-side credits domain.

The platform still owns:

- issuance policy
- spend policy
- correction policy
- balance scope semantics
- integration with billing and product logic

The chain layer owns committed operational state, not the whole business meaning of the credits economy.

---

## Governance and Control Implications

Because credits are the internal economic layer of FUZE, they require explicit control boundaries.

### Sensitive credits actions include:

- adjustment issuance
- corrective reversal
- balance repair
- product charging policy changes
- class-policy changes
- restricted-credit campaign rules
- migration or scope reassignment for balances
- operational override of stuck or disputed credit states

### Governance rules

- sensitive actions must be role-restricted
- high-impact actions must be auditable
- selected actions may require approval workflows
- product teams must not gain unrestricted powers over the credits system
- policy changes affecting credits meaning should be treated as governance-sensitive platform decisions

Credits are a platform trust surface, not just an implementation detail.

---

## Audit and Reporting Requirements

The credits domain must support strong auditability.

### At minimum, audit and reporting must support visibility into:

- issuance source
- issuance class
- scope owner
- spend reason
- reversal reason
- expiry behavior
- adjustment action
- operator/admin intervention where applicable
- commitment status to Base where relevant
- product attribution for usage and spend

### Reporting outputs should support:

- account-level credits views
- workspace-level credits views
- platform-wide issuance/spend reporting
- reconciliation between ledger and chain commitment state
- transparency-facing summaries where policy allows

A credits system without strong reporting is incompatible with FUZE’s transparency-first platform model.

---

## Minimum Data Model Requirements

At minimum, the Platform Credits system must support semantic representation of:

### Credit Balance Context
- owner type (account or workspace)
- owner ID
- credit class
- available balance
- reserved balance where supported
- expired balance where relevant

### Credit Ledger Entry
- ledger entry ID
- owner scope
- entry type
- class
- amount
- source reason
- related payment/billing/product reference where relevant
- created_at
- actor or system source
- commitment status where applicable

### Credit Policy Context
- class rule
- expiry rule
- spend eligibility rule
- product restriction rule where applicable
- policy version or effective rule set reference

These are minimum semantic requirements. The full data model is refined downstream.

---

## Edge Cases and Failure Handling

### Payment verified but Base commitment delayed
Credits issuance may be in a pending commitment state. The platform must distinguish verified issuance intent from final committed operational state.

### Product tries to charge credits twice
Credits consumption must be idempotency-aware. Duplicate spend attempts must not silently double-charge.

### Refund arrives after partial usage
Only unused paid value should normally be directly reversible; consumed value must follow policy-aware correction handling.

### Workspace billing owner changes
Credits scope remains explicit. Ownership and charging context changes must be auditable and policy-safe.

### Bonus credits expire during pending async workflow
Reservation, eligibility, or spend-order policy must determine the correct handling. The platform must not produce ambiguous silent balance outcomes.

### Product-local cache shows old balance
Cached state is non-canonical. The credits domain and ledger remain canonical.

### Support operator makes manual correction
The action must be reason-coded, scoped, auditable, and bounded by permission.

---

## Open Items

The following areas are intentionally refined in downstream specifications:

- exact Base contract structure for credits commitment
- exact reservation versus immediate-spend policy by product/action type
- exact negative-balance policy for fraud or chargeback cases
- exact product-by-product pricing and spend mappings
- exact user-facing display model for class-priority and expiry
- exact reconciliation cadence between platform ledger and chain commitment layer

These do not weaken the canonical credits model established here.

---

## Closing Summary

Platform Credits are the internal economic layer of FUZE. They provide the unified spending unit through which verified value intake is transformed into structured platform purchasing power across multiple products. Credits support subscriptions, usage billing, AI actions, premium features, and shared product consumption through one coherent system. They are class-aware, scope-aware, auditable, policy-driven, and operationally committed on Base. Just as importantly, they remain clearly separated from the FUZE token and from stablecoin profit participation. This separation is what allows FUZE to build a commercial system that is scalable, coherent, and trustworthy across a growing multi-product platform ecosystem.
