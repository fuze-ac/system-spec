# PAYMENT_RAILS_INTEGRATION_SPEC

## Purpose

This document defines the canonical payment rails integration architecture of the FUZE platform. Its purpose is to establish how external payment inputs are accepted, verified, normalized, and transformed into FUZE Platform Credits or related platform billing outcomes under one shared commercial model.

This specification is essential because FUZE is designed to operate across multiple products and multiple commercial entry paths. Users may enter through fiat, stablecoins, app-store billing, Telegram-native payment flows, or other approved methods. Without a disciplined payment-rail integration model, the platform would fragment economically and products would begin to behave like isolated billing systems rather than members of one coherent ecosystem.

The payment rails integration layer therefore exists to make payment diversity at the edge compatible with one internal economic logic at the center.

---

## Scope

This specification covers:

- the role of payment rails inside the FUZE platform architecture
- supported payment input categories
- payment normalization principles
- verification requirements by payment rail class
- mapping from payment event to credits issuance eligibility
- interaction with billing, subscriptions, invoicing, and credits
- handling of refunds, reversals, chargebacks, and rail-specific corrections
- trust boundaries between FUZE and external payment providers
- audit, reporting, and control implications of payment rail integrations
- architecture rules for future payment rail expansion

This specification does not define the full commercial policy of credits, detailed invoice rendering, or complete accounting policy. Those are refined in:

- `PLATFORM_CREDITS_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `INVOICING_AND_RECEIPTS_SPEC.md`
- `CREDIT_LEDGER_AND_SETTLEMENT_SPEC.md`
- `REFUND_REVERSAL_AND_ADJUSTMENT_SPEC.md`

---

## Design Goals

The design goals of the FUZE payment rails integration model are:

1. to support multiple external payment inputs without fragmenting the platform economy
2. to normalize diverse rail semantics into one shared internal commercial model
3. to ensure verified payment is required before economic state changes occur
4. to keep payment rails distinct from Platform Credits, token participation, and payout logic
5. to support both account-scoped and workspace-scoped commercial flows
6. to make rail-specific corrections and refund behavior manageable and auditable
7. to make future rail expansion possible without changing the core economic architecture
8. to preserve strong trust-boundary handling around external providers

---

## Non-Goals

This specification is not intended to:

- make each payment rail a separate internal balance system
- allow products to integrate external rails directly without platform normalization
- treat provider-reported state as automatically equivalent to internal economic truth
- blur payment verification into immediate profit recognition
- treat FUZE token payment input as equivalent to ecosystem participation holding
- define public market exchange logic for credits
- replace downstream fraud, accounting, or settlement specifications

---

## Canonical Payment Rail Principle

The primary principle of the FUZE payment integration model is:

> many external payment rails may be accepted, but all accepted value must pass through platform verification and normalization before it becomes valid internal economic state.

This means:

- payment rails are edge inputs, not the core economic system
- external payment success is not sufficient by itself to mutate platform balances
- normalization into platform-commercial meaning must occur first
- products must consume the normalized platform outcome, not raw provider events

This principle is what allows FUZE to support many commercial entry paths without losing internal coherence.

---

## Canonical Role of the Payment Rails Layer

The payment rails layer exists to perform four core functions:

1. **accept value through approved external rails**
2. **verify that the claimed payment event is valid**
3. **normalize the event into FUZE-internal commercial semantics**
4. **trigger eligible downstream economic actions such as credits issuance, subscription activation, or invoice-state updates**

This layer is not the same thing as billing policy, credits policy, or accounting policy.

Its role is narrower and extremely important:

- it bridges external value input and internal platform economy
- it preserves trust boundaries
- it prevents raw provider state from directly becoming platform economic truth
- it gives all products one consistent payment entry model

---

## Supported Payment Rails

The currently defined FUZE payment rails are:

- fiat / Stripe
- approved stablecoins such as USDT
- FUZE token under approved conversion policy
- Telegram Stars
- Apple In-App Purchase
- Google Play Billing

Additional payment rails may be added in the future, but only if they follow the same normalization and control principles.

These rails are accepted because they reflect the expected user and distribution environments of the FUZE ecosystem:

- web and SaaS flows
- crypto-native flows
- Telegram-native flows
- mobile distribution environments
- future hybrid product environments

The existence of multiple rails is a platform strength only if their internal outcomes remain unified.

---

## Payment Rail Classes

For architectural clarity, payment rails should be classified into the following categories.

### 1. Fiat Processor Rail
Example:
- Stripe

Characteristics:
- card or fiat-based processing
- processor-side payment status lifecycle
- refund and dispute semantics
- off-chain verification and webhook-driven state handling

### 2. Stablecoin Rail
Examples:
- USDT and other approved stablecoins

Characteristics:
- blockchain-native payment input
- wallet and transaction verification requirements
- chain confirmation semantics
- on-chain source-of-funds observation and policy interpretation

### 3. Ecosystem Token Payment Rail
Example:
- FUZE token payment input under approved policy

Characteristics:
- token-denominated payment intake
- conversion rules required
- must remain separate from passive token holding
- must not blur payment input with ecosystem participation logic

### 4. Platform-Embedded Payment Rail
Example:
- Telegram Stars

Characteristics:
- platform-native digital goods payment environment
- provider-specific settlement and refund semantics
- platform verification requirements before credits issuance

### 5. App Store Billing Rail
Examples:
- Apple In-App Purchase
- Google Play Billing

Characteristics:
- store-managed purchase lifecycle
- store-side purchase verification
- store refund or revocation events
- platform-side entitlement and credits normalization rules

These classes are useful because not all rails behave the same way operationally or economically.

---

## Payment Rail Integration Architecture

The canonical architecture for payment rails in FUZE is:

1. **Payment initiation or provider event intake**
2. **Rail-specific verification**
3. **Normalization into platform payment record**
4. **Commercial interpretation**
5. **Credits issuance or subscription/entitlement update**
6. **Ledger and reporting updates**
7. **Ongoing correction handling if refunds, chargebacks, revocations, or disputes occur**

This architecture ensures that:

- raw provider events are not the final internal truth
- products do not have to interpret payment rail semantics independently
- platform billing and credits systems receive one normalized internal model
- correction events can be handled consistently later

---

## Payment Normalization Layer

The normalization layer is one of the most important parts of the architecture.

Its role is to turn rail-specific state into one internal FUZE payment model.

### Why normalization is necessary

Each payment rail has different concepts:
- Stripe has payment intents, charges, refunds, disputes
- Stablecoins have transactions, confirmations, addresses, chain receipts
- Telegram Stars has platform-specific purchase state
- Apple and Google have store-managed transactions, purchase tokens, revocations, and subscription semantics
- FUZE token payment requires conversion logic and separate policy treatment

Without normalization, each product would need to interpret these rail-specific differences on its own. That would fragment the platform economy.

### Normalization outputs should at minimum define:

- payment source rail
- verified value amount
- currency or asset context
- normalized platform value basis
- payment status
- related account or workspace scope
- related billing purpose
- issuance eligibility status
- reversal or refund eligibility status
- audit references

The normalization layer is therefore the canonical bridge from external rail semantics to platform-commercial semantics.

---

## Verification Requirements

No payment rail may bypass verification.

### Verification principles

1. A provider or chain event must be validated according to rail-specific rules.
2. Platform state must not treat a payment as successful until verification is sufficient.
3. Verification must be strong enough to support later audit and reconciliation.
4. Verification outcomes must distinguish between pending, verified, failed, reversed, and disputed states.
5. Products must rely on normalized verified payment state, not raw provider claims.

### Examples by rail class

#### Stripe
Verification may rely on:
- successful processor event status
- webhook confirmation
- anti-duplication handling
- expected amount and metadata validation

#### Stablecoin
Verification may rely on:
- observed on-chain transaction
- correct destination address
- asset and amount match
- chain confirmation threshold
- anti-replay and duplicate handling
- source interpretation policy where relevant

#### Apple / Google
Verification may rely on:
- purchase token or receipt validation
- subscription lifecycle verification where applicable
- store state and revocation checks

#### Telegram Stars
Verification may rely on:
- platform event verification
- transaction reference validation
- settlement/purchase state confirmation under Telegram rules

#### FUZE token payment
Verification may rely on:
- correct token transfer or approved payment path
- conversion policy resolution
- anti-duplication checks
- correct destination and value interpretation

The exact mechanisms are rail-specific, but the requirement for verification is universal.

---

## Payment Status Model

The payment integration layer should support a common normalized payment status model.

Recommended normalized states include:

- `initiated`
- `pending_verification`
- `verified`
- `failed`
- `partially_reversed`
- `reversed`
- `disputed`
- `expired`
- `requires_review`

### State meanings

#### Initiated
The payment attempt exists but has not yet been verified.

#### Pending Verification
The payment has enough signal to continue, but final verification is not complete.

#### Verified
The payment is accepted as valid input to the platform economy.

#### Failed
The payment attempt did not produce valid platform value.

#### Partially Reversed
Some portion of the economic effect has been reversed.

#### Reversed
The payment is no longer valid as platform economic input.

#### Disputed
The rail reports a dispute, chargeback, or contested state requiring special handling.

#### Expired
A time-bounded payment attempt did not complete successfully.

#### Requires Review
The payment entered a conflict, anomaly, or manual-review path.

These normalized states allow the rest of the platform to behave consistently across rails.

---

## Scope Assignment Rules

Every verified payment input must resolve into a scope.

The scope determines who receives the commercial effect of the payment.

### Supported scopes

- account scope
- workspace scope

### Scope assignment principles

- the intended scope must be declared or determinable during payment flow
- credits issuance or entitlement activation must be tied to the correct scope
- products must know which scope is being charged or funded
- workspace payments must not silently become personal balances
- personal purchases must not silently become workspace balances

If scope ambiguity exists, the system must resolve it explicitly before finalizing downstream economic actions.

---

## Payment-to-Credits Mapping

A core function of the rail layer is determining whether a verified payment becomes credits issuance.

### General rule

If the payment path is intended for Platform Credits funding or a credit-backed product purchase, verified payment should map into credits issuance under platform policy.

### Examples

- Stripe purchase of credits package -> verified -> credits issuance
- stablecoin deposit for platform usage -> verified -> credits issuance
- Apple purchase mapped to credit-backed entitlement -> verified -> credits issuance or equivalent normalized balance effect
- Telegram Stars purchase for platform spend -> verified -> credits issuance
- FUZE token payment input -> verified -> converted under policy -> credits issuance

### Important architectural rule

The rail layer does not itself define credit policy. It only ensures that verified value is normalized properly so the credits system can issue value under platform rules.

---

## Payment-to-Subscription Mapping

Some payment flows may map not to free-floating credit balances, but directly to subscription or entitlement changes.

This may happen when the platform chooses a direct-purchase experience for:

- plan activation
- subscription renewal
- seat expansion
- module unlock
- duration-bound access package

### Rule

Even if a purchase leads directly to a subscription or entitlement effect, the rail outcome must still be normalized through platform-commercial logic rather than being treated as a raw provider-side entitlement.

The rail layer must not let product teams bypass shared billing semantics merely because the payment is rail-specific.

---

## Rail-Specific Corrections and Refund Handling

A serious multi-rail system must treat correction handling as first-class architecture.

### Correction categories

- refund
- revocation
- chargeback
- processor dispute
- store cancellation
- chain payment anomaly
- support-approved commercial correction

### Architectural rule

Rail-specific reversal events must be normalized into platform-side correction logic before they affect credits, subscriptions, or entitlements.

### Examples

#### Stripe refund
A Stripe refund does not directly mutate credits by itself. It becomes a verified reversal input that downstream credits and billing systems interpret according to usage state.

#### Apple revocation
Store revocation becomes a normalized reversal event that downstream entitlement or credits logic must handle.

#### Stablecoin anomaly
A stablecoin transfer mismatch or mistaken deposit may require support review or policy-driven handling before credits issuance or reversal.

#### FUZE token payment reversal scenario
If a token-input payment path requires correction, conversion and normalization records must support an auditable unwind path where policy allows.

This is essential for commercial integrity.

---

## Chargebacks, Fraud, and Risk Flags

Because some payment rails include dispute and fraud dynamics, the integration layer must support risk-aware states.

### Risk-aware behaviors may include:

- delayed issuance until stronger verification
- restricted issuance class
- manual review path
- account/workspace risk flag
- negative-balance or debt-state trigger in downstream systems if consumed value is later invalidated
- support or fraud-ops escalation

### Risk principle

The payment integration layer is not the full fraud system, but it must surface sufficient normalized signals so downstream billing, credits, abuse, and control systems can respond appropriately.

---

## Relationship to Platform Credits

The payment rail layer is upstream of the Platform Credits system.

Its role is to verify and normalize input value so the credits system can create or modify internal purchasing power under policy.

The rail layer does not define:

- credit classes
- credit spend order
- expiry rules
- internal ledger semantics
- general transfer restrictions

Those belong to the credits domain.

However, the rail layer must preserve enough source context so the credits domain can later support:

- refund-aware reversal
- class assignment
- spend reporting
- audit traceability
- dispute handling
- reconciliation

This relationship must remain explicit.

---

## Relationship to Billing and Invoicing

The payment rail layer also supports the billing and invoicing system.

It provides the normalized payment-side facts needed for:

- invoice settlement status
- receipt generation
- subscription payment state
- payment-history visibility
- failed-payment workflows
- workspace billing ownership records

Again, the rail layer is not the full billing system. It is the verified input and normalization layer that allows billing to work consistently across multiple rails.

---

## Relationship to Revenue and Profit

The rail layer must not blur payment intake into final economic conclusions.

### Architectural boundaries

- verified payment input is not automatically profit
- credits issuance is not automatically profit
- subscription activation is not automatically distributable profit
- platform accounting and treasury policy determine the path from gross payment inflow to recognized and distributable outcomes

This boundary is especially important in FUZE because the stablecoin profit participation model depends on clear separation between:

- payment intake
- internal credits economy
- revenue and cost treatment
- policy-defined distributable profit
- funded payout cycles

The payment rail layer is the entry point, not the end of that chain.

---

## External Trust Boundaries

Payment rails are external trust-boundary systems.

FUZE must always distinguish:

- what the external provider says
- what the platform has verified
- what the platform records as internal truth
- what the platform can reconstruct during reconciliation
- what the platform must treat as disputed or uncertain

### Trust boundary examples

- Stripe is authoritative for raw processor status, but FUZE is authoritative for internal normalized commercial state
- Apple and Google are authoritative for their store transaction data, but FUZE is authoritative for its internal entitlement and credits outcomes
- chain data is authoritative for stablecoin transfer facts, but FUZE still owns policy interpretation of what that transfer means commercially
- Telegram is authoritative for Stars platform transaction context, but FUZE owns normalized platform-commercial interpretation

This trust-boundary model must be explicit in implementation and operations.

---

## Audit Requirements

The payment rails integration system must generate audit visibility for at least:

- payment initiated
- payment verified
- verification failed
- normalization completed
- credits issuance triggered
- entitlement/subscription update triggered
- refund or revocation processed
- dispute or fraud review state entered
- support or finance intervention on payment interpretation

Auditability matters because payment-rail behavior affects credits, subscriptions, reporting, and ultimately trust in the platform economy.

---

## Minimum Data Model Requirements

At minimum, the payment integration layer must support semantic representation of:

### Payment Record
- payment_record_id
- rail_type
- raw_provider_reference
- normalized status
- normalized value basis
- source asset/currency
- target scope type and scope ID
- related billing purpose
- created_at
- verified_at where applicable
- reversal/dispute metadata where applicable

### Verification Record
- verification_record_id
- payment_record_id
- verification_method
- result
- verification evidence reference
- timestamp
- reviewer or system actor where applicable

### Rail Correction Record
- correction_record_id
- original payment reference
- correction type
- external source reference
- normalized effect
- downstream action reference
- timestamp

These are minimum semantic requirements only.

---

## Future Rail Expansion Rules

New payment rails may be added only if they satisfy the following requirements:

1. they can be verified with sufficient confidence
2. they can be normalized into FUZE’s internal commercial model
3. they do not force product-local economic logic outside platform control
4. their refund/reversal semantics can be modeled clearly enough
5. their audit and reporting behavior can be integrated into platform standards

This rule preserves architectural discipline as the ecosystem grows.

---

## Edge Cases and Failure Handling

### Payment succeeds externally but verification webhook is delayed
The platform should remain in pending-verification state until verification is sufficient. No premature credits issuance should occur unless policy explicitly allows a controlled provisional flow.

### Stablecoin transaction arrives with wrong memo/context or wrong scope
The platform must not silently issue credits to the wrong owner. Review or correction handling is required.

### Same provider event is received multiple times
The integration layer must be idempotency-aware and avoid duplicate downstream economic actions.

### App-store purchase verified after entitlement timeout window
The system must still normalize the event correctly and resolve entitlement/credits state according to platform policy rather than dropping or duplicating effects.

### Telegram Stars event later revoked
The platform must route that signal into reversal/correction logic rather than leaving stale economic state active.

### FUZE token payment path introduces volatility or conversion ambiguity
The platform must use explicit conversion and normalization policy. Products may not improvise their own interpretation.

### Payment verified but downstream credits issuance fails
The system must preserve a recoverable state with explicit auditability rather than silently losing the commercial effect.

---

## Open Items

The following areas are intentionally refined in downstream specifications:

- exact per-rail verification protocols
- exact conversion rules for FUZE token payment input
- exact stablecoin deposit addressing model
- exact Apple and Google subscription renewal treatment
- exact fraud-review integration points
- exact reconciliation cadence and settlement operations

These do not weaken the canonical payment-rail architecture established here.

---

## Closing Summary

The FUZE payment rails integration layer allows the platform to accept value through multiple external channels while preserving one coherent internal commercial model. It does this by verifying and normalizing rail-specific events before they affect credits, subscriptions, entitlements, or reporting. Stripe, stablecoins, FUZE token input, Telegram Stars, Apple In-App Purchase, and Google Play Billing are all treated as external value-entry paths rather than as independent internal economic systems. This architecture is what allows FUZE to remain commercially flexible at the edge while maintaining strong internal coherence, auditability, and trust at the platform center.
