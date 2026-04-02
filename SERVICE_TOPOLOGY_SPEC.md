# SERVICE_TOPOLOGY_SPEC

## Purpose

This document defines the canonical service topology of the FUZE platform. Its purpose is to specify how the platform should be decomposed into service domains, runtime responsibilities, interaction boundaries, and integration surfaces so that implementation remains consistent with FUZE’s platform-first architecture.

This specification translates the platform and domain ownership model into an implementation-oriented service map. It is intended to guide backend architecture, repository boundaries, deployment planning, API ownership, async workflow design, and future product integration.

---

## Scope

This specification covers:

- the canonical service topology for the shared FUZE platform
- the separation between platform services, product services, async workers, and chain integration services
- service ownership and service-level responsibility boundaries
- synchronous versus asynchronous interaction patterns
- service communication rules
- service data ownership rules
- shared services versus product extension services
- control-plane and governance-sensitive service boundaries
- minimum topology requirements for observability, failure isolation, and future scale

This specification does not define the full payload-level API contracts, schema-level table design, or event-field design. Those belong in:

- `API_ARCHITECTURE_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`

---

## Design Goals

The service topology of FUZE is designed to:

1. preserve the platform-first architecture of FUZE
2. ensure that shared platform concerns are owned by shared platform services
3. isolate product-specific logic from shared platform primitives
4. separate synchronous user traffic from heavy async or chain-dependent execution
5. preserve clean ownership of token, credits, payouts, reserves, and governance-sensitive flows
6. support future product expansion without requiring platform redesign
7. improve reliability through bounded service responsibilities
8. improve observability and auditability through explicit service contracts
9. remain compatible with phased implementation from a modular monolith toward service-oriented decomposition if needed

---

## Non-Goals

This topology is not intended to:

- force a premature microservice split where a modular monolith is operationally better
- allow products to own shared platform primitives
- mix chain integration code directly into unrelated application services
- collapse audit, control, and reporting responsibilities into product runtime code
- create a separate internal economic system for each product
- allow one service to become the hidden owner of many unrelated domains

The topology is an ownership and decomposition model first. Deployment shape may evolve, but ownership rules must remain stable.

---

## Canonical Service Topology Principle

The primary service-topology principle of FUZE is:

> services must be decomposed around canonical domain ownership, not around incidental UI screens, temporary implementation convenience, or product-specific shortcuts.

This means:

- platform-wide domains should map to platform-owned services or modules
- product-owned domains should map to product-owned services or bounded modules
- chain-facing domains should be isolated behind explicit adapters or chain services
- async execution should be separated from request-response paths where durability, retries, or heavy execution are required
- governance-sensitive actions should pass through explicitly controlled services

---

## Topology Overview

The canonical FUZE topology is divided into the following service groups:

### 1. Edge and Experience Services
Services that terminate external requests and expose public, product, admin, or partner-facing interfaces.

### 2. Platform Core Services
Shared platform-owned services for identity, workspaces, credits, billing, wallet-aware context, AI orchestration, workflow coordination, audit, reporting, and control.

### 3. Product Domain Services
Product-specific bounded services for QTB, AIMM, ZAGA, AIE, HerHelp, Botmad, and future products.

### 4. Async Execution Services
Queue-backed workers, scheduled jobs, reconciliation loops, retry handlers, heavy AI execution workers, and report-generation workers.

### 5. Chain Integration Services
Services responsible for contract reads, contract writes, snapshot ingestion, credits ledger commitment, payout funding coordination, and contract event ingestion.

### 6. Governance and Control Services
Services that govern sensitive actions, approval workflows, control-plane behaviors, policy enforcement, and privileged operations.

### 7. Reporting and Transparency Services
Services that generate derived reporting views, payout ledgers, transparency records, public registries, and investor/community outputs.

### 8. Integration Adapter Services
Services or bounded adapters for Stripe, stablecoin payment flows, Telegram Stars, app-store billing, AI providers, wallet providers, RPC/indexing providers, and notification providers.

This service grouping is the canonical topology of FUZE.

---

## Recommended Implementation Shape

FUZE should be implemented using a **modular platform core with explicit service boundaries**, with the ability to evolve into distributed services where justified.

This means the platform may begin as:

- a modular monolith with strong internal domain boundaries, or
- a hybrid architecture where some services are deployed independently and others remain in a bounded core runtime

The following principles must hold regardless of deployment style:

1. domain ownership must remain explicit
2. platform services must not be dissolved into product services
3. product services must not own shared platform truth
4. async work must remain durable and retryable
5. chain logic must remain isolated behind explicit service boundaries
6. control-plane logic must remain separated from ordinary runtime traffic

The deployment strategy may be phased. The topology is still normative.

---

## Edge and Experience Services

The edge layer is responsible for receiving traffic from external clients and routing requests into the correct platform or product service boundaries.

### Canonical Edge Surfaces

- Web app/API edge
- Product-specific API edge surfaces
- Admin/control-plane edge
- Partner/integration-facing edge
- Webhook ingestion edge
- Future mobile or embedded edge surfaces
- Future mini-app or Telegram-adjacent edge surfaces

### Edge Responsibilities

- request authentication handoff
- session and identity context resolution
- routing to platform-owned or product-owned service boundaries
- request validation
- rate limiting and abuse guardrails
- basic response shaping
- correlation and trace ID attachment

### Edge Non-Responsibilities

The edge layer must not own:
- business policy interpretation
- credits mutation logic
- payout logic
- reserve logic
- AI routing policy
- product domain state
- contract interaction business decisions

The edge layer is a traffic and interface boundary, not a business domain owner.

---

## Platform Core Services

The following platform services are canonical shared services.

### 1. Identity Service
Owns:
- user account records
- identity lifecycle
- linked login relationships
- account security state

It provides identity truth to all products.

### 2. Auth and Session Service
Owns:
- session issuance
- token/session revocation
- session validation
- auth handoff and auth-provider integration

It is separate from identity because session execution and identity truth are different concerns.

### 3. Workspace and Organization Service
Owns:
- organizations
- workspaces
- membership
- workspace ownership
- collaboration context
- billing-owner relationships

### 4. Role and Access Service
Owns:
- authorization rules
- role resolution
- permission evaluation
- access grants and restrictions

### 5. Wallet-Aware Participation Service
Owns:
- wallet linking
- wallet-to-account association
- multi-wallet context
- holder-aware derived context
- token-aware account enrichment inputs

It does not own token balance truth itself.

### 6. Commerce and Billing Service
Owns:
- subscription state
- usage charging orchestration
- billing policy enforcement
- entitlement charging decisions
- invoice/receipt coordination hooks

### 7. Platform Credits Service
Owns:
- credit classes
- credit issuance policy
- spend rules
- adjustment rules
- reversal rules
- internal credits balance coordination
- commitment requests to the Base credits ledger

### 8. Payment Normalization Service
Owns:
- intake of verified payment events
- conversion of external payment state into FUZE-internal payment truth
- mapping into issuance eligibility
- payment-rail abstraction

### 9. AI Orchestration Service
Owns:
- model selection
- prompt-context assembly
- task routing
- AI policy enforcement
- fallback routing
- AI usage accounting hooks

### 10. Workflow Orchestration Service
Owns:
- workflow definitions at platform level
- multi-step action coordination
- event-trigger routing
- async delegation
- retries orchestration decisions

### 11. Audit Log Service
Owns:
- append-only audit-event generation
- action trace records
- sensitive-action audit trails
- platform-wide activity events

### 12. Transparency and Reporting Coordination Service
Owns:
- transparency data assembly pipelines
- public registry composition
- payout-ledger coordination
- reporting source joins
- derived reporting generation orchestration

### 13. Control Plane Service
Owns:
- privileged admin actions
- policy-governed overrides
- emergency controls
- approval-triggered platform operations
- governance-aware off-chain control pathways

### 14. Integration Service
Owns:
- provider adapters
- webhook ingestion coordination
- outbound integration contracts
- provider status normalization
- integration health visibility

These are the minimum canonical platform services.

---

## Product Domain Services

Each FUZE product must be represented as its own domain-bounded service or bounded module.

### QTB Service
Owns:
- trading intelligence objects
- QTB-specific signal and analysis workflows
- QTB-specific presentation and execution logic
- QTB-specific AI task definitions on top of shared AI orchestration

### AIMM Service
Owns:
- market-operations workflow objects
- liquidity intelligence support models
- AIMM operational states
- AIMM-specific automation logic

### ZAGA Service
Owns:
- token utility operating objects
- utility logic and token-operations workflows
- ZAGA-specific configuration and outputs

### AIE Service
Owns:
- event intelligence objects
- discovery and recommendation workflows
- event classification logic
- AIE-specific views and triggers

### HerHelp Service
Owns:
- sheet-to-app transformation domain objects
- business app generation logic
- SME workflow domain models
- HerHelp-specific automation and AI usage flows

### Botmad Service
Owns:
- work-execution objects
- desktop-workflow artifacts
- task-monitoring and assistance states
- Botmad-specific automation and AI coordination logic

### Future Product Services
Any future product must:
- remain domain-bounded
- reuse platform services
- avoid re-implementing shared primitives
- explicitly declare its dependencies on platform and chain services

---

## Async Execution Services

FUZE requires explicit async execution services because many important workflows are durable, heavy, retryable, or externally dependent.

### Async Service Types

#### 1. Queue Worker Service
Processes:
- credits operations
- billing follow-ups
- AI jobs
- workflow continuation tasks
- reconciliation tasks
- contract event handling follow-ups

#### 2. Scheduler Service
Processes:
- recurring billing jobs
- report generation schedules
- payout-cycle preparation triggers
- cleanup and archive jobs
- retry sweeps
- policy-driven timed actions

#### 3. Reconciliation Worker Service
Processes:
- payment reconciliation
- ledger reconciliation
- payout-cycle reconciliation
- provider-state reconciliation
- reporting consistency scans

#### 4. Heavy AI Worker Service
Processes:
- long-running AI tasks
- expensive orchestration jobs
- batched summarization or generation tasks
- multi-stage reasoning jobs where supported

#### 5. Reporting Worker Service
Processes:
- transparency report generation
- payout-ledger materialization
- investor/community reporting exports
- large derived views

### Async Topology Rules

- async jobs must be idempotent or explicitly idempotency-aware
- queue consumers must not silently redefine canonical truth
- retries must be bounded and observable
- partial completion must be recorded explicitly
- async execution that affects billing, credits, or payouts must generate audit records

---

## Chain Integration Services

Chain integration must be isolated into explicit services or bounded adapters.

### 1. Ethereum Token Read Service
Owns:
- token balance reads
- snapshot-support reads
- token-related holder context reads
- contract metadata reads where relevant

It does not own business interpretation of balances beyond providing chain truth access.

### 2. Base Credits Ledger Service
Owns:
- on-chain credits commitment writes
- credits ledger state reads
- commitment confirmation tracking
- chain event ingestion relevant to credits

### 3. Base Payout Execution Service
Owns:
- payout funding coordination writes
- payout contract reads
- claim-state sync
- claim-event ingestion
- payout execution confirmation tracking

### 4. Snapshot and Eligibility Support Service
Owns:
- snapshot ingestion workflows
- eligibility dataset assembly support
- cross-chain mapping between Ethereum holder truth and payout-cycle preparation

### 5. Contract Event Ingestion Service
Owns:
- ingestion of relevant contract events
- normalization of event streams into internal platform-readable records
- downstream fanout into audit and reporting pipelines

### Chain Service Rules

- no product service should write directly to contracts without going through chain service boundaries
- chain reads used for product UX may be cached, but caches are non-canonical
- chain services must expose explicit success, pending, failed, and unknown states to upstream orchestrators
- chain retries and nonce/transaction handling must remain isolated from ordinary product runtime logic

---

## Governance and Control Services

Sensitive actions require dedicated control services.

### Control Plane Service
The central privileged off-chain control service.

Owns:
- privileged admin actions
- controlled operational overrides
- policy-gated actions
- emergency control triggers
- high-impact service operation approvals

### Governance Action Coordinator
A bounded service or module that coordinates:
- actions requiring multisig awareness
- timelock-aware off-chain preparation
- governance-sensitive change workflows
- reserve-related operational controls where off-chain coordination is required

### Secrets and Provider Control Service
Owns:
- provider credential rotation workflows
- scoped config changes
- integration kill-switches where applicable
- environment-specific provider safety controls

### Governance Service Rules

- governance-sensitive actions must not be executed through ordinary product endpoints
- privileged service paths must produce strong audit traces
- governance-aware services must surface pending/approved/rejected states
- services must remain compatible with on-chain multisig/timelock governance where relevant

---

## Reporting and Transparency Services

The following reporting-oriented services are canonical.

### Transparency Reporting Service
Owns:
- derived transparency views
- public transparency data assembly
- reserve and contract explanatory mapping
- role-aware reporting joins

### Payout Ledger Service
Owns:
- payout-cycle reporting views
- claim-state presentation logic
- funding-state reporting
- payout-cycle public references

### Public Registry Service
Owns:
- public contract registry
- public wallet registry
- role/category explanations for public visibility

### Investor and Community Reporting Service
Owns:
- stakeholder-specific reporting outputs
- report materialization
- export and publication coordination

These services are derived-data services. They do not redefine canonical platform or chain truth.

---

## Integration Adapter Services

External integrations should be decomposed into explicit adapters or bounded adapter modules.

### Required adapters include:

- Stripe adapter
- Stablecoin payment adapter
- Telegram Stars adapter
- Apple billing adapter
- Google billing adapter
- AI provider adapter(s)
- Wallet provider/session adapter(s) where applicable
- RPC/indexing adapter(s)
- Notification adapter(s)

### Adapter Rules

- adapters normalize external provider semantics into FUZE-internal semantics
- adapters do not own platform business rules
- adapters must surface raw-source context where needed for audit and reconciliation
- adapters must support degraded-state handling and observability
- adapters must not directly mutate unrelated canonical domains without platform orchestration

---

## Service Communication Model

FUZE services may communicate through the following patterns.

### 1. Synchronous calls
Used for:
- identity resolution
- authorization checks
- workspace context reads
- balance reads
- immediate product state reads
- lightweight business decisions

### 2. Async job dispatch
Used for:
- long-running work
- retries
- reconciliation
- AI tasks
- report generation
- contract interaction follow-ups

### 3. Event publication
Used for:
- domain state changes
- audit fanout
- reporting inputs
- workflow triggers
- non-blocking downstream reaction patterns

### Communication Rules

- synchronous calls should be used when immediate consistency is required for user experience
- async dispatch should be used when latency, durability, or retries matter more than immediate completion
- event publication should not be used to hide canonical ownership
- service interactions that affect credits, payouts, or sensitive policy domains must be traceable

---

## Data Ownership by Service

Each service may write only to domains it canonically owns, unless a delegated write contract exists.

### Examples

- Identity Service writes identity data
- Workspace Service writes workspace data
- Platform Credits Service writes credits policy state and internal balance coordination data
- Base Credits Ledger Service writes credits commitment state to chain-facing stores/adapters
- QTB Service writes QTB domain objects
- Payout Ledger Service writes derived payout reporting views, not payout claim truth itself

Derived services must not mutate canonical domain truth unless explicitly designed as orchestrators acting through owned service boundaries.

---

## Minimum Service Topology for Initial Implementation

At minimum, FUZE must have explicit bounded modules or deployable services for:

1. Identity/Auth
2. Workspace/Access
3. Wallet-Aware Participation
4. Billing/Commerce
5. Platform Credits
6. Payment Normalization
7. AI Orchestration
8. Workflow Orchestration
9. Async Workers/Scheduler
10. Audit Logging
11. Control Plane
12. Chain Integration
13. Product Domains
14. Reporting/Transparency

Even if these begin inside a modular monolith, the boundaries must be explicit in code and data ownership.

---

## Topology Evolution Rules

The topology may evolve over time, but only in ways that preserve ownership clarity.

### Acceptable evolution patterns

- modular monolith to bounded services
- splitting heavy async workers from core runtime
- isolating chain interaction services further as volume grows
- isolating reporting materialization services as transparency load grows
- isolating high-scale product services as product demand diverges

### Unacceptable evolution patterns

- moving shared platform ownership into product services
- embedding chain writes in arbitrary business handlers
- collapsing control-plane logic into ordinary admin endpoints without separation
- treating reporting views as canonical truth because they are convenient
- creating alternative product-local credits systems outside platform ownership

---

## Security Implications

The service topology is also a security structure.

Proper service boundaries reduce the chance that:

- product services mutate platform truth incorrectly
- chain writes occur through unauthorized paths
- governance-sensitive actions execute through ordinary runtime code
- external provider failures directly corrupt internal state
- audit trails are bypassed
- sensitive credentials are spread across unrelated services

All downstream security, abuse-prevention, and incident-response specifications must respect the topology defined here.

---

## Observability Requirements

Every canonical service boundary must support observability for:

- request tracing
- job execution tracing
- chain interaction state
- credits mutations
- payout-cycle operations
- AI task execution
- provider degradation
- control-plane actions
- audit fanout status
- product integration failures

No critical service should be operationally opaque.

---

## Edge Cases and Failure Handling

### Product service unavailable
Shared platform services must remain operationally isolated where possible. Product failure must not corrupt shared identity, billing, credits, or chain state.

### Chain adapter degraded
Chain integration services may enter degraded state, but product and reporting layers must distinguish degraded read visibility from changed canonical truth.

### Async backlog growth
Heavy async domains must degrade visibly and controllably rather than silently delaying credits, payouts, or critical reporting without trace.

### Provider adapter mismatch
External adapter inconsistency must be normalized through reconciliation workflows rather than directly propagated into canonical platform truth.

### Reporting pipeline failure
Derived reporting services may lag, but they must not block canonical billing, credits, or payout execution domains.

---

## Open Items

The following items are intentionally deferred to downstream specs:

- exact deployable-service boundaries for the first production version
- exact queue and broker technology
- exact data-store segmentation by service
- exact event bus design
- exact language/framework boundaries by service
- exact gateway/API composition model

These do not weaken the service topology. They are implementation-shape choices beneath the canonical ownership model.

---

## Closing Summary

The FUZE service topology is built around explicit platform-owned core services, product-owned domain services, bounded async execution services, isolated chain integration services, governance-aware control services, reporting/transparency services, and explicit external integration adapters. This topology preserves FUZE’s platform-first architecture, keeps economic and chain logic separated from product runtime convenience, and provides a scalable structural model for future product growth. Whether deployed initially as a modular monolith or as a more distributed system, implementation must preserve the service ownership boundaries defined in this specification.
