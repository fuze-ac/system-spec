# INVOICING_AND_RECEIPTS_SPEC

## Title
FUZE Invoicing and Receipts Specification

## Document Metadata

- Document Name: `INVOICING_AND_RECEIPTS_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Version: 1.1.0
- Effective Date: 2026-04-21
- Last Updated: 2026-04-21
- Reviewed On: 2026-04-21
- Document Owner: FUZE Platform Invoicing and Billing Documents Architecture
- Approval Authority: FUZE Platform Architecture and Governance Authority
- Review Cadence: Quarterly or upon material change to billing truth, payment normalization, credits-ledger settlement posture, refund/correction policy, document delivery controls, tax or compliance posture, or audit/traceability requirements
- Governing Layer: Platform core / shared commercial infrastructure / invoicing and receipts
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, billing engineering, payments engineering, credits and ledger engineering, finance operations, support operations, security engineering, audit/compliance, product engineering, API design, data engineering, platform operations, implementation-contract authors
- Primary Purpose: Define the canonical FUZE invoicing and receipts model as the platform-owned billing-document layer for approved commercial obligations and confirmed payment or settlement events, preserving strict separation from subscription truth, usage-billing truth, payment-rail truth, Platform Credits truth, credits-ledger truth, entitlement truth, token/payout/treasury truth, and product-local UI assumptions
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
  - `IDENTITY_AND_ACCOUNT_SPEC.md`
  - `WORKSPACE_AND_ORGANIZATION_SPEC.md`
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
  - `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
  - `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
  - `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
  - `PLATFORM_CREDITS_SPEC.md`
  - `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
  - `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
  - `PAYMENT_RAILS_INTEGRATION_SPEC.md`
  - `REFUND_REVERSAL_AND_ADJUSTMENT_SPEC.md`
  - `PAYMENT_FRAUD_AND_ABUSE_PREVENTION_SPEC.md`
  - `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
- Primary Downstream Dependents:
  - `INVOICING_RECEIPTS_API_SPEC.md`
  - `REFUND_REVERSAL_AND_ADJUSTMENT_SPEC.md`
  - `PAYMENT_FRAUD_AND_ABUSE_PREVENTION_SPEC.md`
  - `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
  - `PAYMENT_RAILS_INTEGRATION_SPEC.md`
  - accounting/export workflows
  - reporting and reconciliation workflows
  - support/control-plane document remediation workflows
  - product billing-document visibility surfaces
- Supersedes: Earlier or weaker FUZE invoicing or receipt writeups that did not clearly separate document truth from billing truth, payment truth, credits truth, or ledger truth
- Superseded By: Not yet known
- Related Decision Records: Not yet known
- Canonical Status Note: This document is the canonical FUZE specification for invoice and receipt semantics. Downstream APIs, products, workers, dashboards, support tooling, payment integrations, billing adapters, credits consumers, and reporting systems MUST NOT reinterpret payment-provider UI state, raw billing events, raw ledger entries, or product-local document summaries as substitutes for canonical invoice or receipt truth.
- Implementation Status: Normative architecture baseline; downstream API, service, runtime, event, audit, and control-plane contracts must conform
- Approval Status: Drafted for refined-system inclusion; formal approval record not yet attached
- Change Summary:
  - strengthened separation between invoice truth, receipt truth, billing truth, payment normalization truth, and credits-ledger truth
  - clarified document subject/scope attachment, issuance triggers, line-item lineage, receipt allocation semantics, and delivery/remediation posture
  - hardened supersession-safe correction rules so document history is preserved rather than rewritten
  - expanded implementation-contract guardrails for backend ownership, idempotent issuance, visibility controls, and derived-view safety

## Purpose

This specification defines the canonical invoicing and receipts model of the FUZE platform.

Its purpose is to make explicit:

- when invoices exist and what they mean
- when receipts exist and what they confirm
- how invoices and receipts relate to subscriptions, usage billing, credits purchases, renewals, seat changes, refunds, and approved commercial adjustments
- how billing documents attach to account or workspace commercial scope
- how document issuance, delivery, supersession, voiding, cancellation, and remediation must work
- how APIs, events, audit lineage, and reporting/export flows must preserve billing-document truth

FUZE supports monetary flows into the platform and product spending within it. Clear invoice and receipt rules are necessary for business customers, finance controls, tax/compliance readiness, internal legitimacy, and downstream reporting. The billing-document layer must therefore be durable, explicit, and distinct from the upstream or downstream commercial systems it references.

## Scope

This specification governs:

- canonical invoice semantics
- canonical receipt semantics
- approved source references for invoice and receipt issuance
- account-scoped and workspace-scoped billing-document ownership and visibility
- line-item, allocation, and source-linkage semantics
- issuance, delivery, supersession, voiding, cancellation, regeneration, and remediation rules
- relationship among invoices, receipts, billing truth, payment normalization, credits purchases, credits-ledger settlement, and refunds/corrections
- API, event, data-model, audit, security, and operational implications of billing-document truth
- migration constraints preventing products or providers from becoming document-truth owners

This specification does not define in full depth:

- recurring subscription state machines
- usage-rating logic
- payment-provider verification protocols
- credits-ledger append-oriented mutation logic
- exact PDF rendering internals
- exact tax-engine integration internals
- exact external accounting-export formats
- exact legal/compliance wording of all documents

Those concerns belong to adjacent billing, payment, credits, refund, accounting-export, and API specifications and MUST remain compatible with this document.

## Out of Scope

This specification is explicitly out of scope for:

- proving actor identity or session validity
- determining final actor authorization for billing-document mutation
- defining payment-processor state semantics
- defining Platform Credits class semantics
- defining final credits-ledger truth
- defining full refund/reversal workflow semantics
- defining payout statements, treasury reports, or token reports
- defining unsupported peer-to-peer invoicing or generic contract-document management

## Design Goals

1. Preserve invoices and receipts as distinct document classes with distinct business meaning.
2. Ensure billing documents derive from approved upstream commercial truth rather than UI or provider assumptions.
3. Support account-scoped and workspace-scoped billing documents with explicit scope clarity.
4. Preserve durable lineage across issuance, delivery, supersession, voiding, and corrective remediation.
5. Support multiple upstream commercial causes without document-semantics drift.
6. Keep documents compatible with subscriptions, usage billing, credits purchases, seat changes, and payment confirmation flows.
7. Support finance, support, reconciliation, audit, and compliance review without creating alternate truth owners.
8. Keep product surfaces as consumers of document truth rather than owners.
9. Support safe rendering, delivery, and access-link workflows without weakening canonical document records.
10. Provide a durable implementation-contract foundation for APIs, storage, exports, and operations.

## Non-Goals

This specification is not intended to:

- make invoices and receipts generic transaction notes
- treat receipts as substitutes for invoices where invoice semantics are required
- treat payment-provider UI state as a receipt
- treat ledger entries as invoices
- treat invoices as proof of payment by themselves
- let products define their own invoice or receipt semantics for shared platform commerce
- allow destructive document rewrite that erases historical commercial truth

## Core Principles

### 1. Invoice / Receipt Distinction Principle
Invoices and receipts are distinct document classes with separate business meaning. Invoices represent billing-document obligations or approved commercial billing records tied to FUZE platform semantics. Receipts represent confirmation of payment or value-settlement events according to allowed business rules. They are not interchangeable.

### 2. Backend-Owned Document Truth Principle
Backend owns canonical billing-document truth. Frontend and product surfaces consume billing-document records and delivery state; they do not invent or author them.

### 3. Approved-Upstream-Sources Principle
Invoices and receipts derive from approved commercial and billing events, not from frontend assumptions, provider-side UI state, or ad hoc operator notes.

### 4. Document-Is-Not-Billing Principle
Billing truth determines commercial obligations, plan cycles, usage-rated outcomes, seat commercial state, and corrections. Billing documents are durable representations derived from approved billing truth, not replacements for it.

### 5. Document-Is-Not-Payment Principle
Payment verification may justify receipt issuance and may contribute to invoice settlement state, but raw payment truth and provider UI state are not invoice or receipt truth.

### 6. Document-Is-Not-Credits Principle
Invoices and receipts must remain distinct from Platform Credits balances and credits-ledger state. A credits purchase or credits-backed subscription may be documented by invoices or receipts, but the documents do not become balance truth.

### 7. Scope-Attached Document Principle
Every invoice or receipt must attach to one explicit commercial scope, typically account or workspace. Wrong-scope document assignment is forbidden.

### 8. Supersession-Safe Correction Principle
Voiding, supersession, cancellation, regeneration, and correction must preserve lineage instead of rewriting historical truth.

### 9. Derived-View Subordination Principle
Invoice summaries, receipt summaries, access links, and rendered-document delivery views are derived from canonical records and MUST remain subordinate to them.

### 10. Auditability Principle
Sensitive document issuance, correction, voiding, supersession, and remediation actions must be attributable and auditable because invoices and receipts are commercial and finance-sensitive artifacts.

## Canonical Definitions

### Billing Document
The canonical supertype representing an invoice or receipt record and its routing metadata.

### Invoice
A canonical billing document that represents a commercial billing obligation, charge summary, or approved billable grouping according to FUZE domain policy. It is not itself payment proof.

### Receipt
A canonical billing document that confirms an approved payment or value-settlement event lineage according to allowed business rules. It is not a substitute for an invoice where invoice semantics are required.

### Invoice Line Item
Canonical line-level detail representing one billable component, charge category, or approved summarized billing element on an invoice.

### Receipt Allocation
Canonical linkage from a receipt to one or more source obligations or settlement-relevant source references, such as an invoice, billing cycle, credits top-up, or other approved commercial source.

### Billing Document Source
A normalized reference to an upstream commercial cause such as a subscription, billing cycle, usage batch, verified payment, credits purchase event, seat adjustment, or approved correction event.

### Document Delivery Record
A durable lineage record for rendering, generation, delivery attempt, access-link production, or delivery failure state associated with an invoice or receipt.

### Document Mutation Action
A durable action record for issuance, supersession, voiding, cancellation, regeneration, or remediation of a billing document.

### Supersession
The explicit replacement of one billing document by a later authoritative document while preserving the historical lineage of the prior one.

## Truth Class Taxonomy

Downstream implementations MUST preserve the following truth classes.

### 1. Canonical Identity Truth
The durable actor anchor represented by `account_id`.

### 2. Runtime Session Truth
Temporary authenticated runtime posture and privileged-session posture where relevant.

### 3. Collaborative Scope Truth
Canonical workspace, organization, and other approved subject-scope structures and lifecycle state.

### 4. Structural Authorization Truth
Role assignments, permission mappings, scoped grants, and scope applicability used to determine who may act.

### 5. Effective-Permission Truth
The final evaluated allow, deny, restricted, or review-required outcome for a concrete action.

### 6. Billing Truth
Recurring commitments, usage-rated charge state, included-usage posture, overage posture, seat commercial state, billing owner, and billing-scope responsibility owned by the subscriptions and usage billing domain.

### 7. Payment-Rail Truth
Verified payment input, normalized payment state, dispute posture, and reversal posture owned by the payment rails integration domain.

### 8. Platform Credits Semantic Truth
The canonical meaning of credits, class semantics, and ownership scope rules owned by the Platform Credits domain.

### 9. Credit Ledger and Settlement Truth
Authoritative credits mutation lineage, balance derivation, settlement posture, and reconciliation posture owned by the credit-ledger domain.

### 10. Invoice / Receipt Truth
Durable billing-document truth owned by this domain, including canonical invoice, receipt, source-reference, delivery, supersession, and remediation state.

### 11. Policy / Restriction Truth
Security, fraud, governance, compliance, review, and containment posture that may constrain billing-document issuance or visibility.

### 12. Derived Read-Model Truth
Invoice summaries, receipt summaries, download/access-link metadata, dashboards, exports, analytics, and support summaries derived from canonical records.

## Architectural Position in the Spec Hierarchy

This document sits below:

- `REFINED_SYSTEM_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `PAYMENT_RAILS_INTEGRATION_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`

and above:

- `INVOICING_RECEIPTS_API_SPEC.md`
- `REFUND_REVERSAL_AND_ADJUSTMENT_SPEC.md`
- `PAYMENT_FRAUD_AND_ABUSE_PREVENTION_SPEC.md`
- accounting/export workflows
- reconciliation workflows
- support/control-plane document remediation workflows
- product document-visibility surfaces

## System Boundaries

This document governs only the following platform-owned boundaries:

- canonical invoice and receipt semantics
- canonical billing-document supertype and source linkage
- invoice line-item and receipt-allocation semantics
- account-scoped and workspace-scoped document ownership and visibility
- document lifecycle state, delivery state, and mutation/remediation state
- supersession-safe correction rules
- API, event, audit, security, and operational expectations needed to preserve document truth

It does not govern:

- payment-provider verification in full depth
- recurring billing and usage-rating logic
- credits-ledger mutation mechanics
- exact tax engine internals
- exact PDF rendering implementation
- external accounting-export schema in full detail
- final refund policy logic

## Adjacent Boundaries

### Billing Domain
Owns subscription and usage-billing truth. Invoices derive from approved billing obligations, billing cycles, usage batches, seat changes, add-ons, and billing corrections, but invoices do not replace billing truth.

### Payment Rails Domain
Owns normalized payment truth. Receipts derive from approved payment or value-settlement outcomes and may reflect payment allocations, but provider-side payment state is not itself receipt truth.

### Platform Credits Domain
Owns credits semantics. Credits top-ups, credits-backed purchases, or credits-backed subscription funding may appear as document sources, but invoices and receipts are not credits balances.

### Credit Ledger and Settlement Domain
Owns authoritative credits mutation and settlement lineage. Receipt issuance may reflect settlement lineage and invoice-state transitions may reflect settlement status, but document truth remains distinct from ledger truth.

### Refund / Reversal / Adjustment Domain
Owns exception-case corrections. Document voiding or supersession may be triggered by approved refund or reversal flows, but exception-case truth remains separate and must link into this domain rather than overwrite it.

### Audit / Traceability Domain
Owns durable traceability surfaces for sensitive document issuance, supersession, delivery remediation, and control-plane actions.

### Product Domains
May trigger approved billable events or reference documents for user display, but do not own invoice or receipt semantics.

## Conflict Resolution Rules

When invoicing or receipt layers disagree, FUZE MUST resolve conflicts in the following order unless a higher-order policy explicitly overrides it:

1. canonical billing scope and billing owner truth
2. canonical upstream billing or normalized payment truth, depending on document class
3. canonical invoice or receipt record and its mutation lineage
4. active review, fraud, compliance, or restriction posture
5. delivery/render-ready state and access-link state
6. derived summaries, dashboards, exports, product-local views, and provider UI

Additional rules:

- provider UI MUST NOT outrank canonical receipt state
- product billing summaries MUST NOT outrank canonical invoice state
- credits balances or ledger balances MUST NOT be used as substitutes for receipts or invoices
- later corrective document actions MUST preserve lineage rather than erasing prior commercial truth
- ambiguous scope or source linkage MUST resolve to hold, review, or explicit correction rather than guessed issuance

## Default Decision Rules

When ambiguity exists and no narrower policy-specific rule is available, the platform MUST apply the following defaults:

- default to no invoice or receipt issuance without an approved source reference
- default to explicit account or workspace scope assignment before issuance
- default to no destructive rewrite; use supersession, voiding, or explicit remediation
- default to backend canonical records over frontend or provider summaries
- default to hold or review for broad-scope, tax-sensitive, or dispute-sensitive document correction
- default to preserving original source references, mutation lineage, and reason codes
- default to redacted derived visibility rather than absent internal truth when visibility is limited

## Roles / Actors / Entities

### Billing Scope Subject
The account or workspace to which the billing document belongs.

### Billing Owner
The commercial controller responsible for the billing obligation or payment context associated with the document.

### Invoice Issuer
The approved backend-owned domain pathway that generates canonical invoice records from approved billing truth.

### Receipt Issuer
The approved backend-owned domain pathway that generates canonical receipt records from approved payment or settlement truth.

### Document Consumer
A product, API surface, worker, support tool, export workflow, or user-facing surface that reads billing-document truth.

### Document Delivery Channel
The rendering or delivery path used to expose invoices or receipts through secure display, download, email, or other approved channels.

### Document Operator
A privileged internal actor allowed to perform bounded document correction, voiding, supersession, regeneration, or delivery remediation under policy.

## Ownership Model

- the invoicing and receipts domain owns canonical invoice, receipt, line-item, source-reference, allocation, delivery, and mutation-lineage truth
- the billing domain owns commercial obligations and usage-rated charge truth that may justify invoice issuance
- the payment rails domain owns normalized payment truth that may justify receipt issuance
- the credits and ledger domains own balances and settlement lineage, which may be referenced but not replaced by billing documents
- products and frontend surfaces consume billing-document truth but do not own it
- admin/control-plane may trigger bounded corrective workflows but do not become canonical document owners

## Authority / Decision Model

### Document Authority
This domain decides what constitutes a canonical invoice or receipt, how documents are linked to approved upstream commercial causes, and how document lifecycle state is represented.

### Upstream Source Authority
Billing and payment domains decide whether the upstream commercial condition exists that justifies document issuance.

### Delivery Authority
This domain decides delivery/render-ready posture, secure access-link generation, and document-distribution lineage.

### Correction Authority
This domain decides document supersession, voiding, regeneration, and remediation posture subject to policy, while preserving linkage to upstream refund/reversal or billing-correction truth.

### Product Authority
Products may reference and present documents but do not decide document semantics, document states, or correction policy.

## Canonical Document Classes

### Invoice
Invoices represent approved billing obligations or commercial billing records. They may summarize:
- subscription cycles
- usage-rated batches
- credits purchases
- seat changes
- add-ons
- approved grouped commercial events

Invoices are authoritative billing documents, not generic transaction notes.

### Receipt
Receipts represent confirmation of approved payment or value-settlement events according to allowed business rules. They may allocate to:
- one invoice
- one billing cycle
- one credits top-up
- one or more approved commercial sources where policy allows

Receipts are confirmation documents, not replacements for invoices where invoicing semantics are required.

### Distinction Rules
- one invoice MAY exist before a receipt
- one receipt MAY exist without an invoice only where policy explicitly allows that business model
- one payment event MUST NOT be treated as its own invoice unless the invoicing domain explicitly models that case
- one receipt MUST preserve allocation logic to its approved source(s)

## Source Reference Model

Canonical billing-document sources MAY include:

- subscription reference
- billing cycle reference
- usage batch reference
- credits purchase reference
- verified payment reference
- seat change reference
- plan-change reference
- approved adjustment or correction reference

### Source Rules
- every invoice or receipt MUST have at least one approved source reference
- source references MUST be durable and queryable
- grouped invoices MUST preserve grouping logic rather than flattening source identity
- corrective documents MUST preserve linkage to both original and superseding causes where relevant

## Lifecycle / Workflow Model

### Invoice Lifecycle
Supported semantic states SHOULD include:
- `draft_if_supported`
- `issued`
- `voided`
- `superseded`
- `cancelled_if_supported`

### Receipt Lifecycle
Supported semantic states SHOULD include:
- `issued`
- `voided_if_supported`
- `superseded_if_supported`

### Delivery Lifecycle
Supported semantic states SHOULD include:
- `pending_render`
- `rendered`
- `delivered`
- `delivery_failed`
- `expired`

### Mutation Action Lifecycle
Supported semantic states SHOULD include:
- `requested`
- `validated`
- `executed`
- `failed`
- `closed`

### Lifecycle Rules
- issued documents are durable business records
- supersession or voiding MUST preserve lineage
- receipts are issued only from approved payment or settlement outcomes
- invoice issuance derives from approved billing-document creation rules, not ad hoc UI triggers
- delivery status remains distinct from document issuance status

## Invariants

1. Invoices and receipts are distinct document classes.
2. Backend owns canonical billing-document truth.
3. Documents derive from approved upstream commercial state, not frontend assumptions.
4. Every document attaches to an explicit billing scope.
5. Receipts do not replace invoices where invoice semantics are required.
6. Document truth remains distinct from credits balances, token balances, payouts, and treasury resources.
7. Supersession and correction preserve lineage rather than rewriting history.
8. Delivery state remains distinct from issuance state.
9. Product summaries and access links remain derived and subordinate.
10. Sensitive document mutation remains attributable and auditable.

## Functional Rules

### Issuance Rule
Invoices and receipts MUST be issued only from approved upstream commercial events under backend-owned rules.

### Distinct Failure Rule
Missing invoice source, missing receipt source, invalid scope, delivery failure, payment dispute, correction review, and missing permission are different failure classes and MUST remain distinguishable internally.

### Scope-Correctness Rule
The platform MUST NOT silently attach a document to the wrong account or workspace scope. Wrong-scope document generation fails closed or enters explicit correction flow.

### Delivery Rule
Rendering and delivery MAY fail or be delayed without changing canonical document truth. Delivery remediation must remain explicit and auditable.

### No Product-Local Document Truth Rule
Products MUST NOT create unofficial invoice or receipt systems for shared platform commerce, nor display provider-side or billing-summary artifacts as canonical invoices or receipts.

### Supersession Rule
Corrective document actions MUST create explicit supersession, voiding, cancellation, or regeneration lineage rather than destructive overwrite.

## Permission / Access Considerations

Billing-document truth does not grant mutation authority by itself.

Required constraints:
- actors may view only account or workspace documents for scopes they are authorized to access
- workspace document visibility and document mutation authority are separate concerns
- privileged correction, voiding, supersession, and remediation require bounded admin/control-plane pathways
- document access-link generation remains subject to scope visibility and policy
- UI visibility of a billing document does not imply mutation authority

## API / Contract Implications

The billing-document layer MUST expose stable platform-consumable contracts for:

- listing visible invoices and receipts by allowed scope
- reading canonical invoice and receipt details
- issuing invoices and receipts through internal backend-owned pathways
- linking documents to approved source references
- reading delivery/render-ready status
- generating bounded access/display metadata
- executing admin/control-plane correction-safe document actions

### Contract Rules
- public and first-party surfaces read document truth but do not create it directly
- internal issuance APIs require approved upstream references and idempotent issuance behavior
- admin routes require reason-coded privileged action
- APIs MUST distinguish invoice and receipt lifecycle state, delivery state, and mutation-action state
- document APIs MUST preserve machine-readable linkage to source references, supersession lineage, and audit lineage

## Event / Async Implications

The invoicing and receipts domain SHOULD support event families such as:

- `invoice.issued`
- `invoice.voided`
- `invoice.superseded`
- `receipt.issued`
- `receipt.voided`
- `receipt.superseded`
- `document.rendered`
- `document.delivered`
- `document.delivery_failed`
- `document.remediation.executed`

Event rules:
- events MUST be emitted after canonical commit
- events MUST preserve document identity, scope, source references, lifecycle state, and correlation references
- consumers MUST treat events as synchronization signals rather than document-truth substitutes
- retries MUST preserve idempotent issuance and mutation behavior
- downstream exports, notifications, support views, and reconciliation consumers MUST remain linked to canonical document records

## Data Model / Storage Implications

At minimum, the invoicing and receipts domain SHOULD support semantic representation of:

### billing_documents
- canonical supertype or routing record for invoices and receipts

### invoices
- canonical invoice records

### invoice_line_items
- canonical line-level charge detail

### receipts
- canonical receipt records

### receipt_allocations
- allocations linking receipts to invoices, billing cycles, credits purchases, or other approved source references

### billing_document_sources
- normalized references to subscriptions, cycles, usage batches, verified payments, credits purchase events, or other approved commercial causes

### document_delivery_records
- rendering and delivery lineage for invoice or receipt documents

### document_mutation_actions
- issuance, supersession, cancellation, regeneration, and remediation actions

### billing_document_audit_events
- durable audit linkage for sensitive document actions

### Derived Views
- invoice summary views
- receipt summary views
- document access-link/download views

## Read Model / Projection / Reporting Rules

- invoice summaries, receipt summaries, and access-link/download views MAY support UX and low-risk reads, but MUST remain explicitly derived
- sensitive document actions MUST re-check canonical backend state rather than relying on stale dashboard or frontend summaries
- reports and exports MAY aggregate document state, but MUST remain traceable to canonical invoice and receipt records
- provider UI, email content, or downloaded cached files MUST NOT outrank canonical backend document state
- projection lag MUST NOT silently create or erase billing-document truth

## Security / Risk / Abuse Controls

The billing-document layer is a sensitive commercial and finance-facing control surface and MUST enforce:

- backend-owned issuance and mutation logic
- strict scope-aware visibility checks
- privileged correction/remediation controls with reason-coded audit trails
- fraud/risk/review posture integration where disputed or reversed commercial events affect document state
- protected access-link generation and delivery remediation
- resistance to provider-side, product-side, or support-side document spoofing
- protection against silent destructive rewrite of historical commercial documents

## Boundary Violation Detection / Non-Canonical Patterns

The following patterns are non-canonical and forbidden:

- frontend or product generation of canonical invoices or receipts outside backend-owned domain rules
- using payment-provider UI as a receipt substitute
- using billing summary rows or ledger entries as invoice substitutes
- treating receipts as interchangeable with invoices where invoice semantics are required
- silent document rewrite that erases correction history
- wrong-scope document issuance due to heuristic guessing
- exposing delivery state as if it were the same thing as issuance truth

Implementations SHOULD detect and surface these conditions through diagnostics, audit, and operational alerts.

## Audit / Traceability Requirements

At minimum, FUZE MUST be able to report on:

- document issuance
- document scope
- line items and source references
- payment or settlement allocation references for receipts
- delivery/render status
- supersession, voiding, cancellation, and regeneration lineage
- billing owner context where relevant
- operator-driven document remediation
- whether a visible view is canonical or derived

Sensitive document actions SHOULD generate immutable audit linkage sufficient for finance, support, dispute, and compliance review.

## Failure Handling / Edge Cases

### Billing Event Exists but Invoice Generation Fails
The platform MUST preserve a recoverable pending or remediation state rather than silently losing the document obligation.

### Payment Verified but Receipt Issuance Lags
Receipt issuance MAY be delayed, but the normalized payment truth and eventual receipt lineage must remain recoverable and auditable.

### Scope Misassignment Discovered Later
The platform MUST use explicit corrective supersession or regeneration flow rather than destructive rewrite.

### Delivery Fails After Issuance
Canonical document truth remains `issued`; delivery state becomes `delivery_failed` and remediation must remain explicit.

### Receipt Needs Allocation Across More Than One Source
This is permitted only through explicit receipt-allocation semantics. Hidden grouping is forbidden.

### Provider Revocation or Refund Arrives After Receipt Issuance
The platform MUST route the effect through normalized reversal/correction logic and preserve original receipt lineage plus later corrective state.

### Product Wants “Quick Transaction Note” Instead of Canonical Document
Allowed only if clearly non-canonical and not presented as invoice or receipt truth for shared platform commerce.

## Operational Considerations

Operational systems SHOULD support:

- deterministic invoice and receipt issuance pipelines
- rendering and delivery health monitoring
- explicit document source-link validation
- reconciliation between billing/payment events and document issuance
- support and finance control-plane tools for bounded remediation
- wrong-scope detection
- duplicate issuance detection
- runbooks for missing document, late receipt, delivery failure, source mismatch, and supersession flows

Because billing documents are shared commercial artifacts, operational defects can affect multiple products and downstream finance/reporting surfaces. Operational posture MUST therefore be platform-grade rather than product-local.

## Migration / Compatibility / Supersession Considerations

Migrations into this canonical model MUST enforce the following:

- product-local invoice or receipt systems with shared platform meaning must be retired or explicitly marked non-canonical
- provider-facing or UI-only document artifacts must not be represented as canonical billing documents unless normalized into this domain
- old summary-only document flows must be upgraded so lineage, scope, and source references are explicit
- backward-compatibility adapters MAY preserve older document numbers, URLs, or layout conventions, but canonical document semantics, lifecycle state, and correction lineage MUST remain explicit internally
- historical assumptions that “paid” equals “receipt exists” or “bill exists” are superseded within this scope

## Implementation-Contract Guardrails

Downstream implementations MUST preserve all of the following:

1. invoices and receipts remain distinct document classes with different semantics
2. backend remains the canonical owner of billing-document truth
3. every document attaches to an explicit canonical scope
4. invoices, receipts, billing truth, payment truth, credits truth, and ledger truth remain distinct
5. product surfaces consume canonical document truth rather than inventing substitutes
6. provider UI and derived summaries MUST NOT outrank canonical backend records
7. delivery state remains distinct from issuance state
8. supersession, voiding, regeneration, and remediation remain explicit and lineage-preserving
9. degraded runtime conditions MUST NOT silently widen visibility or silently hide document ambiguity for sensitive commercial actions
10. idempotency and replay safety MUST be preserved for issuance, regeneration, delivery remediation, and corrective workflows
11. reason capture, source references, scope references, and audit lineage MUST remain explicit for high-impact document changes
12. downstream teams MUST NOT optimize away source linkage, allocation logic, scope references, lifecycle states, or supersession lineage where those elements protect auditability, supportability, compliance posture, or migration safety

## Downstream Execution Staging

This specification implies the following preferred downstream implementation order:

1. stabilize invoice and receipt class semantics
2. stabilize scope attachment and source-reference rules
3. stabilize lifecycle, delivery, and mutation-action states
4. integrate billing and payment upstream issuance triggers through explicit contracts
5. integrate audit, reporting, support/control-plane, and corrective workflows
6. integrate derived views, access-link generation, rendering, and delivery surfaces

## Required Downstream Specs / Contract Layers

This specification directly requires compatible downstream refinement and implementation-contract work in:

- `INVOICING_RECEIPTS_API_SPEC.md`
- `REFUND_REVERSAL_AND_ADJUSTMENT_SPEC.md`
- `PAYMENT_FRAUD_AND_ABUSE_PREVENTION_SPEC.md`
- accounting/export workflows
- reporting and reconciliation workflows
- support/control-plane document remediation workflows
- product billing-document visibility surfaces

## Canonical Examples / Anti-Examples

### Canonical Example 1 — Invoice for Workspace Renewal
A workspace renewal creates a billing obligation. The invoicing domain issues a workspace-scoped invoice linked to the subscription cycle and billing owner. Later payment may create a separate receipt.

### Canonical Example 2 — Receipt for Credits Purchase
A verified payment for a credits package is normalized through the payment domain. The invoicing domain issues a receipt linked to the verified payment and credits purchase source references. The receipt is not the credits balance.

### Canonical Example 3 — Superseded Invoice
A billing correction requires replacing an incorrect invoice. The original invoice remains preserved and a superseding invoice explicitly links back to it.

### Canonical Example 4 — Delivery Failure After Issuance
An invoice is correctly issued but PDF/email delivery fails. Delivery remediation occurs without changing the canonical invoice issuance truth.

### Anti-Example 1 — Provider UI as Receipt
A payment-provider dashboard showing `paid` is treated as the user’s canonical receipt. This is forbidden.

### Anti-Example 2 — Receipt as Invoice Substitute
A receipt is used in a context where the platform requires invoice semantics, with no invoice record. This is forbidden unless policy explicitly says invoices are not required for that business model.

### Anti-Example 3 — Product-Local Invoice
A product generates its own invoice PDF and treats it as canonical shared platform billing truth. This is forbidden.

### Anti-Example 4 — Silent Rewrite
Support edits an issued invoice in place so prior line items and history disappear. This is forbidden.

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
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `PAYMENT_RAILS_INTEGRATION_SPEC.md`
- `REFUND_REVERSAL_AND_ADJUSTMENT_SPEC.md`
- `PAYMENT_FRAUD_AND_ABUSE_PREVENTION_SPEC.md`
- `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`

This specification directly governs or materially informs:

- `INVOICING_RECEIPTS_API_SPEC.md`
- `REFUND_REVERSAL_AND_ADJUSTMENT_SPEC.md`
- `PAYMENT_FRAUD_AND_ABUSE_PREVENTION_SPEC.md`
- accounting/export workflows
- reporting and reconciliation workflows
- support/control-plane document remediation workflows
- product billing-document visibility surfaces

## Explicitly Deferred Items

The following are intentionally deferred to adjacent or downstream specifications:

- exact PDF rendering internals
- exact tax-engine internals
- exact accounting-export schema and formats
- exact email/render delivery channel implementation details
- exact receipt numbering and jurisdiction-specific formatting rules
- exact external compliance wording and disclosure templates
- exact document storage/indexing implementation details

These do not weaken the canonical invoicing and receipts model established here.

## Final Normative Summary

The FUZE invoicing and receipts domain is the platform-owned billing-document layer for approved commercial operations. It defines invoices and receipts as distinct, durable document classes derived from approved upstream billing and payment truth. It preserves explicit scope attachment, source linkage, allocation logic, delivery lineage, and supersession-safe correction behavior. It is not a billing engine, not a payment processor, not a credits balance system, and not a frontend rendering convenience layer.

This document is the canonical FUZE rule set for invoicing and receipt semantics.
