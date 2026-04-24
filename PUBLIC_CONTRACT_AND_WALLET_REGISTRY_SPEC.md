# FUZE Public Contract and Wallet Registry Specification

## Document Metadata

- **Document Name:** `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
- **Document Type:** Canonical refined system specification
- **Status:** Active refined system spec
- **Version:** 2.0.0
- **Effective Date:** 2026-04-23
- **Last Updated:** 2026-04-23
- **Reviewed On:** 2026-04-23
- **Document Owner:** FUZE Public Contract and Wallet Registry Domain (canonical owner for public registry publication truth, publication-state governance, verification lineage, public role classification, registry supersession, and public trust-safe contract and wallet designation); named individual owner is not explicitly specified in the retrieved governing materials
- **Approval Authority:** Not explicitly specified in the retrieved governing materials; constitutional approval authority remains governed by `REFINED_SYSTEM_SPEC_INDEX.md` and the active FUZE approval workflow
- **Review Cadence:** SHOULD be reviewed quarterly and whenever chain architecture, transparency posture, wallet-aware user posture, treasury/governance controls, public API posture, public trust publication posture, registry role taxonomy, or public correction policy materially changes
- **Governing Layer:** Public trust / reporting-and-transparency-adjacent public registry governance / chain-adjacent public publication layer
- **Parent Registry:** `REFINED_SYSTEM_SPEC_INDEX.md`
- **Primary Audience:** Platform architecture, backend engineering, contracts engineering, public API authors, governance/control-plane authors, treasury and finance stakeholders, audit/compliance, community/public trust stakeholders, reporting authors, implementation-contract authors, security and operations teams
- **Primary Purpose:** Define the canonical FUZE domain that governs public contract and wallet registry publication, including how FUZE publicly designates official contracts and wallets, classifies and verifies registry entries, preserves public correction and supersession lineage, and exposes public trust-safe registry records without collapsing registry publication truth into wallet-link truth, chain-native truth, treasury-control truth, governance-action truth, transparency-report truth, or private operational wallet truth
- **Primary Upstream References:**
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
  - `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
  - `WALLET_AWARE_USER_SPEC.md`
  - `API_ARCHITECTURE_SPEC.md`
  - `PUBLIC_API_SPEC.md`
  - `INTERNAL_SERVICE_API_SPEC.md`
  - `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
  - `IDEMPOTENCY_AND_VERSIONING_SPEC.md`
  - `MIGRATION_AND_BACKWARD_COMPATIBILITY_SPEC.md`
  - `AUDIT_LOG_AND_ACTIVITY_SPEC.md`
  - `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - `SECRETS_CONFIG_AND_ENVIRONMENT_SPEC.md`
  - `MONITORING_ALERTING_AND_INCIDENT_RESPONSE_SPEC.md`
  - `DEPLOYMENT_AND_RUNTIME_OPERATIONS_SPEC.md`
  - `BUSINESS_CONTINUITY_AND_RECOVERY_SPEC.md`
  - `TRANSPARENCY_MODEL_SPEC.md`
  - `TRANSPARENCY_REPORTING_SPEC.md`
  - `INVESTOR_AND_COMMUNITY_REPORTING_SPEC.md`
  - `GOVERNANCE_MODEL_SPEC.md`
  - `FOUNDATION_GOVERNANCE_SPEC.md`
  - `TREASURY_CONTROL_POLICY_SPEC.md`
  - `VAULT_ACTION_POLICY_SPEC.md`
  - `MULTISIG_AND_TIMELOCK_SPEC.md`
  - `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
  - `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
  - `PAYOUT_LEDGER_SPEC.md`
  - `CHAIN_ARCHITECTURE_SPEC.md`
- **Primary Downstream Dependents:**
  - `PUBLIC_CONTRACT_WALLET_REGISTRY_API_SPEC.md`
  - `PUBLIC_REGISTRY_LOOKUP_API_SPEC.md`
  - `PUBLIC_API_SPEC.md`
  - `INTERNAL_SERVICE_API_SPEC.md`
  - `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
  - `TRANSPARENCY_MODEL_SPEC.md`
  - `TRANSPARENCY_REPORTING_SPEC.md`
  - `INVESTOR_AND_COMMUNITY_REPORTING_SPEC.md`
  - `PAYOUT_LEDGER_SPEC.md`
  - `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
  - `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
  - public registry sites and public metadata surfaces
  - public wallet/contract verification surfaces
  - partner and exchange verification integrations
  - chain-adjacent trust-artifact exports
- **Supersedes:** Earlier or weaker interpretations that treat the public registry as a convenience export of internal contract data, allow public registry entries to expose private wallet inventories or signer/control detail, treat public registry records as chain-native truth or wallet-link truth, let public publication silently rewrite historical meaning, or allow operators to publish official wallets/contracts without explicit verification, classification, supersession, and audit lineage
- **Superseded By:** None currently defined
- **Related Decision Records:** Not explicitly specified in the retrieved governing materials
- **Canonical Status Note:** This document is the canonical governing FUZE specification for public contract and wallet registry semantics. Downstream APIs, registry lookup surfaces, public sites, reporting layers, admin/control-plane tools, transparency linkages, and chain-adjacent trust publication surfaces MUST preserve the ownership, truth-separation, visibility, publication, correction, and audit rules defined here.
- **Implementation Status:** Normative source; downstream registry services, publication controls, public APIs, export processes, partner verification integrations, transparency linkages, and operator/admin tools MUST align
- **Approval Status:** Draft refined canonical specification pending explicit approval workflow
- **Change Summary:** Refined the public registry domain into a production-grade canonical specification; normalized public registry truth classes, publication-state governance, registry role and family taxonomy, verification and supersession posture, visibility boundaries, control-plane mutation rules, correction lineage, public-trust-safe read models, and implementation-contract guardrails while preserving clear separation from wallet-link, chain, treasury, governance, and transparency domains

## Title

FUZE Public Contract and Wallet Registry Specification

## Purpose

This specification defines the canonical FUZE public contract and wallet registry architecture.

Its purpose is to make explicit:

- what the public contract and wallet registry domain governs and what it does not govern
- how FUZE publicly designates, classifies, verifies, publishes, deprecates, supersedes, and corrects official contracts and designated wallets
- how public registry outputs interact with chain truth, wallet-aware user truth, governance and treasury control truth, transparency artifacts, payout and eligibility systems, and public APIs without replacing those domains
- what public-safe fields, visibility states, and lifecycle semantics the registry must preserve
- how registry publication, correction, and lookup must remain historically intelligible, audit-linked, and operationally safe
- what downstream APIs, exports, public sites, partner verification surfaces, and control-plane tools MUST preserve

This specification is intentionally governing rather than descriptive. It does not merely describe a public list of addresses. It defines the durable FUZE public trust layer for official contract and wallet designation.

## Scope

This specification governs:

- the shared FUZE public registry for official contracts and designated public wallets
- registry family and role classification for public contracts and public wallets
- chain/network/address binding semantics for registry entries
- registry verification, publication, deprecation, restriction, supersession, and revocation posture
- public-safe aliasing, labeling, and artifact linkage
- public lookup, discovery, and public trust-safe registry read models
- admin/control-plane publication and correction actions for registry entries
- internal synchronization and export from canonical registry truth to public trust surfaces
- audit, traceability, security, and operational requirements for registry publication
- implementation-contract guardrails for APIs, events, read models, exports, and public publication behavior

## Out of Scope

This specification does not define:

- chain-native contract behavior, balances, role references, or other committed contract facts in full detail
- exact smart-contract deployment mechanics or contract ABI details
- private hot-wallet, cold-wallet, signer, custody, or key-management details
- wallet-link truth between user accounts and wallets
- treasury action approval, vault execution, multisig signing, or timelock execution internals
- exact transparency-report schema or authoring workflow
- exact investor/community report composition
- exact static-site rendering or website implementation details
- exact low-level block explorer integration mechanics
- exact partner/exchange onboarding processes beyond registry-safe verification expectations

Those concerns belong in adjacent specifications and downstream implementation contracts and MUST NOT be silently redefined here.

## Design Goals

The design goals of the public contract and wallet registry domain are to:

1. provide one canonical FUZE public registry layer for official contracts and designated public wallets
2. preserve public trust by making official addresses explicit, queryable, and historically intelligible
3. keep public registry publication distinct from chain-native truth, wallet-link truth, treasury/control truth, and transparency-report truth
4. support public verification and partner discovery without exposing unsafe internal control detail
5. make publication, deprecation, supersession, and correction explicit rather than silent
6. preserve one canonical off-chain owner for registry publication truth and visibility state
7. support public and bounded authenticated discovery surfaces where policy allows
8. ensure downstream APIs, sites, and public trust artifacts consume canonical registry truth instead of inventing local address lists
9. support public-safe degraded modes and correction-safe continuity during runtime or operational incidents
10. provide enough semantic precision that downstream contract, API, reporting, and public communication layers cannot reinterpret registry meaning incompatibly

## Non-Goals

This specification is not intended to:

- make the registry the owner of chain truth, wallet ownership truth, treasury truth, governance truth, or payout truth
- expose all wallets that FUZE may control privately or operationally
- prove live balances, signer control, or contract safety in full detail merely by listing an address
- let public publication or lookup replace transparency reporting or public reporting
- allow products or teams to keep divergent “official address” lists outside the shared registry domain
- let public registry convenience override stronger security, governance, or privacy boundaries
- silently erase historical registry meaning through overwrite-only mutations
- turn public registry surfaces into hidden control interfaces or privileged mutation surfaces

If there is tension between convenience and public trust, the stricter trust-preserving interpretation wins.

## Core Principles

### 1. Public Registry as Trust Layer Principle
The registry is a public trust and discovery layer for officially designated FUZE contracts and wallets, not an undifferentiated export of internal address data.

### 2. Publication-Is-Off-Chain Principle
Registry publication truth is off-chain backend-governed truth even when the addresses themselves exist on-chain.

### 3. Registry-Is-Not-Chain-Truth Principle
A published registry entry communicates official designation and public-safe metadata. It does not replace chain-native facts or contract-native semantics.

### 4. Registry-Is-Not-Wallet-Link-Truth Principle
Public registry records about wallets do not replace account-to-wallet linkage, proof-of-control history, or private wallet-aware user truth.

### 5. Visibility-Boundary Principle
Public registry entries MUST remain distinct from private operational wallet inventories, signer details, internal risk posture, and control-plane-only metadata.

### 6. Explicit Lifecycle Principle
Verification, publication, deprecation, restriction, revocation, correction, and supersession MUST be explicit lifecycle states with preserved lineage.

### 7. Public Historical Intelligibility Principle
Registry changes MUST remain intelligible over time. Deprecated or superseded entries should preserve replacement guidance rather than disappearing silently.

### 8. Controlled Mutation Principle
Only bounded internal and privileged admin/control-plane pathways may create or mutate canonical registry truth, and such actions MUST be audit-linked.

### 9. Public Trust Surface Separation Principle
Registry entries may link to transparency artifacts, governance references, payout artifacts, or public documents, but those linked artifacts remain owned by adjacent domains.

### 10. Derived Surface Subordination Principle
Public sites, lookup APIs, metadata catalogs, partner exports, and documentation renderings are derived surfaces subordinate to canonical registry truth.

## Canonical Definitions

### Public Registry Entry
A canonical registry-owned record designating an official FUZE contract or designated public wallet for public trust and discovery purposes.

### Registry Family
A governed classification that identifies whether a registry entry belongs to a contract family, wallet family, network reference family, or another approved public registry family.

### Registry Role Classification
The governed role/category assignment attached to a registry entry, such as token contract, treasury reserve vault, foundation vault, payout wallet, multisig, or another explicitly approved public role.

### Publication State
The explicit visibility and lifecycle state of a registry entry, including whether and how it may be surfaced publicly or in bounded authenticated contexts.

### Verification Record
The durable lineage record explaining how and under what basis a registry entry was verified for publication or trust-safe usage.

### Supersession Link
A durable lineage link connecting deprecated, replaced, corrected, or withdrawn registry entries to their successor or explanatory state.

### Public-Safe Artifact Link
A registry-owned linkage from a registry entry to another approved public artifact, such as a transparency artifact, public documentation, or public report reference.

### Registry Correction
A bounded corrective action that changes public registry interpretation while preserving historical lineage.

### Registry Revocation
An exceptional state in which a previously published entry must be explicitly marked as no longer trusted for official public designation.

### Registry Export
A derived output generated from canonical registry truth for public sites, API caches, partner verification surfaces, or public trust artifacts.

## Truth Class Taxonomy

The public contract and wallet registry domain operates with the following truth classes, which MUST remain distinct:

1. **Registry publication truth** — canonical off-chain registry entry records, publication state, role classification, chain/network bindings, and verification lineage
2. **Chain-native truth** — on-chain contract state, balances, role assignments, pause state, and other committed chain facts
3. **Wallet-link truth** — canonical account-to-wallet linkage and wallet-aware user semantics
4. **Governance / treasury / control truth** — approval, treasury policy, vault action, multisig, timelock, and other control-plane truths
5. **Transparency / reporting truth** — transparency reports, public reporting artifacts, payout ledgers, investor/community reports, and other separately governed public disclosures
6. **Verification-input truth** — deployment evidence, chain observations, internal approval references, external/public evidence, and other inputs used in verification
7. **Runtime / execution truth** — publication jobs, export jobs, retries, remediation, and operational progress
8. **Projection / read-model truth** — public lookup indexes, public list/detail views, status views, partner exports, and public site renderings
9. **Audit truth** — immutable audit and activity records about sensitive registry mutations
10. **Presentation truth** — labels, summaries, public explanatory copy, explorer links, and display formatting

These truth classes MUST NOT be collapsed into one undifferentiated model.

## Architectural Position in the Spec Hierarchy

This document sits below:

- `REFINED_SYSTEM_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `API_ARCHITECTURE_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `IDEMPOTENCY_AND_VERSIONING_SPEC.md`
- `MIGRATION_AND_BACKWARD_COMPATIBILITY_SPEC.md`
- `AUDIT_LOG_AND_ACTIVITY_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`

and above or alongside:

- `PUBLIC_CONTRACT_WALLET_REGISTRY_API_SPEC.md`
- `PUBLIC_REGISTRY_LOOKUP_API_SPEC.md`
- `TRANSPARENCY_MODEL_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`
- `PAYOUT_LEDGER_SPEC.md`
- `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- public metadata surfaces
- public registry sites and public docs surfaces
- partner/exchange verification contracts
- public trust export contracts

This document governs registry publication semantics. It does not redefine chain-native state, wallet-link semantics, treasury control, governance truth, or transparency-report meaning.

## System Boundaries

This document governs only the following platform-owned boundaries:

- canonical public registry records for official contracts and designated public wallets
- registry-family and public-role classification
- registry publication-state truth
- verification and publication lineage
- public-safe aliasing, labeling, artifact linkage, and supersession behavior
- public lookup and discovery read models derived from canonical registry truth
- internal and admin mutation pathways for registry publication and correction
- security, audit, and operational posture for the registry domain

It does not govern:

- private wallet inventory or private signer topology
- live chain balance truth or contract-owned state
- wallet-aware user linkage between accounts and wallets
- treasury execution, vault control, or governance approval truth
- full transparency report semantics or report publication truth
- eligibility calculation, payout execution, or payout-cycle truth
- exact public website rendering or documentation IA
- generic public metadata beyond registry-owned public trust records

## Adjacent Boundaries

### On-Chain / Off-Chain Responsibility
On-chain/off-chain responsibility governs the division between chain-committed truth and off-chain reporting/publication truth. The registry is part of the reporting-and-transparency layer and does not redefine contract-owned facts.

### Wallet-Aware User
Wallet-aware user owns account-to-wallet linkage and wallet-link lifecycle. Public registry records about designated wallets are downstream publication artifacts and MUST NOT become account-linked wallet truth.

### Transparency Model and Transparency Reporting
Transparency domains own transparency-specific publication truth and report semantics. Registry entries may link to transparency artifacts but do not own report periods, attestations, or transparency-report publication rules.

### Treasury Control / Vault Action / Multisig and Timelock / Governance
These domains own control and decision truth. Registry entries may publicly identify certain control-related addresses or roles, but public registry publication does not itself authorize or explain the underlying control mechanics in full depth.

### Public API and Public Registry Lookup
These domains refine how public registry records are exposed externally. They consume registry truth and MUST preserve its visibility, supersession, and safety rules.

### Payout, Eligibility, and Profit Participation
These domains may reference official wallets and contracts from the registry as public trust artifacts, but they remain owners of payout, snapshot, eligibility, and participation truth.

### Security and Audit
Security governs risk controls and safe exposure boundaries; audit owns immutable activity records. Registry mutation and publication actions must generate and respect these stronger control layers.

## Conflict Resolution Rules

When materials, systems, or interpretations conflict, FUZE MUST resolve them in the following order:

1. the active refined registry and higher constitutional materials win over narrower documents
2. `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`, `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`, `PLATFORM_ARCHITECTURE_SPEC.md`, and `DOMAIN_OWNERSHIP_MATRIX_SPEC.md` win on top-level ownership, plane separation, and domain hierarchy
3. `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md` wins on canonical-versus-derived entity interpretation
4. `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md` wins on chain-native truth versus off-chain publication and reporting boundaries
5. `WALLET_AWARE_USER_SPEC.md` wins on account-to-wallet linkage and wallet-aware user semantics
6. treasury, governance, and control specifications win on approval, vault, multisig, timelock, and control truth
7. transparency specifications win on transparency-report publication semantics
8. this document wins on public registry publication truth, verification lineage, registry role classification, public visibility, supersession, revocation, and public trust-safe registry semantics
9. public APIs, sites, and exports never win over stronger canonical registry truth
10. when ambiguity remains, FUZE MUST choose the more conservative trust-preserving interpretation and escalate the ambiguity into downstream refinement or recorded decision work

## Default Decision Rules

When no narrower approved exception exists, FUZE MUST default to the following:

1. addresses are not public-official by default; publication requires explicit registry action
2. ambiguous address role classification defaults to narrower or unpublished treatment
3. publication uncertainty defaults to non-public or review-required rather than public exposure
4. deprecated or replaced entries default to preserved historical visibility with successor guidance rather than silent deletion
5. public registry surfaces default to excluding private signer, custody, or operational control details
6. if chain-native and registry views appear to disagree, the registry remains publication truth while chain-native truth remains chain truth; the discrepancy must be handled explicitly
7. if wallet-link context and public registry context appear to disagree, wallet-link truth remains privately canonical and registry meaning remains public-publication truth
8. lookup and export layers default to derived non-authoritative status relative to canonical registry records
9. public artifact links default to omission until public-safe linkage is explicitly approved
10. revocation or restriction defaults to explicit explanatory state rather than silent disappearance whenever public trust would otherwise be harmed

## Roles / Actors / Entities

### Human Actors
- public users and community members
- partners, exchanges, auditors, and ecosystem verifiers
- authenticated first-party users where bounded enrichments are approved
- platform operators
- governance / treasury reviewers
- support and audit reviewers
- platform architects and API authors

### System Actors
- canonical registry service
- public lookup/read-model services
- internal service callers
- admin/control-plane services
- public API layer
- export and publication pipelines
- event and webhook infrastructure
- audit and monitoring systems
- public site/rendering systems
- discrepancy-detection and remediation tooling

### Canonical Entities
- public registry entries
- registry family profiles
- registry role classifications
- registry verification records
- registry publication states
- registry chain bindings
- registry aliases
- registry supersession links
- registry artifact links
- registry export records
- registry discrepancy cases
- registry mutation actions

## Ownership Model

The Public Contract and Wallet Registry domain is the canonical owner of:

- public registry entry semantics
- registry-family and public-role classification within the registry domain
- registry publication-state truth
- verification lineage for registry publication
- registry supersession, deprecation, restriction, and revocation posture
- public-safe alias and artifact-link semantics within the registry domain
- registry-owned public lookup and export source truth

It is not the canonical owner of:

- chain-native balances or contract state
- wallet-link truth
- treasury control or governance decisions
- transparency-report content truth
- payout-cycle or eligibility truth
- private security and operational wallet topology
- generic public metadata outside the registry domain

## Authority / Decision Model

Authority MUST be separated as follows:

- chain and deployment domains determine what exists on-chain; they do not automatically determine public-official designation
- treasury, governance, and control domains determine approval and control truth for sensitive wallet/contract roles
- the registry domain determines whether, how, and in what state a contract or wallet is publicly designated and exposed
- transparency and reporting domains determine report publication semantics and public narrative for their artifacts
- public API and public site layers consume canonical registry publication truth but do not own it
- operators MAY verify, publish, deprecate, supersede, restrict, revoke, or correct registry entries only through bounded admin/control-plane paths with reason codes and audit lineage

No product, static site, vendor, or downstream integration may mint canonical registry truth outside these boundaries.

## State Model

### Registry Entry Lifecycle
Supported semantic states SHOULD include:

- `draft`
- `verified`
- `published`
- `restricted`
- `deprecated`
- `superseded`
- `revoked_if_required`
- `archived`

### Publication-State Lifecycle
Supported semantic states SHOULD include:

- `unpublished`
- `published_public`
- `published_authenticated_if_approved`
- `restricted`
- `withdrawn`

### Verification Lifecycle
Supported semantic states SHOULD include:

- `pending`
- `validated`
- `rejected`
- `superseded`

### Export Lifecycle
Supported semantic states SHOULD include:

- `pending`
- `generated`
- `published`
- `failed`
- `superseded`

### Discrepancy Lifecycle
Supported semantic states SHOULD include:

- `opened`
- `under_review`
- `resolved`
- `failed`
- `closed`

Implementations MAY vary in naming, but these semantic distinctions MUST remain expressible.

## Lifecycle / Workflow Model

1. a candidate contract or wallet is identified for possible public designation
2. source evidence, role classification, and chain/network binding are prepared through approved internal pathways
3. verification is performed under policy and recorded in durable verification lineage
4. a canonical registry record is created or updated in draft or verified state
5. privileged publication action moves the entry into an approved visibility state
6. derived read models, lookup indexes, and exports are refreshed from canonical registry truth
7. public APIs, public sites, and partner/public trust artifacts consume the derived outputs
8. deprecation, supersession, restriction, correction, or revocation proceeds through explicit lifecycle actions with lineage preservation
9. discrepancy and operational incidents are remediated through bounded review and correction flows

## Invariants

1. registry publication truth is off-chain and backend-owned
2. public registry entries remain distinct from private wallet inventories and signer/control detail
3. chain-native truth remains distinct from registry publication truth
4. wallet-link truth remains distinct from registry publication truth
5. every public registry entry has explicit chain/network/address binding and explicit family/type semantics
6. publication, deprecation, supersession, restriction, and revocation remain explicit lifecycle states
7. public lookup and export layers remain derived from canonical registry records
8. operators cannot publish or materially change trust-sensitive registry entries without bounded authority and audit lineage
9. historical public meaning must remain intelligible; silent rewrite is forbidden
10. linked public artifacts remain links, not ownership transfers

## Functional Rules

### Official Designation Rule
No contract or wallet becomes an official public FUZE registry entry without explicit registry-domain creation and approved publication state.

### Public-Safe Scope Rule
The registry MUST expose only public-safe metadata required for trust, verification, and discovery. Unsafe internal details are forbidden.

### Role Classification Rule
Every registry entry MUST have an explicit family/type and public role classification before publication.

### One-Current-Entry-Per-Binding Rule
Within a defined chain/network/address scope, an address SHOULD resolve to one current canonical registry entry in normal operation. Historical lineage MAY retain prior deprecated or superseded records.

### Verification Lineage Rule
Trust-sensitive entries MUST preserve durable verification lineage showing how publication was justified.

### Supersession Rule
Deprecations and replacements MUST preserve old-to-new lineage and public successor guidance rather than silently removing prior meaning.

### Restriction and Revocation Rule
Restriction or revocation MUST remain explicit, auditable, and reason-bearing. It MUST NOT silently erase history where public trust would be harmed.

### Artifact-Link Rule
Links to transparency reports, docs, explorer-safe references, or other public artifacts MUST remain explicit and bounded; they do not transfer ownership.

### Derived-View Rule
Public lookup indexes, public list/detail views, metadata surfaces, and site exports MUST be treated as derived views, not source-of-truth mutation owners.

## Permission / Access Considerations

The registry domain includes public, internal, and privileged control surfaces with different authority levels.

Required constraints:

- public users may read only entries and fields approved for public visibility
- authenticated users may receive only bounded enrichments explicitly allowed by policy
- internal services may create or synchronize registry records only with explicit least-privilege service authority
- admin/control-plane users require privileged identity, scope checks, and reason-coded actions for trust-sensitive mutations
- restricted or internal-only registry metadata MUST NOT leak through public lookup or export surfaces
- access failures on sensitive mutation routes MUST fail closed rather than falling back to weaker trust assumptions

## Entitlement Considerations

Entitlement is not the primary owner of registry visibility, but it MAY shape certain bounded authenticated enrichments.

Rules:

- entitlement MUST NOT redefine which entries are officially public
- entitlement MAY gate internal or authenticated value-added views if separately approved
- public-trust-critical visibility rules MUST remain grounded in registry publication state, not product entitlement convenience
- products MUST NOT hide or relabel canonical public registry truth through entitlement-specific reinterpretation

## API / Contract Implications

The registry layer MUST expose stable platform-consumable contracts for:

- public list and lookup of published registry entries
- public detail retrieval for one registry entry
- bounded authenticated registry enrichments where approved
- internal creation and update of draft/verified registry entries
- privileged verify, publish, deprecate, supersede, restrict, revoke, and correct actions
- export and synchronization of canonical registry truth to public trust surfaces
- machine-readable indication of family/type, role, visibility state, supersession state, and linked artifacts

### Contract Rules

- public APIs MUST preserve public-safe visibility and historical intelligibility
- internal APIs MUST preserve source legitimacy, idempotency, and lineage
- admin/control APIs MUST require reason-coded privileged action for trust-sensitive state transitions
- lookup APIs and static/public exports MUST remain derived from canonical registry records
- public read contracts MUST distinguish current, deprecated, superseded, restricted, and withdrawn semantics where relevant
- APIs MUST preserve explicit linkage to verification lineage, publication state, and successor guidance where policy allows

## Event / Async Implications

The registry domain SHOULD support event families such as:

- `registry.entry_created`
- `registry.entry_verified`
- `registry.entry_published`
- `registry.entry_deprecated`
- `registry.entry_superseded`
- `registry.entry_restricted`
- `registry.entry_revoked`
- `registry.export_generated`
- `registry.discrepancy_opened`
- `registry.discrepancy_resolved`

Event rules:

- events MUST be emitted after canonical commit
- events MUST preserve entry identity, family/type, role classification, visibility state, chain binding, and lineage references
- downstream consumers MUST treat registry events as synchronization signals rather than replacements for canonical registry records
- replay, retries, and corrections MUST preserve idempotency and supersession safety
- public site, lookup, analytics, and transparency consumers MUST remain anchored to canonical registry truth rather than local caches alone

## Data Model / Storage Implications

At minimum, the registry domain SHOULD support semantic representation of:

- `public_registry_entries`
- `registry_family_profiles`
- `registry_role_classifications`
- `registry_verification_records`
- `registry_publication_states`
- `registry_chain_bindings`
- `registry_aliases`
- `registry_supersession_links`
- `registry_artifact_links`
- `registry_export_records`
- `registry_discrepancy_cases`
- `registry_mutation_actions`

Derived stores MAY include public lookup indexes, public list/detail views, partner/export snapshots, and discrepancy dashboards, provided they remain subordinate to canonical registry truth.

## Read Model / Projection / Reporting Rules

- public registry sites, public lookup APIs, partner exports, and public metadata catalogs are derived registry surfaces
- derived surfaces MUST NOT become write owners for canonical registry truth
- public views MUST preserve publication state, deprecation/supersession guidance, and explicit visibility limits
- restricted or withdrawn states MUST be represented in a trust-preserving manner appropriate to the visibility policy
- public projections MAY lag operationally, but they MUST remain reconcilable to canonical registry records
- transparency reports, investor/community reports, or payout reports MAY reference registry entries, but they do not own registry publication meaning

## Security / Risk / Abuse Controls

The registry domain is a public trust surface and MUST enforce:

- strict separation between public-safe registry fields and sensitive internal wallet/control metadata
- protection against spoofed publication, unauthorized role changes, silent history rewrite, and unsafe public exposure
- bounded service-to-service mutation pathways
- privileged, reason-coded admin/control-plane actions for trust-sensitive state transitions
- safe handling of revocation, restriction, and public correction events
- protection against export drift, stale caches, and unsafe third-party mirroring being mistaken for canonical truth
- monitoring and discrepancy detection for duplicate, conflicting, or suspicious public registry bindings
- defense against public surfaces leaking signer identities, secret material, internal control graphs, or risk notes

## Boundary Violation Detection / Non-Canonical Patterns

The following patterns are non-canonical and forbidden:

- treating the public registry as chain-native truth rather than publication truth
- exposing private operational wallet inventory or signer topology through public registry surfaces
- using public registry records as a substitute for wallet-link account truth
- letting a public site or export become the write owner of official registry meaning
- silently reusing an address for a new public role without preserved historical lineage
- publishing trust-sensitive contracts or wallets without explicit verification and publication-state transitions
- allowing public registry labels to imply treasury or governance authorization beyond what the registry actually owns
- silently deleting deprecated or superseded entries in a way that destroys public intelligibility
- letting product teams or external docs maintain divergent “official address” truth outside the shared registry domain

Implementations SHOULD detect and surface these conditions through diagnostics, audits, alerts, and discrepancy workflows.

## Audit / Traceability Requirements

At minimum, FUZE MUST be able to report on:

- who or what created, verified, published, deprecated, superseded, restricted, revoked, or corrected a registry entry
- what chain/network/address binding and role classification applied
- what evidence or internal references supported verification
- what publication state and visibility state applied over time
- what successor or correction lineage exists for historical entries
- what public artifacts were linked or exported
- what discrepancy cases were opened and how they were resolved
- which policy or ruleset version governed trust-sensitive publication decisions where applicable

Public registry actions are part of FUZE’s trust surface and must remain reconstructable.

## Failure Handling / Edge Cases

### Chain Address Exists but Registry Record Does Not
Existence on-chain does not imply official public designation. The registry remains unpublished until explicit registry action occurs.

### Registry Record Published but Derived Export Fails
Canonical registry truth remains intact. Export/read-model failure is operational and must be remediated without mutating registry meaning informally.

### Address Role Changes Over Time
The platform MUST preserve supersession or deprecation lineage rather than silently relabeling history.

### Public Entry Appears Incorrect
Correction MUST proceed through explicit review, reason capture, correction lineage, and, where needed, public successor or revocation guidance.

### Wallet Appears in Public Registry and Wallet-Aware User Context Differently
The registry and wallet-aware user domains remain separate. Public registry meaning does not override private canonical wallet-link truth.

### Contract Is Official on One Network but Not Another
Chain/network scope remains explicit. Publication MUST NOT generalize across scopes without explicit registry entries.

### Revocation Is Necessary for Trust Protection
Revocation MUST be explicit, historically preserved, and operationally propagated across derived surfaces as quickly as safety requires.

### Public Surface Shows Stale Data During Incident or Recovery
Canonical registry truth remains the source of correction; derived surfaces must recover or be restricted with trust-preserving messaging rather than inventing ad hoc interpretations.

## Operational Considerations

Operational systems SHOULD support:

- deterministic publication and export pipelines
- discrepancy detection for conflicting chain/network/address bindings
- health monitoring for public APIs, export jobs, and site/publication synchronization
- trust-sensitive alerting for unauthorized mutation attempts, drift, or stale-publication conditions
- bounded emergency restriction/revocation flows with post-review
- runbooks for stale registry surfaces, incorrect public designation, supersession propagation failures, export failures, and public trust incidents
- continuity posture that preserves canonical registry truth even if public projection layers are degraded

Because the registry is part of the public trust layer, its operational posture MUST be platform-grade rather than ad hoc content publishing.

## Migration / Compatibility / Supersession Considerations

Migrations into this canonical model MUST enforce the following:

- legacy address lists, docs pages, or ad hoc website tables must be retired or explicitly marked derived
- historical public labels and aliases MAY be preserved as lineage but MUST NOT silently override canonical current classification
- backward-compatibility lookup aliases MAY continue temporarily, but canonical current registry identity and supersession links MUST remain explicit
- migrations from older public surfaces MUST preserve public intelligibility and correction history where material
- public API or site migrations MUST preserve stable trust semantics, even if route shapes or render paths change
- derived exports and downstream consumers MUST migrate to canonical registry truth rather than continuing local shadow registries

## Implementation-Contract Guardrails

Downstream implementations MUST preserve all of the following:

1. registry publication truth remains off-chain and backend-owned
2. registry entries remain distinct from chain-native truth, wallet-link truth, and governance/control truth
3. public-safe visibility boundaries remain explicit and enforced
4. verification, publication, deprecation, supersession, restriction, and revocation remain explicit lifecycle concepts
5. historical public meaning and successor guidance remain preserved
6. public APIs, exports, and sites remain derived from canonical registry records
7. idempotency, replay safety, and correction lineage remain explicit for trust-sensitive mutations
8. operators and services MUST NOT optimize away audit lineage, evidence references, or publication-state transitions
9. public surfaces MUST NOT leak signer topology, secret material, or unsafe internal control detail
10. downstream teams MUST NOT create local “official address” truth that conflicts with the registry domain

## Downstream Execution Staging

This specification implies the following preferred downstream implementation order:

1. stabilize registry family/type and public-role taxonomy
2. stabilize canonical registry entry, verification, publication-state, and supersession semantics
3. stabilize public lookup/read-model and export behavior
4. integrate transparency/reporting/public metadata linkages
5. integrate partner and public trust verification surfaces
6. integrate discrepancy, revocation, and continuity/remediation tooling

## Required Downstream Specs / Contract Layers

This specification directly requires compatible downstream refinement and implementation-contract work in:

- `PUBLIC_CONTRACT_WALLET_REGISTRY_API_SPEC.md`
- `PUBLIC_REGISTRY_LOOKUP_API_SPEC.md`
- public metadata/public discovery contracts
- registry export and publication contracts
- partner/exchange verification integration contracts
- registry discrepancy/remediation runbooks
- public site/public-doc consumption contracts
- transparency/report linkage contracts where applicable

## Canonical Examples / Anti-Examples

### Canonical Example 1 — Official Token Contract Publication
A contract is deployed on-chain, verified internally, classified as the official token contract for a specific chain/network scope, and then published in the canonical registry. Public lookup surfaces derive from that registry record. The registry does not replace the contract’s on-chain semantics.

### Canonical Example 2 — Deprecated Treasury Wallet With Successor Guidance
A designated public treasury wallet is replaced. The prior entry becomes deprecated or superseded, successor guidance is preserved, and historical public meaning remains intelligible.

### Canonical Example 3 — Transparency Link Without Ownership Transfer
A public registry entry links to a transparency artifact explaining a reporting period or reserve context. The registry owns the link, but transparency reporting still owns the report semantics.

### Canonical Example 4 — Restricted Authenticated Enrichment
A public registry entry remains broadly public, but a bounded authenticated view may expose additional safe context for approved first-party users. This does not make the entry private or transfer ownership away from the registry domain.

### Anti-Example 1 — Static Site as Source of Truth
A docs page is edited manually and becomes the de facto owner of official address truth. This is forbidden.

### Anti-Example 2 — Public Registry as Wallet-Link Truth
A wallet appearing in the public registry is assumed to be canonically linked to a specific user account. This is forbidden.

### Anti-Example 3 — Silent Role Reassignment
A published address changes role meaning without deprecation or supersession lineage. This is forbidden.

### Anti-Example 4 — Registry Exposes Signer/Control Graph
A public registry entry exposes private signer relationships or internal control topology because it is “useful context.” This is forbidden.

## Dependencies / Cross-Spec Links

This specification depends on:

- `REFINED_SYSTEM_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `WALLET_AWARE_USER_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `API_ARCHITECTURE_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `IDEMPOTENCY_AND_VERSIONING_SPEC.md`
- `MIGRATION_AND_BACKWARD_COMPATIBILITY_SPEC.md`
- `AUDIT_LOG_AND_ACTIVITY_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `SECRETS_CONFIG_AND_ENVIRONMENT_SPEC.md`
- `MONITORING_ALERTING_AND_INCIDENT_RESPONSE_SPEC.md`
- `DEPLOYMENT_AND_RUNTIME_OPERATIONS_SPEC.md`
- `BUSINESS_CONTINUITY_AND_RECOVERY_SPEC.md`
- `TRANSPARENCY_MODEL_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`
- `GOVERNANCE_MODEL_SPEC.md`
- `FOUNDATION_GOVERNANCE_SPEC.md`
- `TREASURY_CONTROL_POLICY_SPEC.md`
- `VAULT_ACTION_POLICY_SPEC.md`
- `MULTISIG_AND_TIMELOCK_SPEC.md`
- `PAYOUT_LEDGER_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
- `CHAIN_ARCHITECTURE_SPEC.md`

This specification directly governs or materially informs:

- public contract/wallet registry APIs
- public registry lookup APIs
- public metadata catalogs
- public registry sites and docs surfaces
- transparency/report linkage layers
- payout and eligibility public trust references
- partner/exchange verification surfaces
- operator/admin publication tools
- discrepancy/revocation operational tooling

## Explicitly Deferred Items

The following are intentionally deferred to adjacent or downstream specifications:

- exact contract ABI and chain deployment semantics
- exact static-site generation and public website rendering
- exact partner verification workflow mechanics
- exact explorer integration details
- exact transparency-report schemas and attestation formats
- exact internal approval routing for every treasury/governance category
- exact bounded authenticated enrichment semantics where later needed

These deferrals do not weaken the canonical registry model established here.

## Final Normative Summary

The FUZE public contract and wallet registry domain is the canonical off-chain public publication layer for officially designated contracts and designated public wallets. It owns public registry records, public role classification, publication state, verification lineage, supersession posture, and trust-safe public lookup semantics. It does not own chain-native contract state, wallet-link identity truth, treasury or governance approval truth, transparency-report truth, or private operational wallet topology. It exists so FUZE can publicly designate official addresses in a way that is explicit, auditable, historically intelligible, and safe for partners, users, auditors, and the broader ecosystem.

This document is the canonical FUZE rule set for public contract and wallet registry semantics.

## Quality Gate Checklist

- Canonical owner is explicit for registry publication truth, publication-state truth, verification lineage, and registry supersession truth.
- Mutation boundaries are explicit for internal services, admin/control-plane tools, and derived public surfaces.
- Adjacent boundaries are explicit for chain, wallet-aware user, transparency, governance, treasury, and payout domains.
- Truth classes are explicit and separated.
- Conflict-resolution rules are explicit.
- Default decision rules are explicit for publication uncertainty, role ambiguity, restricted visibility, and discrepancy handling.
- Non-canonical patterns are called out clearly.
- Operator/admin override paths are bounded, reason-coded, and auditable.
- Read-model, export, and public projection rules are explicit.
- Failure and degraded-mode behaviors are explicit.
- Downstream implementation guardrails are explicit.
- Dependencies and downstream impacts are explicit.
- Non-goals and deferred items are explicit.
- The document is strong enough to support downstream API, backend, public trust, reporting, and operational implementation without contradictory semantic invention.
