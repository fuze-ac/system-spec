# BOTMAD_PRODUCT_INTEGRATION_SPEC

## Purpose

This document defines the canonical product integration architecture for **Botmad — AI desktop employee** within the FUZE platform ecosystem. Its purpose is to establish how Botmad integrates with shared platform layers, what Botmad owns as a product domain, how Botmad consumes shared platform capabilities without redefining them, and how Botmad should behave commercially, operationally, and architecturally inside the wider FUZE environment.

This specification is important because Botmad extends FUZE into AI-assisted work execution, desktop workflow support, and operational error-reduction. Botmad is not only another AI interface. It is intended to become a structured software environment in which routine work, scoped decision-making, workflow scanning, task assistance, and monitored execution can be augmented by AI in a controlled and measurable way. As a result, Botmad must integrate deeply with FUZE identity, workspaces, Platform Credits, AI orchestration, workflow execution, scheduled and queue-backed infrastructure, transparency discipline, and policy-aware control surfaces.

---

## Scope

This specification covers:

- the canonical role of Botmad inside the FUZE ecosystem
- the distinction between Botmad-owned domain logic and platform-owned shared systems
- Botmad integration with identity, account, workspace, permission, and commercial layers
- Botmad integration with Platform Credits, subscriptions, usage billing, and entitlements
- Botmad integration with AI orchestration, model routing, workflows, jobs, and scheduled tasks
- Botmad-specific product context, workflow context, operator context, and task-assistance logic
- Botmad reporting, audit, transparency, and operational control integration
- boundaries, edge cases, and failure-handling expectations for Botmad-platform interactions

This specification does not define desktop-agent implementation internals, OS-specific automation APIs, device-level permissions, or detailed front-end UX. It also does not redefine shared platform layers already specified in:

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

The design goals of the Botmad integration architecture are:

1. to make Botmad a platform-native AI work-execution product rather than a detached desktop assistant
2. to preserve Botmad’s domain-specific workflow-support logic while reusing FUZE shared infrastructure
3. to support workspace-centric and operator-centric usage patterns cleanly
4. to integrate Botmad into Platform Credits, subscriptions, AI usage, and action-based monetization
5. to ensure Botmad task-assistance and workflow-execution behavior remain auditable, commercially measurable, and operationally safe
6. to support async, scheduled, and AI-assisted Botmad behavior without product-local orchestration drift
7. to preserve clear boundaries between user work context, platform state, and product-generated execution artifacts
8. to make Botmad a strong reference model for future AI-assisted work products in the FUZE ecosystem

---

## Non-Goals

This specification is not intended to:

- define Botmad as the owner of shared identity, billing, credits, or AI execution infrastructure
- collapse Botmad-specific workflow-support logic into generic platform logic
- make Botmad an unrestricted autonomous agent with no policy, review, or permission boundaries
- allow Botmad to bypass shared AI usage metering or premium billing controls
- treat token ownership as a substitute for Botmad authorization or entitlement
- define all desktop integration surfaces or automation permissions in this file
- make Botmad responsible for ecosystem-wide governance outside its product domain

---

## Canonical Product Principle

The primary integration principle for Botmad is:

> Botmad owns AI-assisted work-execution product meaning, but it must consume shared FUZE platform systems for identity, billing, credits, AI orchestration, workflow execution, and transparency in a way that preserves one coherent platform architecture.

This means:

- Botmad defines what workflow scanning, scoped execution support, routine-task assistance, and error-reduction features do
- FUZE defines how shared identity, credits, billing, AI orchestration, and automation infrastructure behave
- Botmad may shape product-specific monetization and AI task intent, but not invent alternate platform truth
- Botmad usage and value capture should strengthen the shared platform rather than form a detached AI-worker silo

This principle makes Botmad both specialized and platform-native.

---

## Botmad Product Role in the FUZE Ecosystem

Botmad is the **AI-assisted work execution and desktop employee product** in the FUZE ecosystem.

Its purpose is to help users and teams reduce routine manual work, improve workflow consistency, detect process friction, and support scoped operational decision-making through AI-assisted execution and workflow-aware guidance. Botmad is not intended to be only a conversational assistant window. It is intended to become a product environment that can assist with structured work flows, analyze workflow steps, recommend improvements, support controlled actions, and reduce avoidable human error in recurring operational processes.

Botmad is strategically important because it expands FUZE beyond market intelligence, token infrastructure, and discovery into direct work assistance and operational productivity. This makes it relevant not only to Web3-native workflows but also to broader business and operational environments. It also provides a strong use case for combining:

- AI orchestration
- workflow automation
- action-based usage metering
- workspace collaboration
- approval gates
- and policy-bounded execution logic

Botmad should therefore be treated as a first-class FUZE product and a reference implementation for AI-assisted work products built on top of the platform.

---

## Botmad Domain Ownership

Botmad must own its domain-specific product meaning and product-specific data.

### Botmad-owned concerns include:

- Botmad workflow-scan definitions and results
- Botmad task-assistance objects and execution recommendations
- Botmad run records, task contexts, and product-level execution artifacts
- Botmad operator preferences and workspace workflow configurations
- Botmad domain-specific AI task intent
- Botmad validation of AI-generated or AI-assisted workflow outputs
- Botmad product-specific workflow outcomes
- Botmad reports, summaries, and workflow-improvement artifacts

### Botmad does not own:

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

Botmad may support work execution and workflow guidance, but it must not become a shadow platform with its own hidden identity, billing, AI, or control system.

---

## Identity and Account Integration

Botmad must integrate directly with the canonical FUZE identity and account layer.

### Botmad requirements

Botmad should rely on FUZE for:

- account registration and login continuity
- canonical user identity resolution
- linked-login compatibility
- session continuity across products
- account-level restriction or suspension interpretation
- account-level product history references where allowed

### Botmad rules

- Botmad must not create a separate user identity system for core product use
- Botmad must not treat local device or operator profile state as canonical account truth
- Botmad must interpret current session and account status through shared platform controls
- Botmad should preserve cross-product continuity so a FUZE user can move into Botmad without entering a separate identity environment

This helps Botmad behave as part of one ecosystem rather than as a detached desktop utility.

---

## Workspace and Organization Integration

Botmad is naturally a **workspace-oriented** product and should integrate strongly with the FUZE workspace and organization layer.

### Account-scoped Botmad usage may include:

- personal task assistance
- solo workflow scan usage
- personal premium AI work support
- individual automation experiments
- self-service workflow-improvement use cases

### Workspace-scoped Botmad usage may include:

- team workflow environments
- shared operational playbooks
- workspace-owned task templates and automation contexts
- operator roles and approval chains
- shared billing and credits funding
- role-based access to execution and review workflows
- company-owned Botmad workspace configurations

### Workspace integration principles

- Botmad must resolve whether an action is personal or workspace-scoped
- workspace-owned Botmad resources should belong to the workspace, not only to the initiating member
- workspace billing, credits usage, seat logic, and access control must remain explicit
- Botmad must not invent hidden collaborative models outside the shared workspace architecture for platform-relevant concerns

This is especially important because Botmad is likely to serve teams, operations units, and business environments rather than only solo users.

---

## Role and Permission Integration

Botmad must integrate with FUZE’s canonical role and permission system.

### Platform-level permissions relevant to Botmad may include:

- view Botmad access entitlement
- manage own Botmad settings
- access workspace Botmad resources
- consume workspace Botmad balances where authorized
- manage workspace Botmad workflow configurations where permitted
- approve or execute selected Botmad actions where product policy allows

### Botmad product-specific roles may include:

- Botmad viewer
- Botmad operator
- Botmad workflow reviewer
- Botmad workspace admin
- Botmad approver
- Botmad automation manager

These roles should extend, not replace, the platform authorization model.

### Rules

- token ownership must not grant Botmad operator authority by itself
- active session alone is not sufficient for workspace-scoped Botmad actions
- Botmad premium access does not bypass workspace permission rules
- Botmad must remain compatible with the shared access-control layer for audit and support clarity

Because Botmad may support AI-assisted work execution and approval-sensitive flows, permission discipline is especially important.

---

## Commercial Integration with Platform Credits

Botmad must integrate into the shared FUZE commercial system through Platform Credits and the shared billing architecture.

### Botmad monetization paths may include:

- workspace subscriptions
- operator or seat-based access
- AI-assisted workflow scan usage
- action-based premium assistance
- premium automation modules
- task-execution bundles
- advanced reporting or workflow-analysis features
- credits-backed premium execution flows

### Botmad commercial rules

Botmad may define:

- what counts as a premium Botmad action
- what features are included in a Botmad plan
- what usage is metered
- what actions require credits-backed settlement
- what workspace or operator packages exist

Botmad may not define:

- a private Botmad-only credits system
- direct product-local billing logic outside FUZE billing
- hidden AI or execution charges not visible to platform metering
- separate balance semantics that bypass Platform Credits

Because Botmad may combine recurring access with frequent action-based usage, strong shared commercial integration is especially important.

---

## Subscription and Usage Billing Integration

Botmad should use FUZE’s shared subscriptions and usage billing framework.

### Botmad subscription use cases may include:

- workspace work-assistance plan
- operator seat plan
- premium workflow-intelligence tier
- advanced automation tier
- pro operations-support plan

### Botmad usage-billing use cases may include:

- premium AI workflow analysis
- advanced run or execution-support actions
- workflow simulation or scan actions
- premium recommendation batches
- high-volume automation executions
- advanced report generation

### Hybrid model

Botmad may combine:

- recurring workspace access,
- included monthly workflow-assistance usage,
- operator or seat structures,
- and premium overage or credits-backed usage beyond baseline limits.

### Billing principles

- Botmad charges should be attributable to product and feature
- Botmad usage should map into platform usage and invoice/receipt structures where relevant
- workspace and account billing context must remain explicit
- Botmad should support visible included-usage and premium-usage semantics rather than hidden backend-only limits

This allows Botmad to function as a serious AI-assisted work product while preserving one coherent FUZE internal economy.

---

## Entitlement Integration

Botmad product access must be derived from platform-commercial and authorization state rather than from UI assumptions or raw provider-side events.

### Botmad entitlements may include:

- baseline product access
- operator or seat access
- AI workflow analysis availability
- premium automation modules
- advanced execution-support features
- workspace-shared task environments
- advanced reporting or review capabilities

### Rules

- entitlement should be evaluated from normalized platform state
- credits availability does not automatically equal Botmad product entitlement
- token ownership does not automatically equal Botmad entitlement
- plan status, workspace scope, role, and product access must remain distinct but coordinated

This matters because Botmad combines recurring SaaS value with potentially frequent, high-variance AI and workflow usage.

---

## AI Orchestration Integration

Botmad is expected to rely heavily on FUZE’s shared AI orchestration layer.

### Botmad AI task examples may include:

- workflow-scan analysis
- task-step explanation
- process-improvement recommendation
- exception-case suggestion
- structured task decomposition
- execution-support summary
- run review and recap
- scoped decision-support outputs

### Botmad owns:

- the domain meaning of these tasks
- product-specific output schemas
- acceptance criteria for AI-assisted outputs
- Botmad-specific validation of AI-assisted results

### Shared AI platform owns:

- routing and model selection
- context assembly policy
- provider abstraction
- usage metering hooks
- fallback and degraded-mode execution support
- async execution scaffolding where needed

### Rule

Botmad must not create hidden AI execution pathways for core product behavior that bypass shared orchestration and shared AI commercial visibility.

Because Botmad may become one of the most workflow-intensive AI products in the ecosystem, this integration discipline is especially important.

---

## Model Routing and Context Requirements for Botmad

Botmad should supply strong task intent while relying on the shared routing and context framework.

### Botmad context may need to include:

- account or workspace scope
- operator or reviewer role context
- Botmad workflow state
- task or run context
- approval state where applicable
- entitlement tier
- product-specific output format requirements
- workflow history or prior-step context where applicable

### Context rules

- Botmad should use least-context discipline
- irrelevant workspace or user data should not be included merely because it is available
- structured-output requirements should be explicit where results drive workflow progression or review
- premium or high-value routes should remain policy-aware and commercially classified
- sensitive work-context data should be bounded carefully in context assembly and downstream storage

Because Botmad may interact with operational processes and human work patterns, context precision and control are especially important.

---

## AI Usage Metering for Botmad

Botmad must integrate fully with FUZE’s AI usage metering layer.

### Botmad metered AI categories may include:

- standard workflow analysis
- premium reasoning-heavy process recommendation
- advanced run review
- task decomposition or structured guidance
- workflow simulation support
- included quota usage under a subscription
- overage usage after included limits

### Metering requirements

- every meaningful AI action should generate a Botmad-attributed AI usage record
- metering should distinguish included, premium, overage, released, failed, and corrected usage
- workspace versus account usage must remain explicit
- Botmad reporting should be able to show usage by feature or task class where useful

Because Botmad may generate high-frequency AI activity in operational contexts, metering integrity is central to both cost control and monetization clarity.

---

## Workflow and Automation Integration

Botmad should integrate with the shared workflow and automation layer for multi-step, event-driven, or approval-sensitive behaviors.

### Relevant Botmad workflows may include:

- workflow scan request -> analyze -> recommend -> review -> apply or skip
- premium run request -> entitlement check -> AI execution -> result validation -> settlement
- automation definition -> validate -> schedule -> execute -> summarize
- task-support flow -> context load -> AI assist -> user confirm -> action log
- exception detection -> trigger -> summarize -> route for review
- correction workflow after invalid charge or failed async result

### Workflow rules

- Botmad workflow state must not replace canonical Botmad product truth
- Botmad must use platform workflow semantics for retries, delays, approvals, and completion states
- high-cost or async Botmad workflows should support reservation/release behavior where appropriate
- product-specific execution-support logic must remain legible through shared orchestration records
- approval gates should be supported where actions are sensitive, high-impact, or operator-confirmed

This matters because Botmad is likely to evolve from suggestion support into broader AI-assisted workflow execution and monitoring.

---

## Queue, Worker, and Scheduled Task Integration

Botmad must use the shared queue, worker, and scheduled-task substrate for deferred execution.

### Likely Botmad async workloads include:

- heavy workflow-scan jobs
- batch run analysis
- delayed recommendation generation
- scheduled automation executions
- notification fanout
- retryable integration follow-up work
- periodic cleanup and reconciliation for premium usage flows
- scheduled workflow-health checks or reporting jobs

### Principles

- Botmad should not create hidden async engines for core product behavior
- Botmad job classes should map into shared operational queues
- retries must remain idempotent and commercially safe
- scheduled Botmad tasks should follow shared retry policy classes rather than product-local timing logic
- high-frequency workflow tasks should remain observable and bounded by shared platform operations discipline

Because Botmad may support ongoing execution support rather than one-off requests only, disciplined async integration is especially important.

---

## Product Data Ownership for Botmad

Botmad owns its domain-specific product data and generated workflow artifacts.

### Botmad-owned data may include:

- workflow-scan results
- task templates and task-support definitions
- run records
- recommendation artifacts
- execution summaries
- operator annotations
- workflow configurations
- product-specific settings and review states

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

Derived read models may combine Botmad and platform data for dashboards or reporting, but they must not change canonical ownership.

---

## Reporting and Transparency Integration

Botmad should integrate into FUZE reporting and transparency systems wherever Botmad activity has platform relevance.

### Botmad integration should support reporting for:

- product-attributed Platform Credits consumption
- Botmad AI usage volume and class distribution
- premium versus included usage
- workspace versus account usage patterns
- Botmad billing and entitlement error visibility
- correction or reversal patterns affecting Botmad usage
- product-operational health and async backlog visibility
- workflow-assistance and automation workload patterns where useful

### Principle

Botmad does not need to expose every workspace-private operational artifact in platform-level reporting, but it must emit enough structured signals that its economic and operational role in FUZE is legible.

Because Botmad broadens FUZE into AI-assisted work execution, structured reporting is important both commercially and strategically.

---

## Audit Integration

Botmad should generate meaningful audit visibility for trust-sensitive product actions.

### Examples include:

- premium workflow analysis execution linked to billable state
- support/admin correction involving Botmad commercial or entitlement behavior
- workspace-scoped high-impact Botmad workflow configuration changes
- AI usage correction or reversal
- operator replay or cancellation of sensitive Botmad async workflows
- approval-gated workflow transitions where product policy requires

This does not mean every small advisory response needs heavy audit logging. It means meaningful actions affecting money, access, execution-support state, workflow outcome, or sensitive shared configuration should be explainable.

---

## Holder-Aware and Cross-Product Privilege Integration

Botmad may participate in ecosystem-level holder-aware behavior as allowed by platform policy, but this should remain bounded and secondary to Botmad’s SaaS and workspace logic.

### Possible uses include:

- holder-rank-informed early access
- ecosystem-level recognition or selected product privileges
- selected cross-product benefits

### Rules

- holder-aware logic must come from platform policy, not Botmad-local interpretation
- token participation must not replace Botmad subscriptions, Platform Credits, operator logic, or premium AI billing by default
- cross-product privileges must remain compatible with the distinction between participation and consumption

This allows Botmad to remain part of the wider FUZE ecosystem without weakening the clarity of the product’s core SaaS and workflow business model.

---

## Support, Correction, and Recovery Integration

Botmad must be able to participate in platform correction and support flows.

### Relevant Botmad correction cases may include:

- wrongly charged premium AI workflow action
- duplicate metered usage
- failed async analysis after provisional reservation
- entitlement mismatch after renewal or credits funding issue
- workspace-scope misbilling
- stale or failed AI output requiring release or correction
- workflow duplication with commercial effect

### Rules

- commercial corrections affecting Botmad must flow through shared refund/reversal/adjustment systems
- AI-related usage corrections must preserve lineage
- support compensation should be explicit rather than hidden mutation
- Botmad must remain compatible with shared support, finance, and ops workflows

Because Botmad may operate in high-frequency workspace contexts, strong correction compatibility is necessary.

---

## Product Launch and Expansion Readiness for Botmad

Botmad should satisfy the full FUZE product-integration readiness model.

At minimum, Botmad should have:

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

Because Botmad is one of the most important AI work-expansion products in FUZE, its readiness quality should demonstrate that the platform can support AI-assisted operational software with the same discipline it applies to intelligence, infrastructure, and SME products.

---

## Edge Cases and Failure Handling

### User has Botmad session but lost premium entitlement
Botmad access should remain bound to current entitlement state rather than stale client assumptions.

### Workspace member triggers premium workflow action using wrong scope
The system must resolve and charge the correct scope explicitly rather than silently falling back.

### Botmad AI task times out after reservation
Reservation release, retry, or recovery path must be explicit and auditable.

### Duplicate workflow scan or execution-support request enters async flow
Idempotency and business-action lineage must prevent duplicate commercial or product effects.

### Holder-aware Botmad feature is visible but user lacks required plan
Visibility does not imply full entitlement; product access must still respect platform-commercial rules.

### Workflow context changes materially during async analysis
Botmad may need validation, refresh, or failure-safe handling rather than blindly finalizing stale output.

### Support grants compensation after Botmad metering bug
This must appear as explicit platform compensation or adjustment rather than silent deletion of history.

### External workflow environment becomes unavailable during follow-up task
Botmad must follow shared degraded-mode, retry, and escalation policy rather than improvising product-local behavior.

---

## Open Items

The following items are intentionally refined in downstream Botmad product design and implementation:

- exact Botmad plan tiers and pricing
- exact operator and seat structures
- exact workflow and run object schemas
- exact desktop/workstation integration architecture
- exact premium AI feature boundaries
- exact automation and execution cadence
- exact public/private visibility rules for Botmad-generated artifacts

These do not weaken the canonical Botmad integration model established here.

---

## Closing Summary

Botmad is the AI-assisted work execution and desktop employee product of the FUZE ecosystem, and it should integrate as a fully platform-native product rather than as an isolated assistant tool. Botmad owns its product-specific workflow-support logic, run artifacts, recommendations, and execution-related workflows, but it must rely on shared FUZE systems for identity, permissions, workspaces, Platform Credits, billing, AI orchestration, AI usage metering, workflow execution, async infrastructure, and transparency-compatible reporting. This architecture allows Botmad to function as a commercially meaningful AI work product while reinforcing the broader FUZE platform model rather than fragmenting it.
