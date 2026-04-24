# FUZE Vault Action Policy Specification

## Document Metadata

- **Document Name:** `VAULT_ACTION_POLICY_SPEC.md`
- **Document Type:** Canonical refined system specification
- **Status:** Active refined system spec
- **Version:** 2.0.0
- **Effective Date:** 2026-04-23
- **Last Updated:** 2026-04-23
- **Reviewed On:** 2026-04-23
- **Document Owner:** FUZE Vault Action Policy Domain (canonical owner for vault-purpose semantics, category-aware action allowance semantics, destination and timing legibility posture, vault-sensitive action meaning, and vault-policy correction/supersession lineage); named individual owner is not explicitly specified in the retrieved governing materials
- **Approval Authority:** Not explicitly specified in the retrieved governing materials; constitutional approval authority remains governed by `REFINED_SYSTEM_SPEC_INDEX.md` and the active FUZE approval workflow
- **Review Cadence:** SHOULD be reviewed quarterly and whenever treasury-control posture, Foundation stewardship posture, multisig/timelock posture, payout-funding posture, reserve architecture, tokenomics vault composition, or public-trust-sensitive reserve treatment materially changes
- **Governing Layer:** Platform governance / reserve-behavior policy architecture / vault-meaning and vault-behavior control layer
- **Parent Registry:** `REFINED_SYSTEM_SPEC_INDEX.md`
- **Primary Audience:** Platform architecture, backend engineering, contracts engineering, treasury and finance stakeholders, governance/control-plane authors, security engineering, audit/compliance, transparency/reporting authors, implementation-contract authors
- **Primary Purpose:** Define the canonical FUZE vault action policy as the category-aware policy layer that determines what each reserve or reserve-adjacent vault may meaningfully do, how vault purpose constrains action space, destinations, timing, and sensitivity, and how vault-sensitive actions remain auditable, explainable, and structurally consistent over time without collapsing vault-policy truth into treasury-control truth, Foundation governance truth, multisig/timelock enforcement truth, profit-participation truth, or contract execution truth
- **Primary Upstream References:** `REFINED_SYSTEM_SPEC_INDEX.md`, `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`, `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`, `PLATFORM_ARCHITECTURE_SPEC.md`, `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`, `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`, `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`, `GOVERNANCE_MODEL_SPEC.md`, `TREASURY_CONTROL_POLICY_SPEC.md`, `FOUNDATION_GOVERNANCE_SPEC.md`, `MULTISIG_AND_TIMELOCK_SPEC.md`, `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`, `TRANSPARENCY_REPORTING_SPEC.md`, `TRANSPARENCY_MODEL_SPEC.md`, `CHAIN_ARCHITECTURE_SPEC.md`, `FUZE_WHITEPAPER_v.2026.3.0.1.pdf`, tokenomics vault docs under `fuze.ac > docs/tokenomics/`
- **Primary Downstream Dependents:** `VAULT_ACTION_POLICY_API_SPEC.md`, `TREASURY_CONTROL_POLICY_SPEC.md`, `FOUNDATION_GOVERNANCE_SPEC.md`, `MULTISIG_AND_TIMELOCK_SPEC.md`, `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`, `TRANSPARENCY_REPORTING_SPEC.md`, public-safe vault reporting surfaces, vault-sensitive admin/control-plane tooling, discrepancy and correction runbooks
- **Supersedes:** Earlier or weaker interpretations that treat separated vaults as sufficient by themselves, allow category drift through reasonable exceptions, collapse vaults into one omnibus reserve pool, or treat vault labels as descriptive narrative rather than operationally enforceable policy
- **Superseded By:** None currently defined
- **Related Decision Records:** Not explicitly specified in the retrieved governing materials
- **Canonical Status Note:** This document is the canonical governing FUZE specification for vault-action policy semantics. Downstream APIs, treasury controls, Foundation linkage rules, multisig/timelock enforcement layers, payout-related funding workflows, reporting surfaces, and vault-sensitive admin/control-plane tools MUST preserve the ownership, truth-separation, category-aware allowed-behavior, destination/timing discipline, correction, and visibility rules defined here.
- **Implementation Status:** Normative source; downstream vault-policy services, treasury and governance integrations, reporting linkages, and chain-adjacent execution references MUST align
- **Approval Status:** Draft refined canonical specification pending explicit approval workflow
- **Change Summary:** Refined vault action policy into a production-grade canonical specification; normalized vault action policy as the “what a vault may meaningfully do” domain, clarified action classes and sensitivity treatment, formalized category-specific action profiles, tightened destination and timing discipline, strengthened separation from treasury control and Foundation governance, made payout-related vault actions explicitly trust-sensitive, and defined correction/supersession lineage and public-safe reporting guardrails

## Title

FUZE Vault Action Policy Specification

## Purpose

This specification defines the canonical FUZE vault action policy.

Its purpose is to make explicit:

- what vault action policy governs and what it does not govern
- how separated vault architecture becomes operationally meaningful behavior rather than static reserve labeling
- how each vault category’s published economic role constrains the actions that category may meaningfully perform
- how vault action policy relates to treasury control, Foundation governance, multisig/timelock enforcement, payout-related behavior, transparency, and downstream execution without replacing those domains
- how action classes, sensitivity treatment, destination discipline, timing discipline, exceptional handling, correction, and supersession must work
- what downstream APIs, policies, reports, admin surfaces, and chain-adjacent execution layers MUST preserve

This specification is intentionally governing rather than descriptive. It defines vault action policy as the architecture layer that turns reserve separation into enforceable economic meaning.

## Scope

This specification governs:

- the canonical action-policy model for FUZE reserve and reserve-adjacent vaults
- the distinction between vault existence and vault behavior
- action-class semantics across observation, administration, internal coordination, category deployment, beneficiary release, payout-related, and emergency contexts
- category-aware vault behavior and how action policy differs by vault category
- destination, timing, and sensitivity constraints for vault-level actions
- approval, execution-linkage, and reporting expectations for vault-sensitive activity
- how vault behavior must preserve reserve meaning over time
- exceptional-case handling, pause controls, and recovery principles
- audit and transparency expectations for vault-level behavior
- implementation-contract guardrails for vault APIs, events, admin controls, reporting, and chain-adjacent execution references

It does not govern:

- full treasury-control approval mechanics in depth
- full Foundation-governance rules in depth
- final multisig signer/timelock parameter internals
- low-level vault contract ABIs
- final reserve accounting exports
- full beneficiary-release mechanics in contract detail
- full payout-cycle execution mechanics
- final transparency-report composition in depth

## Design Goals

The design goals of the FUZE vault action policy are to:

1. make vault purpose operationally enforceable rather than merely descriptive
2. prevent vault-category drift through clear action boundaries
3. define which actions are appropriate, restricted, or disallowed for each vault type
4. preserve stronger protection for stewardship and trust-sensitive vaults
5. ensure deployment-oriented vaults still operate within bounded and explainable rules
6. create a reusable action-policy vocabulary across the reserve architecture
7. improve governance quality by tying approval requirements to action class, destination, timing, and vault meaning
8. make vault behavior easier to report, audit, reconcile, and defend publicly over time

## Non-Goals

This specification is not intended to:

- make all vaults operationally identical
- allow category-specific exceptions to become a substitute for policy clarity
- turn every vault into a generic capital source
- replace treasury control policy with contract-only assumptions
- define vault behavior purely through narrative labels with no action model
- make emergency pathways broad enough to dissolve vault boundaries
- imply that every technically possible movement from a vault is economically appropriate

## Core Principles

### 1. Canonical Vault Action Principle
Every vault should be able to perform only those classes of actions that are consistent with its published economic role, and those actions should remain bounded by approval, destination, sensitivity, timing, and reporting rules appropriate to that role.

### 2. Vault Identity and Vault Behavior Principle
Vault identity and vault behavior must match. A vault’s published purpose should constrain what it may do, not merely how it is labeled.

### 3. Same-Action-Different-Meaning Principle
The same broad action may be acceptable for one vault and inappropriate, restricted, or exceptional for another. Category meaning outranks action-shape similarity.

### 4. Category Preservation Principle
Vault action policy exists to preserve category meaning rather than merely allow execution.

### 5. Destination Legibility Principle
A vault action must have a destination and use rationale that can be explained structurally. Destination ambiguity is itself a policy risk.

### 6. Timing Discipline Principle
An action may be category-consistent in abstract terms but still improper if executed at the wrong time, without the correct notice, or outside the correct governance window.

### 7. Foundation and Trust-Sensitive Restraint Principle
Foundation, Transparency/Stability, and Liquidity Operations categories require stronger restraint than ordinary treasury convenience.

### 8. Payout-Related Trust Principle
Payout-related vault actions are holder-facing, high-trust economic events and must not be treated as ordinary internal transfers.

### 9. Emergency Narrowness Principle
Emergency controls should protect vault assets and vault meaning without becoming broad discretionary override paths.

### 10. Reporting Compatibility Principle
Material vault actions must remain compatible with transparency reporting, reserve-architecture visibility, and long-term institutional memory.

## Canonical Definitions

### Vault
A reserve or reserve-adjacent capital structure with a published economic role in the FUZE token-side architecture.

### Vault Category
The canonical category used to interpret a vault’s purpose, allowable action space, reporting posture, and trust meaning.

### Vault-Sensitive Action
A policy-relevant action that affects vault-controlled value, vault control posture, vault destination routing, vault timing posture, or vault-category meaning.

### Action Class
A governed category of vault-sensitive action, such as observation, administrative control, internal coordination, category deployment, beneficiary release, payout-related action, or emergency action.

### Sensitivity Tier
The risk and trust significance level attached to a vault-sensitive action, determined by both the action itself and the vault it is applied to.

### Destination Category
The canonical classification of where value is intended to move or what structure an action will affect.

### Timing Reference
The policy-relevant reporting, governance, payout, vesting, launch, timelock, or emergency timing context that may make an otherwise category-consistent action proper or improper.

### Vault Policy Version
The canonical policy record describing active vault-action rules and their version lineage.

### Vault Control Reference
A structured reference to a bounded control mechanism associated with a vault-sensitive action, such as multisig, timelock, emergency authority, or other approved path.

### Vault Execution Reference
A bounded reference to the downstream execution artifact or pathway linked to an approved vault action.

### Vault Reporting Reference
A structured reference to transparency, registry, payout, or exception artifacts linked to a vault-sensitive action.

## Truth Class Taxonomy

The vault-action policy domain operates with the following truth classes, which MUST remain distinct:

1. **Vault-policy truth** — vault policy versions, vault-sensitive action records, vault category profiles, action-class records, destination validations, timing treatment, and correction/supersession semantics
2. **Treasury-control truth** — treasury-control approval posture, governance mechanics, sensitivity-based approval discipline, and treasury-domain authority
3. **Foundation-governance truth** — Foundation-specific stewardship, principal-treatment, and stronger constraints
4. **Execution truth** — downstream contract or operational execution state linked to a vault action
5. **Profit-participation truth** — payout and eligibility-system meaning, including vault interaction only where explicitly referenced
6. **Reporting / public-visibility truth** — public-safe vault summaries, transparency references, registry references, payout references, and trust-facing reporting linkages
7. **Audit truth** — immutable audit and activity records for vault-sensitive mutations
8. **Runtime / execution-plane truth** — retries, async jobs, export jobs, discrepancy cases, and operational remediation state
9. **Presentation truth** — labels, summaries, status wording, and explanatory framing
10. **Public interpretation truth** — the currently effective public-facing meaning of a vault policy or vault-sensitive action after correction and supersession rules are applied

These truth classes MUST NOT be collapsed into one undifferentiated reserve page, treasury dashboard, or public narrative surface.

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

- `VAULT_ACTION_POLICY_API_SPEC.md`
- `TREASURY_CONTROL_POLICY_SPEC.md`
- `FOUNDATION_GOVERNANCE_SPEC.md`
- `MULTISIG_AND_TIMELOCK_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`
- public-safe vault reporting surfaces
- vault-sensitive admin/control-plane tooling

This document governs vault-action policy semantics. It does not redefine narrower source-domain truth.

## System Boundaries

This document governs only the following platform-owned boundaries:

- vault-purpose semantics and what each vault may meaningfully do
- action-class and category-aware allowed-behavior semantics
- destination and timing discipline
- vault-specific sensitivity treatment
- category-aware exceptional handling and discrepancy posture
- vault correction, supersession, and historical-intelligibility posture
- public-safe reporting linkage and implementation-contract guardrails

It does not govern:

- treasury-control approval mechanics in full depth
- Foundation principal-lock rules in full depth
- multisig signer and timelock parameter details
- exact contract-execution logic
- raw accounting-book truth
- full payout execution semantics
- public transparency-report composition in full depth

## Adjacent Boundaries

### Treasury Control Policy
Treasury control policy governs how treasury-sensitive actions are controlled. Vault action policy governs what those vaults are meaningfully allowed to do. Both are required for a strong reserve architecture.

### Foundation Governance
Foundation governance should override ordinary treasury convenience when Foundation meaning is at stake. Vault action policy must explicitly preserve the Foundation as a structurally distinct category.

### Multisig and Timelock
Multisig and timelock own their narrower enforcement and queued-action/control-path semantics. Vault action policy determines why and when a given vault action requires stronger control treatment.

### Profit Participation and Eligibility
Vault action policy preserves clear treatment of payout-related action. A vault action funding a payout cycle is not merely another treasury transfer; it is a holder-facing, high-trust economic event and should be treated as such in sensitivity, reporting, and approval logic.

### Transparency and Reporting
Transparency and reporting domains own public-trust interpretation and recurring public reporting. Vault action policy owns the structural meaning that must remain explainable and reportable.

### Public Registry
The public registry owns public designation truth for official contracts and wallets. Vault action policy may link to registry artifacts where vault-sensitive actions require trust-safe reference.

## Conflict Resolution Rules

When materials, systems, or interpretations conflict, FUZE MUST resolve them in the following order:

1. the active refined registry and higher constitutional materials win over narrower documents
2. top-level boundary, ownership, and architecture specs win on top-level ownership and boundary posture
3. `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md` wins on chain-native versus off-chain policy/reporting separation
4. narrower source-domain specifications win on the meaning of their canonical truths
5. `GOVERNANCE_MODEL_SPEC.md` wins on higher-order governance semantics, scope classification, approval-path posture, and DAO-lite future-direction boundaries
6. `TREASURY_CONTROL_POLICY_SPEC.md`, `FOUNDATION_GOVERNANCE_SPEC.md`, and `MULTISIG_AND_TIMELOCK_SPEC.md` win within their narrower approval, stewardship, or enforcement scope
7. this document wins on vault-policy semantics, category-aware allowed-behavior meaning, destination/timing discipline, payout-related vault treatment, category-specific exceptional handling, and vault correction/supersession semantics
8. public dashboards, reserve pages, summaries, exports, and API views never win over canonical vault-policy truth
9. when ambiguity remains, FUZE MUST choose the more conservative trust-preserving interpretation and escalate the ambiguity into downstream refinement or recorded decision work

## Default Decision Rules

When no narrower approved exception exists, FUZE MUST default to the following:

1. vault purpose constrains action space by default
2. the same action does not imply the same allowability across vault categories
3. ambiguous action classes default to narrower or higher-sensitivity treatment
4. ambiguous destination or use rationale defaults to review-required or disallowed treatment rather than silent interpretation
5. timing-sensitive actions default to policy-window and governance-window correctness rather than convenience timing
6. Foundation, Transparency/Stability, and Liquidity Operations categories default to stronger restraint than ordinary treasury convenience
7. payout-related vault actions default to holder-facing high-trust treatment rather than ordinary transfer treatment
8. public visibility defaults to public-safe structural explanation rather than full internal exposure
9. emergency treatment defaults to narrower duration and mandatory post-review rather than standing shortcut authority
10. if vault category, action class, sensitivity tier, destination category, timing reference, policy version, or approval path cannot be identified, the action is incomplete and MUST NOT proceed

## Roles / Actors / Entities

### Human Actors
- treasury and finance stakeholders
- governance reviewers
- privileged operators
- Foundation stewards where applicable
- security and incident operators
- public-trust/reporting authors

### System Actors
- vault-action policy service
- treasury-control service
- governance model service
- Foundation governance service
- multisig/timelock control services
- contract execution services
- profit-participation and payout-linkage services
- reporting and transparency services
- audit and monitoring systems
- discrepancy and correction tooling

### Canonical Entities
- vault policy versions
- vault action records
- vault category profiles
- vault action classifications
- vault action destinations
- vault action approval paths
- vault action control references
- vault action reporting references
- vault exception records
- vault discrepancy cases
- vault mutation actions

## Ownership Model

The Vault Action Policy domain is the canonical owner of:

- vault-purpose semantics and category-aware allowed-behavior meaning
- vault-sensitive action lifecycle semantics
- action class and sensitivity-tier meaning as applied at the vault-behavior layer
- destination and timing discipline interpretation
- category-specific exceptional handling
- vault correction, discrepancy, and supersession semantics
- vault-owned public-safe read and reporting-linkage source truth

It is not the canonical owner of:

- treasury-control approval mechanics
- Foundation stewardship truth in full depth
- multisig or timelock implementation internals
- payout-cycle execution truth
- raw contract execution truth
- final transparency-report truth
- raw accounting exports

## Authority / Decision Model

Authority MUST be separated as follows:

- the governance model domain determines higher-order governance semantics
- the vault-action policy domain determines how each vault category’s published purpose constrains allowed behavior, destination posture, timing posture, and sensitivity posture
- treasury control determines how treasury-sensitive actions are reviewed, approved, and controlled
- Foundation governance determines stronger stewardship interpretation where Foundation meaning is at stake
- multisig and timelock layers enforce bounded execution discipline where applicable
- reporting and transparency domains determine how vault meaning is surfaced publicly under public-safe rules

No dashboard, static page, spreadsheet, or product-local flow may mint canonical vault-policy truth outside these boundaries.

## State Model

### Vault Policy Version Lifecycle
- `draft`
- `active`
- `deprecated`
- `superseded`
- `archived`

### Vault Action Lifecycle
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

1. a vault-sensitive change is identified and classified by vault category, action class, sensitivity tier, destination category, timing posture, and policy version
2. a vault proposal is recorded with source-domain references
3. required approval path and control references are attached
4. the action is approved, rejected, paused, escalated, or exceptionally handled under bounded policy and adjacent governance rules
5. if approved, a vault execution reference is linked to the downstream execution pathway
6. public-safe reporting, registry, or payout linkage occurs where policy requires
7. corrections, supersessions, or discrepancy cases are handled with preserved lineage
8. any exceptional vault action enters mandatory post-review and closure discipline

## Invariants

1. vault policy remains a distinct meaning-and-behavior layer
2. vault identity and vault behavior remain aligned
3. vault-sensitive actions remain explicitly classified
4. category meaning remains preserved over time
5. destination and use rationale remain explicit
6. timing-sensitive actions remain timing-disciplined
7. payout-related vault actions remain distinct from ordinary treasury transfers
8. public-safe vault visibility does not authorize unsafe disclosure
9. exceptional vault treatment remains narrow, reason-bearing, and reviewable
10. corrections and supersessions preserve historical intelligibility

## Functional Rules

### Vault Purpose Rule
A vault action must be consistent with the published purpose of the vault category.

### Explicit Authorization Rule
A vault action must have a defined approval path. No material vault action should occur based solely on operator convenience.

### Destination Legibility Rule
A vault action must have a destination and use rationale that can be explained structurally.

### Audit Lineage Rule
A vault action must preserve enough metadata, governance linkage, and reporting context to remain explainable later.

### Category Preservation Rule
A vault action should preserve category meaning rather than erode it.

### Emergency Narrowness Rule
Emergency controls should protect the vault without becoming a backdoor for ordinary discretionary use.

### Reporting Compatibility Rule
Material vault actions must be compatible with transparency reporting and reserve-architecture visibility.

### Foundation Vault Rule
The Foundation Vault should be governed by the most conservative action policy among non-vesting categories. Principal-impairing actions, ordinary operating deployment, generic fallback funding, liquidity-style actions, and rapid discretionary response to ordinary commercial pressure are strongly restricted or exceptional.

### Treasury Reserve Vault Rule
The Treasury Reserve Vault is the primary category for strategic platform flexibility, but it must remain policy-bounded. Broadest deployment-capable does not mean unbounded.

### Team and Advisor Vesting Rule
Team and Advisor Vesting Vaults follow beneficiary-alignment logic rather than treasury-deployment logic. They MUST NOT become reserve pools, liquidity sources, or ad hoc external-relations budgets.

### Holder Incentives Vault Rule
The Holder Incentives Vault exists for explicitly holder-facing or participation-facing programs and MUST NOT become generic treasury spending or undefined business-development spending.

### Ecosystem Partnership Vault Rule
The Ecosystem Partnership Vault exists for relationship-driven expansion, not for treasury shortfall patching, generic operating expense support, or liquidity management through partnership labeling.

### Liquidity Operations Vault Rule
Liquidity Operations is one of the most trust-sensitive deployment categories and should have narrower operational freedom than Treasury. Liquidity actions must be especially legible because market-facing token movement is highly scrutinized.

### Transparency / Stability Vault Rule
Transparency / Stability is among the most tightly bounded categories. Its misuse would damage one of FUZE’s strongest structural claims.

## Permission / Access Considerations

Vault mutation authority is privileged and bounded.

Required constraints:

- privileged vault actions require explicit operator identity, least-privilege access, and reason-coded execution
- creation, review, approval, pause, escalation, exceptional treatment, supersession, and discrepancy resolution MUST remain access-controlled
- public-safe vault views are distinct from internal vault records
- vault or product operators do not automatically gain vault-policy mutation authority outside bounded roles
- access failures on vault-sensitive mutation paths MUST fail closed

## Entitlement Considerations

Entitlement is not a primary vault-action policy owner.

Rules:

- entitlement MUST NOT redefine vault-policy truth
- token status or product entitlement MUST NOT be treated as automatic vault authority
- community-facing vault visibility does not imply mutation authority
- any future participation-linked features touching vault meaning MUST remain bounded by explicit governance policy and activation

## API / Contract Implications

The vault-action policy layer MUST expose stable contracts for:

- internal creation and read of vault policy versions and vault-sensitive actions
- vault-category, action-class, sensitivity-tier, destination, and timing interpretation
- proposal, approval-path, control-reference, execution-reference, and reporting-reference recording
- privileged approve, reject, pause, escalate, exceptional-handle, supersede, and discrepancy-resolution actions
- public-safe vault policy summaries and vault-action summaries where approved

## Event / Async Implications

The vault-action policy domain SHOULD support event families such as:

- `vault_action_policy.action_created`
- `vault_action_policy.destination_validated`
- `vault_action_policy.approval_path_recorded`
- `vault_action_policy.control_linked`
- `vault_action_policy.reporting_linked`
- `vault_action_policy.execution_linked`
- `vault_action_policy.action_approved`
- `vault_action_policy.action_rejected`
- `vault_action_policy.action_paused`
- `vault_action_policy.action_escalated`
- `vault_action_policy.exception_declared`
- `vault_action_policy.action_superseded`
- `vault_action_policy.discrepancy_resolved`

## Data Model / Storage Implications

At minimum, the vault-action policy domain SHOULD support semantic representation of:

- `vault_policy_versions`
- `vault_action_records`
- `vault_category_profiles`
- `vault_action_classifications`
- `vault_action_destinations`
- `vault_action_approval_paths`
- `vault_action_control_references`
- `vault_action_reporting_references`
- `vault_exception_records`
- `vault_discrepancy_cases`
- `vault_mutation_actions`

Derived stores MAY include public vault policy views, public-safe vault action views, internal status views, and discrepancy dashboards, provided they remain subordinate to canonical vault truth.

## Read Model / Projection / Reporting Rules

- public-safe vault policy summaries and vault-action summaries are derived views
- internal status dashboards are derived views
- public summaries MUST NOT overtake canonical vault records
- reporting, registry, and payout linkages remain references, not ownership transfers
- public or internal views MAY lag operationally, but they MUST remain reconcilable to canonical vault records and stronger source domains

## Security / Risk / Abuse Controls

The vault-action policy domain is a trust-sensitive control surface and MUST enforce:

- strict separation between vault-policy truth and downstream execution truth
- protection against unauthorized approval, category drift, destination ambiguity, silent history rewrite, misclassification, or unsafe public exposure
- bounded service-to-service mutation pathways
- privileged reason-coded vault actions
- safe handling of exceptional vault treatment, restriction, and correction events
- discrepancy detection when vault meaning and downstream reporting or execution references drift
- protection against vault-sensitive actions hiding inside ordinary runtime or product-local pathways

## Boundary Violation Detection / Non-Canonical Patterns

The following patterns are non-canonical and forbidden:

- treating separated vaults as one omnibus reserve pool in practice
- allowing category-specific exceptions to become a substitute for policy clarity
- treating any vault as a generic capital source
- collapsing Foundation meaning into ordinary treasury convenience
- using Team or Advisor Vesting as general-purpose transfer authority
- routing vault-controlled value through vague omnibus or structurally weak destinations
- funding payout cycles as though they were ordinary internal transfers
- letting dashboards, exports, or narrative pages become write owners of vault meaning
- silently rewriting vault classification or treatment without discrepancy or correction lineage

## Audit / Traceability Requirements

FUZE MUST be able to report on:

- what vault acted
- what vault category and action class applied
- what sensitivity tier applied
- what destination category and timing posture applied
- whether the action fit the vault’s intended role
- what approval path and control references applied
- what policy version governed the action
- how the action was reported or disclosed
- whether payout linkage or exceptional treatment occurred
- what discrepancy or correction lineage exists

## Failure Handling / Edge Cases

- if a proposal exists but the approval path is incomplete, the action remains incomplete and MUST NOT proceed
- if approval exists but execution fails, vault approval remains true while execution truth remains separate and failure-bearing
- if a public vault summary lags behind internal action, the public summary is a derived view and must reconcile rather than redefine internal vault truth
- if payout-related treatment is unclear, the action MUST remain unresolved until policy-defined readiness, authorization posture, and reporting linkage are explicit
- if an exceptional vault action is taken, exceptional handling MUST preserve explicit declaration, bounded treatment, and post-review requirements
- if vault category, destination, or timing posture is misclassified, a discrepancy case MUST be opened and resolved with preserved lineage rather than silent relabeling

## Operational Considerations

Operational systems SHOULD support:

- deterministic vault-action recording and lifecycle handling
- vault-category, destination, and timing validation
- approval-path completeness checks
- discrepancy detection between vault truth and downstream execution/reporting references
- bounded emergency handling with post-review
- health monitoring for public-safe vault views and internal control surfaces
- runbooks for misclassification, incomplete review paths, payout-related ambiguity, supersession propagation failure, public-summary lag, and exceptional-vault review closure

## Migration / Compatibility / Supersession Considerations

Migrations into this canonical model MUST enforce the following:

- legacy approvals or ad hoc admin sign-offs that act as hidden vault-policy truth must be retired or normalized
- historical vault labels MAY be preserved as lineage, but MUST NOT silently override canonical current interpretation
- compatibility layers MAY preserve older route shapes or admin screens temporarily, but canonical vault semantics MUST remain explicit
- payout-related treatment and reporting linkage MUST migrate from implicit assumptions to explicit references
- downstream consumers MUST migrate to canonical vault truth rather than local shadow approval stores or narrative pages

## Implementation-Contract Guardrails

1. vault action policy remains distinct from treasury-control approval mechanics and downstream execution truth
2. vault category, action class, sensitivity tier, destination category, timing posture, and policy version remain explicit
3. proposal, approval, and execution remain distinct lifecycle concepts
4. destination and timing discipline remain explicit
5. category-specific action profiles remain enforceable and visible
6. Foundation, Transparency/Stability, and Liquidity Operations remain more conservative than ordinary treasury convenience
7. payout-related vault treatment remains explicit
8. public-safe vault visibility remains bounded and distinct from internal vault detail
9. exceptional vault handling remains narrow and reviewable
10. historical vault meaning and correction/supersession lineage remain preserved
11. public summaries, APIs, and dashboards remain derived from canonical vault truth
12. idempotency, replay safety, and correction lineage remain explicit for trust-sensitive mutations
13. downstream teams MUST NOT create local vault-policy truth that conflicts with this domain

## Downstream Execution Staging

1. stabilize vault-category taxonomy, action-class semantics, sensitivity-tier semantics, destination semantics, and timing semantics
2. stabilize canonical vault policy, action, classification, and approval-path semantics
3. stabilize control-reference, execution-reference, and reporting-reference behavior
4. integrate treasury, Foundation, payout, and multisig/timelock linkage rules
5. integrate public-safe vault summaries and transparency/reporting linkages
6. integrate exceptional handling, discrepancy, correction, and remediation tooling

## Required Downstream Specs / Contract Layers

- `VAULT_ACTION_POLICY_API_SPEC.md`
- `TREASURY_CONTROL_POLICY_SPEC.md`
- `FOUNDATION_GOVERNANCE_SPEC.md`
- `MULTISIG_AND_TIMELOCK_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- vault discrepancy/remediation runbooks
- public-safe vault reporting contracts
- payout-linkage contracts where applicable

## Canonical Examples / Anti-Examples

### Canonical Examples
- Foundation Vault rejects ordinary operating deployment and escalates principal-sensitive action into exceptional governance treatment.
- Treasury Reserve funds a properly authorized strategic deployment or payout-related action with explicit destination, timing, approval, and reporting lineage.
- Team Vesting executes beneficiary-bound release behavior without becoming a reserve pool.
- Transparency / Stability performs a narrowly bounded trust-protective action with stronger reporting and review expectations.

### Anti-Examples
- A partnership vault patches a treasury shortfall because the outcome sounds reasonable.
- A Transparency / Stability vault funds ordinary campaign activity.
- A Foundation vault routes value into an ordinary operating wallet under treasury convenience logic.
- A payout-cycle funding transfer is recorded as just another internal treasury movement with no holder-facing reporting context.

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
- `TREASURY_CONTROL_POLICY_SPEC.md`
- `FOUNDATION_GOVERNANCE_SPEC.md`
- `MULTISIG_AND_TIMELOCK_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`
- `TRANSPARENCY_MODEL_SPEC.md`
- `CHAIN_ARCHITECTURE_SPEC.md`
- tokenomics vault docs under `fuze.ac > docs/tokenomics/`
- `FUZE_WHITEPAPER_v.2026.3.0.1.pdf`

## Explicitly Deferred Items

- exact allowed destination registry model
- exact blocked-destination categories
- exact vesting-release mechanics and beneficiary event schema
- exact reporting disclosure thresholds by sensitivity tier
- exact emergency containment procedures per vault type
- exact interaction rules for future reserve categories if added
- exact contract ABI implementation details

## Final Normative Summary

The FUZE vault action policy defines how reserve and reserve-adjacent vaults may behave in practice. It turns separated vault architecture into enforceable economic meaning by defining action classes, sensitivity tiers, destination rules, timing rules, category-specific behavior, and stronger governance and reporting expectations for trust-sensitive actions. By making vault behavior explicit rather than implied, FUZE reduces category drift, omnibus reserve behavior, and governance ambiguity, and strengthens one of the most important trust layers in the ecosystem.

## Quality Gate Checklist

- Canonical owner is explicit for vault-policy truth, category-aware allowed-behavior truth, and correction/supersession semantics.
- Mutation boundaries are explicit for internal services, control-plane tools, and derived public surfaces.
- Adjacent boundaries are explicit for treasury control, Foundation governance, multisig/timelock, payout, transparency, registry, and on-chain/off-chain domains.
- Truth classes are explicit and separated.
- Conflict-resolution rules are explicit.
- Default decision rules are explicit for category ambiguity, destination ambiguity, timing ambiguity, payout-related treatment, exceptional handling, and Foundation override.
- Non-canonical patterns are called out clearly.
- Operator/admin override paths are bounded, reason-coded, and auditable.
- Read-model, reporting, and public-summary rules are explicit.
- Failure and degraded-mode behaviors are explicit.
- Downstream implementation guardrails are explicit.
- Dependencies and downstream impacts are explicit.
- Non-goals and deferred items are explicit.
- The document is strong enough to support downstream API, backend, control-plane, reporting, and chain-adjacent implementation without contradictory semantic invention.
