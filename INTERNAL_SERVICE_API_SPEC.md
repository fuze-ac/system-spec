# INTERNAL_SERVICE_API_SPEC

## Purpose

This document defines the canonical internal service API architecture of the FUZE ecosystem. Its purpose is to establish how FUZE services communicate with one another inside the platform boundary, how internal write and coordination paths align with domain ownership, how service-to-service contracts differ from public APIs, and how internal interfaces support a multi-product, transparency-first platform with shared identity, billing, credits, AI orchestration, workflows, governance, treasury, and payout-sensitive systems.

This specification is foundational because FUZE is not a monolithic application. It is a multi-domain platform with shared services, product-specific services, async orchestration, policy-sensitive economic systems, and on-chain/off-chain coordination. In such an environment, internal APIs are not just implementation detail. They are one of the main ways entity ownership, write authority, side-effect handling, and operational safety become enforceable in practice.

---

## Scope

This specification covers:

- the canonical role of internal service APIs in FUZE
- the distinction between internal service APIs, public APIs, first-party application APIs, and event-driven interfaces
- how internal APIs align with canonical domain ownership and mutation authority
- synchronous service-to-service interaction patterns
- asynchronous coordination boundaries and when not to use direct internal APIs
- internal API treatment for identity, workspace, wallet-aware, credits, billing, AI, workflow, governance, treasury, and payout-sensitive domains
- internal authentication, authorization, traceability, error handling, and retry expectations
- versioning, compatibility, and evolution rules for internal service contracts
- security, observability, auditability, and failure-handling principles for internal API operations

This specification does not define every route, event schema, or deployment topology detail. Those are refined in:

- `API_ARCHITECTURE_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `IDEMPOTENCY_AND_VERSIONING_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `JOB_QUEUE_AND_WORKER_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`

---

## Design Goals

The design goals of the FUZE internal service API architecture are:

1. to align service-to-service writes with canonical ownership boundaries
2. to prevent hidden cross-domain mutation and private database coupling
3. to support explicit synchronous coordination where it is appropriate
4. to avoid misusing internal APIs for work better handled through async jobs or events
5. to provide consistent internal request, response, and error semantics across services
6. to support strong auditability and traceability for internal side effects
7. to preserve tighter security and control discipline for governance-, treasury-, and payout-sensitive flows
8. to make internal service contracts stable enough for platform scale without freezing internal evolution unnecessarily

---

## Non-Goals

This specification is not intended to:

- force every cross-domain interaction to use synchronous APIs
- make internal APIs identical to public APIs
- collapse event-driven coordination into request/response convenience
- allow one service to mutate another domain’s truth through undocumented private paths
- expose governance-, treasury-, or payout-sensitive internal controls to broad internal callers without domain-specific restriction
- require every internal read to go through an expensive cross-service call when a derived read model is more appropriate
- define every service mesh or network control implementation detail in this document

---

## Canonical Internal API Principle

The primary principle of FUZE internal service APIs is:

> internal service APIs must allow domains to collaborate without weakening canonical ownership, so that one service may request or observe another domain’s behavior through explicit contracts, but may not silently take over that domain’s truth or control responsibilities.

This means:

- internal APIs are ownership-respecting collaboration boundaries
- the owning domain defines the mutation contract for its entities
- service-to-service coordination should be explicit, typed, and traceable
- cross-domain convenience must not become hidden write authority
- trust-sensitive actions require narrower internal contracts and stronger controls than routine operational reads

This principle is one of the main ways the FUZE architecture prevents platform sprawl from turning into hidden coupling.

---

## Why FUZE Needs a Distinct Internal Service API Layer

FUZE needs a distinct internal service API layer because the platform contains many collaborating domains that still must remain separately owned.

Examples include:

- identity and auth
- workspace and membership management
- wallet-aware participation logic
- payment verification
- Platform Credits issuance and mutation
- subscriptions and entitlements
- AI orchestration
- workflow orchestration and job execution
- product-domain services
- governance, treasury, and vault logic
- payout-cycle preparation and publication
- transparency and reporting services

If these domains communicate through ad hoc direct data access, several risks emerge:

- ownership becomes ambiguous
- correction and audit lineage weakens
- cross-service changes become harder to reason about
- product teams begin to depend on undocumented side effects
- support and reconciliation become harder because no explicit contract explains what happened
- governance- or economic-sensitive domains may be mutated from too many places

A distinct internal service API layer solves this by ensuring that cross-domain behavior happens through defined contracts. These contracts make it clearer:

- what one service may ask another service to do
- what one service may read directly
- what data is canonical versus derived
- what failures are retryable
- what side effects should be emitted as events instead of chained synchronous writes
- what controls apply to sensitive domains

This is especially important in FUZE because the platform grows by adding products and shared capabilities. Strong internal APIs are part of what makes that growth sustainable.

---

## Internal Service APIs vs Public APIs

FUZE should maintain a strong distinction between internal service APIs and public APIs.

### Internal service APIs
These are contracts between trusted FUZE services or control-plane components inside the platform boundary.

They may:
- expose richer domain semantics
- assume stronger caller identity
- operate within stricter service authorization models
- include internal-only control or correction paths
- support ownership-aligned writes not appropriate for public exposure

### Public APIs
These are externally supported contracts exposed to outside consumers, users, or partners under stricter compatibility and visibility rules.

### Why the distinction matters

If internal APIs are treated as public:
- internal platform evolution slows unnecessarily
- sensitive control surfaces risk accidental overexposure
- product implementation details may harden into external contracts too early

If public APIs are treated as internal:
- external integrators may not get the stability, documentation, or safety guarantees they need

### Principle

Internal APIs are for controlled platform collaboration.  
Public APIs are curated external contracts.  
The platform should never confuse the two.

---

## Internal Service APIs vs Event-Driven Coordination

Internal request/response APIs are not the only internal coordination mechanism in FUZE.

FUZE should distinguish clearly between:

### Internal synchronous APIs
Used when one service needs immediate answer or immediate domain-owned action result.

### Event-driven coordination
Used when cross-domain side effects, background execution, or durable multi-step behavior are better handled asynchronously.

### Why this matters

If synchronous internal APIs are overused:
- latency chains grow
- dependency coupling increases
- retry behavior becomes fragile
- cross-domain failures become harder to contain
- async-capable domains get forced into artificial request/response behavior

If events are overused indiscriminately:
- immediate control paths may become too vague
- simple reads may become harder than necessary

### Principle

Internal APIs should be used for explicit, bounded synchronous collaboration.  
Events and jobs should be used for durable side effects, workflows, and async coordination.

This balance is especially important in credits, billing, workflow, and payout domains.

---

## Internal API Surface Families

FUZE should recognize different families of internal service APIs.

### 1. Canonical Domain Mutation APIs
These are owned by the domain that owns the truth and are the only approved internal write surfaces for canonical entity mutation.

Examples:
- create or update workspace membership
- issue credits after verified payment
- mutate subscription state
- publish payout-cycle business record
- create governance action record

### 2. Domain Query APIs
These expose canonical or tightly owned domain reads to other services.

Examples:
- resolve account state
- fetch workspace membership permissions
- retrieve current entitlement state
- retrieve credits owner balance projection
- retrieve payout-cycle current status

### 3. Control and Policy APIs
These expose internal actions with stronger restrictions around trust-sensitive domains.

Examples:
- treasury action proposal submission
- governance approval recording
- vault action validation
- payout-cycle publication gating
- registry publication controls

### 4. Orchestration Support APIs
These help workflow engines, schedulers, or coordinators interact with owning domains.

Examples:
- start payout preparation
- request AI task routing
- mark workflow step outcome
- finalize report artifact publication
- trigger entitlement refresh

### 5. Internal Derived Read APIs
These expose composed views for internal operational use when a dedicated read model exists.

Examples:
- finance reconciliation summary
- operator billing dashboard summary
- payout review overview
- governance queue overview

### Principle

Different internal API families should have different control expectations. A canonical mutation API is not the same thing as an internal dashboard query.

---

## Domain-Aligned Internal API Ownership

Internal APIs in FUZE must align with domain ownership.

### Identity domain
Owns internal mutation APIs for:
- account lifecycle state
- linked auth relationships
- security posture changes
- account restriction or restoration actions

Owns internal query APIs for:
- account existence and status
- auth linkage state
- security state

### Workspace domain
Owns internal mutation APIs for:
- workspace creation
- membership changes
- workspace owner or billing-owner transitions
- workspace-scoped access changes

### Wallet-aware domain
Owns internal mutation APIs for:
- wallet link
- wallet unlink
- verification status changes
- rank recalculation triggers where owned by this domain

### Payment domain
Owns internal mutation APIs for:
- normalized payment record creation
- verified payment outcome state
- provider status reconciliation inputs

### Credits domain
Owns internal mutation APIs for:
- issue
- reserve
- spend
- release
- reverse
- adjust
- correct ledger-linked balance state through owned pathways

### Subscription and entitlement domain
Owns internal mutation APIs for:
- plan lifecycle transitions
- entitlement creation and update
- billing-state transitions
- renewal state changes

### Product domains
Each product owns internal mutation APIs for its domain objects and outputs.

### Governance / treasury / payout domains
These own their trust-sensitive internal APIs and must not be bypassed by other services.

### Principle

An internal service may request change from the owning domain, but it may not take write ownership because service-to-service access happens to exist.

---

## Canonical Mutation APIs

Canonical mutation APIs are the most important internal service API class because they protect ownership boundaries.

A canonical mutation API should have the following properties:

- it belongs to the domain that owns the entity or state transition
- it is the only approved internal write path for that mutation class
- it performs domain-level validation and policy enforcement
- it emits or records audit lineage
- it returns explicit success, conflict, accepted, or failure semantics
- it does not expose raw persistence internals to callers

### Examples

#### Payment verified -> credits issued
The payment domain should not directly update credits tables. It should call the credits domain’s issuance API or emit the event path that leads to that API being invoked by the credits domain.

#### Product usage -> credits spend
A product may request a credits spend through the credits domain, but it must not decrement balance in product-owned data.

#### Governance approval -> payout-cycle release
A governance domain or orchestrator may call the payout domain’s publication/funding-preparation API, but it should not write payout-cycle truth directly.

### Principle

Canonical mutation APIs are one of the strongest implementation protections against ownership erosion in FUZE.

---

## Internal Query APIs and Read Strategy

FUZE should treat internal reads differently from internal writes.

### Internal query APIs are appropriate when:
- another service needs current canonical state owned elsewhere
- derived data is insufficient or unsafe
- the read is low enough latency and stable enough for service-to-service use
- the caller should not own a local copy of the data as truth

### Derived read models are preferable when:
- the view is heavily aggregated
- the data is needed at high volume
- dashboard or operator views need many domains at once
- the query load would create excessive synchronous dependency
- eventual consistency is acceptable

### Examples

Appropriate internal queries:
- fetch current workspace role for an account
- fetch current subscription entitlement state
- fetch current payout cycle publication status
- resolve wallet-link verification state

Prefer derived reads:
- operator platform overview dashboard
- finance reconciliation summary
- cross-domain holder summary dashboard
- multi-product commercial analytics views

### Principle

Internal reads should balance correctness and coupling. FUZE should not force every internal reader into either direct dependency or unsafe local truth duplication.

---

## Synchronous Coordination Patterns

Synchronous internal APIs are appropriate when immediate answer or state transition is required.

### Good synchronous use cases
- permission and role check before performing a product action
- fetch current credits balance before presenting a UX decision
- create canonical product request record
- verify current entitlement before serving a premium path
- create support-approved adjustment request in the owning domain

### Poor synchronous use cases
- multi-step long-running AI jobs
- payout-cycle preparation pipelines
- bulk reporting generation
- large workflow replay or backfill
- chained financial reconciliations across many systems

### Pattern principle

Use synchronous APIs for bounded domain actions and immediate answers.  
Use async jobs or events where the work is durable, multi-step, retry-heavy, or latency-insensitive.

This distinction keeps internal service APIs usable without turning them into a fragile orchestration web.

---

## Async-Aware Internal API Design

Even when an internal API is request/response, it may still represent the start of async work.

FUZE should support internal APIs that return accepted-state rather than final-state when the operation is naturally asynchronous.

### Typical pattern
1. service submits domain-owned request
2. owning domain validates and records request
3. domain returns accepted + operation/job reference
4. workflow or job system processes downstream work
5. status is checked through domain or orchestration APIs
6. final result becomes available through explicit read APIs or events

### Internal async examples
- start HerHelp generation project
- start Botmad scan run
- start payout entitlement preparation
- request large export generation
- request transparency report build/publish workflow

### Principle

Internal APIs should not force pretending that async work is synchronous. Accepted-state semantics are a first-class part of FUZE service architecture.

---

## Internal Authentication and Service Identity

Internal service APIs should use explicit service identity rather than implicit network trust alone.

At minimum, internal callers should be distinguishable as:

- product service
- platform core service
- workflow engine
- scheduler/worker
- support/admin control-plane service
- reporting/publication service
- governance or treasury control service

### Why this matters

Different services should not all look identical from an authorization perspective. A workflow engine should not automatically have the same powers as the credits service. A reporting service should not have mutation authority over governance truth. Service identity is necessary to enforce least privilege inside the platform.

### Principle

Being inside the FUZE infrastructure boundary does not itself grant broad internal authority. Internal APIs should still authenticate and classify callers.

---

## Internal Authorization and Domain Scoping

Internal service APIs should apply authorization rules even for trusted internal callers.

Authorization should consider:

- calling service identity
- domain relationship
- operation type
- scope reference
- environment and control-plane posture
- policy restriction for trust-sensitive actions

### Examples

#### Product service calling credits service
Allowed to request spend under approved business action flow, but not allowed to issue arbitrary credits.

#### Reporting service calling payout domain
Allowed to read published cycle state, but not allowed to mutate entitlement or funding state.

#### Workflow engine calling governance domain
May be allowed to advance workflow state around approval processing, but not to invent approvals or bypass role checks.

### Principle

Internal authorization should be narrow, operation-specific, and domain-aware. This is one of the most important protections in a platform with many services and sensitive economic flows.

---

## Internal APIs for Credits, Billing, and Economic Domains

Economic domains need especially strong internal API discipline.

### Payment domain internal APIs
Should own normalized payment record creation, provider status reconciliation, and verified outcome publication.

### Credits domain internal APIs
Should own:
- issue
- reserve
- spend
- release
- reverse
- adjust
- current balance queries
- mutation history queries for controlled internal use

### Subscription domain internal APIs
Should own:
- subscription create/renew/cancel paths
- entitlement refresh
- plan transitions
- billing-state mutation

### Important rule

No product or reporting service should directly mutate credits or billing truth through private database access or “temporary” internal shortcuts.

### Principle

Economic domains are central to platform coherence. Internal APIs here must be explicit, narrow, and strongly audited.

---

## Internal APIs for AI, Workflow, and Product Orchestration

FUZE products rely on shared AI and workflow systems, so internal APIs must support orchestration without collapsing boundaries.

### AI orchestration internal APIs may own:
- AI task submission
- route determination
- result status
- output release or retrieval references
- usage metering hooks

### Workflow internal APIs may own:
- workflow creation
- step progression
- approval request submission
- retry scheduling
- cancellation
- execution status reads

### Product orchestration principle

Shared AI/workflow services should coordinate execution, but product domains should still own product meaning. An orchestration service may run work on behalf of a product, but it should not silently redefine the product’s canonical domain objects.

This distinction is essential in QTB, AIMM, HerHelp, and Botmad flows.

---

## Internal APIs for Governance, Treasury, and Payout Domains

Governance-, treasury-, and payout-sensitive internal APIs must be narrower and more explicit than ordinary service contracts.

### Governance internal APIs should support:
- action proposal creation
- approval state recording
- policy validation references
- execution state linkage
- audit and reporting references

### Treasury / vault internal APIs should support:
- category-aware action proposal
- validation against policy class
- destination classification
- execution-path preparation
- status and review reads

### Payout internal APIs should support:
- payout-cycle business record creation
- snapshot/eligibility reference attachment
- publication state transitions
- funding linkage recording
- payout-ledger publication inputs
- status and correction flows

### Principle

Sensitive internal APIs should use explicit domain language, narrow operation sets, and stronger authorization. Generic mutation endpoints are especially dangerous in these domains.

---

## Internal Error Architecture

Internal service APIs should use structured errors that are useful both to machines and to operators.

At minimum, internal errors should distinguish among:

- validation failure
- authorization denial
- state conflict
- dependency failure
- retryable transient failure
- non-retryable business rule failure
- accepted-for-async-processing
- policy denial
- internal invariant violation

Each internal error should preserve:
- machine-readable code
- short human-readable explanation
- domain context
- correlation ID
- retry guidance or retryability classification

### Why this matters

Internal callers frequently need to decide:
- whether to retry
- whether to escalate
- whether to record business failure
- whether to trigger compensation flow
- whether to open incident or support workflow

A vague internal error model causes cascading fragility across services.

---

## Idempotency and Safe Retries for Internal APIs

Many internal service operations in FUZE will be retried due to network failures, worker retries, or orchestration replay. Internal APIs must therefore support idempotency for appropriate mutation classes.

### Internal operations that should generally support idempotency
- credits issuance after verified payment
- credits reversal requests
- subscription renewal transitions
- payout-cycle publication steps
- registry publication actions
- support compensation issuance through owned domain APIs
- governance action creation where duplicate submission is possible

### Principle

If an operation can plausibly be retried by infrastructure, it should have a clear strategy for preventing duplicate business mutation.

This is especially important in economic and payout-sensitive domains.

---

## Auditability and Traceability of Internal API Operations

Internal service API operations should be traceable end to end.

At minimum, important internal API calls should support:

- request or operation ID
- correlation ID
- calling service identity
- actor reference where human-initiated
- workspace/account scope where relevant
- business reason or action type
- resulting domain object reference
- async job reference where applicable
- audit event linkage
- governance or payout-cycle reference where applicable

### Principle

Internal APIs are part of platform truth production. FUZE should be able to reconstruct which service requested which mutation, why it happened, and what outcome occurred.

This is particularly important in support, billing, treasury, and governance investigations.

---

## Internal API Security Principles

FUZE internal service APIs should follow several security principles.

### Principle 1: Least privilege
Each service should have only the internal powers it requires.

### Principle 2: Explicit trust-sensitive segmentation
Governance, treasury, payout, and support-correction APIs should be more restricted than ordinary product read APIs.

### Principle 3: No implicit broad trust by network location
Being “inside the cluster” should not be enough to gain sensitive access.

### Principle 4: Sensitive mutation hardening
High-impact mutations should require stronger caller identity, stronger authorization checks, and stronger audit generation.

### Principle 5: Safe degradation
If an internal domain is degraded, dependent services should fail clearly or move to accepted/pending states rather than attempt unsafe local mutation.

These principles keep internal APIs from becoming hidden platform-wide root access.

---

## Compatibility and Evolution of Internal APIs

Internal APIs should evolve with discipline, but they do not need the exact same stability model as public APIs.

### Internal evolution principles
- preserve explicit ownership boundaries even as endpoint shapes evolve
- prefer additive change where practical
- deprecate intentionally rather than silently
- coordinate breaking changes across dependent services
- keep old and new contracts parallel where migration risk is high
- update audit and observability expectations when interface semantics change

### Important principle

Internal does not mean unstable by default. FUZE is a platform, so internal contracts should still be treated seriously, especially when many products or control-plane services depend on them.

---

## Failure Handling and Degraded Modes

Internal service APIs should support clear degraded-mode behavior.

### Examples
- identity reads fail closed for sensitive auth decisions
- credits domain temporarily unavailable causes pending/failed spend decision rather than local product-side deduction
- payout publication service degraded does not redefine payout truth; it delays publication flow
- reporting service degraded does not block canonical economic mutation flows
- AI orchestration degraded may leave product request accepted but not yet completed

### Principle

Internal degraded modes should preserve canonical ownership and truth boundaries. A dependent service should not take over another domain’s truth because the owning domain is temporarily unhealthy.

---

## Minimum Architectural Entities

At minimum, the FUZE internal service API architecture should recognize the following conceptual entities:

### Internal Surface Entities
- `internal_api_surface_id`
- `surface_family`
- `owning_domain`
- `service_identity_scope`
- `version_reference`

### Request/Response Entities
- `request_id`
- `correlation_id`
- `caller_service_reference`
- `actor_reference` where applicable
- `scope_reference`
- `operation_type`
- `response_status`
- `error_code` where applicable

### Async Internal Operation Entities
- `job_id`
- `operation_status`
- `accepted_at`
- `started_at`
- `completed_at`
- `failed_at`
- `result_reference`

### Integrity and Audit Entities
- `idempotency_key` where applicable
- `audit_lineage_reference`
- `workflow_reference`
- `governance_action_reference` where applicable
- `payout_cycle_reference` where applicable
- `policy_reference` where applicable

These are minimum conceptual entities. Detailed schemas and route patterns are refined downstream.

---

## Open Items

The following areas are intentionally refined in downstream implementation and domain-specific API design:

- exact service topology and ownership map by deployed service
- exact internal auth credential model
- exact request/response envelope schema for internal APIs
- exact retry and timeout policies by domain
- exact split between synchronous internal APIs and event-driven coordination by workflow family
- exact internal control-plane route families for governance and treasury
- exact service discovery and network-layer policy model

These do not weaken the canonical internal service API architecture established here.

---

## Closing Summary

The FUZE internal service API architecture is the service-to-service collaboration layer of a multi-product, ownership-driven platform. It exists so that domains can coordinate through explicit contracts without weakening canonical ownership, auditability, or trust-sensitive control boundaries. By distinguishing internal APIs from public APIs, balancing synchronous coordination with async orchestration, narrowing sensitive domain contracts, and enforcing service identity and least privilege, FUZE creates an internal interface model that supports platform scale without sacrificing architectural discipline.
