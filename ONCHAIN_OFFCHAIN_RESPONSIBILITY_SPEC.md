# FUZE On-Chain / Off-Chain Responsibility Specification

## Document Metadata
- **Document Name:** `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- **Document Type:** Canonical refined system specification
- **Status:** Active refined system spec
- **Version:** 1.0
- **Effective Date:** 2026-04-21
- **Last Updated:** 2026-04-21
- **Reviewed On:** 2026-04-21
- **Document Owner:** FUZE Platform Architecture (canonical owner); named individual owner not explicitly specified in the retrieved governing materials
- **Approval Authority:** Not explicitly specified in the retrieved governing materials; constitutional approval authority remains governed by `REFINED_SYSTEM_SPEC_INDEX.md` and the active FUZE approval workflow
- **Review Cadence:** Not explicitly specified in the retrieved governing materials; SHOULD be reviewed whenever chain commitments, off-chain policy boundaries, payout design, credits design, reserve controls, transparency obligations, or cross-layer recovery rules materially change
- **Governing Layer:** Platform constitution / chain boundary / on-chain-off-chain responsibility
- **Parent Registry:** `REFINED_SYSTEM_SPEC_INDEX.md`
- **Primary Audience:** Platform architecture, backend engineering, contracts engineering, product engineering, finance systems, treasury, security, governance, reporting, audit, operations, implementation-contract authors
- **Primary Purpose:** Define the canonical division of responsibility between on-chain and off-chain systems in FUZE, including which truths belong on-chain, which truths remain off-chain, how policy binds contract actions, how cross-layer coordination must work, and how reconciliation, failure handling, and correction must preserve ownership clarity
- **Primary Upstream References:**
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
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
  - `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
- **Primary Downstream Dependents:**
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
  - product integration specifications
- **Supersedes:** Earlier or weaker interpretations that collapse chain truth into full business meaning, allow off-chain reporting to redefine contract-owned state, let product domains own chain-sensitive roles by default, or treat external/provider input as FUZE truth before normalization
- **Superseded By:** None currently defined
- **Related Decision Records:** Not explicitly linked in the retrieved governing materials
- **Canonical Status Note:** This document is the canonical refined interpretation of the FUZE on-chain/off-chain boundary beneath the top-level boundary, overview, architecture, domain-ownership, and entity-ownership specifications. It is normative unless an explicitly higher constitutional source overrides it.
- **Implementation Status:** Normative architecture and implementation-contract source; downstream contracts, APIs, events, ledgers, workflows, reporting systems, runbooks, and recovery procedures MUST align
- **Approval Status:** Draft refined canonical specification pending explicit approval workflow
- **Change Summary:** Consolidated the active refined interpretation into one production-grade governing document; normalized metadata; clarified truth classes, economic-layer boundaries, policy-binding rules, normalization duties, reconciliation posture, transparency discipline, governance split, and downstream implementation guardrails

## Title
FUZE On-Chain / Off-Chain Responsibility Specification

## Purpose
This specification defines the canonical division of responsibilities between on-chain and off-chain systems in the FUZE ecosystem.

Its purpose is to make explicit:
- which categories of truth belong on-chain
- which categories of truth remain off-chain
- which policy, accounting, product, reporting, and orchestration functions MUST remain off-chain even when they affect on-chain outcomes
- how FUZE’s layered chain model interacts with the top-level platform boundary model
- how cross-layer coordination, normalization, snapshotting, funding, claim execution, and reporting must work without collapsing ownership
- how recovery, correction, and reconciliation must be handled when hybrid flows partially fail

This specification is foundational because FUZE is intentionally hybrid. FUZE combines Ethereum token participation truth, Base operational credits and payout execution rails, shared off-chain platform and product systems, external payment and provider integrations, off-chain accounting and policy determination, and transparency obligations. Confusion about this boundary quickly becomes confusion about authority, trust, accounting meaning, payout meaning, and product behavior. This file exists to prevent that confusion.

## Scope
This specification governs:
- the canonical role of on-chain and off-chain systems in FUZE
- the distinction between chain truth, platform truth, product truth, provider-input truth, policy truth, execution truth, and reporting truth
- what categories of state SHOULD be committed on-chain
- what categories of state MUST remain off-chain for privacy, policy, flexibility, or product-complexity reasons
- how Ethereum token truth, Base credits truth, Base payout execution truth, reserve/vault truth, and off-chain systems interact
- the cross-layer role of payment verification, accounting, eligibility preparation, treasury decisions, reporting publication, governance controls, and public visibility
- the correction, supersession, reconciliation, and failure-handling rules across the on-chain/off-chain boundary
- the implications of this boundary for APIs, events, storage, audit, operations, and implementation contracts

This specification does not define:
- detailed contract ABI or storage layout
- endpoint-by-endpoint API contracts
- exact payout entitlement encoding format
- exact snapshot proof or materialization implementation
- exact ledger table schemas
- exact queue, worker, or callback runtime implementation details
- exact reporting page or public UI design
- exact policy contents for every downstream domain

Those are refined in downstream specifications.

## Out of Scope
This specification is explicitly out of scope for:
- chain-specific smart-contract source code design details
- low-level RPC, indexing, or event-ingestion infrastructure decisions
- exact provider-integration mechanics by rail or vendor
- exact user-facing wallet UX
- full accounting-policy text
- detailed treasury operating procedures
- exact public-report formatting
- exact service-decomposition or database-topology choices

## Design Goals
The design goals of FUZE’s on-chain/off-chain responsibility model are to:
1. preserve chain state only where public verifiability, contract enforceability, or visible structural separation create real value
2. preserve off-chain execution where policy logic, privacy, product complexity, accounting interpretation, or operational practicality require it
3. make cross-layer coordination explicit rather than informal
4. prevent conceptual collapse among token balances, credits balances, accounting profit, payout execution, and product usage
5. support a real multi-product SaaS platform without pretending all meaningful business logic belongs on-chain
6. make policy-bound chain actions auditable and explainable
7. strengthen transparency by requiring both chain structure and off-chain reporting
8. support reconciliation and correction without weakening ownership clarity
9. preserve future platform scale without forcing every capability onto one chain or into one storage model
10. make the hybrid model intelligible to engineers, operators, auditors, and public readers

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
- let chain presence imply ownership of business meaning never formally committed on-chain

The FUZE hybrid model is an intentional architectural design, not a compromise.

## Core Principles
### 1. Hybrid Architecture Principle
FUZE is intentionally hybrid. On-chain and off-chain systems each own bounded categories of truth and function. Neither side is “the whole system.”

### 2. On-Chain Specificity Principle
On-chain contracts are canonical only for truths explicitly committed to the contract design and the relevant chain rail. On-chain presence alone MUST NOT transfer ownership of broader policy, accounting, eligibility, reporting, or product meaning.

### 3. Off-Chain Policy and Orchestration Principle
Off-chain systems own policy interpretation, accounting treatment, execution coordination, reporting, and product behavior unless those responsibilities are explicitly committed on-chain.

### 4. Explicit Connection Principle
Cross-layer behavior MUST be linked through explicit policy references, verified inputs, normalized transitions, snapshot or dataset references, auditable execution lineage, and stable identifiers.

### 5. Economic Separation Principle
Token participation, Platform Credits, accounting profit, funded payout cycles, reserve state, and product usage remain distinct concepts even when operationally connected.

### 6. Transparency Requires Both Layers Principle
Public trust in FUZE requires both visible on-chain structure and off-chain reporting/explanation. One layer alone is insufficient.

### 7. Reconciliation Is Architectural Principle
Reconciliation across on-chain and off-chain layers is not a narrow finance-only cleanup task. It is part of the architecture.

### 8. Correction Lineage Principle
Mistakes across the boundary MUST be corrected through explicit lineage, supersession, reversal, or compensating records rather than silent reinterpretation.

### 9. Governance Split Principle
Governance in FUZE spans enforceable contract controls on-chain and policy/procedural approval off-chain. Governance authority MUST NOT erase the ownership of the underlying business domain.

### 10. Product Non-Encroachment Principle
Products MAY consume chain-aware context where appropriate, but they MUST NOT redefine token truth, credits semantics, payout logic, reserve logic, or chain-sensitive shared platform primitives.

## Canonical Definitions
### On-Chain Truth
Truth explicitly committed to a contract and chain environment and authoritative within that contract’s intended role.

### Off-Chain Truth
Truth owned by FUZE platform or product systems outside chain state, including policy, product behavior, accounting, workflow, reporting, and normalized provider outcomes.

### Chain-Adjacent Coordination
Off-chain orchestration or adapter behavior that interacts with contracts while preserving the distinction between contract-native truth and off-chain policy, execution, or accounting truth.

### Policy Truth
The governing logic that determines how actions should be interpreted, approved, classified, funded, excluded, expired, adjusted, or reported.

### Accounting Truth
The off-chain business interpretation of revenue, cost, refund, adjustment, distributable profit, reserve treatment, and related financial meaning.

### Eligibility Truth
The authoritative cycle-specific off-chain dataset that determines which holders are entitled to participate in a given payout cycle under the applicable policy.

### Execution Truth
Operational truth about processing status, retries, submissions, confirmations, funding readiness, callback processing, or job progression. Execution truth does not automatically equal business truth.

### Normalized External Outcome
A FUZE-approved internal consequence derived from verified provider or chain-adjacent input through explicit normalization logic.

### Committed Economic State
A contract-visible balance, allocation, role, entitlement, funding, or claim state whose meaning is intentionally enforced or represented on-chain.

### Derived Reporting State
A report, registry artifact, transparency view, dashboard, or public explanation built from canonical sources without replacing them.

## Truth Class Taxonomy
FUZE MUST distinguish the following truth classes wherever relevant:
1. **Semantic truth** — what a concept means and which owner governs that meaning
2. **Policy truth** — what rules govern permissible action and interpretation
3. **Runtime truth** — what is happening during execution now
4. **Ledger / storage truth** — where the authoritative durable record is maintained
5. **Provider-input truth** — raw external or chain-originating input before owner-controlled normalization
6. **Implementation-adapter truth** — verification, translation, mediation, or normalization behavior at a boundary
7. **Execution truth** — job, submission, callback, retry, and progression state
8. **Projection / reporting truth** — dashboards, analytics, transparency outputs, and publication artifacts
9. **Presentation truth** — UX wording, display composition, and explanatory formatting

These truth classes MUST NOT be collapsed into one undifferentiated model.

## Architectural Position in the Spec Hierarchy
This document sits below:
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`

and above:
- `CHAIN_ARCHITECTURE_SPEC.md`
- `ETHEREUM_TOKEN_LAYER_SPEC.md`
- `BASE_PLATFORM_CREDITS_LAYER_SPEC.md`
- `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md`
- `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`
- `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
- `TREASURY_CONTROL_POLICY_SPEC.md`
- `VAULT_ACTION_POLICY_SPEC.md`
- `MULTISIG_AND_TIMELOCK_SPEC.md`
- `API_ARCHITECTURE_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- relevant product integration specifications

This document does not redefine the top-level layer model. It refines the specific boundary between chain-committed truth and off-chain platform truth.

## System Boundaries
FUZE MUST reason about on-chain/off-chain behavior across the following responsibility layers:
1. **Contract Truth Layer** — chain-committed balances, claims, role references, pause state, and other explicitly encoded contract facts
2. **Platform Policy Layer** — cross-product rules, economic meaning, lifecycle policy, exclusion rules, spend rules, expiry rules, and other off-chain policy logic
3. **Product Execution Layer** — product objects, product workflows, product AI behavior, and workspace-private operational state
4. **Accounting and Treasury Layer** — revenue interpretation, cost treatment, distributable profit determination, reserve treatment, and funding-decision logic
5. **Reporting and Transparency Layer** — public-safe publication, registry artifacts, payout ledgers, transparency reports, and explanatory outputs
6. **Control and Governance Layer** — approvals, restrictions, treasury controls, timelock-sensitive decisions, emergency response, and recovery behavior

These layers interact heavily, but they do not share undifferentiated ownership.

## Adjacent Boundaries
This specification must be interpreted together with adjacent files as follows:
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md` governs top-level ownership, mutation-owner interpretation, and truth-family ownership
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md` governs ecosystem framing and top-level layer interpretation
- `PLATFORM_ARCHITECTURE_SPEC.md` governs plane separation, chain-adjacent posture, and runtime interaction model
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md` assigns canonical owners to the major domains represented here
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md` governs entity ownership, persistence discipline, and canonical-versus-derived status
- chain-specific layer specs refine the particular contract, credits, payout, reserve, registry, and transparency implications of this boundary
- identity/account/session and workspace/access-control foundation documents refine access-related constraints that still apply to off-chain systems participating in hybrid flows

This document defines the on-chain/off-chain responsibility model those files must preserve. It does not absorb all narrower detail.

## Conflict Resolution Rules
When materials, systems, or interpretations disagree, the following rules apply:
1. the active refined registry and higher constitutional materials win over narrower documents
2. `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md` wins on top-level ownership and mutation-owner interpretation
3. `PLATFORM_ARCHITECTURE_SPEC.md` wins on plane separation and shared-runtime posture
4. `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md` wins on entity-family ownership, persistence discipline, and canonical-versus-derived interpretation
5. this document wins on the division of responsibility between chain-committed truth and off-chain policy, accounting, product, reporting, and execution truth
6. explicit on-chain committed truth wins only for categories actually committed on-chain; broader off-chain meaning remains off-chain unless explicitly committed
7. raw provider input and raw chain visibility NEVER win by themselves; verified and normalized owner-controlled consequences win
8. governance or control-plane approval MAY authorize, constrain, or block action, but the resulting business state MUST still be written through the correct owner domain
9. when ambiguity remains, FUZE MUST choose the more conservative architecture-consistent interpretation and escalate the ambiguity into downstream refinement or decision recording

## Default Decision Rules
When ambiguity exists and no narrower approved exception is available, FUZE MUST default to the following:
1. token, credits, payout, reserve, registry, and governance-sensitive contract semantics default to on-chain ownership only where explicitly committed by contract design
2. policy, accounting, eligibility, workflow, reporting interpretation, and product behavior default to off-chain ownership unless explicitly committed on-chain
3. product domains default to consumers of chain-aware context, not owners of chain-sensitive shared semantics
4. provider callbacks, chain events, and wallet/client signals default to external-input or observed-state status until normalization succeeds
5. dashboards, public registries, transparency views, analytics outputs, and reports default to derived-state status
6. queues, workers, retry systems, and chain-submission trackers default to execution-state status
7. control-plane actions default to policy restriction, approval, or remediation status, not business-domain ownership status
8. if no explicit owner or truth class can be named, the design is incomplete and MUST NOT be treated as production-grade

## Roles / Actors / Entities
### Human Actors
- end users
- workspace members and owners
- product operators
- support and remediation operators
- finance and treasury reviewers
- governance and approval actors
- security reviewers
- public, community, investor, or partner readers of approved trust surfaces

### System Actors
- platform domain services
- product domain services
- provider-verification and normalization services
- workflow and worker systems
- chain-adjacent services
- reporting and registry publication systems
- control-plane systems
- on-chain contracts

### Minimum Conceptual Entity Families
- token participation entities and holder references
- credits commitments, credits mutations, and credits policy references
- payout-cycle, entitlement, funding, and claim-execution references
- reserve, vault, multisig, and timelock references
- payment verification and external-rail normalization entities
- accounting, treasury, and funding-decision entities
- snapshot and eligibility datasets
- chain submission, confirmation, and reconciliation entities
- audit and traceability entities
- reporting, registry, and transparency publication artifacts

## Ownership Model
This boundary requires each major concern to be analyzed across at least five dimensions:
1. **semantic ownership** — who defines what the concept means
2. **mutation ownership** — who accepts writes that change canonical state
3. **runtime execution ownership** — who performs the operational work
4. **presentation ownership** — who renders or explains state
5. **control/governance ownership** — who may approve, constrain, or remediate sensitive actions

These dimensions MUST remain distinct. A component may execute, present, approve, or monitor an action without owning the underlying business meaning.

## Authority / Decision Model
- Platform Architecture is the canonical constitutional owner of the on-chain/off-chain responsibility model.
- Chain contracts own only the categories of truth expressly committed by their contract semantics.
- Off-chain owner domains retain authority over policy, accounting, eligibility, workflow, reporting interpretation, and privacy-sensitive product meaning unless explicitly superseded by a narrower approved contract commitment.
- Treasury, governance, or control-plane actors MAY authorize or block sensitive action, but they MUST NOT silently become the owner of the resulting domain truth.
- Product domains MAY request or consume chain-aware capabilities but MUST NOT redefine platform-wide chain semantics.

## State Model
FUZE MUST distinguish at least the following state classes across the boundary:
- **Chain-Committed State** — canonical because it is explicitly committed on-chain
- **Off-Chain Canonical Business State** — platform- or product-owned canonical state that remains off-chain
- **Policy-Derived Canonical State** — governed off-chain output such as an eligibility dataset or entitlement input that becomes authoritative for a bounded process
- **Execution State** — job, submission, callback, reconciliation, or workflow progression state
- **External Raw State** — provider-, wallet-, or client-originated state not yet normalized by FUZE
- **Derived Reporting State** — report, registry artifact, dashboard output, or transparency view built from canonical sources

When disagreement occurs, the owning canonical state for the relevant category wins over execution, raw external input, or derived reporting state.

## Lifecycle / Workflow Model
Hybrid flows in FUZE SHOULD generally follow this lifecycle:
1. **Input Observation** — provider input, user action, wallet interaction, or platform intent is observed
2. **Verification / Normalization** — external or chain-adjacent input is verified and translated into FUZE-recognized consequences
3. **Owner-Side Decisioning** — the owning domain applies policy, validation, and authorization
4. **Preparation for Commitment** — if chain mutation is required, off-chain preparation produces the validated dataset, references, funding intent, or policy-bound operation
5. **Chain Submission / Off-Chain Mutation** — the correct mutation boundary accepts the write
6. **Confirmation / Reconciliation** — execution truth is reconciled with canonical truth across layers
7. **Publication / Reporting** — approved derived outputs and public trust surfaces are updated without replacing the canonical owners
8. **Correction / Supersession** — if failure or error occurs, explicit recovery and lineage-preserving correction paths are used

## Invariants
The following invariants are mandatory:
- on-chain state MUST NOT be described as the full source of business meaning unless that meaning is explicitly committed on-chain
- off-chain policy MUST bind on-chain outcomes where on-chain consequences depend on policy
- product domains MUST NOT own chain-sensitive shared semantics by default
- raw provider or chain signals MUST NOT mutate unrelated business truth without owner-controlled normalization
- reporting and transparency outputs MUST remain downstream to canonical owners
- execution systems MUST NOT silently become business truth owners
- governance-sensitive actions MUST be explicit, bounded, auditable, and reason-coded where they alter or constrain cross-layer behavior
- corrections MUST preserve lineage across affected layers

## Functional Rules
### Canonical On-Chain Responsibilities
At minimum, the following categories belong on-chain where formally committed by contract design:
1. **Canonical Token Participation State**
   - token identity
   - total supply state
   - holder-balance truth
   - transfer and ownership state
   - other explicitly committed token-layer state
2. **Platform Credits Committed State**
   - credits issuance commitment state
   - credits balance state by approved scope
   - spend, reservation, release, reversal, or adjustment representation where the credits design commits those transitions on-chain
   - committed internal commerce-layer state where formally encoded
3. **Stablecoin Payout Execution State**
   - payout-cycle funding state
   - claim-open / claim-closed / finalized state
   - claim execution state
   - over-claim prevention state
   - contract balance state for funded cycles
4. **Reserve and Vault Contract State**
   - allocation contract state
   - vesting state
   - category-bounded reserve state
   - contract-specific permission and separation state
5. **Governance-Critical Contract Controls**
   - multisig ownership or control references
   - timelock-based role protections
   - pause or constrained administrative controls
   - contract-registry relationships and similarly committed governance controls

### Canonical Off-Chain Responsibilities
At minimum, the following categories belong off-chain:
1. **Payment Verification and Normalization**
   - Stripe and equivalent provider verification
   - stablecoin deposit normalization where applicable
   - Telegram Stars verification
   - Apple and Google billing verification
   - fraud and chargeback handling
   - external payment-rail interpretation
2. **Accounting and Treasury Interpretation**
   - revenue recognition logic
   - cost and expense treatment
   - refund and reversal treatment
   - distributable profit determination
   - treasury reconciliation
   - reserve accounting interpretation
   - policy-driven financial classification
3. **Snapshot and Eligibility Preparation**
   - snapshot reference selection
   - raw holder extraction
   - address classification
   - exclusion application
   - entitlement dataset construction
   - proof/root preparation or equivalent dataset preparation where applicable
4. **Product Domain Logic**
   - product objects and product-private workflows
   - workspace-scoped product state
   - product AI behavior and operational data
5. **AI Orchestration and Execution**
   - model routing
   - context assembly
   - prompt and instruction layering
   - provider calls
   - tool orchestration
   - output validation
   - fallback, retry, and metering behavior
6. **Workflow and Automation Coordination**
   - queue-backed orchestration
   - retries and backoff
   - scheduled tasks
   - approval gates
   - job routing
   - multi-step execution state
   - operator review and support flows
7. **Reporting and Transparency Publication**
   - transparency reports
   - payout ledgers
   - contract and wallet registry publication
   - investor/community reporting
   - analytics and observability outputs
   - structured explanation of chain state and policy outcomes
8. **Privacy-Sensitive and Workspace-Sensitive State**
   - business documents
   - generated app content
   - operator notes
   - workflow history
   - workspace-private settings
   - permissioned product artifacts
   - non-public account and workspace state

### Economic-Layer Rules
FUZE MUST preserve the following per-layer split:
- **Token Participation Layer** — Ethereum holds token truth; off-chain systems own analytics, rank derivation, snapshot extraction, eligibility interpretation, and public explanation
- **Platform Credits Layer** — Base may hold committed credits state; off-chain systems own payment normalization, credits policy, billing logic, spend policy, refund interpretation, usage attribution, and commercial reporting
- **Profit Participation Layer** — Base may hold payout-cycle funding and claim execution state; off-chain systems own distributable profit determination, eligibility dataset construction, exclusion logic, cycle reporting, audit support, and funding readiness
- **Reserve and Treasury Layer** — contracts may hold vault balances and control state; off-chain systems own treasury decisions, reserve accounting interpretation, action approvals, policy-defined action classification, and contextual reporting

### Product-Layer Rules
- Products MUST NOT independently define token truth, credits semantics, payout logic, or reserve logic.
- Products MAY consume wallet-aware participation context, holder-rank or privilege signals, credits-balance state, entitlement state, and payout-status visibility where relevant.
- Product objects, AI actions, workspace data, private outputs, and product-local reports remain primarily off-chain unless a strong platform-approved reason exists to place a bounded element on-chain.

## Permission / Access Considerations
- Access to off-chain systems participating in hybrid workflows MUST remain governed by canonical account, session, workspace, authorization, and access-control rules.
- Wallet awareness MAY provide context but MUST NOT replace account-rooted identity, authorization, or operator control requirements.
- Sensitive chain-adjacent operations MUST require explicit authorization and, where applicable, governance-aware control-plane enforcement.
- Public chain visibility MUST NOT be interpreted as permission to expose off-chain private or workspace-sensitive information.

## Entitlement Considerations
- Entitlements influenced by holder state or payout eligibility MUST be treated as policy-bound off-chain canonical outputs unless the entitlement itself is explicitly committed on-chain.
- Eligibility datasets, exclusion lists, and cycle-scoped participation inputs MUST preserve policy references, snapshot references, and traceable lineage.
- Product capability gating MAY consume chain-aware signals but MUST remain consistent with the platform entitlement model and MUST NOT invent contradictory semantics.

## API / Contract Implications
This boundary model imposes the following downstream API rules:
- chain-adjacent APIs MUST distinguish preparation, submission, confirmation, reconciliation, and reporting concerns
- APIs MUST NOT imply that a non-owning layer is the canonical owner of meaning
- public-read status APIs MUST distinguish contract truth from off-chain business interpretation
- internal mutation APIs MUST route through owner-controlled preparation and validation before chain submission
- provider callbacks and chain event ingestion MUST terminate in normalization boundaries, not directly in unrelated business domains
- contract interaction APIs MUST preserve references to policy versions, cycle IDs, funding references, dataset references, or equivalent lineage markers where relevant
- an API surface that cannot answer “what is canonical here, and what is only execution or reporting state?” is incomplete

## Event / Async Implications
This boundary model imposes the following event and async rules:
- chain events are coordination and visibility signals, not substitutes for business-policy ownership
- retries MUST NOT create duplicate business outcomes
- execution records remain execution truth unless the owning business domain explicitly adopts the result
- snapshot publication, funding readiness, and claim-cycle progression MUST preserve lineage across off-chain and on-chain boundaries
- reporting pipelines MAY lag, but they MUST NOT overwrite canonical chain or off-chain business truth
- event families SHOULD carry trace identifiers, policy references, and entity references sufficient for audit and reconciliation

## Data Model / Storage Implications
This boundary model imposes the following data-discipline rules:
- chain-derived records and off-chain policy records MUST remain distinguishable
- off-chain canonical business entities MUST remain distinguishable from chain-committed entities
- eligibility datasets and payout inputs MUST preserve provenance to snapshot references and policy references
- execution entities such as chain submission records or callback processing records MUST remain distinct from business-domain entities
- reporting and publication stores MUST preserve traceability back to chain and off-chain canonical sources
- caches, dashboards, and convenience stores MUST NOT become hidden truth owners

## Read Model / Projection / Reporting Rules
- Transparency views, dashboards, public registries, investor/community reports, and analytics outputs are ordinarily derived read models.
- Derived outputs MAY summarize canonical sources, but they MUST NOT redefine them.
- Public read models MUST clearly distinguish contract-visible facts from off-chain accounting, policy, eligibility, or reporting interpretation.
- Reporting publication latency MAY exist, but reporting systems MUST preserve source references and correction lineage.
- If a narrow downstream specification elevates a reporting artifact for a bounded truth family, that elevation MUST be explicit and MUST NOT silently broaden into general ownership.

## Security / Risk / Abuse Controls
This boundary is also a security boundary.

If the division is weak:
- products may overreach into chain-sensitive roles
- providers may mutate economic outcomes without proper normalization
- dashboards or public summaries may redefine business meaning
- off-chain systems may overstate what chain state proves
- chain events may be misread as full accounting truth
- governance-sensitive actions may occur through ordinary runtime flows
- recovery actions may destroy lineage instead of preserving it

All downstream security, abuse-prevention, monitoring, treasury, and reporting specifications MUST preserve this boundary.

## Boundary Violation Detection / Non-Canonical Patterns
The following are non-canonical and forbidden unless a narrower approved exception explicitly authorizes them:
- treating chain visibility alone as complete business truth
- letting product domains define credits, payout, reserve, or token semantics by local convention
- allowing provider callbacks or chain events to bypass normalization and directly mutate unrelated canonical business domains
- letting dashboards, registries, transparency pages, or AI explanations become systems of record
- silently rewriting off-chain records to hide on-chain mistakes
- using governance or admin tooling to bypass owner-domain mutation boundaries without explicit bounded override semantics and audit lineage
- publishing public trust surfaces that conflate contract state with off-chain accounting or policy meaning

## Audit / Traceability Requirements
The following are mandatory wherever the boundary materially affects trust, finance, eligibility, governance, or public reporting:
- stable entity references and trace identifiers
- policy version references where policy binds outcomes
- snapshot, funding, cycle, or dataset references where applicable
- lineage between off-chain preparation and on-chain submission
- lineage between chain-confirmed execution and downstream reporting
- reason codes for sensitive operator or control-plane actions
- preserved correction and supersession records for non-routine recovery

## Failure Handling / Edge Cases
Common failure types include:
- payment verified off-chain but credits commitment delayed on-chain
- credits mutation committed on-chain but product workflow not finalized off-chain
- snapshot prepared incorrectly before payout entitlement publication
- payout contract funded before reporting metadata is fully synchronized
- treasury approval granted but chain execution delayed or failed
- support correction needed after chain-visible action already occurred
- AI-heavy product usage settled incorrectly relative to commercial policy

The following recovery rules are mandatory:
- failure lineage MUST remain visible
- on-chain mistakes MUST NOT be hidden by silent off-chain edits
- off-chain corrections MUST preserve linkage to affected on-chain records
- economic corrections MUST remain auditable
- incident response MUST distinguish among chain-state correction, off-chain policy correction, reporting correction, and operational retry or replay
- pending confirmation MUST be distinguished from contradiction
- when ambiguity exists during recovery, FUZE MUST favor the more conservative interpretation until reconciliation completes

## Operational Considerations
- Hybrid flows SHOULD expose explicit status distinctions for prepared, submitted, confirmed, reconciled, published, corrected, and superseded states where relevant.
- Observability MUST preserve cross-layer traceability.
- Runbooks for chain-adjacent or financial incidents MUST respect the ownership split defined here.
- Operators MAY coordinate recovery across layers, but they MUST NOT collapse the semantic distinction between those layers.
- Degraded modes MUST make uncertainty more explicit, not less.

## Migration / Compatibility / Supersession Considerations
- migrations MUST NOT silently move responsibility from off-chain policy systems into contracts or vice versa without explicit specification change
- compatibility layers MAY temporarily support older flows, but they MUST preserve canonical ownership and traceability
- if a downstream contract or integration evolves, the owning on-chain/off-chain role MUST remain explicit
- if older materials imply that chain state alone explains the full platform meaning, this refined specification supersedes that interpretation within its scope

## Implementation-Contract Guardrails
Downstream implementation contracts MUST preserve all of the following:
- explicit distinction between chain-committed truth and off-chain business truth
- explicit policy references where policy binds on-chain outcomes
- explicit normalization boundaries for provider and chain-originating input
- explicit idempotency posture for submissions, retries, callbacks, and replay handling
- explicit reconciliation lineage across payment, credits, holder, eligibility, payout, reserve, and reporting flows
- explicit control-plane treatment for sensitive overrides or approvals
- explicit source references in public or internal trust surfaces
- explicit tie-break rules when provider input, chain state, cache state, report state, and owner-domain state disagree

Downstream implementations MUST NOT optimize away ownership clarity for convenience.

## Downstream Execution Staging
This document should be consumed by downstream authors in the following order:
1. chain architecture and chain-layer specifications
2. credits, payout, reserve, treasury, snapshot, and transparency specifications
3. API, event, and workflow specifications
4. audit, security, monitoring, and operations specifications
5. product integration specifications that consume chain-aware context

## Required Downstream Specs / Contract Layers
This specification materially informs:
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
- product integration specifications

## Canonical Examples / Anti-Examples
### Canonical Examples
- Ethereum holder balances are canonical token participation truth; holder ranking and payout eligibility interpretation remain off-chain.
- A Base payout cycle funding transaction may be canonical execution funding truth; distributable profit determination and exclusion policy remain off-chain.
- A public registry page may accurately publish contract addresses and state summaries; it does not become the owner of reserve policy or payout-cycle meaning.
- A verified payment event may normalize into an internal credits outcome; the raw provider callback itself is not FUZE commercial truth until normalization succeeds.

### Anti-Examples
- “The chain proves profit exists.”
- “The dashboard balance is the canonical credits owner.”
- “The product can define its own payout semantics because it consumes payouts.”
- “The report corrected the meaning of the contract state.”
- “The callback already happened, so normalization can be skipped.”

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
- identity/account/auth/session foundation documents
- workspace/access-control foundation documents

This specification directly governs or materially informs:
- chain-specific layer specifications
- credits, payout, reserve, treasury, snapshot, transparency, and registry specifications
- chain-adjacent API and event specifications
- reporting, audit, security, monitoring, and migration specifications
- product integrations that consume chain-aware context

## Explicitly Deferred Items
The following are intentionally deferred to downstream specifications:
- exact Base credits contract design
- exact payout entitlement encoding model
- exact snapshot proof format
- exact reporting publication latency requirements
- exact provider-by-provider normalization mechanics
- exact treasury funding procedure details
- exact public registry schema and visibility rules
- exact incident escalation procedures by domain

These do not weaken the canonical on-chain/off-chain responsibility model established here.

## Final Normative Summary
FUZE uses a deliberate hybrid architecture. On-chain systems hold canonical committed state only where public verifiability, contract enforceability, and structural visibility create real trust value. Off-chain systems own policy interpretation, product execution, payment verification, accounting meaning, eligibility construction, workflow coordination, privacy-sensitive state, and transparency reporting where flexibility, privacy, and operational depth are required.

Ethereum remains the canonical participation layer for token truth. Base hosts the operational credits and payout-execution layers where those capabilities are formally committed. Products remain primarily off-chain domains. Governance spans both enforceable contract controls and off-chain policy and approval systems. Reporting and transparency also span both layers.

On-chain state is not the same as full business meaning. Off-chain policy is not an excuse for hidden discretion. The correct FUZE model is explicit role separation plus explicit linkage through policy references, normalized inputs, auditable coordination, and reconciliation lineage. Any downstream design that weakens this boundary is inconsistent with the FUZE system-spec library.

## Quality Gate Checklist
- [x] Canonical owner explicit for every material truth family
- [x] Mutation boundaries explicit
- [x] Adjacent boundaries explicit
- [x] Truth classes explicit
- [x] Conflict-resolution rules explicit where needed
- [x] Default decision rules explicit where ambiguity could arise
- [x] Non-canonical patterns called out clearly
- [x] Operator/admin/control paths bounded and audited
- [x] Read-model, cache, reporting, and projection rules explicit
- [x] On-chain vs off-chain responsibilities explicit
- [x] Failure and degraded-mode behaviors explicit
- [x] Downstream implementation guardrails explicit
- [x] Dependencies and downstream impacts explicit
- [x] Non-goals and deferred items explicit
- [x] Strong enough to support backend, API, data, runtime, finance, reporting, and audit implementation without contradictory reinterpretation
