# SCHEDULED_TASKS_AND_RETRY_POLICY_SPEC

## Purpose

This document defines the canonical scheduled tasks and retry policy architecture of the FUZE platform. Its purpose is to establish how time-based execution, retry behavior, backoff logic, sweep jobs, delayed continuations, compensation attempts, and recovery-oriented reprocessing are governed across platform and product domains.

This specification is essential because FUZE is a multi-product platform with recurring billing, async AI tasks, workflow continuations, credits reservations, payout preparation, reconciliation routines, notifications, exports, and transparency/reporting pipelines. Many of these behaviors depend not only on event-driven execution, but also on disciplined time-based orchestration and failure-recovery rules. Without a shared scheduling and retry model, the platform would accumulate inconsistent operational behavior, duplicate commercial effects, hidden backlog risks, and weak incident recovery.

---

## Scope

This specification covers:

- the canonical role of scheduled tasks in the FUZE platform
- the distinction between scheduled execution and event-driven execution
- the classification of retryable and non-retryable work
- retry policy structure, backoff behavior, and escalation rules
- time-based sweeps, reconciliation tasks, renewals, expirations, and cleanup jobs
- workflow-linked delayed execution and retry scheduling
- policy boundaries for commercial, AI, product, and governance-sensitive retries
- observability, auditability, and operational control of scheduled and retried work
- failure handling and operator intervention principles

This specification does not define exact queue technology, exact cron syntax, or full incident escalation procedures. Those are refined in:

- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `JOB_QUEUE_AND_WORKER_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `AI_ORCHESTRATION_SPEC.md`
- `MONITORING_ALERTING_AND_INCIDENT_RESPONSE_SPEC.md`

---

## Design Goals

The design goals of the FUZE scheduled tasks and retry model are:

1. to provide one shared platform policy for time-based execution and retry behavior
2. to prevent products and services from inventing inconsistent retry logic for critical work
3. to reduce duplicate side effects in billing, credits, entitlements, and product workflows
4. to support resilient recovery from transient failures without masking deeper system problems
5. to make scheduled execution observable, auditable, and operationally controllable
6. to support lifecycle-driven platform tasks such as expirations, renewals, sweeps, and reconciliation
7. to preserve domain ownership even when retries and delayed tasks are needed
8. to distinguish clearly between safe automation and tasks that require manual review or approval

---

## Non-Goals

This specification is not intended to:

- make retries the primary error-handling strategy for badly designed domain mutations
- replace domain-level idempotency requirements
- allow critical economic actions to retry indefinitely without escalation
- define every product-specific scheduled task in this file
- treat all failures as transient
- let governance-sensitive or high-risk jobs auto-retry without stronger controls
- make scheduler state the canonical source of business truth

---

## Canonical Scheduling and Retry Principle

The primary principle of FUZE scheduled execution is:

> time-based execution and retry behavior must be explicit, class-aware, idempotency-aware, and subordinate to the canonical domain rules of the actions they trigger.

This means:

- scheduled tasks exist to coordinate execution over time, not to redefine business truth
- retries exist to recover from transient or operationally expected failure modes, not to hide ambiguity
- delayed or repeated execution must preserve lineage to the original business action
- task classes with different risk profiles must not share one undifferentiated retry strategy
- commercial, governance-sensitive, and AI-heavy workloads must remain policy-bounded

This principle is essential to the operational discipline of the FUZE platform.

---

## Why FUZE Needs a Shared Scheduled Task and Retry Model

FUZE requires a shared scheduled-task and retry model because important platform behaviors depend on time and on controlled reprocessing.

Examples include:

- subscription renewals
- recurring billing checks
- credits expiry processing
- stale reservation cleanup
- retry of transient payment-verification follow-ups
- AI task retry after provider timeout
- export and report generation retries
- notification reattempts
- reconciliation sweeps
- payout-cycle preparation checkpoints
- dead-letter review and replay preparation
- workflow timeouts and delayed continuation tasks

If each service or product defines these behaviors independently, the platform becomes difficult to reason about. The same failure may be retried three different ways depending on which component triggered it. Some tasks may retry forever while others fail prematurely. Sensitive commercial actions may duplicate. Scheduled cleanups may drift. Support teams may lose confidence in what the platform will do next.

A shared scheduled-task and retry model solves this by making time-based behavior a first-class platform concern rather than a collection of local implementation habits.

---

## Core Concepts

### Scheduled Task
A task intentionally planned for execution at a specific time, interval, or policy-defined window.

### Retryable Task
A task that may be re-executed after failure according to policy.

### Retry Policy
The defined rules controlling whether a failure may be retried, how often, how soon, and under what escalation conditions.

### Backoff
The delay strategy between retry attempts.

### Sweep Task
A periodic task that scans for stale, inconsistent, expired, or incomplete state and applies policy-defined follow-up behavior.

### Recovery Task
A scheduled or operator-triggered task designed to repair or continue incomplete business processes.

### Escalation Threshold
The point at which normal automatic retry stops and review, dead-letter handling, or incident escalation begins.

### Retry Lineage
The traceable relationship between original attempt and later retry attempts.

### Delayed Continuation
A workflow or job step intentionally resumed later due to waiting conditions, timing windows, or provider dependencies.

---

## Canonical Role of Scheduled Tasks

Scheduled tasks in FUZE exist to handle work that depends on time rather than only immediate events.

The scheduler layer should support tasks such as:

- recurring subscription renewal scans
- credits expiration processing
- stale reservation sweeps
- periodic reconciliation
- daily or periodic reporting generation
- delayed follow-up on pending states
- reminder or notification windows
- cleanup and archival tasks
- payout-cycle checkpoint tasks
- provider-status recheck tasks where policy requires

### Scheduling principle

A scheduled task should exist because:
- the business logic is inherently time-based,
- delayed follow-up is safer or clearer than immediate looping,
- periodic consistency checks are required,
- or platform operations require windowed processing.

Scheduled tasks should not be used merely as a substitute for proper event-driven design when immediate event handling is the better architecture.

---

## Canonical Role of Retry Policies

Retry policies exist to help the platform recover from transient or expected operational failure modes without creating duplicate business effects.

Retries are appropriate when failures may be caused by:

- temporary provider downtime
- network interruption
- queue or worker instability
- lock contention
- rate limiting
- chain RPC degradation
- malformed-but-recoverable transient output
- external callback lag
- temporary unavailability of dependent systems

Retries are not a license to repeat ambiguous or high-impact economic actions without strong safeguards.

### Retry principle

A retry is valid only when:
- the failure is classified as retryable,
- idempotency or mutation safety is preserved,
- the task remains meaningful to perform again,
- and policy does not require human review instead.

Retries are therefore a controlled resilience mechanism, not a convenience feature.

---

## Scheduled Task Categories

FUZE should classify scheduled tasks by role and risk.

### 1. Commercial Lifecycle Tasks
Examples:
- subscription renewal scan
- grace-period expiration
- credits expiry processing
- billing retry window follow-up
- unpaid-state restriction transition

### 2. Workflow Continuation Tasks
Examples:
- timeout follow-up
- delayed continuation after waiting state
- approval reminder or timeout branch
- scheduled release of stale reservations

### 3. Reconciliation and Consistency Tasks
Examples:
- payment-to-ledger reconciliation
- ledger-to-chain reconciliation
- invoice/receipt consistency scan
- entitlement-state repair scan
- stale pending-state detection

### 4. Reporting and Export Tasks
Examples:
- daily usage summary generation
- payout ledger refresh
- transparency report generation
- export finalization retry
- archive generation

### 5. Notification and Reminder Tasks
Examples:
- reminder send windows
- delayed user follow-up
- dunning notice schedule
- operator alert fanout retries

### 6. AI and Heavy Compute Follow-Up Tasks
Examples:
- AI job retry after transient model failure
- delayed validation pass
- batched transformation rerun
- heavy processing retry after capacity recovery

### 7. Operational and Support Tasks
Examples:
- dead-letter review sweeps
- stuck-job inspection
- recovery requeue candidates
- scheduled migration steps
- incident-repair follow-up tasks

These categories matter because retry rules and scheduling discipline should differ based on the type of work involved.

---

## Retry Classes

FUZE should classify retry behavior into explicit classes.

### 1. Safe Automatic Retry
Used where:
- the task is strongly idempotent
- failure is likely transient
- side effects are well-guarded
- retry can occur without human review

Examples:
- provider timeout on non-finalized AI call
- notification delivery retry
- temporary RPC unavailability for read-side operation

### 2. Guarded Automatic Retry
Used where:
- retry is allowed
- but stronger mutation guards or revalidation are required
- and the action may affect product or commercial state

Examples:
- credits settlement continuation
- subscription renewal follow-up
- structured-output regeneration for workflow continuation
- delayed entitlement activation after verified funding

### 3. Deferred Review Retry
Used where:
- the task might still be recoverable
- but ambiguity or repeated failure makes blind retry unsafe

Examples:
- repeated provider inconsistency
- repeated malformed output
- unclear external state after prior side effect may have occurred
- repeated commitment failure on chain-affecting job

### 4. No Automatic Retry
Used where:
- retry could create harmful duplication
- the action is governance-sensitive
- the payload is corrupted
- or domain ambiguity is too high

Examples:
- high-impact manual correction task
- reserve-related governance-sensitive job
- payout-cycle critical mutation without confirmed prior state
- repeated unsafe compensation action

Retry classes should be part of the platform model rather than scattered inside service-specific code.

---

## Backoff Policy Principles

Retry timing should be deliberate rather than immediate and uniform.

### Backoff should consider:
- task criticality
- provider rate limits
- likelihood of transient recovery
- commercial sensitivity
- queue pressure
- user-facing urgency
- chain confirmation timing where relevant

### Recommended backoff patterns
The platform may use:
- short exponential backoff for transient low-risk failures
- longer bounded backoff for provider degradation
- scheduled retry windows for commercial tasks
- manual review after threshold breach for sensitive workloads

### Backoff principle

The backoff strategy should reduce system stress, reduce duplicate pressure on dependencies, and improve recovery probability without causing hidden indefinite delays.

The exact numeric schedule may vary by class, but class-specific behavior must remain explicit.

---

## Retry Attempt Limits and Escalation

Every retryable class should have attempt limits and escalation rules.

### Attempt-limit principles

- automatic retry count should be bounded
- repeated failure should produce a state transition, not silent looping
- different job classes may have different thresholds
- high-risk commercial or governance-sensitive tasks should escalate sooner
- dead-letter or review-required handling should begin once automatic retry is no longer safe

### Escalation outcomes may include
- dead-lettering
- support or ops review queue
- workflow failure transition
- compensation/release path
- incident alerting
- replay-preparation state

A bounded retry model is essential for trust because endless retries can create invisible harm and operational confusion.

---

## Scheduling Windows and Timing Discipline

Some scheduled tasks should not run continuously. They should run within defined windows.

Examples include:
- billing-cycle renewals
- dunning reminders
- daily summary generation
- payout preparation stages
- report refresh windows
- archival or cleanup windows

### Windowing principle

Time-based work should be scheduled in ways that fit its business meaning and operational risk. The platform should avoid both:
- uncontrolled constant polling, and
- vague “eventually sometime” background behavior.

The timing of scheduled execution should be explainable, observable, and consistent with product expectations.

---

## Workflow Timeouts and Delayed Continuations

The workflow layer and scheduler layer must interact closely.

Scheduled tasks should support workflow behaviors such as:
- wait until external callback deadline
- retry continuation after transient failure
- time out waiting-approval state
- release stale reservation if work never finalized
- check delayed completion of async task
- escalate after max wait duration

### Principle

A delayed continuation is not a new business action. It is continuation lineage of an existing workflow or job. The platform must preserve this linkage for auditability, idempotency, and support clarity.

---

## Credits, Billing, and Entitlement Retry Safety

Commercial state is one of the most sensitive areas for retries.

### Required rules

- credits issuance must not double-issue on repeated callbacks or retries
- credits spend must not double-deduct on repeated settlement attempts
- reservation release must not destroy active valid reservation state incorrectly
- subscription renewal must not double-renew
- entitlement activation must not silently drift from actual commercial state
- refund-linked reversals must not over-correct due to duplicate retry behavior

### Commercial retry principle

Commercial retries must be domain-idempotent, not merely queue-retried. The scheduled-task layer may coordinate timing, but the canonical billing and credits domains must guard the actual economic mutation.

This is a core requirement of the FUZE internal economy.

---

## AI Retry Policy

AI tasks require their own retry discipline.

### AI retries may be appropriate for:
- transient provider timeout
- malformed but retryable structured output
- fallback-provider reroute
- temporary rate-limit recovery
- transient tool-call dependency failure

### AI retries should be restricted when:
- task cost is too high to repeat blindly
- prior side effects may already have occurred downstream
- repeated output invalidity suggests instruction or schema mismatch
- human review is required
- user permissions or commercial state may have changed materially since original request

### AI retry principle

AI retry should optimize for bounded recovery, not for infinite generation attempts. The platform should prefer:
- one or more guarded retries,
- then fallback or degrade,
- then explicit failure or review state.

---

## Scheduled Sweeps and Reconciliation Tasks

FUZE should use scheduled sweeps to maintain operational integrity.

Examples include:
- detect stale reservations
- detect pending commitment lag
- detect failed or missing receipt generation after payment verification
- detect orphaned workflow waiting states
- detect expired bonus credits
- detect unreconciled platform-vs-chain state
- detect misaligned entitlement states
- detect dead-letter backlog growth

### Sweep principle

A sweep does not redefine business truth. It inspects system state and triggers defined repair, escalation, or cleanup actions where policy allows.

Sweeps are important because not all inconsistencies announce themselves through events. Some only become visible over time.

---

## Manual Review and Operator Intervention

Some scheduled or retry paths must transition to human review.

Examples:
- repeated failure of a commercial correction task
- repeated commitment failure for credits-related chain write
- ambiguous payout-cycle preparation state
- repeated malformed AI structured output for a sensitive workflow
- dead-lettered billing or entitlement task
- scope ambiguity after repeated retries

### Operator-intervention principles

- human intervention must be auditable
- replay should preserve lineage
- manual override must not silently bypass canonical domain controls
- reviewed tasks should record whether they were resolved, requeued, canceled, or compensated

The scheduler must support human review as a first-class operational outcome, not as an improvised emergency fix.

---

## Governance-Sensitive Scheduled Tasks

Some scheduled tasks are governance-sensitive or operationally critical enough to require special handling.

Examples:
- payout-cycle stage progression
- reserve or registry-related coordination jobs
- high-impact migration windows
- bulk treasury or vault reporting tasks with control significance
- governance-approved delayed execution windows

### Governance-sensitive scheduling rules

- should use explicit authorization lineage
- should have stricter retry and escalation behavior
- should not auto-retry blindly
- may require dedicated queues or review gates
- should be highly observable and auditable

This matters because time-based execution must not become a hidden side path around governance discipline.

---

## Observability Requirements

The platform should be able to observe, at minimum:

- scheduled task counts by class
- retry counts and retry success rates
- backoff delay distributions
- dead-letter and escalation rates
- stuck scheduled task counts
- stale waiting-state counts
- timeout frequencies
- reconciliation sweep findings
- commercial-impacting retry failures
- queue growth caused by repeated scheduled work
- provider degradation effects on retry behavior

Observability is essential because retry loops and timing drift are among the easiest ways for async platforms to fail invisibly.

---

## Audit Requirements

The scheduler and retry system should generate audit visibility for meaningful actions such as:

- creation of sensitive scheduled tasks
- retry of commercial-impacting actions
- escalation of dead-letter-worthy jobs
- manual replay or cancel of sensitive tasks
- timeout of approval-sensitive or governance-sensitive workflows
- automatic release or compensation after timed-out reserved work
- operator resolution of repeated-failure tasks

Not every low-risk reminder requires the same audit depth, but important delayed and retried actions must remain explainable.

---

## Minimum Data Model Requirements

At minimum, the scheduled-task and retry system should support semantic representation of:

### Scheduled Task Record
- scheduled_task_id
- task_type
- owning_domain_reference
- related_workflow_or_job_ref
- owner_scope_type / owner_scope_id where relevant
- schedule_class
- next_run_at
- created_at
- status
- correlation_id

### Retry Policy Record
- retry_policy_id
- task_class
- retry_class
- max_attempts
- backoff_strategy
- escalation_rule
- timeout_rule reference where applicable
- policy_version

### Retry Attempt Record
- retry_attempt_id
- related_task_or_job_ref
- attempt_number
- started_at
- ended_at
- outcome
- failure_code
- next_retry_at where applicable

### Escalation Record
- escalation_id
- related_task_or_job_ref
- escalation_type
- triggered_at
- review_status
- resolution_reference where applicable

These are minimum semantic requirements. Detailed schema design is refined elsewhere.

---

## Edge Cases and Failure Handling

### Same transient failure happens across many tasks simultaneously
The platform should surface systemic degradation rather than treating each retry as isolated noise.

### Repeated retries keep succeeding after long delay but create poor user experience
Retry policy must be paired with product-visible timeout or status handling rather than silently extending uncertainty.

### Scheduled renewal task runs late because scheduler backlog grows
Commercial timing drift must be visible and recoverable, especially where entitlement or billing outcomes are time-sensitive.

### Sweep task identifies inconsistent state but repair action is unsafe
The system should escalate to review rather than automatically mutating sensitive state.

### Retry occurs after user entitlement changed materially
Sensitive actions should revalidate commercial and permission context before final mutation.

### Dead-lettered task later becomes safe to replay
Replay should preserve lineage and resolution history rather than appearing as a fresh unrelated action.

### Long-running provider outage causes retry storm
Backoff, throttling, and class-aware degradation controls should prevent uncontrolled platform pressure.

---

## Open Items

The following areas are intentionally refined in downstream specifications:

- exact retry schedules by task class
- exact timeout durations
- exact sweep cadence by domain
- exact dunning and billing reminder timing
- exact operator tooling for replay and escalation
- exact degraded-mode policies during systemic provider outages

These do not weaken the canonical scheduling and retry model established here.

---

## Closing Summary

The FUZE scheduled tasks and retry policy layer governs how the platform executes time-based work, recovers from transient failures, and escalates ambiguous or repeated failure modes. It gives the ecosystem one shared discipline for renewals, expirations, sweeps, delayed continuations, AI retries, commercial retries, and recovery tasks without allowing scheduler behavior to become a hidden source of business truth. By making retries bounded, class-aware, idempotency-aware, and operationally observable, FUZE strengthens the reliability of its workflows, credits economy, product automations, and platform trust model.
