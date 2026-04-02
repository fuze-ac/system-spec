# PROFIT_PARTICIPATION_SYSTEM_SPEC

## Purpose

This document defines the canonical profit participation system of the FUZE ecosystem. Its purpose is to establish how FUZE connects policy-defined distributable platform profit to eligible FUZE token holders through a stablecoin-based, cycle-driven payout architecture while preserving strong separation between token participation, internal product consumption, treasury accounting, and payout execution.

This specification is foundational because profit participation is one of the most trust-sensitive and externally visible parts of the FUZE model. If it is vague, discretionary, or structurally blurred, it weakens the credibility of the wider platform. If it is explicit, policy-bounded, cycle-based, and transparently reported, it becomes one of the clearest demonstrations of FUZE’s platform-first and transparency-first architecture.

---

## Scope

This specification covers:

- the canonical role of profit participation within the FUZE ecosystem
- the distinction between platform revenue, Platform Credits, accounting profit, and distributable profit
- the stablecoin-based payout model
- cycle-based payout logic
- eligibility derived from Ethereum-based FUZE holder snapshots
- the relationship between treasury/accounting, snapshot pipelines, and Base payout execution
- policy boundaries and governance expectations for profit participation
- transparency, reporting, and payout-ledger requirements
- correction, edge cases, and failure-handling principles

This specification does not define every accounting rule, every vault policy, or every payout-contract implementation detail. Those are refined in:

- `CHAIN_ARCHITECTURE_SPEC.md`
- `ETHEREUM_TOKEN_LAYER_SPEC.md`
- `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md`
- `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `TREASURY_AND_RESERVE_ARCHITECTURE_SPEC.md`
- `PAYOUT_LEDGER_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`

---

## Design Goals

The design goals of the FUZE profit participation system are:

1. to align eligible token holders with real platform success rather than with vague token narrative
2. to preserve explicit separation between token participation, internal product consumption, and payout execution
3. to ensure payouts are based on policy-defined distributable profit rather than raw gross inflows
4. to make payout eligibility deterministic, cycle-based, and auditable
5. to use a practical and transparent stablecoin claim model
6. to reduce ambiguity around funding, eligibility, claim execution, and reporting
7. to support long-term platform credibility through bounded governance and transparency discipline
8. to make profit participation strong enough for public scrutiny by holders, partners, and investors

---

## Non-Goals

This specification is not intended to:

- define all gross platform revenue as automatically distributable
- make Platform Credits sales automatically equal holder payout rights
- make the FUZE token itself the payout asset
- make profit participation continuous, automatic, or discretionary without cycle logic
- treat token ownership as identical to product entitlement
- replace treasury, accounting, or reserve architecture with payout rhetoric
- imply that every holder-aware feature uses the same rules as profit participation

---

## Canonical Profit Participation Principle

The primary principle of the FUZE profit participation system is:

> profit participation is a policy-defined stablecoin payout system that distributes a finalized portion of real platform profit to eligible FUZE holders through cycle-based execution, without confusing token ownership, internal consumption, accounting, or payout mechanics.

This means:

- the FUZE token represents ecosystem participation
- Platform Credits represent internal platform consumption
- accounting and treasury determine whether distributable profit exists
- stablecoin payout cycles are funded only after that determination
- eligible holders claim through a separate payout execution system
- each stage remains structurally distinct and explainable

This principle is one of the clearest expressions of FUZE’s economic role separation.

---

## Why FUZE Uses a Profit Participation System

FUZE uses a profit participation system because it is building a platform company with real products, real platform usage, and real internal economic flows. In that environment, holder alignment becomes stronger when it is connected to actual platform outcomes rather than to symbolic narrative alone.

Many token ecosystems describe alignment abstractly. Holders are told they are part of the ecosystem, yet the relationship between product usage, revenue, reserves, and participation remains vague. FUZE is designed to avoid that pattern by creating a structured path from platform performance to holder participation.

That path is not immediate and not automatic. Product usage produces commercial outcomes through subscriptions, usage billing, and Platform Credits. Those flows enter treasury and accounting systems. Costs, reversals, and policy treatment are applied. Only then can policy-defined distributable profit be determined. Once finalized, that amount becomes the basis of a funded stablecoin payout cycle for eligible holders.

This architecture matters because it creates a stronger form of alignment:

- users consume products through the internal platform economy
- the platform builds real economic activity
- holders participate at the ecosystem level
- payouts occur only after accounting and policy reconcile the underlying economics

The result is a more serious model than one in which token holders are promised alignment without a credible path from product success to holder participation.

---

## Core Concepts

### Profit Participation
The policy-defined process by which a finalized portion of platform profit is distributed to eligible FUZE holders.

### Distributable Profit
The portion of profit that treasury and accounting determine may be allocated to a payout cycle under policy.

### Payout Cycle
A bounded operational cycle in which a defined stablecoin amount is funded and made claimable by eligible holders.

### Eligibility Dataset
The cycle-specific holder dataset derived from Ethereum FUZE balances after policy-defined treatment and exclusions.

### Entitlement
The cycle-specific payout share attributed to an eligible holder according to the payout rules of that cycle.

### Payout Asset
The stablecoin used to execute claimable profit participation on Base.

### Claim Execution
The act of an eligible holder claiming stablecoin payout from the funded payout contract on Base.

### Policy Boundary
The set of rules that define what may count toward distribution, who may participate, and how payout execution is governed.

---

## Canonical Role of the Profit Participation System

The profit participation system exists to do the following:

1. define how platform profit may become holder-facing stablecoin payouts
2. preserve the distinction between platform consumption and holder participation
3. connect Ethereum-based holder truth to Base-based payout execution
4. make payout cycles explicit rather than informal
5. provide transparency around funding, eligibility, and execution
6. reinforce that FUZE is a platform company with policy-based economic discipline

The profit participation system is not intended to replace treasury logic, billing logic, or product monetization logic. It sits downstream of those systems and translates finalized distributable profit into holder-facing payout execution.

---

## Economic Role Separation

The profit participation system depends on explicit separation between several economic roles.

### FUZE Token
The ecosystem participation asset held on Ethereum mainnet.

### Platform Credits
The internal consumption unit used for subscriptions, usage billing, and premium actions across products.

### Product Revenue and Usage
The platform-commercial outcomes generated by product activity.

### Treasury and Accounting
The systems that determine the actual profit position of the platform and whether a policy-defined distributable amount exists.

### Stablecoin Payouts
The funded cycle-based claim system used to distribute a portion of finalized profit to eligible holders.

### Why this separation matters

Without this separation, the platform would accumulate multiple forms of confusion:

- token ownership might be mistaken for spending power
- Platform Credits sales might be mistaken for immediate profit
- product usage might be mistaken for direct payout rights
- treasury activity might be mistaken for discretionary reward behavior

FUZE avoids this by keeping each role bounded and explicit.

---

## Stablecoin-Based Payout Model

FUZE uses a stablecoin-based payout model rather than a token-denominated reward model.

### Why stablecoins are used

Stablecoins are used because the payout layer is meant to represent realized, policy-defined profit participation rather than recycled token emissions or vague token utility loops.

This creates several advantages:

- the payout asset is clearly distinct from the participation asset
- holders receive a more legible payout medium
- accounting discipline becomes stronger
- profit distribution is easier to explain
- the system avoids blurring payout logic into token price narrative

### Important distinctions

The stablecoin payout asset is not:

- the FUZE token
- Platform Credits
- a general product balance
- a substitute for treasury reserves in general

It is the specific asset used for a funded payout cycle under policy-defined profit participation logic.

---

## Relationship to Platform Revenue and Platform Credits

One of the most important rules in FUZE is that sold Platform Credits do not automatically equal distributable profit.

### Correct economic sequence

1. external payment is verified
2. value is normalized into Platform Credits
3. credits are consumed across products
4. revenue, costs, reversals, and platform obligations are reconciled
5. treasury and accounting determine policy-defined distributable profit
6. a stablecoin payout cycle may be funded
7. eligible holders claim through the payout system

### Why this matters

Credits are internal consumption units. Their sale or issuance reflects internal purchasing power entering the ecosystem. That does not automatically mean the platform has realized profit available for distribution.

Costs and deductions may still apply, including:

- payment-rail fees
- AI and infrastructure costs
- support and service costs
- refunds and chargebacks
- platform operating expenses
- other policy-defined treasury and accounting treatments

This distinction is essential to the credibility of the profit participation model.

---

## Distributable Profit Determination

The profit participation system begins only after distributable profit has been determined.

### Distributable profit principles

- distributable profit is policy-defined, not assumed
- gross revenue is not identical to distributable profit
- treasury and accounting must apply cost and policy treatment first
- not all realized profit must necessarily be distributed
- a cycle may exist only if treasury and accounting finalize an approved distributable amount

### Boundary

The profit participation system does not determine whether profit exists. Treasury and accounting do that. The profit participation system receives the finalized distributable amount and translates it into holder-facing cycle execution.

This preserves discipline between platform economics and payout architecture.

---

## Eligibility Framework

Eligibility for profit participation is derived from Ethereum-based FUZE holder truth through the snapshot and eligibility pipeline.

### Eligibility source

- canonical FUZE token balances on Ethereum mainnet
- cycle-specific snapshot reference
- policy-defined exclusions and treatment rules

### Eligibility principles

- raw holder balances are the source input
- eligible balances are derived after policy is applied
- treasury, foundation, team, vesting, or operational addresses may receive special treatment according to published rules
- eligibility should be deterministic for the cycle
- payout execution should rely on already-prepared entitlement input rather than ad hoc interpretation during claim

### Important boundary

Ethereum holder truth is the source of participation identity. Base payout execution is the claim environment. The eligibility pipeline is what connects them.

---

## Snapshot-Based Participation Model

FUZE uses a snapshot-based participation model for profit participation.

### Snapshot model role

The snapshot model allows FUZE to:

- keep the token on Ethereum
- derive eligibility from canonical token balances
- keep payout execution on Base
- create cycle-specific entitlement logic
- preserve auditability and reporting clarity

### Benefits of the snapshot model

- no need to migrate token balances to Base for eligibility
- clearer historical reference for each payout cycle
- better compatibility with public explanation and audit
- cleaner separation between token truth and payout execution

### Principle

Ethereum remains the participation layer.  
Base remains the payout execution layer.  
The snapshot model is the bridge between them.

---

## Payout Cycle Logic

Profit participation in FUZE should occur through explicit payout cycles.

Each payout cycle should have a bounded lifecycle that includes, at minimum:

### 1. Cycle Initialization
A cycle is defined as a distinct distribution event.

### 2. Profit Finalization
Treasury and accounting determine the policy-defined distributable amount.

### 3. Snapshot and Eligibility Preparation
The Ethereum-based holder dataset is prepared according to cycle policy.

### 4. Entitlement Construction
The eligible balances are transformed into cycle-specific entitlement input.

### 5. Contract Funding
Stablecoins are funded into the payout execution contract on Base for the cycle.

### 6. Claim Opening
The claim period becomes active for eligible participants.

### 7. Claim Execution
Eligible holders claim stablecoin payouts through the Base payout layer.

### 8. Cycle Closure
The cycle reaches its closed or finalized state according to payout policy.

### 9. Reporting and Ledger Publication
Cycle data is reflected in the payout ledger and transparency reports.

### Why cycles matter

A cycle structure prevents profit participation from becoming vague or always-on in a way that is hard to audit. It turns distribution into a governed financial process rather than a narrative promise.

---

## Entitlement Construction Principles

The profit participation system must convert the eligible holder dataset into a cycle-specific entitlement structure.

At minimum, entitlement construction should preserve:

- linkage to the cycle identifier
- linkage to the source snapshot and policy version
- holder-level or proof-level claim basis
- deterministic treatment of eligible balances
- compatibility with downstream payout contract execution

### Principle

Entitlement construction is not the same as raw balance extraction. It is the step where policy-treated participation data becomes cycle-specific claimable value.

This step is one of the most trust-sensitive parts of the pipeline because it connects holder participation to concrete payout rights.

---

## Relationship to the Base Payout Execution Layer

The profit participation system is closely linked to the Base payout execution layer, but the two are not identical.

### Profit participation system owns
- payout philosophy
- policy boundaries
- relationship to treasury/accounting
- relationship to eligibility and snapshots
- cycle structure and economic meaning

### Base payout execution layer owns
- funded cycle contract state
- claim-open / claim-closed state
- stablecoin transfer on valid claim
- double-claim prevention
- practical user-facing claim execution

### Boundary

The profit participation system defines what the payout model means.  
The Base payout layer executes the funded cycle operationally.

This distinction keeps economic design and execution mechanics cleanly separated.

---

## Relationship to the Ethereum Token Layer

The profit participation system is also downstream from the Ethereum token layer.

### Ethereum token layer provides
- the canonical participation asset
- holder-balance truth
- the source of snapshot-based eligibility

### Profit participation system uses
- Ethereum-derived snapshots
- holder categories and exclusions
- cycle-specific eligibility inputs

### Important rule

Token ownership on Ethereum is the basis of participation identity, but it does not by itself execute the payout. The payout occurs later, through stablecoin claims on Base, after the profit participation system and eligibility pipeline have done their work.

---

## Foundation Participation Treatment

The Foundation must be treated explicitly in the profit participation system.

### Principles

- Foundation treatment must be policy-defined
- inclusion or exclusion must not be informal
- if Foundation-held balances count, that treatment should be explicit in cycle policy
- if Foundation-held balances are excluded, that should also be explicit
- Foundation participation should not rely on hidden exceptional logic

This matters because the Foundation is a visible and structurally meaningful part of FUZE’s architecture. Ambiguity around Foundation treatment would weaken the transparency of the participation model.

---

## Policy Boundaries

The profit participation system must operate within clear policy boundaries.

### Boundary 1: Product usage is not profit participation
Using Platform Credits or paying for products does not automatically create payout rights.

### Boundary 2: Token ownership does not bypass policy
Eligibility depends on published cycle rules, exclusions, and treatment logic.

### Boundary 3: Payouts are funded, not implied
A token does not create an automatic cashflow. Profit participation becomes active only when a cycle is funded and opened.

### Boundary 4: Treasury funding is not arbitrary payout narrative
The funded amount should correspond to policy-defined distributable profit.

### Boundary 5: Reporting and transparency are required
The profit participation system should remain structurally legible through ledgers, reports, and public explanation.

These boundaries are necessary because many ecosystems weaken trust precisely where payout logic becomes vague.

---

## Governance Expectations

The profit participation system should be governed with strong role-specific discipline.

### Governance-sensitive actions may include:

- approval of payout-cycle funding
- publication of cycle parameters
- authorization of entitlement input for payout execution
- claim-window control where applicable
- emergency or pause behavior where necessary
- resolution of discovered pre-launch issues in a cycle

### Governance principles

- governance should be explicit, auditable, and role-bounded
- eligibility and payout authority should not be casually mixed with product admin power
- high-impact changes should follow multisig/timelock discipline where appropriate
- policy changes affecting payout meaning should be treated as major ecosystem actions

Because profit participation is central to holder trust, its control model must be serious.

---

## Transparency, Reporting, and Payout Ledger Requirements

The profit participation system should be supported by strong transparency and reporting surfaces.

At minimum, the ecosystem should be able to explain:

- what payout cycles exist
- whether a cycle was funded
- the structural basis of eligibility for the cycle
- that eligibility comes from Ethereum snapshots
- that claims execute on Base
- what payout asset is used
- how the payout system remains distinct from Platform Credits and token balances

The payout ledger should preserve cycle-level visibility, including:

- cycle identifiers
- cycle status
- funded amount
- claim state references
- linkage to reporting references

### Why reporting matters

The profit participation system is too important to be explained only by contract activity and scattered announcements. Structured reporting is part of the system itself, not an optional public-relations layer.

---

## Audit Requirements

The profit participation system should preserve enough structure to support meaningful audit and review.

At minimum, auditability should support:

- verification of cycle funding lineage
- verification of policy version used for the cycle
- traceability from snapshot source to eligibility output
- reconciliation between funded amount and payout execution
- visibility into Foundation or excluded-category treatment at the policy level
- support investigation where holders question cycle treatment or claim status

This matters because the credibility of the payout model depends not only on code, but also on the ability to explain what happened and why.

---

## Correction and Recovery Principles

The profit participation system should include bounded correction and recovery principles.

### Pre-funding issue discovery
If an eligibility or entitlement issue is discovered before a cycle is funded, the cycle should be corrected before claim opening rather than pushed forward ambiguously.

### Post-funding issue discovery
If an issue is discovered after funding, recovery should follow explicit governance and incident procedures rather than hidden ad hoc operator fixes.

### Claim execution issues
If claim execution is impaired operationally, the system should preserve traceability and use defined pause, repair, or follow-up procedures.

### Principle

Recovery paths must preserve trust. Silent discretionary intervention in payout logic is one of the clearest ways to weaken a transparency-first participation system.

---

## Risks and Safeguards within the Profit Participation System

The profit participation model includes several key risks:

### 1. Eligibility risk
If snapshot or exclusion logic is wrong, payout execution can be technically correct but economically wrong.

### 2. Funding interpretation risk
If the platform cannot explain how distributable profit was determined, payout credibility weakens.

### 3. Layer confusion risk
If holders confuse Platform Credits, FUZE token, and stablecoin payouts, the economic model becomes harder to trust.

### 4. Governance risk
If funding or entitlement logic appears too discretionary, the model loses legitimacy.

### 5. Reporting risk
If cycle reporting is weak, even sound contract execution may still look opaque.

### Safeguard principles

FUZE addresses these risks through:
- role separation
- cycle-based payout structure
- Ethereum-based eligibility source
- Base-based payout execution
- treasury/accounting finalization before funding
- structured reporting and payout ledgers
- governance discipline around cycle-sensitive actions

---

## Minimum Architectural Entities

At minimum, the profit participation system should recognize the following entities:

### Profit Participation Cycle
- cycle identifier
- cycle status
- policy version
- funded distributable amount
- payout asset reference

### Eligibility and Entitlement Entities
- snapshot reference
- eligibility dataset reference
- exclusion-treatment reference
- entitlement input reference

### Execution Entities
- payout contract reference
- claim status reference
- cycle funding reference
- cycle closure reference

### Reporting and Audit Entities
- payout ledger reference
- transparency report reference
- audit validation reference
- governance approval reference where applicable

These are minimum conceptual entities. More detailed schemas are refined downstream.

---

## Open Items

The following areas are intentionally refined in downstream specifications:

- exact distributable-profit policy formulas
- exact Foundation participation treatment
- exact claim-window and residual-handling policy
- exact payout-asset policy and funding source mechanics
- exact cycle-publication and reporting depth
- exact recovery playbooks for post-funding issues

These do not weaken the canonical profit participation system established here.

---

## Closing Summary

The FUZE profit participation system is a policy-defined, stablecoin-based, cycle-driven architecture that connects real platform success to eligible FUZE holders without blurring token ownership, Platform Credits, treasury accounting, or payout execution. It begins from Ethereum-based holder truth, applies explicit eligibility treatment through the snapshot pipeline, receives finalized distributable profit from treasury and accounting, and executes funded stablecoin claims on Base through a dedicated payout layer. By making each of these stages explicit, FUZE turns holder alignment into a structurally credible system rather than a vague token promise.
