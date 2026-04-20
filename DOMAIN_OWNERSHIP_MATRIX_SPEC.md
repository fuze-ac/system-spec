# DOMAIN_OWNERSHIP_MATRIX_SPEC

## Document Metadata

- Document Name: `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Governing Layer: Platform constitution / ownership matrix
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, data, API design, security, operations, governance, reporting
- Primary Purpose: Define the canonical domain ownership matrix for FUZE by mapping major domains to their ownership class, canonical owner, source-of-truth location, mutation boundary, execution posture, presentation rights, governance relevance, and default allowed consumers
- Primary Upstream References:
  - `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
  - `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
  - `PLATFORM_ARCHITECTURE_SPEC.md`
  - `SYSTEM_SPEC_INDEX.md`
  - `API_SPEC_INDEX.md`
  - `DOCS_SPEC_INDEX.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
  - `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
- Primary Downstream Dependents:
  - `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
  - `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
  - `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
  - `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md`
  - `API_ARCHITECTURE_SPEC.md`
  - `INTERNAL_SERVICE_API_SPEC.md`
  - `PUBLIC_API_SPEC.md`
  - `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
  - product integration specs
  - security, monitoring, audit, migration, and runtime specs

---

## Purpose

This specification defines the canonical domain ownership matrix for FUZE.

Its purpose is to convert the platform boundary and architecture model into an implementation-usable ownership map that can be applied consistently across services, schemas, APIs, events, reporting surfaces, admin operations, chain-adjacent flows, and future product integrations.

This document is intentionally narrower than the top-level boundary and overview documents and intentionally broader than entity-level or endpoint-level files. It answers the practical question:

**Which FUZE domain owns this category of truth, where is that truth canonical, who may mutate it, who may derive from it, and how must non-owners interact with it?**

---

## Scope

This specification governs:

- the canonical inventory of major FUZE domains
- domain classification across platform, product, on-chain, reporting, control, and external layers
- default canonical owner assignment for each major domain
- source-of-truth location rules for each major domain
- default mutation, execution, derivation, presentation, and governance rights by domain class
- decision rules for assigning new services, tables, APIs, events, or workflows to a canonical domain
- escalation rules for ambiguous or contested ownership
- matrix-level rules that downstream specs must preserve when refining entities, APIs, events, or storage models

This specification does not define:

- full entity-by-entity schema ownership
- exact service topology or codebase boundaries
- exact API route ownership at the endpoint level
- exact event producer/consumer lists
- exact team staffing or organization chart accountability
- full lifecycle logic for each individual domain

Those belong in downstream specifications.

---

## Out of Scope

This document is explicitly out of scope for:

- detailed database schema design
- endpoint-by-endpoint contract design
- repo module or package boundaries
- queue topic naming and worker implementation details
- product-local UX decisions
- low-level contract ABI/storage definitions
- human team management structure
- exact deployment topology

---

## Design Goals

The design goals of the domain ownership matrix are:

1. Give every major FUZE concern one clear canonical owner.
2. Make source-of-truth boundaries implementation-ready.
3. Prevent hidden overlap between platform, product, on-chain, reporting, and provider domains.
4. Preserve strict separation between identity, session, workspace, authorization, entitlement, billing, credits, token, payout, reporting, and governance-sensitive control.
5. Support clean service, schema, API, and event decomposition.
6. Make future product integration predictable and platform-safe.
7. Strengthen auditability, incident response, and transparency through explicit ownership mapping.
8. Make domain ambiguity visible early rather than discovered through production drift.

---

## Non-Goals

This matrix is not intended to:

- create soft co-ownership by default
- replace lifecycle specs for narrower domains
- let products redefine shared platform primitives
- let reports or dashboards become silent systems of record
- let provider inputs directly become canonical business truth without normalization
- let on-chain presence imply ownership of broader off-chain policy
- let convenience implementation override explicit ownership

---

## Core Principles

### 1. One Domain, One Canonical Owner
Every material FUZE domain must have one canonical owner.

### 2. Domain Ownership Is Multi-Dimensional
Ownership must be analyzed across:
- canonical ownership
- mutation ownership
- execution ownership
- presentation ownership
- governance ownership

These dimensions may interact, but they must not be conflated.

### 3. Platform-First for Shared Concerns
Any domain that is cross-product, identity-defining, economically shared, governance-sensitive, or required for future platform expansion defaults to platform ownership unless a narrower exception is formally specified.

### 4. Product Domains Are Extension Domains
Products may own product-local truth and workflows, but they may not redefine shared platform primitives.

### 5. Chain Ownership Is Explicit and Narrow
On-chain domains are canonical only for truths explicitly committed to contract design.

### 6. Reporting Is Usually Derived
Reporting, transparency, and publication domains may aggregate and present, but they do not redefine upstream truth unless a narrower spec explicitly elevates a reporting-owned dataset.

### 7. External Systems Remain External
Provider-owned raw state remains external until FUZE verifies, normalizes, and records an internal consequence through the correct owner boundary.

### 8. Ambiguity Must Escalate
If canonical ownership cannot be stated clearly, the design is incomplete.

---

## Canonical Definitions

### Domain
A bounded category of responsibility that owns a coherent class of truth, decisions, state transitions, interfaces, and validation rules.

### Canonical Owner
The domain or contract layer authoritative for canonical fields, valid transitions, mutation acceptance, and event authority for a category of truth.

### Canonical Truth Location
The system layer or storage boundary where the authoritative state for that domain resides.

### Domain Contract
The explicit mutation and interaction boundary through which non-owners must request reads, writes, or execution.

### Derived Domain
A domain whose primary purpose is read-optimized aggregation, reporting, search, registry publication, or presentation rather than primary truth ownership.

### Control Domain
A domain whose primary role is approval, restriction, risk, remediation, or governance-aware operational enablement.

### Domain Consumer
A non-owning domain, surface, or service that reads, derives from, presents, or executes against an owning domain.

---

## Architectural Position in the Spec Hierarchy

This document sits below:

- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`

and above:

- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
- API architecture and internal/public API specs
- event and workflow specs
- product integration specs

This matrix does not override the boundary model. It operationalizes it at domain-reference level.

---

## Domain Classification Model

All FUZE domains must be classified into one of the following classes.

### 1. Platform Core Domain
A reusable cross-product domain owned by the FUZE platform.

### 2. Product Domain
A product-specific extension domain owned by one product layer.

### 3. On-Chain Contract Domain
A domain whose canonical truth is committed to a smart contract and chain state.

### 4. Governance and Control Domain
A domain that owns approvals, restrictions, emergency controls, sensitive remediations, or other trust-sensitive control behavior.

### 5. Reporting and Transparency Domain
A domain that primarily owns derived outputs, publication artifacts, reconciliation views, or public/stakeholder transparency artifacts.

### 6. External Integration Domain
A domain representing raw provider state, provider execution environments, or provider-facing access/adapter boundaries.

These classes exist so ownership remains explicit even when many domains interact.

---

## Ownership Dimensions

Each domain must be evaluated across the following ownership dimensions.

### Canonical Ownership
Who owns the source truth and governing policy.

### Mutation Ownership
Who is allowed to accept writes that change canonical truth.

### Execution Ownership
Who runs the operational process or technical action.

### Presentation Ownership
Who may display or publish domain state.

### Governance Ownership
Who may approve, restrict, or constrain sensitive actions involving the domain.

A domain may present or execute without owning canonical truth.

---

## Domain Rights Model

Each domain interaction must use one or more explicit right types.

### Allowed right types
- **Read**
- **Write**
- **Derive**
- **Present**
- **Execute Against**
- **Govern**

### Default rights rules
1. Only the canonical owner may define canonical domain state.
2. Non-owners may not write canonical state unless explicitly authorized by the owning domain contract.
3. Reporting domains usually receive read + derive + present rights, not write rights.
4. Product domains usually receive read + execute-against rights for platform domains, not ownership rights.
5. Platform domains may orchestrate against on-chain domains but do not redefine chain truth.
6. Control domains may govern sensitive actions without becoming the owner of underlying business truth.
7. External domains expose provider signals; FUZE-owned consequences must be recorded only after normalization.

---

## Canonical Domain Inventory

The canonical FUZE domain inventory is grouped below.

### Platform Core Domains
- Identity and Account
- Auth / Session / Linked Login
- Workspace and Organization
- Role / Permission / Access Control
- Wallet-Aware Participation
- Commerce and Billing
- Platform Credits Policy and Off-Chain Credits Orchestration
- Invoicing and Receipts
- Payment Verification and Normalization
- Entitlement and Capability Gating
- AI Orchestration
- Model Routing and Context
- AI Usage Metering
- Workflow and Automation
- Job Queue and Worker Coordination
- Audit Log and Activity
- Transparency Reporting Coordination
- Public Registry Publication
- Payout Orchestration
- Integration Adapter Layer
- Control Plane / Admin Controls / Governance-Aware Operations

### Product Domains
- QTB Product Domain
- AIMM Product Domain
- ZAGA Product Domain
- AIE Product Domain
- HerHelp Product Domain
- Botmad Product Domain
- Future Product Domains

### On-Chain Contract Domains
- Ethereum Token Domain
- Base Credits Ledger Commitment Domain
- Base Payout Execution Domain
- Reserve and Vault Contract Domains
- Governance-Controlled Contract Role Domains

### Reporting and Public Visibility Domains
- Public Contract Registry
- Public Wallet Registry
- Payout Ledger
- Investor Reporting
- Community Reporting
- Derived Transparency Views

### External Integration Domains
- Stripe Payment Domain
- Stablecoin Payment Rail Domain
- Telegram Stars Rail Domain
- Apple Billing Domain
- Google Billing Domain
- Wallet Client Domain
- RPC / Indexing Provider Domain
- AI Model Provider Domain
- Cloud / Runtime Provider Domain

---

## Canonical Domain Ownership Matrix

| Domain | Class | Canonical Owner | Canonical Truth Location | Default Mutation Boundary | Default Non-Owner Rights | Notes |
|---|---|---|---|---|---|---|
| Identity and Account | Platform Core | Platform | Platform identity/account records | Identity domain APIs/services | Read, Execute Against, limited Present | Cross-product identity root |
| Auth / Session / Linked Login | Platform Core | Platform | Platform auth/session stores | Auth/session domain APIs/services | Read, Execute Against, limited Present | Sessions are access state, not identity |
| Workspace and Organization | Platform Core | Platform | Platform workspace/org stores | Workspace domain APIs/services | Read, Execute Against, limited Present | Shared collaborative scope |
| Role / Permission / Access Control | Platform Core | Platform | Platform authorization stores | Access-control domain APIs/services | Read, Execute Against | Product-specific role extensions may exist, but platform remains canonical for shared authz |
| Wallet-Aware Participation | Platform Core | Platform | Platform wallet-link mapping + chain reads | Wallet-aware domain APIs/services | Read, Execute Against, limited Present | Mapping/context layer, not token truth |
| Commerce and Billing | Platform Core | Platform | Platform billing/commercial state | Billing domain APIs/services | Read, Execute Against, Derive | Cross-product commercial rules |
| Platform Credits Policy and Off-Chain Credits Orchestration | Platform Core | Platform | Platform credits policy + off-chain credits records | Credits domain APIs/services | Read, Execute Against, Derive | Policy and orchestration remain off-chain platform-owned |
| Base Credits Ledger Commitment | On-Chain Contract | Base contract layer | Base chain state | Contract calls via governed chain-adjacent pathways | Read, Derive, Present, Execute Against | Canonical only where credits are committed on-chain |
| Invoicing and Receipts | Platform Core | Platform | Platform financial records | Invoicing domain APIs/services | Read, Present, Derive | Tied to billing truth |
| Payment Verification and Normalization | Platform Core / Integration | Platform | Platform normalized payment records | Payment normalization domain APIs/services | Read, Execute Against, Derive | Raw payments remain external inputs until normalized |
| Entitlement and Capability Gating | Platform Core | Platform | Platform entitlement/capability state | Entitlement domain APIs/services | Read, Execute Against | Distinct from authz and billing truth |
| AI Orchestration | Platform Core | Platform | Platform orchestration state | AI orchestration APIs/services | Read, Execute Against, Derive | Shared AI execution policy |
| Model Routing and Context | Platform Core | Platform | Platform routing/context state | Routing/context APIs/services | Read, Execute Against | Distinct from orchestration truth |
| AI Usage Metering | Platform Core | Platform | Platform metering records | Metering APIs/services | Read, Derive, Execute Against | Distinct from billing truth |
| Workflow and Automation | Platform Core | Platform | Platform workflow state | Workflow APIs/services | Read, Execute Against, Derive | Shared reusable workflow primitives |
| Job Queue and Worker Coordination | Platform Core | Platform | Execution-plane records | Worker/job control interfaces | Read, Execute Against, Derive | Operational execution domain, not generic business truth owner |
| Audit Log and Activity | Platform Core / Reporting | Platform | Platform audit/activity stores | Audit domain interfaces | Read, Derive, Present | Platform-wide traceability |
| Transparency Reporting Coordination | Platform Core / Reporting | Platform | Platform transparency coordination records | Reporting publication interfaces | Read, Derive, Present | Derived/publication-oriented |
| Public Registry Publication | Platform Core / Reporting | Platform | Registry publication records + chain refs | Registry publication interfaces | Read, Present, Derive | Public-safe publication owner, not contract owner |
| Payout Orchestration | Platform Core | Platform | Platform payout-cycle records | Payout orchestration APIs/services | Read, Execute Against, Derive | Off-chain cycle coordination before/around claim execution |
| Integration Adapter Layer | Platform Core / Integration | Platform | Adapter configs + normalization records | Integration adapter interfaces | Execute Against, limited Read | Provider and chain boundary mediation |
| Control Plane / Admin Controls / Governance-Aware Operations | Governance / Control | Platform control layer | Control-plane records and approval state | Control/admin interfaces | Govern, limited Read, limited Execute Against | Governs sensitive actions without replacing business-domain truth |
| QTB Product Domain | Product | QTB product domain | QTB product data | QTB-owned APIs/services | Read, Present, Derive | Product-owned logic |
| AIMM Product Domain | Product | AIMM product domain | AIMM product data | AIMM-owned APIs/services | Read, Present, Derive | Product-owned logic |
| ZAGA Product Domain | Product | ZAGA product domain | ZAGA product data | ZAGA-owned APIs/services | Read, Present, Derive | Product-owned logic |
| AIE Product Domain | Product | AIE product domain | AIE product data | AIE-owned APIs/services | Read, Present, Derive | Product-owned logic |
| HerHelp Product Domain | Product | HerHelp product domain | HerHelp product data | HerHelp-owned APIs/services | Read, Present, Derive | Product-owned logic |
| Botmad Product Domain | Product | Botmad product domain | Botmad product data | Botmad-owned APIs/services | Read, Present, Derive | Product-owned logic |
| Ethereum Token Domain | On-Chain Contract | Ethereum token contract layer | Ethereum mainnet | Contract calls via governed chain-adjacent pathways | Read, Derive, Present, Execute Against | Canonical token balance truth |
| Base Payout Execution Domain | On-Chain Contract | Base payout contract layer | Base chain state | Contract calls via governed chain-adjacent pathways | Read, Derive, Present, Execute Against | Canonical funded payout claim truth |
| Reserve and Vault Contract Domains | On-Chain Contract | Relevant vault contracts | Ethereum/Base contract state as designed | Contract calls via governed pathways | Read, Derive, Present, Execute Against, Govern | Role-specific reserve truth and constraints |
| Governance-Controlled Contract Role Domains | On-Chain Contract / Governance | Relevant contracts + governance controls | Contract role state | Contract/governance interfaces | Read, Derive, Present, Govern | Sensitive authority layer |
| Public Contract Registry | Reporting / Transparency | Platform | Registry records + chain refs | Registry publication interfaces | Read, Present, Derive | Publication of contract structure |
| Public Wallet Registry | Reporting / Transparency | Platform | Registry records + chain refs | Registry publication interfaces | Read, Present, Derive | Presentation only, not wallet-client owner |
| Payout Ledger | Reporting / Transparency | Platform | Reporting/publication records | Payout-ledger publication interfaces | Read, Present, Derive | Reporting of payout cycles and claims |
| Investor Reporting | Reporting / Transparency | Platform | Derived reporting outputs | Reporting publication interfaces | Read, Present, Derive | Derived, non-canonical |
| Community Reporting | Reporting / Transparency | Platform | Derived reporting outputs | Reporting publication interfaces | Read, Present, Derive | Derived, non-canonical |
| Derived Transparency Views | Reporting / Transparency | Platform | Derived reporting/read models | Reporting publication interfaces | Read, Present, Derive | Derived, non-canonical |
| Stripe Payment Domain | External Integration | Stripe | Stripe systems | Stripe/provider interfaces | Read provider result only after verification | FUZE normalizes verified outcomes |
| Stablecoin Payment Rail Domain | External Integration | External rail/provider or chain rail as specified elsewhere | External/provider or explicit chain state | Rail-specific interfaces | Read/Execute Against only through normalized boundaries | Must not bypass billing/payout policy ownership |
| Telegram Stars Rail | External Integration | Telegram | Telegram systems | Telegram/provider interfaces | Read provider result only after verification | FUZE derives credits or commercial outcomes after policy |
| Apple Billing Domain | External Integration | Apple | Apple systems | Apple/provider interfaces | Read provider result only after verification | FUZE derives internal entitlements/commercial outcomes |
| Google Billing Domain | External Integration | Google | Google systems | Google/provider interfaces | Read provider result only after verification | FUZE derives internal entitlements/commercial outcomes |
| Wallet Client Domain | External Integration | User wallet/client | External wallet software | Wallet interactions | Execute Against only | Not FUZE-owned |
| RPC / Indexing Provider Domain | External Integration | Provider | External infra | RPC/indexer access interfaces | Execute Against only | Access path, not canonical meaning owner |
| AI Model Provider Domain | External Integration | Provider | Provider infra | Model provider interfaces | Execute Against only | FUZE owns orchestration, not provider infra |
| Cloud / Runtime Provider Domain | External Integration | Provider | Provider infra | Cloud/provider interfaces | Execute Against only | Does not own FUZE application truth |

This matrix is normative unless superseded by a narrower consistent downstream specification.

---

## Platform Core Domain Definitions

### Identity and Account Domain
Owns canonical account-rooted identity across FUZE, including account records, account lifecycle state, and identity continuity across products.

It does not own session truth, workspace membership truth, or token balance truth.

### Auth / Session / Linked Login Domain
Owns authentication method linkage, session issuance, refresh, revocation, and continuity-safe access behavior.

It is subordinate to the canonical account model and must not redefine identity.

### Workspace and Organization Domain
Owns workspaces, organizations, membership context, collaborative scope, and related structural context.

Workspace scope is not itself authorization truth.

### Role / Permission / Access Control Domain
Owns roles, permission catalogs, grants, scoped authorization logic, access evaluation, and shared access semantics.

It does not own identity, billing, or product-local business objects.

### Wallet-Aware Participation Domain
Owns wallet-link mapping, wallet verification state, account-associated holder context, and wallet-aware recognition inputs.

It does not own token balances or raw wallet-client behavior.

### Commerce and Billing Domain
Owns subscriptions, usage-billing logic, commercial state transitions, and chargeable service semantics.

### Platform Credits Domain
Owns credits policy, class definitions, issuance rules, spend rules, reversal rules, and off-chain orchestration semantics.

### Invoicing and Receipts Domain
Owns generated invoice and receipt records tied to platform commercial truth.

### Payment Verification and Normalization Domain
Owns the verified transition from provider-originated payment or purchase signals into FUZE-approved internal commercial consequences.

### Entitlement and Capability Gating Domain
Owns the mapping from plans, purchases, credits, or policy into allowed capability exposure.

It must remain distinct from core authorization and billing truth.

### AI Orchestration Domain
Owns shared AI execution lifecycle, policy-bounded orchestration, and shared AI task coordination.

### Model Routing and Context Domain
Owns model-selection policy, routing policy, context-binding decisions, and bounded routing metadata.

### AI Usage Metering Domain
Owns canonical AI usage measurement and attribution, distinct from both orchestration and billing truth.

### Workflow and Automation Domain
Owns reusable workflow definitions, workflow runs, automation coordination, and platform workflow policy.

### Job Queue and Worker Coordination Domain
Owns execution-plane lifecycle records, retry state, scheduling state, and worker coordination state.

It does not own the business meaning of every object it processes.

### Audit Log and Activity Domain
Owns audit lineage for meaningful state changes, sensitive actions, and cross-domain traceability.

### Transparency Reporting Coordination Domain
Owns reporting-period coordination, transparency-oriented publication lineage, and derived reporting preparation.

### Public Registry Publication Domain
Owns public-safe registry artifacts for contracts, wallets, and related trust surfaces.

### Payout Orchestration Domain
Owns payout-cycle preparation, funding-intent coordination, and off-chain orchestration prior to or alongside on-chain claim execution.

### Integration Adapter Layer
Owns provider adapters, adapter config metadata, verification bridges, and normalization boundaries.

### Control Plane / Admin Controls / Governance-Aware Operations Domain
Owns approvals, restrictions, rollouts, emergency controls, remediation pathways, sensitive admin flows, and governance-aware operational constraints.

It governs sensitive actions without replacing the canonical owner of the underlying business domain.

---

## Product Domain Definitions

Each product domain is an extension domain built on platform primitives.

A product domain may own:
- product-local entities
- product-local workflows
- product-local AI outputs and prompt patterns
- product-local UX and reports
- product-local operational views
- product-local analytics objects where designated as product-owned

A product domain may not own:
- alternate identity roots
- alternate session truth
- alternate workspace truth
- alternate cross-product authorization truth
- alternate credits truth
- alternate payout truth
- alternate public registry truth
- hidden governance-sensitive control authority

Future products must follow the same pattern.

---

## On-Chain Domain Definitions

### Ethereum Token Domain
Owns canonical FUZE token truth on Ethereum, including balances, transfers, and contract-level holder-visible state.

### Base Credits Ledger Commitment Domain
Owns canonical on-chain credits commitment state where Platform Credits are formally committed to Base.

### Base Payout Execution Domain
Owns funded payout pool state, claim rights as encoded for a cycle, claim execution state, and payout-related on-chain events.

### Reserve and Vault Contract Domains
Own encoded reserve constraints, lock rules, vesting logic, and contract-specific action restrictions.

### Governance-Controlled Contract Role Domains
Own contract-level roles, timelock constraints, multisig-bound authority, and related contract-native control truth where implemented.

On-chain domains own only the truths explicitly committed to contract design.

---

## Reporting and Transparency Domain Definitions

Reporting domains are usually derived domains.

### Public Contract Registry
Owns structured public presentation of contract identities, chain references, and public explanatory mappings.

### Public Wallet Registry
Owns structured public presentation of reserve or platform-relevant wallets and their public role categories.

### Payout Ledger
Owns publication and reporting structure of payout cycles and claim-related public outputs.

### Investor Reporting
Owns stakeholder-facing derived reports built from canonical sources.

### Community Reporting
Owns public/community-facing derived reports built from canonical and audit sources.

### Derived Transparency Views
Own read models, dashboards, and publication outputs built from canonical sources.

Reporting domains may aggregate. They may not redefine upstream truth unless a narrower spec explicitly says otherwise.

---

## External Integration Domain Definitions

External domains are never FUZE-owned canonical business domains, even when FUZE depends on them.

### Payment Providers and App Stores
Own raw payment or purchase state in their ecosystems.

### Wallet Clients
Own local wallet behavior, signature UX, and client-side transaction state.

### RPC / Indexing Providers
Own transport/access services, not token meaning or contract-policy meaning.

### AI Model Providers
Own model execution infrastructure, not FUZE orchestration or policy meaning.

### Cloud / Runtime Providers
Own infrastructure delivery, not FUZE application truth.

---

## Cross-Domain Dependency Rules

### Rule 1: Upward Dependency Is Preferred Over Redefinition
Products should depend on platform domains instead of recreating them.

### Rule 2: Chain Dependencies Must Be Explicit
No platform or product domain may assume chain truth indirectly without declaring the adapter, read model, or contract boundary used.

### Rule 3: Reporting Depends on Canonical Truth, Not Vice Versa
Canonical domains must never depend on reporting outputs as source truth.

### Rule 4: External Dependencies Must Pass Through Normalization Boundaries
Raw provider outputs must not directly mutate canonical FUZE domains without validation and owner-controlled transition.

### Rule 5: Control/Governance Rights Must Be Explicit
No domain may implicitly gain governance rights over another domain.

### Rule 6: Execution Does Not Transfer Ownership
Workers, queues, chain adapters, and callback handlers may execute work without becoming the owner of business meaning.

### Rule 7: Publication Does Not Transfer Ownership
Registry outputs, dashboards, exports, and public reports may present meaningfully but do not become source truth.

---

## Domain Boundary Decision Rules

When introducing a new service, table, object, API, event, workflow, or publication artifact, assign it using the following rules.

### Rule A: Shared Across Products
If it is shared across more than one product, place it in a platform domain unless a formal exception exists.

### Rule B: Balances, Entitlements, or Commercial State
If it defines balances, entitlements, pricing consequence, or commercial state, it is platform-owned unless explicitly committed on-chain.

### Rule C: Token, Claim, Reserve, or Contract Role Truth
If it defines token balance truth, claim execution truth, reserve constraints, or contract role truth, it is on-chain-owned.

### Rule D: Product-Local Business Logic
If it defines category-specific product logic with no shared ecosystem meaning, it is product-owned.

### Rule E: Summary, Dashboard, Registry, or Public Display
If it primarily exists to summarize, publish, or present, it is reporting-owned or publication-owned as a derived domain.

### Rule F: Raw Provider Signal
If it only represents a provider’s raw signal or environment state, it remains external until normalized.

### Rule G: Sensitive Approval or Restriction Logic
If it primarily decides whether a sensitive action may proceed, it is a control/governance domain even if the final state is written by another owner.

### Rule H: Unclear or Mixed Meaning
If the object mixes multiple meanings, split the object or escalate ownership rather than forcing ambiguous co-ownership.

---

## Domain Escalation Rules

Ownership ambiguity must be escalated rather than silently implemented.

Escalation is required when:

- more than one product needs the same capability
- a product attempts to create balance-like, entitlement-like, or payout-like state
- a derived report begins to define business meaning
- a provider integration starts holding business-critical interpretation logic
- an off-chain domain appears to redefine chain truth
- a workflow or worker starts owning business meaning instead of execution state
- a product domain attempts to own governance-sensitive state
- a cache, search index, or reporting store becomes relied on as canonical truth
- a new capability appears to touch identity, billing, credits, payout, registry, or governance across multiple domains

The escalation decision must resolve:

- canonical owner
- canonical truth location
- mutation boundary
- read/derive/present rights
- execution owner if different
- governance rights if any
- whether the domain is platform, product, on-chain, reporting, control, or external

---

## Permission / Access Considerations

This matrix does not replace access-control specs, but it imposes the following rules:

- ownership does not imply universal read access
- product admin capability is not platform control-plane authority
- workspace scope is not equivalent to authorization
- authorization is not equivalent to entitlement
- admin/control pathways must not bypass canonical owner boundaries
- governance-sensitive actions require explicit elevated paths even when the initiating actor is already authenticated and authorized for ordinary work

---

## API / Contract Implications

This matrix requires downstream API specs to make ownership explicit.

At minimum:

- every mutation-capable API must identify the owning domain
- public, product, internal, admin, reporting, and chain-adjacent routes must not blur ownership
- non-owners must request mutation through owner-controlled commands rather than ambiguous convenience endpoints
- provider callbacks must terminate in normalization/verification boundaries before affecting canonical truth
- chain-adjacent interfaces must distinguish contract truth from off-chain orchestration truth

A downstream API specification that cannot answer “who owns this object and who is authoritative when values disagree” is incomplete.

---

## Event / Async Implications

This matrix requires downstream event and workflow designs to preserve ownership.

At minimum:

- owner domains or explicitly delegated owner-controlled components must publish canonical events
- execution-plane records remain execution state unless the owning domain explicitly elevates a result
- retries must not create duplicate business outcomes
- provider callbacks and chain event ingestion must resolve through normalized, idempotent owner-controlled transitions
- derived pipelines must tolerate lag without overwriting canonical truth

---

## Data Model / Storage Implications

This matrix requires downstream data-model specs to preserve owner boundaries.

At minimum:

- entity ownership must align with domain ownership
- cached copies, search indexes, analytics stores, and dashboards are derived unless explicitly elevated
- on-chain and off-chain records must remain distinguishable
- wallet-link records remain platform-owned mapping state, not token-balance truth
- reporting/publication stores must preserve source traceability
- schema migrations must preserve ownership continuity when domains move, split, or consolidate

---

## Security / Risk / Abuse Controls

This matrix is a security document as much as an architecture document.

If domains are not explicit:

- products may overreach into platform state
- reports may redefine business truth
- chain truth may be shadowed by stale caches or projections
- provider signals may mutate balances or entitlements incorrectly
- execution systems may silently become business owners
- governance-sensitive domains may be controlled through ordinary runtime paths

All downstream security, abuse-prevention, monitoring, and incident specs must preserve this matrix.

---

## Audit / Traceability Requirements

FUZE must be able to determine:

- which domain owns a category of truth
- which boundary accepted a mutation
- which component executed a step
- whether a visible output is canonical, execution, derived, or presentational
- how provider signals were normalized
- how off-chain decisions related to on-chain execution
- how approval/control actions influenced a sensitive change

This matrix exists partly to make those answers auditable.

---

## Failure Handling / Edge Cases

### Cached Product Copy of Platform State
A cached product copy is non-canonical and must reconcile to the platform owner.

### Reporting Mismatch With Chain Events
The chain domain remains canonical for chain-owned truth. Reporting must reconcile or mark lag.

### Provider Outage During Payment or Purchase Flow
External provider failure must not be treated as canonical commercial completion until verification and normalization are complete.

### Product Invents Product-Local Credits
This violates the matrix unless formally approved as a non-canonical restricted sub-ledger under the Platform Credits domain.

### Wallet-Aware Holder Tier Differs From Latest Chain State
Ethereum token domain remains canonical for balance truth. Wallet-aware participation may lag temporarily but must reconcile.

### Worker or Workflow Starts Holding Long-Lived Business Meaning
The owning business domain must absorb the canonical state, or the domain model must be refactored explicitly. Execution systems may not quietly become the owner.

### Derived Investor or Community Report Computes a Metric Incorrectly
The report is wrong, but it does not alter canonical platform or on-chain truth.

### Governance Approval Is Granted
Approval authorizes a sensitive action path but does not transfer ownership of the resulting business state.

---

## Migration / Compatibility / Supersession Considerations

- migrations must not silently transfer ownership from one domain to another without an explicit specification change
- compatibility layers may preserve older integrations temporarily, but must preserve canonical ownership semantics
- renamed or split domains must document continuity of authority, data ownership, and event ownership
- deprecated caches, reports, APIs, or shadow stores must not remain relied upon as hidden truth
- if older files or implementations contain looser ownership assumptions, this refined specification supersedes them within its scope

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
- identity/account/auth/session foundation docs
- workspace/access-control foundation docs

This specification directly governs or materially informs:

- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
- `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md`
- `API_ARCHITECTURE_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- product integration specs
- security, monitoring, audit, and runtime specs

---

## Explicitly Deferred Items

The following are intentionally deferred to downstream specifications:

- exact entity-to-domain mapping for every table and event
- exact API ownership per route
- exact event producer/consumer ownership by event name
- exact write-path and idempotency ownership by workflow step
- exact service-boundary implementation shape
- exact team or repository mapping by domain

These items refine the matrix. They do not replace it.

---

## Final Normative Summary

The FUZE domain ownership matrix establishes the implementation-grade domain map required for a platform-first ecosystem to remain coherent.

Platform domains own shared operating truth and shared economic/control truth. Product domains own product-specific objects and workflows. On-chain domains own only the truths explicitly committed to contract state. Reporting domains are usually derived and presentational. Control domains approve, restrict, or gate sensitive actions without replacing underlying business-domain ownership. External domains remain external until normalized.

Every meaningful domain must have one canonical owner, one canonical truth location, and one explicit rule for how other domains may read, write, derive, present, execute against, or govern it. If a future design cannot state those answers clearly, it is incomplete and must escalate before implementation.
