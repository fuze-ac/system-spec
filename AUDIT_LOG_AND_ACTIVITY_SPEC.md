# FUZE Audit Log and Activity Specification

## Document Metadata

- **Document Name:** `AUDIT_LOG_AND_ACTIVITY_SPEC.md`
- **Document Type:** Canonical refined system specification
- **Status:** Active refined system spec
- **Version:** 2.0.0
- **Effective Date:** 2026-04-22
- **Last Updated:** 2026-04-22
- **Reviewed On:** 2026-04-22
- **Document Owner:** FUZE Platform Audit and Activity Governance Domain (canonical owner for shared audit-record, activity-history, evidence-access, and derived activity-surface posture); named individual owner is not explicitly specified in the retrieved governing materials
- **Approval Authority:** Not explicitly specified in the retrieved governing materials; constitutional approval authority remains governed by `REFINED_SYSTEM_SPEC_INDEX.md` and the active FUZE approval workflow
- **Review Cadence:** Not explicitly specified in the retrieved governing materials; SHOULD be reviewed whenever platform-wide audit posture, activity-history semantics, privileged operator controls, evidence access rules, event/publication/reporting boundaries, retention posture, or cross-domain traceability obligations materially change
- **Governing Layer:** Platform core / shared audit, evidence, and activity-history governance
- **Parent Registry:** `REFINED_SYSTEM_SPEC_INDEX.md`
- **Primary Audience:** Platform architecture, backend engineering, security engineering, support operations, governance/control-plane operators, trust and safety, audit/compliance, data engineering, product engineering, API and contract authors, reliability engineering, implementation-contract authors
- **Primary Purpose:** Define the canonical FUZE audit-log and activity domain that governs immutable audit records, user-facing and operator-facing activity-history semantics, evidence-access posture, derived activity surfaces, and downstream implementation guardrails without collapsing audit truth into business-domain truth, access-trace truth, event truth, queue truth, reporting truth, analytics truth, or UI presentation
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
  - `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
  - `PRODUCT_ADMISSION_AND_EXPANSION_GATE_SPEC.md`
  - `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`
  - `API_ARCHITECTURE_SPEC.md`
  - `PUBLIC_API_SPEC.md`
  - `INTERNAL_SERVICE_API_SPEC.md`
  - `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
  - `IDEMPOTENCY_AND_VERSIONING_SPEC.md`
  - `MIGRATION_AND_BACKWARD_COMPATIBILITY_SPEC.md`
  - `WORKFLOW_AND_AUTOMATION_SPEC.md`
  - `JOB_QUEUE_AND_WORKER_SPEC.md`
  - `AI_ORCHESTRATION_SPEC.md`
  - `MODEL_ROUTING_AND_CONTEXT_SPEC.md`
  - `AI_USAGE_METERING_SPEC.md`
  - `FEATURE_FLAG_AND_ROLLOUT_CONTROL_SPEC.md`
  - `SECURITY_AND_RISK_CONTROL_SPEC.md`
  - `SECRETS_CONFIG_AND_ENVIRONMENT_SPEC.md`
  - `MONITORING_ALERTING_AND_INCIDENT_RESPONSE_SPEC.md`
  - `DATA_CLASSIFICATION_AND_HANDLING_SPEC.md`
  - `DATA_RETENTION_DELETION_AND_ARCHIVAL_SPEC.md`
  - `FILE_OBJECT_AND_ARTIFACT_STORAGE_SPEC.md`
  - `SEARCH_INDEXING_AND_DISCOVERY_SPEC.md`
  - `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_THESIS_FINAL_SPEC.md`
  - `FUZE_ACCOUNT_ACCESS_AND_SESSION_CANONICAL_FINAL_SPEC.md`
  - `FUZE_WORKSPACE_ACCESS_CONTROL_BASICS_THESIS_FINAL_SPEC.md`
- **Primary Downstream Dependents:**
  - product activity-history surfaces
  - support and control-plane audit consoles
  - compliance and governance review workflows
  - incident-response evidence workflows
  - domain API specifications
  - public/internal/admin history and export contracts
  - reporting, analytics, and evidence-export pipelines
  - notification, transparency, and user-communication contracts where activity summaries are exposed
- **Supersedes:** Earlier or weaker interpretations that treat audit as generic logs, activity feeds as sufficient evidence, event streams as substitutes for audit records, reports and dashboards as canonical history, product-local telemetry as platform-wide evidence, or operator remediation as allowable without explicit reason-coded audit lineage
- **Superseded By:** None currently defined
- **Related Decision Records:** Not explicitly linked in the retrieved governing materials
- **Canonical Status Note:** This document is the canonical governing FUZE specification for audit-log and activity semantics. Downstream APIs, products, services, workflows, events, operator tooling, reports, exports, and evidence stores MUST preserve the ownership, truth separation, access control, durability, redaction, and lineage rules defined here.
- **Implementation Status:** Normative source; downstream services, evidence stores, history APIs, export tools, dashboards, notifications, and control-plane workflows MUST align
- **Approval Status:** Draft refined canonical specification pending explicit approval workflow
- **Change Summary:** Refined the audit/activity domain into a production-grade shared platform specification aligned with the April 2026 refined API, event, migration, data-governance, and access-trace stack; clarified the distinction among canonical business truth, audit truth, access-trace truth, activity-history truth, reporting truth, and presentation truth; strengthened operator controls, evidence minimization, redaction posture, lineage, supersession, retention, export, and implementation-contract guardrails

## Title

FUZE Audit Log and Activity Specification

## Purpose

This specification defines the canonical audit-log and activity domain of FUZE.

Its purpose is to make explicit:

- what the audit-log and activity domain governs and what it does not govern
- how FUZE preserves immutable, reviewable evidence for meaningful actions, state changes, and privileged operations across the platform
- how FUZE distinguishes audit evidence from business-domain truth, access-trace evidence, event records, workflow records, delivery records, analytics outputs, and UI history
- how user-facing history, operator-facing history, and governance/compliance evidence views are derived from canonical evidence without becoming new truth owners
- how evidence visibility, redaction, minimization, reason codes, policy references, and supersession lineage must work
- how downstream APIs, products, exports, notifications, reports, and support tooling MUST consume audit and activity semantics without inventing contradictory local interpretations

This specification is governing rather than descriptive. It does not merely list log tables, history pages, or event names. It defines the durable FUZE platform posture for evidentiary recording, activity history, and audit-safe explanation.

## Scope

This specification governs:

- canonical FUZE audit record classes for meaningful mutations, accepted async intents, privileged reads where required, security- and governance-sensitive decisions, evidence-access actions, and operator remediations
- platform-wide activity-history semantics for user-visible and operator-visible timelines derived from canonical records
- the distinction among canonical audit records, narrower access-trace records, business-domain records, event records, workflow/job records, and derived reports
- evidence immutability, append-only correction posture, and supersession lineage requirements
- evidence visibility classes, redaction posture, evidence-access authorization, and minimization rules
- reason-code, policy-reference, actor-attribution, target-attribution, scope, causation, and correlation requirements for meaningful records
- export, disclosure, reconciliation, reporting, retention, archival, and deletion implications for audit records and activity projections
- implementation-contract guardrails for downstream APIs, products, notifications, support tools, and evidence-consuming systems

## Out of Scope

This specification does not define:

- the full source-of-truth lifecycle for each business domain
- the exact low-level database engine, table partitioning, or warehouse topology
- every field of every product-local history card or activity UI
- every SIEM, log-shipping, or observability vendor implementation
- the full semantics of access-trace reconstruction, which remain governed by `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- the exact legal or jurisdiction-specific retention schedules for every evidence category
- exact notification copy, presentation text, or support playbook steps

Those concerns belong in adjacent specifications and downstream implementation documents, provided they remain consistent with this document.

## Design Goals

The design goals of the FUZE audit-log and activity domain are to:

1. preserve immutable, attributable evidence for meaningful and review-relevant platform actions
2. keep audit truth clearly separate from business truth, event truth, trace truth, workflow truth, and reporting truth
3. support trustworthy support, incident response, governance, security, and dispute investigation
4. provide user-facing and operator-facing activity history without letting those surfaces become hidden sources of truth
5. ensure privileged and sensitive actions are bounded, reason-coded, and reviewable
6. enable evidence export, retention, redaction, and deletion posture without destroying required lineage
7. preserve platform-first consistency across products and domains
8. provide a strong source document for downstream implementation-contract work

## Non-Goals

This specification is not intended to:

- make the audit domain the write owner of every business fact it records
- turn all telemetry, metrics, debug logs, or product analytics into audit evidence
- make every user-visible timeline entry an immutable audit record
- guarantee identical visibility of every audit record to every viewer
- allow dashboards, reports, activity feeds, or notifications to substitute for canonical evidence
- allow operator convenience to bypass evidence creation, reason coding, or approval lineage
- allow correction to silently overwrite history

If there is tension between convenience and evidentiary integrity, the evidentiary-integrity interpretation wins.

## Core Principles

### 1. Audit-Is-Evidence Principle
Audit records are platform evidence about meaningful actions and outcomes. They describe and preserve what happened; they do not become the semantic owner of every underlying domain fact.

### 2. Activity-Is-Derived Principle
Activity feeds, history cards, status timelines, notifications, and operator summaries are downstream derived surfaces. They MUST remain subordinate to canonical audit records and canonical owner-domain truth.

### 3. Append-Only Correction Principle
Canonical audit records are immutable after acceptance. Correction, redaction, suppression, or policy reinterpretation requires new linked records or protected overlays, not destructive rewrite.

### 4. Attribution-and-Scope Principle
Meaningful audit records MUST preserve actor attribution, actor class, execution context, materially relevant scope, affected target, and enough causality to support review.

### 5. Privileged-Action Visibility Principle
Admin, support, governance, risk, finance, security, and other privileged actions require stronger audit requirements than routine end-user interactions.

### 6. Least-Disclosure Evidence Principle
Canonical evidence may contain more detail than user-visible activity surfaces. Each derived audience receives only the minimum permitted evidence necessary for its purpose.

### 7. Platform-First Consistency Principle
Products may add approved local detail, but they MUST consume the shared FUZE audit and activity model for any action that materially affects platform trust, access, money, governance, user status, or durable business outcomes.

### 8. Evidence-Access-Is-Itself-Auditable Principle
Access to sensitive evidence, exports, redactions, holds, restores, and privileged disclosures MUST themselves create bounded audit records.

### 9. Derived-Surfaces-Never-Outrank Principle
Reports, dashboards, notifications, search indexes, and history timelines MUST NOT outrank canonical evidence when they disagree.

### 10. Future-Safe Governance Principle
The audit domain MUST remain strong enough to support future compliance, transparency, disputes, security review, and implementation-contract work without changing the underlying semantic model.

## Canonical Definitions

### Audit Record
A durable evidence artifact describing a meaningful platform action, state transition, review action, privileged access, policy outcome, export, disclosure, or remediation.

### Activity Record
A derived representation of one or more canonical records optimized for user-facing or operator-facing history and explanation.

### Canonical Evidence Store
The protected durable store, or coordinated set of stores, used to retain canonical audit records and their integrity-protecting metadata.

### Derived Activity View
A queryable or rendered projection of canonical evidence suitable for product history, support timelines, or explanation surfaces. It is not canonical evidence.

### Evidence Visibility Class
A classification describing who may see which parts of a record, at what fidelity, and under which policy.

### Evidence Overlay
A bounded, policy-governed derivative attached to a canonical record to support redaction, disclosure shaping, or audience-specific explanation without mutating the underlying evidence.

### Policy Reference
A stable reference to a rule, policy module, control family, or governing specification materially relevant to the recorded outcome.

### Reason Code
A machine-usable code representing why a meaningful action, restriction, override, escalation, export, or suppression occurred.

### Supersession Link
A durable linkage showing that a later record corrects, narrows, cancels, replaces, or otherwise governs interpretation of an earlier record.

### Evidence Access Event
A canonical audit record showing that a protected evidence object, evidence export, or high-sensitivity timeline entry was viewed, exported, disclosed, or administratively acted upon.

## Truth Class Taxonomy

This specification MUST preserve the following truth classes:

1. **Semantic truth** — what a domain action or outcome means and which domain owns that meaning
2. **Audit truth** — immutable evidence that a meaningful action, review step, or disclosure event occurred
3. **Access-trace truth** — the narrower reconstruction lineage for access-domain evaluations and mutations governed by `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
4. **Policy truth** — rules for visibility, retention, export, disclosure, redaction, and privileged evidence handling
5. **Runtime truth** — current workflow, job, request, delivery, or operational status at execution time
6. **Ledger / storage truth** — durable owner-domain records, durable audit records, retention state, export records, and redaction overlays
7. **Provider-input truth** — external inputs, callback data, chain observations, or receiver responses before owner interpretation
8. **Projection / reporting truth** — dashboards, activity feeds, analytics, notifications, support summaries, and compliance packages derived from canonical records
9. **Presentation truth** — user-visible wording, timeline labels, notification copy, and UI composition choices

These truth classes MUST remain distinct. Audit truth does not absorb owner-domain truth, access-trace truth, workflow truth, queue truth, or reporting truth.

## Architectural Position in the Spec Hierarchy

This document sits below:

- `REFINED_SYSTEM_SPEC_INDEX.md`
- `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`
- `SYSTEM_OVERVIEW_AND_BOUNDARIES_SPEC.md`
- `PLATFORM_ARCHITECTURE_SPEC.md`
- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `DATA_MODEL_AND_ENTITY_OWNERSHIP_SPEC.md`
- `PRODUCT_BOUNDARY_AND_DOMAIN_OWNERSHIP_SPEC.md`
- `ONCHAIN_OFFCHAIN_RESPONSIBILITY_SPEC.md`

and above or alongside:

- `API_ARCHITECTURE_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `JOB_QUEUE_AND_WORKER_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `DATA_CLASSIFICATION_AND_HANDLING_SPEC.md`
- `DATA_RETENTION_DELETION_AND_ARCHIVAL_SPEC.md`
- product history specifications
- evidence export contracts
- support and governance console contracts
- analytics and reporting derivation layers

This document governs platform-wide audit and activity semantics. It does not replace the adjacent specifications listed above.

## System Boundaries

The audit-log and activity domain spans multiple FUZE planes but does not own every semantic domain it records.

It MUST be interpreted as follows:

- the **experience / edge layer** may display timelines, notifications, and explanation surfaces, but it does not own canonical evidence
- the **application plane** owns most canonical business mutations whose results become audit-worthy
- the **execution plane** produces operational records and accepted/completion lineage that may be audited, but workflow and queue mechanics do not become audit meaning
- the **integration plane** may emit connector, callback, export, and disclosure evidence, but provider callbacks do not become audit truth until normalized and accepted
- the **control plane** governs privileged overrides, investigations, holds, exports, redactions, and disclosures under stronger audit posture
- the **reporting plane** may derive reports and history summaries from canonical evidence without becoming the owner of that evidence
- the **on-chain layer** remains separately authoritative for chain-native state; audit records may refer to chain interactions without redefining contract-native truth

## Adjacent Boundaries

This specification interacts with adjacent domains as follows:

- **Audit and Access Traceability** governs the narrower cross-domain evidence required to reconstruct access-related actions and decisions. This document governs the broader audit and activity domain that consumes that trace layer and combines it with other meaningful evidence classes.
- **API Architecture** governs surface-family posture and accepted-state semantics. This document governs when API actions, accepted async intents, privileged reads, and control actions must become canonical audit records.
- **Public API** governs external exposure posture. This document governs what audit- or activity-derived public or partner-visible disclosures are allowed and what MUST remain internal.
- **Internal Service API** governs service-to-service contracts. This document governs the evidence that meaningful internal actions and privileged service behavior must preserve.
- **Event Model and Webhook** governs event and webhook semantics. Event records may correlate to audit records, but event delivery, replay, or consumer state MUST NOT substitute for audit truth.
- **Workflow and Automation** governs workflow meaning. Workflow progression may emit auditable outcomes, but workflow state is not automatically equivalent to audit evidence.
- **Job Queue and Worker** governs retry, claim, lease, timeout, and execution substrate semantics. Those records may be inputs to audit history but do not replace audit meaning.
- **Security and Risk Control** governs cross-cutting restrictions, abuse posture, incident handling, and sensitive-path hardening. This document requires those actions and overrides to be auditable and reason-coded.
- **Data Classification and Handling** governs classification and handling posture. This document requires evidence visibility, export, redaction, and activity-surface derivations to preserve classification obligations.
- **Data Retention, Deletion, and Archival** governs lifecycle posture. This document governs which audit records remain canonical evidence and how deletion or redaction must preserve allowed lineage.
- **File, Object, and Artifact Storage** governs stored exports and evidence artifacts. This document governs their audit meaning, not their storage mechanics.
- **Search, Indexing, and Discovery** governs evidence discoverability and snippet posture. Search may help locate audit records but MUST NOT become the canonical evidence store.
- **Monitoring, Alerting, and Incident Response** governs operational detection and response. Monitoring outputs may trigger investigations, but alerts do not by themselves become sufficient audit evidence.
- **Notification and User Communication** may expose history-derived messaging, but notifications are presentational derivatives and MUST NOT become hidden canonical history.

## Conflict Resolution Rules

When materials, implementations, or interpretations conflict, the following rules apply:

1. the active refined registry and higher constitutional materials win over narrower documents
2. `PLATFORM_ARCHITECTURE_SPEC.md`, `SYSTEM_BOUNDARY_AND_OWNERSHIP_SPEC.md`, and `DOMAIN_OWNERSHIP_MATRIX_SPEC.md` win on platform-plane roles, owner-domain interpretation, and mutation boundaries
3. this document wins on audit-record classes, activity-history semantics, evidence visibility posture, export and redaction semantics, and derived-activity constraints
4. `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md` wins on the narrower requirements for access-domain evidence and reconstruction lineage within its scope
5. `API_ARCHITECTURE_SPEC.md`, `PUBLIC_API_SPEC.md`, and `INTERNAL_SERVICE_API_SPEC.md` win on interface-family posture outside this document’s narrower audit/activity scope
6. `EVENT_MODEL_AND_WEBHOOK_SPEC.md` wins on event and webhook meaning, replay, redelivery, and public webhook projection posture outside this document’s narrower audit/activity scope
7. owner-domain specifications win on the semantic meaning of business facts even when those facts are recorded in audit history
8. dashboards, reports, feeds, notifications, exports, search indexes, and UI history never win over canonical evidence or owner-domain truth
9. when ambiguity remains, FUZE MUST choose the more conservative architecture-consistent interpretation and escalate unresolved ambiguity into downstream refinement or recorded decision work

## Default Decision Rules

When ambiguity exists and no narrower approved exception is available, FUZE MUST default to the following:

1. meaningful mutations default to requiring canonical audit records
2. privileged operator and control-plane actions default to stronger audit posture than ordinary end-user actions
3. activity-history surfaces default to derived status, not canonical truth
4. user-visible history defaults to redacted/minimized explanation rather than full internal evidence disclosure
5. evidence correction defaults to superseding or overlaying prior records, not mutating historical records in place
6. evidence export defaults to reason-coded, access-controlled, and itself audited
7. unresolved disagreement between a report/feed and canonical evidence defaults to the canonical evidence
8. if a surface cannot name the canonical source audit record, its audience, and its visibility policy, it is incomplete and MUST NOT proceed as production-grade

## Roles / Actors / Entities

### Human Actors

- end users
- workspace owners and administrators
- support operators
- security and risk reviewers
- governance or approval actors
- finance/control-plane operators
- compliance or audit reviewers

### System Actors

- owner-domain services
- internal services and gateways
- workflow engines
- workers and schedulers
- integration adapters
- event stores and dispatchers
- evidence stores and export services
- search and reporting systems
- notification and communication systems
- support and control-plane tooling

### Core Entity Families

- audit records
- activity records
- evidence overlays
- reason-code catalogs
- policy references
- correlation and causation references
- evidence export requests
- export packages and disclosure artifacts
- redaction actions
- evidence-access events
- retention and hold references
- timeline aggregation entries

## Ownership Model

### The Audit Log and Activity Domain Owns

- shared audit-record classes and minimum evidentiary requirements
- shared activity-history semantics and derived-surface rules
- evidence visibility classes and audience-safe derivation posture
- append-only correction, redaction-overlay, and supersession semantics
- evidence export and disclosure semantics at architecture level
- architecture-level implementation guardrails for history APIs, exports, and activity feeds
- the distinction between canonical evidence and derived activity surfaces

### The Audit Log and Activity Domain Does Not Own

- business meaning of every domain mutation
- access-domain evaluation semantics or trace reconstruction specifics beyond what this domain consumes
- workflow or queue mechanics
- event meaning or webhook delivery state
- analytics meaning
- notification wording or product presentation design
- chain-native truth

### Owner Domains MUST

- emit or enable creation of canonical audit records for meaningful actions they own
- provide stable identifiers, actor/scope references, reason context where required, and policy references where material
- avoid hiding privileged or sensitive mutations behind local logs only
- preserve explicit lineage when correction, override, or containment changes interpretation

### Non-Owners MAY

- derive activity surfaces, user timelines, exports, dashboards, or support summaries from canonical evidence where authorized
- aggregate multiple canonical records into one activity entry when lineage remains reconstructable
- expose bounded explanations appropriate to audience and policy

### Non-Owners MUST NOT

- rewrite or reinterpret audit history without canonical lineage
- treat local telemetry, reports, or notifications as sufficient evidence
- omit reason codes, actor attribution, or approval lineage for privileged actions
- bypass owner-domain or control-plane policy by publishing ungoverned history views

## Authority / Decision Model

### Shared Audit Governance Authority
Has final authority over shared audit-record classes, evidence visibility posture, derived-activity semantics, export posture, and implementation-contract guardrails for audit and activity surfaces.

### Domain Authority
Each owner domain has final authority over the meaning and lifecycle of the underlying business facts recorded in audit evidence.

### Control-Plane Authority
Control-plane actors may approve, export, redact, suppress, disclose, or otherwise remediate evidence surfaces under stronger policy and audit posture. They do not become owners of the underlying business fact.

### Viewer Authority
Each viewer class receives only the evidence fidelity permitted by policy, scope, classification, and authorization. The right to see an activity summary does not imply the right to view full protected evidence.

## State Model

### Audit Record States
At architecture level, the audit domain MUST preserve semantic distinction among:

- `recorded`
- `confirmed`
- `superseded`
- `overlay_applied`
- `exported`
- `accessed_sensitive`
- `retention_locked`
- `archived`

### Activity Record States
- `materialized`
- `updated_from_source`
- `suppressed_from_audience`
- `expired_from_view`

### Export and Disclosure States
- `requested`
- `approved`
- `generated`
- `delivered`
- `revoked`
- `expired`

### State Rules

- canonical audit records are immutable after `recorded`
- `superseded` does not erase the original record
- `overlay_applied` modifies audience-visible interpretation, not the underlying record identity
- export state is distinct from audit-record state
- activity-record state is distinct from the state of the underlying canonical evidence

## Lifecycle / Workflow Model

### 1. Action Acceptance or Observation
A meaningful action, decision, review step, disclosure action, or control-plane intervention occurs or is durably accepted.

Required posture:
- actor attribution MUST be captured or explicitly marked as service/system context
- materially relevant scope and target MUST be reconstructable
- reason codes and policy references MUST be attached where required

### 2. Canonical Audit Recording
The platform records canonical evidence.

Required posture:
- record identity, causation, and correlation MUST be durable
- canonical records MUST be immutable after acceptance
- if audit creation is mandatory for the action class, absence of such a record is a platform defect

### 3. Derived Activity Materialization
Authorized history surfaces derive user-facing or operator-facing activity entries.

Required posture:
- every activity entry MUST remain traceable to canonical record(s)
- audience-specific minimization/redaction MUST be policy governed
- activity views MUST NOT invent contradictory semantics

### 4. Access, Export, Redaction, and Disclosure
Protected evidence may be viewed, exported, redacted, or disclosed through bounded controls.

Required posture:
- these actions MUST themselves be auditable
- sensitive exports MUST be reason-coded and access-controlled
- disclosure posture MUST preserve classification and retention obligations

### 5. Correction, Suppression, or Supersession
Later review may determine that a prior record needs clarified interpretation, reduced audience visibility, or superseding explanation.

Required posture:
- original canonical evidence MUST remain intact
- later records or overlays MUST explicitly link back to the prior record
- public or user-facing activity may be narrowed without destroying internal evidence lineage

### 6. Retention, Archival, and Lifecycle End
Records age according to lifecycle policy.

Required posture:
- archival does not erase canonical historical meaning
- deletion or minimization MUST respect holds, evidence requirements, and cross-spec lifecycle policy
- derived views MUST eventually reflect allowed lifecycle changes

## Invariants

1. Canonical audit records are immutable after acceptance.
2. Derived activity surfaces are never canonical evidence.
3. Meaningful privileged actions require stronger audit posture than ordinary actions.
4. Evidence access, export, and disclosure are themselves auditable.
5. Canonical evidence and activity derivations must remain linked.
6. Reports, dashboards, search indexes, and notifications never outrank canonical evidence.
7. Redaction or suppression for one audience does not silently erase protected internal evidence unless a narrower lifecycle rule explicitly requires it.
8. Absence of required evidence for a high-impact action is a platform defect and MUST be detectable.
9. Supersession preserves history rather than flattening it.
10. Products must not invent incompatible activity-history semantics for shared platform actions.

## Functional Rules

### Rule 1: What Requires Canonical Audit Recording
The following classes generally require canonical audit recording:

- canonical mutations of meaningful business state
- accepted async intents for meaningful deferred work
- privileged reads where policy requires access evidence
- support, risk, finance, governance, or security control actions
- restriction, override, remediation, containment, and approval actions
- evidence exports, disclosures, redactions, suppressions, and hold applications
- public-trust, money-moving, entitlement-affecting, and access-affecting actions where downstream review matters

### Rule 2: What May Remain Non-Audit Telemetry
Debug logs, transient metrics, low-value product analytics, and local UI interaction traces MAY remain non-audit telemetry unless separately promoted into auditable scope.

### Rule 3: Activity History Must Be Derived
User-visible history and operator-visible activity surfaces MUST be derived from canonical evidence or owner-domain truth through governed mappings. They MUST NOT rely solely on free-form logs or notifications.

### Rule 4: Aggregation Is Allowed but Must Preserve Lineage
Multiple canonical records MAY be compressed into one user-facing history card or operator summary only if the derived entry preserves traceability back to its source records.

### Rule 5: Protected Evidence Must Support Redacted Views
The system MAY expose redacted or minimized activity explanations to less-privileged audiences. Such redaction MUST be policy-governed and MUST NOT mutate the protected canonical record.

### Rule 6: Export and Disclosure Controls
Evidence export, disclosure, and regulator/compliance packaging MUST be access-controlled, reason-coded, bounded by classification and retention policy, and themselves audited.

### Rule 7: Operator Overrides
Operator-administered hide, annotate, export, reopen, suppress, or correction actions MUST be policy-constrained, reason-coded, attributable, and reviewable.

### Rule 8: Cross-Domain Correlation
Canonical audit records MUST support correlation to relevant owner-domain identifiers, event identifiers where applicable, workflow/job references where applicable, and access-trace references where applicable.

## Permission / Access Considerations

- access to full protected evidence MUST require explicit authorization aligned to role, scope, and sensitivity
- the right to act in a domain does not automatically grant the right to inspect full audit detail for that domain
- user-facing history SHOULD expose only what is appropriate to the subject user and scope
- support and governance tooling MAY expose broader evidence views, but only under bounded audit and least-privilege controls
- evidence exports and bulk reads require stronger scrutiny than single-record view access

## Entitlement Considerations

- entitlement to a product or feature does not by itself authorize full audit-evidence access
- product capability gating MAY influence which activity surfaces a user can see, but MUST NOT reduce canonical evidence creation
- internal premium or admin tooling capabilities MUST NOT bypass evidence-access controls

## API / Contract Implications

- downstream APIs that expose activity history MUST identify their canonical source classes and visibility policy
- APIs MUST distinguish between canonical audit exports and derived activity reads
- accepted async responses for meaningful actions SHOULD yield evidence lineage that can later be surfaced through history APIs
- admin/control APIs for export, redact, suppress, annotate, or disclose actions MUST be separate from ordinary activity-read APIs where risk posture requires it
- public APIs MUST NOT expose protected audit detail unless explicitly approved and minimized under public-contract governance

## Event / Async Implications

- event publication MAY trigger activity materialization, but event records do not substitute for audit records
- workflow or job completion MAY create audit evidence when the outcome is materially meaningful
- retries and redeliveries MUST NOT create false duplicates in canonical activity meaning; duplicate technical observations must be normalized or explicitly classified
- replay MAY re-materialize derived views but MUST preserve linkage to the original canonical record identity

## Data Model / Storage Implications

At minimum, downstream data models should preserve:

- canonical audit record identity
- actor identity or actor class
- target identity
- materially relevant scope
- action or outcome class
- reason codes and policy references where applicable
- causation and correlation identifiers
- sensitivity / visibility classification
- supersession or overlay linkage
- export/disclosure lineage where applicable
- retention and hold references where applicable

The implementation MAY split canonical evidence and derived activity materializations across different stores, but canonical-vs-derived distinction MUST remain explicit.

## Read Model / Projection / Reporting Rules

- activity feeds, dashboards, reports, search indexes, and notifications are all derived read models
- derived read models MAY optimize for audience, latency, or convenience, but MUST NOT become hidden sources of truth
- if a derived view cannot trace back to canonical evidence or canonical owner-domain truth, it is non-conforming
- reports MUST clearly indicate when they are summaries, projections, or disclosures rather than canonical evidence
- evidence overlays used for audience shaping MUST be policy-governed and lineage-preserving

## Security / Risk / Abuse Controls

- canonical evidence stores and export paths MUST be protected against unauthorized read, write, deletion, and silent tampering
- evidence-access operations MUST be logged
- bulk export, bulk search, and cross-scope evidence review require stronger controls than ordinary scoped reads
- support and operator tooling MUST enforce reason codes and case linkage where policy requires it
- abuse or incident controls MAY temporarily narrow evidence visibility, but such narrowing MUST itself be auditable

## Boundary Violation Detection / Non-Canonical Patterns

The following are explicitly non-canonical and forbidden:

- treating product analytics or debug logs as sufficient audit evidence for meaningful actions
- treating events, webhooks, workflow state, or queue state as canonical audit truth
- allowing user-visible notifications or timeline cards to exist without traceable source lineage
- silently deleting or rewriting canonical evidence in place
- allowing privileged operators to suppress or disclose evidence without reason codes and audit lineage
- letting search indexes, dashboards, or reports become the only remaining copy of meaningful history
- storing protected evidence in presentation caches with no canonical reference

## Audit / Traceability Requirements

- meaningful records MUST preserve actor, target, scope, action class, timestamp, and lineage fields
- privileged actions MUST preserve stronger reason and approval lineage
- evidence-access events MUST be recorded for sensitive evidence classes
- derived surfaces MUST retain source references sufficient for investigation and export
- canonical audit records SHOULD correlate to access-trace identifiers, event identifiers, workflow references, and owner-domain record identifiers when relevant

## Failure Handling / Edge Cases

- if an action class requires canonical audit creation and evidence recording fails, the platform MUST treat the outcome as degraded and MUST NOT silently report success without recovery posture
- if derived activity materialization fails, canonical evidence remains source of truth and remediation MUST be possible without changing evidence meaning
- duplicate technical emissions MUST be normalized or explicitly classified so user-visible activity does not falsely duplicate business meaning
- if visibility policy changes after record creation, audience views MAY change via overlays or rematerialization, but historical evidence lineage MUST remain intact
- if retention or deletion policy conflicts with a hold, the hold wins until formally released
- if a product-local history conflicts with canonical evidence, the canonical evidence wins and the product-local history is defective

## Operational Considerations

- canonical evidence creation SHOULD be treated as a critical supporting obligation for meaningful platform actions
- derived history pipelines SHOULD be observable, replayable, and repairable without changing canonical meaning
- operators SHOULD have tooling to inspect evidence lineage, supersession chains, export history, and visibility overlays
- high-volume activity materialization MAY be asynchronous, but canonical evidence timing and identity MUST remain deterministic
- incident response must distinguish evidence-store failures from derived-history failures

## Migration / Compatibility / Supersession Considerations

- audit-record classes and reason-code semantics MUST evolve through explicit compatibility and supersession rules
- user-facing history contracts MAY change presentation, but MUST preserve the source-lineage model
- deprecated activity-card formats or export bundles MUST remain historically interpretable for their supported retention window
- migration of evidence stores or history views MUST preserve record identity, timestamps, visibility classification, and supersession lineage

## Implementation-Contract Guardrails

Downstream implementations MUST preserve at minimum:

- immutable canonical record identity and post-acceptance immutability
- clear separation between canonical evidence and derived activity materializations
- policy-governed visibility classes and audience shaping
- reason-coded, attributable operator actions for sensitive evidence controls
- replay-safe regeneration of derived history without changing canonical meaning
- explicit linkage for supersession, overlays, exports, and disclosures
- deterministic treatment of duplicate technical signals

Downstream implementations MUST NOT:

- optimize away canonical evidence creation for meaningful actions
- collapse source record identity into lossy presentation-only history
- store only redacted projections while discarding protected canonical lineage where policy still requires it
- let derived stores or caches become the sole evidence substrate
- hide operator remediation or evidence-access actions

## Downstream Execution Staging

This specification is expected to stage downstream work into:

1. canonical audit-record taxonomy and field contracts
2. activity derivation contracts for user and operator audiences
3. export, disclosure, and evidence-access control contracts
4. evidence store and retention lifecycle contracts
5. search, reporting, and notification projection contracts
6. operator and governance tooling contracts

## Required Downstream Specs / Contract Layers

This document directly governs or materially informs:

- domain audit-record contract specifications
- activity-history API specifications
- admin/control evidence-export and evidence-access API specifications
- user history and support timeline derivation specifications
- reporting and compliance evidence-export specifications
- notification/history consistency specifications
- search/index evidence discovery contracts
- retention and archival implementation contracts
- security and incident runbooks for evidence integrity or disclosure events

## Canonical Examples / Anti-Examples

### Example: Privileged Membership Removal
A governance-aware operator removes a member from a workspace. Canonical records include the operator, target membership, scope, reason code, approval lineage if required, and correlation to the owner-domain mutation. User-facing activity may say “An administrator changed your workspace access,” while internal evidence preserves full detail.

### Example: User Password Reset Notification
A user-facing notification about account security may appear in activity history, but the notification itself is presentational. The canonical evidence is the accepted and completed owner-domain security action plus any required support or risk review records.

### Example: Export of Sensitive Audit Package
A compliance reviewer requests an export of sensitive evidence. The export request, approval, package generation, and delivery are each auditable. The export package is not itself the canonical evidence owner.

### Anti-Example: Dashboard as Source of Truth
A support dashboard shows a timeline assembled from notifications and event streams only, with no canonical evidence references. This is non-canonical and unsafe.

### Anti-Example: Silent Redaction Rewrite
An operator edits a prior audit record in place to hide a mistaken action. This is forbidden. The proper approach is an overlay or superseding record with reason-coded lineage.

### Anti-Example: Search Index as Only Remaining History
A search system retains snippets of activity after the canonical evidence has been impermissibly deleted. This is forbidden and indicates lifecycle/control failure.

## Dependencies / Cross-Spec Links

This specification depends materially on:

- `DOMAIN_OWNERSHIP_MATRIX_SPEC.md`
- `API_ARCHITECTURE_SPEC.md`
- `PUBLIC_API_SPEC.md`
- `INTERNAL_SERVICE_API_SPEC.md`
- `EVENT_MODEL_AND_WEBHOOK_SPEC.md`
- `WORKFLOW_AND_AUTOMATION_SPEC.md`
- `SECURITY_AND_RISK_CONTROL_SPEC.md`
- `AUDIT_AND_ACCESS_TRACEABILITY_SPEC.md`
- `DATA_CLASSIFICATION_AND_HANDLING_SPEC.md`
- `DATA_RETENTION_DELETION_AND_ARCHIVAL_SPEC.md`
- `FILE_OBJECT_AND_ARTIFACT_STORAGE_SPEC.md`
- `SEARCH_INDEXING_AND_DISCOVERY_SPEC.md`

## Explicitly Deferred Items

The following are intentionally deferred:

- exact evidence schema tables and column definitions
- exact audience-by-audience redaction matrices
- exact UI layout for history cards and operator consoles
- exact jurisdiction-specific retention schedules
- exact export file formats and packaging structure
- exact warehouse schemas and BI tool modeling
- exact case-routing workflows for evidence review and disclosure

These do not weaken the canonical model established here.

## Final Normative Summary

FUZE audit-log and activity semantics form the platform’s canonical evidence and history-governance layer. This domain owns immutable audit-record posture, derived activity-history rules, evidence visibility classes, export and disclosure semantics, append-only correction posture, and the guardrails that keep reports, notifications, search results, dashboards, and UI histories subordinate to canonical evidence.

Audit truth is not business truth. Activity history is not canonical evidence. Eventing, workflow, queue, reporting, and presentation layers may correlate with audit history, but they do not replace it. Privileged actions, sensitive disclosures, evidence access, exports, and overrides require reason-coded, attributable, reviewable evidence. Derived surfaces may optimize for audience and usability, but they MUST preserve lineage and MUST NOT reinterpret platform truth on their own.

This document is the canonical FUZE rule set for audit-log and activity behavior.

## Quality Gate Checklist

- [x] Canonical owner is explicit for every material truth family
- [x] Mutation boundaries are explicit
- [x] Adjacent boundaries are explicit
- [x] Truth classes are explicit
- [x] Conflict-resolution rules are explicit
- [x] Default decision rules are explicit
- [x] Non-canonical patterns are clearly called out
- [x] Operator and reviewer evidence actions are bounded and auditable
- [x] Read-model, cache, and reporting rules are explicit
- [x] Failure and degraded-mode behaviors are explicit
- [x] Downstream implementation guardrails are explicit
- [x] Dependencies and downstream impacts are explicit
- [x] Non-goals and deferred items are explicit
- [x] The document is strong enough to support downstream implementation-contract work without contradictory semantics
