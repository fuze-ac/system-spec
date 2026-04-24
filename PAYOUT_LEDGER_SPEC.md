# FUZE Payout Ledger Specification

## Document Metadata

- **Document Name:** `PAYOUT_LEDGER_SPEC.md`
- **Document Type:** Canonical refined system specification
- **Status:** Active refined system spec
- **Version:** 2.0.0
- **Effective Date:** 2026-04-23
- **Last Updated:** 2026-04-23
- **Reviewed On:** 2026-04-23
- **Document Owner:** FUZE Payout Ledger Domain (canonical owner for payout-cycle ledger semantics, ledger-state meaning, correction and supersession lineage, visibility-class governance, reconciliation posture, and implementation-contract guardrails); named individual owner is not explicitly specified in the retrieved governing materials
- **Approval Authority:** Not explicitly specified in the retrieved governing materials; constitutional approval authority remains governed by `REFINED_SYSTEM_SPEC_INDEX.md` and the active FUZE approval workflow
- **Review Cadence:** SHOULD be reviewed quarterly and whenever profit-participation policy, eligibility lineage, Base payout execution posture, treasury-control posture, transparency posture, registry posture, correction posture, or implementation-contract posture materially changes
- **Governing Layer:** Economic trust record / cycle-ledger governance / payout-history and reconciliation layer
- **Parent Registry:** `REFINED_SYSTEM_SPEC_INDEX.md`
- **Primary Audience:** Platform architecture, backend engineering, contracts engineering, finance and accounting, treasury/governance operators, reporting authors, public-trust and registry authors, security engineering, audit/compliance, runtime operations, implementation-contract authors
- **Primary Purpose:** Define the canonical FUZE payout ledger domain that preserves the structured, correction-safe, reconcilable record of payout cycles by linking policy basis, eligibility basis, treasury funding lineage, Base execution references, claim-state posture, and public-trust publication references without collapsing ledger truth into accounting books, execution truth, audit truth, reporting truth, or registry truth
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
  - `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
  - `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
  - `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md`
  - `BASE_PLATFORM_CREDITS_LAYER_SPEC.md`
  - `TREASURY_CONTROL_POLICY_SPEC.md`
  - `TRANSPARENCY_MODEL_SPEC.md`
  - `TRANSPARENCY_REPORTING_SPEC.md`
  - `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
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
  - `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md`
  - `TRANSPARENCY_REPORTING_SPEC.md`
  - `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
  - `PAYOUT_LEDGER_API_SPEC.md`
  - public payout-status and cycle-history surfaces
  - treasury and finance reconciliation workflows
  - payout-cycle administration and correction tooling
  - discrepancy, incident, and supersession runbooks
  - holder-facing and public-safe payout visibility surfaces
- **Supersedes:** Earlier or weaker interpretations that treat contract transactions alone as the full payout history, treat transparency reports as canonical cycle truth, treat accounting books as the public payout ledger, allow silent mutation of historical cycle records, collapse claim-state facts and ledger-state facts into one ambiguous field, or let dashboards, spreadsheets, exports, or ad hoc scripts own payout-history meaning
- **Superseded By:** None currently defined
- **Related Decision Records:** Not explicitly specified in the retrieved governing materials
- **Canonical Status Note:** This document is the canonical governing FUZE specification for payout-ledger semantics. Downstream APIs, services, public and internal read models, correction tooling, reporting outputs, registry linkages, and reconciliation workflows MUST preserve the ownership boundaries, truth separation, lifecycle rules, conflict posture, and implementation guardrails defined here.
- **Implementation Status:** Normative source; downstream ledger storage, reconciliation jobs, publication surfaces, and operational runbooks MUST align
- **Approval Status:** Draft refined canonical specification pending explicit approval workflow
- **Change Summary:** Refined the payout-ledger domain into a production-grade canonical specification; normalized truth classes, ownership boundaries, cycle identity, ledger-state semantics, visibility classes, correction and supersession posture, reconciliation rules, conflict handling, read-model restrictions, operator constraints, and implementation-contract guardrails while preserving explicit separation from execution truth, accounting truth, audit truth, transparency truth, and registry publication truth

## Title

FUZE Payout Ledger Specification

## Purpose

This specification defines the canonical FUZE payout ledger.

Its purpose is to make explicit:

- what the payout-ledger domain governs and what it does not govern
- how each profit-participation cycle is preserved as a durable, structured, explainable ledger object
- how the ledger links eligibility basis, policy basis, funding lineage, execution references, claim-state posture, reporting references, and correction lineage without replacing those domains
- what ownership, authority, lifecycle, reconciliation, publication, correction, and conflict-resolution rules downstream implementations MUST preserve
- what non-canonical shortcuts teams MUST NOT introduce in holder-facing, operator-facing, reporting-facing, or finance-facing payout history surfaces

This document is not a narrative summary, not a treasury ledger, not a contract ABI spec, and not a reporting template. It is the governing specification for the semantic and operational meaning of payout-ledger truth inside FUZE.

## Scope

This specification governs:

- canonical payout-ledger semantics
- payout-cycle identity, versioning, and ledger-state meaning
- required linkage among cycle policy references, eligibility references, funding references, execution references, reporting references, and registry references where applicable
- visibility-class rules for public, operator, and audit-adjacent ledger surfaces
- reconciliation posture across cycle funding, execution, claim-state summaries, reporting, and treasury lineage
- correction, supersession, cancellation, and closure rules for payout-ledger records
- auditability, idempotency, retry safety, observability, and migration requirements for trust-sensitive payout-history behavior
- implementation-contract guardrails for APIs, storage, events, read models, caches, exports, dashboards, and operator tooling that consume or expose ledger truth

## Out of Scope

This specification does not fully define:

- the accounting methodology for distributable profit, reserves, or treasury books
- the detailed snapshot and eligibility algorithms or proof construction formats
- the Base payout contract ABI, claim-proof mechanics, or low-level execution receipts
- the full public registry schema for contract and wallet designation
- the full transparency report composition model, cadence, or publishing templates
- low-level database topology, queue internals, infrastructure layout, or rendering implementation details
- user account, session, wallet-link, or workspace authorization semantics beyond dependency constraints

Those remain governed by adjacent documents, especially `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`, `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`, `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md`, `TREASURY_CONTROL_POLICY_SPEC.md`, `TRANSPARENCY_MODEL_SPEC.md`, `TRANSPARENCY_REPORTING_SPEC.md`, `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`, and the account/session foundation specifications.

## Design Goals

The design goals of the FUZE payout ledger are to:

1. preserve a durable, intelligible record of each payout cycle as a formal trust-bearing object
2. make funded payout history easier to interpret than raw contract activity alone
3. maintain explicit linkage between policy-finalized cycle meaning, eligibility basis, treasury funding lineage, execution references, and public-trust artifacts
4. distinguish cycle-ledger truth from execution truth, accounting truth, audit truth, and reporting truth
5. preserve historical integrity through visible correction and supersession lineage rather than silent rewrite
6. support holder trust, operator review, finance reconciliation, and long-term institutional memory
7. enable public-safe visibility without exposing unsafe internal detail or collapsing into a private operations dump
8. give downstream API and storage layers enough precision that they cannot legally reinterpret cycle history inconsistently

## Non-Goals

This specification is not intended to:

- replace the Base payout contract as the canonical source of chain-execution facts
- replace treasury or accounting systems as the source of financial-book truth
- expose every raw individual claimant detail in the main cycle ledger
- treat public reporting artifacts as canonical ledger owners
- allow dashboards, exports, or admin consoles to become the origin of payout-cycle meaning
- make payout history mutable by convenience, aesthetics, or narrative preference
- collapse payout-ledger state into one generic “status” field that erases important truth separation

## Core Principles

### Principle 1: The Ledger Is the Structured Cycle Record
Every profit-participation cycle MUST have a durable, uniquely identifiable payout-ledger record that explains the cycle as a formal ecosystem event rather than a loose collection of contract transactions or announcements.

### Principle 2: The Ledger Links Domains but Does Not Replace Them
The payout ledger links profit-participation semantics, eligibility lineage, treasury funding lineage, Base execution references, and reporting references. It MUST NOT absorb or redefine the ownership of those adjacent domains.

### Principle 3: Ledger Truth Is Distinct from Execution Truth
Base execution proves funding and claim execution behavior on-chain. The payout ledger explains which cycle that behavior belongs to, under what policy and eligibility basis, and with what correction or publication lineage.

### Principle 4: Historical Integrity Beats Convenience
Corrections, cancellations, and supersessions MUST preserve visible lineage. Silent overwrite of trust-sensitive cycle history is forbidden.

### Principle 5: Public Transparency Must Be Structured and Bounded
Public-facing payout-ledger visibility SHOULD expose enough cycle structure to sustain trust, but MUST NOT leak unsafe operational detail, private claimant data beyond approved exposure, or internal incident-only artifacts.

### Principle 6: Reconciliation Is Mandatory
If ledger truth cannot be reconciled to approved upstream and downstream references, the inconsistency MUST be surfaced as a discrepancy state rather than ignored or narrated away.

## Canonical Definitions

### Payout Ledger
The canonical off-chain structured record layer that preserves payout-cycle identity, lifecycle, linkage, visibility posture, correction lineage, and reconciliation references.

### Payout Cycle Ledger Record
The canonical ledger object representing one payout cycle, including its identifiers, lifecycle state, structural references, and correction lineage.

### Cycle Identifier
The durable unique identifier for a payout cycle record. It remains stable across the cycle lifecycle and survives correction or supersession linkage.

### Ledger State
The lifecycle state of the ledger record itself, distinct from execution or claim-state facts. Examples include draft, published, active, superseded, cancelled, closed, and discrepancy-under-review.

### Claim-State Summary
The ledger-visible representation of claim-window posture or aggregate claim progress for a cycle. It is derived from execution truth and remains distinct from raw per-claim execution receipts.

### Funding Reference
The canonical reference linking a ledger record to the approved treasury-controlled funding lineage and the corresponding execution-layer funding event.

### Eligibility Basis Reference
The stable reference to the approved cycle-specific eligibility basis, including snapshot and policy lineage as required by the snapshot and eligibility domain.

### Reporting Reference
A linkage from the ledger record to transparency-reporting or other approved public-safe explanatory artifacts.

### Registry Reference
A linkage from the ledger record to a public contract or wallet registry artifact where such linkage improves public intelligibility.

### Correction
A bounded mutation that changes the interpreted ledger state or references of a cycle while preserving visible historical lineage.

### Supersession
A formal lineage relationship in which one ledger record or record version becomes the successor to another because correction cannot be safely represented as an in-place update of current meaning.

### Visibility Class
The approved audience class for a ledger view or field family, such as public, operator, finance-restricted, or audit-restricted.

## Truth Class Taxonomy

The payout ledger domain MUST distinguish the following truth classes:

1. **Ledger truth** — canonical off-chain cycle-ledger records, ledger-state transitions, visibility posture, correction lineage, and structured references
2. **Profit-participation semantic truth** — the business meaning of a payout cycle and the semantic rules governing its lifecycle
3. **Eligibility truth** — the approved cycle-specific eligibility basis and entitlement-construction lineage owned by the snapshot and eligibility domain
4. **Treasury / accounting truth** — finalized distributable-profit posture, funding authorization lineage, reserve-sensitive context, and finance-book records
5. **Execution truth** — Base contract funding state, claim-open / claim-close posture, and claim execution outcomes
6. **Transparency / reporting truth** — report-family publication truth and public explanatory artifacts owned by reporting domains
7. **Registry truth** — public designation and publication truth for official contracts and wallets owned by the registry domain
8. **Audit truth** — immutable internal decision, mutation, investigation, and operator-action lineage
9. **Projection / read-model truth** — dashboards, public pages, exports, APIs, indexes, and caches derived from the canonical ledger
10. **Presentation truth** — labels, summaries, human-readable status copy, charts, and UI framing

These truth classes MUST NOT be collapsed into one undifferentiated payout-history object.

## Architectural Position in the Spec Hierarchy

This document sits:

- below top-level constitutional platform, ownership, and data-governance specifications
- adjacent to profit participation, snapshot and eligibility, Base payout execution, treasury control, transparency, registry, and chain-architecture specifications
- above downstream payout-ledger APIs, publication surfaces, reconciliation jobs, runbooks, and storage contracts

This document governs payout-ledger semantics. It does not override higher-order ownership or on-chain/off-chain boundary specifications, and it does not absorb the narrower responsibilities of execution, accounting, reporting, or registry domains.

## System Boundaries

The payout-ledger domain governs:

- cycle-ledger record identity and lifecycle meaning
- structured linkage among cycle references
- ledger publication posture and visibility classes
- correction, cancellation, closure, and supersession semantics for ledger records
- reconciliation posture and discrepancy-handling semantics for ledger-visible cycle state
- the semantic contract of what downstream systems may consume as payout-ledger truth

The payout-ledger domain does **not** own:

- distributable-profit accounting truth
- treasury authorization policy or reserve management truth
- Ethereum token balances or eligibility derivation truth
- Base contract execution or per-claim receipt truth
- transparency report publication truth
- public registry publication truth
- generic audit-log semantics
- workspace authorization, wallet-link, session, or product entitlement truth

## Adjacent Boundaries

### Relationship to Profit Participation
Profit participation owns the semantic meaning of a payout cycle and its cross-domain lifecycle requirements. The payout ledger records that cycle in a durable structured form. It MUST NOT redefine cycle semantics independently.

### Relationship to Snapshot and Eligibility
The snapshot and eligibility pipeline owns the approved basis for who is eligible and why. The payout ledger references that basis and makes it intelligible; it MUST NOT become the owner of eligibility logic.

### Relationship to Base Payout Execution
The Base payout execution layer owns contract funding state, claim-open posture, and claim execution facts. The ledger reflects and links those facts but MUST NOT be treated as the source of raw execution truth.

### Relationship to Treasury and Accounting
Treasury and accounting own distributable-profit finalization and funding authorization truth. The payout ledger references those approvals and outcomes but does not compute, approve, or reinterpret them.

### Relationship to Transparency Reporting
Transparency reporting owns recurring public explanation and publication truth. The payout ledger provides the structured cycle record that reporting may explain. Reports MUST NOT become the canonical owner of payout-ledger facts.

### Relationship to Public Registry
The registry owns public designation of official contracts and wallets. The payout ledger may link to registry entries where it improves legibility, but registry publication truth remains separately governed.

## Conflict Resolution Rules

When materials, systems, or operators disagree, the following rules apply:

1. the active refined registry and higher constitutional materials win over narrower documents
2. `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md` wins on top-level ownership and mutation-owner interpretation
3. `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md` wins on canonical-versus-derived status and entity-family ownership
4. `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md` and `CHAIN_ARCHITECTURE_SPEC.md` win on chain-role boundaries and on-chain/off-chain responsibility posture
5. `PROFIT_PARTICIPATION_SYSTEM_SPEC.md` wins on the semantic business meaning of payout cycles
6. `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md` wins on eligibility-basis meaning and dataset lineage semantics
7. `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md` wins on execution mechanics, contract-state meaning, and claim-state facts
8. `TREASURY_CONTROL_POLICY_SPEC.md` wins on treasury authorization and funding-control interpretation
9. this document wins on structured ledger-record meaning, visibility classes, correction lineage, reconciliation posture, and ledger-state semantics
10. if ambiguity remains, the more conservative architecture-consistent interpretation MUST be chosen, and no downstream layer may silently optimize away historical intelligibility or traceability

## Default Decision Rules

Where ambiguity is likely, the following defaults apply:

1. **No unique cycle identifier means no canonical ledger record.**
2. **No approved eligibility basis reference means the cycle MUST NOT be published as canonically open or claim-ready.**
3. **No treasury authorization reference means a funded amount MUST NOT be treated as canonical cycle funding in the ledger.**
4. **If execution truth and ledger truth disagree, execution truth controls contract and claim-state facts, but ledger correction MUST follow explicitly.**
5. **If reporting copy and ledger references disagree, ledger truth controls structured cycle history, and reporting correction MUST follow explicitly.**
6. **If a correction would erase trust-sensitive historical meaning, supersession or visible version lineage MUST be used instead of silent in-place rewrite.**
7. **If public exposure would reveal unsafe internal detail, public output MUST be narrowed while preserving full internal ledger and audit lineage.**
8. **If a cycle cannot be reconciled across funding, eligibility, and execution references, the safe default is discrepancy-under-review rather than inferred success.**
9. **If claim-state aggregation is delayed or partial, the ledger MUST label it as derived or delayed rather than implying finality.**
10. **If operator convenience conflicts with historical integrity, historical integrity wins.**

## Roles / Actors / Entities

### Roles / Actors
- **Payout Ledger Domain Owner:** owns ledger semantics, lifecycle meaning, correction posture, reconciliation posture, and downstream guardrails
- **Profit Participation Domain Owner:** owns cycle semantic meaning and upstream cycle-governance interpretation
- **Snapshot and Eligibility Domain Owner:** owns eligibility-basis truth consumed by the ledger
- **Base Payout Execution Domain Owner:** owns execution and claim-state truth consumed by the ledger
- **Treasury / Accounting Authority:** owns approved funding lineage and finance-sensitive references
- **Transparency Reporting Domain Owner:** owns recurring explanatory publication built from ledger-linked references
- **Public Registry Domain Owner:** owns registry publication truth for official payout-related contracts and wallets
- **Security / Audit / Operations Teams:** enforce controls, investigate discrepancies, and support correction or incident response
- **Holder / Public Reader / Operator Reader:** consumes ledger views according to approved visibility class

### Core Entities
- payout cycle ledger record
- cycle identifier
- cycle version identifier
- ledger state
- claim-state summary
- policy version reference
- snapshot reference
- eligibility basis reference
- entitlement-input reference where applicable
- treasury funding reference
- execution funding reference
- payout contract reference
- reporting reference
- registry reference where applicable
- supersession reference
- correction reason code
- audit trace identifier
- visibility class
- public-safe status label

## Ownership Model

The canonical ownership model is:

- **Payout Ledger Domain** owns structured cycle-ledger truth, ledger-state meaning, visibility classes, correction lineage, reconciliation posture, and ledger publication semantics
- **Profit Participation Domain** owns the semantic business meaning of payout cycles
- **Snapshot and Eligibility Domain** owns eligibility-basis truth
- **Base Payout Execution Domain** owns contract funding and claim-execution truth
- **Treasury / Accounting Domain** owns approved funding and book truth
- **Transparency Reporting Domain** owns report publication truth
- **Public Contract and Wallet Registry Domain** owns public registry publication truth
- **Audit Domain** owns immutable audit-event lineage

No public page, spreadsheet, export, admin console, or local service MAY redefine these ownership boundaries.

## Authority / Decision Model

### Canonical Authority Rules
1. routine operators MUST NOT create, delete, or reinterpret canonical payout cycles outside approved domain workflows
2. ledger publication authority MUST remain distinct from treasury funding authority and from execution authority
3. operators MAY register corrections or supersessions only through explicit, reason-coded, audited paths
4. finance or treasury staff MAY contribute approved funding references but MUST NOT unilaterally rewrite ledger-state history
5. reporting teams MAY summarize ledger data but MUST NOT mutate canonical ledger truth through reporting tools
6. public-read surfaces MUST remain subordinate to canonical ledger records and approved visibility policies

### Operator/Admin Override Posture
Override paths MAY exist only where they are:

- explicitly named
- policy-constrained
- narrowly scoped
- reason-coded
- fully audited
- reviewable after the fact
- unable to silently erase prior cycle meaning

Permitted override examples MAY include cancellation before public publication, discrepancy marking, controlled correction registration, and supersession linkage. Silent deletion or silent rewrite of a published canonical cycle record is forbidden.

## State Model

At minimum, the payout ledger MUST support explicit lifecycle state.

Suggested canonical ledger states include:

- `draft`
- `prepared_internal`
- `published_internal`
- `public_published`
- `funded`
- `open_for_claims`
- `claims_active`
- `closed`
- `finalized`
- `discrepancy_under_review`
- `corrected`
- `superseded`
- `cancelled_pre_funding`
- `cancelled_pre_publication`

Downstream implementations MAY refine internal sub-states, but they MUST preserve explicit separation between ledger publication posture, funding posture, active-claim posture, discrepancy posture, closure posture, and supersession posture.

## Lifecycle / Workflow Model

### 1. Cycle Record Initialization
A unique cycle ledger record is created with preliminary structural references and internal visibility only.

### 2. Structural Reference Attachment
Policy, eligibility, funding-planning, and execution-environment references are attached in stable structured form.

### 3. Publication Readiness Check
The ledger verifies that required upstream references exist and that visibility policy allows internal or public publication.

### 4. Funding Registration
Approved treasury and execution funding references are recorded and linked to the cycle ledger record.

### 5. Claim-State Activation
When the execution layer opens claims, the ledger reflects the approved claim-state posture and exposes any public-safe claim summary fields.

### 6. Active Reconciliation
During the claim period, the ledger may update derived aggregate posture, discrepancy markers, reporting references, and public-safe explanatory state.

### 7. Closure Recording
When the claim period or cycle concludes, the ledger records closure posture and any required residual-handling references.

### 8. Finalization
The ledger marks the cycle finalized when no open discrepancy or correction remains pending.

### 9. Correction or Supersession
If a material issue is found, the ledger records a reason-coded correction or creates a superseding lineage relationship. Silent rewrite is forbidden.

### 10. Historical Preservation
The ledger preserves visible historical linkage for all material changes so past and current public interpretation remain understandable.

## Invariants

The following invariants are mandatory:

1. every canonical payout cycle has a stable unique cycle identifier
2. ledger truth is distinct from execution truth, accounting truth, reporting truth, registry truth, and audit truth
3. every published funded cycle is linkable to approved funding lineage
4. every published open cycle is linkable to approved eligibility lineage
5. claim-state summaries are derived from execution truth and never invented ad hoc
6. silent rewrite of published trust-sensitive cycle history is forbidden
7. visibility class is explicit for ledger data exposed beyond the canonical store
8. public-safe ledger outputs never expose unsafe internal-only operational detail without explicit approval
9. every material correction or supersession is reason-coded and trace-linked
10. a cycle in discrepancy state is never represented as unambiguously successful without explicit remediation

## Functional Rules

1. every canonical ledger record MUST preserve unique cycle identity and version lineage
2. every ledger record MUST preserve policy, eligibility, funding, execution, and reporting references as applicable
3. ledger-state changes MUST be distinguishable from execution-state changes and from reporting publication changes
4. public-facing cycle history MUST preserve supersession and correction lineage where omission would mislead
5. derived aggregate claim fields MUST indicate whether they are final, estimated, delayed, or partial
6. cancellation, correction, supersession, and closure MUST use explicit reason codes and audit traces
7. a funded amount MUST NOT be recorded as canonical without approved treasury lineage and an execution-reference linkage strategy
8. read surfaces MUST preserve the distinction between cycle identity, ledger state, claim state, and public explanatory label
9. exports and APIs MUST expose stable references sufficient for reconciliation and historical lookup
10. holder-specific detailed claim history, if exposed elsewhere, MUST remain subordinate to the cycle-ledger record and MUST NOT redefine it

## Permission / Access Considerations

Although the payout ledger is not a workspace-owned product workflow, access controls still apply:

- privileged mutation surfaces MUST require explicit domain-appropriate authorization
- correction, supersession, cancellation, and public-visibility changes MUST require stronger controls than routine reads
- finance-sensitive, audit-sensitive, and incident-sensitive fields MUST be visibility-bounded
- public or holder-safe read surfaces MAY expose bounded cycle-ledger fields according to explicit publication policy
- identity, session, and wallet-aware context MAY authenticate holder-facing views, but they MUST NOT redefine payout-ledger semantics
- workspace scope MUST NOT be misused as a substitute for cycle-level payout truth or public-ledger eligibility

## Entitlement Considerations

The payout ledger does not compute entitlement. It records the structural linkage between:

- the approved eligibility basis
- the entitlement-input reference where applicable
- the funded execution environment
- the resulting claim-state posture

Downstream teams MUST NOT conflate:

- payout-ledger cycle history with user-specific entitlement truth
- claim-state summary with per-holder receipt truth
- public-safe cycle visibility with private claimant-level detail
- ledger publication state with funding authority or entitlement authority

## API / Contract Implications

Downstream APIs and contracts MUST preserve the following:

1. separate command surfaces for cycle-record creation, funding registration, publication-state changes, claim-state updates, closure, correction, and supersession
2. strong idempotency for trust-sensitive mutations such as record creation, funding registration, publication, correction registration, and supersession linkage
3. explicit correlation IDs, cycle references, policy references, eligibility references, funding references, execution references, and actor trace data for material mutations
4. narrow, explicit mutation contracts rather than generic “update payout record” endpoints
5. a clear distinction between public-read, holder-read, internal-service, and admin/control-plane surfaces
6. no API surface may permit silent mutation of canonical published history without correction or supersession lineage
7. contract or chain-facing components MUST expose enough stable references that ledger systems can reconcile cycle identity, funding state, and claim-state posture without becoming semantic owners of execution truth

## Event / Async Implications

The payout ledger is a cross-domain trust record and SHOULD use event-first coordination for side effects.

Required event families MAY include:

- cycle ledger record created
- structural references attached
- funding registered
- claim-state summary updated
- cycle published internally
- cycle published publicly
- cycle closed
- cycle finalized
- discrepancy detected
- correction registered
- cycle superseded
- reporting reference attached or superseded
- registry reference attached or superseded

Events MUST be idempotent or replay-safe, include stable identifiers and correlation references, and preserve source-domain ownership boundaries.

## Data Model / Storage Implications

The payout-ledger domain SHOULD maintain durable canonical records for:

- cycle identity and version lineage
- ledger-state history
- visibility class and public-safe labels
- policy and eligibility references
- treasury and execution funding references
- claim-state summary references and aggregate posture
- reporting and registry references
- discrepancy, correction, cancellation, and supersession records
- audit trace identifiers and operator reason codes

It MUST NOT rely solely on dashboards, spreadsheets, exports, or chain explorers as the canonical ledger store.

## Read Model / Projection / Reporting Rules

Derived read models MAY exist for:

- public cycle-history pages
- holder-safe payout-status pages
- treasury and finance reconciliation views
- admin cycle-control consoles
- transparency report inputs
- public registry-linked payout views
- exports and API caches

These projections:

- MUST remain subordinate to canonical payout-ledger records
- MUST indicate when data is derived, delayed, or partial
- MUST preserve supersession and correction lineage where omission would mislead
- MUST NOT merge funding state, ledger state, claim state, and explanatory status into one ambiguous field
- MUST NOT invent cycle existence or meaning from contract observations alone
- MUST NOT allow cached or exported views to become the mutation owner of ledger truth

## Security / Risk / Abuse Controls

The payout-ledger domain MUST include controls for:

- unauthorized mutation of cycle records or visibility state
- replay or duplicate processing of funding, publication, correction, or closure actions
- mis-linkage between payout cycles and execution or treasury references
- silent post-publication rewriting of trust-sensitive history
- unsafe publication of restricted operational or claimant-sensitive detail
- spoofed or unofficial payout references being represented as canonical
- compromised operator or service credentials affecting payout-history truth
- discrepancy suppression or misleading presentation of unresolved inconsistencies

Security posture MUST remain stronger for payout-ledger mutation paths than for ordinary product reporting surfaces.

## Boundary Violation Detection / Non-Canonical Patterns

The following patterns are explicitly non-canonical and forbidden unless a newer approved higher-order document says otherwise:

1. treating Base contract transactions alone as the canonical payout ledger
2. treating treasury books alone as the public or holder-facing cycle history
3. allowing transparency reports or blog-style updates to mutate canonical cycle truth
4. exposing one generic “status” field that hides whether the value refers to ledger state, funding state, or claim state
5. silently replacing a published cycle record with a corrected one without visible lineage
6. letting spreadsheets, dashboards, or exports own correction logic
7. publishing public-safe claim aggregates without indicating derivation, delay, or partiality where material
8. permitting routine operators to delete or rewrite published cycle history for convenience
9. conflating user-specific claim history with the canonical cycle-ledger record
10. inferring eligibility or funding certainty from narrative communications alone

Implementations SHOULD detect and alert on attempted boundary violations.

## Audit / Traceability Requirements

The payout-ledger domain MUST preserve auditable lineage for:

- cycle record creation
- material reference attachment or change
- funding registration
- publication-state changes
- correction, cancellation, and supersession actions
- claim-state summary updates where material
- visibility-class changes
- discrepancy creation, remediation, and closure

Material actions MUST include actor identity, reason code where applicable, correlation ID, timestamp, and affected record identifiers.

## Failure Handling / Edge Cases

The payout-ledger domain MUST explicitly handle at least the following:

- funding observed on-chain before treasury-reference attachment
- treasury approval present but execution funding not yet observed
- claim window opened while public ledger publication is delayed
- reporting artifact published with a stale or incorrect ledger linkage
- registry entry changes after the cycle has already been published
- aggregate claim progress temporarily unavailable or delayed
- pre-publication cycle cancellation
- post-publication discrepancy requiring correction or supersession
- chain reorg or execution-observation anomaly affecting derived claim-state summaries
- operator error attempting to mutate a finalized or superseded record

In each case, the implementation MUST preserve canonical history, mark uncertainty explicitly, and avoid silent downgrade of traceability.

## Operational Considerations

Operational systems SHOULD support:

- reconciliation jobs across treasury references, execution references, and ledger records
- discrepancy queues with bounded severity and explicit remediation states
- publication controls for public-safe versus operator-only views
- safe replay and retry of ledger ingestion or update events
- health checks for stale aggregates, missing references, or unresolved discrepancies
- operator tooling that shows lineage before allowing correction or supersession actions

Operational convenience MUST NOT override canonical history preservation.

## Migration / Compatibility / Supersession Considerations

When migrating legacy payout-history models or publication surfaces:

- legacy records MUST be mapped into explicit cycle identities where possible
- incompatible legacy “status” fields MUST be decomposed into explicit ledger, funding, and claim-state concepts
- silent historical backfill that erases uncertainty is forbidden
- migrated records SHOULD preserve provenance notes and lineage references
- API and read-model changes MUST remain backward-compatible where feasible or provide explicit versioned transition paths
- superseded records MUST remain discoverable enough to preserve historical intelligibility

## Implementation-Contract Guardrails

Downstream implementation contracts MUST preserve the following:

1. cycle identity is stable and never reused
2. ledger-state transitions are explicit and auditable
3. public-safe labels are derived from canonical structured state rather than replacing it
4. correction and supersession are first-class flows, not exceptional hacks
5. claim-state summaries remain demonstrably linked to execution truth
6. funding references remain demonstrably linked to treasury and execution truth
7. read models and exports remain subordinate to canonical ledger records
8. degraded-mode behavior still preserves explicit uncertainty and lineage
9. operator tools cannot optimize away reason codes or audit traces for material mutations
10. downstream teams MUST NOT reinterpret this domain as a generic analytics table or content-management surface

## Downstream Execution Staging

The payout-ledger domain SHOULD be implemented downstream in staged form:

1. canonical record model and identity rules
2. structural reference linkage rules
3. funding and execution reconciliation ingestion
4. public-safe and operator-safe read models
5. correction and supersession tooling
6. discrepancy detection and remediation workflows
7. reporting and registry linkage automation
8. observability, migration hardening, and policy-driven publication controls

Staging MAY vary by implementation, but canonical truth separation and historical integrity MUST be preserved at every stage.

## Required Downstream Specs / Contract Layers

This specification requires or strongly implies downstream alignment in:

- `PAYOUT_LEDGER_API_SPEC.md`
- payout-ledger storage and schema contracts
- payout-ledger reconciliation job contracts
- public payout-status API and cache contracts
- admin/control-plane mutation contracts
- discrepancy and supersession runbooks
- reporting integration and registry-linkage contracts
- audit and activity logging contracts

## Canonical Examples / Anti-Examples

### Canonical Example: Funded and Open Cycle
A cycle ledger record contains a stable cycle ID, approved eligibility-basis reference, treasury funding reference, execution funding reference, payout contract reference, open-for-claims claim-state summary, visibility class, and linked reporting reference. A public page shows a bounded summary derived from that record.

### Canonical Example: Post-Publication Correction
A cycle’s reporting reference was attached incorrectly. The ledger preserves the original published version, records a correction reason code, links the corrected reporting reference, and exposes visible correction lineage to operator and public-safe views as policy requires.

### Canonical Example: Superseded Cycle
A material issue in funding linkage cannot be corrected safely in place. The ledger marks the original cycle record superseded, creates a successor record with a new version linkage, and preserves clear historical lineage.

### Anti-Example: Explorer-Only History
A team treats contract explorer transactions as the whole payout history and omits eligibility, funding-authorization, and reporting references. This is non-canonical.

### Anti-Example: Silent Dashboard Edit
An operator edits a spreadsheet or admin UI field to “fix” a public cycle status without correction lineage. This is forbidden.

### Anti-Example: Ambiguous Status Field
An API exposes `status=active` without clarifying whether that means funded, published, or claim-open. This is non-canonical because it collapses distinct truths.

## Dependencies / Cross-Spec Links

This document depends materially on:

- `REFINED_SYSTEM_SPEC_INDEX.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
- `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md`
- `TREASURY_CONTROL_POLICY_SPEC.md`
- `TRANSPARENCY_MODEL_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`
- `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
- `CHAIN_ARCHITECTURE_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `API_ARCHITECTURE_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `IDEMPOTENCY_AND_VERSIONING_SPEC.md`
- `MIGRATION_AND_BACKWARD_COMPATIBILITY_SPEC.md`
- `AUDIT_LOG_AND_ACTIVITY_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `MONITORING_ALERTING_AND_INCIDENT_RESPONSE_SPEC.md`

## Explicitly Deferred Items

The following are intentionally deferred to adjacent or downstream specifications:

- exact database schema and partitioning strategy
- exact public payload shapes for all payout-ledger APIs
- exact aggregate claim metrics exposed publicly by policy class
- exact UI rendering and information architecture for public pages and admin tools
- exact residual-handling reporting fields and workflow details
- exact registry-link export formats and caching behavior
- exact queue, worker, and retry topology for ledger ingestion pipelines

These deferrals do not weaken the canonical rules defined here.

## Final Normative Summary

The FUZE payout ledger is the canonical structured trust record for payout cycles. It exists to preserve a durable, uniquely identifiable, correction-safe, reconcilable account of each cycle by linking policy basis, eligibility basis, treasury funding lineage, execution references, claim-state posture, and public-trust publication references without collapsing those domains into one ambiguous surface. Downstream implementations MUST preserve explicit truth separation, stable cycle identity, visible correction lineage, bounded public transparency, strong auditability, and explicit discrepancy handling.

## Quality Gate Checklist

- [x] Canonical owner is explicit for every material truth family relevant to this domain
- [x] Mutation boundaries are explicit
- [x] Adjacent boundaries are explicit
- [x] Truth classes are explicit
- [x] Conflict-resolution rules are explicit
- [x] Default decision rules are explicit where ambiguity is likely
- [x] Non-canonical patterns are called out clearly
- [x] Operator/admin override paths are bounded and audited
- [x] Read-model, cache, reporting, and projection rules are explicit
- [x] On-chain vs off-chain responsibilities are explicit where relevant
- [x] Failure and degraded-mode behaviors are explicit
- [x] Downstream implementation guardrails are explicit
- [x] Dependencies and downstream impacts are explicit
- [x] Non-goals and deferred items are explicit
- [x] The document is strong enough that backend, API, data, runtime, reporting, and public-trust teams can implement without inventing contradictory semantics
