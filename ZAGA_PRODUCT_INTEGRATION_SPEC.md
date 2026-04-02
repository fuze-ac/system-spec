# ZAGA_PRODUCT_INTEGRATION_SPEC

## Purpose

This document defines the canonical product integration architecture for **ZAGA — Token Utility OS** within the FUZE platform ecosystem. Its purpose is to establish how ZAGA integrates with shared platform layers, what ZAGA owns as a product domain, how ZAGA consumes shared platform capabilities without redefining them, and how ZAGA should behave commercially, operationally, and architecturally inside the wider FUZE environment.

This specification is important because ZAGA is one of the clearest products through which FUZE extends beyond intelligence and operations into token-system infrastructure. ZAGA is intended to serve projects, ecosystems, and operators that need a more structured way to design, manage, and evolve token utility, participation logic, and programmatic utility systems over time. As a result, ZAGA must integrate deeply with FUZE identity, wallet-aware context, Platform Credits, AI orchestration, workflow execution, transparency discipline, and governance-aware controls.

---

## Scope

This specification covers:

- the canonical role of ZAGA inside the FUZE ecosystem
- the distinction between ZAGA-owned domain logic and platform-owned shared systems
- ZAGA integration with identity, account, workspace, permission, and wallet-aware layers
- ZAGA integration with Platform Credits, subscriptions, usage billing, and entitlements
- ZAGA integration with AI orchestration, model routing, workflows, jobs, and scheduled tasks
- ZAGA-specific product context, utility-program context, and holder-aware participation use
- ZAGA reporting, audit, transparency, and operational control integration
- boundaries, edge cases, and failure-handling expectations for ZAGA-platform interactions

This specification does not define detailed token-utility frameworks for every client ecosystem, chain-specific execution logic for external token projects, or front-end UX design. It also does not redefine shared platform layers already specified in:

- `PRODUCT_INTEGRATION_ARCHITECTURE_SPEC.md`
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `WALLET_AWARE_USER_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `AI_ORCHESTRATION_SPEC.md`
- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `AI_USAGE_METERING_SPEC.md`

---

## Design Goals

The design goals of the ZAGA integration architecture are:

1. to make ZAGA a platform-native token-utility infrastructure product rather than a detached utility tool
2. to preserve ZAGA’s domain-specific utility-system logic while reusing FUZE shared infrastructure
3. to support workspace-centric and project-centric ZAGA usage patterns cleanly
4. to integrate ZAGA into Platform Credits, premium utility tooling, AI usage, and reporting
5. to ensure ZAGA workflows remain auditable, commercially measurable, and governance-aware
6. to support async, scheduled, and workflow-driven ZAGA behavior without product-local orchestration drift
7. to preserve clear boundaries between product state, platform state, external-ecosystem state, and governance-sensitive operations
8. to make ZAGA a strong reference model for future infrastructure-style products in the FUZE ecosystem

---

## Non-Goals

This specification is not intended to:

- define ZAGA as the owner of shared identity, billing, credits, or AI execution infrastructure
- collapse ZAGA-specific utility-system logic into generic platform logic
- make ZAGA the governance authority of external token ecosystems by default
- allow ZAGA to bypass shared AI usage metering or premium billing controls
- treat token ownership as a substitute for ZAGA authorization or entitlement
- define every external token-utility implementation pattern in this file
- make ZAGA responsible for FUZE ecosystem-wide governance outside its own product domain

---

## Canonical Product Principle

The primary integration principle for ZAGA is:

> ZAGA owns token-utility operating-system product meaning, but it must consume shared FUZE platform systems for identity, billing, credits, AI orchestration, workflow execution, wallet-aware context, and transparency in a way that preserves one coherent platform architecture.

This means:

- ZAGA defines what utility-system products, modules, and workflows do
- FUZE defines how shared identity, credits, billing, AI orchestration, and automation infrastructure behave
- ZAGA may shape product-specific monetization and AI task intent, but not invent alternate platform truth
- ZAGA usage and value capture should strengthen the shared platform rather than form a detached infrastructure silo

This principle makes ZAGA both specialized and platform-native.

---

## ZAGA Product Role in the FUZE Ecosystem

ZAGA is the **token utility infrastructure and token-operations operating-system product** in the FUZE ecosystem.

Its purpose is to help projects and operators create, manage, and scale token utility systems with more structure and less improvisation. Rather than treating token utility as a scattered collection of one-off mechanisms, ZAGA is intended to provide a product environment for utility design, participation programs, campaign logic, holder pathways, engagement frameworks, operational tooling, and related token-system workflows.

Strategically, ZAGA is important because token ecosystems often suffer from weak utility structure after launch. They may have narrative, community, and token distribution, but lack an operating layer for utility evolution, holder engagement systems, incentive design, or programmatic execution. ZAGA is intended to address that gap as a reusable product and category.

ZAGA is therefore a strong fit for FUZE because it:

- extends the platform into token infrastructure software
- aligns with the ecosystem’s Web3-native strengths
- benefits directly from wallet-aware identity and holder-aware context
- creates natural use cases for AI-supported analysis, recommendation, and workflow design
- reinforces the platform thesis that structured shared infrastructure is more valuable than isolated product silos

ZAGA should be treated as a first-class FUZE product and a reference implementation for infrastructure-style platform products.

---

## ZAGA Domain Ownership

ZAGA must own its domain-specific product meaning and product-specific data.

### ZAGA-owned concerns include:

- ZAGA utility program definitions
- ZAGA ecosystem/project configuration objects
- ZAGA campaign, module, and utility-flow records
- ZAGA participation-program logic and product-level state
- ZAGA domain-specific AI task intent
- ZAGA validation of AI-generated or AI-assisted utility outputs
- ZAGA product-specific workflow outcomes
- ZAGA reports, recommendations, utility maps, and participation artifacts

### ZAGA does not own:

- canonical user identity
- canonical workspace membership truth
- canonical role and permission framework
- canonical payment normalization
- Platform Credits meaning
- canonical subscriptions and invoices
- shared AI model-routing policy
- shared workflow infrastructure
- shared audit or transparency record architecture
- governance authority over the FUZE ecosystem outside its product boundary

### Boundary principle

ZAGA may support governance-aware and utility-aware product behavior, but it must not become a shadow platform or shadow governance system inside FUZE.

---

## Identity and Account Integration

ZAGA must integrate directly with the canonical FUZE identity and account layer.

### ZAGA requirements

ZAGA should rely on FUZE for:

- account registration and login continuity
- canonical user identity resolution
- linked-login compatibility
- session continuity across products
- account-level restriction or suspension interpretation
- account-level product history references where allowed

### ZAGA rules

- ZAGA must not create a separate user identity system for core product use
- ZAGA must not treat local project profile state as canonical account truth
- ZAGA must interpret current session and account status through shared platform controls
- ZAGA should preserve cross-product continuity so a FUZE user can move into ZAGA without entering a separate identity environment

This helps ZAGA behave as part of one ecosystem rather than as a detached token-tooling application.

---

## Workspace and Organization Integration

ZAGA is naturally a **workspace-oriented and project-oriented** product and should integrate strongly with the FUZE workspace and organization layer.

### Account-scoped ZAGA usage may include:

- personal utility-system exploration
- individual program design experiments
- personal prototype or sandbox usage
- self-service premium utility analysis

### Workspace-scoped ZAGA usage may include:

- project or token-team workspaces
- shared utility program environments
- multi-operator configuration workflows
- organization-level billing and credits funding
- workspace-owned campaigns, reports, and participation logic
- role-based access to configuration and review workflows
- shared premium utility tooling

### Workspace integration principles

- ZAGA must resolve whether an action is personal or workspace-scoped
- workspace-owned ZAGA resources should belong to the workspace, not only to the initiating member
- workspace billing, credits usage, and operator access must remain explicit
- ZAGA must not invent hidden collaborative models outside the shared workspace architecture for platform-relevant concerns

This is particularly important because ZAGA is likely to be used by token projects, operating teams, and collaborative ecosystems rather than only by solo users.

---

## Role and Permission Integration

ZAGA must integrate with FUZE’s canonical role and permission system.

### Platform-level permissions relevant to ZAGA may include:

- view ZAGA access entitlement
- manage own ZAGA settings
- access workspace ZAGA resources
- consume workspace ZAGA balances where authorized
- manage workspace ZAGA configurations where permitted
- approve or publish selected utility workflows where product policy allows

### ZAGA product-specific roles may include:

- ZAGA viewer
- ZAGA operator
- ZAGA utility designer
- ZAGA campaign manager
- ZAGA workspace admin
- ZAGA report reviewer

These roles should extend, not replace, the platform authorization model.

### Rules

- token ownership must not grant ZAGA operator authority by itself
- active session alone is not sufficient for workspace-scoped ZAGA actions
- ZAGA premium access does not bypass workspace permission rules
- ZAGA must remain compatible with the shared access-control layer for audit and support clarity

Because ZAGA may influence project utility structures and token-facing programs, permission discipline is especially important.

---

## Wallet-Aware Integration

ZAGA should integrate deeply with FUZE’s wallet-aware user layer because wallet-linked context is often directly relevant to utility-system design and participation logic.

### Appropriate wallet-aware uses for ZAGA may include:

- wallet-linked participant recognition
- holder-aware rule design context
- rank-aware utility pathways
- wallet-linked eligibility previews or participation states
- ecosystem-level holder privilege reflection where policy allows

### Inappropriate wallet-aware uses include:

- replacing platform account identity with wallet identity
- granting ZAGA workspace authority purely from wallet ownership
- bypassing billing or permissions because a wallet is linked
- using product-local wallet registries as canonical user-to-wallet truth
- redefining holder meaning independently from shared platform policy

### Principle

Wallet-aware context is strategically important to ZAGA, but it must remain integrated through the canonical wallet-aware user model. ZAGA may be one of the most wallet-sensitive products in the ecosystem, which makes strict boundary discipline even more important.

---

## Commercial Integration with Platform Credits

ZAGA must integrate into the shared FUZE commercial system through Platform Credits and the shared billing architecture.

### ZAGA monetization paths may include:

- workspace subscriptions
- premium utility-design modules
- credits-backed program or campaign actions
- premium AI-assisted utility analysis
- premium reporting or simulation tools
- add-on modules for advanced utility workflows
- operator or seat-based access where relevant
- project-level infrastructure plans

### ZAGA commercial rules

ZAGA may define:

- what counts as a premium ZAGA action
- what features are included in a ZAGA plan
- what usage is metered
- what actions require credits-backed settlement
- what workspace or project packages exist

ZAGA may not define:

- a private ZAGA-only credits system
- direct product-local billing logic outside FUZE billing
- hidden utility-tooling charges not visible to platform metering
- separate internal balance semantics that bypass Platform Credits

Because ZAGA is intended to be infrastructure software, strong commercial integration is important to keep the platform economy coherent.

---

## Subscription and Usage Billing Integration

ZAGA should use FUZE’s shared subscriptions and usage billing framework.

### ZAGA subscription use cases may include:

- workspace utility operating-system plan
- pro utility design tier
- project-level infrastructure subscription
- premium campaign tooling tier
- advanced reporting or analytics tier

### ZAGA usage-billing use cases may include:

- premium AI-assisted utility analysis
- campaign or simulation runs
- advanced report generation
- large-context utility recommendation tasks
- premium configuration or workflow actions
- specialized export or rule-evaluation operations

### Hybrid model

ZAGA may combine:

- recurring workspace access,
- included monthly utility-system usage,
- operator or project-based structures,
- and premium overage or credits-backed usage beyond baseline limits.

### Billing principles

- ZAGA charges should be attributable to product and feature
- ZAGA usage should map into platform usage and invoice/receipt structures where relevant
- workspace and account billing context must remain explicit
- ZAGA should support visible included-usage and premium-usage semantics rather than hidden backend-only limits

This allows ZAGA to behave like serious infrastructure software without fragmenting the wider FUZE economy.

---

## Entitlement Integration

ZAGA product access must be derived from platform-commercial and authorization state rather than from UI assumptions or raw provider-side events.

### ZAGA entitlements may include:

- baseline product access
- premium utility module access
- advanced AI route availability
- shared workspace project access
- premium campaign or simulation access
- advanced report generation
- project/operator-specific tooling access

### Rules

- entitlement should be evaluated from normalized platform state
- credits availability does not automatically equal ZAGA product entitlement
- token ownership does not automatically equal ZAGA entitlement
- plan status, workspace scope, role, and product access must remain distinct but coordinated

This matters because ZAGA sits close to both project operations and token-aware participation logic, where entitlement confusion would be especially damaging.

---

## AI Orchestration Integration

ZAGA is expected to rely significantly on FUZE’s shared AI orchestration layer, especially for recommendation, structure design, summarization, and analysis support.

### ZAGA AI task examples may include:

- utility-system recommendation
- participation-program analysis
- campaign-structure generation
- holder-pathway explanation
- utility-gap analysis
- configuration summarization
- structured rule proposal drafting
- report synthesis for project operators

### ZAGA owns:

- the domain meaning of these tasks
- product-specific output schemas
- acceptance criteria for AI-assisted outputs
- ZAGA-specific validation of AI-assisted results

### Shared AI platform owns:

- routing and model selection
- context assembly policy
- provider abstraction
- usage metering hooks
- fallback and degraded-mode execution support
- async execution scaffolding where needed

### Rule

ZAGA must not create hidden AI execution pathways for core product behavior that bypass shared orchestration and shared AI commercial visibility.

Because ZAGA may use AI in project-facing and wallet-aware contexts, this integration discipline is especially important.

---

## Model Routing and Context Requirements for ZAGA

ZAGA should supply strong task intent while relying on the shared routing and context framework.

### ZAGA context may need to include:

- account or workspace scope
- operator role context
- ZAGA product state
- project or utility configuration context
- wallet-aware or holder-aware context where relevant and allowed
- entitlement tier
- product-specific output format requirements
- workflow or campaign context where applicable

### Context rules

- ZAGA should use least-context discipline
- irrelevant user, workspace, or project data should not be included merely because it is available
- structured-output requirements should be explicit where results drive workflow or project-state updates
- wallet-aware context should be bounded to what is needed for the task
- premium or high-value routes should remain policy-aware and commercially classified

Because ZAGA may deal with program design and token-utility structure, context precision and boundary control are especially important.

---

## AI Usage Metering for ZAGA

ZAGA must integrate fully with FUZE’s AI usage metering layer.

### ZAGA metered AI categories may include:

- standard utility recommendation
- premium reasoning-heavy utility analysis
- advanced report generation
- campaign-structure synthesis
- project-configuration interpretation
- included quota usage under a subscription
- overage usage after included limits

### Metering requirements

- every meaningful AI action should generate a ZAGA-attributed AI usage record
- metering should distinguish included, premium, overage, released, failed, and corrected usage
- workspace versus account usage must remain explicit
- ZAGA reporting should be able to show usage by feature or task class where useful

Because ZAGA may deliver high-value recommendation and configuration assistance, metering integrity is central to its monetization and operational clarity.

---

## Workflow and Automation Integration

ZAGA should integrate with the shared workflow and automation layer for multi-step, event-driven, or operator-reviewed behaviors.

### Relevant ZAGA workflows may include:

- utility configuration request -> validate -> analyze -> recommend -> operator review
- premium report request -> reservation -> async generation -> delivery -> final spend
- campaign setup -> structured generation -> validation -> publish or revise
- holder-pathway analysis -> summarize -> export or act
- scheduled project-state review -> insight refresh -> alert or update
- correction workflow after invalid charge or failed async result

### Workflow rules

- ZAGA workflow state must not replace canonical ZAGA product truth
- ZAGA must use platform workflow semantics for retries, delays, approvals, and completion states
- high-cost or async ZAGA workflows should support reservation/release behavior where appropriate
- product-specific utility logic must remain legible through shared orchestration records
- operator approval gates should be supported where actions are sensitive or configuration-affecting

This matters because ZAGA is likely to be workflow-rich even when user-facing interactions appear simple.

---

## Queue, Worker, and Scheduled Task Integration

ZAGA must use the shared queue, worker, and scheduled-task substrate for deferred execution.

### Likely ZAGA async workloads include:

- heavy AI utility-analysis jobs
- scheduled utility-state scans
- batched campaign/report generation
- delayed export generation
- notification fanout
- retryable provider or data follow-up work
- periodic cleanup and reconciliation for premium usage flows
- scheduled project review or refresh tasks

### Principles

- ZAGA should not create hidden async engines for core product behavior
- ZAGA job classes should map into shared operational queues
- retries must remain idempotent and commercially safe
- scheduled ZAGA tasks should follow shared retry policy classes rather than product-local timing logic
- sensitive or commercially impactful jobs may require stricter escalation and review paths

Because ZAGA may operate across project cycles and repeated program reviews, disciplined scheduled-task integration is especially important.

---

## Product Data Ownership for ZAGA

ZAGA owns its domain-specific product data and derived utility artifacts.

### ZAGA-owned data may include:

- utility program definitions
- campaign records
- project configuration records
- participation rule objects
- report artifacts
- operator annotations
- workflow outputs
- domain-specific recommendation objects

### Platform-owned related data includes:

- accounts
- workspace membership
- permissions
- wallet-link records
- credits ledger effects
- AI usage records
- invoices and receipts
- shared audit logs
- shared workflow/job metadata

### Rule

Derived read models may combine ZAGA and platform data for dashboards or reporting, but they must not change canonical ownership.

---

## Reporting and Transparency Integration

ZAGA should integrate into FUZE reporting and transparency systems wherever ZAGA activity has platform relevance.

### ZAGA integration should support reporting for:

- product-attributed Platform Credits consumption
- ZAGA AI usage volume and class distribution
- premium versus included usage
- workspace versus account usage patterns
- ZAGA billing and entitlement error visibility
- correction or reversal patterns affecting ZAGA usage
- product-operational health and async backlog visibility
- utility-workflow volume and failure patterns where useful

### Principle

ZAGA does not need to expose every project-private detail in platform-level reporting, but it must emit enough structured signals that its economic and operational role in FUZE is legible.

Because ZAGA is an infrastructure-style product and closely related to the platform’s Web3-native identity, structured reporting and transparency are especially important.

---

## Audit Integration

ZAGA should generate meaningful audit visibility for trust-sensitive product actions.

### Examples include:

- premium utility analysis execution linked to billable state
- support/admin correction involving ZAGA commercial or entitlement behavior
- workspace-scoped high-impact ZAGA configuration changes
- AI usage correction or reversal
- operator replay or cancellation of sensitive ZAGA async workflows
- approval-gated publication or configuration transitions where product policy requires
- holder-aware privilege changes affecting ZAGA if introduced

This does not mean every simple ZAGA read action needs heavy audit logging. It means meaningful actions affecting money, access, workflow outcome, or sensitive product state should be explainable.

---

## Holder-Aware and Cross-Product Privilege Integration

ZAGA may participate in ecosystem-level holder-aware behavior as allowed by platform policy.

### Possible uses include:

- holder-rank-informed feature visibility
- ecosystem-level early-access flags
- selected cross-product privileges
- token-aware recognition in ZAGA surfaces
- holder-aware participation pathways where policy allows

### Rules

- holder-aware logic must come from platform policy, not ZAGA-local interpretation
- token participation must not replace ZAGA subscriptions, Platform Credits, or premium utility billing by default
- cross-product privileges must remain compatible with the distinction between participation and consumption

This allows ZAGA to feel integrated into the wider FUZE ecosystem without weakening the clarity of token versus credits.

---

## Support, Correction, and Recovery Integration

ZAGA must be able to participate in platform correction and support flows.

### Relevant ZAGA correction cases may include:

- wrongly charged premium AI utility action
- duplicate metered usage
- failed async report after provisional reservation
- entitlement mismatch after renewal or credits funding issue
- workspace-scope misbilling
- stale or failed AI output requiring release or correction
- configuration workflow duplication with commercial effect

### Rules

- commercial corrections affecting ZAGA must flow through shared refund/reversal/adjustment systems
- AI-related usage corrections must preserve lineage
- support compensation should be explicit rather than hidden mutation
- ZAGA must remain compatible with shared support, finance, and ops workflows

Because ZAGA may combine product value with trust-sensitive project configuration workflows, strong correction compatibility is necessary.

---

## Product Launch and Expansion Readiness for ZAGA

ZAGA should satisfy the full FUZE product-integration readiness model.

At minimum, ZAGA should have:

1. canonical product registration
2. account and session integration
3. workspace integration
4. permission and entitlement alignment
5. wallet-aware integration compatibility
6. Platform Credits and billing integration
7. AI orchestration and metering integration
8. workflow, queue, and scheduled-task alignment
9. reporting and audit emission compatibility
10. correction and support compatibility
11. observability compatibility with shared platform operations

Because ZAGA is strategically important to FUZE’s infrastructure thesis, its readiness quality should set a high standard for future ecosystem infrastructure products.

---

## Edge Cases and Failure Handling

### User has ZAGA session but lost premium entitlement
ZAGA access should remain bound to current entitlement state rather than stale client assumptions.

### Workspace operator triggers premium analysis using wrong scope
The system must resolve and charge the correct scope explicitly rather than silently falling back.

### ZAGA AI task times out after reservation
Reservation release, retry, or recovery path must be explicit and auditable.

### Duplicate campaign or report request enters async flow
Idempotency and business-action lineage must prevent duplicate commercial or product effects.

### Holder-aware ZAGA feature is visible but user lacks required plan
Visibility does not imply full entitlement; product access must still respect platform-commercial rules.

### Utility or project context changes materially during async job
ZAGA may need validation, refresh, or failure-safe handling rather than blindly finalizing stale analysis.

### Support grants compensation after ZAGA metering bug
This must appear as explicit platform compensation or adjustment rather than silent deletion of history.

### Wallet-aware context changes after task creation
Sensitive ZAGA tasks may require revalidation or policy-aware context version handling before final mutation.

---

## Open Items

The following items are intentionally refined in downstream ZAGA product design and implementation:

- exact ZAGA plan tiers and pricing
- exact project and utility object schemas
- exact operator role taxonomy
- exact premium AI feature boundaries
- exact campaign, refresh, and report cadence
- exact public/private visibility rules for ZAGA-generated artifacts
- exact external project integration model where relevant

These do not weaken the canonical ZAGA integration model established here.

---

## Closing Summary

ZAGA is the token utility infrastructure and token-operations operating-system product of the FUZE ecosystem, and it should integrate as a fully platform-native product rather than as an isolated token tool. ZAGA owns its product-specific utility logic, program definitions, campaign workflows, and domain artifacts, but it must rely on shared FUZE systems for identity, permissions, workspaces, wallet-aware user context, Platform Credits, billing, AI orchestration, AI usage metering, workflow execution, async infrastructure, and transparency-compatible reporting. This architecture allows ZAGA to function as a commercially meaningful, infrastructure-oriented flagship product while reinforcing the broader FUZE platform model rather than fragmenting it.
