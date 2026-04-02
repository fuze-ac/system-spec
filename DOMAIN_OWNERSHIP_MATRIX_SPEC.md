# DOMAIN_OWNERSHIP_MATRIX_SPEC

## Purpose

This document defines the canonical domain ownership matrix for FUZE. Its purpose is to map the major platform, product, on-chain, governance, and reporting domains to their explicit owners, scope boundaries, source-of-truth rules, and allowed consumers.

This specification is a direct extension of `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md` and `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`. Those documents define the ownership philosophy and top-level responsibility model. This file turns that model into a practical domain matrix that can guide implementation, service decomposition, schema design, API boundaries, event contracts, and review of new product integrations.

---

## Scope

This specification covers:

- the canonical domain list for FUZE
- ownership assignment for each major domain
- source-of-truth designation for each domain
- the distinction between platform domains, product domains, on-chain domains, and external domains
- allowed reads, writes, derivations, and presentation rights by domain
- cross-domain dependency rules
- escalation rules for new or ambiguous domains
- domain classification for future services and entities

This specification does not define full internal data schemas, API contracts, or event payloads. Those belong in downstream files such as:

- `SERVICE_TOPOLOGY_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `API_ARCHITECTURE_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `PRODUCT_INTEGRATION_ARCHITECTURE_SPEC.md`

---

## Design Goals

The design goals of the domain ownership matrix are:

1. to give every major FUZE concern a clear domain owner
2. to make source-of-truth boundaries implementation-ready
3. to prevent platform/product overlap from becoming hidden duplication
4. to support clean service and schema decomposition
5. to preserve the separation of token, credits, payouts, treasury, and reporting
6. to make future product integration easier and more disciplined
7. to improve transparency and auditability through explicit ownership mapping

---

## Non-Goals

This matrix is not intended to:

- define human team staffing
- define every field of every entity
- replace full lifecycle specifications
- create soft “co-owned” domains when one explicit owner is required
- allow products to silently promote product concerns into platform concerns
- redefine canonical chain truth through off-chain convenience

---

## Canonical Domain Ownership Principle

The primary rule of this matrix is:

> every meaningful FUZE domain must have one canonical owner, one canonical source of truth, and one explicit rule for who may read, derive, present, or execute against it.

A domain may be:

- platform-owned
- product-owned
- on-chain-owned
- external-provider-owned

A consumer of a domain is not the owner of that domain.

---

## Domain Classification Model

All FUZE domains must be classified into one of the following classes.

### 1. Platform Core Domain
A reusable cross-product domain owned by the FUZE platform.

### 2. Product Domain
A product-specific domain owned by one product extension layer.

### 3. On-Chain Contract Domain
A domain whose canonical truth is committed to a smart contract.

### 4. Governance and Control Domain
A domain that governs sensitive permissions, approvals, restrictions, or platform-wide controls.

### 5. Reporting and Transparency Domain
A domain focused on audit, reporting, public visibility, reconciliation, and derived presentation.

### 6. External Integration Domain
A domain representing provider-facing adapters, verification flows, and normalized external dependencies.

These classes exist to keep ownership explicit even when domains interact heavily.

---

## Canonical Domain Inventory

The FUZE domain inventory is divided into the following major groups.

### Platform Core Domains
- Identity
- Auth and Session
- Workspace and Organization
- Role and Access Control
- Wallet-Aware Participation
- Commerce and Billing
- Platform Credits
- Invoicing and Receipts
- Payment Verification and Normalization
- AI Orchestration
- Workflow and Automation
- Job Queue and Worker Coordination
- Audit Log and Activity
- Transparency Reporting
- Payout Orchestration
- Control Plane / Admin Controls
- Integration Adapter Layer

### Product Domains
- QTB Product Domain
- AIMM Product Domain
- ZAGA Product Domain
- AIE Product Domain
- HerHelp Product Domain
- Botmad Product Domain
- Future Product Domains

### On-Chain Domains
- Ethereum Token Domain
- Base Credits Ledger Domain
- Base Payout Execution Domain
- Reserve and Vault Contract Domains
- Governance Contract Domains where applicable

### Reporting and Public Visibility Domains
- Public Contract Registry
- Public Wallet Registry
- Payout Ledger
- Investor Reporting
- Community Reporting
- Derived Transparency Views

### External Domains
- Stripe Payment Domain
- Stablecoin Payment Rail Domain
- Telegram Stars Rail Domain
- Apple Billing Domain
- Google Billing Domain
- Wallet Client Domain
- RPC / Indexing Provider Domain
- AI Model Provider Domain
- Cloud / Runtime Provider Domain

---

## Canonical Domain Ownership Matrix

| Domain | Class | Canonical Owner | Canonical Truth Location | Notes |
|---|---|---|---|---|
| Identity | Platform Core | Platform | Platform data model | Cross-product user identity |
| Auth and Session | Platform Core | Platform | Platform session/auth state | Product cannot redefine |
| Workspace and Organization | Platform Core | Platform | Platform workspace model | Shared team context |
| Role and Access Control | Platform Core | Platform | Platform authorization model | Product-specific roles may extend |
| Wallet-Aware Participation | Platform Core | Platform | Platform wallet-link model + chain reads | Mapping layer, not token truth |
| Commerce and Billing | Platform Core | Platform | Platform billing state | Cross-product commercial rules |
| Platform Credits Policy | Platform Core | Platform | Platform credits domain | Internal economic rules |
| Platform Credits Ledger Commitment | On-Chain | Base credits contract layer | Base | On-chain committed credit truth |
| Invoicing and Receipts | Platform Core | Platform | Platform financial records | Presentation tied to billing truth |
| Payment Verification and Normalization | Platform Core / Integration | Platform | Platform normalized payment records | External payments are inputs only |
| AI Orchestration | Platform Core | Platform | Platform orchestration state | Shared AI execution rules |
| Workflow and Automation | Platform Core | Platform | Platform workflow state | Shared execution primitives |
| Job Queue / Worker Coordination | Platform Core | Platform | Async execution layer | Operational execution domain |
| Audit Log and Activity | Reporting / Control | Platform | Platform audit records | Platform-wide visibility |
| Transparency Reporting | Reporting | Platform | Derived reporting store | Derived from canonical sources |
| Payout Orchestration | Platform Core | Platform | Platform payout-cycle records | Off-chain cycle coordination |
| Public Contract Registry | Reporting | Platform | Registry records + chain refs | Presentation of contract structure |
| Public Wallet Registry | Reporting | Platform | Registry records + chain refs | Presentation only, not chain owner |
| Investor Reporting | Reporting | Platform | Derived reporting outputs | Derived, non-canonical |
| Community Reporting | Reporting | Platform | Derived reporting outputs | Derived, non-canonical |
| QTB Product Domain | Product | QTB product domain | QTB product data | Product-owned logic |
| AIMM Product Domain | Product | AIMM product domain | AIMM product data | Product-owned logic |
| ZAGA Product Domain | Product | ZAGA product domain | ZAGA product data | Product-owned logic |
| AIE Product Domain | Product | AIE product domain | AIE product data | Product-owned logic |
| HerHelp Product Domain | Product | HerHelp product domain | HerHelp product data | Product-owned logic |
| Botmad Product Domain | Product | Botmad product domain | Botmad product data | Product-owned logic |
| Ethereum Token Domain | On-Chain | Ethereum token contract layer | Ethereum mainnet | Canonical token truth |
| Base Payout Execution Domain | On-Chain | Base payout contract layer | Base | Canonical payout claim truth |
| Reserve and Vault Domains | On-Chain | Relevant vault contracts | Ethereum/Base as designed | Role-specific reserve truth |
| Governance-Controlled Contract Roles | On-Chain / Governance | Relevant contracts + governance controls | Contract state | Sensitive authority layer |
| Stripe Payment Domain | External | Stripe | Stripe systems | FUZE normalizes verified results |
| Apple Billing Domain | External | Apple | Apple systems | FUZE derives internal entitlements |
| Google Billing Domain | External | Google | Google systems | FUZE derives internal entitlements |
| Telegram Stars Rail | External | Telegram | Telegram systems | FUZE derives internal credits outcomes |
| Wallet Client Domain | External | User wallet/client | External wallet software | Not FUZE-owned |
| RPC / Indexing Provider Domain | External | Provider | External infra | Access path, not canonical meaning |
| AI Model Provider Domain | External | Provider | Provider infra | FUZE owns orchestration, not provider infra |

This matrix is normative unless superseded by a downstream spec with narrower, consistent detail.

---

## Platform Core Domain Definitions

### Identity Domain
Owns canonical user identity across the platform.

It includes:
- user records
- account identity
- identity status
- identity continuity across products

It does not own session state or wallet truth directly, though it interacts with both.

### Auth and Session Domain
Owns authentication flows, linked auth methods, session issuance, session invalidation, and login continuity.

### Workspace and Organization Domain
Owns organizations, workspaces, seats, membership, shared collaboration context, and billing ownership relationships.

### Role and Access Control Domain
Owns role resolution, permission boundaries, platform-wide access control rules, and shared authorization semantics.

### Wallet-Aware Participation Domain
Owns wallet linking, multi-wallet mapping, user-to-wallet relationship state, and derived participation context for holder-aware features.

It does not own token balances. Those remain in the Ethereum token domain.

### Commerce and Billing Domain
Owns subscriptions, usage-billing logic, commercial policy application, billing state transitions, and chargeable service semantics.

### Platform Credits Domain
Owns the policy meaning of credits, credit classes, issuance rules, spend rules, reversal rules, and internal economic constraints.

### Invoicing and Receipts Domain
Owns generated financial documents, receipt records, and user-facing financial output tied to platform billing truth.

### Payment Verification and Normalization Domain
Owns the transition from external payment events into FUZE-internal verified payment records and approved downstream issuance outcomes.

### AI Orchestration Domain
Owns model routing, execution policy, context routing, usage-metering hooks, and shared AI task orchestration interfaces.

### Workflow and Automation Domain
Owns reusable workflow primitives, automation coordination, queue-triggered processes, and event-driven execution patterns.

### Job Queue and Worker Coordination Domain
Owns operational execution of background tasks, retries, scheduling, queue state, and worker-level lifecycle behavior.

### Audit Log and Activity Domain
Owns platform-wide audit trails for meaningful state changes, sensitive actions, and traceable execution outcomes.

### Transparency Reporting Domain
Owns derived reporting pipelines and reporting views for public and stakeholder visibility.

### Payout Orchestration Domain
Owns payout-cycle preparation, entitlement construction inputs, cycle metadata, funding coordination, and payout reporting coordination before claim execution.

### Control Plane / Admin Domain
Owns platform-sensitive controls, policy-driven admin actions, emergency controls, approval-gated platform operations, and governance-aware off-chain actions.

### Integration Adapter Domain
Owns provider adapters, verification bridges, chain adapters, webhook ingestion boundaries, and dependency-specific normalization logic.

---

## Product Domain Definitions

Each product domain is an extension domain that owns product-specific logic but not platform primitives.

### QTB Domain
Owns trading-intelligence-specific entities, signals, analysis workflows, strategy-assistance outputs, and QTB-specific views.

### AIMM Domain
Owns market-operation-specific entities, workflow states, operational intelligence outputs, and AIMM-specific execution support logic.

### ZAGA Domain
Owns token-utility operating entities, utility configuration models, token-operating workflows, and ZAGA-specific outputs.

### AIE Domain
Owns event-intelligence entities, discovery objects, recommendation logic, event classification, and AIE-specific views.

### HerHelp Domain
Owns sheet-to-app transformation objects, SME operational objects, generated app artifacts, and HerHelp-specific workflows.

### Botmad Domain
Owns desktop-workflow objects, task execution artifacts, workflow-monitoring states, and Botmad-specific automation logic.

### Future Product Domains
Must follow the same rule: own product-specific objects and flows, but consume shared platform primitives.

---

## On-Chain Domain Definitions

### Ethereum Token Domain
Owns the canonical FUZE token truth:
- balances
- transfers
- supply-related events
- holder state as visible on Ethereum

### Base Credits Ledger Domain
Owns the canonical on-chain commitment of Platform Credits state where credits are written to Base by platform policy.

### Base Payout Execution Domain
Owns:
- funded payout pools
- claim rights as encoded for a cycle
- claim execution status
- payout-related on-chain events

### Reserve and Vault Contract Domains
Each vault contract owns its own encoded reserve constraints, lock rules, vesting logic, and contract-specific action restrictions.

### Governance Contract Domains
Where governance, timelock, or multisig-controlled contract roles are encoded on-chain, those contracts own that authorization truth.

---

## Reporting and Transparency Domain Definitions

Reporting domains are almost always derived domains, not canonical domains.

### Public Contract Registry
Owns the structured presentation of contract identities, roles, chain references, and explanatory mappings.

### Public Wallet Registry
Owns the structured presentation of reserve or platform-relevant wallets and their stated role categories.

### Payout Ledger
Owns the presentation and reporting structure of payout cycles and claim-related public outputs.

### Investor Reporting
Owns stakeholder-facing derived reports built from canonical sources across platform, product, financial, and on-chain systems.

### Community Reporting
Owns public/community-facing summaries and reports derived from canonical and audit sources.

Reporting domains may aggregate. They may not redefine.

---

## External Domain Definitions

External domains are never FUZE-owned canonical business domains, even when FUZE depends on them.

### Stripe
Owns raw processor state.

### App Stores
Own purchase/refund source events in their ecosystems.

### Telegram Stars
Owns Stars transaction environment.

### Wallet Clients
Own local wallet behavior and signing UX.

### RPC and Indexing Providers
Own transport and access services, not token meaning.

### AI Model Providers
Own model execution infrastructure, not FUZE orchestration or policy meaning.

### Cloud Providers
Own infrastructure service delivery, not FUZE application truth.

---

## Domain Rights Model

Each domain must explicitly define allowed rights by other domains.

### Allowed right types

- **Read**: consumer may read domain state
- **Write**: consumer may mutate domain state
- **Derive**: consumer may compute derived outputs from domain state
- **Present**: consumer may render or display domain state
- **Execute Against**: consumer may trigger actions using the domain’s interfaces
- **Govern**: consumer may approve or restrict sensitive actions in that domain

### Default rules

1. Only the canonical owner may define canonical domain state.
2. Consumers may not write canonical state unless explicitly authorized by the domain contract.
3. Reporting domains usually receive read + derive + present rights, not write rights.
4. Products usually receive read + execute-against rights for platform domains, not ownership rights.
5. Platform domains may orchestrate against on-chain domains but do not redefine chain truth.

---

## Cross-Domain Dependency Rules

The following rules apply to domain dependencies.

### Rule 1: Upward dependency is preferred over redefinition
Products should depend on platform domains instead of recreating them.

### Rule 2: Chain dependencies must be explicit
No product or platform domain may assume chain truth indirectly without declaring the adapter or read model used.

### Rule 3: Reporting depends on canonical truth, not vice versa
Canonical domains must never depend on reporting outputs as source truth.

### Rule 4: External dependencies must pass through normalization boundaries
Raw external provider outputs must not directly mutate unrelated canonical domains without validation.

### Rule 5: Governance-sensitive domains require explicit control paths
No domain may implicitly gain governance rights over another domain.

---

## Domain Boundary Decision Rules

When a new service, table, object, API, or event is introduced, it must be assigned to a canonical domain using the following rules.

### Rule A
If it is shared across multiple products, place it in a platform domain unless a formal exception exists.

### Rule B
If it defines balances, entitlements, or commercial state, it is platform-owned unless explicitly on-chain.

### Rule C
If it defines token balance truth, claim truth, or reserve constraints, it is on-chain-owned.

### Rule D
If it defines category-specific product logic with no shared ecosystem meaning, it is product-owned.

### Rule E
If it is only a summary, dashboard, or external-facing display, it is reporting-owned as a derived or presentation domain.

### Rule F
If it only represents an external provider’s raw signal, it remains external until normalized.

---

## Domain Escalation Rules

Ambiguous ownership must be escalated rather than silently implemented.

Escalation is required when:

- more than one product needs the same capability
- a product attempts to create balance-like or entitlement-like state
- a derived report begins to define business meaning
- a provider integration starts holding business-critical interpretation logic
- an off-chain domain appears to redefine chain truth
- a product domain attempts to own governance-sensitive state

The escalated decision must resolve:
- canonical owner
- source of truth
- write authority
- read/derive/present rights
- governance rights if any

---

## Security Implications

This matrix is a security document as much as an architecture document.

If domains are not explicit:

- products may overreach into platform state
- reports may redefine business truth
- chain truth may be shadowed by stale caches
- payment rails may mutate balances incorrectly
- governance-sensitive domains may be controlled through normal runtime paths

All downstream security and abuse-prevention specifications must preserve this matrix.

---

## Transparency Implications

The domain matrix also supports public intelligibility.

FUZE must be able to explain:

- which domain owns token truth
- which domain owns credits policy
- which domain owns claim execution
- which domain owns reserve constraints
- which domain owns public reporting
- which outputs are canonical and which are derived

This is essential to FUZE’s transparency-first architecture.

---

## Edge Cases and Failure Handling

### Cached product copy of platform state
A cached product copy is non-canonical and must reconcile to the platform owner.

### Reporting mismatch with chain events
The chain domain remains canonical for chain-owned truth. Reporting must reconcile.

### Provider outage during payment flow
External provider domain failure must not be treated as canonical platform billing completion until normalization and verification are completed.

### Product invents product-local credits
This violates the matrix unless formally approved as a non-canonical restricted sub-ledger under the Platform Credits domain.

### Wallet-linked participation tier differs from latest chain state
Ethereum token domain remains canonical for balance truth. The platform wallet-aware domain may lag temporarily but must reconcile.

### Derived investor report computes a metric incorrectly
The report is wrong, but it does not alter canonical platform or on-chain truth.

---

## Open Items

The following items are intentionally deferred to downstream specs:

- exact entity-to-domain mapping for every table and event
- exact API ownership per service
- exact event producer/consumer ownership
- exact write-path and idempotency ownership by workflow
- exact repository boundary design by domain

These items refine but do not replace the matrix in this file.

---

## Closing Summary

The FUZE domain ownership matrix establishes the explicit domain map required for a platform-first ecosystem to remain coherent. Platform domains own shared infrastructure and shared economic logic. Product domains own product-specific objects and flows. On-chain domains own only the truths explicitly committed to contract state. Reporting domains are primarily derived and presentational. External domains remain external until normalized. This matrix is the implementation-grade foundation for service design, schema boundaries, API design, event architecture, governance controls, and future product expansion across the FUZE ecosystem.
