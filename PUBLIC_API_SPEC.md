# PUBLIC_API_SPEC

## Purpose

This document defines the canonical public API specification architecture of the FUZE ecosystem. Its purpose is to establish what parts of the FUZE platform may be exposed to external consumers, how public APIs differ from internal platform and product APIs, what safety and stability requirements apply to external-facing interfaces, and how public API design should preserve architectural clarity around identity, products, Platform Credits, transparency, and payout-related information.

This specification is foundational because FUZE is a multi-product, transparency-first platform ecosystem rather than a closed single-application stack. Over time, external consumers may include public frontend clients, partner integrations, ecosystem services, external developers, selected enterprise consumers, public transparency surfaces, and holder-facing information clients. In such an environment, public API design cannot be treated as a thin export layer added after the fact. It must be aligned with platform ownership, security, privacy, stability, and trust-sensitive architectural boundaries from the beginning.

---

## Scope

This specification covers:

- the canonical role of public APIs in the FUZE ecosystem
- the distinction between public APIs, internal service APIs, first-party application APIs, and event-driven integrations
- what kinds of capabilities are appropriate for public exposure
- what domains should remain restricted or indirect rather than publicly writable
- authentication, authorization, rate-limiting, visibility, and stability expectations for public-facing APIs
- public API treatment for identity-aware reads, product access, wallet-aware context, transparency surfaces, and payout-related views
- how public APIs should handle derived data, public registry access, and reporting-oriented surfaces
- versioning, deprecation, and compatibility principles for externally consumed FUZE APIs
- security, auditability, abuse prevention, and failure-handling requirements for public API operations

This specification does not define every route path, schema field, or SDK shape. Those are refined in:

- `API_ARCHITECTURE_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `IDEMPOTENCY_AND_VERSIONING_SPEC.md`
- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`
- `PAYOUT_LEDGER_SPEC.md`

---

## Design Goals

The design goals of the FUZE public API architecture are:

1. to expose external-facing capabilities without weakening platform ownership boundaries
2. to provide stable and understandable integration surfaces for selected external consumers
3. to preserve strong security and privacy boundaries around internal platform and product state
4. to support public transparency, registry, payout, and ecosystem-read surfaces in a structured way
5. to make public write operations narrower and more controlled than internal write operations
6. to reduce confusion between canonical truth APIs and public reporting or derived-read APIs
7. to support future ecosystem integrations without turning the platform into an undifferentiated open write surface
8. to make public interfaces trustworthy, versionable, and supportable over time

---

## Non-Goals

This specification is not intended to:

- expose every internal domain capability through public APIs
- allow external consumers to bypass platform governance, treasury, or payout controls
- make public APIs the default write path for all product-domain mutations
- publish unsafe operational, security, or confidential partner details
- replace internal service APIs with public endpoints for convenience
- make transparency surfaces equivalent to unrestricted raw internal data access
- imply that every first-party app request should be supported as a permanent public contract

---

## Canonical Public API Principle

The primary principle of FUZE public APIs is:

> public APIs in FUZE should expose only those capabilities and data surfaces that are intentionally safe, architecturally appropriate, and externally supportable, while preserving stricter boundaries around canonical writes, internal orchestration, governance-sensitive operations, and private platform state.

This means:

- public APIs are curated interfaces, not direct windows into all platform capabilities
- externally writable operations should be narrower than internal writable operations
- public read surfaces may include canonical public truth or carefully derived views
- public APIs must preserve the distinction between token, credits, payouts, transparency data, and private business state
- every public endpoint should exist because it supports a valid external contract, not merely because it is technically easy to expose

This principle is one of the main ways FUZE protects long-term platform integrity while still supporting openness where appropriate.

---

## Why FUZE Needs a Distinct Public API Layer

FUZE needs a distinct public API layer because external-facing integrations and clients have different requirements from internal platform services.

Internally, FUZE may use service-to-service APIs, job orchestration, internal administrative controls, event-driven side effects, and product-domain mutation paths that depend on trusted platform context. These interfaces often assume stronger operator control, richer private state, and tighter coupling to canonical entity owners.

Externally, the platform must assume a different environment:

- callers may be less trusted
- call patterns may be unpredictable
- compatibility obligations are stronger
- security and abuse risk is higher
- data privacy constraints are broader
- public misunderstandings can propagate faster
- trust-sensitive architectural distinctions must remain especially clear

Without a distinct public API layer, FUZE would risk exposing either too much capability or the wrong capability. It might also accidentally let external consumers depend on unstable internal interfaces that were never meant to be public contracts.

A distinct public API layer solves this by creating a formal answer to several questions:

- what data or actions are meant for external use
- which operations are safe for public write exposure
- which domains are read-only in public context
- how partner and ecosystem integrations should interact with the platform
- how transparency-facing and payout-facing public information should be delivered
- what versioning and compatibility promises apply externally

This is especially important in FUZE because the ecosystem includes:
- multiple products,
- a shared credits economy,
- wallet-aware context,
- public token participation,
- payout-ledger and transparency surfaces,
- and governance-sensitive structures that must not be exposed loosely.

The public API layer exists to support openness with discipline.

---

## Public API vs First-Party App API vs Internal Service API

FUZE should explicitly distinguish among three major interface categories.

### Public API
Public APIs are stable external contracts intentionally exposed for use by outside consumers, ecosystem services, partner integrations, or selected public clients.

### First-Party App API
First-party apps may use application-facing APIs that are not automatically permanent public contracts. Some may later become public, but many may remain private-to-platform or private-to-product.

### Internal Service API
Internal service APIs support service-to-service coordination, ownership-aligned writes, orchestration, policy enforcement, and background workflows. These should not be exposed merely because public consumers want convenience.

### Why the distinction matters

If FUZE does not distinguish these categories clearly, several problems may appear:

- internal change speed will slow because private interfaces become de facto public
- product teams may unintentionally promise too much stability externally
- governance- and economic-sensitive operations may leak into public surface area
- public users may obtain capabilities that were intended only for controlled platform workflows

### Principle

A first-party interface is not public merely because a frontend uses it.  
An internal interface is not public merely because an external consumer might find it useful.

FUZE should treat public API designation as a deliberate product and architecture choice.

---

## Public API Surface Families

FUZE should structure its public APIs into clearly understood surface families.

At minimum, the platform should recognize the following public-facing API families.

### 1. Public Read APIs
Public or partner-safe read surfaces exposing intentionally visible ecosystem data.

Examples may include:
- public contract and wallet registry reads
- payout-ledger reads
- transparency-report index and retrieval
- public platform metadata
- public product catalog or ecosystem directory surfaces

### 2. Authenticated User Public APIs
Externally consumable APIs used by authenticated users or client applications under explicit identity scope.

Examples may include:
- account profile reads
- workspace-scoped product access reads
- credits balance reads
- subscription and entitlement reads
- wallet-link status reads
- user-specific claim status reads where supported

### 3. Public Product Access APIs
Selected public product capability surfaces intentionally designed for controlled external consumption.

Examples may include:
- product request submission APIs
- report retrieval APIs
- status-check endpoints for async tasks
- partner-safe product query APIs

### 4. Partner / Integration APIs
APIs intended for selected external platforms, ecosystem partners, or enterprise integrations.

Examples may include:
- partner webhooks or callbacks
- ecosystem status feeds
- integration-oriented data exports
- limited product-operation interfaces under contract

### 5. Transparency and Registry APIs
Read-focused surfaces that strengthen public intelligibility.

Examples may include:
- reserve and registry lookup endpoints
- payout-cycle public status endpoints
- transparency-report catalog endpoints
- public governance-history summary endpoints where appropriate

### Surface-family principle

Each public API family should have different expectations for:
- authentication,
- authorization,
- rate limits,
- compatibility,
- and data sensitivity.

FUZE should not treat all public APIs as one generic access plane.

---

## Public API Exposure Criteria

A capability should be exposed publicly only if it satisfies explicit exposure criteria.

At minimum, FUZE should ask:

1. **Is this capability intentionally useful to an external consumer?**
2. **Can this be exposed without weakening canonical ownership or internal control?**
3. **Is the data or action safe to expose under public or partner-facing security rules?**
4. **Can the platform realistically support this as a stable contract over time?**
5. **Does exposure improve the ecosystem, product adoption, or transparency model meaningfully?**
6. **Can we version, rate-limit, audit, and support this surface properly?**
7. **Will this surface preserve role clarity between product usage, credits, governance, token participation, and payout architecture?**

### Principle

Public exposure should be deliberate.  
No endpoint should become public merely because it already exists internally.

This is especially important in FUZE because the platform spans both ordinary product operations and highly trust-sensitive economic systems.

---

## Domains Appropriate for Public Exposure

The following domains are generally more suitable for public or externally consumable API exposure when properly designed.

### Public Transparency and Registry Surfaces
These are naturally aligned with FUZE’s transparency-first model.

Examples:
- registry entries
- transparency reports
- public architectural metadata
- payout-ledger summaries
- cycle status and related public trust surfaces

### Authenticated Account and Workspace Reads
Users should be able to retrieve their own platform context through stable APIs.

Examples:
- account profile
- workspace membership list
- entitlements
- credits balance
- wallet-link overview
- async job history where appropriate

### Product Request and Result APIs
Some product flows may be exposed publicly as controlled request/response or async job interfaces.

Examples:
- submit analysis request
- query status of a product job
- fetch result artifact
- manage user-owned product objects where the product contract is designed for external use

### Public Ecosystem Metadata
Certain public platform and product metadata may safely support discovery or integration.

Examples:
- product listing
- public status references
- supported network metadata
- public documentation references

### Principle

Public APIs are strongest when they expose intentionally stable capabilities rather than weakly filtered copies of internal logic.

---

## Domains That Should Remain Restricted or Non-Public

Some domains should remain strongly restricted and should not be exposed as ordinary public write surfaces.

### Governance-Sensitive Actions
Examples:
- approve treasury action
- queue governance change
- execute payout-sensitive governance mutation
- rotate signer
- update control-path configuration

### Treasury and Vault Mutations
Examples:
- reserve deployment
- vault action approval
- Foundation-sensitive action execution
- liquidity-operations contract movement

### Raw Credits Mutation Controls
Products and users may initiate flows that result in credits mutation, but public consumers should not get generic unrestricted mutation endpoints for:
- arbitrary issue
- arbitrary adjust
- arbitrary reverse
- arbitrary release

These actions should remain domain-owned, policy-checked, and tightly controlled.

### Payout Entitlement Mutation
Public APIs may expose payout-cycle visibility and claim-status views, but not raw entitlement-authoring or eligibility-editing operations.

### Internal Orchestration Controls
Examples:
- workflow replay control
- provider failover toggles
- internal compensation paths
- fraud-resolution controls
- manual operator overrides

### Principle

Public interfaces should support participation, usage, and visibility — not expose the control plane of the ecosystem.

---

## Canonical Public Read Models vs Public Canonical Truth

Public APIs may expose two broad types of data surfaces.

### Public Canonical Truth
These are public-facing records that are canonical for the ecosystem at the public layer.

Examples:
- public registry entries
- published transparency reports
- published payout-ledger cycle records
- public product metadata

### Public Derived Read Models
These are safe, externalized aggregations or projections built for usability.

Examples:
- ecosystem summaries
- public dashboard statistics
- summarized payout cycle overviews
- public product or platform status summaries

### Important principle

Public derived reads must not be mistaken for the canonical owner of the underlying fact. For example:
- a payout summary API is not the source of payout execution truth
- a registry lookup API is not the source of contract state truth
- a public usage summary is not the source of billing or credits truth

Public APIs should therefore describe whether a resource is:
- canonical public artifact,
- public reporting artifact,
- or derived presentation model.

This improves clarity for integrators and reduces misuse of public surfaces as hidden system-of-record interfaces.

---

## Public Write API Philosophy

FUZE should be conservative and deliberate about public write APIs.

Public write APIs are appropriate when they represent externally initiated actions that belong naturally at the platform or product edge.

Examples may include:
- create account
- start authenticated product request
- link wallet
- create workspace
- initiate subscription checkout or upgrade flow
- request a report generation job
- submit a support-safe correction request or claim request flow where designed

### Important boundary

Public write APIs should not expose internal mutation primitives directly.

For example:
- a user may initiate a credits-consuming action, but should not call a generic `spend_credits` primitive directly
- a user may initiate a payout claim flow, but should not mutate entitlement state directly
- an external partner may send an integration request, but should not bypass policy checks or domain-owned validation

### Principle

Public writes should express business actions, not raw internal mutation power.

This allows FUZE to maintain strong ownership and policy enforcement while still supporting useful external interaction.

---

## Authentication Models for Public APIs

FUZE public APIs should support multiple authentication postures according to surface type.

### Unauthenticated Public Access
Used only for intentionally public read surfaces.

Examples:
- public registry lookup
- published transparency reports
- public product metadata
- public payout-cycle status summaries

### User-Authenticated Access
Used for account-, workspace-, or user-owned resource access.

Examples:
- profile reads
- credits balance
- workspace views
- job result access
- wallet-link operations
- claim history views

### Partner / Client Credential Access
Used for external integrations and ecosystem services under explicit agreements or scoped permissions.

Examples:
- partner ingestion
- integration polling
- contract-safe partner read surfaces
- external callback configuration where supported

### Principle

Authentication posture should be specific to the surface family.  
Public visibility should not imply authenticated mutability.  
Partner access should not imply operator-level authority.

This layered model helps FUZE remain flexible without weakening control.

---

## Authorization and Scope Rules for Public APIs

Public API authorization should be scope-aware and role-aware.

At minimum, authorization may depend on:

- authenticated account identity
- workspace role
- product entitlement
- credits or plan state where relevant
- wallet-linked participation context where relevant
- partner/client scope
- public visibility class
- environment and product-specific feature gating

### Scope examples

#### Account scope
Used for personal profile, wallet link, personal credits, and personal activity views.

#### Workspace scope
Used for workspace billing, memberships, shared balance views, and workspace-owned product operations.

#### Product scope
Used for product-owned objects, product jobs, and product results.

#### Public transparency scope
Used for registry, reports, and payout-ledger public resources.

### Principle

Public APIs should never rely on identity alone.  
They must also understand what scope the caller is acting in and what kind of rights that scope allows.

---

## Public APIs for Platform Credits and Billing

Public credits and billing APIs require especially careful design because they sit close to trust-sensitive commercial logic.

### Appropriate public reads may include:
- current credits balance
- credits owner scope summary
- credits transaction history views within user or workspace scope
- active subscription plan
- entitlement summary
- invoice and receipt retrieval where appropriate

### Appropriate public writes may include:
- initiate checkout or purchase flow
- confirm payment status inquiry
- request plan change
- initiate cancellation or downgrade request
- redeem a bounded promotional credit code if supported

### Restricted operations that should remain internal-domain controlled:
- arbitrary credits issue
- arbitrary credits adjustment
- raw ledger correction
- fraud or support compensation paths
- unrestricted reversal behavior

### Principle

The public commercial API should let users and partners interact with the commercial system, but not control the internal economic engine directly.

---

## Public APIs for Wallet-Aware and Participation Context

The wallet-aware layer may support selected public APIs.

### Appropriate reads may include:
- linked wallet list for the authenticated account
- wallet verification state
- holder-rank or participation-tier summary where policy allows
- claim eligibility summary for a user-linked context where appropriate

### Appropriate writes may include:
- initiate wallet link
- confirm wallet verification
- unlink wallet under policy rules

### Restricted operations
Public APIs should not expose:
- arbitrary manual rank override
- hidden eligibility mutation
- internal wallet classification controls

### Principle

Public wallet-aware APIs should expose user-participation context, not internal governance logic about that context.

---

## Public APIs for Payout and Transparency Surfaces

FUZE should provide especially strong public-read treatment for payout and transparency surfaces because they are core to the transparency-first architecture.

### Appropriate public payout reads may include:
- payout-cycle index
- cycle status
- funded amount
- payout asset
- chain and contract reference
- claim window state
- public payout-ledger references
- authenticated user claim-status views where applicable

### Appropriate transparency reads may include:
- transparency-report catalog
- transparency-report document metadata
- public contract and wallet registry lookup
- governance-history summaries where explicitly published
- reserve-category public descriptions

### Important boundary

Public payout and transparency APIs should expose public structure and public artifacts. They should not expose raw internal eligibility datasets, unsafe operational controls, or internal-only audit lineage in unrestricted form.

### Principle

Public transparency APIs are one of the main ways FUZE converts transparency architecture into accessible, stable external interfaces.

---

## Async Job Model in Public APIs

Many public product and reporting interactions in FUZE will be asynchronous.

The public API layer should therefore support a standard async pattern for external consumers.

### Standard pattern
1. submit a request
2. receive `accepted` status with job or request ID
3. check status through a status endpoint or callback pattern
4. retrieve final result or failure details
5. access any resulting artifact through authorized result endpoints

### Public async use cases may include:
- AI-heavy analysis requests
- report generation
- HerHelp generation flows
- Botmad scan jobs
- large export generation
- payout-related statement generation if exposed

### Principle

Public consumers should have a consistent mental model for long-running operations across products, not a different custom pattern for every async endpoint family.

---

## Public Error and Response Semantics

Public APIs should use consistent, stable response and error semantics.

At minimum, public responses should make clear:

- whether the request succeeded, was accepted, or failed
- whether the returned resource is canonical, derived, or artifact-oriented
- what correlation or request reference applies
- what pagination or continuation applies where relevant
- what next action is expected for async flows

Public errors should preserve:

- machine-readable error code
- human-readable message
- retry guidance where applicable
- authorization or validation distinction
- correlation reference

### Important principle

Public API consumers should not need platform-internal knowledge to interpret ordinary success and failure paths.

This is particularly important because external consumers often have less context than internal services and less tolerance for ambiguous error handling.

---

## Rate Limiting, Abuse Prevention, and Public Stability Controls

Public APIs require stronger boundary protection than internal APIs.

At minimum, FUZE public APIs should support:

- rate limits by endpoint family
- actor- or token-based throttling
- abuse detection and blocking
- replay protection where relevant
- idempotency for selected public write actions
- input validation hardening
- public-surface-specific timeout and pagination controls

### Why this matters

Public APIs are exposed to:
- accidental misuse
- integration bugs
- scraping pressure
- denial patterns
- abusive automation
- unexpected client diversity

### Principle

A stable public API is not only well designed. It is well protected.

This is especially important for:
- credits and billing entry points
- public payout and transparency surfaces
- async job submission APIs
- authenticated workspace-sensitive operations

---

## Versioning and Backward Compatibility for Public APIs

Public APIs should follow the strongest compatibility discipline in the FUZE interface model.

At minimum:

- public APIs should use explicit versioning
- breaking changes should be rare and deliberate
- deprecation windows should be visible
- resource shape evolution should prefer additive change where possible
- version-specific documentation and client expectations should be maintained
- public event/webhook contracts should also follow compatibility rules

### Principle

Once an interface is public, FUZE owes external consumers a stronger stability promise than it owes to most internal APIs.

This matters because public API instability becomes an ecosystem trust problem, not only a developer inconvenience.

---

## Public API Documentation and Discoverability

A public API is not complete unless it is documented clearly enough for external consumers to use it safely.

At minimum, public API documentation should support:

- endpoint purpose and surface family
- auth requirements
- scope rules
- request and response examples
- error codes
- async patterns where relevant
- rate-limit expectations
- versioning information
- deprecation notes where applicable
- whether a resource is canonical public artifact, public report artifact, or derived summary model

### Principle

Discoverability and clarity are part of API quality. A public API that is technically powerful but poorly documented will still create support burden and misinterpretation risk.

---

## Auditability and Public API Event Lineage

Public API operations should preserve strong traceability into internal audit systems.

At minimum, public API requests should support linkage to:

- request or correlation ID
- actor identity
- workspace or account scope
- product context
- async job reference
- billing or credits mutation reference where relevant
- governance or payout-cycle reference where relevant
- audit event lineage

### Principle

Public APIs are part of the platform’s trust surface. The platform should be able to explain what happened when public actions fail, succeed, or are disputed.

This is particularly important for:
- commercial operations
- payout-related reads and claim flows
- wallet link operations
- product request submissions
- public registry or report publication pipelines

---

## Public API Failure and Degraded Modes

FUZE public APIs should degrade in ways that preserve trust and interpretability.

Examples:

- public transparency reads may stay available even if some product mutation paths are degraded
- async job submission may remain available while completion latency increases
- payout-cycle visibility may remain readable even if claim execution is temporarily paused
- public product metadata may remain available while authenticated product actions are restricted
- report publication may lag without redefining canonical cycle or reserve truth

### Principle

Degraded mode should preserve the difference between:
- visibility failure,
- execution delay,
- and canonical truth mutation.

Public APIs should communicate degraded states explicitly rather than fail ambiguously where possible.

---

## Minimum Architectural Entities

At minimum, the FUZE public API architecture should recognize the following conceptual entities:

### Public Surface Entities
- `public_api_surface_id`
- `surface_family`
- `visibility_class`
- `version_reference`
- `documentation_reference`

### Request/Response Entities
- `request_id`
- `correlation_id`
- `actor_reference`
- `scope_reference`
- `operation_type`
- `response_status`
- `error_code` where applicable

### Async Public Operation Entities
- `job_id`
- `operation_status`
- `accepted_at`
- `completed_at`
- `failed_at`
- `result_reference`

### Linkage and Integrity Entities
- `audit_lineage_reference`
- `idempotency_key` where applicable
- `credits_reference` where applicable
- `payout_cycle_reference` where applicable
- `registry_reference` where applicable
- `transparency_report_reference` where applicable

These are minimum conceptual entities. Detailed public schemas are refined downstream.

---

## Open Items

The following areas are intentionally refined in downstream design and implementation:

- exact public route families and resource layout
- exact auth credential models for partner APIs
- exact public documentation system and SDK strategy
- exact public rate-limit policies by endpoint family
- exact public claim-status and payout-surface detail level
- exact distinction between first-party app endpoints and permanently supported public endpoints
- exact public API monetization or quota model if introduced later

These do not weaken the canonical public API architecture established here.

---

## Closing Summary

The FUZE public API architecture defines the external-facing interface layer of a multi-product, transparency-first platform ecosystem. It exposes intentionally safe, useful, and supportable capabilities while protecting canonical ownership boundaries, internal orchestration logic, governance-sensitive controls, treasury-sensitive systems, and private product state. By distinguishing public surfaces from first-party and internal interfaces, using stronger stability and security rules, and supporting transparency-, payout-, registry-, and product-oriented external access in a disciplined way, FUZE creates a public API model that is open where appropriate and bounded where necessary.
