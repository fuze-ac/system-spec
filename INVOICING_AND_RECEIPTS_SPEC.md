# INVOICING_AND_RECEIPTS_SPEC

## Purpose

This document defines the canonical invoicing and receipts architecture of the FUZE platform. Its purpose is to establish how commercial events are rendered into formal billing documents, how invoice and receipt records relate to subscriptions, usage billing, Platform Credits, payment rails, and billing scope, and how FUZE preserves traceable commercial documentation across a multi-product ecosystem.

This specification is important because FUZE is a platform company with shared billing and shared internal economics. A platform with subscriptions, usage charging, multiple payment rails, account and workspace billing scopes, and Platform Credits requires a disciplined documentation layer for what was charged, what was paid, what was credited, what was refunded, and which scope owned the commercial event.

Invoices and receipts are therefore not decorative billing outputs. They are part of the platform’s commercial truth, support operations, support user trust, and support downstream audit, reporting, reconciliation, and finance workflows.

---

## Scope

This specification covers:

- the canonical role of invoices and receipts inside FUZE
- the distinction between invoice, receipt, billing record, payment record, and credit-ledger record
- invoice and receipt generation rules
- account-scoped versus workspace-scoped billing documents
- subscriptions, usage billing, add-ons, and credit-funding documentation
- document lifecycle states
- refunds, corrections, credit notes, and replacement-document behavior
- interaction with payment rails, Platform Credits, subscriptions, and reporting
- user-facing and internal-facing documentation requirements
- audit, control, and retention implications

This specification does not define tax advice, legal jurisdiction-specific invoicing compliance, or complete accounting treatment for every geography. Those may be refined operationally later. This file also does not define the full payment verification or credits ledger internals, which are covered in:

- `PAYMENT_RAILS_INTEGRATION_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `REFUND_REVERSAL_AND_ADJUSTMENT_SPEC.md`

---

## Design Goals

The design goals of the invoicing and receipts system are:

1. to provide one shared billing-document architecture across the FUZE ecosystem
2. to make commercial events understandable across multiple products and billing models
3. to preserve clean distinction between charge intent, payment verification, credits issuance, and final documentation
4. to support both account-scoped and workspace-scoped billing contexts
5. to make corrections, refunds, and replacement-document flows auditable and policy-driven
6. to support transparency, reporting, support operations, and finance reconciliation
7. to avoid product-local ad hoc receipt models that fragment the platform economy
8. to keep user-facing documentation aligned with the platform’s structured commercial architecture

---

## Non-Goals

This specification is not intended to:

- make invoices the canonical source of payment truth
- make receipts the canonical source of credits-ledger truth
- require every product interaction to create a separate standalone invoice
- define every jurisdiction-specific VAT, GST, or local tax rule
- replace accounting ledgers with user-facing billing documents
- let products create private commercial-document formats outside platform rules
- make invoice display formatting more important than commercial traceability

---

## Canonical Documentation Principle

The primary principle of FUZE invoicing and receipts is:

> invoices and receipts are formal documentary views of normalized platform-commercial events, not substitutes for payment verification, subscription state, entitlement logic, or credits-ledger truth.

This means:

- an invoice documents a chargeable commercial event or payable obligation
- a receipt documents a completed payment or equivalent settled billing outcome
- credits issuance may be related to invoices or receipts, but credits are still tracked in the credits system
- payment records remain distinct from invoice records
- reporting may derive from invoice and receipt records, but does not redefine them

This separation is necessary for clarity and auditability.

---

## Core Concepts

### Invoice
A formal billing document representing a chargeable commercial obligation, whether for subscription, usage, add-on, seat expansion, credit funding, or other approved platform-commercial event.

### Receipt
A formal document representing completed payment or settled commercial intake for a relevant invoice or platform purchase event.

### Billing Record
The platform-commercial record that tracks what happened commercially, such as a subscription charge, one-time purchase, usage charge, refund, cancellation, or correction.

### Payment Record
The normalized platform record of external payment state after rail-specific verification.

### Credit Ledger Record
The canonical internal economic record for Platform Credits issuance, spend, reversal, adjustment, expiry, or settlement behavior.

### Credit Funding Document
An invoice or receipt tied to the purchase or funding of Platform Credits.

### Adjustment Document
A documentary record for a correction, replacement, refund-related note, or compensating billing action where required by platform policy.

### Billing Scope
The owner of the commercial event, typically an account or a workspace.

---

## Why FUZE Needs Shared Invoicing and Receipts

FUZE needs a shared invoicing and receipts system because it is not a single-product application with one narrow payment model.

The ecosystem supports:

- multiple products
- subscriptions
- usage billing
- account and workspace billing scopes
- Platform Credits funding
- add-ons and premium features
- multiple external payment rails
- refunds and corrections
- future enterprise and partner-oriented commercial flows

Without a shared documentation architecture, each product would be tempted to generate its own billing records and user-facing receipts independently. This would fragment support, reporting, finance workflows, and user understanding.

A shared billing-document layer creates:

- one consistent commercial language across products
- one shared understanding of what was charged and what was paid
- better reconciliation between subscriptions, payments, credits, and reporting
- better support tooling
- stronger platform-level auditability

This is consistent with FUZE’s wider thesis that important shared systems should live at the platform layer rather than inside disconnected products.

---

## Canonical Distinction Between Invoice and Receipt

FUZE must preserve a clear distinction between an invoice and a receipt.

### Invoice
An invoice represents the commercial obligation, requested charge, or formal billing statement.

Examples:
- monthly workspace subscription invoice
- seat expansion invoice
- usage overage invoice
- one-time credit-funding invoice
- add-on feature invoice

### Receipt
A receipt represents the fact that payment or equivalent settlement has been completed for the relevant commercial event.

Examples:
- successful Stripe payment receipt
- app-store confirmed purchase receipt as normalized by platform policy
- stablecoin-funded platform purchase receipt after verification
- successful purchase of credit package receipt

### Key rule

An invoice may exist before a receipt exists.  
A receipt should not exist unless the platform recognizes that the associated payment or settlement event has been completed under policy.

This distinction helps the rest of the platform reason correctly about:
- obligations
- payment completion
- entitlement activation
- credits issuance
- refund logic
- commercial history

---

## Canonical Commercial Document Types

FUZE should support, at minimum, the following document types.

### 1. Subscription Invoice
Documents recurring plan or recurring access charges.

### 2. Usage Invoice
Documents metered or overage-based billing.

### 3. Seat / Expansion Invoice
Documents seat increases, workspace upgrades, or plan expansions.

### 4. Credit Funding Invoice
Documents the purchase or funding of Platform Credits.

### 5. One-Time Purchase Invoice
Documents non-recurring purchases such as add-ons, premium modules, or bounded access packages.

### 6. Payment Receipt
Documents successful payment or settled purchase.

### 7. Refund Receipt or Refund Record
Documents completed refund outcomes where the platform represents them formally.

### 8. Credit Note / Adjustment Note
Documents a downward billing correction, credit correction, or compensating commercial adjustment where policy requires a formal document trail.

The platform does not need to expose every document type identically in every UI, but the underlying semantic model must support them.

---

## Billing Scope Model

Invoices and receipts must be scope-aware.

### 1. Account-Scoped Documents
Commercial documents owned by an individual account.

Examples:
- personal subscription invoice
- individual QTB receipt
- personal credit package purchase receipt

### 2. Workspace-Scoped Documents
Commercial documents owned by a workspace or organization billing context.

Examples:
- workspace HerHelp plan invoice
- team Botmad subscription invoice
- workspace credits funding receipt
- seat expansion invoice for a collaborative plan

### Scope principles

- every invoice and receipt must identify its billing scope clearly
- account and workspace documents must remain distinguishable
- support tools and reporting must preserve scope clarity
- products must not issue workspace charges into personal document history by accident, or vice versa

This distinction is necessary because FUZE supports both individual and multi-user commercial operation.

---

## Relationship to Payment Rails

The invoicing and receipts layer is downstream from payment normalization.

### Architectural rule

A payment event does not become a canonical receipt merely because a provider says payment occurred. The payment rail must first be verified and normalized into platform-commercial state.

Once normalized, the invoicing and receipts system may generate or finalize the relevant document outcome.

### Examples

#### Stripe
A subscription invoice may be created before payment completes. Once verified Stripe payment succeeds, a receipt may be issued or invoice state may transition to paid.

#### Stablecoin
A credit-funding invoice may be initiated. Once the stablecoin transfer is verified under policy, the receipt may be issued and credits funding completed.

#### Apple / Google
The store may be the raw payment source, but the platform still decides how that event is reflected as internal invoice/receipt and entitlement state.

#### Telegram Stars
Telegram-originated purchase state must be normalized before the relevant receipt or platform purchase record is finalized.

This architecture keeps user-facing and finance-facing documents aligned with verified platform truth rather than with raw provider semantics.

---

## Relationship to Platform Credits

Invoices and receipts are especially important in a credits-based platform economy.

### Credit-related documentation may include:

- invoice for purchase of a credit package
- receipt for successful credit funding
- documentation of bonus-credit campaigns where exposed to the user
- correction note for credits-related refund or reversal outcome
- workspace-level receipt for credits funding used by multiple products

### Important distinction

An invoice or receipt may document a credits funding event, but it is not the ledger of the credits system itself.

The credits domain remains canonical for:
- issuance
- spend
- reversal
- adjustment
- expiry
- scope-aware balance state

The invoice/receipt layer documents the associated commercial event in a user-facing and finance-facing form.

This separation is especially important in FUZE because the platform is designed to keep:
- payment intake,
- credits issuance,
- product usage,
- and profit participation

as distinct but connected layers.

---

## Relationship to Subscriptions and Usage Billing

The invoicing and receipts layer is also downstream from the subscriptions and usage billing system.

The billing system determines:
- what should be charged
- when it should be charged
- which scope owns the charge
- whether it is recurring, one-time, included, overage, or usage-driven
- whether entitlement changes are tied to the charge

The invoicing layer then renders that commercial outcome into formal documentation.

### Examples

- subscription renewal -> subscription invoice -> payment verified -> receipt -> entitlement remains active
- usage overage event -> usage invoice -> credits or payment settlement -> receipt or paid-state update
- plan upgrade -> expansion invoice -> payment success -> receipt -> new entitlement state

Invoices and receipts therefore make the commercial state legible, but they do not independently define commercial policy.

---

## Invoice Lifecycle

The invoice system should support explicit lifecycle states.

Recommended states include:

- `draft`
- `issued`
- `pending_payment`
- `paid`
- `partially_paid` if relevant
- `canceled`
- `voided`
- `replaced`
- `adjusted`

### State meanings

#### Draft
Prepared internally but not yet finalized for customer-facing or operational use.

#### Issued
Officially created as a payable or recognized billing statement.

#### Pending Payment
Payment is expected or in progress.

#### Paid
The invoice is satisfied by verified payment or equivalent settlement logic.

#### Partially Paid
Relevant only if the billing architecture supports partial settlement behavior.

#### Canceled
The charge intent is withdrawn before completion.

#### Voided
The invoice should no longer be treated as a valid commercial document in its original form.

#### Replaced
A newer invoice supersedes the prior one due to correction or restructuring.

#### Adjusted
The invoice has related correction records, credit notes, or modified interpretation paths.

All transitions must be auditable.

---

## Receipt Lifecycle

Receipts should also support explicit lifecycle meaning, though usually simpler than invoices.

Recommended states include:

- `pending`
- `issued`
- `reversed`
- `replaced`

### State meanings

#### Pending
Optional state used when internal processing is not yet complete even though external payment may exist.

#### Issued
The platform recognizes completed payment or settled purchase and creates a formal receipt.

#### Reversed
The receipt remains part of history but the underlying payment was reversed or refunded in a way that materially changes commercial effect.

#### Replaced
A corrected receipt or settlement document supersedes the original.

Receipts should remain traceable even when reversal or correction occurs.

---

## Invoice Generation Rules

Invoices should be generated only from approved commercial events.

### Valid invoice triggers include:

- new subscription activation
- renewal event
- seat increase
- plan upgrade or downgrade transition where applicable
- metered usage overage
- one-time add-on purchase
- credits funding purchase
- enterprise billing event where supported
- support-approved corrective rebill flow

### Invoice generation requirements

At minimum, an invoice should contain enough information to identify:

- invoice identifier
- billed scope
- billed product or service context
- line items or summarized charge reasons
- currency or asset context
- issue time
- payment due or settlement expectation where relevant
- related subscription or usage references where applicable
- tax fields where required by operating policy
- status

Products must not invent separate invoice-generation logic that bypasses shared platform conventions.

---

## Receipt Generation Rules

Receipts should be issued only after the platform recognizes verified payment or settled billing completion.

### Valid receipt triggers include:

- verified Stripe payment
- verified stablecoin settlement
- verified app-store purchase normalization
- verified Telegram Stars purchase normalization
- verified credits funding purchase completion
- support-approved manual settlement correction where policy allows

### Receipt requirements

At minimum, a receipt should contain enough information to identify:

- receipt identifier
- related invoice or commercial event reference
- payment source rail
- paid amount or settled amount
- scope owner
- settlement timestamp
- status
- refund/reversal linkage where applicable

The receipt must communicate what actually completed, not merely what was requested.

---

## Line Item Model

FUZE invoices should support structured line items.

### Line items may represent:

- recurring plan fee
- seat count or seat delta
- usage overage
- add-on purchase
- one-time setup or activation fee if such a model is introduced
- credits funding package
- adjustment amount
- discount or promotion effect where visible

### Line item principles

- line items must be attributable to a commercial reason
- line items should remain interpretable in support and reporting contexts
- product-specific items must still fit platform naming and reconciliation standards
- discounting and promotional logic should be visible rather than silently changing totals without trace

A line-item model makes the platform easier to support and more trustworthy for users.

---

## Refunds, Credit Notes, and Replacement Documents

The invoicing and receipts system must support correction behavior.

### Refunds
When value is returned through a valid refund flow, the platform may need to generate:
- refund receipt
- reversal record
- adjusted invoice state
- related credit note where policy requires

### Credit Notes / Adjustment Notes
Where the original invoice amount should be reduced, corrected, or compensated, the platform should support formal adjustment documents rather than silently mutating history.

### Replacement Documents
If an invoice or receipt was generated incorrectly, the system may support:
- voiding the original
- issuing a corrected replacement
- preserving trace links between old and new documents

### Principle

Commercial history should be corrected through auditable document relationships, not by silent overwrite of the past.

This matters for user trust, support quality, reporting integrity, and future finance operations.

---

## User-Facing Document Access

FUZE should support user-visible access to relevant billing documents.

### Account-level visibility may include:

- personal invoices
- personal receipts
- personal refund records
- credits funding receipts
- subscription renewal history

### Workspace-level visibility may include:

- workspace invoices
- workspace receipts
- seat-related billing documents
- workspace credit funding records
- shared commercial history where permitted by role

### Visibility principles

- document access must follow role and permission rules
- billing owners and authorized operators may have broader visibility than ordinary members
- document visibility should preserve privacy and scope boundaries
- user-facing views are presentation layers, not the canonical source of billing truth

This is important because multi-product and multi-workspace platforms require commercial visibility that is both useful and permission-aware.

---

## Internal Operational Use

Invoices and receipts are also important operational artifacts.

Internal teams may use them for:

- support investigation
- billing dispute review
- finance reconciliation
- product-usage correlation
- refund handling
- credits funding traceability
- workspace billing-owner review
- abuse and fraud investigation

### Internal-access rule

Internal access to commercial documents must remain permission-bounded and auditable, especially when support, finance, or admin roles access user or workspace billing history.

---

## Relationship to Reporting and Reconciliation

Invoices and receipts feed reporting and reconciliation but do not replace them.

### Reporting may derive from invoices and receipts to support:

- MRR and subscription views
- billing volume summaries
- payment completion reporting
- workspace billing health views
- credits funding summaries
- refund and dispute reporting
- product revenue attribution summaries

### Reconciliation may use invoices and receipts alongside:

- payment records
- credit ledger records
- subscription state
- usage records
- refund events
- chain commitment state where relevant

Derived reporting must not mutate or redefine the underlying document history.

---

## Control and Governance Implications

Certain invoice and receipt actions are sensitive and must be controlled.

### Sensitive actions include:

- manual invoice void
- manual receipt correction
- backdated billing-document creation
- replacement of issued documents
- support or finance correction affecting user-visible billing history
- bulk workspace billing adjustments
- policy change affecting document semantics

### Control principles

- sensitive document actions must be permission-bounded
- changes must be auditable
- document history must preserve lineage between original and corrected states
- products must not obtain unrestricted power to alter billing documents outside shared platform rules

Billing documents are part of the trust surface of the platform and should be treated accordingly.

---

## Audit Requirements

The invoicing and receipts system must support strong auditability.

At minimum, audit events should exist for:

- invoice generated
- invoice issued
- invoice paid
- invoice canceled or voided
- receipt issued
- refund or reversal documented
- document replaced
- billing scope reassigned where relevant
- support/admin document correction
- user-visible export or internal-access events where policy requires logging

Because billing documents often become evidence in support, finance, or dispute workflows, audit discipline is mandatory.

---

## Minimum Data Model Requirements

At minimum, the invoicing and receipts system must support semantic representation of:

### Invoice
- invoice_id
- scope_type
- scope_id
- product or billing context
- status
- currency or asset basis
- subtotal / total
- issue timestamp
- due or settlement expectation where relevant
- related subscription or billing references

### Invoice Line Item
- line_item_id
- invoice_id
- item type
- description / charge reason
- quantity where relevant
- unit amount where relevant
- total amount
- product or meter reference where applicable

### Receipt
- receipt_id
- related invoice or billing event reference
- payment rail reference
- settled amount
- status
- issued timestamp
- refund/reversal reference where applicable

### Adjustment / Correction Record
- adjustment_id
- related original document
- adjustment type
- reason
- operator/system actor
- timestamp
- replacement linkage where applicable

These are minimum semantic requirements. More detailed schemas are refined downstream.

---

## Edge Cases and Failure Handling

### Payment verified but receipt generation delayed
The platform must preserve recoverable state and avoid duplicate receipts. The verified payment remains part of commercial truth even if presentation/document issuance lags.

### Invoice generated with wrong scope
The system must support correction through auditable replacement or void/reissue logic rather than silent mutation.

### Duplicate provider callback
Invoice paid-state and receipt issuance must be idempotency-aware to prevent duplicate commercial documents.

### Usage charge corrected after invoice issued
The platform must support adjustment notes, replacement invoices, or equivalent correction pathways under policy.

### Workspace billing owner changes after invoices already exist
Historical documents must preserve historical scope and ownership context rather than being rewritten to match new state.

### Refund occurs after credits partially consumed
The receipt/document layer must reflect the commercial correction outcome, while the credits and billing systems apply policy-aware reversal logic.

### Product-local display differs from canonical invoice record
The product display is non-canonical. The shared invoicing system remains authoritative for document truth.

---

## Open Items

The following areas are intentionally refined in downstream or operational specifications:

- exact jurisdiction-specific tax handling
- exact PDF / export rendering rules
- exact receipt formatting by rail type
- exact enterprise billing document conventions
- exact localized language and currency presentation behavior
- exact retention duration and archival policy by region

These do not weaken the canonical invoicing and receipts model established here.

---

## Closing Summary

The FUZE invoicing and receipts system is the shared documentary layer of the platform’s commercial architecture. It turns normalized subscriptions, usage charges, credits funding events, add-ons, and refunds into formal billing records that are understandable, auditable, and scope-aware. Invoices represent payable or chargeable commercial events. Receipts represent completed settlement outcomes. Both are distinct from payment verification, credits ledgers, and accounting truth, but they connect to all of those layers. This structure allows FUZE to maintain a coherent commercial documentation model across a growing multi-product ecosystem without fragmenting into product-specific billing records or ambiguous payment history.
