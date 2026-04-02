# VAULT_ACTION_POLICY_SPEC

## Purpose

This document defines the canonical vault action policy of the FUZE ecosystem. Its purpose is to establish what kinds of actions may be performed against each major vault or reserve contract, how those actions are classified, what policy boundaries apply to them, how vault-specific meaning is preserved over time, and how governance, execution, reporting, and emergency controls should interact with vault-level behavior.

This specification is foundational because the existence of separated vaults alone does not guarantee disciplined reserve behavior. A reserve architecture can still become ambiguous if the system lacks a clear rule set describing what a vault is actually allowed to do. FUZE therefore treats vault action policy as the layer that translates reserve architecture into operationally meaningful behavior. It is the policy system that protects vault purpose from gradual erosion through ad hoc exceptions, convenience-driven reuse, or category drift.

---

## Scope

This specification covers:

- the canonical action-policy model for FUZE vaults and reserve contracts
- the distinction between vault existence and vault behavior
- how actions are classified across observation, administration, internal coordination, deployment, payout-related, and emergency contexts
- how action policy differs by vault category
- the relationship between vault action policy and treasury control policy
- the relationship between vault action policy and Foundation governance
- destination, timing, and sensitivity constraints for vault-level actions
- approval, execution, and reporting expectations for vault-sensitive activity
- exceptional-case handling, pause controls, and recovery principles
- audit and transparency expectations for vault-level behavior

This specification does not define every contract ABI, every timelock parameter, or every reserve accounting rule. Those are refined in:

- `TREASURY_AND_RESERVE_ARCHITECTURE_SPEC.md`
- `TREASURY_CONTROL_POLICY_SPEC.md`
- `FOUNDATION_GOVERNANCE_SPEC.md`
- `MULTISIG_AND_TIMELOCK_SPEC.md`
- `SMART_CONTRACT_ARCHITECTURE_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`

---

## Design Goals

The design goals of the FUZE vault action policy are:

1. to make vault purpose operationally enforceable rather than merely descriptive
2. to prevent vault-category drift through clear action boundaries
3. to define which actions are appropriate, restricted, or disallowed for each vault type
4. to preserve stronger protection for stewardship and trust-sensitive vaults
5. to ensure deployment-oriented vaults still operate within bounded and explainable rules
6. to create a reusable action-policy vocabulary across the reserve architecture
7. to improve governance quality by tying approval requirements to action class and vault meaning
8. to make vault behavior easier to report, audit, and defend publicly over time

---

## Non-Goals

This specification is not intended to:

- make all vaults operationally identical
- allow category-specific exceptions to become a substitute for policy clarity
- turn every vault into a generic capital source
- replace treasury control policy with contract-only assumptions
- define vault behavior purely through narrative labels with no action model
- make emergency pathways broad enough to dissolve vault boundaries
- imply that every technically possible movement from a vault is economically appropriate

---

## Canonical Vault Action Principle

The primary principle of FUZE vault action policy is:

> every vault should be able to perform only those classes of actions that are consistent with its published economic role, and those actions should remain bounded by approval, destination, sensitivity, and reporting rules appropriate to that role.

This means:

- vault identity and vault behavior must match
- a vault’s purpose should constrain what it may do
- the same action may be acceptable for one vault and inappropriate for another
- policy should be designed to preserve meaning, not merely allow execution
- exceptions should be rare, explicit, and governance-sensitive

This principle is one of the most important safeguards against the slow collapse of reserve architecture into informal treasury discretion.

---

## Why Vaults Need Action Policy

FUZE uses a separated reserve architecture with purpose-specific structures such as the Foundation Vault, Treasury Reserve Vault, Team Vesting Vault, Advisor Vesting Vault, Holder Incentives Vault, Ecosystem Partnership Vault, Liquidity Operations Vault, and Transparency / Stability Vault.

That separation is architecturally strong, but it does not answer all the questions that matter in practice.

For example:

- May a partnership vault fund a treasury shortfall?
- May a transparency/stability vault support ordinary campaign activity?
- May a team vesting vault be redirected because of operating urgency?
- May a Foundation vault participate in payout-related logic without explicit policy?
- May a liquidity vault route through generic intermediate addresses?

Without a vault action policy, those questions are often answered informally over time. That is where reserve architecture begins to weaken. The vault still exists, but its economic meaning erodes through repeated “reasonable” exceptions.

FUZE avoids that outcome by using a vault action policy layer that says, in effect:

- what kinds of actions are consistent with each vault,
- what actions require stronger scrutiny,
- what actions should never happen through that vault,
- and what reporting or governance expectations attach to the action.

This transforms the vault system from static storage architecture into living economic policy architecture.

---

## Vaults Covered by This Policy

This action policy applies to the major token-side reserve and reserve-adjacent vault structures of FUZE, including:

- Foundation Vault
- Treasury Reserve Vault
- Team Vesting Vault
- Advisor Vesting Vault
- Holder Incentives Vault
- Ecosystem Partnership Vault
- Liquidity Operations Vault
- Transparency / Stability Vault

It also applies, where relevant, to any future reserve-class vault introduced into the canonical token architecture.

### Important distinction

Not all covered vaults are “treasury” in the same sense.

For example:

- Treasury Reserve is a strategic reserve
- Foundation is a stewardship structure
- Team and Advisor vaults are alignment structures
- Holder Incentives and Ecosystem Partnership vaults are bounded deployment structures
- Liquidity Operations is a market-sensitive deployment structure
- Transparency / Stability is a trust-sensitive bounded structure

This is why one shared vault action policy is useful only if it is explicitly category-aware.

---

## Vault Action Classes

FUZE vault action policy should define clear action classes that can be applied consistently across vault types.

### 1. Observation Actions

Observation actions do not move value or alter vault control materially. They exist to support visibility, reporting, and status awareness.

Examples:
- balance observation
- public registry refresh
- vault-state reporting
- attestation publication
- policy-reference inspection

Observation actions are generally low sensitivity, but they remain important because vault transparency is part of the trust model.

### 2. Administrative Control Actions

Administrative control actions modify governance or control posture without directly deploying vault value.

Examples:
- signer rotation
- timelock-role update
- registry pointer update
- control-role configuration
- emergency-contact update where applicable

These actions may not move funds, but they can materially affect future vault risk and therefore require meaningful governance treatment.

### 3. Internal Coordination Actions

Internal coordination actions prepare, align, or reconcile vault state without constituting outward economic deployment.

Examples:
- pre-approved vault-internal migration
- contract synchronization after formal governance approval
- category-consistent reconciliation step
- setup for authorized downstream action

Internal coordination actions should remain tightly bounded because “internal” is often where vault drift begins if policy is weak.

### 4. Category Deployment Actions

Category deployment actions use vault value in a way consistent with the stated role of that vault.

Examples:
- Treasury Reserve funding a policy-approved strategic initiative
- Holder Incentives funding a holder-facing program
- Ecosystem Partnership funding a strategic collaboration
- Liquidity Operations performing a policy-bounded liquidity action
- Transparency / Stability supporting a narrowly approved trust-protective function

These actions are among the most important and most sensitive because they are where vault purpose becomes visible in practice.

### 5. Beneficiary Release Actions

Beneficiary release actions apply specifically to vesting-style or beneficiary-bound vaults.

Examples:
- vesting release to team beneficiary
- advisor unlock according to schedule
- beneficiary claim after milestone or time condition

These actions should follow rules rooted in vesting logic rather than in ordinary treasury deployment logic.

### 6. Payout-Related Actions

Payout-related actions connect vault logic to funded stablecoin profit participation or related holder-facing execution where explicitly allowed.

Examples:
- treasury-authorized funding pathway related to profit participation
- Foundation treatment handling where policy permits and documents such interaction
- linked cycle-reference publication where vault action is relevant to cycle execution

Because profit participation is highly trust-sensitive, payout-related actions should be treated as high or critical sensitivity depending on context.

### 7. Exceptional or Emergency Actions

Exceptional or emergency actions are narrow, protective, and recovery-oriented actions activated when normal operation would expose the ecosystem to unacceptable risk.

Examples:
- freeze or pause
- emergency destination block
- rapid containment after detected compromise
- exceptional protective action pending full review

Emergency action should preserve assets and vault meaning, not become an informal override of policy.

---

## Action Sensitivity Framework

Every vault action should also be evaluated through a sensitivity framework.

### Low Sensitivity
Observation-only actions and certain low-risk administrative updates that do not materially affect control or capital meaning.

### Moderate Sensitivity
Administrative or coordination actions that affect governance posture or vault readiness but do not yet deploy material value.

### High Sensitivity
Material category deployment, beneficiary releases of meaningful size, payout-related funding logic, or role changes that materially alter control pathways.

### Critical Sensitivity
Foundation-principal-sensitive action, Transparency / Stability category-sensitive action, emergency intervention, major category reallocation, or any action that could materially change public trust assumptions about the vault.

### Sensitivity principle

Sensitivity should be determined by both:
- the action itself,
- and the vault it is applied to.

The same transfer amount may be moderate in a large treasury context but critical in a trust-sensitive or principal-protected context.

---

## Shared Policy Rules Across All Vaults

Certain action-policy rules should apply across all vaults, regardless of category.

### Rule 1: Purpose Consistency
A vault action must be consistent with the published purpose of the vault category.

### Rule 2: Explicit Authorization
A vault action must have a defined approval path. No material vault action should occur based solely on operator convenience.

### Rule 3: Destination Legibility
A vault action must have a destination and use rationale that can be explained structurally.

### Rule 4: Audit Lineage
A vault action must preserve enough metadata, governance linkage, and reporting context to remain explainable later.

### Rule 5: Category Preservation
A vault action should preserve category meaning rather than erode it.

### Rule 6: Emergency Narrowness
Emergency controls should protect the vault without becoming a backdoor for ordinary discretionary use.

### Rule 7: Reporting Compatibility
Material vault actions must be compatible with transparency reporting and reserve-architecture visibility.

These rules make the action model consistent across the architecture while still leaving room for category-specific behavior.

---

## Category-Specific Vault Action Policy

### Foundation Vault

The Foundation Vault should be governed by the most conservative action policy among non-vesting categories.

#### Strongly preferred action profile
- observation actions
- limited administrative control actions
- narrowly bounded stewardship-consistent actions
- explicit policy-linked treatment if the Foundation participates in holder-facing systems

#### Strongly restricted or exceptional profile
- principal-impairing actions
- ordinary operating deployment
- generic fallback funding for platform needs
- liquidity-style actions
- rapid discretionary response to ordinary commercial pressure

#### Foundation action principle
The Foundation exists to preserve stewardship meaning. If the action weakens that meaning, it should generally not occur through the Foundation except through exceptional, policy-heavy governance treatment.

---

### Treasury Reserve Vault

The Treasury Reserve Vault should be the primary category for strategic platform flexibility, but it must remain policy-bounded.

#### Allowed profile
- category-consistent strategic deployment
- pre-approved operating-capacity support
- policy-defined ecosystem investment or enablement actions
- payout-related funding actions after treasury/accounting finalization
- governance-approved internal coordination steps

#### Restricted profile
- acting as a substitute for every other vault
- absorbing incentive, partnership, or stability logic without clear policy basis
- opaque routing through unnecessary intermediate structures
- discretionary bypass of sensitivity-based approval

#### Treasury action principle
Treasury is the broadest deployment-capable vault, but “broadest” does not mean “unbounded.” Treasury still exists within category meaning.

---

### Team Vesting Vault

The Team Vesting Vault should follow beneficiary-alignment logic rather than treasury-deployment logic.

#### Allowed profile
- vesting schedule release
- beneficiary verification actions
- vesting-state administrative maintenance
- policy-defined corrective action if schedule implementation needs bounded repair

#### Disallowed or strongly restricted profile
- discretionary redeployment for operating needs
- redirection to treasury-style destinations
- campaign or market-support use
- general-purpose transfer authority unrelated to beneficiary logic

#### Team vesting principle
The Team Vesting Vault exists to align builders, not to act as a reserve pool.

---

### Advisor Vesting Vault

The Advisor Vesting Vault should follow the same beneficiary-bound logic as team vesting, but with advisor-specific alignment structure.

#### Allowed profile
- scheduled or condition-consistent beneficiary release
- vesting metadata maintenance
- bounded corrective actions tied to beneficiary structure

#### Restricted profile
- treasury substitution
- liquidity operations
- generalized partnership deployment
- ad hoc reassignment without formal policy pathway

#### Advisor vesting principle
Advisor alignment should remain visible and bounded. The vault should not become a flexible external-relations budget.

---

### Holder Incentives Vault

The Holder Incentives Vault should support explicitly holder-facing or participation-facing programs.

#### Allowed profile
- loyalty or rewards distributions
- holder campaign support
- participation incentives
- ecosystem-alignment programs consistent with holder-facing policy
- bounded promotional issuance through approved structures

#### Restricted profile
- generic treasury spending
- liquidity support
- undefined business-development spending
- substitute funding for product operations unrelated to holder participation

#### Incentives principle
The incentives vault exists to create structured participation programs, not to fund any activity that is loosely “community related.”

---

### Ecosystem Partnership Vault

The Ecosystem Partnership Vault should support strategic relationships and ecosystem-expansion pathways.

#### Allowed profile
- strategic partnership deployment
- integration support programs
- relationship-bound growth allocation
- platform-expansion collaboration under defined policy
- category-consistent network-development actions

#### Restricted profile
- treasury shortfall patching
- generic operating expense support
- holder rewards masquerading as partnerships
- liquidity management through partnership category

#### Partnership principle
This vault exists for relationship-driven expansion, not for any action that happens to involve an external party.

---

### Liquidity Operations Vault

The Liquidity Operations Vault is one of the most trust-sensitive deployment categories and should have narrower operational freedom than Treasury.

#### Allowed profile
- policy-bounded liquidity-support actions
- market-operations actions tied to explicit destination and operational logic
- pre-approved structured liquidity programs
- category-consistent liquidity maintenance or support behavior

#### Strongly restricted profile
- ordinary treasury deployment
- discretionary transfers lacking market-operations rationale
- informal routing through opaque destination chains
- repurposing for unrelated ecosystem programs

#### Liquidity principle
Liquidity actions must be especially legible because market-facing token movement is one of the most scrutinized behaviors in any token ecosystem.

---

### Transparency / Stability Vault

The Transparency / Stability Vault should be among the most tightly bounded categories.

#### Allowed profile
- narrowly approved transparency-supporting functions
- bounded stability-supportive actions consistent with published architecture
- trust-protective structural use cases
- exceptional actions whose rationale directly preserves ecosystem stability or transparency integrity

#### Strongly restricted profile
- general treasury support
- broad growth spending
- incentives substitution
- undefined “market support” reclassification
- convenience-based emergency funding of unrelated needs

#### Transparency / Stability principle
This vault exists because FUZE claims transparency and trust as architecture. Its misuse would damage one of the platform’s strongest structural claims.

---

## Vault Actions and Destination Policy

Every material vault action should be evaluated not only by source vault, but also by destination type.

At minimum, destination policy should distinguish among:

- beneficiary destinations
- program contract destinations
- treasury-linked controlled destinations
- external partner destinations
- payout-contract funding destinations
- approved operational destinations
- blocked or disallowed destination classes

### Destination principle

A vault action may remain inappropriate even if the amount is small and the source category seems flexible. Destination ambiguity is often the first place reserve meaning breaks down. Vault action policy should therefore require destination categories to be policy-legible.

Examples:

- a Treasury Reserve deployment to a clearly approved strategic contract may be valid
- a Holder Incentives transfer to a vague omnibus wallet is structurally weak even if the stated intent sounds reasonable
- a Liquidity Operations action to an unclassified destination should generally be treated as high risk
- a Foundation transfer into an ordinary operating wallet should generally be unacceptable absent extraordinary governance treatment

---

## Vault Actions and Timing Policy

Vault behavior should also respect timing discipline where appropriate.

### Timing-sensitive action types may include:
- vesting releases
- payout-cycle funding
- program launch allocations
- timelock-gated deployments
- emergency containment actions
- policy-change-linked reconfiguration

### Timing principle

An action may be category-consistent in abstract terms but still improper if executed at the wrong time, without the correct notice, or outside the correct governance window. Vault action policy should therefore consider not only what action is taken, but when it is taken relative to policy cycle, reporting cycle, or governance posture.

---

## Relationship to Treasury Control Policy

The vault action policy and treasury control policy are closely related, but they are not identical.

### Treasury control policy focuses on:
- governance posture
- action approval mechanics
- role separation
- sensitivity tiers
- treasury-domain authority

### Vault action policy focuses on:
- what each vault may actually do
- how vault purpose constrains behavior
- which destinations and use cases are category-consistent
- how reserve meaning is preserved in practice

### Boundary principle

Treasury control policy governs **how** treasury-sensitive actions are controlled.  
Vault action policy governs **what** those vaults are meaningfully allowed to do.

Both are required for a strong reserve architecture.

---

## Relationship to Foundation Governance

Foundation governance should override ordinary treasury convenience when Foundation meaning is at stake.

This means vault action policy must explicitly preserve the Foundation as a structurally distinct category.

### Foundation-specific implications
- principal-sensitive actions should be treated as critical
- ordinary treasury-logic shortcuts should not apply automatically
- even technically simple transfers may be economically inappropriate
- reporting expectations should be stronger because Foundation behavior has outsized trust implications

### Principle

Where Foundation governance and general treasury convenience come into tension, the policy should favor preservation of Foundation meaning unless exceptional governance has explicitly approved otherwise.

---

## Relationship to Profit Participation

Vault action policy must also preserve clear treatment of payout-related action.

### Treasury Reserve and payout
The Treasury Reserve may have a formal role in enabling payout-cycle funding after accounting and policy finalization.

### Foundation and payout
If Foundation-held balances or Foundation-controlled flows interact with profit participation, that interaction must be explicit and policy-defined rather than inferred.

### Important boundary
Vault policy should preserve the distinction between:
- reserve capital,
- internal platform commerce,
- and funded stablecoin payout cycles.

A vault action funding a payout cycle is not merely “another treasury transfer.” It is a holder-facing, high-trust economic event and should therefore be treated as such in sensitivity, reporting, and approval logic.

---

## Reporting Requirements for Vault Actions

Material vault actions should generate reporting expectations appropriate to their category and sensitivity.

At minimum, reporting should preserve:

- vault category
- action class
- sensitivity tier
- destination category
- approval lineage
- execution timing
- policy basis
- public reporting reference where applicable

### Reporting principle

A vault action should remain explainable in a way that reinforces vault meaning. If a transfer cannot be described without resorting to vague language such as “general ecosystem purposes,” that is often a sign that action policy is too weak.

---

## Audit and Review Requirements

Vault action policy should support structured review over time.

At minimum, vault actions should preserve enough information to answer:

- what vault acted
- what action class applied
- whether the action fit the vault’s intended role
- what destination received the action
- what approval path authorized it
- what policy version governed it
- how the action was reported or disclosed
- whether exceptional treatment occurred

### Review principle

Vault review is not only about catching misuse. It is about preserving institutional memory and long-term clarity of reserve behavior.

---

## Emergency and Recovery Policy

Vault action policy must include emergency behavior, but emergency behavior should remain narrow.

### Emergency actions may include:
- pause or freeze
- temporary destination blocking
- urgent control rotation
- emergency containment of incorrect or malicious action pathway
- temporary suspension of non-essential vault movement pending review

### Emergency principles
- emergency action should protect category meaning where possible
- emergency authority should not become a substitute for ordinary deployment policy
- post-incident review should be mandatory
- exceptional corrective movement should preserve audit and reporting lineage
- emergency actions affecting trust-sensitive vaults should receive stronger later disclosure

### Recovery principle

Recovery should restore structural clarity, not quietly rewrite history. If a vault action was problematic, the corrective path should remain visible and policy-bound.

---

## Risks Addressed by Vault Action Policy

Vault action policy is designed to reduce several important risks:

### 1. Category Drift Risk
A vault gradually becomes used for functions outside its stated purpose.

### 2. Omnibus Behavior Risk
Separated vaults behave in practice like one flexible reserve pool.

### 3. Destination Ambiguity Risk
Vault actions route through poorly classified destinations that make reserve meaning harder to interpret.

### 4. Governance Shortcut Risk
Operators use expedient approvals to bypass category-sensitive reasoning.

### 5. Foundation Meaning Erosion Risk
Stewardship capital is treated like ordinary treasury capital.

### 6. Trust-Signal Failure Risk
Transparency/stability or liquidity-sensitive categories lose credibility because action policy is too weak.

These risks explain why FUZE needs an explicit vault action policy rather than relying only on reserve labels and contract deployment.

---

## Minimum Architectural Entities

At minimum, the vault action policy should recognize the following conceptual entities:

### Vault Entities
- vault_id
- vault_category
- vault_role_description
- control_reference
- policy_version

### Action Entities
- vault_action_id
- action_class
- sensitivity_tier
- destination_category
- action_status
- proposed_at
- approved_at
- executed_at

### Governance Entities
- proposer_reference
- approver_reference
- executor_reference
- multisig_reference
- timelock_reference where applicable
- emergency_authority_reference where applicable

### Reporting and Audit Entities
- audit_lineage_reference
- transparency_report_reference
- registry_reference where applicable
- payout_cycle_reference where applicable
- exception_reference where applicable

These are minimum conceptual entities. Detailed schema and contract integration are refined downstream.

---

## Open Items

The following areas are intentionally refined in downstream specifications:

- exact allowed destination registry model
- exact blocked-destination categories
- exact vesting-release mechanics and beneficiary event schema
- exact reporting disclosure thresholds by sensitivity tier
- exact emergency containment procedures per vault type
- exact interaction rules for future reserve categories if added

These do not weaken the canonical vault action policy established here.

---

## Closing Summary

The FUZE vault action policy defines how reserve and reserve-adjacent vaults may behave in practice. It turns separated vault architecture into enforceable economic meaning by defining action classes, sensitivity tiers, destination rules, category-specific behavior, and stronger governance and reporting expectations for trust-sensitive actions. By making vault behavior explicit rather than implied, FUZE reduces category drift, omnibus reserve behavior, and governance ambiguity, and strengthens one of the most important trust layers in the ecosystem.
