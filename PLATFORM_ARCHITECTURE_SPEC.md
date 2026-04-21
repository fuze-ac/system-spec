# FUZE Platform Architecture Specification

## Document Metadata
- **Document Name:** `PLATFORM_ARCHITECTURE_SPEC.md`
- **Document Type:** Canonical refined system specification
- **Status:** Active refined system spec
- **Version:** 1.0
- **Effective Date:** 2026-04-21
- **Last Updated:** 2026-04-21
- **Reviewed On:** 2026-04-21
- **Document Owner:** FUZE Platform Architecture (canonical owner); named individual owner not explicitly specified in the retrieved source material
- **Approval Authority:** Not explicitly specified in the retrieved source material; constitutional approval authority remains governed by `REFINED_SYSTEM_SPEC_INDEX.md` and the active FUZE approval workflow
- **Review Cadence:** Not explicitly specified in the retrieved source material; SHOULD be reviewed whenever top-level planes, service-domain boundaries, chain-adjacent posture, control-plane posture, product-extension rules, or cross-domain execution rules materially change
- **Governing Layer:** Platform constitution / architecture
- **Parent Registry:** `REFINED_SYSTEM_SPEC_INDEX.md`
- **Primary Audience:** Platform architecture, backend engineering, product engineering, API design, contracts engineering, data engineering, security, operations, governance, finance systems, reporting, implementation-contract authors
- **Primary Purpose:** Define the canonical shared-core architecture of FUZE, including platform planes, service-domain structure, runtime interaction model, control-plane boundaries, product-extension architecture, chain-adjacent coordination posture, and implementation-contract guardrails that all downstream specifications and implementations MUST preserve
- **Primary Upstream References:**
  - `REFINED_SYSTEM_SPEC_INDEX.md`
  - `DOCS_SPEC_INDEX.md`
  - `SYSTEM_SPEC_INDEX.md`
  - `API_SPEC_INDEX.md`
  - `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
  - `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
  - `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
  - `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
  - `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
  - `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
  - `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
- **Primary Downstream Dependents:**
  - `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
  - `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
  - `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
  - `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
  - `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md`
  - `API_ARCHITECTURE_SPEC.md`
  - `PUBLIC_API_SPEC.md`
  - `INTERNAL_SERVICE_API_SPEC.md`
  - `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
  - `IDEMPOTENCY_AND_VERSIONING_SPEC.md`
  - `WORKFLOW_AND_AUTOMATION_SPEC.md`
  - `JOB_QUEUE_AND_WORKER_SPEC.md`
  - `AI_ORCHESTRATION_SPEC.md`
  - `MODEL_ROUTING_AND_CONTEXT_SPEC.md`
  - `AI_USAGE_METERING_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - `MONITORING_ALERTING_AND_INCIDENT_RESPONSE_SPEC.md`
  - `DEPLOYMENT_AND_RUNTIME_OPERATIONS_SPEC.md`
  - product integration specifications
- **Supersedes:** Earlier or weaker interpretations of FUZE architecture that treat the platform as a loose portfolio of product backends, conflate planes into one undifferentiated runtime, let surfaces or reporting layers become truth owners, treat provider callbacks as canonical without normalization, or allow admin/control pathways to silently bypass domain ownership
- **Superseded By:** None currently defined
- **Related Decision Records:** Not explicitly linked in the retrieved source material
- **Canonical Status Note:** This document is the canonical architecture-bridge document between the top-level boundary/ownership model and the narrower domain, API, event, workflow, data, runtime, and product specifications. It governs plane separation, service-domain interaction posture, shared-core platform structure, and product-extension discipline.
- **Implementation Status:** Normative architecture source; downstream services, APIs, events, jobs, schemas, storage models, observability, runbooks, and product integrations MUST align
- **Approval Status:** Draft refined canonical specification pending explicit approval workflow
- **Change Summary:** Consolidated the active refined platform-architecture interpretation into one production-grade governing document; normalized metadata; clarified plane model, architectural roles of FUZE runtime surfaces, shared service-domain composition, synchronous versus asynchronous acceptance rules, integration and chain-adjacent posture, control-plane boundaries, storage and reporting discipline, and downstream implementation-contract guardrails

## Title
FUZE Platform Architecture Specification

## Purpose
This specification defines the canonical high-level platform architecture of FUZE.

Its purpose is to make explicit:
- how FUZE is structured as a platform-first, multi-product ecosystem with one shared operating foundation
- which architectural planes exist inside the FUZE off-chain platform boundary
- how shared platform service domains are organized and how they collaborate without erasing ownership boundaries
- how product-extension domains consume platform primitives without becoming shadow platforms
- how synchronous requests, async execution, provider normalization, chain-adjacent coordination, reporting/publication, and control-plane actions must interact
- what architectural posture downstream API, event, data, workflow, security, observability, and product specifications MUST preserve
- which patterns are forbidden because they would produce architectural drift, hidden ownership, or implementation-contract inconsistency

This specification is intentionally governing rather than descriptive. It is not merely a deployment diagram, codebase tour, or implementation memo. It defines the durable architectural model that narrower FUZE specifications and runtime implementations MUST inherit.

## Scope
This specification governs:
- the canonical shared-core architecture of the FUZE platform
- the plane model across experience/edge, application, execution, integration, reporting, control, and adjacent on-chain layers
- the architectural role of `fuze-backend-api`, `fuze-frontend-webapp`, `fuze-frontend-admin`, `fuze-contracts`, `fuze-public-registry`, and future bounded SDK/public-consumer surfaces
- the shared service-domain model for identity, auth/session, workspace, access, entitlement, commerce, credits, AI, workflow, audit, reporting, public registry, payout orchestration, governance-aware control, and integration normalization
- the product-extension architecture model
- the architectural posture for synchronous acceptance, async progression, normalization of external signals, and chain-adjacent execution
- the architectural implications for APIs, events, jobs, data/storage, reporting, security, auditability, failure handling, operations, and migration

## Out of Scope
This specification does not define:
- full schema detail for every domain entity or table
- endpoint-by-endpoint API routes or payloads
- exact queue technologies, event-bus technologies, or broker choices
- exact modular-monolith versus service-split rollout timing
- exact cloud, network, Kubernetes, or infrastructure-vendor configuration
- exact smart-contract ABI, storage layout, or chain indexer internals
- exact frontend component trees, UX structure, or client package boundaries
- exact product-local data models where they do not affect shared architecture semantics
- human org-chart staffing or repository ownership assignments

Those belong in downstream specifications and implementation documents, provided they remain consistent with this architecture.

## Design Goals
The design goals of the FUZE platform architecture are to:
1. preserve FUZE as one coherent platform rather than a collection of disconnected product stacks
2. make shared cross-product capabilities first-class platform domains
3. preserve explicit ownership and plane separation under growth, automation, and hybrid on-chain/off-chain execution
4. support both synchronous user-facing behavior and long-running asynchronous behavior without losing traceability or semantic clarity
5. support provider integrations and chain-connected operations through explicit boundary layers rather than scattered direct calls
6. provide a reusable product-extension model for current and future FUZE products
7. ensure reporting, registry, transparency, and public-trust surfaces remain downstream to canonical source domains
8. make control-plane and governance-sensitive intervention explicit, bounded, reason-coded, and auditable
9. enable downstream implementation-contract documents to derive stable architectural constraints without reinterpreting the platform model
10. support migration, operational resilience, and future expansion without forcing architectural reinvention

## Non-Goals
This architecture is not intended to produce:
- one backend per product with duplicated shared primitives
- frontend-owned or dashboard-owned business truth
- workflow, worker, or AI runtime components treated as owners of upstream business meaning
- reporting systems or registries that silently become systems of record
- provider callbacks that directly mutate canonical platform truth without normalization
- chain presence that automatically replaces off-chain policy, accounting, or workflow ownership
- admin or support tooling that bypasses ownership rules through convenience shortcuts
- “shared ownership” as the default answer to ambiguity

If there is tension between convenience and architecture-preserving explicitness, the architecture-preserving interpretation wins.

## Core Principles
### 1. Platform-First Shared-Core Principle
Capabilities that are identity-defining, cross-product, economically shared, control-sensitive, reporting-sensitive, or required for future ecosystem expansion MUST remain platform-owned.

### 2. Ownership-Preserving Architecture Principle
Architecture MUST preserve canonical ownership rather than hide it behind technical convenience layers.

### 3. Plane Separation Principle
Experience, application, execution, integration, reporting, and control behavior MUST remain distinguishable even when implemented inside a single broader backend estate.

### 4. Product Extension Principle
Products extend the platform through bounded domains. They consume platform primitives; they do not redefine them.

### 5. Chain-Adjacent Discipline Principle
On-chain interaction is an explicit architectural lane. It does not become the default owner of off-chain business policy, reporting meaning, or workflow logic.

### 6. Derived-Not-Canonical Reporting Principle
Reporting, public registry, transparency, and analytics outputs are downstream derived or publication-oriented layers unless a narrower specification explicitly elevates a narrowly scoped reporting artifact.

### 7. Governance-Aware Control Principle
Sensitive operations require explicit control-plane or governance-aware pathways rather than hidden ordinary-runtime shortcuts.

### 8. Operational Clarity Principle
Failures, retries, degraded modes, and remediation MUST preserve visibility into which plane and which domain own the affected truth.

### 9. Integration Normalization Principle
External-provider and chain-originating signals MUST cross an explicit normalization boundary before they influence FUZE canonical state.

### 10. Future-Safe Extensibility Principle
The architecture MUST support new products, new providers, new chain rails, and new publication surfaces without changing the platform’s core semantic model.

## Canonical Definitions
### Shared Platform Core
The reusable FUZE operating foundation that supports all products and shared ecosystem capabilities.

### Product Extension Domain
A bounded product-specific architecture domain that owns product-local workflows, data, outputs, and experiences while consuming shared platform primitives.

### Experience / Edge Layer
The entry layer for first-party surfaces and bounded public consumers that initiates requests and renders state without owning durable business truth.

### Application Plane
The primary request/response and domain-service plane where canonical off-chain business truth is validated, accepted, created, mutated, and read.

### Execution Plane
The async and long-running execution layer that coordinates jobs, retries, step progression, and deferred work without replacing the owner of upstream business truth.

### Integration Plane
The boundary layer for provider adapters, inbound callbacks, outbound provider calls, external identity handoffs, wallet mediation, chain-adjacent coordination, and normalization of non-FUZE-originated signals.

### Reporting Plane
The derived-output and publication layer for analytics, transparency, public registry publication, dashboards, exported views, and approved reports.

### Control Plane
The operational and policy-control layer that governs approvals, restrictions, rollouts, emergency controls, incident-response execution controls, provider configuration, and governance-aware operational actions.

### Chain-Adjacent Service
An off-chain service or boundary that coordinates with on-chain contracts while preserving the distinction between contract-native truth and off-chain policy, accounting, or orchestration truth.

### Product Surface
A first-party user-facing product experience or product-specific surface built on platform and product domains.

### Accepted Async Intent
A canonical owner-approved record that deferred work should proceed. It is not equivalent to final business success.

## Truth Class Taxonomy
This architecture MUST preserve the following truth classes wherever relevant:
1. **Semantic truth** — what a concept means and which domain owns that meaning
2. **Policy truth** — what rules govern action, restriction, eligibility, or interpretation
3. **Runtime truth** — what is happening during execution now
4. **Ledger / storage truth** — the durable authoritative record of a domain
5. **Provider-input truth** — unnormalized external state
6. **Implementation-adapter truth** — translation, verification, mediation, or normalization state at a boundary
7. **Execution truth** — job, workflow, retry, submission, and progression state
8. **Projection / reporting truth** — derived analytics, dashboards, public registry artifacts, and reports
9. **Presentation truth** — UX composition and wording state

These truth classes MUST NOT be collapsed into one undifferentiated runtime model.

## Architectural Position in the Spec Hierarchy
This document sits below:
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`

and above:
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
- `API_ARCHITECTURE_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `JOB_QUEUE_AND_WORKER_SPEC.md`
- `AI_ORCHESTRATION_SPEC.md`
- `MODEL_ROUTING_AND_CONTEXT_SPEC.md`
- `AI_USAGE_METERING_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `MONITORING_ALERTING_AND_INCIDENT_RESPONSE_SPEC.md`
- product integration specifications

This document does not override the top-level system-boundary model. It refines that model into an actionable shared-core architecture.

## System Boundaries
The FUZE architecture MUST be interpreted through the following layers:
1. **Experience / Edge Layer** — surfaces and bounded external entrypoints
2. **Application Plane** — canonical domain-service plane for off-chain business truth
3. **Execution Plane** — async, deferred, retry-capable, and long-running work plane
4. **Integration Plane** — trust-boundary mediation and normalization plane
5. **Reporting Plane** — derived-output, analytics, and publication plane
6. **Control Plane** — policy-sensitive operator and governance-aware control plane
7. **On-Chain Contract Layer** — adjacent contract-governed truth layer for explicitly committed chain truth only

The first six layers are FUZE-operated off-chain architecture. The on-chain contract layer is adjacent and explicitly bounded. These layers MAY share infrastructure in practice but MUST remain architecturally distinct.

## Adjacent Boundaries
This specification must be interpreted together with adjacent files as follows:
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md` governs top-level ownership and truth-family ownership
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md` governs ecosystem framing and top-level architectural separation
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md` assigns canonical owners to major domains represented here
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md` refines storage and entity implications of this architecture
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md` refines how chain-adjacent boundaries described here divide responsibility
- `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md` refines how products extend the platform without redefining primitives
- identity/account/session and workspace/access-control foundation documents refine the access domains that the architecture must host
- shared API, event, workflow, AI, security, monitoring, and migration specifications refine narrower implications of this architecture

This document defines the architecture model within which those documents must remain coherent. It does not absorb all their detail.

## Conflict Resolution Rules
When materials, implementations, or interpretations conflict, the following rules apply:
1. the active refined registry and higher constitutional materials win over narrower documents
2. `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md` wins on canonical ownership, mutation-owner interpretation, and top-level truth-family ownership
3. `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md` wins on ecosystem framing and top-level layer interpretation
4. this document wins on platform plane separation, shared-core architecture, runtime interaction posture, and architectural role of runtime surfaces and shared domains
5. canonical source domains win over reports, surfaces, caches, queues, AI explanations, exports, and public registry artifacts
6. on-chain committed truth wins only for categories explicitly committed on-chain; broader off-chain policy, accounting, and workflow meaning remains off-chain unless explicitly committed
7. provider input never wins by itself; verified and normalized owner-controlled consequences win
8. control-plane involvement does not transfer ordinary business-domain ownership
9. when ambiguity remains, FUZE MUST prefer the more conservative architecture-consistent interpretation and escalate the ambiguity into downstream refinement or recorded decision work

## Default Decision Rules
When ambiguity exists and no narrower approved exception is available, FUZE MUST default to the following:
1. cross-product capabilities default to platform ownership
2. user-facing surfaces default to non-canonical presentation ownership only
3. execution systems default to execution-state ownership, not business-domain ownership
4. provider and chain callbacks default to external-input status until normalization succeeds
5. reporting and registry systems default to derived/publication-state status
6. control-plane systems default to policy constraint, approval, or remediation ownership, not business-domain ownership
7. product-local logic defaults to product ownership only if it does not redefine a shared primitive
8. if no explicit owner or plane can be named, the design is incomplete and MUST NOT be treated as production-grade

## Roles / Actors / Entities
### Human Actors
- end users
- workspace members and owners
- product operators
- support operators
- security reviewers
- finance and treasury reviewers
- governance or approval actors
- public and stakeholder readers of approved trust surfaces

### System Actors
- edge and frontend surfaces
- platform domain services
- product domain services
- workflow and worker systems
- integration adapters and verification systems
- chain-adjacent services
- reporting and registry publishers
- control-plane systems
- on-chain contracts

### Core Architectural Entity Families
- account, linked-login, and session entities
- workspace, organization, membership, role, permission, and effective-access entities
- entitlement and capability state
- commerce, billing, invoice, receipt, credits, and payout-orchestration entities
- product-local domain entities
- workflow, job, retry, and execution-lineage entities
- provider normalization and callback-ingestion entities
- chain reference, submission, confirmation, and reconciliation entities
- audit and traceability entities
- reporting, publication, registry, and analytics artifacts
- control-plane action, approval, restriction, and remediation entities

## Ownership Model
This architecture requires each major concern to be analyzed across at least five dimensions:
1. **semantic ownership** — who defines what the concept means
2. **mutation ownership** — who accepts writes that change canonical state
3. **runtime execution ownership** — who performs the operational work
4. **presentation ownership** — who renders or explains state
5. **control/governance ownership** — who may approve, constrain, or remediate sensitive actions

These dimensions MUST remain distinct. A component may execute, present, or approve an action without owning the underlying business meaning.

## Authority / Decision Model
### Platform Authority
The platform has final authority over shared primitives, cross-product policy boundaries, plane separation, integration normalization posture, and control-plane architecture.

### Product Authority
Products have authority over product-local object models, workflows, AI specialization, UX, analytics, and bounded product-local operations within platform constraints.

### Contract Authority
Contracts have authority only over the state and enforcement explicitly committed into contract design. Contract state does not automatically own broader off-chain policy or reporting meaning.

### Control / Governance Authority
Governance-sensitive control pathways may authorize, restrict, pause, review, or require remediation for elevated actions. They do not become default owners of downstream business state.

### External Authority
Providers have authority only inside their own systems. FUZE decides what it accepts, how it verifies it, and which internal consequences follow.

## Canonical Platform Architecture
FUZE MUST be implemented as a platform-first shared-core architecture with bounded product-extension domains.

At the highest level, the architecture contains:
- one bounded experience/edge layer
- five major off-chain planes: application, execution, integration, reporting, and control
- one adjacent on-chain contract layer for explicitly committed chain truth

The experience/edge layer is the first entry layer but is not a plane of durable truth ownership. The five off-chain planes are FUZE-operated. The on-chain contract layer remains adjacent and separately governed for committed contract truth.

## Architectural Layer Model
### 1. Experience / Edge Layer
This is the first-party and bounded public entry layer.

Includes:
- `fuze-frontend-webapp`
- `fuze-frontend-admin`
- bounded public API consumers
- bounded public registry consumers
- future partner or SDK consumers where explicitly allowed

Responsibilities:
- request initiation
- presentation and user interaction
- local drafts and client-side transient state
- rendering canonical or derived backend state
- carrying transport context into backend-owned flows

Non-responsibilities:
- durable business truth ownership
- silent policy interpretation
- final mutation authority for shared domains
- direct ownership of cross-domain internal coordination

### 2. Application Plane
This is the primary canonical domain-service plane.

Responsibilities:
- domain validation
- canonical mutation acceptance
- canonical domain reads
- shared platform capability ownership
- product-domain capability ownership
- issuance of owner-domain events where appropriate
- acceptance and recording of deferred execution intent
- ownership-aligned write-path enforcement

Architecture rule:
- canonical off-chain business truth MUST be created or mutated through owning domains in this plane or through an explicitly owner-controlled equivalent path that preserves the same semantics

### 3. Execution Plane
This plane handles deferred, async, multi-step, long-running, or retry-driven work.

Responsibilities:
- job dispatch and status tracking
- workflow-run progression
- retry and backoff behavior
- ordered step execution
- worker-side operational execution
- bounded compensation or remediation lineage
- accepted-state handling for async commands

Non-responsibilities:
- becoming the owner of billing, credits, identity, workspace, authorization, entitlement, or other business truth outside execution-domain-owned records

Architecture rule:
- the execution plane MUST act through owner-domain APIs or contracts rather than performing shadow mutation of canonical state

### 4. Integration Plane
This plane handles inbound and outbound interactions across trust boundaries.

Responsibilities:
- payment-rail verification and normalization
- provider callbacks and signature/claim verification
- external identity-provider handoff boundaries
- AI vendor access mediation
- wallet and chain access mediation
- chain event ingestion and normalization
- RPC/indexing access mediation
- communication/delivery provider coordination
- provider configuration mediation under control-plane governance

Non-responsibilities:
- direct promotion of raw external signals into canonical platform truth without explicit normalization and owner-domain validation

### 5. Reporting Plane
This plane handles derived outputs, publication artifacts, and cross-domain read models.

Responsibilities:
- transparency publications
- public registry outputs
- dashboards and read models
- analytics-facing aggregates
- export artifacts
- search and discovery materializations
- stakeholder-facing reports

Non-responsibilities:
- canonical mutation of source business truth
- hidden correction of canonical domains through reporting rewrites

### 6. Control Plane
This plane governs sensitive runtime constraints and elevated operator/gov-aware actions.

Responsibilities:
- approvals
- restrictions and emergency controls
- rollout gating and feature posture
- provider/config enablement controls
- sensitive remediation pathways
- incident-response execution controls
- admin/operator actions under stronger authorization
- governance-sensitive action routing

Non-responsibilities:
- replacing domain-owned truth
- silently bypassing ownership rules

### 7. On-Chain Contract Layer
This layer is adjacent to the off-chain platform architecture.

Responsibilities:
- token-native truth on Ethereum or other approved chains
- explicitly committed Base credits or payout-execution truth where applicable
- contract-enforced constraints
- contract-governed events and contract-level permissions

Non-responsibilities:
- owning off-chain identity, workspace, billing policy, entitlement policy, workflow policy, reporting policy, or product-local semantics unless those meanings are explicitly committed by contract design

## Shared Platform Domain Model
The shared platform core is composed of reusable platform-owned domains.

### Identity and Account Domain
Owns canonical account identity and core identity state.

### Auth / Session / Linked Login Domain
Owns authentication-path linkage, session lifecycle, device/session security posture, and continuity-safe access behavior.

### Workspace / Organization Domain
Owns workspace structures, organizations, memberships, and shared collaboration context.

### Role / Access / Authorization Domain
Owns roles, grants, scoped authorization, access-evaluation inputs, and effective-permission outcomes.

### Wallet-Aware Participation Domain
Owns wallet-link mapping and account-associated participation context without replacing account-rooted identity.

### Commerce / Billing Domain
Owns pricing application, subscription and usage billing state, payment normalization outcomes, invoicing, receipts, refunds, reversals, and commercial orchestration state.

### Platform Credits Domain
Owns credits policy, issuance, balances, spend coordination, adjustments, reversals, and related accounting boundaries.

### Entitlement / Capability Gating Domain
Owns governed mapping from plans, purchases, credits, policy, or rollout posture into allowed capability exposure.

### AI Orchestration Domain
Owns platform-governed AI execution lifecycle and orchestration truth.

### Model Routing / Context Domain
Owns routing policy, model-selection posture, context composition policy, retrieval/reference-release rules, and bounded routing decisions.

### AI Usage Metering Domain
Owns canonical metering and attribution for AI usage, distinct from orchestration truth and billing truth.

### Workflow and Automation Domain
Owns workflow definitions, workflow runs, automation triggers, approvals, and cross-step orchestration lineage.

### Job Queue / Worker Coordination Domain
Owns job execution records, worker coordination state, and operational execution lineage for deferred work.

### Audit / Activity Domain
Owns immutable audit lineage and distinct user/admin activity representations where applicable.

### Transparency / Reporting Domain
Owns transparency publication truth, reporting-period artifacts, and reporting lineage.

### Public Registry Domain
Owns publication-safe contract and wallet registry outputs and correction-safe public reference artifacts.

### Payout Orchestration Domain
Owns off-chain payout-cycle orchestration, funding-intent coordination, reconciliation posture, and execution preparation before or alongside chain execution.

### Control / Governance-Aware Operations Domain
Owns operator-sensitive and governance-sensitive control actions, approval state, remediation pathways, rollout controls, and incident-aligned action constraints.

### Integration Adapter Domain
Owns provider-specific normalization boundaries, provider configuration metadata, inbound verification posture, and outbound/inbound integration mediation.

These shared domains may collaborate, but they MUST NOT erase ownership boundaries through convenience shortcuts.

## Product Extension Architecture
Products are not independent platforms. Each FUZE product MUST be implemented as a bounded extension domain using shared platform capabilities.

A product extension domain MAY own:
- product-local entities
- product-local workflows
- product-local AI behaviors or prompt patterns
- product-local UX and reports
- product-local configuration
- product-local operational views

A product extension domain MAY consume:
- account and identity context
- session state
- workspace context
- authorization and entitlement results
- billing and credits primitives
- AI orchestration and routing services
- workflow and automation services
- audit and reporting services
- notification and scheduling services where applicable
- chain-aware context through platform-governed boundaries

A product extension domain MUST NOT own:
- alternate account identity
- alternate session truth
- alternate workspace authority
- alternate authorization or entitlement foundations
- alternate credits truth
- alternate payout or reserve truth
- alternate public registry truth
- alternate control-plane authority for shared operations

## Product-Surface and Platform-Surface Responsibilities
### `fuze-frontend-webapp`
- first-party product and platform experience surface
- consumes backend APIs
- owns presentation state and local drafts only
- MAY initiate canonical flows but does not own them

### `fuze-frontend-admin`
- privileged operator/admin/control interface
- triggers review, remediation, approval, and operational actions under stronger controls
- does not own durable truth

### `fuze-backend-api`
- primary off-chain backend runtime
- hosts domain services, internal APIs, orchestration entrypoints, workflow coordination APIs, integration ingress, reporting publication backends, and control-plane backend logic
- MUST preserve domain and plane separation internally even if deployed as one broader backend estate

### `fuze-contracts`
- owns contract code and chain-native truth where explicitly committed on-chain
- does not own broader off-chain platform policy or product-local semantics

### `fuze-public-registry`
- publishes public-safe derived or publication-owned trust artifacts
- does not redefine underlying source truth

### Future `fuze-sdk`
- derives from approved contracts or public-safe APIs
- does not own source truth

## State Model
### Canonical Off-Chain State
Canonical off-chain state MUST live in owning application-plane domains and their authoritative storage layers.

### Execution State
Execution state MUST remain distinct from business-domain state, even when tightly linked by lineage.

### Provider Normalization State
Provider raw input, verification records, and normalized outcomes MUST remain distinguishable.

### Chain Coordination State
Submission intent, tx-tracking state, confirmation-tracking state, reconciliation state, and chain-derived observations MUST remain distinguishable from contract-native truth.

### Reporting and Publication State
Reporting stores, registry artifacts, analytics aggregates, and exported views are downstream derived or publication-oriented state unless a narrower spec explicitly elevates a narrow artifact.

### Control State
Control approvals, restrictions, rollout posture, and remediation records are control-plane state. They constrain business actions without replacing canonical business ownership.

## Lifecycle / Workflow Model
The canonical FUZE architecture interaction pattern is layered:
1. request or signal arrives through an edge or integration boundary
2. identity, session, workspace, authorization, and entitlement context is resolved where relevant through appropriate domains
3. the owning domain validates the request and decides whether to accept mutation or read
4. if immediate completion is possible, the application plane records the canonical outcome directly
5. if deferred work is needed, the application plane records accepted async intent and hands off to the execution plane
6. the execution plane coordinates work using owner-domain APIs or contracts rather than shadow mutation
7. the integration plane verifies and normalizes provider or chain-related signals before they influence canonical platform state
8. the reporting plane derives publication and read models downstream from canonical sources
9. the control plane constrains or remediates sensitive actions through explicit pathways
10. chain interactions occur through chain-adjacent coordination while preserving contract-truth versus off-chain-policy separation

No workflow may skip owner-controlled mutation acceptance merely because the initiating layer is convenient, trusted, or privileged.

## Invariants
The following invariants are mandatory:
1. architecture MUST preserve one coherent platform rather than per-product shadow platforms
2. each material truth family MUST identify a canonical owner and a governing plane posture
3. experience surfaces MUST NOT own durable business truth
4. execution systems MUST NOT be treated as owners of upstream business meaning by implication
5. provider and chain-originating signals MUST pass through normalization before mutating internal truth
6. reporting/publication systems MUST remain downstream to canonical source domains
7. control-plane intervention MUST be explicit, bounded, audited, and reason-coded where material
8. products MUST consume platform-owned primitives rather than redefining them
9. failure and degraded-mode behavior MUST preserve ownership and plane clarity rather than hide them
10. downstream implementation layers MUST preserve this architecture even when service packaging evolves

## Functional Rules
### Rule 1: Canonical Writes Terminate in Owning Domains
Every state-mutating operation MUST terminate in the owning domain’s mutation boundary.

### Rule 2: Async Acceptance Must Be Explicit
Accepted async intent MUST be explicitly recorded and distinguishable from final business success.

### Rule 3: Execution Plane Uses Owner Contracts
Workers, schedulers, and orchestrators MUST invoke owner-domain contracts rather than perform ad hoc direct mutation of another domain’s truth.

### Rule 4: Integration Plane Normalizes Before Influence
Provider callbacks, chain observations, and external signals MUST be verified and normalized before influencing canonical state.

### Rule 5: Reports Do Not Rewrite Truth
Reporting, registry, search, analytics, export, or AI-explanation layers MUST NOT silently rewrite canonical domain truth.

### Rule 6: Control Paths Are Explicitly Privileged
Admin, support, incident, and governance-sensitive actions MUST use explicit control-plane contracts with stronger authorization and audit posture.

### Rule 7: Product Extensions Consume, Not Re-Own
Products MUST consume platform context and platform-owned primitives through explicit contracts instead of copying and re-owning shared truth.

### Rule 8: Chain Coordination Is Bounded
Chain-adjacent services MAY coordinate submission, confirmation, and reconciliation but MUST NOT redefine contract-native truth or broader off-chain policy semantics.

### Rule 9: Storage Convenience Does Not Redefine Ownership
Physical co-location, denormalization, or caching MUST NOT change which domain owns truth.

### Rule 10: Ambiguity Requires Escalation
If a proposed runtime design cannot name the owning domain, plane posture, and normalization path, the design is incomplete and MUST NOT proceed as production-grade implementation.

## Permission / Access Considerations
1. the experience layer MUST NOT treat login or transport context as final permission truth
2. admin/control-plane pathways require stronger authorization than ordinary product runtime paths
3. shared platform services MUST not assume universal cross-domain access without explicit contracts and policy support
4. worker and automation actors MUST use bounded service identities and reason-coded control paths where elevated actions are involved
5. public and partner-facing surfaces MUST remain narrower than internal service/control surfaces

## Entitlement Considerations
1. entitlement remains a distinct domain consumed by the architecture; it MUST NOT be hidden inside session, role, or product-local UI state
2. capability-gating may influence execution acceptance, API response posture, and workflow progression, but it does not replace authorization or business-domain validation
3. products MAY add product-local gating only within platform-governed entitlement rules and MUST NOT redefine entitlement semantics for shared capabilities

## API / Contract Implications
This architecture imposes the following downstream API requirements:
1. API surface families MUST distinguish platform, product, internal, admin/control, public, reporting, and chain-adjacent concerns
2. canonical writes MUST terminate in owning domains
3. public APIs MUST remain narrower, safer, and more compatibility-governed than internal service APIs
4. internal APIs MUST preserve ownership-aligned collaboration rather than encourage implicit shared writes
5. admin/control-plane actions MUST remain explicitly privileged, reason-coded where material, and auditable
6. derived-public or registry surfaces MUST NOT masquerade as internal mutation surfaces
7. chain-adjacent APIs MUST expose coordination and status, not false ownership of contract truth
8. degraded, pending, normalized, provider-sourced, and chain-sourced states MUST be explicit where relevant to caller correctness

## Event / Async Implications
1. meaningful state-change events SHOULD be emitted by owning domains or explicitly delegated owner-controlled components
2. execution-plane progression MAY emit lifecycle events for coordination and observability
3. provider or chain events MUST be normalized before they become platform-owned outcomes
4. retries and replay MUST preserve idempotent business meaning
5. async execution lineage MUST remain explicit for audit, remediation, and reporting
6. event consumers MUST NOT reinterpret events into contradictory source truth
7. AI-triggered actions MUST pass deterministic validation and owner acceptance before becoming truth

## Data Model / Storage Implications
1. canonical transactional records MUST remain with owning domains
2. execution records MUST remain distinct from business-domain records
3. provider raw input and normalized outcomes MUST remain distinguishable
4. chain-derived records and off-chain policy records MUST remain distinguishable
5. reporting/read models MUST remain downstream and derivable
6. caches, search indexes, dashboard stores, and analytics marts MUST NOT become unofficial write owners
7. product-local data MUST remain distinct from shared-platform data even when co-located physically
8. migrations MUST preserve ownership semantics and lineage when schemas move, split, merge, or proxy legacy state

## Read Model / Projection / Reporting Rules
1. reporting and analytics layers MAY aggregate across domains, but they MUST preserve source traceability
2. publication and registry surfaces MUST carry freshness, correction, and supersession discipline appropriate to their trust role
3. search indexes MAY accelerate discovery but MUST NOT become write authorities
4. AI summaries and explanations MAY derive from canonical truth but MUST NOT be treated as authoritative mutation sources
5. reporting lag MUST be represented as lag, not as evidence that source truth changed

## Security / Risk / Abuse Controls
The architecture MUST preserve the following security and risk controls:
- platform-owned truth must not be mutable from ungoverned product-local shortcuts
- integration-plane trust boundaries must not be bypassed
- economic, governance-sensitive, and control-plane flows require stronger protections than ordinary product requests
- wallet-aware participation must remain bounded by account-rooted identity architecture
- provider degradation or fraud signals must not directly mutate high-value truth without normalization and policy checks
- secret handling and provider configuration must remain separated from ordinary product logic
- remediation and emergency controls must be explicit, bounded, and auditable

## Boundary Violation Detection / Non-Canonical Patterns
The following patterns are non-canonical and forbidden unless a higher-order approved exception explicitly states otherwise:
- per-product reimplementation of shared identity, session, workspace, authorization, entitlement, credits, payout, or control primitives
- frontend or admin-surface mutation of canonical truth without backend owner-domain acceptance
- workflow or worker systems directly rewriting another domain’s truth without owner contracts
- reporting or registry artifacts treated as upstream systems of record
- provider callbacks directly accepted as canonical business truth
- chain-submission services treated as owners of broader off-chain policy meaning
- compatibility layers that silently harden into alternate permanent architecture
- “temporary” local tables or caches becoming de facto sources of truth for shared primitives

Architecture reviews, audits, and incident analysis SHOULD explicitly test for these patterns.

## Audit / Traceability Requirements
FUZE MUST be able to determine:
- which domain owns the affected truth
- which plane processed the action
- whether a state transition was synchronous, async, provider-normalized, control-plane-mediated, reporting-derived, or chain-adjacent
- which actor, service identity, or operator initiated the work
- which policy version, rollout posture, or approval path constrained the action where relevant
- how accepted async intent relates to final outcome or failure
- how visible derived/public state relates to canonical source records

This traceability is necessary for incident response, finance integrity, governance review, security review, and transparency-supporting reporting.

## Failure Handling / Edge Cases
### Domain-Ownership Ambiguity
If architecture cannot clearly identify the owning domain, the design is incomplete and MUST escalate before implementation.

### Provider Failure or Partial Verification
Integration failure MUST preserve the distinction between unavailable provider outcome and changed platform truth.

### Workflow / Worker Failure
Execution failure MUST NOT be misinterpreted as automatic rollback of already committed business truth unless the owning domain explicitly defines compensation behavior.

### Reporting Lag
Reporting and publication surfaces MAY lag source truth but MUST reconcile without rewriting canonical source records.

### Control-Plane Unavailability
Sensitive actions MUST fail safely rather than converting into ungated ordinary runtime behavior.

### Chain Visibility Lag
Indexing or RPC lag MUST NOT be mistaken for changed contract truth.

### Product Isolation Failure
Product-specific faults MUST NOT corrupt shared platform primitives.

### Mixed-Plane Partial Completion
If application-plane acceptance occurred but later execution, provider, control, or chain stages fail, the system MUST preserve lineage of partial completion rather than collapse state into a misleading binary.

### Migration in Progress
Compatibility layers MAY exist temporarily, but they MUST preserve canonical ownership and explicit plane separation while migration remains incomplete.

## Operational Considerations
The architecture intentionally does not force one deployment style, but it does require:
- architectural separation even when multiple domains are deployed inside one broader runtime
- observability that distinguishes domain and plane boundaries
- runbooks and remediation flows that identify canonical owners before action
- rollout discipline that favors reuse of shared domains over local reimplementation
- explicit provider adapters and chain-adjacent services rather than scattered direct calls
- safe operator tooling aligned to control-plane boundaries
- migration paths that preserve ownership semantics and lineage

## Migration / Compatibility / Supersession Considerations
This refined architecture supersedes looser interpretations in which:
- products behave as mini-platforms
- frontend or reporting layers own business truth
- workflow or AI runtime is treated as owner of downstream effects
- provider callbacks directly become platform truth
- contracts are treated as owners of off-chain business policy
- admin tools silently bypass domain ownership

Compatibility layers MAY exist temporarily, but they MUST preserve canonical ownership, explicit plane separation, and audit lineage. This specification intentionally avoids locking FUZE into a premature deployment topology. Service decomposition may evolve over time, but the architectural boundaries in this document remain governing.

## Implementation-Contract Guardrails
Downstream implementation-contract layers MUST preserve the following:
1. explicit owner-domain mutation boundaries
2. explicit distinction between canonical state, execution state, normalized provider state, derived state, and presentation state
3. explicit idempotency and replay-safety rules where retries or async progression exist
4. explicit control-plane privilege boundaries and audit requirements for elevated actions
5. explicit reason codes and trace identifiers for materially sensitive remediation or override pathways
6. explicit treatment of pending, accepted, failed, reconciled, and superseded states where relevant
7. explicit prohibition on treating caches, reports, registries, or surfaces as canonical write owners
8. explicit chain-adjacent boundaries separating submission/coordination from contract-native truth

No downstream implementation may optimize away these distinctions merely to simplify local code shape.

## Downstream Execution Staging
Downstream runtime and contract layers SHOULD stage execution as follows where relevant:
1. **intent capture** — validated request or signal reaches the correct owner boundary
2. **canonical acceptance** — the owner domain accepts or rejects mutation
3. **execution handoff** — deferred work is recorded and handed to the execution plane where needed
4. **external coordination** — provider or chain work is mediated through the integration plane or chain-adjacent services
5. **owner reconciliation** — normalized outcomes are interpreted by owning domains
6. **projection/publication** — reporting and registry layers derive downstream views
7. **control/remediation** — control-plane pathways manage elevated exceptions, pauses, reviews, or corrections

This staging model is normative in posture, though exact technical packaging is deferred to downstream implementation specifications.

## Required Downstream Specs / Contract Layers
This specification directly requires consistent refinement in:
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
- `API_ARCHITECTURE_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `IDEMPOTENCY_AND_VERSIONING_SPEC.md`
- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `JOB_QUEUE_AND_WORKER_SPEC.md`
- `AI_ORCHESTRATION_SPEC.md`
- `MODEL_ROUTING_AND_CONTEXT_SPEC.md`
- `AI_USAGE_METERING_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `MONITORING_ALERTING_AND_INCIDENT_RESPONSE_SPEC.md`
- `DEPLOYMENT_AND_RUNTIME_OPERATIONS_SPEC.md`
- product integration specifications

## Canonical Examples / Anti-Examples
### Canonical Example: Deferred Payment Normalization
A payment signal enters through the integration plane, is verified and normalized, then an owning commerce domain accepts the canonical outcome. A workflow record tracks deferred progression, and reporting surfaces later derive user-visible summaries. The callback itself is not the business truth owner.

### Canonical Example: Product Feature Using Shared Identity
A product surface uses platform identity, session, workspace, authorization, entitlement, and credits context from shared services. The product owns its local workflow and output, but it does not recreate account or credits truth.

### Canonical Example: Governance-Sensitive Remediation
An operator triggers a control-plane remediation path with reason code and audit lineage. The control plane authorizes and constrains the action, while the owning business domain writes the resulting state transition.

### Anti-Example: Worker Directly Rewriting Credits Balances
A queue consumer that directly adjusts credits balances without going through the owning credits domain is non-canonical.

### Anti-Example: Public Registry Correcting Canonical Source Records
A public registry artifact that silently rewrites or backfills upstream canonical data is non-canonical.

### Anti-Example: Product-Owned Shadow Workspace Model
A product that creates its own authoritative workspace/team model for shared operations instead of consuming platform workspace truth is non-canonical.

## Dependencies / Cross-Spec Links
This specification depends on:
- `REFINED_SYSTEM_SPEC_INDEX.md`
- `DOCS_SPEC_INDEX.md`
- `SYSTEM_SPEC_INDEX.md`
- `API_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- identity/account/auth/session foundation documents
- workspace/access-control foundation documents
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`

This specification directly governs or materially informs:
- all major domain ownership, entity ownership, API, event, workflow, AI, security, operations, and product-integration specifications
- the architectural interpretation of `fuze-backend-api`, `fuze-frontend-webapp`, `fuze-frontend-admin`, `fuze-contracts`, and `fuze-public-registry`
- downstream implementation contracts, schemas, events, jobs, runbooks, and observability posture

## Explicitly Deferred Items
The following are intentionally deferred to downstream specifications:
- exact service topology and service-split timing
- exact queue and event-bus technology choices
- exact database, warehouse, and search-stack selection
- exact edge gateway and mesh configuration
- exact provider-by-provider adapter internals
- exact chain indexer and ABI implementation detail
- exact frontend composition architecture
- exact deployment graph across environments
- exact SDK shape and packaging decisions

These are implementation-detail refinements of the architecture, not replacements for it.

## Final Normative Summary
FUZE MUST be implemented as a platform-first shared-core architecture with bounded product-extension domains. The architecture is organized around an experience/edge layer, five distinct off-chain planes — application, execution, integration, reporting, and control — and one adjacent on-chain contract layer for explicitly committed chain truth only.

`fuze-backend-api` is the primary off-chain backend runtime and MUST preserve domain and plane separation internally. `fuze-frontend-webapp` and `fuze-frontend-admin` are surfaces, not owners of durable business truth. `fuze-contracts` own only explicitly committed chain truth. `fuze-public-registry` publishes public-safe derived or publication-owned artifacts without redefining source truth. Products extend the platform but MUST NOT reinvent shared platform primitives.

All downstream FUZE specifications and implementations MUST preserve this architecture. If a later design weakens platform-first shared-core behavior, erases plane separation, hides ownership through convenience shortcuts, or treats derived layers as truth owners, this specification governs and the weaker interpretation is non-canonical.

## Quality Gate Checklist
- [x] Canonical owner posture is explicit for every material architectural truth family
- [x] Mutation boundaries are explicit
- [x] Adjacent boundaries are explicit
- [x] Truth classes are explicit
- [x] Conflict-resolution rules are explicit
- [x] Default decision rules are explicit
- [x] Non-canonical patterns are called out clearly
- [x] Operator/admin/control pathways are bounded and auditable in posture
- [x] Read-model, cache, reporting, and projection rules are explicit
- [x] On-chain versus off-chain responsibilities are kept explicit at the architecture level
- [x] Failure and degraded-mode behaviors are explicit
- [x] Downstream implementation guardrails are explicit
- [x] Dependencies and downstream impacts are explicit
- [x] Non-goals and deferred items are explicit
- [x] The document is strong enough to constrain backend, API, data, runtime, and product implementation without inviting contradictory semantics
