# ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC

## Purpose

This document defines the canonical division of responsibilities between on-chain and off-chain systems in the FUZE ecosystem. Its purpose is to establish which parts of the platform must live on-chain, which parts should remain off-chain, how those layers coordinate, and why FUZE intentionally uses a hybrid architecture rather than forcing every economic, product, governance, and reporting function into one execution environment.

This specification is foundational because FUZE combines multiple products, a shared internal credits economy, Ethereum-based token participation, Base-based payout execution, treasury and reserve structures, AI-native platform systems, and policy-defined transparency requirements. In a system of this kind, confusion about on-chain versus off-chain responsibility quickly becomes confusion about trust, authority, accounting, payout logic, and product behavior. FUZE therefore treats this boundary as a first-class architectural concern.

---

## Scope

This specification covers:

- the canonical role of on-chain and off-chain systems in FUZE
- the distinction between canonical chain state, policy logic, product logic, and reporting logic
- what must be enforced or anchored on-chain
- what should remain off-chain for practicality, policy, privacy, or system-design reasons
- how off-chain systems interact with Ethereum token truth and Base-based credits and payout layers
- how accounting, payment normalization, eligibility preparation, and reporting coordinate with on-chain layers
- the trust, transparency, and governance implications of hybrid architecture
- failure handling, reconciliation, and correction principles across the on-chain/off-chain boundary

This specification does not fully define every contract, every API, or every reporting schema. Those are refined in:

- `CHAIN_ARCHITECTURE_SPEC.md`
- `ETHEREUM_TOKEN_LAYER_SPEC.md`
- `BASE_PLATFORM_CREDITS_LAYER_SPEC.md`
- `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md`
- `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `TREASURY_AND_RESERVE_ARCHITECTURE_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`
- `API_ARCHITECTURE_SPEC.md`

---

## Design Goals

The design goals of the FUZE on-chain/off-chain responsibility model are:

1. to preserve chain state only where chain state adds enforceability, public verifiability, or clear structural value
2. to preserve off-chain execution where policy logic, privacy, product complexity, or operational practicality require it
3. to make cross-layer coordination explicit rather than informal
4. to prevent economic ambiguity between token balances, credits balances, accounting profit, and payout execution
5. to support a real multi-product SaaS platform without pretending all business logic belongs on-chain
6. to strengthen transparency by making responsibilities explicit
7. to support auditability and reconciliation across chain and non-chain systems
8. to reduce the risk that “on-chain” is used rhetorically where better system design requires hybrid architecture

---

## Non-Goals

This specification is not intended to:

- force all FUZE systems fully on-chain for appearance or ideology
- reduce the role of on-chain systems to symbolic contract deployments with no real structural meaning
- make off-chain systems invisible, discretionary, or weakly governed
- imply that product logic, accounting, or AI orchestration should be moved wholesale on-chain
- imply that on-chain state alone is sufficient to explain the full economic meaning of the platform
- collapse privacy-sensitive or workspace-sensitive product state into public chain storage
- treat hybrid architecture as a compromise rather than as an intentional design choice

---

## Canonical Responsibility Principle

The primary principle of FUZE’s hybrid architecture is:

> on-chain systems should hold canonical public state and enforce bounded economic or governance logic where that creates real trust value, while off-chain systems should handle policy interpretation, product execution, payment verification, accounting, AI orchestration, and reporting functions that require flexibility, privacy, or operational complexity.

This means:

- on-chain does not mean “everything important”
- off-chain does not mean “informal” or “untrusted”
- each side has bounded responsibilities
- trust comes from clear role separation and coordination, not from pretending one layer can or should do every job
- the stronger the ecosystem becomes, the more important this explicit division becomes

This principle is one of the clearest expressions of FUZE’s architecture-first approach.

---

## Why FUZE Uses a Hybrid On-Chain / Off-Chain Model

FUZE uses a hybrid model because it is building a real platform business, not a narrow on-chain primitive and not a conventional centralized SaaS stack hidden behind Web3 branding.

The ecosystem contains:

- a canonical participation token on Ethereum
- an internal credits layer on Base
- a stablecoin payout execution layer on Base
- treasury and reserve contracts
- subscriptions and usage billing
- payment normalization across multiple external rails
- AI orchestration across multiple products
- workflow automation, queues, retries, and scheduled tasks
- workspace-scoped and privacy-sensitive product data
- accounting and reporting functions
- governance-sensitive policy systems

These functions do not all belong naturally in one place.

Some functions benefit from chain-based enforceability and public visibility. Others would become impractical, privacy-breaking, or conceptually distorted if forced on-chain. FUZE therefore uses a hybrid model not because the architecture is incomplete, but because the architecture is trying to be correct.

The on-chain/off-chain split helps FUZE maintain:
- enforceable participation and payout structure,
- practical internal platform commerce,
- credible reserve architecture,
- scalable product execution,
- and reporting discipline without overloading smart contracts with responsibilities they should not own.

This hybrid model is therefore a structural strength, not an admission of incompleteness.

---

## Canonical On-Chain Responsibilities

On-chain systems in FUZE should be used where public state, contract enforceability, or visible structural separation provides real value.

At minimum, the following categories belong on-chain.

### 1. Canonical Token Participation State
The FUZE token contract on Ethereum should hold:
- token identity
- total supply state
- holder-balance truth
- canonical participation-layer state

### 2. Platform Credits Operational Ledger State
The Base credits layer should hold:
- credits issuance commitment state
- credits balance state by scope
- spend, reservation, release, reversal, or adjustment state where reflected on-chain
- internal commerce layer state

### 3. Stablecoin Payout Execution State
The Base payout layer should hold:
- payout cycle funding state
- claim-open / claim-closed state
- claim execution state
- double-claim prevention logic
- payout contract balance state

### 4. Reserve and Vault Contract State
Ethereum-side reserve architecture should hold:
- allocation contract state
- vesting state
- category-bounded reserve state
- role-specific contract separation
- governance-controlled action state where relevant

### 5. Governance-Critical Contract Controls
Where relevant, on-chain systems should hold:
- multisig ownership or control references
- timelock-based role protections
- pause or constrained administrative controls
- contract-level public registry relationships

### On-chain principle

On-chain state should represent the enforceable, publicly visible structural layer of FUZE. It should not try to absorb the full complexity of product behavior or business accounting.

---

## Canonical Off-Chain Responsibilities

Off-chain systems in FUZE should handle functions where policy logic, privacy, operational flexibility, or product complexity make off-chain execution more appropriate.

At minimum, the following categories belong off-chain.

### 1. Payment Verification and Normalization
Off-chain systems should handle:
- Stripe verification
- stablecoin deposit normalization where applicable
- Telegram Stars verification
- Apple and Google billing verification
- fraud signals
- chargeback handling
- external payment-rail interpretation

### 2. Accounting and Treasury Interpretation
Off-chain systems should handle:
- revenue recognition logic
- cost and expense treatment
- refund treatment
- distributable profit determination
- treasury reconciliation
- reserve accounting interpretation
- policy-driven financial classification

### 3. Snapshot and Eligibility Preparation
Although based on on-chain token truth, off-chain systems should handle:
- snapshot reference selection
- raw holder extraction
- address classification
- exclusion application
- entitlement dataset construction
- proof or root preparation where applicable

### 4. Product Domain Logic
Off-chain product systems should handle:
- QTB intelligence logic
- AIMM operations logic
- ZAGA utility-system logic
- AIE recommendation logic
- HerHelp app and workflow generation
- Botmad workflow-support and execution assistance
- workspace-scoped product state
- user-level or team-level operational data

### 5. AI Orchestration and Execution
Off-chain systems should handle:
- model routing
- context assembly
- prompt and instruction layering
- AI provider calls
- tool orchestration
- output validation
- AI usage metering hooks
- fallback and retry behavior

### 6. Workflow and Automation Coordination
Off-chain systems should handle:
- queue-backed orchestration
- retries and backoff
- scheduled tasks
- human approval gates
- job routing
- multi-step execution state
- operator review and support flows

### 7. Reporting and Transparency Publication
Off-chain systems should handle:
- transparency reports
- payout ledgers
- contract registry publication
- investor/community reporting
- internal analytics and observability
- structured public explanation of chain state

### 8. Privacy-Sensitive and Workspace-Sensitive Data
Off-chain systems should handle:
- business documents
- generated app content
- operator notes
- workflow history
- workspace-private settings
- permissioned product artifacts
- non-public user and workspace state

### Off-chain principle

Off-chain systems are where FUZE executes the rich, policy-sensitive, privacy-sensitive, and product-heavy logic of the platform. These systems are not secondary. They are essential parts of the architecture.

---

## Responsibilities by Economic Layer

One of the clearest ways to understand the on-chain/off-chain division is by economic layer.

### Token Participation Layer
**On-chain:** Ethereum token contract, holder balances, token distribution state  
**Off-chain:** holder analytics, rank derivation, snapshot extraction, public explanation, ecosystem policy interpretation

### Platform Credits Layer
**On-chain:** Base credits state, issuance commitment, balance state, spend/release/reversal representation  
**Off-chain:** payment normalization, credits policy, billing logic, refund interpretation, usage attribution, commercial reporting

### Profit Participation Layer
**On-chain:** Base payout cycles, funded stablecoin pool, claim execution, claim state  
**Off-chain:** distributable profit determination, eligibility dataset construction, exclusion logic, cycle reporting, audit support

### Why this matters

This model makes clear that on-chain layers hold enforceable state, while off-chain layers determine how and why that state should be created or interpreted under platform policy.

---

## Responsibilities by Product Layer

FUZE also needs an explicit division of on-chain/off-chain responsibility at the product level.

### Products should not own chain roles by default
Products such as QTB, AIMM, ZAGA, AIE, HerHelp, and Botmad should not independently define token truth, credits semantics, or payout logic.

### Products may consume chain-aware context
Products may consume:
- wallet-aware participation context
- holder-rank signals
- credits-balance state
- entitlement state influenced by on-chain systems
- payout-status visibility where relevant

### Products remain primarily off-chain domains
Product domain objects, workflows, AI actions, private reports, and workspace data should remain primarily off-chain unless a very strong architectural reason exists to place some element on-chain.

### Product principle

Products are platform consumers of on-chain state where relevant, but they remain primarily off-chain execution domains. This helps FUZE stay scalable and privacy-aware while preserving one coherent platform model.

---

## On-Chain State Is Not the Same as Full Economic Meaning

One of the most important principles in FUZE is that on-chain state and full economic meaning are not identical.

Examples:

- Credits issued on Base do not by themselves prove profit exists.
- A funded payout cycle on Base does not by itself explain how distributable profit was determined.
- A token balance on Ethereum does not by itself define final payout eligibility without policy treatment.
- A reserve contract balance does not by itself explain what treasury is allowed to do with it.

### Why this matters

If the platform overstates what chain state alone means, it risks creating false certainty. If it understates chain state, it weakens transparency. FUZE solves this by explicitly pairing on-chain state with off-chain policy, accounting, and reporting systems.

This principle is crucial to public trust. Chain state provides enforceable structure. Off-chain systems provide interpretation, classification, policy, and explanation.

---

## Policy Lives Mostly Off-Chain, But Must Bind On-Chain Outcomes

In FUZE, much of the platform’s policy logic lives off-chain, but that policy must still govern how on-chain actions occur.

Examples include:

- which external payment events can issue Platform Credits
- which address categories are excluded from payout eligibility
- how credit classes may be spent or expire
- how distributable profit is determined
- what counts as a valid payout cycle
- when reserve or vault actions are allowed
- which product actions require premium billing or approval

### Policy principle

Policy may be off-chain, but it must be:
- explicit,
- versionable,
- auditable,
- and tightly linked to the resulting on-chain operations.

FUZE should avoid both extremes:
- “everything is discretionary off-chain interpretation”
- and “policy doesn’t exist because the chain will somehow explain itself”

The stronger architecture is structured policy controlling bounded contract actions.

---

## Transparency Requires Both Layers

A transparency-first platform cannot rely only on on-chain contracts or only on off-chain reports. FUZE requires both.

### On-chain provides:
- public state
- contract separation
- visible balances
- funding visibility
- claim execution history
- token-holder truth
- reserve architecture visibility

### Off-chain provides:
- human-readable explanation
- product-level reporting
- accounting context
- payout-cycle interpretation
- eligibility methodology
- support and auditability
- investor/community summaries

### Transparency principle

On-chain state without reporting can be technically visible but strategically opaque.  
Reporting without on-chain structure can be rhetorically clear but weakly verifiable.  
FUZE therefore requires both.

This is one of the core reasons a formal on-chain/off-chain responsibility model is necessary.

---

## Governance Responsibilities Across the Boundary

Governance in FUZE also spans both layers.

### On-chain governance responsibilities may include:
- contract ownership
- multisig control
- timelock-protected actions
- reserve-vault permissions
- payout-cycle execution controls where applicable
- pause or emergency controls

### Off-chain governance responsibilities may include:
- policy setting
- approval workflows
- treasury decisions before funding
- eligibility interpretation rules
- reporting publication
- operational recovery and incident handling
- platform rollout decisions

### Governance principle

Not all governance becomes on-chain simply because contracts exist. FUZE governance is hybrid:
- enforceable contract controls on-chain,
- policy and procedural governance off-chain,
- both linked through explicit authorization and reporting discipline.

This is the correct model for a real multi-product platform with meaningful economic structure.

---

## Reconciliation Responsibilities

Because FUZE uses hybrid architecture, reconciliation is a first-class responsibility.

### Reconciliation is needed between:
- payment verification and credits issuance
- credits ledger records and Base credits state
- Ethereum holder truth and eligibility datasets
- eligibility datasets and Base payout entitlements
- treasury/accounting records and funded payout cycles
- contract balances and transparency reports
- product consumption data and credits or billing records

### Reconciliation principle

Hybrid architecture is trustworthy only when coordination is disciplined. Reconciliation is what makes the hybrid model operationally credible.

FUZE should therefore treat reconciliation as part of the architecture itself rather than as a narrow finance-only task.

---

## Failure and Recovery Principles Across the Boundary

A hybrid architecture also requires explicit failure-handling principles.

### Common failure types include:
- payment verified off-chain but credits issuance delayed on-chain
- credits mutation committed on-chain but product workflow not finalized off-chain
- snapshot prepared incorrectly before payout entitlement publication
- payout contract funded before reporting metadata is fully synchronized
- support correction needed after chain-visible action already occurred
- AI-heavy product usage settled incorrectly relative to commercial policy

### Recovery principles

- failure lineage should remain visible
- on-chain mistakes should not be “papered over” by silent off-chain edits
- off-chain corrections should preserve linkage to affected on-chain records
- economic corrections should remain auditable
- incident response should distinguish between chain-state correction, policy correction, and reporting correction

These principles are essential because hybrid systems fail most dangerously when one side tries to hide the mistakes of the other.

---

## Minimum Architectural Entities

At minimum, the on-chain/off-chain responsibility model should recognize the following conceptual entities:

### On-Chain Responsibility Entities
- Ethereum token contract
- Base credits ledger / credits contract
- Base payout contract
- reserve and vault contracts
- multisig / timelock control references
- chain-visible balance and mutation states

### Off-Chain Responsibility Entities
- payment verification systems
- accounting and treasury systems
- snapshot and eligibility pipeline
- product domain services
- AI orchestration systems
- workflow, queue, and scheduler systems
- reporting and transparency publication systems
- support and recovery systems

### Coordination Entities
- correlation IDs across systems
- policy version references
- reporting references
- reconciliation references
- audit lineage references
- cycle and funding references

These are minimum conceptual entities. Detailed schemas and service boundaries are refined elsewhere.

---

## Open Items

The following areas are intentionally refined in downstream specifications:

- exact reconciliation workflows by domain
- exact reporting publication latency requirements
- exact policy versioning model
- exact incident escalation pathways across chain and off-chain systems
- exact proof publication format for snapshot-derived eligibility
- exact enterprise/privacy handling for product-state reporting

These do not weaken the canonical on-chain/off-chain responsibility model established here.

---

## Closing Summary

The FUZE on-chain/off-chain responsibility model defines a deliberate hybrid architecture. On-chain systems hold canonical participation state, internal credits state, payout execution state, reserve structure, and governance-critical contract controls where public enforceability matters. Off-chain systems handle payment verification, accounting, treasury interpretation, eligibility preparation, product execution, AI orchestration, workflow coordination, privacy-sensitive state, and transparency reporting where flexibility, policy, and operational depth are required. By making this boundary explicit, FUZE strengthens the trustworthiness, scalability, and intelligibility of its entire ecosystem architecture.
