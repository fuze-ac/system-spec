# BASE_PAYOUT_EXECUTION_LAYER_SPEC

## Purpose

This document defines the canonical Base-based payout execution layer of the FUZE ecosystem. Its purpose is to establish why Base is used as the execution environment for stablecoin profit participation, what the payout layer owns, how it connects to Ethereum-based holder eligibility, how funded payout cycles are represented and claimed, and how this layer remains distinct from both the Ethereum token layer and the Base Platform Credits layer.

This specification is foundational because the profit participation model of FUZE depends not only on policy and accounting, but also on a practical, transparent, and operationally credible claim environment. The payout layer must allow funded stablecoin distributions to be executed efficiently while preserving the role of Ethereum as the canonical participation layer and preserving the role of Platform Credits as the internal consumption layer. The Base payout execution layer is therefore one of the most important trust-bearing components in the overall FUZE architecture.

---

## Scope

This specification covers:

- the canonical role of Base in the stablecoin payout architecture
- why Base is used as the payout execution layer
- what the Base payout layer owns and what it does not own
- how Ethereum holder truth becomes Base claimable entitlement
- how funded payout cycles are represented, opened, claimed, and closed on Base
- how the payout layer interacts with treasury, accounting, snapshot, and reporting systems
- how payout execution remains distinct from Platform Credits and ordinary product commerce
- transparency, security, and governance expectations for the payout layer
- risks, edge cases, and coordination principles for payout execution

This specification does not define every treasury policy decision, every eligibility exclusion rule, or every reporting format. Those are refined in:

- `CHAIN_ARCHITECTURE_SPEC.md`
- `ETHEREUM_TOKEN_LAYER_SPEC.md`
- `SNAPSHOT_AND_ELIGIBILITY_PIPELINE_SPEC.md`
- `PROFIT_PARTICIPATION_SYSTEM_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
- `PAYOUT_LEDGER_SPEC.md`
- `TRANSPARENCY_REPORTING_SPEC.md`

---

## Design Goals

The design goals of the Base payout execution layer are:

1. to provide a low-cost and practical claim environment for stablecoin profit participation
2. to preserve Ethereum as the canonical source of token-holder truth
3. to make payout execution cycle-based, auditable, and operationally clear
4. to keep payout assets, internal credits, and token participation structurally separate
5. to support transparent funding, claim, and closure behavior for each payout cycle
6. to reduce holder friction in interacting with funded payout cycles
7. to make cross-layer coordination explicit rather than ambiguous
8. to strengthen the credibility of FUZE’s profit participation model through bounded architecture

---

## Non-Goals

This specification is not intended to:

- make Base the canonical source of FUZE token ownership truth
- make the payout layer the same thing as the Platform Credits layer
- make stablecoin payouts continuous or automatic without funded cycles
- replace off-chain accounting, treasury policy, or eligibility preparation with naive on-chain assumptions
- define stablecoin payouts as equivalent to token transfers
- make the payout layer a generic treasury wallet
- allow payout logic to blur into product revenue recognition or internal credits consumption

---

## Canonical Layer Principle

The primary principle of the Base payout execution layer is:

> Base is the practical execution environment where policy-finalized stablecoin profit participation becomes funded and claimable for eligible FUZE holders, while Ethereum remains the canonical source of holder truth.

This means:

- Base hosts the payout contract and cycle execution state
- stablecoin claims happen on Base
- eligibility does not originate from Base token balances
- payout funding follows treasury and accounting finalization
- Base payout execution remains distinct from Base Platform Credits usage and from Ethereum token participation

This principle is essential because it preserves clarity across all three economic layers of FUZE:
- token participation,
- internal consumption,
- and profit participation.

---

## Why Base Is Used for Payout Execution

Base is used for payout execution because the stablecoin profit participation system needs a claim environment that is practical for funded cycle interaction.

Payout execution must support:

- contract funding of stablecoin pools
- cycle-specific claim opening
- claim interaction by many eligible holders
- affordable gas costs
- transparent on-chain execution state
- repeated future cycles without excessive friction

If payout execution were forced onto Ethereum mainnet, the operational and user-cost profile would be less favorable for repeated claim interaction. Ethereum remains the correct canonical layer for token truth, but it is not necessarily the most practical layer for routine stablecoin claim execution.

Base is therefore used because it better fits the operational needs of the payout system:

- lower-cost claim transactions
- more practical holder interaction
- better usability for repeated cycles
- compatibility with a structured payout contract
- clearer separation between participation truth and payout execution

This is consistent with the broader FUZE chain thesis: different chain environments should host the roles they are best suited to perform.

---

## Canonical Role of the Base Payout Layer

The Base payout layer exists to serve as the on-chain execution environment for funded stablecoin profit participation cycles.

This means the Base payout layer is responsible for the on-chain representation of:

- payout cycle identity
- funded stablecoin pool state
- cycle open/closed claim state
- claim execution by eligible participants
- claim-completion visibility
- payout-related operational state for each cycle

The payout layer is not a general balance system for everyday platform usage. It is a bounded layer designed specifically for cycle-based claim execution.

### Strategic meaning

In FUZE, profit participation must be both credible and usable. A payout model that is conceptually attractive but operationally cumbersome weakens over time. The Base payout layer solves this by giving the profit participation system a practical and structured execution home without changing the role of the token itself.

---

## What the Base Payout Layer Owns

The Base payout layer owns the on-chain operational meaning of:

- funded stablecoin payout cycle state
- payout contract state for claim execution
- claim-open and claim-closed lifecycle state
- claim completion state for eligible participants
- cycle-level payout execution records
- operational payout contract balances associated with funded cycles

### Clarification of “owns”

To say the Base payout layer owns these meanings does not mean it explains the full payout system by itself. Off-chain systems still determine:
- accounting treatment,
- distributable profit,
- snapshot-derived eligibility,
- exclusion rules,
- and reporting structure.

But when the question becomes “what is the on-chain state of this funded payout cycle and how are claims executed?”, the answer belongs to the Base payout layer.

This gives FUZE a clean on-chain home for profit participation execution.

---

## What the Base Payout Layer Does Not Own

The Base payout layer does **not** own:

- canonical FUZE token balances
- holder-truth determination
- raw payout eligibility policy by itself
- treasury accounting for distributable profit
- Platform Credits balances
- product subscriptions or usage billing
- general platform revenue recognition
- ordinary user spending balances
- unrestricted reserve management outside the payout role

This distinction is crucial because the payout layer is intentionally bounded. It executes funded stablecoin claims. It does not define the entire economics of the ecosystem.

---

## Relationship to Ethereum Holder Truth

The Base payout execution layer is downstream from Ethereum-based holder truth.

### Ethereum provides:

- canonical FUZE token contract state
- canonical holder balances
- the participation-layer source of truth
- the source dataset for snapshot-based eligibility

### Base payout layer provides:

- funded stablecoin claim execution
- cycle-specific claim state
- practical claim interaction environment

### Key principle

A holder does not become eligible because of anything on Base alone.  
Eligibility is derived from Ethereum participation truth as interpreted through the snapshot and eligibility pipeline.  
Base is where that already-determined entitlement becomes claimable.

This is one of the most important boundaries in the entire FUZE architecture.

---

## Relationship to Snapshot and Eligibility Systems

The Base payout layer depends on a snapshot and eligibility pipeline that transforms Ethereum holder balances into cycle-specific claimable entitlements.

### Canonical flow

1. Ethereum holder balances are observed at the relevant snapshot reference.
2. Policy-defined exclusions and treatment rules are applied.
3. The eligible dataset for the cycle is produced.
4. The cycle-specific entitlement structure is constructed.
5. The stablecoin payout pool is funded on Base.
6. Claimable entitlement is made available through the Base payout contract.

### Why this matters

The payout contract should not be expected to discover or interpret the entire economic meaning of holder eligibility by itself. That work belongs to the snapshot, eligibility, and accounting systems. The payout layer receives and executes the result of that process.

This separation improves clarity and reduces conceptual overload in the payout execution environment.

---

## Profit Finalization to Base Funding Flow

The Base payout layer should only become active after the upstream profit-participation logic is finalized.

The correct high-level flow is:

### 1. Platform activity occurs
Products generate usage, subscriptions, and other commercial outcomes through the internal platform economy.

### 2. Accounting and treasury reconciliation occur
Revenue, costs, refunds, chargebacks, and policy treatment are processed to determine the policy-defined distributable amount.

### 3. Cycle amount is finalized
A specific amount of stablecoin is designated for the payout cycle.

### 4. Eligibility is constructed
The Ethereum-derived snapshot and exclusion framework determines the eligible holder dataset.

### 5. Base payout contract is funded
Stablecoins are transferred to the payout execution contract on Base for the defined cycle.

### 6. Claims open
Eligible holders may claim on Base according to cycle logic.

This flow is important because it preserves the distinction between:
- product revenue,
- internal credits usage,
- accounting profit determination,
- and payout execution.

The Base payout layer is the funded execution endpoint of the cycle, not the source of economic truth for the cycle.

---

## Payout Cycle Structure on Base

The Base payout layer should represent profit participation through explicit payout cycles.

Each payout cycle should be a bounded operational object with at least the following meanings:

- unique cycle identity
- funded stablecoin amount
- cycle status
- claim-open state
- claim-close or closure state
- claim-completion visibility
- linkage to eligibility dataset and reporting references

### Why cycle structure matters

A cycle structure prevents the payout system from becoming vague or continuous in an undefined way. It creates a formal sequence:

- fund
- open
- claim
- close
- report

This makes the payout model easier to govern, easier to report, and easier to audit.

A cycle-based structure also helps holders understand that profit participation is not an always-on wallet faucet. It is a policy-defined, funded, and time-bounded system.

---

## Claim Execution Model

The Base payout layer should support an explicit claim execution model.

### Claim model requirements

The payout contract should support, at minimum:

- proof or entitlement recognition for eligible claimants
- prevention of double claim
- correct stablecoin transfer on valid claim
- visibility into claim success or prior claim state
- claim-open state checks
- cycle-specific claim handling

### Claim principles

- claims should be deterministic under the cycle rules
- claim execution should not redefine eligibility on the fly
- the contract should prevent duplicate payout for the same cycle entitlement
- stablecoin movement should be directly tied to valid entitlement state
- claim interaction should remain practical and understandable for holders

The claim model is one of the most visible user-facing parts of the profit participation system. It must therefore prioritize both correctness and clarity.

---

## Stablecoin Asset Role on Base

The payout layer executes in stablecoins rather than in FUZE token.

### Why stablecoins are used

Stablecoins are used because the payout layer is intended to represent realized, policy-defined profit participation rather than token-distributed narrative rewards.

This creates several advantages:

- payout asset is clearly distinct from participation asset
- holder receives a more legible payout medium
- accounting discipline is stronger
- product usage and payout logic remain easier to separate
- claim behavior is easier to understand

### Important distinction

The payout stablecoin is not:
- the FUZE token
- Platform Credits
- a replacement for token ownership
- a general reserve asset inside the platform economy

It is the payout asset used in the cycle-specific Base execution layer.

---

## Relationship to the Base Platform Credits Layer

The Base payout layer and the Base Platform Credits layer are intentionally separate, even though both live on Base.

### Base Platform Credits layer
Owns:
- internal product spending power
- subscriptions and usage-linked balances
- credits issuance, deduction, and adjustment
- account/workspace consumption state

### Base payout layer
Owns:
- funded stablecoin payout cycles
- cycle-based claim execution
- holder-facing stablecoin distribution state

### Why this separation is necessary

If the Base payout layer and credits layer were blurred together, the platform would inherit major confusion:
- product balances could be mistaken for holder profit rights
- sold credits could be mistaken for distributable payout assets
- payout claims could be confused with internal spending balances
- treasury and profit logic would become harder to explain

### Principle

Shared chain does not imply shared meaning.  
Base hosts more than one bounded role in FUZE.  
The credits layer and payout layer remain architecturally separate.

This separation is one of the key trust-preserving rules of the ecosystem.

---

## Funding Rules and Treasury Interaction

The Base payout layer should only be funded through approved treasury and accounting pathways.

### Funding principles

- payout funding must correspond to a specific cycle
- the funded amount must reflect policy-defined distributable profit
- funding should be deliberate and attributable
- the payout contract should not behave like a casual treasury sink or omnibus reserve
- funding should preserve clear linkage to reporting and payout-cycle records

### Treasury boundary

Treasury determines whether and how much stablecoin is distributable under policy.  
The payout contract executes the funded cycle.  
The payout layer should not independently decide what amount is available for distribution.

This boundary protects both the treasury model and the payout model.

---

## Transparency Expectations for the Base Payout Layer

Because profit participation is one of FUZE’s most visible holder-facing mechanisms, the Base payout layer should support strong transparency.

At minimum, the ecosystem should be able to explain:

- that payout execution occurs on Base
- that eligibility is derived from Ethereum holder snapshots
- that stablecoin claims are funded cycle by cycle
- that the payout asset is distinct from the token and from Platform Credits
- what payout cycles exist
- whether a cycle is funded and open
- whether claim execution has occurred at the cycle level
- how payout-cycle execution relates to transparency reporting and payout ledgers

This matters because the Base payout layer is part of the public trust surface of FUZE. If it is not clearly explained, the ecosystem may confuse payout execution with either token transfer or internal credits logic.

---

## Governance and Administrative Expectations

The Base payout layer should be governed with strong role-specific discipline.

### Governance-sensitive actions may include:

- cycle funding authorization
- claim window open/close controls where applicable
- emergency pause or protective controls where necessary
- contract upgrade or role-change decisions where applicable
- entitlement-root or cycle-parameter publication where relevant
- operational recovery actions under defined policy

### Governance principles

- administrative control should be explicit
- sensitive actions should remain policy-bounded
- payout execution controls should not be casually mixed with ordinary product admin controls
- governance-sensitive changes should be auditable and compatible with multisig/timelock discipline where applicable

The payout execution layer is a high-trust system. Its governance model must reflect that importance.

---

## Security Principles for the Base Payout Layer

The Base payout layer should follow strong security and correctness principles.

### Key principles include:

#### Claim Correctness
Only valid cycle entitlements should be claimable.

#### Double-Claim Prevention
The system should prevent duplicate payout for the same claimant in the same cycle.

#### Cycle Clarity
Each payout cycle should be bounded and identifiable.

#### Funding Integrity
Stablecoin funding should be attributable to defined cycle logic rather than ambiguous pooled behavior.

#### Layer Separation
The payout layer should remain distinct from credits balances and token balances.

#### Operational Recoverability
Where operational issues occur, the platform should have bounded, policy-consistent recovery paths.

#### Observability
Operators should be able to monitor funded amounts, claim activity, unclaimed balances where relevant, and cycle-state health.

Because the payout system directly affects holder trust, security and clarity must both be strong.

---

## Risks and Failure Considerations

The Base payout layer introduces several important risks that must be managed.

### 1. Cross-Layer Eligibility Risk
If the Ethereum-derived snapshot or entitlement preparation is wrong, Base execution can be technically correct but economically wrong.

### 2. Claim-State Risk
If claim windows, entitlement publication, or cycle closure behavior are not clear, holder trust may weaken.

### 3. Funding Risk
If treasury funding behavior is not clearly tied to cycle logic, the payout system may appear discretionary or opaque.

### 4. Layer Confusion Risk
Because Base also hosts Platform Credits, users may confuse stablecoin payout claims with credits balances if the architecture is not explained clearly.

### 5. Operational Recovery Risk
If a cycle is funded but operational metadata or reporting lags, the user experience may become confusing even when contract state is valid.

These risks reinforce the need for strong reporting, explicit lifecycle controls, and clear separation of roles.

---

## Minimum Architectural Entities

At minimum, the Base payout execution layer should recognize the following entities:

### Payout Contract Entities
- payout contract identity
- cycle identifier
- cycle status
- funded stablecoin amount
- claim-open/claim-closed state

### Claim Entities
- claimant entitlement reference
- claim status
- claim completion record
- double-claim prevention reference

### Coordination Entities
- snapshot dataset reference
- eligibility or entitlement root/reference
- treasury funding reference
- reporting and payout-ledger linkage

### Governance Entities
- payout control role references
- pause/emergency control references where applicable
- cycle publication and operational control references

These are minimum conceptual entities. More detailed schemas and contract mechanics are refined in downstream specifications.

---

## Open Items

The following areas are intentionally refined in downstream specifications:

- exact entitlement proof model
- exact payout-cycle closure and residual handling rules
- exact claim-window timing policy
- exact emergency or pause controls for the payout contract
- exact reporting schema for Base payout activity
- exact upgradeability and control philosophy for the payout contract

These do not weaken the canonical Base payout execution layer model established here.

---

## Closing Summary

The Base payout execution layer is the practical claim environment for FUZE’s stablecoin profit participation system. It exists to receive funded cycle-specific stablecoin pools and allow eligible FUZE holders to claim according to entitlement derived from Ethereum-based holder snapshots. It is intentionally separate from the Ethereum token layer and intentionally separate from the Base Platform Credits layer. By giving stablecoin profit participation a low-cost, bounded, and clearly explained execution environment on Base, FUZE strengthens the usability, transparency, and credibility of its holder-alignment architecture.
