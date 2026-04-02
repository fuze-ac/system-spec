# JOB_QUEUE_AND_WORKER_SPEC

## Purpose

This document defines the canonical job queue and worker architecture of the FUZE platform. Its purpose is to establish how asynchronous work is queued, claimed, executed, retried, finalized, and observed across the ecosystem, and how worker execution remains aligned with platform ownership boundaries, commercial safety, and operational resilience.

This specification is foundational because FUZE includes many operations that should not run entirely inside synchronous request-response flows. These include AI-heavy tasks, payment follow-ups, credits settlement steps, reporting jobs, workflow continuations, reconciliation jobs, notification fanout, snapshot preparation, payout support work, and product-specific long-running tasks. The queue and worker layer is therefore one of the shared operational backbones of the platform.

---

## Scope

This specification covers:

- the canonical role of job queues and workers in the FUZE platform
- queue-backed async execution patterns
- job lifecycle and worker lifecycle
- separation between workflow orchestration and worker execution
- job ownership, idempotency, retry, timeout, and dead-letter behavior
- product and platform use of shared worker infrastructure
- commercial and policy-sensitive implications of worker execution
- observability, audit, and operational controls for queued work
- failure handling and recovery principles for async jobs

This specification does not define exact cron schedules, exact retry tables, or exact event payload schemas. Those are refined in:

- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `SCHEDULED_TASKS_AND_RETRY_POLICY_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `AI_ORCHESTRATION_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `MONITORING_ALERTING_AND_INCIDENT_RESPONSE_SPEC.md`

---

## Design Goals

The design goals of the FUZE queue and worker model are:

1. to provide one shared async execution layer across platform and product domains
2. to keep long-running or retryable work out of latency-sensitive request paths
3. to preserve explicit ownership boundaries even when jobs touch many domains
4. to make queued work idempotent, observable, recoverable, and commercially safe
5. to support both platform-core and product-specific workloads on one coherent execution model
6. to make failures and partial completion visible rather than silent
7. to support future product expansion without each product building a separate async substrate
8. to protect billing, credits, entitlements, and governance-sensitive flows from duplicate or ambiguous execution

---

## Non-Goals

This specification is not intended to:

- make the queue system the canonical owner of business truth
- replace workflow orchestration with raw queue mechanics
- allow products to create hidden private worker systems that bypass platform control
- treat retries as a substitute for proper domain-level idempotency
- let workers mutate sensitive state without explicit service contracts
- define a specific broker or queue vendor as mandatory in this file
- model every background action as equivalent in criticality or recovery needs

---

## Canonical Queue Principle

The primary principle of the FUZE queue and worker architecture is:

> queues coordinate deferred execution, and workers perform bounded units of async work, but neither queues nor workers become the canonical source of truth for the domains they touch.

This means:

- jobs represent execution intent, not ultimate business truth
- workers invoke domain-owned operations through explicit contracts
- job state records processing progress, not the final meaning of billing, credits, product state, or governance-sensitive authority
- worker success must be reflected back into the owning domains and workflows explicitly
- worker failure must remain visible and recoverable

This principle prevents async infrastructure from quietly becoming an ungoverned source of system truth.

---

## Why FUZE Needs a Shared Queue and Worker Layer

FUZE needs a shared queue and worker layer because many important platform and product actions are:

- long-running
- retryable
- externally dependent
- high-latency
- heavy in compute cost
- batch-oriented
- order-sensitive
- failure-prone in ways that request-response paths should not absorb directly

Examples include:

- heavy AI generation
- report generation
- credits reservation follow-up and final settlement
- payment verification follow-ups
- subscription renewal continuation tasks
- workflow continuation after external callback
- notification fanout
- reconciliation scans
- payout-cycle preparation steps
- Base commitment follow-ups
- wallet snapshot enrichment jobs
- product-specific transformation or enrichment jobs

Without a shared async execution layer, these behaviors would become fragmented across products and services. That would weaken observability, idempotency discipline, and operational control.

A shared queue and worker model gives FUZE a common execution fabric for deferred work while still preserving clear domain ownership in the results.

---

## Core Concepts

### Job
A queued unit of deferred execution representing a bounded async action.

### Job Type
A normalized category of queued work such as AI generation, credits settlement follow-up, report generation, notification dispatch, or reconciliation.

### Queue
A channel or grouped backlog of jobs with shared operational characteristics.

### Worker
An execution process or logical worker role that claims and processes queued jobs.

### Claim
The act of taking responsibility for a queued job for active execution.

### Attempt
One execution try for a job.

### Retry
A later attempt to process a job after a retryable failure.

### Dead-Letter State
A terminal or quarantined state for jobs that cannot continue automatically.

### Idempotency Key
A stable key or equivalent guard used to prevent duplicate economic or business effects.

### Result Contract
The structured success/failure output by which a worker reports outcome back to orchestration or domain services.

---

## Canonical Role of the Queue Layer

The queue layer exists to:

1. accept jobs for deferred execution
2. order or prioritize jobs according to class and policy
3. make jobs available to appropriate worker classes
4. persist job state transitions
5. support retries, backoff, and dead-letter behavior
6. expose operational visibility into backlog and execution health

The queue layer does not decide the business meaning of successful or failed domain actions. It coordinates deferred execution.

---

## Canonical Role of the Worker Layer

The worker layer exists to:

1. claim jobs from queues
2. load required execution context
3. invoke bounded domain or platform actions
4. classify outcomes as success, retryable failure, terminal failure, or manual-review-needed
5. emit job result records, domain updates, and observability signals
6. release, settle, compensate, or escalate as required by policy

Workers are execution agents. They are not governance authorities, billing authorities, or product truth owners. They must remain subordinate to explicit platform and domain rules.

---

## Job Categories

FUZE should support normalized job categories so that operational policy can remain structured.

### 1. AI Execution Jobs
Examples:
- heavy generation
- batch summarization
- asynchronous reasoning tasks
- structured content transformation
- large prompt-and-context execution

### 2. Workflow Continuation Jobs
Examples:
- continue after validation
- continue after AI completion
- continue after external callback
- continue after approval outcome

### 3. Commerce and Billing Jobs
Examples:
- subscription renewal processing
- usage settlement continuation
- invoice generation follow-up
- refund-linked reversal continuation
- credits expiration processing

### 4. Credits and Settlement Jobs
Examples:
- reservation cleanup
- spend finalization
- Base commitment retry
- ledger reconciliation continuation
- scope correction processing

### 5. Reporting and Export Jobs
Examples:
- transparency report generation
- payout ledger materialization
- investor report export
- CSV/PDF/report generation
- large workspace activity summary generation

### 6. Reconciliation Jobs
Examples:
- payment reconciliation
- ledger-to-chain reconciliation
- stale reservation sweep
- document-state consistency scans
- product-commercial mismatch scan

### 7. Notification and Fanout Jobs
Examples:
- email dispatch
- webhook dispatch
- user alert fanout
- internal ops alerts
- product event notifications

### 8. Operational and Support Jobs
Examples:
- support correction execution
- replay processing
- migration jobs
- backfill jobs
- post-incident repair jobs

### 9. Product-Specific Async Jobs
Examples:
- QTB signal enrichment
- AIMM market workflow refresh
- ZAGA utility batch update
- AIE event indexing
- HerHelp app-generation pipeline
- Botmad workflow simulation or scan

Job categories should remain explicit because retry, timeout, cost, and observability behavior often differ by class.

---

## Queue Topology Principles

FUZE should use a queue topology that reflects job characteristics rather than a single undifferentiated backlog.

At minimum, the topology should support separation by:

- execution criticality
- latency sensitivity
- retry characteristics
- commercial sensitivity
- compute heaviness
- product versus platform ownership
- provider dependency class

### Recommended queue groupings

- interactive-follow-up queue
- heavy AI queue
- commerce and credits queue
- reporting/export queue
- reconciliation queue
- notification queue
- support/ops queue
- product-specific specialized queues where justified

### Queue topology principle

Separate queues should exist where operational behavior differs meaningfully. This reduces interference between lightweight and heavyweight tasks and improves recovery and prioritization.

---

## Job Lifecycle

At minimum, jobs should support the following lifecycle states:

- `created`
- `queued`
- `claimed`
- `running`
- `succeeded`
- `retryable_failure`
- `retry_scheduled`
- `failed_terminal`
- `dead_lettered`
- `canceled`
- `timed_out`
- `compensated` where applicable

### State meanings

#### Created
The job intent has been accepted by the platform but not yet enqueued for active claiming.

#### Queued
The job is waiting in the target queue.

#### Claimed
A worker has taken responsibility for the job.

#### Running
The worker is actively executing the job.

#### Succeeded
The job completed successfully and emitted its expected result contract.

#### Retryable Failure
The attempt failed in a way that policy allows retry.

#### Retry Scheduled
The job is waiting for its next attempt according to retry policy.

#### Failed Terminal
The job cannot continue automatically.

#### Dead Lettered
The job is isolated for manual review, replay, or incident handling.

#### Canceled
The job was deliberately stopped.

#### Timed Out
The execution exceeded allowed time.

#### Compensated
The system has executed required release, cleanup, or corrective follow-up after failure or cancellation.

These states must be visible to workflows and operations where relevant.

---

## Worker Lifecycle

Workers should also have explicit lifecycle meaning.

Recommended worker states include:

- `starting`
- `idle`
- `claiming`
- `processing`
- `backing_off`
- `degraded`
- `draining`
- `stopped`

This supports operational visibility into worker health and helps incident handling avoid treating all missing progress as silent job failure.

---

## Job Ownership and Domain Boundaries

Each job must have a clear owning domain or primary domain reference.

### Ownership model

- the queue/worker infrastructure owns execution mechanics
- the owning domain owns business meaning
- the workflow layer owns multi-step coordination where applicable

### Example

A credits settlement job:
- is executed by worker infrastructure
- is coordinated by workflow or billing logic where needed
- but the credits domain remains canonical for the resulting ledger truth

### Boundary rule

Workers must call explicit service contracts or domain operations rather than mutating unrelated domain state through informal internal shortcuts.

This is especially important for:
- credits
- entitlements
- subscription state
- payout-cycle operations
- governance-sensitive actions

---

## Job Submission Rules

Jobs should be submitted only when deferred execution is justified.

Appropriate reasons include:

- high latency
- external dependency
- heavy compute
- retry need
- decoupling from request path
- batch efficiency
- scheduled execution
- fanout behavior

Jobs should not be used merely to avoid designing proper synchronous service boundaries. Async work should exist because the execution profile requires it, not because it is convenient to hide business logic in background tasks.

Job submission should capture at minimum:

- job type
- owning domain reference
- owner scope if relevant
- business action reference
- priority or execution class
- idempotency context
- retry policy reference
- correlation ID

---

## Idempotency Principles

The queue and worker layer must be idempotency-aware.

This is especially important because jobs may be retried due to:

- provider timeouts
- worker crashes
- partial failures
- queue redelivery
- operator replay
- duplicate upstream event generation

### Idempotency requirements

- the same business action must not cause duplicate economic effects
- credits issuance, spend, reversal, and settlement jobs must be explicitly guarded
- subscription renewal jobs must not double-renew due to callback duplication
- AI-heavy jobs with reservation semantics must not double-consume credits
- replay should be possible without silent duplication

### Important distinction

Queue-level deduplication is helpful but not sufficient. Domain-level idempotency or mutation guards are also required because the worker layer cannot guarantee external-provider or end-to-end uniqueness by itself.

---

## Retry Model

Retries must be policy-driven and job-class-aware.

### Retry categories

#### Safe Retries
Appropriate where the job is idempotent and failure is transient.

Examples:
- transient provider timeout
- temporary network failure
- non-terminal queue/broker issue
- retryable AI provider failure
- temporary Base RPC unavailability

#### Guarded Retries
Appropriate where retries are allowed only after revalidation or with strict mutation controls.

Examples:
- credits settlement retry
- subscription renewal follow-up
- entitlement activation retry
- report-generation resumption

#### No-Auto-Retry
Appropriate where retry could worsen damage or duplicate harmful effects without manual review.

Examples:
- ambiguous high-impact support correction
- governance-sensitive operational job
- corrupted payload case
- repeated structured validation failure with unclear state

Retry policy should remain visible by job class rather than hidden inside worker code.

---

## Timeout and Heartbeat Rules

Jobs and workers should support timeout-aware execution.

### Timeout principles

- every job class should have an expected execution window
- long-running jobs should emit heartbeat or progress signals where practical
- a timed-out job should not be assumed safe to rerun without idempotency protections
- timeout handling should distinguish between worker death, provider stall, and legitimate long-running execution
- timed-out jobs may transition to retry, dead-letter, or manual review depending on class

This is especially important for heavy AI jobs, chain-dependent jobs, and export/report generation jobs.

---

## Dead-Letter and Quarantine Behavior

FUZE must support dead-letter or equivalent quarantine handling for jobs that cannot continue safely.

### Jobs may be dead-lettered when:

- retry limits are exhausted
- payload is invalid or corrupted
- domain state is inconsistent
- repeated ambiguity exists about whether side effects already occurred
- policy requires manual review
- provider output is repeatedly malformed
- correction or compensation is needed before replay

### Dead-letter principles

- dead-letter is not deletion
- dead-lettered jobs must remain visible to operations
- replay or repair paths should exist where appropriate
- commercial or governance-sensitive dead-letter jobs should be reviewable with stronger urgency

A serious async platform must be able to isolate toxic or ambiguous jobs instead of endlessly retrying them.

---

## Relationship to Workflows

The queue and worker layer is closely tied to workflows, but their roles differ.

### Workflow layer owns
- multi-step coordination
- branch logic
- waiting states
- approval states
- business-level progression across time

### Queue/worker layer owns
- execution of deferred step work
- retries and backoff
- resource isolation
- job-level failure handling
- operational visibility into async execution

A workflow may dispatch multiple jobs. A job may report back into a workflow. But the queue does not replace the workflow state machine, and the workflow does not replace the worker execution substrate.

---

## Relationship to AI Orchestration

Many AI tasks will execute through queues and workers.

### Common patterns include:
- enqueue heavy AI generation
- queue tool-orchestrated retrieval and generation
- batch AI jobs for reports or transformations
- AI validation retries
- post-generation normalization or persistence jobs

### Important rule

The AI orchestration layer still owns model-routing and execution semantics. Workers execute the deferred task. Products still own the meaning of the result.

This separation prevents the worker system from becoming a hidden AI platform unto itself.

---

## Relationship to Credits, Billing, and Entitlements

Workers often touch sensitive commercial systems and therefore must operate with stricter discipline.

### Examples
- finalize credits spend after job success
- release credits reservation after failure
- continue subscription renewal after payment verification
- process refund-linked reversals
- activate entitlements after durable completion of purchase flow

### Commercial safety rules

- workers must not produce duplicate credits or entitlement effects
- retries must preserve idempotency
- partial success must be visible
- compensation or release behavior must be explicit
- commercial mutation should occur through domain-owned service contracts, not inline ad hoc logic

This is one of the most important reasons queue and worker design is a platform-level concern rather than a product-only concern.

---

## Governance-Sensitive and Admin-Sensitive Jobs

Some jobs are too sensitive to be treated like ordinary background work.

Examples include:
- high-impact support corrections
- payout-cycle operational jobs
- registry update propagation
- reserve-related coordination jobs
- bulk migration jobs affecting commercial state

### Rules for sensitive jobs

- must have clear authorization lineage
- may require approval reference before enqueue
- should have stricter audit trails
- may use dedicated queues or worker pools
- should not auto-retry blindly
- should support operator review and replay discipline

This is necessary because async execution convenience must not become a side door around governance and control rules.

---

## Worker Isolation and Resource Policy

The platform should support worker isolation where operational profiles differ meaningfully.

### Isolation may be useful for:
- heavy AI jobs
- high-priority commerce jobs
- low-priority reporting jobs
- provider-sensitive integration jobs
- support/ops correction jobs

### Benefits include:
- reduced noisy-neighbor effects
- clearer prioritization
- safer scaling by workload type
- better incident containment
- more predictable latency for important queues

Exact deployment shape may vary, but the architectural principle of workload-aware isolation should remain.

---

## Observability Requirements

The queue and worker layer must support strong observability.

At minimum, the platform should be able to observe:

- queue depth by queue
- claim latency
- processing latency
- success/failure rates by job type
- retry rates
- dead-letter volume
- timeout volume
- worker availability and degradation
- duplicate-delivery or idempotency-conflict signals
- commercial-impacting job failures
- product-specific async health

These metrics are essential because async systems often hide failure unless explicitly surfaced.

---

## Audit Requirements

The queue and worker layer must produce sufficient audit visibility for meaningful events.

At minimum, audit records should exist for:

- enqueue of sensitive jobs
- execution of commercial-impacting jobs
- support/admin-triggered replay or cancellation
- dead-letter entry for trust-sensitive jobs
- manual resolution of quarantined jobs
- execution of governance-sensitive async actions
- compensation or release actions triggered after failure

Not every low-risk notification job needs equal audit depth, but the platform must be able to explain important async actions when they affect trust, money, access, or governance.

---

## Minimum Data Model Requirements

At minimum, the queue and worker system should support semantic representation of:

### Job Record
- job_id
- job_type
- queue_name
- owning_domain_reference
- owner_scope_type / owner_scope_id where relevant
- business_action_reference
- status
- priority
- created_at
- scheduled_at / available_at
- claimed_at
- completed_at / failed_at
- attempt_count
- idempotency_key or equivalent mutation key
- correlation_id

### Job Attempt Record
- job_attempt_id
- job_id
- worker_reference
- started_at
- ended_at
- attempt_result
- failure_code / summary where applicable
- retry_decision

### Worker Record
- worker_id
- worker_class
- status
- current_job_reference where applicable
- started_at
- last_heartbeat_at
- degradation flag where applicable

### Dead-Letter / Quarantine Record
- dead_letter_id
- job_id
- reason
- created_at
- review_status
- resolution_reference where applicable

These are minimum semantic requirements. Detailed schemas are refined downstream.

---

## Edge Cases and Failure Handling

### Worker crashes after applying side effect but before acknowledging job
Domain-level idempotency must prevent duplicate side effects on replay, and the job should remain recoverable.

### Same upstream event enqueues duplicate jobs
Submission and domain mutation guards must prevent duplicate commercial or product effects.

### Job payload becomes stale before execution
Sensitive jobs may require revalidation before final mutation.

### Queue backlog grows during provider outage
The platform should surface degraded state clearly and may throttle, reroute, or delay low-priority queues according to policy.

### Heavy report job succeeds but result persistence fails
The system should preserve a recoverable partial-completion state rather than treating the job as simply lost.

### Credits reservation tied to timed-out AI job
Release or recovery workflow must be explicit and auditable.

### Operator replays dead-lettered job without resolving root cause
The system should preserve lineage and surface repeated failure rather than hiding replay loops.

---

## Open Items

The following areas are intentionally refined in downstream specifications:

- exact queue/broker technology
- exact worker deployment pattern
- exact backoff schedule tables by job class
- exact dead-letter replay tooling
- exact concurrency and rate-limiting policy by queue
- exact incident-response automation around queue saturation

These do not weaken the canonical queue and worker model established here.

---

## Closing Summary

The FUZE job queue and worker layer is the shared async execution substrate that allows heavy, retryable, and long-running work to run safely outside synchronous request paths. It coordinates deferred execution for AI tasks, billing follow-ups, credits settlement, reporting, reconciliation, notifications, and product-specific async workloads while preserving canonical domain ownership in the systems it touches. By making jobs explicit, scoped, idempotent, observable, and recoverable, FUZE turns async execution into a disciplined platform capability rather than a hidden source of inconsistency.
