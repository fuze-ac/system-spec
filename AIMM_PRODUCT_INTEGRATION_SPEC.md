# AIMM_PRODUCT_INTEGRATION_SPEC

## Purpose

This document defines the canonical product integration architecture for **AIMM — AI Market Maker** within the FUZE platform ecosystem. Its purpose is to establish how AIMM integrates with shared platform layers, what AIMM owns as a product domain, how AIMM consumes shared platform capabilities without redefining them, and how AIMM should behave commercially, operationally, and architecturally inside the wider FUZE environment.

This specification is important because AIMM is one of the earliest and most strategically important products in the FUZE ecosystem. It sits close to the founder’s market-making and market-structure experience, aligns naturally with crypto-native demand, and provides a strong practical use case for shared AI orchestration, workflow automation, Platform Credits, premium operational tooling, and transparency-oriented product reporting. AIMM is therefore both a product and a platform validation surface.

---

## Scope

This specification covers:

- the canonical role of AIMM inside the FUZE ecosystem
- the distinction between AIMM-owned domain logic and platform-owned shared systems
- AIMM integration with identity, account, workspace, and permission layers
- AIMM integration with Platform Credits, subscriptions, usage billing, and entitlements
- AIMM integration with AI orchestration, model routing, workflows, jobs, and scheduled tasks
- AIMM-specific product context, operator context, and wallet-aware participation use
- AIMM reporting, audit, transparency, and operational control integration
- boundaries, edge cases, and failure-handling expectations for AIMM-platform interactions

This specification does not define proprietary market-making strategies, exchange-integration details, internal routing alpha, or front-end UX design. It also does not redefine shared platform layers already specified in:

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

The design goals of the AIMM integration architecture are:

1. to make AIMM a platform-native market-operations product rather than an isolated tooling stack
2. to preserve AIMM’s domain-specific operational intelligence while reusing FUZE shared infrastructure
3. to support workspace-centric and operator-centric AIMM usage patterns cleanly
4. to integrate AIMM into Platform Credits, premium operational tooling, AI usage, and reporting
5. to ensure AIMM workflows remain auditable, commercially measurable, and operationally safe
6. to support async, scheduled, and workflow-driven AIMM behavior without product-local orchestration drift
7. to preserve clear boundaries between product state, platform state, and governance-sensitive operations
8. to make AIMM a strong reference model for future operations-heavy products in the FUZE ecosystem

---

## Non-Goals

This specification is not intended to:

- define AIMM as the owner of shared identity, billing, credits, or AI execution infrastructure
- collapse AIMM-specific market-operations logic into generic platform logic
- treat AIMM as a direct executor of unrestricted market actions without policy, workflow, or operator boundaries
- allow AIMM to bypass shared AI usage metering or premium billing controls
- treat token ownership as a substitute for AIMM authorization or entitlement
- define trading venue integrations or market operations policies in full detail in this file
- make AIMM responsible for ecosystem-wide governance outside its product domain

---

## Canonical Product Principle

The primary integration principle for AIMM is:

> AIMM owns market-operations product meaning, but it must consume shared FUZE platform systems for identity, billing, credits, AI orchestration, workflow execution, and transparency in a way that preserves one coherent platform architecture.

This means:

- AIMM defines what operational intelligence, monitoring, coordination, and market-support features do
- FUZE defines how shared identity, credits, billing, AI orchestration, and automation infrastructure behave
- AIMM may shape product-specific monetization and AI task intent, but not invent alternate platform truth
- AIMM usage and value capture should strengthen the shared platform rather than form a detached operations silo

This principle makes AIMM both specialized and platform-native.

---

## AIMM Product Role in the FUZE Ecosystem

AIMM is the **market-operations and liquidity-intelligence product** in the FUZE ecosystem.

Its purpose is to support market-making and market-operations workflows through software, AI-assisted interpretation, operator tooling, and structured execution support. AIMM is not intended to be merely a dashboard. It is intended to become a product environment that can help teams and operators understand and manage market conditions, liquidity-support contexts, inventory-aware workflows, exchange-facing operational state, risk-sensitive operational signals, and related market-structure coordination tasks.

Strategically, AIMM is important because it provides:

- a strong crypto-native operational use case
- a product naturally aligned with premium B2B or operator-focused monetization
- a workflow-heavy environment that validates shared automation infrastructure
- an AI-relevant domain where interpretation and operational support can create strong value
- a second early-wedge product alongside QTB that proves the platform can support both intelligence and operations

AIMM should therefore be treated as a first-class platform product and a reference implementation for how operationally sensitive products integrate with FUZE.

---

## AIMM Domain Ownership

AIMM must own its domain-specific product meaning and product-specific data.

### AIMM-owned concerns include:

- AIMM market-operation configurations
- AIMM operational views, state models, and run artifacts
- AIMM inventory, spread, liquidity, and operational support objects where modeled in product space
- AIMM alerting rules and operator-specific workflows
- AIMM domain-specific AI task intent
- AIMM validation of AI-generated or AI-assisted operational outputs
- AIMM product-specific workflow outcomes
- AIMM domain reports, summaries, and operational decision-support artifacts

### AIMM does not own:

- canonical user identity
- canonical workspace membership truth
- canonical role and permission framework
- canonical payment normalization
- Platform Credits meaning
- canonical subscriptions and invoices
- shared AI model-routing policy
- shared workflow infrastructure
- shared audit or transparency record architecture
- governance-sensitive reserve or ecosystem control paths outside product scope

### Boundary principle

AIMM may be operationally serious, but it must not become a shadow platform or shadow governance layer inside FUZE.

---

## Identity and Account Integration

AIMM must integrate directly with the canonical FUZE identity and account layer.

### AIMM requirements

AIMM should rely on FUZE for:

- account registration and login continuity
- canonical user identity resolution
- linked-login compatibility
- session continuity across products
- account-level restriction or suspension interpretation
- account-level product history references where allowed

### AIMM rules

- AIMM must not create a separate user identity system for core product use
- AIMM must not treat local operator profile state as canonical account truth
- AIMM must interpret current session and account status through shared platform controls
- AIMM should preserve cross-product continuity so a FUZE user can move into AIMM without entering a separate identity environment

This helps AIMM behave as part of one ecosystem rather than as a detached operations console.

---

## Workspace and Organization Integration

AIMM is naturally a **workspace-oriented** product and should integrate strongly with the FUZE workspace and organization layer.

### Account-scoped AIMM usage may include:

- personal market view experiments
- individual operator sandbox usage
- personal premium operational analysis
- limited self-service product evaluation

### Workspace-scoped AIMM usage may include:

- team market-operations environments
- shared liquidity-monitoring contexts
- shared exchange-operation or token-operation workspaces
- role-based operator access
- organization-level billing and credits funding
- workspace-owned reports, alerts, and workflow state
- shared premium operational tooling

### Workspace integration principles

- AIMM must resolve whether an action is personal or workspace-scoped
- workspace-owned AIMM resources should belong to the workspace, not only to the initiating operator
- workspace billing, credits usage, and seat or operator entitlement behavior should remain explicit
- AIMM must not invent hidden collaborative models outside the shared workspace architecture for platform-relevant concerns

This is especially important because AIMM is likely to serve teams, projects, treasuries, token operators, or specialized operating groups rather than only individuals.

---

## Role and Permission Integration

AIMM must integrate with FUZE’s canonical role and permission system.

### Platform-level permissions relevant to AIMM may include:

- view AIMM access entitlement
- manage own AIMM settings
- access workspace AIMM resources
- consume workspace AIMM balances where authorized
- manage workspace AIMM configurations where permitted
- approve or execute specific operator workflows where product policy allows

### AIMM product-specific roles may include:

- AIMM viewer
- AIMM operator
- AIMM operations manager
- AIMM workspace admin
- AIMM report reviewer
- AIMM alert manager

These roles should extend, not replace, the platform authorization model.

### Rules

- token ownership must not grant AIMM operator authority by itself
- active session alone is not sufficient for workspace-scoped AIMM actions
- AIMM premium access does not bypass workspace permission rules
- AIMM must remain compatible with the shared access-control layer for audit and support clarity

Because AIMM may touch operationally important workflows, permission discipline is especially important.

---

## Wallet-Aware Integration

AIMM may use wallet-aware and holder-aware platform context where relevant, but only through the canonical FUZE wallet-aware user layer.

### Appropriate wallet-aware uses for AIMM may include:

- wallet-linked project/operator recognition
- holder-aware access to selected ecosystem-level AIMM privileges if policy allows
- token-aware contextual displays where relevant to broader ecosystem participation
- wallet-linked project identity in selected operational setups where explicitly modeled

### Inappropriate wallet-aware uses include:

- granting AIMM workspace or operator authority purely from wallet ownership
- bypassing subscription, credits, or approval requirements because a wallet is linked
- replacing platform account identity with wallet identity inside AIMM
- using product-local wallet registries as canonical user-to-wallet truth

### Principle

Wallet-aware context may enrich AIMM participation, but it must not erase the distinction between:
- platform identity,
- product authorization,
- and token participation.

---

## Commercial Integration with Platform Credits

AIMM must integrate into the shared FUZE commercial system through Platform Credits and the shared billing architecture.

### AIMM monetization paths may include:

- workspace subscriptions
- premium operational dashboards
- premium alerting or reporting
- credits-backed operational actions
- premium AI-supported operational analysis
- seat or operator access
- specialized add-ons or workflow modules
- high-value market-operations support tiers

### AIMM commercial rules

AIMM may define:
- what counts as a premium AIMM action
- what features are included in an AIMM plan
- what usage is metered
- what actions require credits-backed settlement
- what operator or workspace packages exist

AIMM may not define:
- a private AIMM-only credits system
- direct product-local billing logic outside FUZE billing
- hidden operational charges not visible to platform metering
- separate balance semantics that bypass Platform Credits

Because AIMM is likely to have premium B2B-style economics, strong shared commercial integration is especially important.

---

## Subscription and Usage Billing Integration

AIMM should use FUZE’s shared subscriptions and usage billing framework.

### AIMM subscription use cases may include:

- workspace operational plan
- pro market-support tier
- enterprise-grade operational access in future expansion
- premium alerting or monitoring tier
- advanced reporting or analytics tier

### AIMM usage-billing use cases may include:

- premium AI operational analysis
- advanced market-structure report generation
- high-frequency alerting packages
- specialist workflow execution
- large-context operational review tasks
- premium configuration or simulation actions

### Hybrid model

AIMM may combine:
- recurring workspace access,
- included monthly operational intelligence usage,
- operator or seat structures,
- and premium overage or credits-backed usage beyond baseline limits.

### Billing principles

- AIMM charges should be attributable to product and feature
- AIMM usage should map into platform usage and invoice/receipt structures where relevant
- workspace and account billing context must remain explicit
- AIMM should support visible included usage and premium usage semantics rather than hidden backend-only limits

This allows AIMM to behave like a serious operational SaaS product without fragmenting the FUZE economy.

---

## Entitlement Integration

AIMM product access must be derived from platform-commercial and authorization state rather than from UI assumptions or raw provider-side events.

### AIMM entitlements may include:

- baseline product access
- premium operational dashboard access
- advanced AI route availability
- shared workspace operational access
- premium report generation
- advanced alerting and workflow modules
- higher-frequency or higher-sensitivity operational tooling if introduced later

### Rules

- entitlement should be evaluated from normalized platform state
- credits availability does not automatically equal AIMM product entitlement
- token ownership does not automatically equal AIMM entitlement
- plan status, workspace scope, role, and product access must remain distinct but coordinated

This matters because AIMM is likely to operate with premium workspace logic and potentially operator-tier distinctions.

---

## AI Orchestration Integration

AIMM is expected to rely significantly on FUZE’s shared AI orchestration layer, especially for interpretation, workflow support, and premium operational assistance.

### AIMM AI task examples may include:

- market-condition interpretation
- operational anomaly explanation
- spread or inventory context explanation
- ranked action-support summaries
- alert prioritization support
- structured operational report drafting
- policy-bounded recommendations for operator review
- workflow-linked summarization or classification

### AIMM owns:

- the domain meaning of these tasks
- product-specific output schemas
- acceptance criteria for AI-assisted operational outputs
- AIMM-specific validation of AI-assisted results

### Shared AI platform owns:

- routing and model selection
- context assembly policy
- provider abstraction
- usage metering hooks
- fallback and degraded-mode execution support
- async execution scaffolding where needed

### Rule

AIMM must not create hidden AI execution pathways for core product intelligence or operator support that bypass shared orchestration and shared AI commercial visibility.

Because AIMM may contain high-value premium AI use cases, this integration is strategically important.

---

## Model Routing and Context Requirements for AIMM

AIMM should supply strong task intent while relying on the shared routing and context framework.

### AIMM context may need to include:

- account or workspace scope
- operator role context
- AIMM product state
- alert, report, or operational object context
- relevant market or execution-environment inputs
- entitlement tier
- wallet-aware or holder-aware context where relevant and allowed
- product-specific output format requirements

### Context rules

- AIMM should use least-context discipline
- irrelevant user, workspace, or market data should not be included merely because it is available
- structured-output requirements should be explicit where the result drives workflow or reporting
- high-impact or premium routes should remain policy-aware and commercially classified
- operator-sensitive flows may require tighter validation and stricter context boundaries

Because AIMM sits closer to operational workflows than many products, context quality and policy discipline are especially important.

---

## AI Usage Metering for AIMM

AIMM must integrate fully with FUZE’s AI usage metering layer.

### AIMM metered AI categories may include:

- standard operational explanation
- premium reasoning-heavy market-operations analysis
- advanced report generation
- anomaly interpretation tasks
- workflow-coupled operational AI support
- included quota usage under a subscription
- overage usage after included limits

### Metering requirements

- every meaningful AI action should generate an AIMM-attributed AI usage record
- metering should distinguish included, premium, overage, released, failed, and corrected usage
- workspace versus account usage must remain explicit
- AIMM reporting should be able to show usage by feature or task class where useful

Because AIMM may combine operational value with higher-cost reasoning tasks, metering integrity is central to its economics.

---

## Workflow and Automation Integration

AIMM should integrate with the shared workflow and automation layer for multi-step, event-driven, or operator-reviewed behaviors.

### Relevant AIMM workflows may include:

- operational signal detected -> enrich -> explain -> notify
- premium analysis requested -> entitlement check -> AI execution -> validation -> settlement
- report request -> reservation -> async generation -> operator delivery -> final spend
- threshold breach -> workflow trigger -> rank or summarize -> operator review
- scheduled operational scan -> analysis refresh -> alerting or report update
- correction workflow after invalid charge or failed async result

### Workflow rules

- AIMM workflow state must not replace canonical AIMM product truth
- AIMM must use platform workflow semantics for retries, delays, approvals, and completion states
- high-cost or async AIMM workflows should support reservation/release behavior where appropriate
- product-specific operational logic must remain legible through shared orchestration records
- operator approval gates should be supported where actions are sensitive

This is important because AIMM is likely to rely heavily on automation and queued operational assistance rather than only one-shot user requests.

---

## Queue, Worker, and Scheduled Task Integration

AIMM must use the shared queue, worker, and scheduled-task substrate for deferred execution.

### Likely AIMM async workloads include:

- heavy AI operational analysis jobs
- scheduled market-state scans
- batched alert evaluation
- delayed report generation
- notification fanout
- retryable provider or data-source follow-up work
- periodic cleanup and reconciliation for premium usage flows
- scheduled operational refreshes or scans

### Principles

- AIMM should not create hidden async engines for core product behavior
- AIMM job classes should map into shared operational queues
- retries must remain idempotent and commercially safe
- scheduled AIMM tasks should follow shared retry policy classes rather than product-local timing logic
- sensitive or commercially impactful operational jobs may require stricter escalation and review paths

Because AIMM may run continuously or near-continuously in some operating contexts, disciplined async integration is especially important.

---

## Product Data Ownership for AIMM

AIMM owns its domain-specific product data and derived operational artifacts.

### AIMM-owned data may include:

- operational configuration records
- monitoring or alert definitions
- report artifacts
- market-operation analysis objects
- workflow outputs
- operator annotations
- domain-specific ranking or recommendation objects
- product-specific configuration state

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

Derived read models may combine AIMM and platform data for dashboards or reporting, but they must not change canonical ownership.

---

## Reporting and Transparency Integration

AIMM should integrate into FUZE reporting and transparency systems wherever AIMM activity has platform relevance.

### AIMM integration should support reporting for:

- product-attributed Platform Credits consumption
- AIMM AI usage volume and class distribution
- premium versus included usage
- workspace versus account usage patterns
- AIMM billing and entitlement error visibility
- correction or reversal patterns affecting AIMM usage
- product-operational health and async backlog visibility
- operator workflow volume and failure patterns where useful

### Principle

AIMM does not need to expose every proprietary operational detail in platform-level reporting, but it must emit enough structured signals that its economic and operational role in FUZE is legible.

Because AIMM is one of the flagship products of the ecosystem, structured reporting is important both commercially and operationally.

---

## Audit Integration

AIMM should generate meaningful audit visibility for trust-sensitive product actions.

### Examples include:

- premium operational analysis execution linked to billable state
- support/admin correction involving AIMM commercial or entitlement behavior
- workspace-scoped high-impact AIMM configuration changes
- AI usage correction or reversal
- operator replay or cancellation of sensitive AIMM async workflows
- approval-gated operational workflow decisions where product policy requires
- holder-aware privilege changes affecting AIMM if introduced

This does not mean every simple AIMM read action needs heavy audit logging. It means meaningful actions affecting money, access, operational workflow, or sensitive product state should be explainable.

---

## Holder-Aware and Cross-Product Privilege Integration

AIMM may participate in ecosystem-level holder-aware behavior as allowed by platform policy.

### Possible uses include:

- holder-rank-informed feature visibility
- ecosystem-level early-access flags
- selected cross-product privileges
- token-aware recognition in AIMM surfaces

### Rules

- holder-aware logic must come from platform policy, not AIMM-local interpretation
- token participation must not replace AIMM subscriptions, Platform Credits, or premium operational billing by default
- cross-product privileges must remain compatible with the distinction between participation and consumption

This allows AIMM to feel integrated into the larger FUZE ecosystem without weakening the clarity of token versus credits.

---

## Support, Correction, and Recovery Integration

AIMM must be able to participate in platform correction and support flows.

### Relevant AIMM correction cases may include:

- wrongly charged premium AI operational action
- duplicate metered usage
- failed async report after provisional reservation
- entitlement mismatch after renewal or credits funding issue
- workspace-scope misbilling
- stale or failed AI output requiring release or correction
- scheduled-scan or alert workflow duplication with commercial effect

### Rules

- commercial corrections affecting AIMM must flow through shared refund/reversal/adjustment systems
- AI-related usage corrections must preserve lineage
- support compensation should be explicit rather than hidden mutation
- AIMM must remain compatible with shared support, finance, and ops workflows

Because AIMM is both premium and operations-heavy, strong correction compatibility is necessary.

---

## Product Launch and Expansion Readiness for AIMM

AIMM should satisfy the full FUZE product-integration readiness model.

At minimum, AIMM should have:

1. canonical product registration
2. account and session integration
3. workspace integration
4. permission and entitlement alignment
5. Platform Credits and billing integration
6. AI orchestration and metering integration
7. workflow, queue, and scheduled-task alignment
8. reporting and audit emission compatibility
9. correction and support compatibility
10. observability compatibility with shared platform operations

Because AIMM is strategically important to FUZE’s early wedge, its readiness quality should set a high standard for later operations-oriented products.

---

## Edge Cases and Failure Handling

### User has AIMM session but lost premium entitlement
AIMM access should remain bound to current entitlement state rather than stale client assumptions.

### Workspace operator triggers premium analysis using wrong scope
The system must resolve and charge the correct scope explicitly rather than silently falling back.

### AIMM AI task times out after reservation
Reservation release, retry, or recovery path must be explicit and auditable.

### Duplicate operational alert or report request enters async flow
Idempotency and business-action lineage must prevent duplicate commercial or product effects.

### Holder-aware AIMM feature is visible but user lacks required plan
Visibility does not imply full entitlement; product access must still respect platform-commercial rules.

### Market or operational context changes materially during async job
AIMM may need validation, refresh, or failure-safe handling rather than blindly finalizing stale analysis.

### Support grants compensation after AIMM metering bug
This must appear as explicit platform compensation or adjustment rather than silent deletion of history.

### Scheduled operational scans degrade under provider outage
AIMM must follow shared degraded-mode, retry, and escalation policy rather than improvising product-local behavior.

---

## Open Items

The following items are intentionally refined in downstream AIMM product design and implementation:

- exact AIMM plan tiers and pricing
- exact operator role taxonomy
- exact market-operation object schemas
- exact premium AI feature boundaries
- exact scan, alert, and report cadence
- exact exchange- or venue-facing integration architecture
- exact public/private visibility rules for AIMM-generated artifacts

These do not weaken the canonical AIMM integration model established here.

---

## Closing Summary

AIMM is the market-operations and liquidity-intelligence product of the FUZE ecosystem, and it should integrate as a fully platform-native product rather than as an isolated operations tool. AIMM owns its product-specific operational logic, analysis artifacts, alerts, and domain workflows, but it must rely on shared FUZE systems for identity, permissions, workspaces, Platform Credits, billing, AI orchestration, AI usage metering, workflow execution, async infrastructure, and transparency-compatible reporting. This architecture allows AIMM to function as a commercially strong, operations-heavy flagship product while reinforcing the broader FUZE platform model rather than fragmenting it.
