# FUZE AI Usage Metering Specification

## Document Metadata
- **Document Name:** `AI_USAGE_METERING_SPEC.md`
- **Document Type:** Canonical refined system specification
- **Status:** Active refined system spec
- **Version:** 2.0.0
- **Effective Date:** 2026-04-22
- **Last Updated:** 2026-04-22
- **Reviewed On:** 2026-04-22
- **Document Owner:** FUZE AI Platform Commerce Architecture / AI Usage Metering Domain
- **Approval Authority:** FUZE Platform Architecture and Governance Authority
- **Review Cadence:** Quarterly or upon material change to AI orchestration semantics, routing/context policy, entitlement posture, pricing policy, subscriptions and usage billing posture, Platform Credits semantics, credit-ledger settlement posture, async execution posture, audit requirements, or security/risk controls
- **Governing Layer:** Platform core / shared AI commercial infrastructure / AI usage metering
- **Parent Registry:** `REFINED_SYSTEM_SPEC_INDEX.md`
- **Primary Audience:** Platform architecture, AI platform engineering, backend engineering, commerce and billing engineering, credits and ledger engineering, product engineering, finance operations, support operations, security engineering, audit/compliance, API design, data engineering, platform operations, implementation-contract authors
- **Primary Purpose:** Define the canonical FUZE domain that measures, classifies, attributes, settles, corrects, explains, and reports AI consumption across products and scopes without collapsing metering truth into orchestration truth, routing/context truth, billing truth, entitlement truth, Platform Credits semantic truth, credit-ledger truth, or product-local analytics
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
  - `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
  - `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md`
  - `IDENTITY_AND_ACCOUNT_SPEC.md`
  - `WORKSPACE_AND_ORGANIZATION_SPEC.md`
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
  - `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
  - `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
  - `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
  - `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
  - `PLATFORM_CREDITS_SPEC.md`
  - `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
  - `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
  - `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
  - `AI_ORCHESTRATION_SPEC.md`
  - `MODEL_ROUTING_AND_CONTEXT_SPEC.md`
  - `WORKFLOW_AND_AUTOMATION_SPEC.md`
  - `JOB_QUEUE_AND_WORKER_SPEC.md`
  - `API_ARCHITECTURE_SPEC.md`
  - `PUBLIC_API_SPEC.md`
  - `INTERNAL_SERVICE_API_SPEC.md`
  - `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
  - `IDEMPOTENCY_AND_VERSIONING_SPEC.md`
  - `MIGRATION_AND_BACKWARD_COMPATIBILITY_SPEC.md`
  - `AUDIT_LOG_AND_ACTIVITY_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - `MONITORING_ALERTING_AND_INCIDENT_RESPONSE_SPEC.md`
  - `SECRETS_CONFIG_AND_ENVIRONMENT_SPEC.md`
- **Primary Downstream Dependents:**
  - `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
  - `PLATFORM_CREDITS_SPEC.md`
  - `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
  - `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
  - `WORKFLOW_AND_AUTOMATION_SPEC.md`
  - `JOB_QUEUE_AND_WORKER_SPEC.md`
  - `AI_USAGE_METERING_API_SPEC.md`
  - product-specific monetization and quota specifications
  - billing, support, reporting, finance-reconciliation, and control-plane remediation workflows
- **Supersedes:** Earlier or weaker FUZE metering writeups that treated provider usage as sufficient commercial truth, blurred metering into orchestration or billing, or failed to preserve correction lineage and settlement boundaries
- **Superseded By:** Not yet known
- **Related Decision Records:** Not yet known
- **Canonical Status Note:** This document is the canonical FUZE specification for AI usage metering semantics. Downstream APIs, workers, workflows, products, dashboards, support tooling, billing services, credits consumers, reporting layers, and finance/reconciliation views MUST NOT reinterpret product-local counters, provider token reports, raw model invoices, dashboard summaries, or cached quota displays as substitutes for the canonical metering model defined here.
- **Implementation Status:** Normative architecture baseline; downstream API, service, runtime, event, audit, and control-plane contracts MUST conform
- **Approval Status:** Drafted for refined-system inclusion; formal approval record not yet attached
- **Change Summary:**
  - elevated AI usage metering into a stricter shared platform truth domain with explicit separation from orchestration, routing/context, billing, entitlement, and ledger truth
  - normalized usage attribution, settlement linkage, reservation/release posture, correction lineage, dispute handling, and reporting-class boundaries
  - hardened async, workflow, queue, idempotency, audit, and operator-remediation guardrails for high-cost and high-volume AI execution
  - clarified non-canonical patterns, contested-ownership rules, read-model limits, and downstream implementation-contract requirements

## Title
FUZE AI Usage Metering Specification

## Purpose
This specification defines the canonical AI usage metering model of the FUZE platform.

Its purpose is to make explicit:
- how FUZE identifies meaningful AI usage events across products and scopes
- how those events become normalized metering records with durable attribution and policy lineage
- how metering differs from orchestration, routing/context, workflow, queue execution, billing, entitlement, Platform Credits, and credit-ledger settlement
- how included usage, premium usage, overage posture, reservation-aware usage, internal operational usage, and corrected usage must behave
- how downstream APIs, events, storage, reporting, support, finance, and control-plane remediation must preserve canonical metering truth

FUZE is a multi-product AI platform. AI consumption therefore cannot remain a product-local estimate, a provider-side invoice abstraction, or a dashboard counter. It must be governed as one shared platform accounting and attribution layer.

## Scope
This specification governs:
- canonical AI usage metering semantics across FUZE products and shared services
- identification, normalization, attribution, classification, and lifecycle of AI usage records
- relationship of metering to included usage, premium usage, overage posture, credits-backed settlement, and subscription-linked usage treatment
- reservation-aware and deferred-settlement behavior for long-running or high-cost AI work
- correction, release, reversal, dispute, and non-billable failure behavior
- integration with orchestration, routing/context, workflows, jobs, entitlement, billing, audit, observability, and control-plane systems
- implementation-contract guardrails for public, internal, admin, and event-driven interfaces that create, mutate, explain, or consume metering outcomes

## Out of Scope
This specification does not define in full depth:
- the AI run lifecycle, step semantics, or output publication lifecycle
- route-selection matrices, context-pack schemas, provider selection, or retrieval topology
- exact pricing tables, product-specific quotas, or customer-facing package design
- exact Platform Credits semantic class taxonomy or final credits-ledger append mechanics
- full subscription lifecycle management or invoice/receipt document semantics
- exact queue technology, retry tables, worker deployment topology, or alert thresholds
- every product’s AI UX or local display language

Those concerns are refined in adjacent specifications and MUST remain compatible with this document.

## Design Goals
1. Provide one shared AI usage accounting layer across all FUZE products and shared services.
2. Preserve explicit separation between AI execution truth and AI commercial measurement truth.
3. Support included, premium, overage, restricted, internal, reserved, released, corrected, and disputed AI usage classes.
4. Preserve scope-correct attribution across account, workspace, product, and workflow contexts.
5. Support low-latency, streaming, hybrid, and long-running AI work in one coherent metering model.
6. Make AI usage auditable, explainable, reconcilable, and operator-remediable.
7. Support cost discipline and pricing flexibility without letting products invent separate AI commerce systems.
8. Ensure retries, replays, queue duplication, and provider ambiguity do not create duplicate economic effects.
9. Enable downstream billing, credits, entitlement, support, and reporting systems without semantic drift.
10. Remain future-safe for new products, new AI capabilities, new providers, and new monetization shapes.

## Non-Goals
This specification is not intended to:
- treat raw provider token counts or raw provider invoices as the canonical business meter
- require every AI action to be monetized identically
- let products or experiments create hidden AI charging systems outside platform metering
- make metering a substitute for authorization or entitlement checks
- make metering truth equivalent to billing truth, credits truth, or ledger truth
- silently erase failures, releases, or corrections that materially affect trust or economics
- replace downstream API specs, schemas, runbooks, or operational playbooks

## Core Principles
### 1. Shared Metering Principle
AI usage metering is a shared FUZE platform capability. Products may originate chargeable or non-chargeable AI actions, but they MUST NOT define separate metering truth.

### 2. Metering-Is-Not-Orchestration Principle
Orchestration governs AI execution lifecycle. Metering governs normalized economic and reporting measurement of that execution. The two domains are related but distinct.

### 3. Metering-Is-Not-Routing Principle
Routing/context decides route and context admissibility. Metering consumes route and context lineage where relevant, but MUST NOT redefine routing or context semantics.

### 4. Metering-Is-Not-Billing Principle
Metering records and classifies usage. Billing decides how usage participates in subscriptions, included usage, overage, or related commercial obligations. Metering informs billing but does not replace billing truth.

### 5. Metering-Is-Not-Credits Principle
Metering may cause or justify credits-backed settlement, reservation, release, or reversal linkage, but metering is not Platform Credits semantic truth and is not credit-ledger truth.

### 6. Business-Relevant Unit Principle
Canonical metering units MUST reflect business-relevant AI consumption rather than only provider-native implementation details.

### 7. Scope-Correct Attribution Principle
Every metered AI event MUST resolve to an explicit commercial owner scope and product/workflow attribution context. Wrong-scope metering is forbidden.

### 8. Reservation-and-Release Principle
High-cost, long-running, or ambiguity-prone AI work MAY require provisional reservation or pending treatment. Final economic effect MUST remain explicit, releasable, and auditable.

### 9. Correction-Lineage Principle
Correction, reversal, release, compensation, and dispute outcomes MUST preserve lineage to the original metering event. Silent mutation or destructive overwrite of commercially meaningful history is forbidden.

### 10. Idempotent Economic Effect Principle
The same business AI action MUST NOT produce duplicate chargeable or included-usage effects because of retries, queue redelivery, operator replay, workflow duplication, or provider ambiguity.

## Canonical Definitions
### AI Usage Event
A business-relevant occurrence of AI execution or AI execution attempt that is eligible to become a metering record.

### AI Usage Record
The canonical structured record representing one normalized AI usage event and its attribution, classification, lifecycle, and settlement linkage.

### Metering Unit
The normalized business unit by which AI activity is measured for FUZE commercial, audit, and reporting purposes.

### Commercial Class
The monetization treatment of an AI usage event, such as included, premium, overage, restricted, internal operational, non-billable failure, or corrected.

### Usage Scope
The commercial owner context of an AI usage event, typically an account or workspace.

### Usage Status
The lifecycle state of a metering record, such as created, pending_settlement, included, charged, released, failed_non_billable, corrected, reversed, or disputed.

### Settlement Link
The controlled reference that ties a metering record to included quota consumption, credits-backed settlement, billing outcome, or other approved commercial result.

### Reservation
A provisional hold or usage intent established before final AI settlement for high-cost, long-running, or ambiguous work.

### Correction Record
An explicit record describing why and how a metering outcome was changed after original recognition.

### Internal Operational Usage
AI usage generated by FUZE internal platform or operator functions for cost visibility or operations rather than ordinary customer-billable treatment.

### Metering Policy
The approved policy bundle defining metering units, classes, thresholds, settlement posture, reservation requirements, and correction rules for a defined AI capability or workload class.

## Truth Class Taxonomy
Downstream implementations MUST preserve the following truth classes.

### 1. Metering Semantic Truth
The canonical meaning of AI usage entities, statuses, classes, and lineage owned by this domain.

### 2. Metering Policy Truth
Metering policy versions, unit definitions, threshold classes, reservation rules, and correction rules.

### 3. Runtime Execution Truth
Current run state, provider interaction state, queue state, retries, and transient execution posture owned by orchestration and execution substrates.

### 4. Ledger / Storage Truth
Durable AI usage records, settlement links, correction records, and metering policy references.

### 5. Billing Truth
Recurring billing obligations, included-usage treatment, overage treatment, and billing-scope commercial state. Billing may consume metering outcomes but remains separately governed.

### 6. Platform Credits Semantic Truth
The canonical meaning of credits as an internal spend unit, class semantics, and usage rules.

### 7. Credit Ledger and Settlement Truth
Authoritative append-only mutation lineage, reservation state, balance derivation, and settlement-grade economic recording.

### 8. Entitlement Truth
Eligibility for premium or restricted AI capability classes. Metering may reference entitlement context but does not own it.

### 9. Projection / Reporting Truth
Dashboards, quota summaries, exports, support views, finance reports, and analytics generated from canonical metering records.

### 10. Presentation Truth
User-visible usage counters, plan warnings, or AI usage explanations shown in products.

These truth classes MUST remain distinct. Metering truth does not absorb orchestration truth, routing/context truth, workflow truth, billing truth, or ledger truth.

## Architectural Position in the Spec Hierarchy
This document sits below:
- `REFINED_SYSTEM_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
- `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md`

and above or alongside:
- `AI_ORCHESTRATION_SPEC.md`
- `MODEL_ROUTING_AND_CONTEXT_SPEC.md`
- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `JOB_QUEUE_AND_WORKER_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
- `API_ARCHITECTURE_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `IDEMPOTENCY_AND_VERSIONING_SPEC.md`
- product-specific AI monetization and quota specifications
- support, reconciliation, reporting, and finance-control contracts

This document governs AI usage metering semantics. It does not replace the adjacent documents listed above.

## System Boundaries
AI usage metering sits primarily in the FUZE **application plane** and commercial coordination layer. It interacts explicitly with the **execution plane**, **integration plane**, **control plane**, and bounded **reporting plane**.

It MUST be interpreted as follows:
- the **experience / edge layer** may display counters, warnings, or summaries but does not own metering truth
- the **application plane** owns metering record creation, policy binding, attribution normalization, usage classification, and metering-state meaning
- the **execution plane** supplies run attempts, retries, timing, and completion signals but does not own metering semantics
- the **integration plane** supplies provider usage details and external signals but does not define FUZE business units
- the **control plane** may correct, suppress, dispute, or resolve metering under approved policy but does not become the ordinary owner of metering truth
- the **reporting plane** may project quota, usage, anomaly, and revenue-support views but is never authoritative for canonical metering state

## Adjacent Boundaries
### AI Orchestration
Owns AI execution request acceptance, run lifecycle, step lineage, tool use, and bounded output publication. Metering consumes execution lineage and must not redefine run meaning.

### Model Routing and Context
Owns route resolution, model-family/provider-lane selection, and context-governance semantics. Metering may record route or class references for cost or billing interpretation but MUST NOT redefine routing or context policies.

### Workflow and Automation
Owns multi-step coordination, waits, approvals, compensation sequencing, and workflow state. Metering may participate in workflows but is not a workflow engine.

### Job Queue and Worker
Owns deferred execution mechanics, claims, retries, timeouts, dead-letter handling, and worker isolation. Metering requires job-safe idempotency and lineage but does not own queue mechanics.

### Subscriptions and Usage Billing
Owns recurring commercial commitments, included-usage posture, billing-scope state, and overage policy consumption. Metering is the normalized usage input, not the billing owner.

### Platform Credits
Owns credits semantic meaning and approved spend-unit posture. Metering may justify credits-backed settlement but does not define what a credit is.

### Credit Ledger and Settlement
Owns authoritative credits mutation lineage and reservation/final settlement recording. Metering may link to ledger outcomes but is not itself the ledger.

### Entitlement and Capability Gating
Owns actor/scope eligibility for premium or restricted AI capability classes. Metering may reflect the entitlement context that applied but does not authorize execution.

### Audit and Security
Own audit immutability, enterprise controls, and cross-platform risk posture. Metering must emit sufficient lineage and respect applicable restrictions.

## Conflict Resolution Rules
When materials, implementations, or interpretations conflict, the following rules apply:
1. the active refined registry and higher constitutional materials win over narrower documents
2. `PLATFORM_ARCHITECTURE_SPEC.md` wins on plane separation and cross-plane posture
3. this document wins on AI usage metering semantics, usage classes, metering lifecycle meaning, attribution requirements, and correction-lineage rules
4. `AI_ORCHESTRATION_SPEC.md` wins on run lifecycle and execution semantics
5. `MODEL_ROUTING_AND_CONTEXT_SPEC.md` wins on route selection and context-governance specifics
6. `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md` wins on recurring billing state, included-usage commercial interpretation, and overage billing outcomes
7. `PLATFORM_CREDITS_SPEC.md` wins on credits semantic meaning
8. `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md` wins on ledger mutation truth, reservation append semantics, and balance derivation
9. `WORKFLOW_AND_AUTOMATION_SPEC.md` wins on workflow-state meaning and compensation choreography
10. `JOB_QUEUE_AND_WORKER_SPEC.md` wins on queue mechanics, retry infrastructure, dead-letter behavior, and worker execution substrate
11. when product-local counters or analytics disagree with canonical metering records, canonical metering records win
12. raw provider usage never wins by itself over canonical FUZE metering meaning

## Default Decision Rules
1. If a meaningful AI action occurred but final economic effect is not yet determined, create a metering record in a non-final state rather than omitting the record.
2. If execution ambiguity exists for a high-cost or long-running action, prefer `pending_settlement`, reservation, or dispute posture over premature charging.
3. If scope ownership is ambiguous, fail closed and require re-resolution rather than assigning usage to the nearest convenient scope.
4. If retries or replay produce multiple candidate usage signals for one business action, treat them as one canonical metering lineage unless policy explicitly defines separate billable meaning.
5. If provider-side usage and FUZE business-unit interpretation disagree, preserve both as separate truths and use FUZE metering policy as canonical commercial meaning.
6. If a product wants a new metering unit or commercial class with shared commercial impact, platform metering policy approval is required.
7. If corrected usage affects downstream credits or billing state, correction lineage MUST remain explicit even when downstream repair is performed.
8. If dashboard summaries, exports, or support tools disagree with canonical metering records, derived views must be rebuilt rather than rewriting metering truth to match the view.

## Roles / Actors / Entities
- **Account** — canonical identity anchor for personal-scope metering when relevant
- **Workspace** — canonical collaborative commercial scope for workspace-billed usage
- **Product Domain** — defines feature meaning and origin context but not shared metering truth
- **AI Orchestration Domain** — provides run, step, output, and outcome lineage used by metering
- **Routing / Context Domain** — provides route and context-policy lineage where relevant to usage classification
- **Billing Domain** — consumes metering for included usage and overage billing outcomes
- **Credits / Ledger Domains** — perform credits-backed reservation, spend, release, and reversal effects
- **Workflow Domain** — coordinates multi-step usage-related flows such as approval, compensation, or recovery
- **Queue / Worker Infrastructure** — performs async execution and retries without owning metering meaning
- **Support / Control-Plane Operator** — may correct, dispute, release, or annotate metering under bounded policy
- **Finance / Reconciliation Actor** — may review or reconcile metering-linked commercial inconsistencies without mutating metering semantics ad hoc

## Ownership Model
The AI Usage Metering Domain owns:
- AI usage record semantics
- metering unit definitions and commercial class taxonomy
- usage attribution requirements
- metering lifecycle states and state transitions
- settlement-link semantics
- correction, release, reversal, and dispute lineage within the metering domain
- metering-specific audit and reporting requirements

The domain does **not** own:
- AI run acceptance or execution progression
- model/provider selection
- entitlement authorization decisions
- recurring subscription state
- final credits ledger mutation truth
- invoice/document truth
- product-local UX counters as canonical truth

## Authority / Decision Model
- Platform architecture and governance approve material changes to shared metering semantics.
- The AI Usage Metering Domain approves metering classes, unit definitions, lifecycle rules, and correction semantics.
- Product teams may propose product-specific feature attribution or meter subclasses but MUST NOT unilaterally change shared semantics.
- Billing, credits, and entitlement domains retain authority over their adjacent truths and must not be bypassed by metering shortcuts.
- Support or admin operators may execute bounded corrections only through approved control-plane pathways with reason codes and audit lineage.

## State Model
At minimum, canonical metering records MUST support the following statuses:
- `created`
- `pending_settlement`
- `included`
- `charged`
- `released`
- `failed_non_billable`
- `corrected`
- `reversed`
- `disputed`

### State Interpretation Rules
- `created` means the platform recognized a meaningful AI usage event and persisted it.
- `pending_settlement` means final included/charged/released treatment is not yet resolved.
- `included` means usage consumed included or bundled capacity under approved policy.
- `charged` means the usage event produced a finalized paid usage outcome.
- `released` means provisional or reserved commercial treatment was removed under policy.
- `failed_non_billable` means the AI action failed in a policy-defined way that must not create billable usage.
- `corrected` means the original treatment was changed under a controlled correction path.
- `reversed` means a previously settled usage effect was formally reversed under policy.
- `disputed` means correctness, fraud, or other trust-sensitive concerns require review.

### State Mutation Guardrails
- Final states MUST NOT be overwritten destructively.
- Correction and reversal MUST preserve prior-state lineage.
- `disputed` does not itself settle economic truth; it suspends ordinary trust in the current result pending resolution.
- State transition rights MUST be permission-bounded and reason-coded where operator action is involved.

## Lifecycle / Workflow Model
The canonical AI usage metering lifecycle is:
1. usage-relevant AI intent or execution signal is recognized
2. usage event is normalized into a metering candidate
3. scope, product, feature, route class, and policy references are bound
4. a metering record is created
5. if required, reservation or pending-settlement posture is established
6. execution outcome and policy evaluation resolve whether the usage is included, charged, released, failed_non_billable, or disputed
7. downstream billing and/or credits-linked settlement may consume the metering outcome
8. correction, reversal, or support remediation may later append additional lineage

### Workflow Coupling Rules
- Multi-step AI flows may coordinate through the workflow domain, but workflow state must not replace metering state.
- Reservation, execution, validation, and final settlement may span async jobs, but one canonical metering lineage must remain reconstructible.
- If a workflow is canceled after metering record creation, the final metering state MUST still be explicit.

## Invariants
1. Every meaningful metered AI event MUST have an explicit owner scope.
2. Every meaningful metered AI event MUST preserve product attribution.
3. The same business action MUST NOT cause duplicate chargeable or included-usage effects.
4. Metering records MUST preserve distinction between business AI usage and raw provider usage.
5. Metering outcomes that affect money, credits, or entitlements MUST be reproducible from durable lineage.
6. Released, corrected, reversed, and disputed outcomes MUST remain visible in history.
7. Product teams MUST NOT maintain shadow metering truth with shared commercial meaning.
8. Derived reports and UI counters MUST be subordinate to canonical metering truth.
9. High-cost or long-running AI work MUST support explicit reservation, pending-settlement, or equivalent ambiguity-safe handling where policy requires.
10. Control-plane remediation MUST be bounded, audited, and policy-constrained.

## Functional Rules
### Metering Record Creation
- A metering record MUST be created for every AI event that is commercially meaningful, policy-sensitive, or materially relevant for audit/reporting, even if final billing effect is not yet known.
- The platform MAY suppress trivial purely internal signals that have no economic, audit, or operational value, but such suppression rules MUST be policy-defined.

### Metering Units
- Metering units MUST be business-relevant and implementation-safe.
- FUZE MAY use action-based units, tiered action units, job-based units, included-quota consumption units, or internal non-billable units.
- Raw provider token counts MAY be retained as auxiliary evidence but MUST NOT be treated as sufficient canonical business units by default.

### Commercial Classes
At minimum, metering policy MUST support:
- included usage
- premium usage
- overage usage
- restricted usage
- internal operational usage
- non-billable failure or released usage
- corrected usage

### Scope and Product Attribution
- Each record MUST include owner scope type and owner scope ID.
- Account-scoped and workspace-scoped usage MUST remain distinguishable.
- Product attribution MUST include product reference and, where meaningful, feature or workflow class.
- Products MUST NOT silently meter workspace activity as personal activity or vice versa.

### Settlement Linkage
- Metering MAY reference included-quota consumption, credits reservation, credits spend, usage-billing outcome, or other approved settlement posture.
- Settlement linkage MUST remain explicit rather than inferred from dashboard counters or invoice rows.
- Absence of settlement linkage does not erase the metering event.

### Internal Operational Usage
- Internal operational AI usage SHOULD be measurable under a distinct class.
- Internal usage MUST NOT accidentally consume customer credits or customer included quota.
- Reporting for internal usage MUST remain distinguishable from customer usage.

## Permission / Access Considerations
- Metering creation pathways MUST be backend-governed.
- Product UIs may initiate AI actions or read bounded summaries, but they MUST NOT author canonical metering truth directly.
- Access to raw metering records SHOULD be permission-bounded according to scope sensitivity, finance sensitivity, and support role.
- Operator correction tools MUST require elevated permission, scoped authority, and reason-coded intent.

## Entitlement Considerations
- Entitlement evaluation remains upstream or adjacent to execution and premium capability access.
- Metering records SHOULD retain the entitlement posture or capability class that applied at execution/settlement time where material.
- A requested AI action is not automatically chargeable merely because it was requested; policy and entitlement conditions still apply.
- If entitlement changes while an async action is in flight, revalidation MAY be required at settlement time according to policy.

## API / Contract Implications
Downstream API specifications MUST preserve the following:
- canonical creation and mutation of metering records occur through backend-governed interfaces
- read APIs may expose summaries, record details, correction lineage, and settlement status within permission bounds
- public-facing APIs MUST NOT expose internal ledger semantics or sensitive provider details unless explicitly approved
- admin/control-plane APIs for correction, release, dispute, or replay-linked resolution MUST require reason codes, actor identity, and policy references
- mutation APIs MUST be idempotency-aware and capable of correlating business action lineage
- downstream APIs MUST preserve scope, product, route class, execution reference, and policy version references where relevant

## Event / Async Implications
- Metering events MUST be publishable as domain events when downstream billing, credits, support, reporting, or anomaly-detection systems depend on them.
- Event families SHOULD distinguish record created, pending settlement, included, charged, released, corrected, reversed, and disputed transitions.
- Async continuation MUST preserve correlation IDs linking workflow instance, job, run, metering record, and settlement actions.
- Replayed or duplicate upstream signals MUST NOT create duplicate final metering effects.

## Data Model / Storage Implications
At minimum, the metering domain MUST support semantic representation of:

### AI Usage Record
- `ai_usage_record_id`
- `execution_reference` or `ai_run_id`
- `product_reference`
- `feature_reference` or `workflow_class`
- `owner_scope_type`
- `owner_scope_id`
- `account_id` where relevant
- `workspace_id` where relevant
- `usage_meter_type`
- `commercial_class`
- `status`
- `quantity`
- `policy_version_reference`
- `created_at`
- `settled_at` where relevant
- `correlation_id`
- `idempotency_context`

### Settlement Link Record
- `settlement_link_id`
- `ai_usage_record_id`
- `settlement_type`
- `billing_reference` where relevant
- `credits_reservation_reference` where relevant
- `credits_ledger_reference` where relevant
- `included_quota_reference` where relevant
- `settlement_outcome`
- `linked_at`

### Correction / Dispute Record
- `correction_or_dispute_id`
- `ai_usage_record_id`
- `action_type`
- `reason_code`
- `actor_reference` or `system_reference`
- `created_at`
- `prior_status`
- `resulting_status`
- `related_compensation_reference` where relevant
- `resolution_reference` where relevant

Detailed schemas are refined downstream, but these semantic fields MUST remain preservable.

## Read Model / Projection / Reporting Rules
- Usage dashboards, plan summaries, quota counters, finance reports, and support views are derived read models.
- Derived views MUST identify the canonical metering source and refresh lineage where meaningful.
- Read models MAY aggregate by scope, product, feature class, route class, or commercial class.
- Read models MUST NOT invent chargeable outcomes that are not present in canonical metering state.
- Historical reports MUST preserve corrected/released/reversed visibility rather than flattening history into a single net number without audit support.
- Product UI counters MAY lag briefly, but canonical metering records prevail over cached or delayed views.

## Security / Risk / Abuse Controls
- High-cost or abuse-sensitive AI usage classes SHOULD support anomaly detection, policy throttles, or review posture.
- Metering creation and mutation interfaces MUST reject unauthorized direct client-side authorship.
- Route/class/provider metadata exposed outside trusted services MUST be minimized according to security policy.
- Suspicious bursts, abuse loops, or replay storms MUST be observable and may transition affected usage into restriction or dispute posture under approved policy.
- Internal operational usage and support/operator usage SHOULD remain separable for abuse monitoring and least-privilege review.

## Boundary Violation Detection / Non-Canonical Patterns
The following patterns are non-canonical and forbidden unless explicitly approved as bounded exceptions:
- product-local AI charging counters acting as shared commercial truth
- raw provider tokens or invoice line items treated as the only canonical usage record
- direct ledger writes used to imply metering without a canonical metering record
- destructive overwrite of incorrect metering history instead of explicit correction lineage
- dashboard summaries, export snapshots, or cached quota views back-written into canonical metering truth
- frontend-generated canonical metering mutations
- replay or retry pathways that can create duplicate chargeable effects without domain-level idempotency guards
- hidden operator adjustments without reason code, approval basis where applicable, and audit trail

## Audit / Traceability Requirements
At minimum, audit visibility MUST exist for:
- premium or high-cost AI usage recognition
- metering records entering charged, released, corrected, reversed, or disputed states
- support/admin metering overrides or corrections
- workspace-billed AI usage in trust-sensitive contexts
- reservation release after timeout, cancellation, or ambiguous completion
- repeated metering anomalies or suspected duplicate economic effects
- material metering policy changes

Audit records SHOULD preserve:
- actor or system origin
- scope and product context
- execution reference
- policy version
- reason code where manual action occurred
- correlation ID / trace ID linking workflow, job, run, metering, and settlement events

## Failure Handling / Edge Cases
### Duplicate upstream signals
The same business action may surface multiple orchestration, workflow, queue, or provider signals. The metering domain MUST consolidate them through idempotency context and lineage rather than double-charge.

### Task completed technically but failed validation
Technical completion alone MUST NOT force final charging. Metering policy MUST explicitly determine whether the result is included, charged, released, or failed_non_billable.

### In-flight entitlement change
If premium or restricted AI capability access changes before settlement, the platform MAY require revalidation. Result handling MUST remain deterministic and auditable.

### Worker crash after provisional reservation
Reservation release, retry, or dispute handling MUST be explicit. Ambiguous abandoned reservations are forbidden.

### Provider success with persistence failure
Recoverable partial-completion state MUST be preserved. The platform MUST not silently drop economically meaningful usage.

### Operator replay without root-cause resolution
Replay MUST preserve lineage and MUST NOT create duplicate economic effects. Repeated failure loops SHOULD surface for review.

### Dashboard or export mismatch
Derived reporting mismatch MUST trigger rebuild, reconciliation, or correction workflow rather than direct ad hoc mutation of canonical metering state.

### Support goodwill compensation
Goodwill compensation is a separate corrective or compensating event. It MUST NOT silently erase the original metering lineage.

## Operational Considerations
- Metering services SHOULD expose health indicators for ingestion latency, pending-settlement backlog, correction backlog, dispute backlog, and idempotency-conflict rate.
- High-cost or premium AI classes SHOULD be visible in monitoring and anomaly tooling.
- Async-heavy metering pipelines SHOULD preserve clear backlog separation from interactive request paths where justified.
- Recovery tools SHOULD support replay, reconciliation, and correction with bounded blast radius and strong audit lineage.
- Dead-letter or quarantine handling for ambiguous economic events SHOULD be available through adjacent workflow/queue systems.

## Migration / Compatibility / Supersession Considerations
- Existing product-local counters or ad hoc AI charging logic MUST be migrated into canonical metering records before being relied upon for shared billing or reporting.
- Historical provider-side usage data MAY be backfilled into canonical metering only through explicit normalization rules and provenance markers.
- New meter types, classes, or settlement postures MUST be versioned and introduced compatibly.
- Historical records MUST retain their original policy version or supersession lineage so later pricing or policy changes do not silently rewrite history.
- Supersession of older metering behavior MUST preserve explainability for finance, support, and audit use cases.

## Implementation-Contract Guardrails
Downstream implementations MUST preserve the following:
- canonical metering truth is backend-owned
- business action lineage and idempotency context are first-class, not optional metadata
- high-cost or long-running AI flows support pending/reservation-safe handling where policy requires
- correction, release, reversal, and dispute remain append-safe and lineage-preserving
- metering records are separable from billing records, credits semantics, and ledger entries
- queues, workers, and workflows call metering through explicit contracts rather than informal shared-state mutation
- degraded runtime conditions do not justify hidden charge decisions or silent data loss
- operator/admin override paths remain permission-bounded, audited, and reason-coded

## Downstream Execution Staging
1. **Semantic baseline stage** — finalize metering units, class taxonomy, scope attribution rules, and lifecycle semantics.
2. **API and event stage** — define public/internal/admin contracts and event families that preserve canonical lineage.
3. **Billing and settlement integration stage** — bind metering outcomes into included usage, overage, credits reservation, and credits-ledger settlement workflows.
4. **Workflow and queue stage** — align async reservation, retry, timeout, release, dead-letter, and recovery behavior.
5. **Reporting and control-plane stage** — implement bounded read models, support tooling, anomaly detection, and finance reconciliation views.

## Required Downstream Specs / Contract Layers
- `AI_USAGE_METERING_API_SPEC.md`
- product-specific AI quota and monetization specifications
- billing-rating and included-usage contract specifications
- credits-reservation and settlement integration contracts
- metering event family and webhook/internal-event contracts
- support/control-plane correction and dispute handling contracts
- reporting/reconciliation/export contract specifications

## Canonical Examples / Anti-Examples
### Canonical Examples
- A premium async report-generation action creates a metering record, places provisional reservation, runs through orchestration, and later resolves to `charged` with explicit settlement linkage.
- A low-cost included summary action creates a metering record that resolves to `included`, preserving product and scope attribution even though no direct credits deduction occurs.
- A failed AI action that reserved value transitions to `released` with explicit release lineage rather than disappearing.
- A support correction for a wrongly scoped AI charge appends correction lineage and, if necessary, triggers separate downstream billing or credits repair.

### Anti-Examples
- A product dashboard counter is treated as the canonical record of billable AI usage.
- Raw provider token counts are posted directly into billing with no canonical FUZE metering record.
- A worker retries an AI settlement step and creates duplicate chargeable records for one business action.
- A support agent edits a usage count in place with no correction record or reason code.
- A cached quota badge is copied back into metering storage to “fix” a reporting mismatch.

## Dependencies / Cross-Spec Links
This document depends materially on:
- registry and constitutional interpretation from `REFINED_SYSTEM_SPEC_INDEX.md`
- platform plane and ownership boundaries from `PLATFORM_ARCHITECTURE_SPEC.md`
- AI execution boundaries from `AI_ORCHESTRATION_SPEC.md`
- route/context governance from `MODEL_ROUTING_AND_CONTEXT_SPEC.md`
- recurring and usage billing semantics from `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- pricing policy posture from `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
- workflow/queue async recovery rules from `WORKFLOW_AND_AUTOMATION_SPEC.md` and `JOB_QUEUE_AND_WORKER_SPEC.md`
- audit and security posture from `AUDIT_LOG_AND_ACTIVITY_SPEC.md` and `SECURITY_AND_RISK_CONTROL_SPEC.md`

## Explicitly Deferred Items
The following are intentionally deferred to downstream or adjacent specifications:
- exact meter values and quotas per product
- exact provider-cost normalization algorithms
- exact queue technology and retry tables
- exact UI presentation language for usage counters and warnings
- exact anomaly-scoring models and abuse thresholds
- exact legal/tax treatment of specific monetization outcomes
- exact database schemas, indexes, partitioning, and retention policy details

## Final Normative Summary
FUZE AI usage metering is the canonical shared platform domain for normalized AI consumption truth. It exists to convert business-relevant AI activity into durable, scope-correct, product-attributed, policy-bound usage records that can be included, charged, released, corrected, reversed, disputed, explained, and reported without collapsing into orchestration truth, routing/context truth, billing truth, entitlement truth, Platform Credits semantic truth, or credit-ledger truth. All downstream implementations MUST preserve this separation, preserve explicit lineage, preserve idempotent economic effect, and reject product-local or provider-local shortcuts that would weaken commercial correctness, auditability, or operational safety.

## Quality Gate Checklist
- [x] Canonical owner is explicit for every material truth family.
- [x] Mutation boundaries are explicit.
- [x] Adjacent boundaries are explicit.
- [x] Truth classes are explicit.
- [x] Conflict-resolution rules are explicit.
- [x] Default decision rules are explicit.
- [x] Non-canonical patterns are called out clearly.
- [x] Operator/admin override paths are bounded and audited.
- [x] Read-model, cache, reporting, and projection rules are explicit.
- [x] Failure and degraded-mode behaviors are explicit.
- [x] Downstream implementation guardrails are explicit.
- [x] Dependencies and downstream impacts are explicit.
- [x] Non-goals and deferred items are explicit.
- [x] The document is strong enough for backend/API/data/runtime teams to implement without inventing contradictory semantics.
