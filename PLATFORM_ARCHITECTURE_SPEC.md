# PLATFORM_ARCHITECTURE_SPEC

## Document Metadata

- Document Name: `PLATFORM_ARCHITECTURE_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Governing Layer: Platform constitution / architecture
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, backend engineering, product engineering, security, data, operations, governance, reporting
- Primary Purpose: Define the canonical shared-core architecture of FUZE, including platform runtime layers, shared service domains, interaction model, control-plane boundaries, async and chain-adjacent execution posture, and product-extension architecture
- Primary Upstream References:
  - `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
  - `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
  - `SYSTEM_SPEC_INDEX.md`
  - `API_SPEC_INDEX.md`
  - `DOCS_SPEC_INDEX.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
  - `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
- Primary Downstream Dependents:
  - `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
  - `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
  - `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
  - `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
  - `API_ARCHITECTURE_SPEC.md`
  - `PUBLIC_API_SPEC.md`
  - `INTERNAL_SERVICE_API_SPEC.md`
  - `WORKFLOW_AND_AUTOMATION_SPEC.md`
  - `JOB_QUEUE_AND_WORKER_SPEC.md`
  - `AI_ORCHESTRATION_SPEC.md`
  - `MODEL_ROUTING_AND_CONTEXT_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - `MONITORING_ALERTING_AND_INCIDENT_RESPONSE_SPEC.md`
  - product integration specifications

---

## Purpose

This specification defines the canonical high-level platform architecture of FUZE.

Its purpose is to establish how FUZE should be structured as a platform-first, multi-product ecosystem with shared operating layers, shared commercial layers, shared governance-aware controls, shared AI and workflow infrastructure, bounded chain-adjacent execution, and product-extension domains built on top of those shared capabilities.

This document is the main architectural bridge between:
- the top-level system/boundary documents, and
- narrower domain, API, workflow, control, security, data, and product specifications.

It is not a deployment-diagram file and it is not a repository-layout file. It is the governing system-architecture document that defines how shared platform capabilities are organized, how runtime layers interact, how ownership is preserved through those interactions, and how products must extend the platform without becoming shadow platforms.

---

## Scope

This specification governs:

- the canonical architectural model of the FUZE shared platform
- the major runtime and service layers inside the platform boundary
- the distinction between application plane, execution plane, integration plane, reporting plane, and control plane
- the architectural role of `fuze-backend-api`, `fuze-frontend-webapp`, `fuze-frontend-admin`, `fuze-contracts`, and `fuze-public-registry`
- the shared service-domain model for identity, workspace, access, commerce, credits, AI, workflow, reporting, governance-aware controls, and chain-adjacent coordination
- the product-extension architecture model
- the architectural posture for synchronous requests, asynchronous execution, provider normalization, chain-adjacent orchestration, public reporting, and administrative control
- the interaction rules between platform-owned domains, product-owned domains, on-chain systems, and external providers
- the architectural implications for APIs, events, workers, observability, failure handling, and migration

This specification does not define:

- every entity schema or table
- every API route or event payload
- every queue topology or transport implementation
- every Kubernetes, cloud, or network deployment detail
- smart-contract ABI details
- every product’s local object model
- every monitoring rule or operational playbook
- team staffing or code-repository organization

Those belong in downstream specifications.

---

## Out of Scope

This specification is explicitly out of scope for:

- vendor-specific infrastructure decisions
- exact modular-monolith versus service-split rollout timing
- frontend component structure
- exact database engine choices
- exact broker or queue technology choices
- low-level chain indexing implementation
- UI composition decisions
- full data-warehouse design
- narrow product-internal implementation details that do not affect shared-platform architecture

---

## Design Goals

The design goals of the FUZE platform architecture are:

1. Preserve FUZE as one coherent platform rather than a group of disconnected product stacks.
2. Make shared cross-product capabilities first-class platform domains.
3. Prevent shadow ownership and shadow platform creation.
4. Keep identity, session, workspace, authorization, entitlement, billing, credits, token, payout, and governance-sensitive controls architecturally separate.
5. Support both synchronous user-facing operations and asynchronous long-running automation without losing ownership clarity.
6. Support chain-adjacent and transparency-oriented capabilities without collapsing off-chain policy into on-chain state.
7. Provide a reusable product-extension architecture for current and future FUZE products.
8. Make control-plane intervention explicit, auditable, and structurally separate from ordinary runtime flow.
9. Make reporting and public trust surfaces downstream to canonical source domains.
10. Support long-term platform expansion without forcing re-architecture every time a new product or rail is added.

---

## Non-Goals

This architecture is not intended to produce:

- one backend per product with duplicated shared primitives
- frontend-owned business truth
- workflow or AI runtime as owners of business truth
- reporting systems that silently become source of record
- provider callbacks that directly own platform meaning
- chain presence that replaces platform policy/orchestration
- admin tools that bypass domain ownership
- “shared ownership” as the default answer for cross-domain work

---

## Core Principles

### 1. Platform-First Shared-Core Principle
Capabilities that are identity-defining, economically shared, control-sensitive, cross-product, or required for future ecosystem expansion belong to the shared platform.

### 2. Ownership-Preserving Architecture Principle
Architecture must preserve canonical ownership rather than hide it behind convenience layers.

### 3. Plane Separation Principle
Application runtime, async execution, integration boundaries, reporting/publication, and control-plane behavior must remain distinguishable even when implemented in one broader backend environment.

### 4. Product Extension Principle
Products extend the platform through bounded product domains. They consume platform primitives; they do not redefine them.

### 5. Chain-Adjacent Discipline Principle
On-chain interaction is an explicit architectural lane, not a general-purpose owner of off-chain business policy.

### 6. Derived-Not-Canonical Reporting Principle
Reporting, registry, and transparency outputs are downstream, derived, and publication-oriented unless a narrower spec explicitly elevates a reporting artifact for a narrow truth category.

### 7. Governance-Aware Control Principle
Sensitive operations require explicit control-plane or governance-aware pathways rather than hidden runtime shortcuts.

### 8. Operational Clarity Principle
Incidents, retries, degraded modes, and remediation must preserve visibility into which plane and which domain own the affected truth.

---

## Canonical Definitions

### Shared Platform Core
The reusable FUZE operating foundation that supports all products and shared ecosystem capabilities.

### Product Extension Domain
A bounded product-specific architecture domain that owns product-local workflows, data, and user experiences while using platform primitives.

### Application Plane
The primary request/response and domain-service plane where canonical off-chain business truth is created, validated, mutated, and read.

### Execution Plane
The async and long-running execution layer that carries jobs, orchestrations, retries, step progression, and deferred work without becoming the owner of business truth outside its own domain.

### Integration Plane
The boundary layer for provider adapters, inbound callbacks, outbound provider calls, chain-adjacent coordination, and normalization of non-FUZE-originated signals.

### Reporting Plane
The derived-output layer for analytics, transparency, public registry publication, and public/private reports.

### Control Plane
The operational and policy-control layer that governs approvals, restrictions, rollouts, emergency controls, sensitive remediation, provider configuration, and governance-aware operational actions.

### Chain-Adjacent Service
An off-chain service or boundary that coordinates with on-chain contracts while preserving the difference between contract-native truth and off-chain policy/orchestration truth.

### Product Surface
A first-party user-facing product experience or product-specific API/surface built on top of product and platform domains.

---

## Architectural Position in the Spec Hierarchy

This document sits below:
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`

and above:
- domain ownership, data ownership, on-chain/off-chain responsibility, API architecture, internal API, workflow, AI, financial rail, security, operations, and product integration specs.

This document does not override the top-level system boundary model. It refines that model into an actionable shared-core architecture.

---

## Canonical Platform Architecture

FUZE must be implemented as a platform-first shared-core architecture with product-extension domains.

At the highest level, the architecture has five major off-chain planes plus one bounded on-chain interaction layer:

1. **Application Plane**
2. **Execution Plane**
3. **Integration Plane**
4. **Reporting Plane**
5. **Control Plane**
6. **On-Chain Contract Layer** (adjacent, not part of off-chain platform ownership)

The first five planes are off-chain and FUZE-operated.
The on-chain layer is contract-governed for explicitly committed chain truth only.

These planes may share runtime infrastructure in practice, but they must remain architecturally distinguishable.

---

## Architectural Layer Model

### 1. Experience / Edge Layer

This is the first-party and bounded public entry layer.

Includes:
- `fuze-frontend-webapp`
- `fuze-frontend-admin`
- bounded public API and public registry consumers
- future partner or SDK consumers where allowed

Responsibilities:
- request initiation
- presentation
- user interaction
- bounded client-side drafts
- non-canonical UX state
- rendering of canonical or derived backend state

Non-responsibilities:
- durable business truth ownership
- silent policy interpretation
- final mutation authority for shared domains
- direct ownership of internal service coordination

### 2. Application Plane

This is the primary canonical domain-service plane.

Responsibilities:
- domain validation
- canonical mutation acceptance
- canonical domain reads
- shared platform capability ownership
- product-domain capability ownership
- issuance of domain events where appropriate
- coordination of accepted async work
- enforcement of ownership-aligned write paths

This plane is primarily implemented in `fuze-backend-api`.

### 3. Execution Plane

This plane handles deferred, async, multi-step, long-running, or retry-driven work.

Responsibilities:
- job dispatch and status tracking
- workflow run progression
- retry and backoff behavior
- ordered step execution
- worker-side operational execution
- bounded compensation/remediation lineage
- explicit accepted-state behavior for async commands

Non-responsibilities:
- becoming the owner of billing truth, credits truth, identity truth, or other domain truth outside execution-domain-owned records

### 4. Integration Plane

This plane handles inbound and outbound interactions across trust boundaries.

Responsibilities:
- payment-rail normalization
- provider callbacks and verification
- AI vendor access boundaries
- external identity-provider handoff boundaries
- messaging/mini-app launch and callback normalization
- wallet and chain access mediation
- chain event ingestion and normalization
- RPC/indexing access mediation
- external delivery provider coordination

Non-responsibilities:
- direct transfer of raw provider state into canonical platform truth without explicit normalization and domain validation

### 5. Reporting Plane

This plane handles derived outputs, publication artifacts, and cross-domain read models.

Responsibilities:
- transparency publications
- public registry outputs
- payout-ledger materialization where applicable
- investor/community reporting artifacts
- dashboards and read models
- analytics-facing aggregates
- search and discovery materializations

Non-responsibilities:
- canonical mutation of source business truth
- hidden correction of canonical domains through reporting rewrites

### 6. Control Plane

This plane governs sensitive runtime constraints and operator/gov-aware control actions.

Responsibilities:
- approvals
- restrictions and kill-switches
- rollout gating
- provider/config enablement controls
- sensitive remediation pathways
- incident-response execution controls
- admin/operator actions under stronger authorization
- governance-sensitive action routing

Non-responsibilities:
- replacing domain-owned truth
- silently bypassing ownership rules

### 7. On-Chain Contract Layer

This is adjacent to the platform architecture rather than absorbed into it.

Responsibilities:
- token-native truth on Ethereum
- explicitly committed Base credits or payout execution truth where applicable
- contract-enforced constraints
- contract-governed events and contract-level permissions

Non-responsibilities:
- owning off-chain identity, workspace, billing policy, entitlement policy, workflow policy, or reporting policy unless explicitly committed to contract design

---

## Shared Platform Domain Model

The canonical shared platform is composed of reusable domains.

### Identity and Account Domain
Owns canonical account identity and core identity state.

### Auth / Session / Linked Login Domain
Owns authentication method linkage, session lifecycle, device/session security posture, and continuity-safe access behavior.

### Workspace / Organization Domain
Owns workspace structures, membership context, workspace selection, and organization-scoped operating context.

### Role / Access / Authorization Domain
Owns roles, grants, effective permission rules, scoped authorization, and access evaluation.

### Wallet-Aware Participation Domain
Owns wallet-link mapping and account-associated participation context without replacing account-rooted identity.

### Commerce / Billing Domain
Owns subscription/billing semantics, payment normalization outcomes, invoicing/receipts, usage-linked charging rules, and related commercial state.

### Platform Credits Domain
Owns credits policy, credits account semantics, issuance, balance, spend coordination, adjustments, and related accounting boundaries.

### Entitlement / Capability Gating Domain
Owns governed mapping from plans, purchases, credits, or policy into allowed capability exposure.

### AI Orchestration Domain
Owns platform-governed AI execution lifecycle and policy-bounded orchestration truth.

### Model Routing / Context Domain
Owns routing policy, context-binding policy, retrieval-reference release rules, and bounded routing decisions.

### AI Usage Metering Domain
Owns canonical metering and attribution for AI usage, distinct from orchestration truth and billing truth.

### Workflow and Automation Domain
Owns workflow definitions, workflow runs, automation triggers, approvals, and workflow execution lineage.

### Job Queue / Worker Coordination Domain
Owns job execution records, worker coordination state, and operational execution lineage for deferred work.

### Audit / Activity Domain
Owns immutable audit lineage and distinct user/admin activity representations where applicable.

### Transparency / Reporting Domain
Owns transparency publication truth, reporting-period artifacts, public trust publication state, and reporting lineage.

### Public Registry Domain
Owns publication-safe contract and wallet registry outputs and correction-safe public reference artifacts.

### Payout Orchestration Domain
Owns off-chain payout-cycle orchestration, funding-intent coordination, reconciliation posture, and execution preparation before or alongside chain execution.

### Control / Governance-Aware Operations Domain
Owns operator-sensitive and governance-sensitive control actions, approval state, remediation pathways, rollout controls, and incident-aligned action constraints.

### Integration Adapter Domain
Owns provider-specific normalization boundaries, provider configuration metadata, and outbound/inbound integration mediation.

These shared domains may internally collaborate, but they may not erase their ownership boundaries.

---

## Product Extension Architecture

Products are not independent platforms. Each product must be implemented as a bounded extension domain using shared platform capabilities.

A product extension domain may own:
- product-local entities
- product-local workflows
- product-local AI behaviors or prompt patterns
- product-local UX and reports
- product-local configuration
- product-local operational views

A product extension domain may consume:
- account and identity context
- session state
- workspace context
- authorization and entitlement results
- billing and credits primitives
- AI orchestration and routing services
- workflow/automation services
- audit and reporting services
- notifications and scheduling where applicable

A product extension domain may not own:
- alternate account identity
- alternate session truth
- alternate workspace authority
- alternate credits truth
- alternate payout truth
- alternate public registry truth
- alternate control-plane authority for shared operations

---

## Product-Surface and Platform-Surface Responsibilities

### `fuze-frontend-webapp`
- first-party product and platform experience surface
- consumes backend APIs
- owns presentation state and local drafts only
- may initiate canonical flows but does not own them

### `fuze-frontend-admin`
- privileged operator/admin/control interface
- triggers review, remediation, approval, and operational actions under stronger controls
- does not own durable truth

### `fuze-backend-api`
- primary off-chain backend runtime
- owns domain services, internal APIs, orchestration entrypoints, workflow coordination APIs, integration ingress, reporting publication backends, and control-plane backend logic
- must preserve domain and plane separation internally even if deployed as one broader backend estate

### `fuze-contracts`
- owns contract code and chain-native truth where explicitly committed on-chain
- does not own broader off-chain platform policy

### `fuze-public-registry`
- publishes public-safe derived or publication-owned trust artifacts
- does not redefine underlying source truth

### Future `fuze-sdk`
- derives from approved contracts
- does not own source truth

---

## Runtime Interaction Model

The canonical FUZE interaction pattern is layered:

1. request or signal arrives through an edge or integration boundary
2. application plane resolves identity/session/workspace/access context where relevant
3. owning domain validates and decides whether to accept mutation or read
4. if immediate completion is possible, the application plane records canonical outcome directly
5. if deferred work is needed, the application plane creates accepted execution intent and hands off to the execution plane
6. execution plane coordinates async work using owning-domain APIs rather than direct shadow mutation
7. integration plane verifies and normalizes provider or chain-related signals before they influence canonical platform state
8. reporting plane derives publication/read models downstream from canonical sources
9. control plane constrains or remediates sensitive actions through explicit pathways
10. on-chain interactions occur through chain-adjacent coordination while preserving contract-truth vs off-chain-policy separation

---

## Synchronous and Asynchronous Architecture

FUZE requires both synchronous and asynchronous execution modes.

### Synchronous Mode
Use when:
- validation and state transition are quick
- user-facing immediacy is required
- no long-running provider or chain coordination is needed

### Asynchronous Mode
Use when:
- processing is long-running
- external callbacks or provider lag are expected
- retries or ordered step progression are required
- multi-domain coordination is necessary
- chain submission or later confirmation is required
- approval or remediation checkpoints may occur

Architecture rule:
- async acceptance must be explicit
- accepted async intent is not equivalent to final business success
- the execution plane must expose lineage without replacing the owner of underlying business truth

---

## API / Contract Architecture Implications

This platform architecture requires API design to reflect architecture rather than frontend convenience.

Required implications:
- API surfaces must distinguish platform, product, internal, admin/control, public, reporting, and chain-adjacent families
- canonical writes must terminate in owning domains
- internal service APIs must preserve ownership-aligned collaboration
- public APIs must remain narrower and safer than internal surfaces
- admin/control-plane actions must remain explicitly privileged and auditable
- derived-public or registry surfaces must not masquerade as internal mutation surfaces
- chain-adjacent APIs must expose coordination, not false ownership of chain truth

---

## Event / Async Architecture Implications

Events are coordination and propagation mechanisms, not substitutes for canonical ownership.

Required implications:
- meaningful state changes may emit events from owning domains
- execution-plane progression may emit lifecycle events for coordination and observability
- provider or chain events must be normalized before becoming platform-owned outcomes
- retries and replay must preserve idempotent business meaning
- async execution lineage must remain explicit for audit and remediation

---

## Data / Storage Architecture Implications

The platform architecture requires storage discipline aligned to domain ownership and plane separation.

Required implications:
- canonical transactional records must stay with owning domains
- execution records must remain distinct from business-domain records
- provider raw input and normalized outcomes must remain distinguishable
- chain-derived records and off-chain policy records must remain distinguishable
- reporting/read models must remain downstream and derivable
- caches, search indexes, and dashboard stores must not become unofficial write owners
- product-local data must remain distinct from shared-platform data even when co-located physically

---

## Security / Risk / Abuse Architecture

Security posture is architectural, not purely operational.

Required architecture rules:
- platform-owned truth must not be mutable from ungoverned product-local shortcuts
- integration-plane trust boundaries must not be bypassed
- admin/control capabilities require stronger authorization and audit posture
- economic and governance-sensitive flows require stricter boundaries than ordinary product requests
- wallet-aware participation must remain bounded by account-rooted identity architecture
- provider degradation or fraud signals must not directly mutate high-value truth without normalization and policy checks

---

## Audit / Traceability Requirements

The architecture must make it possible to determine:
- which domain owns the affected truth
- which plane processed the action
- whether a state transition was synchronous, async, control-plane, provider-normalized, reporting-derived, or chain-adjacent
- which service or actor initiated the work
- whether the visible state is canonical, execution, derived, or presentational
- how upstream intent connects to downstream execution and publication

Auditability must remain derivable from architecture rather than bolted on later.

---

## Failure Handling / Edge Cases

### Domain-Ownership Ambiguity
If architecture cannot clearly identify the owning domain, the design is incomplete and must escalate to ownership clarification rather than being implemented ad hoc.

### Provider Failure
Integration failures must preserve the distinction between unavailable provider outcome and changed platform truth.

### Workflow / Worker Failure
Execution failure must not be misinterpreted as automatic rollback of already committed business truth unless owning domains explicitly define that compensation behavior.

### Reporting Lag
Reporting/publication surfaces may lag source truth but must reconcile without rewriting canonical source records.

### Control-Plane Unavailability
Sensitive actions must fail safely rather than converting into ungated ordinary runtime behavior.

### Chain Visibility Lag
Indexing or RPC lag must not be mistaken for changed chain truth.

### Product Isolation
Product-specific faults must not corrupt shared platform primitives.

### Mixed-Plane Partial Completion
If application-plane acceptance occurred but later execution, provider, or chain stages fail, the system must preserve lineage of partial completion rather than collapse state into a misleading binary.

---

## Operational Considerations

The refined architecture intentionally does not force one deployment style, but it does require:

- architectural separation even if multiple domains are deployed within one backend estate
- observability that distinguishes plane and domain boundaries
- runbooks and remediation flows that identify canonical owners before action
- rollout discipline that favors reuse of shared domains over local reimplementation
- explicit provider adapters and chain-adjacent services rather than scattered direct calls
- safe operator tooling aligned to control-plane boundaries
- migration paths that preserve ownership semantics and lineage

---

## Migration / Compatibility / Supersession Considerations

This refined platform architecture supersedes looser interpretations in which:
- products behave as mini-platforms
- frontend or reporting layers own business truth
- workflow or AI runtime is treated as owner of all downstream effects
- provider callbacks directly become platform truth
- contracts are treated as owners of off-chain business policy
- admin tools silently bypass domain ownership

Compatibility layers may exist temporarily, but they must preserve canonical ownership and explicit plane separation.

This refined file also intentionally avoids locking FUZE into a premature deployment topology. Service decomposition may evolve over time, but the architectural boundaries in this document remain governing.

---

## Dependencies / Cross-Spec Links

This specification depends on:
- `REFINED_SYSTEM_SPEC_INDEX.md`
- `DOCS_SPEC_INDEX.md`
- `SYSTEM_SPEC_INDEX.md`
- `API_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- identity/account/auth/session foundation docs
- workspace/access-control foundation docs
- `API_ARCHITECTURE_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- workflow, AI, monitoring, migration, and reporting specs that further refine execution and operations

This specification directly governs:
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
- `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md`
- `API_ARCHITECTURE_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `JOB_QUEUE_AND_WORKER_SPEC.md`
- `AI_ORCHESTRATION_SPEC.md`
- `MODEL_ROUTING_AND_CONTEXT_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `MONITORING_ALERTING_AND_INCIDENT_RESPONSE_SPEC.md`
- all product integration specifications

---

## Explicitly Deferred Items

The following are intentionally deferred:
- exact service topology and service-split timing
- exact queue and event-bus technologies
- exact database/warehouse/search-stack selection
- exact edge-gateway and mesh configuration
- exact provider-by-provider adapter internals
- exact contract ABI and indexer implementation detail
- exact frontend composition architecture
- exact deployment graph across environments

These are implementation-detail refinements of the architecture, not replacements for it.

---

## Final Normative Summary

FUZE must be implemented as a platform-first shared-core architecture with bounded product-extension domains. The architecture is organized around distinct planes: application, execution, integration, reporting, and control, with the on-chain contract layer treated as an adjacent explicitly bounded truth layer rather than a replacement for off-chain platform architecture.

`fuze-backend-api` is the primary off-chain backend runtime and must preserve domain and plane separation internally. `fuze-frontend-webapp` and `fuze-frontend-admin` are surfaces, not owners of durable business truth. `fuze-contracts` own only explicitly committed chain truth. `fuze-public-registry` publishes public-safe artifacts without redefining source truth. Products extend the platform but may not reinvent shared platform primitives.

All downstream FUZE specifications must preserve this architecture. If a later design weakens platform-first shared-core behavior, erases plane separation, or hides ownership through convenience shortcuts, this specification governs.
