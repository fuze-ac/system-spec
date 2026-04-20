# DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC

## Document Metadata

- Document Name: `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Governing Layer: Platform constitution / data model / entity ownership
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, data engineering, API design, security, audit, operations, governance, reporting
- Primary Purpose: Define the canonical entity-ownership architecture and data-model discipline for FUZE, including which entity families are canonical, which are derived, how lifecycle and mutation authority are assigned, how cross-domain references must work, and how correction, reconciliation, and migration must preserve ownership clarity
- Primary Upstream References:
  - `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
  - `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
  - `PLATFORM_ARCHITECTURE_SPEC.md`
  - `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
  - `SYSTEM_SPEC_INDEX.md`
  - `API_SPEC_INDEX.md`
  - `DOCS_SPEC_INDEX.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
  - `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
- Primary Downstream Dependents:
  - `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
  - `IDENTITY_AND_ACCOUNT_SPEC.md`
  - `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
  - `WORKSPACE_AND_ORGANIZATION_SPEC.md`
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
  - `WALLET_AWARE_USER_SPEC.md`
  - `PLATFORM_CREDITS_SPEC.md`
  - `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
  - `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
  - `PAYOUT_LEDGER_SPEC.md`
  - `API_ARCHITECTURE_SPEC.md`
  - `PUBLIC_API_SPEC.md`
  - `INTERNAL_SERVICE_API_SPEC.md`
  - `AUDIT_LOG_AND_ACTIVITY_SPEC.md`
  - product integration specifications

---

## Purpose

This specification defines the canonical data model and entity ownership architecture of the FUZE ecosystem.

Its purpose is to establish how durable truth is modeled across the FUZE platform, which domain owns which entity family, where canonical state resides, how lifecycle ownership is assigned, how derived entities differ from canonical entities, and how entity-level discipline must be preserved across products, reporting, workflows, APIs, on-chain systems, and external integrations.

This document is intentionally foundational and implementation-usable. It is the entity-level refinement of the FUZE boundary, architecture, and domain-ownership model. It exists to prevent duplicated truth, accidental co-ownership, ambiguous lifecycle control, and silent mutation through derived or convenience stores.

---

## Scope

This specification governs:

- the canonical entity-ownership philosophy of FUZE
- the distinction between platform entities, product entities, on-chain entities, policy-derived canonical entities, execution entities, and derived/reporting entities
- the source-of-truth rules for major canonical entity families
- lifecycle ownership and mutation authority for canonical entities
- cross-domain reference rules and identifier discipline
- the treatment of projections, caches, ledgers, exports, indexes, and reporting artifacts
- the data-model implications of platform/product/on-chain/external boundaries
- correction, supersession, reconciliation, and migration discipline at the entity layer
- the implications of entity ownership for APIs, events, audit, and operational remediation

This specification does not define:

- every database table or full schema field list
- every contract storage layout
- every event payload
- exact physical database topology
- exact service decomposition or repository module boundaries
- exact archival/retention policy by entity
- exact foreign-key strategy across every service boundary

Those belong in downstream specifications.

---

## Out of Scope

This specification is explicitly out of scope for:

- low-level persistence engine choices
- narrow product-UI shape
- exact queue technology
- exact graph/search/index implementation details
- exact contract ABI detail
- endpoint-by-endpoint API schemas
- human team ownership and staffing
- environment-specific deployment decisions

---

## Design Goals

The design goals of the FUZE data model and entity ownership architecture are:

1. Ensure every materially important fact has one canonical owner.
2. Prevent the same business fact from being silently authored in multiple places.
3. Distinguish canonical write models from derived read, cache, reporting, search, and analytics models.
4. Preserve platform-first shared reuse without flattening product-specific truth.
5. Make lifecycle, support, reconciliation, audit, and governance review easier through explicit entity lineage.
6. Preserve strong separations among off-chain truth, on-chain truth, external truth, and policy-derived truth.
7. Support multi-product expansion without forcing repeated re-litigation of entity ownership.
8. Keep APIs, workflows, workers, and reporting aligned with entity ownership rather than convenience duplication.
9. Support correction and supersession without destroying trust-sensitive history.
10. Make future migrations and domain splits possible without loss of authority clarity.

---

## Non-Goals

This specification is not intended to:

- force all entities into one monolithic schema
- collapse product-domain truth into generic platform abstractions when that weakens product value
- treat every derived model as ephemeral if the platform formally depends on it as a canonical pipeline output
- treat blockchain data as the owner of all platform business meaning
- allow convenience denormalization to replace ownership clarity
- define exact physical storage implementation for every entity family
- imply that dashboards, reports, AI summaries, caches, or exports own the facts they display

---

## Core Principles

### 1. One Material Fact, One Canonical Owner
Every materially important fact in the FUZE ecosystem must have one canonical owner.

### 2. Storage Convenience Never Overrides Ownership
An entity may be copied, cached, indexed, summarized, or joined in many places, but it may not be silently re-authored in many places.

### 3. Entity Ownership Is Lifecycle Ownership
Canonical ownership includes existence, canonical fields, valid transitions, correction pathway, closure rules, and archival responsibility.

### 4. Derived Does Not Necessarily Mean Disposable
A derived entity may still be canonical for its own domain if it is the authoritative output of a formal pipeline governed by the platform.

### 5. Append-Oriented Correction Beats Silent Rewrite
Trust-sensitive domains should prefer correction lineage, reversals, adjustments, or superseding records over silent destructive rewrites.

### 6. Cross-Domain References Must Preserve Ownership
Products and adjacent domains should reference canonical entities rather than copy and re-own them.

### 7. On-Chain and Off-Chain Entity Models Must Stay Distinct
Contract-native entities and off-chain platform entities may be related, but they must not be collapsed into one ambiguous entity model.

### 8. Reporting Artifacts Are Usually Downstream
Reports, dashboards, exports, search indexes, and public views are usually derived artifacts, not source truth.

---

## Canonical Definitions

### Canonical Entity
The primary entity record or record family authoritative for a fact, including canonical fields, valid transitions, mutation acceptance, and lifecycle truth.

### Derived Entity
A computed, copied, summarized, indexed, cached, exported, or publication-oriented representation created from canonical truth.

### Projection / Read Model
A query-optimized or interface-optimized representation built for product UX, reporting, search, dashboards, or operational views. It does not own the underlying fact.

### Ledger Entity
An append-oriented entity that records event or economic mutation history. A ledger may be canonical for historical mutation lineage without being the only canonical representation of current state.

### Reference Entity
A pointer or relationship artifact used to connect domains without transferring ownership of the underlying truth.

### External Truth
Truth originating outside the platform, such as a provider-originated payment outcome, wallet-client state, or raw external runtime behavior.

### Policy-Derived Canonical Entity
An entity derived from one or more canonical truths under an approved policy or pipeline and treated as the authoritative output for a bounded platform concern.

### Execution Entity
An entity that tracks job status, retry state, processing attempts, or workflow progression. Execution entities own execution lineage, not necessarily the business meaning of the work.

### Superseding / Corrective Entity
An append-oriented correction or replacement record that preserves prior history rather than silently erasing it.

### Entity Family
A coherent class of related canonical or derived entities, such as accounts, workspaces, credits mutations, payout cycles, or transparency reports.

---

## Architectural Position in the Spec Hierarchy

This document sits below:

- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`

and above:

- domain-specific entity and lifecycle specs
- API architecture and internal/public API specs
- ledger, billing, payout, audit, registry, and reporting specs
- product integration specs
- on-chain/off-chain responsibility refinements

This document does not override the system boundary model. It operationalizes that model at the durable entity and persistence-discipline layer.

---

## Ownership Layers in FUZE

FUZE must distinguish among the following ownership layers.

### 1. Platform Canonical Entities
Core shared platform entities such as accounts, linked logins, sessions, workspaces, memberships, roles, wallet links, payment records, credits entities, subscriptions, entitlements, and governance records.

### 2. Product-Domain Canonical Entities
Product-specific truth entities owned by product domains such as QTB, AIMM, ZAGA, AIE, HerHelp, Botmad, and future products.

### 3. On-Chain Canonical Entities
Contract-native or chain-native truths such as FUZE token balances on Ethereum, Base credits commitment state where applicable, Base payout execution truth, and reserve/vault state.

### 4. Policy-Derived Canonical Entities
Authoritative platform outputs derived from canonical inputs under governed policy, such as eligibility datasets, holder-rank classifications, and payout entitlement inputs.

### 5. Execution Entities
Workflow-run, job, retry, orchestration, provider-processing, or chain-submission records that own execution lineage but not necessarily the business meaning of the underlying domain object.

### 6. Derived and Reporting Entities
Transparency reports, payout ledgers, dashboards, search indexes, exports, analytics facts, and UI projections that do not own the underlying business or contract fact they display.

These layers may reference one another, but they must not silently replace one another.

---

## Entity Ownership Rules by Truth Type

### Identity Truth
Owned by the platform identity and account domain.

### Linked Login and Session Truth
Owned by the auth/session/linked-login domain.

### Workspace Truth
Owned by the workspace and organization domain.

### Authorization Grant Truth
Owned by the role, permission, and access-control domain.

### Wallet-Link Truth
Owned by the wallet-aware user / wallet-link domain.

### External Payment Verification Truth
Owned by the payment integration and normalization domain after verification and normalization.

### Platform Credits Mutation Truth
Owned by the credits ledger and settlement domain.

### Platform Credits Current Balance Truth
Owned by the credits domain’s formally maintained current balance projection if such a projection is designated canonical by the credits domain.

### Subscription Truth
Owned by the subscriptions and usage-billing domain.

### Entitlement Truth
Owned by the entitlement / subscriptions domain.

### Product Object Truth
Owned by the relevant product domain.

### Ethereum Holder Truth
Owned by Ethereum contract state for FUZE token balances.

### Base Credits Commitment Truth
Owned by the Base contract layer where credits are formally committed on-chain.

### Eligibility Truth
Owned by the snapshot and eligibility pipeline as a policy-derived canonical dataset.

### Payout Preparation Truth
Owned by the payout-preparation / payout domain for off-chain business-cycle state.

### Payout Execution Truth
Owned by Base payout contract state for funded cycle execution and claim behavior.

### Treasury and Reserve Structural Truth
Owned by reserve/vault contracts and governance-controlled treasury records, with distinct ownership for business decision records versus execution records.

### Governance Action Truth
Owned by governance action records plus contract-level control state where applicable.

### Reporting Artifact Truth
Owned by the reporting domain as an artifact, but not for the underlying business or chain facts summarized.

---

## Canonical Platform Entity Families

The following entity families should be treated as canonical platform entities.

### Account
Represents the persistent FUZE user identity.

Canonical owner:
- Identity and account domain

Core facts owned:
- `account_id`
- lifecycle status
- canonical identity references
- security posture references
- linkage root to auth methods
- closure/restriction state

### LinkedLoginMethod
Represents a provider or credential linkage tied to an account.

Canonical owner:
- Auth / session / linked-login domain

### Session
Represents active or historical authentication session truth.

Canonical owner:
- Auth / session domain

### AccountSecurityState
Represents account-level security posture and restriction-relevant identity security state.

Canonical owner:
- Identity/auth security domain as refined downstream

### Workspace
Represents an organization or collaborative operating context.

Canonical owner:
- Workspace and organization domain

### WorkspaceMembership
Represents account participation within a workspace.

Canonical owner:
- Workspace domain

### WorkspaceRoleAssignment / AccessGrant
Represents access authority in platform or workspace scope.

Canonical owner:
- Role / permission / access-control domain

### WorkspaceBillingOwner
Represents billing ownership assignment for a workspace.

Canonical owner:
- Billing / workspace-linked commercial domain as refined downstream

### WalletLink
Represents linkage between a wallet and a platform account.

Canonical owner:
- Wallet-aware participation domain

### WalletVerificationRecord
Represents proof or lineage of wallet-link verification.

Canonical owner:
- Wallet-aware participation domain

### HolderRankRecord
Represents platform-owned holder classification derived from wallet links, token state, and policy logic.

Canonical owner:
- Wallet-aware participation / rank domain

### ParticipationContextRecord
Represents wallet-aware participation context consumed by products or platform logic.

Canonical owner:
- Wallet-aware participation domain

### PaymentRecord
Represents the normalized platform record of an external payment or purchase event.

Canonical owner:
- Payment rails integration / normalization domain

### CreditsOwnerScope
Represents the owning scope for credits, whether account-bound, workspace-bound, or other approved scope.

Canonical owner:
- Platform credits domain

### CreditsMutationRecord
Represents append-oriented credits mutation truth.

Canonical owner:
- Credits ledger / settlement domain

### CreditsBalanceProjection
Represents the current balance projection derived from credits mutation history and adjustments.

Canonical owner:
- Platform credits domain when formally maintained as canonical current-state output

### Subscription
Represents recurring commercial agreement state for an account or workspace.

Canonical owner:
- Subscriptions and usage-billing domain

### Entitlement
Represents current access rights or service entitlements resulting from billing and policy.

Canonical owner:
- Entitlement / subscriptions domain

### Invoice
Represents invoice artifact truth for a commercial event.

Canonical owner:
- Invoicing domain

### Receipt
Represents receipt artifact truth for a commercial event.

Canonical owner:
- Invoicing and receipts domain

### RefundRecord / ReversalRecord / AdjustmentRecord
Represents commercial correction lineage.

Canonical owner:
- Refund / reversal / adjustment domain as refined downstream

### AuditEvent
Represents immutable audit lineage for material actions.

Canonical owner:
- Audit log and activity domain

### ActivityEvent
Represents user/admin/system activity where downstream specs distinguish it from immutable audit lineage.

Canonical owner:
- Audit log and activity domain or related activity domain as refined downstream

### SupportInterventionRecord
Represents a bounded support or operator intervention artifact linked to correction lineage.

Canonical owner:
- Support/control domain under downstream refinement

### RegistryEntry
Represents public contract or wallet registry truth as a publication artifact.

Canonical owner:
- Public contract and wallet registry domain

### TransparencyReportArtifact
Represents a canonical published report artifact.

Canonical owner:
- Transparency reporting domain

---

## Product-Domain Canonical Entities

Products in FUZE retain canonical ownership over their own product-domain objects.

This is necessary because shared platform layers create leverage, but products still need product-specific durable truth.

Examples include:

### QTB Product Entities
Possible canonical entities:
- analysis request
- signal package
- strategy workspace artifact
- alert definition
- report object

Canonical owner:
- QTB product integration domain

### AIMM Product Entities
Possible canonical entities:
- liquidity strategy object
- market-operation configuration
- execution recommendation object
- monitoring state object

Canonical owner:
- AIMM product integration domain

### ZAGA Product Entities
Possible canonical entities:
- utility program definition
- campaign configuration
- token utility module
- project-side utility policy object

Canonical owner:
- ZAGA product integration domain

### AIE Product Entities
Possible canonical entities:
- event profile
- discovery item
- recommendation object
- participation context item

Canonical owner:
- AIE product integration domain

### HerHelp Product Entities
Possible canonical entities:
- sheet connection definition
- app generation project
- page definition
- generated business workflow asset
- form mapping object

Canonical owner:
- HerHelp product integration domain

### Botmad Product Entities
Possible canonical entities:
- workflow scan result
- execution suggestion
- approved automation instruction
- task-run artifact
- desktop workflow model

Canonical owner:
- Botmad product integration domain

### Product-Domain Principle
Products own product truth, but they should depend on platform-owned entities for:
- identity
- workspace context
- wallet-link and participation context
- payments
- credits
- subscriptions and entitlements
- audit infrastructure
- control/governance restriction context where relevant

Products may cache or denormalize platform data for local use, but those copies remain non-canonical.

---

## On-Chain Canonical Entities

FUZE must recognize on-chain canonical entities whose truth originates from contracts rather than mutable off-chain records.

### FUZE Token Balance
Canonical owner:
- Ethereum token contract state

Important note:
- off-chain holder tables, rank views, analytics tables, and dashboards are derived from this truth

### Reserve / Vault Contract State
Canonical owner:
- respective reserve, vesting, or vault contract

Examples:
- Foundation Vault state
- Treasury Reserve Vault state
- Team Vesting Vault state
- Advisor Vesting Vault state
- Holder Incentives Vault state
- Ecosystem Partnership Vault state
- Liquidity Operations Vault state
- Transparency / Stability Vault state

### Base Credits Contract / Commitment State
Canonical owner:
- Base credits execution layer where applicable

Important note:
- off-chain accounting, payment normalization, and balance projections may reconcile to this state, but do not replace it

### Base Payout Contract State
Canonical owner:
- payout execution contract on Base

Core facts owned:
- funded cycle execution state
- claim-open state
- claim-completion state
- contract balance state for funded cycles

### Governance-Control Contract State
Canonical owner:
- multisig, timelock, or control contracts where applicable

Important note:
- internal governance records and reports contextualize these truths, but contract state remains canonical for execution posture

On-chain domains own only the truths explicitly committed to contract design.

---

## Policy-Derived Canonical Entities

Some entities are canonical within the platform even though they are derived from other canonical truths under approved policy.

These must be modeled as policy-derived canonical entities rather than treated as casual temporary files.

### EligibilityDataset
Derived from:
- Ethereum FUZE balances
- snapshot reference
- exclusion and treatment policy

Canonical owner:
- Snapshot and eligibility pipeline domain

Why canonical:
- for a given cycle, it becomes the authoritative eligibility basis used by downstream payout logic

### HolderRankClassification
Derived from:
- wallet links
- token holdings
- policy-defined rank logic

Canonical owner:
- wallet-aware participation / rank domain

Why canonical:
- products may consume it, but must not invent competing rank truth locally

### PayoutEntitlementInput
Derived from:
- eligibility dataset
- payout cycle rules
- approved adjustments or exclusions where allowed

Canonical owner:
- payout-preparation domain

Why canonical:
- payout execution depends on it even though it is not raw chain truth

### Important Principle
A derived entity can still be canonical for its own bounded domain if it is the authoritative output of a formal pipeline. Derived does not automatically mean disposable.

---

## Execution Entities

FUZE must explicitly distinguish execution entities from business entities.

Examples:
- workflow run
- job record
- retry record
- provider callback processing record
- chain submission record
- reconciliation run record
- notification dispatch attempt
- AI execution task lineage record

Execution entities are canonical for execution lineage, not automatically for business meaning.

Rules:
- execution state may explain how work progressed
- execution state may authorize retry or compensation logic within the execution domain
- execution state does not replace business-domain canonical state
- a successful execution record is not automatically identical to business success unless the owning business domain formally commits the resulting state transition

---

## Derived, Read, and Reporting Entities

FUZE should treat many common entities as explicitly derived rather than canonical.

### Dashboard Aggregates
Examples:
- workspace usage summaries
- credits summary cards
- holder overview cards
- treasury summary widgets

These are derived read models, not source truth.

### Transparency Reports
Examples:
- transparency report artifacts
- investor/community reports
- platform progress summaries

These are canonical as published report artifacts, but not canonical for the underlying economics or contract truths they summarize.

### Payout Ledger Entries
The payout ledger may be canonical as a structured reporting or ledger artifact for cycle records, but it is not the canonical owner of:
- Ethereum holder truth
- raw eligibility processing
- Base payout contract execution truth

It is a linked derived cycle record unless a downstream spec elevates a narrower subset of fields.

### Search Indexes
Searchable projections of product, workspace, support, or reporting data are derived artifacts only.

### Exports
CSV, PDF, spreadsheet, statement, or external export files are derived artifacts.

### Cache Entries
Runtime caches and client-side caches do not own truth.

### Derived-Entity Principle
If an entity can be rebuilt from canonical truth and exists primarily for speed, visibility, publication, or convenience, it should be treated as derived unless explicitly designated as canonical for a narrow bounded purpose.

---

## Mutation Authority Rules

FUZE must apply strict mutation authority rules.

### Rule 1: Only Canonical Owners May Author Canonical Fact Changes
Examples:
- only the identity domain may mutate account lifecycle state
- only the workspace domain may mutate workspace membership truth
- only the credits mutation pipeline may author credits ledger mutations
- only product domains may mutate product-domain core objects
- only the snapshot/eligibility pipeline may publish a canonical eligibility dataset for a cycle

### Rule 2: Derived Systems May Not Silently Mutate Owned Truth
Examples:
- analytics systems may not “fix” subscription state
- reporting pipelines may not redefine payout cycle status
- product dashboards may not overwrite credits truth
- search indexes may not act as hidden write owners

### Rule 3: Cross-Domain Writes Require Explicit APIs, Commands, or Events
No domain should mutate another domain’s canonical store through private direct-write behavior except through intentionally defined integration boundaries owned or approved by the canonical domain.

### Rule 4: Manual Intervention Must Preserve Ownership Clarity
Support, admin, or remediation overrides must operate through the owning domain’s correction pathway, not by bypassing ownership with undocumented direct data edits.

### Rule 5: Control Authorization Does Not Transfer Entity Ownership
A governance or control-plane approval may authorize a change, but the owning domain must still write the resulting canonical state or correction lineage.

### Rule 6: External Signals Must Be Normalized Before Entity Mutation
Provider or chain-adjacent inputs may influence internal entities only after verification, normalization, and owner-controlled transition logic.

---

## Entity Lifecycle Ownership

Every canonical entity family must have a defined lifecycle owner.

Lifecycle ownership includes:
- creation
- mutation
- correction
- closure or completion
- supersession if applicable
- archival posture
- reconciliation linkage where applicable

### Example Lifecycle Structure

#### Account
- created by identity domain
- linked to auth methods by auth domain
- referenced by products
- may be restricted or closed by control logic through owner-approved pathways
- lifecycle remains platform-owned

#### Workspace
- created by workspace domain
- memberships mutated by workspace/access-control logic
- billing linked by billing domain
- products consume workspace context
- lifecycle remains workspace-domain-owned

#### CreditsMutationRecord
- created by payment verification or spend/reversal flows
- appended by credits ledger domain
- referenced by balance projections and reports
- lifecycle remains credits-domain-owned

#### PayoutCycle
- initiated by payout-preparation / governance flow
- funded via treasury/payout interaction
- executed on Base
- reported via payout ledger and transparency systems
- lifecycle truth is segmented:
  - cycle business record owned by payout domain
  - execution truth owned by contract state
  - reporting artifact owned by reporting/ledger domain

### Lifecycle Principle
A clear owner must exist for creation, mutation, correction, closure, and archival logic for every canonical entity family.

---

## Cross-Domain Reference Rules

Cross-domain integration in FUZE should prefer reference-based composition over ownership confusion.

### Preferred Pattern
A product or adjacent domain entity should store references such as:
- `account_id`
- `workspace_id`
- `wallet_link_id`
- `credits_charge_reference`
- `subscription_id`
- `payout_cycle_id`
- `eligibility_dataset_id`

rather than copying and re-owning the full underlying object.

### Why This Matters
Reference-driven integration:
- reduces inconsistency
- improves reconciliation
- preserves product independence
- improves supportability
- makes migration and correction safer
- preserves platform-wide lineage

### Rules
- references must point to canonical entities or approved canonical artifacts
- local copies must be explicitly marked as cached, denormalized, projected, or exported
- denormalized fields should be refreshable from canonical truth
- identity-like attributes should not be re-authored locally unless explicitly product-specific
- references must not imply mutation rights over the referenced entity

---

## Canonical IDs and Global Entity Identity

FUZE should use stable identifiers across entity domains where practical.

### Principles
- every canonical entity should have a stable primary identifier
- cross-domain references should use canonical IDs
- public-facing IDs and internal IDs may differ where security or UX requires it, but mapping must remain controlled
- identifiers should survive projection, reporting, export, and audit contexts where long-term traceability matters

### Important Note
The same conceptual subject may have multiple related IDs across layers, for example:
- `account_id`
- linked `wallet_address`
- payout claimant address
- workspace membership ID
- credits owner scope ID

These are not duplicates if they refer to different entity concepts. The data model must preserve conceptual clarity rather than forcing one identifier to represent unrelated things.

---

## Entity Ownership for Economic Records

Economic records require especially strong ownership clarity.

### PaymentEvent / PaymentRecord
Canonical owner:
- payment rails integration domain after provider verification and normalization

### CreditsMutation
Canonical owner:
- credits ledger / settlement domain

### CurrentCreditsBalanceProjection
Canonical owner:
- credits domain projection over mutation truth where formally maintained as canonical current state

Important note:
- append ledger is canonical for mutation history
- current balance projection may be canonical for current balance state if explicitly maintained by the credits domain
- product-local balance copies are derived only

### SubscriptionState
Canonical owner:
- subscriptions and usage-billing domain

### Invoice / Receipt Artifact
Canonical owner:
- invoicing domain / receipts domain as refined downstream

### Refund / Reversal / Adjustment Record
Canonical owner:
- refund, reversal, and adjustment domain

### DistributableProfitDeterminationRecord
Canonical owner:
- treasury/accounting policy domain

### PayoutCycleBusinessRecord
Canonical owner:
- profit participation / payout domain

### Principle
Economic state must never be left to inference from scattered product events alone.

---

## Entity Ownership for Governance and Treasury Records

### GovernanceActionRecord
Canonical owner:
- governance domain

### TreasuryActionRecord
Canonical owner:
- treasury control domain

### VaultActionRecord
Canonical owner:
- vault action policy / treasury execution domain

### FoundationPolicyTreatmentRecord
Canonical owner:
- foundation governance domain

### Multisig / Timelock Execution Reference
Canonical owner:
- on-chain control state for execution truth
- governance records for contextual business truth

### TransparencyReportArtifact
Canonical owner:
- transparency reporting domain

### PublicRegistryEntry
Canonical owner:
- public contract and wallet registry domain

### Principle
Governance truth often spans business decision records plus contract execution records. FUZE must model both explicitly rather than assuming one replaces the other.

---

## Canonical vs Derived State in APIs

APIs must preserve ownership clarity.

### Canonical Write APIs
These should route to the owning domain only.

Examples:
- create workspace
- link wallet
- issue credits after verified payment
- create subscription
- approve treasury action through the proper domain pathway
- publish payout cycle business record

### Derived Read APIs
These may aggregate across domains.

Examples:
- dashboard summary
- workspace billing overview
- holder privilege summary
- payout cycle overview
- reserve architecture summary

### API Principle
A read API may compose truth from many canonical owners, but a write API should only mutate through the rightful owner. This keeps service topology aligned with entity architecture.

---

## Event / Async Implications

Entity ownership must also govern event and async design.

Rules:
- owner domains or explicitly delegated owner-controlled components should publish canonical events
- execution-plane records remain execution state unless the owning domain explicitly elevates a result
- retries must not create duplicate business outcomes
- provider callbacks and chain event ingestion must resolve through normalized, idempotent owner-controlled transitions
- derived pipelines must tolerate lag without overwriting canonical truth
- AI-generated proposals must not become canonical entities without owner-domain validation and acceptance

---

## Audit, Correction, and Supersession Rules

Entity ownership must define how corrections happen.

### Principle
Canonical truth should not be silently overwritten in trust-sensitive domains where append-oriented correction is more appropriate.

### Examples
- credits errors should create adjustment or reversal records
- payout-cycle reporting errors should create correction lineage
- governance mistakes should produce superseding governance records where possible
- registry entry corrections should remain visible
- support interventions should preserve original and corrective linkage

### Ownership Implication
Only the owning domain should author corrective or superseding records for its entities, though governance or support pathways may authorize them.

---

## Reconciliation Model Across Entity Owners

FUZE should support reconciliation across owner domains without weakening owner clarity.

Important reconciliation pairs include:

- external payment events ↔ payment records ↔ credits issuance
- credits ledger ↔ current balance projections ↔ product consumption charges
- Ethereum holder state ↔ eligibility dataset ↔ payout entitlement input
- treasury authorization ↔ payout funding ↔ payout ledger artifact
- workspace billing ownership ↔ subscription state ↔ invoice/receipt artifacts
- governance action record ↔ multisig/timelock contract execution

### Reconciliation Principle
Reconciliation links truths across owners. It does not erase the fact that ownership remains distinct.

---

## Data Model Boundaries for New Products

When FUZE adds a new product, the following ownership rules apply by default.

### The New Product Must Reuse Platform-Owned Entities For:
- accounts
- linked login and session context
- workspaces
- memberships and roles
- wallet links and holder context
- payment and credits context
- subscriptions and entitlements
- audit infrastructure
- control/governance restriction context where relevant

### The New Product May Own Its Own Canonical Entities For:
- product objects
- product workflows
- product outputs
- product-specific settings
- domain-specific AI artifacts
- product-side operational history

### Expansion Principle
New products should expand the platform by adding product truth, not by recreating platform truth.

---

## Minimum Canonical Entity Families

At minimum, FUZE should recognize the following canonical entity families.

### Platform Identity Family
- Account
- LinkedLoginMethod
- Session
- AccountSecurityState

### Workspace Family
- Workspace
- WorkspaceMembership
- WorkspaceRoleAssignment
- WorkspaceBillingOwner

### Wallet / Participation Family
- WalletLink
- WalletVerificationRecord
- HolderRankRecord
- ParticipationContextRecord

### Commerce Family
- PaymentRecord
- CreditsOwnerScope
- CreditsMutationRecord
- CreditsBalanceProjection
- Subscription
- Entitlement
- Invoice
- Receipt
- RefundRecord
- ReversalRecord
- AdjustmentRecord

### Product Family
- product-specific domain entities by product
- ProductUsageRecord where product-owned
- ProductOutputArtifact

### Governance / Treasury Family
- GovernanceAction
- TreasuryAction
- VaultAction
- FoundationPolicyRecord
- RegistryEntry
- TransparencyReportArtifact

### Profit Participation Family
- SnapshotReference
- EligibilityDataset
- PayoutEntitlementInput
- PayoutCycle
- PayoutLedgerRecord

### Audit / Execution Family
- AuditEvent
- ActivityEvent
- IncidentLinkedEvent
- SupportInterventionRecord
- WorkflowRun
- JobRecord
- ProviderProcessingRecord
- ChainSubmissionRecord

These families create the minimum platform-wide ownership vocabulary for implementation.

---

## Minimum Conceptual Ownership Metadata

At minimum, the data model and entity ownership architecture should recognize the following conceptual metadata where applicable:

### Ownership Metadata
- `entity_type`
- `canonical_owner_domain`
- `ownership_layer`
- `mutation_authority_reference`
- `lifecycle_owner_reference`

### Reference Metadata
- `entity_id`
- `source_entity_id`
- `derived_from_reference`
- `correlation_id`
- `supersedes_entity_id`

### Visibility and Control Metadata
- `visibility_class`
- `scope_type`
- `scope_id`
- `policy_reference`
- `audit_lineage_reference`

### Integrity Metadata
- `is_canonical`
- `is_derived`
- `projection_type`
- `reconciliation_reference`
- `correction_reason_code`

These are conceptual requirements, not a mandate that every entity store every field physically.

---

## Data Model / Storage Implications

This specification imposes the following storage-discipline rules:

1. entity ownership must align with domain ownership
2. canonical transactional stores must remain distinguishable from derived stores
3. execution entities must remain distinguishable from business-domain entities
4. provider raw input and normalized internal outcomes must remain distinguishable
5. chain-derived records and off-chain policy records must remain distinguishable
6. reporting/publication stores must preserve source traceability
7. caches, search indexes, analytics stores, and dashboards must not become accidental write owners
8. schema migrations must preserve ownership continuity when domains move, split, or consolidate

---

## Permission / Access Considerations

This file does not replace access-control specs, but it imposes the following entity-layer rules:

- ownership does not imply universal read access
- product admin capability is not platform control-plane authority
- workspace scope is not itself authorization truth
- authorization is not identical to entitlement
- privileged operators must still use owner-correct pathways for trust-sensitive entity changes
- governance-sensitive actions require explicit elevated pathways even when the initiating actor is already authenticated for ordinary work

---

## Security / Risk / Abuse Controls

This entity-ownership model is a security control.

If entity ownership is unclear:
- products can overreach into platform state
- dashboards and reports can redefine business truth
- chain truth can be shadowed by stale projections
- provider signals can mutate balances or entitlements incorrectly
- execution systems can silently become business owners
- governance-sensitive changes can occur without proper lineage

All downstream security, abuse-prevention, monitoring, and incident specs must preserve this entity model.

---

## Failure Handling / Edge Cases

### Cached Product Copy of Platform State
A cached product copy is non-canonical and must reconcile to the platform owner.

### Reporting Mismatch With Chain Events
The chain domain remains canonical for chain-owned truth. Reporting must reconcile or mark lag.

### Provider Outage During Payment or Purchase Flow
External provider failure must not be treated as canonical commercial completion until verification and normalization are complete.

### Product Invents Product-Local Credits
This violates the ownership model unless formally approved as a non-canonical restricted sub-ledger under the Platform Credits domain.

### Wallet-Aware Rank Differs From Latest Chain State
Ethereum token truth remains canonical for balance state. Holder rank may lag temporarily but must reconcile.

### Worker or Workflow Starts Holding Long-Lived Business Meaning
The owning business domain must absorb the canonical state, or the model must be explicitly refactored. Execution systems may not quietly become the owner.

### Derived Investor or Community Report Computes a Metric Incorrectly
The report artifact is wrong, but it does not alter canonical platform or on-chain truth.

### Governance Approval Is Granted
Approval authorizes a sensitive action path but does not transfer ownership of the resulting business state.

---

## Migration / Compatibility / Supersession Considerations

- migrations must not silently transfer ownership from one domain to another without explicit specification change
- compatibility layers may preserve older integrations temporarily, but must preserve canonical ownership semantics
- renamed or split entity families must document continuity of authority, lifecycle ownership, and event ownership
- deprecated caches, reports, shadow tables, or exports must not remain relied upon as hidden truth
- if older files or implementations contain looser entity-ownership assumptions, this refined specification supersedes them within its scope

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
- identity/account/auth/session foundation docs
- workspace/access-control foundation docs

This specification directly governs or materially informs:

- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `WALLET_AWARE_USER_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `PAYOUT_LEDGER_SPEC.md`
- `AUDIT_LOG_AND_ACTIVITY_SPEC.md`
- `API_ARCHITECTURE_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- event, workflow, monitoring, reporting, and product integration specs

---

## Explicitly Deferred Items

The following are intentionally deferred to downstream specifications:

- exact canonical schema by entity family
- exact persistence topology across services and databases
- exact event-sourcing boundaries where adopted
- exact cross-service reference and foreign-key strategy
- exact archival and retention policy by entity type
- exact physical projection strategy for reporting/search/caching
- exact data migration model when entity ownership evolves

These do not weaken the canonical data model and entity ownership architecture established here.

---

## Final Normative Summary

The FUZE data model and entity ownership architecture defines how durable truth is structured across a multi-product, multi-layer, transparency-first platform ecosystem. Every materially important fact must have one canonical owner. Platform entities, product entities, on-chain entities, policy-derived canonical entities, execution entities, and derived/reporting artifacts must remain conceptually distinct even when they interact heavily.

Products, workflows, reports, ledgers, dashboards, caches, and chain-integrated services may reference or derive from canonical truth, but they may not silently compete with it. Correction must preserve lineage. Reconciliation must preserve owner clarity. Cross-domain integration must prefer references over re-ownership. If a future design cannot clearly state who owns an entity, where its canonical state resides, who may mutate it, and how corrections occur, that design is incomplete and must escalate before implementation.
