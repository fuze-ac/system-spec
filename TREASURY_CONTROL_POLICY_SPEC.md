# FUZE Treasury Control Policy Specification

## Document Metadata

- **Document Name:** `TREASURY_CONTROL_POLICY_SPEC.md`
- **Document Type:** Canonical refined system specification
- **Status:** Active refined system spec
- **Version:** 2.0.0
- **Effective Date:** 2026-04-23
- **Last Updated:** 2026-04-23
- **Reviewed On:** 2026-04-23
- **Document Owner:** FUZE Treasury Control Policy Domain (canonical owner for treasury-control semantics, reserve-category-aware control posture, treasury-sensitive action meaning, approval-path discipline, destination and use restriction interpretation, and treasury-specific correction/supersession lineage); named individual owner is not explicitly specified in the retrieved governing materials
- **Approval Authority:** Not explicitly specified in the retrieved governing materials; constitutional approval authority remains governed by `REFINED_SYSTEM_SPEC_INDEX.md` and the active FUZE approval workflow
- **Review Cadence:** SHOULD be reviewed quarterly and whenever treasury architecture, Foundation boundaries, vault-action posture, multisig/timelock posture, payout funding posture, transparency posture, or public-trust-sensitive reserve treatment materially changes
- **Governing Layer:** Platform governance / treasury-governed capital control architecture / category-aware treasury policy layer
- **Parent Registry:** `REFINED_SYSTEM_SPEC_INDEX.md`
- **Primary Audience:** Platform architecture, backend engineering, contracts engineering, treasury and finance stakeholders, governance/control-plane authors, security engineering, audit/compliance, public-trust/reporting authors, implementation-contract authors
- **Primary Purpose:** Define the canonical FUZE treasury control policy as the category-aware policy-and-control layer for treasury-governed capital, including reserve-sensitive action classification, approval-path posture, destination and use restriction interpretation, payout-funding treatment, exceptional treasury handling, and public-trust-safe reporting linkage, without collapsing treasury-control truth into Foundation stewardship truth, vault-action truth, multisig/timelock enforcement truth, profit-participation truth, contract execution truth, or generic operator discretion
- **Primary Upstream References:** `REFINED_SYSTEM_SPEC_INDEX.md`, `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`, `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`, `PLATFORM_ARCHITECTURE_SPEC.md`, `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`, `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`, `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`, `GOVERNANCE_MODEL_SPEC.md`, `FOUNDATION_GOVERNANCE_SPEC.md`, `VAULT_ACTION_POLICY_SPEC.md`, `MULTISIG_AND_TIMELOCK_SPEC.md`, `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`, `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`, `TRANSPARENCY_MODEL_SPEC.md`, `TRANSPARENCY_REPORTING_SPEC.md`, `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`, `CHAIN_ARCHITECTURE_SPEC.md`, `FUZE_WHITEPAPER_v.2026.3.0.1.pdf`
- **Primary Downstream Dependents:** `TREASURY_CONTROL_POLICY_API_SPEC.md`, `VAULT_ACTION_POLICY_SPEC.md`, `FOUNDATION_GOVERNANCE_SPEC.md`, `MULTISIG_AND_TIMELOCK_SPEC.md`, `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`, `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`, `TRANSPARENCY_REPORTING_SPEC.md`, public-safe treasury reporting surfaces, treasury-specific admin/control-plane tooling, discrepancy and correction runbooks
- **Supersedes:** Earlier or weaker interpretations that treat treasury-controlled capital as one flexible pool, treat separated reserve categories as practically interchangeable, collapse Foundation stewardship into treasury convenience, or allow material treasury-sensitive actions to proceed without category-aware policy, bounded authority, destination/use restriction clarity, reporting lineage, and meaningful proposal/approval/execution separation
- **Superseded By:** None currently defined
- **Related Decision Records:** Not explicitly specified in the retrieved governing materials
- **Canonical Status Note:** This document is the canonical governing FUZE specification for treasury-control policy semantics. Downstream APIs, vault policies, Foundation linkage rules, multisig/timelock enforcement layers, profit-participation funding workflows, reporting surfaces, and treasury-sensitive admin/control-plane tools MUST preserve the ownership, truth-separation, reserve-category meaning, action classification, approval, restriction, correction, and visibility rules defined here.
- **Implementation Status:** Normative source; downstream treasury-control services, vault and execution controls, reporting linkages, and chain-adjacent execution references MUST align
- **Approval Status:** Draft refined canonical specification pending explicit approval workflow
- **Change Summary:** Refined treasury control into a production-grade canonical specification; normalized treasury control as a distinct governance domain, clarified reserve-category-aware action semantics, formalized action classes and sensitivity tiers, strengthened proposal/approval/execution separation, tightened destination and use restrictions, made payout-funding treatment explicit, reinforced Foundation separation, and defined correction/supersession lineage and public-safe reporting guardrails

## Title

FUZE Treasury Control Policy Specification

## Purpose

This specification defines the canonical FUZE treasury control policy.

Its purpose is to make explicit:

- what treasury control governs and what it does not govern
- how treasury-controlled capital is classified, bounded, authorized, restricted, executed by reference, reviewed, and reported
- how reserve-category meaning is preserved across Treasury Reserve, Foundation, incentives, partnerships, liquidity, transparency/stability, and treasury-adjacent vesting structures
- how treasury control relates to governance, Foundation stewardship, vault-action policy, multisig/timelock enforcement, payout funding, transparency, and public trust without replacing those domains
- how action classes, sensitivity tiers, destination restrictions, exceptional handling, and correction/supersession must work
- what downstream APIs, policies, reports, admin surfaces, and chain-adjacent execution layers MUST preserve

This specification is intentionally governing rather than descriptive. It defines treasury control as the operational governance layer between reserve architecture and real treasury behavior.

## Scope

This specification governs:

- the canonical control philosophy for treasury-governed capital in FUZE
- how treasury authority is bounded across reserve categories
- the distinction between treasury control and ordinary platform administration
- treasury-sensitive action classes and sensitivity tiers
- approval, authorization, and execution-linkage principles for treasury-sensitive actions
- destination and use restriction posture
- the relationship between treasury control, multisig, timelock, policy, and reporting
- the boundaries between Treasury Reserve, Foundation, incentives, partnerships, liquidity, transparency/stability, and treasury-adjacent vesting categories
- correction, emergency, and exceptional treasury treatment
- transparency, audit, and reconciliation expectations for treasury-controlled actions
- implementation-contract guardrails for treasury APIs, events, admin controls, reporting, and chain-adjacent execution references

It does not govern:

- full smart-contract implementation of each vault
- exact allowed-use matrix by reserve category in contract detail
- full Foundation-governance rules in depth
- full multisig signer or timelock configuration internals
- exact payout-cycle execution mechanics
- exact transparency-report composition
- low-level contract ABI implementation details

## Design Goals

The design goals of the FUZE treasury control policy are to:

1. prevent treasury-controlled capital from being treated as broad discretionary operator capital
2. preserve the distinct economic meaning of each treasury-related reserve category
3. define clear action classes and sensitivity posture for treasury-sensitive decisions
4. require explicit authorization pathways for material treasury actions
5. make treasury behavior auditable, reportable, and structurally explainable
6. support strong separation between governance authority, execution authority, and reporting authority
7. create safer handling for exceptional or emergency treasury cases
8. improve long-term public trust by making treasury control visible and bounded

## Non-Goals

This specification is not intended to:

- make treasury policy identical across all reserve categories
- treat all treasury-related actions as equally sensitive
- collapse Foundation governance into ordinary treasury control
- define treasury as a generic source of liquidity for any platform need
- allow control policy to be overridden casually by product urgency
- replace contract-level security controls with pure procedural trust
- make reporting optional for material treasury-sensitive actions

## Core Principles

### 1. Canonical Treasury Control Principle
Treasury-controlled capital may be activated only through explicit category-aware policy, bounded authority, and auditable governance pathways that preserve the intended economic meaning of the capital being controlled.

### 2. Treasury Control as Distinct Governance Domain Principle
Treasury-sensitive actions MUST be governed through a distinct control discipline closer to governance architecture than to routine product operations.

### 3. Category-Aware Control Principle
“Treasury” is not one undifferentiated pool of power. The same broad control philosophy may apply across the architecture, but the permitted action space MUST differ by category.

### 4. Proposal-Approval-Execution Separation Principle
Proposal, approval, and execution MUST remain meaningfully separated for material treasury-sensitive actions whenever feasible.

### 5. Destination-and-Use Discipline Principle
A treasury action is not justified only because its immediate use appears reasonable. It must also preserve the structural clarity of where the value is going and why that destination fits the reserve category.

### 6. Foundation-Boundary Preservation Principle
Treasury control MUST reinforce, not weaken, Foundation governance boundaries.

### 7. Payout-Funding Distinction Principle
Funding a payout cycle is treasury-sensitive, but it is not equivalent to routine treasury deployment. It is a formal ecosystem event with tighter transparency, holder-facing, and reporting consequences.

### 8. Timelock-and-Multisig Support Principle
Timelock and multisig are enforcement and control instruments that help implement treasury policy. They do not replace treasury policy meaning.

### 9. Exceptional-Treasury Narrowness Principle
Emergency pathways are necessary, but they must remain narrower than ordinary deployment authority, explicitly documented, and followed by structured review.

### 10. Reporting-Is-Part-of-Control Principle
A treasury action that cannot be explained clearly after execution was probably not governed clearly enough before execution.

## Canonical Definitions

### Treasury-Controlled Capital
Capital governed under FUZE treasury-control policy, including treasury-governed and treasury-adjacent reserve categories with category-specific constraints.

### Reserve Category
The canonical category used to interpret the purpose, allowable action space, reporting posture, and trust meaning of a treasury-controlled capital pool.

### Treasury-Sensitive Action
A control-relevant action affecting treasury-controlled capital, treasury configuration, treasury-linked payout funding, or reserve-category interpretation.

### Action Class
A governed category of treasury-sensitive action, such as observation/reporting, administrative configuration, internal coordination, category deployment, payout-related funding, or exceptional/emergency action.

### Sensitivity Tier
The risk and trust significance level attached to a treasury-sensitive action, used to determine required review quality, separation, timelock/multisig expectations, and reporting discipline.

### Destination Category
The canonical classification of where treasury-controlled capital is intended to move or what it will affect.

### Treasury Policy Version
The canonical policy record describing active treasury-control rules and their version lineage.

### Treasury Control Reference
A structured reference to the bounded control mechanism associated with a treasury-sensitive action, such as multisig, timelock, emergency authority, or exceptional path.

### Treasury Execution Reference
A bounded reference to the downstream execution artifact or pathway linked to an approved treasury action.

### Treasury Reporting Reference
A structured reference to the reporting, registry, or payout artifact linked to a treasury-sensitive action.

## Truth Class Taxonomy

The treasury-control domain operates with the following truth classes, which MUST remain distinct:

1. **Treasury-control truth** — treasury policy versions, treasury-sensitive action records, reserve-category interpretations, action classes, sensitivity tiers, destination/use restrictions, and correction/supersession semantics
2. **General governance truth** — higher-order governance-model semantics, approval-path posture, and cross-domain governance architecture
3. **Foundation-governance truth** — Foundation-specific stewardship, principal-treatment, and stronger constraints
4. **Vault-action truth** — what each vault category may meaningfully do
5. **Execution truth** — downstream contract or operational execution state linked to a treasury action
6. **Profit-participation truth** — payout and eligibility-system meaning, including treasury funding only where explicitly referenced
7. **Reporting / public-visibility truth** — public-safe treasury summaries, transparency references, registry references, and payout linkages
8. **Audit truth** — immutable audit and activity records for treasury-sensitive mutations
9. **Runtime / execution-plane truth** — retries, async jobs, export jobs, discrepancy cases, and operational remediation state
10. **Presentation truth** — labels, summaries, status wording, and explanatory framing

These truth classes MUST NOT be collapsed into one undifferentiated treasury dashboard or reserve page.

## Architectural Position in the Spec Hierarchy

This document sits below:

- `REFINED_SYSTEM_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `GOVERNANCE_MODEL_SPEC.md`

and above or alongside:

- `TREASURY_CONTROL_POLICY_API_SPEC.md`
- `FOUNDATION_GOVERNANCE_SPEC.md`
- `VAULT_ACTION_POLICY_SPEC.md`
- `MULTISIG_AND_TIMELOCK_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`
- public-safe treasury reporting surfaces
- treasury-specific admin/control-plane tooling

This document governs treasury-control semantics. It does not redefine narrower source-domain truth.

## System Boundaries

This document governs only the following platform-owned boundaries:

- treasury-control philosophy and reserve-category-aware control meaning
- treasury-sensitive action classes and sensitivity-tier semantics
- proposal, approval, execution-linkage, and reporting-linkage posture for treasury-sensitive actions
- destination and use restriction interpretation
- treasury-specific exceptional treatment and discrepancy posture
- treasury correction, supersession, and historical-intelligibility posture
- public-safe treasury reporting linkage and implementation-contract guardrails

It does not govern:

- Foundation-specific principal-lock rules in full depth
- vault-specific allow/deny matrices in full depth
- multisig signer and timelock parameter details
- exact contract-execution logic
- raw accounting-book truth
- full payout execution semantics
- public transparency-report composition in full depth

## Adjacent Boundaries

### Governance Model
The governance model owns higher-order governance semantics, scope classification, approval-path posture, and future participation boundaries. Treasury control is a narrower governance domain for treasury-governed capital.

### Foundation Governance
Foundation governance owns the stricter stewardship posture for Foundation-controlled value. Treasury control must reinforce, not weaken, those boundaries.

### Vault Action Policy
Vault-action policy governs what each vault may meaningfully do. Treasury control governs how treasury-sensitive actions are controlled.

### Multisig and Timelock
Multisig and timelock own their narrower enforcement and queued-action/control-path semantics. Treasury control determines what kinds of actions deserve those protections and under what circumstances.

### Profit Participation and Eligibility
Profit-participation and eligibility domains own payout-cycle and claim-related truth. Treasury control governs how payout-cycle funding is treated as a treasury-sensitive action and how it links to reporting and payout-ledger structures.

### Transparency and Reporting
Transparency and reporting domains own public-trust interpretation and recurring public reporting. Treasury control owns the structural meaning that must be explainable and reportable.

### Public Registry
The public registry owns public designation truth for official contracts and wallets. Treasury control may link to registry artifacts where treasury-sensitive actions require trust-safe reference.

## Conflict Resolution Rules

When materials, systems, or interpretations conflict, FUZE MUST resolve them in the following order:

1. the active refined registry and higher constitutional materials win over narrower documents
2. top-level boundary, ownership, and architecture specs win on top-level ownership and boundary posture
3. `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md` wins on chain-native versus off-chain policy/reporting separation
4. narrower source-domain specifications win on the meaning of their canonical truths
5. `GOVERNANCE_MODEL_SPEC.md` wins on higher-order governance semantics, scope classification, approval-path posture, and DAO-lite future-direction boundaries
6. `FOUNDATION_GOVERNANCE_SPEC.md`, `VAULT_ACTION_POLICY_SPEC.md`, and `MULTISIG_AND_TIMELOCK_SPEC.md` win within their narrower policy or enforcement scope
7. this document wins on treasury-control semantics, reserve-category-aware action meaning, destination/use restrictions, payout-funding treatment, treasury-specific exceptional handling, and treasury correction/supersession semantics
8. public dashboards, reserve pages, summaries, exports, and API views never win over canonical treasury-control truth
9. when ambiguity remains, FUZE MUST choose the more conservative trust-preserving interpretation and escalate the ambiguity into downstream refinement or recorded decision work

## Default Decision Rules

When no narrower approved exception exists, FUZE MUST default to the following:

1. treasury authority is not a blanket right to move ecosystem capital
2. reserve categories retain their role-specific meaning by default
3. ambiguous action classes default to narrower or higher-sensitivity treatment
4. ambiguous destination or use rationale defaults to review-required or disallowed treatment rather than silent interpretation
5. payout-cycle funding defaults to treasury-sensitive formal-event treatment rather than routine treasury deployment treatment
6. Foundation-sensitive flows default to stronger restraint than ordinary treasury behavior
7. public visibility defaults to public-safe structural explanation rather than full internal exposure
8. exceptional treasury treatment defaults to narrower duration and mandatory post-review rather than standing shortcut authority
9. if reserve category, action class, sensitivity tier, policy version, or approval path cannot be identified, the action is incomplete and MUST NOT proceed
10. treasury control MUST resist pressure to use separated reserves as one flexible pool

## Roles / Actors / Entities

### Human Actors
- treasury and finance stakeholders
- governance reviewers
- privileged operators
- Foundation stewards where applicable
- security and incident operators
- public-trust/reporting authors

### System Actors
- treasury-control service
- governance model service
- vault-action services
- Foundation governance service
- multisig/timelock control services
- contract execution services
- profit-participation and payout-linkage services
- reporting and transparency services
- audit and monitoring systems
- discrepancy and correction tooling

### Canonical Entities
- treasury policy versions
- treasury action records
- treasury action categories
- treasury action destinations
- treasury action approval paths
- treasury action control references
- treasury action execution references
- treasury action reporting references
- treasury exception records
- treasury discrepancy cases
- treasury mutation actions

## Ownership Model

The Treasury Control Policy domain is the canonical owner of:

- treasury-control philosophy and reserve-category-aware control semantics
- treasury-sensitive action lifecycle semantics
- action class and sensitivity-tier meaning
- destination and use restriction interpretation
- treasury-specific proposal/approval/execution-linkage posture
- treasury correction, discrepancy, and supersession semantics
- treasury-owned public-safe read and reporting-linkage source truth

It is not the canonical owner of:

- Foundation stewardship truth
- vault-specific allowed-action truth
- multisig or timelock implementation internals
- payout-cycle execution truth
- raw contract execution truth
- final transparency-report truth
- raw accounting exports

## Authority / Decision Model

Authority MUST be separated as follows:

- the governance model domain determines higher-order governance semantics
- the treasury-control domain determines how treasury-sensitive actions are classified, reviewed, approved, constrained, and historically recorded
- vault, Foundation, payout, and execution domains own the resulting narrower action or execution truth in their own scope
- multisig and timelock layers enforce bounded execution discipline where applicable
- reporting and transparency domains determine how treasury meaning is surfaced publicly under public-safe rules

No dashboard, static page, spreadsheet, or product-local flow may mint canonical treasury-control truth outside these boundaries.

## State Model

### Treasury Policy Version Lifecycle
- `draft`
- `active`
- `deprecated`
- `superseded`
- `archived`

### Treasury Action Lifecycle
- `draft`
- `proposed`
- `under_review`
- `approved`
- `rejected`
- `ready_for_execution`
- `executed_reference_linked`
- `reported`
- `paused`
- `superseded`
- `closed`

### Approval-Path Lifecycle
- `proposal_recorded`
- `approval_pending`
- `approved`
- `rejected`
- `execution_linked`
- `closed`

### Exceptional-Action Lifecycle
- `declared`
- `containment_active`
- `post_review_pending`
- `closed`
- `superseded`

### Discrepancy Lifecycle
- `opened`
- `under_review`
- `resolved`
- `failed`
- `closed`

## Lifecycle / Workflow Model

1. a treasury-sensitive change is identified and classified by reserve category, action class, sensitivity tier, destination class, and policy version
2. a treasury proposal is recorded with source-domain references
3. required approval path and control references are attached
4. the action is approved, rejected, paused, escalated, or exceptionally handled under bounded policy
5. if approved, a treasury execution reference is linked to the downstream execution pathway
6. public-safe reporting, registry, or payout linkage occurs where policy requires
7. corrections, supersessions, or discrepancy cases are handled with preserved lineage
8. any exceptional treasury action enters mandatory post-review and closure discipline

## Invariants

1. treasury control remains a distinct governance domain
2. separated reserve categories remain semantically meaningful
3. treasury-sensitive actions remain explicitly classified
4. approval remains distinct from execution completion
5. destination and use restrictions remain explicit
6. Foundation-sensitive flows remain more conservative than ordinary treasury behavior
7. payout-cycle funding remains distinct from routine treasury deployment
8. public-safe treasury visibility does not authorize unsafe disclosure
9. exceptional treasury handling remains narrow, reason-bearing, and reviewable
10. corrections and supersessions preserve historical intelligibility

## Functional Rules

### Treasury Policy Layer Rule
Treasury architecture alone is not sufficient to create trust. Treasury control policy is the rule layer that gives operational meaning to the reserve architecture.

### Distinct Treasury Governance Rule
Treasury-sensitive actions MUST NOT be governed through the same informal pathways used for routine product operations, content changes, or day-to-day platform administration.

### Category-Specific Philosophy Rule
At minimum, Treasury Reserve, Foundation, Holder Incentives, Ecosystem Partnership, Liquidity Operations, Transparency/Stability, Team Vesting, and Advisor Vesting MUST remain structurally distinguishable and must not all share the same permitted action space.

### Action-Class Rule
Treasury control MUST distinguish at least observation/reporting, administrative configuration, internal coordination, category deployment, payout-related funding, and exceptional/emergency actions.

### Sensitivity-Tier Rule
Treasury control MUST distinguish at least low, moderate, high, and critical sensitivity tiers, with stronger requirements for approval structure, role separation, timelock, reporting, and audit traceability as sensitivity increases.

### Approval Rule
The correct treasury question is not merely “can we do this?” It is “should this reserve category do this under the architecture FUZE has publicly defined?”

### Destination Principle
A treasury action must preserve the structural clarity of where the value is going and why that destination fits the reserve category.

### Foundation Boundary Rule
Foundation policy should not be silently overridden through ordinary treasury procedure.

### Profit Participation Funding Rule
Profit-participation funding should occur only after accounting and treasury finalize the policy-defined distributable amount, eligibility preparation is ready, and the cycle is properly authorized.

### Exceptional Treasury Rule
Emergency pathways MUST prioritize containment over convenience, remain narrower than ordinary deployment authority, and require post-incident review and reporting.

## Permission / Access Considerations

Treasury mutation authority is privileged and bounded.

Required constraints:

- privileged treasury actions require explicit operator identity, least-privilege access, and reason-coded execution
- creation, review, approval, pause, escalation, exceptional treatment, supersession, and discrepancy resolution MUST remain access-controlled
- public-safe treasury views are distinct from internal treasury records
- vault or product operators do not automatically gain treasury-control mutation authority outside bounded roles
- access failures on treasury-sensitive mutation paths MUST fail closed

## Entitlement Considerations

Entitlement is not a primary treasury-control owner.

Rules:

- entitlement MUST NOT redefine treasury truth
- token status or product entitlement MUST NOT be treated as automatic treasury authority
- community-facing treasury visibility does not imply mutation authority
- any future participation-linked features touching treasury meaning MUST remain bounded by explicit governance policy and activation

## API / Contract Implications

The treasury-control layer MUST expose stable contracts for:

- internal creation and read of treasury policy versions and treasury-sensitive actions
- reserve-category, action-class, sensitivity-tier, destination, and use restriction interpretation
- proposal, approval-path, control-reference, execution-reference, and reporting-reference recording
- privileged approve, reject, pause, escalate, exceptional-handle, supersede, and discrepancy-resolution actions
- public-safe treasury policy summaries and treasury-action summaries where approved

## Event / Async Implications

The treasury-control domain SHOULD support event families such as:

- `treasury_control.action_created`
- `treasury_control.destination_validated`
- `treasury_control.approval_path_recorded`
- `treasury_control.control_linked`
- `treasury_control.reporting_linked`
- `treasury_control.execution_linked`
- `treasury_control.action_approved`
- `treasury_control.action_rejected`
- `treasury_control.action_paused`
- `treasury_control.action_escalated`
- `treasury_control.exception_declared`
- `treasury_control.action_superseded`
- `treasury_control.discrepancy_resolved`

## Data Model / Storage Implications

At minimum, the treasury-control domain SHOULD support semantic representation of:

- `treasury_policy_versions`
- `treasury_action_records`
- `treasury_action_categories`
- `treasury_action_destinations`
- `treasury_action_approval_paths`
- `treasury_action_control_references`
- `treasury_action_reporting_references`
- `treasury_action_execution_references`
- `treasury_exception_records`
- `treasury_discrepancy_cases`
- `treasury_mutation_actions`

Derived stores MAY include public treasury policy views, public-safe treasury action views, internal status views, and discrepancy dashboards, provided they remain subordinate to canonical treasury truth.

## Read Model / Projection / Reporting Rules

- public-safe treasury policy summaries and treasury-action summaries are derived views
- internal status dashboards are derived views
- public summaries MUST NOT overtake canonical treasury records
- reporting, registry, and payout linkages remain references, not ownership transfers
- public or internal views MAY lag operationally, but they MUST remain reconcilable to canonical treasury records and stronger source domains

## Security / Risk / Abuse Controls

The treasury-control domain is a trust-sensitive control surface and MUST enforce:

- strict separation between treasury-control truth and downstream execution truth
- protection against unauthorized approval, category drift, silent history rewrite, misclassification, or unsafe public exposure
- bounded service-to-service mutation pathways
- privileged reason-coded treasury actions
- safe handling of exceptional treasury treatment, restriction, and correction events
- discrepancy detection when treasury meaning and downstream reporting or execution references drift
- protection against treasury-sensitive actions hiding inside ordinary runtime or product-local pathways

## Boundary Violation Detection / Non-Canonical Patterns

The following patterns are non-canonical and forbidden:

- treating separated reserves as one flexible pool in practice
- treating treasury authority as a blanket right to move ecosystem capital
- collapsing Foundation governance into ordinary treasury control
- using separated reserves as generic backup capital for any platform need
- funding payout cycles as though they were routine treasury deployment
- routing treasury-controlled capital toward destinations inconsistent with reserve meaning
- letting dashboards, exports, or narrative pages become write owners of treasury meaning
- silently rewriting treasury classification or treatment without discrepancy or correction lineage

## Audit / Traceability Requirements

FUZE MUST be able to report on:

- who or what proposed, approved, rejected, paused, escalated, exceptionally handled, superseded, or corrected a treasury-sensitive action
- what reserve category, action class, sensitivity tier, destination class, and policy version applied
- what approval path and control references applied
- what downstream execution reference, if any, was linked
- what reporting, registry, or payout linkage exists
- what discrepancy or correction lineage exists
- which policy or ruleset version governed the decision

## Failure Handling / Edge Cases

- if a proposal exists but the approval path is incomplete, the action remains incomplete and MUST NOT proceed
- if approval exists but execution fails, treasury approval remains true while execution truth remains separate and failure-bearing
- if a public treasury summary lags behind internal action, the public summary is a derived view and must reconcile rather than redefine internal treasury truth
- if payout funding posture is unclear, the action MUST remain unresolved until policy-defined distributable amount, readiness, and authorization posture are explicit
- if an exceptional treasury action is taken, exceptional handling MUST preserve explicit declaration, bounded treatment, and post-review requirements
- if reserve category or destination is misclassified, a discrepancy case MUST be opened and resolved with preserved lineage rather than silent relabeling

## Operational Considerations

Operational systems SHOULD support:

- deterministic treasury-action recording and lifecycle handling
- reserve-category and destination validation
- approval-path completeness checks
- discrepancy detection between treasury truth and downstream execution/reporting references
- bounded emergency handling with post-review
- health monitoring for public-safe treasury views and internal control surfaces
- runbooks for misclassification, incomplete review paths, payout-funding ambiguity, supersession propagation failure, public-summary lag, and exceptional-treasury review closure

## Migration / Compatibility / Supersession Considerations

Migrations into this canonical model MUST enforce the following:

- legacy approvals or ad hoc admin sign-offs that act as hidden treasury-control truth must be retired or normalized
- historical treasury labels MAY be preserved as lineage, but MUST NOT silently override canonical current interpretation
- compatibility layers MAY preserve older route shapes or admin screens temporarily, but canonical treasury semantics MUST remain explicit
- payout-funding treatment and reporting linkage MUST migrate from implicit assumptions to explicit references
- downstream consumers MUST migrate to canonical treasury truth rather than local shadow approval stores or narrative pages

## Implementation-Contract Guardrails

1. treasury control remains distinct from source-domain truth and downstream execution truth
2. reserve category, action class, sensitivity tier, and policy version remain explicit
3. proposal, approval, and execution remain distinct lifecycle concepts
4. destination and use restrictions remain explicit
5. Foundation-sensitive flows remain more conservative than ordinary treasury behavior
6. payout-funding treatment remains explicit
7. public-safe treasury visibility remains bounded and distinct from internal treasury detail
8. exceptional treasury handling remains narrow and reviewable
9. historical treasury meaning and correction/supersession lineage remain preserved
10. public summaries, APIs, and dashboards remain derived from canonical treasury truth
11. idempotency, replay safety, and correction lineage remain explicit for trust-sensitive mutations
12. downstream teams MUST NOT create local treasury-control truth that conflicts with this domain

## Downstream Execution Staging

1. stabilize reserve-category taxonomy, action-class semantics, and sensitivity-tier semantics
2. stabilize canonical treasury policy, action, destination, and approval-path semantics
3. stabilize control-reference, execution-reference, and reporting-reference behavior
4. integrate Foundation, vault, payout, and multisig/timelock linkage rules
5. integrate public-safe treasury summaries and transparency/reporting linkages
6. integrate exceptional handling, discrepancy, correction, and remediation tooling

## Required Downstream Specs / Contract Layers

- `TREASURY_CONTROL_POLICY_API_SPEC.md`
- `VAULT_ACTION_POLICY_SPEC.md`
- `FOUNDATION_GOVERNANCE_SPEC.md`
- `MULTISIG_AND_TIMELOCK_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- treasury discrepancy/remediation runbooks
- public-safe treasury reporting contracts
- payout-linkage contracts where applicable

## Canonical Examples / Anti-Examples

### Canonical Examples
- Category-Aware Treasury Reserve Deployment
- Foundation Boundary Reinforcement
- Payout-Related Funding as Formal Ecosystem Event
- Transparent Post-Execution Explanation

### Anti-Examples
- Omnibus Treasury Behavior
- Category Drift Through “Reasonable Exceptions”
- Treasury as Generic Liquidity Source
- Silent Rewrite of Treasury Meaning

## Dependencies / Cross-Spec Links

This specification depends on:
- `REFINED_SYSTEM_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `GOVERNANCE_MODEL_SPEC.md`
- `FOUNDATION_GOVERNANCE_SPEC.md`
- `VAULT_ACTION_POLICY_SPEC.md`
- `MULTISIG_AND_TIMELOCK_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
- `TRANSPARENCY_MODEL_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`
- `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
- `CHAIN_ARCHITECTURE_SPEC.md`
- `FUZE_WHITEPAPER_v.2026.3.0.1.pdf`

## Explicitly Deferred Items

- exact allowed-use matrix per reserve category
- exact quorum thresholds by action class and sensitivity
- exact timelock application by action category
- exact emergency-action escalation and rollback procedures
- exact reporting disclosure depth for treasury-sensitive actions
- exact residual-handling rules for failed or partially executed treasury actions

## Final Normative Summary

The FUZE treasury control policy defines how treasury-governed capital may be proposed, approved, executed by reference, and reported without eroding the meaning of the reserve architecture. It treats treasury control as a distinct governance domain, uses category-aware action classes and sensitivity tiers, reinforces separation between Treasury, Foundation, incentives, partnerships, liquidity, stability, and treasury-adjacent vesting functions, and requires policy-linked, auditable, and transparent handling of material treasury actions. By making treasury authority explicit and bounded, FUZE strengthens one of the most important trust layers in the ecosystem.

## Quality Gate Checklist

- Canonical owner is explicit for treasury-control truth, action classification truth, and correction/supersession semantics.
- Mutation boundaries are explicit for internal services, control-plane tools, and derived public surfaces.
- Adjacent boundaries are explicit for governance model, Foundation, vault, multisig/timelock, payout, transparency, registry, and on-chain/off-chain domains.
- Truth classes are explicit and separated.
- Conflict-resolution rules are explicit.
- Default decision rules are explicit for category ambiguity, destination ambiguity, payout-funding ambiguity, exceptional handling, and Foundation separation.
- Non-canonical patterns are called out clearly.
- Operator/admin override paths are bounded, reason-coded, and auditable.
- Read-model, reporting, and public-summary rules are explicit.
- Failure and degraded-mode behaviors are explicit.
- Downstream implementation guardrails are explicit.
- Dependencies and downstream impacts are explicit.
- Non-goals and deferred items are explicit.
- The document is strong enough to support downstream API, backend, control-plane, reporting, and chain-adjacent implementation without contradictory semantic invention.
