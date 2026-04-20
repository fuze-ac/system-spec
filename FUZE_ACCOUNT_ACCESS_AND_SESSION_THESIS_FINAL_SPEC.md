# FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC

## Document Metadata

- Document Name: `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
- Document Type: Thesis-style platform system specification
- Status: Active refined thesis spec
- Governing Layer: Platform core / public-facing thesis for account, access, and session
- Parent Registry: `REFINED_SYSTEM_SPEC_INDEX.md`
- Primary Audience: FUZE community, product builders, platform builders, operators, support, security reviewers, ecosystem readers
- Primary Purpose: Explain, in clear platform language, how FUZE thinks about account identity, access methods, and session runtime state as one connected foundation without collapsing them into one concept
- Primary Upstream References:
  - `REFINED_SYSTEM_SPEC_INDEX.md`
  - `DOCS_SPEC_INDEX.md`
  - `SYSTEM_SPEC_INDEX.md`
  - `API_SPEC_INDEX.md`
  - `IDENTITY_AND_ACCOUNT_SPEC.md`
  - `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
  - `WALLET_AWARE_USER_SPEC.md`
  - `WORKSPACE_AND_ORGANIZATION_SPEC.md`
  - `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
  - `AUTH_IDENTITY_API_SPEC.md`
  - `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
  - `PUBLIC_API_SPEC.md`
  - `INTERNAL_SERVICE_API_SPEC.md`
- Primary Downstream Dependents:
  - community-facing architecture explanation
  - public positioning of FUZE identity/access model
  - deeper identity, session, provider, continuity, recovery, and API specs
  - product onboarding and builder-facing identity guidance

---

## Purpose

This document explains, in clear public language, how FUZE thinks about **account**, **authentication**, and **session** as one connected platform foundation.

It is written so that the FUZE community, product builders, future contributors, and internal operators can understand the philosophy behind the system design, why the boundaries matter, and how FUZE can support many products, many login styles, many workspaces, and many user contexts without fragmenting identity.

This is a **thesis-style system design specification**.

It is intentionally more explanatory and more human-readable than the deeper implementation-oriented specs. It does not replace the detailed canonical platform specs. Instead, it explains the model, the reasoning, and the durable platform rules in a form that is easier to communicate publicly and easier to use as the narrative foundation for the deeper core library.

---

## Scope

This thesis explains the platform meaning of:

- canonical account identity
- approved access methods and linked login
- session as temporary authenticated runtime state
- the separation between authentication and later authorization
- continuity of one person across many FUZE products
- the relationship between account, workspace, wallet-aware participation, and product access
- the public design principles FUZE should communicate clearly

This thesis does not attempt to fully define:

- exact provider-resolution heuristics
- exact recovery playbooks
- exact token or cookie transport details
- exact password, MFA, or step-up implementation
- exact API schemas
- exact workspace or permission models
- exact wallet verification protocols
- exact support tooling or admin procedures

Those belong in deeper canonical specifications.

---

## Why This Document Exists

FUZE is not a single-product app.

FUZE is designed as a **multi-product platform ecosystem**. One person may interact with FUZE through different products, different workspaces, different access methods, and different ecosystem contexts over time.

For example, one person may:

- first enter through HerHelp using Google
- later use another FUZE product with Telegram
- later join a team workspace
- later link a wallet for holder-aware or participation-aware features
- and still remain the same person in the FUZE ecosystem

That is why FUZE cannot treat login as the whole identity model.

A login method is only **one way to reach** a person’s platform account.  
The account itself is the durable platform identity.  
The session is the temporary runtime access state after authentication succeeds.

This separation is central to the FUZE platform design.

---

## The Core Thesis

The core thesis of FUZE account access is simple:

> **A person should have one canonical FUZE account. That account may be reached through multiple approved access methods. A session is then issued as temporary authenticated runtime access.**

This leads to three separate but connected layers.

### 1. Account
The **account** is the canonical identity in FUZE.

It is the long-term anchor that holds continuity across:

- products
- workspaces
- billing relationships
- wallet links
- audit history
- product settings
- participation context

The account answers the question:

**“Who is this actor in FUZE?”**

### 2. Authentication Method
An **authentication method** is an approved way to prove access to the account.

Examples may include:

- email and password
- Google
- Telegram
- Line
- Facebook
- wallet-based proof if explicitly approved later
- future enterprise or partner identity providers

An authentication method is **not** a separate user identity.  
It is only a verified access path to the canonical account.

### 3. Session
A **session** is the temporary authenticated runtime state created after a successful login flow.

The session answers:

**“This actor has recently proven access, so what may they do right now?”**

A session is not permanent identity truth.  
It is temporary, reviewable, revocable, and policy-controlled.

---

## The Platform Rule FUZE Should Never Break

FUZE should treat the following rule as permanent:

> **Identity belongs to the account. Access belongs to linked login methods. Runtime presence belongs to the session.**

This rule matters because confusion between those three layers creates most platform-auth problems:

- product teams accidentally create separate identities
- login providers get mistaken for the user’s real platform identity
- sessions become overtrusted
- wallet linkage gets mistaken for full identity
- workspace membership gets mistaken for user identity
- support recovery becomes risky and inconsistent

The current FUZE core direction is strong precisely because it avoids those mistakes.

---

## What FUZE Is Trying to Support

FUZE needs an account core that can support **multiple products with different entry styles** while still preserving one platform identity.

That means the system should support patterns like:

- HerHelp may offer Line, Facebook, or Google login
- QTB may offer Telegram and wallet-aware entry paths
- another FUZE product may offer email/password plus Google
- a future enterprise product may use SSO or another federation model

Even when those products feel different at the entry level, FUZE should still resolve them into the same account model.

That is what allows:

- cross-product continuity
- shared support history
- shared workspace relationships
- stable wallet-aware participation context
- future platform expansion without identity fragmentation

---

## The Most Important Design Decision

The single most important design decision is this:

## FUZE should have one canonical account model for the whole platform

Not one account model per product.  
Not one account model per provider.  
Not one account model for Web2 flows and another for Web3 flows.

A person may have many ways to access FUZE.  
But FUZE should still know that they are the same canonical account.

This is one of the strongest protections against long-term platform chaos.

---

## The Correct Order of Platform Access

FUZE should keep the following order clear in every product and every service:

1. authenticate the account
2. establish the session
3. resolve workspace or organization context
4. evaluate role and permission rules
5. evaluate product entitlements or special access rules
6. grant the capability the user is allowed to use

This ordering matters.

It means:

- successful login does not automatically mean full product access
- provider identity does not automatically mean workspace membership
- wallet linkage does not automatically mean platform authorization
- session existence does not automatically mean unrestricted permission

In other words:

> **Authentication is not the same thing as authorization.**

That distinction is critical for FUZE because it is both multi-product and workspace-aware.

---

## The Human Meaning of the Account

From the user and community point of view, the account should feel like:

- **your FUZE identity**
- **your continuity across FUZE products**
- **your stable place in the ecosystem**

That means if you:

- change your preferred login method
- lose access to one provider
- join or leave a workspace
- connect a wallet later
- use more FUZE products over time

you should still feel like:

> **“I am still me in FUZE.”**

That feeling of continuity is not a cosmetic feature.  
It is one of the core promises of a real platform.

---

## What Linked Login Should Mean in FUZE

Linked login should be understood very specifically.

### What It Is
A linked login is a durable relationship between:

- a FUZE account
- and one approved provider-specific access method

### What It Is Not
A linked login is not:

- a second user profile
- a separate account
- a replacement for the canonical account
- permission truth
- workspace truth
- wallet truth

### Why Linked Login Matters
Linked login gives FUZE three major benefits.

#### A. Flexibility
Users can sign in using the method that best fits the product or their preference.

#### B. Resilience
If one provider becomes unavailable or the user loses access to it, another method may preserve continuity.

#### C. Product Adaptability
Different FUZE products can expose different approved providers without breaking the identity model.

---

## Provider Flexibility Without Identity Fragmentation

FUZE should be provider-flexible but identity-strict.

That means FUZE can support different provider types over time, but every provider flow must be normalized into the same platform identity logic.

A healthy way to think about providers is:

### Social Providers
Examples:
- Google
- Facebook
- Line

### Messaging Identity Providers
Examples:
- Telegram

### Wallet-Aware Access Providers
Examples:
- wallet-signature-based proof or wallet-connect-style flows, if explicitly approved later

### Enterprise Providers
Examples:
- SSO
- organization-controlled identity providers

All of those may be different **entry paths**.  
But FUZE should still resolve them to:

- one account
- one durable access model
- one session model
- one policy model for continuity and security

---

## The Strongest Rule for Provider Mapping

FUZE should never rely too heavily on soft profile hints when resolving identity.

The safest pattern is:

- use provider-stable subject identity where available
- store provider-scoped identifiers separately from the canonical account ID
- treat mutable profile fields as supporting data, not canonical identity keys

In plain language:

- email can help
- profile name can help
- avatar can help
- phone can help
- but the stable provider identity should be the real provider key

This is especially important as FUZE grows across different login ecosystems and products.

---

## Why Wallet Must Stay Adjacent, Not Dominant

FUZE has wallet-aware and participation-aware ideas in the ecosystem.  
That is valuable.  
But wallet linkage should remain **attached to the account**, not become the whole identity model.

That matters because:

- not every product needs wallet logic
- one account may have multiple wallets
- wallets may change over time
- chain truth and platform identity are not the same thing
- platform access should not collapse into token ownership alone

So the right public principle is:

> **Wallets enrich account context. They do not replace the account.**

This is one of the clearest boundaries FUZE should continue protecting.

---

## Why Workspace Must Stay Layered Above Identity

FUZE is also workspace-aware.  
That means identity alone is not enough.

A person may:

- own a workspace
- join a workspace
- leave a workspace
- belong to multiple workspaces
- have different roles in different scopes

So the right public principle is:

> **The account is the person layer. The workspace is the collaboration layer.**

That means workspace should be resolved **after** login, not instead of login.

This is exactly why authentication must remain separated from later scope and permission evaluation.

---

## Why Session Deserves Its Own Serious Design

A session is often treated like an implementation detail.  
For FUZE, it should be treated more seriously than that.

A session is the platform’s live runtime promise that:

- the account was authenticated
- the current access is still valid
- the current runtime can be audited, expired, revoked, rotated, or restricted when needed

### A Session Should Never Pretend to Be Permanent Identity
That is the job of the account.

### A Session Should Never Become Invisible Operationally
The platform should be able to:

- inspect it
- list it
- revoke it
- rotate it where supported
- invalidate it after risk events
- trace it during support and security review

### A Session Should Be Subordinate to Account State
If the account becomes:

- restricted
- suspended
- security-reviewed
- recovery-reset

then the session should not continue acting as if nothing happened.

This is one of the most important operational truths in the FUZE access model.

---

## The Continuity Principle

One of the strongest ideas in the FUZE direction is **access continuity**.

That deserves to be made explicit as a public principle.

### What Access Continuity Means
A person should be able to keep reaching the same account even if:

- they switch products
- they change login preference
- they lose one access path
- they add a backup method
- they need support-assisted recovery

### What Continuity Protects
Continuity protects:

- account identity
- product history
- workspace membership
- billing continuity
- credits continuity where account-scoped
- wallet links
- audit continuity
- long-term trust in the platform

### What Continuity Should Block
FUZE should block unsafe actions that would strand a user without a viable access path unless an approved recovery path already exists.

In public language:

> **You should not be able to accidentally lock yourself out of your real FUZE identity.**

---

## Sensitive Actions Should Be Treated Like Security Actions

FUZE should not treat access mutations as ordinary profile edits.

Adding or removing a login method changes the security posture of the account.  
So do:

- password resets
- primary email changes
- global logout
- provider disablement
- recovery completion
- support-led access correction

These should be treated as **sensitive account actions**.

That means they should require some combination of:

- recent authentication or step-up proof
- stronger validation
- audit records
- reason codes where appropriate
- support or security review where appropriate

The public model should make this obvious, because users and builders should understand that access changes are security events, not ordinary cosmetic edits.

---

## The No-Silent-Merge Rule

FUZE should make one promise very clearly:

> **The platform should not silently split, merge, or duplicate your identity when a login conflict happens.**

If a provider sign-in does not map cleanly, the system should enter an explicit resolution path such as:

- create a new account only when clearly valid
- link to an existing account only when safely proven
- enter conflict review when needed
- enter risk review when needed

That is far healthier than hidden behavior that later becomes impossible to support.

The same rule protects both users and operators.

---

## Recommended Platform Invariants

The following invariants should be treated as durable FUZE platform rules.

### Identity Invariant
One canonical `account_id` is the durable person-level identity across the FUZE ecosystem.

### Access-Path Invariant
Linked authentication methods are access paths to the account, not separate identities.

### Session Invariant
Sessions are temporary authenticated runtime state, not canonical identity truth.

### Ordering Invariant
Authentication happens before workspace resolution, role evaluation, entitlement evaluation, and product authorization.

### Product Invariant
Products may extend identity with product-local profiles or settings, but may not redefine canonical platform identity.

### Wallet Invariant
Wallet links attach context to accounts but do not replace platform identity.

### Continuity Invariant
No ordinary self-service action should strand the account without an approved alternative access or recovery path.

### Security Invariant
Sensitive auth and session mutations must remain auditable and policy-controlled.

---

## Suggested Structural Improvements to the Core Direction

The current FUZE direction is already strong.  
The best next move is not a redesign.  
It is refinement.

### Clarify the Account / Access / Session Boundary Even More
FUZE should keep repeating the conceptual separation:

- account = identity truth
- auth method = access truth
- session = runtime truth

### Make Continuity a First-Class Design Concept
Access continuity should not only exist as implied logic. It should be named, testable, and visible.

### Normalize Provider Completion Into One Canonical Backend Resolution Model
Different providers may have different protocols, but the backend should resolve them through one shared outcome model.

### Make Restricted and Suspended States More Operationally Explicit for Session Behavior
Products and support tools should know exactly what those states mean for current and future sessions.

### Keep Frontends as Consumers, Not Owners, of Identity Truth
Frontends should initiate flows and present results, but canonical auth, account, and recovery truth should stay backend-owned.

---

## Suggested Data-Model Direction for the Deeper Core Docs

The deeper implementation specs can refine exact schemas later, but the public thesis should signal several important design ideas.

### Canonical Account Entity
FUZE should keep a durable account entity that owns platform identity.

### Durable Auth-Method Entity
FUZE should keep a durable auth-method or linked-login entity that stores provider-specific access bindings.

### Durable Session Entity
FUZE should keep a durable session record that supports security review, revocation, observability, and support workflows.

### Continuity View or Access Summary
FUZE should expose a safe derived view that explains whether the account has resilient access posture.

### Explicit Conflict-Case Handling
Provider conflicts, duplicate-account risk, or risky access correction should be represented explicitly rather than hidden in ad hoc code paths.

---

## Suggested API Direction

At the public thesis level, the most important API idea is clear ownership.

### Identity-Facing APIs
These should answer:

- who is this account
- what durable access methods exist
- what is the account’s identity posture

### Session-Facing APIs
These should answer:

- is the actor currently authenticated
- what sessions exist
- what can be revoked or refreshed
- what happened in this auth runtime

### Access-Control APIs
These should answer:

- what is the current scope
- what roles apply
- what permissions are effective
- what product capability is allowed

That keeps the API story aligned with the deeper core library and avoids mixing unrelated ownership domains into one surface.

---

## The Community-Facing Explanation in One Sentence

If FUZE had to explain the whole model in one sentence, it should be this:

> **FUZE gives each person one real platform account, allows multiple approved ways to access it, and then issues controlled sessions so that products, workspaces, and permissions can be resolved safely on top.**

---

## Recommended Public Design Principles

These principles are suitable for community-facing explanation.

### Principle 1 — One Person, One Canonical FUZE Account
FUZE should preserve one durable identity anchor across the ecosystem.

### Principle 2 — Many Access Methods, One Identity
Different login methods may be supported, but they should resolve to the same account model.

### Principle 3 — Session Is Temporary and Reviewable
A session exists to support live access, not to replace identity truth.

### Principle 4 — Authentication Is Not Authorization
Login proves access. Permissions still depend on workspace, role, scope, entitlement, and product rules.

### Principle 5 — Wallet-Aware Does Not Mean Wallet-Only
Wallets enrich account context. They do not replace the platform account model.

### Principle 6 — Products Should Share the Same Identity Foundation
Products may have different entry paths but should not invent separate identity systems.

### Principle 7 — Account Continuity Matters
A user should keep their real FUZE identity even as products, providers, and access preferences change.

### Principle 8 — Sensitive Access Changes Must Be Explicit and Auditable
Security-relevant account changes should never be casual or invisible.

---

## What This Means for FUZE Products

For product builders inside FUZE, this thesis implies a clear responsibility model.

### Products Should Do
- consume canonical account identity
- consume session truth from shared platform services
- resolve scope and permissions through shared access-control layers
- attach product-local profile or settings as child extensions
- support product-appropriate login UX on top of shared core rules

### Products Should Not Do
- invent product-local canonical user IDs for the same actor
- treat provider identity as sufficient platform identity
- bypass workspace and access-control rules
- treat wallet address alone as the universal identity model
- keep auth/session truth only in frontend state

Products may innovate at the experience layer.  
They should not fork the identity foundation.

---

## Relationship to Current and Future FUZE Documents

This thesis document is intended to sit above the detailed core docs and connect them in a more public, human, and platform-explanatory way.

### Current Core References
This thesis aligns most directly with:

- `IDENTITY_AND_ACCOUNT_SPEC.md`
- `AUTH_SESSION_AND_LINKED_LOGIN_SPEC.md`
- `SESSION_AND_LINKED_LOGIN_API_SPEC.md`
- `WORKSPACE_AND_ORGANIZATION_SPEC.md`
- `ROLE_PERMISSION_AND_ACCESS_CONTROL_SPEC.md`
- `WALLET_AWARE_USER_SPEC.md`
- `AUTH_IDENTITY_API_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- `SYSTEM_SPEC_INDEX.md`
- `API_SPEC_INDEX.md`
- `DOCS_SPEC_INDEX.md`

### Future Core Deep Dives
This thesis can also point cleanly to deeper future or adjacent core documents such as:

- `FUZE_ACCOUNT_ACCESS_CONTINUITY_SPEC.md`
- `FUZE_PROVIDER_RESOLUTION_AND_LINKING_SPEC.md`
- `FUZE_SESSION_LIFECYCLE_AND_SECURITY_SPEC.md`
- `FUZE_ACCOUNT_RECOVERY_AND_CONFLICT_HANDLING_SPEC.md`

Those deeper documents can carry lifecycle charts, exact rules, schemas, and implementation-level contracts.

---

## Final Position

The current FUZE account / access / session direction is already correct in its fundamentals.

Its strongest qualities are:

- one canonical account
- multiple linked access methods
- sessions treated as temporary runtime state
- workspace and authorization layered after authentication
- wallet-aware participation kept adjacent to identity rather than replacing it
- backend-owned truth for core identity and session logic

The best next step is not to simplify this model.  
The best next step is to make it clearer, stricter, and easier to communicate.

That is the purpose of this thesis.

---

## Final Thesis Statement

> **FUZE should remain a platform where one canonical account anchors identity, many approved login methods can reach that identity, and controlled sessions make secure multi-product access possible without fragmenting the user across providers, products, workspaces, or wallets.**

---

## End of Document

**Document Name:** `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
