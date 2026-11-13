# Implementation Requirements Checklist

**Purpose**: Provide an implementation acceptance checklist that agents can execute one item at a time.
**Feature**: Fraud Detection — Audit & Retention of Account Management Planning Records and AI Interaction Evidence (Feature 89780 / US 89780)

## Functional Acceptance Criteria

- [ ] [R1] Immutable audit record is created for every account management plan access, creation, and update event, capturing actor, timestamp, action, target record, channel, and outcome
- [ ] [R2] Immutable audit record is created for every AI interaction initiation (voice, visual, written) with the same required metadata fields
- [ ] [R3] Immutable audit record is created for every export access and every retention action (evaluation, hold, disposition)
- [ ] [R4] Each account management plan artifact, written conversation record, visual validation artifact reference, audio interaction metadata record, and audit log entry is assigned to an approved retention class based on policy rules
- [ ] [R5] Standard application workflows cannot modify or delete audit records; integrity indicators required for compliance review are preserved
- [ ] [R6] Only authorized Compliance Officer and approved support roles can search, filter, and export audit evidence
- [ ] [R7] All review and export activity performed by Compliance Officer/support roles is itself logged as an auditable event
- [ ] [R8] Records under active hold or policy restriction are excluded from disposition processing, and the evaluation reason (retained or disposed) is recorded per item
- [ ] [R9] Compliance evidence exports support filtering by user, customer account, date range, interaction type, and event category, and include required metadata while masking/excluding unauthorized sensitive content fields
- [ ] [R10] Scheduled retention evaluation and disposition processing completes within the approved operational window and produces a verifiable retention processing report
- [ ] [R11] Primary flow (action → audit event → artifact storage under retention class → compliance review/export → scheduled retention processing → disposition) is fully implemented end to end
- [ ] [R12] Alternate/failure paths are covered: unauthorized export attempt rejected and logged, record under hold skipped during disposition with reason recorded, missing mandatory filter blocks export request

## UI Acceptance Criteria

- [ ] [R13] Governed audit and records review interface allows Compliance Officer to select filters (customer account, account manager, date range, interaction type, event category)
- [ ] [R14] Frontend validates mandatory filters and prevents unauthorized export scopes before submission
- [ ] [R15] Export/review request workflow surfaces validation messages when required filters are missing or scope exceeds permission
- [ ] [R16] UI displays export status/progress and final evidence package availability (per 5-minute generation expectation)
- [ ] [R17] UI masks or excludes unauthorized sensitive content fields in displayed and exported results
- [ ] [R18] Access to audit/retention administration screens is restricted to authorized compliance and approved support roles; unauthorized users do not see these entry points
- [ ] [R19] Existing design-system and local UI conventions are followed for the native mobile application surfaces touched by this feature

## API and Integration Acceptance Criteria

- [ ] [R20] `POST /api/v1/audit/events` implemented to record immutable audit events with required metadata
- [ ] [R21] `GET /api/v1/audit/events` implemented with filtering by user, customer account, date range, interaction type, event category, restricted to authorized roles
- [ ] [R22] `POST /api/v1/compliance/exports` implemented to request compliance evidence exports with filter validation and permission checks
- [ ] [R23] `GET /api/v1/audit/exports/{exportId}` implemented to retrieve export status/result
- [ ] [R24] `GET /api/v1/retention/policies` implemented to expose approved retention policy classes
- [ ] [R25] `POST /api/v1/retention/evaluate` implemented to evaluate records against policy class, event type, record creation date, hold status, and disposition approval rules
- [ ] [R26] `POST /api/v1/retention/disposition` implemented to execute disposition actions, excluding held/restricted records, and logging each decision
- [ ] [R27] Backend enforces authorized compliance role check on all export and retention operations
- [ ] [R28] Retention engine validates policy class, event type, record creation date, hold status, and disposition approval rules before any disposition action
- [ ] [R29] Legal hold handling is implemented only if/where policy applies (`legal_hold` table referenced as conditional); behavior not explicitly defined in source must not be invented — see Open Questions
- [ ] [R30] Existing contracts remain backward-compatible; no breaking changes introduced to unrelated APIs

## Business Logic and Data Acceptance Criteria

- [ ] [R31] `audit_log` and `audit_event_detail` tables (or equivalent) store chain-of-custody metadata: actor, timestamp, source device/session, action outcome, and referenced account management plan or customer account
- [ ] [R32] `account_management_plan`, `ai_interaction_record`, `retention_policy`, `retention_disposition_job`, `export_request` tables (or equivalent) implemented and mapped to retention classes
- [ ] [R33] Retention class assignment logic is deterministic and rule-driven per bank records policy (policy class mapping validated against `retention_policy` table)
- [ ] [R34] Disposition processing logic prevents deletion of records under approved hold or active audit review and records the reason for each retain/dispose decision
- [ ] [R35] Export generation logic excludes unauthorized fields and preserves source integrity markers (e.g., hash/checksum or equivalent integrity indicator)
- [ ] [R36] Error handling covers: invalid/missing filter input, unauthorized role attempting export/retention action, record evaluation failure during disposition batch, export generation failure/timeout
- [ ] [R37] Edge cases covered: record simultaneously eligible for disposition and under hold (hold takes precedence), interaction record spanning multiple artifact types (voice + visual + written) all retained under correct classes

## Non-Functional Acceptance Criteria

- [ ] [R38] RBAC enforced for Compliance Officer and approved support roles across audit, review, export, and retention administration capabilities
- [ ] [R39] Immutable storage controls applied to audit records (write-once or append-only mechanism verified)
- [ ] [R40] Encryption at rest implemented for stored evidence (plans, transcripts, image artifact references, audio metadata, audit logs)
- [ ] [R41] Export authorization checks enforced server-side independent of UI restrictions
- [ ] [R42] All audit access (read/search/export) is itself fully audited
- [ ] [R43] Privacy-aware handling verified for customer-related voice, image, and written artifacts (masking/exclusion of unauthorized sensitive fields in exports)
- [ ] [R44] Audit event write latency verified under 300 ms at p95 for normal interaction volume
- [ ] [R45] Filtered audit retrieval verified under 3 seconds for standard review ranges
- [ ] [R46] Retention batch processing verified to complete nightly within the approved operational window
- [ ] [R47] Export package generation verified to complete within 5 minutes for standard compliance requests
- [ ] [R48] Banking/records-management terminology, entities (account management plan, AI interaction record, retention policy, legal hold, disposition job), and constraints from the source are applied consistently
- [ ] [R49] Implementation does not introduce unrelated domains (e.g., no enterprise records policy authoring, no external regulator portal integration, no eDiscovery beyond hold/export, no AI model governance approvals)
- [ ] [R50] Tests/verification cover highest-risk behavior: immutability enforcement, RBAC bypass attempts, hold-vs-disposition conflict resolution, export field masking correctness

## Traceability

- [ ] [R51] Every implemented change maps back to US 89780 Acceptance Criteria 1–7 and the Key Functionality/Technical Considerations sections of the source description
- [ ] [R52] Every non-blocking Open Question that was implemented has a recorded decision + one-line rationale in specs/<slug>/assumptions.md (no Open Question is silently assumed)
- [ ] [R53] No BLOCKING Open Question was implemented as an assumption (a feature with an unresolved blocking question is held at needs-clarification, not completed)

### Open Questions (must not be implemented as assumptions)
- [R54] Exact legal hold data model and workflow (`legal_hold table if applicable by policy`) is conditional in source — whether legal hold is in scope for this release is unresolved; do not implement hold logic beyond excluding held/restricted records from disposition until confirmed.
- [R55] Specific retention duration values per record/policy class are not defined in source — do not hardcode retention periods; must be configurable/validated against `retention_policy` table content supplied externally.
- [R56] Definition of "approved support roles" (which roles qualify, beyond Compliance Officer) is not enumerated in source — do not invent a role list; must be confirmed before RBAC role mapping is finalized.
- [R57] Masking rules for "unauthorized sensitive content fields" in exports are not field-by-field specified — do not guess which fields to mask; must be validated against bank privacy policy before export logic is finalized.

## Assumptions

### ASM-1
- [R58] Statement: The audit, retention, and compliance export capabilities will be implemented as part of the existing monolith application rather than as separate services.
- [R59] Source: User-selected Architecture Style: monolith (feature metadata)
- [R60] Rationale: The feature metadata explicitly designates monolith as the architecture style, and no source content indicates a service-separation requirement.
- [R61] Potential impact: If a modular/service boundary is actually required for compliance isolation, retrofitting later could require significant rework.
- [R62] Confidence: medium
- [R63] Recommended resolution: Confirm with architecture owner whether audit/retention modules require logical or physical isolation within the monolith before implementation.

### ASM-2
- [R64] Statement: The data source tables listed (audit_log, audit_event_detail, account_management_plan, ai_interaction_record, retention_policy, retention_disposition_job, export_request, legal_hold) are treated as the authoritative persistence model to implement against, pending schema validation.
- [R65] Source: TECHNICAL CONSIDERATIONS — Data source list in feature description
- [R66] Rationale: Source explicitly names these tables as the data source; no alternate schema is provided, so these names are used as the implementation target.
- [R67] Potential impact: If actual schema/table names differ in the target database, implementation will require renaming/migration.
- [R68] Confidence: medium
- [R69] Recommended resolution: Validate exact table/column definitions with the data/schema owner before finalizing persistence layer code.

## Notes

- [R70] Never resolve an Open Question silently. In an unattended run, record the chosen assumption + rationale in specs/<slug>/assumptions.md; blocking questions must instead hold the feature at needs-clarification.
- [R71] Mark an item complete only after verifying actual implementation code and behavior.