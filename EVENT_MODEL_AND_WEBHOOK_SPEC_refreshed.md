# EVENT_MODEL_AND_WEBHOOK_SPEC

## Purpose

This document defines the canonical event model and webhook architecture of the FUZE ecosystem. Its purpose is to establish how meaningful domain events are produced, classified, versioned, propagated, and consumed across the platform, and how FUZE should expose selected external webhook surfaces without weakening canonical ownership, security boundaries, or trust-sensitive platform controls.

This specification is foundational because FUZE is a multi-product, workflow-capable, transparency-first platform with shared identity, workspaces, Platform Credits, billing, AI orchestration, asynchronous jobs, governance-sensitive controls, treasury-sensitive actions, and payout-sensitive systems. In a platform of this kind, request/response APIs alone are not sufficient. The system needs a durable event model for cross-domain side effects, operational coordination, reporting inputs, and ecosystem notifications. At the same time, selected external consumers may require webhook delivery for partner integrations, async completion callbacks, or public-facing platform interactions. FUZE therefore treats event design and webhook design as first-class architecture layers rather than as incidental messaging details.

---

## Scope

This specification covers:

- the canonical event model of the FUZE ecosystem
- the distinction between events, audit records, activity feeds, and API responses
- the difference between internal domain events, integration events, system events, and public webhook events
- how events align with canonical entity ownership and mutation authority
- event production, delivery, retry, idempotency, and versioning principles
- webhook exposure rules, security rules, and partner-facing integration expectations
- event handling across identity, workspace, wallet-aware, credits, billing, AI, workflow, governance, treasury, and payout domains
- failure handling, replay, deduplication, observability, and auditability expectations for event-driven behavior

This specification does not define every queue implementation detail, every transport technology, or every domain-specific payload schema. Those are refined in:

- `API_ARCHITECTURE_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- `IDEMPOTENCY_AND_VERSIONING_SPEC.md`
- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `JOB_QUEUE_AND_WORKER_SPEC.md`
- `AUDIT_LOG_AND_ACTIVITY_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`

---

## Design Goals

The design goals of the FUZE event model and webhook architecture are:

1. to make cross-domain side effects explicit and durable rather than hidden behind synchronous chains
2. to align emitted events with canonical domain ownership and mutation truth
3. to distinguish internal coordination events from externally supported webhook contracts
4. to support retries, replay, deduplication, and idempotent downstream handling
5. to make long-running and asynchronous platform behavior more observable and supportable
6. to expose external webhooks only where they provide real integration value and remain safe to support
7. to preserve auditability, policy traceability, and trust-sensitive control boundaries
8. to create a consistent event vocabulary for a growing multi-product platform ecosystem

---

## Non-Goals

This specification is not intended to:

- turn every internal state change into a public webhook
- use events as an excuse to avoid clear ownership of canonical writes
- replace all synchronous APIs with message passing regardless of use case
- expose governance-, treasury-, or payout-sensitive internal control events externally by default
- treat audit logs and events as identical concepts
- guarantee exactly-once delivery semantics where infrastructure reality cannot support it
- define every reporting, analytics, or notification stream in this document

---

## Canonical Event Principle

The primary principle of the FUZE event model is:

> events in FUZE must communicate meaningful state changes or accepted domain outcomes from the owning domain outward, so that other systems can react without taking over the ownership of the underlying truth.

This means:

- events are produced by the domain that owns the fact
- events describe something that happened, was accepted, or became true in a domain sense
- consumers may react, project, report, notify, or orchestrate follow-up work, but they do not become the source of truth for the original mutation
- events should be durable enough to support replay and supportability where business-critical
- public webhook exposure must be much narrower than internal event production

This principle is one of the strongest safeguards against hidden cross-domain mutation and architectural drift.

---

## Why FUZE Needs a Formal Event Model

FUZE needs a formal event model because many important platform behaviors are multi-step, asynchronous, or cross-domain.

Examples include:

- a verified payment causing credits issuance
- a subscription renewal causing entitlement refresh
- a credits spend causing product-usage accounting side effects
- an AI request causing a background job and later result publication
- a workflow step completion causing downstream task execution
- a governance action causing reporting or registry updates
- a payout cycle funding action causing payout-ledger publication and stakeholder-facing visibility updates

If these behaviors are implemented only through direct synchronous calls, the platform becomes increasingly coupled and fragile. Services begin to depend on each other’s immediate availability, retry semantics become harder to reason about, and ownership boundaries blur because downstream consequences are hidden inside request chains.

A formal event model solves this by making the side-effect architecture explicit.

It answers:
- what just happened
- which domain owns that fact
- what downstream systems may react
- whether the reaction is internal, external, or reporting-related
- how retries and deduplication should work
- how support and audit systems can reconstruct the chain later

This is especially important in FUZE because the platform is designed for growth across products. The more products and services the ecosystem adds, the more valuable a disciplined event model becomes.

---

## Events vs APIs vs Audit Records

FUZE should explicitly distinguish among events, APIs, and audit records.

### APIs
APIs are request/response contracts used to ask a domain to do something, read something, or begin something.

### Events
Events are domain-originated notifications or records that something meaningful has happened, been accepted, or become true after domain processing.

### Audit Records
Audit records preserve durable traceability for support, governance review, finance reconciliation, dispute handling, and institutional memory. Many events should generate or correlate with audit records, but the two are not identical.

### Why the distinction matters

A platform becomes harder to reason about when:
- API acceptance is confused with final domain state
- audit records are treated as integration contracts
- events are treated as permission to mutate other domains directly
- public webhooks are mistaken for canonical truth sources

### Principle

APIs initiate or query.
Events propagate outcomes and state changes.
Audit records preserve durable review lineage.

FUZE should keep these concepts distinct even when one action creates all three.

---

## Event Families in FUZE

FUZE should recognize several distinct event families.

### 1. Domain Events
Produced by the domain that owns a canonical entity or business transition.

Examples:
- `workspace.created`
- `wallet.linked`
- `credits.issued`
- `subscription.renewed`
- `payout_cycle.published`

### 2. Integration Events
Produced for cross-domain coordination where downstream services need a stable signal derived from domain truth.

Examples:
- `payment.verified`
- `entitlements.refreshed`
- `product_job.completed`
- `payout_cycle.funded`

### 3. System / Operational Events
Produced for platform execution status, job lifecycle, or workflow coordination where operational systems need event-driven behavior.

Examples:
- `workflow.run.started`
- `workflow.step.failed`
- `job.retry.scheduled`
- `report.build.completed`

### 4. Public Webhook Events
Externally delivered events intended for third-party or partner consumption under explicit contract.

Examples:
- `product.request.completed`
- `credits.purchase.completed`
- `subscription.changed`
- `payout_cycle.opened` where public webhook exposure is explicitly supported

### Family principle

The same underlying platform fact may result in multiple representations across families, but those representations should remain clearly classified. Internal domain events are not automatically public webhook contracts.

---

## Canonical Domain Event Rules

Canonical domain events should follow strict ownership rules.

### Rule 1: only the owning domain emits canonical domain events for its truth
Examples:
- only the credits domain emits `credits.issued`
- only the subscription domain emits `subscription.changed`
- only the workspace domain emits `workspace.membership.updated`

### Rule 2: events describe completed or accepted domain outcomes
A domain event should not imply a mutation that the owning domain has not accepted.

### Rule 3: events must preserve domain language
Events should use the vocabulary of the owning domain, not the convenience vocabulary of a downstream consumer.

### Rule 4: emitted events should remain traceable to canonical mutation or state transition
Event lineage should point back to the domain action, request, job, or governance record that caused the event.

These rules matter because events become much more reliable when downstream consumers know they originate from the proper owner.

---

## Event Naming and Vocabulary

FUZE should use clear, domain-oriented event names.

### Naming pattern
A preferred pattern is:
`<domain>.<entity_or_scope>.<action_or_state>`

Examples:
- `identity.account.created`
- `workspace.membership.removed`
- `wallet.link.verified`
- `credits.balance.adjusted`
- `billing.subscription.renewed`
- `ai.task.completed`
- `workflow.run.failed`
- `governance.action.approved`
- `payout.cycle.funded`

### Naming principles

- names should reflect domain ownership
- names should be explicit enough to remain understandable outside the producing service
- names should avoid UI-specific wording
- names should distinguish state outcomes from mere request receipt when that distinction matters
- names should remain stable enough to support versioning discipline

Consistent vocabulary is especially important in a multi-product platform because many teams will consume events over time.

---

## Event Payload Principles

FUZE event payloads should preserve enough information for safe downstream handling without becoming unbounded raw data dumps.

At minimum, meaningful events should preserve:

- `event_id`
- `event_name`
- `event_version`
- `occurred_at`
- `producer_domain`
- `correlation_id`
- `entity_type`
- `entity_id`
- `scope_type` and `scope_id` where relevant
- `actor_reference` where relevant
- `status` or outcome state
- `payload` with structured domain fields
- `audit_lineage_reference` where relevant

### Payload principles

- include the minimum data needed for correct downstream reaction
- avoid embedding unnecessary sensitive content
- prefer stable identifiers and references over duplicating large mutable objects
- preserve explicit versioning
- include enough context that replay and support remain practical

### Important boundary

The event payload is not the full domain database record. It is a structured contract for downstream use.

---

## Event Timing Semantics

FUZE should distinguish event timing semantics clearly.

### Fact events
Describe something that has already happened or is now true.

Examples:
- `credits.issued`
- `subscription.cancelled`
- `payout.cycle.closed`

### Accepted events
Describe that a domain has accepted a request into an async workflow, not that the work is complete.

Examples:
- `report.generation.accepted`
- `product.job.accepted`

### Operational state events
Describe internal progress through multi-step execution.

Examples:
- `workflow.run.started`
- `job.execution.retrying`

### Principle

Event consumers must be able to tell whether the event means:
- the business fact is final,
- the request has been accepted for later work,
- or the platform is only reporting operational progress.

This distinction is essential to correctness.

---

## Event Delivery Semantics

FUZE should design event delivery with realistic platform guarantees.

### Preferred assumption
Events should generally be treated as **at-least-once delivered** rather than exactly once.

### Why this matters

In real distributed systems:
- retries happen
- broker redelivery happens
- consumer failures happen
- network interruptions happen

Trying to pretend exactly-once semantics exist everywhere often creates unsafe assumptions.

### Delivery principle

Consumers must be built to tolerate duplicates, and producers must preserve event identity and idempotency support so consumers can do so safely.

This is especially important in:
- credits flows
- subscription renewal flows
- payout publication flows
- governance reporting updates
- async product completion handling

---

## Idempotency and Deduplication in Event Consumers

Because delivery may be at-least-once, FUZE consumers must support idempotent handling where business consequences matter.

### Consumer requirements may include:
- remember processed `event_id`
- deduplicate by business idempotency reference where relevant
- ignore duplicate event deliveries that would otherwise duplicate mutation
- distinguish replay from new occurrence where needed

### Examples

#### Credits consumer
A downstream reporting consumer should not record the same `credits.issued` event twice as two separate issuance facts.

#### Product artifact publication
A result publication consumer should not create two identical external notification artifacts from duplicate completion events.

#### Webhook dispatcher
The webhook system should track delivery attempts and event identity to avoid confusing duplicate delivery with distinct events.

### Principle

Idempotency is not optional in an event-driven platform with economic and workflow-sensitive behavior.

---

## Event Ordering Principles

FUZE should avoid assuming perfect global event ordering.

### Appropriate assumptions
A domain may preserve meaningful ordering for events related to the same entity or stream where infrastructure supports it.

### Inappropriate assumptions
Consumers should not rely on a perfect globally ordered universe of all events across all services.

### Practical principles

- use timestamps and versions carefully
- design consumers to handle late arrivals or reordered deliveries
- prefer explicit entity state reads when downstream correctness depends on current truth rather than event arrival order alone
- use workflow or entity sequence references where ordering matters strongly

This matters because platform scale and distributed systems make overreliance on global ordering fragile.

---

## Internal Event Usage by Domain

### Identity and Access Events
Examples:
- `identity.account.created`
- `identity.session.revoked`
- `identity.linked_login.added`

Used for:
- audit
- support
- account lifecycle coordination
- downstream entitlement or risk systems

### Workspace Events
Examples:
- `workspace.created`
- `workspace.membership.added`
- `workspace.role.changed`

Used for:
- access updates
- billing scope recalculation
- product workspace provisioning
- audit feeds

### Wallet-Aware Events
Examples:
- `wallet.linked`
- `wallet.verified`
- `holder.rank.updated`

Used for:
- participation context refresh
- privilege updates
- payout context checks
- cross-product holder-aware behavior

### Credits and Billing Events
Examples:
- `payment.verified`
- `credits.issued`
- `credits.spent`
- `billing.subscription.renewed`
- `entitlement.changed`

Used for:
- product access changes
- balance projections
- finance reconciliation
- notifications
- audit and support

### AI and Workflow Events
Examples:
- `ai.task.accepted`
- `ai.task.completed`
- `workflow.run.started`
- `workflow.step.completed`
- `job.retry.scheduled`

Used for:
- async coordination
- UI status
- usage metering
- product result retrieval
- support visibility

### Governance, Treasury, and Payout Events
Examples:
- `governance.action.proposed`
- `treasury.action.approved`
- `vault.action.executed`
- `payout.cycle.funded`
- `payout.claim_window.opened`

Used for:
- reporting
- transparency surfaces
- payout ledger updates
- restricted internal operational flows
- audit and review

This domain mapping helps keep event usage explicit and disciplined.

---

## Webhook Philosophy in FUZE

Webhooks are externally delivered events and must be treated as a narrower, more carefully governed layer than internal events.

The primary webhook principle in FUZE is:

> only expose webhook events externally when they represent a stable, intentional integration contract that is useful to an external consumer and safe to support over time.

This means:
- not every internal event becomes a webhook
- webhook payloads may differ from internal event payloads
- public or partner-facing webhooks require stronger versioning, security, and support guarantees
- webhook consumers must never be assumed to be as trusted as internal services

This distinction is critical because webhook sprawl can easily turn internal architecture into accidental external contract surface.

---

## Appropriate Webhook Use Cases

FUZE should expose webhooks for external-facing workflows where callback-style notification materially improves integration quality.

Appropriate webhook categories may include:

### Product Async Completion
Examples:
- analysis completed
- generation finished
- export ready
- workflow request finished

### Commercial Lifecycle
Examples:
- purchase completed
- subscription changed
- renewal failed
- entitlement updated where externally relevant

### Public Transparency / Registry Publication
Examples:
- transparency report published
- payout cycle opened
- registry updated
- public artifact published

### Partner Integration Flows
Examples:
- partner request accepted/completed
- enterprise integration state changes
- approved ecosystem notification events

### Principle

Webhook use should be driven by real external workflow value, not by the desire to mirror internal event streams.

---

## Restricted or Unsafe Webhook Categories

Some events should remain internal only or highly restricted.

These generally include:

- raw governance approval workflow events
- treasury proposal internals
- Foundation-sensitive action internals
- raw payout entitlement preparation events
- internal fraud and abuse signals
- internal support overrides
- signer rotation or sensitive control-path internals
- security incident response details
- internal workflow retry churn not useful externally

### Principle

The public webhook layer should support externally meaningful workflows, not expose the internal nervous system of the platform.

---

## Webhook Delivery Model

FUZE webhooks should follow a clear delivery model.

At minimum, webhook delivery should support:

- explicit subscription or endpoint registration
- signed delivery to consumer endpoint
- delivery attempt tracking
- retry with bounded backoff
- failure state visibility for operators
- disable or quarantine behavior for persistently failing endpoints
- event identity and version in each delivery
- replay support where policy allows

### Delivery principle

Webhook systems must be operated as real integration infrastructure, not as fire-and-forget notification utilities. External consumers depend on predictable behavior.

---

## Webhook Security Principles

Webhook delivery should follow strong security rules.

At minimum, FUZE should support:

- webhook signing or equivalent authenticity mechanism
- replay window protection where appropriate
- endpoint verification during registration
- scoped event subscriptions
- secret rotation support
- HTTPS-only delivery expectations
- abuse and destination validation
- operator visibility into delivery health

### Why this matters

Webhooks cross the platform boundary. Without strong authenticity and destination controls, they can become a security risk or a source of confusing spoofed behavior.

---

## Webhook Payload Design

Webhook payloads should be stable, minimal, and integration-oriented.

At minimum, webhook payloads should preserve:

- `webhook_event_id`
- `event_name`
- `event_version`
- `occurred_at`
- `delivery_attempt`
- `resource_type`
- `resource_id`
- `status`
- `data` with integration-relevant fields
- `correlation_id` where helpful

### Payload principles

- do not leak internal-only sensitive fields
- include enough fields that consumers can correlate or fetch richer data if needed
- preserve versioning discipline
- treat webhook payloads as supported contracts, not raw debug dumps

This is especially important because public contract changes become costly once external systems depend on them.

---

## Webhook Registration and Subscription Model

FUZE should support controlled webhook subscription models.

At minimum, webhook registration should define:

- endpoint URL
- owning account/workspace/partner
- subscribed event families or event names
- authentication / secret configuration
- active status
- retry policy class
- environment or sandbox/production separation
- created_at / updated_at metadata

### Subscription principle

Consumers should subscribe to explicit event scopes. FUZE should avoid broad “everything” subscriptions by default because they increase security risk, support burden, and contract instability.

---

## Event and Webhook Versioning

Events and webhooks must support explicit versioning.

### Principles
- every event contract should carry an `event_version`
- breaking payload changes require version evolution
- public webhook contracts should be more stable than internal event contracts
- internal event changes still require discipline when many consumers exist
- documentation and schema evolution should remain explicit

### Important distinction

Internal event evolution may be faster than webhook evolution, but neither should be casual in a multi-product platform. Versioning is part of long-term supportability.

---

## Event Replay and Reprocessing

FUZE should support controlled replay for internal events where operationally necessary.

Replay may be needed for:
- rebuilding derived read models
- recovering failed consumers
- regenerating reporting outputs
- reconciling payout-ledger updates
- reprocessing workflow side effects after incident recovery

### Replay principles

- replay should preserve original event identity
- consumers must distinguish replay from first delivery where relevant
- replay authorization should be controlled
- not all external webhooks need consumer-facing replay guarantees by default
- trust-sensitive replay flows should maintain audit lineage

Replay support is especially valuable in a platform with transparency, payout, and economic reporting dependencies.

---

## Auditability and Observability of Event Flows

Event-driven behavior should remain observable and auditable.

At minimum, FUZE should preserve:

- event production logs or lineage
- consumer processing outcome
- retry state
- dead-letter or failure handling references
- webhook delivery attempts and outcomes
- correlation to originating API request, job, or governance action
- audit event linkage for trust-sensitive domains

### Principle

Event systems should not become invisible side-effect machinery. The platform must be able to explain what events were produced, who consumed them, and what happened when something failed.

---

## Failure Handling and Dead-Letter Principles

FUZE should explicitly define failure behavior for event consumers and webhook delivery.

### Internal event failure principles
- retry transient failures
- stop and surface poison-message patterns
- use dead-letter or quarantine paths where appropriate
- preserve operator visibility and replay options
- avoid unsafe automatic mutation retries that duplicate business meaning

### Webhook failure principles
- retry with bounded policy
- mark failing endpoints visibly
- optionally disable endpoints after repeated failure
- allow operator or consumer remediation
- preserve delivery history

### Principle

A failed consumer or webhook should not silently erase platform behavior. Failures should be observable, recoverable, and auditable.

---

## Minimum Architectural Entities

At minimum, the FUZE event model and webhook architecture should recognize the following conceptual entities:

### Event Entities
- `event_id`
- `event_name`
- `event_family`
- `event_version`
- `producer_domain`
- `occurred_at`
- `entity_type`
- `entity_id`
- `correlation_id`

### Delivery Entities
- `delivery_id`
- `consumer_reference`
- `delivery_status`
- `attempt_count`
- `last_attempted_at`
- `next_retry_at` where applicable
- `dead_letter_reference` where applicable

### Webhook Entities
- `webhook_endpoint_id`
- `subscriber_reference`
- `subscribed_event_scope`
- `endpoint_url`
- `secret_reference`
- `endpoint_status`
- `last_delivery_status`

### Integrity and Audit Entities
- `idempotency_reference`
- `audit_lineage_reference`
- `workflow_reference`
- `governance_action_reference` where applicable
- `payout_cycle_reference` where applicable
- `replay_reference` where applicable

These are minimum conceptual entities. Detailed schema and transport implementation are refined downstream.

---

## Open Items

The following areas are intentionally refined in downstream implementation and domain-level contracts:

- exact event catalog by domain
- exact broker/transport choices
- exact retry and dead-letter policies by event family
- exact webhook signing format
- exact replay tooling and authorization model
- exact documentation format for public webhook contracts
- exact partner-specific webhook subscription policies

These do not weaken the canonical event model and webhook architecture established here.

---

## Closing Summary

The FUZE event model and webhook architecture is the platform’s structured asynchronous coordination and external notification layer. It ensures that domains can publish meaningful outcomes without giving up ownership, that downstream systems can react through durable event contracts, and that selected external consumers can receive stable webhook notifications without exposing the internal control plane of the ecosystem. By separating internal domain events, integration events, operational events, and public webhook contracts—and by pairing them with versioning, idempotency, replay, auditability, and security discipline—FUZE creates an event architecture that supports multi-product scale without sacrificing architectural clarity.
