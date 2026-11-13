# Feature: Fraud Detection As a Compliance Officer, I want DemoIntern to retain and audit account management planning records and AI interaction evidence so that regulated banking reviews can verify who did what, when, and under which policy
Status: NEW
Owner: Astra
Last Updated: 2026-10-28

## Summary
DemoIntern is a native mobile, multimodal account management application used by Account Managers to conduct customer account planning activity and AI-assisted voice, visual, and written interactions. This feature establishes the compliance, audit, and records retention capability needed to support regulated banking reviews. It ensures that every account planning action, AI interaction, and security-relevant system event is captured as an immutable audit record, mapped to an approved retention class, protected against unauthorized modification or deletion, and made available to authorized Compliance Officers and approved support roles through controlled search, filter, and export workflows. The feature closes a gap identified in the BRD: the target mobile solution and its multimodal interaction model do not yet define how audit logs, interaction records, and account management plan artifacts are retained, queried, and disposed of, which is required for internal audit, regulatory review, incident investigation, and records management obligations.

## Scope
**In scope:**
- Immutable audit event creation for account management plan access, creation, update, AI interaction initiation, export access, retention actions, and security-relevant actions
- Retention classification of account management plan artifacts, written conversation records, visual validation artifact references, audio interaction metadata, and audit log entries
- Controlled compliance search, filter, and export workflows restricted to authorized Compliance Officer and approved support roles
- Legal hold and policy-restriction exclusion from disposition processing
- Scheduled retention evaluation and disposition processing with a verifiable processing report
- Masking or exclusion of unauthorized sensitive content fields in exported evidence

**Out of scope:**
- Enterprise records policy authoring outside the application boundary
- External regulator portal integration
- eDiscovery workflows beyond retention hold and export support required by current bank policy
- AI model governance approvals not directly related to record retention or audit evidence

## Application Type & Platform Context
This feature spans a native mobile client (where Account Managers perform account planning and AI-assisted interactions) and backend/API services (where audit, retention, and compliance export processing occur). Source evidence: "the native mobile application must preserve auditable evidence for customer account planning activity, AI-assisted voice interactions, visual validation events, and written conversations," and "The BRD establishes the target mobile solution and its multimodal interaction model." Backend processing is evidenced by the listed data sources (audit_log, retention_policy, retention_disposition_job, export_request tables) and API endpoints (POST /api/v1/audit/events, GET /api/v1/retention/policies, etc.). The compliance review and export interface is described as a "governed audit and records review interface or export workflow," without specifying whether this is a mobile, web, or admin-console surface.

**Open Question:** Confirm whether the Compliance Officer audit/export interface is delivered within the native mobile app, a separate web/admin console, or both.

## Actors and Permissions
- **Account Manager**: Performs account planning actions and initiates voice, visual, or written AI interactions. Actions generate audit events but this actor has no audit/retention administration access per the source.
- **Compliance Officer**: Authorized to search, filter, and export audit and retention evidence through the governed review interface; restricted to approved oversight purposes.
- **Approved Support Roles**: Granted access equivalent to Compliance Officer for audit/retention search, filter, and export operations, per RBAC requirements ("RBAC for Compliance Officer and approved support roles").
- **System (Retention Engine / Scheduler)**: Executes scheduled retention evaluation and disposition processing, validates policy class, event type, record creation date, hold status, and disposition approval rules.

All other roles (e.g., general bank staff, unauthenticated users) are implicitly excluded from audit/retention administration and export capabilities, as stated: "Restrict audit and retention administration capabilities to authorized compliance and support roles only."

**Open Question:** The specific set of "approved support roles" and how they are provisioned/approved is not defined in the source.

## Feature Development Intent
This is feature-development work because it requires new backend data capture, storage, retention logic, and export capabilities that do not currently exist in the BRD's target solution. The system must be built to:
- Emit immutable audit events for a defined set of account planning and AI interaction actions
- Classify and tag artifacts and logs with retention classes according to bank records rules
- Enforce RBAC on audit/retention search, filter, and export operations
- Run scheduled retention evaluation/disposition jobs that respect legal hold and active audit review exclusions
- Produce compliance evidence exports with masking of unauthorized sensitive fields
- Generate a verifiable retention processing report after each scheduled run

The outcome delivered is a verifiable, tamper-resistant audit and retention trail that allows auditors and compliance reviewers to confirm who performed which action, when, and under which policy, without altering source records.

## UI Design & Interaction Contract
The source describes a "governed audit and records review interface or export workflow" used by the Compliance Officer, with the following supported interaction flow:
1. Compliance Officer opens the governed audit and records review interface or export workflow.
2. User selects filters such as customer account, account manager, date range, or interaction type.
3. System validates reviewer permissions and retrieves immutable audit and retention metadata.
4. Compliance Officer requests an export or review package for approved oversight purposes.
5. System generates the evidence package, excluding unauthorized fields and preserving source integrity markers.

**Validation and interaction rules supported by source:**
- Frontend must validate mandatory filters before executing a search or export request.
- Frontend must prevent unauthorized export scopes from being submitted.
- Only authorized Compliance Officer and approved support roles may access the review/export interface.

The source does not describe specific screen layouts, navigation structure, visual design, copy/tone, or accessibility requirements for this interface.

**Open Questions:**
- Exact screen/page layout, navigation hierarchy, and visual design for the audit/records review interface are not specified.
- Specific validation error messages or copy/tone for filter validation and unauthorized export attempts are not specified.
- Accessibility requirements (e.g., WCAG conformance level) for this interface are not specified in the source.

## API Contract
The following endpoints are explicitly supported by the source:

| Method & Path | Purpose |
|---|---|
| POST /api/v1/audit/events | Create an immutable audit event record |
| GET /api/v1/audit/events | Search/filter audit events (subject to reviewer permission validation) |
| GET /api/v1/audit/exports/{exportId} | Retrieve a previously generated compliance evidence export package |
| POST /api/v1/compliance/exports | Request generation of a compliance evidence export |
| GET /api/v1/retention/policies | Retrieve retention policy definitions/classes |
| POST /api/v1/retention/evaluate | Trigger/perform retention evaluation against records |
| POST /api/v1/retention/disposition | Execute approved disposition actions on eligible records |

**Permissions:** Backend must require an authorized compliance role for export and retention operations (POST /api/v1/compliance/exports, POST /api/v1/retention/evaluate, POST /api/v1/retention/disposition, GET /api/v1/audit/events).

**Validation rules (source-supported):**
- Retention engine must validate policy class, event type, record creation date, hold status, and disposition approval rules before executing disposition.
- Export requests must be checked against export authorization rules; unauthorized export scopes must be rejected.
- Sensitive content fields must be masked or excluded in export responses for unauthorized viewers.

**Error handling, idempotency, and detailed schemas are not specified in the source.**

**Open Questions:**
- Request/response schemas, field-level contracts, and status codes for each endpoint are not defined.
- Idempotency behavior for POST /api/v1/audit/events and POST /api/v1/retention/disposition is not specified.
- Error response formats and failure-mode handling (e.g., partial export failures) are not defined.

## Business Logic & Rules
- Every account management plan access, creation, or update must generate an immutable audit event capturing actor, timestamp, action, target record, channel, and outcome.
- Every AI interaction initiation (voice, visual, written) must generate an immutable audit event with the same required metadata set.
- Export access and retention actions must themselves generate auditable events (audit of audit access).
- Each account management plan artifact, written conversation record, visual validation artifact reference, audio interaction metadata entry, and audit log entry must be assigned to an approved retention class based on policy rules.
- Audit records cannot be modified or deleted through standard application workflows; integrity indicators must be preserved for compliance review.
- Records under active legal hold or policy restriction must be excluded from disposition processing; the system must record the reason each retained or disposed item was evaluated.
- Retention evaluation and disposition must validate: policy class, event type, record creation date, hold status, and disposition approval rules.
- Compliance evidence exports must apply approved filters (user, customer account, date range, interaction type, event category) and include required metadata while masking/excluding unauthorized sensitive content fields.
- Scheduled retention evaluation and disposition processing must complete within the approved operational window and produce a verifiable retention processing report.
- Only authorized Compliance Officer and approved support roles may search, filter, or export audit evidence; all such review and export activity must itself be logged as auditable events.

## Data Model & Validation
Source-identified data sources (schemas/fields not fully specified beyond names):

| Entity | Source-Supported Role |
|---|---|
| audit_log | Stores immutable audit event records |
| audit_event_detail | Stores detailed context for audit events |
| account_management_plan | Stores account planning artifacts subject to retention classification |
| ai_interaction_record | Stores voice, visual, and written AI interaction records/transcripts and references |
| retention_policy | Defines approved retention classes and policy rules |
| retention_disposition_job | Tracks scheduled retention evaluation and disposition execution |
| export_request | Tracks compliance export requests and generated packages |
| legal_hold | Tracks records under active legal hold (if applicable by policy) |

**Required audit event metadata (per Acceptance Criteria):** actor, timestamp, action, target record, channel, action outcome, source device/session, referenced account management plan or customer account.

**Validation constraints (source-supported):**
- Retention classification must be based on approved policy rules mapped to record type.
- Disposition processing must validate hold status and exclude records under active hold or audit review.
- Export filters are mandatory and must be validated before processing.
- Sensitive content fields must be identifiable and maskable/excludable in export output.

**Open Questions:**
- Full field-level schema (data types, constraints, required vs. optional fields) for each table is not specified.
- Specific retention period durations per record/policy class are not defined in the source.
- Definition of which fields constitute "unauthorized sensitive content" for masking purposes is not specified.

## Functional Requirements
1. [FR1] The system shall create an immutable audit event for every account management plan access, creation, and update action, capturing actor, timestamp, action, target record, channel, and outcome. *(AC 1)*
2. [FR2] The system shall create an immutable audit event for every AI interaction initiation (voice, visual, written), capturing the same required metadata. *(AC 1)*
3. [FR3] The system shall create an immutable audit event for every export access and retention action performed. *(AC 1, AC 4)*
4. [FR4] The system shall assign each account management plan artifact, written conversation record, visual validation artifact reference, audio interaction metadata entry, and audit log entry to an approved retention class based on policy rules. *(AC 2)*
5. [FR5] The system shall prevent modification or deletion of audit records through standard application workflows and shall preserve integrity indicators for compliance review. *(AC 3)*
6. [FR6] The system shall restrict search, filter, and export of audit evidence to authorized Compliance Officer and approved support roles only. *(AC 4)*
7. [FR7] The system shall log all audit/retention review and export activity as auditable events. *(AC 4)*
8. [FR8] The system shall exclude records under active legal hold or policy restriction from disposition processing. *(AC 5)*
9. [FR9] The system shall record the reason each retained or disposed item was evaluated during retention/disposition processing. *(AC 5)*
10. [FR10] The system shall generate compliance evidence exports using approved filters (user, customer account, date range, interaction type, event category) and required metadata. *(AC 6)*
11. [FR11] The system shall mask or exclude unauthorized sensitive content fields in generated compliance exports. *(AC 6)*
12. [FR12] The system shall complete scheduled retention evaluation and disposition processing within the approved operational window. *(AC 7)*
13. [FR13] The system shall produce a verifiable retention processing report after each scheduled retention/disposition run. *(AC 7)*
14. [FR14] The backend shall require an authorized compliance role for all export and retention administration operations.
15. [FR15] The retention engine shall validate policy class, event type, record creation date, hold status, and disposition approval rules before executing disposition.
16. [FR16] The frontend shall validate that mandatory filters are supplied and shall prevent submission of unauthorized export scopes.

## Non-Functional Requirements
- **Performance:** Audit event write latency shall be under 300 ms at p95 for normal interaction volume.
- **Performance:** Filtered audit retrieval shall complete under 3 seconds for standard review ranges.
- **Performance:** Retention batch processing shall complete nightly within the approved operational window.
- **Performance:** Export package generation shall complete within 5 minutes for standard compliance requests.
- **Security:** RBAC shall be enforced for Compliance Officer and approved support roles on all audit/retention/export operations.
- **Security:** Audit record storage shall use immutable storage controls.
- **Security:** Stored evidence shall be encrypted.
- **Security:** Export operations shall be subject to authorization checks and masked response fields for sensitive content.
- **Security:** All audit access (including reviewer search/export activity) shall itself be fully audited.
- **Privacy:** Customer-related voice, image, and written artifacts shall receive privacy-aware treatment.
- **Reliability:** Retention/disposition processing shall produce a verifiable processing report to confirm completion and outcomes.

## Acceptance Scenarios

**Scenario 1 — Audit event creation on account planning action**
Given an Account Manager accesses, creates, or updates an account management plan
When the action completes
Then the system creates an immutable audit event capturing actor, timestamp, action, target record, channel, and outcome.

**Scenario 2 — Audit event creation on AI interaction**
Given an Account Manager initiates a voice, visual, or written AI interaction
When the interaction is submitted
Then the system creates an immutable audit event with the required metadata referencing the related account management plan or customer account.

**Scenario 3 — Retention class assignment**
Given an account management plan artifact, interaction transcript, image artifact reference, or audio metadata record is created
When the record is persisted
Then the system assigns it to an approved retention class based on policy rules.

**Scenario 4 — Immutable record protection**
Given an existing audit record
When any standard application workflow attempts to modify or delete it
Then the system prevents the action and preserves integrity indicators.

**Scenario 5 — Authorized compliance search and export**
Given a Compliance Officer with authorized role access
When they apply approved filters (customer account, account manager, date range, interaction type) and request a search or export
Then the system validates permissions, retrieves immutable audit and retention metadata, and logs the review/export activity as an auditable event.

**Scenario 6 — Unauthorized export attempt**
Given a user without Compliance Officer or approved support role access
When they attempt to search, filter, or export audit evidence
Then the system denies the request and does not return audit or retention data.

**Scenario 7 — Disposition exclusion for hold**
Given a record is under active legal hold or policy restriction
When scheduled retention evaluation runs
Then the system excludes the record from disposition and records the reason it was retained.

**Scenario 8 — Compliance export with sensitive field masking**
Given an approved Compliance Officer requests an evidence export
When the export is generated
Then the system includes required metadata, excludes unauthorized sensitive content fields, and preserves source integrity markers.

**Scenario 9 — Scheduled retention processing completion**
Given scheduled retention evaluation and disposition processing is triggered
When processing completes within the approved operational window
Then the system produces a verifiable retention processing report documenting each retention decision.

## Traceability Matrix

| Source ID | Requirement | Acceptance Criteria | Test Coverage |
|---|---|---|---|
| US 89780-AC1 | Immutable audit event creation for plan access/creation/update, AI interaction, export access, retention actions | AC 1 | Scenario 1, Scenario 2 |
| US 89780-AC2 | Retention class assignment for plan artifacts, transcripts, visual/audio artifacts, audit logs | AC 2 | Scenario 3 |
| US 89780-AC3 | Prevention of audit record modification/deletion; integrity indicators preserved | AC 3 | Scenario 4 |
| US 89780-AC4 | RBAC restriction of search/filter/export to Compliance Officer and approved support roles; logging of review/export activity | AC 4 | Scenario 5, Scenario 6 |
| US 89780-AC5 | Exclusion of held/restricted records from disposition; reason recording | AC 5 | Scenario 7 |
| US 89780-AC6 | Compliance export generation with approved filters, required metadata, masking of sensitive fields | AC 6 | Scenario 8 |
| US 89780-AC7 | Scheduled retention evaluation/disposition within operational window; verifiable processing report | AC 7 | Scenario 9 |

## Open Questions
- Confirm whether the Compliance Officer audit/export review interface is part of the native mobile app, a separate web/admin console, or both.
- What specific roles qualify as "approved support roles," and how is that approval granted/maintained?
- What are the exact UI screen layouts, navigation flows, copy/tone, and accessibility requirements for the governed audit/records review interface?
- What are the full request/response schemas, status codes, and idempotency behaviors for each listed API endpoint?
- What are the specific retention period durations and disposition rules per record/policy class?
- What fields constitute "unauthorized sensitive content" subject to masking/exclusion in exports?
- Is a legal_hold table/workflow actually implemented, or only conditionally applicable "if applicable by policy"?
- What is the defined "approved operational window" for nightly retention batch processing?

## Source References
- Feature ID: 89780 — "Fraud Detection As a Compliance Officer, I want DemoIntern to retain and audit account management planning records and AI interaction evidence so that regulated banking reviews can verify who did what, when, and under which policy"
- User Story: US 89780 (Story Points: 8), Acceptance Criteria 1–7
- BRD excerpts: CONTEXT & BACKGROUND, CURRENT STATE, DESIRED STATE, KEY FUNCTIONALITY, USER INTERACTION FLOW, TECHNICAL CONSIDERATIONS, OUT OF SCOPE, SUCCESS METRICS sections of Feature 89780 description
- Derived Source Signals: Application Type (mixed) and Design Guidelines Extracted From Source, as provided in Feature 89780 metadata