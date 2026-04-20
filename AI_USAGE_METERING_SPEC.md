# AI_USAGE_METERING_SPEC

## Purpose

This document defines the canonical AI usage metering architecture of the FUZE platform. Its purpose is to establish how AI-related activity is measured, classified, attributed, charged, reported, and controlled across the FUZE ecosystem.

This specification is foundational because FUZE is an AI-powered multi-product platform. AI execution is not a minor supporting feature. It is part of the platform’s core operating layer. That means FUZE must be able to measure AI usage consistently across products, scopes, plans, execution modes, and provider paths. Without a disciplined metering model, the platform would lose commercial clarity, cost visibility, policy control, and cross-product comparability.

---

## Scope

This specification covers:

- the canonical role of AI usage metering in the FUZE platform
- the distinction between AI execution, AI usage records, billing outcomes, and product outcomes
- metering units and metering classes
- scope-aware attribution across accounts, workspaces, and products
- included usage, premium usage, and overage-aware AI usage handling
- linkage between AI usage, Platform Credits, subscriptions, and entitlements
- policy and operational controls around metered AI activity
- audit, reporting, reconciliation, and observability requirements for AI usage records
- failure handling and correction behavior for AI usage metering

This specification does not fully define provider-specific billing contracts, prompt registries, or detailed product pricing formulas. Those are refined in:

- `AI_ORCHESTRATION_SPEC.md`
- `MODEL_ROUTING_AND_CONTEXT_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
- `REFUND_REVERSAL_AND_ADJUSTMENT_SPEC.md`

---

## Design Goals

The design goals of the FUZE AI usage metering model are:

1. to provide one shared AI usage accounting layer across all FUZE products
2. to make AI activity commercially measurable without collapsing AI usage into raw provider invoices
3. to support included usage, premium usage, overage, and policy-gated AI execution
4. to preserve scope clarity between personal, workspace, and product-level usage
5. to support both low-latency and long-running AI work in one coherent metering model
6. to make AI usage attributable, auditable, and reconcilable
7. to support cost discipline and product-level monetization without fragmenting AI billing by product
8. to preserve clear boundaries between AI usage records, credits ledger records, and product output records

---

## Non-Goals

This specification is not intended to:

- treat raw provider token counts or raw API charges as the only meaningful business unit
- require every AI task to be monetized identically
- let products invent hidden AI billing systems outside platform metering
- make AI usage metering a substitute for entitlement checks
- define every provider-specific cost formula in this file
- assume that every AI action must always result in final chargeable usage
- collapse AI product value into provider cost passthrough

---

## Canonical Metering Principle

The primary principle of FUZE AI usage metering is:

> every meaningful AI action should generate a structured platform usage record that identifies what happened, for whom it happened, in what product context it happened, under what commercial class it happened, and whether it produced chargeable, included, released, or corrected usage outcomes.

This means:

- AI usage is measured at the platform level
- metering records business-relevant usage, not only provider internals
- scope and product attribution are mandatory
- AI usage may be chargeable, included, reserved, released, or corrected
- metering must remain linked to billing and credits without becoming identical to them

This principle is essential because FUZE treats AI as shared platform infrastructure rather than as isolated product plumbing.

---

## Why AI Usage Metering Matters

AI usage metering matters because FUZE products depend on AI in ways that create both value and cost.

Products across the ecosystem may use AI for:

- summarization
- recommendation
- classification
- extraction
- generation
- planning
- workflow support
- transformation
- report drafting
- decision support
- structured assistant behavior

These actions vary in latency, cost, value, and user expectations. Some should be included within subscription tiers. Some should consume premium capacity. Some should deduct Platform Credits directly. Some should run in low-cost modes, while others may require higher-capability execution.

Without a shared AI usage metering layer, several problems emerge:

- products lose comparability
- premium AI value is hard to charge consistently
- AI cost exposure becomes harder to manage
- support cannot easily explain what was consumed
- workspace billing becomes opaque
- usage caps and entitlement rules become inconsistent

A shared metering model solves this by giving FUZE one common language for AI consumption across all products.

---

## Core Concepts

### AI Usage Event
A business-relevant occurrence of AI execution or AI execution attempt that should be tracked by the platform.

### AI Usage Record
The structured platform record representing a measured AI usage event.

### Metering Unit
The business unit by which AI activity is counted for platform purposes.

### Commercial Class
The monetization treatment of the AI usage event, such as included, premium, overage, restricted, internal-only, or non-billable recovery.

### Usage Scope
The commercial owner of the AI usage, typically an account or a workspace.

### Usage Status
The lifecycle or financial status of the AI usage record, such as pending, metered, charged, included, released, failed, corrected, or reversed.

### Settlement Link
The reference connecting an AI usage record to credits deduction, usage bundle consumption, subscription-included quota consumption, or other billing outcome.

### Product Attribution
The product, feature, or workflow context that requested or consumed the AI action.

---

## Canonical Role of AI Usage Metering

The AI usage metering layer exists to do the following:

1. identify meaningful AI usage events
2. classify those events under platform-commercial rules
3. attribute usage to account, workspace, product, and task type
4. link usage to included limits, overage logic, or credits-backed settlement
5. preserve usage traceability for audit, reporting, support, and optimization
6. support correction when AI tasks fail, release, or require compensation
7. provide consistent data across multiple AI-powered products

This layer is not intended to define AI output quality or AI model routing. Those belong to orchestration and product domains. Metering measures and classifies usage in the platform economy.

---

## Metering Units

FUZE should support platform-level metering units that reflect business meaning rather than exposing only raw provider-side values.

### Recommended metering-unit categories

#### 1. Action-Based Unit
One completed or attempted AI action counts as one billable or included unit.

Examples:
- one summary request
- one generation request
- one classification task
- one workflow recommendation run

This is often the most user-understandable unit.

#### 2. Tiered Action Unit
AI actions are counted differently depending on complexity class.

Examples:
- standard AI action
- premium AI action
- advanced reasoning action
- heavy generation action

This is useful where products offer multiple levels of AI capability.

#### 3. Batch / Job Unit
A large asynchronous or batch AI task is counted as a job or workload unit.

Examples:
- one report-generation job
- one batched transformation run
- one large document synthesis task

#### 4. Included Usage Consumption Unit
A usage record consumes quota rather than immediately deducting credits.

Examples:
- one included monthly generation
- one included daily suggestion run
- one workspace AI seat allowance consumption event

#### 5. Internal Non-Billable Unit
Some AI actions may be measured operationally but not charged directly to the user.

Examples:
- internal moderation assist
- support-side AI helper
- platform-internal enrichment pass

### Metering principle

The platform may track raw provider usage details for cost control, but the canonical AI usage meter should reflect business-relevant units suitable for billing, reporting, and user understanding.

---

## Metering Classes

AI usage in FUZE should be classified into explicit commercial classes.

### 1. Included Usage
AI activity covered by a subscription, plan tier, bundle, or included quota.

### 2. Premium Usage
AI activity allowed only under premium entitlement or higher-cost plan class.

### 3. Overage Usage
AI activity exceeding included quota and therefore subject to additional credits deduction or paid usage logic.

### 4. Restricted Usage
AI activity allowed only in bounded product, policy, or plan contexts.

### 5. Internal Operational Usage
AI activity measured for platform cost visibility but not exposed as ordinary user-billable usage.

### 6. Non-Billable Failure / Released Usage
AI activity that was initiated or reserved but ultimately did not settle as billable usage due to failure, cancellation, or policy-defined release.

### 7. Corrected Usage
AI activity whose original commercial treatment was changed due to platform correction, support action, or reconciliation outcome.

These classes allow FUZE to support sophisticated AI monetization while preserving one shared commercial structure.

---

## Scope-Aware Attribution

Every AI usage record must belong to an explicit scope.

### Supported scopes

- account scope
- workspace scope

### Scope requirements

- every metered AI event must resolve to a billing owner
- account-scoped usage and workspace-scoped usage must remain distinguishable
- products must not silently meter workspace activity as personal activity
- reporting and billing must preserve scope identity
- correction behavior must also preserve scope lineage

This is especially important in FUZE because products such as HerHelp and Botmad naturally operate in multi-user or workspace environments, while other products may support both personal and collaborative usage models.

---

## Product Attribution Rules

Every AI usage record must also be attributable to a product and, where meaningful, to a product feature or workflow class.

### Required attribution examples

- QTB signal explanation
- AIMM operational analysis
- ZAGA utility recommendation
- AIE event summarization
- HerHelp transformation or generation
- Botmad workflow assistance
- platform-internal support or reporting usage

### Attribution principle

Products may define task meaning, but the platform metering system must normalize attribution enough to support:

- product-level billing visibility
- product-level AI demand analysis
- cost and monetization comparison across products
- support and correction investigation
- future pricing optimization

A metering system with no product attribution would weaken one of the biggest advantages of running multiple AI-powered products on one platform.

---

## AI Usage Lifecycle

At minimum, AI usage records should support the following lifecycle states:

- `created`
- `pending_settlement`
- `included`
- `charged`
- `released`
- `failed_non_billable`
- `corrected`
- `reversed`
- `disputed` where applicable

### State meanings

#### Created
The platform recognized a meaningful AI usage event and created the usage record.

#### Pending Settlement
The record exists, but final billing or included-usage treatment is not yet complete.

#### Included
The event consumed included usage or subscription-covered capacity.

#### Charged
The event resulted in Platform Credits deduction or other finalized paid usage outcome.

#### Released
Reserved or provisional commercial treatment was removed because the task failed, was canceled, or settled below the provisional amount.

#### Failed Non-Billable
The AI action failed in a way that should not produce billable usage.

#### Corrected
The original usage treatment was changed through a controlled correction path.

#### Reversed
A previously settled usage event was formally reversed under policy.

#### Disputed
The event entered a review state because of billing, fraud, or correctness concerns.

This lifecycle helps distinguish technical execution from commercial outcome.

---

## Relationship to AI Orchestration

AI usage metering is downstream from AI orchestration but tightly linked to it.

### AI orchestration determines:
- what task was requested
- what model class was used
- what execution mode applied
- whether fallback occurred
- what output state resulted

### AI usage metering determines:
- whether the task counts as metered usage
- how the usage is classified commercially
- which scope and product own it
- whether it consumes included usage, credits, or other entitlement capacity
- whether it is billable, released, corrected, or non-billable

This separation matters because orchestration is about execution control. Metering is about economic and reporting treatment.

---

## Relationship to Billing, Credits, and Entitlements

The AI usage metering layer must integrate directly with subscriptions, Platform Credits, and entitlement logic.

### Usage metering may interact with billing in several ways

#### Included Quota Consumption
An AI usage record may consume included monthly or periodic usage under an active plan.

#### Credits-Backed Charging
An AI usage record may deduct Platform Credits once the action is validated and settled.

#### Premium Gate Enforcement
An AI usage record may only be allowed if the account or workspace has access to premium AI features or premium tier routing.

#### Overage Handling
An AI usage record may exceed included capacity and therefore trigger credits deduction, block, upgrade prompt, or policy-defined overage behavior.

### Important architectural rule

AI usage metering does not replace entitlement checks. It operates together with them. A task should not become chargeable simply because it was requested. It must still satisfy entitlement and policy rules.

---

## Reservation and Deferred Settlement for AI Usage

Some AI tasks may justify reservation or delayed settlement behavior.

This is especially relevant for:
- long-running async jobs
- heavy report generation
- batch synthesis
- multi-stage workflow tasks
- expensive advanced reasoning routes

### Reservation-aware AI metering may support:

1. create usage intent
2. place reservation against included quota or Platform Credits where policy requires
3. run AI task
4. finalize actual settlement
5. release reserved value if the task fails or is canceled

### Principle

High-cost or long-running AI tasks should not rely only on optimistic immediate final charging where failure recovery is likely. Reservation-aware behavior improves fairness and commercial correctness.

The detailed reservation mechanics may live in billing and credits layers, but AI usage metering must preserve linkage between the AI task and the resulting settlement state.

---

## Included Usage and Quota Model

FUZE should support included AI usage within plans or bundles.

Examples include:
- included monthly AI actions
- included report generation volume
- included premium assistant requests up to a threshold
- included workspace AI seat allowance
- included Botmad automation recommendations per period

### Included usage principles

- included usage should still be metered
- included usage should remain visible in reporting
- included usage should preserve product attribution
- hitting quota should trigger explicit policy behavior
- included usage consumption should not be invisible simply because no credits were deducted

This is important because AI usage can carry real platform cost even when bundled into a plan.

---

## Overage and Premium AI Logic

AI usage beyond included quotas or beyond standard plan class should be handled explicitly.

### Overage examples
- additional AI generations after monthly quota is exhausted
- heavy async AI jobs above included plan limits
- premium structured outputs outside baseline plan allowance

### Premium AI examples
- advanced reasoning route
- high-cost model class
- large context job
- premium report synthesis
- workflow-critical AI assistance tier

### Policy options may include
- direct Platform Credits deduction
- premium usage bundle consumption
- upgrade prompt
- task block until funding exists
- alternate downgraded routing option

The important point is that premium or overage AI usage must remain policy-governed and observable, not improvised inside product code.

---

## Raw Provider Usage vs Platform Usage

FUZE must preserve the distinction between raw provider usage and platform AI usage.

### Raw provider usage may include:
- token counts
- API request counts
- latency and retry details
- provider-specific billing units
- provider-side error codes

### Platform AI usage includes:
- business action performed
- product attribution
- user/workspace ownership
- included vs charged classification
- settlement outcome
- entitlement context
- correction lineage

### Why this distinction matters

Raw provider data is useful for cost analysis and infrastructure operations, but it is not sufficient as the canonical platform business meter. Users do not buy “provider token counts.” They buy product experiences, AI actions, plan tiers, and premium capabilities.

FUZE therefore needs both layers, but the platform usage record is the canonical commercial abstraction.

---

## Correction, Release, and Reversal Behavior

AI usage metering must support correction behavior.

### Common scenarios include:
- AI task failed after provisional reservation
- task timed out and should not be charged
- output validation failed and no billable result should be recognized
- duplicate AI usage record created by retry
- support remediation for improperly charged premium action
- product bug caused overmetering
- credits settlement succeeded but usage attribution was wrong

### Metering correction principles

- correction must preserve lineage to original usage record
- released usage must not be silently deleted
- charged usage requiring correction should use explicit corrected or reversed state
- support-issued compensation should remain distinct from reversal
- reporting should be able to distinguish normal usage from corrected usage

This is important because AI tasks often have more complex failure and retry behavior than simple CRUD operations.

---

## Internal Operational AI Usage

FUZE may also meter internal operational AI usage separately from user-billable usage.

Examples include:
- support operator AI assist
- internal moderation workflows
- reporting enrichment
- internal summarization for ops teams
- platform-side content categorization

### Principles

- internal operational usage should still be measurable
- internal usage should be distinguishable from customer usage
- internal usage should not accidentally deduct user or workspace Platform Credits
- internal usage should still support cost visibility and optimization analysis

Because FUZE is a platform company, internal AI use can become material over time. It should therefore remain visible, even if monetization treatment differs.

---

## Policy and Control Implications

AI usage metering is part of the platform control surface.

Sensitive policy areas include:
- premium AI gating
- included usage definitions
- overage behavior
- reservation rules
- correction authority
- product-specific metering class definitions
- enterprise or partner exceptions
- internal-use classification

### Control principles

- products should not have unrestricted power to alter shared AI metering semantics
- changes affecting commercial meaning should be platform-governed
- support and finance correction powers should be permission-bounded
- metering policy changes should remain auditable
- high-impact pricing or usage-policy changes should be treated as platform-level decisions

This matters because AI metering directly affects trust, customer experience, and ecosystem economics.

---

## Observability Requirements

The AI usage metering layer should support observability into:

- AI usage volume by product
- included vs charged usage mix
- workspace vs account usage distribution
- premium vs standard AI usage volume
- release and failure rates
- correction rates
- average cost pressure by usage class
- quota exhaustion patterns
- overage frequency
- high-cost task concentration

This observability is essential because AI is both a product capability and a platform cost center.

---

## Audit Requirements

At minimum, audit visibility should exist for:

- premium AI usage charges
- AI usage corrections and reversals
- support/admin metering overrides
- workspace-billed AI usage in sensitive contexts
- high-impact enterprise or partner exceptions
- repeated metering anomalies
- release of reserved AI usage after failure
- large-scale metering policy changes

Not every simple included usage event needs the same audit depth, but the platform must be able to explain commercially meaningful AI usage outcomes when needed.

---

## Reporting Requirements

The platform should support reporting and explainability for:

- AI usage by product
- AI usage by scope
- included vs charged usage
- premium and overage usage
- workspace AI consumption patterns
- monthly or periodic quota consumption
- correction and reversal patterns
- internal operational AI consumption
- cost-to-usage comparison inputs
- monetization effectiveness by AI feature class

These reports are valuable for:
- support teams
- finance and pricing teams
- product strategy
- capacity planning
- transparency and investor communication where relevant

---

## Minimum Data Model Requirements

At minimum, the AI usage metering layer should support semantic representation of:

### AI Usage Record
- ai_usage_record_id
- ai_task_id or execution reference
- product_reference
- feature_reference or task_class
- owner_scope_type
- owner_scope_id
- account_id where relevant
- workspace_id where relevant
- usage_meter_type
- commercial_class
- status
- quantity or usage unit count
- created_at
- settled_at where relevant

### AI Usage Settlement Link
- ai_usage_record_id
- related credits ledger reference where applicable
- related subscription/included quota reference where applicable
- related invoice or billing reference where applicable
- settlement outcome

### AI Usage Policy Context
- plan or tier reference
- premium eligibility flag
- quota rule reference
- overage rule reference
- policy version

### AI Usage Correction Record
- related usage record
- correction type
- correction reason
- actor/system source
- created_at
- reversal/release linkage where relevant

These are minimum semantic requirements. Detailed schemas are refined downstream.

---

## Edge Cases and Failure Handling

### AI task completes technically but output fails product validation
The platform must decide whether the event is billable, retryable, or releasable according to policy. Technical completion alone should not force automatic final charge.

### Same AI action retried due to user impatience and network delay
The platform should preserve business-action lineage so metering and billing do not double-charge the same intended action.

### Workspace member triggers AI usage but workspace lost entitlement before settlement
Sensitive or high-cost usage may require revalidation at settlement time according to policy.

### Included usage counter is stale in UI
Canonical metering and billing state must prevail over cached views.

### Premium AI action downgraded to cheaper fallback route
The metering record should reflect actual execution class and actual commercial treatment under policy.

### AI task reserved credits but worker crashed before finalization
Metering must remain linked to release, retry, or recovery flow rather than leaving ambiguous charge state.

### Support grants goodwill credits after metering bug
That should be represented as a separate corrective or compensating event, not as silent erasure of original usage history.

---

## Open Items

The following areas are intentionally refined in downstream specifications:

- exact per-product AI meters
- exact included quota shapes by plan
- exact reservation thresholds for heavy AI workloads
- exact provider-cost mapping for internal analysis
- exact enterprise or partner exceptions
- exact user-facing usage-display conventions

These do not weaken the canonical AI usage metering model established here.

---

## Closing Summary

The FUZE AI usage metering layer is the shared commercial measurement system for AI activity across the ecosystem. It turns AI execution into structured platform usage records that can be classified, attributed, settled, corrected, and reported consistently across products, scopes, and plans. By separating AI execution from AI metering, and by keeping AI metering distinct from both raw provider usage and credits-ledger truth, FUZE creates a disciplined model for charging, including, and governing AI-powered value across a growing multi-product platform.
