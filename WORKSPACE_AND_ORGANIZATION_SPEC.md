# WORKSPACE_AND_ORGANIZATION_SPEC

## Purpose

This document defines the canonical workspace and organization model of the FUZE platform. Its purpose is to establish how collaborative entities are represented, how accounts participate in shared contexts, how workspace ownership and membership operate, how billing and balances may be attached to workspace scope, and how products should interpret organization-level access across the FUZE ecosystem.

This specification is essential because FUZE is not designed only for isolated individual usage. It is intended to support products used by teams, operators, trading groups, SMEs, internal business units, and future multi-user environments. The workspace and organization layer is therefore one of the core shared platform capabilities that turns FUZE from a set of user accounts into a real operating environment.

---

## Scope

This specification covers:

- the canonical organization and workspace model
- the distinction between account identity and collaborative context
- workspace lifecycle and state transitions
- workspace ownership and membership rules
- role relationships at workspace level
- account-to-workspace associations
- billing ownership and shared-balance implications
- workspace-scoped product access and entitlements
- multi-workspace participation by one account
- relationship between workspace context and product domains
- audit, security, and control implications of workspace operations

This specification does not fully define detailed authorization semantics, wallet linkage, or billing algorithms. Those are refined in:

- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `PLATFORM_CREDITS_SPEC.md`
- `SUBSCRIPTIONS_AND_USAGE_BILLING_SPEC.md`
- `WALLET_AWARE_USER_SPEC.md`

---

## Design Goals

The design goals of the workspace and organization model are:

1. to provide one canonical collaborative layer across all FUZE products
2. to separate individual account identity from team or business operating context
3. to support both solo and multi-member product usage inside one platform
4. to allow a single account to participate in multiple organizations or workspaces
5. to support workspace-scoped balances, entitlements, and billing ownership where policy requires
6. to provide a clean foundation for shared product usage across teams
7. to prevent products from inventing incompatible collaboration models
8. to preserve auditability and access clarity in multi-user environments

---

## Non-Goals

This specification is not intended to:

- treat a workspace as a replacement for canonical account identity
- require every user to belong to an organization before using FUZE
- define every product-specific team feature in this file
- allow products to create incompatible organization models that replace platform workspace truth
- define full enterprise account contracting or legal-entity compliance flows unless later added explicitly
- treat wallet ownership as the same thing as workspace ownership

---

## Canonical Workspace Principle

The primary principle of the FUZE workspace layer is:

> the account is the canonical identity of the actor, while the workspace or organization is the canonical collaborative and operating context through which one or more accounts may access shared balances, shared permissions, and shared product resources.

This means:

- accounts represent who the actor is
- workspaces represent the collaborative context in which the actor is operating
- products should resolve both account and workspace context where applicable
- workspace context may affect access, billing, and entitlements, but it does not replace the account as canonical identity

This distinction is mandatory across the ecosystem.

---

## Core Concepts

### Account
The canonical individual identity object of FUZE.

### Organization
A top-level collaborative container representing a business, team, project group, or structured multi-user entity.

### Workspace
A concrete operating environment under an organization or directly attached collaborative scope in which members, balances, product access, roles, and workflows may be coordinated.

### Membership
The relationship between an account and a workspace.

### Workspace Owner
The account or authority designated as the primary owner/controller of the workspace, subject to governance and platform rules.

### Billing Owner
The account or workspace-level financial context responsible for subscriptions, credit balance ownership, or payment responsibility.

### Seat
A unit of workspace participation used where licensing, plan limits, or product policy require member-count or access-count tracking.

### Workspace-Scoped Resource
Any product, balance, entitlement, setting, or shared object whose owner is the workspace rather than one individual account.

---

## Canonical Model of Organization and Workspace

FUZE should support a hierarchical but flexible model.

### Recommended model

- **Account** is the individual identity root
- **Organization** is the broader container for business or group identity where needed
- **Workspace** is the operating container where actual collaboration, billing scope, and product usage occur

In early implementations, organization and workspace may be closely aligned or represented in simplified form if operationally appropriate. However, the semantic distinction should still be preserved:

- organization represents the broader entity or grouping
- workspace represents the actionable operating environment

This distinction becomes more valuable as the platform grows into enterprise, partner, multi-team, or cross-product usage.

---

## Workspace Lifecycle

At minimum, the workspace lifecycle should support the following states:

- `pending_setup`
- `active`
- `restricted`
- `suspended`
- `archived`
- `closed`

### Lifecycle meanings

#### Pending Setup
Workspace exists but has not completed required setup steps.

#### Active
Workspace is operational and may hold members, balances, product access, and active workflows.

#### Restricted
Workspace remains present but is limited due to risk controls, billing issues, policy concerns, or administrative constraints.

#### Suspended
Workspace access is materially limited pending review, abuse handling, security review, or billing/governance intervention.

#### Archived
Workspace is no longer active for normal operations but remains retained for reporting, recovery, export, or compliance reasons.

#### Closed
Workspace is no longer intended for use and has been finalized according to platform retention and policy rules.

Lifecycle transitions must be auditable.

---

## Workspace Creation Model

Workspace creation is a platform-controlled capability.

### Workspace may be created by:

- an authenticated account with appropriate platform permission
- an approved invitation or onboarding workflow
- future enterprise provisioning pathways if supported

### Workspace creation must define at minimum:

- workspace identifier
- creator account
- owner relationship
- initial status
- initial membership state
- billing-owner model
- optional organization association

### Creation principles

1. workspace creation must not create a new canonical user identity
2. workspace creation must attach to an existing canonical account
3. workspace creation should define initial access and ownership unambiguously
4. workspace creation must produce audit events

---

## Ownership Model

Workspace ownership is a sensitive platform concept and must remain explicit.

### Workspace owner responsibilities may include:

- managing workspace settings
- inviting or removing members
- assigning or approving roles subject to access rules
- controlling billing ownership or payment setup where policy allows
- managing product-level enablement within workspace scope
- initiating workspace archive or closure flows where permitted

### Ownership rules

- every workspace must have at least one valid owner or equivalent controlling authority
- ownership transfer must be explicit and auditable
- products may not reinterpret workspace ownership independently
- owner status does not eliminate platform governance authority
- workspace ownership is distinct from wallet ownership and token ownership

---

## Membership Model

Membership connects accounts to workspace context.

### Membership must support:

- account ID
- workspace ID
- membership status
- role assignment(s)
- invitation and acceptance flow where applicable
- join time
- removal or leave time where relevant

### Membership states may include:

- `invited`
- `active`
- `restricted`
- `removed`
- `left`

### Membership principles

- one account may belong to multiple workspaces
- a workspace may contain multiple accounts
- product access inside a workspace must evaluate membership and role, not just login state
- removed membership should not destroy canonical account identity
- membership history should remain auditable

---

## Multi-Workspace Participation

A single canonical account may participate in multiple workspaces.

This is a required platform behavior because:

- users may operate across multiple teams, companies, or project groups
- service providers or partners may support several workspaces
- one user may have both personal and organizational contexts
- a user may interact with multiple products under different collaborative scopes

### Multi-workspace rules

- workspace selection or workspace context resolution must be explicit where ambiguity exists
- access to one workspace must not imply access to another
- credits and billing scope must be determined according to account-vs-workspace ownership rules
- product state must remain tied to the correct workspace context where a resource is workspace-owned

This model is essential for serious multi-user and multi-product operation.

---

## Relationship Between Workspace and Product Access

Workspace context influences product access but does not replace product entitlements or role evaluation.

### Product access within a workspace typically depends on:

1. canonical account identity
2. valid session/auth state
3. workspace membership
4. role and permission rules
5. workspace-level product enablement
6. billing/entitlement status
7. product-specific policy rules

This layered model matters because a user may be authenticated successfully but still lack access to a workspace-scoped product feature if:
- they are not a member of that workspace
- the workspace has no entitlement for that product
- the workspace role does not allow the action
- billing or credit state does not support the feature

Products must therefore interpret workspace context through platform rules rather than provider-specific or product-local shortcuts.

---

## Workspace Billing Ownership

The workspace model must support clear billing ownership.

A workspace may be:

- individually billed through one account acting as billing owner
- workspace-billed through a shared commercial context
- enterprise-billed through a future managed arrangement if supported later

### Billing ownership implications

The workspace model should be able to support:
- workspace-scoped subscriptions
- seat-based plans
- shared Platform Credits balances where enabled
- invoice visibility at workspace scope
- usage attribution by workspace
- product-level entitlements attached to workspace rather than one user

### Key principle

Billing ownership does not redefine canonical account identity. It defines who pays or what context holds the commercial obligation.

This distinction is essential for platform coherence.

---

## Workspace and Platform Credits Relationship

Workspace context may be a holder of Platform Credits under platform policy.

This means FUZE should support, where appropriate:

- workspace-level credit balances
- workspace-scoped credit spending
- shared consumption of credits by authorized members
- role-based restrictions on who may spend or manage workspace balances
- reporting of workspace credit usage distinct from personal balances

### Required principles

- credits held by a workspace remain distinct from credits held personally by an account
- movement between personal and workspace credit contexts, if allowed, must be explicit and auditable
- products must know whether they are charging the user context or the workspace context
- billing and spend records must preserve scope clarity

This relationship is especially important for team-oriented products and future enterprise usage.

---

## Workspace-Scoped Resources

A workspace may own resources distinct from personal account-owned resources.

Examples may include:

- shared dashboards
- shared product configurations
- shared AI outputs where policy allows
- team workflow objects
- shared automation configurations
- workspace credits balances
- workspace subscriptions
- shared project or operating resources

### Canonical rule

If a resource is workspace-scoped, its owner is the workspace context, not one member account, even if one member created it.

This rule is important for portability, continuity, offboarding, and team operation.

---

## Organization Layer Semantics

Where organization support is implemented distinctly from workspace support, organization should represent the broader entity boundary.

An organization may:
- contain one or more workspaces
- define higher-level ownership or administration
- serve as a reporting, billing, or policy umbrella
- represent a company, protocol team, partner entity, or structured group identity

### Important distinction

The organization is not automatically the same thing as the workspace.
A workspace is the actionable operating environment.
An organization is the broader umbrella entity.

Early implementations may simplify this relationship, but downstream design must preserve the semantic distinction.

---

## Invitation and Join Model

Workspaces must support controlled member admission.

### Canonical invitation flow should support:

- invite initiated by authorized actor
- pending invite state
- acceptance by target account or target provider-linked identity
- role assignment on acceptance
- invite expiry or invalidation where policy requires

### Join principles

- joining a workspace must not create a second canonical account if the actor already has one
- invite acceptance must bind membership to the canonical account
- invitation misuse or conflict must be auditable
- products must not bypass workspace membership rules through local sharing shortcuts unless platform-approved

---

## Workspace Security Model

Workspace security is broader than account authentication.

### Workspace security must consider:

- who may invite members
- who may remove members
- who may assign roles
- who may manage billing
- who may manage shared balances
- who may access product-specific shared resources
- who may archive or close the workspace
- what happens if the workspace owner is lost, restricted, or removed

### Security principle

Access to workspace scope must be governed through role-aware and membership-aware platform controls. Product-local assumptions are not sufficient.

---

## Workspace Ownership Transfer

Ownership transfer is a sensitive action.

### Ownership transfer rules

- must be explicit
- must be auditable
- must require authorized initiator(s)
- must preserve continuity of workspace access and billing control
- must not orphan the workspace without a valid controlling authority

### Special cases

Support/admin-controlled ownership reassignment may be needed when:
- the original owner loses access
- the original owner leaves the organization
- abuse/security review requires reassignment
- enterprise control rules override individual owner control

These flows must remain governance-aware and auditable.

---

## Workspace Suspension, Restriction, and Closure

Workspace state control is a platform responsibility.

### Restriction or suspension may occur because of:

- abuse or fraud concerns
- billing failure
- policy violations
- security incidents
- support review needs
- governance-sensitive issues affecting shared resources

### Closure or archive principles

- closure must not erase canonical account identity
- closure must preserve auditability and reporting continuity
- closure must preserve or settle shared balances and billing obligations according to policy
- archived or closed workspace resources must have clearly defined retention behavior

Products must not invent incompatible workspace closure logic.

---

## Minimum Data Model Requirements

At minimum, the workspace and organization layer must support:

### Organization
- organization_id
- name or canonical label
- status
- created_at
- owner/admin references as needed

### Workspace
- workspace_id
- optional organization_id
- owner reference
- billing_owner reference or model
- status
- created_at
- archived/closed fields where relevant

### Membership
- membership_id
- account_id
- workspace_id
- role(s)
- membership_status
- invited_at
- joined_at
- removed_at/left_at where relevant

### Workspace Audit Events
- workspace created
- owner changed
- billing-owner changed
- member invited
- member joined
- member removed
- workspace restricted/suspended
- workspace archived/closed

---

## Read / Write Rights

### Workspace Domain may:
- create and mutate canonical workspace records
- manage membership relationships
- manage owner relationships
- expose workspace-scoped read models
- coordinate billing-owner and workspace-level lifecycle state

### Product domains may:
- read workspace context
- attach product-specific workspace settings
- create workspace-owned product resources
- apply product-specific logic to workspace-scoped entitlements

### Product domains may not:
- redefine canonical workspace existence
- silently create hidden workspace membership outside platform rules
- redefine owner semantics
- mutate platform workspace lifecycle state outside approved service contracts

### Reporting domains may:
- derive and present workspace metrics or organization summaries
- join workspace context into reporting outputs

Reporting domains may not redefine canonical workspace truth.

---

## Integration Points

The workspace and organization layer integrates with:

- identity and account domain
- auth and session domain
- role and access control domain
- billing and commerce domain
- Platform Credits domain
- wallet-aware participation domain where workspace-aware benefits exist
- product domain services
- audit log domain
- reporting and transparency domains
- control-plane and support operations

All such integrations must treat workspace records as canonical for collaborative context.

---

## Edge Cases and Failure Handling

### Account leaves all workspaces
The account remains canonical and active as an individual identity unless separately restricted.

### Owner account suspended
Workspace may require controlled reassignment, restricted mode, or support intervention according to policy.

### Product resource created under wrong scope
The system must support correction or explicit migration while preserving auditability. Scope ambiguity must not remain silent.

### Billing owner removed from workspace
Workspace billing control must be reassigned or the workspace restricted according to platform policy.

### Invite accepted with wrong account
The membership must attach to the canonical accepting account and support/admin tooling must handle correction if misbinding occurs.

### Multi-workspace user confusion
The platform must support clear active-context selection so users and products know which workspace scope is in effect.

### Reporting mismatch
Derived workspace reports do not override canonical workspace membership or ownership truth.

---

## Open Items

The following areas are intentionally refined downstream:

- exact organization-to-workspace hierarchy rules for enterprise mode
- exact seat licensing semantics
- exact shared-balance rules for each product
- exact invite transport and invite token model
- exact offboarding data-retention behavior by product resource type
- exact support/admin procedures for ownership reassignment

These do not weaken the canonical model established here.

---

## Closing Summary

The FUZE workspace and organization layer defines the collaborative operating context of the platform. Accounts remain the canonical identity of actors, while organizations and workspaces provide the shared context in which members, balances, entitlements, product resources, and workflows operate. This distinction allows FUZE to support individual use, team use, SME use, and future enterprise use inside one coherent platform architecture. Products may extend workspace behavior, but they may not redefine the canonical workspace model. This structure is essential for making FUZE a real multi-product operating environment rather than a collection of disconnected user accounts.
