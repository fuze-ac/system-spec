# REFUND_REVERSAL_AND_ADJUSTMENT_SPEC

## Title
FUZE Refund, Reversal, and Adjustment Specification

## Document Metadata

- Document Name: `REFUND_REVERSAL_AND_ADJUSTMENT_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Version: 1.1.0
- Effective Date: 2026-04-21
- Last Updated: 2026-04-21
- Reviewed On: 2026-04-21
- Document Owner: FUZE Platform Commercial Corrections Architecture
- Approval Authority: FUZE Platform Architecture and Governance Authority
- Review Cadence: Quarterly or upon material change to payment-rail normalization, Platform Credits semantics, credit-ledger settlement posture, subscriptions and usage billing, invoicing/receipts, dispute handling, fraud/risk controls, or chain commitment correction posture
- Governing Layer: Platform core / shared commercial infrastructure / refund, reversal, and adjustment
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, payments engineering, billing engineering, credits and ledger engineering, finance operations, support operations, fraud/risk operations, security engineering, audit/compliance, product engineering, API design, data engineering, platform operations, implementation-contract authors
- Primary Purpose: Define the canonical FUZE correction layer for refunds, reversals, and adjustments so payment-originating return-of-value events, internal economic unwinds, and operator- or policy-driven corrective mutations remain explicitly typed, scope-aware, lineage-preserving, auditable, and safe across payment records, subscriptions, usage billing, Platform Credits, credits-ledger state, invoices/receipts, entitlements, and Base commitment linkage without silently rewriting earlier commercial truth
- Primary Upstream References:
  - `REFINED_SYSTEM_SPEC_INDEX.md`
  - `DOCS_SPEC_INDEX.md`
  - `SYSTEM_SPEC_INDEX.md`
  - `API_SPEC_INDEX.md`
  - `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
  - `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
  - `PLATFORM_ARCHITECTURE_SPEC.md`
  - `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
  - `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
  - `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
  - `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
  - `PLATFORM_CREDITS_SPEC.md`
  - `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
  - `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
  - `PAYMENT_RAILS_INTEGRATION_SPEC.md`
  - `INVOICING_AND_RECEIPTS_SPEC.md`
  - `PAYMENT_FRAUD_AND_ABUSE_PREVENTION_SPEC.md`
  - `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
  - `AI_USAGE_METERING_SPEC.md`
  - `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
- Primary Downstream Dependents:
  - `PAYMENT_FRAUD_AND_ABUSE_PREVENTION_SPEC.md`
  - `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
  - `AI_USAGE_METERING_SPEC.md`
  - `REFUND_REVERSAL_ADJUSTMENT_API_SPEC.md`
  - finance, reconciliation, reporting, support/control-plane, and remediation workflows
  - product-specific exception-handling integrations
- Supersedes: Earlier or weaker FUZE correction writeups that did not clearly separate refund truth, reversal truth, adjustment truth, document supersession, ledger correction, and commercial exception handling
- Superseded By: Not yet known
- Related Decision Records: Not yet known
- Canonical Status Note: This document is the canonical FUZE specification for refund, reversal, and adjustment semantics. Downstream APIs, products, workers, dashboards, support tooling, payment integrations, billing adapters, ledger consumers, and document systems MUST NOT reinterpret provider refunds, disputes, voids, support goodwill, ledger counter-entries, or product-local compensations as substitutes for the explicit correction model defined here.
- Implementation Status: Normative architecture baseline; downstream API, service, runtime, event, audit, and control-plane contracts must conform
- Approval Status: Drafted for refined-system inclusion; formal approval record not yet attached
- Change Summary:
  - hardened the typed separation between refund, reversal, and adjustment
  - clarified unused-versus-consumed value handling, partial correction logic, and debt/restriction posture for invalidated consumed value
  - strengthened linkage across payment records, billing truth, invoices/receipts, credits-ledger entries, entitlements, and Base commitment state
  - expanded implementation-contract guardrails for correction lineage, approval-bounded operator actions, idempotency, and no-destructive-rewrite behavior

## Purpose

This specification defines the canonical refund, reversal, and adjustment architecture of the FUZE platform.

Its purpose is to establish how FUZE corrects commercial state when payments are refunded, transactions are reversed, credits must be unwound, disputes occur, support remediation is required, or billing and ledger errors need structured correction. FUZE is a multi-product platform with multiple payment rails, Platform Credits as the internal economic layer, subscriptions and usage billing, account-scoped and workspace-scoped balances, and a transparency-first operating model. In such a system, commercial correction cannot be left to informal manual edits or inconsistent product-specific behavior. It must be treated as a first-class platform capability. fileciteturn38file0L1-L13

Refunds, reversals, and adjustments are therefore not exceptional afterthoughts. They are part of the platform’s economic integrity model. fileciteturn38file0L1-L13

## Scope

This specification governs:

- the canonical distinction between refund, reversal, and adjustment
- correction behavior across payment rails, billing, credits, subscriptions, usage events, invoices/receipts, entitlements, and Base commitment state
- correction paths for unused, partially used, and already consumed Platform Credits
- refund and reversal impact on account-scoped and workspace-scoped balances
- rail-specific correction normalization
- support, finance, fraud/risk, and admin correction pathways
- negative-balance, deficit, or debt-state considerations where policy permits
- ledger and settlement effects of correction flows
- audit, reporting, and governance implications of correction behavior
- failure handling for partial, delayed, or conflicting correction outcomes

This specification does not define in full depth:

- payment verification protocols
- full Platform Credits issuance policy
- full subscription pricing or proration logic
- full invoice-rendering or receipt-rendering behavior
- full accounting treatment
- exact legal/compliance refund rights in every jurisdiction
- exact fraud-review staffing or threshold policy
- exact chain-write batching or Base correction contract internals

Those concerns are refined in adjacent specifications and MUST remain compatible with this document. fileciteturn38file0L14-L31

## Out of Scope

This specification is explicitly out of scope for:

- proving actor identity or session validity
- deciding final actor authorization for protected actions
- defining the semantic meaning of Platform Credits
- defining final credits-ledger append mechanics
- defining recurring billing truth in full depth
- defining invoice/document truth in full depth
- defining token or treasury semantics
- defining final accounting-book or payout truth

## Design Goals

1. Preserve economic integrity when platform-commercial state must be corrected.
2. Distinguish clearly between external-value refund, internal-value reversal, and controlled corrective adjustment.
3. Support consistent correction behavior across multiple payment rails and products.
4. Make credit-related and scope-related corrections explicit, auditable, and policy-driven.
5. Support unused, partially used, and already consumed value cases without ambiguity.
6. Prevent silent commercial mutation through undocumented manual edits.
7. Preserve reconciliation quality across payment records, billing records, ledger entries, invoices, receipts, and chain commitment layers.
8. Make exception handling legible enough for support, finance, fraud, audit, and governance review.
9. Support corrective precision rather than forcing all-or-nothing outcomes.
10. Keep products from inventing local correction semantics that drift away from platform truth.

## Non-Goals

This specification is not intended to:

- guarantee that every external refund maps to a full internal unwind in all cases
- make support goodwill credits equivalent to payment refunds
- allow products to implement hidden correction models
- treat reversal as a substitute for audit or reporting
- collapse revenue accounting, treasury accounting, billing correction, credits correction, and document correction into one indistinguishable process
- let admin or support tools mutate balances or documents silently without correction records

## Core Principles

### 1. Canonical Correction Principle
When economic state changes after initial issuance, charge, or settlement, the platform MUST correct that state through explicit, traceable, policy-defined correction events rather than through hidden mutation of balances, invoices, or product access. Refunds are formal return-of-value events tied to original payment or billing state, reversals are internal unwinds of economic effects such as credits or entitlements, and adjustments are controlled corrective mutations used where refund or reversal semantics alone are insufficient. All correction paths MUST preserve lineage to the original commercial event. fileciteturn38file0L52-L66

### 2. Typed-Correction Principle
FUZE MUST preserve a clear distinction between refund, reversal, and adjustment. A serious platform must correct state precisely, not vaguely. fileciteturn38file0L90-L118

### 3. Lineage-Preservation Principle
Correction is additive and lineage-preserving. It MUST NOT silently overwrite earlier commercial truth. Later corrective actions must supersede or counteract earlier state explicitly. fileciteturn38file0L196-L228 fileciteturn38file1L72-L99

### 4. Unused-vs-Consumed Principle
Unused value and consumed value are not equivalent. Unused value is the cleanest category for refund-linked reversal; consumed value generally requires more constrained policy such as adjustment, partial refund, debt-state handling, or restricted-account treatment. fileciteturn38file0L229-L257

### 5. Scope-Aware Correction Principle
Every correction MUST bind to the correct account or workspace correction scope. Wrong-scope correction is a commercial integrity defect and MUST fail closed or route to explicit remediation.

### 6. Downstream-Propagation Principle
A valid correction may require coordinated downstream effects on payment records, subscriptions, usage state, invoices/receipts, Platform Credits, credits-ledger state, entitlements, product-visible access, reporting, and Base commitment alignment. The platform must know which layers are affected and in what order. fileciteturn38file0L120-L134

### 7. Correction-Is-Not-Document-Only Principle
Invoicing and receipt changes document corrections; they do not replace the underlying correction logic in payment, billing, or credits systems. fileciteturn38file0L374-L385 fileciteturn38file2L210-L262

### 8. Correction-Is-Not-Ledger-Bypass Principle
Refunds, reversals, and adjustments that affect credits MUST flow through the canonical credits-ledger model. The correction layer does not bypass the ledger; it instructs or triggers formal ledger behavior. fileciteturn38file0L360-L373 fileciteturn38file1L1-L20

### 9. External-Rail Normalization Principle
Different payment rails create different correction patterns, but FUZE MUST normalize those patterns without losing their meaning. Chain facts and provider facts are raw signals; platform correction meaning remains platform-owned. fileciteturn38file0L277-L319

### 10. Auditability Principle
Correction without auditability is incompatible with FUZE’s transparency-first model. fileciteturn38file0L399-L412

## Canonical Definitions

### Refund
A return of value tied to an original paid commercial event, usually originating from or reflected through a payment rail or formal billing correction. It is typically customer-facing and often tied to external rails or formal invoice state. fileciteturn38file0L90-L103

### Reversal
An internal economic undoing of a prior effect already represented inside FUZE, such as credits issuance, credits availability, subscription access, or usage-linked commercial state. A reversal may occur even when no external customer-facing refund is processed. fileciteturn38file0L103-L110

### Adjustment
A controlled corrective mutation for cases that do not fit neatly into standard refund or reversal semantics, such as support-issued compensating credits, incorrectly scoped balances, manual reconciliation fixes, enterprise billing correction, or policy-bounded migration of credits between scopes. fileciteturn38file0L110-L118

### Chargeback / Dispute
A rail-originating payment invalidation or contested state that may require downstream reversal, restriction, debt-state handling, or support review. fileciteturn38file0L74-L89

### Unused Value
Commercial value that remains available and unconsumed, such as available paid credits, reserved credits not yet settled, prepaid service period not yet substantially consumed where policy supports proration or refund, or add-on capacity not yet used. fileciteturn38file0L229-L239

### Consumed Value
Commercial value that has already been finally settled into product usage, subscription renewal, premium unlock, completed reports, completed workflow executions, or already activated and consumed service periods where policy says value was delivered. fileciteturn38file0L240-L257

### Correction Scope
The account or workspace whose commercial state is being corrected. fileciteturn38file0L74-L89

### Correction Lineage
The traceable relationship between original commercial event, subsequent refund/reversal/adjustment actions, and resulting ledger, document, entitlement, and reporting state. fileciteturn38file0L74-L89

### Debt State
A policy-defined post-invalidated-value posture in which the platform records, offsets, restricts, or otherwise manages consumed value that cannot be unwound solely from unused balance. fileciteturn38file0L335-L347

## Truth Class Taxonomy

Downstream implementations MUST preserve the following truth classes.

### 1. Payment-Rail Truth
Verified external payment input, normalized payment state, disputes, refunds, revocations, and external-source correction signals owned by the payment rails integration domain. fileciteturn37file10

### 2. Billing Truth
Recurring commitments, usage-rated charge state, seat commercial state, included usage posture, overage posture, billing owner, and billing-scope responsibility owned by the subscriptions and usage billing domain. fileciteturn37file15

### 3. Platform Credits Semantic Truth
The canonical meaning of credits, classes, ownership scopes, issuance categories, spend semantics, and transfer restrictions owned by `PLATFORM_CREDITS_SPEC.md`. fileciteturn37file16

### 4. Credit Ledger and Settlement Truth
Authoritative mutation lineage, balance derivation, reservation state, corrective settlement, and commitment linkage owned by the credits-ledger domain. fileciteturn38file1L1-L20

### 5. Invoice / Receipt Truth
Canonical billing-document truth owned by the invoicing and receipts domain, including invoice, receipt, source reference, delivery, supersession, and remediation state. fileciteturn38file2L1-L20

### 6. Entitlement Truth
Product and capability eligibility owned by the entitlement domain. Commercial correction may change entitlements, but only through explicit commercial logic rather than direct document or payment mutation. fileciteturn38file0L321-L334

### 7. Correction Truth
The typed correction layer owned by this domain, including correction records, correction type, status, reason code, approval metadata, operator lineage, and downstream-effect coordination. This truth does not replace upstream or downstream domain truth; it coordinates explicit exception-case correction over them.

### 8. Derived Read-Model Truth
Dashboards, support summaries, exported reports, user-facing refund status views, correction analytics, and cached product views derived from canonical records.

## Architectural Position in the Spec Hierarchy

This document sits below:

- `REFINED_SYSTEM_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `PAYMENT_RAILS_INTEGRATION_SPEC.md`
- `INVOICING_AND_RECEIPTS_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`

and above:

- `PAYMENT_FRAUD_AND_ABUSE_PREVENTION_SPEC.md`
- `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
- `AI_USAGE_METERING_SPEC.md`
- `REFUND_REVERSAL_ADJUSTMENT_API_SPEC.md`
- finance, reconciliation, reporting, support/control-plane, and remediation workflows
- product-specific exception-handling integrations

## System Boundaries

This document governs only the following platform-owned boundaries:

- canonical distinction between refund, reversal, and adjustment
- typed correction lifecycle and approval posture
- correction-scope attachment rules
- unused-versus-consumed value handling
- correction effects across payment, billing, credits, ledger, document, entitlement, and Base commitment layers
- partial correction handling
- debt-state or restriction-state entry posture where policy allows
- minimum API, event, audit, security, and operational rules for correction behavior

It does not govern:

- payment verification details in full depth
- general credits issuance policy
- subscription pricing or proration policy in full depth
- exact invoice-rendering or refund-note rendering behavior
- exact legal/compliance refund-rights wording
- exact chain-write implementation for Base correction commitments

## Adjacent Boundaries

### Payment Rails Domain
Owns normalized payment-origin truth. Refunds, chargebacks, revocations, and rail anomalies originate here, but this domain does not own internal unwind behavior by itself. The correction layer classifies and propagates those signals into platform-commercial consequences. fileciteturn37file14

### Billing Domain
Owns subscription and usage-billing truth. Correction may change invoice state, receipt state, entitlement state, and credits state differently; subscription history must remain auditable, and support correction must not silently rewrite history. fileciteturn38file0L320-L333

### Credits and Ledger Domains
Own semantic credits truth and authoritative ledger truth. The correction layer does not bypass the ledger. It triggers formal ledger behaviors such as reversal entries, release events, adjustment entries, and migration/reassignment records. fileciteturn38file0L360-L373 fileciteturn38file1L72-L99

### Invoicing and Receipts Domain
Owns billing-document truth. Commercial correction may affect invoice or receipt state, but documents record correction consequences rather than replace correction logic. fileciteturn38file0L374-L385 fileciteturn38file2L210-L262

### Entitlement Domain
Owns product-visible access eligibility. Payment or credits correction may change entitlements, but only through explicit commercial logic, and product-visible access must reconcile to entitlement truth after correction. fileciteturn38file0L321-L334

### Fraud / Abuse Domain
Owns risk thresholds, holds, review, and dispute-handling escalation. The correction layer must provide structured, explicit inputs into those controls and consume their higher-order restrictions.

## Conflict Resolution Rules

When correction-related layers disagree, FUZE MUST resolve conflicts in the following order unless a higher-order policy explicitly overrides it:

1. canonical original commercial event and its scope
2. canonical typed correction classification
3. active policy hold, fraud/risk hold, dispute state, review, or restriction posture
4. canonical ledger and document correction lineage
5. Base commitment linkage and reconciliation posture
6. derived dashboards, support summaries, exports, product caches, and UI history

Additional rules:

- a payment refund does not automatically imply full internal unwind of all downstream usage effects fileciteturn38file0L151-L158
- correction MUST distinguish fully unused, partially used, and fully consumed value fileciteturn38file0L174-L180
- support goodwill credits MUST NOT be represented as false payment refunds fileciteturn37file1L165-L173
- valid platform correction may exist before chain commitment completes, and chain failure does not erase correction truth fileciteturn37file1L113-L121
- later corrections MUST preserve historical truth rather than silently overwrite it fileciteturn38file0L196-L228

## Default Decision Rules

When ambiguity exists and no narrower policy-specific rule is available, the platform MUST apply the following defaults:

- default to explicit typed correction rather than vague “fix” behavior
- default to no direct balance or document rewrite
- default to no full unwind when consumed-value posture is ambiguous
- default to explicit review for broad-scope or high-impact corrections
- default to ledger-linked corrective entries over product-local fixes
- default to preserving both original and corrected lineage
- default to restriction, hold, debt-state, or future-offset posture rather than pretending invalidated consumed value is still unused where policy permits
- default to canonical backend correction records over support notes or provider UI

## Roles / Actors / Entities

### Correction Requester
The actor or system path proposing a refund, reversal, or adjustment.

### Correction Subject
The account or workspace whose commercial state is being corrected.

### Original Commercial Event
The payment, billing, usage, issuance, settlement, or document event that correction references.

### Refund Authority
The approved pathway or operator role allowed to authorize or execute return-of-value actions tied to original payment or billing state.

### Reversal Authority
The approved pathway or operator role allowed to unwind internal economic effects through canonical ledger-linked behavior.

### Adjustment Authority
The tightly bounded operator or automated control path allowed to execute corrective mutations not reducible to ordinary refund or reversal semantics.

### Correction Reviewer
A fraud, finance, support, governance-aware, or platform operator who reviews high-impact corrections where policy requires.

## Ownership Model

- the refund, reversal, and adjustment domain owns typed correction records, correction classification, correction status, approval posture, and downstream-effect orchestration semantics
- payment, billing, credits, ledger, document, and entitlement domains remain owners of their own canonical state
- products may request eligible corrections or consume corrected downstream state, but do not define correction semantics
- admin/control-plane may approve or remediate under controlled policy but do not own underlying commercial truth
- fraud/risk systems may impose holds or review, but typed correction behavior remains explicit in this domain

## Authority / Decision Model

### Correction Authority
This domain decides whether the event is a refund, reversal, or adjustment and what downstream domains must be touched.

### Upstream Authority
Payment and billing domains decide whether the original commercial event, payment invalidation, or overcharge condition exists that justifies correction.

### Downstream Authority
Credits-ledger, document, and entitlement domains apply domain-owned corrective consequences once correction authority and scope are established.

### Operator Authority
Support, finance, billing specialists, abuse/risk reviewers, platform admins, or governance-aware operators may intervene only through policy-bounded, reason-coded pathways. High-impact corrections may require dual control or approval. fileciteturn38file0L348-L359

## Canonical Correction Categories

### Refund
A formal return-of-value event tied to an original paid commercial event. Valid refund sources may include:
- Stripe or fiat processor refund
- Apple refund or revocation
- Google Play refund or revocation
- Telegram Stars reversal under supported rules
- stablecoin payment return under approved policy
- support-approved commercial refund
- billing correction resulting in formal overcharge refund fileciteturn38file0L143-L158

Refund principles:
1. a refund MUST reference an original commercial event
2. a refund does not automatically imply full unwind of downstream usage effects
3. the platform MUST evaluate how much original value remains unused
4. refund outcomes MUST be auditable and documentable
5. refunds MUST remain separate from goodwill or promotional compensation fileciteturn38file0L151-L158

### Reversal
An internal unwind of economic effects already represented inside FUZE. Common reversal targets include unused paid credits, pending or reserved credits not finally consumed, duplicated ledger effects, subscription state not legitimately funded, usage events that should not have settled commercially, and entitlements activated from invalidated payment paths. fileciteturn38file0L160-L180

Reversal principles:
- reversal MUST be reason-coded
- reversal MUST preserve lineage to original issuance or charge
- reversal SHOULD NOT erase historical truth
- reversal MUST distinguish between fully unused, partially used, and fully consumed value
- reversal MUST be reflected in the canonical ledger and related reporting state fileciteturn38file0L170-L180

### Adjustment
A controlled corrective mutation for cases that refund and reversal cannot resolve fully, including support-issued compensating credits, migration of credits between scopes after billing correction, manual reconciliation repair after incident, wrong workspace assignment, under- or over-consumption caused by product bugs, enterprise settlement adjustment, or post-review correction after fraud or dispute handling. fileciteturn38file0L182-L199

Adjustment principles:
- adjustments MUST be tightly permission-bounded
- adjustments MUST be reason-coded and actor-attributed
- adjustments SHOULD preserve both original state and corrected lineage
- large or sensitive adjustments MAY require approval workflow
- adjustments MUST never become an undocumented escape hatch for economic mutation fileciteturn38file0L191-L199

## Canonical Correction Flow Model

The canonical correction flow in FUZE MUST follow this pattern:

1. identify the original commercial event and scope
2. classify the correction type
3. verify rail-side or policy-side authority for correction
4. determine internal effects on credits, subscription state, usage state, invoice/receipt state, and entitlements
5. apply the correction through explicit ledger and record mutations
6. update reporting, audit, and reconciliation views
7. surface resulting commercial state to affected users, workspaces, or operators where appropriate

This model exists to prevent silent fixes that undermine transparency and create support confusion later. fileciteturn38file0L135-L142

## Unused vs Consumed Value Logic

This is one of the most important rules in FUZE correction handling.

### Unused Value
Unused value generally includes:
- available paid credits not yet spent
- reserved credits not yet settled
- prepaid subscription or service period not yet substantially consumed where policy supports proration or refund
- add-on capacity not yet used

Unused value is the cleanest category for refund-linked reversal. fileciteturn38file0L229-L239

### Consumed Value
Consumed value generally includes:
- credits already finally settled into product use
- completed premium AI actions
- completed reports
- completed workflow executions
- already activated and consumed service periods where policy says value was delivered

Consumed value is not generally equivalent to unused balance. If correction is still required, it may need adjustment, compensation, partial refund, or dispute-handling logic rather than simple reversal. fileciteturn38file0L240-L257

### Partial Correction Rules
FUZE MUST support partial correction outcomes. The platform must be able to isolate corrected portions from unaffected portions, preserve lineage to the original event, keep invoices, receipts, credits ledger, and entitlements internally consistent after correction, and avoid false all-or-nothing outcomes when a more precise outcome is available. fileciteturn37file6

## Rail-Specific Correction Behavior

Different payment rails create different correction patterns, and FUZE must normalize them without losing their meaning.

### Fiat Processors
Direct refunds, partial refunds, disputes, and failed captures or expired payment intents must normalize into internal refund/reversal behavior. fileciteturn38file0L259-L269

### Stablecoin Rail
Mistaken deposit handling, overpayment or underpayment, manual return under approved support policy, and chain-observed payment invalidation require platform policy interpretation rather than raw chain fact equality with correction truth. fileciteturn38file0L269-L276

### Apple / Google
Purchase revocation, cancellation affecting future renewal state, and refund after entitlement was granted must normalize into internal entitlement, credits, and documentation outcomes. fileciteturn38file0L277-L288

### Telegram Stars
Purchase reversal, provider-side invalidation, and normalization mismatches must support explicit internal unwind or compensation logic. fileciteturn38file0L289-L294

### FUZE Token Input Path
If accepted under policy, correction handling must preserve conversion reference, normalized platform value basis, internal credits lineage, and approved unwind logic. Product-level improvisation is forbidden. fileciteturn38file0L295-L302

## Subscription, Usage, and Entitlement Correction Behavior

### Subscription Corrections
Refund, reversal, and adjustment MUST support renewal paid but entitlement failed, subscription activated against wrong scope, duplicate renewal event, cancellation with future unused-period treatment, plan migration requiring corrective rebill or credit, app-store subscription revocation, and past-due recovery correction. Subscription state is distinct from payment state; correction may affect invoice, receipt, entitlement, and credits state differently; and subscription history must remain auditable. fileciteturn37file6

### Usage Corrections
Usage charging correction scenarios include duplicate usage records, failed jobs that should not have been charged, reserved credits not consumed after cancellation, overmetered product action, underbilled usage requiring compensating charge under policy, and product bugs causing improper premium charges. Idempotency and mutation safety must be primary defenses, and explicit compensating entries are preferred over hidden edits. fileciteturn37file6

### Entitlement Corrections
Refund of unused premium add-on may remove entitlement; reversal of invalid subscription renewal may restrict access; support compensation credits may restore or extend entitlement if policy says so; and partial downgrade may narrow entitlement scope without removing it entirely. Entitlements are neither payment state nor credit balances, and product-visible access must reconcile to entitlement truth after correction. fileciteturn38file0L321-L334

## Negative Balance and Debt-State Policy

In chargeback, fraud, or invalidated-payment scenarios where value has already been consumed and cannot simply be reversed from unused balance, the platform may need policy-defined post-correction states such as:
- restricting the account or workspace until balance is corrected
- creating explicit negative-balance state
- recording debt-state for future offset
- blocking new usage until remediation
- escalating to support or fraud review

Negative-balance or debt-state behavior must be explicit, policy-defined, and limited to cases where reversal of unused balance is not sufficient. It must not be improvised product-by-product. fileciteturn38file0L335-L347

## Relationship to Ledger, Documents, and Base Commitment

### Credits Ledger
Unused paid credits refund produces reversal ledger entries; reservation release produces explicit release events; corrective issuance produces adjustment entries; scope reassignment produces migration/reassignment ledger events; and chargeback response produces explicit correction lineage. The correction layer instructs or triggers formal ledger behavior and does not bypass the ledger. fileciteturn38file0L360-L373

### Invoices and Receipts
Possible document effects include invoice cancellation or voiding, invoice adjustment, receipt reversal, refund receipt issuance, credit note or adjustment note creation, and replacement invoice issuance. The invoicing layer documents the correction but does not replace the underlying correction logic. Document correction must preserve historical traceability rather than silently overwriting commercial history. fileciteturn38file0L374-L385

### Base Commitment State
Where corrected credits were committed or intended to be committed on Base, correction state and commitment state must be separately visible. A valid platform correction may exist before chain commitment completes, and a chain event may require reconciliation into the platform correction record. This layered model preserves both commercial truth and operational transparency. fileciteturn37file1

## Invariants

1. Refund, reversal, and adjustment are distinct correction categories.
2. Every correction references an original commercial event and explicit correction scope.
3. Corrections preserve lineage; they do not silently rewrite earlier commercial truth.
4. Refund does not automatically imply full downstream unwind.
5. Reversal is an explicit counter-effect, not a hidden delete.
6. Adjustments are tightly bounded and cannot become undocumented escape hatches.
7. Unused and consumed value are distinct and must drive different correction outcomes.
8. Corrections affecting credits must flow through the canonical ledger.
9. Corrections affecting documents, entitlements, or Base commitment must remain explicitly linked rather than inferred.
10. High-impact correction actions are reason-coded, attributable, and auditable.

## Functional Rules

### No Silent Fix Rule
Commercial correction MUST never be represented as silent balance mutation, silent document rewrite, or silent product-access rewrite.

### Distinct Failure Rule
Refund rejected, refund completed, reversal pending, adjustment awaiting approval, debt-state entered, receipt superseded, entitlement restricted, and commitment reconciliation pending are distinct internal states and must remain distinguishable.

### Scope-Correctness Rule
Correction scope MUST be explicit. Wrong-scope corrections fail closed or route to explicit scope-correction flow.

### Idempotency Rule
Duplicate refund callbacks, webhook retries, and repeated internal correction attempts MUST NOT produce duplicate downstream correction effects. fileciteturn37file1

### Operator-Discipline Rule
Manual correction must write explicit platform records, not silent direct edits. Every action must be reason-coded, and high-impact corrections may require dual control or approval. fileciteturn38file0L348-L359

### Product-Subordination Rule
Product teams may propose correction logic, but shared billing and ledger systems remain canonical. Products may not implement hidden correction semantics. fileciteturn37file6

## Permission / Access Considerations

Correction truth does not grant unlimited mutation authority.

Required constraints:
- support, finance, billing, fraud/risk, platform admin, and governance-aware operators have distinct bounded roles
- the operator must be permission-authorized for the correction type and scope
- high-impact corrections may require dual control or approval
- product-visible access changes caused by correction remain downstream of entitlement and authorization truth
- UI visibility of a dispute or refund status does not imply correction authority

## API / Contract Implications

The correction layer MUST expose stable platform-consumable contracts for:

- creating and classifying correction requests
- linking a correction to the original commercial event
- recording refund, reversal, and adjustment state transitions
- surfacing downstream domain effects
- preserving approval posture and correction lineage
- exposing safe read views for support, finance, fraud, and product consumers

### Contract Rules
- backend owns canonical exception-case truth; frontend and admin surfaces may initiate eligible requests and read status but do not own correction truth fileciteturn37file12
- APIs MUST distinguish refund, reversal, and adjustment rather than one ambiguous correction action
- mutation-capable routes MUST support correlation identifiers and idempotent handling where retries are plausible
- internal contracts MUST preserve original-event linkage, scope linkage, operator lineage, approval metadata, and downstream-effect references
- downstream corrections MUST preserve lineage instead of rewriting earlier commercial history fileciteturn37file12

## Event / Async Implications

The correction layer SHOULD support event families such as:

- `refund.requested`
- `refund.approved`
- `refund.completed`
- `reversal.created`
- `reversal.completed`
- `adjustment.created`
- `adjustment.approved`
- `adjustment.executed`
- `entitlement.correction_applied`
- `document.correction_applied`
- `scope.correction_applied`
- `correction.commitment_reconciliation_started`
- `correction.commitment_reconciliation_completed`

Event rules:
- events MUST be emitted after canonical commit
- events MUST preserve correction type, original-event references, scope, reason, approval posture, and correlation references
- consumers MUST treat events as synchronization signals rather than independent sources of correction truth
- retries MUST preserve idempotent correction behavior
- downstream billing, ledger, entitlement, document, and reporting consumers MUST remain trace-linked to the initiating correction record

## Data Model / Storage Implications

At minimum, the correction domain SHOULD support semantic representation of:

### Correction Record
- `correction_id`
- `correction_type` (`refund`, `reversal`, `adjustment`)
- `related_original_commercial_event_type`
- `related_original_commercial_event_id`
- `owner_scope_type`
- `owner_scope_id`
- `status`
- `reason_code`
- `created_at`
- `actor_type`
- `actor_id`
- `policy_or_approval_reference` where applicable fileciteturn37file1

### Refund Record
- `refund_id`
- related payment record
- related invoice/receipt reference where applicable
- `refund_amount`
- `rail_type`
- `refund_status`
- `processed_at` where relevant fileciteturn37file1

### Reversal Record
- `reversal_id`
- related ledger entry or issuance/spend reference
- `reversal_amount`
- affected balance class or scope
- `status`
- `created_at` fileciteturn37file1

### Adjustment Record
- `adjustment_id`
- `adjustment_subtype`
- target domain(s)
- amount or structural effect
- operator or system actor
- approval metadata where applicable
- `created_at` fileciteturn37file1

Additional semantic structures SHOULD include:
- correction-to-commitment linkage
- debt-state or restriction-state marker where policy allows
- downstream document mutation references
- entitlement-correction references
- operator before/after lineage snapshot references

## Read Model / Projection / Reporting Rules

- support dashboards and finance summaries MAY expose correction summaries, but MUST remain explicitly derived
- sensitive correction actions MUST re-check canonical backend correction state rather than stale views
- reports SHOULD support refunds by rail and product, reversals by reason, adjustments by operator/product/scope, chargeback/dispute correction rates, unused vs consumed refund ratios, support compensation issuance, correction impact on credits balances, correction impact on subscriptions and entitlements, and correction/commitment reconciliation health fileciteturn37file1
- projection lag MUST NOT hide active correction, debt-state, review posture, or reconciliation requirements

## Security / Risk / Abuse Controls

The correction layer is a sensitive economic and trust-control surface and MUST enforce:

- typed correction authorization and scope-aware access control
- reason-coded operator actions
- approval-bounded high-impact corrections
- resistance to hidden balance edits, hidden entitlement edits, or hidden document rewrites
- integration with fraud/risk holds and dispute handling
- explicit restriction, debt-state, or review posture where invalidated consumed value cannot be cleanly unwound
- auditable linkage between support/admin intervention and downstream domain effects

## Boundary Violation Detection / Non-Canonical Patterns

The following patterns are non-canonical and forbidden:

- using refund as a vague umbrella for all correction behavior
- representing support goodwill as a false payment refund
- reversing fully consumed value as if it were still unused
- product-local correction models that bypass shared billing, ledger, or document systems
- manual balance or document edits with no explicit correction record
- treating chargeback/dispute handling as identical to ordinary refund
- hiding Base commitment correction failure behind apparently successful UI state
- destructive rewrite that erases earlier commercial truth

Implementations SHOULD detect and surface these conditions through diagnostics, audit, and operational alerts.

## Failure Handling / Edge Cases

### External refund succeeds but internal reversal lags
The platform MUST preserve a recoverable pending-correction state rather than silently losing the linkage. fileciteturn37file1

### Reversal attempted after credits already fully consumed
The system MUST move into policy-defined debt-state, compensation, or restricted-account handling rather than pretending unused balance still exists. fileciteturn37file1

### Duplicate refund callback
Correction flows MUST be idempotency-aware and avoid duplicate downstream reversal or document effects. fileciteturn37file1

### Wrong workspace corrected after several product actions
Correction may require combined balance, entitlement, and document lineage rather than one simple mutation. fileciteturn37file1

### Support issues goodwill credits instead of true refund
The platform MUST represent this as compensating adjustment or bonus issuance, not as a false payment refund. fileciteturn37file1

### Chargeback arrives after entitlement granted and used
The system MUST preserve the original usage history while applying policy-defined correction, restriction, or debt-state handling. fileciteturn37file1

### Base correction commitment fails
Platform-side correction truth remains valid, but commitment state must surface failure and reconciliation requirements. fileciteturn37file1

## Operational Considerations

Operational systems SHOULD support:

- typed correction queues and review states
- wrong-scope detection and remediation tooling
- explicit linkage from payment/billing/ledger/document records into correction records
- reconciliation health for correction-to-ledger and correction-to-commitment alignment
- support, finance, and fraud control-plane tools for bounded remediation
- runbooks for duplicate refund callbacks, delayed reversal, partial refund logic, debt-state entry, and Base commitment reconciliation failure

Because correction flows cross many shared commercial systems, operational posture MUST be platform-grade rather than product-local.

## Migration / Compatibility / Supersession Considerations

Migrations into this canonical model MUST enforce the following:

- product-local correction models with shared platform meaning must be retired or explicitly marked non-canonical
- historical correction flows that silently rewrote balances, invoices, or entitlements must be replaced with lineage-preserving correction records
- old systems that conflated refund, reversal, and adjustment are superseded within this scope
- backward-compatibility adapters MAY preserve older status labels temporarily, but typed correction semantics, scope linkage, and correction lineage MUST remain explicit internally
- future refinement of chargeback policy, proration policy, or debt-state policy MUST preserve this document’s typed-correction and lineage-preservation principles

## Implementation-Contract Guardrails

Downstream implementations MUST preserve all of the following:

1. refund, reversal, and adjustment remain distinct typed correction categories
2. every correction links to an original commercial event and explicit correction scope
3. payment, billing, credits semantics, ledger truth, document truth, entitlement truth, and correction truth remain distinct
4. unused value and consumed value remain distinct and drive different correction outcomes
5. correction affecting credits flows through canonical ledger behavior rather than bypassing it
6. correction affecting invoices or receipts preserves document lineage rather than rewriting documents in place
7. product teams may not create hidden correction models or hidden economic mutations
8. stale dashboards, support notes, or provider UI MUST NOT outrank canonical backend correction truth
9. degraded runtime conditions MUST NOT silently widen correction powers or silently hide correction ambiguity
10. idempotency and replay safety MUST be preserved for refund callbacks, reversal execution, adjustment workflows, approval flows, and correction-to-ledger/document propagation
11. reason capture, source references, scope references, approval metadata, and audit lineage MUST remain explicit for high-impact corrections
12. downstream teams MUST NOT optimize away original-event linkage, correction type, scope references, document linkage, debt-state markers, or commitment reconciliation posture where those elements protect commercial integrity, auditability, supportability, or migration safety

## Downstream Execution Staging

This specification implies the following preferred downstream implementation order:

1. stabilize correction type taxonomy and original-event linkage
2. stabilize scope attachment, unused-vs-consumed logic, and correction approval posture
3. integrate payment, billing, credits-ledger, entitlement, and document downstream effects through explicit contracts
4. integrate debt-state, restriction, and dispute-handling posture where policy requires
5. integrate Base commitment reconciliation linkage
6. integrate audit, reporting, support/control-plane, and remediation workflows

## Required Downstream Specs / Contract Layers

This specification directly requires compatible downstream refinement and implementation-contract work in:

- `PAYMENT_FRAUD_AND_ABUSE_PREVENTION_SPEC.md`
- `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
- `AI_USAGE_METERING_SPEC.md`
- `REFUND_REVERSAL_ADJUSTMENT_API_SPEC.md`
- finance, reconciliation, reporting, support/control-plane, and remediation workflows
- product-specific exception-handling integrations

## Canonical Examples / Anti-Examples

### Canonical Example 1 — Unused Credits Refund
A user refunds a credits purchase and the platform determines some paid credits remain unused. A refund record is created, reversal ledger entries remove the unused balance, related documents are updated through explicit document logic, and lineage to the original purchase remains intact.

### Canonical Example 2 — Duplicate Usage Correction
An async usage event was recorded twice. No customer-facing refund is needed, but a reversal or adjustment is created to unwind the duplicate internal commercial effect and preserve audit lineage.

### Canonical Example 3 — Wrong-Scope Balance Repair
Credits were assigned to the wrong workspace. The platform creates a bounded adjustment or reassignment correction linked to the original event and preserves both original and corrected scope lineage.

### Canonical Example 4 — Chargeback After Consumed Value
A rail-originating chargeback arrives after value was already consumed. The platform preserves the original usage history, records the typed correction, and enters policy-defined debt-state or restriction posture rather than pretending unused balance still exists.

### Anti-Example 1 — Goodwill as Refund
Support gives bonus credits and records it as a customer refund. This is forbidden.

### Anti-Example 2 — Hidden Balance Edit
An operator directly edits available credits with no typed correction record. This is forbidden.

### Anti-Example 3 — Full Undo Without Consumed-Value Check
A system tries to fully reverse already consumed subscription or AI usage value as though it were entirely unused. This is forbidden.

### Anti-Example 4 — Silent Document Rewrite
A correction removes historical invoice or receipt evidence by editing existing documents in place. This is forbidden.

## Dependencies / Cross-Spec Links

This specification depends on:

- `REFINED_SYSTEM_SPEC_INDEX.md`
- `DOCS_SPEC_INDEX.md`
- `SYSTEM_SPEC_INDEX.md`
- `API_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `PAYMENT_RAILS_INTEGRATION_SPEC.md`
- `INVOICING_AND_RECEIPTS_SPEC.md`
- `PAYMENT_FRAUD_AND_ABUSE_PREVENTION_SPEC.md`
- `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
- `AI_USAGE_METERING_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`

This specification directly governs or materially informs:

- `PAYMENT_FRAUD_AND_ABUSE_PREVENTION_SPEC.md`
- `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
- `AI_USAGE_METERING_SPEC.md`
- `REFUND_REVERSAL_ADJUSTMENT_API_SPEC.md`
- finance, reconciliation, reporting, support/control-plane, and remediation workflows
- product-specific exception-handling integrations

## Explicitly Deferred Items

The following are intentionally deferred to adjacent or downstream specifications:

- exact proration rules for recurring subscriptions
- exact debt-state implementation if adopted
- exact chargeback response policy by rail
- exact approval thresholds for large manual adjustments
- exact user-facing refund UX and status presentation
- exact reconciliation jobs for correction-to-commitment alignment

These do not weaken the canonical correction model established here.

## Final Normative Summary

The FUZE refund, reversal, and adjustment system is the structured correction layer of the platform economy. It ensures that payment refunds, unused-credit unwinds, billing fixes, entitlement corrections, support remediation, and operational repairs occur through explicit, traceable, policy-defined actions rather than through silent mutation. By distinguishing clearly between refunds, reversals, and adjustments, and by connecting those actions to payment records, billing state, Platform Credits, documents, and Base commitment behavior, FUZE preserves commercial integrity across a complex multi-product ecosystem.

This document is the canonical FUZE rule set for refund, reversal, and adjustment semantics.
