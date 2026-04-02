# HERHELP_PRODUCT_INTEGRATION_SPEC

## Purpose

This document defines the canonical product integration architecture for **HerHelp — AI-assisted business that starts with sheet-to-app — an AI SaaS for SMEs** within the FUZE platform ecosystem. Its purpose is to establish how HerHelp integrates with shared platform layers, what HerHelp owns as a product domain, how HerHelp consumes shared platform capabilities without redefining them, and how HerHelp should behave commercially, operationally, and architecturally inside the wider FUZE environment.

This specification is important because HerHelp represents one of the most important expansion paths in the FUZE ecosystem. It extends FUZE beyond crypto-native intelligence and infrastructure into a broader SME software market while remaining fully compatible with the shared platform thesis. HerHelp begins with sheet-to-app because spreadsheet-centered work is one of the clearest operational realities for SMEs. However, the product direction is intentionally wider: HerHelp is meant to evolve into an AI-assisted business operating environment for SMEs rather than remaining only a spreadsheet conversion utility.

---

## Scope

This specification covers:

- the canonical role of HerHelp inside the FUZE ecosystem
- the distinction between HerHelp-owned domain logic and platform-owned shared systems
- HerHelp integration with identity, account, workspace, permission, and commercial layers
- HerHelp integration with Platform Credits, subscriptions, usage billing, and entitlements
- HerHelp integration with AI orchestration, model routing, workflows, jobs, and scheduled tasks
- HerHelp-specific product context, business-data context, and AI-assisted generation logic
- HerHelp reporting, audit, transparency, and operational control integration
- boundaries, edge cases, and failure-handling expectations for HerHelp-platform interactions

This specification does not define all sheet connectors, detailed page-builder UX, spreadsheet schema design for every template, or front-end implementation details. It also does not redefine shared platform layers already specified in:

- `PRODUCT_INTEGRATION_ARCHITECTURE_SPEC.md`
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `AI_ORCHESTRATION_SPEC.md`
- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `AI_USAGE_METERING_SPEC.md`

---

## Design Goals

The design goals of the HerHelp integration architecture are:

1. to make HerHelp a platform-native SME software product rather than a detached sheet tool
2. to preserve HerHelp’s domain-specific business-operation logic while reusing FUZE shared infrastructure
3. to support workspace-centric and business-centric usage patterns cleanly
4. to integrate HerHelp into Platform Credits, subscriptions, AI usage, and feature-level monetization
5. to ensure HerHelp generation, sync, and workflow behavior remain auditable, commercially measurable, and operationally safe
6. to support async, scheduled, and AI-assisted HerHelp behavior without product-local orchestration drift
7. to preserve clear boundaries between business data, platform data, and product-generated artifacts
8. to make HerHelp a strong reference model for future non-crypto vertical products in the FUZE ecosystem

---

## Non-Goals

This specification is not intended to:

- define HerHelp as the owner of shared identity, billing, credits, or AI execution infrastructure
- collapse HerHelp-specific business-generation logic into generic platform logic
- make spreadsheet state the canonical owner of all platform or product meaning
- allow HerHelp to bypass shared AI usage metering or premium billing controls
- treat token ownership as a substitute for HerHelp authorization or entitlement
- define all future SME vertical templates in this file
- make HerHelp responsible for ecosystem-wide governance outside its product domain

---

## Canonical Product Principle

The primary integration principle for HerHelp is:

> HerHelp owns AI-assisted business-software product meaning, but it must consume shared FUZE platform systems for identity, billing, credits, AI orchestration, workflow execution, and transparency in a way that preserves one coherent platform architecture.

This means:

- HerHelp defines what sheet-to-app, business-generation, workspace operations, and AI-assisted SME workflows do
- FUZE defines how shared identity, credits, billing, AI orchestration, and automation infrastructure behave
- HerHelp may shape product-specific monetization and AI task intent, but not invent alternate platform truth
- HerHelp usage and value capture should strengthen the shared platform rather than form a detached SaaS silo

This principle makes HerHelp both category-expanding and platform-native.

---

## HerHelp Product Role in the FUZE Ecosystem

HerHelp is the **AI-assisted business software product for SMEs** in the FUZE ecosystem.

It begins with a sheet-to-app entry point because many SMEs already run core operations in spreadsheets. This includes lead tracking, inventory management, attendance tracking, operational checklists, customer records, task flows, approvals, and simple financial tracking. The sheet-to-app starting point allows HerHelp to meet users where they already are.

However, HerHelp is not intended to remain limited to spreadsheet transformation. Its wider role is to become an AI-assisted environment that helps SMEs move from loosely structured manual operations toward more structured, generated, and automatable business software.

This may include:

- sheet-to-app generation
- business workflow generation
- forms and CRUD interfaces
- approval flows
- AI-assisted restructuring of manual business processes
- template-driven business operations
- workspace collaboration
- team and role-based operations
- usage-based premium business automation

Strategically, HerHelp is important because it proves that FUZE is not confined to crypto-native categories. It extends the platform into a much broader software market while still relying on the same shared systems for identity, billing, Platform Credits, AI orchestration, workflows, and transparency-compatible reporting.

HerHelp should therefore be treated as a first-class FUZE product and a reference implementation for vertical SaaS expansion on top of the platform.

---

## HerHelp Domain Ownership

HerHelp must own its domain-specific product meaning and product-specific data.

### HerHelp-owned concerns include:

- HerHelp app/project definitions
- HerHelp generated page, form, and flow configurations
- HerHelp business template and workspace configuration logic
- HerHelp spreadsheet mapping and app-generation artifacts
- HerHelp domain-specific AI task intent
- HerHelp validation of AI-generated or AI-assisted business outputs
- HerHelp product-specific workflow outcomes
- HerHelp SME operation artifacts, generated views, and product-level settings

### HerHelp does not own:

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

HerHelp may manage business-operational artifacts, but it must not become a shadow platform with its own hidden identity, billing, credits, or orchestration layer.

---

## Identity and Account Integration

HerHelp must integrate directly with the canonical FUZE identity and account layer.

### HerHelp requirements

HerHelp should rely on FUZE for:

- account registration and login continuity
- canonical user identity resolution
- linked-login compatibility
- session continuity across products
- account-level restriction or suspension interpretation
- account-level product history references where allowed

### HerHelp rules

- HerHelp must not create a separate user identity system for core product use
- HerHelp must not treat local editor or builder profile state as canonical account truth
- HerHelp must interpret current session and account status through shared platform controls
- HerHelp should preserve cross-product continuity so a FUZE user can move into HerHelp without entering a separate identity environment

This helps HerHelp behave as part of one platform rather than as a detached SME builder application.

---

## Workspace and Organization Integration

HerHelp is naturally a **workspace-oriented** product and should integrate strongly with the FUZE workspace and organization layer.

### Account-scoped HerHelp usage may include:

- personal prototype or sandbox apps
- solo business experiments
- simple self-service page or form generation
- initial onboarding and template testing

### Workspace-scoped HerHelp usage may include:

- SME team workspaces
- role-based collaborator access
- shared business apps and internal pages
- shared data-entry and approval environments
- shared billing and credits funding
- team-operated business workflows
- company-owned app and template environments

### Workspace integration principles

- HerHelp must resolve whether an action is personal or workspace-scoped
- workspace-owned HerHelp resources should belong to the workspace, not only to the initiating builder
- workspace billing, credits usage, seat logic, and access control must remain explicit
- HerHelp must not invent hidden collaborative models outside the shared workspace architecture for platform-relevant concerns

This is especially important because SMEs often require multi-user collaboration even when the product entry point starts with one person and one spreadsheet.

---

## Role and Permission Integration

HerHelp must integrate with FUZE’s canonical role and permission system.

### Platform-level permissions relevant to HerHelp may include:

- view HerHelp access entitlement
- manage own HerHelp settings
- access workspace HerHelp resources
- consume workspace HerHelp balances where authorized
- manage workspace HerHelp app configurations where permitted
- approve or publish selected HerHelp flows where product policy allows

### HerHelp product-specific roles may include:

- HerHelp viewer
- HerHelp editor
- HerHelp builder
- HerHelp workspace admin
- HerHelp approver
- HerHelp operator

These roles should extend, not replace, the platform authorization model.

### Rules

- token ownership must not grant HerHelp admin or builder authority by itself
- active session alone is not sufficient for workspace-scoped HerHelp actions
- HerHelp premium access does not bypass workspace permission rules
- HerHelp must remain compatible with the shared access-control layer for audit and support clarity

Because HerHelp may support internal business workflows and approvals, permission discipline is especially important.

---

## Commercial Integration with Platform Credits

HerHelp must integrate into the shared FUZE commercial system through Platform Credits and the shared billing architecture.

### HerHelp monetization paths may include:

- workspace subscriptions
- page and feature limits
- collaborator or seat-based access
- AI-assisted generation usage
- premium templates or modules
- automation features
- public sharing or publishing features
- add-on business tools
- usage-based premium operations

### HerHelp commercial rules

HerHelp may define:

- what counts as a premium HerHelp action
- what features are included in a HerHelp plan
- what usage is metered
- what actions require credits-backed settlement
- what workspace packages or limits exist

HerHelp may not define:

- a private HerHelp-only credits system
- direct product-local billing logic outside FUZE billing
- hidden AI or automation charges not visible to platform metering
- separate balance semantics that bypass Platform Credits

Because HerHelp may operate in a high-frequency workspace environment, strong shared commercial integration is especially important.

---

## Subscription and Usage Billing Integration

HerHelp should use FUZE’s shared subscriptions and usage billing framework.

### HerHelp subscription use cases may include:

- free or starter workspace tier
- SME workspace plan
- pro business operations tier
- collaborator or seat-based plan variants
- premium module bundles

### HerHelp usage-billing use cases may include:

- AI app-generation requests
- premium transform or regenerate actions
- automation executions
- template deployment actions
- high-volume workflow runs
- export or advanced publishing operations

### Hybrid model

HerHelp may combine:

- recurring workspace access,
- included AI generation or automation usage,
- collaborator or seat structures,
- and premium overage or credits-backed usage beyond baseline limits.

### Billing principles

- HerHelp charges should be attributable to product and feature
- HerHelp usage should map into platform usage and invoice/receipt structures where relevant
- workspace and account billing context must remain explicit
- HerHelp should support visible included-usage and premium-usage semantics rather than hidden backend-only limits

This allows HerHelp to function as a serious SME SaaS product while preserving one coherent FUZE internal economy.

---

## Entitlement Integration

HerHelp product access must be derived from platform-commercial and authorization state rather than from UI assumptions or raw provider-side events.

### HerHelp entitlements may include:

- baseline product access
- workspace builder access
- collaborator seat access
- AI generation availability
- premium templates or modules
- automation access
- public publishing or sharing access
- advanced workflow capabilities

### Rules

- entitlement should be evaluated from normalized platform state
- credits availability does not automatically equal HerHelp product entitlement
- token ownership does not automatically equal HerHelp entitlement
- plan status, workspace scope, role, and product access must remain distinct but coordinated

This matters because HerHelp combines recurring SaaS value with potentially metered AI and automation usage.

---

## AI Orchestration Integration

HerHelp is expected to rely heavily on FUZE’s shared AI orchestration layer.

### HerHelp AI task examples may include:

- sheet-to-app generation
- page or form generation
- business workflow generation
- field mapping suggestion
- layout restructuring
- prompt-based business-tool creation
- automation suggestion
- template adaptation
- data transformation assistance

### HerHelp owns:

- the domain meaning of these tasks
- product-specific output schemas
- acceptance criteria for AI-assisted outputs
- HerHelp-specific validation of AI-assisted results

### Shared AI platform owns:

- routing and model selection
- context assembly policy
- provider abstraction
- usage metering hooks
- fallback and degraded-mode execution support
- async execution scaffolding where needed

### Rule

HerHelp must not create hidden AI execution pathways for core product behavior that bypass shared orchestration and shared AI commercial visibility.

Because HerHelp may become one of the highest-frequency AI products in the ecosystem, this integration discipline is especially important.

---

## Model Routing and Context Requirements for HerHelp

HerHelp should supply strong task intent while relying on the shared routing and context framework.

### HerHelp context may need to include:

- account or workspace scope
- builder or operator role context
- HerHelp project or app state
- sheet schema or mapped structure context
- template or module context
- entitlement tier
- product-specific output format requirements
- workflow or publication context where applicable

### Context rules

- HerHelp should use least-context discipline
- irrelevant workspace or business data should not be included merely because it is available
- structured-output requirements should be explicit where results drive product generation or workflow state
- premium or high-value routes should remain policy-aware and commercially classified
- sensitive business data should be bounded carefully in context assembly and downstream storage

Because HerHelp may work with business-process content and business data, context precision and privacy discipline are especially important.

---

## AI Usage Metering for HerHelp

HerHelp must integrate fully with FUZE’s AI usage metering layer.

### HerHelp metered AI categories may include:

- standard AI generation
- premium reasoning-heavy business transformation
- advanced workflow generation
- form/page regeneration
- template adaptation
- large-context business structure synthesis
- included quota usage under a subscription
- overage usage after included limits

### Metering requirements

- every meaningful AI action should generate a HerHelp-attributed AI usage record
- metering should distinguish included, premium, overage, released, failed, and corrected usage
- workspace versus account usage must remain explicit
- HerHelp reporting should be able to show usage by feature or task class where useful

Because HerHelp may combine frequent generation actions with workspace billing, metering integrity is central to both cost control and monetization clarity.

---

## Workflow and Automation Integration

HerHelp should integrate with the shared workflow and automation layer for multi-step, event-driven, or approval-sensitive behaviors.

### Relevant HerHelp workflows may include:

- sheet import -> schema detect -> map -> generate -> validate -> publish
- prompt-based generation -> AI output -> validation -> preview -> save
- premium generation request -> entitlement check -> AI execution -> result validation -> settlement
- collaborator action -> approval flow -> state update
- automation definition -> validation -> activation -> execution
- correction workflow after invalid charge or failed async result

### Workflow rules

- HerHelp workflow state must not replace canonical HerHelp product truth
- HerHelp must use platform workflow semantics for retries, delays, approvals, and completion states
- high-cost or async HerHelp workflows should support reservation/release behavior where appropriate
- product-specific business-generation logic must remain legible through shared orchestration records
- approval gates should be supported where builder, operator, or workflow-sensitive actions require them

This matters because HerHelp is likely to evolve from simple generation into broader business-process execution support.

---

## Queue, Worker, and Scheduled Task Integration

HerHelp must use the shared queue, worker, and scheduled-task substrate for deferred execution.

### Likely HerHelp async workloads include:

- heavy AI generation jobs
- batch regeneration jobs
- sheet sync follow-up tasks
- scheduled business workflow checks
- delayed export generation
- retryable integration follow-up work
- periodic cleanup and reconciliation for premium usage flows
- scheduled automation runs

### Principles

- HerHelp should not create hidden async engines for core product behavior
- HerHelp job classes should map into shared operational queues
- retries must remain idempotent and commercially safe
- scheduled HerHelp tasks should follow shared retry policy classes rather than product-local timing logic
- high-frequency business workflows should remain observable and bounded by shared platform operations discipline

Because HerHelp may support repeated sync, automation, and generation flows, disciplined async integration is especially important.

---

## Product Data Ownership for HerHelp

HerHelp owns its domain-specific product data and generated business artifacts.

### HerHelp-owned data may include:

- workspace app definitions
- generated pages, forms, and views
- workflow definitions
- template instances
- field mappings
- publish configurations
- business-operation artifacts
- builder annotations and product-specific settings

### Platform-owned related data includes:

- accounts
- workspace membership
- permissions
- credits ledger effects
- AI usage records
- invoices and receipts
- shared audit logs
- shared workflow/job metadata

### Spreadsheet and external business data boundary

When HerHelp uses spreadsheet-connected or imported business data, that data may remain product-context data or external-source data depending on the integration model. It should not be treated automatically as canonical platform-owned identity, billing, or governance data merely because HerHelp can transform or display it.

### Rule

Derived read models may combine HerHelp and platform data for dashboards or reporting, but they must not change canonical ownership.

---

## Reporting and Transparency Integration

HerHelp should integrate into FUZE reporting and transparency systems wherever HerHelp activity has platform relevance.

### HerHelp integration should support reporting for:

- product-attributed Platform Credits consumption
- HerHelp AI usage volume and class distribution
- premium versus included usage
- workspace versus account usage patterns
- HerHelp billing and entitlement error visibility
- correction or reversal patterns affecting HerHelp usage
- product-operational health and async backlog visibility
- automation and generation workload patterns where useful

### Principle

HerHelp does not need to expose every workspace-private business artifact in platform-level reporting, but it must emit enough structured signals that its economic and operational role in FUZE is legible.

Because HerHelp broadens FUZE into SME software, structured reporting is important both commercially and strategically.

---

## Audit Integration

HerHelp should generate meaningful audit visibility for trust-sensitive product actions.

### Examples include:

- premium generation execution linked to billable state
- support/admin correction involving HerHelp commercial or entitlement behavior
- workspace-scoped high-impact HerHelp configuration changes
- AI usage correction or reversal
- operator replay or cancellation of sensitive HerHelp async workflows
- publication or approval-gated workflow transitions where product policy requires

This does not mean every small edit inside a generated business app needs heavy audit logging. It means meaningful actions affecting money, access, generation state, workflow outcome, or sensitive shared business configuration should be explainable.

---

## Holder-Aware and Cross-Product Privilege Integration

HerHelp may participate in ecosystem-level holder-aware behavior as allowed by platform policy, but this should remain bounded and secondary to HerHelp’s SaaS and workspace logic.

### Possible uses include:

- holder-rank-informed early access
- ecosystem-level recognition or selected product privileges
- selected cross-product benefits

### Rules

- holder-aware logic must come from platform policy, not HerHelp-local interpretation
- token participation must not replace HerHelp subscriptions, Platform Credits, collaborator logic, or premium AI billing by default
- cross-product privileges must remain compatible with the distinction between participation and consumption

This allows HerHelp to remain part of the wider FUZE ecosystem without weakening the clarity of the product’s core SaaS business model.

---

## Support, Correction, and Recovery Integration

HerHelp must be able to participate in platform correction and support flows.

### Relevant HerHelp correction cases may include:

- wrongly charged premium AI generation action
- duplicate metered usage
- failed async generation after provisional reservation
- entitlement mismatch after renewal or credits funding issue
- workspace-scope misbilling
- stale or failed AI output requiring release or correction
- workflow duplication with commercial effect

### Rules

- commercial corrections affecting HerHelp must flow through shared refund/reversal/adjustment systems
- AI-related usage corrections must preserve lineage
- support compensation should be explicit rather than hidden mutation
- HerHelp must remain compatible with shared support, finance, and ops workflows

Because HerHelp may operate in high-frequency workspace contexts, strong correction compatibility is necessary.

---

## Product Launch and Expansion Readiness for HerHelp

HerHelp should satisfy the full FUZE product-integration readiness model.

At minimum, HerHelp should have:

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

Because HerHelp is one of the most important category-expansion products in FUZE, its readiness quality should demonstrate that the platform can support both Web3-native and broader SME SaaS products with equal architectural discipline.

---

## Edge Cases and Failure Handling

### User has HerHelp session but lost premium entitlement
HerHelp access should remain bound to current entitlement state rather than stale client assumptions.

### Workspace member triggers premium generation using wrong scope
The system must resolve and charge the correct scope explicitly rather than silently falling back.

### HerHelp AI task times out after reservation
Reservation release, retry, or recovery path must be explicit and auditable.

### Duplicate generation or publish request enters async flow
Idempotency and business-action lineage must prevent duplicate commercial or product effects.

### Holder-aware HerHelp feature is visible but user lacks required plan
Visibility does not imply full entitlement; product access must still respect platform-commercial rules.

### Spreadsheet or project context changes materially during async generation
HerHelp may need validation, refresh, or failure-safe handling rather than blindly finalizing stale output.

### Support grants compensation after HerHelp metering bug
This must appear as explicit platform compensation or adjustment rather than silent deletion of history.

### Business data source becomes unavailable during follow-up workflow
HerHelp must follow shared degraded-mode, retry, and escalation policy rather than improvising product-local behavior.

---

## Open Items

The following items are intentionally refined in downstream HerHelp product design and implementation:

- exact HerHelp plan tiers and pricing
- exact collaborator and seat structures
- exact page/form/template object schemas
- exact sync and import architecture
- exact premium AI feature boundaries
- exact automation and publish workflow cadence
- exact public/private visibility rules for HerHelp-generated artifacts

These do not weaken the canonical HerHelp integration model established here.

---

## Closing Summary

HerHelp is the AI-assisted business software product for SMEs in the FUZE ecosystem, and it should integrate as a fully platform-native product rather than as an isolated sheet-to-app tool. HerHelp owns its product-specific generation logic, workspace artifacts, templates, and business workflows, but it must rely on shared FUZE systems for identity, permissions, workspaces, Platform Credits, billing, AI orchestration, AI usage metering, workflow execution, async infrastructure, and transparency-compatible reporting. This architecture allows HerHelp to function as a commercially meaningful SME SaaS product while reinforcing the broader FUZE platform model rather than fragmenting it.
