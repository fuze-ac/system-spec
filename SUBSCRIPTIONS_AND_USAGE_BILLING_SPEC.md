# SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC

## Title
FUZE Subscriptions and Usage Billing Specification

## Document Metadata

- Document Name: `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Version: 1.1.0
- Effective Date: 2026-04-21
- Last Updated: 2026-04-21
- Reviewed On: 2026-04-21
- Document Owner: FUZE Platform Commerce and Billing Architecture
- Approval Authority: FUZE Platform Architecture and Governance Authority
- Review Cadence: Quarterly or upon material change to Platform Credits semantics, credit-ledger settlement posture, entitlement posture, pricing model categories, payment normalization, invoicing, fraud controls, workspace scope rules, or AI usage metering
- Governing Layer: Platform core / shared commercial infrastructure / subscriptions and usage billing
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, commerce and billing engineering, payments engineering, credits and ledger engineering, product engineering, finance operations, support operations, security engineering, audit/compliance, API design, data engineering, platform operations, implementation-contract authors
- Primary Purpose: Define the canonical FUZE subscriptions and usage billing model for recurring commercial commitments, usage-rated charging, seat-aware billing, included-usage posture, overage posture, billing-scope ownership, and commercial state transitions across products and workspaces while preserving strict separation from payment-rail truth, Platform Credits truth, entitlement truth, authorization truth, invoice/document truth, and final profit/accounting truth
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
  - `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
  - `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md`
  - `IDENTITY_AND_ACCOUNT_SPEC.md`
  - `WORKSPACE_AND_ORGANIZATION_SPEC.md`
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
  - `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
  - `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
  - `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
  - `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
  - `PLATFORM_CREDITS_SPEC.md`
  - `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
  - `PAYMENT_RAILS_INTEGRATION_SPEC.md`
  - `INVOICING_AND_RECEIPTS_SPEC.md`
  - `REFUND_REVERSAL_AND_ADJUSTMENT_SPEC.md`
  - `PAYMENT_FRAUD_AND_ABUSE_PREVENTION_SPEC.md`
  - `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
  - `AI_USAGE_METERING_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
- Primary Downstream Dependents:
  - `PAYMENT_RAILS_INTEGRATION_SPEC.md`
  - `INVOICING_AND_RECEIPTS_SPEC.md`
  - `REFUND_REVERSAL_AND_ADJUSTMENT_SPEC.md`
  - `PAYMENT_FRAUD_AND_ABUSE_PREVENTION_SPEC.md`
  - `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
  - `AI_USAGE_METERING_SPEC.md`
  - `SUBSCRIPTIONS_BILLING_API_SPEC.md`
  - `WORKSPACE_ORGANIZATION_API_SPEC.md`
  - `ROLE_PERMISSION_ACCESS_API_SPEC.md`
  - product-specific monetization and rating specifications
  - finance, reconciliation, reporting, support/control-plane, and remediation workflows
- Supersedes: Earlier or weaker FUZE billing writeups that did not clearly separate recurring commercial truth, usage-rated truth, credits-backed settlement, entitlement activation, billing-scope ownership, and downstream document or ledger effects
- Superseded By: Not yet known
- Related Decision Records: Not yet known
- Canonical Status Note: This document is the canonical FUZE specification for subscriptions and usage billing semantics. Downstream APIs, products, workers, dashboards, support tooling, pricing adapters, invoice generators, payment integrations, and credits consumers MUST NOT reinterpret plan labels, UI state, raw payment signals, raw credits balances, or product-local metering as substitutes for the canonical billing model defined here.
- Implementation Status: Normative architecture baseline; downstream API, service, runtime, event, audit, and control-plane contracts must conform
- Approval Status: Drafted for refined-system inclusion; formal approval record not yet attached
- Change Summary:
  - strengthened separation between billing truth, credits truth, entitlement truth, and payment-rail truth
  - clarified billing scope, billing owner, seat model, renewal posture, included usage, overage, and hybrid plan semantics
  - hardened usage-rating, idempotency, auditability, and degraded-state handling
  - expanded implementation-contract guardrails so products cannot create local billing truth, local seat truth, or hidden charge paths

## Purpose

This specification defines the canonical subscriptions and usage billing model of the FUZE platform.

Its purpose is to make explicit:

- how recurring subscriptions, usage-rated charging, seats, included usage, overage, add-ons, and product-level monetization resolve through one shared platform commercial layer
- how billing differs from payment normalization, Platform Credits, entitlement activation, actor authorization, invoice generation, and profit/accounting determination
- how account-scoped and workspace-scoped billing responsibilities are represented and preserved
- how products may monetize differently without creating incompatible billing truth
- how billing state transitions, renewals, restrictions, dunning/post-funding recovery, and corrections must behave
- how APIs, events, audit lineage, ledger interactions, and product/runtime enforcement must preserve a coherent shared commercial model

FUZE is a multi-product platform with both recurring access value and usage-sensitive value. The billing layer therefore must support both subscriptions and usage billing inside one platform-owned framework tied to Platform Credits and normalized payment state rather than product-local commerce shortcuts. fileciteturn32file0L1-L84

## Scope

This specification governs:

- canonical subscription semantics
- canonical usage-billing semantics
- relationship between subscriptions, usage-rated charging, Platform Credits, and entitlement activation
- account-scoped and workspace-scoped billing behavior
- seat-aware commercial behavior
- included usage and overage posture
- billing owner and billing responsibility semantics
- renewal, cancellation, past-due, grace, restriction, and correction behavior
- API, event, data-model, audit, security, and operational implications of billing truth
- migration constraints preventing product-local billing fragmentation

This specification does not define in full depth:

- payment verification or payment-channel behavior
- exact ledger implementation or settlement mechanics
- invoice rendering or receipt formatting
- exact pricing tables or monetization campaign configuration
- exact AI meter formulas or product-specific included-usage formulas
- exact accounting recognition, treasury treatment, or distributable-profit rules

Those concerns are refined in adjacent payment, credits, ledger, invoicing, pricing, AI-usage, and treasury specifications. fileciteturn32file0L10-L35

## Out of Scope

This specification is explicitly out of scope for:

- proving actor identity or session validity
- deciding final actor authorization for a protected action
- defining the semantic meaning of a Platform Credit
- defining final credits-ledger truth
- defining invoice, receipt, or tax-document semantics in full depth
- defining payment processor state machines in full depth
- defining final accounting-book or profit/payout truth
- defining every per-product price point, seat formula, or quota number

## Design Goals

1. Provide one shared billing architecture across the FUZE ecosystem.
2. Support subscriptions and usage billing in the same commercial model.
3. Keep billing compatible with Platform Credits as the internal spend unit.
4. Support both individual and workspace-scoped commercial contexts.
5. Allow product-specific monetization without product-specific billing fragmentation.
6. Preserve a clear distinction between billing truth, entitlement truth, credits truth, and payment truth.
7. Make billing outcomes auditable, policy-driven, and operationally scalable across products.
8. Support AI-native and workflow-native products whose value is often usage-sensitive rather than purely access-sensitive.
9. Preserve clean scope ownership, seat ownership, and billing-owner lineage.
10. Support durable downstream API, document, ledger, and reconciliation contracts.

## Non-Goals

This specification is not intended to:

- make subscriptions the only commercial model in FUZE
- make usage billing the only commercial model in FUZE
- require every product to use the same visible pricing shape
- let products bill directly against raw external payment events without platform normalization
- redefine Platform Credits as subscription objects
- make billing state equivalent to invoice state, ledger state, profit state, or payout state
- let products create isolated commercial systems that bypass shared platform rules

## Core Principles

### 1. Shared Billing Architecture Principle
Products may differ in what they charge for, but recurring access, usage-based charging, and entitlement activation MUST resolve through one shared platform billing architecture tied to Platform Credits and normalized payment state. fileciteturn32file0L69-L84

### 2. Billing-Is-Not-Payment Principle
Payment-rail state is upstream commercial input. It is not by itself recurring billing truth.

### 3. Billing-Is-Not-Credits Principle
Billing determines when and why commercial obligations should create or consume value. Platform Credits remain the internal spend unit, not the billing object itself. fileciteturn32file0L270-L286

### 4. Billing-Is-Not-Entitlement Principle
Billing state may activate or restrict entitlement posture, but it is not the same thing as entitlement truth. fileciteturn32file0L288-L304

### 5. Billing-Scope Principle
Every billable object MUST belong to one explicit commercial scope, usually an account or a workspace. Wrong-scope charging is forbidden. fileciteturn32file0L170-L193

### 6. Hybrid Commercial Principle
FUZE MUST support both recurring plans and usage-rated charging because different products create value through different shapes. fileciteturn32file0L86-L120

### 7. Seat-Separation Principle
Seat state and membership state are related but not identical. Seat billing MUST remain explicit and platform-consistent. fileciteturn32file0L320-L338

### 8. Deterministic Renewal Principle
Renewal, downgrade, grace, restriction, and recovery posture MUST be explicit, deterministic, and auditable. fileciteturn32file0L356-L370

### 9. Idempotent Usage Principle
Usage-rated charging MUST be idempotency-aware so async or repeated usage events do not create duplicate billing effects. fileciteturn32file0L245-L250

### 10. No Product-Local Billing Truth Principle
Products MAY define chargeable value but MUST NOT define separate recurring billing truth, separate balance systems, or incompatible meter semantics. fileciteturn32file0L306-L319

## Canonical Definitions

### Subscription
A recurring commercial arrangement that grants access to a plan, tier, product, workspace, module, or service bundle over a defined billing period. fileciteturn32file0L141-L147

### Usage Billing
Charging based on measured consumption rather than only fixed recurring access. fileciteturn32file0L141-L148

### Plan
A named commercial package that defines recurring access conditions, included usage, seat allowances, or feature boundaries. fileciteturn32file0L145-L151

### Billing Scope
The commercial owner of the chargeable state, typically either an account or a workspace. fileciteturn32file0L154-L159

### Billing Owner
The commercial controller responsible for the billing obligation, which may be an account holder, workspace owner, billing manager, or approved enterprise-managed context. Billing owner is not automatically identical to every product operator. fileciteturn32file0L340-L355

### Included Usage
A defined quantity of usage bundled into a subscription before additional metered charging begins. fileciteturn32file0L156-L160

### Overage
Usage consumed above included thresholds and charged according to policy. fileciteturn32file0L156-L161

### Chargeable Action
A product or platform action mapped to usage billing, credits consumption, or other approved commercial effect. fileciteturn32file0L161-L162

### Seat
A licensable or allocatable participation unit inside a workspace-billed or team-billed context. Seat state may affect billing without being identical to membership state. fileciteturn32file0L148-L153 fileciteturn32file0L320-L338

### Hybrid Subscription
A recurring plan with included usage and overage logic. fileciteturn32file0L219-L229

### Billing Period
The interval over which recurring subscription value is granted and evaluated. fileciteturn32file0L158-L161

### Billing Correction
A controlled, auditable repair of mistaken commercial state, such as wrong scope, wrong seat count, wrong plan assignment, or backdated rating/renewal repair, executed through approved control-plane pathways.

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

### 6. Entitlement Truth
Commercial and policy eligibility for products, plan features, capability classes, and usage posture. Billing may justify entitlement changes but does not replace entitlement truth. fileciteturn31file13L18-L31

### 7. Platform Credits Semantic Truth
The canonical meaning of credits, class semantics, ownership scopes, issuance categories, spend semantics, and transfer restrictions. fileciteturn32file2L1-L20

### 8. Credit Ledger and Settlement Truth
Authoritative mutation lineage, balance derivation, reservation state, settlement posture, and reconciliation posture for credits-backed economic effects. fileciteturn32file1L1-L19

### 9. Billing Truth
Recurring commitments, usage-rated charge state, included-usage posture, overage posture, seat commercial state, billing owner, and billing-scope responsibility owned by this domain.

### 10. Payment-Rail Truth
Verified payment input, channel behavior, charge outcomes, and dispute signals that may justify billing state transitions but do not replace billing truth.

### 11. Invoice / Receipt Truth
Commercial document truth derived from approved billing and payment events rather than replacing them. fileciteturn31file12L20-L44

### 12. Derived Read-Model Truth
UI plan badges, dashboard summaries, billing warnings, usage summaries, exports, and support views derived from canonical records.

## Architectural Position in the Spec Hierarchy

This document sits below:

- `REFINED_SYSTEM_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
- `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md`
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`

and above:

- `PAYMENT_RAILS_INTEGRATION_SPEC.md`
- `INVOICING_AND_RECEIPTS_SPEC.md`
- `REFUND_REVERSAL_AND_ADJUSTMENT_SPEC.md`
- `PAYMENT_FRAUD_AND_ABUSE_PREVENTION_SPEC.md`
- `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
- `AI_USAGE_METERING_SPEC.md`
- `SUBSCRIPTIONS_BILLING_API_SPEC.md`
- product-specific monetization and rating specifications
- finance, reconciliation, reporting, support/control-plane, and remediation workflows

## System Boundaries

This document governs only the following platform-owned boundaries:

- canonical subscription model
- canonical usage-billing model
- billing scope and billing owner semantics
- seat-aware commercial state
- included-usage and overage posture
- renewal, cancellation, grace, past-due, restriction, and recovery semantics
- relationship between billing truth, credits-backed settlement, entitlement activation, and invoice/document generation
- minimum event, API, audit, security, and operational rules for the billing domain

It does not govern:

- payment provider state machines in full depth
- exact credits-ledger implementation
- exact invoice document lifecycles
- exact product pricing numbers
- exact metering math for every product
- exact treasury or accounting treatment

## Adjacent Boundaries

### Platform Credits Domain
Owns the shared internal spend unit. Billing consumes credits as the internal settlement-capable unit but does not redefine what a credit is. fileciteturn32file2L1-L20

### Credit Ledger and Settlement Domain
Owns authoritative credits mutation lineage, balances derived from entries, reservation semantics, and settlement-grade economic recording. Billing determines what should happen commercially; the ledger records the resulting credits effect. fileciteturn32file1L1-L19

### Entitlement Domain
Owns whether a subject is commercially and policy-eligible to use a product or capability. Billing may activate, restrict, or degrade entitlements, but entitlement remains separate truth. fileciteturn31file16L1-L18

### Payment Rails Domain
Owns payment intake, verification, channel constraints, and dispute signals. Payment success may justify billing activation or recovery but does not itself become billing truth.

### Invoicing and Receipts Domain
Owns billing-document truth. Invoices and receipts derive from approved billing events and must preserve document lineage separately from billing truth. fileciteturn31file12L20-L44

### Pricing Domain
Owns commercial charge rules, rate posture, seat formulas, included-usage limits, overage rates, and package structure. Billing consumes pricing outputs but does not redefine them.

### AI Usage Metering Domain
Owns usage measurement and attribution for AI-native or usage-sensitive actions. Billing uses metering outputs to determine included-versus-chargeable posture, but metering is not billing truth.

### Authorization Domain
Owns actor authority. A workspace may have a valid subscription while a specific actor still lacks permission to mutate billing state or spend shared credits.

## Conflict Resolution Rules

When billing-related layers disagree, FUZE MUST resolve conflicts in the following order unless a higher-order policy explicitly overrides it:

1. canonical billing scope and owner-scope truth
2. canonical billing state and lifecycle truth
3. active policy hold, fraud/risk hold, review, or restriction posture
4. canonical entitlement and credits-supporting inputs required by the product policy
5. payment, invoice, and reporting derivatives
6. derived dashboards, UI badges, exports, product caches, and frontend assumptions

Additional rules:

- subscription state MUST NOT be inferred solely from payment-provider UI or customer screenshots
- credits balance MUST NOT by itself substitute for subscription state
- entitlement activation MUST NOT be inferred solely from stale UI plan badges
- seat and member counts MUST NOT be collapsed into each other without explicit policy
- later billing correction MUST preserve lineage rather than silently rewriting past commercial truth

## Default Decision Rules

When ambiguity exists and no narrower policy-specific rule is available, the platform MUST apply the following defaults:

- default to no commercial effect without explicit billing state mutation
- default to explicit billing scope over heuristic scope inference
- default to no usage charge without an attributable usage record and applicable pricing rule
- default to no entitlement activation when canonical commercial posture cannot be established safely
- default to review or hold for high-impact backdated billing repair or broad-scope correction
- default to canonical backend billing reads over frontend assumptions and stale caches
- default to preserving historical subscription and usage lineage rather than destructive overwrite

## Roles / Actors / Entities

### Billing Scope Subject
The account or workspace that owns the subscription, usage obligation, seat package, or other commercial responsibility.

### Billing Owner
The entity or designated controller responsible for the commercial obligation.

### Plan
The named recurring commercial package applied to the billing scope.

### Usage Meter
The canonical rated meter that identifies a measurable usage family tied to pricing and included usage rules.

### Seat Allocation
The commercial participation state tied to a workspace-billed seat model.

### Billing Consumer
A product, API, worker, or internal service that requests rating, creates billable events, or consumes billing status.

### Renewal Engine
The platform-controlled mechanism that moves subscriptions through renewal or recovery posture.

### Billing Operator
A tightly bounded internal actor or approved automated pathway allowed to execute billing corrections or support remediation under policy.

## Ownership Model

- the subscriptions and usage billing domain owns recurring commercial commitments, usage-rated billing posture, billing scope, billing owner, seat-aware commercial state, included usage posture, overage posture, renewal posture, and related lifecycle states
- the pricing domain owns rate logic and pricing tables
- the credits and ledger domains own internal spend unit semantics and economic mutation recording
- the entitlement domain owns eligibility truth downstream of billing activation logic
- products may emit billable events and read billing outcomes, but they do not own recurring billing truth
- support/control-plane may trigger bounded corrections but do not become billing truth owners

## Authority / Decision Model

### Platform Constitutional Authority
The platform architecture and governance layers decide that FUZE uses one shared subscriptions and usage billing architecture rather than product-local billing silos. fileciteturn32file0L69-L84

### Billing Domain Authority
This domain decides subscription states, usage-billing posture, included-usage classification, overage classification, billing owner, seat commercial state, and lifecycle semantics.

### Pricing Authority
The pricing domain decides what should be charged and how products package recurring and usage value.

### Settlement Authority
Credits and ledger domains decide how approved commercial effects become economic balance movement and final settlement.

### Product Authority
Products decide what counts as valuable or chargeable inside their domain, but only within the shared billing architecture and approved platform contracts. fileciteturn32file0L306-L319

## Canonical Commercial Layers

FUZE commercial architecture contains the following canonical layers:

1. **Payment Input Layer** — approved payment rails receive external value.
2. **Payment Normalization Layer** — payment state is verified and normalized into platform-commercial truth.
3. **Platform Credits Layer** — verified value becomes Platform Credits according to policy.
4. **Subscriptions and Usage Billing Layer** — recurring plans, seat logic, metered usage, feature unlocks, and chargeable actions are evaluated.
5. **Entitlement Layer** — commercial outcomes determine whether the subject may access products or features.
6. **Reporting and Reconciliation Layer** — billing outcomes become visible through invoices, usage records, ledger records, receipts, and reports. fileciteturn32file0L121-L139

This layered structure is mandatory because it prevents raw payment state, raw credits balances, and entitlement outcomes from being conflated.

## Billing Scope Model

### Account Scope
The subscription or usage obligation belongs to an individual account. Appropriate for solo use, personal AI usage, personal tool subscriptions, and self-service purchases. fileciteturn32file0L164-L179

### Workspace Scope
The subscription or usage obligation belongs to a workspace. Appropriate for team plans, seat-based products, shared AI usage pools, business subscriptions, collaborative usage, and shared credits-backed activity. fileciteturn32file0L180-L193

### Scope Rules
- every billable object MUST belong to one explicit scope
- account scope and workspace scope MUST remain distinguishable
- products MUST know which scope they are charging
- invoices, receipts, and reporting MUST preserve scope clarity
- scope changes require explicit migration or reassignment logic
- wrong-scope charge or wrong-scope renewal MUST fail closed and be auditable

## Subscription Model

FUZE MUST support recurring access arrangements across products and scopes.

### Minimum Subscription Capabilities
- plan creation and plan versioning
- recurring billing period definition
- account-scoped subscription ownership
- workspace-scoped subscription ownership
- subscription status tracking
- seat and member association where relevant
- renewal behavior
- cancellation behavior
- grace, restriction, or dunning states where policy requires
- integration with credits consumption or other approved funding logic fileciteturn32file0L194-L208

### Recommended Subscription States
- `pending_activation`
- `active`
- `trialing`
- `grace_period`
- `past_due`
- `restricted`
- `canceled`
- `expired` fileciteturn32file0L209-L217

### Lifecycle Rules
The canonical subscription lifecycle is:
1. created
2. pending funding or activation
3. active
4. renewing
5. grace or pending recovery if needed
6. restricted or past due if payment or credits conditions fail
7. canceled or expired
8. archived for reporting retention fileciteturn32file0L218-L229

Additional rules:
- activation MUST be tied to valid commercial state
- subscription state MUST remain distinct from raw payment-rail state
- subscription state MUST remain distinct from credits balance by itself
- cancellation MUST preserve history
- post-cancel access behavior MUST be policy-defined and propagated downstream

## Subscription Types

At minimum, the platform SHOULD support:

### Account Subscription
Recurring plan for one account.

### Workspace Subscription
Recurring plan owned by a workspace.

### Seat-Based Subscription
Workspace subscription where member count, assigned seats, or operator seats matter.

### Hybrid Subscription
Recurring plan with included usage and overage logic. fileciteturn32file0L230-L243

This variety is necessary because FUZE products differ in how they deliver value.

## Usage Billing Model

Usage billing MUST support charging for measurable consumption.

### Representative Chargeable Usage
- AI inference actions
- advanced analysis jobs
- document or report generation
- workflow execution
- premium alert runs
- data-processing tasks
- automation steps
- compute-intensive product functions
- add-on operations fileciteturn32file0L245-L258

### Usage-Billing Requirements
At minimum, the platform MUST be able to:
- define a usage meter
- associate usage with a product and scope
- determine whether usage is included or chargeable
- convert usage into credits deduction or equivalent commercial effect
- record usage with sufficient attribution for audit and reconciliation
- prevent duplicate charging through idempotency-aware design fileciteturn32file0L245-L266

### Included Usage and Overage Model
Plans MAY include bundled usage before metered overage applies. Once included thresholds are exceeded, the platform MAY:
- deduct additional Platform Credits
- block further usage until top-up
- require add-on purchase
- recommend higher tier
- apply usage-based charges according to product policy fileciteturn32file0L267-L287

### Principle
Included usage improves predictability. Overage improves fairness and sustainability, especially for AI-heavy products. fileciteturn32file0L281-L287

## Seat Model

The platform MUST support seat-oriented billing where relevant.

### Seat Concepts
A seat is a licensable or allocatable participation unit in a workspace-billed environment. Seat state MAY include:
- assigned seat count
- occupied seat count
- pending seat invitations
- active seat entitlement
- seat-based pricing tiers
- seat-limit enforcement
- seat expansion through plan upgrade or credits-backed add-ons fileciteturn32file0L320-L338

### Seat Rules
- seats belong to the billing scope, typically the workspace
- seat assignment and membership are related but not identical
- products may define when seats matter, but seat billing semantics MUST remain platform-consistent
- seat changes MUST be auditable and reflect in billing state
- seat removal and membership removal MUST not be silently conflated in downstream systems

## Billing Ownership Model

The billing system MUST maintain explicit ownership of commercial responsibility.

### Representative Billing Owners
- personal account paying for personal subscription
- workspace owner funding workspace plan
- designated billing manager handling workspace payments
- future enterprise-managed billing context fileciteturn32file0L340-L355

### Billing Ownership Rules
- billing owner is not always the same as every product operator
- workspace billing does not collapse into personal identity
- billing-owner changes MUST be explicit
- billing records MUST preserve historical responsibility context
- products MUST NOT improvise billing ownership locally

## Renewal Model

Recurring subscriptions require explicit renewal posture.

### Renewal Funding Paths
Renewal MAY be funded through:
- available credits in the billing scope
- fresh payment input through approved payment rails
- approved enterprise settlement pathways where modeled fileciteturn32file0L356-L370

### Renewal Rules
Renewal behavior MUST define:
- renewal date or cycle boundary
- what happens if credits are insufficient
- whether grace period exists
- whether access is immediately restricted
- whether auto-top-up or renewal-helper flows exist
- what dunning or recovery path applies if relevant fileciteturn32file0L356-L370

Renewal logic MUST be deterministic and visible.

## Cancellation, Restriction, and Past-Due Model

### Cancellation Types
The platform MAY support:
- cancel at period end
- immediate cancellation
- scheduled downgrade
- support/admin cancellation under policy
- workspace closure-driven cancellation fileciteturn32file0L371-L383

### Cancellation Rules
- cancellation does not erase commercial history
- cancellation must preserve auditability
- post-cancel entitlement behavior must be explicit
- included usage and workspace access after cancellation must be policy-defined
- data retention is downstream and separate from billing truth

### Restriction / Past-Due States
The platform SHOULD support degraded states such as:
- grace period
- insufficient credits
- payment failure
- renewal failed
- manual review
- fraud/risk hold
- workspace restricted for billing reasons fileciteturn32file0L384-L401

### Degraded-State Rules
The system MUST define:
- whether existing access remains temporarily active
- whether only usage-based actions are blocked
- whether plan features downgrade
- whether workspace members see warning states
- whether support/admin override paths exist under policy fileciteturn32file0L384-L401

## Relationship to Platform Credits

Platform Credits are the internal spend unit used by the billing system, but they are not the same thing as subscriptions or billing state. Recurring plans, usage-billed actions, feature unlocks, add-ons, and overages may deduct credits depending on the plan structure and product policy. Account- or workspace-scoped commercial activity MUST resolve to the correct credits owner scope. fileciteturn32file0L270-L286

Required distinctions:
- subscription is not the same as a credit balance
- credit balance is not the same as entitlement
- entitlement is not the same as an invoice
- verified payment is not the same as recurring subscription state fileciteturn32file0L274-L286

## Relationship to Product Entitlements

Subscriptions and usage billing produce or modify entitlements. Representative examples include active plan activation, workspace-level product eligibility, credits-aware premium action eligibility, expired subscription restrictions, and included-usage exhaustion leading to denial or overage paths. fileciteturn32file0L288-L304

Entitlement rules:
- products MUST NOT infer entitlement from UI state or provider payment state alone
- entitlements SHOULD derive from normalized platform-commercial state
- account and workspace scope MUST be preserved
- billing and usage changes MUST propagate reliably into entitlement evaluation fileciteturn32file0L288-L304

## Relationship to Invoicing and Receipts

Subscriptions and usage billing generate billing events that may require invoices, receipts, or billing summaries. The billing system must therefore provide enough commercial structure for downstream invoicing systems to represent:
- recurring charges
- usage charges
- add-ons
- seat changes
- renewal outcomes
- refunds and adjustments
- scope owner context fileciteturn32file0L402-L410

The billing system is not the invoice-rendering system, but it MUST produce the commercial truth those documents depend on.

## Relationship to Revenue and Profit

Subscriptions and usage billing are upstream commercial systems, not final accounting systems. A subscription activation is not automatically profit, and a credits-backed usage event is not automatically distributable profit. Commercial activity must still pass through accounting, cost recognition, refund logic, treasury treatment, and policy-defined profit determination. fileciteturn32file0L411-L421

## Invariants

1. Billing truth remains distinct from payment truth.
2. Billing truth remains distinct from credits truth.
3. Billing truth remains distinct from entitlement truth.
4. Every billable object belongs to one explicit scope.
5. Seat billing and membership lifecycle remain related but non-identical.
6. Usage charging must be idempotency-aware.
7. Products may define chargeable value but not separate billing truth.
8. Renewal, past-due, grace, restriction, and cancellation states must remain explicit and auditable.
9. Sensitive billing correction must preserve lineage rather than destructive rewrite.
10. Shared billing architecture is platform-owned, not product-local.

## Functional Rules

### Chargeable Value Rule
Products MAY define what is chargeable, what is included, and which features are premium, but only within the shared billing architecture. fileciteturn32file0L306-L319

### No Product Fragmentation Rule
Products MUST NOT define:
- standalone balance systems outside Platform Credits
- independent recurring billing truth outside the platform billing layer
- hidden spend logic not visible to shared reporting and ledger systems
- incompatible usage meters that cannot be reconciled
- entitlement shortcuts that bypass platform-commercial checks fileciteturn32file0L306-L319

### Distinct Failure Rule
Missing funding, missing entitlement activation, insufficient credits, wrong scope, stale seat state, and missing permission are different failure classes and MUST remain distinguishable internally.

### Scope-Correctness Rule
The platform MUST not silently deduct from the wrong balance owner. Wrong-scope charge attempts fail closed and remain auditable. fileciteturn31file3L31-L37

### Recovery Rule
Payment success, renewal success, or credits availability that arrives after a temporary degraded state MUST produce recoverable, explicit state transitions rather than hidden silent recovery. fileciteturn31file3L38-L43

### Governance-Sensitive Rule
Backdated billing repair, broad scope migration, billing-owner corrections, platform-wide pricing policy changes, and similar financially sensitive actions are permission-bounded, audited, and governance-sensitive where appropriate. fileciteturn31file7L23-L37

## Permission / Access Considerations

Billing truth does not grant actor authority by itself.

Required constraints:
- actor permission to mutate billing state remains separate from billing ownership
- workspace-scoped billing actions must respect workspace authorization and scoped commercial controls
- products MUST NOT infer billing mutation authority from session validity or generic workspace membership
- support and admin billing corrections require bounded elevated pathways with explicit audit lineage
- UI visibility of a plan or billing summary does not imply mutation authority

## API / Contract Implications

The billing layer MUST expose stable platform-consumable contracts for:

- querying subscription state by scope
- querying usage posture, included usage, and overage status
- creating and updating subscriptions through approved funding and policy paths
- rating and recording usage through idempotent usage-billing contracts
- retrieving seat commercial state where applicable
- surfacing deterministic failure reasons for insufficient funding, invalid scope, past-due posture, restriction, or review-required status

### Contract Rules
- backend owns canonical billing truth; frontend and products consume it rather than invent it fileciteturn31file5L20-L36
- APIs MUST distinguish billing truth from payment truth and credits truth
- mutation-capable routes MUST support correlation identifiers and idempotent handling where retries are plausible
- async workers MUST carry enough scope, meter, and commercial context to re-rate safely when policy requires
- service-to-service calls MUST preserve actor-on-behalf-of context when mutation authority matters

## Event / Async Implications

The subscriptions and usage billing layer SHOULD support event families such as:

- `subscription.created`
- `subscription.activated`
- `subscription.renewed`
- `subscription.restricted`
- `subscription.canceled`
- `usage.recorded`
- `usage.classified_included`
- `usage.classified_overage`
- `seat.changed`
- `billing_owner.changed`
- `billing_correction.executed`

Event rules:
- events MUST be emitted after canonical commit
- events MUST preserve scope, plan, meter, reason, and correlation references
- consumers MUST treat events as synchronization signals rather than independent billing truth
- async retries MUST preserve idempotent linkage
- downstream invoice, entitlement, credits, and reporting consumers MUST remain trace-linked to the initiating billing event

## Data Model / Storage Implications

At minimum, the billing domain SHOULD support semantic representation of:

### Subscription
- `subscription_id`
- `owner_scope_type`
- `owner_scope_id`
- `plan_reference`
- `status`
- `billing_period`
- `renewal_configuration`
- `billing_owner_reference`
- `created_at`
- `updated_at` fileciteturn31file7L58-L67

### Usage Record
- `usage_record_id`
- owner scope
- product reference
- meter type
- quantity
- included or overage classification
- charge reason
- created_at
- billing linkage reference fileciteturn31file7L68-L76

### Entitlement Link
- entitlement reference
- related subscription or usage policy
- target scope
- active/inactive status
- activation time
- restriction reason where applicable fileciteturn31file7L77-L83

### Seat Record
- seat allocation reference
- workspace ID
- assigned account where relevant
- seat status
- billing linkage fileciteturn31file7L84-L89

Additional semantic structures SHOULD include:
- billing owner history
- renewal attempt history
- downgrade or cancellation schedule
- included-usage bucket state
- overage classification record
- correction lineage record

## Read Model / Projection / Reporting Rules

- derived plan badges, warnings, seat summaries, and usage dashboards MAY support UX and low-risk reads, but MUST remain explicitly derived
- sensitive or high-value billing mutations MUST bypass or revalidate stale derived views
- invoices, receipts, and reports MAY aggregate billing state, but MUST remain traceable to canonical billing events
- public or end-user-facing explanations MAY collapse detail, but protected internal records MUST preserve richer reason and lifecycle context
- projection lag MUST NOT silently extend expired, restricted, or past-due access where canonical backend billing truth says otherwise

## Security / Risk / Abuse Controls

The billing domain is a sensitive commercial control surface and MUST enforce:

- permission-bounded mutation of subscriptions, billing owner, seat commercial state, and corrections
- fraud/risk holds and manual review posture where commercial abuse, compromised funding, or suspicious activity is detected
- strong idempotency and replay protection for usage charging and renewal flows
- anti-shadow-billing controls so products cannot bypass shared controls
- traceability for support/admin overrides and finance-side corrections
- explicit containment or review posture for broad-scope or backdated commercial repairs

## Boundary Violation Detection / Non-Canonical Patterns

The following patterns are non-canonical and forbidden:

- product-local recurring billing truth outside the platform billing layer
- hidden spend logic not visible to shared reporting and ledger systems
- incompatible usage-meter semantics that cannot be reconciled
- entitlement shortcuts that bypass platform-commercial checks
- treating workspace membership as the same thing as seat or billing-rights state
- inferring active subscription solely from payment-provider UI state
- treating credits balance alone as full subscription truth
- destructive rewrite that erases commercial correction history

Implementations SHOULD detect and surface these conditions through diagnostics, audit, and operational alerts.

## Audit / Traceability Requirements

At minimum, FUZE MUST be able to report on:

- active subscriptions by scope
- plan assignments
- seat usage
- usage-billed actions
- included usage consumption
- overage events
- renewals
- failures and past-due states
- cancellations
- billing owner changes
- billing corrections and overrides fileciteturn31file7L38-L57

Audit events SHOULD include:
- subscription created
- subscription activated
- subscription renewed
- usage charged
- seat changed
- cancellation requested
- cancellation executed
- billing scope changed
- support/admin billing correction
- restriction due to billing state fileciteturn31file7L46-L57

## Failure Handling / Edge Cases

### Active Session but Subscription Expired
Authentication remains valid, but entitlement-based access must be restricted according to policy. fileciteturn31file3L31-L33

### Workspace Has Credits but Wrong Scope Selected During Charge
The platform MUST NOT silently deduct from the wrong balance owner. Scope resolution must be explicit and auditable. fileciteturn31file3L33-L35

### Usage Event Arrives Twice
Usage charging MUST be idempotency-aware to prevent double billing. fileciteturn31file3L35-L36

### Included Usage Exhausted Mid-Workflow
The platform MUST apply explicit policy: deny, reserve credits, charge overage, or require top-up. fileciteturn31file3L36-L37

### Seat Removed While Member Still Has Active Session
Subsequent authorization and entitlement checks MUST reflect new seat reality; stale session state must not override billing truth. fileciteturn31file3L37-L39

### Renewal Succeeds in Payment Rail but Entitlement Activation Lags
The system MUST preserve recoverable commercial state and not silently lose the renewal outcome. fileciteturn31file3L39-L41

### Product Wants One-Off Monetization Outside Normal Plan System
Allowed only if it still resolves through shared usage, add-on, or credits-backed billing semantics. fileciteturn31file3L41-L43

## Operational Considerations

Operational systems SHOULD support:
- deterministic renewal and recovery workflows
- usage-rating health monitoring
- explicit billing-scope resolution and wrong-scope detection
- seat commercial-state visibility and drift detection
- support and finance control-plane tools for bounded correction
- reconciliation between billing events, credits settlement, entitlement activation, and invoice/document outputs
- runbooks for duplicate usage events, renewal lag, plan misassignment, scope migration, and seat-state drift

Because billing is shared across products, operational defects can affect multiple products simultaneously. Operational posture MUST therefore be platform-grade rather than product-local.

## Migration / Compatibility / Supersession Considerations

Migrations into this canonical model MUST enforce the following:

- product-local recurring billing systems with shared platform meaning must be retired or explicitly scoped as non-canonical
- product-local usage meters that cannot map into shared rating and reconciliation must be normalized or deprecated
- historical assumptions that paid status equals permission are superseded
- historical assumptions that workspace membership equals seat or commercial access are superseded
- old plan or package names may persist temporarily, but canonical billing semantics must remain explicit
- backward-compatibility adapters MUST preserve canonical scope, lifecycle, and audit semantics rather than institutionalizing drift

## Implementation-Contract Guardrails

Downstream implementations MUST preserve all of the following:

1. billing truth remains distinct from payment, credits, entitlement, and invoice truth
2. every billable object and every subscription attaches to an explicit canonical scope
3. subscriptions, usage billing, seats, included usage, and overage remain semantically distinguishable
4. products define chargeable value but do not define separate billing truth
5. stale UI badges, product caches, or provider-side state MUST NOT outrank canonical backend billing state
6. seat commercial state remains separate from membership lifecycle even when tightly coordinated
7. credits-backed settlement remains coordinated with but not collapsed into billing truth
8. hidden support or admin bypasses remain forbidden as permanent billing mechanisms
9. degraded runtime conditions MUST NOT silently widen access or silently hide billing ambiguity for sensitive commercial actions
10. idempotency and replay safety MUST be preserved for subscription creation, renewal, usage charging, seat changes, cancellation, scope migration, and correction workflows
11. reason capture, source references, policy references, and audit lineage MUST remain explicit for high-impact billing changes
12. downstream teams MUST NOT optimize away scope references, lifecycle states, seat linkage, correction lineage, or failure distinctions where those elements protect monetization integrity, auditability, supportability, or migration safety

## Downstream Execution Staging

This specification implies the following preferred downstream implementation order:

1. stabilize billing scope and billing owner rules
2. stabilize subscription lifecycle and state semantics
3. stabilize usage meter and included-usage/overage semantics
4. integrate pricing, credits-backed settlement, payment normalization, entitlement activation, and invoice/document references
5. integrate seat commercial-state handling
6. integrate audit, reporting, support/control-plane, and correction workflows
7. integrate derived views, dashboards, and reconciliation surfaces

## Required Downstream Specs / Contract Layers

This specification directly requires compatible downstream refinement and implementation-contract work in:

- `PAYMENT_RAILS_INTEGRATION_SPEC.md`
- `INVOICING_AND_RECEIPTS_SPEC.md`
- `REFUND_REVERSAL_AND_ADJUSTMENT_SPEC.md`
- `PAYMENT_FRAUD_AND_ABUSE_PREVENTION_SPEC.md`
- `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
- `AI_USAGE_METERING_SPEC.md`
- `SUBSCRIPTIONS_BILLING_API_SPEC.md`
- `WORKSPACE_ORGANIZATION_API_SPEC.md`
- `ROLE_PERMISSION_ACCESS_API_SPEC.md`
- product-specific monetization and rating specifications
- finance, reconciliation, reporting, support/control-plane, and remediation workflows

## Canonical Examples / Anti-Examples

### Canonical Example 1 — Workspace Plan, Actor Still Limited
A workspace owns an active subscription. A member without the required role is denied billing mutation or sensitive plan administration. Commercial state exists; actor authority does not.

### Canonical Example 2 — Hybrid Plan with Included Usage
A workspace plan includes 5,000 AI actions monthly. Usage is classified as included until the threshold is exhausted, then explicit overage or credits-backed policy applies.

### Canonical Example 3 — Renewal Through Credits
A workspace renewal draws from available workspace-scoped credits under explicit funding rules. The renewal state, credits settlement, and entitlement activation remain related but distinct.

### Canonical Example 4 — Seat Drift Corrected
A workspace seat allocation is wrong after membership changes. The platform records an auditable billing correction rather than silently mutating historical seat-linked commercial truth.

### Anti-Example 1 — Product-Local Billing Island
A product keeps its own recurring subscription state with no shared billing integration. This is forbidden.

### Anti-Example 2 — Paid Provider UI Equals Active Subscription
A product infers active commercial state solely from provider-side payment UI and skips canonical backend billing state. This is forbidden.

### Anti-Example 3 — Membership Equals Seat
A product assumes every active workspace member has a paid seat and monetized access rights. This is forbidden.

### Anti-Example 4 — Credits Balance Equals Entitlement
A product enables premium plan features solely because a scope has credits, without canonical billing or entitlement posture. This is forbidden.

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
- `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
- `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md`
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `SCOPED_AUTHORIZATION_MODEL_SPEC.md`
- `ACCESS_EVALUATION_AND_EFFECTIVE_PERMISSION_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `PAYMENT_RAILS_INTEGRATION_SPEC.md`
- `INVOICING_AND_RECEIPTS_SPEC.md`
- `REFUND_REVERSAL_AND_ADJUSTMENT_SPEC.md`
- `PAYMENT_FRAUD_AND_ABUSE_PREVENTION_SPEC.md`
- `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
- `AI_USAGE_METERING_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`

This specification directly governs or materially informs:

- `PAYMENT_RAILS_INTEGRATION_SPEC.md`
- `INVOICING_AND_RECEIPTS_SPEC.md`
- `REFUND_REVERSAL_AND_ADJUSTMENT_SPEC.md`
- `PAYMENT_FRAUD_AND_ABUSE_PREVENTION_SPEC.md`
- `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
- `AI_USAGE_METERING_SPEC.md`
- `SUBSCRIPTIONS_BILLING_API_SPEC.md`
- product-specific monetization and rating specifications
- finance, reconciliation, reporting, support/control-plane, and remediation workflows

## Explicitly Deferred Items

The following are intentionally deferred to adjacent or downstream specifications:

- exact plan-price tables and commercial packaging
- exact renewal retry cadence and dunning details
- exact per-product included-usage formulas
- exact seat-pricing formulas
- exact direct-subscription versus credits-backed funding mix by product
- exact billing UI conventions and end-user explanation copy
- exact database schema and transport payloads for every API

These do not weaken the canonical billing model established here.

## Final Normative Summary

The FUZE subscriptions and usage billing system is the shared commercial framework through which recurring access, metered usage, seats, included usage, overages, add-ons, and premium product behavior are governed across the ecosystem. It sits between payment normalization, Platform Credits, entitlement activation, and downstream invoice/document and reporting systems. It is neither payment truth, nor credits truth, nor entitlement truth, nor actor authority truth.

A scope may have billing state while a specific actor still lacks permission. A scope may have credits while a subscription is still inactive. A plan may be active while a narrower feature is unavailable because entitlement or permission remains insufficient. Products may monetize differently, but they must still resolve recurring and usage-rated commercial effects through this shared platform-owned billing architecture.

This document is the canonical FUZE rule set for subscriptions and usage billing semantics.
