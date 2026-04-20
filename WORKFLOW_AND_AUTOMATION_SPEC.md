# WORKFLOW_AND_AUTOMATION_SPEC

## Purpose

This document defines the canonical workflow and automation architecture of the FUZE platform. Its purpose is to establish how FUZE models multi-step execution, event-driven behavior, scheduled actions, approvals, retries, long-running product operations, and AI-coupled workflows across the ecosystem.

This specification is foundational because FUZE is not designed as a set of passive interfaces alone. Multiple products in the ecosystem depend on structured execution over time. They require actions that continue after a user click, react to events, wait for external confirmations, trigger AI tasks, update balances, send notifications, apply policy checks, and finalize results safely. The workflow and automation layer is the shared execution backbone that allows those behaviors to remain coherent across products.

---

## Scope

This specification covers:

- the canonical role of workflow and automation in the FUZE platform
- workflow structure and lifecycle
- event-driven and scheduled automation behavior
- synchronous versus asynchronous workflow boundaries
- human approval and review gates
- interaction between workflows, AI orchestration, jobs, billing, credits, and entitlements
- idempotency, retries, and failure handling principles
- workflow ownership across platform and product domains
- audit, observability, and control requirements for workflow execution
- boundaries between orchestration logic and product/business truth

This specification does not define exact queue technology, exact event payload schemas, or exact cron/task schedules. Those are refined in:

- `JOB_QUEUE_AND_WORKER_SPEC.md`
- `SCHEDULED_TASKS_AND_RETRY_POLICY_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `AI_ORCHESTRATION_SPEC.md`
- `AI_USAGE_METERING_SPEC.md`

---

## Design Goals

The design goals of the FUZE workflow and automation layer are:

1. to provide one shared execution model across platform and product domains
2. to support multi-step, long-running, and event-driven behaviors without fragmenting implementation
3. to separate workflow coordination from canonical domain ownership
4. to support both AI-powered and non-AI-powered automation flows
5. to preserve auditability, retry safety, and failure recoverability
6. to make workflow execution commercially and operationally traceable
7. to support future product expansion without forcing each product to build its own automation substrate
8. to keep sensitive or governance-relevant actions bounded by explicit controls

---

## Non-Goals

This specification is not intended to:

- make workflow state the canonical source of truth for product or financial domains
- replace product domain models with generic workflow objects
- allow products to build hidden private automation systems that bypass platform controls
- define every possible product workflow in this file
- grant automation engines permission to bypass billing, credits, or governance rules
- treat every UI action as a workflow when ordinary request-response logic is sufficient
- create unconstrained autonomous agent behavior without policy, review, or domain ownership

---

## Canonical Workflow Principle

The primary principle of FUZE workflow architecture is:

> workflows coordinate work across time, systems, and steps, but they do not replace the canonical ownership of identity, billing, credits, product data, chain state, or governance-sensitive authority.

This means:

- workflows orchestrate actions
- product and platform domains still own their business truth
- workflow state records execution coordination, not ultimate domain meaning
- automation should invoke domain actions through explicit contracts rather than mutate truth through side effects
- workflow success or failure must remain legible relative to the domains it touches

This principle is one of the most important safeguards against turning orchestration infrastructure into an accidental source of truth.

---

## Why FUZE Needs a Shared Workflow Layer

FUZE needs a shared workflow layer because many important platform and product behaviors are not single-step or synchronous.

Examples include:

- AI task request -> context build -> execution -> validation -> usage settlement
- payment verification -> credits issuance -> invoice state update -> entitlement activation
- subscription renewal -> billing check -> credits deduction -> renewal success/failure branch
- report request -> reservation -> background generation -> delivery -> final spend
- workspace invite -> acceptance -> seat assignment -> access enablement
- payout-cycle preparation -> eligibility build -> funding readiness -> reporting updates
- Botmad workflow improvement suggestion -> review -> apply or skip -> log and summary
- HerHelp generation flow -> AI transform -> validation -> preview -> publish
- ZAGA utility workflow -> configuration proposal -> operator approval -> execution logging

Without a shared workflow layer, these behaviors become product-local patchwork. That makes retries unsafe, visibility weak, and platform coherence harder to maintain.

A shared workflow layer makes FUZE more scalable because it allows the platform to standardize how multi-step work is coordinated while preserving domain-specific meaning inside the owning services.

---

## Core Concepts

### Workflow
A structured sequence of steps, transitions, and conditions used to coordinate work across one or more services or domains.

### Workflow Instance
A single execution of a defined workflow.

### Workflow Definition
The versioned structure that describes steps, transitions, inputs, conditions, and terminal states for a workflow class.

### Step
A bounded unit of workflow progress, such as validation, AI execution, approval wait, spend settlement, or finalization.

### Trigger
The event or action that starts a workflow.

### Transition
The move from one workflow state or step to another based on outcome or policy.

### Automation Rule
A policy-defined rule that allows a workflow to progress automatically under certain conditions.

### Human Gate
A review, approval, or confirmation step requiring a human actor.

### Orchestration State
The workflow-coordination state that records progress, waiting conditions, failure state, or completion state.

### Business Action Reference
A link from the workflow to the domain action or entity it coordinates.

---

## Canonical Role of the Workflow Layer

The workflow layer exists to do the following:

1. receive or recognize workflow triggers
2. instantiate the correct workflow definition
3. coordinate step execution across platform and product services
4. branch based on success, failure, review, timeout, or policy checks
5. persist workflow-coordination state
6. delegate heavy work to jobs, workers, or AI orchestration
7. surface progress and terminal outcomes to users, products, or operators
8. support retries, compensation, or cleanup when needed

The workflow layer is not intended to own:
- canonical user identity
- canonical billing truth
- canonical credits truth
- canonical product data truth
- canonical chain state
- unrestricted governance authority

It is an execution coordinator, not a replacement for domain ownership.

---

## Workflow Categories

FUZE should support several broad workflow categories.

### 1. Product Interaction Workflow
Workflows triggered by direct user or workspace actions.

Examples:
- create report
- generate HerHelp transformation
- request Botmad workflow analysis
- configure ZAGA utility sequence

### 2. Billing and Commerce Workflow
Workflows tied to subscriptions, credits, invoices, refunds, and entitlements.

Examples:
- renew subscription
- fund credits
- process usage charge
- handle failed payment recovery
- apply refund-linked reversal

### 3. AI-Coupled Workflow
Workflows that depend on one or more AI tasks.

Examples:
- classify input then route to next step
- generate structured output then validate
- summarize then require approval
- enrich data before final user-facing delivery

### 4. Async Fulfillment Workflow
Long-running or queued workflows.

Examples:
- heavy report generation
- batched intelligence refresh
- background transformation pipelines
- export generation

### 5. Approval Workflow
Workflows that require human confirmation before sensitive continuation.

Examples:
- workflow suggestion approval
- billing correction approval
- governance-sensitive off-chain action approval
- workspace ownership transfer review

### 6. Operational / Platform Workflow
Workflows used by support, reporting, reconciliation, or control-plane systems.

Examples:
- support correction flow
- payout-cycle preparation flow
- reconciliation flow
- provider incident mitigation flow

These categories help normalize platform behavior without erasing product specificity.

---

## Workflow Definition Model

Workflow definitions should be explicit and versionable.

At minimum, a workflow definition should describe:

- workflow name and class
- owning domain or primary domain reference
- trigger type
- required input references
- steps
- step ordering or graph transitions
- auto-progress rules
- human-gate steps where applicable
- timeout rules
- retry policy reference
- terminal states
- audit and observability expectations
- commercial handling requirements where applicable

### Definition principle

Products may define product-specific workflow definitions, but the structure, lifecycle semantics, and orchestration rules should remain compatible with the shared platform workflow architecture.

This is important because FUZE wants reusable execution infrastructure without forcing all products into identical task shapes.

---

## Workflow Lifecycle

At minimum, workflow instances should support the following lifecycle states:

- `created`
- `pending_validation`
- `pending_execution`
- `running`
- `waiting_external`
- `waiting_approval`
- `retry_scheduled`
- `completed`
- `failed`
- `canceled`
- `timed_out`
- `compensating`
- `archived`

### State meanings

#### Created
The workflow instance has been instantiated but not yet validated for execution.

#### Pending Validation
Required context, permissions, entitlement checks, or input validation are in progress.

#### Pending Execution
The workflow is valid and ready to progress into execution.

#### Running
One or more workflow steps are actively being executed.

#### Waiting External
The workflow is waiting on an external dependency, such as provider verification, chain confirmation, or user callback.

#### Waiting Approval
The workflow is paused for human review or approval.

#### Retry Scheduled
The workflow encountered a retryable failure and is scheduled to continue later.

#### Completed
The workflow reached a successful terminal state.

#### Failed
The workflow reached a non-recoverable failure state.

#### Canceled
The workflow was deliberately stopped.

#### Timed Out
The workflow exceeded the allowed execution window.

#### Compensating
The workflow is executing correction, rollback, release, or cleanup actions.

#### Archived
The workflow is preserved for history, reporting, or audit but is no longer active.

This lifecycle should be consistent across products even where step semantics differ.

---

## Trigger Model

Workflows in FUZE may be triggered by several sources.

### 1. User Action Trigger
Examples:
- click “generate”
- activate subscription
- purchase credits
- approve suggestion
- start export

### 2. System Event Trigger
Examples:
- payment verified
- usage threshold reached
- provider callback arrived
- entitlement changed
- chain event ingested

### 3. Schedule Trigger
Examples:
- daily refresh
- renewal cycle start
- reconciliation run
- reporting cycle generation
- cleanup sweep

### 4. Workflow-to-Workflow Trigger
Examples:
- completion of an upstream task starts a downstream task
- approval completion starts fulfillment
- AI classification result starts product routing flow

### 5. Operator Trigger
Examples:
- support remediation
- admin retry
- manual approval request
- operational incident handling

Triggers should be explicit and attributable. Hidden side-trigger behavior should be avoided.

---

## Step Model

Each workflow should consist of explicit steps.

A step should be:

- bounded in purpose
- attributable to a domain action or orchestration function
- observable
- retry-aware where applicable
- idempotent or idempotency-aware when re-executed
- able to report success, failure, wait, or review-needed outcomes

### Common step types include:

- validation step
- authorization/entitlement check
- reservation step
- AI execution step
- background job dispatch
- external provider wait
- approval step
- billing/credits settlement step
- notification step
- finalization step
- cleanup/release step

A workflow step should not be so broad that it hides many distinct side effects under one opaque label.

---

## Workflow Boundaries and Domain Ownership

The workflow layer must preserve domain boundaries.

### Workflow may coordinate:
- identity checks
- workspace validation
- billing actions
- credits reservations and settlements
- AI execution
- product object transitions
- notifications
- reporting hooks

### But canonical ownership remains with:
- identity domain for user/account truth
- workspace domain for membership truth
- billing and credits domains for commercial truth
- product domains for product-state truth
- chain services for chain interaction truth
- governance/control plane for governance-sensitive approval truth

### Boundary rule

A workflow instance may reference or invoke domain actions, but it must not silently become the owner of that domain’s truth merely because it orchestrates the sequence.

This rule is central to FUZE’s architecture.

---

## Synchronous vs Asynchronous Workflow Behavior

Not every workflow should run entirely in request-response mode.

### Synchronous workflow behavior is appropriate when:
- the work is small and bounded
- user-facing latency is acceptable
- no heavy external dependency exists
- no long-running AI or batch task is required

### Asynchronous workflow behavior is appropriate when:
- the work is heavy or long-running
- retries may be required
- provider latency is high or variable
- chain confirmation is involved
- multiple dependent steps should not block the user
- batch or scheduled execution is needed

### Hybrid behavior is appropriate when:
- a quick acknowledgement or preview is returned synchronously
- heavy continuation work proceeds asynchronously
- the UI should reflect pending status and later completion

The workflow layer must be able to support all three patterns.

---

## Automation Rules

Automation in FUZE should be policy-driven rather than uncontrolled.

An automation rule may define:
- when a workflow can auto-progress
- when a workflow must pause for approval
- when retries are allowed
- what conditions allow automatic downgrade or fallback
- when credits reservation may proceed automatically
- what notifications should be sent automatically
- what product actions may be applied without human review

### Automation principle

A workflow should auto-progress only when the policy and domain boundaries for doing so are clear.

This matters because the platform combines user-facing products, internal billing logic, AI actions, and governance-sensitive operations. Not every action that can be automated should be fully automated without bounds.

---

## Human Review and Approval Gates

The workflow system must support human gates.

Human gates are important for:

- high-impact commercial actions
- sensitive support corrections
- product changes requiring operator confirmation
- Botmad recommendation approval
- ZAGA utility configuration approval
- payout-cycle operational approvals
- governance-sensitive off-chain preparations

### Human-gate principles

- the workflow must clearly enter a waiting-approval state
- the approver identity and decision must be auditable
- approval and rejection must produce explicit workflow transitions
- timeout or abandonment behavior must be defined
- human approval should not be simulated through hidden operator side edits

Human gates allow FUZE to automate aggressively where appropriate without pretending every meaningful action should be fully autonomous.

---

## Relationship to AI Orchestration

Many FUZE workflows will interact closely with AI orchestration.

The workflow layer should be able to:
- start AI tasks
- wait on AI outcomes
- branch on AI validation results
- retry or fallback when AI execution fails
- couple AI output to review steps
- link AI usage to commercial settlement behavior

### Important rule

The workflow layer coordinates AI usage, but AI orchestration remains the owner of AI execution mechanics. Product domains remain the owners of product meaning. The workflow coordinates the sequence.

This separation keeps the platform modular and easier to reason about.

---

## Relationship to Billing, Credits, and Entitlements

Workflows frequently touch commercial domains and therefore must respect strict economic boundaries.

Examples:
- payment verification workflow -> credits issuance
- subscription renewal workflow -> credits spend -> entitlement continuation
- refund workflow -> reversal -> document update -> access restriction
- premium AI action workflow -> reservation -> execution -> settlement

### Commercial workflow principles

- workflows must not mutate credits outside the canonical credits domain
- workflows must respect entitlement checks before premium or high-cost work
- retries must not create duplicate spend
- reservation/release behavior must be explicit for long-running work
- commercial failures must remain recoverable and auditable

This is especially important because FUZE uses a shared credits economy across products.

---

## Relationship to Jobs and Workers

The workflow layer coordinates execution. Jobs and workers perform many of the actual asynchronous tasks.

The workflow layer should be able to:
- dispatch jobs
- correlate job results back to workflow instances
- handle job retry outcomes
- track waiting state while jobs run
- transition to failure, retry, or completion based on worker outputs

### Boundary rule

Workers are execution agents. Workflows are orchestration coordinators. Neither should silently redefine the product or billing truth they act upon.

This distinction should remain clear in implementation.

---

## Compensation and Recovery Behavior

Some workflows will fail after partial progress. FUZE must support compensation-aware design where appropriate.

Examples:
- credits reserved but work failed -> release reservation
- duplicate intermediate step -> ignore via idempotency controls
- entitlement activated but downstream setup failed -> compensation or pending-repair state
- provider callback never arrives -> timeout and recovery path
- invoice issued but payment normalization failed -> cancel or hold commercial state

### Compensation principles

- not every workflow needs rollback
- where rollback is impossible or inappropriate, explicit repair paths must exist
- correction actions should be explicit and auditable
- compensation logic should respect canonical domain ownership

The workflow layer should not hide partial failure. It should surface and coordinate recovery.

---

## Observability Requirements

The workflow and automation layer must support strong observability.

At minimum, the platform should be able to observe:

- workflow creation rates
- active workflow counts
- completion/failure rates
- retry volume
- average step latency
- waiting-approval backlog
- waiting-external backlog
- timeout frequency
- compensation frequency
- product-specific workflow distribution
- commercial impact of workflow failures
- AI-coupled workflow success rates

Observability matters because workflows are where many cross-domain failures become visible first.

---

## Audit Requirements

The workflow layer must generate sufficient audit visibility for meaningful execution.

At minimum, audit records should exist for:

- workflow started for auditable actions
- approval gate entered and resolved
- support/admin-triggered workflow actions
- workflow-linked commercial mutation
- workflow-linked entitlement change
- cancellation or manual override of sensitive workflows
- compensation or correction triggered by workflow failure

Not every low-risk background step needs equal audit depth, but the platform must be able to explain meaningful workflow history when trust-sensitive or commercially significant actions occur.

---

## Minimum Data Model Requirements

At minimum, the workflow and automation layer should support semantic representation of:

### Workflow Definition
- workflow_definition_id
- name
- version
- workflow_class
- owning_domain_reference
- trigger_type
- timeout_policy_reference
- retry_policy_reference
- approval_requirements where applicable

### Workflow Instance
- workflow_instance_id
- workflow_definition_id
- owner_scope_type
- owner_scope_id
- account_id where relevant
- workspace_id where relevant
- product_reference
- status
- created_at
- started_at
- completed_at / failed_at where relevant
- business_action_reference
- correlation_id

### Workflow Step Record
- workflow_step_id
- workflow_instance_id
- step_name
- step_type
- status
- started_at
- completed_at
- retry_count
- related_job_or_ai_task_ref where applicable
- result summary or code

### Approval Record where applicable
- approval_record_id
- workflow_instance_id
- approval_type
- approver_scope
- decision
- decided_at
- rationale where applicable

These are minimum semantic requirements. Detailed schemas are refined downstream.

---

## Edge Cases and Failure Handling

### Same business action triggers workflow twice
The platform must use idempotency or business-action lineage to prevent duplicate economic or product effects.

### Approval arrives after timeout
The workflow should reject, reopen under explicit policy, or route to recovery flow rather than silently continue.

### User permissions change while workflow is pending
Sensitive workflows may require revalidation before finalization.

### AI output arrives after workflow cancellation
The workflow should ignore or archive the result according to lineage rules rather than resurrecting canceled state implicitly.

### Credits reserved for async task but worker never completes
Timeout and release or recovery logic must be explicit.

### Product object changed materially mid-workflow
The workflow may need revalidation, merge logic, or failure transition rather than blindly applying stale intent.

### Provider callback repeated many times
Workflow progression must remain idempotent and resistant to duplicate side effects.

---

## Open Items

The following areas are intentionally refined in downstream specifications:

- exact DSL or representation for workflow definitions
- exact queue, broker, and scheduler technology
- exact cross-workflow event choreography patterns
- exact human-approval UX surfaces
- exact cancellation and compensation policy by workflow class
- exact archival and retention policy for workflow records

These do not weaken the canonical workflow and automation model established here.

---

## Closing Summary

The FUZE workflow and automation layer is the shared execution backbone that allows platform and product behavior to extend across time, services, and events without fragmenting into product-local automation silos. It coordinates multi-step actions, AI-coupled flows, commercial processes, approval gates, retries, and long-running fulfillment while preserving canonical domain ownership in the systems it touches. This makes workflow infrastructure a core part of FUZE’s platform architecture rather than an implementation afterthought, which is exactly what a serious multi-product AI-powered ecosystem requires.
