# Feature: [Planning] Define submission API schema, auth model, and persistence mapping
Status: NEW
Owner: Astra
Last Updated: 2026-10-23

## Summary
This feature defines the backend contract for a digital claim submission service in a monolith architecture. The work is planning-focused and must produce an implementation-ready contract covering the submission API schema, authentication and authorization expectations, persistence mapping, and error handling behavior.

The feature solves the need for a clear, approved contract before implementation so teams can build claim submission consistently and with traceability. The expected outcome is published documentation for:
- the submission endpoint path and HTTP method,
- request, response, and error payload schemas,
- authentication and policy-access authorization decision points,
- field-to-persistence mapping for claim header, status, and metadata storage,
- transaction boundary requirements for successful creation,
- and the status code/error matrix.

This supports digitized and standardized insurance journeys while ensuring traceability, auditability, and strong authentication and authorization expectations for insurance operations.

## Scope
In scope:
- Definition of a backend API contract for digital claim submission.
- Documentation of:
  - endpoint path,
  - HTTP method,
  - request schema,
  - response schema,
  - error payloads.
- Inclusion of the following source-identified fields in the contract and/or persistence mapping:
  - `policyId`
  - `insuredId`
  - `claimType`
  - `dateOfService`
  - `incidentDescription`
  - `totalClaimAmount`
  - `attachments[]`
  - `clientRequestId`
  - `claimId`
  - `status`
  - `submittedAt`
  - `correlationId`
- Documentation of auth expectations for:
  - bearer token identity, or
  - session-derived user identity.
- Documentation of how policyholder identity is bound to the referenced policy for authorization.
- Error matrix covering:
  - `400` malformed JSON,
  - `401` unauthenticated,
  - `403` unauthorized policy access,
  - `409` duplicate `clientRequestId` if applicable,
  - `422` field validation failures.
- Persistence mapping to claim header, status, and metadata tables.
- Definition of the transaction boundary required for successful claim creation.
- Production of approved planning artifacts: OpenAPI contract, field-to-table mapping, auth decision points, and status code matrix.

Out of scope:
- Implementation of the API.
- UI screens or user-facing interface design.
- Definition of infrastructure details not supplied by the source.
- Exact database schema beyond mapping fields to the identified persistence targets.
- Downstream claim workflow after submission beyond initial created status/metadata capture.
- Attachment binary storage mechanism, upload transport, or storage service specifics.
- Analytics, queues, jobs, or notifications.

## Application Type & Platform Context
Application type: API/service.

Source evidence:
- “Define the backend contract for the digital claim submission service.”
- “Specify endpoint path, HTTP method, request schema, response schema...”
- Derived Source Signal: `api-service`.

Architecture context:
- User-selected architecture style: monolith.

Platform details not otherwise specified in source. No mobile, web, or desktop client behavior is defined by this feature.

## Actors and Permissions
Supported actors from source context and Golden Repo business context:
- Customer / policyholder
- Insured person

System/API access model supported by source:
- Authenticated caller using a bearer token, or
- Authenticated caller using session-derived user identity.

Permissions and access constraints explicitly supported:
- The API must distinguish unauthenticated access (`401`) from authenticated but unauthorized policy access (`403`).
- Authorization must validate how the policyholder identity is bound to the referenced `policyId`.
- A caller must not be allowed to submit a claim against a policy they are not authorized to access.

Permissions not fully defined by source:
- Whether non-policyholder roles such as agent, broker, or customer service representative may submit claims.
- Whether `insuredId` must match the authenticated identity, be associated with the policy, or may differ under allowed business scenarios.

These gaps are listed under Open Questions.

## Feature Development Intent
This is feature-development planning work intended to define the implementation contract for claim submission before coding begins. The feature must establish the authoritative API and persistence behavior so engineering can implement submission consistently and testers can verify behavior against a published contract.

The delivered outcome must include:
- an approved OpenAPI contract,
- documented auth and authorization decision points tied to policy access,
- field-to-table persistence mapping across claim header, status, and metadata tables,
- and a published status code matrix.

The behavior being defined is the creation of a new digital claim submission with contractually specified input, output, validation, authorization, error handling, and persistence atomicity requirements.

## UI Design & Interaction Contract
No end-user UI is defined in the source context for this feature.

UI-related contract for this planning feature is limited to documentation outputs:
- OpenAPI schema must define request, response, and error payloads.
- Auth and authorization checks must be documented against policy access.
- Persistence mapping must cover claim, status, and metadata fields.
- Status code matrix must be published for implementation.

Open Question:
- Where these planning artifacts will be published and what approval workflow is required beyond “approved” is not specified.

## API Contract
The feature requires a defined submission API contract for creating a digital claim.

### Operation
The following contract elements must be defined:
- Endpoint path: Validate exact path.
- HTTP method: Validate exact method.
- Purpose: submit a new digital claim.

Because the exact path and method are explicitly required by the source but not provided, they must be finalized during planning and published in the approved OpenAPI contract.

### Request Schema
The request schema must account for the following source-specified fields where applicable to claim submission input:
- `policyId`
- `insuredId`
- `claimType`
- `dateOfService`
- `incidentDescription`
- `totalClaimAmount`
- `attachments[]`
- `clientRequestId`

Open Questions remain for which of these fields are required vs optional and detailed field formats, except where validation outcomes are required via `422`.

### Response Schema
The response schema must account for the following source-specified output fields where applicable:
- `claimId`
- `status`
- `submittedAt`
- `correlationId`

The OpenAPI contract must define the success response payload shape and the error payload shapes.

### Error Contract
The error matrix must include, at minimum:
- `400` for malformed JSON.
- `401` for unauthenticated requests.
- `403` for unauthorized policy access.
- `409` for duplicate `clientRequestId`, if idempotency/deduplication is applicable.
- `422` for field validation failures.

The OpenAPI contract must define error payloads for these statuses.

### Authentication Contract
The API must document authentication expectations for:
- bearer token-based identity, or
- session-derived user identity.

The contract must specify how authenticated identity is obtained for authorization decisions, but the source does not determine whether both modes are required in the same deployment or whether one is conditional.

### Authorization Contract
The API must document authorization checks against policy access, including:
- how policyholder identity is bound to the referenced `policyId`,
- and how unauthorized policy access results in `403`.

### Idempotency / Duplicate Detection
The source requires handling `409 duplicate clientRequestId if applicable`.
Therefore:
- the contract must explicitly state whether `clientRequestId` is used for duplicate detection,
- and if so, define duplicate behavior as `409`.
- if not applicable, the contract must explicitly state that the duplicate case does not apply.

### Integration Behavior
No downstream integrations are defined in the source. No queues, events, notifications, or external services may be specified in this contract unless separately decided.

## Business Logic & Rules
- A claim submission contract must be defined for creating a new claim record set.
- Submission must evaluate authentication before authorization.
- If the caller is not authenticated, the API returns `401`.
- If the caller is authenticated but does not have access to the referenced policy, the API returns `403`.
- Malformed JSON must produce `400`.
- Field validation failures must produce `422`.
- Duplicate `clientRequestId` must produce `409` if duplicate detection is part of the approved contract.
- Persistence mapping must cover:
  - claim header data,
  - claim status data,
  - claim metadata data.
- Successful creation must occur within a defined transaction boundary.
- The transaction boundary must ensure successful creation is treated atomically across the required persistence targets for claim header, status, and metadata.
- The source requires `status` and `submittedAt` in the contract output or mapped fields, but does not define the initial status value; this remains an Open Question unless decided during planning.
- The contract must include `correlationId` in the response schema, but the source does not define its generation source or propagation rules.

Golden Repo-aligned rules applicable to this feature:
- The design should support traceability and auditability.
- Strong authentication and authorization are required considerations.
- Privacy and data minimization apply to defined request/response and persistence content.
- Observability and operational support expectations make `correlationId` materially relevant to the contract.
- Encryption in transit and at rest is a relevant non-functional expectation for claim data handling.

## Data Model & Validation
The feature requires field-to-persistence mapping for source-named fields and persistence targets.

### Entities / Persistence Targets
The following persistence targets are explicitly in scope:
- claim header table
- status table
- metadata table

Exact table names, schemas, keys, and relational structure are not provided and must not be invented.

### Source-Specified Fields
The contract and mapping must address:
- `policyId`
- `insuredId`
- `claimType`
- `dateOfService`
- `incidentDescription`
- `totalClaimAmount`
- `attachments[]`
- `clientRequestId`
- `claimId`
- `status`
- `submittedAt`
- `correlationId`

### Validation
The source establishes validation outcome categories but not detailed validation rules. Required validation coverage includes:
- malformed JSON -> `400`
- field validation failures -> `422`

Validation rules that must be defined in the approved contract or mapping, but are not fully specified in source:
- required vs optional fields,
- field formats,
- max lengths,
- enum/reference values for `claimType` and `status`,
- date format and timezone handling for `dateOfService` and `submittedAt`,
- currency/precision rules for `totalClaimAmount`,
- allowable structure for `attachments[]`,
- uniqueness scope and retention for `clientRequestId`.

### Data Quality and Policy Constraints
- Authorization logic must validate the relationship between authenticated identity and `policyId`.
- The relationship between `insuredId` and `policyId` must be clarified if enforced.
- Privacy and data minimization should govern schema and persistence design; no unnecessary personal data should be added beyond source-supported fields.

## Functional Requirements
FR-1. The feature shall define an approved OpenAPI contract for the digital claim submission API.

FR-2. The OpenAPI contract shall define the submission endpoint path.

FR-3. The OpenAPI contract shall define the HTTP method for submission.

FR-4. The OpenAPI contract shall define the request payload schema for claim submission.

FR-5. The request schema shall address the following input fields: `policyId`, `insuredId`, `claimType`, `dateOfService`, `incidentDescription`, `totalClaimAmount`, `attachments[]`, and `clientRequestId`.

FR-6. The OpenAPI contract shall define the success response payload schema.

FR-7. The success response schema shall address the following fields: `claimId`, `status`, `submittedAt`, and `correlationId`.

FR-8. The OpenAPI contract shall define error payload schemas for supported error responses.

FR-9. The API contract shall document authentication expectations for bearer token identity and/or session-derived user identity.

FR-10. The API contract shall document authorization checks that bind authenticated policyholder identity to the referenced `policyId`.

FR-11. The API contract shall define `400` handling for malformed JSON.

FR-12. The API contract shall define `401` handling for unauthenticated requests.

FR-13. The API contract shall define `403` handling for authenticated requests with unauthorized policy access.

FR-14. The API contract shall define `422` handling for field validation failures.

FR-15. The API contract shall explicitly state whether duplicate `clientRequestId` handling applies.

FR-16. If duplicate `clientRequestId` handling applies, the API contract shall define `409` behavior for duplicate submission attempts.

FR-17. The planning output shall define field-to-table persistence mapping for claim header fields.

FR-18. The planning output shall define field-to-table persistence mapping for claim status fields.

FR-19. The planning output shall define field-to-table persistence mapping for claim metadata fields.

FR-20. The persistence mapping shall include the source-specified fields where applicable: `policyId`, `insuredId`, `claimType`, `dateOfService`, `incidentDescription`, `totalClaimAmount`, `attachments[]`, `clientRequestId`, `claimId`, `status`, `submittedAt`, and `correlationId`.

FR-21. The planning output shall define the transaction boundary required for successful claim creation.

FR-22. The transaction boundary definition shall ensure atomic successful creation across claim header, status, and metadata persistence targets.

FR-23. The published planning output shall include an error/status code matrix covering `400`, `401`, `403`, `422`, and `409` where applicable.

FR-24. The published planning output shall include documented auth decision points.

FR-25. The published planning output shall be sufficient for implementation approval as defined by the feature definition of done.

## Non-Functional Requirements
- Strong authentication and authorization expectations shall be reflected in the contract documentation.  
- The design shall support traceability and auditability for claim submission decisions and persisted records.  
- The contract and persistence mapping shall align to privacy and data minimization principles.  
- Claim submission data shall be protected by encryption in transit and at rest, consistent with Golden Repo expectations.  
- The contract shall support observability and operational support through inclusion of `correlationId` in the response schema.  
- Availability and resilience are relevant platform expectations, but no measurable SLA/SLO is specified in the source.
- Localization and timezone support may affect date/time fields such as `dateOfService` and `submittedAt`, but exact requirements are not specified and require clarification.

## Acceptance Scenarios
### Scenario 1: OpenAPI contract defines claim submission request and response
Given Feature 29426 is in planning scope  
When the submission API contract is published  
Then it defines an endpoint path and HTTP method for digital claim submission  
And it defines request payload schema elements for `policyId`, `insuredId`, `claimType`, `dateOfService`, `incidentDescription`, `totalClaimAmount`, `attachments[]`, and `clientRequestId`  
And it defines success response schema elements for `claimId`, `status`, `submittedAt`, and `correlationId`

### Scenario 2: Error payloads and status code matrix are documented
Given the planning output for the submission API  
When error handling is reviewed  
Then the published contract includes error payload definitions  
And the status code matrix includes `400` for malformed JSON  
And `401` for unauthenticated requests  
And `403` for unauthorized policy access  
And `422` for field validation failures  
And `409` for duplicate `clientRequestId` if applicable

### Scenario 3: Authentication and policy authorization are documented
Given the submission API requires authenticated access  
When auth behavior is reviewed  
Then the contract documents bearer token identity and/or session-derived user identity expectations  
And it documents how authenticated identity is evaluated against the referenced policy  
And it distinguishes unauthenticated access from unauthorized policy access

### Scenario 4: Persistence mapping covers required storage targets
Given the planning output includes persistence documentation  
When field-to-table mapping is reviewed  
Then the mapping covers claim header storage  
And status storage  
And metadata storage  
And it maps the source-specified claim submission fields to those targets where applicable

### Scenario 5: Transaction boundary for successful creation is defined
Given a claim submission creates multiple persisted records  
When the persistence contract is reviewed  
Then it defines the transaction boundary for successful creation  
And successful creation is treated as atomic across claim header, status, and metadata persistence targets

### Scenario 6: Duplicate clientRequestId behavior is explicitly decided
Given `clientRequestId` is included in the submission field list  
When idempotency or duplicate handling is reviewed  
Then the published contract explicitly states whether duplicate detection applies  
And if it applies, duplicate `clientRequestId` returns `409`

### Scenario 7: Validation failures are separated from malformed JSON
Given a claim submission request is processed against the contract  
When the request body is malformed JSON  
Then the documented error response is `400`  
When the JSON is well formed but field validation fails  
Then the documented error response is `422`

## Traceability Matrix
| Source ID | Requirement | Acceptance Criteria | Test Coverage |
|---|---|---|---|
| Feature 29426 / US QA231026-28 | FR-1, FR-2, FR-3, FR-4, FR-5, FR-6, FR-7 | OpenAPI schema defines request, response, and error payloads | Review approved OpenAPI document for endpoint, method, request fields, response fields |
| Feature 29426 / US QA231026-28 | FR-8, FR-11, FR-12, FR-13, FR-14, FR-15, FR-16, FR-23 | OpenAPI schema defines error payloads; status code matrix published for implementation | Review error payload schemas and status code matrix for 400/401/403/409-if-applicable/422 |
| Feature 29426 / US QA231026-28 | FR-9, FR-10, FR-24 | Auth and authorization checks are documented against policy access | Review auth model documentation for bearer/session identity and policy binding decision points |
| Feature 29426 / US QA231026-28 | FR-17, FR-18, FR-19, FR-20 | Persistence mapping covers claim, status, and metadata fields | Review field-to-table mapping against required persistence targets and named fields |
| Feature 29426 / US QA231026-28 | FR-21, FR-22 | Definition of transaction boundary required for successful creation | Review persistence design document for atomic creation boundary across claim header, status, metadata |
| Feature 29426 / US QA231026-28 | FR-25 | Definition of done: approved OpenAPI contract, field-to-table mapping, auth decision points, and status code matrix published for implementation | Confirm all required planning artifacts are approved and published |
| 01_business_context.md #0 | NFR: traceability, auditability, strong authentication and authorization, privacy and data minimization, encryption, observability | Applicable Golden Repo operational and security expectations reflected in contract planning | Review spec for inclusion of applicable business-context constraints |

## Open Questions
1. What is the exact endpoint path for the claim submission API?
2. What is the HTTP method for claim submission?
3. Which request fields are mandatory versus optional?
4. What are the exact data types, formats, length limits, and validation rules for each field?
5. What is the canonical schema for `attachments[]`? For example, does it contain file references, metadata objects, or uploaded content descriptors?
6. What is the initial `status` value assigned on successful submission?
7. What are the allowed values for `claimType` and `status`?
8. What is the exact format for `dateOfService` and `submittedAt`, including timezone handling expectations?
9. What currency and decimal precision rules apply to `totalClaimAmount`?
10. Is `clientRequestId` required on every submission?
11. Is duplicate detection by `clientRequestId` mandatory, optional, or environment-dependent?
12. If `clientRequestId` duplicate detection applies, what is the uniqueness scope and retention window?
13. Are both bearer token and session-derived identity supported simultaneously, or is one selected per channel/deployment?
14. Which actors beyond customer/policyholder are allowed to submit claims, if any?
15. Must `insuredId` belong to the referenced policy, and must it match the authenticated user or merely be associated with the policy?
16. What exact policy-binding rule determines authorized access for `403` decisions?
17. What is the exact error payload structure for each status code?
18. What is the success status code for a successful submission?
19. Are `claimId`, `submittedAt`, and `correlationId` system-generated, and if so, by what governing rules?
20. What are the exact claim header, status, and metadata table names and key relationships?
21. How should `attachments[]` be persisted across claim header, status, and metadata targets, if at all?
22. What audit logging requirements must accompany submission creation and authorization decisions?
23. Where must the approved OpenAPI contract, mapping, and status matrix be published for implementation consumption?
24. Are there any localization, jurisdiction, or currency rules that materially affect claim submission validation for this feature?

## Source References
- Feature ID 29426
- Feature Reference QA231026-28
- User Story: US QA231026-28 — [Planning] Define submission API schema, auth model, and persistence mapping
- Acceptance checklist:
  - OpenAPI schema defines request, response, and error payloads
  - Auth and authorization checks are documented against policy access
  - Persistence mapping covers claim, status, and metadata fields
- Golden Repo:
  - `01_business_context.md` (Introduction)