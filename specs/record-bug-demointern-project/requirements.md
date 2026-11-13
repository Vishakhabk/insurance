# Implementation Requirements Checklist

**Purpose**: Provide an implementation acceptance checklist that agents can execute one item at a time.
**Feature**: Edited As a Bug Management User, I want to record a bug for the DemoIntern project so that defects are captured with consistent and traceable information (Feature 91918 / US 91918)

## Functional Acceptance Criteria

- [ ] [R1] POST /api/projects/{projectId}/bugs is implemented and creates exactly one `bug_records` entry per valid request, scoped to the DemoIntern project
- [ ] [R2] Successful creation response includes the generated `bugId`, `projectId`, `reporter`, and `creationTimestamp`
- [ ] [R3] Created bug persists baseline fields: title, description, status, priority, severity, and reporter
- [ ] [R4] Status, priority, and severity values are validated against `bug_status_lookup`, `bug_priority_lookup`, and `bug_severity_lookup` respectively; unsupported codes are rejected
- [ ] [R5] Missing, blank, whitespace-only, or invalid title, description, status, priority, severity, or reporter causes request rejection with field-level validation errors and no `bug_records` row is created
- [ ] [R6] Requests whose `projectId` does not map to the DemoIntern project scope are blocked, return a project-scope error response, and do not generate a `bugId`
- [ ] [R7] Title and description containing markup or script input are sanitized before persistence; sanitized values are stored, not raw input
- [ ] [R8] Created bug is retrievable via GET /api/projects/{projectId}/bugs/{bugId} returning the sanitized, persisted record
- [ ] [R9] Reporter is derived from the authenticated session or accepted as an allowed supplied field, with identity required
- [ ] [R10] Primary path (valid create), field-validation failure path, invalid-lookup failure path, and project-scope mismatch path are all covered by implementation and tests

## UI Acceptance Criteria

- [ ] [R11] N/A — feature is API-service-only per derived Application Type signal (no UI screens specified in source); do not implement UI screens beyond what source supports
- [ ] [R12] If a bug-creation UI is later introduced, field-level validation error display and required-field indicators must follow existing design-system conventions (currently out of scope — not source-supported)

## API and Integration Acceptance Criteria

- [ ] [R13] POST /api/projects/{projectId}/bugs accepts title, description, status, priority, severity, and reporter (or derives reporter from session) and returns the created bug record on success
- [ ] [R14] GET /api/projects/{projectId}/bugs/{bugId} returns the stored bug record by bugId
- [ ] [R15] Both POST and GET endpoints are served only over TLS-protected connections
- [ ] [R16] Endpoint access requires authentication; creation is additionally authorized as project-scoped to DemoIntern
- [ ] [R17] Field-level validation error responses are returned for each failing field without partial persistence
- [ ] [R18] Project-scope error response is returned distinctly when projectId does not map to DemoIntern, without generating a bugId
- [ ] [R19] Foreign key validation against `bug_projects`, `bug_priority_lookup`, `bug_severity_lookup`, and `bug_status_lookup` occurs before persistence
- [ ] [R20] No existing bug-related contract is broken by this change (new endpoint/feature; verify no overlapping route conflicts)

## Business Logic and Data Acceptance Criteria

- [ ] [R21] `bugId` is generated uniquely for each successfully created bug record
- [ ] [R22] `creationTimestamp` is generated and immutable once set
- [ ] [R23] Bug record is persisted to `bug_records` with project association (foreign key to DemoIntern project in `bug_projects`)
- [ ] [R24] Text length limits are enforced for title and description; overlength values are rejected
- [ ] [R25] Blank or whitespace-only title/description values are rejected as invalid
- [ ] [R26] Sanitization logic strips/escapes unsafe markup or script content from title and description prior to storage
- [ ] [R27] Uniqueness and integrity of `bugId` are preserved under concurrent create traffic (e.g., via DB-level uniqueness constraint or equivalent)
- [ ] [R28] Reporter identity is required and validated as part of persistence payload
- [ ] [R29] Status transition rules beyond accepting a valid initial status are explicitly out of scope and not implemented
- [ ] [R30] Ownership assignment/reassignment logic is explicitly out of scope and not implemented
- [ ] [R31] Notifications, dashboards, approval flows, and external defect synchronization are explicitly out of scope and not implemented

## Non-Functional Acceptance Criteria

- [ ] [R32] Authenticated access is enforced for both create and retrieve operations
- [ ] [R33] Project-scoped authorization is enforced for bug creation (only within DemoIntern project context)
- [ ] [R34] Create attempts are logged with correlationId, projectId, actorId, validation failure details, and persistence outcome
- [ ] [R35] Logs never contain raw unsafe/unsanitized input (e.g., raw markup/script payloads excluded from log output)
- [ ] [R36] Create endpoint targets sub-2-second response time for normal payloads; at least 95% of valid requests meet this under normal load (verify via load/perf test)
- [ ] [R37] 100% of invalid required-field, invalid-lookup, or invalid-project submissions are rejected before persistence (verify via test suite covering each invalid case)
- [ ] [R38] 100% of successful creations include a unique `bugId` and `creationTimestamp` (verify via test assertions)
- [ ] [R39] Golden Repo bug-management terminology (bug_records, bug_projects, bug_priority_lookup, bug_severity_lookup, bug_status_lookup) is used consistently in implementation where this feature touches that domain
- [ ] [R40] The implementation does not introduce unrelated domains (e.g., no ownership workflow, notification, or dashboard logic added)
- [ ] [R41] Automated tests cover: valid creation, each missing/invalid required field, invalid lookup codes, invalid/mismatched projectId, and sanitization of malicious text input

## Traceability

- [ ] [R42] Every implemented change maps back to US 91918 Acceptance Criteria 1–5 and the Key Functionality items in the feature description
- [ ] [R43] Every non-blocking Open Question that was implemented has a recorded decision + one-line rationale in specs/<slug>/assumptions.md (no Open Question is silently assumed)
- [ ] [R44] No BLOCKING Open Question was implemented as an assumption (a feature with an unresolved blocking question is held at needs-clarification, not completed)
- [ ] [R45] Open Question: exact text length limits for title/description are not specified in source — must not be implemented as an assumed numeric value without recorded decision; flag as Open Question requiring product/config clarification
- [ ] [R46] Open Question: exact allowed lookup values/codes for status, priority, and severity are not enumerated in source — must not be invented; implementation must read from configured lookup tables rather than hardcoding specific values
- [ ] [R47] Open Question: mechanism for determining "DemoIntern project scope" (e.g., specific projectId value or identifier format) is not defined in source — must not be hardcoded as an assumption without recorded decision

## Assumptions

### ASM-1
- [R48] Statement: This feature is implemented as an API-only capability with no accompanying UI screens in this iteration.
- [R49] Source: Derived Application Type signal marks this as "api-service"; description and acceptance criteria reference only POST/GET endpoints, no screens, forms, or UI states.
- [R50] Rationale: No UI wireframes, screen states, or interaction components are described anywhere in the source beyond the described programmatic request/response flow.
- [R51] Potential impact: If a UI is later required, design-system and accessibility checklist items will need to be added in a follow-up spec.
- [R52] Confidence: high
- [R53] Recommended resolution: Confirm with product whether a bug-creation UI is planned in a separate feature; if so, scope it separately.

### ASM-2
- [R54] Statement: Bug record uniqueness/integrity under concurrent traffic is assumed to be enforced via a database-level unique constraint or equivalent transactional guarantee rather than application-only locking.
- [R55] Source: Technical Considerations: "preserve uniqueness/integrity under concurrent create traffic."
- [R56] Rationale: No specific concurrency-control mechanism (e.g., DB constraint, optimistic locking, distributed lock) is specified in source; a standard, low-risk approach is inferred to satisfy the stated outcome.
- [R57] Potential impact: If a different mechanism is required by the monolith's existing data-layer conventions, implementation may need rework.
- [R58] Confidence: medium
- [R59] Recommended resolution: Validate against the project's existing persistence-layer patterns for concurrency control before finalizing implementation.

## Notes

- [R60] Never resolve an Open Question silently. In an unattended run, record the chosen assumption + rationale in specs/<slug>/assumptions.md; blocking questions must instead hold the feature at needs-clarification.
- [R61] Mark an item complete only after verifying actual implementation code and behavior.