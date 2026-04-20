# SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC

## Purpose

This document defines the canonical subscriptions and usage billing architecture of the FUZE platform. Its purpose is to establish how recurring access, usage-based charging, seat-based billing, feature unlocks, AI action metering, and workspace-scoped or account-scoped commercial logic are structured across the FUZE ecosystem.

This specification is foundational because FUZE is a multi-product SaaS platform ecosystem with a shared internal economic layer. Products inside FUZE must not invent unrelated billing systems. They must rely on a coherent commercial framework that can support recurring plans, metered usage, credit-based spending, shared team environments, and product-specific monetization patterns without fragmenting the platform.

This document therefore defines the commercial operating model that sits between payment normalization, Platform Credits, product entitlements, and long-term platform revenue structure.

---

## Scope

This specification covers:

- the canonical subscription model of FUZE
- the canonical usage-billing model of FUZE
- the relationship between subscriptions, usage billing, and Platform Credits
- account-scoped versus workspace-scoped billing behavior
- seat-based and team-oriented billing concepts
- product-specific monetization on top of shared billing infrastructure
- subscription lifecycle rules
- usage-metering and charging rules
- billing ownership and billing scope
- interaction with payment rails, invoicing, credits, and entitlements
- audit, reporting, and governance-sensitive implications of billing behavior

This specification does not define detailed payment verification, invoice rendering, credits ledger internals, or specific product pricing numbers. Those are refined in:

- `PAYMENT_RAILS_INTEGRATION_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `INVOICING_AND_RECEIPTS_SPEC.md`
- `PRICING_AND_MONETIZATION_MODEL_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `AI_USAGE_METERING_SPEC.md`

---

## Design Goals

The design goals of the FUZE subscriptions and usage billing model are:

1. to provide one shared billing architecture across the FUZE ecosystem
2. to support recurring plans and usage-based charging in the same platform
3. to make billing compatible with Platform Credits as the internal consumption layer
4. to support both individual and workspace-scoped commercial contexts
5. to allow product-specific monetization without product-specific billing fragmentation
6. to preserve a clear distinction between entitlement, balance, and payment state
7. to make billing outcomes auditable, policy-driven, and scalable across products
8. to support AI-native products whose costs and value are often usage-sensitive rather than purely subscription-sensitive

---

## Non-Goals

This specification is not intended to:

- make subscriptions the only commercial model in FUZE
- make usage billing the only commercial model in FUZE
- require every product to use the same visible pricing shape
- allow products to bill directly against raw external payments without platform normalization
- redefine Platform Credits as subscription objects
- make billing state equivalent to profit or payout state
- create isolated product-local commercial systems that bypass shared platform rules

---

## Canonical Billing Principle

The primary billing principle of FUZE is:

> products may differ in what they charge for, but all recurring access, usage-based charging, and entitlement activation must resolve through one shared platform billing architecture tied to Platform Credits and normalized payment state.

This means:

- subscriptions and usage billing are platform capabilities
- products define chargeable value, but not separate billing truth
- Platform Credits are the internal spend unit used by billing logic
- entitlements are activated through commercial rules, not by product-local billing shortcuts
- account and workspace scope must be explicit in all commercial decisions

This principle is essential for maintaining FUZE as one platform rather than many unrelated SaaS products.

---

## Why FUZE Needs Both Subscriptions and Usage Billing

FUZE requires both subscription logic and usage-billing logic because the product ecosystem contains multiple product shapes and multiple value-delivery patterns.

Some platform value is naturally recurring and access-based. Examples include:

- core product access
- workspace plans
- seat-based access
- premium dashboard availability
- recurring team features
- baseline product tiers

Other platform value is naturally consumption-based. Examples include:

- AI actions
- high-cost model usage
- report generation
- workflow execution
- premium analysis runs
- task processing
- add-on usage bursts
- pay-per-operation utilities

If FUZE were designed only for subscriptions, products with heavy AI or event-based value would either be under-monetized or forced into inflexible pricing. If FUZE were designed only for usage billing, users and teams would lose the clarity and predictability of recurring access models.

The shared billing architecture therefore must support both.

This is especially important because FUZE is building a platform, not one app. A platform architecture should support multiple commercial forms while preserving one internal economic logic.

---

## Canonical Commercial Layers

The FUZE commercial architecture contains the following layers:

### 1. Payment Input Layer
External value enters through approved payment rails such as Stripe, stablecoins, FUZE token conversion paths, Telegram Stars, Apple In-App Purchase, and Google Play Billing.

### 2. Payment Normalization Layer
External payment state is verified and normalized into platform-commercial truth.

### 3. Platform Credits Layer
Verified value becomes Platform Credits according to policy.

### 4. Subscription and Usage Billing Layer
The platform evaluates recurring plans, seat logic, metered usage, feature unlocks, and chargeable actions using one commercial framework.

### 5. Entitlement Layer
Commercial outcomes determine whether the account or workspace may access a product, feature, module, or workflow.

### 6. Reporting and Reconciliation Layer
Billing outcomes become visible through invoices, receipts, usage records, ledger records, and reporting views.

This layered model is one of the reasons FUZE can support complex product monetization while remaining coherent.

---

## Core Concepts

### Subscription
A recurring commercial arrangement that grants access to a plan, tier, product, workspace, module, or service bundle over a defined billing period.

### Usage Billing
Charging based on measured consumption rather than only fixed recurring access.

### Plan
A named commercial package that defines recurring access conditions, included usage, seat allowances, or feature boundaries.

### Seat
A unit of allocatable user participation in a workspace-billed or team-billed commercial context.

### Entitlement
A commercially activated permission for an account or workspace to use a product, plan, or feature.

### Billing Scope
The commercial owner of the chargeable state, typically either an account or a workspace.

### Included Usage
A defined amount of usage bundled into a subscription plan before additional metered charging begins.

### Overage
Usage consumed above included thresholds, charged according to policy.

### Billing Period
The interval over which recurring subscription value is granted and evaluated.

### Chargeable Action
A product or platform action mapped to usage-billing or credits consumption.

---

## Billing Scopes

Subscriptions and usage billing must support explicit commercial scopes.

### 1. Account Scope
The subscription or usage obligation belongs to an individual account.

Appropriate for:
- solo users
- personal AI usage
- personal tool subscriptions
- self-service purchases

### 2. Workspace Scope
The subscription or usage obligation belongs to a workspace.

Appropriate for:
- team plans
- seat-based products
- shared AI usage pools
- business subscriptions
- collaborative product usage
- shared credits-backed activity

### Scope Rules

- every billable object must belong to one explicit scope
- account scope and workspace scope must remain distinguishable
- products must know which scope they are charging
- invoices, receipts, and reporting must preserve scope clarity
- scope changes require explicit migration or reassignment logic

This distinction is required because FUZE supports both individual and team use.

---

## Subscription Model

The FUZE subscription model must support recurring access arrangements across products and workspace contexts.

### Minimum subscription capabilities

- plan creation and plan versioning
- recurring billing period definition
- account-scoped subscription ownership
- workspace-scoped subscription ownership
- subscription status tracking
- seat and member association where relevant
- renewal behavior
- cancellation behavior
- grace, restriction, or dunning states where policy requires
- integration with credits consumption or direct plan funding logic

### Recommended subscription states

- `pending_activation`
- `active`
- `trialing`
- `grace_period`
- `past_due`
- `restricted`
- `canceled`
- `expired`

These states allow the platform to model recurring commercial access without ambiguity.

---

## Subscription Lifecycle

The canonical subscription lifecycle is:

1. **created**
2. **pending funding or activation**
3. **active**
4. **renewing**
5. **grace / pending recovery if needed**
6. **restricted or past due if payment or credits conditions fail**
7. **canceled or expired**
8. **archived for reporting retention**

### Lifecycle principles

- subscription activation must be tied to valid commercial state
- subscription state is distinct from raw payment-rail state
- subscription state is distinct from credits balance by itself
- cancellation must not silently erase commercial history
- post-cancellation access behavior must be policy-defined

This lifecycle must be auditable.

---

## Subscription Types

FUZE should support multiple subscription types.

### 1. Account Subscription
A recurring plan for one account.

Examples:
- individual QTB access
- personal AI assistant tier
- personal analytics plan

### 2. Workspace Subscription
A recurring plan owned by a workspace.

Examples:
- HerHelp business plan
- Botmad team plan
- AIMM operational workspace plan
- organization-level access tier

### 3. Seat-Based Subscription
A workspace subscription where member count, assigned seats, or operator seats matter.

Examples:
- per-member platform access
- per-operator team workflow access
- billing by workspace seats

### 4. Hybrid Subscription
A recurring plan with included usage and overage logic.

Examples:
- Botmad team plan with included automation credits
- HerHelp plan with included AI generations
- QTB plan with included analysis runs and overage after threshold

This variety is necessary because FUZE products differ in how they create value.

---

## Usage Billing Model

The usage-billing layer must support charging for measurable consumption.

### Chargeable usage examples

- AI inference actions
- advanced analysis jobs
- document/report generation
- workflow execution
- premium alert runs
- data-processing tasks
- automation steps
- product-specific compute-intensive functions
- add-on module operations

### Usage-billing requirements

At minimum, the platform must be able to:

- define a usage meter
- associate usage with a product and scope
- determine whether usage is included or chargeable
- convert usage into credits deduction or equivalent commercial effect
- record usage with sufficient attribution for audit and reconciliation
- prevent duplicate charging through idempotency-aware design

Usage billing is especially important because FUZE includes AI-native and workflow-native products whose cost and value are not always well represented by flat recurring access alone.

---

## Included Usage and Overage Model

Subscriptions may include bundled usage before metered overage applies.

### Included usage examples

- monthly AI action allowance
- included report generation volume
- included workflow runs
- included seat count
- included premium alerts
- included automation quotas

### Overage logic

Once included limits are exceeded, the platform may:

- deduct additional Platform Credits
- block further usage until top-up occurs
- require add-on purchase
- move the user/workspace into higher tier recommendation
- apply usage-based charges according to product policy

### Principle

Included usage improves plan predictability. Overage logic improves commercial fairness and operational sustainability.

This combination is especially useful for AI products where light users and heavy users should not necessarily pay the same effective amount forever.

---

## Relationship to Platform Credits

Platform Credits are the internal spend unit used by the billing system.

### Billing-to-credits rules

- recurring plans may consume credits directly, depending on plan structure
- usage-billed actions may deduct credits
- feature unlocks may deduct credits
- add-ons and overages may deduct credits
- account- or workspace-scoped commercial activity must resolve to the appropriate credits owner scope

### Important distinction

A subscription is not the same thing as a credit balance.
A credit balance is not the same thing as an entitlement.
An entitlement is not the same thing as an invoice.
A verified payment is not the same thing as a recurring subscription state.

These distinctions are important because FUZE separates the internal consumption layer from the recurring commercial layer while still connecting them coherently.

---

## Relationship to Product Entitlements

Subscriptions and usage billing produce or modify entitlements.

### Examples

- active QTB plan -> QTB product entitlement active
- HerHelp workspace plan -> workspace-level product entitlement active
- sufficient credits -> premium AI action may execute
- expired subscription -> feature access restricted even if old UI state still exists
- included usage exhausted -> action denied or moved to overage path

### Entitlement principles

- products must not infer entitlement from UI state or provider-side payment state alone
- entitlements should be derived from normalized platform-commercial state
- account and workspace scope must be preserved
- billing and usage state changes must propagate to entitlement evaluation reliably

This is a key part of how FUZE maintains one coherent commercial framework across products.

---

## Product Monetization on Top of Shared Billing

Products may define their own monetization logic, but only within the shared billing architecture.

### Products may define

- what counts as a plan tier
- what actions are usage-billed
- what counts as included usage
- which features require premium access
- whether the relevant scope is personal or workspace-based
- what charge reason is associated with each product event

### Products may not define

- their own standalone balance system outside Platform Credits
- independent recurring billing truth outside the platform billing layer
- hidden spend logic not visible to shared reporting and ledger systems
- incompatible usage-meter semantics that cannot be reconciled
- entitlement shortcuts that bypass platform-commercial checks

This rule is central to FUZE’s platform-company model.

---

## Seat Model

The platform must support seat-oriented billing where relevant.

### Seat concepts

A seat represents one licensable or allocatable participation unit inside a workspace-billed environment.

### Seat capabilities may include

- assigned seat count
- occupied seat count
- pending seat invitations
- active seat entitlement
- seat-based pricing tiers
- seat-limit enforcement
- seat expansion through plan upgrade or credits-backed add-ons

### Seat principles

- seats belong to the billing scope, typically the workspace
- seat assignment and membership are related but not identical concepts
- products may define when seats matter, but seat billing semantics should remain platform-consistent
- seat changes should be auditable and reflected in billing state

Seat billing is especially relevant for HerHelp, Botmad, and future team-oriented products.

---

## Billing Ownership

The billing system must maintain explicit ownership of commercial responsibility.

### Billing owner examples

- personal account paying for personal subscription
- workspace owner funding workspace plan
- designated billing manager handling workspace payments
- enterprise-managed billing context in future expansion

### Billing ownership principles

- the billing owner is not always the same as every product operator
- workspace billing does not collapse into personal identity
- billing-owner changes must be explicit
- billing records must preserve historical responsibility context
- products must not improvise billing ownership locally

This is important for support, reporting, reconciliation, and governance-aware actions.

---

## Renewal Model

Recurring subscriptions require a clear renewal model.

### Renewal may be funded through

- available credits in the billing scope
- fresh payment input through approved payment rails
- approved enterprise settlement pathway in future contexts

### Renewal behavior must define

- renewal date or cycle boundary
- what happens if credits are insufficient
- whether grace period exists
- whether access is immediately restricted
- whether auto top-up or renewal helper flows exist
- what dunning or recovery path applies if relevant

### Principle

Renewal logic must be deterministic and visible. Silent commercial ambiguity is not acceptable in a multi-product platform.

---

## Cancellation Model

Subscriptions must support explicit cancellation behavior.

### Cancellation types may include

- cancel at period end
- immediate cancellation
- scheduled downgrade
- support/admin cancellation under policy
- workspace closure-driven cancellation

### Cancellation principles

- cancellation does not erase commercial history
- cancellation should preserve auditability
- post-cancel entitlement behavior must be explicit
- included usage or workspace access after cancellation must be policy-defined
- product data retention after cancellation must be handled separately from billing state

A cancellation model is part of platform trust, not just product convenience.

---

## Restriction and Past-Due Handling

The billing system must support controlled degraded states.

### Possible states include

- grace period
- insufficient credits
- payment failure
- renewal failed
- manual review
- fraud/risk hold
- workspace restricted for billing reasons

### Behavioral requirements

The system must define:

- whether existing access remains temporarily active
- whether only usage-based actions are blocked
- whether plan features downgrade
- whether workspace members see warning states
- whether support/admin override paths exist

This is necessary because a mature SaaS ecosystem must be able to manage commercial failure states without creating inconsistent product behavior.

---

## Relationship to Invoicing and Receipts

Subscriptions and usage billing generate billing events that may require invoices, receipts, or billing summaries.

The billing system must therefore provide enough commercial structure for downstream invoicing systems to represent:

- recurring charges
- usage charges
- add-ons
- seat changes
- renewal outcomes
- refunds and adjustments
- scope owner context

The billing system is not the invoice-rendering system, but it must produce the underlying commercial truth those documents depend on.

---

## Relationship to Revenue and Profit

Subscriptions and usage billing are upstream commercial systems, not final accounting systems.

This means:

- a subscription activation is not automatically profit
- a credits-backed usage event is not automatically distributable profit
- recurring commercial activity must still pass through accounting, cost recognition, refund logic, treasury treatment, and policy-defined profit determination

This distinction is especially important in FUZE because the profit participation system depends on disciplined separation between:

- payment intake
- credits issuance
- product usage
- recognized revenue and costs
- distributable profit
- stablecoin payout funding

The billing system is one layer in that chain, not the end of it.

---

## Governance and Control Implications

Certain billing actions are sensitive and require stronger control.

### Sensitive billing actions include

- manual subscription correction
- backdated billing repair
- credits-backed commercial reversal
- seat reassignment correction affecting billing
- forced entitlement restoration or restriction
- large-scale migration of billing scopes
- product charging-policy changes with platform-wide impact
- support or finance overrides

### Control principles

- sensitive billing actions must be permission-bounded
- changes must be audited
- platform-wide pricing or charging policy changes should be treated as governance-sensitive platform decisions
- products must not gain unrestricted correction powers over billing truth

Billing is part of the platform trust surface and must be treated accordingly.

---

## Audit and Reporting Requirements

The subscriptions and usage billing system must support clear audit and reporting visibility.

### At minimum, the platform must be able to report on:

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
- billing corrections and overrides

### Audit events should include

- subscription created
- subscription activated
- subscription renewed
- usage charged
- seat changed
- cancellation requested
- cancellation executed
- billing scope changed
- support/admin billing correction
- restriction due to billing state

A multi-product billing system without strong auditability will become difficult to trust and difficult to operate.

---

## Minimum Data Model Requirements

At minimum, the subscriptions and usage billing system must support semantic representation of:

### Subscription
- subscription_id
- owner_scope_type
- owner_scope_id
- product or plan reference
- status
- billing period
- renewal configuration
- created_at
- updated_at

### Usage Record
- usage_record_id
- owner scope
- product reference
- meter type
- quantity
- included/overage classification
- charge reason
- created_at
- billing linkage reference

### Entitlement Link
- entitlement reference
- related subscription or usage policy
- target scope
- active/inactive status
- activation time
- restriction reason where applicable

### Seat Record where applicable
- seat_id or seat allocation reference
- workspace_id
- assigned account where relevant
- seat status
- billing linkage

These are minimum semantic requirements. The full data model is refined in downstream specifications.

---

## Edge Cases and Failure Handling

### User has active session but subscription expired
Authentication remains valid, but entitlement-based access must be restricted according to policy.

### Workspace has credits but wrong scope selected during charge
The platform must not silently deduct from the wrong balance owner. Scope resolution must be explicit and auditable.

### Usage event arrives twice from async workflow
Usage charging must be idempotency-aware to prevent double billing.

### Included usage exhausted mid-workflow
The platform must apply explicit policy: deny, reserve credits, charge overage, or require top-up.

### Seat removed while member still has active session
Subsequent authorization and entitlement checks must reflect new seat reality; stale session state must not override billing truth.

### Renewal succeeds in payment rail but entitlement activation lags
The system must preserve recoverable commercial state and not silently lose the renewal outcome.

### Product wants one-off monetization outside normal plan system
This is allowed only if it still resolves through shared usage, add-on, or credit-backed billing semantics.

---

## Open Items

The following areas are intentionally refined in downstream specifications:

- exact product-specific pricing models
- exact renewal retry and dunning workflows
- exact included-usage computation logic by product
- exact seat-pricing formulas
- exact direct-subscription versus credits-backed subscription funding modes by product
- exact user-facing billing UI conventions

These do not weaken the canonical billing model established here.

---

## Closing Summary

The FUZE subscriptions and usage billing system is the shared commercial framework through which recurring access, metered usage, seat-based participation, add-ons, and premium product behavior are governed across the ecosystem. It works together with payment normalization, Platform Credits, and entitlement logic to ensure that products can monetize differently without fragmenting the platform economy. By supporting both subscriptions and usage billing inside one coherent architecture, FUZE creates the commercial flexibility required for a growing AI-powered multi-product platform while preserving clarity, auditability, and long-term economic discipline.
