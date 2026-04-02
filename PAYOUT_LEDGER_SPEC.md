# PAYOUT_LEDGER_SPEC

## Purpose

This document defines the canonical payout ledger architecture of the FUZE ecosystem. Its purpose is to establish how FUZE records, organizes, publishes, and reconciles stablecoin profit-participation cycle data so that funded payout activity remains explainable, auditable, and consistent with the transparency-first architecture of the platform.

This specification is foundational because profit participation is one of the most trust-sensitive systems in FUZE. The payout contract on Base may execute claims correctly at the contract level, but the ecosystem still requires a structured ledger layer that explains what payout cycles exist, how they were funded, what their state is, how claims progressed, and how each cycle relates to the wider architecture of eligibility, treasury authorization, reporting, and governance. The payout ledger is therefore not a cosmetic reporting table. It is one of the core structural trust surfaces of the holder-alignment model.

---

## Scope

This specification covers:

- the canonical role of the payout ledger in the FUZE ecosystem
- how payout-ledger records differ from payout contracts, internal audit logs, and transparency reports
- what cycle-level and claim-level information the payout ledger must preserve
- how payout cycles are identified, structured, versioned, corrected, and closed
- how the payout ledger connects Ethereum-based eligibility, Base-based payout execution, treasury authorization, and reporting
- visibility, reconciliation, and audit expectations for payout-ledger records
- correction, failure-handling, and public trust principles for payout-ledger maintenance

This specification does not define the full payout-contract ABI, the full snapshot-proof construction format, or the complete public-report publication templates. Those are refined in:

- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `BASE_PAYOUT_EXECUTION_LAYER_SPEC.md`
- `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`
- `AUDIT_LOG_AND_ACTIVITY_SPEC.md`
- `PUBLIC_CONTRACT_AND_WALLET_REGISTRY_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`

---

## Design Goals

The design goals of the FUZE payout ledger are:

1. to provide a durable and intelligible record of all profit-participation payout cycles
2. to make funded payout activity easier to interpret than raw contract activity alone
3. to preserve strong linkage between eligibility preparation, funding, claim execution, and reporting
4. to distinguish cycle-level public truth from internal-only operational audit detail
5. to support auditability, reconciliation, and holder-facing trust
6. to make corrections explicit rather than silently rewriting payout history
7. to support public visibility without exposing unnecessary sensitive operational detail
8. to strengthen the credibility of the stablecoin profit-participation model over time

---

## Non-Goals

This specification is not intended to:

- replace the Base payout contract as the source of on-chain execution truth
- expose every internal eligibility-processing detail publicly in raw form
- make the payout ledger identical to a user-specific claim history interface
- replace internal audit or finance reconciliation systems
- imply that the payout ledger alone determines eligibility or funding policy
- publish sensitive security details or unsafe operational data
- turn payout records into vague narrative summaries without structured lineage

---

## Canonical Payout Ledger Principle

The primary principle of the FUZE payout ledger is:

> every profit-participation cycle must have a structured, durable, and explainable ledger record that links cycle identity, funding, eligibility basis, claim state, and reporting references without blurring on-chain execution, policy interpretation, or internal audit domains.

This means:

- the ledger should record cycles as formal ecosystem events
- each cycle should be identifiable independently of transient announcements
- the ledger should preserve enough structure to explain what happened and why it matters
- contract execution, eligibility preparation, and treasury authorization should remain linkable through the ledger
- corrections should append to or supersede prior records transparently rather than erasing trust-sensitive history

This principle is central because the payout ledger is one of the clearest places where FUZE’s transparency-first philosophy becomes operational and holder-visible.

---

## Why FUZE Needs a Dedicated Payout Ledger

FUZE needs a dedicated payout ledger because profit participation is not a single on-chain transfer event. It is a multi-stage ecosystem process involving:

- treasury and accounting finalization of policy-defined distributable profit
- Ethereum-based holder snapshot and eligibility preparation
- cycle-specific entitlement construction
- stablecoin funding of the payout contract on Base
- claim opening and claim execution
- cycle closure and reporting

Raw contract visibility alone does not explain all of these stages. A user may be able to inspect a contract balance or a claim transaction, but that does not automatically explain:

- what cycle this funding belongs to
- what period or policy version it corresponds to
- whether eligibility was prepared from the correct dataset
- whether the cycle is currently open, closed, funded, or superseded
- whether a correction occurred
- how the cycle fits into the broader profit-participation history of the platform

The payout ledger solves this by giving the ecosystem a structured, cycle-oriented record of profit participation.

This matters especially because FUZE is not describing profit participation as a vague reward stream. It is describing a policy-defined, cycle-based stablecoin payout architecture. Such a model becomes materially stronger when cycle history is maintained in a clear ledger form rather than scattered across announcements, contract activity, and internal records.

The payout ledger therefore acts as the public-facing structural memory of the payout system.

---

## Payout Ledger vs Related Systems

FUZE should explicitly distinguish the payout ledger from adjacent systems.

### Base Payout Contract
The Base payout contract is the on-chain execution layer for funded claim cycles. It is authoritative for claim execution state and funded contract-side behavior, but it is not by itself the full explanation layer for payout history.

### Snapshot and Eligibility Pipeline
The eligibility pipeline constructs the entitlement basis for a cycle. It informs the ledger, but it is not the ledger itself.

### Internal Audit Records
Internal audit systems record richer lineage for approval, preparation, correction, and review. The payout ledger may be derived from or linked to those records but should remain a clearer and more bounded transparency surface.

### Transparency Reports
Transparency reports explain payout behavior at a narrative or summary level. The payout ledger provides the structured cycle record those reports can reference.

### User Claim History
A user-facing claim history may show a particular holder’s claims. The payout ledger is broader. It records cycle-level ecosystem truth, not merely individual-user activity.

### Principle

The payout ledger is the structured public or semi-public cycle record that connects the payout contract, eligibility preparation, treasury authorization, and transparency reporting without replacing any of them.

---

## Canonical Role of the Payout Ledger

The payout ledger exists to preserve formal records of profit-participation cycles.

At minimum, it should allow the ecosystem to answer questions such as:

- what payout cycles exist
- when each cycle was created, funded, opened, closed, or corrected
- what the funded amount was
- which payout asset was used
- what the structural eligibility basis was
- what the cycle status is
- whether claims occurred and what the aggregate claim state is
- what governance or reporting references are associated with the cycle

This means the payout ledger is not just a convenience for explorers or dashboards. It is one of the primary structural records that makes the payout model intelligible to stakeholders.

A strong payout ledger helps FUZE demonstrate that profit participation is:
- formal,
- cycle-based,
- policy-governed,
- and transparently maintained over time.

That is one of the reasons this ledger deserves its own explicit architectural definition.

---

## Core Payout Ledger Concepts

### Payout Cycle
A bounded distribution event in which a policy-defined stablecoin amount is funded and made claimable for eligible holders.

### Cycle Identifier
A unique reference that distinguishes one payout cycle from every other cycle.

### Eligibility Basis
The structural reference to the Ethereum-derived snapshot and policy treatment used to determine entitlement for the cycle.

### Funding Record
The record of stablecoin amount and associated funding state for the cycle on Base.

### Claim State
The ledger-visible state describing whether the cycle is not yet open, open for claims, partially claimed, fully concluded, or otherwise administratively closed.

### Ledger Status
The canonical lifecycle state of the payout ledger entry itself, including whether it is active, superseded, corrected, cancelled before launch, or fully closed.

### Reporting Reference
A linkage from the payout ledger entry to transparency reporting and related public explanation surfaces.

These concepts are needed because the payout system is easiest to trust when each cycle is formalized as a recognizable object rather than as an informal cluster of transactions.

---

## Payout Cycle Identity and Versioning

Every payout cycle should have a stable, unique identity in the payout ledger.

This identity should allow the ecosystem to track a cycle through its full lifecycle:

- cycle initialization
- funding preparation
- funding execution
- claim opening
- claim activity
- closure
- correction or superseding action if required

### Identity principles

- cycle identifiers should not be reused
- a cycle should remain identifiable even if later corrected or superseded
- cycles should be linkable to their associated reporting windows or economic periods where appropriate
- cycle identity should remain durable enough that outside references do not break when reporting evolves

### Versioning principles

If a cycle record is corrected, supplemented, or superseded, the payout ledger should preserve that relationship explicitly. A later version should not silently overwrite the earlier record in a way that erases historical meaning.

This is especially important because payout cycles are trust-sensitive economic events. Silent mutation of cycle history would weaken the transparency model.

---

## Minimum Payout Ledger Record Structure

At minimum, each payout-ledger cycle record should preserve:

- `payout_cycle_id`
- `cycle_status`
- `cycle_version`
- `policy_version_reference`
- `snapshot_reference`
- `eligibility_basis_reference`
- `payout_asset`
- `payout_chain`
- `payout_contract_reference`
- `funded_amount`
- `funded_at`
- `claims_opened_at` where applicable
- `claims_closed_at` where applicable
- `ledger_published_at`
- `reporting_reference`
- `governance_action_reference` where applicable
- `supersedes_cycle_id` where applicable
- `correction_reason_code` where applicable
- `audit_lineage_reference`
- `notes_or_status_explanation` where appropriate

### Why these fields matter

The payout ledger should not merely say “cycle happened.” It should preserve enough structured data that a serious reader can understand:

- which cycle they are looking at
- what amount was funded
- what structural basis determined eligibility
- whether the cycle is open, closed, or corrected
- and how the cycle connects to reports and governance history

This is part of what makes the payout ledger a durable trust layer rather than a temporary announcement record.

---

## Cycle Lifecycle in the Ledger

The payout ledger should reflect the full lifecycle of each payout cycle.

### 1. Pre-Funding Initialization
A cycle may be initialized internally before funding, but public or broadly visible ledger publication should occur according to policy once the cycle is mature enough to be meaningfully explained.

### 2. Funding Recorded
Once the stablecoin amount is funded on Base, the ledger should record the cycle as funded.

### 3. Claims Open
When claims become active, the ledger should update to reflect that state.

### 4. Active Claim Period
The ledger should preserve the fact that the cycle is in active claim state even though individual claim transactions may be tracked separately or in aggregate views.

### 5. Closure
When the cycle reaches its claim-close or finalization condition, the ledger should record the closure status.

### 6. Correction / Supersession if Needed
If a cycle requires correction, containment, or superseding record logic, the ledger should preserve that state explicitly.

### Lifecycle principle

The payout ledger should model cycles as lifecycle objects rather than as one-time funding events. This improves intelligibility and makes reporting much stronger.

---

## Eligibility and Snapshot References in the Ledger

The payout ledger should preserve the structural basis of eligibility without necessarily exposing every raw internal processing artifact publicly.

At minimum, each cycle should reference:

- the snapshot identifier or reference point
- the applicable policy version
- the eligibility basis reference or dataset identifier
- the fact that eligibility derives from Ethereum-based FUZE holder balances
- any important structural treatment notes that materially affect public understanding

### Important boundary

The ledger does not need to expose unsafe or excessive raw processing detail. It does need to preserve enough structure that stakeholders can understand that eligibility was not improvised.

### Principle

The payout ledger should make clear that:
- eligibility comes from Ethereum participation truth,
- policy treatment was applied,
- and the resulting cycle entitlements were derived through a defined process.

This is one of the most important explanatory functions of the ledger.

---

## Funding and Asset Recording

The payout ledger should explicitly record cycle funding details.

At minimum, this includes:

- payout asset type
- funded amount
- funding chain
- payout contract reference
- funding timestamp
- cycle status at funding

### Why funding records matter

Funding is one of the clearest moments where the profit-participation model becomes economically real. A cycle may be discussed conceptually, but once it is funded, it becomes a holder-facing economic event.

The ledger should therefore preserve funding state in a way that is easy to understand and link to both:
- contract execution,
- and treasury/accounting authorization.

### Important distinction

Funding in the ledger should not be described as though it automatically explains the accounting logic that produced it. The ledger should preserve linkage to the relevant reporting or policy references rather than pretending the funding number alone tells the whole economic story.

---

## Claim State and Aggregation

The payout ledger should preserve claim-state visibility at the cycle level.

This may include:

- whether claims are open
- whether claims are closed
- whether the cycle is partially claimed or fully matured in its claim lifecycle
- high-level aggregate claim progress where policy chooses to expose it
- any closure or residual-handling state once the cycle ends

### Important boundary

The payout ledger is not required to publish full raw personal claim data for every user in the main cycle record. Individual claim-level views may exist in adjacent systems. The main ledger should remain focused on cycle-level intelligibility.

### Principle

The ledger should help users understand the status of a cycle without forcing them to inspect every claim transaction manually.

This is especially important because a transparency-first payout system should make the state of the cycle easier to interpret than raw chain activity alone.

---

## Payout Ledger and Transparency Reporting Relationship

The payout ledger and transparency reporting system should reinforce one another.

### The payout ledger provides:
- structured cycle records
- formal identifiers
- state history
- funding and status references
- correction lineage

### Transparency reports provide:
- narrative explanation
- policy context
- broader ecosystem interpretation
- cycle summary framing
- connection to treasury and participation model

### Principle

The payout ledger should be the structured trust record.  
The transparency report should be the explanatory layer built around it.

This separation matters because it keeps the ledger durable and systematic while allowing reports to remain readable and strategically useful.

---

## Payout Ledger and Public Registry Relationship

The payout ledger should also remain linked to the public contract and wallet registry.

This helps the ecosystem map:

- payout cycles
- payout contract identity
- chain role
- related public-facing contract references
- status of current or deprecated payout infrastructure

### Principle

A reader should not need to reconstruct the payout architecture manually across many sources. The ledger and registry should work together to make the payout system easier to understand.

---

## Payout Ledger Visibility Classes

FUZE may support more than one visibility surface for payout-ledger data.

### Public Visibility
A public-facing cycle ledger should preserve enough structure to support holder trust and ecosystem transparency.

### Expanded Operator Visibility
Internal or operator-facing views may contain richer metadata for finance, governance, support, or incident review.

### Restricted Audit Visibility
Deep audit linkage, entitlement-preparation internals, or incident-response fields may remain restricted to appropriate internal roles.

### Principle

Strong transparency does not require exposing all internal fields equally. It requires that the important public structure of cycles remain visible while deeper operational fields stay appropriately protected.

---

## Corrections, Supersession, and Historical Integrity

The payout ledger must preserve historical integrity.

### Core principle

If a payout-ledger entry needs correction, the preferred model is:
- preserve the original record,
- issue a correction or superseding record,
- and make the relationship visible.

### Why this matters

Profit-participation cycles are trust-sensitive. If public cycle records can be silently rewritten, the transparency model weakens materially.

### Correction cases may include:
- pre-open correction to cycle status
- funding reference correction
- reporting-reference correction
- superseding cycle after a contained launch issue
- closure-status correction after delayed reconciliation

### Principle

Corrections should increase trust by showing that the system can acknowledge and trace change, not by pretending prior public understanding never existed.

---

## Reconciliation Requirements

The payout ledger should support reconciliation across several domains.

At minimum, reconciliation should be possible between:

- payout-ledger cycle records
- Base payout contract funding state
- claim-state summaries
- internal audit records
- treasury/accounting funding authorization
- snapshot and eligibility references
- transparency reports

### Why reconciliation matters

A payout ledger is only as credible as its consistency with the systems it represents. If the ledger says a cycle is funded but contract state or reporting says otherwise, trust weakens quickly.

### Principle

The payout ledger should therefore be treated as a formal reconciliation surface, not only as a reporting convenience.

---

## Governance and Audit Expectations

The payout ledger should support governance-sensitive review and long-term auditability.

At minimum, the ledger should make it easier to answer:

- who authorized the cycle or its funding pathway
- what policy version applied
- what eligibility basis was used
- whether a correction occurred
- what reporting references were published
- whether the cycle followed the expected architecture

### Principle

The payout ledger is not only for users. It is also for institutional memory. Years later, FUZE should still be able to explain how a particular cycle was structured and executed.

---

## Privacy and Security Boundaries

The payout ledger should preserve trust-sensitive visibility without exposing unnecessary security or privacy risks.

The ledger should avoid turning into a raw dump of:

- sensitive internal eligibility-processing details
- user-private metadata unrelated to claim transparency
- unsafe operational response details
- internal-only incident triage information
- unnecessary mappings that weaken security without improving trust

### Boundary principle

The payout ledger should publish what stakeholders need to understand cycle structure, not every internal artifact used to build it.

---

## Failure Modes and Safeguards

The payout ledger can fail in several ways if poorly designed.

### 1. Structural Ambiguity
Cycles exist but are not clearly identifiable or comparable over time.

### 2. Silent Correction
Historical payout understanding changes without visible correction lineage.

### 3. Reporting Drift
Ledger state and transparency reports diverge.

### 4. Contract Drift
Ledger records do not reconcile with actual payout-contract state.

### 5. Eligibility Opaqueness
Ledger entries give no usable clue about how the cycle’s eligibility basis was determined.

### 6. Overexposure or Underexposure
The ledger either reveals too much raw operational detail or too little structural meaning.

### Safeguard principles

FUZE reduces these risks through:
- cycle identifiers
- structured field requirements
- correction lineage
- registry linkage
- audit-grounded ledger maintenance
- reconciliation with contract and reporting systems
- explicit separation between public ledger and internal-only audit layers

These safeguards help make the payout ledger a strong long-term trust surface.

---

## Minimum Architectural Entities

At minimum, the payout ledger should recognize the following conceptual entities:

### Cycle Record Entities
- `payout_cycle_id`
- `cycle_version`
- `cycle_status`
- `funded_amount`
- `payout_asset`
- `payout_chain`
- `funded_at`
- `claims_opened_at`
- `claims_closed_at`

### Structural Linkage Entities
- `snapshot_reference`
- `eligibility_basis_reference`
- `policy_version_reference`
- `payout_contract_reference`
- `governance_action_reference`
- `reporting_reference`
- `registry_reference`

### Correction and Integrity Entities
- `supersedes_cycle_id`
- `correction_reason_code`
- `correction_published_at`
- `audit_lineage_reference`

### Visibility Entities
- `visibility_class`
- `public_status_label`
- `operator_status_reference`
- `notes_or_explanation_reference`

These are minimum conceptual entities. Detailed schema and publication formats are refined downstream.

---

## Open Items

The following areas are intentionally refined in downstream implementation and operational policy:

- exact public schema of the payout ledger
- exact aggregate claim metrics exposed publicly
- exact cycle-closure and residual-handling reporting fields
- exact integration with the public contract registry
- exact correction and supersession workflow tooling
- exact operator-review procedures before cycle publication or correction

These do not weaken the canonical payout ledger model established here.

---

## Closing Summary

The FUZE payout ledger is the structured cycle record of the stablecoin profit-participation system. It exists to make payout cycles identifiable, explainable, reconcilable, and transparently maintainable over time by linking eligibility basis, funding state, claim status, reporting references, and correction lineage into one durable ledger surface. By treating payout history as a formal ledger rather than as scattered announcements or raw contract activity alone, FUZE strengthens one of the most important trust-bearing parts of its ecosystem architecture.
