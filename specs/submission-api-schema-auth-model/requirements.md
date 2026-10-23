# Implementation Requirements Checklist

**Purpose**: Provide an implementation acceptance checklist that agents can execute one item at a time.  
**Feature**: [Planning] Define submission API schema, auth model, and persistence mapping

## Functional Acceptance Criteria

- [ ] [R1] An approved OpenAPI contract is published for the digital claim submission service with a defined endpoint path and HTTP method for claim submission
- [ ] [R2] The OpenAPI contract defines request payload fields for `policyId`, `insuredId`, `claimType`, `dateOfService`, `incidentDescription`, `totalClaimAmount`, `attachments[]`, and `clientRequestId`
- [ ] [R3] The OpenAPI contract defines response payload fields for `claimId`, `status`, `submittedAt`, and `correlationId`
- [ ] [R4] The OpenAPI contract defines error payloads for malformed request, authentication failure, authorization failure, duplicate request conflict where applicable, and validation failure
- [ ] [R5] Auth expectations are documented for both bearer-token and session-derived identity handling, or the unresolved auth-mode choice is held for clarification and not implemented as an assumption
- [ ] [R6] Authorization rules document how the authenticated policyholder identity is validated against the referenced `policyId`
- [ ] [R7] The status code matrix is published and covers `400` malformed JSON, `401` unauthenticated, `403` unauthorized policy access, `409` duplicate `clientRequestId` if idempotency is supported, and `422` field validation failures
- [ ] [R8] Persistence mapping is published for all source-supported fields across claim header, status, and metadata persistence targets
- [ ] [R9] The transaction boundary for successful claim creation is defined so claim header, initial status, and metadata persistence succeed or fail together
- [ ] [R10] Primary success path, unauthorized access path, malformed payload path, validation failure path, and duplicate `clientRequestId` path where applicable are documented for implementation

## UI Acceptance Criteria

- [ ] [R11] API-facing contract artifacts are documented in a form consumable by implementers without introducing unsupported UI scope
- [ ] [R12] Error payload shapes and field-level validation outputs are defined clearly enough for client applications to present actionable validation and access-denied states
- [ ] [R13] Existing API and documentation naming conventions are followed for insurance-domain terminology such as policyholder, insured person, claim, status, and correlation identifiers

## API and Integration Acceptance Criteria

- [ ] [R14] The submission operation defines request and response schemas, content types, required fields, optional fields, and field formats in the OpenAPI contract
- [ ] [R15] The API contract specifies authentication requirements and distinguishes unauthenticated (`401`) from authenticated-but-unauthorized (`403`) behavior
- [ ] [R16] The contract defines how policy access is checked against the referenced policy without inventing unsupported identity-provider or session-store implementation details
- [ ] [R17] Error responses are specified for `400`, `401`, `403`, `409` if applicable, and `422`, including observable conditions that trigger each status
- [ ] [R18] The duplicate `clientRequestId` behavior is specified only if the feature confirms idempotency support; otherwise this remains an open question and is not implemented as an assumption
- [ ] [R19] Correlation handling is defined for the submission response and any related logging or traceability expectations that apply to the API contract
- [ ] [R20] The API contract remains backward-compatible with existing consumers unless an explicit breaking change is required and approved
- [ ] [R21] No exact hostnames, infrastructure components, storage schemas, or integration endpoints absent from the source are invented in the contract or mapping

## Business Logic and Data Acceptance Criteria

- [ ] [R22] Validation rules are defined for all source-supported submission fields, including malformed JSON vs semantic field validation distinctions
- [ ] [R23] Field-to-persistence mapping identifies where `policyId`, `insuredId`, `claimType`, `dateOfService`, `incidentDescription`, `totalClaimAmount`, `attachments[]`, `clientRequestId`, `claimId`, `status`, `submittedAt`, and `correlationId` are stored across claim header, status, and metadata targets
- [ ] [R24] The initial claim status at creation is defined, or if unresolved it is held as an open question and not implemented as an assumption
- [ ] [R25] The persistence design defines how attachments metadata is represented in the mapping, or if attachment storage scope is unresolved it is held for clarification and not silently assumed
- [ ] [R26] The persistence design distinguishes claim header data from status-history/status-row data and metadata/audit-supporting data where source-supported
- [ ] [R27] Duplicate request handling via `clientRequestId` defines whether uniqueness is enforced and at what persistence boundary, or remains open if not decided
- [ ] [R28] Creation-time fields such as `submittedAt` and `correlationId` have a defined source of truth and persistence destination, or unresolved generation responsibility is held for clarification
- [ ] [R29] The mapping supports traceability and auditability expectations for insurance operations without introducing unrelated domain entities
- [ ] [R30] Privacy and data-minimization expectations are satisfied by limiting persisted data to the fields required by the submission contract and operational traceability

## Non-Functional Acceptance Criteria

- [ ] [R31] Strong authentication and authorization requirements are reflected in the auth model for claim submission policy access
- [ ] [R32] Encryption-in-transit expectations are satisfied for the submission API contract where this feature defines transport assumptions
- [ ] [R33] Auditability and traceability expectations are satisfied through documented correlation, identity binding, status creation, and persistence mapping decisions
- [ ] [R34] Observability expectations are supported through defined correlation behavior and error classification for operational troubleshooting
- [ ] [R35] Availability and resilience expectations are supported by a transaction boundary that prevents partial successful creation of claim header, status, and metadata records
- [ ] [R36] Golden Repo terminology is used where applicable, including policyholder, insured person, strong authentication and authorization, privacy and data minimization, audit logging, and observability (`01_business_context.md`)
- [ ] [R37] The implementation does not introduce an unrelated domain, workflow, or infrastructure assumption beyond the digital claim submission service scope
- [ ] [R38] Verification covers the highest-risk behaviors: policy access authorization, request validation classification, duplicate submission handling if applicable, and transactional persistence consistency

## Traceability

- [ ] [R39] Every implemented change maps back to the feature acceptance checklist items for OpenAPI schema, auth and authorization checks, and persistence mapping coverage
- [ ] [R40] Every implemented change maps back to the user story requirement for endpoint definition, schema definition, auth model, error matrix, persistence mapping, and transaction boundary
- [ ] [R41] Every non-blocking Open Question that was implemented has a recorded decision + one-line rationale in assumptions documentation (no Open Question is silently assumed)
- [ ] [R42] No BLOCKING Open Question was implemented as an assumption (for example: unresolved endpoint path/method choice, auth-mode selection, initial status definition, idempotency support for `clientRequestId`, or attachment persistence treatment must hold completion until clarified if required for implementation sign-off)

## Assumptions

### ASM-1
- [R43] Statement: The checklist treats this feature as producing implementation-ready API contract and persistence design artifacts rather than delivering a running submission endpoint.
- [R44] Source: Feature category is Planning; definition of done requires approved OpenAPI contract, field-to-table mapping, auth decision points, and status code matrix published for implementation.
- [R45] Rationale: The source emphasizes definition and publication of backend contract decisions, not endpoint coding.
- [R46] Potential impact: If the intended scope includes executable endpoint implementation, this checklist may understate runtime delivery tasks.
- [R47] Confidence: high
- [R48] Recommended resolution: Confirm whether completion is limited to design artifacts or includes coded implementation in this feature.

## Notes

- [R49] Never resolve an Open Question silently. In an unattended run, record the chosen assumption + rationale in assumptions documentation; blocking questions must instead hold the feature at needs-clarification.
- [R50] Mark an item complete only after verifying actual implementation code and behavior.