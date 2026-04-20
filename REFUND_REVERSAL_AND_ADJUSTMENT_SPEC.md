# REFUND_REVERSAL_AND_ADJUSTMENT_SPEC

## Purpose

This document defines the canonical refund, reversal, and adjustment architecture of the FUZE platform. Its purpose is to establish how the platform corrects commercial state when payments are refunded, transactions are reversed, credits must be unwound, disputes occur, support remediation is required, or billing and ledger errors need structured correction.

This specification is essential because FUZE is a multi-product platform with multiple payment rails, Platform Credits as the internal economic layer, subscriptions and usage billing, account-scoped and workspace-scoped balances, and a transparency-first operating model. In such a system, commercial correction cannot be left to informal manual edits or inconsistent product-specific behavior. It must be treated as a first-class platform capability.

Refunds, reversals, and adjustments are therefore not exceptional afterthoughts. They are part of the platform’s economic integrity model.

---

## Scope

This specification covers:

- the canonical distinction between refund, reversal, and adjustment
- correction behavior across payment rails, billing, credits, subscriptions, and usage events
- correction paths for unused and partially used Platform Credits
- refund and reversal impact on account-scoped and workspace-scoped balances
- rail-specific correction normalization
- support and finance correction pathways
- negative-balance or debt-state considerations where policy allows
- ledger and settlement effects of correction flows
- audit, reporting, and governance implications of correction behavior
- failure handling for partial or delayed correction outcomes

This specification does not define full payment verification, full credits issuance policy, or full accounting treatment. Those are refined in:

- `PAYMENT_RAILS_INTEGRATION_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `INVOICING_AND_RECEIPTS_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`

---

## Design Goals

The design goals of the refund, reversal, and adjustment model are:

1. to preserve economic integrity when platform-commercial state must be corrected
2. to distinguish clearly between external-value refund, internal-value reversal, and controlled corrective adjustment
3. to support consistent correction behavior across multiple payment rails and products
4. to make credit-related corrections scope-aware, auditable, and policy-driven
5. to support partial-use and partial-refund scenarios without ambiguity
6. to prevent silent commercial mutation through undocumented manual edits
7. to preserve reconciliation quality across payment records, billing records, credit ledgers, invoices, receipts, and chain commitment layers
8. to protect the trust model of FUZE by making correction behavior legible and governable

---

## Non-Goals

This specification is not intended to:

- guarantee that every external refund maps to a full internal unwind in all cases
- define consumer law or jurisdiction-specific refund rights in every country
- make support goodwill credits equivalent to payment refunds
- allow products to implement their own hidden correction models
- treat reversal as a substitute for proper audit and reporting
- collapse revenue accounting, treasury accounting, and credits correction into one indistinguishable process

---

## Canonical Correction Principle

The primary correction principle of FUZE is:

> when economic state changes after initial issuance, charge, or settlement, the platform must correct that state through explicit, traceable, policy-defined correction events rather than through hidden mutation of balances, invoices, or product access.

This means:

- refunds are formal return-of-value events tied to original payment or billing state
- reversals are internal unwinds of economic effects such as credits or entitlements
- adjustments are controlled corrective mutations used where refund or reversal semantics alone are insufficient
- all correction paths must preserve lineage to the original commercial event

This principle is mandatory across the ecosystem.

---

## Core Concepts

### Refund
A return of value tied to an original paid commercial event, usually originating from or reflected through a payment rail or formal billing correction.

### Reversal
An internal unwind of economic effects already represented inside FUZE, such as credits issuance, credits availability, subscription access, or usage-linked commercial state.

### Adjustment
A controlled corrective mutation used when the required fix is not captured adequately by ordinary refund or reversal semantics.

### Chargeback / Dispute
A rail-originating payment invalidation or contested state that may require downstream reversal, restriction, debt-state handling, or support review.

### Unused Credits
Credits that remain available and unconsumed in the relevant owner scope.

### Consumed Credits
Credits that have already been finally settled into product usage, subscription renewal, premium unlock, or other completed commercial action.

### Correction Scope
The account or workspace whose commercial state is being corrected.

### Correction Lineage
The traceable relationship between original commercial event, subsequent refund/reversal/adjustment actions, and resulting ledger/document state.

---

## Canonical Distinction Between Refund, Reversal, and Adjustment

FUZE must preserve a clear distinction between these three correction categories.

### Refund

A refund is the return of paid value associated with a commercial event.

Examples:
- Stripe refund for unused credits purchase
- app-store refund for a reversed subscription
- stablecoin payment returned under approved policy
- support-approved commercial refund for misbilled service

A refund is typically customer-facing and often tied to external payment rails or formal invoice state.

### Reversal

A reversal is the internal economic undoing of a prior effect.

Examples:
- removal of unused paid credits after refund
- cancellation of pending credits issuance after failed verification
- revocation of entitlement after payment reversal
- undoing of duplicate spend caused by operational error

A reversal may occur even when no external customer-facing refund is processed, such as when the platform corrects internal state before settlement is finalized.

### Adjustment

An adjustment is a controlled corrective mutation for cases that do not fit neatly into standard refund or reversal semantics.

Examples:
- support-issued compensating credits
- finance-side correction of incorrectly scoped balance
- manual reconciliation fix after operational incident
- enterprise billing correction
- policy-bounded migration of credits between scopes

These distinctions matter because a serious platform must correct state precisely, not vaguely.

---

## Correction Domains Covered by This Spec

Refund, reversal, and adjustment logic may affect the following domains:

- payment records
- invoices and receipts
- Platform Credits issuance and balances
- subscriptions
- usage-billed events
- entitlements
- seat-based access
- workspace or account billing scope
- product-visible access state
- reporting and reconciliation views
- Base commitment state for credits, where applicable

Not every correction affects every layer. But the platform must know which layers are affected and in what order.

---

## Canonical Correction Flow Model

The canonical correction flow in FUZE should follow this pattern:

1. identify the original commercial event and scope
2. classify the correction type
3. verify rail-side or policy-side authority for correction
4. determine internal effects on credits, subscription state, usage state, invoice/receipt state, and entitlements
5. apply the correction through explicit ledger and record mutations
6. update reporting, audit, and reconciliation views
7. surface resulting commercial state to affected users, workspaces, or operators where appropriate

This model exists to prevent “silent fixes” that undermine transparency and create support confusion later.

---

## Refund Model

FUZE must support structured refund handling for valid refund scenarios.

### Valid refund sources may include:

- Stripe or fiat processor refund
- Apple refund or revocation
- Google Play refund or revocation
- Telegram Stars reversal under supported rules
- stablecoin payment return under approved policy
- support-approved commercial refund
- billing correction resulting in formal overcharge refund

### Refund principles

1. A refund must reference an original commercial event.
2. A refund does not automatically imply full unwind of all downstream usage effects.
3. The platform must evaluate how much of the original economic value remains unused.
4. Refund outcomes must be auditable and documentable.
5. Refunds must remain separate from goodwill or promotional compensation.

The refund domain is customer-facing and commercial in nature. It should not be used as a vague umbrella for all correction behavior.

---

## Reversal Model

The reversal model governs how internal platform effects are unwound.

### Common reversal targets include:

- unused paid credits
- pending or reserved credits not finally consumed
- duplicated ledger effects
- subscription state not legitimately funded
- usage events that should not have settled commercially
- entitlements activated from invalidated payment paths

### Reversal principles

- reversal must be reason-coded
- reversal must preserve lineage to original issuance or charge
- reversal should not erase historical truth
- reversal must distinguish between fully unused, partially used, and fully consumed value
- reversal must be reflected in the canonical ledger and related reporting state

A reversal is not a hidden delete. It is an explicit counter-effect or unwind event.

---

## Adjustment Model

Adjustments exist for cases that ordinary refund and reversal logic cannot resolve fully.

### Adjustment scenarios may include:

- support-issued compensating credits
- migration of credits between scopes after billing correction
- manual reconciliation repair after incident
- correcting wrong workspace assignment
- correcting over-consumption or under-consumption due to product bug
- enterprise settlement adjustment
- post-review correction after fraud or dispute handling

### Adjustment principles

- adjustments must be tightly permission-bounded
- adjustments must be reason-coded and actor-attributed
- adjustments should preserve both original state and corrected lineage
- large or sensitive adjustments may require approval workflow
- adjustments must never become an undocumented escape hatch for economic mutation

FUZE needs adjustments because real systems encounter messy cases. But the presence of an adjustment path must strengthen control, not weaken it.

---

## Unused vs Consumed Value Logic

One of the most important rules in FUZE correction handling is the distinction between unused and consumed value.

### Unused value

Unused value generally includes:
- available paid credits not yet spent
- reserved credits not yet settled
- prepaid subscription or service period not yet substantially consumed, where policy supports proration or refund
- add-on capacity not yet used

Unused value is the cleanest category for refund-linked reversal.

### Consumed value

Consumed value generally includes:
- credits already finally settled into product use
- completed premium AI actions
- completed reports
- completed workflow executions
- already activated and consumed service periods where policy says value was delivered

Consumed value is not generally equivalent to unused balance. If correction is still required, it may need adjustment, compensation, partial refund, or dispute-handling logic rather than simple reversal.

This distinction is essential to prevent commercial confusion and to maintain credibility in the internal credits economy.

---

## Partial Refund and Partial Reversal Handling

FUZE must support partial correction outcomes.

### Examples of partial scenarios

- only part of a credits package remains unused
- a subscription period is partially consumed before cancellation
- a usage bundle was partially used
- a multi-line-item invoice requires refund of only one component
- a workspace add-on was partially activated

### Partial-correction principles

- the platform must be able to isolate corrected portions from unaffected portions
- partial correction must preserve lineage to the original event
- invoices, receipts, credits ledger, and entitlements must remain internally consistent after correction
- partial correction should not force false all-or-nothing outcomes when a more precise outcome is available

This is necessary in a multi-product ecosystem where commercial events are often composite rather than simple.

---

## Rail-Specific Correction Behavior

Different payment rails create different correction patterns. FUZE must normalize these patterns without losing their meaning.

### Stripe / fiat processors

Common cases:
- direct refund
- partial refund
- dispute / chargeback
- failed capture or expired payment intent

The platform must normalize processor-side correction state into internal refund/reversal behavior.

### Stablecoin rail

Common cases:
- mistaken deposit handling
- overpayment or underpayment
- manual return under approved support policy
- chain-observed payment invalidation under platform interpretation

The platform must treat chain events as raw facts and still apply platform policy to determine internal correction meaning.

### Apple / Google

Common cases:
- purchase revocation
- subscription cancellation affecting future renewal state
- refund after entitlement was granted

The platform must normalize store-side state into internal entitlement, credit, and documentation outcomes.

### Telegram Stars

Common cases:
- purchase reversal
- provider-side invalidation
- normalization mismatch

The platform must support internal unwind or compensation logic according to verified platform event state.

### FUZE token input path

If FUZE token is accepted as payment input under policy, correction handling must preserve:
- conversion reference
- normalized platform value basis
- internal credits lineage
- any unwind logic approved by policy

The platform must not improvise token-input correction rules at product level.

---

## Subscription Correction Behavior

Refund, reversal, and adjustment logic must also support recurring commercial state.

### Subscription correction scenarios

- renewal paid but entitlement failed
- subscription activated against wrong scope
- duplicate renewal event
- cancellation with future unused period treatment
- plan migration requiring corrective rebill or credit
- app-store subscription revocation
- past-due recovery correction

### Subscription correction principles

- subscription state is distinct from payment state
- correction may affect invoice state, receipt state, entitlement state, and credits state differently
- subscription history must remain auditable
- support correction must not silently rewrite history

A recurring billing platform must be able to explain how subscription corrections happened and why.

---

## Usage-Billing Correction Behavior

Usage charging requires its own correction discipline.

### Usage correction scenarios

- duplicate usage record
- failed job that should not have been charged
- reserved credits not consumed after cancellation
- overmetered product action
- underbilled usage requiring compensating charge under policy
- product bug causing improper premium action charges

### Usage correction principles

- idempotency and mutation safety must be primary defenses
- correction should prefer explicit compensating entries over hidden edits
- product teams may propose correction logic, but shared billing and ledger systems remain canonical
- completed usage should not be reversed casually without reason and audit visibility

This is especially important for AI-native products where usage may be high-frequency and async.

---

## Entitlement Correction Behavior

Commercial correction may require entitlement correction.

### Examples

- refund of unused premium add-on -> entitlement removed
- reversal of invalid subscription renewal -> access restricted
- support compensation credits -> entitlement restored or extended if policy says so
- partial downgrade -> entitlement scope narrowed but not removed entirely

### Entitlement principles

- entitlements are not the same as payment state
- entitlements are not the same as credit balances
- correction to payment or credits may change entitlements, but only through explicit commercial logic
- product-visible access must reconcile to entitlement truth after correction

The platform must avoid the common failure mode where money is corrected but access state remains wrong, or vice versa.

---

## Negative Balance and Debt-State Policy

In some chargeback, fraud, or invalidated-payment scenarios, the platform may need to handle cases where value has already been consumed and cannot simply be reversed from unused balance.

### Possible policy options

- restrict account or workspace until balance is corrected
- create negative-balance state
- record debt-state for future offset
- block new usage until remediation
- escalate to support or fraud review

### Principle

Negative-balance or debt-state behavior should be explicit, policy-defined, and limited to cases where reversal of unused balance is not sufficient.

This is a sensitive area and must not be improvised product-by-product.

---

## Support, Finance, and Admin Correction Paths

Some correction cases require operational intervention.

### Possible operators

- support operator
- finance operator
- billing specialist
- abuse/risk reviewer
- platform admin under policy
- governance-aware operator for high-impact cases

### Operational correction rules

- the operator must be permission-authorized
- every action must be reason-coded
- affected records must preserve before/after lineage
- high-impact corrections may require dual control or approval
- manual correction must write explicit platform records, not silent direct edits

The platform must make operator intervention visible enough to support trust and later review.

---

## Relationship to Credits Ledger and Settlement

Refunds, reversals, and adjustments must flow through the canonical credits-ledger model where credits are affected.

### This means:

- unused paid credits refund -> reversal ledger entries
- reservation release -> explicit release ledger events
- corrective issuance -> adjustment ledger entries
- scope reassignment -> migration/reassignment ledger events
- chargeback response -> explicit correction lineage

The correction layer does not bypass the ledger. It instructs or triggers formal ledger behavior.

This is essential because the credits ledger is the canonical internal economic record of FUZE.

---

## Relationship to Invoices and Receipts

Commercial correction may also affect billing documents.

### Possible document effects include:

- invoice canceled or voided
- invoice adjusted
- receipt reversed
- refund receipt issued
- credit note or adjustment note created
- replacement invoice issued

### Principle

The invoicing layer documents the correction. It does not replace the underlying correction logic in payment, billing, or credits systems.

Document correction must preserve historical traceability rather than silently overwriting commercial history.

---

## Relationship to Base Commitment State

Where corrected credits were committed or intended to be committed on Base, the platform must also manage correction/commitment alignment.

### Examples

- platform-side reversal pending chain commitment update
- correction created after original entry already committed
- failed commitment for adjustment entry
- reconciliation required between platform correction state and chain-visible commitment state

### Principle

Correction state and commitment state must be separately visible.  
A valid platform correction may exist before chain commitment completes.  
A chain event may need reconciliation into the platform correction record.

This layered model preserves both commercial truth and operational transparency.

---

## Audit Requirements

The refund, reversal, and adjustment system must be strongly auditable.

At minimum, audit trails should exist for:

- refund requested
- refund approved or rejected
- refund completed
- reversal created
- reversal completed
- adjustment created
- adjustment approved or rejected where relevant
- operator intervention
- entitlement correction
- invoice/receipt correction
- scope correction
- commitment reconciliation related to correction flows

Correction without auditability is incompatible with FUZE’s transparency-first model.

---

## Reporting Requirements

The platform should support reporting and explainability for:

- refunds by rail and product
- reversals by reason
- adjustments by operator, product, and scope
- chargeback/dispute correction rates
- unused vs consumed refund ratios
- support compensation issuance
- correction impact on credits balances
- correction impact on subscriptions and entitlements
- correction/commitment reconciliation health

These views are necessary for support, finance, abuse operations, and long-term platform trust.

---

## Minimum Data Model Requirements

At minimum, the correction model must support semantic representation of:

### Correction Record
- correction_id
- correction_type (refund, reversal, adjustment)
- related original commercial event type
- related original commercial event ID
- owner_scope_type
- owner_scope_id
- status
- reason_code
- created_at
- actor_type / actor_id
- policy or approval reference where applicable

### Refund Record
- refund_id
- related payment record
- related invoice/receipt reference where applicable
- refund_amount
- rail_type
- refund_status
- processed_at where relevant

### Reversal Record
- reversal_id
- related ledger entry or issuance/spend reference
- reversal_amount
- affected balance class/scope
- status
- created_at

### Adjustment Record
- adjustment_id
- adjustment_subtype
- target domain(s)
- amount or structural effect
- operator/system actor
- approval metadata where applicable
- created_at

These are minimum semantic requirements. More detailed schemas are refined downstream.

---

## Edge Cases and Failure Handling

### External refund succeeds but internal reversal lags
The platform must preserve recoverable pending-correction state rather than silently losing the linkage.

### Reversal attempted after credits already fully consumed
The system must move into policy-defined debt-state, compensation, or restricted-account handling rather than pretending unused balance still exists.

### Duplicate refund callback
Correction flows must be idempotency-aware and avoid duplicate downstream reversal or document effects.

### Wrong workspace corrected after several product actions
Correction may require combined balance, entitlement, and document lineage rather than one simple mutation.

### Support issues goodwill credits instead of true refund
The platform must represent this as compensating adjustment or bonus issuance, not as a false payment refund.

### Chargeback arrives after entitlement granted and used
The system must preserve the original usage history while applying policy-defined correction, restriction, or debt-state handling.

### Base correction commitment fails
Platform-side correction truth remains valid, but commitment state must surface failure and reconciliation requirements.

---

## Open Items

The following areas are intentionally refined in downstream or operational specifications:

- exact proration rules for recurring subscriptions
- exact debt-state behavior if adopted
- exact chargeback response policy by rail
- exact approval thresholds for large manual adjustments
- exact user-facing refund UX and status presentation
- exact reconciliation jobs for correction-to-commitment alignment

These do not weaken the canonical correction model established here.

---

## Closing Summary

The FUZE refund, reversal, and adjustment system is the structured correction layer of the platform economy. It ensures that payment refunds, unused-credit unwinds, billing fixes, entitlement corrections, support remediation, and operational repairs occur through explicit, traceable, policy-defined actions rather than through silent mutation. By distinguishing clearly between refunds, reversals, and adjustments, and by connecting those actions to payment records, billing state, Platform Credits, documents, and Base commitment behavior, FUZE preserves commercial integrity across a complex multi-product ecosystem. This is essential to the trustworthiness of the platform’s shared internal economy.
