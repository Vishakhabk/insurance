# Feature: Edited As a Bug Management User, I want to record a bug for the DemoIntern project so that defects are captured with consistent and traceable information
Status: NEW
Owner: Astra
Last Updated: 2026-10-28

## Summary
DemoIntern requires a dependable mechanism for recording bugs within Bug Record Management so that every newly reported defect is created with a consistent structure, valid project linkage, and traceable metadata. This feature introduces a project-scoped bug creation capability that enforces required fields, validates status/priority/severity against configured lookup tables, sanitizes free-text inputs, and generates a unique bug identifier and immutable creation timestamp. The expected outcome is that all DemoIntern bugs begin their lifecycle with consistent, auditable, and retrievable data, enabling later workflow, ownership, and audit capabilities to build on a trustworthy foundation.

## Scope
**In scope:**
- Creating a new bug record for the DemoIntern project via `POST /api/projects/{projectId}/bugs`.
- Validating required fields: title, description, status, priority, severity, and reporter.
- Enforcing status, priority, and severity values against configured lookup tables (`bug_status_lookup`, `bug_priority_lookup`, `bug_severity_lookup`).
- Validating that the `projectId` maps to the authorized DemoIntern project scope (`bug_projects`).
- Sanitizing title and description content to remove unsafe markup/script before persistence.
- Generating a unique `bugId` and immutable `creationTimestamp` for each successfully created bug.
- Returning the created bug record, and making it retrievable via `GET /api/projects/{projectId}/bugs/{bugId}`.
- Authenticated, TLS-protected access for both create and retrieve operations.
- Logging of create attempts with correlationId, projectId, actorId, validation failures, and persistence outcome.

**Out of scope:**
- Ownership assignment or reassignment after bug creation.
- Status transition rules beyond accepting a valid initial status value.
- Notifications, dashboards, approval flows, or external defect synchronization.

## Application Type & Platform Context
This feature targets an **API/service** context within a **monolith** architecture style.

Evidence:
- "Accept POST /api/projects/{projectId}/bugs only for an authorized DemoIntern project context"
- "Return the created record so it can be retrieved through GET /api/projects/{projectId}/bugs/{bugId}"
- "Serve POST/GET operations only over TLS-protected endpoints"

No UI screens, wireframes, or front-end interaction details are specified in the source; the "user interaction flow" describes a logical sequence of request submission and validation rather than a defined UI. See Open Questions regarding any client/UI layer.

## Actors and Permissions
| Actor | Role | Permissions | Constraints |
|---|---|---|---|
| Bug Management User | Authenticated actor creating/retrieving bugs | Create bug via POST; retrieve bug via GET, scoped to DemoIntern project | Must be authenticated; creation requires project-scoped authorization for DemoIntern; access only over TLS |

- Reporter identity is derived from the authenticated session or supplied as an allowed field, but must always resolve to a valid reporter identity.
- No other roles (e.g., admin, auditor) are described in the source. See Open Questions.

## Feature Development Intent
This is feature-development work because it introduces a new, enforced bug creation capability that does not currently exist in a reliable form. The system must be built or changed to:
- Accept and validate a structured bug creation request scoped to the DemoIntern project.
- Enforce referential integrity against lookup tables for status, priority, and severity.
- Sanitize free-text fields to prevent storage of unsafe markup or script content.
- Generate guaranteed-unique identifiers and immutable timestamps at creation time.
- Persist a new `bug_records` row and expose it for retrieval.

The delivered outcome is that every DemoIntern bug begins its lifecycle with consistent, validated, sanitized, and traceable data, forming a reliable foundation for subsequent workflow, ownership, and audit functionality.

## UI Design & Interaction Contract
The source does not describe specific screens, layouts, visual states, or UI copy/tone. It describes a logical user interaction flow at the request/response level:

1. The authenticated user opens bug creation while working inside the DemoIntern project.
2. The user enters title, description, status, priority, and severity; reporter is derived from the authenticated session or supplied as an allowed field.
3. The system validates the projectId, required inputs, text length limits, and lookup-backed values.
4. If any field fails validation, the system returns field-level errors without creating a bug record.
5. If validation succeeds, the system sanitizes free-text inputs and prepares the persistence payload.
6. The system creates the bug record with a new bugId and creationTimestamp linked to the DemoIntern project.
7. The system returns a success response containing the stored bug details; the new bug becomes retrievable by bugId.

No specific screen names, layout structures, accessibility requirements, or exact validation message copy are provided in the source. These must be defined before UI implementation. See Open Questions.

## API Contract

### Create Bug
- **Operation:** `POST /api/projects/{projectId}/bugs`
- **Authorization:** Requires authenticated access; requires project-scoped authorization confirming `projectId` maps to the DemoIntern project.
- **Inputs (request body, source-supported fields only):**
  - `title` (required)
  - `description` (required)
  - `status` (required; must exist in `bug_status_lookup`)
  - `priority` (required; must exist in `bug_priority_lookup`)
  - `severity` (required; must exist in `bug_severity_lookup`)
  - `reporter` (required; derived from authenticated session or supplied as an allowed field)
- **Path parameter:** `projectId` (must resolve to the DemoIntern project scope)
- **Successful output:** Response containing `bugId`, `projectId`, `reporter`, `creationTimestamp`, and the persisted baseline fields (title, description, status, priority, severity) using sanitized values.
- **Error outputs:**
  - Field-level validation errors for missing, blank, whitespace-only, overlength, or invalid-lookup values — no bug record is created.
  - Project-scope error response when `projectId` does not map to the DemoIntern project — no bug identifier is generated.
- **Idempotency:** Not specified as idempotent; each valid request creates exactly one new `bug_records` entry (per Acceptance Criterion 1). Duplicate submissions are not described as deduplicated. See Open Questions.
- **Transport:** TLS-protected endpoint only.

### Retrieve Bug
- **Operation:** `GET /api/projects/{projectId}/bugs/{bugId}`
- **Authorization:** Requires authenticated, TLS-protected access.
- **Inputs:** `projectId`, `bugId` path parameters.
- **Output:** The stored bug record (as persisted after creation), retrievable using the generated `bugId`.
- **Error outputs:** Not detailed in source beyond implied retrieval failure for invalid identifiers. See Open Questions.

No other endpoints, update/delete operations, pagination, filtering, or batch operations are supported by the source.

## Business Logic & Rules
- A bug may only be created when `projectId` resolves to the authorized DemoIntern project scope; otherwise creation is blocked and no `bugId` is generated.
- Required fields for creation: title, description, status, priority, severity, reporter. Missing, blank, or whitespace-only values are rejected.
- Title and description are subject to length limits; overlength values are rejected.
- Status, priority, and severity values must match entries in their respective configured lookup tables (`bug_status_lookup`, `bug_priority_lookup`, `bug_severity_lookup`); unsupported codes are rejected.
- Reporter identity must be valid and is required; it is derived from the authenticated session or supplied as an allowed field.
- Title and description content is sanitized to remove unsafe markup or script content before persistence; sanitized values (not raw input) are stored and returned.
- On successful validation and sanitization, the system generates a unique `bugId` and an immutable `creationTimestamp`, and links the record to the DemoIntern project (`bug_projects`).
- Exactly one `bug_records` entry is created per valid request.
- Uniqueness and data integrity of created records must be preserved under concurrent create traffic.
- Validation failures and project-scope failures must prevent persistence entirely — no partial records and no `bugId` generation occur on failure.

## Data Model & Validation
**Entity: bug_records**
| Field | Description | Validation Rules |
|---|---|---|
| bugId | Generated unique identifier | Generated by system on successful creation; immutable |
| projectId | Project association | Must map to the authorized DemoIntern project scope (validated against `bug_projects`) |
| title | Bug title | Required; reject missing, blank, whitespace-only, or overlength values; sanitized before persistence |
| description | Bug description | Required; reject missing, blank, whitespace-only, or overlength values; sanitized before persistence |
| status | Initial bug status | Required; must exist in `bug_status_lookup` |
| priority | Bug priority | Required; must exist in `bug_priority_lookup` |
| severity | Bug severity | Required; must exist in `bug_severity_lookup` |
| reporter | Reporter identity | Required; derived from authenticated session or supplied as an allowed field; must be a valid identity |
| creationTimestamp | Record creation time | Generated by system; immutable |

**Reference data:**
- `bug_projects` — used to validate project association/scope.
- `bug_priority_lookup` — valid priority codes.
- `bug_severity_lookup` — valid severity codes.
- `bug_status_lookup` — valid status codes.

**Data-quality constraints:**
- No bug record may be persisted with missing, blank, whitespace-only, overlength, or lookup-invalid values in required fields.
- No bug record may be persisted without a valid, authorized project association.
- Stored title/description must be sanitized; unsafe markup or script content must not be persisted in raw form.

Retention expectations are not specified in the source. See Open Questions.

## Functional Requirements
1. [FR1] The system must accept `POST /api/projects/{projectId}/bugs` requests only for an authenticated Bug Management User.
2. [FR2] The system must validate that `projectId` maps to the authorized DemoIntern project scope before any further processing.
3. [FR3] The system must require and validate presence of title, description, status, priority, severity, and reporter on every create request.
4. [FR4] The system must reject requests where title or description is missing, blank, whitespace-only, or exceeds configured length limits.
5. [FR5] The system must reject requests where status, priority, or severity values do not exist in their respective configured lookup tables.
6. [FR6] The system must reject requests with an invalid or unresolved reporter identity.
7. [FR7] The system must return field-level validation errors for each failing field without creating a `bug_records` row.
8. [FR8] The system must return a project-scope error response and must not generate a `bugId` when `projectId` does not map to the DemoIntern project.
9. [FR9] The system must sanitize title and description content to remove unsafe markup or script input prior to persistence.
10. [FR10] The system must generate a unique `bugId` and an immutable `creationTimestamp` for each successfully created bug.
11. [FR11] The system must create exactly one `bug_records` entry per successful valid request.
12. [FR12] The system must return a response containing `bugId`, `projectId`, `reporter`, `creationTimestamp`, and the persisted baseline fields upon successful creation.
13. [FR13] The system must make the created bug retrievable via `GET /api/projects/{projectId}/bugs/{bugId}` using sanitized stored values.
14. [FR14] The system must serve both POST and GET operations only over TLS-protected endpoints.
15. [FR15] The system must preserve uniqueness and integrity of bug records under concurrent create requests.
16. [FR16] The system must log create attempts with correlationId, projectId, actorId, validation failures, and persistence outcome, while avoiding storage of raw unsafe input in logs.

## Non-Functional Requirements
- **Performance:** At least 95% of valid create requests must complete within 2 seconds under normal load (sub-2-second target for normal payloads).
- **Security:** All create and retrieve operations require authenticated access; creation requires project-scoped authorization; text inputs must be sanitized before persistence; endpoints must be TLS-protected.
- **Reliability/Integrity:** Uniqueness and integrity of `bugId` and record data must be preserved under concurrent create traffic.
- **Observability:** Create attempts must be logged with correlationId, projectId, actorId, validation failure details, and persistence outcome; raw unsafe input must not appear in logs.
- **Data Consistency:** 100% of successful DemoIntern bug creations must include a unique `bugId` and `creationTimestamp`.
- **Validation Enforcement:** 100% of invalid required-field, invalid-lookup, or invalid-project submissions must be rejected before persistence.

## Acceptance Scenarios

**Scenario 1: Successful bug creation**
- Given an authenticated Bug Management User with authorization for the DemoIntern project
- When they submit a valid `POST /api/projects/{projectId}/bugs` request with title, description, status, priority, severity, and reporter all valid
- Then exactly one `bug_records` entry is created, and the response contains the generated `bugId`, `projectId`, `reporter`, and `creationTimestamp`

**Scenario 2: Valid lookup-backed baseline fields persisted**
- Given a valid create request for the DemoIntern project
- When the bug is created
- Then the baseline fields title, description, status, priority, severity, and reporter are persisted using only values that exist in the configured status, priority, and severity lookup tables

**Scenario 3: Missing or invalid required field**
- Given a create request for the DemoIntern project
- When title, description, status, priority, severity, or reporter is missing, blank, or invalid
- Then the system rejects the request, returns field-level validation errors for the failing fields, and does not create a `bug_records` row

**Scenario 4: Invalid project scope**
- Given a create request whose `projectId` does not map to the DemoIntern project scope
- When the request is submitted
- Then the system blocks creation, returns a project-scope error response, and does not generate a bug identifier

**Scenario 5: Sanitization of unsafe content and retrieval**
- Given a create request containing title or description with markup or script input
- When the system processes the request
- Then it sanitizes the content before persistence, stores the sanitized values, and the created bug can subsequently be retrieved through `GET /api/projects/{projectId}/bugs/{bugId}` over authenticated TLS-protected access

## Traceability Matrix
| Source ID | Requirement | Acceptance Criteria | Test Coverage |
|---|---|---|---|
| US 91918-AC1 | Successful POST creates exactly one bug_records entry; response includes bugId, projectId, reporter, creationTimestamp | AC1 | Scenario 1 |
| US 91918-AC2 | Baseline fields persisted using only configured lookup-table values | AC2 | Scenario 2 |
| US 91918-AC3 | Missing/blank/invalid required fields rejected with field-level errors; no record created | AC3 | Scenario 3 |
| US 91918-AC4 | Invalid projectId blocks creation with project-scope error; no bugId generated | AC4 | Scenario 4 |
| US 91918-AC5 | Sanitization of unsafe title/description content; retrieval via GET over authenticated TLS | AC5 | Scenario 5 |

## Open Questions
- What are the exact field length limits for title and description ("overlength" is referenced but no numeric limit is specified)?
- What specific error response schema/status codes should be returned for validation failures and project-scope errors?
- Is there a defined UI (web/mobile) for bug creation, or is this feature purely an API/service capability with no first-party client described?
- What values/codes populate `bug_status_lookup`, `bug_priority_lookup`, and `bug_severity_lookup` for DemoIntern?
- Is the create operation idempotent, or can duplicate submissions create multiple records?
- What are the exact error responses/status codes for `GET /api/projects/{projectId}/bugs/{bugId}` when the bug or project is not found?
- What is the data retention policy for bug records?
- Are there additional actor roles (e.g., admin, auditor) with different permissions beyond the Bug Management User?
- What sanitization method/library or rule set should be applied to title/description content?

## Source References
- Feature ID: 91918 — "Edited As a Bug Management User, I want to record a bug for the DemoIntern project so that defects are captured with consistent and traceable information"
- User Story US 91918 (Acceptance Criteria 1–5)
- Derived Source Signal: Application Type — api-service