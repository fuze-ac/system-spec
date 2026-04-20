# PRODUCT_INTEGRATION_ARCHITECTURE_SPEC

## Purpose

This document defines the canonical product integration architecture of the FUZE platform. Its purpose is to establish how current and future products integrate with shared platform layers, how product domains consume platform capabilities without redefining them, how cross-product consistency is preserved, and how product-specific logic remains compatible with one unified FUZE operating model.

This specification is foundational because FUZE is a multi-product platform ecosystem, not a single application with optional modules. Products such as QTB, AIMM, ZAGA, AIE, HerHelp, Botmad, and future products must be able to launch, scale, and evolve on top of shared platform systems without fragmenting identity, billing, credits, AI orchestration, workflows, transparency, or governance discipline. The product integration architecture is the structural model that makes that possible.

---

## Scope

This specification covers:

- the canonical integration model between FUZE platform layers and product domains
- the distinction between platform-owned systems and product-owned systems
- shared capability consumption patterns
- product boundary and responsibility rules
- account-scoped and workspace-scoped product integration
- product interaction with Platform Credits, subscriptions, AI orchestration, workflows, and transparency surfaces
- cross-product continuity requirements
- shared integration contracts and extension principles for future products
- observability, audit, and control implications of product integration
- failure handling and boundary protection for product/platform interactions

This specification does not define the detailed implementation of any one product. Those are refined in downstream product-specific specifications such as:

- `QTB_PRODUCT_INTEGRATION_SPEC.md`
- `AIMM_PRODUCT_INTEGRATION_SPEC.md`
- `ZAGA_PRODUCT_INTEGRATION_SPEC.md`
- `AIE_PRODUCT_INTEGRATION_SPEC.md`
- `HERHELP_PRODUCT_INTEGRATION_SPEC.md`
- `BOTMAD_PRODUCT_INTEGRATION_SPEC.md`

It also does not redefine the deeper internals of shared platform layers, which are covered in:

- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `AI_ORCHESTRATION_SPEC.md`
- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`

---

## Design Goals

The design goals of the FUZE product integration architecture are:

1. to let multiple products operate on one coherent platform without becoming disconnected silos
2. to preserve clear ownership boundaries between platform services and product services
3. to allow products to reuse platform capabilities rather than rebuilding them
4. to make product expansion faster, safer, and more consistent over time
5. to support both shared platform economics and differentiated product behavior
6. to preserve auditability, commercial traceability, and governance discipline across product integrations
7. to support future products without changing the core platform model for each new launch
8. to prevent products from weakening the clarity of token, credits, payout, billing, identity, and transparency distinctions

---

## Non-Goals

This specification is not intended to:

- force all products into the same UX or feature model
- erase product-specific intelligence, workflows, or domain logic
- make the platform own every piece of product state
- allow products to redefine canonical platform meaning for identity, billing, credits, or governance-sensitive actions
- require that every product expose the same commercial packaging
- collapse all product differentiation into one generic application shell
- define product roadmaps or market strategy in this file

---

## Canonical Integration Principle

The primary principle of FUZE product integration is:

> products may differ in domain purpose, workflows, and monetization shape, but they must integrate through shared platform capabilities and shared architectural contracts rather than inventing parallel platform systems.

This means:

- products own product meaning
- the platform owns shared infrastructure and shared economic layers
- products integrate by consuming platform capabilities through explicit contracts
- products may extend the ecosystem, but they may not redefine core platform truth
- future product expansion should strengthen the platform rather than fragment it

This principle is central to FUZE’s identity as a platform company.

---

## Why Product Integration Needs Its Own Architecture

FUZE does not treat products as unrelated applications under one brand. The ecosystem is designed so that products share:

- identity
- account continuity
- workspace context
- Platform Credits
- billing and entitlements
- AI orchestration
- workflows and async execution
- audit and transparency surfaces
- wallet-aware participation context
- governance and control boundaries

Without a product integration architecture, each product would gradually interpret these shared layers differently. Over time, that would create:

- inconsistent account behavior
- fragmented billing semantics
- duplicate AI stacks
- divergent usage metering
- separate support pathways
- incompatible reporting
- unclear cross-product continuity
- weakened platform leverage

A shared product integration architecture prevents this by defining what belongs to the platform, what belongs to products, and how those layers connect.

This is especially important in FUZE because the ecosystem already spans multiple categories:

- trading intelligence
- market operations
- token utility systems
- event intelligence
- SME software
- AI work automation
- and future product categories beyond the current list

The more diverse the products become, the more important shared integration discipline becomes.

---

## Canonical Product Integration Model

FUZE products should be integrated through a layered model.

### Platform layer responsibilities
The platform owns reusable systems that should behave consistently across products.

These include:

- identity and account
- linked login and session handling
- workspace and organization logic
- role and permission controls
- wallet-aware user context
- payment intake normalization
- Platform Credits
- subscriptions and usage billing
- invoices and receipts
- AI orchestration
- workflow and automation substrate
- queue and worker substrate
- transparency and audit systems
- governance and control plane

### Product layer responsibilities
Each product owns:

- product-specific domain objects
- product-specific user and workspace workflows
- product-specific decision logic
- product-specific AI task intent
- product-specific UX and interaction surfaces
- product-specific validation of AI outputs
- product-specific value proposition and market-facing behavior

### Integration boundary
Products consume platform capabilities through explicit service contracts, APIs, events, and policy-bounded extension points.

This means the product integration architecture is neither:
- product-only, nor
- platform-only.

It is the disciplined interface between them.

---

## Platform-Owned vs Product-Owned Concerns

A strong product integration architecture depends on explicit ownership separation.

### Platform-owned concerns

The following concerns are canonical platform concerns and must not be redefined independently by products:

- who the user is
- how the user authenticates
- what workspace the user belongs to
- what role and permission the user holds
- how external payments are normalized
- what Platform Credits mean
- how subscriptions and usage billing are represented
- how AI usage is commercially metered
- how shared workflow execution is coordinated
- how transparency and audit records are structured
- how governance-sensitive platform actions are controlled

### Product-owned concerns

The following concerns are typically product-owned:

- what domain-specific objects the product creates
- what workflows matter to the product
- what product-specific AI tasks are requested
- what a product-specific action means
- what outputs need product validation
- what feature bundles and product-specific usage meters exist within platform constraints
- how the product presents information and actions to the user

### Boundary rule

Products may not quietly absorb platform concerns simply because local implementation would be easier. Likewise, the platform should not absorb product-specific domain logic so aggressively that products lose meaningful differentiation.

---

## Shared Capability Consumption

Products in FUZE are expected to consume shared platform capabilities through explicit integration patterns.

### 1. Identity and Session Consumption
Products should rely on the canonical platform identity and session layer rather than implementing separate login systems.

### 2. Workspace and Permission Consumption
Products should use platform workspace context, membership, and authorization rules rather than inventing incompatible collaborative models for shared concerns.

### 3. Platform Credits and Billing Consumption
Products should use platform-commercial infrastructure for:
- subscriptions
- usage charging
- feature unlocks
- add-ons
- premium AI activity
- workspace billing

### 4. AI Orchestration Consumption
Products should declare task intent and product-specific needs, while platform AI systems handle execution routing, context policy, metering hooks, and structured orchestration.

### 5. Workflow and Async Consumption
Products should use platform workflow, queue, scheduler, and worker infrastructure for multi-step or heavy execution rather than building hidden async stacks for core product behavior.

### 6. Audit and Reporting Consumption
Products should emit product-meaningful events and records in ways that allow shared audit, transparency, and reporting systems to remain coherent.

### 7. Wallet-Aware Participation Consumption
Where relevant, products should consume wallet-aware and holder-aware platform context through canonical platform models rather than creating hidden wallet identity systems.

The principle across all of these is reuse with discipline.

---

## Canonical Integration Paths

Products should integrate with the platform through a small set of canonical paths.

### API-based integration
Used when a product needs synchronous access to shared platform services, such as:
- identity resolution
- billing checks
- balance reads
- entitlement checks
- permissions evaluation

### Event-based integration
Used when a product or platform service needs to react to state changes or produce downstream effects, such as:
- payment verified
- credits issued
- subscription renewed
- AI task completed
- workflow finalized
- product usage metered

### Workflow-coupled integration
Used when product actions require multi-step coordination involving AI, async jobs, billing, approvals, or retries.

### Read-model integration
Used when products consume derived platform views for dashboards, balances, reports, holder context, or workspace summaries.

### Policy-governed control integration
Used when a product needs to invoke or reflect a sensitive platform action that remains bounded by governance, control-plane, or operator workflows.

These integration paths allow products to connect deeply to the platform without bypassing shared architectural discipline.

---

## Product Integration Lifecycle

A FUZE product should follow a structured integration lifecycle.

### 1. Product registration in platform context
The product is recognized as a product domain with defined identifiers, integration contracts, usage attribution, and policy class.

### 2. Identity and workspace integration
The product integrates with account, workspace, session, and authorization systems.

### 3. Commercial integration
The product defines how it uses:
- subscriptions
- usage billing
- Platform Credits
- entitlements
- invoices and receipts where relevant

### 4. AI and workflow integration
If AI or multi-step execution is relevant, the product defines how it invokes:
- AI orchestration
- workflows
- jobs and workers
- scheduling and retry behavior

### 5. Reporting and transparency integration
The product ensures its key economic and operational outputs can be reflected in shared audit and reporting surfaces.

### 6. Policy and control alignment
The product aligns sensitive operations with platform control, correction, and governance pathways where required.

This lifecycle exists so that adding a new product does not mean inventing a new platform.

---

## Account and Workspace Integration Rules

Products must integrate cleanly with both account scope and workspace scope where relevant.

### Account integration rules
A product should be able to support:

- personal usage
- account-level history
- account-level billing where applicable
- account-level permissions for self-service actions

### Workspace integration rules
Where the product supports collaborative or business use, it should be able to support:

- workspace-owned product resources
- shared billing context
- workspace seats or operator roles if needed
- workspace-scoped entitlements
- membership-aware access control
- shared credit usage where policy allows

### Scope clarity rule
A product must always know whether:
- the user is acting in personal context,
- or the user is acting in workspace context.

Commercial, access, and reporting behavior should follow that scope explicitly.

This is particularly important for HerHelp and Botmad, but the rule is broadly relevant across the ecosystem.

---

## Product and Platform Data Ownership

Product integration requires clear data-ownership rules.

### Platform-owned data examples
- account records
- workspace records
- role assignments
- linked login records
- wallet-link records
- payment normalization records
- credits ledger records
- invoice and receipt records
- AI usage records
- shared audit event records

### Product-owned data examples
- QTB strategy artifacts
- AIMM operational configurations
- ZAGA utility configurations
- AIE event views or domain objects
- HerHelp generated app/project artifacts
- Botmad workflow recommendations or task objects

### Derived/shared data rule
Derived read models may combine platform and product data for reporting or UX, but they must not redefine canonical ownership.

This rule matters because products need freedom to evolve without corrupting shared platform truth.

---

## Commercial Integration Requirements

Every FUZE product must integrate into the shared platform commercial model if it exposes monetized value.

### Commercial integration may involve:
- subscription plans
- usage-based charging
- add-ons
- premium AI usage
- included quotas
- workspace plans
- seat-based billing
- credits-funded unlocks

### Required commercial rules
Products may define:
- what is monetized
- what usage is counted
- what features require premium access
- what plan tiers exist

Products may not define:
- an alternate hidden balance system
- direct product-local payment truth that bypasses normalization
- product-local “credits” that conflict with Platform Credits
- entitlements that ignore platform-commercial state
- silent monetization changes with no platform reporting trace

This is one of the clearest ways the platform-company model asserts itself.

---

## AI Integration Requirements

Products using AI must integrate through the shared AI architecture.

### Products may define:
- AI task intent
- product-specific context needs
- output schemas
- validation logic
- AI-assisted UX patterns

### Platform AI layer provides:
- routing
- provider selection
- context policy
- usage metering hooks
- retries and fallback scaffolding
- execution observability

### Boundary rule
Products must not implement hidden AI provider stacks for core product behavior if doing so would break metering, cost visibility, auditability, or policy consistency.

This is particularly important because AI is a major shared layer in FUZE.

---

## Workflow and Async Integration Requirements

Products with multi-step, long-running, or event-driven behaviors must integrate through the shared workflow and async substrate.

### Relevant product behaviors include:
- AI-heavy generation
- approval-driven actions
- async report generation
- billing follow-ups
- queued analysis
- delayed fulfillment
- scheduled scans or sync jobs
- export generation
- automated alerts or triggers

### Required rules
Products may define workflow meaning, but they should use:
- shared workflow lifecycle semantics
- shared queue and worker discipline
- shared retry policy classes
- shared idempotency expectations
- shared observability and audit patterns

Products should not create hidden background engines for core platform-relevant behavior that would make support, billing, or reliability inconsistent.

---

## Transparency and Reporting Integration

Products must integrate into shared transparency and reporting systems where their actions affect platform trust, commercial meaning, or ecosystem visibility.

### Product integration should support:
- product-attributed usage reporting
- product-attributed AI usage reporting
- workspace and account activity visibility where relevant
- billing and credits traceability
- correction and refund traceability where relevant
- public-facing or investor-facing summaries where policy allows

### Important distinction
Not every product detail must become public. But platform-relevant economic and structural meaning should remain visible enough that shared reporting is possible.

A multi-product platform with no shared reporting logic becomes hard to evaluate and hard to govern.

---

## Wallet-Aware and Holder-Aware Product Integration

Products may use wallet-aware and holder-aware context, but only through shared platform rules.

### Allowed product uses
- show holder rank
- unlock holder-aware benefits
- support wallet-linked participation flows
- display snapshot-related context where appropriate
- reflect ecosystem-level holder privileges

### Disallowed product behavior
- redefining wallet linkage independently
- granting workspace authority based only on token ownership
- bypassing billing or permissions because a user holds tokens
- inventing product-local holder logic that conflicts with platform policy

This rule preserves the distinction between:
- token participation,
- product consumption,
- and platform authorization.

It is central to FUZE’s clarity.

---

## Cross-Product Continuity Principles

FUZE products should strengthen cross-product continuity rather than create repeated resets of user context.

### Continuity should exist across:
- account identity
- linked login methods
- wallet-aware participation context
- workspace membership
- billing and credit environment where policy allows
- entitlement awareness
- AI usage and workflow reporting visibility where relevant
- product discovery inside one ecosystem

### Important rule
Cross-product continuity does not mean every product shares every piece of product-private data. It means the user should experience one ecosystem logic rather than many unrelated software islands.

This continuity is one of the strongest strategic reasons for building FUZE as a platform company first.

---

## Product Launch Readiness Requirements

Before a FUZE product should be considered properly integrated, it should satisfy a minimum readiness checklist.

At minimum, the product should have:

1. canonical product registration and identifiers
2. account and session integration
3. workspace integration where relevant
4. role and permission alignment
5. commercial integration with billing and Platform Credits
6. AI integration through shared orchestration if AI is used
7. workflow/async integration if deferred execution is used
8. audit and reporting emission alignment
9. correction and support integration for monetized or trust-sensitive behavior
10. observability compatibility with shared platform operations

This readiness model helps ensure that “shipping a new product” does not mean bypassing the platform’s own architectural standards.

---

## Future Product Expansion Rules

Future FUZE products should be judged partly by how well they fit the platform architecture.

A future product is strong when:

- it benefits from shared infrastructure
- it reinforces the Platform Credits economy
- it can use shared AI or workflow systems meaningfully
- it fits the transparency and control model
- it adds strategic breadth without adding architectural fragmentation

A future product is weak if it requires:
- a separate identity system
- a separate internal economy
- a separate uncontrolled AI or workflow substrate
- incompatible wallet or holder logic
- a different interpretation of token/credits/payout roles

This is why the phrase “and more” in FUZE should never imply undisciplined expansion. It should imply platform-compatible expansion.

---

## Observability Requirements

The product integration architecture should support platform-wide visibility into:

- which products are integrated
- which platform capabilities each product consumes
- product-attributed credits usage
- product-attributed AI usage
- product-attributed workflow volume
- account vs workspace distribution by product
- entitlement and commercial errors by product
- product integration failure patterns
- cross-product user continuity patterns

This matters because product integration quality directly affects platform leverage.

---

## Audit Requirements

At minimum, meaningful product integration events should be auditable enough to support:

- support investigation
- commercial dispute handling
- correction workflows
- governance-sensitive review where applicable
- platform trust and reporting
- launch-readiness review for new products

Examples include:

- product registration or configuration changes
- monetized feature rollout changes
- product usage affecting Platform Credits
- AI-usage policy changes by product
- cross-product holder benefit changes
- support/admin corrections involving product/platform boundaries

Not every product action needs the same audit depth, but important product-to-platform interactions must remain explainable.

---

## Minimum Data Model Requirements

At minimum, the product integration architecture should support semantic representation of:

### Product Registry Record
- product_id
- product_name
- product_class
- active status
- integration version
- owning product domain reference
- shared-capability flags
- created_at

### Product Capability Mapping
- product_id
- capability_type
- capability_reference
- scope applicability
- commercial integration flag
- AI integration flag
- workflow integration flag
- reporting integration flag

### Product Context Linkage
- product_id
- account integration mode
- workspace integration mode
- billing integration mode
- holder-aware integration mode
- product-specific policy references

### Integration Audit Record
- product_id
- integration action
- actor/system source
- timestamp
- result
- related platform capability references where relevant

These are minimum semantic requirements. Detailed schemas are refined downstream.

---

## Edge Cases and Failure Handling

### Product launches without full billing integration
The product should not expose monetized behavior that bypasses shared platform commercial systems.

### Product caches stale workspace permissions
Sensitive actions must defer to current platform authorization rather than stale product-local assumptions.

### Product-specific AI flow bypasses shared metering
This should be treated as architectural drift and corrected, because it weakens commercial and observability integrity.

### Product wants a unique wallet-linked behavior
Allowed only if it remains compatible with canonical wallet-aware and holder-aware platform rules.

### Product migration from standalone prototype into FUZE
A migration path may exist, but canonical platform systems should replace prototype-local substitutes over time.

### Cross-product entitlement confusion
The platform should preserve clarity about which entitlements are product-specific and which are ecosystem-level.

### Future product requires novel commercial pattern
The product may extend pricing and billing behavior, but only through shared platform-commercial architecture rather than parallel economic systems.

---

## Open Items

The following areas are intentionally refined in downstream or product-specific specifications:

- exact service boundaries by product domain
- exact product registry implementation
- exact launch-readiness process and gates
- exact event schemas for product-to-platform integrations
- exact cross-product discovery and shared-nav behavior
- exact partner or third-party product integration patterns if introduced later

These do not weaken the canonical product integration architecture established here.

---

## Closing Summary

The FUZE product integration architecture defines how multiple products operate on one shared platform without becoming disconnected silos or weakening the clarity of core platform systems. It preserves explicit boundaries between platform-owned infrastructure and product-owned domain logic, while requiring products to integrate through shared identity, workspace, billing, Platform Credits, AI orchestration, workflow, transparency, and control models. This architecture is what allows FUZE to function as a true platform company: one that can launch, monetize, govern, and expand multiple AI-powered products inside one coherent operating system.
