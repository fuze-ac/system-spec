# QTB_PRODUCT_INTEGRATION_SPEC

## Purpose

This document defines the canonical product integration architecture for **QTB — Quant AI Trade Brain** within the FUZE platform ecosystem. Its purpose is to establish how QTB integrates with shared platform layers, what QTB owns as a product domain, how QTB consumes shared platform capabilities without redefining them, and how QTB should behave commercially, operationally, and architecturally inside the wider FUZE environment.

This specification is important because QTB is one of the clearest early-wedge products in the FUZE ecosystem. It sits close to the platform’s crypto-native strengths, uses AI heavily, has natural premium monetization paths, and can benefit directly from shared identity, credits, AI orchestration, workflows, wallet-aware context, and transparency infrastructure. QTB is therefore both a product in its own right and a strategic proof point for the FUZE platform model.

---

## Scope

This specification covers:

- the canonical role of QTB inside the FUZE ecosystem
- the distinction between QTB-owned domain logic and platform-owned shared systems
- QTB integration with identity, account, workspace, and permission layers
- QTB integration with Platform Credits, subscriptions, usage billing, and entitlements
- QTB integration with AI orchestration, model routing, workflows, jobs, and scheduling
- QTB-specific product context and wallet-aware participation use
- QTB reporting, audit, transparency, and operational control integration
- boundaries, edge cases, and failure-handling expectations for QTB-platform interactions

This specification does not define detailed market strategies, quantitative trading models, or full front-end UX design. It also does not redefine shared platform layers already specified in:

- `PRODUCT_INTEGRATION_ARCHITECTURE_SPEC.md`
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `AI_ORCHESTRATION_SPEC.md`
- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `AI_USAGE_METERING_SPEC.md`

---

## Design Goals

The design goals of the QTB integration architecture are:

1. to make QTB a strong platform-native product rather than a loosely attached application
2. to preserve QTB’s product-specific intelligence value while reusing FUZE shared infrastructure
3. to support both individual and workspace-oriented QTB usage where relevant
4. to integrate QTB cleanly into Platform Credits, subscriptions, premium AI usage, and reporting
5. to ensure QTB AI actions are metered, attributable, and commercially governed
6. to support async and workflow-driven QTB behaviors without product-local orchestration drift
7. to preserve clear data ownership and domain boundaries
8. to make QTB a repeatable model for future high-value intelligence products in FUZE

---

## Non-Goals

This specification is not intended to:

- define QTB as the owner of shared identity, billing, credits, or AI execution infrastructure
- collapse QTB-specific product logic into generic platform logic
- force QTB into a collaboration model if a feature remains individual by design
- allow QTB to bypass shared AI usage metering or premium billing controls
- treat token ownership as a substitute for QTB authorization or entitlement
- define proprietary strategy logic, alpha-generation logic, or trading methods in this file
- make QTB responsible for ecosystem-wide governance concerns outside its product domain

---

## Canonical Product Principle

The primary integration principle for QTB is:

> QTB owns trading-intelligence product meaning, but it must consume shared FUZE platform systems for identity, billing, credits, AI orchestration, workflow execution, and transparency in a way that preserves one coherent platform architecture.

This means:

- QTB defines what its intelligence features do
- FUZE defines how shared identity, credits, billing, AI orchestration, and automation infrastructure behave
- QTB may shape product-specific monetization and AI task intent, but not invent alternate platform truth
- QTB usage and value capture should strengthen the shared platform rather than form an isolated product silo

This principle makes QTB both differentiated and platform-native.

---

## QTB Product Role in the FUZE Ecosystem

QTB is the **trading intelligence and decision-support product** in the FUZE ecosystem.

Its purpose is to help users interpret market conditions, signals, patterns, and decision contexts through AI-powered or system-assisted product workflows. QTB is not defined merely as a raw charting product or a simple signal feed. It is intended to become an intelligence layer that can support:

- signal explanation
- market-context interpretation
- ranked observations
- alerting and prioritization
- workflow-oriented trading support
- research and reasoning assistance
- premium analysis or deeper structured insight
- potentially broader execution-support coordination over time

Strategically, QTB is important because it provides:

- a commercially legible early wedge
- a crypto-native product aligned with the founder’s background
- a natural use case for premium AI-powered SaaS behavior
- a strong proof of the Platform Credits, AI, and workflow architecture in a real product

QTB should therefore be treated as a first-class platform product and a reference implementation for how high-value intelligence products should integrate with FUZE.

---

## QTB Domain Ownership

QTB must own its domain-specific product meaning and product-specific data.

### QTB-owned concerns include:

- QTB signal definitions and signal outputs
- QTB analysis objects and intelligence artifacts
- QTB watchlists, views, and product-specific user settings
- QTB alert logic and product-specific ranking rules
- QTB domain-specific AI task intent
- QTB validation of AI-generated or AI-assisted intelligence outputs
- QTB product-specific workflow outcomes
- QTB domain reports, summaries, and product explanations

### QTB does not own:

- canonical user identity
- canonical workspace membership truth
- canonical role and permission framework
- canonical payment normalization
- Platform Credits meaning
- canonical subscriptions and invoices
- shared AI model-routing policy
- shared workflow infrastructure
- shared audit or transparency record architecture

### Boundary principle

QTB should be powerful as a product, but it should not become a shadow platform inside FUZE.

---

## Identity and Account Integration

QTB must integrate directly with the canonical FUZE identity and account layer.

### QTB requirements

QTB should rely on FUZE for:

- account registration and login continuity
- canonical user identity resolution
- linked-login compatibility
- session continuity across products
- account-level restriction or suspension interpretation
- account-level product history references where allowed

### QTB rules

- QTB must not create a separate user identity system for core product use
- QTB must not treat local profile state as canonical account truth
- QTB must interpret current session and account status through shared platform controls
- QTB should preserve cross-product continuity so a FUZE user can move into QTB without re-entering a separate identity environment

This helps QTB feel like part of one ecosystem rather than a detached application.

---

## Workspace and Organization Integration

QTB should support both account-scoped and workspace-scoped operating contexts where commercially and operationally relevant.

### Account-scoped QTB usage may include:

- personal watchlists
- personal signals view
- personal premium analysis
- personal AI-assisted market interpretation
- individual credits-backed usage

### Workspace-scoped QTB usage may include:

- team signal boards
- shared market workspaces
- shared watchlists or alert contexts
- organization-level billing and credits funding
- operator roles around collaborative market-intelligence workflows
- future team-oriented intelligence environments

### Workspace integration principles

- QTB must resolve whether an action is personal or workspace-scoped
- workspace-owned QTB resources should belong to the workspace, not merely the member who created them
- workspace billing, credits usage, and seat or entitlement behavior should remain explicit
- QTB must not invent hidden team models outside the shared workspace architecture for platform-relevant concerns

This gives QTB a path from individual product value into collaborative business value without architectural drift.

---

## Role and Permission Integration

QTB must integrate with FUZE’s canonical role and permission system.

### Platform-level permissions relevant to QTB may include:

- view QTB access entitlement
- manage own QTB settings
- access workspace QTB resources
- consume workspace QTB balances where authorized
- manage team watchlists or alerts where permitted
- administer workspace QTB configuration where applicable

### QTB product-specific roles may include:

- QTB viewer
- QTB analyst
- QTB alert manager
- QTB workspace operator
- QTB admin within workspace scope

These product roles should extend, not replace, the platform authorization model.

### Rules

- token ownership must not grant QTB admin rights by itself
- active session alone is not sufficient for workspace-scoped QTB actions
- QTB premium access does not bypass workspace permission rules
- QTB must remain compatible with the shared access-control layer for audit and support clarity

---

## Wallet-Aware Integration

QTB may use wallet-aware and holder-aware platform context where relevant, but only through the canonical FUZE wallet-aware user layer.

### Appropriate wallet-aware uses for QTB may include:

- holder-aware feature visibility
- ecosystem-rank display context
- holder-linked access to selected QTB privileges if policy allows
- wallet-linked user recognition where relevant to the wider platform experience

### Inappropriate wallet-aware uses include:

- granting QTB authority purely from wallet ownership
- bypassing subscription or credits requirements because a wallet is linked
- replacing platform identity with wallet identity inside QTB
- using product-local wallet lists as canonical user-to-wallet truth

### Principle

Wallet-aware participation may enrich QTB experience, but QTB must preserve the distinction between:
- platform identity,
- product entitlement,
- and token participation.

---

## Commercial Integration with Platform Credits

QTB must integrate into the shared FUZE commercial system through Platform Credits and the shared billing architecture.

### QTB monetization paths may include:

- subscription access tiers
- premium analysis access
- usage-based premium AI actions
- advanced alerting or report packs
- workspace intelligence plans
- add-on capacity or premium workflow features

### QTB commercial rules

QTB may define:
- what counts as a premium QTB action
- what features are included in a plan
- what usage is metered
- what actions require credits-backed settlement
- what product packages or tiers exist

QTB may not define:
- a private QTB-only credits system
- direct product-local billing logic outside FUZE billing
- hidden AI charges not visible to platform metering
- separate internal balance semantics that bypass Platform Credits

This is important because QTB is expected to be one of the clearest examples of FUZE’s platform-commercial model in practice.

---

## Subscription and Usage Billing Integration

QTB should use FUZE’s shared subscriptions and usage billing framework.

### QTB subscription use cases may include:

- individual premium tier
- pro intelligence tier
- team/workspace intelligence tier
- premium alerting tier
- structured access to high-value product surfaces

### QTB usage-billing use cases may include:

- premium AI explanation calls
- advanced report generation
- large-context structured analysis
- specialized signal enrichment
- on-demand high-cost reasoning flows

### Hybrid model

QTB may combine:
- recurring subscription access,
- included monthly AI or intelligence usage,
- and overage or premium usage beyond baseline limits.

### Billing principles

- QTB charges should be attributable to product and feature
- QTB usage should map into platform usage and invoice/receipt structures where relevant
- personal and workspace billing context must remain explicit
- QTB should support included usage visibility rather than only hidden backend limits

This allows QTB to operate as a premium intelligence SaaS product without fragmenting the broader FUZE economy.

---

## Entitlement Integration

QTB product access must be derived from platform-commercial and authorization state rather than from UI or provider-side assumptions.

### QTB entitlements may include:

- baseline product access
- premium analysis access
- advanced AI route availability
- workspace-shared intelligence access
- premium report generation
- alerting and notification tiers
- historical data or extended context access if introduced later

### Rules

- entitlement should be evaluated from normalized platform state
- credits availability does not automatically equal product entitlement
- token ownership does not automatically equal QTB entitlement
- plan status, workspace scope, role, and product access must remain distinct but coordinated

This is important because QTB sits at the intersection of premium SaaS access and AI usage, where entitlement confusion would quickly damage user trust.

---

## AI Orchestration Integration

QTB is expected to rely heavily on FUZE’s shared AI orchestration layer.

### QTB AI task examples may include:

- signal explanation
- market-condition summary
- trade-context interpretation
- ranked scenario comparison
- alert prioritization support
- structured risk/observation generation
- report drafting for market intelligence
- workflow-linked research assistance

### QTB owns:

- the domain meaning of these tasks
- product-specific output schemas
- acceptance criteria for AI-assisted results
- QTB-specific output validation

### Shared AI platform owns:

- routing and model selection
- context assembly policy
- provider abstraction
- usage metering hooks
- fallback and degraded-mode execution support
- async execution scaffolding where needed

### Rule

QTB must not create hidden AI execution pathways for core product intelligence that bypass shared orchestration and shared AI commercial visibility.

Because QTB is AI-heavy, its integration discipline here is strategically important for the whole platform.

---

## Model Routing and Context Requirements for QTB

QTB should supply strong task intent while relying on the shared routing and context framework.

### QTB context may need to include:

- account or workspace scope
- QTB product state
- watchlist or signal context
- relevant market or event inputs
- entitlement tier
- wallet-aware holder context where relevant and allowed
- product-specific output format requirements

### Context rules

- QTB should use least-context discipline
- irrelevant user or workspace data should not be included merely because it is available
- structured-output requirements should be explicit where the result drives workflow or report generation
- high-value or premium routes should remain policy-aware and commercially classified

QTB is likely to be one of the more demanding products in terms of reasoning quality and context sensitivity, so alignment with the shared routing/context layer is essential.

---

## AI Usage Metering for QTB

QTB must integrate fully with FUZE’s AI usage metering layer.

### QTB metered AI categories may include:

- standard AI signal explanation
- premium reasoning analysis
- advanced market-structure report generation
- multi-item comparison tasks
- large-context async research jobs
- included quota usage under a subscription
- overage usage after included limits

### Metering requirements

- every meaningful AI action should generate a QTB-attributed AI usage record
- metering should distinguish included, premium, overage, released, failed, and corrected usage
- workspace versus account usage must remain explicit
- QTB product reporting should be able to show usage by feature or task class where useful

Because QTB is a strong candidate for premium AI monetization, metering integrity is central to its platform fit.

---

## Workflow and Automation Integration

QTB should integrate with the shared workflow and automation layer for multi-step or event-driven product behaviors.

### Relevant QTB workflows may include:

- signal detected -> enrich -> explain -> deliver
- user requests premium analysis -> entitlement check -> AI execution -> result validation -> settlement
- report request -> reservation -> async generation -> delivery -> final spend
- alert threshold reached -> workflow trigger -> ranking -> notify
- repeated market scan -> scheduled intelligence refresh -> state update

### Workflow rules

- QTB workflow state must not replace canonical QTB product truth
- QTB must use platform workflow semantics for retries, delays, approvals, and completion states
- high-cost or async QTB workflows should support reservation/release behavior where appropriate
- product-specific logic must remain legible through shared orchestration records

This is important because QTB value may increasingly depend on productized intelligence workflows rather than one-shot requests alone.

---

## Queue, Worker, and Scheduled Task Integration

QTB must use the shared queue, worker, and scheduled-task substrate for deferred execution.

### Likely QTB async workloads include:

- heavy AI analysis jobs
- batched signal enrichment
- scheduled market scans
- delayed report generation
- alert fanout
- retryable provider follow-up work
- periodic cleanup and reconciliation for premium usage flows

### Principles

- QTB should not create hidden async engines for core product behavior
- QTB job classes should map into shared operational queues
- retries must remain idempotent and commercially safe
- scheduled QTB tasks should follow shared retry policy classes rather than product-local ad hoc timing logic

Because QTB may operate in high-frequency and latency-sensitive market contexts, disciplined async integration is especially important.

---

## Product Data Ownership for QTB

QTB owns its domain-specific product data and derived intelligence artifacts.

### QTB-owned data may include:

- signal records
- analysis results
- alert definitions
- watchlists
- product-specific configurations
- report artifacts
- workflow output records
- domain-specific ranking or annotation objects

### Platform-owned related data includes:

- accounts
- workspace membership
- permissions
- credits ledger effects
- AI usage records
- invoices and receipts
- shared audit logs
- shared workflow/job metadata

### Rule

Derived read models may combine QTB and platform data for dashboards or reporting, but they must not change canonical ownership.

---

## Reporting and Transparency Integration

QTB should integrate into FUZE reporting and transparency systems wherever QTB activity has platform relevance.

### QTB integration should support reporting for:

- product-attributed Platform Credits consumption
- QTB AI usage volume and class distribution
- premium versus included usage
- workspace versus account usage patterns
- QTB billing and entitlement error visibility
- correction or reversal patterns affecting QTB usage
- product-operational health and async backlog visibility

### Principle

QTB does not need to expose every proprietary product detail in platform-level reporting, but it must emit enough structured signals that its economic and operational role in FUZE is legible.

This matters because QTB is one of the flagship products of the ecosystem.

---

## Audit Integration

QTB should generate meaningful audit visibility for trust-sensitive product actions.

### Examples include:

- premium analysis execution linked to billable state
- support/admin correction involving QTB commercial or entitlement behavior
- workspace-scoped high-impact QTB configuration changes where relevant
- AI usage correction or reversal
- operator replay or cancellation of sensitive QTB async workflows
- holder-aware privilege changes affecting QTB if introduced

This does not mean every simple QTB read action needs heavy audit logging. It means meaningful product actions that affect money, access, workflow outcome, or sensitive product state should be explainable.

---

## Holder-Aware and Cross-Product Privilege Integration

QTB may participate in ecosystem-level holder-aware behavior as allowed by platform policy.

### Possible uses include:

- holder-rank-informed feature visibility
- ecosystem-level early-access flags
- selected cross-product privileges
- token-aware recognition in QTB surfaces

### Rules

- holder-aware logic must come from platform policy, not QTB-local interpretation
- token participation must not replace QTB subscriptions, Platform Credits, or premium AI billing by default
- cross-product privileges must remain compatible with the distinction between participation and consumption

This allows QTB to feel integrated into the larger FUZE ecosystem without weakening the architectural clarity of token versus credits.

---

## Support, Correction, and Recovery Integration

QTB must be able to participate in platform correction and support flows.

### Relevant QTB correction cases may include:

- wrongly charged premium AI action
- duplicate metered usage
- failed async report after provisional reservation
- entitlement mismatch after renewal or credits funding issue
- workspace-scope misbilling
- stale or failed AI output requiring release or correction

### Rules

- commercial corrections affecting QTB must flow through shared refund/reversal/adjustment systems
- AI-related usage corrections must preserve lineage
- support compensation should be explicit rather than hidden mutation
- QTB must remain compatible with shared support and finance workflows

This is important because QTB is expected to be both premium and AI-heavy, which increases the likelihood of nuanced support cases.

---

## Product Launch and Expansion Readiness for QTB

QTB should satisfy the full FUZE product-integration readiness model.

At minimum, QTB should have:

1. canonical product registration
2. account and session integration
3. workspace integration where applicable
4. permission and entitlement alignment
5. Platform Credits and billing integration
6. AI orchestration and metering integration
7. workflow, queue, and scheduled-task alignment
8. reporting and audit emission compatibility
9. correction and support compatibility
10. observability compatibility with shared platform operations

Because QTB is strategically important to FUZE’s early wedge, its readiness quality should set the standard for later products.

---

## Edge Cases and Failure Handling

### User has QTB session but lost premium entitlement
QTB access should remain bound to current entitlement state rather than stale client assumptions.

### Workspace member triggers premium analysis using wrong scope
The system must resolve and charge the correct scope explicitly rather than silently falling back.

### QTB AI task times out after reservation
Reservation release, retry, or recovery path must be explicit and auditable.

### Duplicate alert or report request enters async flow
Idempotency and business-action lineage must prevent duplicate commercial or product effects.

### Holder-aware QTB feature is visible but user lacks required plan
Visibility does not imply full entitlement; product access must still respect platform-commercial rules.

### Product-specific market context changes materially during async job
QTB may need validation, refresh, or failure-safe handling rather than blindly finalizing stale analysis.

### Support grants compensation after QTB metering bug
This must appear as explicit platform compensation or adjustment rather than silent deletion of history.

---

## Open Items

The following items are intentionally refined in downstream QTB product design and implementation:

- exact QTB plan tiers and pricing
- exact signal taxonomy and product object schemas
- exact team/workspace collaboration model for QTB
- exact premium AI feature boundaries
- exact alerting and scheduled-scan cadence
- exact public/private visibility rules for QTB-generated artifacts

These do not weaken the canonical QTB integration model established here.

---

## Closing Summary

QTB is the trading-intelligence and decision-support product of the FUZE ecosystem, and it should integrate as a fully platform-native product rather than as an isolated application. QTB owns its product-specific intelligence logic, analysis artifacts, and domain workflows, but it must rely on shared FUZE systems for identity, permissions, workspaces, Platform Credits, billing, AI orchestration, AI usage metering, workflow execution, async infrastructure, and transparency-compatible reporting. This architecture allows QTB to function as a commercially strong, AI-heavy flagship product while reinforcing the broader FUZE platform model rather than fragmenting it.
