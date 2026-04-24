# FUZE Snapshot and Eligibility Pipeline Specification

## Document Metadata

- **Document Name:** `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
- **Document Type:** Canonical refined system specification
- **Status:** Active refined system spec
- **Version:** 2.0.0
- **Effective Date:** 2026-04-23
- **Last Updated:** 2026-04-23
- **Reviewed On:** 2026-04-23
- **Document Owner:** FUZE Snapshot and Eligibility Pipeline Domain (canonical owner for snapshot-cycle semantics, policy-bound eligibility derivation, address-treatment interpretation, dataset lineage, exclusion discipline, and implementation-contract guardrails); named individual owner is not explicitly specified in the retrieved governing materials
- **Approval Authority:** Not explicitly specified in the retrieved governing materials; constitutional approval authority remains governed by `REFINED_SYSTEM_SPEC_INDEX.md` and the active FUZE approval workflow
- **Review Cadence:** SHOULD be reviewed quarterly and whenever Ethereum token-layer posture, payout-cycle design, address-classification policy, registry posture, treasury-control posture, transparency posture, wallet-aware linkage posture, chain-indexing posture, or implementation-contract posture materially changes
- **Governing Layer:** Holder-participation derivation / cross-chain eligibility preparation / policy-bound dataset governance
- **Parent Registry:** `REFINED_SYSTEM_SPEC_INDEX.md`
- **Primary Audience:** Platform architecture, backend engineering, contracts engineering, data engineering, treasury/governance operators, reporting authors, security engineering, audit/compliance, runtime operations, wallet-aware platform authors, implementation-contract authors
- **Primary Purpose:** Define the canonical FUZE snapshot and eligibility pipeline that transforms Ethereum FUZE holder-balance truth into policy-finalized cycle-specific eligible datasets and entitlement inputs for profit participation and other approved holder-aware functions without collapsing token truth, wallet-link truth, treasury policy, payout execution, reporting, or registry publication into one ambiguous system
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
  - `CHAIN_ARCHITECTURE_SPEC.md`
  - `WALLET_AWARE_USER_SPEC.md`
  - `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
  - `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
  - `TREASURY_CONTROL_POLICY_SPEC.md`
  - `TRANSPARENCY_MODEL_SPEC.md`
  - `TRANSPARENCY_REPORTING_SPEC.md`
  - `API_ARCHITECTURE_SPEC.md`
  - `PUBLIC_API_SPEC.md`
  - `INTERNAL_SERVICE_API_SPEC.md`
  - `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
  - `IDEMPOTENCY_AND_VERSIONING_SPEC.md`
  - `MIGRATION_AND_BACKWARD_COMPATIBILITY_SPEC.md`
  - `AUDIT_LOG_AND_ACTIVITY_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - `MONITORING_ALERTING_AND_INCIDENT_RESPONSE_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
  - `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
- **Primary Downstream Dependents:**
  - `PAYOUT_LEDGER_SPEC.md`
  - `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md`
  - `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
  - `TRANSPARENCY_REPORTING_SPEC.md`
  - `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
  - `SNAPSHOT_ELIGIBILITY_API_SPEC.md`
  - payout entitlement-construction services
  - holder-rank and privilege derivation services where approved
  - public and holder-safe payout visibility surfaces
  - discrepancy, remediation, and supersession runbooks
- **Supersedes:** Earlier or weaker interpretations that treat raw Ethereum balances as automatically identical to payout entitlement, allow discretionary or undocumented exclusions, let treasury or product operators redefine eligibility post hoc, blur wallet-link context with token-balance truth, or treat spreadsheets, exports, dashboards, or public pages as canonical eligibility owners
- **Superseded By:** None currently defined
- **Related Decision Records:** Not explicitly specified in the retrieved governing materials
- **Canonical Status Note:** This document is the canonical governing FUZE specification for snapshot and eligibility pipeline semantics. Downstream APIs, services, datasets, proof-generation components, reporting outputs, registry linkages, operator/admin tooling, and remediation workflows MUST preserve the ownership, truth-separation, lifecycle, policy-versioning, lineage, and correction rules defined here.
- **Implementation Status:** Normative source; downstream snapshot, indexing, classification, eligibility, proof, payout, reporting, and read-model systems MUST align
- **Approval Status:** Draft refined canonical specification pending explicit approval workflow
- **Change Summary:** Refined the snapshot and eligibility pipeline into a production-grade canonical specification; normalized truth classes, ownership boundaries, lifecycle states, address-treatment semantics, dataset lineage, validation and reconciliation posture, conflict rules, default decisions, read-model restrictions, and implementation-contract guardrails while preserving explicit separation from Ethereum token truth, wallet-link truth, treasury control, payout execution, payout-ledger publication, and transparency artifacts

## Title

FUZE Snapshot and Eligibility Pipeline Specification

## Purpose

This specification defines the canonical FUZE snapshot and eligibility pipeline.

Its purpose is to make explicit:

- what the snapshot and eligibility domain governs and what it does not govern
- how Ethereum FUZE holder-balance truth becomes policy-treated cycle-specific eligibility truth
- how address classification, exclusions, and treatment rules interact with holder balances without mutating token truth
- how approved eligibility outputs become downstream entitlement inputs for profit participation and other bounded holder-aware functions
- how auditability, reproducibility, traceability, correction, supersession, and public explanation must be preserved
- what downstream implementations MUST preserve so they cannot reinterpret eligibility semantics inconsistently

This document is not a thesis, a white paper, an analytics note, or a convenience export guide. It is the governing specification for the semantic and operational meaning of snapshot and eligibility preparation inside FUZE.

## Scope

This specification governs:

- canonical snapshot and eligibility semantics
- cycle- or purpose-specific snapshot reference selection
- the distinction between raw holder-balance truth and policy-finalized eligible-balance truth
- address-category, inclusion, exclusion, and special-treatment interpretation
- canonical lifecycle of raw dataset extraction, policy application, eligible dataset construction, validation, publication readiness, and downstream linkage
- dataset lineage, versioning, idempotency, replay safety, correction, and supersession
- what must be explicit for profit participation cycles and what MAY be reused for other holder-aware functions
- interaction with wallet-aware context, public registry surfaces, transparency reporting, and payout execution without collapsing those domains together
- implementation-contract guardrails for APIs, services, storage, proofs, events, projections, runbooks, and admin/control-plane tooling

## Out of Scope

This specification does not fully define:

- the FUZE token contract ABI, Ethereum token-layer internals, or low-level chain-indexing implementation details
- the detailed Base payout contract ABI, claim UX, or final proof-verification mechanics
- treasury approval matrices, multisig signing procedures, or reserve-category operating procedures in full detail
- full payout-ledger publication schema, public-report templates, or registry publication templates
- full wallet-link proof semantics, account recovery, or session/auth behavior beyond dependency constraints
- generic holder-rank, campaign, or privilege rules beyond the requirements they must preserve if they consume this pipeline
- low-level service topology, queue internals, database partitioning, or infrastructure-specific implementation details

Those remain governed by adjacent specifications, especially `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`, `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md`, `PAYOUT_LEDGER_SPEC.md`, `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`, `WALLET_AWARE_USER_SPEC.md`, `TREASURY_CONTROL_POLICY_SPEC.md`, and the account/session foundation documents.

## Design Goals

The design goals of the FUZE snapshot and eligibility pipeline are to:

1. preserve Ethereum as the canonical holder-participation source for holder-aware economic logic
2. transform raw balances into policy-defined eligible datasets without pretending those two truths are identical
3. require explicit, versioned, reviewable treatment of treasury, Foundation, team, vesting, operational, migration, and other special address classes
4. make each cycle or purpose reproducible through explicit snapshot references, policy references, dataset identifiers, and validation artifacts
5. support profit participation cycles with enough rigor that downstream payout execution cannot silently distort eligibility meaning
6. preserve strong audit, transparency, and public-trust posture without exposing unsafe private detail
7. support bounded reuse of the pipeline for other holder-aware functions while preventing local redefinition of the platform model
8. reduce architectural drift, terminology drift, and spreadsheet-driven shadow semantics across engineering, finance, governance, and reporting layers

## Non-Goals

This specification is not intended to:

- make raw balances automatically identical to eligible balances or claimable payout amounts
- let Base payout execution decide eligibility independently
- let wallet-link ownership replace token-balance truth or account-rooted identity rules
- let public registries, transparency reports, dashboards, or exported CSVs become canonical eligibility owners
- allow product teams or routine operators to improvise exclusion logic on a per-cycle basis
- force all eligibility logic on-chain when policy interpretation and classification are explicitly off-chain responsibilities
- require every holder-aware function to use identical treatment rules as profit participation

## Core Principles

### Principle 1: Ethereum Participation Truth Is Canonical
The FUZE token on Ethereum mainnet is the canonical source of raw holder-balance truth for holder-based eligibility.

### Principle 2: Eligibility Truth Is Derived, Not Observed Directly
Eligible-balance truth is a policy-treated derivative of raw balance truth. It is authoritative only after the approved snapshot and policy-treatment process has completed.

### Principle 3: Policy Must Be Explicit
Address-category treatment, exclusions, threshold rules, and purpose-specific policy variations MUST be explicit, versioned, and traceable. No payout-sensitive exclusion may exist only in operator memory or ad hoc spreadsheets.

### Principle 4: The Pipeline Bridges, It Does Not Replace Domains
This pipeline bridges token participation truth to downstream holder-aware systems. It does not absorb treasury truth, wallet-link truth, payout-execution truth, payout-ledger truth, or public-report truth.

### Principle 5: Reproducibility Is Mandatory
A cycle or purpose MUST be reproducible from its snapshot reference, policy version, source token reference, classification basis, resulting dataset identifier, and validation lineage.

### Principle 6: Traceability Beats Convenience
If an optimization would weaken lineage, reversibility, or public intelligibility for a trust-sensitive cycle, the optimization MUST be rejected or explicitly bounded.

## Canonical Definitions

### Snapshot Reference
The explicit canonical reference used to measure Ethereum FUZE balances for a given cycle or holder-aware purpose. This reference MAY be a block number, block hash, timestamp-bound block selection, or equivalent deterministic chain reference approved by policy.

### Raw Holder Dataset
The unmodified address-to-balance view derived from canonical Ethereum token truth at the chosen snapshot reference.

### Address Classification
The policy-bound assignment of an address to one or more recognized treatment categories for the relevant purpose.

### Policy Treatment
The rules that determine whether, how, and to what extent a balance counts toward the relevant holder-aware output.

### Eligible Dataset
The canonical derived dataset that results from applying policy treatment to the raw holder dataset for a specific cycle or purpose.

### Entitlement Input
The downstream-ready representation derived from the eligible dataset for use by payout execution or another approved holder-aware system.

### Cycle Eligibility Basis
The linked set of references that together explain a specific cycle’s eligibility truth: token source, snapshot reference, policy version, classification basis, eligible dataset identifier, validation status, and approval lineage.

### Excluded Address
An address or address class whose balance is excluded entirely for the relevant purpose under the applicable policy.

### Special-Treatment Address
An address or address class that is counted only under explicit alternative treatment, partial counting, capped counting, or other bounded policy logic.

### Purpose Variant
A formally named variant of the pipeline for a specific approved downstream use such as profit participation, holder rank, ecosystem privilege, or future governance-aware dataset generation.

## Truth Class Taxonomy

The snapshot and eligibility pipeline MUST distinguish the following truth classes:

1. **Participation Truth** — raw FUZE holder balances on Ethereum at a deterministic reference
2. **Classification Truth** — policy-governed address-category assignments and treatment bases
3. **Eligibility Truth** — the canonical derived result of applying policy to participation truth
4. **Entitlement-Preparation Truth** — the downstream-ready allocation or proof input derived from eligibility truth
5. **Wallet-Link Truth** — account-to-wallet linkage and wallet-proof acceptance owned by the wallet-aware user domain
6. **Treasury / Governance Truth** — funding authority, approval posture, and sensitive control decisions owned by adjacent domains
7. **Execution Truth** — Base funding state, claim state, and execution outcomes owned by the payout-execution domain
8. **Ledger Truth** — the structured cycle-linked record owned by the payout-ledger domain
9. **Reporting / Registry Truth** — public-safe publication outputs owned by transparency and registry domains
10. **Audit Truth** — internal decision, mutation, and validation lineage
11. **Projection / Read-Model Truth** — dashboards, public pages, internal summaries, search views, and exported files derived from canonical domains

These truth classes MUST NOT be collapsed into one undifferentiated “holder eligibility” object.

## Architectural Position in the Spec Hierarchy

This document sits:

- below the top-level constitutional platform, ownership, and on-chain/off-chain responsibility specifications
- adjacent to token-layer, wallet-aware, payout, treasury, transparency, governance, and public-registry specifications
- above downstream API, storage, proof, worker, publication, and remediation specifications for the snapshot and eligibility domain

This document governs snapshot and eligibility semantics. It does not override higher-order ownership or chain-boundary documents, and it does not absorb the narrower responsibilities of adjacent domains.

## System Boundaries

The snapshot and eligibility domain governs:

- snapshot reference semantics
- raw holder dataset extraction semantics
- address classification and treatment semantics
- eligible dataset construction semantics
- validation, reconciliation, and approval readiness semantics
- dataset identity, lineage, versioning, and correction semantics
- the semantic contract of what downstream systems may consume as eligibility basis

The snapshot and eligibility domain does **not** own:

- Ethereum token balances or token contract state itself
- wallet-link records or account identity semantics
- distributable-profit calculation or treasury funding authorization
- Base claim execution state or payout contract balances
- payout-ledger publication records
- public registry publication records
- transparency report publication truth
- workspace authorization, product entitlements, or session/auth truths

## Adjacent Boundaries

### Relationship to Ethereum Token Layer
The Ethereum token layer owns token identity, supply, transfer, and holder-balance truth. This pipeline reads that truth; it does not mutate or reinterpret token-layer ownership.

### Relationship to Wallet-Aware User Domain
Wallet-aware user truth governs account-to-wallet associations and wallet-proof acceptance. This pipeline MAY consume wallet-link context where a downstream experience requires account association, but wallet-link truth MUST NOT replace raw holder-balance truth or silently modify eligibility semantics.

### Relationship to Profit Participation
Profit participation defines the business meaning of a payout cycle and consumes approved eligibility outputs. It does not own the algorithmic or policy semantics of snapshot derivation.

### Relationship to Base Payout Execution
Base payout execution consumes approved entitlement inputs and owns claim execution state. It MUST NOT redefine the pipeline’s eligibility outputs.

### Relationship to Payout Ledger
The payout ledger owns the structured cycle record linking eligibility basis, funding basis, execution basis, and reporting basis. The pipeline must provide stable references that the payout ledger can link, but it does not own ledger truth.

### Relationship to Treasury and Governance
Treasury and governance domains approve whether a cycle is funded or paused. They MUST NOT silently alter classification or eligibility meaning after the fact. Changes affecting treatment policy require explicit policy versioning through the appropriate owner path.

### Relationship to Public Registry and Transparency Reporting
Registry and reporting domains may publish public-safe explanations and references about eligibility methodology, contract usage, and cycle lineage. They MUST remain derived and must not invent new eligibility truth.

## Conflict Resolution Rules

When materials, systems, or operators disagree, the following rules apply:

1. the active refined registry and higher constitutional materials win over narrower documents
2. `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md` wins on top-level ownership and mutation-owner interpretation
3. `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md` wins on canonical-versus-derived status and entity-family ownership
4. `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md` and `CHAIN_ARCHITECTURE_SPEC.md` win on chain-role boundaries and on-chain/off-chain responsibility posture
5. `WALLET_AWARE_USER_SPEC.md` wins on wallet-link semantics, account association, and wallet-proof interpretation
6. this document wins on snapshot-reference meaning, address-treatment semantics, eligibility derivation posture, validation discipline, and dataset lineage rules
7. `PROFIT_PARTICIPATION_SYSTEM_SPEC.md` wins on the semantic business meaning of funded payout cycles
8. `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md` wins on claim-execution mechanics and contract-state meaning
9. `PAYOUT_LEDGER_SPEC.md` wins on structured cycle-ledger record meaning and correction lineage
10. if ambiguity remains, the more conservative architecture-consistent interpretation MUST be chosen, and no downstream layer may silently optimize away traceability or trust-sensitive explicitness

## Default Decision Rules

Where ambiguity is likely, the following defaults apply:

1. **No deterministic snapshot reference means no valid eligibility basis.**
2. **No explicit policy version means the dataset MUST NOT be treated as canonical.**
3. **If raw balance truth and eligible balance truth differ, eligible balance truth controls only for the named cycle or purpose.**
4. **If an address category is contested and no explicit treatment exists, the conservative default is exclusion until policy states otherwise.**
5. **If wallet-link context conflicts with token-holder truth, token-holder truth controls eligibility unless a higher-order approved policy explicitly introduces an account-linked overlay for a specific downstream use.**
6. **If funding has been approved but eligibility lineage is incomplete, the cycle MUST NOT open.**
7. **If execution-ready entitlement input cannot be reconciled to the approved eligible dataset, downstream publication and execution MUST stop pending remediation.**
8. **If public explanation would reveal unsafe or private operational detail, public output MUST be narrowed while preserving full internal canonical lineage.**
9. **If operator convenience conflicts with reproducibility, reproducibility wins.**
10. **If a material post-publication dataset error cannot be corrected safely in place, supersession MUST be used instead of silent rewrite.**

## Roles / Actors / Entities

### Roles / Actors
- **Snapshot and Eligibility Domain Owner:** owns semantic rules, lifecycle meaning, conflict posture, and downstream guardrails
- **Chain-Data / Indexing Function:** retrieves token-holder state from Ethereum according to the approved deterministic reference
- **Policy / Classification Authority:** owns or approves address-category treatment and policy versions within the bounded governance model
- **Treasury / Governance Authority:** approves downstream cycle-sensitive actions that depend on eligibility outputs, without owning eligibility derivation semantics
- **Payout Execution Domain Owner:** consumes approved entitlement inputs for Base execution
- **Payout Ledger Domain Owner:** records linked cycle basis and correction lineage
- **Transparency / Registry Domain Owners:** publish public-safe derived artifacts
- **Security / Audit / Operations Teams:** enforce controls, investigate discrepancies, and support remediation
- **Eligible Holder or Downstream Consumer:** receives or consumes the consequences of the approved dataset according to the relevant purpose

### Core Entities
- purpose or cycle identifier
- token contract reference
- snapshot reference
- raw holder dataset identifier
- policy version record
- address classification registry reference
- treatment result set
- eligible dataset identifier
- entitlement-input identifier or proof-root reference
- validation result reference
- approval readiness reference
- publication readiness reference
- correction / supersession reference
- operator reason code
- audit trace identifier

## Ownership Model

The canonical ownership model is:

- **Ethereum Token Domain** owns raw holder-balance truth
- **Snapshot and Eligibility Domain** owns snapshot reference selection, address-treatment semantics, raw-to-eligible transformation rules, validation meaning, and eligible-dataset truth
- **Wallet-Aware User Domain** owns account-to-wallet linkage and wallet-proof acceptance truth
- **Profit Participation Domain** owns the payout-cycle semantic meaning of consuming the approved eligibility basis
- **Base Payout Execution Domain** owns claim-execution truth
- **Payout Ledger Domain** owns structured cycle-ledger truth
- **Treasury / Governance Domains** own funding, approval, and control posture
- **Transparency Reporting Domain** owns recurring public reporting publication truth
- **Public Contract and Wallet Registry Domain** owns public registry publication truth
- **Audit Domain** owns durable audit-event lineage

No dashboard, spreadsheet, export, or local service MAY redefine this ownership model.

## Authority / Decision Model

### Canonical Authority Rules
1. routine operators MUST NOT invent or adjust eligibility semantics outside approved policy versions
2. address-category changes that materially affect a cycle MUST be explicit, reason-coded, and auditable
3. funding approval does not confer authority to rewrite classification or treatment policy
4. payout-execution teams MAY validate consumability of an entitlement input but MUST NOT redefine its meaning
5. public-reporting or registry teams MAY summarize methodology but MUST NOT author canonical eligibility truth
6. wallet-aware or product teams MAY consume the pipeline but MUST NOT replace it with product-local approximations for shared economic behavior

### Operator/Admin Override Posture
Override paths MAY exist only where they are:

- explicitly named
- policy-constrained
- narrowly scoped
- reason-coded
- fully audited
- reviewable after the fact
- unable to silently rewrite historical dataset meaning

Permitted override examples MAY include pausing publication, invalidating an in-progress dataset before approval, or superseding a published dataset through formal lineage. Silent mutation of an already-approved canonical dataset is forbidden.

## State Model

At minimum, the snapshot and eligibility pipeline MUST support explicit lifecycle state.

Suggested canonical lifecycle states include:

- `draft`
- `reference_selected`
- `raw_extracted`
- `classification_applied`
- `eligibility_constructed`
- `validation_pending`
- `validated`
- `approved_for_downstream_use`
- `published_internal`
- `linked_to_cycle`
- `discrepancy_under_review`
- `superseded`
- `cancelled_pre_approval`

Downstream implementations MAY refine internal sub-states, but they MUST preserve explicit separation between reference selection, raw extraction, treatment, validation, approval readiness, downstream linkage, and supersession.

## Lifecycle / Workflow Model

### 1. Purpose or Cycle Initialization
A specific purpose is declared, such as a profit participation cycle or another approved holder-aware use.

### 2. Snapshot Reference Selection
The deterministic Ethereum reference point is fixed and recorded.

### 3. Raw Holder Extraction
The raw address-to-balance dataset is extracted from canonical token truth at that reference.

### 4. Address Classification Resolution
Known addresses and categories are resolved against the approved classification basis, registry references, and policy version.

### 5. Policy Treatment Application
Inclusion, exclusion, capping, weighting, or special-treatment rules are applied explicitly.

### 6. Eligible Dataset Construction
The canonical eligible dataset is produced and given a stable identifier.

### 7. Entitlement-Preparation Transformation
Where required, the eligible dataset is transformed into the downstream input representation needed by payout execution or another approved consumer.

### 8. Validation and Reconciliation
The dataset is validated against expected totals, treatment expectations, reference integrity, and downstream consumability constraints.

### 9. Approval Readiness and Internal Publication
The approved eligibility basis is published internally with stable references and audit lineage.

### 10. Downstream Linkage
The dataset is linked to a payout cycle, payout ledger record, proof-generation workflow, or another approved holder-aware downstream system.

### 11. Public-Safe Explanation
Where appropriate, public reporting or registry surfaces receive a derived explanation of methodology and lineage without exposing unsafe internal detail.

### 12. Correction, Remediation, or Supersession
If a material issue is found, the dataset is corrected through explicit lineage or superseded according to policy; silent rewrite is forbidden.

## Invariants

The following invariants are mandatory:

1. Ethereum remains the canonical source of raw FUZE holder-balance truth
2. raw balances are never automatically equal to eligible balances
3. eligible datasets are purpose-specific and policy-versioned
4. address treatment is always explicit, never implied by operator convention alone
5. wallet-link truth never replaces token-balance truth
6. funding approval never silently rewrites eligibility semantics
7. payout execution never becomes the owner of eligibility meaning
8. derived reports and public pages never become canonical eligibility truth
9. every approved dataset must be reproducible from its references and lineage
10. correction and supersession must preserve historical meaning and auditability

## Functional Rules

1. every canonical dataset MUST have a unique identifier
2. every canonical dataset MUST reference the exact token contract and deterministic snapshot reference used
3. every canonical dataset MUST reference the policy version applied
4. every canonical dataset MUST record which address classification basis or registry references were used
5. classification, exclusion, and special treatment MUST be explainable in structured terms
6. downstream consumability checks MUST be explicit for payout-sensitive use cases
7. entitlement inputs MUST remain traceable to the approved eligible dataset from which they were derived
8. re-running extraction for the same reference and policy version MUST be idempotent or yield equivalent canonical output unless a correction path is explicitly invoked
9. a dataset linked to an open payout cycle MUST NOT be silently mutated
10. any purpose-specific variant MUST preserve the pipeline discipline even if treatment rules differ from profit participation

## Permission / Access Considerations

Although the pipeline is not a workspace-owned workflow, access controls still apply:

- privileged mutation surfaces MUST require explicit domain-appropriate authorization
- classification-policy changes and dataset-approval actions MUST require stronger controls than routine reads
- public and holder-safe surfaces MAY expose bounded methodology and cycle linkage, but not unsafe internal detail
- account, session, workspace, and authorization systems MAY authenticate user access to holder-facing views, but they MUST NOT redefine eligibility semantics
- workspace scope MUST NOT be misused as a substitute for holder participation truth

## Entitlement Considerations

Eligibility truth is not the same as product entitlement or workspace capability.

For profit participation, eligibility truth becomes a bounded cycle-specific input to downstream entitlement construction. For other approved uses, a purpose variant MAY derive rank, privilege, or participation-aware outputs, but those outputs MUST remain explicitly derived from the pipeline rather than replacing it.

Downstream teams MUST NOT conflate:

- holder eligibility with workspace roles
- holder eligibility with credits balances
- wallet linkage with token-holder truth
- eligible balances with executed payout receipts
- public reporting state with canonical dataset state

## API / Contract Implications

Downstream APIs and contracts MUST preserve the following:

1. separate command surfaces for reference selection, extraction, validation, approval, publication linkage, and supersession
2. strong idempotency for trust-sensitive mutations such as dataset creation, approval, invalidation, correction registration, and downstream linkage
3. explicit correlation IDs, cycle/purpose references, policy references, dataset identifiers, and actor trace data for material mutations
4. narrow, explicit mutation contracts rather than generic “update eligibility” endpoints
5. a clear distinction between internal-service APIs, admin/control-plane APIs, and public-safe read APIs
6. no API surface may permit silent post-approval mutation of canonical eligibility outputs
7. contract layers that consume entitlement inputs MUST expose enough stable references to support ledger and reporting linkage without becoming the semantic owner of eligibility truth

## Event / Async Implications

The pipeline is a cross-domain workflow and SHOULD use event-first coordination for side effects.

Required event families MAY include:

- snapshot reference selected
- raw holder dataset extracted
- classification basis resolved
- policy treatment applied
- eligible dataset constructed
- validation completed
- dataset approved for downstream use
- entitlement input materialized
- dataset linked to cycle
- dataset invalidated pre-publication
- discrepancy detected
- dataset corrected or superseded
- public methodology artifact published or superseded

Events MUST be idempotent or replay-safe, include stable identifiers and policy references, and preserve source-domain ownership boundaries.

## Data Model / Storage Implications

The snapshot and eligibility domain SHOULD maintain durable canonical records for:

- snapshot references
- raw dataset identifiers and provenance references
- policy versions and treatment references
- address classification references and materialization lineage
- eligible datasets and summary totals
- entitlement-input lineage
- validation results and reconciliation artifacts
- approval status and downstream linkage references
- discrepancy, correction, and supersession records
- audit trace identifiers and reason codes

It MUST NOT rely solely on ephemeral jobs, spreadsheets, or unverifiable exports as the canonical record.

## Read Model / Projection / Reporting Rules

Derived read models MAY exist for:

- internal cycle-readiness dashboards
- holder-facing methodology summaries
- public payout explanation pages
- classification-review consoles
- audit and support investigation views
- data exports for bounded approved use

These projections:

- MUST remain subordinate to canonical source domains
- MUST indicate when data is derived, delayed, or partial
- MUST preserve supersession and correction lineage where omission would mislead
- MUST NOT merge raw balance truth, eligible balance truth, and executed payout truth into one ambiguous field
- MUST NOT expose unsafe internal-only classification detail unless explicitly approved for the audience and purpose

## Security / Risk / Abuse Controls

The snapshot and eligibility domain MUST include controls for:

- unauthorized mutation of snapshot references, classifications, or approved datasets
- replay or duplicate processing of extraction, approval, or downstream-linkage actions
- incorrect token contract or wrong chain reference usage
- malicious or accidental misclassification of controlled addresses
- hidden post-approval changes to eligibility treatment
- unsafe publication of private operational detail through public surfaces
- shadow dataset generation outside canonical services
- compromised operator or service credentials affecting payout-sensitive outputs

Security posture MUST be stronger for payout-sensitive paths than for ordinary analytics or support exports.

## Boundary Violation Detection / Non-Canonical Patterns

The following patterns are explicitly non-canonical and forbidden unless a newer approved higher-order document says otherwise:

1. treating raw Ethereum balances as automatically identical to payout entitlement
2. using a spreadsheet, BI dashboard, or manual CSV as the canonical dataset owner
3. letting treasury funding approval silently redefine classification or exclusion treatment
4. letting wallet-link state rewrite token-holder truth
5. allowing a product team to publish a “holder eligibility” list that conflicts with the canonical pipeline for shared economic behavior
6. silently mutating an approved dataset after downstream linkage
7. generating public explanations that imply every holder-aware function uses identical treatment when that is not true
8. consuming an entitlement proof or root whose lineage cannot be traced to an approved eligible dataset
9. collapsing raw balance truth, eligible balance truth, and execution truth into one field called “holder balance”
10. bypassing formal correction or supersession because “the public won’t notice”

## Audit / Traceability Requirements

The pipeline MUST preserve audit lineage sufficient to answer:

- what token contract was used
- what deterministic reference was used
- what policy version was used
- what address classification basis was used
- what inclusion/exclusion logic was applied
- who initiated, approved, invalidated, corrected, or superseded the dataset
- which downstream systems consumed the output
- which public-safe artifacts referenced the output
- what discrepancy, if any, was discovered and how it was resolved

Material actions MUST be reason-coded where operator or admin discretion exists.

## Failure Handling / Edge Cases

The pipeline MUST define deterministic handling for at least the following cases:

1. **Wrong Token or Wrong Chain Reference:** output MUST be invalidated; downstream linkage MUST stop; correction lineage MUST be created.
2. **Ambiguous Address Ownership:** conservative exclusion MUST apply until explicit treatment is approved.
3. **Late Discovery of Controlled Address:** if pre-publication, regenerate or supersede; if post-linkage, formal discrepancy handling and downstream remediation are required.
4. **Validation Totals Do Not Reconcile:** dataset MUST NOT be approved.
5. **Entitlement Transformation Mismatch:** downstream execution MUST NOT open until reconciliation succeeds.
6. **Post-Publication Methodology Clarification:** public-safe explanation MAY be updated, but historical dataset lineage MUST remain intact.
7. **Chain Reorg or Reference Integrity Issue:** affected datasets MUST be reviewed, invalidated, or superseded explicitly according to policy.
8. **Purpose Variant Drift:** if a non-profit-participation use tries to reuse the pipeline with conflicting semantics, a formal purpose variant or adjacent spec is required before production use.

## Operational Considerations

Operational systems SHOULD provide:

- deterministic and reviewable snapshot jobs
- explicit validation checkpoints before downstream approval
- reproducible artifact storage for source references and summary outputs
- monitoring for dataset-generation failure, reconciliation anomalies, and downstream-consumption mismatches
- operator tooling for discrepancy review, invalidation, and supersession with bounded authority
- bounded emergency stop paths for payout-sensitive dataset usage

Operational convenience MUST NOT erase canonical references or audit lineage.

## Migration / Compatibility / Supersession Considerations

Older exports, spreadsheets, or loosely defined eligibility lists MUST migrate toward explicit canonical dataset records with stable references.

During migration:

- legacy identifiers MAY be preserved as non-canonical aliases for lookup
- canonical dataset identifiers, policy references, and lineage MUST become explicit
- downstream consumers MUST migrate off local shadow logic and onto canonical eligibility references
- supersession MUST preserve historical intelligibility instead of rewriting old records in place
- backward-compatible read models MAY exist temporarily, but canonical meaning MUST remain explicit

## Implementation-Contract Guardrails

1. raw balance truth and eligible balance truth remain distinct
2. deterministic reference, policy version, and classification basis remain explicit
3. dataset approval and downstream linkage remain distinct lifecycle concepts
4. wallet-link truth remains separate from eligibility truth
5. funding and execution domains remain consumers, not owners, of eligibility semantics
6. public-safe reporting remains derived and bounded
7. correction and supersession lineage remain explicit and queryable
8. idempotency, replay safety, and auditability remain mandatory for trust-sensitive mutations
9. downstream teams MUST NOT create shadow eligibility truth that conflicts with this domain
10. degraded runtime conditions MUST not cause the system to lose explicit references or silently fall back to ad hoc lists

## Downstream Execution Staging

1. stabilize snapshot-reference semantics and deterministic selection rules
2. stabilize address-classification registry and treatment taxonomy
3. stabilize eligible-dataset identity, summary, and lineage semantics
4. stabilize entitlement-input linkage semantics for payout execution
5. stabilize discrepancy, correction, and supersession workflows
6. stabilize public-safe methodology publication and transparency/reporting linkage

## Required Downstream Specs / Contract Layers

- `SNAPSHOT_ELIGIBILITY_API_SPEC.md`
- `PAYOUT_LEDGER_SPEC.md`
- `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- public-safe eligibility methodology and payout-status publication contracts
- snapshot discrepancy/remediation runbooks
- classification registry and review contracts
- proof-generation or entitlement-materialization contracts where applicable

## Canonical Examples / Anti-Examples

### Canonical Examples
- **Deterministic Cycle Snapshot:** a payout cycle references an exact Ethereum snapshot reference, policy version, classification basis, eligible dataset identifier, and validation record before any claim-opening occurs.
- **Conservative Controlled-Address Treatment:** a newly identified treasury-controlled address is excluded until explicit policy approval clarifies treatment, and the resulting decision is trace-linked.
- **Purpose-Variant Reuse With Discipline:** a holder-rank service reuses the same snapshot discipline and explicit policy-versioning model but applies a distinct approved purpose variant rather than pretending payout logic and rank logic are identical.
- **Supersession With Lineage:** a pre-publication validation issue causes a dataset to be superseded by a corrected version, with both lineage and reason code preserved.

### Anti-Examples
- **Spreadsheet Canonicality:** an analyst-maintained CSV becomes the de facto owner of eligibility for a payout cycle.
- **Funding Implies Eligibility:** operators assume that because treasury has funded a cycle, the latest convenient address list is automatically valid.
- **Wallet-Link Override:** an account-linked wallet table is used to omit or add holders without the canonical token snapshot and policy path.
- **Silent Post-Open Rewrite:** an already-consumed dataset is changed without discrepancy handling, supersession, or payout-ledger correction lineage.

## Dependencies / Cross-Spec Links

This specification depends on:

- `REFINED_SYSTEM_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `CHAIN_ARCHITECTURE_SPEC.md`
- `WALLET_AWARE_USER_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md`
- `PAYOUT_LEDGER_SPEC.md`
- `TREASURY_CONTROL_POLICY_SPEC.md`
- `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
- `TRANSPARENCY_MODEL_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`
- `API_ARCHITECTURE_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `AUDIT_LOG_AND_ACTIVITY_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`

## Explicitly Deferred Items

- exact block-selection algorithm if timestamp-to-block resolution is used
- exact address-classification registry schema and storage topology
- exact partial-counting or weighting rules for every future address category
- exact proof-root or claimant-proof materialization format
- exact public disclosure depth for methodology artifacts and excluded-address detail
- exact quorum thresholds for classification-sensitive approvals and emergency actions
- exact automation topology for indexing, retries, and recomputation workers

## Final Normative Summary

The FUZE snapshot and eligibility pipeline is the canonical bridge between Ethereum holder-participation truth and downstream holder-aware platform behavior. It begins from deterministic Ethereum token-balance truth, applies explicit policy-bound address treatment, produces purpose-specific canonical eligible datasets, and links those outputs to payout and reporting systems through explicit lineage. It does not replace wallet-link truth, treasury control, payout execution, payout-ledger publication, or transparency publication. Downstream implementations MUST preserve deterministic references, policy versioning, explicit classification, idempotent mutation discipline, correction-safe lineage, and clear separation between raw balances, eligible balances, entitlement inputs, and executed outcomes.

## Quality Gate Checklist

- [x] Canonical owner is explicit for every material truth family
- [x] Mutation boundaries are explicit
- [x] Adjacent boundaries are explicit
- [x] Truth classes are explicit
- [x] Conflict-resolution rules are explicit where needed
- [x] Default decision rules are explicit where ambiguity could arise
- [x] Non-canonical patterns are called out clearly
- [x] Operator/admin override paths are bounded and audited
- [x] Read-model, cache, reporting, and projection rules are explicit
- [x] On-chain vs off-chain responsibilities are explicit
- [x] Failure and degraded-mode behaviors are explicit
- [x] Downstream implementation guardrails are explicit
- [x] Dependencies and downstream impacts are explicit
- [x] Non-goals and deferred items are explicit
- [x] The document is strong enough that backend/API/data/runtime teams can implement without inventing contradictory semantics
