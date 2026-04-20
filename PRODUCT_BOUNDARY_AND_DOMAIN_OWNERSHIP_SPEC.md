# PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC

## Document Metadata

- Document Name: `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
- Document Type: Canonical refined system specification
- Status: Active refined system spec
- Governing Layer: Platform constitution / product boundary / product domain ownership
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: Platform architecture, product architecture, backend engineering, product engineering, API design, data engineering, security, operations, governance, reporting
- Primary Purpose: Define the canonical boundary between the FUZE shared platform and FUZE product domains, including what products may own, what must remain platform-owned, how products consume shared capabilities, how product-local domains are structured, and how future products may be admitted without fragmenting the platform
- Primary Upstream References:
  - `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
  - `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
  - `PLATFORM_ARCHITECTURE_SPEC.md`
  - `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
  - `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
  - `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
  - `SYSTEM_SPEC_INDEX.md`
  - `API_SPEC_INDEX.md`
  - `DOCS_SPEC_INDEX.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
  - `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
- Primary Downstream Dependents:
  - `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md`
  - product integration specifications
  - `API_ARCHITECTURE_SPEC.md`
  - `PUBLIC_API_SPEC.md`
  - `INTERNAL_SERVICE_API_SPEC.md`
  - `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
  - `WORKFLOW_AND_AUTOMATION_SPEC.md`
  - `AI_ORCHESTRATION_SPEC.md`
  - `MODEL_ROUTING_AND_CONTEXT_SPEC.md`
  - `PLATFORM_CREDITS_SPEC.md`
  - `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
  - `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - `MONITORING_ALERTING_AND_INCIDENT_RESPONSE_SPEC.md`

---

## Purpose

This specification defines the canonical product boundary and product-domain ownership model for the FUZE ecosystem.

Its purpose is to make explicit:

- what a FUZE product is within a platform-first ecosystem
- what belongs to the shared platform versus what belongs to an individual product domain
- how product-specific domain logic may extend the platform without redefining platform primitives
- how products must consume identity, session, workspace, authorization, entitlement, credits, billing, audit, and chain-aware context
- how product services, APIs, workflows, AI behavior, reports, and local data must remain bounded
- how new products may be added without turning FUZE into a portfolio of loosely shared mini-platforms

This document exists because FUZE is intentionally multi-product. In a multi-product platform, product speed is valuable, but platform coherence is more valuable than local convenience. Without a strict product-boundary model, products drift into alternate identity systems, alternate spending semantics, alternate payout interpretations, shadow entitlement logic, and hidden governance pathways. This specification prevents that drift.

---

## Scope

This specification governs:

- the canonical definition of a product in FUZE
- the boundary between shared platform domains and product-local domains
- product-domain ownership rules for services, entities, workflows, AI behaviors, analytics, reports, and UX
- the mandatory shared-platform dependencies that products must reuse
- prohibited product-owned concerns
- product consumption of chain-aware, credits-aware, entitlement-aware, and wallet-aware context
- product API, event, storage, execution, reporting, and control-plane implications
- failure-handling and migration rules required to preserve platform coherence
- the ownership and integration assumptions that future product-admission decisions must respect

This specification does not define:

- the full business design of any one product
- the admission criteria for new products in detail
- detailed entity schemas for every product
- detailed workflow logic for every product
- detailed AI prompt libraries or product UX
- exact service topology or deployment model for each product
- detailed commercial packaging per product
- detailed permission matrices per product

Those belong in downstream product and platform specifications.

---

## Out of Scope

This specification is explicitly out of scope for:

- product strategy prioritization
- product roadmap or launch sequencing
- UI composition details
- exact team ownership assignments
- exact database topology
- exact queue topology
- exact contract ABI and chain-integration implementation
- full pricing model or marketing packaging language

---

## Design Goals

The design goals of the FUZE product-boundary model are:

1. Keep FUZE platform-first while still enabling strong product differentiation.
2. Prevent each product from becoming a shadow platform.
3. Make product ownership explicit for product-local truth, workflows, and surfaces.
4. Preserve one platform-wide model for identity, session, workspace, authorization, entitlement, billing, credits, payout, audit, and control.
5. Support product-specific AI, automation, and workflow behavior without breaking shared policy and infrastructure.
6. Make future product expansion predictable and architecture-safe.
7. Preserve economic and governance separations across all products.
8. Prevent derived reports, local caches, or product-side convenience logic from becoming alternate sources of record.
9. Make API and event boundaries implementation-usable.
10. Keep product isolation compatible with shared transparency, auditability, and control-plane governance.

---

## Non-Goals

This specification is not intended to:

- flatten all product logic into generic platform services
- block product-local innovation where shared ownership is unnecessary
- let products redefine platform primitives for convenience
- let products own their own alternate commerce, credits, entitlement, or payout architectures
- let products treat platform context as optional when it is required for cross-product continuity
- create soft co-ownership between product and platform when one owner is required
- let products hide governance-sensitive actions inside ordinary runtime flows
- let product-local reports or AI outputs redefine platform or chain truth

---

## Core Principles

### 1. Platform-First Product Principle
Products in FUZE are extension domains built on top of a shared platform. They are not separate platforms.

### 2. Product Differentiation Principle
Products may own category-specific business logic, entities, workflows, AI outputs, interfaces, analytics, and reports when those concerns are product-local.

### 3. Shared Primitive Reuse Principle
Any concern that is cross-product, identity-defining, economically shared, governance-sensitive, chain-sensitive, or required for future expansion remains platform-owned unless a formal exception is approved.

### 4. No Alternate Primitive Principle
A product may not instantiate alternate versions of identity, workspace, authorization, entitlement, credits, payout, reserve, registry, or governance primitives.

### 5. Bounded Product Autonomy Principle
Products should move quickly inside their domain, but their autonomy ends at the platform boundary.

### 6. Consumption-Not-Reownership Principle
Products should consume platform-owned context and services through explicit contracts rather than copying and re-owning the same truth locally.

### 7. Product Reports Are Downstream Principle
Product reports, views, dashboards, and AI explanations may compose platform and product truth, but they do not redefine the owning source.

### 8. Explicit Escalation Principle
If product functionality appears to require ownership of a platform primitive, the correct response is escalation and architectural review, not silent local implementation.

---

## Canonical Definitions

### Product
A bounded product-specific application domain inside the FUZE ecosystem that owns product-local entities, workflows, interfaces, outputs, and domain logic while consuming shared platform capabilities.

### Product Domain
The bounded service, data, API, workflow, and UX scope owned by one product.

### Shared Platform Primitive
A reusable cross-product capability whose truth or policy must be platform-owned, such as identity, workspace, billing, credits, entitlement, audit, or governance-aware control.

### Product Extension Domain
A product domain specifically designed to extend the shared platform rather than replace it.

### Product-Local Canonical Truth
Truth whose canonical owner is the product domain because it represents product-specific objects, lifecycle, workflows, outputs, or domain-specific behavior.

### Product Capability
A function, feature, or product behavior potentially gated by entitlement, policy, workspace context, or credits/billing rules.

### Product Surface
A user-facing, admin-facing, or partner-facing product interface built on top of product and platform domains.

### Product Integration Contract
The explicit API, event, workflow, or service boundary through which a product consumes shared platform capabilities.

### Shared Context
Platform-owned context such as account, session, workspace, authorization result, entitlement result, wallet-aware participation context, credits state, or product-admission restriction state.

---

## Architectural Position in the Spec Hierarchy

This document sits below:

- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`

and above:

- `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md`
- product integration specifications
- product-specific API and workflow specs
- product-specific data and analytics specs
- entitlement/capability, billing, credits, AI, workflow, and security specs where products consume shared platform primitives

This specification does not override the top-level layer model. It refines how the product layer must exist within it.

---

## Canonical Definition of a FUZE Product

A FUZE product is a bounded product-extension domain that:

- owns product-specific domain objects and product-specific workflow truth
- consumes shared platform identity, account, session, workspace, authorization, entitlement, commerce, credits, audit, and control-plane capabilities
- may consume chain-aware context through platform-controlled boundaries
- may expose product-specific interfaces, AI experiences, automations, and analytics
- may define product-specific monetization configuration only within platform-owned commerce and entitlement rules
- must not redefine the platform’s shared primitives or economic rails

A FUZE product is therefore not just “any app in the ecosystem.” It is an application domain that fits the shared platform model.

---

## Product Layer Versus Platform Layer

### Platform Owns
The platform owns, at minimum:

- canonical account-rooted identity
- linked login methods and session/security state
- workspace and organization structure
- role, permission, access-control, and scoped-authorization frameworks
- wallet-link mapping and wallet-aware participation context
- billing, subscriptions, invoicing, receipts, and payment normalization
- Platform Credits policy and shared credits orchestration
- entitlement and capability-gating framework
- shared AI orchestration, routing, and metering primitives
- shared workflow, automation, job, and execution infrastructure
- audit, transparency, registry, and reporting infrastructure
- chain-adjacent coordination and on-chain responsibility boundaries
- control-plane, restriction, remediation, and governance-aware operational pathways

### Products Own
Products own, at minimum:

- product-local entities
- product-local workflows
- product-local AI artifacts and prompt specializations where these are product outputs rather than shared orchestration metadata
- product-local interfaces and user experiences
- product-local configuration within platform rules
- product-local analytics objects where designated as product-owned
- product-local dashboards, reports, and derived views
- product-specific runtime policies that do not redefine platform policy
- product-local orchestration patterns built on shared workflow primitives

### Key Boundary Rule
The platform owns shared capability. Products own category-specific extension behavior. If a capability materially affects more than one product or defines shared trust/economic meaning, it belongs to the platform by default.

---

## Mandatory Platform Dependencies for All Products

Every FUZE product must reuse the following platform-owned capabilities where relevant.

### Identity and Account
Products must use the platform’s canonical account model. Products may not define alternate identity roots. This preserves cross-product continuity and aligns with the account/session foundation model.

### Authentication and Session
Products must treat authentication and session state as platform-owned access mechanisms. Products may not create alternate durable session truth or bypass platform continuity/safety rules.

### Workspace and Organization
Where collaborative scope exists, products must use platform workspaces or formally approved platform-compatible scope constructs. Products may not invent incompatible parallel workspace systems for shared use cases.

### Authorization
Products must integrate with platform access-control and scoped-authorization models when product actions depend on user/workspace authority. Products may add product-specific permission logic only inside the platform-aligned authorization framework.

### Entitlement and Capability Gating
Products must use platform-owned entitlement and capability-gating outcomes for commercially gated features. Product capability exposure must not bypass shared billing/credits consequences.

### Billing, Payments, and Commerce
Products must not create alternate commercial truth. All recurring billing, payment verification, invoice/receipt generation, refund/reversal handling, and shared commercial state remain platform-owned.

### Platform Credits
Products may consume Platform Credits, trigger credits spend requests through approved owner-controlled boundaries, and render credits-aware experiences. Products may not create alternate internal spending assets outside approved platform credits models.

### Wallet-Aware Participation Context
Products may consume wallet-aware context, holder classification, rank, or chain-aware signals through platform-owned boundaries. Products may not redefine token participation semantics or raw chain truth.

### Audit and Activity
Products must produce required audit and activity evidence through platform-approved audit infrastructure and may not treat local logs as sufficient replacement for material actions.

### Control and Governance Pathways
Products must route sensitive actions through the appropriate control-plane or governance-aware pathways. Product UIs may initiate, but may not silently complete, trust-sensitive actions that belong to platform or governance control.

---

## Prohibited Product-Owned Concerns

Products may not own the following as canonical truth unless an explicit platform-level specification changes the boundary.

### Prohibited Identity and Access Concerns
- alternate account identity systems
- alternate session truth
- alternate shared workspace models
- alternate cross-product authorization truth
- alternate entitlement frameworks for shared commerce

### Prohibited Economic Concerns
- alternate internal spending assets outside approved Platform Credits
- alternate billing truth
- alternate refund/reversal truth
- alternate payout architecture
- alternate reserve or treasury semantics
- alternate cross-product pricing consequence truth

### Prohibited Governance and Trust Concerns
- hidden governance-sensitive control flows
- product-owned contract registry truth
- product-owned reserve-vault policy
- product-owned multisig/timelock semantics
- product-owned public trust reporting that contradicts platform reporting truth

### Prohibited Chain and Reporting Concerns
- product-local reinterpretation of token-balance truth
- product-local reinterpretation of on-chain payout execution truth
- product-owned public registry truth
- product reports that silently redefine platform or chain-owned state

### Prohibited Operational Shortcuts
- direct mutation of platform-owned canonical entities from product-local stores
- convenience-layer APIs that bypass owner contracts
- product-local correction of platform-owned balances or entitlements
- product-local execution systems that become owners of business meaning outside the product domain

---

## Product-Local Canonical Ownership

The following categories are valid product-owned canonical concerns.

### Product Domain Objects
Products may own their own objects, content models, records, project items, user-facing artifacts, strategy objects, generated outputs, product-specific recommendations, task objects, or domain-specific resources.

### Product Workflow State
Products may own workflow state that is product-specific and not shared as a platform workflow primitive.

### Product AI Outputs
Products may own AI outputs, prompt results, generated plans, generated app artifacts, recommendations, analyses, or drafts when those outputs are product-domain results rather than shared orchestration state.

### Product-Local Configuration
Products may own product-specific settings and configuration scoped to product behavior, within platform-owned billing, entitlement, and governance boundaries.

### Product-Local Analytics
Products may own analytics objects or reports specifically about product behavior, product performance, or product content, provided they do not redefine platform commercial or chain truth.

### Product UX and Local Drafts
Products may own local draft state, local surface state, UX composition, and presentation logic.

---

## Product Integration With Shared Platform Services

Products must integrate with shared platform services through explicit contracts.

### API Integration
A product should call or depend on owner-controlled platform APIs for identity, workspace, access, credits, billing, entitlement, audit, and control-plane interaction rather than directly writing platform-owned stores.

### Event Integration
Products may subscribe to owner-published events from platform domains and publish product events for downstream consumers. Product events must not contradict the owner-published meaning of platform domains.

### Workflow Integration
Products may compose platform workflow primitives with product-owned workflow steps. Shared workflow infrastructure remains platform-owned; product workflow business meaning remains product-owned.

### AI Integration
Products may specialize prompts, tool sequences, domain-specific output validation, and user-facing AI behavior. Shared orchestration, routing, context policy, and AI usage metering remain platform-owned.

### Chain-Aware Integration
Products may consume chain-aware context or trigger owner-controlled flows that ultimately reach chain-adjacent services. Products may not directly redefine on-chain policy or claim contract-native truth they do not own.

### Reporting Integration
Products may publish product-local reports and dashboards. Reports consuming platform or chain-owned data must preserve source traceability and must not restate derived views as source truth.

---

## Product API Boundary Rules

This specification imposes the following product API rules.

### Rule 1: Product Write APIs May Only Write Product Truth
A product-owned mutation API may change product-owned canonical entities only.

### Rule 2: Platform-Owned Writes Must Route to Platform Owners
If a product action requires credits spend, entitlement refresh, billing consequence, or workspace-level mutation, the product must route through the owning platform boundary.

### Rule 3: Product Read APIs May Aggregate, But Must Not Reassign Ownership
A product read API may compose product, platform, and chain-aware data into a product view, but must not imply that the product is the owner of all returned fields.

### Rule 4: Sensitive Paths Must Stay Explicit
A product surface must not hide governance-sensitive, treasury-sensitive, or shared-control actions behind ordinary product API semantics.

### Rule 5: Public and Internal Product APIs Must Preserve Ownership Labels
If a product exposes public or partner-facing surfaces, those surfaces must not blur platform-owned versus product-owned responsibilities.

---

## Product Event and Async Rules

Products may participate heavily in async workflows, but ownership must remain clear.

### Product Events
Products should publish product-owned canonical events for product-domain state changes.

### Consuming Platform Events
Products may react to platform events such as entitlement changes, credits results, account restrictions, or workspace updates, but may not reinterpret those events into contradictory source truth.

### Execution State
Product jobs, workers, callbacks, and async pipelines own execution lineage, not platform truth. Execution does not transfer ownership.

### Retries and Idempotency
Product retries must be safe for product-owned mutation boundaries and must not create duplicate commercial or platform-owned side effects.

### Chain and Provider Inputs
When product behavior depends on chain or provider inputs, those inputs must pass through owner-controlled normalization or integration boundaries.

---

## Product Data Model and Storage Rules

Products must preserve explicit ownership at the data layer.

### Product-Owned Entities
Product-owned entities should remain clearly distinguishable from platform-owned entities.

### Cross-Domain References
Products should reference platform-owned entities such as `account_id`, `workspace_id`, `wallet_link_id`, `subscription_id`, `entitlement_id`, `credits_owner_scope_id`, or other canonical references rather than copying and re-owning those objects.

### Product Caches and Projections
Product caches, denormalized fields, search indexes, analytics stores, and dashboard projections are derived unless explicitly elevated by a narrower spec.

### Product Reports
Product reports may be canonical as report artifacts for the product, but not for the upstream platform or chain facts they summarize.

### Migration Discipline
If a product-local concern becomes cross-product over time, ownership must be reviewed and potentially elevated to a platform domain rather than allowing shadow duplication.

---

## Product Boundary Rules for Existing and Future FUZE Products

This specification applies to all current and future FUZE products, including but not limited to:

- QTB
- AIMM
- ZAGA
- AIE
- HerHelp
- Botmad
- future admitted products

Each product may own product-specific entities, workflows, AI outputs, interfaces, analytics, and product-local policies within platform rules. None of these products may redefine the shared platform primitives that preserve ecosystem continuity.

---

## Product Monetization and Capability Rules

Products may have distinct monetization packaging and capability exposure, but these must remain bounded.

### Allowed
- product-specific feature packaging
- product-specific usage semantics
- product-specific display of plan/capability outcomes
- product-specific spend requests within Platform Credits rules
- product-specific pricing presentation within platform commercial boundaries

### Not Allowed
- product-local truth for recurring subscription status that contradicts platform billing truth
- product-created alternative credits or spend assets
- product-local entitlement frameworks that bypass platform billing or credits logic
- product-local payout promises or reserve semantics outside approved platform/governance structures

---

## Product Control-Plane and Governance Rules

Products must distinguish ordinary runtime actions from sensitive control actions.

### Product Runtime Actions
Normal product actions may be handled through product and platform runtime APIs according to ordinary authorization and entitlement rules.

### Sensitive Product Actions
If a product action touches:
- shared platform billing
- credits issuance or reversal
- payout or treasury behavior
- registry publication
- reserve or vault policy
- contract-control state
- high-impact permissions
- emergency restriction behavior

then the action must pass through the appropriate control-plane or governance-aware pathway.

### Principle
Products may initiate sensitive requests through surface flows, but they may not silently complete them outside the correct owner/governance boundary.

---

## Product Failure Handling and Isolation Rules

Products must fail in a way that preserves platform truth.

### Product Isolation
Failure in one product must not corrupt shared identity, session, workspace, billing, credits, entitlement, wallet-link, or reporting truth.

### Degraded Platform Dependencies
If a platform dependency is degraded, the product must distinguish:
- unavailable platform data
- stale derived view
- pending reconciliation
- blocked mutation
- successful completed truth

The product must not silently invent replacement truth.

### Local Cache Drift
If local caches or projections drift from platform or chain-owned truth, the canonical owner wins and the product must reconcile.

### Sensitive Action Failure
If a control-plane or governance-sensitive action fails, the product must not represent it as complete merely because a local workflow step advanced.

---

## Security / Risk / Abuse Controls

This product-boundary model is a security and integrity control.

If products are allowed to overreach:
- platform identity may fragment
- credits and billing semantics may diverge
- entitlements may become inconsistent
- payout and chain-aware meaning may be misrepresented
- local dashboards may redefine business truth
- governance-sensitive actions may be executed through ordinary flows
- product incidents may escalate into platform incidents

All downstream product, security, abuse, monitoring, and operations specifications must preserve these boundaries.

---

## Audit / Traceability Requirements

FUZE must be able to determine:

- which product owns a product-local domain object
- which platform owner supplied any shared context consumed by the product
- whether a visible value is product-owned, platform-owned, chain-owned, or derived
- whether a product action mutated product truth or requested a platform mutation
- whether a product-local report is derived from platform or chain state
- whether a sensitive action crossed a control/governance pathway
- how product workflows, platform workflows, and chain-adjacent steps relate in lineage

This is necessary for incident response, governance review, and cross-product trust.

---

## Migration / Compatibility / Supersession Considerations

- if a product-local capability becomes cross-product and foundational, it should be considered for elevation into a platform domain
- if a legacy product has product-local implementations of now-shared primitives, compatibility layers may exist temporarily, but canonical ownership must move to the platform
- product migration must preserve references to canonical platform entities
- deprecated product-local shadow truth must not remain relied upon by downstream consumers
- if older materials imply looser product/platform separation, this refined specification supersedes them within its scope

---

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
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- identity/account/auth/session foundation docs
- workspace/access-control foundation docs

This specification directly governs or materially informs:

- `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md`
- product integration specs
- `API_ARCHITECTURE_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `AI_ORCHESTRATION_SPEC.md`
- `MODEL_ROUTING_AND_CONTEXT_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `ENTITLEMENT_AND_CAPABILITY_GATING_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `MONITORING_ALERTING_AND_INCIDENT_RESPONSE_SPEC.md`

---

## Explicitly Deferred Items

The following are intentionally deferred to downstream specifications:

- product-by-product detailed object inventories
- exact product API route families and schemas
- exact product-specific permission catalogs
- exact commercial packaging by product
- exact product-specific AI prompt and tool patterns
- exact product-local analytics and telemetry models
- exact migration playbooks for legacy product-local primitives

These are refinements of the product-boundary model, not replacements for it.

---

## Final Normative Summary

FUZE products are bounded extension domains built on a shared platform. Products own product-local entities, workflows, outputs, UX, and category-specific logic. The platform owns shared identity, session, workspace, authorization, entitlement, commerce, credits, audit, control-plane, chain-adjacent coordination, and other cross-product primitives. On-chain systems own only the truths explicitly committed to contract design. External systems remain external trust boundaries.

Products must consume platform-owned capabilities through explicit contracts rather than recreating them. Products may differentiate strongly within their own domain, but they may not define alternate identity roots, alternate commerce or credits truth, alternate payout semantics, alternate public registry truth, or hidden governance-sensitive control pathways. If a future product feature appears to require ownership of a shared primitive, the correct response is architectural escalation, not silent local implementation.

This document is the canonical product-boundary rule set for all current and future FUZE products.
