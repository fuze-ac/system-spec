# SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC

## Purpose

This document defines the canonical snapshot and eligibility pipeline of the FUZE ecosystem. Its purpose is to establish how Ethereum-based FUZE holder balances become policy-defined eligibility inputs for stablecoin profit participation and other holder-aware ecosystem functions, while preserving strong separation between token ownership truth, policy interpretation, payout execution, and public reporting.

This specification is foundational because FUZE uses a layered chain model. The FUZE token remains on Ethereum as the canonical participation layer, while stablecoin profit participation executes on Base. The snapshot and eligibility pipeline is the bridge between those two layers. If this bridge is weak, ambiguous, or poorly governed, the entire holder-alignment model loses credibility. If it is strong, explicit, and auditable, the layered architecture becomes a long-term trust advantage.

---

## Scope

This specification covers:

- the canonical role of the snapshot and eligibility pipeline in FUZE
- how Ethereum holder balances become cycle-specific eligibility inputs
- the distinction between raw holder-balance truth and policy-defined eligible balances
- snapshot timing, cycle references, and dataset construction principles
- included and excluded address treatment categories
- eligibility derivation for stablecoin profit participation
- optional reuse of holder datasets for rank, privileges, or ecosystem participation logic where applicable
- off-chain and on-chain coordination in the pipeline
- audit, transparency, governance, and failure-handling expectations for snapshot operations

This specification does not fully define payout funding policy, full profit accounting logic, or final payout contract implementation details. Those are refined in:

- `ETHEREUM_TOKEN_LAYER_SPEC.md`
- `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`
- `PAYOUT_LEDGER_SPEC.md`

---

## Design Goals

The design goals of the snapshot and eligibility pipeline are:

1. to preserve Ethereum as the canonical source of FUZE holder truth
2. to translate raw token balances into policy-defined eligibility datasets without blurring those two layers
3. to support payout-cycle-specific entitlement construction with strong auditability
4. to make inclusion and exclusion logic explicit rather than informal
5. to reduce ambiguity around treasury, foundation, team, and operational address treatment
6. to support transparent and reproducible payout-cycle preparation
7. to provide a reusable participation dataset model for other holder-aware platform functions where appropriate
8. to strengthen the trustworthiness of the layered chain architecture through explicit cross-layer coordination

---

## Non-Goals

This specification is not intended to:

- make raw Ethereum balances automatically identical to payout entitlement
- let the payout contract determine all eligibility logic independently on-chain
- define token-holder truth separately from Ethereum mainnet
- replace treasury/accounting profit determination with snapshot logic
- make every holder-aware feature use the exact same eligibility rules as profit participation
- allow silent or ad hoc exclusions without policy basis
- treat snapshot output as discretionary operator interpretation without structured controls

---

## Canonical Pipeline Principle

The primary principle of the FUZE snapshot and eligibility pipeline is:

> Ethereum provides canonical holder-balance truth, while the snapshot and eligibility pipeline applies published policy to that truth in order to produce cycle-specific eligible datasets for profit participation and other holder-aware ecosystem functions.

This means:

- raw balance truth and eligible-balance truth are related but not identical
- the snapshot pipeline is the formal bridge between token participation and payout execution
- policy is applied through explicit dataset construction rather than vague narrative interpretation
- every payout cycle should be explainable in terms of source balances, applied policy, and resulting eligibility output
- the pipeline must remain structured enough to support reporting, audit, and public credibility

This principle is essential because the quality of the holder-alignment model depends heavily on how clearly participation truth is transformed into payout eligibility.

---

## Why FUZE Needs a Snapshot and Eligibility Pipeline

FUZE needs a dedicated snapshot and eligibility pipeline because the ecosystem intentionally separates:

- the **FUZE token** on Ethereum,
- the **stablecoin payout execution layer** on Base,
- and the **internal consumption economy** on Base through Platform Credits.

That separation is an architectural strength, but it requires a disciplined bridge.

If the system attempted to infer eligibility loosely, or relied only on vague statements such as “holders get payouts,” several problems would emerge:

- it would be unclear which balances count
- it would be unclear when balances count
- treasury and operational wallets could distort participation meaning
- payout-cycle funding could be technically correct but economically disputed
- public trust would depend too heavily on informal explanation

The snapshot and eligibility pipeline solves this by providing a formal process for converting canonical Ethereum token truth into cycle-specific participation eligibility.

This makes the layered chain architecture workable in practice. Ethereum remains the canonical participation layer. Base remains the practical payout execution layer. The snapshot pipeline is what links them with structure rather than confusion.

---

## Core Concepts

### Snapshot
A policy-defined reference state of Ethereum FUZE holder balances at a specified point or block reference for a specific purpose.

### Eligibility Dataset
The processed dataset derived from the snapshot after inclusion, exclusion, and policy treatment rules are applied.

### Raw Holder Truth
The direct balance state of FUZE token holders on Ethereum at the snapshot reference.

### Eligible Balance
The balance amount that counts toward a specific payout cycle or holder-aware logic after policy is applied.

### Excluded Address
An address or address category whose balance is not counted, or is counted differently, for the relevant cycle or purpose.

### Cycle Reference
The unique identifier or timeframe associated with the payout cycle or holder-aware operation being prepared.

### Entitlement Input
The dataset or proof basis produced by the pipeline for later payout execution or holder-aware feature logic.

### Policy Treatment
The rule set that determines how categories of addresses or balances are handled in the eligibility process.

---

## Canonical Role of the Pipeline

The snapshot and eligibility pipeline exists to do the following:

1. read canonical FUZE holder-balance truth from Ethereum
2. bind that truth to a specific cycle or purpose
3. apply policy-defined treatment rules
4. construct a clean eligibility dataset
5. provide the resulting dataset to downstream payout, reporting, and audit systems
6. preserve traceability between source balances and resulting entitlement logic

This means the pipeline is not just a one-time export script or an informal spreadsheet exercise. It is a structural part of the ecosystem’s participation model.

The pipeline is also not the same thing as the payout contract. It prepares eligibility. The payout contract executes claims based on already-prepared entitlement logic.

---

## Canonical Input Source

The canonical input source for the snapshot and eligibility pipeline is the **FUZE token on Ethereum mainnet**.

This is important because Ethereum is the canonical token layer of the ecosystem. The pipeline must begin from that layer in order to preserve the participation truth model of FUZE.

### Canonical input includes:

- token contract identity
- total holder-balance view at the chosen snapshot reference
- address-level balances
- policy-linked metadata about known reserve or special-purpose addresses where applicable

### Important boundary

The pipeline should not treat Base balances, internal credits balances, or payout contract state as substitutes for Ethereum holder truth. Those are different layers with different meanings.

The only strong starting point for holder-based eligibility is the canonical token balance layer on Ethereum.

---

## Snapshot Timing Model

Each payout or holder-aware eligibility operation should be tied to an explicit snapshot timing model.

### Snapshot timing should define:

- which cycle or purpose the snapshot belongs to
- what block height, timestamp reference, or equivalent canonical reference is used
- how the timing relates to the payout cycle timeline
- whether any freeze, cutoff, or publication policy applies

### Why timing matters

Eligibility can become disputed if the system is vague about when balances were measured. A strong pipeline should allow the platform to answer questions such as:

- Which balances counted for this cycle?
- At what reference point were they measured?
- How does this relate to the payout cycle announcement and funding timeline?

### Timing principle

The snapshot timing model should be explicit enough to support reproducibility and trust, while remaining compatible with the operational needs of cycle preparation.

This does not require publishing every internal operational step immediately, but it does require having a deterministic snapshot reference for each cycle.

---

## Raw Balance Truth vs Eligible Balance Truth

A crucial principle of the pipeline is the distinction between raw balance truth and eligible balance truth.

### Raw balance truth
Raw balance truth is the balance state visible on Ethereum at the snapshot reference.

### Eligible balance truth
Eligible balance truth is the balance state after policy-defined inclusion and exclusion rules are applied.

### Why the distinction matters

Not every raw balance should automatically count the same way in every cycle. For example:

- treasury-controlled balances may not count as public-holder participation
- foundation-held balances may require explicit policy treatment
- team vesting balances may be excluded or treated specially
- operational addresses may be non-participating
- migration-related or internal-purpose contracts may not represent circulating holder participation

This distinction is one of the most important trust features of the FUZE architecture. The system should not pretend that raw holder state and payout entitlement are always identical. Instead, it should make the transformation explicit.

---

## Address Treatment Categories

The pipeline should support policy-defined treatment categories for addresses and balances.

At minimum, the platform should be able to distinguish among categories such as:

### 1. Ordinary Holder Addresses
Balances that count normally for the relevant purpose.

### 2. Treasury-Controlled Addresses
Addresses associated with treasury reserves or operational platform control that may be excluded or specially treated.

### 3. Foundation Addresses
Addresses associated with Foundation-controlled balances whose participation treatment should be explicitly defined by policy.

### 4. Team and Vesting Addresses
Addresses representing locked team, founder-merged, or advisor vesting balances that may require exclusion or special treatment.

### 5. Operational or Programmatic Addresses
Addresses used for migration, liquidity operations, incentives, or other structured categories that may not represent ordinary circulating participation.

### 6. Explicitly Excluded Addresses
Addresses excluded by published policy due to category, control, or structural reason.

### Important principle

The existence of categories does not mean every category must always be excluded. It means treatment must be explicit. The market should not have to guess whether Foundation, treasury, or vesting balances are counted in the same way as ordinary holder balances.

---

## Eligibility Rules for Stablecoin Profit Participation

For stablecoin profit participation, the eligibility pipeline should produce a cycle-specific eligible dataset that reflects:

- the canonical Ethereum balance source
- the cycle snapshot reference
- the applicable exclusion and treatment policy
- the final eligible-balance mapping used to construct payout entitlements

### Stablecoin eligibility principles

1. eligibility should be deterministic for a given cycle
2. the dataset should reflect policy, not improvisation
3. balances that do not represent intended circulating participation should not silently distort holder payout logic
4. the resulting eligibility set should be suitable for Base payout execution
5. the entitlement construction process should preserve traceability back to the snapshot and policy version used

The stablecoin profit participation model is one of the strongest reasons this pipeline exists. The more clearly the platform can explain how eligibility is produced, the stronger the payout architecture becomes.

---

## Relationship to Holder Rank and Ecosystem Privileges

The snapshot and eligibility pipeline may also support other holder-aware ecosystem functions beyond profit participation, such as:

- holder-rank calculations
- cross-product privilege eligibility
- ecosystem access tiers
- participation-aware campaign logic
- governance- or DAO-lite-related holder datasets in the future

### Important boundary

Not every holder-aware function must use the exact same eligibility rules as stablecoin profit participation. For example, a holder-rank system may choose to count or treat some categories differently from a payout cycle.

### Reuse principle

The snapshot and eligibility pipeline should therefore be reusable at the framework level while allowing purpose-specific policy variants where justified. What must remain consistent is the discipline:

- canonical token source
- explicit timing
- explicit treatment policy
- explicit output dataset

This makes the pipeline a long-term ecosystem asset rather than a one-purpose payout tool only.

---

## Pipeline Lifecycle

At minimum, the snapshot and eligibility pipeline should support a structured lifecycle.

### 1. Cycle or Purpose Initialization
A specific payout cycle or holder-aware purpose is declared.

### 2. Snapshot Reference Selection
The canonical Ethereum reference point for balance measurement is fixed.

### 3. Raw Holder Dataset Extraction
The raw balance view is constructed from Ethereum token state.

### 4. Policy Application
Exclusion and address-treatment rules are applied.

### 5. Eligible Dataset Construction
The final eligible-balance dataset is produced.

### 6. Entitlement Input Preparation
The dataset is transformed into the format required by downstream systems such as payout execution or rank logic.

### 7. Validation and Audit Check
The dataset is reviewed, validated, and recorded for reporting and audit compatibility.

### 8. Downstream Publication / Use
The dataset or derived proof/reference is used by the Base payout layer or other holder-aware systems.

This lifecycle helps ensure that the pipeline behaves like a formal system rather than a discretionary operational task.

---

## On-Chain and Off-Chain Responsibilities in the Pipeline

The snapshot and eligibility pipeline is an explicitly cross-domain system.

### On-chain responsibilities
- Ethereum token state provides canonical balances
- downstream Base contracts may use the final entitlement output for claim execution

### Off-chain responsibilities
- selecting snapshot references
- extracting holder data
- classifying address categories
- applying policy treatment
- constructing eligibility datasets
- preparing entitlement proofs or roots where needed
- producing reporting and audit artifacts

### Responsibility principle

On-chain layers provide canonical state and execution environments. Off-chain systems provide policy interpretation, classification, and dataset construction. The pipeline is strong when these responsibilities are explicit rather than blurred.

This is important because some observers may expect “everything” to happen on-chain. FUZE takes a more disciplined view: what needs enforceable chain state should be on-chain, and what requires policy logic, classification, and reporting may appropriately exist off-chain so long as it remains auditable and transparent enough.

---

## Validation and Reconciliation Requirements

The pipeline should include validation and reconciliation steps before downstream use.

At minimum, validation should help confirm:

- that the correct token contract and snapshot reference were used
- that total included and excluded balances reconcile sensibly against raw supply views
- that known controlled-address categories were treated according to policy
- that the eligible dataset is internally consistent
- that the resulting entitlement input matches the intended cycle parameters

### Why validation matters

A payout cycle can be technically executed on Base and still be strategically damaging if the eligibility dataset was prepared incorrectly. The stronger the validation discipline, the stronger the trustworthiness of the payout model.

Validation and reconciliation should therefore be treated as a first-class part of the pipeline rather than an optional quality-control afterthought.

---

## Transparency and Reporting Requirements

Because the snapshot and eligibility pipeline is central to holder trust, it should support meaningful transparency and reporting.

At minimum, the ecosystem should be able to explain:

- that Ethereum is the eligibility source
- that a snapshot or equivalent reference is used
- that exclusion and treatment policy exists
- that payout eligibility is derived, not improvised
- how the cycle dataset relates to downstream payout execution
- what policy version or treatment logic applied for the cycle at a structural level

The platform does not necessarily need to expose every operational detail in raw form immediately, but it should preserve enough structure that the eligibility logic can be explained and defended publicly.

This is especially important because payout trust depends not only on contract funding, but also on confidence that the correct people are eligible.

---

## Governance and Control Expectations

The snapshot and eligibility pipeline should be governed with strong discipline because it affects holder economics directly.

### Governance-sensitive areas include:

- selection of the official snapshot policy
- treatment of controlled and excluded addresses
- publication of cycle-specific eligibility logic
- approval of entitlement-root or eligibility outputs for payout execution
- changes to address-category interpretation
- emergency handling of discovered dataset issues before funding or claim opening

### Governance principles

- policy should be explicit
- meaningful changes should be auditable
- eligibility-related authority should not be casually mixed with product admin powers
- payout-affecting decisions should remain bounded by control-plane discipline, multisig processes, or equivalent governance structure where appropriate

The pipeline is too important to holder trust to operate as an informal internal spreadsheet exercise.

---

## Security Principles

The snapshot and eligibility pipeline should follow strong correctness and trust-preserving principles.

### Key principles include:

#### Canonical Source Integrity
The pipeline must always begin from the correct Ethereum token source.

#### Deterministic Reference Integrity
The chosen snapshot reference must be explicit and stable.

#### Policy Legibility
Inclusion and exclusion logic must be understandable and bounded.

#### Controlled Classification
Address categorization must be deliberate rather than ad hoc.

#### Validation Before Execution
Entitlement datasets should be validated before being used for payout execution.

#### Traceability
A cycle’s eligibility result should remain traceable to source balances and policy treatment.

#### Separation from Treasury Discretion
Treasury funding should not quietly redefine eligibility treatment after the fact.

These principles matter because errors in this pipeline can damage trust even when every smart contract behaves technically as written.

---

## Risks and Failure Considerations

The snapshot and eligibility pipeline introduces several important risks that must be managed.

### 1. Address Classification Risk
A controlled or excluded address may be categorized incorrectly.

### 2. Snapshot Reference Risk
The wrong block or timing reference may be used for a cycle.

### 3. Policy Interpretation Risk
Eligibility policy may be ambiguous or inconsistently applied.

### 4. Cross-Layer Coordination Risk
The entitlement dataset may be correct in spirit but transformed incorrectly for downstream Base payout execution.

### 5. Transparency Risk
If the platform cannot explain the pipeline clearly, holders may distrust even technically valid payout cycles.

### 6. Governance Risk
If eligibility-affecting decisions are too discretionary, the participation model weakens.

These risks do not invalidate the architecture. They reinforce why FUZE treats this pipeline as a formal trust-bearing system.

---

## Minimum Architectural Entities

At minimum, the snapshot and eligibility pipeline should recognize the following entities:

### Snapshot Reference Entities
- cycle or purpose identifier
- Ethereum token contract reference
- block height / timestamp reference
- policy version reference

### Raw Dataset Entities
- raw holder-address list
- raw balance mapping
- total raw counted balance summary

### Policy Treatment Entities
- address category references
- exclusion list references
- special-treatment rules
- inclusion/exclusion result markers

### Eligible Dataset Entities
- eligible address list
- eligible balance mapping
- cycle-specific dataset identifier
- entitlement-input reference or proof root where applicable

### Audit and Reporting Entities
- validation result reference
- publication/reporting reference
- downstream payout linkage reference

These are minimum conceptual entities. Detailed schema and proof formats are refined downstream.

---

## Open Items

The following areas are intentionally refined in downstream specifications:

- exact snapshot-reference timing policy
- exact excluded-address categories and registry structure
- exact entitlement-root or proof-generation model
- exact Foundation-treatment policy in payout cycles
- exact publication level for cycle datasets
- exact replay and recovery rules if a dataset issue is found before or after funding

These do not weaken the canonical snapshot and eligibility pipeline model established here.

---

## Closing Summary

The FUZE snapshot and eligibility pipeline is the formal bridge between Ethereum-based token participation and Base-based stablecoin payout execution. It begins from canonical FUZE holder balances on Ethereum, applies explicit policy treatment to construct cycle-specific eligible datasets, and prepares the entitlement inputs required for downstream payout and holder-aware platform logic. By separating raw balance truth from eligible-balance truth, and by making the transformation structured, auditable, and policy-driven, FUZE strengthens one of the most important trust-bearing parts of its holder-alignment architecture.
