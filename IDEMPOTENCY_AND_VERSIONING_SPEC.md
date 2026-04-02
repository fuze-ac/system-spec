# IDEMPOTENCY_AND_VERSIONING_SPEC

## Purpose

This document defines the canonical idempotency and versioning architecture of the FUZE ecosystem. Its purpose is to establish how FUZE prevents unintended duplicate mutation across APIs, jobs, events, and external integrations, and how FUZE evolves contracts, payloads, endpoints, and public interfaces over time without undermining architectural clarity, economic correctness, or platform continuity.

This specification is foundational because FUZE is a multi-product, workflow-capable, transparency-first platform with shared identity, payments, Platform Credits, subscriptions, AI orchestration, async execution, treasury-sensitive actions, governance-sensitive controls, and payout-sensitive systems. In such an environment, duplicate execution and uncontrolled interface evolution are not minor implementation issues. They are systemic risks. Idempotency protects correctness under retries, replays, duplicate submissions, and failure recovery. Versioning protects continuity as APIs, events, ledgers, registries, and reporting surfaces evolve over time.

---

## Scope

This specification covers:

- the canonical idempotency philosophy of the FUZE ecosystem
- the distinction between idempotency, deduplication, replay safety, and conflict handling
- idempotency rules across public APIs, internal service APIs, async jobs, event consumers, webhook delivery, and trust-sensitive workflows
- versioning philosophy across APIs, events, webhook contracts, reports, registries, schemas, and public artifacts
- compatibility and deprecation rules
- how idempotency and versioning interact with entity ownership, auditability, workflow execution, credits, billing, governance, treasury, and payout-sensitive systems
- failure handling, correction handling, and recovery principles

This specification does not define every wire-level header, every schema version field, or every transport-level mechanism. Those are refined in:

- `API_ARCHITECTURE_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `JOB_QUEUE_AND_WORKER_SPEC.md`
- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `REFUND_REVERSAL_AND_ADJUSTMENT_SPEC.md`
- `PAYOUT_LEDGER_SPEC.md`

---

## Design Goals

The design goals of the FUZE idempotency and versioning architecture are:

1. to prevent unintended duplicate business mutation under retries, replays, and transient failures
2. to make write paths safe across public clients, internal services, workflows, jobs, and event consumers
3. to distinguish idempotent repetition from true conflict or attempted double execution
4. to preserve correctness in credits, billing, governance, treasury, and payout-sensitive flows
5. to enable controlled evolution of APIs, events, ledgers, registries, and published artifacts
6. to support backward compatibility where appropriate without freezing platform evolution
7. to improve auditability and supportability by making repeated actions and changed contracts easier to interpret
8. to make reliability and evolution part of the architecture rather than after-the-fact patches

---

## Non-Goals

This specification is not intended to:

- guarantee that all operations can be repeated safely without domain-specific design
- imply that every read operation needs explicit idempotency treatment
- treat conflict handling and idempotency as the same concept
- freeze every interface permanently in its first released shape
- force identical versioning mechanisms on every internal and external surface
- eliminate the need for correction workflows when incorrect but valid mutations already happened
- rely on infrastructure-level retry behavior without business-level safeguards

---

## Canonical Idempotency Principle

The primary principle of idempotency in FUZE is:

> if the same business action is submitted more than once due to retry, replay, or duplicate delivery, the platform should apply that action at most once in business meaning, while returning a stable and explainable outcome for subsequent equivalent attempts.

This means:

- retries should not create duplicate credits, duplicate subscriptions, duplicate payouts, duplicate governance actions, or duplicate product-side artifacts when the business action is meant to be singular
- the platform must distinguish between a repeated request for the same intended action and a genuinely new action that happens to look similar
- idempotency should be defined in business terms, not only transport terms
- trust-sensitive domains require stronger idempotency discipline than routine reads
- idempotency outcomes should remain traceable and auditable

This principle is one of the strongest correctness safeguards in the entire FUZE platform.

---

## Canonical Versioning Principle

The primary principle of versioning in FUZE is:

> every externally or cross-domain consumed contract surface should evolve in a deliberate, explicitly versioned way that preserves clarity about what changed, who is expected to adapt, and what compatibility guarantees still apply.

This means:

- interface evolution must be intentional
- public consumers require stronger stability guarantees than purely internal callers
- event and webhook consumers must be able to understand payload evolution
- reports, registries, and payout-ledger artifacts should preserve historical meaning when format or interpretation changes
- versioning should clarify change rather than hide it

This principle is essential because FUZE is intended to scale as a platform. Platform scale requires reliable evolution discipline.

---

## Why FUZE Needs Strong Idempotency

FUZE needs strong idempotency because many important actions can be retried or re-delivered under normal operating conditions.

Examples include:

- users retrying a purchase or product request after a timeout
- mobile or browser clients re-submitting the same request
- internal services retrying due to transient dependency failure
- workers replaying jobs after crash recovery
- event consumers receiving the same event more than once
- webhook dispatchers retrying external delivery
- payout publication or reporting flows being retried after partial failure
- governance-related workflows being resumed after operational interruption

Without strong idempotency, these conditions can cause serious business errors:
- duplicate credits issuance
- duplicate refunds
- duplicate subscription transitions
- duplicate product jobs or duplicated billable actions
- duplicate payout-cycle publication artifacts
- duplicate support compensations
- duplicate governance or treasury records

These are not merely annoying bugs. In credits, billing, governance, treasury, and payout domains they directly weaken trust, reconciliation, and platform integrity.

FUZE therefore treats idempotency as a business-correctness requirement, not merely as a network optimization.

---

## Why FUZE Needs Strong Versioning

FUZE also needs strong versioning because the platform includes many interface surfaces that will evolve over time:

- public APIs
- internal service contracts
- events
- webhooks
- reports
- public registries
- payout-ledger artifacts
- product resource schemas
- async job status contracts

Without disciplined versioning, platform growth creates ambiguity:
- consumers may not know whether payload changes are breaking
- external integrations may silently misinterpret new responses
- internal services may drift out of compatibility
- public artifacts may lose historical interpretability
- support and audit teams may struggle to understand which schema or behavior applied at a given time

Versioning makes platform evolution explainable. It helps FUZE grow without making old behavior or historical records unintelligible.

---

## Idempotency vs Deduplication vs Conflict

FUZE should distinguish clearly among idempotency, deduplication, and conflict handling.

### Idempotency
Idempotency means the same intended business action may be submitted multiple times, but the domain should apply it once and return a stable interpretation for repeats.

Example:
- a verified payment should not create credits twice if the same issuance request is retried.

### Deduplication
Deduplication is the operational act of recognizing repeated deliveries or repeated requests and suppressing duplicate downstream handling.

Example:
- an event consumer tracks processed `event_id` values to avoid handling the same event twice.

### Conflict
Conflict means the later request is not a valid repeat of the same action, but a competing mutation or incompatible request against the same resource or workflow state.

Example:
- one request attempts to cancel a subscription while another attempts to upgrade it from an incompatible state.

### Principle

Idempotency is about safe repetition of the same action.
Deduplication is one mechanism used to achieve that safety.
Conflict handling is about non-equivalent actions that require domain judgment or rejection.

Keeping these concepts separate makes platform behavior more correct and easier to explain.

---

## Business-Level Idempotency Keys

FUZE should prefer business-level idempotency over transport-only repetition detection.

A repeated request is idempotent only if the platform can identify that it refers to the same intended business action.

At minimum, business-level idempotency may be anchored by:

- explicit idempotency keys supplied by callers
- payment-provider transaction references
- unique workflow-operation references
- event IDs
- job IDs
- cycle IDs
- governance action proposal IDs
- domain-owned mutation request IDs

### Principle

The right idempotency key depends on the domain. A generic HTTP retry is not enough. The platform should know what business operation is being protected and what makes two attempts “the same.”

---

## Idempotency Scope

Idempotency in FUZE should always be scoped.

Examples of important scope dimensions include:

- account scope
- workspace scope
- product scope
- subscription scope
- payment scope
- payout-cycle scope
- governance-action scope
- job or workflow scope

### Why scope matters

The same idempotency key reused in different scopes should not necessarily collide. Likewise, the same user submitting two distinct product requests should not be treated as a duplicate merely because the payload looks similar.

### Scope principle

An idempotent action must be identifiable as the same action within the correct domain and scope boundary. Scope-free idempotency is often too weak or too dangerous.

---

## Idempotency Classes by Operation Type

Not all operations require the same idempotency treatment. FUZE should recognize operation classes.

### 1. Naturally Idempotent Reads
Repeated reads do not mutate business truth and therefore do not need business mutation protection.

Examples:
- get credits balance
- get workspace details
- get payout cycle status

### 2. Write-once Economic Mutations
These require the strongest idempotency protection.

Examples:
- credits issuance after verified payment
- credits reversal
- support compensation issuance
- subscription renewal record creation
- refund record creation tied to a specific refund decision

### 3. Async Request Submission
These require idempotent request acceptance rules.

Examples:
- AI job submission
- report generation request
- HerHelp generation request
- Botmad scan request

### 4. Governance / Treasury / Payout-Sensitive Actions
These require explicit, traceable idempotency because duplicate creation or duplicate advancement can be structurally harmful.

Examples:
- governance action proposal creation
- treasury action request creation
- payout-cycle publication step
- registry publication record creation
- transparency report publication reference

### Principle

The stronger the trust and economic sensitivity of the action, the stronger the idempotency requirement.

---

## Public API Idempotency Rules

Public APIs require explicit idempotency treatment for externally initiated write actions that may be retried by clients.

Public idempotency should generally apply to:

- create account where duplicate creation risk exists under external retry
- initiate checkout or payment-linked flows where platform-owned mutation occurs after provider confirmation
- create product job request
- generate export or report request
- link wallet where repeated confirmation can happen
- submit other externally initiated business actions that must not multiply under retry

### Public API principles

- callers should be able to supply an idempotency key where the action is retry-sensitive
- the platform should return a stable response for valid repeat attempts
- mismatched reuse of the same idempotency key with materially different business intent should be treated as error or conflict
- public idempotency behavior should be documented, not implied

This is especially important because public clients are unreliable by default from a retry and connectivity perspective.

---

## Internal Service API Idempotency Rules

Internal service APIs require equally strong, and often stronger, idempotency protection because retries are common in service-to-service coordination.

Internal idempotency should generally apply to:

- credits issuance calls
- subscription transition calls
- payout-cycle publication calls
- internal correction or compensation calls
- workflow step advancement where duplicate advancement would be harmful
- registry or reporting publication actions
- governance record creation under retriable orchestration

### Internal principles

- internal callers should not assume “trusted caller” removes the need for idempotency
- retry-capable infrastructure makes duplicate attempts likely
- internal operations should preserve idempotency references in audit lineage
- internal idempotency must be domain-owned, not left to the calling service alone

This is especially important in platform domains where duplicate mutation would create reconciliation problems long after the original retry event.

---

## Async Job Submission Idempotency

FUZE should treat async job submission as a distinct idempotency category.

Submitting an async job twice may create:
- duplicate compute cost
- duplicate user-visible artifacts
- duplicate billing
- confusing UX state
- duplicated notifications

Examples include:
- AI analysis requests
- report builds
- HerHelp generation jobs
- Botmad scan jobs
- export generation jobs

### Async principles

- the request-acceptance layer should identify whether the caller is repeating the same request intentionally or accidentally
- a repeat submission of the same job request should usually return the existing job reference rather than creating a new one
- if the caller wants a genuinely new run, the platform should require a new request identity
- job identity and business action identity should remain distinct where appropriate

This keeps async operations usable without making them duplicate-prone.

---

## Event Consumer Idempotency

FUZE event consumers must be idempotent because internal event delivery should generally be treated as at-least-once.

Event consumer protections should generally include:

- event ID tracking
- business-reference deduplication where needed
- safe ignore behavior for already-processed events
- replay-aware handling
- operator visibility into duplicate suppression

### Examples

- a `credits.issued` event should not produce duplicate downstream reporting or duplicate partner notifications
- a `payout.cycle.funded` event should not create duplicate payout-ledger entries
- a `subscription.renewed` event should not create duplicate entitlement refresh artifacts if the operation is already complete

### Principle

Every meaningful consumer should assume duplicate delivery is possible.

---

## Webhook Idempotency and Delivery Identity

External webhook delivery also requires idempotency discipline.

Webhooks may be retried due to:
- receiver timeout
- receiver error
- temporary network failure
- bounded retry policy

### Webhook principles

- every webhook delivery should include stable event identity
- webhook consumers should be expected to handle duplicate delivery
- FUZE should distinguish between retrying delivery of the same webhook event and generating a new event
- webhook delivery attempts should be trackable without confusing business event identity

This protects both FUZE and its external consumers from retry-induced confusion.

---

## Idempotency in Credits and Billing Domains

The credits and billing domains require the strongest idempotency discipline in the platform.

### High-risk operations include:
- paid credits issuance after verified payment
- reversal of issued credits
- refund-linked credit corrections
- subscription renewal transitions
- plan change applications
- entitlement adjustment triggered by commercial change
- support compensation issuance

### Domain principles

- provider transaction identity should be used where relevant
- credits mutation records should never be duplicated under equivalent retry
- the same renewal cycle should not create two renewals
- refund and reversal operations should be bound to explicit commercial references
- support/admin correction flows should still be idempotent when retried by operators or automation

Because Platform Credits are a core economic layer, idempotency failures here directly damage the integrity of the platform.

---

## Idempotency in Governance, Treasury, and Payout Domains

Governance-, treasury-, and payout-sensitive domains also require especially strong idempotency treatment.

### High-risk operations include:
- governance action proposal creation
- treasury action submission
- vault action request creation
- payout-cycle record creation
- payout-cycle publication
- payout-ledger publication
- transparency-report publication tied to a cycle or governance event
- registry entry publication or status transition

### Domain principles

- duplicates must not silently create multiple structurally significant records
- repeat requests should resolve to the same action record when they represent the same intended act
- advancement of state machines should protect against duplicate transitions
- audit lineage must preserve idempotency references because later review may depend on understanding retries versus new actions

This matters because duplicate governance or payout artifacts can create public confusion even if funds are not directly misapplied.

---

## Idempotency Outcome Semantics

FUZE should return and record clear semantics for idempotent outcomes.

At minimum, an idempotent write may result in one of the following meanings:

### Applied
The action was newly applied and mutated domain truth.

### Replayed / Previously Applied
The same action was already applied earlier; the platform is returning the previously established result.

### Conflicted
The supplied idempotency identity or domain reference is being reused for a materially different action or incompatible state.

### Rejected
The action is invalid independent of idempotency, such as authorization failure or policy denial.

### Principle

An idempotency-protected system should not force callers and operators to guess whether a repeat produced new mutation or not. The outcome semantics should be explicit.

---

## Versioning Scope in FUZE

FUZE should apply versioning discipline across multiple interface and artifact families, not only APIs.

At minimum, versioning scope includes:

- public APIs
- internal service APIs with meaningful multi-consumer use
- domain events
- public webhooks
- transparency reports
- public registry entries or schemas
- payout-ledger record formats
- async job result contracts
- selected product resource representations where external or cross-domain use justifies it

### Principle

Versioning applies wherever consumers need durable interpretation across time.

---

## Public API Versioning

Public APIs require the strongest versioning discipline.

### Public API principles

- public APIs should use explicit versioning
- breaking changes should be rare and deliberate
- additive evolution is preferred where practical
- deprecated versions should remain supported for defined windows where appropriate
- documentation must state active versions and deprecation status
- public SDK or client expectations should align to versioned contracts

### Why this matters

Public APIs are ecosystem promises. Once exposed, they affect integrators, partners, and future platform trust.

---

## Internal API Versioning

Internal APIs also require versioning discipline, although the exact mechanism may be more flexible.

### Internal principles

- high-dependency internal surfaces should evolve deliberately
- cross-service breaking changes should be coordinated
- additive changes are preferred when many consumers exist
- internal version transitions should preserve audit and operational clarity
- critical internal contracts in economic or governance-sensitive domains should not change casually even if they are not public

### Principle

Internal does not mean disposable. The more internal consumers a service has, the more version discipline it needs.

---

## Event and Webhook Versioning

Events and webhooks should carry explicit version identity.

### Event versioning principles

- each event contract should include an explicit version
- breaking payload changes require explicit evolution
- consumers should be able to distinguish payload schema by version
- event meaning should remain stable within a version
- replay and audit systems should preserve the original event version

### Webhook versioning principles

- public webhooks require stronger compatibility treatment than many internal events
- webhook consumers should not be forced into silent breakage
- major payload changes should introduce a new version or explicit contract evolution path
- documentation should explain current and legacy webhook contracts where relevant

This is especially important because event-driven systems degrade quickly when schema meaning changes implicitly.

---

## Report, Ledger, and Registry Versioning

FUZE should also apply versioning concepts to published artifacts that shape public trust.

### Transparency reports
Reports should carry identifiable versions or publication references.

### Payout ledger
Cycle records should preserve version or correction lineage when format or interpretation changes.

### Public registry
Registry entries and registry schemas should preserve historical meaning across updates, deprecations, or structural change.

### Principle

Public trust artifacts must remain historically interpretable. Silent format evolution or silent reinterpretation weakens long-term credibility.

---

## Versioning vs Correction

FUZE should distinguish between a new version and a correction.

### Version evolution
A new version changes the schema, contract, or supported structure.

### Correction
A correction fixes or supersedes content, interpretation, or a prior published artifact while preserving lineage.

### Examples

- a new webhook payload shape is a versioning event
- a corrected transparency report is a correction event
- a payout-ledger cycle record updated to reflect correction lineage is a correction, not necessarily a new schema version
- a major report format redesign may involve both correction and version change

### Principle

Versioning explains structural contract evolution.
Correction explains historical content repair or supersession.

Both matter, but they should not be blurred together.

---

## Backward Compatibility Philosophy

FUZE should follow a practical backward compatibility philosophy.

### Principles

- preserve compatibility for public consumers whenever reasonably possible
- prefer additive changes over breaking changes
- document deprecation and migration paths
- do not preserve harmful or incorrect behavior indefinitely merely for comfort
- keep the distinction between semantic stability and transport stability clear
- prioritize compatibility most strongly where external dependency and trust are highest

### Domain nuance

- public APIs and webhooks require the strongest backward compatibility
- internal APIs require coordinated compatibility
- reports and ledgers require historical readability
- domain events require consumer-aware change control
- trust-sensitive artifacts require especially clear correction lineage

---

## Deprecation Rules

FUZE should deprecate interfaces and artifact forms explicitly.

At minimum, deprecation should preserve:

- what is being deprecated
- why it is being deprecated
- when support or interpretation changes
- what replacement should be used
- whether the change is breaking or additive
- whether existing artifacts remain valid historically

### Principle

A platform should not force consumers to infer retirement from sudden disappearance.

---

## Auditability of Idempotency and Versioning

Idempotency and versioning behavior should remain auditable.

At minimum, the platform should preserve:

- idempotency key or reference used
- whether the action was newly applied or previously applied
- conflict outcome where applicable
- version of the API, event, webhook, report, or artifact involved
- correction lineage where applicable
- correlation to originating actor, workflow, payment, governance action, or payout cycle where relevant

### Why this matters

When something looks duplicated or misinterpreted, support and operators must be able to answer:
- was this a duplicate submission
- was it safely idempotent
- which version did the consumer receive
- did correction or supersession occur later

This makes idempotency and versioning part of supportability, not only engineering hygiene.

---

## Failure and Recovery Principles

FUZE should handle idempotency and versioning failure cases explicitly.

### Idempotency failure examples
- duplicate request arrives before prior result is fully persisted
- conflicting reuse of the same idempotency key
- downstream retries occur after partial side effects
- replayed events reach consumers after correction

### Recovery principles
- prefer explicit repair or compensation through owning domain
- avoid hidden manual mutation that bypasses lineage
- preserve evidence of duplicate attempt versus new attempt
- preserve version and correction references in recovery records

### Versioning failure examples
- consumer receives incompatible payload without explicit version awareness
- report or registry meaning changes without publication note
- webhook contract evolves without migration signaling

### Recovery principles
- publish correction or migration guidance
- preserve prior artifact lineage where trust-sensitive
- avoid silent reinterpretation of historical records

---

## Minimum Architectural Entities

At minimum, the FUZE idempotency and versioning architecture should recognize the following conceptual entities:

### Idempotency Entities
- `idempotency_key`
- `idempotency_scope`
- `business_action_type`
- `first_applied_at`
- `last_seen_at`
- `idempotency_status`
- `result_reference`
- `conflict_reference` where applicable

### Versioning Entities
- `version_reference`
- `contract_surface_type`
- `version_status`
- `introduced_at`
- `deprecated_at` where applicable
- `superseded_by` where applicable

### Correction and Compatibility Entities
- `correction_reference`
- `supersedes_reference`
- `compatibility_policy_reference`
- `migration_reference`

### Audit and Trace Entities
- `correlation_id`
- `actor_reference`
- `workflow_reference`
- `payment_reference`
- `governance_action_reference`
- `payout_cycle_reference`
- `audit_lineage_reference`

These are minimum conceptual entities. Detailed schema and implementation vary by domain and surface family.

---

## Open Items

The following areas are intentionally refined in downstream implementation and domain-specific contracts:

- exact idempotency header or request-field conventions
- exact TTL or retention policy for idempotency records by domain
- exact retry interaction rules for long-running job submissions
- exact API versioning format by public and internal surface
- exact event and webhook schema registry process
- exact compatibility and migration policy by report and registry family

These do not weaken the canonical idempotency and versioning architecture established here.

---

## Closing Summary

The FUZE idempotency and versioning architecture protects the platform against duplicate business mutation and uncontrolled contract drift. Idempotency ensures that repeated submissions, retries, replays, and duplicate deliveries do not create duplicate economic, governance, payout, or product-side meaning when the intended action is singular. Versioning ensures that APIs, events, webhooks, reports, registries, and payout-ledger artifacts evolve in a deliberate and interpretable way over time. Together, these disciplines strengthen correctness, auditability, supportability, and long-term platform trust across the entire FUZE ecosystem.
