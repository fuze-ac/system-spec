# FUZE Event Model and Webhook Specification

## Document Metadata
- **Document Name:** `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- **Document Type:** Canonical refined system specification
- **Status:** Active refined system spec
- **Version:** 2.0.0
- **Effective Date:** 2026-04-22
- **Last Updated:** 2026-04-22
- **Reviewed On:** 2026-04-22
- **Document Owner:** FUZE Platform Eventing and Webhook Governance Domain (canonical owner for shared internal event and external webhook contract posture); named individual owner is not explicitly specified in the retrieved governing materials
- **Approval Authority:** Not explicitly specified in the retrieved governing materials; constitutional approval authority remains governed by `REFINED_SYSTEM_SPEC_INDEX.md` and the active FUZE approval workflow
- **Review Cadence:** Not explicitly specified in the retrieved governing materials; SHOULD be reviewed whenever platform plane boundaries, internal event semantics, public exposure posture, async execution posture, retry/replay rules, security/risk controls, trust-publication posture, or compatibility rules materially change
- **Governing Layer:** Shared platform event and webhook contract governance / integration architecture
- **Parent Registry:** `REFINED_SYSTEM_SPEC_INDEX.md`
- **Primary Audience:** Platform architecture, backend engineering, product engineering, workflow/runtime engineering, AI engineering, integration engineering, API and contract authors, security, audit, operations, support/control-plane operators, implementation-contract authors
- **Primary Purpose:** Define the canonical FUZE event model and webhook architecture governing internal domain-event semantics, integration-event posture, operational event discipline, external webhook exposure, envelope structure, ownership boundaries, retry/replay behavior, audit lineage, and downstream implementation-contract guardrails without collapsing eventing into APIs, workflow truth, queue truth, audit truth, reporting truth, or public publication truth
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
  - `PUBLIC_API_SPEC.md`
  - `INTERNAL_SERVICE_API_SPEC.md`
  - `IDEMPOTENCY_AND_VERSIONING_SPEC.md`
  - `MIGRATION_AND_BACKWARD_COMPATIBILITY_SPEC.md`
  - `WORKFLOW_AND_AUTOMATION_SPEC.md`
  - `JOB_QUEUE_AND_WORKER_SPEC.md`
  - `AI_ORCHESTRATION_SPEC.md`
  - `MODEL_ROUTING_AND_CONTEXT_SPEC.md`
  - `AI_USAGE_METERING_SPEC.md`
  - `AUDIT_LOG_AND_ACTIVITY_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - `MONITORING_ALERTING_AND_INCIDENT_RESPONSE_SPEC.md`
  - `SECRETS_CONFIG_AND_ENVIRONMENT_SPEC.md`
  - `TRANSPARENCY_REPORTING_SPEC.md`
  - `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
- **Primary Downstream Dependents:**
  - `PUBLIC_API_SPEC.md`
  - `INTERNAL_SERVICE_API_SPEC.md`
  - `IDEMPOTENCY_AND_VERSIONING_SPEC.md`
  - `MIGRATION_AND_BACKWARD_COMPATIBILITY_SPEC.md`
  - `WORKFLOW_AND_AUTOMATION_SPEC.md`
  - `JOB_QUEUE_AND_WORKER_SPEC.md`
  - `AI_ORCHESTRATION_SPEC.md`
  - `MODEL_ROUTING_AND_CONTEXT_SPEC.md`
  - `AI_USAGE_METERING_SPEC.md`
  - domain event catalogs
  - webhook catalog contracts
  - AsyncAPI / OpenAPI / SDK derivation layers
  - dispatcher, outbox, retry, replay, and delivery-observability implementations
  - product integration specifications
- **Supersedes:** Earlier or weaker interpretations that treat events as transport-only side effects, let public webhooks mirror internal events automatically, let workflow or queue infrastructure redefine business event meaning, treat audit/activity streams as substitutes for event contracts, or allow public trust surfaces to consume leaked internal state as if it were supported webhook truth
- **Superseded By:** None currently defined
- **Related Decision Records:** Not explicitly specified in the retrieved governing materials
- **Canonical Status Note:** This document is the canonical governing system specification for FUZE event semantics and webhook contract posture. Downstream APIs, workflow systems, queue/worker systems, AI systems, retry/replay tooling, public integrations, delivery infrastructure, observability systems, and contract artifacts MUST preserve the ownership, truth-separation, compatibility, and exposure rules defined here.
- **Implementation Status:** Normative source; downstream services, event publishers, consumers, outboxes, dispatchers, schemas, routing rules, replay tooling, and operator tooling MUST align
- **Approval Status:** Draft refined canonical specification pending explicit approval workflow
- **Change Summary:** Refined the prior event/webhook draft into a registry-aligned production-grade canonical specification; normalized the distinction among domain events, integration events, operational events, and public webhook projections; strengthened truth classes, ownership boundaries, public-exposure rules, replay/idempotency discipline, ordering posture, security constraints, operator controls, read-model restrictions, and implementation-contract guardrails

## Title
FUZE Event Model and Webhook Specification

## Purpose
This specification defines the canonical event model and webhook architecture of FUZE.

Its purpose is to make explicit:
- what events mean in FUZE and what they do not mean
- how internal domain outcomes are emitted, classified, versioned, persisted, consumed, retried, replayed, and audited
- how FUZE distinguishes internal domain events, internal integration events, operational execution events, and externally supported webhook projections
- how eventing interacts with APIs, workflows, queues, AI systems, reporting systems, public trust surfaces, and control-plane tooling without collapsing into them
- how external webhook contracts are curated, secured, versioned, and supported
- what downstream implementations, schemas, and operators MUST preserve

This specification is intentionally governing rather than descriptive. It does not merely list event names or describe message transport. It defines the durable FUZE architecture for asynchronous coordination and curated outbound notification.

## Scope
This specification governs:
- the shared FUZE eventing and webhook layer across platform and product domains
- event classes, naming posture, envelope structure, lifecycle semantics, and timing semantics
- ownership-aligned event publication and consumption discipline
- durable event persistence posture and replay lineage expectations
- retry, deduplication, idempotency, and bounded ordering expectations
- public webhook exposure rules, endpoint lifecycle rules, dispatch behavior, and security posture
- interaction with internal service APIs, public APIs, workflow, queue/worker, AI, reporting, security, audit, and operations domains
- event and webhook implementation-contract guardrails for downstream contract layers

## Out of Scope
This specification does not define:
- every domain-specific event payload field in full
- the exact broker product, queue vendor, or transport substrate
- the full machine-readable AsyncAPI or OpenAPI documents
- the complete workflow-state model, queue lease model, or audit-event schema
- exact SDK package shapes
- exact notification copy, email content, or UI event-feed wording
- arbitrary public subscription to all internal event classes
- frontend-local state changes as canonical platform events

Those concerns belong in adjacent and downstream specifications, provided they remain consistent with this document.

## Design Goals
The design goals of the FUZE event model and webhook architecture are to:
1. make cross-domain side effects explicit, durable, and ownership-safe
2. preserve separation between business truth, execution truth, event truth, audit truth, and public publication truth
3. distinguish internal event contracts from externally supported webhook contracts
4. support retries, replay, deduplication, and idempotent downstream handling without inventing false exactly-once guarantees
5. support asynchronous, long-running, and multi-domain progression without hiding material behavior behind synchronous APIs
6. expose external webhook contracts only where they are supportable, stable, secure, and public-safe
7. preserve auditability, correlation lineage, policy traceability, and operational supportability
8. provide a future-safe shared event vocabulary for a growing multi-product platform ecosystem

## Non-Goals
This specification is not intended to:
- make every internal state transition a public webhook
- let consumers infer mutation ownership from event receipt alone
- replace owner-domain APIs with free-form event publishing
- turn audit logs or activity feeds into the primary event contract layer
- let queue, worker, workflow, reporting, or frontend systems redefine event meaning
- promise exactly-once delivery where infrastructure and multi-domain reality cannot sustain it
- expose governance-sensitive, treasury-sensitive, fraud-sensitive, support-sensitive, or unpublished economic internals as public webhook contracts by default

If there is tension between convenience and ownership-safe explicitness, the ownership-safe interpretation wins.

## Core Principles
### 1. Owner-Domain Event Principle
Only the domain that owns the meaning of a business fact may emit the canonical event describing that fact.

### 2. Event-Is-Not-Write-Authority Principle
Receiving an event does not grant mutation authority over the underlying truth.

### 3. Internal-Is-Broader-Than-Public Principle
Internal event families MAY be richer and more numerous than externally exposed webhooks. Public webhook exposure MUST remain narrower, safer, and more stable.

### 4. Accepted-vs-Final Principle
An accepted intent, an in-progress operational state, and a final business fact are different event meanings and MUST NOT be conflated.

### 5. Immutable-Envelope Principle
Accepted event envelopes are immutable historical records. Correction happens by later events or explicit remediation lineage, not by rewriting prior event meaning.

### 6. Derived-Public-Projection Principle
A webhook payload is a derived externally supported projection of internal truth, not the canonical owner-domain record.

### 7. Normalization-Before-Influence Principle
Provider callbacks, partner signals, and chain-observation inputs MUST be normalized and validated before influencing owner-domain truth or event emission.

### 8. Replay-Safe Principle
Replay, retry, and redelivery are expected operating conditions. Event design and consumer design MUST assume duplicate handling and explicit lineage.

### 9. Sensitive-Surface Restraint Principle
Control-sensitive, fraud-sensitive, treasury-sensitive, governance-sensitive, and unpublished economic events require narrower exposure and stronger handling.

### 10. Auditability Principle
Meaningful publication, projection, dispatch, replay, redelivery, disablement, quarantine, and override actions MUST remain reconstructible through durable lineage.

## Canonical Definitions
### Domain Event
A canonical event emitted by the domain that owns the meaning of a business fact or accepted intent.

### Integration Event
A stable downstream coordination event emitted for internal cross-domain collaboration, derived from owner-domain truth without transferring ownership.

### Operational Event
An event describing execution-plane or operational progression, such as accepted, queued, running, retrying, failed, canceled, quarantined, or completed execution states.

### Public Webhook Event
A curated external contract projection intentionally exposed to external consumers under stricter compatibility, exposure, and security rules.

### Event Envelope
The durable metadata wrapper that gives an event its stable identity, lineage, timing, classification, and handling context.

### Event Payload
The domain-defined body describing the fact, accepted intent, or operational transition represented by the event.

### Webhook Projection
The externally supported representation of an internal event after public-safety filtering, schema shaping, and version assignment.

### Consumer Checkpoint
A durable consumer-local record used to support deduplication, replay safety, and progress tracking for event consumption.

### Replay
A bounded reprocessing action over previously accepted event records that preserves original event identity and marks replay lineage explicitly.

### Redelivery
A bounded re-attempt of webhook dispatch for an already projected external event. Redelivery creates new delivery attempts, not new business facts.

### Public Exposure Class
The classification that determines whether and how an internal event may be projected outward.

## Truth Class Taxonomy
This specification MUST preserve the following truth classes:
1. **Semantic truth** — what an event means and which domain owns that meaning
2. **Policy truth** — rules governing publication eligibility, external exposure, retention, replay, compatibility, and security posture
3. **Runtime truth** — current dispatch, retry, consumer, queue, workflow, or execution state
4. **Ledger / storage truth** — durable event records, projection records, delivery attempts, consumer checkpoints, and lineage references
5. **Provider-input truth** — raw external inputs before owner validation and normalization
6. **Implementation-adapter truth** — broker mediation, dispatcher behavior, schema mapping, and transport adaptation state
7. **Projection / reporting truth** — dashboards, operational summaries, exports, activity feeds, and public publication projections
8. **Presentation truth** — UI wording, endpoint dashboards, support views, and human-readable operational labels

These truth classes MUST remain distinct. Eventing does not absorb domain ownership, audit ownership, workflow ownership, queue ownership, or public publication ownership.

## Architectural Position in the Spec Hierarchy
This document sits below:
- `REFINED_SYSTEM_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`

and above or alongside:
- `API_ARCHITECTURE_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- `IDEMPOTENCY_AND_VERSIONING_SPEC.md`
- `MIGRATION_AND_BACKWARD_COMPATIBILITY_SPEC.md`
- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `JOB_QUEUE_AND_WORKER_SPEC.md`
- `AI_ORCHESTRATION_SPEC.md`
- `MODEL_ROUTING_AND_CONTEXT_SPEC.md`
- `AI_USAGE_METERING_SPEC.md`
- domain event catalogs
- webhook catalogs
- AsyncAPI derivation artifacts

This document governs event and webhook semantics. It does not replace adjacent specifications that own API exposure, workflow meaning, queue meaning, audit meaning, or public artifact meaning.

## System Boundaries
The event and webhook layer spans multiple FUZE planes but does not own all truths within those planes.

It MUST be interpreted as follows:
- the **application plane** owns canonical business acceptance and owner-domain mutation boundaries; domain events arise from accepted or committed owner-domain outcomes
- the **execution plane** emits operational events about deferred, queued, running, retried, failed, or completed work, but execution systems do not become owners of upstream domain meaning
- the **integration plane** mediates external inputs and outbound webhook delivery; raw provider inputs are not canonical events until normalized and accepted
- the **reporting plane** may project event-derived views, exports, activity feeds, and public artifacts, but those projections are downstream to canonical event and domain truth
- the **control plane** may constrain, quarantine, replay, or redeliver under policy, but control actions do not rewrite event meaning
- the **on-chain contract layer** remains separately bounded; contract-native state may be observed and represented, but off-chain policy MUST NOT be misrepresented as on-chain fact

## Adjacent Boundaries
This specification interacts with adjacent domains as follows:
- **API Architecture** governs surface-family posture; events complement APIs rather than replace them
- **Public API** governs externally callable route posture; webhooks are curated outbound contracts adjacent to but distinct from public APIs
- **Internal Service API** governs service-to-service requests; internal events are not a license for hidden cross-domain writes
- **Workflow and Automation** owns workflow-instance meaning; workflow-related events describe workflow truth but do not replace it
- **Job Queue and Worker** owns queue, lease, attempt, retry, timeout, and dead-letter semantics; operational events may reflect that truth without redefining it
- **AI Orchestration** owns AI run lifecycle and result-governance meaning; AI events describe those outcomes without turning eventing into orchestration ownership
- **Audit Log and Activity** owns immutable audit records and user-facing activity semantics; event records and audit records are correlated but non-identical
- **Security and Risk Control** governs sensitive-path posture, secret handling, destination restrictions, and abuse response
- **Migration and Backward Compatibility** governs overlap periods, cutovers, coexistence windows, and deprecation rules

## Conflict Resolution Rules
When materials, implementations, or interpretations conflict, the following rules apply:
1. the active refined registry and higher constitutional materials win over narrower documents
2. `PLATFORM_ARCHITECTURE_SPEC.md` wins on plane separation and platform role boundaries
3. `DOMAIN_OWNERSHIP_MATRIX_SPEC.md` and `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md` win on owner-domain interpretation and mutation authority
4. this document wins on event classification, event-envelope posture, replay/redelivery semantics, and webhook exposure rules
5. `API_ARCHITECTURE_SPEC.md` wins on cross-surface interface posture where eventing touches broader API families
6. `PUBLIC_API_SPEC.md` wins on external/public contract posture outside webhook-specific outbound semantics
7. `INTERNAL_SERVICE_API_SPEC.md` wins on service-call semantics where service APIs and events interact
8. domain owner specifications win on business meaning, lifecycle truth, and mutation semantics of domain-specific events
9. audit/activity projections, dashboards, exports, and convenience layers never win over owner-domain truth or canonical event lineage
10. when ambiguity remains, FUZE MUST prefer the more conservative architecture-consistent interpretation and escalate unresolved ambiguity into downstream refinement or recorded decision work

## Default Decision Rules
When ambiguity exists and no narrower approved exception is available, FUZE MUST default to the following:
1. canonical business events default to owner-domain publication only after acceptance or commit
2. external exposure defaults to internal-only unless explicitly approved
3. webhook payloads default to minimal, stable, identifier-forward projections rather than full mutable snapshots
4. delivery semantics default to at-least-once with deduplication obligations, not exactly-once claims
5. consumer design defaults to idempotent, replay-safe handling
6. replay defaults to preserving original event identity and marking replay lineage explicitly
7. redelivery defaults to creating a new delivery attempt against the same event and projection lineage
8. restricted domains default to non-public exposure and stronger internal authorization
9. when an event cannot clearly name its owner domain, timing meaning, truth class, exposure class, and version posture, it is incomplete and MUST NOT ship as production-grade

## Roles / Actors / Entities
### Human Actors
- end users
- workspace members and owners
- partner integrators
- support operators
- security reviewers
- product operators
- finance/control-plane operators
- governance or approval actors

### System Actors
- owner-domain services
- integration adapters
- workflow engines
- queues and workers
- AI runtimes
- event outbox / store
- internal consumers
- webhook projection services
- webhook dispatchers
- audit and observability systems
- public registry and transparency publication systems
- control-plane tooling

### Core Entity Families
- `event_record`
- `event_consumer_checkpoint`
- `webhook_endpoint`
- `webhook_event_projection`
- `webhook_delivery_attempt`
- request lineage references
- correlation and causation references
- policy version references
- replay and redelivery references

## Ownership Model
### Canonical Ownership
- the **producer domain** owns the semantic meaning of the event
- the **eventing domain** owns the shared envelope rules, persistence posture, publication discipline, replay lineage, and shared tooling semantics
- the **integration/webhook domain** owns projection and dispatch posture for externally supported contracts
- the **consumer domain** owns its own checkpoints, dedupe logic, and side-effect discipline
- the **security domain** owns cross-cutting destination restrictions, signing-secret posture, and abuse response constraints
- the **audit domain** owns immutable audit records for management and control actions

### Ownership Constraints
- eventing infrastructure stores events but does not become the owner of the underlying business truth
- webhook projections do not replace canonical event or domain truth
- delivery-attempt records are operational truth, not business truth
- consumers MUST NOT mutate foreign owner-domain truth solely because they received an event

## Authority / Decision Model
### Authority to Publish
A service MAY publish a canonical event only when:
- it is acting as or on behalf of the owning domain
- the underlying owner-domain outcome has been accepted or committed
- the service principal is authorized for the declared producer domain
- required envelope, lineage, and classification metadata are present

### Authority to Project Publicly
An event MAY be projected to a public webhook only when:
- the event’s exposure class explicitly permits it
- the payload can be supported as a stable external contract
- the event does not leak restricted internals or unsupported semantics
- the receiving endpoint is registered within an approved scope and environment

### Authority to Override
Disablement, quarantine, forced replay, and redelivery are control actions. They MUST be reason-coded, policy-constrained, attributed to an actor or service principal, and durably audited.

## State Model
### Event Lifecycle
`candidate -> accepted_for_publication -> persisted -> available_for_consumption -> consumed | replayed | archived`

### Async Accepted-Intent Lifecycle
`requested -> accepted -> queued | scheduled -> running -> completed | failed | canceled`

### Webhook Projection Lifecycle
`not_eligible -> eligible -> projected -> scheduled -> dispatched -> delivered | retrying | dead_lettered | suppressed`

### Endpoint Lifecycle
`draft -> verification_pending -> verified -> active -> quarantined | disabled | retired`

### Delivery Attempt Lifecycle
`scheduled -> sent -> acknowledged | retryable_failure | terminal_failure | expired`

## Lifecycle / Workflow Model
1. an owner domain accepts or commits a meaningful outcome
2. the owner domain publishes a canonical event envelope and payload through the eventing capability
3. the event record is durably persisted before publish acknowledgement is treated as successful
4. internal consumers process the event under idempotent handling rules
5. if the event is externally eligible, the webhook projection layer maps it to an approved public contract projection
6. the dispatcher signs and attempts delivery to subscribed, verified, eligible endpoints
7. delivery attempts are tracked independently of business truth
8. retries, replay, quarantine, redelivery, or suppressions occur under explicit policy and lineage

## Invariants
The following invariants are mandatory:
1. canonical domain events MUST originate from the owning domain
2. canonical events MUST NOT be emitted before the underlying owner-domain action has been accepted or committed
3. accepted-state events MUST NOT masquerade as completed-state events
4. event envelopes MUST be immutable after acceptance
5. event names and versions MUST identify stable business meaning
6. public webhook projection MUST remain downstream to internal event truth
7. webhook delivery failure MUST NOT roll back the underlying business fact
8. duplicate receipt MUST be expected for internal consumption and webhook delivery
9. replay and redelivery MUST preserve historical lineage rather than erase it
10. restricted internal domains default to non-public exposure

## Functional Rules
### Event Classification Rules
FUZE recognizes four canonical classes:
- **domain events** for owner-domain facts or accepted intents
- **integration events** for stable internal downstream coordination
- **operational events** for runtime progression and execution states
- **public webhook events** for curated external projections

### Timing Rules
- **fact events** describe something now true in owner-domain semantics
- **accepted events** describe that work was durably admitted for later execution
- **operational events** describe execution progression without claiming final business success
- **publication events** describe that a public-facing artifact or registry item entered a governed publication state

### Naming Rules
- event names MUST follow durable, domain-oriented naming such as `<domain>.<entity_or_scope>.<action_or_state>`
- names MUST describe meaning, not transport, implementation detail, or UI wording
- names MUST remain stable unless business meaning changes materially
- aliases, product-local shorthand, and ambiguous synonyms are non-canonical and SHOULD be removed over time

### Payload Rules
- payloads MUST prefer stable identifiers, bounded status fields, and lineage references over oversized mutable snapshots
- payloads MUST clearly separate canonical identifiers from display fields and derived convenience fields
- payloads SHOULD include actor, scope, and resource references where necessary for downstream attribution
- payloads MUST NOT embed secrets, raw credentials, or unsupported sensitive internals

### Ordering Rules
- FUZE does not guarantee global total ordering across all event families
- ordering MAY be stronger within a partition or resource lineage, but downstream consumers MUST NOT assume stronger guarantees than documented
- when order matters to correctness, the event contract MUST define partitioning or sequencing expectations explicitly

### Consumer Rules
- consumers MUST be idempotent and replay-safe
- consumers MUST NOT treat one successful processing attempt as proof that duplicates cannot occur later
- consumers MUST NOT infer authorization for foreign writes from event receipt
- consumers SHOULD checkpoint and dedupe using event identity and consumer-local references

## Permission / Access Considerations
### Internal Publication and Consumption
- internal publication requires authenticated service identity and publish grants for the producer domain
- internal consumption requires authenticated service identity and least-privilege grants for the relevant event families
- sensitive topics require narrower trust tiers and explicit grants

### Webhook Management
- endpoint registration, update, disablement, verification, and redelivery requests require owner-scoped or admin-scoped authorization as appropriate
- cross-scope endpoint management is forbidden unless an explicitly authorized control-plane path permits it
- management visibility MUST remain scoped; callers may view only endpoints and delivery records they are entitled to inspect

## Entitlement Considerations
Entitlement MAY constrain which products or scopes may register and consume certain webhook families, but entitlement does not redefine event meaning. Event semantics remain owned by the producer domain and this shared eventing specification.

## API / Contract Implications
This specification governs several contract families:
1. internal publication interfaces for accepted event publication
2. internal consumption interfaces or broker subscriptions for downstream consumers
3. webhook-management APIs for endpoint lifecycle and delivery visibility
4. outbound webhook delivery contracts for approved external consumers

### Webhook Management Expectations
The webhook-management layer SHOULD support contracts for:
- endpoint registration
- endpoint verification
- endpoint listing and detail
- endpoint update
- secret rotation
- disablement and quarantine visibility
- delivery-attempt listing
- bounded redelivery request

Mutation-capable webhook-management routes MUST require idempotency protection and MUST emit audit lineage.

## Event / Async Implications
- eventing is a primary coordination layer for cross-domain async progression
- accepted-state APIs SHOULD pair with accepted events where downstream systems must react asynchronously
- workflow and queue systems MAY emit operational events, but those events MUST not redefine workflow or queue ownership
- event-driven triggers for workflows or jobs MUST pass through owning policy and authorization checks rather than silently executing material effects

## Data Model / Storage Implications
### Canonical Records
#### `event_record`
Represents an immutable accepted event envelope and payload.

Minimum durable facts:
- `event_id`
- `event_name`
- `event_family`
- `event_version`
- `producer_domain`
- `producer_service`
- `entity_type`
- `entity_id`
- `scope_type`
- `scope_id`
- `correlation_id`
- `causation_id`
- `idempotency_key_ref`
- `occurred_at`
- `accepted_at`
- `partition_key`
- `sequence_ref` nullable
- `payload_json`
- `sensitivity_class`
- `public_exposure_class`
- `audit_lineage_ref`
- `replayable_until`
- `retention_class`

#### `event_consumer_checkpoint`
Represents consumer-local idempotent processing lineage.

Minimum durable facts:
- `consumer_checkpoint_id`
- `consumer_name`
- `event_id`
- `processed_at`
- `processing_result`
- `replayed_flag`
- `dedupe_key`

#### `webhook_endpoint`
Represents a registered external delivery target.

Minimum durable facts:
- `webhook_endpoint_id`
- `owner_scope_type`
- `owner_scope_id`
- `environment`
- `endpoint_url`
- `status`
- `secret_ref`
- `signing_algorithm`
- `event_subscription_mode`
- `event_filter_json`
- `verified_at`
- `disabled_at`
- `quarantined_at`
- `created_at`
- `updated_at`

#### `webhook_event_projection`
Represents an externally supported projection of an internal event.

Minimum durable facts:
- `webhook_event_projection_id`
- `event_id`
- `public_event_name`
- `public_event_version`
- `projection_schema_version`
- `resource_type`
- `resource_id`
- `status`
- `payload_json`
- `published_eligibility_class`

#### `webhook_delivery_attempt`
Represents one outbound attempt to deliver a webhook projection.

Minimum durable facts:
- `webhook_delivery_attempt_id`
- `webhook_endpoint_id`
- `event_id`
- `delivery_id`
- `attempt_number`
- `scheduled_at`
- `sent_at`
- `response_status_code`
- `response_latency_ms`
- `delivery_state`
- `next_retry_at`
- `last_error_code`
- `last_error_detail_redacted`
- `signature_key_version`
- `payload_hash`
- `archived_headers_json`

### Storage Rules
- event records are immutable event history, not mutable state rows
- webhook projections are derived records, not owner-domain truth
- delivery attempts are operational records, not business facts
- cached summaries and dashboards are downstream projections and MUST NOT replace canonical storage truth

## Read Model / Projection / Reporting Rules
- activity feeds MAY summarize endpoint created, disabled, verified, or failed-delivery states, but such feeds remain derived from canonical event, delivery, and audit lineage
- dashboards MAY aggregate delivery health, backlog, retry volume, and success rates, but those dashboards are not authoritative for replay or redelivery decisions by themselves
- public registry or transparency surfaces MAY derive publication outputs from approved publication events, but they MUST NOT expose unsupported internal event classes as if they were public webhook contracts
- read models MUST preserve the distinction between internal canonical events, public projections, and operational delivery attempts

## Security / Risk / Abuse Controls
The following controls are mandatory where relevant:
1. webhook endpoint URLs MUST be HTTPS except where an explicitly bounded non-production sandbox exception exists
2. destination validation MUST block loopback, link-local, RFC1918, and otherwise forbidden destinations unless an explicit sandbox policy says otherwise
3. outbound webhook requests MUST be signed with governed secret material
4. secret rotation MUST support explicit versioning and overlap windows where policy requires them
5. verification, disablement, quarantine, and replay/redelivery tooling MUST be authorization-guarded and auditable
6. sensitive internal event families MUST NOT be projected externally by default
7. rate-limiting, abuse controls, and retry backoff MUST exist for external-facing webhook management and delivery surfaces
8. redacted diagnostics MUST be used where raw delivery failures could leak secrets or sensitive system detail

## Boundary Violation Detection / Non-Canonical Patterns
The following patterns are forbidden or non-canonical:
- a non-owning service emitting a canonical business event for another domain
- emitting completion events for work that has only been accepted or queued
- using webhook delivery attempts as proof of business success
- exposing all internal events to external consumers by default
- letting dashboards, activity streams, or support tooling act as canonical mutation surfaces
- rewriting event envelopes in place to “fix” history
- allowing replay or redelivery without lineage markers, policy checks, and audit records
- embedding secrets or privileged internals in event payloads or webhook bodies
- treating provider callbacks as canonical truth before normalization and owner-domain validation

Implementations SHOULD actively detect and alert on such drift patterns.

## Audit / Traceability Requirements
The following lineage is required where relevant:
- correlation identifiers for cross-domain request chains
- causation identifiers linking downstream events to prior accepted actions where meaningful
- actor or service-principal attribution for publish, projection, disablement, quarantine, replay, and redelivery actions
- policy or configuration version references for exposure and dispatch decisions where materially relevant
- durable timestamps for acceptance, projection, scheduling, send, retry, failure, and terminal delivery state

Every mutation-capable webhook-management action MUST create an audit record. Secret rotation, disablement, quarantine, and admin override actions are high-sensitivity audit events.

## Failure Handling / Edge Cases
### Internal Publication Failure
- if durable persistence fails, the event MUST NOT be acknowledged as successfully published
- transient publication failures MAY be retried under owner-domain-safe idempotency posture

### Consumer Failure
- consumer-side processing MAY fail and retry independently of event persistence
- failed consumption MUST NOT justify mutating or deleting canonical event history
- poison-message and repeated-failure handling MUST be explicit and observable

### Webhook Delivery Failure
- delivery failure MUST NOT roll back the underlying business fact or canonical event
- retryable failures MAY schedule later attempts under bounded policy
- persistently unsafe or failing endpoints MAY be quarantined or disabled under policy

### Replay and Redelivery Edge Cases
- replay MUST preserve original `event_id`, `occurred_at`, and lineage while marking replay context explicitly
- redelivery MUST create a new delivery attempt record while referencing the same event and projection lineage
- replay and redelivery eligibility windows MUST be policy-bounded

### Degraded Mode
- in degraded runtime conditions, FUZE MUST prefer visible backlog growth, controlled suppression, or explicit hold states over silent loss of meaningful event history
- degraded handling MUST preserve recoverability and operator visibility

## Operational Considerations
Operational implementations SHOULD support:
- backlog visibility by event family and partition
- delivery success/failure observability
- consumer lag visibility
- bounded replay tooling
- bounded redelivery tooling
- quarantine review workflows
- secret rotation workflows
- environment separation between sandbox and production webhook lanes
- retention and archival controls appropriate to event sensitivity and replay needs

## Migration / Compatibility / Supersession Considerations
- every event contract MUST have explicit version posture
- every public webhook projection MUST have explicit public version posture
- breaking changes require new versions rather than silent mutation
- additive evolution is preferred over destructive field changes
- public webhook deprecations require overlap periods and migration guidance
- machine-readable contract derivations MUST remain consistent with this canonical narrative source
- superseded event aliases, names, and projections SHOULD be retired through explicit compatibility planning, not silent replacement

## Implementation-Contract Guardrails
Downstream implementation-contract layers MUST preserve at minimum:
- owner-domain publication discipline
- immutable event-envelope posture
- explicit event class and timing semantics
- public exposure classification and narrowing rules
- idempotent consumer and delivery behavior
- replay and redelivery lineage separation
- event-versus-audit-versus-delivery truth separation
- authorization and audit posture for management and control paths
- bounded handling of sensitive event families and sensitive diagnostics

Downstream layers MUST NOT optimize away:
- durable event identity
- correlation and causation lineage where required
- exposure classification
- replay and redelivery auditability
- accepted-vs-completed semantic distinction

## Downstream Execution Staging
The following downstream layers are expected:
1. event catalog definitions by domain
2. machine-readable AsyncAPI and schema artifacts
3. webhook catalog and exposure matrix
4. dispatcher and retry policy implementation specs
5. replay and redelivery operator tooling specs
6. observability and incident-response runbooks

## Required Downstream Specs / Contract Layers
This specification requires or informs downstream work in:
- domain event-catalog specifications
- webhook-catalog specifications
- AsyncAPI derivation artifacts
- internal publication and consumption contract definitions
- replay and redelivery operator tooling contracts
- dispatcher implementation contracts
- delivery observability and incident-response runbooks
- storage schema and retention implementation documents

## Canonical Examples / Anti-Examples
### Canonical Examples
- `billing.subscription.changed` emitted by the billing domain after owner-domain acceptance of the change
- `workflow.run.accepted` emitted when workflow execution has been durably admitted, before final completion exists
- `job.execution.retry_scheduled` emitted as an operational execution event without claiming business success
- `product.request.completed` projected externally only if that completion class is approved and supportable as a webhook contract

### Anti-Examples
- a frontend action emitting `credits.balance.updated` directly
- a worker emitting `payout.executed` before the payout owner domain accepts final execution truth
- exposing `fraud.signal.detected` as a standard public webhook
- using a delivery 2xx as proof that the business action itself succeeded

## Dependencies / Cross-Spec Links
This specification materially depends on and coordinates with:
- `API_ARCHITECTURE_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- `IDEMPOTENCY_AND_VERSIONING_SPEC.md`
- `MIGRATION_AND_BACKWARD_COMPATIBILITY_SPEC.md`
- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `JOB_QUEUE_AND_WORKER_SPEC.md`
- `AUDIT_LOG_AND_ACTIVITY_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `MONITORING_ALERTING_AND_INCIDENT_RESPONSE_SPEC.md`
- domain owner specifications across identity, workspace, billing, credits, AI, governance, treasury, payouts, transparency, and product domains

## Explicitly Deferred Items
The following are intentionally deferred to narrower specifications or implementation documents:
- exact domain-by-domain event catalogs
- exact broker, topic, partition, or queue topology choices
- exact payload field definitions for every event family
- exact public webhook catalog and partner-specific subscription matrices
- exact signature format and canonical header names
- exact retry schedules, backoff constants, and dead-letter thresholds by event family
- exact retention periods by event sensitivity class
- exact operator UI behavior for replay, redelivery, and quarantine tooling

## Final Normative Summary
FUZE eventing is the platform’s durable asynchronous coordination layer, and FUZE webhooks are its curated external notification layer. The owning domain publishes canonical events for accepted or committed outcomes. Internal consumers react under idempotent, replay-safe rules. Public webhooks are derived, narrower, and more stable projections of approved internal events. Delivery, replay, redelivery, and projection infrastructure are operationally important but do not replace owner-domain truth. Downstream systems MUST preserve ownership boundaries, truth-class separation, immutable event history, explicit timing semantics, bounded public exposure, strong auditability, and policy-governed control actions.

## Quality Gate Checklist
- [x] Canonical owner is explicit for every material truth family
- [x] Mutation boundaries are explicit
- [x] Adjacent boundaries are explicit
- [x] Truth classes are explicit
- [x] Conflict-resolution rules are explicit
- [x] Default decision rules are explicit
- [x] Non-canonical patterns are called out clearly
- [x] Operator/admin override paths are bounded and audited
- [x] Read-model, cache, reporting, and projection rules are explicit
- [x] On-chain vs off-chain responsibility posture is explicit where relevant
- [x] Failure and degraded-mode behaviors are explicit
- [x] Downstream implementation guardrails are explicit
- [x] Dependencies and downstream impacts are explicit
- [x] Non-goals and deferred items are explicit
- [x] The document is strong enough that backend/API/data/runtime teams can implement without inventing contradictory semantics
