# FUZE Domain Ownership Matrix Specification

## Document Metadata
- **Document Name:** `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- **Document Type:** Canonical refined system specification
- **Status:** Active refined system spec
- **Version:** 1.0
- **Effective Date:** 2026-04-21
- **Last Updated:** 2026-04-21
- **Reviewed On:** 2026-04-21
- **Document Owner:** FUZE Platform Architecture (canonical owner); named individual owner is not explicitly specified in the retrieved governing materials
- **Approval Authority:** Not explicitly specified in the retrieved governing materials; constitutional approval authority remains governed by `REFINED_SYSTEM_SPEC_INDEX.md` and the active FUZE approval workflow
- **Review Cadence:** Not explicitly specified in the retrieved governing materials; SHOULD be reviewed whenever top-level domain boundaries, platform/product scope, economic rails, chain commitments, or governance-sensitive control paths materially change
- **Governing Layer:** Platform constitution / domain ownership matrix
- **Parent Registry:** `REFINED_SYSTEM_SPEC_INDEX.md`
- **Primary Audience:** Platform architecture, backend engineering, product engineering, API design, data engineering, security, operations, governance, finance systems, reporting, implementation-contract authors
- **Primary Purpose:** Define the canonical domain ownership matrix for FUZE by assigning major domains to explicit owners, truth locations, mutation boundaries, rights models, and escalation rules that downstream entity, API, event, workflow, reporting, and runtime specifications MUST preserve
- **Primary Upstream References:**
  - `REFINED_SYSTEM_SPEC_INDEX.md`
  - `DOCS_SPEC_INDEX.md`
  - `SYSTEM_SPEC_INDEX.md`
  - `API_SPEC_INDEX.md`
  - `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
  - `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
  - `PLATFORM_ARCHITECTURE_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
  - `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
- **Primary Downstream Dependents:**
  - `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
  - `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
  - `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
  - `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md`
  - `API_ARCHITECTURE_SPEC.md`
  - `PUBLIC_API_SPEC.md`
  - `INTERNAL_SERVICE_API_SPEC.md`
  - `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
  - `WORKFLOW_AND_AUTOMATION_SPEC.md`
  - `JOB_QUEUE_AND_WORKER_SPEC.md`
  - domain-specific identity, session, workspace, entitlement, credits, billing, payout, governance, reporting, and security specifications
- **Supersedes:** Earlier or weaker interpretations of FUZE ownership that permit soft co-ownership, product-local reinvention of shared primitives, execution-plane shadow ownership, reporting-owned business truth, or provider-input mutation without owner-controlled normalization
- **Superseded By:** None currently defined
- **Related Decision Records:** Not explicitly linked in the retrieved governing materials
- **Canonical Status Note:** This document is the canonical domain-reference ownership map beneath the top-level boundary and architecture documents. It operationalizes the FUZE ownership model at the major-domain level and is normative unless a higher constitutional source explicitly overrides it.
- **Implementation Status:** Normative architecture and implementation-contract source; downstream APIs, schemas, events, jobs, reports, controls, and migrations MUST align
- **Approval Status:** Draft refined canonical specification pending explicit approval workflow
- **Change Summary:** Consolidated the active refined ownership-matrix interpretation into one governing document; normalized metadata; tightened domain classes, rights semantics, conflict rules, escalation posture, matrix rows, control-plane treatment, projection/reporting constraints, and downstream implementation guardrails

## Title
FUZE Domain Ownership Matrix Specification

## Purpose
This specification defines the canonical domain ownership matrix for the FUZE ecosystem.

Its purpose is to convert the top-level boundary and architecture model into an implementation-usable ownership map that answers, for each material FUZE domain:
- who canonically owns the domain
- where canonical truth resides
- which boundary may accept mutation
- which other domains may read, derive, present, execute against, or govern the domain
- how ambiguous or contested ownership MUST be resolved
- which downstream API, event, storage, workflow, reporting, and control designs MUST preserve the ownership model

This document is intentionally narrower than `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md` and `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`, and intentionally broader than entity-level, endpoint-level, or service-module-level specifications. It is the normative bridge between constitutional ownership rules and downstream implementation-contract documents.

## Scope
This specification governs:
- the canonical inventory of major FUZE domains
- classification of domains across platform, product, on-chain, control, reporting, and external layers
- explicit owner assignment for each major domain
- canonical truth location rules for each domain
- default mutation, derivation, presentation, execution, and governance rights by domain
- default decision rules for assigning new services, entities, APIs, events, ledgers, workflows, reports, or adapters to a canonical domain
- escalation rules for ambiguous or contested ownership
- the matrix-level constraints that downstream domain, entity, API, workflow, event, security, reporting, and migration specifications MUST preserve

## Out of Scope
This specification does not define:
- every table, object, field, or schema family
- exact repository, package, or microservice boundaries
- endpoint-by-endpoint route ownership
- exact event producer and consumer lists
- exact queue topics, worker implementations, or broker topology
- exact smart-contract ABI, storage layout, or indexer design
- human org-chart accountability or staffing assignments
- detailed product business design for any single product

Those belong in downstream specifications, provided those specifications remain consistent with this document.

## Design Goals
The design goals of the FUZE domain ownership matrix are to:
1. give every major FUZE concern one clear canonical owner
2. make source-of-truth boundaries implementation-ready
3. prevent hidden overlap among platform, product, on-chain, reporting, control-plane, and external domains
4. preserve explicit separations among identity, session, workspace, authorization, entitlement, commercial state, credits, payout, token participation, reporting, and governance-sensitive control
5. support clean service, schema, API, event, and workflow decomposition
6. make future product and chain expansion predictable without fragmenting ownership
7. strengthen security, auditability, incident response, transparency, and migration safety through explicit ownership mapping
8. make ownership ambiguity visible early rather than discovered through production drift

## Non-Goals
This matrix is not intended to:
- create soft co-ownership as the default answer to ambiguity
- replace lifecycle specifications for narrower domains
- let products redefine shared platform primitives
- let reports, dashboards, exports, AI outputs, or caches become systems of record
- let provider input directly become canonical FUZE business truth without normalization
- let on-chain presence imply ownership of broader off-chain business meaning
- let execution infrastructure become the silent owner of the business objects it processes
- let governance-sensitive control authority hide inside ordinary runtime convenience paths

## Core Principles
### 1. One Domain, One Canonical Owner
Every material FUZE domain MUST have one canonical owner.

### 2. Ownership Is Multi-Dimensional
Ownership MUST be analyzed across canonical ownership, mutation ownership, execution ownership, presentation ownership, and governance ownership. These dimensions MAY interact, but they MUST NOT be conflated.

### 3. Platform-First for Shared Concerns
Any domain that is cross-product, identity-defining, economically shared, trust-sensitive, governance-sensitive, or required for future ecosystem expansion MUST default to platform ownership unless a narrower exception is formally specified.

### 4. Product Domains Are Extension Domains
Products MAY own product-local truth and workflows, but they MUST NOT redefine shared platform primitives.

### 5. On-Chain Ownership Is Explicit and Narrow
On-chain domains are canonical only for the truths explicitly committed to contract design and the relevant chain rail.

### 6. Reporting Is Usually Derived
Reporting, transparency, and publication domains MAY aggregate and present, but they MUST NOT redefine upstream truth unless a narrower specification explicitly elevates a narrowly defined reporting artifact.

### 7. External Systems Remain External
Provider-owned state remains external until FUZE verifies, normalizes, and records an internal consequence through the correct owner boundary.

### 8. Ambiguity Must Escalate
If canonical ownership cannot be stated clearly, the design is incomplete and MUST NOT be treated as production-grade.

## Canonical Definitions
### Domain
A bounded category of responsibility that owns a coherent class of truth, decisions, validation rules, state transitions, interfaces, and lineage requirements.

### Canonical Owner
The domain or contract layer authoritative for canonical fields, valid transitions, mutation acceptance, and owner-emitted events for a category of truth.

### Canonical Truth Location
The system layer or storage boundary where the authoritative record for a domain resides.

### Domain Contract
The explicit interaction and mutation boundary through which non-owners read from, request writes from, or execute against the owning domain.

### Derived Domain
A domain whose primary purpose is read-optimized aggregation, publication, reporting, search, or presentation rather than primary truth ownership.

### Control Domain
A domain whose primary responsibility is approval, restriction, remediation, emergency control, rollout control, risk containment, or governance-aware operational enablement.

### Domain Consumer
A non-owning domain, surface, service, or publication layer that reads, derives from, presents, or executes against an owning domain.

## Truth Class Taxonomy
This matrix MUST preserve the following truth classes wherever relevant:
1. **Semantic truth** — what a domain means and which owner governs it
2. **Policy truth** — which rules govern actions and interpretations in that domain
3. **Runtime truth** — what is happening during execution now
4. **Ledger / storage truth** — the durable authoritative record used by the owner domain
5. **Provider-input truth** — raw external signals before normalization
6. **Implementation-adapter truth** — boundary translation, verification, and normalization logic
7. **Execution truth** — jobs, retries, submissions, callback handling, and progression lineage
8. **Projection / reporting truth** — dashboards, analytics, registry artifacts, exports, and reports
9. **Presentation truth** — UX composition and explanatory renderings

These truth classes MUST NOT be collapsed into one undifferentiated ownership model.

## Architectural Position in the Spec Hierarchy
This document sits below:
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`

and above:
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
- `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md`
- `API_ARCHITECTURE_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `JOB_QUEUE_AND_WORKER_SPEC.md`
- domain-specific identity, access, billing, credits, payout, reporting, governance, and security specifications

This document does not override the top-level boundary model. It operationalizes that model at the major-domain reference level.

## System Boundaries
The matrix MUST be interpreted across the following ownership classes:
1. **Platform Core Domains** — reusable cross-product domains owned by the FUZE platform
2. **Product Domains** — bounded product-specific extension domains
3. **On-Chain Contract Domains** — domains whose canonical truth is committed to smart contracts and chain state
4. **Governance and Control Domains** — domains that own approvals, restrictions, remediations, rollouts, and trust-sensitive controls
5. **Reporting and Transparency Domains** — domains that own derived outputs, publication artifacts, and transparency/reporting views
6. **External Integration Domains** — domains representing raw provider state, provider execution environments, or provider-facing boundaries

## Adjacent Boundaries
This specification interacts with adjacent files as follows:
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md` governs top-level ownership, truth families, and mutation-owner interpretation
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md` governs ecosystem framing and top-level layer interpretation
- `PLATFORM_ARCHITECTURE_SPEC.md` governs the platform plane model and runtime interaction posture
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md` refines the entity, persistence, and data-model consequences of this matrix
- `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md` refines how products consume shared domains without redefining them
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md` refines how chain-adjacent domains divide off-chain and on-chain meaning
- identity/account/session and workspace/access-control foundation documents refine the access-related domains listed here

This document defines the major ownership map within which those specifications must remain coherent.

## Conflict Resolution Rules
When materials, implementations, or interpretations conflict, the following rules apply:
1. the active refined registry and higher constitutional materials win over narrower documents
2. `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md` wins on top-level truth-family ownership, mutation-owner interpretation, and cross-domain constitutional rules
3. `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md` wins on overall ecosystem framing and top-level layer interpretation
4. `PLATFORM_ARCHITECTURE_SPEC.md` wins on plane separation and architectural role of runtime surfaces
5. this document wins on major-domain classification, default owner assignment, rights semantics, and domain-reference interpretation
6. canonical source domains win over reports, surfaces, caches, queues, AI explanations, dashboards, exports, and public registry artifacts
7. on-chain committed truth wins only for the categories explicitly committed on-chain; broader off-chain policy, accounting, orchestration, entitlement, reporting, and business meaning remain off-chain unless explicitly committed
8. provider input never wins by itself; verified and normalized owner-controlled consequences win
9. governance or control-plane approval may authorize or constrain a change, but the resulting business state MUST still be written through the correct owner domain
10. when ambiguity remains, FUZE MUST choose the more conservative architecture-consistent interpretation and escalate the ambiguity into downstream refinement or decision recording

## Default Decision Rules
When ambiguity exists and no narrower approved exception is available, FUZE MUST default to the following:
1. cross-product capabilities default to platform ownership
2. product-local workflows default to product ownership only if they do not redefine a shared primitive
3. identity defaults to canonical account ownership, not provider, product, workspace, or wallet ownership
4. authenticated runtime state defaults to the auth/session domain, not frontend or product-local ownership
5. authorization defaults to the access-control domain, not login-success logic or UI flags
6. entitlement defaults to a distinct eligibility/capability layer, not hidden role flags or product-local switches
7. external commercial state defaults to provider-input status until normalized into FUZE commercial consequences
8. credits, billing, payout, reserve, registry, token balance, and reporting remain separate economic and trust concepts even when operationally linked
9. queues, jobs, and schedulers default to execution-state ownership, not business-domain ownership
10. dashboards, exports, reports, search indexes, and public registries default to derived-state ownership, not source ownership
11. if no explicit owner can be named, the design is incomplete and MUST NOT be treated as production-grade

## Roles / Actors / Entities
### Human Actors
- end users
- workspace members and workspace owners
- product operators
- support and remediation operators
- security reviewers
- finance and treasury reviewers
- governance or approval actors
- public, partner, community, and investor readers of approved trust surfaces

### System Actors
- platform domain services
- product domain services
- authorization and entitlement evaluators
- workflow and worker systems
- provider adapters and normalization systems
- chain-adjacent services
- reporting and registry publishers
- control-plane systems

### Core Entity Families Influenced by This Matrix
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
The FUZE ownership model is domain-first and owner-explicit.

Each major domain MUST define:
- canonical owner
- canonical truth location
- mutation boundary
- allowed non-owner rights
- whether governance-sensitive control applies
- whether the domain is primary truth, policy truth, execution truth, or derived/publication truth

Domains MAY interact heavily, but they MUST NOT be treated as co-owning the same business meaning without a formally defined narrower exception.

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
- require reason codes and operator lineage

Control authority does not transfer canonical ownership of the underlying business domain.

### Execution Authority
Execution systems MAY coordinate work, but they do not become the owner of the business meaning of the work they process.

## State Model
Each domain MUST distinguish among:
- canonical state owned by the domain
- policy state constraining the domain
- execution state used to process work involving the domain
- derived state used for reporting, registry publication, search, analytics, or presentation
- provider-input state used as external input before normalization

A downstream implementation MUST NOT silently collapse these state classes.

## Lifecycle / Workflow Model
At the domain-reference level, FUZE domain lifecycles follow this general pattern:
1. a domain accepts or rejects a mutation through its explicit contract
2. if external signals are involved, provider input remains external until normalization succeeds
3. if deferred work is required, execution entities track progression without becoming the owner of business truth
4. owner-controlled state transitions emit owner-governed events or lineage artifacts
5. derived domains may publish reporting, registry, search, or analytics outputs from canonical sources
6. control-plane actions may constrain, block, or remediate the lifecycle but do not replace domain ownership

## Invariants
1. every material FUZE domain MUST have one canonical owner
2. non-owners MUST NOT mutate canonical truth except through an owner-controlled contract
3. products MUST NOT create alternate identity, session, workspace, authorization, entitlement, credits, payout, registry, or governance primitives
4. provider-originated raw state MUST NOT directly become canonical FUZE business truth
5. reporting and publication outputs MUST remain downstream to canonical sources unless explicitly elevated by a narrower specification
6. chain truth MUST remain explicit and narrow to what is committed on-chain
7. execution systems MUST NOT silently acquire long-lived business ownership
8. governance-sensitive overrides MUST be explicit, bounded, reason-coded, and auditable

## Functional Rules
### Domain Classification Rules
Every new service, table, object, workflow, API, event family, ledger, report, or adapter MUST be assigned to one of the ownership classes in this document before production adoption.

### Owner-First Mutation Rules
Only the canonical owner or an explicitly delegated owner-controlled component may accept writes that change canonical domain state.

### Consumer Rules
Non-owning domains MAY receive explicit rights such as read, derive, present, execute-against, or govern, but those rights MUST be named and bounded.

### Derived-Domain Rules
Derived domains MAY aggregate and present; they MUST NOT redefine upstream state or close reconciliation gaps by overwriting canonical owners.

### Control-Domain Rules
Control domains MAY restrict, approve, or remediate; they MUST NOT silently mutate business truth outside owner-controlled pathways.

## Permission / Access Considerations
Domain ownership and authorization are related but separate.
- identity does not imply authorization ownership
- workspace scope does not imply permission truth ownership
- product access does not imply product ownership of platform capabilities
- admin capability does not imply unrestricted cross-domain mutation rights

Downstream access-control specifications MUST preserve domain ownership boundaries when granting operational permissions.

## Entitlement Considerations
Entitlement is a distinct domain concern. Product capability exposure MAY depend on entitlement, but entitlement truth MUST remain separately owned from identity, authorization, billing, and product-local runtime decisions.

## API / Contract Implications
This matrix requires downstream API specifications to make ownership explicit.

At minimum:
- every mutation-capable API MUST identify the owning domain
- public, product, internal, admin, reporting, and chain-adjacent routes MUST NOT blur ownership
- non-owners MUST request mutation through owner-controlled commands rather than ambiguous convenience endpoints
- provider callbacks MUST terminate in normalization and verification boundaries before affecting canonical truth
- chain-adjacent interfaces MUST distinguish contract truth from off-chain orchestration truth
- idempotency obligations MUST be assigned to the owner boundary or an explicitly delegated owner-controlled component

A downstream API specification that cannot answer “who owns this object and who is authoritative when values disagree” is incomplete.

## Event / Async Implications
This matrix requires downstream event and workflow designs to preserve ownership.

At minimum:
- owner domains or explicitly delegated owner-controlled components MUST publish canonical events
- execution-plane records remain execution lineage unless the owning domain explicitly elevates a result
- retries MUST NOT create duplicate business outcomes
- provider callbacks and chain-event ingestion MUST resolve through normalized, idempotent owner-controlled transitions
- derived pipelines MUST tolerate lag without overwriting canonical truth
- trace identifiers SHOULD connect owner decisions, execution progression, and publication outputs

## Data Model / Storage Implications
This matrix requires downstream data-model specifications to preserve owner boundaries.

At minimum:
- entity ownership MUST align with domain ownership
- cached copies, indexes, dashboards, registries, and analytics stores are derived unless explicitly elevated
- on-chain and off-chain records MUST remain distinguishable
- wallet-link records remain platform-owned mapping state, not token-balance truth
- reporting/publication stores MUST preserve source traceability
- schema migrations MUST preserve ownership continuity when domains move, split, or consolidate

## Read Model / Projection / Reporting Rules
1. reports, dashboards, exports, search indexes, AI explanations, and public registry artifacts are downstream derived artifacts by default
2. a derived artifact MAY be canonical only for its own bounded publication/reporting domain, not for the upstream business fact it summarizes
3. read models MAY denormalize for performance, but they MUST preserve source references sufficient for reconciliation and audit
4. projections MUST NOT silently accept writes that bypass the canonical owner
5. lag, reconciliation gaps, or stale projections MUST be visible rather than hidden

## Security / Risk / Abuse Controls
This matrix is a security and abuse-prevention document as much as an architecture document.

If domains are not explicit:
- products may overreach into platform state
- reports may redefine business truth
- chain truth may be shadowed by stale caches or projections
- provider signals may mutate balances or entitlements incorrectly
- execution systems may silently become business owners
- governance-sensitive domains may be controlled through ordinary runtime paths

All downstream security, abuse-prevention, monitoring, and incident specifications MUST preserve this matrix.

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

## Audit / Traceability Requirements
FUZE MUST be able to determine:
- which domain owns a category of truth
- which boundary accepted a mutation
- which component executed a step
- whether a visible output is canonical, execution, derived, or presentational
- how provider signals were normalized
- how off-chain decisions related to on-chain execution where relevant
- how approvals, restrictions, or overrides influenced a sensitive change
- which policy version, reason code, operator identity, and trace identifier applied where relevant

## Failure Handling / Edge Cases
### Cached Product Copy of Platform State
A cached product copy is non-canonical and MUST reconcile to the platform owner.

### Reporting Mismatch With Chain Events
The chain domain remains canonical for chain-owned truth. Reporting MUST reconcile, disclose lag, or mark uncertainty.

### Provider Outage During Payment or Purchase Flow
External provider failure MUST NOT be treated as canonical commercial completion until verification and normalization complete.

### Product Invents Product-Local Credits
This violates the matrix unless a narrower approved specification explicitly establishes a bounded non-canonical sub-ledger governed by the platform credits domain.

### Wallet-Aware Tier Differs From Latest Chain State
The token domain remains canonical for token-balance truth. Wallet-aware participation context MAY lag temporarily but MUST reconcile.

### Worker or Workflow Starts Holding Long-Lived Business Meaning
The owning business domain MUST absorb the canonical state, or the architecture MUST be refactored explicitly. Execution systems MAY NOT quietly become the owner.

### Derived Investor or Community Report Computes a Metric Incorrectly
The report artifact is wrong, but it does not alter canonical platform or chain truth.

### Governance Approval Is Granted
Approval authorizes or constrains a sensitive action path but does not transfer ownership of the resulting business state.

## Operational Considerations
- operational runbooks SHOULD identify domain owners for every major mutation-capable surface
- incident response SHOULD classify impact by affected domain owner and truth class
- remediation pathways MUST preserve owner-controlled correction semantics
- degraded mode MUST prefer explicit holds, read-only posture, or marked uncertainty over ambiguous mutation
- observability SHOULD tag logs, traces, jobs, and events with domain identifiers and owner lineage where feasible

## Migration / Compatibility / Supersession Considerations
- migrations MUST NOT silently transfer ownership from one domain to another without an explicit specification change
- compatibility layers MAY preserve older integrations temporarily, but they MUST NOT preserve shadow ownership as the long-term model
- if a product-local capability becomes cross-product and foundational, it SHOULD be considered for elevation into a platform domain
- if older materials imply looser ownership rules, this refined specification supersedes them within its scope
- any domain split, merge, or deprecation MUST preserve traceability for prior canonical records and clear supersession lineage

## Implementation-Contract Guardrails
Downstream implementation contracts MUST preserve all of the following:
- one explicit canonical owner per material domain
- explicit mutation boundaries and idempotency posture
- explicit distinction among canonical, execution, derived, provider-input, and presentation state
- explicit control-plane treatment for sensitive overrides
- explicit source references in projections and publication artifacts
- explicit tie-break rules when provider, chain, cache, report, and owner state disagree
- explicit audit lineage for non-routine corrections, overrides, or governance-sensitive actions

Downstream implementations MUST NOT optimize away ownership clarity for convenience.

## Downstream Execution Staging
This document should be consumed by downstream authors in the following order:
1. entity and data-model specifications
2. product-boundary and on-chain/off-chain responsibility refinements
3. internal/public API and event specifications
4. workflow, job, reporting, security, monitoring, and migration specifications
5. product integration and control-plane implementation documents

## Required Downstream Specs / Contract Layers
This matrix materially informs:
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
- `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md`
- `API_ARCHITECTURE_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `JOB_QUEUE_AND_WORKER_SPEC.md`
- identity, session, workspace, access-control, entitlement, billing, credits, payout, audit, security, reporting, and governance specifications

## Canonical Domain Inventory
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

## Canonical Domain Ownership Matrix
| Domain | Class | Canonical Owner | Canonical Truth Location | Default Mutation Boundary | Default Non-Owner Rights | Governance Relevance | Notes |
|---|---|---|---|---|---|---|---|
| Identity and Account | Platform Core | Platform | Platform identity/account records | Identity domain APIs/services | Read, Execute Against, limited Present | High | Cross-product identity root |
| Auth / Session / Linked Login | Platform Core | Platform | Platform auth/session stores | Auth/session domain APIs/services | Read, Execute Against, limited Present | High | Session truth is access state, not identity |
| Workspace and Organization | Platform Core | Platform | Platform workspace/org stores | Workspace domain APIs/services | Read, Execute Against, limited Present | Medium | Shared collaboration and billing scope |
| Role / Permission / Access Control | Platform Core | Platform | Platform authorization stores | Access-control domain APIs/services | Read, Execute Against | High | Platform remains canonical for shared authorization |
| Wallet-Aware Participation | Platform Core | Platform | Platform wallet-link mapping plus governed chain reads | Wallet-aware domain APIs/services | Read, Execute Against, limited Present | Medium | Mapping/context layer, not token-balance truth |
| Commerce and Billing | Platform Core | Platform | Platform billing/commercial state | Billing domain APIs/services | Read, Execute Against, Derive | High | Cross-product commercial rules |
| Platform Credits Policy and Off-Chain Credits Orchestration | Platform Core | Platform | Platform credits policy and off-chain credits records | Credits domain APIs/services | Read, Execute Against, Derive | High | Policy and orchestration remain off-chain platform-owned |
| Base Credits Ledger Commitment | On-Chain Contract | Base contract layer | Base chain state | Contract calls via governed chain-adjacent pathways | Read, Derive, Present, Execute Against | High | Canonical only where credits are formally committed on-chain |
| Invoicing and Receipts | Platform Core | Platform | Platform financial records | Invoicing domain APIs/services | Read, Present, Derive | Medium | Downstream to billing truth |
| Payment Verification and Normalization | Platform Core / Integration | Platform | Platform normalized payment records | Payment normalization APIs/services | Read, Execute Against, Derive | High | Raw payments remain external until normalized |
| Entitlement and Capability Gating | Platform Core | Platform | Platform entitlement/capability state | Entitlement domain APIs/services | Read, Execute Against | High | Distinct from authorization and billing truth |
| AI Orchestration | Platform Core | Platform | Platform orchestration state | Shared orchestration APIs/services | Read, Execute Against | Medium | Shared AI lifecycle and policy-bounded orchestration |
| Model Routing and Context | Platform Core | Platform | Platform routing/context policy records | Routing/context services | Read, Execute Against | Medium | Owns model-selection and context-binding policy |
| AI Usage Metering | Platform Core | Platform | Platform metering records | Metering services | Read, Derive, Execute Against | High | Distinct from orchestration and billing truth |
| Workflow and Automation | Platform Core | Platform | Platform workflow definitions and run state | Workflow APIs/services | Read, Execute Against | Medium | Reusable automation primitives |
| Job Queue and Worker Coordination | Platform Core / Execution | Platform | Execution-plane scheduling and retry records | Worker/job coordination services | Read, Execute Against | Medium | Owns execution lineage, not all business meaning |
| Audit Log and Activity | Platform Core | Platform | Platform audit records | Audit domain ingestion/boundaries | Read, Derive, Present | High | Owns lineage for meaningful state change history |
| Transparency Reporting Coordination | Platform Core / Reporting | Platform | Platform reporting coordination records | Reporting coordination services | Read, Derive, Execute Against | Medium | Prepares reporting, does not replace source truth |
| Public Registry Publication | Platform Core / Reporting | Platform | Platform publication records and approved public mappings | Registry publication services | Read, Derive, Present | High | Publication layer, not contract ownership |
| Payout Orchestration | Platform Core | Platform | Off-chain payout-cycle orchestration records | Payout orchestration services | Read, Execute Against, Derive | High | Distinct from on-chain claim execution truth |
| Integration Adapter Layer | Platform Core / Integration | Platform | Adapter configuration and normalization lineage | Adapter services | Execute Against, Read | High | Mediates trust boundaries without owning all business facts |
| Control Plane / Admin Controls / Governance-Aware Operations | Governance and Control | Platform control domain | Control records, approvals, holds, rollout state | Governed control-plane pathways | Govern, limited Execute Against | Critical | Governs sensitive actions without replacing domain owners |
| QTB Product Domain | Product | Product domain | Product-local stores | Product-owned APIs/services | Read, Derive, Present, Execute Against platform contracts | Medium | Owns only product-local truth |
| AIMM Product Domain | Product | Product domain | Product-local stores | Product-owned APIs/services | Read, Derive, Present, Execute Against platform contracts | Medium | Same bounded-extension rule |
| ZAGA Product Domain | Product | Product domain | Product-local stores | Product-owned APIs/services | Read, Derive, Present, Execute Against platform contracts | Medium | Same bounded-extension rule |
| AIE Product Domain | Product | Product domain | Product-local stores | Product-owned APIs/services | Read, Derive, Present, Execute Against platform contracts | Medium | Same bounded-extension rule |
| HerHelp Product Domain | Product | Product domain | Product-local stores | Product-owned APIs/services | Read, Derive, Present, Execute Against platform contracts | Medium | Same bounded-extension rule |
| Botmad Product Domain | Product | Product domain | Product-local stores | Product-owned APIs/services | Read, Derive, Present, Execute Against platform contracts | Medium | Same bounded-extension rule |
| Ethereum Token Domain | On-Chain Contract | Ethereum contract layer | Ethereum chain state | Governed chain interactions | Read, Derive, Present, Execute Against | High | Canonical for token balance and transfer truth |
| Base Payout Execution Domain | On-Chain Contract | Base contract layer | Base payout contract state | Governed chain interactions | Read, Derive, Present, Execute Against | High | Canonical for funded payout-cycle execution truth |
| Reserve and Vault Contract Domains | On-Chain Contract | Contract layer | Contract state | Governed treasury/control pathways | Read, Derive, Present, Execute Against | Critical | Own reserve constraints and contract-native restrictions |
| Governance-Controlled Contract Role Domains | On-Chain Contract / Control | Contract layer | Contract role state and timelock/multisig state | Governed control pathways | Read, Derive, Present, Execute Against | Critical | Contract-native control truth |
| Public Contract Registry | Reporting and Transparency | Reporting/publication domain | Published registry artifacts | Publication workflows only | Read, Derive, Present | High | Derived public mapping of contract identities |
| Public Wallet Registry | Reporting and Transparency | Reporting/publication domain | Published registry artifacts | Publication workflows only | Read, Derive, Present | High | Public-safe wallet role presentation |
| Payout Ledger | Reporting and Transparency | Reporting/publication domain | Published payout-ledger artifacts | Publication workflows only | Read, Derive, Present | High | Reporting artifact; not payout execution owner |
| Investor Reporting | Reporting and Transparency | Reporting domain | Stakeholder-facing derived reports | Publication workflows only | Read, Derive, Present | High | Derived reporting only |
| Community Reporting | Reporting and Transparency | Reporting domain | Public/community-facing derived reports | Publication workflows only | Read, Derive, Present | High | Derived reporting only |
| Derived Transparency Views | Reporting and Transparency | Reporting domain | Derived views and dashboards | Publication/reporting workflows only | Read, Derive, Present | Medium | Must preserve source traceability |
| Stripe Payment Domain | External Integration | External provider | Stripe ecosystem state | N/A to FUZE-owned truth | Execute Against, Read provider input | High | Raw provider state only |
| Stablecoin Payment Rail Domain | External Integration | External rail/provider | Provider/rail state | N/A to FUZE-owned truth | Execute Against, Read provider input | High | Requires normalization |
| Telegram Stars Rail Domain | External Integration | External provider | Telegram Stars ecosystem state | N/A to FUZE-owned truth | Execute Against, Read provider input | Medium | Raw purchase environment |
| Apple Billing Domain | External Integration | External provider | App Store purchase state | N/A to FUZE-owned truth | Execute Against, Read provider input | High | Raw app-store state |
| Google Billing Domain | External Integration | External provider | Play billing state | N/A to FUZE-owned truth | Execute Against, Read provider input | High | Raw app-store state |
| Wallet Client Domain | External Integration | External client | Local wallet behavior and client transaction state | N/A to FUZE-owned truth | Execute Against, Read provider input | Medium | Owns signing UX, not token meaning |
| RPC / Indexing Provider Domain | External Integration | External provider | Transport/indexed chain access state | N/A to FUZE-owned truth | Execute Against, Read provider input | High | Access service, not canonical policy truth |
| AI Model Provider Domain | External Integration | External provider | Provider model-execution state | N/A to FUZE-owned truth | Execute Against, Read provider input | Medium | Provider runtime, not FUZE orchestration meaning |
| Cloud / Runtime Provider Domain | External Integration | External provider | Infrastructure service state | N/A to FUZE-owned truth | Execute Against only | Medium | Does not own FUZE application truth |

## Canonical Examples / Anti-Examples
### Canonical Examples
- a workspace membership record belongs to the workspace domain even if a product consumes it heavily
- a normalized payment record belongs to the payment normalization and commercial domains, not to Stripe or an app-store callback handler
- a product-specific object belongs to its product domain if it does not redefine a shared primitive
- token balance truth belongs to the contract domain, while wallet-aware participation context belongs to the platform mapping/context domain
- a public contract registry artifact belongs to the reporting/publication domain as a publication object while remaining downstream to contract and platform sources

### Anti-Examples
- a product creates its own credits balance and treats it as spend-authoritative across the platform
- a dashboard becomes the place operators edit business truth because it is convenient
- a worker record becomes the only place a long-lived business state is tracked
- an inbound provider callback directly mutates entitlement without owner-controlled normalization
- a chain indexer output is treated as broader off-chain policy truth without an owner-controlled interpretation boundary

## Dependencies / Cross-Spec Links
This specification depends on:
- `REFINED_SYSTEM_SPEC_INDEX.md`
- `DOCS_SPEC_INDEX.md`
- `SYSTEM_SPEC_INDEX.md`
- `API_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
- `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
- `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`

This specification directly governs or materially informs:
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
- `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md`
- shared API, event, workflow, security, monitoring, audit, reporting, and product integration specifications

## Explicitly Deferred Items
The following are intentionally deferred to downstream specifications:
- entity-by-entity schema ownership for every table and field
- endpoint-by-endpoint API ownership and payload design
- event payload structure and exact producer/consumer topology
- exact job orchestration and retry implementation details
- exact repository/module boundaries for each domain
- product-by-product detailed object inventories
- exact migration playbooks when domains split, merge, or move

## Final Normative Summary
FUZE MUST assign every major domain one canonical owner, one canonical truth location, one mutation boundary, and one explicit rights model for non-owners. Platform domains own shared cross-product primitives. Product domains own bounded product-local truth only. On-chain domains own only what is explicitly committed on-chain. Reporting and transparency domains primarily own derived publication artifacts. External providers remain external until their signals are normalized into FUZE-owned consequences. Execution systems, dashboards, AI outputs, and control-plane tools may coordinate, present, or constrain activity, but they do not become the owner of upstream business meaning unless a narrower approved specification explicitly says so.

## Quality Gate Checklist
- [x] Canonical owner is explicit for every major truth family handled by this document
- [x] Mutation boundaries are explicit at the matrix level
- [x] Adjacent boundaries are explicit
- [x] Truth classes are explicit
- [x] Conflict-resolution rules are explicit
- [x] Default decision rules are explicit where ambiguity is likely
- [x] Non-canonical patterns are called out clearly
- [x] Operator/admin override posture is bounded and audited
- [x] Read-model, cache, reporting, and projection rules are explicit
- [x] On-chain versus off-chain ownership is explicit at the matrix level
- [x] Failure and degraded-mode interpretation is explicit
- [x] Downstream implementation guardrails are explicit
- [x] Dependencies and downstream impacts are explicit
- [x] Non-goals and deferred items are explicit
- [x] The document is strong enough to guide backend, API, data, runtime, and reporting implementation without inventing contradictory semantics
