# TREASURY_CONTROL_POLICY_SPEC

## Purpose

This document defines the canonical treasury control policy of the FUZE ecosystem. Its purpose is to establish how treasury-controlled capital may be governed, authorized, restricted, executed, reviewed, and reported so that treasury behavior remains policy-driven, role-bounded, and structurally consistent with the transparency-first architecture of FUZE.

This specification is foundational because treasury architecture alone is not sufficient to create trust. Even when reserve categories are separated into distinct vaults and contracts, the ecosystem still needs a clear control policy that defines what treasury authority means in practice. Without such a policy, category separation can be weakened by overly broad operational discretion, vague approval pathways, or inconsistent interpretation of what treasury-controlled capital is allowed to do. The treasury control policy is therefore the rule layer that gives operational meaning to the reserve architecture.

---

## Scope

This specification covers:

- the canonical control philosophy for treasury-governed capital in FUZE
- how treasury authority is bounded across reserve categories
- the distinction between treasury control and ordinary platform administration
- the action classes that treasury governance must recognize
- approval, authorization, and execution principles for treasury-sensitive actions
- the relationship between treasury control, multisig, timelock, policy, and reporting
- the boundaries between treasury reserve, Foundation, incentives, liquidity, partnerships, and related categories
- correction, emergency, and exceptional-action principles
- transparency, audit, and reconciliation expectations for treasury-controlled actions

This specification does not define the full smart contract implementation of each vault, every allowed-use matrix by reserve category, or the full operational details of profit-participation funding. Those are refined in:

- `TREASURY_AND_RESERVE_ARCHITECTURE_SPEC.md`
- `FOUNDATION_GOVERNANCE_SPEC.md`
- `VAULT_ACTION_POLICY_SPEC.md`
- `MULTISIG_AND_TIMELOCK_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`
- `MONITORING_ALERTING_AND_INCIDENT_RESPONSE_SPEC.md`

---

## Design Goals

The design goals of the FUZE treasury control policy are:

1. to prevent treasury-controlled capital from being treated as broad discretionary operator capital
2. to preserve the distinct economic meaning of each treasury-related reserve category
3. to define clear action classes for treasury-sensitive decisions
4. to require explicit authorization pathways for material treasury actions
5. to make treasury behavior auditable, reportable, and structurally explainable
6. to support strong separation between governance authority, execution authority, and reporting authority
7. to create safer handling for exceptional or emergency treasury cases
8. to improve long-term public trust by making treasury control visible and bounded

---

## Non-Goals

This specification is not intended to:

- make treasury policy identical across all reserve categories
- treat all treasury-related actions as equally sensitive
- collapse Foundation governance into ordinary treasury control
- define treasury as a generic source of liquidity for any platform need
- allow control policy to be overridden casually by product urgency
- replace contract-level security controls with pure procedural trust
- make reporting optional for material treasury-sensitive actions

---

## Canonical Treasury Control Principle

The primary principle of FUZE treasury control is:

> treasury-controlled capital may be activated only through explicit category-aware policy, bounded authority, and auditable governance pathways that preserve the intended economic meaning of the capital being controlled.

This means:

- treasury authority is not a blanket right to move ecosystem capital
- each reserve category should remain subject to role-appropriate action rules
- approval, execution, and review should be distinct where possible
- treasury-sensitive actions should be explainable before and after execution
- policy should constrain discretion rather than merely describe it

This principle is central to FUZE’s transparency-first design. The ecosystem should not rely on assumptions such as “the treasury team will use good judgment.” It should rely on explicit, reviewable control structure.

---

## Why Treasury Control Needs Its Own Policy Layer

FUZE uses a structured reserve architecture with separate vaults for Treasury, Foundation, team vesting, advisor vesting, holder incentives, partnerships, liquidity operations, and transparency/stability functions. That architecture is necessary, but it is not sufficient by itself.

A separated reserve system still requires a policy answer to important questions such as:

- what actions are permitted for each category
- which actions require stronger review
- which actions are disallowed regardless of convenience
- who can propose, approve, execute, or pause a treasury-sensitive action
- what reporting must follow execution
- how exceptional actions are handled without undermining trust

Without a treasury control policy, contract separation can still degrade into ambiguous operator behavior. Funds may remain technically separate but practically governed through unclear decision logic. Over time, this weakens the public meaning of the reserve architecture.

The treasury control policy solves this by making control explicit. It acts as the operational governance layer between reserve architecture and real treasury behavior.

This is especially important in FUZE because the ecosystem includes:

- token-side reserve categories with different purposes
- a Foundation with stronger stewardship logic
- liquidity operations with higher trust sensitivity
- a transparency/stability category whose meaning depends on restraint
- stablecoin profit participation that must not be confused with reserve discretion
- a multi-product platform whose operational needs could otherwise pressure treasury boundaries

The treasury control policy exists so that growth, urgency, and operational convenience do not silently rewrite economic meaning.

---

## Treasury Control as a Distinct Governance Domain

Treasury control should be treated as a distinct governance domain inside FUZE.

This means treasury-sensitive actions must not be governed through the same informal pathways used for routine product operations, content changes, or day-to-day platform administration. Treasury control affects long-term trust, reserve interpretation, and holder confidence. It therefore requires its own discipline.

### Why distinct treatment is necessary

Treasury actions may affect:

- reserve credibility
- market interpretation
- future funding flexibility
- relationship between stewardship and operating capital
- public confidence in governance quality
- consistency between platform narrative and platform behavior

For these reasons, treasury control should sit closer to governance architecture than to routine product operations.

### Treasury-governance principle

Treasury governance should therefore be:
- category-aware,
- policy-driven,
- multisig-compatible,
- timelock-sensitive where appropriate,
- and auditable enough to support meaningful external interpretation.

This does not mean every treasury action must be slow or ceremonial. It means treasury action must always be structurally accountable.

---

## Treasury-Controlled Capital Categories

The treasury control policy applies across treasury-governed or treasury-adjacent reserve categories, but not every category is controlled in exactly the same way.

At minimum, policy must distinguish among:

- Treasury Reserve Vault
- Foundation Vault
- Holder Incentives Vault
- Ecosystem Partnership Vault
- Liquidity Operations Vault
- Transparency / Stability Vault

It must also recognize that some categories are structurally adjacent but not ordinary treasury deployment pools, including:

- Team Vesting Vault
- Advisor Vesting Vault

### Category-aware control principle

The same control philosophy should apply across the architecture, but the permitted action space should differ by category.

For example:
- Treasury Reserve may support strategic platform deployment under policy.
- Foundation should remain more principal-preserving and more constrained.
- Liquidity Operations may allow a narrower but more operationally sensitive set of actions.
- Transparency / Stability should remain tightly bounded to trust-oriented functions.
- Team and Advisor Vesting should not be treated as general treasury capital at all.

A strong treasury control policy therefore begins by recognizing that “treasury” is not one undifferentiated pool of power.

---

## Treasury Action Classes

The treasury control policy should define explicit treasury action classes.

At minimum, FUZE should recognize the following action classes.

### 1. Observation and Reporting Actions
Actions that do not move capital but are required for visibility and governance compatibility.

Examples:
- reserve-state publication
- balance attestations
- registry updates that do not change asset position
- scheduled treasury reporting actions

### 2. Administrative Configuration Actions
Actions that change control configuration but do not directly deploy capital.

Examples:
- signer rotation
- timelock configuration changes
- registry updates
- contract control-role updates
- policy-reference updates where contract-linked

### 3. Internal Coordination Actions
Actions that prepare or coordinate treasury state without constituting outward deployment.

Examples:
- category-internal reconciliation
- contract-to-contract synchronization where policy permits
- setup steps for an already-approved treasury event
- bounded migration actions

### 4. Category Deployment Actions
Actions that use reserve capital according to the allowed role of a category.

Examples:
- approved treasury deployment
- incentives program funding
- partnership allocation release
- transparency/stability bounded action
- policy-approved liquidity operation

### 5. Payout-Related Funding Actions
Actions that relate treasury/accounting finalization to profit-participation cycle funding.

Examples:
- funding a stablecoin payout cycle after formal approval
- publishing payout-cycle references linked to treasury authorization

### 6. Exceptional or Emergency Actions
Actions taken under exceptional circumstances to protect assets, halt harm, or recover from serious issues.

Examples:
- emergency pause
- emergency destination block
- urgent freeze or containment action
- exceptional governance-constrained intervention pending review

These action classes matter because a treasury control system is much stronger when it distinguishes everyday visibility actions from configuration changes, capital deployment, and emergency action.

---

## Action Sensitivity Tiers

In addition to action classes, treasury control should use sensitivity tiers.

At minimum, actions should be understood in terms such as:

### Low Sensitivity
Reporting or low-risk configuration activity with no immediate outward capital deployment.

### Moderate Sensitivity
Administrative or coordination actions that affect treasury control posture but do not directly deploy material capital.

### High Sensitivity
Material capital deployment, payout-cycle funding, category reallocation, or governance-significant control changes.

### Critical Sensitivity
Exceptional actions, emergency actions, Foundation-principal-sensitive actions, or any action capable of materially altering long-term reserve meaning or trust assumptions.

### Sensitivity principle

The more sensitive the action, the stronger the requirements for:
- approval structure,
- role separation,
- timelock,
- reporting,
- and audit traceability.

This allows treasury policy to be practical without being loose.

---

## Proposal, Approval, and Execution Separation

FUZE treasury control should preserve meaningful separation among proposal, approval, and execution functions whenever feasible.

### Proposal role
The proposal role identifies and frames a treasury-sensitive action.

### Approval role
The approval role authorizes whether the action may occur according to policy, sensitivity, and reserve-category meaning.

### Execution role
The execution role performs the authorized action through the appropriate contract control pathway.

### Why separation matters

Without separation, the same small group could:
- decide that an action is appropriate,
- approve it,
- execute it,
- and explain it afterward.

This concentrates too much interpretive and operational power in one path.

### Separation principle

Not every low-sensitivity action requires maximum separation, but material treasury actions should avoid full concentration of proposal, approval, and execution in one informal channel. This strengthens governance credibility and reduces misuse risk.

---

## Treasury Approval Principles

Every treasury-sensitive action should be evaluated against approval principles that go beyond technical feasibility.

At minimum, approval should ask:

1. **Is the action consistent with the role of this reserve category?**
2. **Is the action allowed under current policy?**
3. **Is the destination or use consistent with bounded control rules?**
4. **Does the action preserve category meaning rather than erode it?**
5. **Is the action proportionate to the stated objective?**
6. **Does the action require stronger governance pathing because of sensitivity?**
7. **Will the action remain explainable in future transparency reporting?**

### Approval principle

The correct treasury question is not merely “can we do this?” It is “should this reserve category do this under the architecture we have publicly defined?”

This distinction is essential to trust.

---

## Category-Specific Treasury Control Philosophy

### Treasury Reserve Vault
The Treasury Reserve should support strategic platform flexibility, but only through policy-defined action categories. It should not become a justification for unbounded discretionary deployment.

### Foundation Vault
Foundation control should be more conservative than ordinary treasury control. Principal-sensitive actions should be exceptional, and stewardship meaning must remain stronger than operational convenience.

### Holder Incentives Vault
Actions should remain tied to holder-facing or participation-facing programs. It should not become an alternative treasury budget.

### Ecosystem Partnership Vault
Actions should remain relationship- and expansion-oriented, not substitutes for internal platform operating capital.

### Liquidity Operations Vault
This category should have narrower action pathways, stronger destination discipline, and stronger reporting expectations because of its higher trust sensitivity.

### Transparency / Stability Vault
This category should remain one of the most tightly bounded. It should not be treated as discretionary backup capital. Its actions should preserve the trust-oriented meaning of the reserve.

### Team and Advisor Vesting
These are not ordinary treasury deployment categories. Treasury control policy should recognize them primarily through vesting-governance and beneficiary-protection logic rather than through deployment logic.

This category-specific interpretation is one of the most important parts of the treasury control policy. It preserves reserve meaning over time.

---

## Relationship to Foundation Governance

Treasury control policy must explicitly recognize that the Foundation is not just another treasury bucket.

### Key principle

Foundation governance should remain distinct even where treasury control infrastructure overlaps technically.

This means:
- Foundation principal is not ordinary deployable treasury capital
- Foundation-sensitive actions should require stronger restraint
- Foundation policy should not be silently overridden through ordinary treasury procedure
- Foundation participation in wider ecosystem mechanisms must remain explicit

### Why this matters

If treasury control policy ignores this distinction, the Foundation may lose much of its structural trust value. The treasury control policy should therefore reinforce, not weaken, Foundation governance boundaries.

---

## Relationship to Profit Participation Funding

Treasury control policy must also define how treasury-sensitive funding interacts with profit participation.

### Key principle

Profit-participation funding should occur only after:
- accounting and treasury finalize the policy-defined distributable amount,
- eligibility preparation is ready,
- and the cycle is properly authorized.

### Treasury-control implication

Funding a payout cycle is a treasury-sensitive action, but it is not equivalent to routine treasury deployment. It has:
- stronger transparency consequences,
- stronger holder-facing implications,
- and tighter linkage to reporting and payout-ledger structures.

### Important boundary

Treasury control policy should preserve the distinction between:
- visible reserve balances,
- internal product economics,
- and funded stablecoin payout cycles.

A payout cycle is not simply a treasury spend. It is a formal ecosystem event requiring tighter policy coupling.

---

## Destination and Use Restrictions

Treasury control policy should include destination and use restrictions.

At a minimum, the policy model should support the principle that treasury-controlled capital may move only toward destinations and purposes consistent with the reserve category and approval path.

### Restriction examples by principle

- vesting capital should not route to discretionary operating destinations
- stability-oriented capital should not route to generic product spending
- incentives capital should not route to undefined treasury substitution
- liquidity operations should not route through opaque generic wallets where avoidable
- Foundation-controlled value should not drift into ordinary operating pathways without exceptional governance treatment

### Destination principle

A treasury action should not be approved only because the immediate use seems reasonable. It must also preserve the structural clarity of where the value is going and why that destination fits the reserve category.

This is essential because treasury misuse often appears first as destination ambiguity, not explicit theft.

---

## Timelock and Multisig Expectations

Treasury control policy should assume compatibility with multisig and timelock architecture for material actions.

### Multisig expectations
Sensitive actions should require shared authorization rather than unilateral execution.

### Timelock expectations
Where actions materially affect reserve structure, capital deployment, or governance-sensitive configuration, timelock should provide:
- delay,
- observability,
- and reduced impulsive execution risk.

### Principle

Timelock and multisig are not the treasury policy themselves. They are enforcement and control instruments that help implement treasury policy. Treasury control policy defines what kinds of actions deserve those protections and under what circumstances.

---

## Emergency and Exceptional Treasury Actions

The treasury control policy must define how exceptional or emergency cases are treated.

### Emergency examples may include:
- suspected compromise of treasury-control pathway
- discovered misrouting risk
- contract vulnerability exposure
- incorrect destination configuration
- urgent containment need around liquidity-sensitive behavior
- payout-cycle funding issue discovered before claim opening

### Emergency principles

- emergency action should prioritize containment over convenience
- emergency action should be narrower than ordinary deployment authority
- emergency action should not become a standing shortcut around normal governance
- post-incident review and reporting should be required
- any exceptional use of protected categories should be especially scrutinized

### Exceptional-action principle

Emergency pathways are necessary, but they are strongest when they are limited, documented, and followed by structured review. A transparency-first ecosystem should not rely on emergency ambiguity.

---

## Treasury Reporting Requirements

Treasury control policy should require reporting discipline for material treasury actions.

At minimum, reporting expectations should support:

- category-aware explanation of the action
- date and governance-path visibility
- action-class and sensitivity visibility
- destination or outcome classification
- linkage to policy basis
- relationship to reserve category meaning
- reconciliation to public reserve and contract visibility where applicable

### Reporting principle

A treasury action that cannot be explained clearly after execution was probably not governed clearly enough before execution.

This principle reinforces that treasury transparency is not a public-relations layer. It is part of the control system.

---

## Audit and Review Requirements

Treasury control policy should support meaningful auditability.

At minimum, treasury-sensitive actions should preserve enough structure to answer:

- what category was affected
- what action class applied
- who proposed the action
- who approved the action
- what control path executed it
- what policy basis justified it
- what reporting references describe it
- whether the action matched allowed category behavior

### Review principle

The purpose of auditability is not only after-the-fact blame. It is long-term institutional clarity. The ecosystem should be able to look back at treasury actions and still understand their role-specific logic.

---

## Relationship to Transparency Model

Treasury control policy is one of the most important operational expressions of FUZE’s transparency model.

The transparency model claims that:
- reserve categories are visible,
- roles are separated,
- governance is structured,
- and value movement is more intelligible than in opaque ecosystems.

Treasury control policy is where those claims either become real or fail.

### Transparency-control principle

If treasury control remains vague, the transparency model weakens even if the contract layout looks good. If treasury control is explicit, category-aware, and reported clearly, the transparency model becomes stronger.

This is why treasury control policy is not a narrow finance document. It is part of the public trust architecture of FUZE.

---

## Risks Addressed by Treasury Control Policy

The treasury control policy is designed to reduce several major risks.

### 1. Omnibus Treasury Behavior Risk
The risk that separated reserves are still treated in practice as one flexible pool.

### 2. Category Drift Risk
The risk that reserve categories gradually lose their intended meaning through repeated “reasonable exceptions.”

### 3. Governance Concentration Risk
The risk that too few actors can frame, approve, and execute treasury-sensitive actions without meaningful separation.

### 4. Reporting Weakness Risk
The risk that treasury actions cannot later be explained in structurally clear terms.

### 5. Foundation Boundary Failure Risk
The risk that Foundation-controlled value becomes treasury-like in practice.

### 6. Profit Participation Confusion Risk
The risk that payout-cycle funding, reserves, and product economics become blurred together.

These risks explain why treasury control needs formal policy rather than good intentions alone.

---

## Minimum Architectural Entities

At minimum, the treasury control policy should recognize the following conceptual entities:

### Treasury Action Entities
- treasury_action_id
- reserve_category
- action_class
- sensitivity_tier
- policy_version
- proposed_at
- approved_at
- executed_at
- action_status

### Governance Entities
- proposer_role
- approver_role
- executor_role
- multisig reference
- timelock reference where applicable
- emergency authority reference where applicable

### Policy Entities
- allowed-use reference
- restricted-use reference
- destination-category reference
- exceptional-action reference where applicable

### Audit and Reporting Entities
- audit_lineage_reference
- transparency_report_reference
- public_registry_reference where applicable
- payout_linkage_reference where applicable

These are minimum conceptual entities. Detailed schema and contract integration are refined downstream.

---

## Open Items

The following areas are intentionally refined in downstream specifications:

- exact allowed-use matrix per reserve category
- exact quorum thresholds by action class and sensitivity
- exact timelock application by action category
- exact emergency-action escalation and rollback procedures
- exact reporting disclosure depth for treasury-sensitive actions
- exact residual-handling rules for failed or partially executed treasury actions

These do not weaken the canonical treasury control policy established here.

---

## Closing Summary

The FUZE treasury control policy defines how treasury-governed capital may be proposed, approved, executed, and reported without eroding the meaning of the reserve architecture. It treats treasury control as a distinct governance domain, uses category-aware action classes and sensitivity tiers, reinforces separation between Treasury, Foundation, incentives, partnerships, liquidity, and stability functions, and requires policy-linked, auditable, and transparent handling of material treasury actions. By making treasury authority explicit and bounded, FUZE strengthens one of the most important trust layers in the entire ecosystem.
