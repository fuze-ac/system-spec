# FUZE Data Model and Entity Ownership Specification

## Document Metadata
- **Document Name:** `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- **Document Type:** Canonical refined system specification
- **Status:** Active refined system spec
- **Version:** 1.0
- **Effective Date:** 2026-04-21
- **Last Updated:** 2026-04-21
- **Reviewed On:** 2026-04-21
- **Document Owner:** FUZE Platform Architecture (canonical owner); named individual owner not explicitly specified in the retrieved governing materials
- **Approval Authority:** Not explicitly specified in the retrieved governing materials; constitutional approval authority remains governed by `REFINED_SYSTEM_SPEC_INDEX.md` and the active FUZE approval workflow
- **Review Cadence:** Not explicitly specified in the retrieved governing materials; SHOULD be reviewed whenever entity families, truth boundaries, lifecycle ownership, economic rails, chain commitments, or cross-domain persistence rules materially change
- **Governing Layer:** Platform constitution / data model / entity ownership
- **Parent Registry:** `REFINED_SYSTEM_SPEC_INDEX.md`
- **Primary Audience:** Platform architecture, backend engineering, product engineering, data engineering, API design, contracts engineering, security, audit, operations, governance, finance systems, reporting, implementation-contract authors
- **Primary Purpose:** Define the canonical entity-ownership architecture and data-model discipline for FUZE, including entity-family ownership, truth-location rules, lifecycle and mutation authority, cross-domain reference rules, and the persistence, API, event, audit, reconciliation, and migration guardrails that downstream specifications and implementations MUST preserve
- **Primary Upstream References:**
  - `REFINED_SYSTEM_SPEC_INDEX.md`
  - `DOCS_SPEC_INDEX.md`
  - `SYSTEM_SPEC_INDEX.md`
  - `API_SPEC_INDEX.md`
  - `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
  - `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
  - `PLATFORM_ARCHITECTURE_SPEC.md`
  - `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
  - `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
- **Primary Downstream Dependents:**
  - `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
  - `IDENTITY_AND_ACCOUNT_SPEC.md`
  - `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
  - `WALLET_AWARE_USER_SPEC.md`
  - `WORKSPACE_AND_ORGANIZATION_SPEC.md`
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
  - `PLATFORM_CREDITS_SPEC.md`
  - `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
  - `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
  - `PAYMENT_RAILS_INTEGRATION_SPEC.md`
  - `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
  - `PAYOUT_LEDGER_SPEC.md`
  - `API_ARCHITECTURE_SPEC.md`
  - `PUBLIC_API_SPEC.md`
  - `INTERNAL_SERVICE_API_SPEC.md`
  - `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
  - `AUDIT_LOG_AND_ACTIVITY_SPEC.md`
  - product integration specifications
- **Supersedes:** Earlier or weaker FUZE entity-ownership interpretations that permit soft co-ownership, storage-driven truth drift, direct cross-domain mutation, reporting-owned business truth, or conflation of platform, product, execution, provider, and on-chain records
- **Superseded By:** None currently defined
- **Related Decision Records:** Not explicitly linked in the retrieved governing materials
- **Canonical Status Note:** This document is the canonical entity and persistence interpretation layer beneath the top-level boundary, overview, architecture, and domain-ownership specifications. It is normative unless an explicitly higher constitutional source overrides it.
- **Implementation Status:** Normative architecture and implementation-contract source; downstream schemas, APIs, events, workflows, ledgers, reports, indexes, runbooks, and migrations MUST align
- **Approval Status:** Draft refined canonical specification pending explicit approval workflow
- **Change Summary:** Consolidated the active refined interpretation for FUZE entity ownership and data-model discipline; normalized metadata; clarified entity families, truth classes, lifecycle ownership, policy-derived canonical outputs, projection rules, correction lineage, reconciliation posture, and downstream implementation guardrails

## Title
FUZE Data Model and Entity Ownership Specification

## Purpose
This specification defines the canonical data model and entity ownership architecture of the FUZE ecosystem.

Its purpose is to make explicit:
- which entity families are canonical in FUZE
- which domain canonically owns each entity family
- where authoritative truth resides for those entities
- how lifecycle ownership and mutation authority are assigned
- how platform, product, on-chain, policy-derived, execution, and reporting entities differ
- how cross-domain references MUST preserve ownership clarity
- how APIs, events, workflows, ledgers, caches, indexes, reports, and exports MUST behave relative to canonical entity owners
- how correction, supersession, reconciliation, and migration MUST preserve trust and traceability

This document is intentionally foundational and implementation-usable. It is the entity-layer refinement of the FUZE boundary, overview, architecture, and major-domain ownership model. It exists to prevent duplicate truth, accidental co-ownership, entity drift, storage-driven reinterpretation, and hidden mutation through derived systems. fileciteturn14file0 fileciteturn14file1 fileciteturn14file2

## Scope
This specification governs:
- the canonical entity-ownership philosophy of FUZE
- the distinction between canonical entities, policy-derived canonical entities, execution entities, and derived/reporting entities
- the source-of-truth rules for major FUZE entity families
- lifecycle ownership and mutation authority for canonical entities
- cross-domain reference rules and identifier discipline
- the treatment of projections, caches, ledgers, indexes, exports, and reporting artifacts
- the entity-level implications of platform, product, on-chain, reporting, control-plane, and external boundaries
- correction, supersession, reconciliation, and migration discipline at the entity layer
- the implications of entity ownership for APIs, events, workers, audit, security, and operational remediation

This specification does not define:
- every database table or full schema field list
- every contract storage layout or ABI
- every event payload or queue topic
- exact physical database topology
- exact service decomposition or repository module boundaries
- exact archival or retention schedules for every entity type
- exact foreign-key strategy across every service boundary

Those belong in downstream specifications, provided those specifications remain consistent with this document. fileciteturn14file0

## Out of Scope
This specification is explicitly out of scope for:
- low-level persistence engine choices
- narrow product-UI shapes
- exact graph, search, and indexing implementation details
- exact queue, broker, or worker runtime technology choices
- exact contract ABI detail
- endpoint-by-endpoint API schema detail
- environment-specific deployment decisions
- human staffing or org-chart structure

## Design Goals
The design goals of the FUZE data model and entity ownership architecture are to:
1. ensure every materially important fact has one canonical owner
2. prevent the same business fact from being silently authored in multiple places
3. distinguish canonical write models from derived read, cache, reporting, search, analytics, and publication models
4. preserve platform-first shared reuse without flattening product-specific truth
5. make lifecycle, support, reconciliation, audit, and governance review easier through explicit entity lineage
6. preserve strong separations among off-chain truth, on-chain truth, external truth, execution truth, and policy-derived truth
7. support multi-product expansion without repeated re-litigation of entity ownership
8. keep APIs, workflows, workers, and reporting aligned with entity ownership rather than convenience duplication
9. support correction and supersession without destroying trust-sensitive history
10. make future migrations and domain splits possible without loss of ownership clarity

## Non-Goals
This specification is not intended to:
- force all entities into one monolithic schema
- collapse product-domain truth into generic platform abstractions when that weakens product value
- treat every derived model as ephemeral if the platform formally depends on it as a canonical pipeline output
- treat blockchain data as the owner of all platform business meaning
- allow convenience denormalization to replace ownership clarity
- define exact physical storage implementation for every entity family
- imply that dashboards, reports, AI summaries, caches, or exports own the facts they display

## Core Principles
### 1. One Material Fact, One Canonical Owner
Every materially important fact in the FUZE ecosystem MUST have one canonical owner. fileciteturn14file0 fileciteturn14file2

### 2. Storage Convenience Never Overrides Ownership
An entity MAY be copied, cached, indexed, summarized, joined, exported, or published in many places, but it MUST NOT be silently re-authored in many places. fileciteturn14file0

### 3. Entity Ownership Is Lifecycle Ownership
Canonical ownership includes entity existence, canonical fields, valid state transitions, correction pathways, closure rules, archival posture, and reconciliation responsibilities. fileciteturn14file0

### 4. Derived Does Not Necessarily Mean Disposable
A derived entity MAY still be canonical for its own bounded domain if it is the authoritative output of a formal pipeline governed by FUZE. fileciteturn14file0

### 5. Append-Oriented Correction Beats Silent Rewrite
Trust-sensitive domains SHOULD prefer adjustment, reversal, correction lineage, or superseding records over destructive overwrites. fileciteturn14file0

### 6. Cross-Domain References Must Preserve Ownership
Products and adjacent domains SHOULD reference canonical entities rather than copy and re-own them. fileciteturn14file0

### 7. On-Chain and Off-Chain Entity Models Must Stay Distinct
Contract-native entities and off-chain platform entities MAY be related, but they MUST NOT be collapsed into one ambiguous entity model. fileciteturn14file0 fileciteturn14file2

### 8. Reporting Artifacts Are Usually Downstream
Reports, dashboards, indexes, exports, analytics facts, AI summaries, and public read models are usually derived artifacts, not source truth. fileciteturn14file0 fileciteturn14file1

## Canonical Definitions
### Canonical Entity
The primary entity record or record family authoritative for a fact, including canonical fields, valid transitions, mutation acceptance, and lifecycle truth. fileciteturn14file0

### Derived Entity
A computed, copied, summarized, indexed, cached, exported, or publication-oriented representation created from canonical truth. fileciteturn14file0

### Projection / Read Model
A query-optimized or interface-optimized representation built for product UX, reporting, search, dashboards, analytics, or operational views. It does not own the underlying fact. fileciteturn14file0

### Ledger Entity
An append-oriented entity that records mutation lineage or economic history. A ledger MAY be canonical for historical mutation lineage without being the only canonical representation of current state. fileciteturn14file0

### Reference Entity
A pointer or relationship artifact used to connect domains without transferring ownership of the underlying truth. fileciteturn14file0

### External Truth
Truth originating outside the platform, such as provider-originated payment outcomes, wallet-client state, or raw provider runtime behavior. fileciteturn14file0L161-L168

### Policy-Derived Canonical Entity
An entity derived from one or more canonical truths under approved policy or pipeline logic and treated as the authoritative output for a bounded platform concern. fileciteturn14file0

### Execution Entity
An entity that tracks job status, retry state, processing attempts, callback handling, or workflow progression. Execution entities own execution lineage, not automatically the business meaning of the work. fileciteturn14file0

### Superseding / Corrective Entity
An append-oriented correction or replacement record that preserves prior history rather than silently erasing it. fileciteturn14file0

### Entity Family
A coherent class of related canonical or derived entities, such as accounts, workspaces, credits mutations, payout cycles, public registry entries, or transparency reports. fileciteturn14file0

## Truth Class Taxonomy
This specification MUST preserve the following truth classes wherever relevant:
1. **Semantic truth** — what an entity means and which domain owns that meaning
2. **Policy truth** — which rules govern creation, mutation, correction, and interpretation
3. **Runtime truth** — what is happening during execution now
4. **Ledger / storage truth** — the durable authoritative record used by the owner domain
5. **Provider-input truth** — raw external signals before normalization
6. **Implementation-adapter truth** — verification, normalization, translation, and boundary logic
7. **Execution truth** — jobs, retries, submissions, callback handling, and progression lineage
8. **Projection / reporting truth** — dashboards, analytics, registry artifacts, exports, search indexes, and reports
9. **Presentation truth** — UX composition and explanatory renderings

These truth classes MUST NOT be collapsed into one undifferentiated ownership model. fileciteturn14file1L184-L193 fileciteturn14file2L143-L151

## Architectural Position in the Spec Hierarchy
This document sits below:
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`

and above:
- domain-specific entity and lifecycle specs
- API architecture and internal/public API specs
- ledger, billing, payout, audit, registry, reporting, and transparency specs
- product integration specs
- on-chain/off-chain responsibility refinements

This document does not override the top-level boundary model. It operationalizes that model at the durable entity and persistence-discipline layer. fileciteturn14file0 fileciteturn14file1

## System Boundaries
The entity model MUST be interpreted across the following ownership layers:
1. **Platform Canonical Entities** — reusable shared platform entities such as accounts, sessions, workspaces, roles, wallet links, payment records, credits entities, subscriptions, entitlements, audit records, and governance-linked control artifacts
2. **Product-Domain Canonical Entities** — product-specific truth entities owned by product domains such as QTB, AIMM, ZAGA, AIE, HerHelp, Botmad, and future products
3. **On-Chain Canonical Entities** — contract-native truths such as token balances, credits commitments where committed on-chain, funded payout execution state, reserve/vault state, and contract-governance control state
4. **Policy-Derived Canonical Entities** — authoritative outputs derived under governed policy, such as eligibility datasets, holder-rank classifications, and payout entitlement inputs
5. **Execution Entities** — workflow runs, jobs, retries, provider-processing records, chain-submission records, reconciliation runs, and dispatch attempts
6. **Derived and Reporting Entities** — dashboards, indexes, exports, reporting artifacts, public registry views, analytics facts, and UI projections

These layers MAY reference one another, but they MUST NOT silently replace one another. fileciteturn14file0

## Adjacent Boundaries
This specification interacts with adjacent files as follows:
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md` governs top-level ownership, truth-family ownership, and mutation-owner interpretation
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md` governs the ecosystem framing and top-level layer interpretation
- `PLATFORM_ARCHITECTURE_SPEC.md` governs plane separation and runtime interaction posture
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md` assigns canonical owners to the major domains represented here
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md` refines chain-adjacent and off-chain responsibility splits
- product-boundary specs refine how products consume shared entities without redefining them
- identity/account/session and workspace/access-control foundation documents refine access-related entity families listed here

This document defines the entity and persistence interpretation that those documents must preserve. It does not absorb all of their narrower detail. fileciteturn14file0 fileciteturn14file1 fileciteturn14file2

## Conflict Resolution Rules
When materials, systems, or records disagree, the following rules apply:
1. the active refined registry and higher constitutional materials win over narrower documents
2. `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md` wins on top-level truth-family ownership and mutation-owner interpretation
3. `DOMAIN_OWNERSHIP_MATRIX_SPEC.md` wins on major-domain owner assignment and default rights semantics
4. this document wins on entity-family ownership, lifecycle interpretation, canonical-versus-derived status, and persistence-discipline rules
5. canonical owner entities win over reports, dashboards, caches, indexes, exports, queues, AI explanations, or public publication artifacts
6. on-chain committed truth wins only for categories explicitly committed on-chain; broader off-chain business meaning remains off-chain unless explicitly committed
7. raw provider input NEVER wins by itself; verified and normalized owner-controlled consequences win
8. governance or control-plane approval MAY authorize, constrain, or block change, but the resulting business state MUST still be written through the correct owner-domain entity path
9. when ambiguity remains, FUZE MUST choose the more conservative architecture-consistent interpretation and escalate the ambiguity into downstream refinement or decision recording

## Default Decision Rules
When ambiguity exists and no narrower approved exception is available, FUZE MUST default to the following:
1. cross-product reusable entity families default to platform ownership
2. product-local objects default to product ownership only if they do not redefine a shared primitive
3. financial, credits, payout, reserve, entitlement, registry, and governance-sensitive entities default to platform or on-chain ownership according to the relevant economic and chain-boundary specs, never to dashboards or product-local convenience stores
4. provider callbacks default to external input status until owner-controlled normalization succeeds
5. projections, reports, dashboards, search indexes, exports, and AI summaries default to derived-state status
6. queues, jobs, schedulers, callback processors, and worker state default to execution-state status
7. control-plane actions default to policy restriction or enablement state, not ordinary business-domain ownership state
8. if no explicit owner can be named for an entity family, the design is incomplete and MUST NOT be treated as production-grade

## Roles / Actors / Entities
### Human Actors
- end users
- workspace members and owners
- product operators
- support and remediation operators
- security reviewers
- finance and treasury reviewers
- governance and approval actors
- public, partner, community, and investor readers of approved trust surfaces

### System Actors
- platform domain services
- product domain services
- authorization and entitlement evaluators
- workflow and worker systems
- provider adapters and normalization systems
- chain-adjacent services
- reporting, registry, and publication systems
- control-plane systems

### Core Entity Families Influenced by This Specification
- identity and account entities
- linked login and session entities
- workspace, membership, and access entities
- wallet-link and participation-context entities
- billing, payment normalization, credits, subscription, entitlement, and payout entities
- product-local entities
- on-chain state references and chain-submission artifacts
- reporting, registry, transparency, and analytics artifacts
- audit and control-plane lineage artifacts

## Ownership Model
The FUZE data model is domain-first and owner-explicit.

Every canonical entity family MUST define:
- canonical owner domain
- canonical truth location
- mutation authority boundary
- lifecycle owner
- allowed non-owner rights
- correction and supersession rules
- reporting and projection status
- reconciliation posture where cross-domain truth may diverge temporarily

Entity families MAY interact heavily, but they MUST NOT be treated as co-owning the same business meaning without a formally defined narrower exception. fileciteturn14file0 fileciteturn14file1

## Authority / Decision Model
### Canonical Authority
The canonical owner decides:
- valid state transitions
- canonical fields and invariants
- what downstream APIs and events must preserve
- what forms of correction, reversal, supersession, or reconciliation are allowed
- which derived artifacts are permitted

### Governance / Control Authority
Control domains MAY:
- approve or deny sensitive actions
- apply restrictions, rollouts, remediations, emergency controls, and policy-based holds
- require reason codes, operator lineage, and trace identifiers

Control authority does not transfer canonical ownership of the underlying entity family. fileciteturn14file1 fileciteturn14file2

### Execution Authority
Execution systems MAY coordinate work, but they do not become the owner of the business meaning of the entities they process. fileciteturn14file0

## State Model
Each entity family MUST distinguish among:
- canonical state owned by the domain
- policy state constraining the entity lifecycle
- execution state used to process work involving the entity
- derived state used for reporting, publication, search, analytics, or presentation
- provider-input state used as external input before normalization

A downstream implementation MUST NOT silently collapse these state classes. fileciteturn14file1

## Lifecycle / Workflow Model
At the entity-reference level, FUZE entity lifecycles follow this general pattern:
1. a canonical owner creates the entity or accepts its creation through an owner-controlled contract
2. if external signals are involved, provider input remains external until verification and normalization succeed
3. if deferred work is required, execution entities track progression without becoming the owner of business truth
4. owner-controlled state transitions emit owner-governed events or lineage artifacts
5. derived domains MAY publish reporting, registry, search, or analytics outputs from canonical sources
6. control-plane actions MAY constrain, block, or remediate the lifecycle but do not replace canonical ownership
7. correction, supersession, reconciliation, and archival semantics remain owned by the canonical domain

## Invariants
1. every material FUZE entity family MUST have one canonical owner
2. non-owners MUST NOT mutate canonical truth except through an owner-controlled contract
3. products MUST NOT create alternate identity, session, workspace, authorization, entitlement, credits, payout, registry, or governance primitives
4. provider-originated raw state MUST NOT directly become canonical FUZE business truth
5. reporting and publication outputs MUST remain downstream to canonical sources unless explicitly elevated by a narrower specification
6. chain truth MUST remain explicit and narrow to what is committed on-chain
7. execution systems MUST NOT silently acquire long-lived business ownership
8. governance-sensitive overrides MUST be explicit, bounded, reason-coded, and auditable
9. cached or denormalized copies MUST remain refreshable from canonical truth
10. lag or reconciliation gaps in derived state MUST be visible rather than hidden

## Functional Rules
### Rule 1: Only Canonical Owners May Author Canonical Fact Changes
Only the canonical owner or an explicitly delegated owner-controlled component MAY accept writes that change canonical entity state. fileciteturn14file0

### Rule 2: Derived Systems May Not Silently Mutate Owned Truth
Analytics systems, dashboards, search indexes, reports, exports, AI summaries, and publication systems MUST NOT redefine upstream entity state. fileciteturn14file0

### Rule 3: Cross-Domain Writes Require Explicit APIs, Commands, or Events
No domain should mutate another domain’s canonical entity store through private direct-write behavior except through intentionally defined integration boundaries owned or approved by the canonical domain. fileciteturn14file0

### Rule 4: Manual Intervention Must Preserve Ownership Clarity
Support, admin, or remediation actions MUST operate through the owning domain’s correction pathway, not by bypassing ownership with undocumented direct edits. Such actions MUST be reason-coded and auditable. fileciteturn14file0

### Rule 5: Control Authorization Does Not Transfer Entity Ownership
A governance or control-plane approval MAY authorize a change, but the owning domain MUST still write the resulting canonical state or correction lineage. fileciteturn14file0

### Rule 6: External Signals Must Be Normalized Before Entity Mutation
Provider or chain-adjacent inputs MAY influence internal entities only after verification, normalization, and owner-controlled transition logic. fileciteturn14file0 fileciteturn14file2

### Rule 7: Cross-Domain References Are Preferred Over Copy-and-Reown
A product or adjacent domain SHOULD reference canonical entities by stable identifiers rather than copy full objects and assume local ownership. fileciteturn14file0

### Rule 8: Correction Lineage Must Remain Visible
Trust-sensitive entity families MUST preserve original-versus-corrective linkage via superseding IDs, adjustment records, reversal records, correction reason codes, or equivalent lineage primitives. fileciteturn14file0

## Permission / Access Considerations
Domain ownership and authorization are related but separate.
- identity does not imply authorization ownership
- workspace scope does not imply permission-truth ownership
- product capability does not imply product ownership of shared platform entities
- admin capability does not imply unrestricted cross-domain mutation rights

Downstream access-control specifications MUST preserve entity ownership boundaries when granting operational permissions. fileciteturn14file2L271-L279

## Entitlement Considerations
Entitlement is a distinct domain concern. Product capability exposure MAY depend on entitlement, but entitlement truth MUST remain separately owned from identity, authorization, billing, product-local runtime decisions, and UI toggles. fileciteturn14file1L299-L309

## Canonical Platform Entity Families
The following entity families SHOULD be treated as canonical platform entities unless a narrower downstream specification formally constrains a sub-family:

### Identity and Access Family
- `Account` — owned by the identity and account domain
- `LinkedLoginMethod` — owned by the auth/session/linked-login domain
- `Session` — owned by the auth/session domain
- `AccountSecurityState` — owned by the identity/auth security domain under downstream refinement
- `Workspace` — owned by the workspace and organization domain
- `WorkspaceMembership` — owned by the workspace domain
- `WorkspaceRoleAssignment` / `AccessGrant` — owned by the role/permission/access-control domain
- `WorkspaceBillingOwner` — owned by the workspace-linked commercial domain under downstream refinement

### Wallet and Participation Family
- `WalletLink` — owned by the wallet-aware participation domain
- `WalletVerificationRecord` — owned by the wallet-aware participation domain
- `HolderRankRecord` / `HolderRankClassification` — owned by the wallet-aware participation and rank domain
- `ParticipationContextRecord` — owned by the wallet-aware participation domain

### Commerce and Entitlement Family
- `PaymentRecord` — owned by payment rails integration and normalization
- `CreditsOwnerScope` — owned by the platform credits domain
- `CreditsMutationRecord` — owned by the credits ledger and settlement domain
- `CreditsBalanceProjection` — owned by the platform credits domain when formally maintained as canonical current-state output
- `Subscription` — owned by the subscriptions and usage-billing domain
- `Entitlement` — owned by the entitlement/subscriptions domain
- `Invoice` — owned by the invoicing domain
- `Receipt` — owned by the invoicing and receipts domain
- `RefundRecord` / `ReversalRecord` / `AdjustmentRecord` — owned by the refund/reversal/adjustment domain

### Audit and Trust-Surface Family
- `AuditEvent` — owned by the audit log and activity domain
- `ActivityEvent` — owned by the audit log and activity domain or a narrower activity domain if later refined
- `SupportInterventionRecord` — owned by support/control under downstream refinement
- `RegistryEntry` — owned by the public contract and wallet registry domain as a publication artifact
- `TransparencyReportArtifact` — owned by the transparency reporting domain as a published artifact

These assignments align with the active entity draft and the higher-order ownership matrix. fileciteturn14file0

## Product-Domain Canonical Entities
Products in FUZE retain canonical ownership over their own product-domain objects. Shared platform layers create leverage, but products still need product-specific durable truth.

Illustrative product-owned entity families include:
- QTB: analysis requests, signal packages, strategy workspace artifacts, alert definitions, report objects
- AIMM: liquidity strategy objects, market-operation configurations, execution recommendation objects, monitoring state objects
- ZAGA: utility program definitions, campaign configurations, token utility modules, project-side utility policy objects
- AIE: event profiles, discovery items, recommendation objects, participation context items
- HerHelp: sheet connection definitions, app-generation projects, page definitions, generated business-workflow assets, form-mapping objects
- Botmad: workflow scan results, execution suggestions, approved automation instructions, task-run artifacts, desktop workflow models

Products own product truth, but they MUST consume platform-owned entities for identity, workspace context, wallet-link and participation context, payments, credits, subscriptions, entitlements, audit infrastructure, and control/governance restriction context where relevant. Product-local copies of platform truth remain non-canonical. fileciteturn14file0

## On-Chain Canonical Entities
FUZE MUST recognize on-chain canonical entities whose truth originates from contracts rather than mutable off-chain records.

### Canonical On-Chain Families
- `FUZE Token Balance` — owned by Ethereum token contract state
- reserve and vault contract state — owned by the respective reserve, vesting, or vault contracts
- `Base Credits Commitment State` — owned by the Base credits execution layer where applicable
- `Base Payout Contract State` — owned by the payout execution contract on Base
- contract-governance control state — owned by multisig, timelock, or related control contracts where applicable

Off-chain holder tables, rank views, accounting tables, payout reports, and dashboards are derived from these truths and MUST NOT replace them. On-chain domains own only the truths explicitly committed to contract design. fileciteturn14file0 fileciteturn14file2L167-L175

## Policy-Derived Canonical Entities
Some entities are canonical within the platform even though they are derived from other canonical truths under approved policy.

### Canonical Policy-Derived Families
- `EligibilityDataset` — derived from token balances, snapshot references, and exclusion/treatment policy; owned by the snapshot and eligibility pipeline domain
- `HolderRankClassification` — derived from wallet links, token holdings, and rank policy; owned by the wallet-aware participation/rank domain
- `PayoutEntitlementInput` — derived from eligibility datasets, payout-cycle rules, and approved adjustments or exclusions; owned by the payout-preparation domain

A derived entity MAY still be canonical for its bounded concern if FUZE formally depends on it as the authoritative output of a governed pipeline. fileciteturn14file0

## Execution Entities
FUZE MUST explicitly distinguish execution entities from business entities.

Illustrative execution families include:
- workflow runs
- job records
- retry records
- provider callback processing records
- chain submission records
- reconciliation run records
- notification dispatch attempts
- AI execution task lineage records

Execution entities are canonical for execution lineage, not automatically for business meaning. A successful execution record is not identical to business success unless the owning business domain formally commits the resulting state transition. fileciteturn14file0

## Data Model / Storage Implications
This specification requires downstream data-model and persistence specifications to preserve owner boundaries.

At minimum:
- entity ownership MUST align with domain ownership
- cached copies, indexes, dashboards, registries, and analytics stores are derived unless explicitly elevated
- on-chain and off-chain records MUST remain distinguishable
- wallet-link records remain platform-owned mapping state, not token-balance truth
- reporting and publication stores MUST preserve source traceability
- schema migrations MUST preserve ownership continuity when domains move, split, or consolidate
- current-state projections MAY be canonical only when the owning domain explicitly designates them as authoritative for that bounded state concern
- append-oriented ledgers remain canonical for mutation history even where a current-state projection is separately canonical

## Read Model / Projection / Reporting Rules
1. dashboards, summaries, reports, exports, search indexes, AI explanations, and public registry artifacts are downstream derived artifacts by default
2. a derived artifact MAY be canonical only for its own bounded publication or reporting domain, not for the upstream business fact it summarizes
3. read models MAY denormalize for performance, but they MUST preserve source references sufficient for reconciliation and audit
4. projections MUST NOT silently accept writes that bypass the canonical owner
5. lag, reconciliation gaps, and stale projections MUST be visible rather than hidden
6. a product-local cache MUST be refreshable from the canonical owner and MUST NOT act as the hidden write authority

## Cross-Domain Reference Rules
Cross-domain integration in FUZE SHOULD prefer reference-based composition over ownership confusion.

Preferred references include:
- `account_id`
- `workspace_id`
- `wallet_link_id`
- `credits_charge_reference`
- `subscription_id`
- `payout_cycle_id`
- `eligibility_dataset_id`

Rules:
- references MUST point to canonical entities or approved canonical artifacts
- local copies MUST be explicitly marked as cached, denormalized, projected, or exported
- denormalized fields SHOULD be refreshable from canonical truth
- identity-like attributes MUST NOT be re-authored locally unless explicitly product-specific
- references MUST NOT imply mutation rights over the referenced entity

## Canonical IDs and Global Entity Identity
FUZE SHOULD use stable identifiers across entity domains where practical.

Principles:
- every canonical entity SHOULD have a stable primary identifier
- cross-domain references SHOULD use canonical IDs
- public-facing IDs and internal IDs MAY differ where security or UX requires it, but mapping MUST remain controlled
- identifiers SHOULD survive projection, reporting, export, and audit contexts where long-term traceability matters

The same conceptual subject MAY have multiple related IDs across layers, such as `account_id`, linked wallet address, payout claimant address, workspace membership ID, and credits owner-scope ID. These are not duplicates if they refer to different entity concepts. The data model MUST preserve conceptual clarity rather than forcing one identifier to represent unrelated things. fileciteturn14file0

## Economic Entity Ownership Rules
Economic records require especially strong ownership clarity.

### Canonical Families and Owners
- `PaymentRecord` / `PaymentEvent` — payment integration and normalization domain after verification
- `CreditsMutation` — credits ledger and settlement domain
- `CurrentCreditsBalanceProjection` — platform credits domain projection when explicitly maintained as canonical current-state output
- `SubscriptionState` — subscriptions and usage-billing domain
- `Invoice` / `Receipt` — invoicing and receipts domains
- `RefundRecord` / `ReversalRecord` / `AdjustmentRecord` — refund, reversal, and adjustment domain
- `DistributableProfitDeterminationRecord` — treasury/accounting policy domain
- `PayoutCycleBusinessRecord` — profit participation and payout domain

Economic state MUST never be left to inference from scattered product events alone. Product-local balance copies are derived only. fileciteturn14file0

## Governance and Treasury Entity Ownership Rules
Governance and treasury domains also require explicit separation between business records and contract execution state.

### Canonical Families and Owners
- `GovernanceActionRecord` — governance domain
- `TreasuryActionRecord` — treasury control domain
- `VaultActionRecord` — vault action policy and treasury execution domain
- `FoundationPolicyTreatmentRecord` — foundation governance domain
- multisig/timelock execution references — contract state for execution truth; governance records for contextual business truth
- `TransparencyReportArtifact` — transparency reporting domain
- `PublicRegistryEntry` — public contract and wallet registry domain

Governance truth often spans business decision records plus contract execution records. FUZE MUST model both explicitly rather than assuming one replaces the other. fileciteturn14file0

## API / Contract Implications
This entity model requires downstream API specifications to make ownership explicit.

At minimum:
- every mutation-capable API MUST identify the owning entity family and domain
- write APIs MUST route to the owning domain only
- read APIs MAY aggregate across domains but MUST preserve source-of-truth semantics
- non-owners MUST request mutation through owner-controlled commands rather than convenience endpoints
- provider callbacks MUST terminate in verification and normalization boundaries before affecting canonical truth
- chain-adjacent interfaces MUST distinguish contract truth from off-chain orchestration truth
- idempotency obligations MUST be assigned to the owner boundary or an explicitly delegated owner-controlled component

A downstream API specification that cannot answer “which entity family is canonical here, who owns it, and what happens when values disagree” is incomplete. fileciteturn14file0

## Event / Async Implications
This entity model requires downstream event and workflow designs to preserve ownership.

At minimum:
- owner domains or explicitly delegated owner-controlled components MUST publish canonical events
- execution-plane records remain execution lineage unless the owning domain explicitly elevates a result
- retries MUST NOT create duplicate business outcomes
- provider callbacks and chain-event ingestion MUST resolve through normalized, idempotent owner-controlled transitions
- derived pipelines MUST tolerate lag without overwriting canonical truth
- trace identifiers SHOULD connect owner decisions, execution progression, and publication outputs

## Security / Risk / Abuse Controls
This specification is also a security and abuse-prevention document.

If entity ownership is not explicit:
- products may overreach into platform state
- reports may redefine business truth
- chain truth may be shadowed by stale caches or projections
- provider signals may mutate balances or entitlements incorrectly
- execution systems may silently become business owners
- governance-sensitive domains may be controlled through ordinary runtime paths

All downstream security, abuse-prevention, monitoring, and incident specifications MUST preserve this entity-ownership model. fileciteturn14file1 fileciteturn14file2

## Boundary Violation Detection / Non-Canonical Patterns
The following are non-canonical and forbidden unless a higher approved specification explicitly allows a bounded exception:
- product-local identity roots
- product-local session truth
- product-local cross-product authorization truth
- product-local credits, payout, registry, or reserve primitives
- reporting or dashboard stores used as systems of record for upstream facts
- provider callbacks directly mutating billing, entitlement, credits, or other business truth without normalization
- worker or queue state treated as the owner of long-lived business meaning
- control-plane convenience endpoints bypassing owner validation and audit requirements
- chain-indexer data treated as broader off-chain policy truth without owner-controlled interpretation
- hidden local copies of canonical entities that accept writes outside the owner domain

## Audit / Traceability Requirements
FUZE MUST be able to determine:
- which domain owns an entity family
- which boundary accepted a mutation
- which component executed a step
- whether a visible output is canonical, policy-derived, execution, derived, or presentational
- how provider signals were verified and normalized
- how off-chain decisions related to on-chain execution where relevant
- which approvals, restrictions, overrides, policy version, reason code, operator identity, and trace identifier applied where relevant
- which corrective or superseding entities altered prior interpretation without erasing history

## Failure Handling / Edge Cases
### Cached Product Copy of Platform State
A cached product copy is non-canonical and MUST reconcile to the platform owner.

### Reporting Mismatch With Chain Events
The chain domain remains canonical for chain-owned truth. Reporting systems MUST reconcile, disclose lag, or mark uncertainty.

### Provider Outage During Payment or Purchase Flow
External provider failure MUST NOT be treated as canonical commercial completion until verification and normalization complete.

### Product Invents Product-Local Credits
This violates the entity model unless a narrower approved specification explicitly establishes a bounded non-canonical sub-ledger governed by the platform credits domain.

### Wallet-Aware Tier Differs From Latest Chain State
The token domain remains canonical for token-balance truth. Wallet-aware participation context MAY lag temporarily but MUST reconcile.

### Worker or Workflow Starts Holding Long-Lived Business Meaning
The owning business domain MUST absorb the canonical state, or the architecture MUST be refactored explicitly. Execution systems MAY NOT quietly become the owner.

### Derived Investor or Community Report Computes a Metric Incorrectly
The report artifact is wrong, but it does not alter canonical platform or chain truth.

### Governance Approval Is Granted
Approval authorizes or constrains a sensitive action path but does not transfer ownership of the resulting business state.

## Operational Considerations
- runbooks SHOULD identify entity-family owners for every major mutation-capable surface
- incident response SHOULD classify impact by affected canonical owner and truth class
- remediation pathways MUST preserve owner-controlled correction semantics
- degraded mode MUST prefer explicit holds, read-only posture, or marked uncertainty over ambiguous mutation
- observability SHOULD tag logs, traces, jobs, and events with entity-family and owner identifiers where feasible

## Migration / Compatibility / Supersession Considerations
- migrations MUST NOT silently transfer ownership from one entity family or domain to another without an explicit specification change
- compatibility layers MAY preserve older integrations temporarily, but they MUST NOT preserve shadow ownership as the long-term model
- if a product-local capability becomes cross-product and foundational, it SHOULD be considered for elevation into a platform-owned entity family
- if older materials imply looser entity-ownership rules, this refined specification supersedes them within its scope
- any domain split, merge, entity-family deprecation, or schema reshaping MUST preserve traceability for prior canonical records and clear supersession lineage

## Implementation-Contract Guardrails
Downstream implementation contracts MUST preserve all of the following:
- one explicit canonical owner per material entity family
- explicit mutation boundaries and idempotency posture
- explicit distinction among canonical, execution, policy-derived, provider-input, derived, and presentation state
- explicit source references in projections, publication artifacts, and exports
- explicit tie-break rules when provider, chain, cache, report, and owner state disagree
- explicit audit lineage for corrections, overrides, and governance-sensitive actions
- explicit reason codes for non-routine corrective or operator-driven changes where trust-sensitive state is affected
- explicit lifecycle ownership for creation, mutation, correction, closure, and archival posture
- explicit visibility into lag, pending reconciliation, or projection staleness where those affect interpretation

Downstream implementations MUST NOT optimize away ownership clarity for convenience.

## Downstream Execution Staging
This document SHOULD be consumed by downstream authors in the following order:
1. on-chain/off-chain responsibility and product-boundary refinements
2. identity, session, workspace, wallet-aware, credits, billing, payout, entitlement, audit, and governance entity-domain specifications
3. public/internal API and event specifications
4. workflow, job, reporting, security, monitoring, and migration specifications
5. product-integration and control-plane implementation documents

## Required Downstream Specs / Contract Layers
This specification materially informs:
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `WALLET_AWARE_USER_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `PAYMENT_RAILS_INTEGRATION_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `PAYOUT_LEDGER_SPEC.md`
- `API_ARCHITECTURE_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `AUDIT_LOG_AND_ACTIVITY_SPEC.md`
- product integration specifications

## Canonical Examples / Anti-Examples
### Canonical Examples
- a `CreditsMutationRecord` is appended only through the credits owner domain and then consumed by balance projections and reports
- a product stores `workspace_id` and `account_id` references instead of re-owning workspace or account truth
- an eligibility pipeline produces a cycle-specific `EligibilityDataset` that becomes canonical for that cycle’s payout eligibility basis
- a payout cycle preserves separate business record, contract execution record, and reporting artifact records with explicit linkage

### Anti-Examples
- a product dashboard overwrites entitlement state because it detected a UI inconsistency
- a search index accepts hidden writes that alter source entity status
- a provider callback directly increments credits balance without owner-controlled normalization
- a chain indexer table is treated as the owner of broader off-chain payout-policy meaning
- a worker retry record becomes the hidden source of business truth for a failed commercial flow

## Dependencies / Cross-Spec Links
This document depends most directly on:
- `REFINED_SYSTEM_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
- `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`

## Explicitly Deferred Items
The following items are intentionally deferred to downstream specifications:
- detailed relational, document, or event-schema design
- full contract storage layout and ABI details
- exact queue topics, retry backoff rules, and orchestration step graphs
- product-specific lifecycle details for individual products
- detailed archival and retention posture per entity family
- exact reason-code vocabularies and policy-version field schemas

## Final Normative Summary
FUZE MUST model every materially important entity family with one explicit canonical owner, one explicit mutation boundary, one explicit lifecycle owner, and one explicit interpretation of whether the entity is canonical, policy-derived canonical, execution, derived, or presentational. Products MAY add product truth but MUST NOT recreate shared platform primitives. On-chain truth MUST remain explicit and narrow. Provider input MUST be normalized before it affects canonical entities. Reports, dashboards, indexes, exports, and AI summaries remain downstream by default. Corrections, reversals, and supersession MUST preserve lineage. Downstream implementation contracts MUST preserve ownership clarity even under degraded runtime conditions.

## Quality Gate Checklist
- [x] Canonical owner is explicit for every material truth family
- [x] Mutation boundaries are explicit
- [x] Adjacent boundaries are explicit
- [x] Truth classes are explicit
- [x] Conflict-resolution rules are explicit
- [x] Default decision rules are explicit
- [x] Non-canonical patterns are called out clearly
- [x] Operator and admin override paths are bounded and audited
- [x] Read-model, cache, reporting, and projection rules are explicit
- [x] On-chain versus off-chain responsibilities are explicit where relevant
- [x] Failure and degraded-mode behaviors are explicit
- [x] Downstream implementation guardrails are explicit
- [x] Dependencies and downstream impacts are explicit
- [x] Non-goals and deferred items are explicit
- [x] The document is strong enough that backend, API, data, runtime, and reporting teams can implement without inventing contradictory semantics
