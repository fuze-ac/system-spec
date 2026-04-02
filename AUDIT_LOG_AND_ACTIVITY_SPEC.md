# AUDIT_LOG_AND_ACTIVITY_SPEC

## Purpose

This document defines the canonical audit log and activity architecture of the FUZE ecosystem. Its purpose is to establish how FUZE records, classifies, stores, correlates, and exposes meaningful operational, economic, governance, and product events so that the platform remains explainable, supportable, auditable, and consistent with its transparency-first design principles.

This specification is foundational because FUZE is not a simple single-product application. It is a multi-product platform ecosystem with identity systems, workspaces, Platform Credits, AI orchestration, workflow automation, treasury and reserve controls, payout-sensitive systems, and governance-sensitive contract actions. In an environment like this, activity tracking is not merely a backend convenience. It is part of the operating integrity of the platform. Without a disciplined audit and activity system, the platform becomes harder to support, harder to reconcile, harder to govern, and harder to explain publicly when trust-sensitive events occur.

---

## Scope

This specification covers:

- the canonical role of audit logs and activity records in FUZE
- the distinction between user-facing activity history and internal audit-grade event records
- what kinds of platform, product, economic, and governance actions must be captured
- how event records should be structured, classified, correlated, and retained
- how activity and audit systems interact with identity, workspaces, Platform Credits, AI usage, workflows, payouts, treasury actions, and governance actions
- visibility, access, privacy, and reporting boundaries for activity and audit data
- failure handling, correction, reconciliation, and review expectations for audit-sensitive events

This specification does not define every storage schema, every SIEM integration detail, or every public transparency reporting surface. Those are refined in:

- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `REFUND_REVERSAL_AND_ADJUSTMENT_SPEC.md`
- `AI_USAGE_METERING_SPEC.md`
- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `TREASURY_CONTROL_POLICY_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`
- `MONITORING_ALERTING_AND_INCIDENT_RESPONSE_SPEC.md`

---

## Design Goals

The design goals of the FUZE audit log and activity architecture are:

1. to create a reliable system of record for meaningful platform and product actions
2. to distinguish operational activity history from audit-grade governance and economic records
3. to preserve traceability across users, workspaces, wallets, products, workflows, credits, and governance actions
4. to support supportability, dispute handling, reconciliation, and incident review
5. to improve transparency and trust by making important system behavior explainable
6. to support privacy-aware visibility while preserving strong internal auditability
7. to make activity and audit events reusable across reporting, monitoring, support, compliance, and governance workflows
8. to ensure that critical events remain attributable, structured, and durable over time

---

## Non-Goals

This specification is not intended to:

- log every meaningless interface interaction with the same weight as important economic or governance actions
- expose all internal audit records directly to end users
- replace product analytics with audit logging
- treat operational monitoring alerts as a substitute for durable audit records
- make user-facing activity feeds the same thing as canonical audit records
- store private or sensitive content more broadly than necessary
- define all platform data retention rules for every jurisdiction in this file

---

## Canonical Audit Principle

The primary principle of FUZE audit and activity design is:

> every meaningful action that affects identity, access, money, credits, workflow outcome, contract-sensitive governance, or trust-sensitive platform state must be recorded in a structured way that preserves actor, scope, action, outcome, timing, and lineage.

This means:

- not all events are equally important, but important events must be durable and explainable
- user-facing activity views may be filtered or simplified, while internal audit records remain richer and more authoritative
- economic and governance-sensitive actions require stronger lineage than ordinary UI events
- audit records should support both operational troubleshooting and long-term institutional memory
- activity systems should make FUZE easier to inspect, not harder to reason about

This principle is one of the clearest ways FUZE turns transparency-first architecture into operational discipline.

---

## Why FUZE Needs a Formal Audit and Activity System

FUZE needs a formal audit and activity system because the platform crosses multiple kinds of state and responsibility at once.

A user may:
- sign in through linked authentication,
- join one or more workspaces,
- connect wallets,
- buy Platform Credits,
- trigger AI-heavy product actions,
- run automated workflows,
- consume paid features,
- receive or lose entitlements,
- participate in payout-related flows,
- or interact with support and governance-sensitive controls.

At the same time, operators may:
- adjust balances,
- issue refunds or credits,
- approve treasury-sensitive actions,
- rotate signers,
- publish payout-cycle references,
- change control paths,
- intervene during incidents,
- or apply support corrections affecting user-visible economic state.

Without a formal audit and activity system, the platform would face several problems:

- support teams could not explain what happened clearly
- finance and operations could not reconcile sensitive changes reliably
- governance-sensitive actions would be harder to review later
- users would have less confidence in credits, entitlements, and payout-sensitive processes
- transparency claims would weaken because important system events would not have durable lineage

FUZE therefore treats audit logging not as a compliance add-on, but as a core architectural layer that supports accountability across the entire ecosystem.

---

## Activity Records vs Audit Records

FUZE should explicitly distinguish between **activity records** and **audit records**.

### Activity Records

Activity records are operationally useful event records that may support:

- user activity history
- workspace activity feeds
- support views
- product usage histories
- operator dashboards
- workflow timelines

Activity records may be filtered, summarized, or audience-specific.

### Audit Records

Audit records are stronger, more authoritative records used for:

- economic traceability
- governance review
- dispute resolution
- security investigation
- reconciliation
- treasury-sensitive review
- payout-sensitive lineage
- incident analysis
- long-term institutional memory

Audit records should preserve more structure, stricter timestamps, and stronger actor/scope/outcome metadata than ordinary activity feeds.

### Core distinction

Not every activity record is an audit-grade record.  
Not every audit-grade record should be exposed as a user-facing activity item.

FUZE should preserve this distinction so that:
- product experiences remain usable,
- support systems remain practical,
- and trust-sensitive system history remains durable and reviewable.

---

## Canonical Event Categories

The FUZE audit and activity system should classify records into clear event categories.

At minimum, the platform should support the following categories.

### 1. Identity and Access Events
Examples:
- account created
- login succeeded or failed
- linked login added or removed
- password or authentication method changed
- session invalidated
- role assignment changed
- permission-sensitive access denied

### 2. Workspace and Membership Events
Examples:
- workspace created
- member invited
- member joined or removed
- role changed within workspace
- seat or billing owner changed
- workspace settings changed

### 3. Wallet-Aware Events
Examples:
- wallet linked
- wallet unlinked
- wallet ownership verified
- wallet-awareness status changed
- holder-rank or wallet-derived privilege recalculated where applicable

### 4. Credits and Billing Events
Examples:
- payment verified
- credits issued
- credits spent
- credits reserved
- credits released
- credits reversed
- refund applied
- subscription renewed
- plan changed
- entitlement updated

### 5. AI and Usage Events
Examples:
- AI task requested
- AI task completed
- AI task failed
- premium usage metered
- included quota consumed
- AI result released after correction
- AI route or product mode selected where relevant to billing or review

### 6. Workflow and Automation Events
Examples:
- workflow started
- step completed
- approval requested
- approval granted or denied
- scheduled job triggered
- retry occurred
- workflow failed
- workflow cancelled
- automation modified or paused

### 7. Product Domain Events
Examples:
- report generated
- configuration changed
- project object published
- important state transition occurred in a product domain
- operator-sensitive product action executed

### 8. Governance and Treasury Events
Examples:
- reserve-sensitive action proposed
- reserve action approved
- multisig execution completed
- timelock queued or executed
- policy changed
- emergency control used
- payout-cycle funding authorized

### 9. Payout and Eligibility Events
Examples:
- snapshot reference fixed
- eligibility dataset published internally
- payout cycle funded
- claim period opened
- claim executed
- payout cycle closed
- cycle correction or containment initiated

### 10. Support and Administrative Intervention Events
Examples:
- manual credit adjustment
- support compensation issued
- account restriction applied or removed
- dispute outcome recorded
- admin override applied
- recovery action performed after failure

These categories make the platform easier to reason about and allow domain-specific visibility rules to be applied consistently.

---

## Event Severity and Audit Weight

Not every event requires the same retention priority, visibility, or review expectations. FUZE should therefore support event severity or audit-weight classification.

### Informational
Low-risk events useful for timelines or support context.

Examples:
- successful login
- standard product action completion
- low-sensitivity setting change

### Operational
Events that matter for workflow, support, or product-state interpretation.

Examples:
- workflow retries
- entitlement refresh
- workspace role change
- report generation

### Economic
Events affecting money, credits, subscriptions, or value movement.

Examples:
- credits issuance
- credits reversal
- refund
- paid plan upgrade
- support compensation

### Governance-Sensitive
Events affecting policy, contract control, reserve usage, payout-sensitive execution, or trust-critical actions.

Examples:
- payout-cycle funding
- signer rotation
- timelock queue or execution
- treasury-sensitive approval
- Foundation-sensitive action

### Security-Critical
Events affecting compromise risk, emergency controls, or serious integrity threats.

Examples:
- emergency pause
- suspicious control-path action
- incident containment
- failed privileged access escalation

### Principle

Higher audit weight should correspond to:
- stronger retention guarantees,
- stricter lineage,
- tighter reviewability,
- and often narrower visibility.

This classification helps FUZE preserve both practicality and seriousness.

---

## Core Record Fields

All significant activity and audit records should share a common structural core.

At minimum, meaningful records should preserve:

- `event_id`
- `event_category`
- `event_type`
- `event_weight` or severity
- `occurred_at`
- `actor_type`
- `actor_id` or equivalent reference
- `subject_type`
- `subject_id`
- `scope_type` and `scope_id` where relevant
- `product_code` where relevant
- `workspace_id` where relevant
- `wallet_reference` where relevant
- `action_status`
- `reason_code` where applicable
- `correlation_id` or workflow linkage
- `related_event_id` where applicable
- `metadata` or structured domain payload
- `record_source`
- `visibility_class`

### Why these fields matter

A platform cannot explain complex history well if it only records “something changed.” FUZE needs enough structure to answer:

- who acted
- what changed
- in what scope
- with what outcome
- and how this event relates to earlier or later events

This is essential for support, finance, governance, and trust.

---

## Actor, Subject, and Scope Model

FUZE audit design should preserve a clear distinction between:

### Actor
The entity that performed or initiated the action.

Examples:
- user
- workspace member
- admin operator
- support staff
- automated system
- scheduled job
- governance multisig
- payout contract execution pathway

### Subject
The entity that the action affected.

Examples:
- account
- workspace
- wallet link
- credits balance
- subscription
- product object
- payout cycle
- reserve vault
- governance configuration

### Scope
The wider operating context in which the event occurred.

Examples:
- account scope
- workspace scope
- product scope
- billing scope
- governance domain
- payout cycle
- treasury category

### Principle

Separating actor, subject, and scope makes complex event history far easier to interpret. A support admin may act on a user account inside a workspace billing scope for a credits correction tied to one product. FUZE must be able to represent that correctly.

---

## Identity and Access Audit Requirements

Identity and access are foundational trust layers and therefore require strong audit capture.

At minimum, FUZE should preserve durable records for:

- account creation
- login success and failure patterns where relevant
- authentication method changes
- linked-login addition or removal
- password reset and recovery actions
- session revocation for significant cases
- role or permission changes
- account lock, suspend, or restore actions
- support/admin access-sensitive interventions

### Principle

Identity audit records are not only security artifacts. They are also essential for explaining account-state disputes, suspicious changes, or support escalations.

---

## Workspace and Organization Audit Requirements

Because FUZE is workspace-aware, workspace events require structured activity and audit treatment.

At minimum, the system should log:

- workspace creation
- ownership change
- member invite, accept, reject, remove
- workspace role change
- shared billing owner change
- seat-related changes where meaningful
- workspace-level product access changes
- important workspace configuration changes
- support/admin intervention affecting workspace state

### Principle

Workspace history is central to both support and economic clarity because many platform actions will be billed, authorized, or interpreted in workspace context rather than individual-user context alone.

---

## Credits, Billing, and Economic Audit Requirements

The credits and billing domain requires some of the strongest audit discipline in the platform.

At minimum, FUZE should preserve audit-grade records for:

- external payment verification result
- credits issuance with source rail and class
- credits spend and deduction
- credits reservation and release
- credits reversal and adjustment
- subscription activation, renewal, lapse, downgrade, upgrade
- plan entitlement changes
- refund decisions and outcomes
- chargeback or fraud-sensitive commercial events
- manual operator adjustment affecting economic state
- compensation credits or support-issued credits

### Principle

Economic events must preserve reason codes and lineage. The platform should always be able to explain why balance state changed and what policy path authorized the change.

This is especially important because Platform Credits are one of the core internal economic layers of FUZE.

---

## AI Usage and AI Action Audit Requirements

Because FUZE is an AI-powered platform ecosystem, AI-related activity requires structured tracking.

At minimum, the platform should preserve records for:

- AI task requested
- product and feature context for AI request
- route class or usage class where relevant
- AI completion or failure
- billed versus included usage outcome
- corrected or reversed AI usage
- moderation or policy-related block where applicable
- workflow-linked AI execution references

### Boundary

Not every token of model output must become an audit record. The audit goal is not raw transcript storage by default. The goal is structured traceability of meaningful AI actions, especially where they affect:
- billing,
- workflow outcome,
- product-state mutation,
- or user-visible support history.

---

## Workflow, Automation, and Job Audit Requirements

Because many FUZE products depend on async and workflow-driven behavior, audit records must preserve workflow lineage.

At minimum, the system should capture:

- workflow start
- workflow step transition
- approval requested, approved, denied
- retry, delay, or backoff events where meaningful
- job success or failure
- cancellation or abandonment
- scheduled task trigger
- manual replay or operator intervention
- final workflow outcome

### Principle

Workflow auditing is especially important where a user-visible outcome, a commercial effect, or a governance-sensitive consequence depends on multi-step background execution.

---

## Governance, Treasury, and Control-Plane Audit Requirements

Governance-sensitive domains require the strongest audit discipline in FUZE.

At minimum, the platform should preserve structured records for:

- governance action proposed
- governance action approved or rejected
- multisig approval reached
- timelock queued, executed, cancelled
- control-role changed
- signer set changed
- treasury-sensitive action authorized
- vault-sensitive action executed
- Foundation-sensitive action handled
- emergency control activated or released
- policy version changed
- payout-critical authorization event

### Principle

These records should support long-term institutional memory, not only immediate operations. The platform should be able to review governance history years later and still understand what happened structurally.

---

## Payout, Snapshot, and Eligibility Audit Requirements

The profit participation system is highly trust-sensitive and therefore requires dedicated audit-grade tracking.

At minimum, FUZE should preserve records for:

- snapshot reference fixed
- eligibility dataset version prepared
- exclusion-policy version applied
- entitlement input created
- payout-cycle funding authorized
- payout cycle funded on Base
- claim window opened
- holder claim recorded or correlated
- cycle closure
- payout-related correction or containment actions

### Principle

Payout audit history should preserve the bridge between:
- Ethereum holder truth,
- eligibility preparation,
- treasury funding,
- and Base claim execution.

This is essential to the credibility of the payout model.

---

## Support and Administrative Intervention Audit Requirements

Support and admin actions can materially affect trust even when they are operationally routine. FUZE should therefore capture strong records for interventions such as:

- manual account correction
- manual credits adjustment
- refund override
- entitlement repair
- workspace ownership correction
- dispute-resolution action
- compensation issuance
- access restoration or suspension
- product-state override
- incident-linked support action

### Principle

Admin and support actions should never become invisible history. If a human operator changed trust-sensitive state, that change should be attributable and reviewable.

---

## Correlation, Lineage, and Cross-System Traceability

One of the most important requirements of the FUZE audit system is correlation across systems.

A single user-visible outcome may involve:

- an account event
- a workspace scope
- a payment verification result
- a credits issuance event
- an AI task
- a workflow run
- a product-state mutation
- and a support follow-up

If those records cannot be correlated, the platform becomes much harder to support and audit.

### Correlation requirements

FUZE should support:
- request-level correlation IDs
- workflow IDs
- payment reference IDs
- credits mutation references
- payout-cycle references
- governance-action references
- support-case references
- incident references

### Principle

Events should not exist only as isolated facts. They should be linkable into event chains that explain how state changed across the system.

---

## Visibility Classes and Access Control

Not all activity and audit records should be visible to the same audiences.

FUZE should support visibility classes such as:

### User-Visible
Events suitable for end-user activity history.

Examples:
- credits purchase completed
- subscription changed
- report generated
- wallet linked
- payout claimed

### Workspace-Visible
Events visible to authorized workspace roles.

Examples:
- member added
- workspace plan changed
- shared credits used
- workflow executed in workspace scope

### Operator-Visible
Events visible to platform operators, support, finance, or trust teams.

Examples:
- manual adjustment records
- fraud flags
- workflow replay
- support compensation

### Governance-Restricted
Events limited to governance, treasury, security, or incident-review roles.

Examples:
- signer rotation
- timelock queue actions
- Foundation-sensitive action records
- emergency containment steps

### Principle

Strong auditability does not require universal visibility. FUZE should preserve strict access control while still ensuring that meaningful actions are recorded durably.

---

## Data Minimization and Sensitive Payload Handling

Audit quality does not require indiscriminate storage of sensitive content.

FUZE should follow data-minimization principles such as:

- storing structured event references rather than unnecessary full payloads where possible
- avoiding excessive duplication of sensitive user content
- retaining policy-relevant metadata instead of raw content by default
- separating sensitive payload storage from durable audit event identity where appropriate
- preserving secure access controls for any sensitive associated payloads

### Principle

The audit system should maximize explainability while minimizing unnecessary exposure of private content. This is especially important for workspace-private data, business data in HerHelp, workflow data in Botmad, and any sensitive financial or governance context.

---

## Retention and Durability Principles

FUZE should use differentiated retention based on event class and trust significance.

### Durable Retention
Should apply to:
- economic records
- governance-sensitive records
- payout-sensitive records
- security-critical records
- policy-change records
- support/admin intervention on trust-sensitive state

### Operational Retention
May apply to:
- lower-risk workflow or usage traces
- less sensitive activity history
- records primarily useful for product support or short- to medium-term troubleshooting

### Principle

The stronger the trust or financial relevance of the event, the stronger the case for long-lived durability. The platform should not lose the ability to explain important historical actions because they were treated like disposable logs.

---

## Correction, Immutability, and Append-Only Principles

FUZE audit records should follow an append-oriented philosophy.

### Core principle

Meaningful audit history should not be silently rewritten. If an earlier event was wrong, incomplete, or followed by correction, the preferred model is:

- preserve the original record
- add a correction or superseding record
- link the records clearly

This is especially important for:
- credits adjustments
- refund corrections
- support interventions
- payout-sensitive fixes
- governance corrections
- incident-related reinterpretation

### Why this matters

A transparency-first platform becomes weaker if critical history can be silently mutated. Append-oriented correction preserves institutional memory and makes reconciliation easier.

---

## Monitoring Integration vs Audit Distinction

FUZE should integrate monitoring and audit systems, but must keep them conceptually distinct.

### Monitoring is for:
- live health
- alerting
- anomaly detection
- operational response

### Audit is for:
- durable history
- traceability
- review
- dispute support
- reconciliation
- governance memory

### Principle

An alert is not an audit record by itself.  
An audit record is not a live alert by itself.

The platform should allow important monitoring events to generate or link to audit records where appropriate, especially for incidents, security events, and high-risk economic actions.

---

## Reporting and Transparency Reuse

The audit and activity system should provide reusable inputs to broader transparency and reporting systems.

This may include support for:

- credits activity summaries
- payout-cycle reporting
- treasury-action reporting
- governance-action reporting
- support and incident summaries
- product-usage reporting at a structured level
- investor/community trust reports in aggregated form

### Principle

The audit system is not the public reporting layer by itself, but public reporting becomes stronger when it is grounded in structured internal event history rather than ad hoc narrative preparation.

---

## Risks Addressed by the Audit and Activity Architecture

This architecture is designed to reduce several major risks.

### 1. Explainability Failure Risk
The platform cannot explain what happened when users, operators, or stakeholders ask.

### 2. Economic Reconciliation Risk
Credits, refunds, subscriptions, or payouts cannot be traced clearly enough across systems.

### 3. Governance Memory Failure Risk
Important governance actions are hard to reconstruct later.

### 4. Support Ambiguity Risk
Support and admin interventions change state without durable lineage.

### 5. Transparency Weakness Risk
The platform claims transparency but lacks strong internal evidence chains.

### 6. Incident Review Risk
Failures or security events cannot be reconstructed accurately enough for correction and prevention.

These risks matter because FUZE’s architecture depends on durable clarity across many domains, not only on correct code execution in isolated systems.

---

## Minimum Architectural Entities

At minimum, the FUZE audit and activity system should recognize the following conceptual entities:

### Event Entities
- `event_id`
- `event_category`
- `event_type`
- `event_weight`
- `occurred_at`
- `action_status`
- `reason_code`

### Actor and Scope Entities
- `actor_type`
- `actor_id`
- `subject_type`
- `subject_id`
- `scope_type`
- `scope_id`
- `workspace_id`
- `product_code`

### Correlation Entities
- `correlation_id`
- `workflow_id`
- `payment_reference`
- `credits_mutation_reference`
- `governance_action_reference`
- `payout_cycle_reference`
- `support_case_reference`
- `incident_reference`

### Visibility and Retention Entities
- `visibility_class`
- `retention_class`
- `sensitive_payload_reference`
- `audit_lineage_reference`
- `supersedes_event_id` where applicable

These are minimum conceptual entities. Detailed schema, indexing, and storage strategy are refined downstream.

---

## Open Items

The following areas are intentionally refined in downstream implementation and operational policy documents:

- exact event schema by domain
- exact retention windows by event class
- exact privacy controls for sensitive payload references
- exact user-facing activity-feed materialization rules
- exact SIEM / monitoring integration model
- exact internal review tooling for governance and payout-sensitive audit records

These do not weaken the canonical audit and activity architecture established here.

---

## Closing Summary

The FUZE audit log and activity architecture is the structured system of record for meaningful user, workspace, economic, AI, workflow, governance, treasury, and payout events across the platform. It distinguishes operational activity feeds from audit-grade records, preserves actor/subject/scope lineage, supports reconciliation and supportability, and strengthens transparency by making important platform behavior durable and explainable. By treating audit and activity as a first-class architecture layer rather than as incidental logging, FUZE reinforces one of the core promises of the ecosystem: that long-term trust should be supported by structured visibility, not by narrative alone.
