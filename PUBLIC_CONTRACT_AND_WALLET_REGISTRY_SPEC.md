# PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC

## Purpose

This document defines the canonical public contract and wallet registry model of the FUZE ecosystem. Its purpose is to establish how FUZE identifies, classifies, publishes, maintains, and version-controls the public-facing contracts and wallets that materially affect ecosystem trust, economic interpretation, governance understanding, and transparency reporting.

This specification is foundational because FUZE is designed as a transparency-first platform ecosystem. Transparency in a system of this kind cannot rely on scattered addresses, ad hoc announcements, or informal social explanations. The ecosystem requires one maintained registry that maps important contracts and wallets to their structural role in the architecture. Without such a registry, even well-separated token, reserve, payout, and governance systems become harder to understand over time. The registry is therefore not a minor documentation artifact. It is one of the core public explanation layers of the platform.

---

## Scope

This specification covers:

- the canonical role of the public contract and wallet registry in FUZE
- which contracts and wallets belong in the registry
- how entries are categorized, described, versioned, updated, and deprecated
- the distinction between public structural visibility and internal-only operational address management
- how the registry connects to transparency reporting, payout ledgers, reserve architecture, governance, and chain-role explanation
- visibility, security, correction, and maintenance expectations for registry entries
- audit, publishing, and failure-handling principles for registry operations

This specification does not define every internal secret-management rule, every private infrastructure wallet, or every exact reporting template. Those are refined in:

- `TRANSPARENCY_MODEL_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`
- `AUDIT_LOG_AND_ACTIVITY_SPEC.md`
- `CHAIN_ARCHITECTURE_SPEC.md`
- `TREASURY_AND_RESERVE_ARCHITECTURE_SPEC.md`
- `GOVERNANCE_MODEL_SPEC.md`
- `MULTISIG_AND_TIMELOCK_SPEC.md`
- `PAYOUT_LEDGER_SPEC.md`

---

## Design Goals

The design goals of the FUZE public contract and wallet registry are:

1. to provide a single structured map of trust-relevant public contracts and wallets
2. to make economic and governance roles easier to interpret than raw addresses alone
3. to preserve clear distinctions between token, credits, payouts, reserves, vesting, and control structures
4. to support recurring transparency reporting with a stable reference layer
5. to reduce ambiguity around what an address or contract is for
6. to improve public credibility by maintaining a durable and explainable registry rather than fragmented disclosures
7. to support change management, deprecation, and migration without breaking public intelligibility
8. to preserve transparency while still respecting privacy and security boundaries

---

## Non-Goals

This specification is not intended to:

- publish every internal operational wallet or service address
- expose secrets, unsafe security details, or unnecessary attack-surface information
- replace transparency reports, payout ledgers, or audit systems
- imply that a raw address list without role description is sufficient transparency
- make the registry a dumping ground for low-value or irrelevant addresses
- collapse internal-only infrastructure management into public transparency scope
- allow silent changes to trust-relevant public structures without registry maintenance

---

## Canonical Registry Principle

The primary principle of the FUZE public contract and wallet registry is:

> every public-facing contract or wallet that materially affects ecosystem trust, reserve interpretation, payout understanding, governance visibility, or chain-role clarity should have a maintained registry entry that explains what it is, what chain it lives on, what role it serves, and what its current lifecycle status is.

This means:

- the registry is role-oriented, not merely address-oriented
- every included entry should help a reader understand part of the architecture
- structural visibility should be maintained over time, not only at launch
- replacements, migrations, or deprecations should be visible
- the registry should help make FUZE easier to interpret from first principles

This principle is central because the registry is one of the clearest public bridges between architecture and understanding.

---

## Why FUZE Needs a Public Contract and Wallet Registry

FUZE needs a public contract and wallet registry because the ecosystem includes multiple contracts and public-facing addresses with different meanings, across multiple chains and governance domains.

These include:

- the canonical FUZE token contract on Ethereum
- reserve and vault contracts
- vesting contracts
- Foundation-sensitive structures
- treasury-sensitive structures
- Platform Credits references on Base
- payout execution contracts on Base
- multisig- or governance-relevant public structures
- future upgraded, replaced, or deprecated public-facing contracts

Without a registry, the ecosystem would force users, holders, partners, and investors to reconstruct structural meaning from scattered announcements or raw explorer data. That is a weak transparency pattern. A transparency-first platform should reduce this burden.

The registry solves this by creating a maintained public map of the architecture. It answers questions such as:

- which contract is the canonical FUZE token
- which contract is the current payout contract
- which vault corresponds to Treasury or Foundation
- what chain a given structure lives on
- whether a structure is active, replaced, or legacy
- which public address should be interpreted as a reserve, vesting, or governance structure rather than as ordinary circulation

This is especially important in FUZE because the platform emphasizes role separation. The registry is one of the tools that makes that role separation visible and durable over time.

---

## Registry as Structural Explanation Layer

The public registry should be understood not merely as a list of addresses, but as a structural explanation layer.

A raw address list is insufficient because it forces the reader to infer meaning. A strong registry instead explains:

- what the entry is
- why it exists
- what category it belongs to
- what role it plays in the architecture
- how it relates to other parts of the system
- whether it is current, pending, deprecated, or superseded

This matters because FUZE is not trying to be transparent only in the narrow sense of “addresses can be seen on-chain.” It is trying to be transparent in the stronger sense that the ecosystem can be interpreted coherently.

The registry is therefore one of the main tools that turns on-chain visibility into public understanding.

---

## Registry Coverage Model

The registry should include contracts and wallets that are materially relevant to public trust and ecosystem interpretation.

At minimum, registry coverage should include the following families.

### 1. Token Layer Entries

These entries identify the canonical participation-layer assets and structures.

Examples:
- FUZE token contract on Ethereum
- token-related public references that define canonical holder truth

### 2. Reserve and Vault Entries

These entries identify the major token-side reserve categories.

Examples:
- Foundation Vault
- Treasury Reserve Vault
- Team Vesting Vault
- Advisor Vesting Vault
- Holder Incentives Vault
- Ecosystem Partnership Vault
- Liquidity Operations Vault
- Transparency / Stability Vault

### 3. Migration and Distribution Entries

These entries identify contracts or public structures tied to presale, migration, or major distribution mechanics.

Examples:
- Community Presale contract
- BOARD Migration contract

### 4. Credits Layer Entries

These entries identify public-facing contract structures or ledger references associated with Platform Credits on Base, where public explanation materially benefits trust and platform understanding.

### 5. Payout Execution Entries

These entries identify profit-participation execution structures.

Examples:
- `FuzeStablecoinProfitParticipation`
- cycle-related payout execution references where registry-level visibility is appropriate

### 6. Governance and Control Entries

These entries identify public governance-related structures where disclosure is appropriate and useful.

Examples:
- multisig-controlled public-facing contract owners where appropriate
- timelock controller references where relevant
- governance-critical contract-control structures

### Coverage principle

The registry should include what materially helps explain the ecosystem. It should not attempt to publish every low-value technical address that adds noise without improving trust.

---

## Registry Entry Categories

Each registry entry should be assigned a clear category.

At minimum, FUZE should support categories such as:

- `token_contract`
- `reserve_vault`
- `vesting_vault`
- `migration_contract`
- `presale_contract`
- `credits_layer_contract`
- `payout_contract`
- `governance_control`
- `treasury_wallet`
- `foundation_wallet`
- `public_operations_wallet` where justified
- `deprecated_structure`
- `legacy_reference`

### Why categories matter

Categories make it easier to understand how different entries fit together. A stakeholder should not have to guess whether an address is a reserve vault, a vesting contract, a payout system, or an operational wallet.

Category discipline also improves reporting and deprecation logic over time.

---

## Registry Entry Fields

Each public contract or wallet registry entry should preserve a minimum structured field set.

At minimum, each entry should include:

- `registry_entry_id`
- `public_label`
- `entry_category`
- `chain`
- `network`
- `address`
- `contract_name` where applicable
- `role_description`
- `status`
- `introduced_at` or equivalent publication reference
- `superseded_by` where applicable
- `supersedes` where applicable
- `policy_reference` where relevant
- `reporting_reference` where relevant
- `notes`
- `public_url_reference` where appropriate

### Role of structured fields

These fields ensure the registry is more than a raw address book. They allow the ecosystem to present a public map with continuity, interpretation, and lifecycle clarity.

---

## Status Model for Registry Entries

Registry entries should support lifecycle status.

At minimum, the registry should support statuses such as:

- `active`
- `pending_activation`
- `deprecated`
- `superseded`
- `legacy`
- `paused` where public explanation is appropriate
- `retired`

### Why status matters

Public trust weakens when contracts or wallets remain listed without clear indication that they are no longer current. A strong registry should allow readers to know which structures are current and which belong to prior architecture phases.

### Status principle

The registry should preserve historical context without confusing current operational truth.

---

## Canonical Token Registry Requirements

The registry should clearly identify the canonical FUZE token contract.

At minimum, the token entry should make clear:

- that this is the canonical FUZE token contract
- that it lives on Ethereum mainnet
- that it is the participation-layer source of holder truth
- that it is distinct from Platform Credits and payout contracts
- how it relates to snapshot-based eligibility in a structural sense

### Principle

The token entry is one of the most important entries in the registry because it anchors the ecosystem’s participation layer. It should therefore be especially clear and durable.

---

## Reserve and Vault Registry Requirements

The registry should identify each major reserve or vault structure with clear role descriptions.

At minimum, each reserve or vault entry should state:

- reserve category
- chain and address
- role description
- whether the structure is a reserve vault, vesting vault, or other token-side allocation structure
- whether the structure is active or has been replaced
- where its policy or governance logic is described at a higher level

### Why this matters

Reserve separation is one of the strongest transparency features of FUZE. The registry is where that separation becomes publicly navigable.

Without clear reserve entries, the platform’s differentiated reserve architecture becomes harder to interpret from the outside.

---

## Payout Contract Registry Requirements

The payout execution layer should be represented explicitly in the registry.

At minimum, the registry should clarify:

- the canonical payout contract identity
- that payout execution occurs on Base
- that the payout contract is distinct from the token and credits layers
- that payout cycles are referenced through the payout ledger and related transparency surfaces
- whether the payout contract is active, upgraded, or superseded

### Principle

The payout contract is a major holder-facing trust surface and therefore belongs clearly in the registry.

---

## Credits Layer Registry Requirements

The Platform Credits layer should also be represented in the registry to the extent needed for public understanding.

At minimum, credits-related registry visibility should support:

- identification of the Base-based credits execution layer
- structural clarity that credits are the internal consumption system
- distinction from both the FUZE token and the stablecoin payout system
- references to policy or reporting that explain how the credits layer behaves

### Important boundary

The registry does not need to expose every internal billing implementation detail. It should expose enough to support architecture clarity.

---

## Governance and Control Registry Requirements

Where public disclosure is appropriate and safe, the registry should also include governance-relevant structures.

This may include:
- multisig-controlled public contract roles
- timelock controller structures
- public governance-control contracts or addresses
- category-specific control references tied to reserve or payout infrastructure

### Important boundary

The registry should improve trust without unnecessarily expanding security exposure. Not every internal operator detail needs to be published. What matters is that the ecosystem can understand the governance architecture at the public-structure level.

---

## Registry and Transparency Reporting Relationship

The public registry and transparency reports should reinforce each other.

### Registry provides:
- stable structural references
- current contract and wallet map
- lifecycle status
- role descriptions

### Transparency reports provide:
- narrative explanation
- event summaries
- policy interpretation
- changes over time
- contextual public understanding

### Principle

A strong transparency report should reference the registry rather than restating raw address lists inconsistently. A strong registry should remain stable enough that reports can build on it over time.

---

## Registry and Audit Relationship

Registry changes should also be grounded in internal auditability.

Meaningful registry actions should preserve internal lineage for:

- entry creation
- entry update
- status change
- deprecation
- supersession
- publication or correction

### Principle

A registry is a public explanation layer, but its maintenance should still be supported by durable internal records. This helps reduce accidental drift between actual architecture and published architecture.

---

## Registry Change Management

The registry should support structured change management rather than informal updates.

At minimum, FUZE should recognize the following registry change types:

- new entry creation
- role-description update
- status update
- deprecation
- supersession
- chain migration reference
- correction of prior publication error

### Change-management principle

Registry changes affecting trust-relevant structures should be deliberate, reviewable, and compatible with transparency reporting. Silent major changes weaken confidence in the registry.

---

## Deprecation and Supersession Rules

When public-facing contracts or wallets are replaced, the registry should preserve historical integrity.

### Deprecation rules

A deprecated entry should remain visible enough that prior references do not become meaningless.

### Supersession rules

If a contract or wallet is replaced, the registry should make the replacement relationship explicit by linking:
- old structure -> new structure
- new structure -> prior structure where useful

### Principle

The registry should preserve continuity of understanding across architectural evolution. This is especially important for token ecosystems where observers may compare addresses across time.

---

## Privacy and Security Boundaries

The public registry must preserve clear privacy and security boundaries.

The registry should avoid publishing:

- private infrastructure endpoints
- secret-bearing accounts
- sensitive operational addresses that add attack surface without improving public understanding
- internal-only incident-response pathways
- unsafe operational detail that is not necessary for trust interpretation

### Boundary principle

The purpose of the registry is to explain trust-relevant public structure, not to expose every internal technical component. Good transparency requires judgment.

---

## Registry Failure Modes

The public registry can fail in several ways if poorly maintained.

### 1. Omission Failure
Important trust-relevant structures are missing from the registry.

### 2. Role-Ambiguity Failure
Addresses are listed without clear role descriptions.

### 3. Status Drift Failure
Deprecated or replaced contracts remain indistinguishable from active ones.

### 4. Reporting Drift Failure
Registry state diverges from transparency reports or public explanations.

### 5. Security Overexposure Failure
Too much unsafe operational detail is published under the banner of transparency.

### 6. Silent Change Failure
Meaningful registry changes occur without enough visibility or explanation.

### Principle

A registry is only useful if it is current, interpretable, bounded, and maintained with discipline.

---

## Safeguards for Registry Quality

FUZE should protect registry quality through several safeguards.

### 1. Category Discipline
Every entry should have a clear role category.

### 2. Status Discipline
Every meaningful public structure should have visible lifecycle status.

### 3. Reporting Linkage
Registry changes should connect to transparency reporting where relevant.

### 4. Audit Grounding
Internal records should support registry maintenance and correction.

### 5. Structural Terminology Discipline
Registry descriptions should use consistent language for token, credits, payouts, reserves, governance, and chain roles.

### 6. Correction Visibility
Meaningful public registry corrections should not be silently hidden.

### 7. Ownership Discipline
Registry maintenance should have clear internal ownership and review expectations.

These safeguards help make the registry a durable trust surface rather than a one-time launch artifact.

---

## Minimum Architectural Entities

At minimum, the public contract and wallet registry should recognize the following conceptual entities:

### Registry Entry Entities
- `registry_entry_id`
- `public_label`
- `entry_category`
- `chain`
- `network`
- `address`
- `role_description`
- `status`

### Lifecycle Entities
- `introduced_at`
- `updated_at`
- `deprecated_at` where applicable
- `superseded_by`
- `supersedes`

### Linkage Entities
- `policy_reference`
- `reporting_reference`
- `audit_lineage_reference`
- `payout_cycle_reference` where applicable
- `governance_action_reference` where applicable

### Boundary Entities
- `visibility_scope`
- `security_sensitivity_reference`
- `disclosure_note`

These are minimum conceptual entities. Detailed publishing format and workflow are refined downstream.

---

## Open Items

The following areas are intentionally refined in downstream implementation and publication procedures:

- exact registry schema and publishing format
- exact disclosure depth for governance-control entries
- exact review workflow before registry changes go live
- exact integration between registry and transparency reports
- exact deprecation-notice format for replaced structures
- exact public interface for browsing registry history over time

These do not weaken the canonical public contract and wallet registry model established here.

---

## Closing Summary

The FUZE public contract and wallet registry is the maintained public map of the ecosystem’s trust-relevant contracts and wallets. It exists to make token, reserve, vesting, payout, credits, and governance structures easier to understand than raw addresses alone, while preserving change history, lifecycle status, and role clarity over time. By treating the registry as a structured explanation layer rather than a simple address list, FUZE strengthens one of the most important public trust surfaces in its transparency-first architecture.
