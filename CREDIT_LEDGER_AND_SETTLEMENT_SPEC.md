# CREDIT_LEDGER_AND_SETTLEMENT_SPEC

## Purpose

This document defines the canonical credit ledger and settlement architecture of the FUZE platform. Its purpose is to establish how Platform Credits are recorded, how credit balances are represented across account and workspace scopes, how issuance and spend events become ledger truth, how reservation and settlement states behave, how on-chain commitment on Base relates to platform-side records, and how reconciliation and auditability are preserved across the internal economic layer.

This specification is foundational because Platform Credits are the internal consumption layer of FUZE, and the ledger is the canonical record of how that internal value changes over time. Without a disciplined ledger and settlement model, the platform would not be able to support subscriptions, usage billing, refunds, reversals, reporting, or on-chain transparency in a coherent way.

---

## Scope

This specification covers:

- the canonical credits-ledger model for FUZE
- balance representation and ledger-based truth
- account-scoped and workspace-scoped credit ownership
- ledger event categories
- reservation, authorization, consumption, release, and settlement behavior
- relationship between platform ledger state and Base on-chain commitment state
- reconciliation requirements
- idempotency and mutation safety principles
- audit, reporting, and control requirements for credits-ledger operations
- failure handling across ledger and settlement flows

This specification does not redefine high-level credit policy, payment verification, or full refund policy. Those are refined in:

- `PLATFORM_CREDITS_SPEC.md`
- `PAYMENT_RAILS_INTEGRATION_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `REFUND_REVERSAL_AND_ADJUSTMENT_SPEC.md`
- `BASE_PLATFORM_CREDITS_LAYER_SPEC.md`

---

## Design Goals

The design goals of the credits-ledger and settlement model are:

1. to provide one canonical source of truth for Platform Credits mutations
2. to make every credit mutation attributable, auditable, and reconcilable
3. to support both simple immediate-spend flows and more complex reserve/settle flows
4. to preserve scope clarity between account-owned and workspace-owned balances
5. to connect platform-side commercial truth with Base commitment truth without collapsing their roles
6. to support idempotent and failure-resilient credit mutation behavior
7. to make reporting, finance, support, and operational review feasible at platform scale
8. to preserve clear distinction between ledger truth, payment truth, and payout truth

---

## Non-Goals

This specification is not intended to:

- redefine what a Platform Credit is at a policy level
- make on-chain commitment the only business-level source of truth
- allow products to keep private balance systems outside the shared ledger
- treat invoice or receipt documents as ledger truth
- blur credits-ledger state into treasury or profit accounting
- define full smart contract implementation details for Base credits contracts

---

## Canonical Ledger Principle

The primary ledger principle of FUZE is:

> every Platform Credit mutation must be represented as an explicit ledger event against a defined owner scope, with a traceable cause, a deterministic state transition, and a reconcilable relationship to downstream commitment and reporting layers.

This means:

- balances are derived from ledger truth, not from silent mutable counters alone
- every increase or decrease in credits must have a source event
- ledger entries must preserve reason and attribution
- products consume ledger outcomes, not private product-local balance assumptions
- on-chain commitment and reporting must reconcile to ledger state rather than invent parallel economic meaning

This principle is mandatory across the ecosystem.

---

## Why the Credits Ledger Matters

The credits ledger is one of the most important economic systems in FUZE because Platform Credits are the internal economic layer of the ecosystem.

The ledger matters for several reasons.

First, it creates **economic traceability**. The platform can determine where credits came from, why they were issued, how they were spent, whether they were reserved or consumed, and whether any reversal or adjustment occurred later.

Second, it creates **commercial coherence**. Products across the FUZE ecosystem can use one shared balance logic rather than maintaining incompatible private spending systems.

Third, it creates **operational safety**. A proper ledger makes it easier to reason about retries, pending states, failed jobs, partial settlement, support corrections, and reconciliation against on-chain commitment records.

Fourth, it creates **transparency**. Because the credits system is an important part of FUZE’s trust model, the platform needs a structured way to explain how internal purchasing power moved through the system.

For these reasons, the ledger is not only an implementation detail. It is one of the platform’s core economic control systems.

---

## Core Concepts

### Credit Balance Context
The scoped balance state for a canonical owner, either an account or a workspace.

### Ledger Entry
A canonical record representing one mutation or state transition in the Platform Credits system.

### Ledger Event Type
The semantic category of the mutation, such as issuance, reservation, spend, release, reversal, adjustment, or expiry.

### Reservation
A provisional hold on credits pending later consumption or release.

### Consumption
The final settlement of reserved credits or direct immediate spend.

### Release
The removal of a reservation without final consumption.

### Adjustment
A controlled correction to ledger state for support, finance, or operational reconciliation reasons.

### Commitment State
The relationship between platform-side ledger events and Base on-chain credits commitment records where applicable.

### Owner Scope
The owner of a credit balance, typically an account or a workspace.

### Economic Reference
The upstream reason or business event linked to a ledger mutation, such as payment record, invoice, usage action, subscription renewal, refund, or support correction.

---

## Canonical Ledger Model

The FUZE credits system must use an append-oriented canonical ledger model.

This means ledger truth should be represented primarily through entries, not only through mutable balance snapshots. Balance views may be materialized for performance or UX, but they are derivative of the ledger.

### Why an append-oriented model is required

An append-oriented ledger improves:

- traceability
- auditability
- replayability
- support investigation
- reconciliation
- failure recovery
- policy review

If the system only stored current balances without high-quality mutation history, many important commercial and operational questions would become difficult or impossible to answer.

### Canonical rule

Every balance state visible to users, products, support, reporting, or reconciliation processes must be explainable through ledger entries and their derived current-state projection.

---

## Balance Ownership Model

Credits balances must always belong to an explicit scope.

### Supported owner scopes

- account scope
- workspace scope

### Scope rules

- no credits balance may exist without a defined owner scope
- scope type and scope ID must be attached to each ledger entry
- account-scoped and workspace-scoped balances must remain distinguishable
- products must charge against an explicitly resolved scope
- support or migration operations affecting scope must remain explicit and auditable

This scope clarity is critical because FUZE supports both individual and collaborative product usage.

---

## Ledger Event Categories

At minimum, the credits-ledger system must support the following ledger event categories.

### 1. Issuance
Represents creation of credits through verified payment, approved reward, or approved adjustment path.

Examples:
- verified Stripe credits purchase
- stablecoin-funded credits issuance
- reward campaign bonus
- support compensation issuance

### 2. Reservation
Represents credits temporarily held for a future action but not yet finally consumed.

Examples:
- long-running AI job authorization
- queued workflow reservation
- batch report generation reservation

### 3. Consumption / Spend
Represents final credits deduction for a completed commercial action.

Examples:
- subscription renewal
- metered AI usage
- premium feature unlock
- report completion

### 4. Release
Represents reservation removal when work is canceled, fails, or settles below reserved amount.

### 5. Reversal
Represents removal or negation of credits effect tied to refund, payment invalidation, or policy-defined correction.

### 6. Adjustment
Represents a controlled corrective mutation not reducible to normal spend or reversal semantics.

### 7. Expiry
Represents expiration of eligible bonus or restricted credits under policy.

### 8. Migration / Reassignment
Represents controlled internal movement between scopes or contexts where policy allows.

### 9. Commitment Marker
Represents the internal recording of relation to Base commitment state where on-chain commitment is performed.

These categories are semantically distinct and must remain distinguishable in ledger truth.

---

## Balance Derivation Model

Balances should be derived from ledger state into operational balance views.

At minimum, the platform must be able to derive:

- total issued balance by class
- available balance
- reserved balance
- consumed balance
- expired balance
- pending reversal or review-sensitive state where relevant
- committed versus uncommitted view where Base commitment is part of the model

### Balance-view principle

The platform may maintain materialized balance tables for performance and UX, but these views are subordinate to the ledger. If a balance projection diverges from the canonical ledger, reconciliation must restore the projection rather than reinterpret the ledger.

---

## Reservation and Settlement Model

FUZE should support both direct-immediate spend and two-step reservation/settlement flows.

### Direct-immediate spend
Used where the commercial action is simple and final at the time of charging.

Examples:
- immediate premium unlock
- one-step report purchase
- subscription renewal where no intermediate hold is required

### Reservation / settlement flow
Used where work may take time, may fail, may be canceled, or may complete with variable cost.

Examples:
- long-running AI tasks
- complex automation chains
- queued product operations
- batch compute workflows

### Recommended reservation flow

1. determine charge scope and expected cost or charge basis
2. place reservation hold
3. run work
4. settle final cost
5. either consume reserved credits, partially consume and release remainder, or release full reservation

### Reservation principles

- reserved balance must be visible as distinct from available balance
- a reservation is not final spend
- release behavior must be explicit
- reservation expiry or cleanup must be policy-defined
- settlement transitions must be auditable

This model improves fairness and economic correctness in AI-native and workflow-heavy products.

---

## Settlement Semantics

Settlement is the process by which provisional economic state becomes final economic state.

### Settlement types

#### Immediate settlement
Credits move directly from available to consumed in one step.

#### Reservation-backed settlement
Credits first move into reserved state, then are finalized into consumed state.

#### Corrective settlement
Used when later system facts require adjustment to prior assumptions.

### Settlement requirements

At minimum, settlement must preserve:

- owner scope
- event reason
- product attribution where relevant
- relation to upstream business action
- deterministic outcome even under retry conditions
- reconciliation visibility if chain commitment is involved

Settlement must never produce “mystery deductions.” Every final commercial effect must be attributable to a known path.

---

## Relationship to Payment Records

The credits ledger is downstream from payment normalization but not identical to payment records.

### Payment records answer:
- did value enter through an approved rail?
- was the event verified?
- what amount and source were recognized?
- what correction or dispute state later emerged?

### Credits ledger answers:
- did verified value become platform purchasing power?
- in what class and scope?
- was that value later spent, reserved, released, reversed, or expired?

This distinction matters because a verified payment may exist without immediate final credit availability if commitment or review is pending, and a payment correction may later affect ledger state without erasing the original payment record.

Payment truth and ledger truth must therefore remain linked but distinct.

---

## Relationship to Subscription and Usage Billing

The ledger is also downstream from billing logic but distinct from billing state.

### Billing determines:
- what should be charged
- when a charge should occur
- whether usage is included or overage
- whether subscription renewal is due
- which product event should map to a spend action

### Ledger records:
- the resulting credits mutation
- the balance impact
- the relation to the relevant billing action

This means the ledger does not define pricing policy. It records the economic result of pricing and billing decisions under platform rules.

---

## Relationship to Base On-Chain Commitment

FUZE credits are operationally committed on Base, but platform-side ledger truth remains critical.

### Platform ledger owns:
- issuance policy context
- commercial and product reason mapping
- scope semantics
- reservation and settlement orchestration
- reporting and support interpretation

### Base layer owns:
- committed credits state where the platform writes to chain
- chain-visible event and commitment trace
- operational public record for committed credits behavior

### Commitment principle

Base commitment should be treated as an important operational and transparency layer, but not as a replacement for the platform ledger.

The platform must be able to explain:

- what ledger entry led to which commitment action
- whether commitment is pending, confirmed, failed, retried, or superseded
- how platform-side balance state and chain-side committed state reconcile

This dual-layer model is one of the most important architectural rules of FUZE’s credits system.

---

## Commitment State Model

Where on-chain commitment is performed, the platform should track commitment state explicitly.

Recommended states include:

- `not_required`
- `pending_commitment`
- `committed`
- `commitment_failed`
- `commitment_replaced`
- `reconciling`

### State meanings

#### Not Required
The ledger event does not require Base commitment.

#### Pending Commitment
The ledger event is valid platform-side economic state, but chain commitment is not yet complete.

#### Committed
The corresponding chain operation is accepted as complete.

#### Commitment Failed
The platform-side event exists, but the chain write failed and requires retry or review.

#### Commitment Replaced
A newer or corrected chain relation superseded the original attempt.

#### Reconciling
The platform is actively resolving platform/chain state consistency.

These states allow the platform to distinguish commercial truth from technical completion state without losing control of either.

---

## Reconciliation Requirements

The credits ledger must support formal reconciliation processes.

### Required reconciliation relationships include:

- payment records to issuance entries
- subscription/usage billing events to spend entries
- reversal events to corrected ledger state
- platform ledger entries to Base commitment records
- balance projections to canonical ledger history
- reporting views to underlying ledger truth

### Reconciliation goals

- detect missing or duplicated entries
- detect commitment lag or failure
- detect stale balance projections
- detect unsupported manual changes
- detect mismatch between reported and canonical state

### Principle

A platform with credits as the internal economic layer must be able to explain and repair inconsistencies. Reconciliation is therefore part of the architecture, not a background convenience.

---

## Idempotency and Mutation Safety

The credits-ledger system must be idempotency-aware.

This is especially important because many ledger-affecting flows are triggered by:

- webhooks
- async workers
- retries
- long-running workflows
- payment callbacks
- scheduled billing jobs
- chain interaction retries

### Idempotency principles

- a commercial event should not create duplicate ledger effects
- retries should be safe
- duplicate provider events should not double-issue or double-spend
- product workflows must use deterministic mutation keys or equivalent safeguards
- reconciliation must detect and isolate duplication if it still occurs

### Mutation safety principle

No ledger mutation should occur through undocumented side paths. All writes to the ledger must pass through controlled service contracts and audit-friendly mutation logic.

---

## Support Corrections and Manual Intervention

Some ledger corrections may require support, finance, or admin intervention.

### Allowed intervention categories may include:

- issuance correction
- spend correction
- migration/reassignment correction
- reversal repair
- scope repair
- stuck commitment handling
- post-incident commercial remediation

### Intervention rules

- all manual or semi-manual corrections must be reason-coded
- operator identity or system actor must be recorded
- high-impact corrections should be approval-bounded where policy requires
- correction lineage must remain visible
- support intervention must not erase historical truth; it should add explicit corrective truth

This is essential because the credits system is part of the platform trust surface.

---

## Audit Requirements

The credits-ledger and settlement layer must generate strong audit visibility.

At minimum, audit trails should exist for:

- issuance created
- reservation placed
- reservation released
- spend settled
- reversal applied
- adjustment created
- expiry processed
- scope reassignment performed
- commitment attempted
- commitment failed
- commitment reconciled
- support/admin correction executed

Because Platform Credits sit at the center of the FUZE commercial model, auditability is mandatory rather than optional.

---

## Reporting Requirements

The ledger system must support reporting and explainability for:

- account credits history
- workspace credits history
- product-attributed credits usage
- issuance by class and source
- spend by product and reason
- reservation and release behavior
- expiry totals
- adjustments and reversals
- pending versus committed state
- reconciliation and settlement health

These reporting outputs are necessary for users, operators, finance workflows, support workflows, and transparency-oriented platform reporting.

---

## Minimum Data Model Requirements

At minimum, the ledger and settlement model must support semantic representation of:

### Credit Balance Projection
- owner_scope_type
- owner_scope_id
- credit_class
- available_amount
- reserved_amount
- consumed_amount where materialized
- expired_amount where relevant
- last_reconciled_at

### Ledger Entry
- ledger_entry_id
- owner_scope_type
- owner_scope_id
- event_type
- credit_class
- amount
- direction
- economic_reference_type
- economic_reference_id
- product_reference where applicable
- created_at
- actor_type / actor_id
- idempotency key or equivalent mutation key
- commitment_state

### Reservation Record where applicable
- reservation_id
- related ledger entry or business action
- reserved amount
- status
- created_at
- settled_at / released_at where relevant

### Commitment Reference
- commitment_reference_id
- related ledger entry
- chain/network reference
- tx hash or equivalent chain reference where applicable
- commitment_state
- committed_at / failed_at where relevant

These are minimum semantic requirements. Detailed schema design is refined elsewhere.

---

## Edge Cases and Failure Handling

### Payment verified but issuance settlement partially fails
The platform must preserve a recoverable pending state with explicit auditability rather than silently losing the event.

### Reservation placed but worker crashes before settlement
Reserved state must be discoverable, recoverable, and releasable under policy.

### Duplicate webhook or async retry
Idempotency controls must prevent duplicate issuance or spend.

### Base commitment succeeds but platform callback lags
The platform must support reconciliation from chain-visible state back into commitment status without duplicating ledger effects.

### Platform ledger written but Base commitment fails
Commitment state must remain visibly failed or pending, and retry/review logic must be available.

### Scope misassignment discovered later
Correction must occur through explicit migration/reassignment or adjustment lineage, not silent overwrite.

### Product cache displays wrong available balance
The product cache is non-canonical. The ledger-derived balance projection remains authoritative.

---

## Open Items

The following areas are intentionally refined in downstream specifications:

- exact Base contract write model
- exact reservation timeout policy
- exact negative-balance or debt-state handling if adopted
- exact reconciliation cadence and job design
- exact reporting materialization model
- exact event payload design for ledger-related events

These do not weaken the canonical ledger and settlement model established here.

---

## Closing Summary

The FUZE credit ledger and settlement system is the canonical record of how Platform Credits move through the ecosystem. It provides append-oriented, scope-aware, reason-coded economic truth for issuance, reservation, consumption, release, reversal, adjustment, expiry, and controlled reassignment. It connects payment normalization, subscriptions and usage billing, product consumption, Base commitment, reconciliation, reporting, and auditability into one coherent internal economic system. This structure is essential because Platform Credits are not merely balances. They are the shared internal consumption layer of the FUZE platform, and that layer must be trustworthy, explainable, and operationally resilient.
