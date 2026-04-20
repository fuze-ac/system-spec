# ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC

## Purpose

This document defines the canonical role, permission, and access control architecture of the FUZE platform. Its purpose is to establish how authority is assigned, resolved, constrained, and enforced across accounts, workspaces, products, platform services, governance-sensitive actions, and reporting surfaces.

This specification is critical because FUZE is a multi-product platform ecosystem with shared identity, shared billing, shared credits, wallet-aware participation, workspace collaboration, product-specific operations, and governance-sensitive control paths. Without a disciplined access-control model, the platform would become operationally inconsistent, economically fragile, and difficult to trust.

---

## Scope

This specification covers:

- the canonical access-control model for FUZE
- the distinction between identity, membership, role, permission, and entitlement
- account-level versus workspace-level authority
- platform-wide roles versus product-scoped roles
- action-level permission evaluation
- billing and credits access control
- wallet-aware and holder-aware access implications
- admin, support, and control-plane privileges
- governance-sensitive access boundaries
- audit and failure-handling requirements for access control

This specification does not define the full details of session issuance, workspace lifecycle, or token snapshot logic. Those are refined in:

- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `WALLET_AWARE_USER_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `GOVERNANCE_MODEL_SPEC.md`

---

## Design Goals

The design goals of the FUZE role and access model are:

1. to make authority explicit across platform, workspace, and product contexts
2. to separate who the user is from what the user is allowed to do
3. to support both individual and collaborative product usage
4. to prevent products from inventing incompatible authorization logic for shared platform concerns
5. to preserve strong controls around billing, credits, wallet links, and governance-sensitive operations
6. to support future product expansion without weakening authorization clarity
7. to make access decisions auditable and reviewable
8. to keep permission evaluation consistent with the broader platform-first architecture

---

## Non-Goals

This specification is not intended to:

- use token ownership as a substitute for platform authorization
- collapse platform permissions into product feature toggles
- treat session existence alone as sufficient authorization
- make workspace ownership equivalent to unrestricted platform administration
- define a full policy engine implementation language
- provide uncontrolled “super admin” shortcuts that bypass audit and governance rules
- replace product-specific fine-grained permissions where those are needed on top of platform rules

---

## Canonical Access Principle

The primary access-control principle of FUZE is:

> access is granted by explicit role and permission evaluation against a canonical account operating in a defined context, not merely by authentication success, token ownership, or product-local assumption.

This means:

- authentication proves access to the account
- membership connects the account to a workspace context
- roles define authority within a scope
- permissions define allowed actions
- product entitlements define whether a product or feature is commercially or operationally enabled
- token-aware status may inform certain privileges, but it does not replace authorization

This principle must be preserved across all products and platform services.

---

## Core Concepts

### Account
The canonical identity of the actor in FUZE.

### Workspace Membership
The relationship between an account and a workspace.

### Role
A named authority bundle assigned within a defined scope.

### Permission
A specific allowed action, such as:
- invite member
- spend workspace credits
- view billing history
- edit product configuration
- initiate payout reporting export

### Scope
The context in which a role or permission applies. Common scopes include:
- account scope
- workspace scope
- product scope
- platform-admin scope
- governance-sensitive scope

### Entitlement
A commercial or product-access state determining whether the account or workspace is eligible to use a product, feature, or service.

### Access Decision
The result of evaluating identity, session, membership, role, permission, entitlement, and policy constraints for a requested action.

---

## Canonical Access Model

FUZE uses a layered access model.

At minimum, every meaningful access decision should be understood as a function of:

1. authenticated account identity
2. active session validity
3. selected or resolved workspace context where applicable
4. membership state in that workspace where applicable
5. assigned role(s)
6. relevant permission(s)
7. product entitlement or billing state where applicable
8. additional policy checks for sensitive or governance-aware actions

This layered model is important because no one factor is sufficient by itself.

Examples:
- a valid session is not enough to spend workspace credits
- a workspace membership is not enough to manage billing
- token ownership is not enough to perform admin actions
- product access is not enough to change platform-wide configuration

The access model must therefore remain multi-dimensional.

---

## Distinction Between Role, Permission, and Entitlement

FUZE must preserve three separate concepts:

### Role
Defines structural authority in a scope.

Examples:
- workspace owner
- workspace admin
- workspace member
- product operator
- billing manager
- support operator
- platform admin

### Permission
Defines the precise action allowed.

Examples:
- `workspace.invite_member`
- `workspace.remove_member`
- `billing.view_invoice`
- `credits.spend_workspace`
- `product.qtb.manage_signals`
- `admin.restrict_account`

### Entitlement
Defines whether the account or workspace is commercially or operationally eligible to use the relevant product or feature.

Examples:
- QTB access enabled
- workspace has Botmad plan
- feature unlocked by credits
- premium AI tier active

A user may have permission to configure a feature but lack entitlement to use it until billing conditions are satisfied.

---

## Scope Model

Roles and permissions in FUZE must always be tied to a scope.

### 1. Account Scope
Permissions tied directly to the user’s own account.

Examples:
- update own profile
- manage own linked logins
- view own receipts
- link own wallet

### 2. Workspace Scope
Permissions tied to a collaborative context.

Examples:
- invite workspace member
- manage workspace billing
- spend workspace credits
- configure shared product resources

### 3. Product Scope
Permissions tied to product-specific functions within account or workspace context.

Examples:
- manage QTB watchlists
- approve Botmad workflow
- edit HerHelp generated app
- manage ZAGA utility configuration

### 4. Platform Admin Scope
Permissions tied to platform-wide operational control.

Examples:
- restrict account
- review abuse case
- correct billing state
- rotate provider configuration under policy

### 5. Governance-Sensitive Scope
Permissions tied to actions that require stronger control, explicit policy, or coordination with governance-aware control paths.

Examples:
- approve high-impact credits adjustment path
- change payout-cycle operational status
- alter contract registry mapping
- execute reserve-related off-chain control action

Scope must never be implicit.

---

## Canonical Role Categories

FUZE must support role categories across multiple levels.

### Account-Level Roles
Roles associated with the account’s authority over itself.

Examples:
- account owner (implicit self-authority)
- self-service actor
- restricted account state
- support-reviewed account state

### Workspace-Level Roles
Roles associated with collaborative environments.

Minimum recommended roles:
- workspace owner
- workspace admin
- workspace billing manager
- workspace operator
- workspace member
- workspace viewer

### Product-Level Roles
Roles that extend workspace or account authority inside one product domain.

Examples:
- QTB operator
- AIMM operations manager
- ZAGA utility manager
- AIE editor
- HerHelp app manager
- Botmad workflow manager

### Platform Operational Roles
Roles for internal platform operations.

Examples:
- support operator
- finance operator
- risk reviewer
- platform admin
- reporting operator

### Governance-Aware Roles
Roles used for sensitive control pathways.

Examples:
- governance approver
- control-plane reviewer
- payout-cycle operator
- registry maintainer under policy
- emergency operations operator

Not every installation needs every role at first launch, but the semantic categories must exist.

---

## Workspace-Level Role Semantics

At minimum, the workspace layer should support the following semantic roles.

### Workspace Owner
Highest ordinary workspace authority.

May typically:
- manage membership
- manage workspace settings
- manage workspace billing context
- assign roles subject to platform limits
- archive or close workspace if permitted
- manage shared workspace resources

May not automatically:
- become platform admin
- bypass governance-sensitive controls
- override product-specific safety rules without policy support

### Workspace Admin
Operational administrator inside the workspace.

May typically:
- invite members
- manage many workspace settings
- manage product access within workspace rules
- view usage and reporting where allowed

May not automatically:
- transfer ownership unless explicitly allowed
- perform all billing-sensitive or governance-sensitive actions

### Billing Manager
Commercial authority for workspace billing.

May typically:
- view invoices
- manage subscriptions
- manage payment methods where supported
- view credit balances
- approve certain spending actions

### Workspace Operator
Operational role for managing shared workflows and product functions.

### Workspace Member
Normal active participant with product usage access according to entitlements and product configuration.

### Workspace Viewer
Read-only or limited access participant where appropriate.

These are canonical semantic roles. Exact permission mapping is defined by platform policy and downstream specs.

---

## Product-Level Role Semantics

Products may define additional roles, but only on top of the canonical platform model.

### Product role principles

- product roles must resolve from an authenticated account and, where relevant, a valid workspace membership
- product roles may extend but not replace workspace/platform roles
- product roles must not bypass billing, credits, or governance constraints
- product roles must remain namespaced to the product domain

### Examples

#### QTB
- signal manager
- strategy viewer
- alert operator

#### AIMM
- market-ops manager
- liquidity observer
- execution reviewer

#### ZAGA
- utility manager
- campaign operator
- token-ops editor

#### AIE
- intelligence editor
- event curator
- workspace viewer

#### HerHelp
- app builder
- form manager
- business workflow operator

#### Botmad
- workflow designer
- task reviewer
- execution monitor

Product roles should be implemented only where product complexity justifies them.

---

## Access Decision Flow

The canonical access decision flow in FUZE should be:

1. resolve authenticated account from session
2. verify session is still valid
3. resolve requested scope (account / workspace / product / admin / governance-sensitive)
4. resolve workspace membership if workspace context exists
5. verify workspace and membership are active
6. resolve assigned roles in the relevant scope
7. check required permission(s)
8. check required entitlement or billing state
9. apply policy and restriction checks
10. return allow, deny, or escalate/review-required result

This flow should be explicit in implementation, even if optimized internally.

---

## Minimum Permission Domains

The permission model should at minimum support the following permission domains.

### Identity and Account Permissions
Examples:
- manage linked login methods
- update self profile
- initiate account recovery
- view own audit history where allowed

### Workspace Permissions
Examples:
- invite member
- remove member
- assign role
- view workspace usage
- archive workspace
- transfer workspace ownership

### Billing and Credits Permissions
Examples:
- view invoices
- manage billing plan
- view workspace credits balance
- spend workspace credits
- issue support credit adjustment (internal role only)
- view payment history

### Product Permissions
Examples:
- create product resource
- edit product resource
- run product workflow
- manage shared product settings
- approve product-level automation

### Reporting Permissions
Examples:
- view internal report
- export report
- view transparency report inputs
- access payout ledger views

### Platform Admin Permissions
Examples:
- restrict account
- suspend workspace
- review abuse case
- execute support correction
- view system-wide operational dashboards

### Governance-Sensitive Permissions
Examples:
- initiate payout-cycle operational transition
- approve control-plane change
- perform reserve-related off-chain coordination
- update registry data under policy control

---

## Permission Inheritance and Resolution

FUZE should support explicit permission inheritance, but it must remain bounded.

### Recommended inheritance principles

- higher roles may include lower-role permissions within the same scope
- workspace roles may grant product access only where product policy allows
- product roles may refine capabilities inside a product but may not enlarge platform authority automatically
- platform operational roles do not automatically grant governance-sensitive permissions
- governance-sensitive permissions should not be inherited casually from ordinary admin roles

### Example

A workspace owner may inherit:
- workspace admin permissions
- member permissions
- viewer permissions

But a workspace owner should not automatically inherit:
- support operator privileges
- platform finance correction privileges
- payout-cycle admin privileges
- governance registry modification privileges

Inheritance must be intentional, not assumed.

---

## Restriction and Deny Model

Access control must support explicit deny or restriction outcomes.

### Deny scenarios include:
- no valid session
- missing membership
- missing permission
- inactive workspace
- insufficient entitlement
- account restricted or suspended
- workspace restricted or suspended
- policy-blocked sensitive action

### Restriction behavior

A restricted actor may:
- still exist in the system
- still appear in audit history
- still be readable for support and reporting
- lose normal action rights until reinstatement or review completion

A deny result must not be treated as an implementation inconvenience. It is a required platform state.

---

## Billing and Credits Access Control

Billing and credits are especially sensitive domains and require explicit controls.

### Typical workspace-facing permissions

- view billing summary
- view invoices and receipts
- manage subscription plan
- add or manage payment method where supported
- view credits balance
- approve workspace credits spending for sensitive or high-value actions
- access usage and spend history

### Internal-only permissions

- issue corrective credits adjustment
- reverse balance under policy
- review payment anomalies
- flag fraud or abuse risk
- perform finance-side remediation

Products must not redefine billing or credits authority locally. They must rely on platform-controlled permission domains.

---

## Wallet-Aware and Holder-Aware Access Implications

Wallet-aware context may influence user experience or eligibility, but it must not replace authorization.

### Correct usage examples

- showing holder rank
- enabling a holder-only feature if platform policy says so
- determining payout eligibility context through wallet linkage and snapshot logic

### Incorrect usage examples

- granting workspace admin privileges because a wallet holds tokens
- bypassing billing controls because a wallet is linked
- granting support/admin access based on token ownership
- treating wallet possession as sufficient proof of product authority

Token-aware context is a participation dimension, not a substitute for the platform permission model.

---

## Admin and Support Access Model

FUZE must define a bounded internal operational access model.

### Minimum internal operational roles may include:

- support operator
- finance operator
- abuse/risk reviewer
- reporting operator
- platform admin

### Rules for internal roles

- each role must be scoped and permission-bounded
- internal roles must be auditable
- support operators should not automatically receive finance or governance-sensitive permissions
- platform admin should remain bounded by policy and control-plane rules
- sensitive internal actions must generate audit events and, where necessary, require dual control or approval

Operational convenience must not replace structured access control.

---

## Governance-Sensitive Access Model

Certain actions require stricter control than normal admin permissions.

### Governance-sensitive actions may include:

- payout-cycle control changes
- reserve-related off-chain operational actions
- registry changes affecting public contract or wallet interpretation
- policy-changing credits control paths
- emergency-control triggers
- changes linked to multisig/timelock-governed execution paths

### Governance-sensitive access rules

- must be explicitly classified
- must not be granted through ordinary workspace roles
- must not be assumed from product ownership
- should be routed through control-plane and governance-aware workflows
- should support pending/approved/rejected states where appropriate
- must be auditable

This distinction is essential to FUZE’s trust model.

---

## Audit Requirements

Access-control events must be auditable.

At minimum, audit events should exist for:

- role assignment
- role removal
- membership change affecting access
- billing-authority change
- workspace ownership change
- permission-sensitive action denials where policy requires logging
- internal support/admin actions
- governance-sensitive access requests or approvals
- account or workspace restriction actions

Audit is not optional because authority changes are part of the trust surface of the platform.

---

## Minimum Data Model Requirements

At minimum, the access-control system must support:

### Role Assignment
- subject account ID
- role name
- scope type
- scope ID
- assigned_by
- assigned_at
- expires_at or removal state where applicable

### Membership/Access State
- account ID
- workspace ID
- membership status
- role references

### Permission Mapping
- role name
- permission name(s)
- scope applicability
- policy version or config reference where applicable

### Access Audit Record
- actor
- target
- action
- scope
- result
- timestamp
- reason or policy context where applicable

---

## Integration Points

The role, permission, and access-control system integrates with:

- identity and account domain
- auth/session domain
- workspace and organization domain
- billing and credits domains
- wallet-aware participation domain
- product domain services
- control-plane and governance services
- audit log service
- reporting and transparency services

Each integration must treat the access-control domain as canonical for authorization logic.

---

## Edge Cases and Failure Handling

### User authenticated but no workspace selected
The system must deny workspace-scoped actions until a valid workspace context is resolved.

### User has workspace membership but product not enabled
Access to that product feature must be denied until entitlement exists.

### Role removed while session remains active
Subsequent authorization checks must reflect the updated role state. Session validity does not override changed authority.

### Workspace owner removed or suspended
Controlled reassignment, restriction, or review workflow must occur. Ownership cannot remain logically orphaned.

### Product caches old permission state
Cached state is non-canonical. Fresh authorization checks must prevail for sensitive actions.

### Internal admin attempts governance-sensitive action without proper scope
The action must be denied or routed into the governance-aware control path.

### Holder rank implies feature access but role missing
Feature may be visible or conditionally eligible, but actual action must still satisfy authorization rules.

---

## Open Items

The following items are intentionally refined in downstream or implementation specifications:

- exact policy language or access engine representation
- exact role-to-permission tables by product
- exact seat-based permission interactions
- exact approval workflow for governance-sensitive off-chain actions
- exact just-in-time elevation or break-glass admin procedures if any

These do not weaken the canonical access model established here.

---

## Closing Summary

FUZE uses a layered access-control model in which authentication identifies the canonical account, workspace membership establishes collaborative context, roles define authority, permissions define allowable actions, and entitlements define product or feature eligibility. This model is necessary because FUZE combines individual use, team collaboration, shared billing, Platform Credits, product-specific operations, and governance-sensitive actions inside one ecosystem. Products may extend this model, but they may not replace it. Clear role, permission, and access boundaries are therefore essential to FUZE’s platform coherence, operational integrity, and long-term trust model.
