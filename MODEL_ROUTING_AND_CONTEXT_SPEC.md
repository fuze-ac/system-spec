# MODEL_ROUTING_AND_CONTEXT_SPEC

## Purpose

This document defines the canonical model routing and context architecture of the FUZE platform. Its purpose is to establish how AI tasks are matched to appropriate model classes and providers, how execution context is assembled and constrained, how platform and product instructions are layered, and how routing decisions remain commercially measurable, policy-governed, and operationally observable across the FUZE ecosystem.

This specification is essential because FUZE is a multi-product AI-powered platform. AI quality, cost discipline, reliability, and trust do not depend only on which provider is available. They also depend on whether the platform can route the right task to the right execution path with the right context, under the right policy. Without a shared routing and context model, products would drift into inconsistent AI behavior, inconsistent cost profiles, and inconsistent data-access discipline.

---

## Scope

This specification covers:

- the canonical role of model routing within the FUZE AI architecture
- the distinction between task intent and provider/model selection
- context-envelope structure for AI execution
- layered context assembly across account, workspace, product, workflow, wallet-aware, and policy domains
- prompt and instruction layering
- routing classes and routing decision factors
- fallback, downgrade, and degraded-mode routing behavior
- structured-output and tool-usage considerations in routing
- privacy, permission, and least-context principles
- observability, audit, and failure handling for routing and context assembly

This specification does not define full provider pricing tables, exact prompt templates for every product, or every workflow-specific AI task definition. Those are refined in:

- `AI_ORCHESTRATION_SPEC.md`
- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `JOB_QUEUE_AND_WORKER_SPEC.md`
- `AI_USAGE_METERING_SPEC.md`
- `PRODUCT_INTEGRATION_ARCHITECTURE_SPEC.md`

---

## Design Goals

The design goals of the FUZE model routing and context layer are:

1. to route AI tasks through one shared platform decision model rather than scattered product-local provider logic
2. to ensure AI tasks receive sufficient and correctly scoped context without excessive data exposure
3. to support many products, task types, and commercial tiers with one coherent routing framework
4. to improve reliability through fallback, downgrade, and degraded-mode behavior
5. to preserve cost visibility and commercial attribution for AI execution
6. to make routing and context decisions auditable and explainable enough for platform operations
7. to support structured outputs, tool use, and workflow-linked AI execution without weakening domain ownership
8. to keep model choice platform-governed even when product needs influence the decision

---

## Non-Goals

This specification is not intended to:

- hard-code one provider or one model as universally correct for all FUZE tasks
- let products choose arbitrary providers outside shared routing control
- maximize context volume at the expense of privacy, performance, or policy clarity
- treat model routing as only a cost-optimization problem
- define every product-specific prompt in this file
- make AI output authoritative merely because a larger or more expensive model was selected
- replace downstream product validation or workflow controls

---

## Canonical Routing Principle

The primary principle of FUZE model routing is:

> products declare task intent and required execution characteristics, while the platform determines the model-routing outcome and context envelope under shared policy, commercial, and operational rules.

This means:

- products specify what kind of task they need solved
- products may specify structured requirements and constraints
- the platform decides how that task should be executed
- context is assembled according to scope, permissions, and task relevance
- routing decisions should be explainable at the platform level even when hidden from ordinary end users

This principle preserves platform coherence and prevents hidden product-level AI fragmentation.

---

## Canonical Context Principle

The primary principle of FUZE context handling is:

> AI tasks should receive the minimum sufficient structured context required to complete the task well, and no context should be included merely because it happens to be available.

This means:

- context must be relevant, not merely accessible
- context must respect identity, workspace, and permission boundaries
- context selection must be task-aware
- context must preserve separation between platform truth, product truth, and derived views
- context should be versionable and attributable to the task that used it

This principle is essential for quality, safety, privacy, and cost discipline.

---

## Why Routing and Context Need Their Own Shared Spec

In a multi-product AI platform, routing and context are not minor details. They are two of the most important drivers of:

- output quality
- latency
- cost
- data safety
- workflow correctness
- user trust
- product consistency

If routing and context are left to product-specific improvisation, the platform accumulates several risks:

- inconsistent model quality across similar tasks
- unnecessary cost inflation
- excessive or under-scoped context
- weak provider fallback behavior
- hidden data exposure across scopes
- poor reproducibility in support and debugging
- commercial attribution blind spots

FUZE addresses this by making routing and context shared platform concerns rather than isolated product concerns.

---

## Core Concepts

### Task Intent
The semantic description of what the product or platform wants the AI layer to accomplish.

### Routing Class
A normalized category of AI execution requirement used for model selection and fallback planning.

### Context Envelope
The structured package of inputs, references, instructions, and policy state used to run an AI task.

### Context Source
A domain or system that provides information for an AI task, such as identity, workspace, product state, workflow state, or wallet-aware context.

### Instruction Layer
A distinct tier of instructions applied to the task, such as platform policy, product behavior, task formatting, or tool-use rules.

### Capability Requirement
A declared requirement such as low latency, structured JSON output, multi-step reasoning, summarization, classification, or tool usage.

### Commercial Class
The billing or entitlement-sensitive class of AI execution, such as included usage, premium usage, or restricted high-cost usage.

### Fallback Path
The defined alternative routing option used when the preferred model or provider is unavailable, too slow, too expensive, or policy-denied.

---

## Routing Is Based on Task Intent, Not Provider Preference

Products in FUZE should not think in terms of “call provider X model Y directly.” Նրանք should think in terms of task intent.

Examples of task intent include:

- summarize this event feed
- classify this workflow item
- generate structured output for a HerHelp app transform
- explain a trading signal in QTB
- produce a Botmad workflow recommendation
- extract structured utility configuration suggestions for ZAGA
- produce a safe short response for an interactive assistant surface

The routing layer then decides which model or model class should be used.

### Why this matters

This protects FUZE from:
- provider lock-in by product
- inconsistent quality across products
- uncontrolled cost escalation
- routing logic duplication
- inability to implement platform-wide fallback or downgrade strategy

Products declare the problem. The platform decides the execution path.

---

## Routing Classes

FUZE should normalize AI tasks into routing classes so that model selection remains structured.

Recommended routing classes include:

### 1. Fast Interactive
For low-latency user-facing tasks where speed matters more than deep multi-step reasoning.

Examples:
- short assistant responses
- simple summaries
- lightweight recommendations
- UI-coupled text generation

### 2. Structured Output
For tasks requiring deterministic or schema-constrained output.

Examples:
- JSON generation
- classification labels
- extracted entities
- normalized workflow payloads

### 3. Reasoning-Heavy
For tasks requiring stronger planning, synthesis, or multi-step analysis.

Examples:
- complex QTB explanations
- multi-factor operational interpretation
- deep comparison tasks
- multi-document analysis

### 4. Tool-Orchestrated
For tasks that may need retrieval, workflow references, or structured tool execution.

Examples:
- workflow-coupled actions
- product-state-informed tasks
- multi-source platform summaries

### 5. Batch / Background
For tasks best handled asynchronously due to volume or cost.

Examples:
- large report generation
- batch summarization
- queued intelligence refreshes
- large transformation jobs

### 6. Restricted or Review-Sensitive
For tasks subject to additional policy controls or requiring tighter routing constraints.

Examples:
- policy-sensitive text generation
- support-admin assist flows
- sensitive classifications
- higher-risk product operations

These routing classes are not the final provider choice. They are the structured language used to make routing decisions consistently.

---

## Routing Decision Factors

Routing decisions should consider multiple factors together rather than only one.

### Capability factors
- reasoning depth needed
- structured output requirement
- tool-use requirement
- language or formatting needs
- context length and retrieval needs

### User experience factors
- latency tolerance
- streaming suitability
- synchronous versus async need
- UI sensitivity to partial or delayed output

### Commercial factors
- plan tier
- included versus premium AI usage
- owner scope billing context
- usage cap or quota state
- cost ceiling or task budget policy

### Operational factors
- provider availability
- fallback readiness
- concurrency/load conditions
- historical reliability for task class
- queue state for async workloads

### Policy factors
- allowed providers
- restricted model classes
- data sensitivity
- moderation requirements
- review-gated tasks

### Product factors
- product domain
- task taxonomy
- expected downstream use
- validation expectations
- importance of consistency to that product feature

A routing decision should be recorded as the result of a structured evaluation, not as an opaque ad hoc guess.

---

## Canonical Context Envelope

Every AI task in FUZE should execute against a context envelope rather than an unstructured bag of inputs.

At minimum, the context envelope should be able to represent:

- task intent
- account context reference
- workspace context reference where applicable
- product context reference
- workflow context reference where applicable
- wallet-aware context reference where relevant
- policy context reference
- instruction layers
- structured user or system input
- retrieved supporting materials where applicable
- output constraints
- routing metadata

The context envelope should not require that every field be populated for every task. It should provide a canonical structure so tasks remain attributable and inspectable.

---

## Context Source Layers

The platform should assemble context from explicit source layers.

### 1. Identity and Account Source
May provide:
- canonical account reference
- account status
- account-level preferences where approved
- security or restriction flags relevant to execution

### 2. Workspace Source
May provide:
- workspace ID
- membership context
- role context
- workspace-level entitlement state
- shared configuration relevant to the task

### 3. Product Source
May provide:
- product task type
- product object references
- product state snapshots
- product-specific data needed to solve the task

### 4. Workflow Source
May provide:
- triggering step
- prior workflow outputs
- current workflow state
- human approval state
- retry lineage

### 5. Wallet-Aware Source
May provide:
- linked-wallet-derived context
- holder-aware status
- policy-allowed rank or eligibility signals

### 6. Policy Source
May provide:
- plan tier
- AI usage caps
- product AI permissions
- moderation requirements
- commercial classification
- restricted-mode controls

### 7. Retrieval / Knowledge Source
May provide:
- task-specific retrieved documents
- indexed references
- canonical product or platform reference content
- historical outputs where policy allows

These sources must be included intentionally, not automatically.

---

## Least-Context Rule

FUZE should adopt a least-context rule:

> only include context that is necessary for the specific task and allowed for the requesting scope.

This is important for several reasons:

- reduces unnecessary token/context cost
- improves response quality by reducing noise
- reduces privacy and boundary risk
- improves reproducibility
- lowers accidental cross-product or cross-workspace leakage

### Consequences of violating least-context

Too much context can lead to:
- worse answers
- leakage of unrelated information
- higher cost
- slower latency
- confused outputs
- product behavior that becomes harder to debug

The least-context rule should therefore be treated as a quality and safety principle, not only as an optimization technique.

---

## Instruction Layering Model

The AI routing and context system should support layered instructions.

Recommended instruction layers include:

### 1. Platform Policy Layer
Defines platform-wide constraints such as:
- safety rules
- output limitations
- provider/tool restrictions
- tone or explanation constraints for specific surfaces
- prohibited actions

### 2. Product Behavior Layer
Defines product-specific AI behavior expectations.

Examples:
- QTB should reason as trading-intelligence support
- HerHelp should generate SME-friendly structured outputs
- Botmad should focus on workflow support and error reduction
- AIE should optimize for discovery and event relevance

### 3. Task Layer
Defines what this specific task should achieve.

Examples:
- summarize
- classify
- rank options
- produce JSON
- explain differences
- propose workflow steps

### 4. Output Layer
Defines formatting or schema requirements.

Examples:
- JSON schema
- concise UI text
- long-form analysis
- markdown list
- structured labels

### 5. Tool-Use Layer
Defines if and how retrieval or other tools may be used.

Layering matters because platform policy must remain above product behavior when conflicts occur, and product behavior must remain above ad hoc task phrasing if the product needs consistency.

---

## Context Versioning and Traceability

Context should be traceable and, where meaningful, version-aware.

The platform should be able to answer:

- which context sources were used?
- which version of product instructions was used?
- which workflow state was referenced?
- what retrieved documents were included?
- what policy version or routing rule set applied?

This does not mean storing every raw token forever. It means preserving enough metadata and references to support:

- support investigation
- quality review
- commercial dispute handling
- bug debugging
- reproducibility where possible

In a serious AI platform, routing and context cannot be operational black boxes.

---

## Model Selection Outcomes

Routing should produce a normalized model-selection outcome rather than only a raw provider name.

At minimum, the outcome should indicate:

- routing class
- selected provider/model reference
- fallback plan
- execution mode
- structured-output mode if relevant
- tool-enabled or no-tool mode
- policy flags applied
- cost/commercial class
- trace or correlation reference

This outcome makes routing observable and easier to reason about across products.

---

## Fallback and Downgrade Strategy

FUZE must support explicit fallback behavior.

### Common fallback cases
- provider outage
- model timeout
- structured-output validation failure
- cost ceiling exceeded
- policy-denied premium route
- concurrency saturation
- degraded service window

### Fallback options may include
- switch to alternate provider/model
- switch from reasoning-heavy to lighter route
- switch from sync to async
- return degraded but valid output mode
- deny with explicit retry suggestion or internal retry path
- require human review for sensitive tasks

### Principle
Fallback should preserve correctness and policy before preserving feature richness. A smaller valid result is better than a larger invalid or policy-breaking result.

This is especially important in workflow-coupled and commercially metered environments.

---

## Structured Output and Validation Considerations

Routing should take structured-output requirements into account.

If a task requires:
- JSON
- classification labels
- field extraction
- deterministic shapes
- tool-call-ready outputs

then routing should prefer model classes and execution settings suitable for structured output.

### Validation principle

Structured output should be validated after generation. If validation fails:
- retry with same model where reasonable
- retry with stricter instruction layer
- switch to more structure-capable route
- fail explicitly rather than silently passing malformed output downstream

Products should not have to guess whether a free-form model response can safely drive structured workflows.

---

## Tool-Use and Retrieval Routing Considerations

Some AI tasks need tool use or retrieval; others do not.

Routing should distinguish between:

### No-tool tasks
Simple tasks where direct inference is sufficient.

### Retrieval-assisted tasks
Tasks that require product, workflow, or reference retrieval before inference.

### Tool-orchestrated tasks
Tasks that may use one or more platform-approved tools or intermediate steps.

The routing layer should make this explicit because:
- retrieval changes latency and cost
- tool use changes failure modes
- context provenance becomes more important
- product safety may require clearer boundaries

Not every task should be allowed to use tools merely because tools exist.

---

## Commercial Tier and Plan-Aware Routing

Routing may vary by commercial context when policy permits.

Examples:
- premium plans may access higher-capability routes
- free or trial plans may use narrower context windows or lighter model classes
- workspace enterprise plans may enable larger async jobs
- included usage may route differently from premium paid-overage actions

### Important boundary

Commercial-tier differences should affect quality tiers or capacity where appropriate, but should not create unsafe or misleading behavior. A lower tier may receive smaller capability, slower paths, or reduced limits, but it should not receive broken or deceptive AI behavior.

This distinction keeps monetization aligned with platform trust.

---

## Privacy, Permissions, and Cross-Scope Boundaries

The routing and context layer must enforce scope boundaries.

### Required rules

- an account should not receive workspace-private context without valid membership and permission
- one workspace's context must not be mixed into another workspace's task
- product-private context should not leak into other products without explicit shared policy
- wallet-aware context should be included only when relevant and permitted
- support/admin tasks should use explicitly elevated context paths, not ordinary user paths

The AI layer must not become a hidden data-bypass channel. Context assembly must respect the same ownership model the rest of the platform uses.

---

## Relationship to Product Domains

Products own domain task meaning. The routing/context layer owns execution discipline.

### Products provide
- task intent
- required output semantics
- domain object references
- downstream validation expectations

### Platform provides
- context envelope construction
- model routing policy
- instruction layering
- fallback behavior
- observability and metering hooks

This separation is important because FUZE wants strong product differentiation without fragmented AI infrastructure.

---

## Relationship to AI Usage Metering

Routing and context should emit enough structure for usage metering.

At minimum, metering-related outputs should include:

- task type
- product
- owner scope
- selected routing class
- provider/model reference class
- execution mode
- commercial tier
- result status
- fallback occurrence if any

This allows the platform to correlate:
- quality and cost
- provider usage
- product AI demand
- premium vs included execution
- anomaly detection

A routing system that cannot be metered is incompatible with FUZE’s commercial model.

---

## Observability Requirements

The routing/context layer should support observability for:

- routing-class distribution
- provider/model selection rates
- fallback frequency
- context assembly latency
- validation failure rates
- structured-output retry rates
- degraded-mode usage
- policy-denied routing attempts
- average context size by task class
- cross-product routing patterns

These metrics matter because routing and context quality determine both cost and product quality at platform scale.

---

## Audit Requirements

Meaningful routing and context decisions should be auditable enough to support:

- support investigation
- abuse or policy review
- billing disputes involving premium AI tasks
- quality debugging for structured workflows
- internal review of sensitive AI usage classes

Not every low-stakes inference needs full raw-context preservation, but the platform should preserve enough metadata and references to explain how important tasks were routed and what policy context applied.

---

## Minimum Data Model Requirements

At minimum, the routing and context layer should support semantic representation of:

### Routing Request
- routing_request_id
- ai_task_id
- task_intent
- product_reference
- requested_capabilities
- owner_scope_type
- owner_scope_id
- commercial_class
- created_at

### Context Envelope
- context_envelope_id
- ai_task_id
- account_context_ref
- workspace_context_ref
- product_context_ref
- workflow_context_ref
- wallet_context_ref where relevant
- policy_context_ref
- retrieval_context_refs
- instruction_layer_refs
- context_version

### Routing Decision
- routing_decision_id
- ai_task_id
- routing_class
- selected_provider_model_ref
- execution_mode
- fallback_plan_ref
- structured_output_required
- tool_mode
- decision_timestamp
- decision_reason_summary

### Routing Outcome
- ai_task_id
- final_provider_model_ref
- fallback_used flag
- completion_status
- validation_status where relevant
- degraded_mode flag
- usage_record_ref where applicable

These are minimum semantic requirements. Detailed schemas are refined downstream.

---

## Edge Cases and Failure Handling

### Product requests premium reasoning route but scope lacks entitlement
Routing must deny, downgrade, or queue for alternate paid path according to commercial policy.

### Context retrieval returns too much data
The context envelope must be reduced according to least-context and task-relevance rules rather than passed through unchanged.

### Product object changed after context assembly
Sensitive tasks may require revalidation or context refresh before final execution.

### Selected model unavailable mid-flight
Fallback or degraded-mode policy must apply without silently changing the business meaning of the task.

### Structured-output task fails validation repeatedly
The system must fail explicitly, switch route, or escalate rather than returning malformed output to downstream automation.

### Workspace access revoked while queued task is pending
High-impact tasks may require authorization re-check at execution time.

### Same task retried with slightly different context
The platform should preserve lineage so support and billing can distinguish retries from distinct business actions.

---

## Open Items

The following areas are intentionally refined in downstream specifications:

- exact provider/model inventory and ranking
- exact context truncation algorithms
- exact retrieval ranking and citation behavior
- exact prompt-registry governance workflow
- exact cross-product shared-memory policy if later introduced
- exact degraded-mode UX for each product surface

These do not weaken the canonical routing and context model established here.

---

## Closing Summary

The FUZE model routing and context layer is the shared decision system that determines how AI tasks are executed and what information they are allowed to use. It keeps products focused on task intent while the platform governs model choice, context assembly, policy boundaries, fallback behavior, and commercial attribution. By separating task meaning from execution routing, and by enforcing least-context, layered instructions, and scope-aware data access, FUZE strengthens AI quality, cost discipline, privacy, and platform coherence across a growing multi-product ecosystem.
