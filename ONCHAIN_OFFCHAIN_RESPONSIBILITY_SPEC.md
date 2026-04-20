# ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC

## Document Metadata

- Document Name: `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Governing Layer: Platform constitution / chain boundary / on-chain-off-chain responsibility
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, contracts engineering, finance systems, treasury, security, governance, reporting, operations
- Primary Purpose: Define the canonical division of responsibility between on-chain and off-chain systems in FUZE, including what truths belong on-chain, what truths remain off-chain, how policy binds contract actions, how cross-layer coordination must work, and how reconciliation, failure handling, and correction must preserve ownership clarity
- Primary Upstream References:
  - `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
  - `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
  - `PLATFORM_ARCHITECTURE_SPEC.md`
  - `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
  - `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
  - `SYSTEM_SPEC_INDEX.md`
  - `API_SPEC_INDEX.md`
  - `DOCS_SPEC_INDEX.md`
  - `CHAIN_ARCHITECTURE_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
  - `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
- Primary Downstream Dependents:
  - `CHAIN_ARCHITECTURE_SPEC.md`
  - `ETHEREUM_TOKEN_LAYER_SPEC.md`
  - `BASE_PLATFORM_CREDITS_LAYER_SPEC.md`
  - `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md`
  - `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
  - `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
  - `PLATFORM_CREDITS_SPEC.md`
  - `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
  - `PAYMENT_RAILS_INTEGRATION_SPEC.md`
  - `TRANSPARENCY_REPORTING_SPEC.md`
  - `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
  - `TREASURY_CONTROL_POLICY_SPEC.md`
  - `VAULT_ACTION_POLICY_SPEC.md`
  - `MULTISIG_AND_TIMELOCK_SPEC.md`
  - `API_ARCHITECTURE_SPEC.md`
  - `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
  - product integration specs

---

## Purpose

This specification defines the canonical division of responsibilities between on-chain and off-chain systems in the FUZE ecosystem.

Its purpose is to make explicit:
- which categories of truth belong on-chain
- which categories of truth remain off-chain
- which policy, accounting, product, reporting, and orchestration functions must remain off-chain even when they affect on-chain outcomes
- how FUZE’s layered chain model interacts with the platform boundary model
- how cross-layer coordination, normalization, snapshotting, funding, claim execution, and reporting must work without collapsing ownership
- how recovery, correction, and reconciliation must be handled when hybrid flows partially fail

This specification is foundational because FUZE is not purely on-chain and not purely off-chain. It is a platform-first ecosystem that intentionally combines:
- Ethereum token participation truth
- Base operational credits and payout execution rails
- shared off-chain platform and product systems
- external payment and provider integrations
- off-chain accounting and policy determination
- transparency and reporting obligations

In such a system, confusion about on-chain versus off-chain responsibility quickly becomes confusion about authority, trust, accounting meaning, payout meaning, and product behavior. This file exists to prevent that confusion.

---

## Scope

This specification governs:

- the canonical role of on-chain and off-chain systems in FUZE
- the distinction between chain truth, platform truth, product truth, external-provider truth, policy truth, execution truth, and reporting truth
- what categories of state should be committed on-chain
- what categories of state must remain off-chain for privacy, policy, flexibility, or product-complexity reasons
- how Ethereum token truth, Base credits truth, Base payout execution truth, reserve/vault truth, and off-chain systems interact
- the cross-layer role of payment verification, accounting, eligibility preparation, treasury decisions, reporting publication, and governance controls
- the correction, supersession, reconciliation, and failure-handling rules across the on-chain/off-chain boundary
- the implications of this boundary for APIs, events, storage, audit, and operations

This specification does not define:

- detailed contract ABI or storage layout
- endpoint-by-endpoint API contracts
- exact payout entitlement encoding format
- exact snapshot materialization implementation
- exact ledger table schemas
- exact queue or worker implementation
- exact reporting page or public UI design
- exact policy contents for all domains

Those are refined in downstream specifications.

---

## Out of Scope

This specification is explicitly out of scope for:

- chain-specific smart-contract source code design details
- low-level RPC/indexing infrastructure decisions
- exact provider-integration implementation details
- exact user-facing wallet UX
- full accounting-policy text
- detailed treasury operating procedure
- exact public-report formatting
- exact database topology or service split

---

## Design Goals

The design goals of FUZE’s on-chain/off-chain responsibility model are:

1. Preserve chain state only where public verifiability, contract enforceability, or visible structural separation provide real value.
2. Preserve off-chain execution where policy logic, privacy, product complexity, accounting interpretation, or operational practicality require it.
3. Make cross-layer coordination explicit rather than informal.
4. Prevent conceptual collapse among token balances, credits balances, accounting profit, payout execution, and product usage.
5. Support a real multi-product SaaS platform without pretending all meaningful business logic belongs on-chain.
6. Make policy-bound chain actions auditable and explainable.
7. Strengthen transparency by requiring both chain structure and off-chain reporting.
8. Support reconciliation and correction without weakening ownership clarity.
9. Preserve future platform scale without forcing every capability onto one chain or into one storage model.
10. Make the hybrid model intelligible to engineers, operators, auditors, and public readers.

---

## Non-Goals

This specification is not intended to:

- force all FUZE systems fully on-chain for ideology or appearance
- reduce on-chain structure to symbolic contracts with no real responsibility
- treat off-chain systems as informal, discretionary, or second-class
- imply that accounting truth, product logic, AI orchestration, or workspace-private data should move wholesale on-chain
- imply that on-chain state alone explains the full economic meaning of the platform
- collapse privacy-sensitive or workspace-sensitive product state into public chain storage
- let product domains own chain roles by default
- let off-chain reporting silently redefine chain-owned truth
- let chain presence imply ownership of business meaning never committed on-chain

The hybrid model is not a compromise. It is an intentional architectural design.

---

## Core Principles

### 1. Hybrid Architecture Principle
FUZE is intentionally hybrid. On-chain and off-chain systems each own bounded categories of truth and function. Neither side is “the whole system.”

### 2. On-Chain Specificity Principle
On-chain contracts are canonical only for truths explicitly committed to the contract design and chain rail. On-chain presence does not automatically transfer ownership of broader policy, accounting, or product meaning.

### 3. Off-Chain Policy and Orchestration Principle
Off-chain systems own policy interpretation, accounting treatment, execution coordination, reporting, and product behavior unless those responsibilities are explicitly committed on-chain.

### 4. Explicit Connection Principle
Cross-layer behavior must be linked through explicit policy references, verified inputs, normalized transitions, snapshot or dataset references, and auditable execution lineage.

### 5. Economic Separation Principle
Token participation, Platform Credits, accounting profit, funded payout cycles, and product usage remain distinct concepts even when they are operationally connected.

### 6. Transparency Requires Both Layers Principle
Public trust in FUZE requires both visible on-chain structure and off-chain reporting/explanation. One layer alone is insufficient.

### 7. Reconciliation Is Architectural Principle
Reconciliation across on-chain and off-chain layers is not an optional finance-side cleanup task. It is part of the architecture.

### 8. Correction Lineage Principle
Mistakes across the boundary must be corrected through explicit lineage, supersession, reversal, or compensating records rather than silent reinterpretation.

---

## Canonical Definitions

### On-Chain Truth
Truth explicitly committed to a contract and chain environment and authoritative within that contract’s intended role.

### Off-Chain Truth
Truth owned by FUZE platform or product systems outside chain state, including policy, product behavior, accounting, workflow, reporting, and normalized provider outcomes.

### Chain-Adjacent Coordination
Off-chain orchestration or adapter behavior that interacts with contracts while preserving the distinction between contract-native truth and off-chain policy/execution truth.

### Policy Truth
The published or internal governing logic that determines how actions should be interpreted, approved, classified, funded, excluded, expired, adjusted, or reported.

### Accounting Truth
The off-chain business interpretation of revenue, cost, refund, adjustment, distributable profit, reserve treatment, and related financial meaning.

### Eligibility Truth
The authoritative cycle-specific off-chain dataset that determines which holders are entitled to participate in a given payout cycle under the applicable policy.

### Execution Truth
Operational truth about processing status, retries, submissions, confirmations, funding readiness, callback processing, or job progression. Execution truth does not automatically equal business truth.

### Normalized External Outcome
A FUZE-approved internal consequence derived from verified provider or chain-adjacent input through explicit normalization logic.

### Committed Economic State
A contract-visible balance, allocation, role, or claim state whose meaning is intentionally enforced or represented on-chain.

### Derived Reporting State
A report, registry artifact, transparency view, or public explanation built from canonical sources without replacing them.

---

## Architectural Position in the Spec Hierarchy

This document sits below:

- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`

and above:

- chain-specific layer specs
- credits, payout, treasury, snapshot, registry, and reporting specs
- chain-adjacent API and event specs
- product integration specs that consume chain-aware context

This document does not redefine the top-level layer model. It refines the specific boundary between chain-committed truth and off-chain platform truth.

---

## Canonical Responsibility Principle

The primary principle of FUZE’s hybrid architecture is:

> on-chain systems hold canonical public state and bounded enforceable economic or governance logic where public commitment creates real trust value, while off-chain systems handle policy interpretation, product execution, payment verification, accounting, reporting, workflow coordination, and privacy-sensitive state where flexibility, privacy, or operational complexity require it.

This means:

- on-chain does not mean “everything important”
- off-chain does not mean “informal” or “untrusted”
- the boundary is about role correctness, not ideology
- trust comes from clear separation plus explicit linkage, not from pretending one layer should do every job

---

## Why FUZE Uses a Hybrid On-Chain / Off-Chain Model

FUZE uses a hybrid model because it is building a real platform business, not a narrow contract-only primitive and not a conventional opaque SaaS stack with decorative Web3 elements.

The ecosystem contains:
- a canonical participation token on Ethereum
- an internal credits layer on Base
- a stablecoin payout execution layer on Base
- treasury and reserve contracts
- subscriptions and usage billing
- payment normalization across external rails
- AI orchestration across multiple products
- workflow automation, queues, retries, and scheduled tasks
- workspace-scoped and privacy-sensitive product data
- accounting and reporting functions
- governance-sensitive policy systems

These functions do not all belong in one place.

Some functions benefit from public enforceability and visible contract structure. Others would become impractical, privacy-breaking, or conceptually distorted if forced on-chain. The hybrid model therefore exists because the architecture is trying to be correct.

---

## Responsibility Layers Across the Boundary

FUZE must reason about on-chain/off-chain behavior across six layers of concern.

### 1. Contract Truth Layer
Owns chain-committed state such as balances, claims, role references, pause state, and other explicitly encoded contract facts.

### 2. Platform Policy Layer
Owns cross-product rules, economic meaning, lifecycle policy, exclusion rules, spend rules, expiry rules, and other off-chain policy logic.

### 3. Product Execution Layer
Owns product objects, product workflows, product AI behavior, and workspace-private operational state.

### 4. Accounting and Treasury Layer
Owns revenue interpretation, cost treatment, distributable profit determination, reserve treatment, and funding-decision logic.

### 5. Reporting and Transparency Layer
Owns public-safe publication, registry artifacts, payout ledgers, transparency reports, and explanatory outputs.

### 6. Control and Governance Layer
Owns approvals, restrictions, timelock-sensitive decisions, treasury controls, and emergency or recovery behavior.

These layers interact heavily, but they do not share undifferentiated ownership.

---

## Canonical On-Chain Responsibilities

On-chain systems in FUZE should be used where contract-enforced state, public visibility, or visible structural separation create real trust value.

At minimum, the following categories belong on-chain.

### 1. Canonical Token Participation State
The FUZE token layer on Ethereum should hold:
- token identity
- total supply state
- holder-balance truth
- transfer and ownership state
- other explicitly committed token-layer state

### 2. Platform Credits Committed State
The Base credits layer should hold, where formally committed on-chain:
- credits issuance commitment state
- credits balance state by approved scope
- spend, reservation, release, reversal, or adjustment representation where the credits design commits those transitions on-chain
- the committed internal commerce layer state

### 3. Stablecoin Payout Execution State
The Base payout layer should hold:
- payout cycle funding state
- claim-open / claim-closed / finalized state
- claim execution state
- over-claim prevention state
- contract balance state for funded cycles
- other explicitly committed payout-execution state

### 4. Reserve and Vault Contract State
Ethereum-side reserve/vault architecture should hold:
- allocation contract state
- vesting state
- category-bounded reserve state
- contract-specific permission and separation state
- other explicitly committed reserve/vault controls

### 5. Governance-Critical Contract Controls
Where relevant, on-chain systems should hold:
- multisig ownership or control references
- timelock-based role protections
- pause or constrained administrative controls
- contract registry relationships
- other explicitly committed governance controls

### On-Chain Principle
On-chain state should represent the enforceable, publicly visible structural layer of FUZE. It should not try to absorb the full complexity of accounting, product behavior, policy interpretation, or reporting meaning.

---

## Canonical Off-Chain Responsibilities

Off-chain systems in FUZE should handle functions where policy logic, privacy, product complexity, operational flexibility, or business interpretation make off-chain execution more appropriate.

At minimum, the following categories belong off-chain.

### 1. Payment Verification and Normalization
Off-chain systems own:
- Stripe and equivalent provider verification
- stablecoin deposit normalization where applicable
- Telegram Stars verification
- Apple and Google billing verification
- fraud and chargeback handling
- external payment-rail interpretation
- transition from raw provider signals into FUZE-recognized internal commercial outcomes

### 2. Accounting and Treasury Interpretation
Off-chain systems own:
- revenue recognition logic
- cost and expense treatment
- refund and reversal treatment
- distributable profit determination
- treasury reconciliation
- reserve accounting interpretation
- policy-driven financial classification

### 3. Snapshot and Eligibility Preparation
Although based on on-chain holder truth, off-chain systems own:
- snapshot reference selection
- raw holder extraction
- address classification
- exclusion application
- entitlement dataset construction
- proof/root preparation or equivalent dataset preparation where applicable
- cycle-specific eligibility publication inputs

### 4. Product Domain Logic
Off-chain product systems own:
- product objects and product-private workflows
- QTB intelligence logic
- AIMM operations logic
- ZAGA utility-system logic
- AIE recommendation logic
- HerHelp app and workflow generation
- Botmad workflow-support and execution assistance
- workspace-scoped product state
- user-level and team-level operational data

### 5. AI Orchestration and Execution
Off-chain systems own:
- model routing
- context assembly
- prompt and instruction layering
- provider calls
- tool orchestration
- output validation
- fallback and retry behavior
- AI usage metering hooks

### 6. Workflow and Automation Coordination
Off-chain systems own:
- queue-backed orchestration
- retries and backoff
- scheduled tasks
- approval gates
- job routing
- multi-step execution state
- operator review and support flows

### 7. Reporting and Transparency Publication
Off-chain systems own:
- transparency reports
- payout ledgers
- contract and wallet registry publication
- investor/community reporting
- analytics and observability outputs
- structured human-readable explanation of chain state and policy outcomes

### 8. Privacy-Sensitive and Workspace-Sensitive State
Off-chain systems own:
- business documents
- generated app content
- operator notes
- workflow history
- workspace-private settings
- permissioned product artifacts
- non-public account and workspace state

### Off-Chain Principle
Off-chain systems are where FUZE executes the policy-sensitive, privacy-sensitive, product-heavy, and interpretation-heavy logic of the platform. They are not secondary to chain structure. They are essential parts of the architecture.

---

## Responsibilities by Economic Layer

One of the clearest ways to understand the on-chain/off-chain division is by economic layer.

### Token Participation Layer
**On-chain responsibility:**
- Ethereum token contract
- holder balances
- other token-layer committed state

**Off-chain responsibility:**
- holder analytics
- holder-rank derivation
- snapshot extraction
- eligibility interpretation
- public explanation
- ecosystem policy interpretation

### Platform Credits Layer
**On-chain responsibility:**
- Base credits committed state
- balance and mutation representation where the credits design commits those transitions on-chain

**Off-chain responsibility:**
- payment normalization
- credits policy
- billing logic
- spend policy
- refund interpretation
- usage attribution
- commercial reporting

### Profit Participation Layer
**On-chain responsibility:**
- Base payout cycle funding state
- claim execution state
- finalized cycle claim-state representation

**Off-chain responsibility:**
- distributable profit determination
- eligibility dataset construction
- exclusion logic
- cycle reporting
- audit support
- treasury approval and funding readiness

### Reserve and Treasury Layer
**On-chain responsibility:**
- vault balances and contract-visible reserve controls
- timelock/multisig execution state

**Off-chain responsibility:**
- treasury decision records
- reserve accounting interpretation
- action approvals
- policy-defined action classification
- reporting and contextual explanation

### Economic-Layer Principle
On-chain layers hold enforceable and visible state. Off-chain layers determine how and why that state should be created, funded, interpreted, constrained, and reported.

---

## Responsibilities by Product Layer

FUZE also requires a product-level on-chain/off-chain rule set.

### Products Do Not Own Chain Roles by Default
Products such as QTB, AIMM, ZAGA, AIE, HerHelp, and Botmad should not independently define token truth, credits semantics, payout logic, or reserve logic.

### Products May Consume Chain-Aware Context
Products may consume:
- wallet-aware participation context
- holder-rank or privilege signals
- credits-balance state
- entitlement state influenced by chain or platform policy
- payout-status visibility where relevant

### Products Remain Primarily Off-Chain Domains
Product objects, AI actions, workspace data, private outputs, and product-local reports remain primarily off-chain unless a very strong platform-approved reason exists to place a bounded element on-chain.

### Product Principle
Products are consumers of chain-aware platform context where relevant, but they remain primarily off-chain execution domains. This preserves scale, privacy, and shared-platform coherence.

---

## On-Chain State Is Not the Same as Full Economic Meaning

A central FUZE principle is that on-chain state and full economic meaning are not identical.

Examples:
- credits issued or committed on Base do not by themselves prove distributable profit exists
- a funded payout cycle on Base does not by itself explain how distributable profit was determined
- a token balance on Ethereum does not by itself define final payout eligibility without policy treatment
- a reserve contract balance does not by itself explain what treasury is allowed to do with it

### Why This Matters
If FUZE overstates what chain state alone means, it creates false certainty. If FUZE understates chain state, it weakens transparency. The correct model is explicit pairing:
- chain state provides enforceable structure
- off-chain systems provide interpretation, classification, policy, accounting, and explanation

---

## Policy Lives Mostly Off-Chain, But Must Bind On-Chain Outcomes

In FUZE, much of the platform’s policy logic lives off-chain, but that policy must still govern how on-chain actions occur.

Examples include:
- which external payment events can issue Platform Credits
- which address categories are excluded from payout eligibility
- how credit classes may be spent, expired, reversed, or adjusted
- how distributable profit is determined
- what counts as a valid payout cycle
- when reserve or vault actions are allowed
- which product actions require premium billing or approval

### Policy Requirements
Policy that binds on-chain outcomes must be:
- explicit
- versionable
- auditable
- linked to the resulting contract operations
- stable enough to support public explanation and reconciliation

### Policy Principle
FUZE must avoid both extremes:
- “everything is discretionary off-chain interpretation”
- “policy does not exist because the chain will somehow explain itself”

The stronger architecture is explicit policy controlling bounded contract actions.

---

## State-Class Separation Across the Boundary

FUZE must distinguish at least the following state classes.

### Chain-Committed State
A contract-owned state that is canonical because it is explicitly committed on-chain.

### Off-Chain Canonical Business State
A platform- or product-owned state that remains canonical off-chain.

### Policy-Derived Canonical State
A governed off-chain output such as an eligibility dataset or entitlement input that becomes authoritative for a bounded process.

### Execution State
A job, submission, callback, reconciliation, or workflow state that explains operational progression but does not itself replace business meaning.

### External Raw State
Provider- or client-owned state not yet normalized by FUZE.

### Derived Reporting State
A report, registry artifact, dashboard output, or transparency view derived from canonical sources.

When disagreement occurs, the owning canonical state for the relevant category wins over execution, external raw, or derived reporting state.

---

## Normalization Rules Across the Boundary

Any signal crossing the on-chain/off-chain boundary must pass through explicit normalization rules when FUZE intends to create or mutate off-chain canonical state.

### Provider-to-Off-Chain Normalization
External payment or purchase signals must be verified before they become commercial truth.

### Chain-to-Off-Chain Normalization
Contract events, balances, or state reads may inform off-chain systems, but off-chain systems must not infer unsupported business meaning from chain visibility alone.

### Off-Chain-to-On-Chain Preparation
Funding, issuance, or entitlement preparation must be validated off-chain before chain submission occurs.

### Normalization Principle
Raw signals do not become business truth merely because they exist. They become FUZE-recognized outcomes only after verification, interpretation, and owner-controlled transition.

---

## Reconciliation Responsibilities

Because FUZE is hybrid, reconciliation is a first-class architectural responsibility.

### Reconciliation is required between:
- payment verification and credits issuance
- credits mutation records and Base credits committed state
- Ethereum holder truth and eligibility datasets
- eligibility datasets and Base payout entitlements
- treasury/accounting records and funded payout cycles
- contract balances and transparency reports
- product consumption data and credits or billing records
- governance decisions and executed contract actions

### Reconciliation Requirements
Reconciliation must:
- preserve references across owner domains
- preserve correction lineage
- identify timing gaps versus semantic mismatches
- distinguish pending confirmation from contradiction
- support human-readable explanation where trust surfaces depend on it

### Reconciliation Principle
Hybrid architecture is only trustworthy when coordination is disciplined. Reconciliation is part of the architecture, not a narrow finance-only afterthought.

---

## Failure Handling and Recovery Across the Boundary

Hybrid architecture requires explicit failure-handling rules.

### Common failure types include:
- payment verified off-chain but credits commitment delayed on-chain
- credits mutation committed on-chain but product workflow not finalized off-chain
- snapshot prepared incorrectly before payout entitlement publication
- payout contract funded before reporting metadata is fully synchronized
- treasury approval granted but chain execution delayed or failed
- support correction needed after chain-visible action already occurred
- AI-heavy product usage settled incorrectly relative to commercial policy

### Recovery Principles
- failure lineage must remain visible
- on-chain mistakes must not be hidden by silent off-chain edits
- off-chain corrections must preserve linkage to affected on-chain records
- economic corrections must remain auditable
- incident response must distinguish among:
  - chain-state correction
  - off-chain policy correction
  - reporting correction
  - operational retry/replay

### Safe-Recovery Principle
The correct response to hybrid failure is explicit, owner-aligned recovery with preserved lineage, not conceptual collapse or hidden data rewriting.

---

## Governance Responsibilities Across the Boundary

Governance in FUZE also spans both layers.

### On-Chain Governance Responsibilities May Include:
- contract ownership
- multisig control
- timelock-protected actions
- reserve-vault permissions
- payout-execution controls
- pause or emergency controls
- contract-registry visibility

### Off-Chain Governance Responsibilities May Include:
- policy setting
- approval workflows
- treasury decisions before funding
- eligibility interpretation rules
- reporting publication
- operational recovery and incident handling
- rollout decisions
- support and correction approval pathways

### Governance Principle
Not all governance becomes on-chain simply because contracts exist. FUZE governance is hybrid:
- enforceable contract controls on-chain
- policy and procedural governance off-chain
- both linked through explicit approval and reporting discipline

Governance authority does not collapse ownership of the underlying business domain.

---

## Transparency Requirements Across the Boundary

A transparency-first platform cannot rely only on on-chain contracts or only on off-chain reports. FUZE requires both.

### On-Chain Provides:
- public state
- visible contract separation
- holder truth
- funding visibility
- claim execution history
- reserve architecture visibility
- enforceable role structure

### Off-Chain Provides:
- human-readable explanation
- accounting context
- payout-cycle interpretation
- eligibility methodology
- investor/community summaries
- support and auditability
- product-level context where relevant

### Transparency Principle
On-chain state without reporting can be technically visible but strategically opaque. Reporting without on-chain structure can be rhetorically clear but weakly verifiable. FUZE therefore requires both.

---

## API / Contract Implications

This boundary model imposes the following downstream API rules:

- chain-adjacent APIs must distinguish preparation, submission, confirmation, reconciliation, and reporting concerns
- APIs must not imply that a non-owning layer is the canonical owner of meaning
- public-read status APIs must distinguish contract truth from off-chain business interpretation
- internal mutation APIs must route through owner-controlled preparation and validation before chain submission
- provider callbacks and chain event ingestion must terminate in normalization boundaries, not directly in unrelated business domains
- contract interaction APIs must preserve references to policy versions, cycle IDs, funding references, or dataset references where relevant

An API surface that cannot clearly answer “what is canonical here, and what is only execution or reporting state?” is incomplete.

---

## Event / Async Implications

This boundary model imposes the following event and async rules:

- chain events are coordination and visibility signals, not substitutes for business-policy ownership
- retries must not create duplicate business outcomes
- execution records remain execution truth unless the owning business domain explicitly adopts the result
- snapshot publication, funding readiness, and claim-cycle progression must preserve lineage across off-chain and on-chain boundaries
- reporting pipelines may lag, but may not overwrite canonical chain or off-chain business truth

---

## Data Model / Storage Implications

This boundary model imposes the following data-discipline rules:

- chain-derived records and off-chain policy records must remain distinguishable
- off-chain canonical business entities must remain distinguishable from chain-committed entities
- eligibility datasets and payout inputs must preserve provenance to snapshot references and policy references
- execution entities such as chain submission records or callback processing records must remain distinct from business-domain entities
- reporting/publication stores must preserve traceability back to chain and off-chain canonical sources
- caches and dashboards must not become hidden truth owners

---

## Security / Risk / Abuse Controls

This on-chain/off-chain boundary is also a security boundary.

If the division is weak:
- products may overreach into chain-sensitive roles
- providers may mutate economic outcomes without proper normalization
- dashboards or public summaries may redefine business meaning
- off-chain systems may overstate what chain state proves
- chain events may be misread as full accounting truth
- governance-sensitive actions may occur through ordinary runtime flows
- recovery actions may destroy lineage instead of preserving it

All downstream security, abuse-prevention, monitoring, treasury, and reporting specifications must preserve this boundary.

---

## Minimum Conceptual Entities

At minimum, the on-chain/off-chain responsibility model should recognize the following conceptual entities.

### On-Chain Responsibility Entities
- Ethereum token contract
- Base credits contract / committed credits state
- Base payout contract
- reserve and vault contracts
- multisig and timelock control references
- chain-visible balance, role, and mutation states

### Off-Chain Responsibility Entities
- payment verification systems
- accounting and treasury systems
- snapshot and eligibility pipeline
- product domain services
- AI orchestration systems
- workflow, queue, and scheduler systems
- reporting and transparency publication systems
- support and recovery systems
- governance approval records

### Coordination Entities
- correlation IDs across systems
- policy version references
- snapshot references
- entitlement dataset references
- funding references
- reporting references
- reconciliation references
- audit lineage references
- cycle and claim references

These are conceptual minima. Detailed schemas and service boundaries are refined elsewhere.

---

## Migration / Compatibility / Supersession Considerations

- migrations must not silently move responsibility from off-chain policy systems into contracts or vice versa without explicit specification change
- compatibility layers may temporarily support older flows, but must preserve canonical ownership and traceability
- if a downstream contract or integration evolves, the owning on-chain/off-chain role must remain explicit
- if older materials imply that chain state alone explains the full platform meaning, this refined specification supersedes that interpretation within its scope

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
- `CHAIN_ARCHITECTURE_SPEC.md`
- identity/account/auth/session foundation docs
- workspace/access-control foundation docs

This specification directly governs or materially informs:

- `ETHEREUM_TOKEN_LAYER_SPEC.md`
- `BASE_PLATFORM_CREDITS_LAYER_SPEC.md`
- `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md`
- `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `PAYMENT_RAILS_INTEGRATION_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`
- `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
- `TREASURY_CONTROL_POLICY_SPEC.md`
- `VAULT_ACTION_POLICY_SPEC.md`
- `MULTISIG_AND_TIMELOCK_SPEC.md`
- `API_ARCHITECTURE_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- product integration specs

---

## Explicitly Deferred Items

The following are intentionally deferred to downstream specifications:

- exact Base credits contract design
- exact payout entitlement encoding model
- exact snapshot proof format
- exact reporting publication latency requirements
- exact incident escalation procedures by domain
- exact provider-by-provider normalization mechanics
- exact treasury funding procedure details
- exact public registry schema and visibility rules

These do not weaken the canonical on-chain/off-chain responsibility model established here.

---

## Final Normative Summary

FUZE uses a deliberate hybrid architecture. On-chain systems hold canonical committed state only where public verifiability, contract enforceability, and structural visibility create real trust value. Off-chain systems own policy interpretation, product execution, payment verification, accounting meaning, eligibility construction, workflow coordination, privacy-sensitive state, and transparency reporting where flexibility, privacy, and operational depth are required.

Ethereum remains the canonical participation layer for FUZE token truth. Base hosts the operational credits and payout-execution layers where those capabilities are formally committed. Products remain primarily off-chain domains. Governance spans both enforceable contract controls and off-chain policy/approval systems. Reporting and transparency also span both layers.

On-chain state is not the same as full business meaning. Off-chain policy is not an excuse for hidden discretion. The correct FUZE model is explicit role separation plus explicit linkage through policy references, normalized inputs, auditable coordination, and reconciliation lineage. Any downstream design that weakens this boundary is inconsistent with the FUZE platform model.
