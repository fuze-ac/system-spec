# FUZE Public API Specification

## Document Metadata
- **Document Name:** `PUBLIC_API_SPEC.md`
- **Document Type:** Canonical refined system specification
- **Status:** Active refined system spec
- **Version:** 2.0.0
- **Effective Date:** 2026-04-22
- **Last Updated:** 2026-04-22
- **Reviewed On:** 2026-04-22
- **Document Owner:** FUZE Platform Public API Governance Domain (canonical owner for shared public and external API posture); named individual owner is not explicitly specified in the retrieved governing materials
- **Approval Authority:** Not explicitly specified in the retrieved governing materials; constitutional approval authority remains governed by `REFINED_SYSTEM_SPEC_INDEX.md` and the active FUZE approval workflow
- **Review Cadence:** Not explicitly specified in the retrieved governing materials; SHOULD be reviewed whenever external contract posture, public trust surfaces, authenticated public product access, partner integration posture, rollout and admission posture, compatibility policy, abuse posture, or publication semantics materially change
- **Governing Layer:** Shared platform external interface governance / public contract layer
- **Parent Registry:** `REFINED_SYSTEM_SPEC_INDEX.md`
- **Primary Audience:** Platform architecture, backend engineering, product engineering, API and contract authors, security, abuse-prevention, audit, operations, public registry and transparency authors, partner-integration authors, SDK/OpenAPI derivation authors
- **Primary Purpose:** Define the canonical FUZE public API layer as a curated external contract surface that exposes bounded business actions, public-safe reads, authenticated caller-scoped reads, and approved partner/public-trust interfaces without collapsing public APIs into internal APIs, admin/control APIs, events, reporting owners, or raw mutation primitives
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
  - `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
  - `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md`
  - `API_ARCHITECTURE_SPEC.md`
  - `INTERNAL_SERVICE_API_SPEC.md`
  - `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
  - `IDEMPOTENCY_AND_VERSIONING_SPEC.md`
  - `MIGRATION_AND_BACKWARD_COMPATIBILITY_SPEC.md`
  - `FEATURE_FLAG_AND_ROLLOUT_CONTROL_SPEC.md`
  - `WORKFLOW_AND_AUTOMATION_SPEC.md`
  - `JOB_QUEUE_AND_WORKER_SPEC.md`
  - `AI_ORCHESTRATION_SPEC.md`
  - `MODEL_ROUTING_AND_CONTEXT_SPEC.md`
  - `AI_USAGE_METERING_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - `AUDIT_LOG_AND_ACTIVITY_SPEC.md`
  - `MONITORING_ALERTING_AND_INCIDENT_RESPONSE_SPEC.md`
- **Primary Downstream Dependents:**
  - `INTERNAL_SERVICE_API_SPEC.md`
  - `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
  - `IDEMPOTENCY_AND_VERSIONING_SPEC.md`
  - `MIGRATION_AND_BACKWARD_COMPATIBILITY_SPEC.md`
  - public domain API specifications
  - product-specific external API specifications
  - OpenAPI / AsyncAPI public contract derivation
  - future `fuze-sdk` derivation layers
  - public registry, transparency, partner-integration, and authenticated product-access interface contracts
- **Supersedes:** Earlier or weaker interpretations that treat public APIs as mirrors of internal services, treat first-party frontend consumption as sufficient reason for permanent public exposure, expose raw internal mutation primitives publicly, allow public reads to become hidden source-of-truth layers, or blur public, internal, admin/control, event, and publication surfaces
- **Superseded By:** None currently defined
- **Related Decision Records:** Not explicitly linked in the retrieved governing materials
- **Canonical Status Note:** This document is the canonical governing system specification for FUZE public and external API posture. Downstream route inventories, OpenAPI/AsyncAPI contracts, SDKs, partner integrations, public-registry reads, transparency surfaces, and authenticated external product APIs MUST preserve the ownership, truth-separation, compatibility, abuse, and publication rules defined here.
- **Implementation Status:** Normative source; downstream routes, schemas, gateways, auth/scope logic, idempotency handling, partner callbacks, public artifact retrieval, and external SDK derivation MUST align
- **Approval Status:** Draft refined canonical specification pending explicit approval workflow
- **Change Summary:** Refined the prior standalone public API draft into a production-grade external-contract specification aligned to the April 2026 refined architecture stack; tightened the distinction between curated external contracts and internal/admin surfaces; clarified authenticated external scope, artifact classification, public-write boundaries, async acceptance, partner integration posture, abuse controls, compatibility requirements, audit lineage, and publication/read-model discipline

## Title
FUZE Public API Specification

## Purpose
This specification defines the canonical public API layer of FUZE.

Its purpose is to make explicit:
- what qualifies as a FUZE public or external contract
- how public APIs differ from internal service APIs, admin/control-plane APIs, event contracts, webhook families, reporting/publication artifacts, and product-local interfaces
- how FUZE exposes public-safe reads, authenticated caller-scoped reads, partner-safe interfaces, public-trust artifacts, and bounded external business actions
- how public APIs preserve domain ownership, plane separation, security, privacy, supportability, compatibility, and public trust
- how public async interactions, partner callbacks, derived public read models, and canonical public artifacts must be represented
- what downstream public route families, schemas, SDKs, and machine-readable contracts MUST preserve

This specification is intentionally governing rather than descriptive. It does not merely enumerate routes. It defines the durable architecture posture for external contracts in the FUZE ecosystem.

## Scope
This specification governs:
- the shared public and external API layer across the FUZE platform and ecosystem
- public read surfaces, authenticated external reads, public product-access surfaces, partner integration APIs, and public webhook/callback-adjacent interface posture
- public-safe route-family classification and exposure rules
- bounded business-action write posture for public contracts
- public accepted-state and async interaction posture
- artifact classification rules for canonical public artifacts versus derived public views
- public authentication, scope-aware authorization, partner identity, and visibility posture
- rate-limit, abuse-resistance, replay-safety, and privacy constraints for externally exposed APIs
- public compatibility, versioning, deprecation, and migration posture
- public auditability, traceability, and operational observability requirements
- implementation-contract guardrails for downstream public OpenAPI / AsyncAPI / SDK derivation

## Out of Scope
This specification does not define:
- every final route path or every payload field
- internal service collaboration contracts
- admin or control-plane APIs
- exact credential formats, OAuth flows, or session token internals
- exact SDK package structures
- exact per-route commercial packaging or quota pricing
- exact webhook catalog or event names
- exact smart-contract ABI design
- exact product-by-product public route inventories
- exact field-level visibility matrices for every object type

Those concerns belong in narrower domain API specs, product-specific external contracts, machine-readable contract artifacts, and implementation documents, provided they remain consistent with this document.

## Design Goals
The design goals of the FUZE public API layer are to:
1. expose only intentionally public, supportable external contracts
2. preserve explicit separation among public, internal, admin/control, event, and publication surfaces
3. keep canonical mutations owner-domain aligned even when initiated by external callers
4. support authenticated user, partner, registry, transparency, and selected product-access needs without widening public mutation power unsafely
5. preserve strong compatibility and deprecation discipline for external consumers
6. make abuse resistance, privacy, traceability, and operational safety first-class public-contract concerns
7. support accepted-state async behavior honestly rather than pretending deferred work is complete
8. enable future OpenAPI / AsyncAPI / SDK derivation without weakening architecture semantics

## Non-Goals
This specification is not intended to:
- mirror all internal service routes publicly
- make first-party frontend needs the default justification for permanent external contracts
- expose raw internal mutation primitives such as arbitrary credits issuance, raw treasury actions, workflow replay, or operator override controls
- let public transparency, registry, or reporting views become hidden owners of source truth
- collapse authenticated external access into internal service collaboration
- collapse partner callback handling into arbitrary provider-input mutation authority
- treat public API gateways as owners of business meaning
- allow public contract evolution to follow internal implementation convenience only

If there is tension between convenience and public-contract safety, the public-contract-safe interpretation wins.

## Core Principles
### 1. Curated-Not-Mirrored Principle
Public APIs are intentionally curated external contracts, not serialized exports of internal services.

### 2. Public-Is-A-Promise Principle
Once FUZE publishes an external contract, compatibility, supportability, abuse posture, and public trust obligations increase materially.

### 3. Owner-Domain Mutation Principle
Public write actions MUST terminate in owner-domain logic and MUST express bounded business actions rather than raw internal mutation primitives.

### 4. Authenticated-External-Is-Still-Public Principle
Authenticated product-facing and self-scope routes remain part of the public/external contract layer; they do not become internal APIs merely because the caller is authenticated.

### 5. Public-Artifact Classification Principle
Public APIs MUST distinguish canonical public artifacts from derived public summaries and caller-scoped canonical state from caller-scoped derived views.

### 6. Accepted-State Honesty Principle
Long-running or deferred public actions MUST return explicit accepted-state posture rather than implying final completion.

### 7. Narrower-Than-Internal Principle
Public APIs MUST remain narrower, more stable, and more constrained than internal service or admin/control surfaces.

### 8. Abuse-Resistance Principle
Rate limits, replay safety, signature verification, and anti-abuse posture are integral architectural constraints of public APIs, not optional gateway polish.

### 9. No Hidden Public Ownership Principle
Public-facing registries, transparency views, dashboards, and frontend clients MAY publish and consume but MUST NOT redefine owner-domain truth.

### 10. Public-Trust Publication Principle
Public trust surfaces must derive from governed publication truth and canonical owner lineage, not from leaked internal state.

## Canonical Definitions
### Public API
An intentionally exposed external contract surface for public clients, authenticated end users, partner systems, or other approved external consumers.

### Authenticated Public API
A public/external contract that requires user or client authentication and scope-aware authorization, but remains narrower than internal collaboration contracts.

### Partner Integration API
A public/external contract intentionally exposed to approved partner or ecosystem systems under stronger identity, scope, replay, and support rules.

### Public Business Action
A bounded business-intent write exposed externally, such as workspace creation, wallet-link initiation, checkout initiation, or product request submission.

### Canonical Public Artifact
A publication-oriented artifact whose public representation is itself governed and canonical at the publication layer, while still remaining downstream to underlying source-domain truth where applicable.

### Derived Public View
A public summary, dashboard, transparency aggregation, or simplified read model derived from one or more canonical sources.

### Public Request Lineage
The durable trace of an externally facing request, including caller posture, scope, contract family, correlation context, and outcome classification.

### Public Visibility Class
The architectural classification indicating whether a contract is openly public, authenticated, partner-only, limited-public, or otherwise explicitly constrained.

## Truth Class Taxonomy
This specification MUST preserve the following truth classes:
1. **Semantic truth** — what a public surface means and which domain governs that meaning
2. **Policy truth** — rules governing external exposure, visibility class, public write eligibility, compatibility posture, and deprecation
3. **Runtime truth** — current request processing, auth/scope resolution, queue status, retry state, or accepted async progression
4. **Ledger / storage truth** — durable owner-domain records, publication records, request lineage, idempotency lineage, and contract version lineage
5. **Provider-input truth** — inbound external callback or partner signal prior to owner-domain normalization
6. **Implementation-adapter truth** — gateway validation, signature checks, rate-limit decisions, and transport adaptation state
7. **Execution truth** — workflow, queue, worker, and async operation state for accepted public actions
8. **Projection / reporting truth** — public transparency summaries, registry projections, payout summaries, and partner-safe aggregated outputs
9. **Presentation truth** — user-visible wording, frontend rendering, SDK ergonomics, and consumer-facing explanations

These truth classes MUST remain distinct. Public APIs do not absorb business-domain ownership, internal workflow meaning, queue mechanics, or reporting ownership.

## Architectural Position in the Spec Hierarchy
This document sits below:
- `REFINED_SYSTEM_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `API_ARCHITECTURE_SPEC.md`

and above or alongside:
- `INTERNAL_SERVICE_API_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `IDEMPOTENCY_AND_VERSIONING_SPEC.md`
- `MIGRATION_AND_BACKWARD_COMPATIBILITY_SPEC.md`
- product-specific external API specifications
- public registry, transparency, and partner integration specifications
- OpenAPI / AsyncAPI / SDK derivation artifacts

This document governs external/public contract posture. It does not replace adjacent interface-family or domain-owner specifications.

## System Boundaries
Public APIs span the experience/edge layer, application plane, selected publication outputs, and bounded integration-plane interaction, but they do not own business truth.

They MUST be interpreted as follows:
- the **experience / edge layer** may consume public APIs but does not decide what becomes public
- the **application plane** hosts most owner-domain public mutations and caller-scoped reads
- the **execution plane** may continue accepted public async work, but does not become the owner of external contract meaning
- the **integration plane** handles partner credentials, callback intake, and normalized provider/partner interactions; raw external input remains non-canonical until owner-domain acceptance
- the **reporting plane** may publish canonical public artifacts or derived public views, but public publication does not rewrite source owner truth
- the **control plane** may restrict, roll out, or suspend public surfaces without becoming the owner of the underlying business domain
- the **on-chain contract layer** remains separately bounded; public APIs may expose contract addresses, registry views, or chain-adjacent summaries, but must not collapse on-chain truth into off-chain policy meaning

## Adjacent Boundaries
This specification interacts with adjacent domains as follows:
- **API Architecture** defines shared interface-family rules; this specification narrows those rules for the public/external family
- **Internal Service API** owns service-to-service collaboration posture and MUST remain distinct from public contracts
- **Event Model and Webhook** governs event and webhook semantics; public webhook exposure is a bounded external-safe projection of approved outcomes
- **Idempotency and Versioning** governs replay safety, contract evolution, and deprecation rules that public APIs must apply most strictly
- **Migration and Backward Compatibility** governs coexistence, cutover, and supersession of public contracts
- **Feature Flag and Rollout Control** may constrain whether a public surface is exposed, but does not redefine public contract meaning
- **Workflow and Automation** owns workflow-state meaning; public APIs may initiate or inspect workflow-related status without owning workflow truth
- **Job Queue and Worker** owns execution substrate semantics; public accepted-state contracts may point to async progress without collapsing into queue truth
- **AI Orchestration**, **Model Routing and Context**, and **AI Usage Metering** own AI execution, routing/context, and metering meaning; public AI-related contracts consume those domains through bounded external actions and caller-safe reads
- **Public registry, transparency, payout, credits, billing, entitlement, and identity domains** own their respective truths even when exposed via public APIs

## Conflict Resolution Rules
When materials, implementations, or interpretations conflict, the following rules apply:
1. the active refined registry and higher constitutional materials win over narrower documents
2. `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`, `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`, and `PLATFORM_ARCHITECTURE_SPEC.md` win on ownership, plane separation, and top-level boundary interpretation
3. `API_ARCHITECTURE_SPEC.md` wins on shared interface-family structure and accepted-state semantics
4. this document wins on what may be exposed publicly, how public surfaces are classified, and what public mutation posture is allowed
5. owner-domain specifications win on business meaning, canonical reads, and mutation semantics of their domains
6. internal, admin/control, reporting, frontend, SDK, or gateway convenience layers never win over canonical public-contract rules
7. publication artifacts do not win over source owner truth merely because they are public
8. when ambiguity remains, FUZE MUST choose the more conservative architecture-consistent external exposure posture and escalate the ambiguity

## Default Decision Rules
When ambiguity exists and no narrower approved exception is available, FUZE MUST default to the following:
1. external exposure defaults to non-public unless explicitly approved
2. public contracts default to narrower and more stable subsets of internal capability
3. authenticated end-user and partner access default to public/external posture, not internal-service posture
4. public writes default to bounded business actions rather than generic mutation primitives
5. public trust surfaces default to read-only posture unless a narrower specification explicitly approves a write path
6. public async actions default to accepted-state responses rather than immediate success claims
7. public transparency and registry views default to canonical-public-artifact or derived-public-view classification, never ambiguous state
8. partner callbacks default to normalized-input posture until owner-domain validation succeeds
9. if a proposed route cannot name its owner domain, visibility class, supportability posture, and compatibility expectations, it is incomplete and MUST NOT be treated as production-grade

## Roles / Actors / Entities
### Human Actors
- unauthenticated public readers
- authenticated end users
- workspace-scoped actors
- external developers
- partner operators
- product operators
- support and trust-safety operators
- public/community/investor readers of approved trust surfaces

### System Actors
- public clients
- first-party frontend consumers
- partner integration systems
- public gateways and auth/scope layers
- platform and product domain services
- registry and transparency publication services
- async execution systems
- abuse-detection and rate-limit systems
- outbound webhook delivery systems

### Core Entity Families
- public API surfaces
- public API operations
- public visibility classes
- public contract versions
- public request lineage
- public clients and credentials
- rate-limit and abuse policies
- public idempotency records
- public async operation references
- public deprecation notices
- public registry entries
- public transparency artifacts
- public payout/publication views
- public product metadata

## Ownership Model
### The Public API Governance Domain Owns
- shared public/external surface taxonomy
- public visibility-class semantics
- route-family posture for public, authenticated user, partner, public product, and trust-surface exposure
- external contract compatibility and deprecation posture at architecture level
- public artifact classification rules
- public request-lineage expectations
- public idempotency posture at architecture level
- the prohibition on exposing raw internal mutation primitives publicly

### The Public API Governance Domain Does Not Own
- business truth of credits, billing, identity, entitlement, payouts, workflow, AI, registry publication content, or product-local domain objects
- internal service collaboration semantics
- admin/control-plane mutation semantics
- event or webhook business meaning
- queue, worker, or workflow mechanics
- commercial packaging, price, or entitlement meaning

### Owner Domains MUST
- expose only approved public-safe contracts
- preserve domain meaning when surfaced through public APIs
- classify their public outputs accurately as canonical public artifacts, caller-scoped canonical state, or derived views
- terminate public write effects in owner-domain logic
- preserve audit, idempotency, and compatibility obligations appropriate to public exposure

### Non-Owners MUST NOT
- publish raw internal mutation primitives as public routes
- treat public transparency, registry, or SDK outputs as permission to redefine source truth
- let frontend or gateway convenience become the reason a contract is public
- convert public read models into shadow write owners

## Authority / Decision Model
### Platform Public API Governance Authority
Has final authority over public surface classification, exposure posture, compatibility discipline, and the rules governing public business-action shapes.

### Domain Authority
Each owner domain retains final authority over the meaning, lifecycle, mutation rules, and canonical state of its own objects and business actions.

### Product Authority
Products may propose product-specific public contracts, but those contracts must remain bounded, supportable, and aligned to platform public API rules.

### Control / Governance Authority
Control-plane systems may restrict, narrow, suspend, or gate public surfaces under rollout, incident, or policy posture. They do not become owners of the underlying business truth.

### External Authority
Partners and public clients have authority only over their own requests and externally provided signals. Their inputs do not become canonical truth without owner-domain validation and acceptance.

## State Model
At architecture level, public APIs MUST recognize the following semantic state classes where relevant:
- `proposed`
- `approved`
- `published`
- `active`
- `deprecated`
- `sunset`
- `retired`

And for requests or operations:
- `received`
- `authenticated_or_public`
- `authorized_if_needed`
- `validated`
- `accepted`
- `processed`
- `completed`
- `failed`
- `cancelled`
- `conflicted`

### State Rules
- publication of a route changes compatibility obligations
- `accepted` MUST remain distinct from `completed`
- `deprecated` and `sunset` MUST remain explicit and externally understandable
- a public surface MUST NOT silently move from derived view to canonical public artifact without explicit change control
- caller-facing async status MUST preserve lineage and stable operation references

## Lifecycle / Workflow Model
### 1. Surface Proposal and Approval
A candidate public contract is proposed, reviewed for exposure appropriateness, approved, and published only when its owner domain, supportability, and compatibility posture are explicit.

### 2. Request Intake
An external request enters through a classified public surface. The system determines whether it is public-open, user-authenticated, or partner-authenticated.

### 3. Authentication and Scope Resolution
Where needed, authentication, scope resolution, visibility rules, entitlement or access preconditions, and abuse controls are evaluated before side effects occur.

### 4. Owner-Domain Execution
The owner domain either:
- returns a public-safe read,
- applies a bounded business action synchronously,
- or records accepted async intent for later execution.

### 5. Async Continuation
Accepted public work may continue through workflow, queue, worker, or partner/integration pathways, but those systems do not own the public contract meaning.

### 6. Artifact Publication or Result Retrieval
The system returns or later exposes:
- canonical public artifacts,
- caller-scoped canonical objects,
- or derived public views,
with accurate classification.

### 7. Deprecation, Migration, and Retirement
Public contract changes preserve explicit lineage, coexistence windows where required, and external migration guidance.

## Invariants
1. Public APIs are curated external contracts, not mirrored internal services.
2. Public writes express bounded business actions, not raw mutation primitives.
3. Authenticated external APIs are still public/external contracts, not internal APIs.
4. Public contract publication increases compatibility obligations.
5. Public accepted-state is not final completion.
6. Registry, transparency, and payout views do not become hidden owners of all related truth.
7. Partner or provider callbacks do not become canonical business truth without owner validation.
8. Gateway, frontend, or SDK ergonomics do not redefine contract meaning.
9. Sensitive public actions require stronger abuse, traceability, and safety controls.
10. Public surface classification must remain explicit.

## Functional Rules
### Rule 1: Public Surface Classification
Every significant external contract MUST declare a surface family and visibility class.

### Rule 2: Narrow Public Exposure
Public exposure MUST remain narrower than internal collaboration and admin/control power.

### Rule 3: Bounded Public Write Shape
Public write contracts MUST describe bounded business actions or accepted async requests, not generic internal commands.

### Rule 4: Public Artifact Classification
Public reads SHOULD distinguish among canonical public artifacts, caller-scoped canonical state, and derived public views where confusion is likely.

### Rule 5: Authenticated Scope Discipline
Authenticated public contracts MUST evaluate account, workspace, product, and partner scope explicitly where relevant.

### Rule 6: Accepted-State Honesty
Long-running public actions MUST return accepted-state references and MUST NOT imply completion until owner-domain finalization occurs.

### Rule 7: Public Trust-Surface Caution
Registry, transparency, payout-summary, and similar public-trust APIs MUST avoid collapsing publication truth into underlying ledger, billing, or chain truth.

### Rule 8: Public Partner Callback Safety
Inbound public or partner callbacks MUST use signature/credential verification, replay safety, and bounded owner-domain ingestion.

### Rule 9: Stronger Compatibility
Public routes MUST use explicit versioning and stronger deprecation discipline than ordinary internal surfaces.

### Rule 10: No Public Shortcut Rule
Public routes MUST NOT become shortcuts for internal service mutation, admin remediation, treasury action, governance action, or raw workflow replay.

## Permission / Access Considerations
- unauthenticated public reads are allowed only for intentionally public-safe artifacts
- authenticated public reads and writes require scope-aware authorization beyond identity alone
- workspace-scoped public access MUST evaluate membership, role, and applicable entitlement posture where relevant
- partner scopes MUST remain explicit and narrower than full internal capability
- public access denial MUST not leak unauthorized object details
- public route existence MUST NOT imply public mutability

## Entitlement Considerations
- public contracts may depend on entitlement or plan state, but they do not own entitlement meaning
- entitlement checks for public product access MUST remain distinct from route visibility, rollout, and permission
- public errors SHOULD distinguish entitlement or plan-state denial from generic permission denial where externally meaningful and safe
- public contracts MUST NOT encode commercial or entitlement truth solely through route naming or frontend behavior

## API / Contract Implications
Downstream public contracts MUST preserve at minimum:
- explicit public surface family and visibility class
- explicit owner domain
- bounded business-action naming for mutations
- accepted-state behavior for async actions
- artifact classification where needed
- structured public error classes
- explicit public versioning and deprecation posture
- idempotency support where replay risk exists
- correlation and request-lineage support
- partner callback safety controls
- no hidden public exposure of internal or admin mutation primitives

## Event / Async Implications
- public APIs may initiate actions whose downstream completion is expressed through status APIs, events, or webhooks
- events and webhooks communicate accepted or completed owner-domain outcomes outward; they do not become ownership substitutes
- public async operation references MUST remain stable across retries, polling, and result retrieval
- webhook families exposed to partners MUST be version-aware, retry-safe, and deduplicated
- execution systems MUST preserve the distinction between accepted request, execution progress, and final business completion

## Data Model / Storage Implications
The public API architecture requires durable support records for:
- public surfaces and operations
- visibility classes
- public request lineage
- public client registrations and credentials
- rate-limit policy linkage
- public idempotency records
- public async operation references
- public deprecation notices

Public artifact tables or views MAY exist for:
- registry entries
- transparency artifacts
- payout publication views
- product metadata

Rules:
- these public artifacts do not automatically own source business truth
- request-lineage and idempotency records are cross-cutting public-layer records, not owner-domain substitutes
- public tables MUST preserve linkage to owner-domain or publication-domain lineage
- storage convenience MUST NOT change domain ownership

## Read Model / Projection / Reporting Rules
- public transparency, registry, payout-status, and product-catalog endpoints MAY expose derived or publication-oriented read models
- derived public views MUST NOT be ambiguous about their classification when ambiguity could mislead consumers
- public summaries MUST NOT silently rewrite source owner meaning
- stale publication views MUST be treated as lag, not as proof that source truth changed
- public trust surfaces SHOULD preserve supersession or publication lineage where material

## Security / Risk / Abuse Controls
The public API layer MUST preserve:
- least privilege across unauthenticated, user-authenticated, and partner-authenticated surfaces
- route-family-specific rate limits
- abuse-protection and suspicious-usage controls
- object-level and function-level authorization for caller-scoped resources
- replay safety for mutation-capable routes and inbound callbacks
- safe public error design that avoids leaking internal topology or secrets
- stronger controls for credits-adjacent, billing-adjacent, payout-adjacent, wallet-link, partner-ingestion, and billable async routes
- explicit route inventory governance so unsupported public surfaces do not linger indefinitely
- no public exposure of raw governance-sensitive or treasury-sensitive actions

## Boundary Violation Detection / Non-Canonical Patterns
The following are non-canonical and forbidden unless a narrower approved exception explicitly allows them:
- exporting internal service routes directly as public routes
- exposing generic internal commands such as arbitrary credits adjustment or treasury actions publicly
- treating first-party frontend use as proof that a route should be permanently public
- treating public transparency dashboards as canonical owners of upstream truth
- letting SDK ergonomics define public architecture semantics
- accepting partner/provider callbacks as canonical business truth without normalization
- hiding admin or operator actions behind “special public” routes
- using public route naming to obscure internal mutation power
- silently widening public scope through rollout or gateway shortcuts

## Audit / Traceability Requirements
Meaningful public interactions MUST be reconstructible.

At minimum, significant public routes SHOULD preserve:
- request identity and correlation ID
- surface family and operation class
- caller posture (public, user, partner)
- caller reference or client reference where available
- scope reference where relevant
- owner-domain reference
- idempotency reference where applicable
- accepted async operation reference where applicable
- abuse/rate-limit outcomes where material
- linked workflow/job/event/audit references where relevant

Public request logs are not a substitute for full internal audit records, but public-facing trust-sensitive actions require stronger traceability than routine public metadata reads.

## Failure Handling / Edge Cases
### Unauthorized Scoped Access
If an authenticated caller requests a foreign account or workspace resource, the system MUST deny access without leaking foreign details.

### Rate-Limit or Abuse Block
If a public caller exceeds limits or triggers abuse controls, the request MUST be denied with explicit safe public error classes and no side effects.

### Duplicate Submission
If a public write is retried with the same business intent, idempotency MUST return a stable prior outcome or accepted reference rather than duplicate business meaning.

### Payload Mismatch on Same Idempotency Key
If a repeated key is used with materially different payload, the system MUST return conflict rather than silently reassigning meaning.

### Partner Callback Replay
Duplicate partner callbacks MUST NOT create duplicate business effects.

### Derived Public View Lag
If a public summary lags canonical source truth, the lag MUST remain a publication lag, not a source-truth rewrite.

### Public Surface Restriction
If rollout or incident posture restricts a public surface, the restriction MUST remain explicit and MUST NOT expose hidden internal alternatives as fallback.

## Operational Considerations
Operators MUST be able to:
- inventory active public surfaces and their visibility classes
- identify deprecated or sunset public contracts
- distinguish public, authenticated user, partner, and trust-surface traffic
- correlate public failures with owner-domain outcomes
- observe accepted async backlogs and result retrieval posture
- observe abuse, rate-limit, and replay-control outcomes
- quarantine or restrict unsafe public surfaces through approved control mechanisms
- monitor deprecation usage and migration adoption
- distinguish public publication lag from source-domain failure

## Migration / Compatibility / Supersession Considerations
- public APIs carry the strongest compatibility obligations in the interface stack
- additive evolution is preferred over breaking change
- breaking changes require explicit version transition or approved migration posture
- deprecation must be announced and time-bounded where material
- coexistence windows MAY be required for partner or ecosystem integrations
- public async references and status retrieval semantics MUST remain stable across deployments
- superseded public artifacts or contracts SHOULD preserve lineage and migration guidance
- public contract change MUST NOT bypass owner-domain boundaries merely because a gateway or SDK wants simpler ergonomics

## Implementation-Contract Guardrails
Downstream public implementations MUST preserve:
- curated-not-mirrored public exposure
- explicit public surface family and visibility class
- owner-domain mutation boundaries
- accepted-state honesty for async work
- artifact classification where confusion is possible
- explicit public versioning and deprecation posture
- idempotency and replay-safety where replay matters
- partner callback verification and deduplication
- strong separation from internal and admin/control surfaces
- no raw mutation primitive exposure for sensitive domains

Downstream implementations MUST NOT optimize away:
- accepted vs completed semantics
- classification of canonical public artifact vs derived public view where needed
- scope-aware authorization for authenticated public routes
- correlation and request lineage
- deprecation windows where breaking change risk exists
- stronger abuse posture for trust-sensitive public surfaces

## Downstream Execution Staging
This document should be consumed in the following order:
1. public route-family and visibility classification
2. owner-domain public contract definition
3. public auth/scope and abuse/idempotency policy integration
4. async accepted-state and status retrieval integration
5. publication/read-model and partner callback integration
6. machine-readable contract derivation and SDK generation
7. operational monitoring, deprecation, and control-plane restriction integration

## Required Downstream Specs / Contract Layers
This specification materially informs:
- `INTERNAL_SERVICE_API_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `IDEMPOTENCY_AND_VERSIONING_SPEC.md`
- `MIGRATION_AND_BACKWARD_COMPATIBILITY_SPEC.md`
- identity/account public APIs
- workspace/access public APIs
- entitlement, billing, credits, registry, transparency, payout, and product-specific external APIs
- OpenAPI / AsyncAPI public derivation
- future `fuze-sdk` derivation
- partner onboarding, callback, and public artifact publication specifications

## Canonical Examples / Anti-Examples
### Canonical Examples
- A public registry route returns an intentionally published registry artifact without exposing internal-only lineage.
- An authenticated user retrieves caller-scoped credits or invoice data through a public/external self-scope contract rather than an internal service route.
- A public product request submits a bounded business action and returns accepted-state with an operation reference.
- A partner callback is verified, deduplicated, and then normalized into an owner-domain consequence.

### Anti-Examples
- A public route exposes a generic internal credits adjustment primitive because the frontend is first-party.
- A public transparency summary is treated as the canonical owner of internal payout execution truth.
- A partner callback directly mutates billing or entitlement truth without owner-domain normalization.
- An SDK convenience method becomes the reason a raw internal workflow replay route is published externally.

## Dependencies / Cross-Spec Links
This document depends especially on:
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md` for platform/product/provider/publication/control separation
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md` and `PLATFORM_ARCHITECTURE_SPEC.md` for plane separation and public/publication/control placement
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md` for owner-domain interpretation
- `API_ARCHITECTURE_SPEC.md` for shared interface-family posture and accepted-state semantics
- `INTERNAL_SERVICE_API_SPEC.md` for non-public collaboration boundaries that must remain distinct
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md` for public webhook and outcome-communication posture
- `IDEMPOTENCY_AND_VERSIONING_SPEC.md` and `MIGRATION_AND_BACKWARD_COMPATIBILITY_SPEC.md` for external replay safety and evolution discipline
- `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md` and `FEATURE_FLAG_AND_ROLLOUT_CONTROL_SPEC.md` for admission and exposure constraints on public surfaces
- public registry, transparency, credits, billing, payout, AI, workflow, and product-domain specs for narrower public contract meaning

## Explicitly Deferred Items
The following are intentionally deferred to narrower documents:
- exact public route inventories by product and domain
- exact partner credential issuance workflow
- exact OAuth/session/token mechanisms
- exact rate-limit thresholds per consumer class
- exact webhook catalogs and event names
- exact field-level visibility policies per object type
- exact commercial packaging or quota pricing of public contracts
- exact OpenAPI tag structure and SDK packaging

## Final Normative Summary
FUZE MUST treat public APIs as a curated external contract surface.

Accordingly:
- public APIs MUST remain narrower than internal and admin/control surfaces
- public writes MUST express bounded business actions, not raw mutation primitives
- authenticated external routes remain part of the public/external family, not internal collaboration
- accepted async work MUST remain explicitly accepted rather than falsely completed
- public summaries, transparency views, and registry artifacts MUST preserve accurate artifact classification and MUST NOT silently replace source owner truth
- partner callbacks and external inputs MUST be normalized before owner-domain consequences are recorded
- public contracts MUST carry the strongest compatibility, abuse-resistance, and public-trust obligations in the interface stack

This document is the canonical governing source for FUZE public API posture. Downstream routes, contracts, SDKs, partner integrations, and publication surfaces MUST preserve these rules.

## Quality Gate Checklist
- [x] Canonical owner is explicit for public interface-governance truth families
- [x] Mutation boundaries are explicit
- [x] Adjacent boundaries are explicit
- [x] Truth classes are explicit
- [x] Conflict-resolution rules are explicit
- [x] Default decision rules are explicit
- [x] Non-canonical patterns are called out clearly
- [x] Public restrictions and control interactions are explicit
- [x] Read-model, publication, and artifact-classification rules are explicit
- [x] Failure and degraded-mode behavior are explicit
- [x] Downstream implementation guardrails are explicit
- [x] Dependencies and downstream impacts are explicit
- [x] Non-goals and deferred items are explicit
- [x] The document is strong enough to support downstream public route, auth/scope, async, partner, registry, transparency, and SDK implementation without inventing contradictory semantics
