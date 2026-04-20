# SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC

## Document Metadata

- Document Name: `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Governing Layer: Platform constitution / boundary / ownership
- Primary Audience: Platform architecture, product architecture, engineering, security, finance systems, governance, operations, reporting
- Primary Purpose: Define the top-level canonical system view of FUZE, including what FUZE is, what layers compose the ecosystem, what boundary rules apply between those layers, and how downstream specifications must interpret platform responsibility
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Upstream References:
  - `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
  - `PLATFORM_ARCHITECTURE_SPEC.md`
  - `SYSTEM_SPEC_INDEX.md`
  - `API_SPEC_INDEX.md`
  - `DOCS_SPEC_INDEX.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
  - `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
- Primary Downstream Dependents:
  - `PLATFORM_ARCHITECTURE_SPEC.md`
  - `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
  - `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
  - `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
  - `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
  - `API_ARCHITECTURE_SPEC.md`
  - all identity, workspace, entitlement, commerce, credits, AI, workflow, transparency, governance, treasury, and chain execution specs

---

## Purpose

This specification defines the canonical top-level system overview and system boundaries of the FUZE platform ecosystem.

Its purpose is to establish the stable architectural interpretation layer that all downstream FUZE system specifications must inherit. It defines what FUZE is at the platform level, what major layers exist in the ecosystem, what each layer is responsible for, which categories of truth belong to which layers, and which boundary rules must remain non-negotiable as the platform expands.

This document is intentionally foundational. It is not an implementation-detail file for one subsystem. It is the system-level framing document that defines how FUZE should be understood before narrower architecture, API, product, financial, governance, or chain-specific specs are interpreted.

---

## Scope

This specification governs:

- the canonical definition of FUZE as a platform-first ecosystem
- the top-level structural layers of the ecosystem
- the distinction between platform, product, on-chain, and external domains
- the distinction between off-chain application truth and explicitly on-chain truth
- the distinction between shared platform capability and product-specific extension
- the non-negotiable economic separations across token, credits, revenue, profit, and payouts
- the high-level runtime layer model used to reason about synchronous, asynchronous, reporting, control-plane, and chain-mediated execution
- the top-level trust-boundary model for third-party dependencies
- the top-level rules for how future specifications must assign ownership and interpret system behavior
- the hierarchy of architectural precedence when downstream specifications appear to overlap or conflict

This document does not define:

- entity-by-entity schema ownership
- detailed API endpoint contracts
- detailed service topology or deployment shape
- detailed auth/session mechanics
- detailed workspace, entitlement, billing, ledger, payout, or governance workflows
- detailed chain contract design
- detailed incident procedures or low-level observability implementation

Those belong in downstream refined specifications.

---

## Out of Scope

This document is explicitly out of scope for:

- team organization charts or human reporting lines
- repository layout decisions
- vendor-specific infrastructure implementation
- UI page design or product UX flows
- product-specific domain logic
- policy-level accounting treatment details beyond boundary clarification
- detailed approval trees for governance-sensitive actions
- smart-contract ABI-level or storage-level details
- queue technology, database technology, or runtime framework selection

---

## Design Goals

The design goals of this specification are:

1. Define FUZE as one coherent platform ecosystem rather than a loose portfolio of products.
2. Preserve platform-first architecture as the default rule for cross-product capabilities.
3. Make system layers and trust boundaries explicit enough for architecture, API, and data specs to inherit without ambiguity.
4. Preserve strict economic separation between participation assets, internal consumption assets, and payout assets.
5. Preserve strict responsibility separation between off-chain business logic and explicitly committed on-chain truth.
6. Prevent product fragmentation, shadow platforms, and hidden ownership drift.
7. Preserve clear boundaries between identity, authentication, sessions, workspaces, authorization, entitlements, billing, credits, revenue, profit, payouts, and reserves.
8. Support future product expansion without requiring redefinition of platform primitives.
9. Make security, auditability, governance, and transparency architectural properties rather than afterthoughts.
10. Provide a stable interpretive layer for downstream system specs and API specs.

---

## Non-Goals

FUZE is not intended to be:

- a single-product application disguised as a platform
- a generic token project whose architecture is primarily narrative-driven
- a fully on-chain application runtime
- a per-product backend portfolio with duplicated core systems
- a system where products may redefine platform primitives for convenience
- a system where reporting or public presentation becomes the source of record
- a system where token, credits, revenue, profit, payouts, and reserves collapse into one economic concept
- a system where governance-sensitive authority is silently exercised through ordinary runtime flows
- a system where external providers become implicit long-term truth owners for FUZE-owned business meaning

---

## Core Principles

### 1. Platform-First Principle
Shared platform capabilities must be owned once at the platform layer and reused across products unless a clearly bounded product-specific exception is formally declared.

### 2. Explicit Boundary Principle
Every major category of system concern must belong to a clearly defined layer with clear mutation, execution, and interpretation boundaries.

### 3. Single Canonical Owner Principle
Each meaningful category of truth must have one canonical owner. Other layers may derive, cache, render, or interpret within bounded rules, but may not silently redefine canonical meaning.

### 4. Off-Chain / On-Chain Separation Principle
Off-chain application logic and on-chain committed truth serve different roles and must not be collapsed into one model.

### 5. Product-Extension Principle
Products are extension domains built on the FUZE platform, not alternate platforms.

### 6. Governance-Aware Control Principle
Sensitive actions must remain distinguishable from ordinary runtime actions and must be routed through explicit control or governance-aware pathways when required.

### 7. Transparency-by-Structure Principle
Transparency must result from durable architecture, explicit ownership, auditable flows, and reportable state transitions, not only from narrative explanation.

### 8. Trust-Boundary Principle
External providers are dependencies crossing trust boundaries, not silent extensions of FUZE-owned truth.

---

## Canonical Definitions

### FUZE Platform
The shared operating foundation beneath the FUZE ecosystem that owns reusable cross-product capabilities such as identity, account continuity, workspaces, billing, credits, wallet-aware participation context, AI orchestration, workflow infrastructure, auditability, reporting, and governance-aware controls.

### Product Layer
The set of product-specific application domains built on top of FUZE, each of which owns product-specific workflows, data, interfaces, and category-specific business logic while remaining bound to shared platform rules.

### On-Chain Layer
The smart-contract and chain-committed state layer that owns only the categories of truth explicitly committed to contract design, such as token balances, credits-ledger commitments where applicable, payout execution state, reserve constraints, and certain governance-controlled state.

### External Dependency Layer
Any third-party service or runtime dependency not owned by FUZE, including payment processors, app-store billing systems, AI model providers, cloud services, RPC/indexing providers, wallets, messaging environments, and similar providers.

### Canonical Truth
The authoritative state and policy for a category of system meaning.

### Derived Truth
A summarized, joined, aggregated, or otherwise computed view that depends on canonical sources without replacing them.

### Presentation Layer
A rendering or user-facing representation of truth that may organize, explain, or visualize canonical and derived data without becoming the canonical owner.

### Execution Layer
A runtime layer that performs a state transition or process step but does not necessarily define the policy meaning of that step.

### Governance-Sensitive Action
Any action whose risk, external effect, financial significance, or contractual significance requires stronger-than-ordinary runtime control.

---

## Canonical Platform Definition

FUZE is a transparency-first, platform-first, multi-product SaaS ecosystem.

At the system-design level, FUZE must be understood as all of the following simultaneously:

- a shared operating layer
- a shared commercial layer
- a shared wallet-aware participation layer
- a shared AI orchestration and workflow layer
- a shared transparency and reporting layer
- a shared governance-aware control layer
- a product extension ecosystem built on top of those shared layers
- an ecosystem that integrates off-chain application logic with bounded on-chain financial and participation infrastructure

FUZE is not synonymous with any one product. The platform exists beneath products and remains structurally prior to them for shared concerns.

---

## System Boundaries

The FUZE ecosystem is divided into four canonical top-level system layers.

### 1. FUZE Platform Layer
The shared off-chain application and service layer.

The platform layer owns cross-product operating capabilities, cross-product commercial infrastructure, cross-product policy and orchestration logic, shared audit/reporting capability, and shared control-plane coordination.

### 2. FUZE Product Layer
The product-specific domain layer.

The product layer owns product-specific entities, workflows, interfaces, analytics, prompts, experience logic, and category-specific execution patterns that are not elevated to shared platform scope.

### 3. On-Chain Participation and Financial Layer
The explicit contract and chain-committed state layer.

This layer owns only the categories of truth expressly committed on-chain, such as token balances on Ethereum, credits commitments on Base where applicable, funded payout claims on Base, reserve/vault constraints, and certain governance restrictions.

### 4. External Dependency Layer
The third-party dependency layer.

This layer owns its own provider execution and raw provider state, but does not own FUZE’s internal interpretation, policy, canonical business meaning, or platform truth unless a downstream spec explicitly declares a normalization boundary.

These layers interact heavily, but they do not share undifferentiated ownership.

---

## Adjacent Boundaries

This specification must be interpreted together with the following adjacent boundary documents:

- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md` defines the ownership model and escalation logic for truth, execution, reporting, and governance-sensitive control.
- `PLATFORM_ARCHITECTURE_SPEC.md` defines the runtime architecture, platform service domains, execution layers, and platform interaction model.
- identity, auth/session, and workspace/access-control specs define the platform’s user, account, session, workspace, and authorization primitives.
- financial, ledger, payout, and chain specs refine economic and execution boundaries that are only stated here at the top level.
- API and integration specs refine how these boundaries are represented in contracts, service interfaces, and event flows.

This document wins on top-level system interpretation.
Downstream documents may refine detail, but must not violate the layer model, economic separation model, or platform-first framing declared here.

---

## Roles / Actors / Entities

At the top-level system-overview layer, the principal architectural actors are:

- end users
- workspace members and administrators
- product runtime services
- platform runtime services
- async workers and background processors
- reporting and transparency pipelines
- platform control-plane operators
- governance-constrained operational actors
- smart contracts on Ethereum and Base
- external providers and transport/access-path dependencies

This document does not define person-level permissions. It defines which system layers and actor classes exist and which categories of responsibility they may own.

---

## Ownership Model

### Platform-Owned Categories
The platform layer owns, at minimum:

- canonical account-rooted user identity
- linked login methods and session/security state
- workspace and organization models
- wallet-link mapping and account-associated participation context
- shared billing and commerce orchestration
- Platform Credits policy, issuance logic, spend orchestration, adjustments, and internal balance semantics
- platform-wide entitlements and cross-product capability control where applicable
- shared AI orchestration and workflow primitives
- audit and transparency pipelines
- payout-cycle orchestration before on-chain funding
- provider normalization logic where FUZE internal truth depends on external events
- platform control-plane behavior and governance-aware operational constraints

### Product-Owned Categories
Products own, at minimum:

- product-specific entities and data models
- product-specific interfaces and experience flows
- product-specific AI prompts, workflows, and outputs
- product-specific usage semantics
- product-specific configuration within platform rules
- product-specific reports and views that do not redefine platform truth
- product-specific monetization configuration only within platform-owned commercial rules

### On-Chain-Owned Categories
The on-chain layer owns, at minimum:

- FUZE token balance truth on Ethereum
- contract-level token events and contract-governed restrictions
- Base credits commitment truth where credits are formally committed on-chain
- funded payout pool and claim execution truth on Base
- reserve, lock, vesting, or timelock constraints encoded in contracts
- contract-governed permissions and event history where applicable

### External-Owned Categories
External systems own, at minimum:

- raw provider execution state
- external payment-processor outcomes before FUZE normalization
- external purchase authorization state in app-store environments
- external messaging or mini-app runtime behavior
- AI model vendor execution environments
- infrastructure-provider runtime guarantees
- RPC/indexing access service behavior
- wallet software and client-side transaction environments

---

## Authority / Decision Model

Different kinds of authority must remain distinct.

### Canonical Policy Authority
Usually belongs to the platform layer for cross-product concerns.

Examples:
- identity rules
- billing rules
- credits rules
- wallet-link semantics
- platform entitlement framework
- payout-cycle orchestration policy

### Product Decision Authority
Belongs to product domains for product-specific concerns.

Examples:
- product UX patterns
- product object models
- product AI strategy
- product workflow variants
- product feature packaging within platform billing constraints

### Contract Execution Authority
Belongs to the relevant on-chain contract domains for explicitly committed chain actions and state transitions.

### Governance-Constrained Authority
Belongs to governance structures, multisig/timelock-protected pathways, or explicitly controlled high-impact platform operations.

### Presentation Authority
Belongs to reporting or interface layers for rendering and explanation only, never for canonical reinterpretation of owning-domain truth.

---

## State Model

The FUZE top-level state model is layered.

### 1. Platform Canonical State
Off-chain application truth owned by platform domains.

### 2. Product Canonical State
Product-domain truth owned by individual product domains.

### 3. Chain Canonical State
Explicitly committed on-chain truth owned by relevant contract layers.

### 4. External Source State
Raw provider state controlled by third parties and optionally normalized by FUZE.

### 5. Derived and Reporting State
Summarized, aggregated, joined, reconciled, or audience-specific state derived from canonical sources.

### 6. Presentation State
UI, dashboard, transparency page, and user-facing explanation layers.

When states disagree, the owning canonical layer wins over derived or presentation state.

---

## Lifecycle / Workflow Model

The canonical high-level FUZE system flow is:

1. an end user, service, or system process initiates an action
2. identity, account, session, and workspace context are resolved through platform-owned mechanisms
3. authorization, entitlement, and policy checks are applied in the appropriate platform and/or product boundaries
4. the platform orchestration layer determines whether the action is synchronous, asynchronous, chain-mediated, or mixed
5. product-specific domain logic executes where the action is product-owned
6. shared billing, credits, workflow, AI, reporting, or chain-integration behavior executes where relevant
7. canonical state changes are recorded by the appropriate owners
8. audit, traceability, and transparency-relevant outputs are generated where required
9. reporting and presentation layers render the resulting state without becoming the source of record

This workflow model is intentionally platform-mediated for shared concerns.

---

## Invariants

The following invariants are mandatory.

1. FUZE remains platform-first.
2. Products may extend the platform but may not redefine shared platform primitives without explicit platform-level approval and spec change.
3. Canonical account identity remains platform-owned and account-rooted.
4. Authentication methods, sessions, workspaces, and authorization remain distinct concerns even when closely coordinated.
5. Wallet awareness does not replace account identity.
6. FUZE token, Platform Credits, revenue, profit, stablecoin payouts, and reserve categories remain separate concepts.
7. Ethereum remains the canonical token participation layer.
8. Base remains the operational credits and payout execution layer where those capabilities are committed on-chain.
9. Off-chain business logic must not pretend to own explicitly on-chain truth.
10. On-chain contracts must not be treated as owners of application domains they were not designed to represent.
11. External providers remain trust-boundary systems and may not silently become FUZE’s canonical business owner.
12. Reporting and presentation surfaces must not overwrite or redefine canonical source state.
13. Governance-sensitive actions must not be executed through ordinary runtime convenience paths when stronger control is required.
14. A failure in one product or one provider must not redefine shared platform truth.

---

## Functional Rules

### Rule 1: Platform Before Product
If a capability is cross-product, economically shared, identity-defining, governance-sensitive, or foundational to future products, it defaults to platform scope unless a narrower explicit exception is declared.

### Rule 2: Products Are Extension Domains
Products may configure, consume, and extend platform capabilities, but they may not instantiate alternate versions of identity, workspace, credits, payout, reserve, or governance primitives.

### Rule 3: Chain Truth Is Narrow but Binding
On-chain truth is authoritative only for the categories explicitly committed to chain, but for those categories it is binding.

### Rule 4: External Inputs Must Be Normalized
Provider-originated events may influence FUZE state only through explicit verification, normalization, and policy interpretation boundaries.

### Rule 5: Reporting Is Downstream
Reporting, transparency, and user-facing presentation may summarize and explain, but may not silently redefine source meaning.

### Rule 6: Mixed Execution Is Allowed, Mixed Ownership Is Not
A workflow may cross request/response, async, and chain layers, but each state transition must still have one canonical owner.

### Rule 7: Ambiguity Must Escalate, Not Drift
If a downstream design cannot clearly identify the owner of a category of truth, decision, or state transition, that design is incomplete and must escalate to the relevant boundary or ownership spec.

---

## Permission / Access Considerations

This specification does not define detailed permissions, but it establishes the top-level boundary rules that permission systems must respect:

- identity is not authorization
- workspace context is not itself authority
- entitlement is not identical to role/permission
- product admin capability is not equivalent to platform control-plane authority
- governance-sensitive control is not ordinary runtime permission
- contract authority is not the same as application authorization

Downstream access-control specifications must preserve these distinctions.

---

## Entitlement Considerations

Entitlements are downstream to this system-overview spec, but this document establishes the following top-level rules:

- entitlements are a bounded layer between platform commerce/policy and product capability exposure
- entitlements must not silently redefine identity, workspace authority, billing truth, or credits truth
- product capability gating may be product-specific, but must operate within platform-owned entitlement and billing constraints where shared platform monetization applies

---

## API / Contract Implications

All downstream API specifications must reflect the top-level system boundaries declared here.

Required API-design implications include:

- APIs must make platform-owned, product-owned, and contract-mediated responsibilities distinguishable
- APIs must not imply that non-owning layers are canonical owners
- APIs must preserve separation between operational off-chain flows and on-chain committed state
- APIs must make control-plane operations distinguishable from ordinary runtime operations
- APIs must preserve identity/session/workspace/authorization/entitlement separations
- APIs must not expose presentation or projection layers as if they were sources of record
- public, authenticated product, internal service, admin, and governance-sensitive pathways must remain meaningfully segmented

---

## Event / Async Implications

The FUZE ecosystem includes substantial asynchronous and event-mediated behavior. This document establishes the following rules:

- async execution does not create alternate ownership
- events must be understood as propagation or coordination artifacts, not substitutes for owning-domain truth
- retries for billing, credits, payouts, and chain interactions must be designed so that replay does not invent new truth
- reporting pipelines may lag canonical state, but may not overwrite it
- chain event ingestion is an access and synchronization mechanism, not a license to relocate business policy into event-consumer code

Downstream event and idempotency specs must preserve these rules.

---

## Data Model / Storage Implications

This system-overview specification requires downstream data-model and storage designs to preserve layer boundaries.

At minimum:

- platform-owned entities must remain distinguishable from product-owned entities
- chain-derived records must remain distinguishable from off-chain application records
- provider-normalized state must remain distinguishable from raw provider input
- reporting/read models must remain distinguishable from canonical transactional state
- reserve, payout, credits, billing, and participation records must not collapse into a single undifferentiated economic persistence model
- caches, summaries, and projections must not become writable truth by accident

---

## Security / Risk / Abuse Controls

This document establishes the following top-level security and risk rules:

- ownership clarity is a security requirement, not only an architecture preference
- product-level failure or compromise must not grant product layers authority over platform-owned truth
- external provider inconsistency must not directly corrupt credits, entitlement, or payout meaning without explicit normalization
- wallet identity must not silently replace account identity
- control-plane operations must remain explicitly separable from ordinary product runtime operations
- governance-sensitive actions require stronger controls than ordinary end-user flows
- economic boundary violations are security and integrity risks, not merely reporting mistakes

---

## Audit / Traceability Requirements

This top-level boundary model requires downstream systems to support traceability for:

- which layer owns a category of truth
- which system executed a mutation
- whether an action was runtime, async, control-plane, or contract-mediated
- whether a user-facing report is canonical, derived, or presentational
- how external provider inputs were normalized into FUZE-owned outcomes
- how off-chain policy decisions relate to on-chain execution where applicable

Auditability is part of platform architecture, not a downstream reporting convenience.

---

## Failure Handling / Edge Cases

The following top-level failure rules are mandatory:

### Product Isolation Failure
Failure in one product domain must not corrupt shared platform identity, workspace, billing, credits, wallet-link, or reporting truth.

### Provider Degradation
Temporary payment-provider, AI-provider, or infrastructure-provider degradation must not silently mutate FUZE internal meaning. Degradation must be recorded explicitly.

### Chain Sync Lag
Delayed RPC or indexing visibility must not be confused with changed canonical chain meaning.

### Partial Mixed-Flow Completion
If a workflow partially completes across platform, async, and chain boundaries, the system must record incomplete orchestration rather than pretending fully completed truth.

### Control-Plane Interruption
Missing approval, policy denial, governance delay, or timelock delay must fail safely and explicitly.

### Reporting Lag
Reporting or transparency surfaces may lag source truth, but they must reconcile rather than overwrite.

### Ambiguous Ownership
When the owning layer is unclear, the system must escalate the ambiguity rather than absorb it through informal implementation.

---

## Operational Considerations

This specification imposes the following operational requirements on downstream architecture:

- runtime layers should align with the ownership and flow model rather than collapse boundaries for convenience
- platform observability must distinguish request-path, async, chain, reporting, and control-plane activity
- provider adapters must be treated as integration boundaries with explicit fallback/retry behavior
- public reporting and transparency publication must be downstream to canonical state owners
- product expansion should reuse platform primitives unless a documented exception is approved
- emergency and risk controls must constrain behavior without rewriting canonical source state

---

## Migration / Compatibility / Supersession Considerations

This refined document supersedes weaker or more ambiguous interpretations of FUZE as:

- a single-product system
- a token-first or chain-first architecture
- a per-product backend portfolio with loosely shared concerns
- a system where reporting, projections, or providers silently become canonical owners
- a system where products may invent alternate spending, identity, payout, or reserve semantics

Downstream legacy documents may preserve historical language, but where they conflict with this refined system-overview model, this refined specification governs the top-level interpretation.

---

## Dependencies / Cross-Spec Links

This specification depends on:

- `REFINED_SYSTEM_SPEC_INDEX.md` as the active refined-system registry
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md` for ownership categories, truth classes, and escalation logic
- `PLATFORM_ARCHITECTURE_SPEC.md` for runtime layers, shared platform domains, control-plane structure, and integration model
- identity/account/auth/session specs for account-rooted identity, session boundaries, and provider-link continuity
- workspace/authorization specs for workspace scope, roles, permissions, and access evaluation
- API architecture specs for contract-layer expression of these boundaries
- data and chain-responsibility specs for persistence and on-chain/off-chain truth refinement
- financial and treasury specs for credits, revenue, profit, payouts, reserves, and reporting boundaries

This specification governs:

- all downstream system interpretation involving top-level layer boundaries
- all downstream attempts to define or extend shared platform capability
- all downstream attempts to interpret platform vs product vs chain vs provider ownership
- all downstream attempts to reason about top-level economic and trust separation

---

## Explicitly Deferred Items

The following are intentionally deferred:

- precise service-topology and deployment-shape decisions
- exact cross-product entitlement composition model
- exact event bus and queue technologies
- exact database and tenancy topologies
- exact API surface partitioning details
- exact schema maps for every platform/product entity
- exact provider-by-provider trust and recovery contracts
- exact governance approval graphs for every sensitive action

These deferrals are implementation-detail refinements, not gaps in the top-level system model.

---

## Final Normative Summary

FUZE is the shared platform foundation beneath a multi-product SaaS ecosystem. It must be interpreted as a platform-first architecture with explicit separation between the platform layer, the product layer, the on-chain participation and financial layer, and the external dependency layer. Shared cross-product capabilities belong to the platform. Product-specific domains belong to products. Explicitly committed chain truth belongs to the relevant contracts and chains. Third-party systems remain external trust-boundary systems whose inputs must be normalized before they become FUZE-owned business outcomes.

This specification also makes the following separations non-negotiable: account identity versus wallet awareness; authentication/session versus authorization; workspace scope versus entitlements; token participation versus Platform Credits; revenue versus profit; profit versus funded payout; off-chain policy/orchestration versus on-chain execution; and canonical truth versus derived or presentational state.

All downstream FUZE specifications must remain consistent with this top-level system interpretation. If a downstream design weakens these boundaries, this document wins.
