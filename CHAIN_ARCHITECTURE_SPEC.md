# FUZE Chain Architecture Specification

## Document Metadata

- **Document Name:** `CHAIN_ARCHITECTURE_SPEC.md`
- **Document Type:** Canonical refined system specification
- **Status:** Active refined system spec
- **Version:** 2.0.0
- **Effective Date:** 2026-04-23
- **Last Updated:** 2026-04-23
- **Reviewed On:** 2026-04-23
- **Document Owner:** FUZE Chain Architecture Domain (canonical owner for multi-chain role assignment, chain-layer responsibility boundaries, cross-layer coordination posture, and implementation-contract guardrails); named individual owner is not explicitly specified in the retrieved governing materials
- **Approval Authority:** Not explicitly specified in the retrieved governing materials; constitutional approval authority remains governed by `REFINED_SYSTEM_SPEC_INDEX.md` and the active FUZE approval workflow
- **Review Cadence:** SHOULD be reviewed quarterly and whenever token-layer posture, Base operational-layer posture, payout architecture, registry/transparency posture, treasury-control posture, governance-control posture, chain-adapter posture, or implementation-contract posture materially changes
- **Governing Layer:** Chain topology / multi-layer chain role architecture / cross-chain responsibility map
- **Parent Registry:** `REFINED_SYSTEM_SPEC_INDEX.md`
- **Primary Audience:** Platform architecture, backend engineering, contracts engineering, treasury/governance operators, finance systems, reporting and transparency authors, security engineering, runtime operations, API authors, implementation-contract authors
- **Primary Purpose:** Define the canonical FUZE chain architecture, including why FUZE uses Ethereum and Base, what each chain canonically owns, how those layers connect through off-chain policy and orchestration, and which downstream contracts, APIs, events, ledgers, reports, and runtime systems MUST preserve the resulting boundary model
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
  - `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
  - `FUZE_CHAIN_ARCHITECTURE.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
  - `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
- **Primary Downstream Dependents:**
  - `ETHEREUM_TOKEN_LAYER_SPEC.md`
  - `BASE_PLATFORM_CREDITS_LAYER_SPEC.md`
  - `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md`
  - `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
  - `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
  - `PAYOUT_LEDGER_SPEC.md`
  - `PLATFORM_CREDITS_SPEC.md`
  - `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
  - `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
  - `TRANSPARENCY_MODEL_SPEC.md`
  - `TRANSPARENCY_REPORTING_SPEC.md`
  - `TREASURY_CONTROL_POLICY_SPEC.md`
  - `VAULT_ACTION_POLICY_SPEC.md`
  - `MULTISIG_AND_TIMELOCK_SPEC.md`
  - `API_ARCHITECTURE_SPEC.md`
  - `PUBLIC_API_SPEC.md`
  - `INTERNAL_SERVICE_API_SPEC.md`
  - `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
  - chain-adjacent adapters, reconciliation workers, public-trust surfaces, and runtime-control runbooks
- **Supersedes:** Earlier or weaker FUZE chain-architecture descriptions that treated “being on-chain” as one undifferentiated role, collapsed token participation, platform credits, and payout execution into one asset model, or allowed Base and Ethereum state to absorb off-chain accounting, policy, reporting, or product meaning by implication
- **Superseded By:** None currently defined
- **Related Decision Records:** Not explicitly specified in the retrieved governing materials
- **Canonical Status Note:** This document is the canonical governing FUZE specification for chain-topology and chain-role semantics. Downstream contracts, chain adapters, APIs, events, ledgers, publication surfaces, reporting systems, and control-plane workflows MUST preserve the ownership, chain placement, cross-layer linkage, and conflict-resolution rules defined here.
- **Implementation Status:** Normative source; downstream chain-layer contracts, adapters, APIs, events, reconciliation flows, reporting outputs, and public-trust surfaces MUST align
- **Approval Status:** Draft refined canonical specification pending explicit approval workflow
- **Change Summary:** Refined the FUZE chain architecture into a production-grade governing system spec; normalized multi-chain role assignment; hardened separation among Ethereum token truth, Base operational credits, and Base payout execution; clarified cross-layer linkage through off-chain policy and lineage; added truth classes, ownership boundaries, conflict rules, default decisions, public-trust constraints, failure handling, and implementation-contract guardrails

## Title

FUZE Chain Architecture Specification

## Purpose

This specification defines the canonical chain architecture of the FUZE ecosystem.

Its purpose is to make explicit:

- why FUZE uses more than one chain layer
- which chain is canonically assigned to each economic and trust-bearing function
- how Ethereum, Base, and off-chain FUZE systems interact without collapsing ownership
- how token truth, platform-commerce truth, payout-execution truth, treasury/accounting meaning, reporting truth, and public registry truth remain distinct
- how cross-chain linkage, chain-adjacent coordination, and public explanation MUST work
- what downstream implementations MUST preserve when building contracts, adapters, APIs, events, workers, reporting outputs, or public trust surfaces

This document is architectural and governing in posture. It is not a token explainer, product brief, or chain-marketing note.

## Scope

This specification governs:

- the canonical FUZE multi-chain topology
- chain-role assignment across Ethereum and Base
- the architectural reasons those roles are separated
- the relationship between chain-native truth and off-chain policy, accounting, eligibility, workflow, and reporting truth
- cross-layer connection rules among token participation, Platform Credits, payout execution, treasury funding, snapshot/eligibility derivation, registry publication, and transparency reporting
- chain-sensitive public explanation and non-canonical interpretation controls
- implementation-contract guardrails for contracts, chain adapters, APIs, events, reconciliation, migration, and runtime operations

## Out of Scope

This specification does not define:

- exact smart-contract ABI, bytecode, or storage layout
- exact bridge mechanics or whether any future bridge exists
- exact proof format, Merkle format, or entitlement encoding format
- exact RPC, indexer, relayer, or node-provider topology
- exact gas-optimization strategy
- exact payout math, profit formulas, or reserve-accounting policy
- exact public-site composition or registry rendering
- exact release sequencing for future chain expansion

Those belong in adjacent and downstream specifications.

## Design Goals

The design goals of the FUZE chain architecture are to:

1. preserve Ethereum as the strongest canonical token participation layer
2. preserve Base as the operational low-cost layer for chain-native platform activity where frequent execution is required
3. keep Platform Credits, token participation, and payout execution distinct in meaning even when operationally connected
4. ensure off-chain policy, accounting, treasury, eligibility, and reporting remain explicit rather than hidden inside chain visibility
5. reduce conceptual drift in public explanation, internal implementation, and product behavior
6. support auditability, reconciliation, and public intelligibility across chain and off-chain layers
7. support future-safe implementation-contract work without allowing accidental semantic collapse
8. make chain placement a deliberate architecture rule rather than a convenience choice per team

## Non-Goals

This specification is not intended to:

- force all FUZE logic or business meaning on-chain
- imply that one chain should own every FUZE economic concept
- treat Platform Credits as a freely tradable public market token
- treat stablecoin payout execution as identical to token ownership
- let Base operational state redefine Ethereum holder truth
- let Ethereum token truth redefine off-chain accounting, payout policy, or treasury controls
- let public reporting or registry outputs become owners of chain semantics
- allow products to select alternate chain semantics for convenience

## Core Principles

### 1. Layered Chain Architecture Principle
FUZE uses a layered chain model, not a one-chain-for-everything model. Each chain role exists because the relevant truth family has different trust, cost, throughput, visibility, and operational requirements.

### 2. Canonical Token Layer Principle
Ethereum is the canonical FUZE token and holder-participation layer. Token identity and holder-balance truth belong there unless and until a formal architectural change supersedes that rule.

### 3. Operational Commerce Layer Principle
Base is the operational chain layer for Platform Credits and related high-frequency chain-adjacent platform commerce functions where low fees and practical execution cadence matter.

### 4. Operational Payout Layer Principle
Base is also the operational payout-execution layer for stablecoin profit-participation cycles, while eligibility remains derived from Ethereum holder truth and off-chain policy.

### 5. Economic Separation Principle
FUZE token, Platform Credits, and stablecoin payout participation are connected but not identical assets, rights, or truth families. They MUST NOT be merged conceptually or operationally.

### 6. Off-Chain Interpretation Principle
Policy, accounting, treasury meaning, eligibility derivation, workflow coordination, and public explanation remain off-chain unless explicitly committed on-chain by an approved narrower design.

### 7. Public Intelligibility Principle
The chain architecture MUST remain understandable to engineers, auditors, users, partners, and public readers through explicit chain-role explanations and derived trust surfaces that preserve source ownership.

### 8. No Hidden Alternate Architecture Principle
Product teams, support flows, admin tools, dashboards, or adapters MUST NOT introduce local alternate interpretations of chain roles.

## Canonical Definitions

### Chain Architecture
The explicit FUZE assignment of bounded economic and trust-bearing functions to specific chains and off-chain coordination domains.

### Token Participation Layer
The chain layer that canonically owns token identity, supply, holder balances, and related contract-native token truth.

### Operational Credits Layer
The chain layer that canonically represents operational Platform Credits state after off-chain normalization and owner-approved mutation.

### Operational Payout Layer
The chain layer that canonically represents funded payout execution and claim state for approved payout cycles.

### Chain-Adjacent Coordination
Off-chain orchestration, normalization, preparation, submission, reconciliation, and reporting behavior that interacts with chains without replacing chain-owned or off-chain-owned truth.

### Eligibility Source Layer
The source layer from which holder truth is derived for downstream cycle-specific eligibility preparation.

### Public Trust Surface
A registry, report, transparency artifact, public API, or stakeholder-facing explanation derived from canonical source domains.

## Truth Class Taxonomy

This document requires explicit separation among at least the following truth classes:

1. **Semantic truth** — what the chain-layer concept means
2. **Contract-native truth** — what is explicitly committed on a given chain
3. **Policy truth** — what off-chain rules govern interpretation, exclusion, issuance, funding, or approval
4. **Accounting / treasury truth** — what revenue, cost, reserve, and distributable-profit meanings apply
5. **Eligibility truth** — the authoritative cycle-specific off-chain dataset derived from holder truth and policy
6. **Execution truth** — preparation, submission, confirmation, retry, and reconciliation state
7. **Registry / reporting truth** — approved derived public-facing or stakeholder-facing outputs
8. **Presentation truth** — explanatory wording and UI composition

These truth classes MUST NOT be collapsed.

## Architectural Position in the Spec Hierarchy

This document sits below:

- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`

and above:

- `ETHEREUM_TOKEN_LAYER_SPEC.md`
- `BASE_PLATFORM_CREDITS_LAYER_SPEC.md`
- `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md`
- `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `PAYOUT_LEDGER_SPEC.md`
- chain-layer APIs, contract specifications, chain adapters, reconciliation workers, and public-trust publication layers

This document owns chain-role assignment and cross-layer chain topology semantics. It does not replace the narrower semantics owned by token, credits, payout, treasury, registry, or reporting specifications.

## System Boundaries

This document governs only the following boundaries:

- which chain hosts canonical FUZE token participation truth
- which chain hosts canonical Base operational credits truth
- which chain hosts canonical Base payout execution truth
- how off-chain domains connect those layers without absorbing or confusing their ownership
- how public registry and transparency layers refer to chain state without becoming owners of its meaning
- how chain architecture constrains downstream API, event, storage, and runtime implementation

It does not govern:

- the full semantic model of Platform Credits
- the full semantic model of profit participation
- the full semantic model of payout-ledger records
- the full public registry model
- the full transparency-reporting model
- detailed treasury policy
- detailed governance policy

## Adjacent Boundaries

This specification must be interpreted with the following adjacent boundaries:

- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md` governs the broader division between chain-native truth and off-chain policy, accounting, workflow, and reporting truth.
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md` governs FUZE’s top-level ecosystem decomposition and boundary model.
- `PLATFORM_CREDITS_SPEC.md` governs what Platform Credits mean; this document governs why their operational chain placement is on Base.
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md` governs append-oriented credits ledger truth and commitment linkage; this document does not replace that ledger model.
- `BASE_PLATFORM_CREDITS_LAYER_SPEC.md` governs Base-side credits operational truth in depth.
- `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md` governs how Ethereum token-holder truth becomes cycle-specific eligibility datasets.
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md` governs payout-policy semantics; this document governs chain placement and chain-role consequences.
- `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md` governs Base payout execution in depth.
- `PAYOUT_LEDGER_SPEC.md` governs payout-cycle ledger truth and historical trust record posture.
- `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md` governs public designation of official addresses and contracts.
- `TRANSPARENCY_MODEL_SPEC.md` and `TRANSPARENCY_REPORTING_SPEC.md` govern public-trust interpretation and publication posture.

## Canonical Chain Topology

### Ethereum Mainnet
Ethereum mainnet is the canonical FUZE token participation layer.

It owns, where formally committed by the token design:

- FUZE token identity
- token supply and transfer state
- holder balance truth
- token-holder address visibility
- long-horizon participation anchor state

Ethereum does **not** own by default:

- Platform Credits meaning
- credits ledger truth
- payout entitlement interpretation
- distributable profit determination
- payout funding readiness
- public transparency interpretation
- product-local spending state

### Base
Base is the canonical FUZE operational chain layer.

It owns, through narrower approved downstream specifications:

- Base operational Platform Credits representation
- Base payout execution representation
- low-cost chain-native operational state required for credits and payout claims
- chain-side records and confirmations tied to those operational domains

Base does **not** own by default:

- FUZE token participation truth
- accounting truth
- treasury policy meaning
- payout-cycle policy meaning
- eligibility semantics by itself
- registry publication truth
- transparency explanation truth

### Off-Chain FUZE Domains
Off-chain FUZE systems remain canonical for:

- payment verification and normalization
- credits semantics and append-oriented ledger truth
- accounting and distributable-profit determination
- treasury approval and funding-decision logic
- snapshot policy, eligibility derivation, and exclusion treatment
- reconciliation and discrepancy handling
- public registry publication state
- transparency and stakeholder reporting
- runtime orchestration and control-plane actions

## Conflict Resolution Rules

When materials, systems, or interpretations disagree, the following rules apply:

1. the active refined registry and higher constitutional materials win over narrower documents
2. `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md` wins on top-level ecosystem boundary interpretation
3. `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md` wins on the broader chain/off-chain ownership split
4. this document wins on canonical chain-role assignment and cross-layer topology interpretation
5. `ETHEREUM_TOKEN_LAYER_SPEC.md` wins on Ethereum token-layer semantics once refined
6. `BASE_PLATFORM_CREDITS_LAYER_SPEC.md` wins on Base operational credits semantics
7. `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md` wins on Base payout execution semantics
8. `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md` wins on eligibility derivation from Ethereum holder truth
9. if chain-native state, off-chain policy, and public reporting disagree, chain-native state remains canonical only for the chain-native facts explicitly committed there; semantic, policy, accounting, eligibility, and reporting meaning remains with the proper off-chain owner
10. if a local product, dashboard, adapter, or support tool attempts to reinterpret chain placement for convenience, that interpretation is non-canonical and MUST be rejected

## Default Decision Rules

1. If a concept involves FUZE token identity or holder-balance truth, Ethereum is the default owner unless a formal superseding chain-architecture decision exists.
2. If a concept involves operational Platform Credits representation, Base is the default chain layer, while semantic credits meaning and ledger truth remain governed upstream.
3. If a concept involves stablecoin payout execution or claim state, Base is the default chain layer, while eligibility and accounting meaning remain upstream.
4. If a concept involves policy, accounting, treasury, reporting, or public explanation, it defaults off-chain unless expressly committed on-chain.
5. If public materials fail to distinguish among token truth, credits truth, and payout truth, the more conservative separated interpretation MUST be used.
6. If a proposed implementation cannot name which chain owns which truth and which off-chain domain interprets it, the implementation is incomplete and MUST NOT be treated as production-grade.

## Roles / Actors / Entities

### Human Actors
- end users and token holders
- workspace operators and product teams
- treasury and finance reviewers
- governance and approval actors
- security and runtime operators
- public and stakeholder readers of approved trust surfaces

### System Actors
- Ethereum token contracts
- Base credits contracts or equivalent operational credits commitments
- Base payout execution contracts
- snapshot and eligibility pipeline services
- credits and payout ledgers
- chain adapters, indexers, and reconciliation services
- registry and transparency publication services
- treasury and governance control-plane systems

### Core Entity Families
- token contract and holder references
- Base credits operational records and scope references
- payout-cycle execution references
- eligibility dataset references
- treasury funding references
- registry publication references
- transparency/report references
- cross-layer trace identifiers and policy version references

## Ownership Model

The chain architecture requires explicit separation across:

1. **chain-role ownership** — which chain canonically hosts a truth family
2. **semantic ownership** — which domain defines what that family means
3. **mutation ownership** — which owner accepts authoritative writes
4. **execution ownership** — which adapters, workers, or contracts perform the action
5. **reporting ownership** — which derived surface explains the result

These dimensions MUST remain distinct.

## Authority / Decision Model

- FUZE Platform Architecture is the canonical owner of the overall chain topology and role assignment.
- Ethereum token contracts own token-layer contract-native truth.
- Base layer contracts own their narrower committed operational truth.
- Off-chain commercial, payout, treasury, eligibility, and reporting domains retain semantic and policy authority where chain contracts do not explicitly own that meaning.
- Governance-sensitive controls MAY approve, constrain, or halt chain-sensitive action, but they MUST NOT silently become the owner of token, credits, payout, or reporting truth.

## State Model

The chain architecture distinguishes at least the following state families:

- **Ethereum token state** — holder balances, transfers, supply, and related token-native contract truth
- **Base credits operational state** — operational credits commitments and balance posture where approved
- **Base payout execution state** — funded-cycle execution and claim posture
- **Off-chain semantic state** — credits semantics, payout semantics, product semantics
- **Off-chain accounting/treasury state** — revenue, cost, reserve, and funding readiness meaning
- **Off-chain eligibility state** — cycle-specific derived entitlement inputs
- **Execution / adapter state** — submissions, confirmations, retries, sync, reconciliation, and discrepancy cases
- **Derived registry/reporting state** — public-safe contract and wallet registry records, transparency artifacts, dashboards, exports, and stakeholder reports

## Lifecycle / Workflow Model

### Payment to Credits
1. external payment or value input is observed
2. payment is verified and normalized off-chain
3. semantic and ledger-authorized credits mutation is accepted off-chain
4. Base operational credits state is created or updated
5. derived product, support, and reporting views refresh

### Token to Eligibility
1. Ethereum token-holder truth is observed at the approved snapshot reference
2. raw holder data is extracted
3. policy-bound classification and exclusion rules are applied off-chain
4. canonical cycle-specific eligibility outputs are finalized off-chain
5. downstream payout systems consume those outputs

### Profit Participation Funding and Claiming
1. treasury/accounting finalizes distributable-profit and funding readiness off-chain
2. approved eligibility inputs are bound to the cycle
3. payout-cycle funding is committed on Base
4. Base payout execution is opened or processed
5. claims or disbursements occur on Base
6. payout ledger, transparency, and registry/reporting layers are refreshed downstream as derived outputs

## Invariants

The following invariants are mandatory:

- Ethereum remains the canonical FUZE token participation layer unless formally superseded.
- Base remains the canonical operational chain layer for Platform Credits and payout execution unless formally superseded.
- Token truth, credits truth, and payout truth MUST remain conceptually separate.
- Off-chain accounting meaning MUST NOT be inferred directly from chain visibility alone.
- Eligibility meaning MUST NOT be inferred directly from Base payout execution or raw Ethereum balance alone.
- Public registry and transparency layers MUST remain derived and MUST NOT redefine chain ownership.
- Products MUST NOT create local alternate chain semantics.
- Cross-layer linkage MUST preserve traceability, policy references, and reconciliation capability.

## Functional Rules

### Rule 1: Ethereum Role
Ethereum MUST be treated as the canonical token layer for FUZE token identity and holder-balance truth.

### Rule 2: Base Credits Role
Base MUST be treated as the canonical operational credits chain layer for FUZE Platform Credits where chain-native operational representation is used.

### Rule 3: Base Payout Role
Base MUST be treated as the canonical payout-execution chain layer for approved stablecoin payout cycles.

### Rule 4: No Asset Collapse
FUZE token, Platform Credits, and stablecoin payout rights MUST NOT be represented as though they are the same asset, the same ledger, or the same economic meaning.

### Rule 5: Off-Chain Funding and Accounting
Treasury funding decisions, revenue/cost interpretation, profit determination, and reserve meaning MUST remain off-chain unless a narrower approved specification explicitly commits a bounded subset on-chain.

### Rule 6: Eligibility Separation
Ethereum holder balances MAY seed eligibility preparation, but eligibility remains a policy-bound off-chain output and MUST NOT be collapsed into raw token balance or Base payout state.

### Rule 7: Public Explanation Discipline
Public explanations of chain architecture MUST preserve the distinction between token truth on Ethereum and operational credits/payout execution on Base.

### Rule 8: Chain Expansion Discipline
Any future chain addition or role reassignment MUST be treated as a constitutional architecture change, not a product-local optimization.

## Permission / Access Considerations

- Wallet awareness MAY be relevant to chain participation, but account-rooted identity and scoped authorization remain off-chain platform concerns.
- Sensitive chain-adjacent operations such as funding, correction, pause, contract publication, or remediation MUST require stronger authorization, reason coding, and audit capture.
- Public chain visibility MUST NOT be treated as permission to expose non-public off-chain state.

## Entitlement Considerations

- Token-holder status may influence eligibility-aware features, but entitlement remains a separate policy-bound layer.
- Product capability gating MAY consume chain-aware signals, but MUST NOT redefine token, credits, or payout semantics.
- Cycle-specific payout eligibility remains governed by the snapshot and eligibility pipeline, not by raw holder balance or product-local interpretations.

## API / Contract Implications

The chain architecture imposes the following downstream requirements:

- APIs MUST identify which chain and which owner domain a given field or state belongs to.
- Contract APIs MUST avoid implying broader business meaning than the contract actually owns.
- Public APIs MUST distinguish among Ethereum token state, Base credits state, Base payout state, and off-chain derived interpretation.
- Internal APIs MUST preserve references linking chain-native operations to off-chain policy, ledger, snapshot, funding, and reporting lineage.
- Adapter APIs MUST distinguish prepare, submit, confirm, reconcile, and publish phases.
- Idempotency and replay protection MUST be explicit for chain-adjacent mutations and event processing.

## Event / Async Implications

- Ethereum and Base events are execution and observation signals; they do not by themselves resolve semantic, accounting, or reporting ownership.
- Async workers MUST preserve chain identity, cycle identity, scope identity, and policy lineage when propagating state.
- Event consumers MUST NOT create duplicate business outcomes from retries or reorg/replay handling.
- Chain-related events SHOULD carry correlation IDs, contract references, chain identifiers, and canonical domain references sufficient for audit and reconciliation.

## Data Model / Storage Implications

- Ethereum-derived records, Base-derived records, and off-chain canonical records MUST remain distinguishable.
- Chain identifiers, contract identifiers, scope identifiers, cycle identifiers, and policy/version references MUST be preserved in durable lineage where relevant.
- Reporting, analytics, and public-trust stores MUST remain downstream to source domains.
- Products MUST NOT maintain private hidden systems of record for chain-sensitive shared semantics.

## Read Model / Projection / Reporting Rules

- Public registry outputs, transparency artifacts, dashboards, and stakeholder reports are derived by default.
- Derived outputs MAY explain why Ethereum and Base have different roles, but MUST preserve source lineage.
- Read models MAY summarize balances, cycles, contracts, and claims, but MUST label whether the source is Ethereum token state, Base operational state, or off-chain interpretation.
- If lag or reconciliation-pending posture exists, public-safe and internal read models MUST expose that carefully rather than silently collapsing uncertainty.

## Security / Risk / Abuse Controls

The chain architecture is also a trust and abuse boundary.

The following are required:

- no direct bypass from raw provider input to Base operational state without normalization
- no local product reimplementation of credits or payout chain semantics
- no silent reinterpretation of Ethereum holder truth by Base execution state
- no hidden operator shortcuts for chain-sensitive corrections or funding
- chain-adjacent credentials, adapters, and control paths MUST be tightly bounded, auditable, and environment-scoped
- degraded operation MUST fail safely rather than minting ambiguous cross-layer truth

## Boundary Violation Detection / Non-Canonical Patterns

The following are non-canonical and forbidden unless explicitly approved in a narrower governing specification:

- treating Base credits as a public substitute for FUZE token ownership
- treating a token purchase, token balance, or credits balance as automatic proof of distributable-profit entitlement without policy-bound eligibility derivation
- treating payout execution state as accounting truth
- treating transparency pages or registry pages as owners of contract meaning
- letting products create alternate credits or payout rails on different chains by local convention
- exposing public descriptions that imply FUZE is a one-asset or one-chain economic model when the architecture is intentionally layered

## Audit / Traceability Requirements

Chain-sensitive operations MUST preserve sufficient lineage to reconstruct:

- which chain role was involved
- which contract or operational layer was mutated or observed
- which off-chain domain authorized or interpreted the action
- which policy version, snapshot reference, cycle reference, or funding reference applied
- which operator or system initiated or approved sensitive actions
- which derived public or reporting artifacts were refreshed as a consequence

## Failure Handling / Edge Cases

### Chain/Off-Chain Disagreement
If Base or Ethereum state appears inconsistent with off-chain semantic or ledger state, the platform MUST preserve separation and reconcile through the owning domains rather than inventing ad hoc reinterpretation.

### Funding Ready but Not Published
If treasury funding readiness exists off-chain before Base commitment completes, reporting and user-facing surfaces MUST NOT present the cycle as fully executable until the correct execution layer reflects that state.

### Snapshot Complete but Payout Not Open
Eligibility finalization does not imply payout execution. Systems MUST keep those states distinct.

### Public Registry Lag
If registry or transparency outputs lag chain updates, source-domain truth remains canonical and publication layers MUST reconcile with explicit correction lineage where needed.

### Future Chain Expansion Proposal
Any proposal to move token, credits, or payout execution roles to a new chain MUST be treated as a supersession event requiring explicit specification change and migration planning.

## Operational Considerations

- runtime teams MUST monitor chain adapters, confirmations, discrepancy cases, and reporting lag separately for Ethereum and Base
- incident response MUST preserve clarity about which layer failed: token observation, eligibility preparation, credits synchronization, payout execution, registry publication, or transparency reporting
- recovery procedures MUST preserve correction lineage and MUST NOT rewrite history to hide cross-layer failures
- operator tooling MUST expose chain-role context explicitly

## Migration / Compatibility / Supersession Considerations

- legacy materials such as `FUZE_CHAIN_ARCHITECTURE.md` inform this refined document but do not override it
- compatibility layers MAY temporarily support older assumptions, but MUST preserve the canonical chain-role model defined here
- migration plans MUST identify whether they change chain-role assignment, chain-adjacent implementation only, or derived publication only
- if a downstream implementation assumed that “all FUZE economics live on one chain,” this specification supersedes that interpretation

## Implementation-Contract Guardrails

Downstream implementation contracts MUST preserve all of the following:

- explicit distinction among Ethereum token state, Base credits state, Base payout state, and off-chain policy/accounting/reporting state
- explicit chain identifiers and domain ownership in APIs, events, and data models
- idempotent chain-adjacent processing with replay and retry safety
- reason-coded and audited operator actions for chain-sensitive overrides or corrections
- lineage linkage from chain state to snapshot, policy, funding, ledger, registry, and reporting artifacts where relevant
- fail-closed posture when chain-role ambiguity would otherwise permit unsafe mutation

Downstream implementations MUST NOT optimize away:

- chain-role labels
- policy references
- cycle references
- snapshot references
- funding references
- discrepancy and reconciliation records
- public/private visibility distinctions

## Downstream Execution Staging

1. identify the relevant chain role
2. resolve off-chain owner domain and policy
3. prepare the bounded chain-adjacent mutation or observation
4. perform chain submission or chain observation
5. confirm and reconcile
6. update bounded downstream ledgers and derived publication layers
7. remediate or supersede with explicit lineage if needed

## Required Downstream Specs / Contract Layers

- `ETHEREUM_TOKEN_LAYER_SPEC.md`
- `BASE_PLATFORM_CREDITS_LAYER_SPEC.md`
- `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md`
- chain adapter / synchronization specifications
- chain-aware public/internal API specifications
- eligibility proof or dataset transport specifications
- registry and transparency publication linkage specifications
- discrepancy and reconciliation workflow specifications
- migration and rollback plans for chain-sensitive changes

## Canonical Examples / Anti-Examples

### Canonical Examples
- Ethereum holder balances are used as the canonical source for holder participation and downstream eligibility preparation.
- A verified and normalized payment outcome produces off-chain approved credits mutation lineage and then Base operational credits state.
- A payout cycle is funded and executed on Base while the cycle’s eligibility basis is derived from Ethereum holder truth and off-chain policy.
- A public registry page designates official contracts and wallets without claiming to own token, credits, or payout meaning.

### Anti-Examples
- “Base credits are the FUZE token on a cheaper chain.”
- “If the chain explorer shows a payout transaction, the accounting and policy meaning is fully settled.”
- “A product can treat its cached Base balance as the canonical platform commerce source.”
- “Ethereum token balance alone is the complete payout entitlement model.”
- “The public registry is the source of truth for treasury policy.”

## Dependencies / Cross-Spec Links

This specification depends materially on:

- `REFINED_SYSTEM_SPEC_INDEX.md`
- `DOCS_SPEC_INDEX.md`
- `SYSTEM_SPEC_INDEX.md`
- `API_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `FUZE_CHAIN_ARCHITECTURE.md`
- `PLATFORM_CREDITS_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `PAYOUT_LEDGER_SPEC.md`
- `BASE_PLATFORM_CREDITS_LAYER_SPEC.md`
- `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md`
- `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
- `TRANSPARENCY_MODEL_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`
- `TREASURY_CONTROL_POLICY_SPEC.md`
- `VAULT_ACTION_POLICY_SPEC.md`
- `MULTISIG_AND_TIMELOCK_SPEC.md`

## Explicitly Deferred Items

The following are intentionally deferred to narrower specifications:

- exact contract ABI and event schema
- exact Base contract topology
- exact Ethereum snapshot transport/proof format
- exact indexer and chain-data ingestion infrastructure
- exact public explanation wording for every surface
- exact multi-chain migration mechanics if a future role reassignment occurs
- exact chain-fee optimization and batching logic

## Final Normative Summary

FUZE uses a deliberate layered chain architecture. Ethereum is the canonical token participation layer for FUZE token identity and holder-balance truth. Base is the canonical operational chain layer for Platform Credits and stablecoin payout execution. Off-chain FUZE domains remain canonical for policy interpretation, accounting and treasury meaning, eligibility derivation, ledger lineage, workflow coordination, public registry publication, and transparency reporting. This separation is mandatory. Downstream specifications and implementations MUST preserve explicit distinction among token truth, credits truth, payout execution truth, accounting meaning, eligibility meaning, and public explanation. Any design that collapses those layers into one ambiguous “on-chain truth” model is non-canonical.

## Quality Gate Checklist

- [x] Canonical owner is explicit for the major chain-role truth families.
- [x] Mutation boundaries and semantic boundaries are explicit.
- [x] Adjacent boundaries are explicit.
- [x] Truth classes are explicit.
- [x] Conflict-resolution rules are explicit.
- [x] Default decision rules are explicit.
- [x] Non-canonical patterns are called out clearly.
- [x] Operator and control-path constraints are explicit.
- [x] Read-model and publication rules are explicit.
- [x] Failure and degraded-mode behaviors are explicit.
- [x] Downstream implementation guardrails are explicit.
- [x] Dependencies and downstream impacts are explicit.
- [x] Non-goals and deferred items are explicit.
- [x] The document is strong enough to guide downstream contract, API, runtime, reporting, and migration work without contradictory semantic invention.
