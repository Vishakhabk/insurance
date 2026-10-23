# Feature: Digital claim intake
Status: NEW
Owner: Astra
Last Updated: 2026-10-23

## Summary
Digital claim intake enables a policyholder or claim intake user to submit a health insurance claim digitally as part of the claims journey. The feature supports digitizing and standardizing claims initiation, reducing manual processing and turnaround time, improving customer and operations experience, and providing traceability and auditability for claim submission activities.

In the insurance domain, this feature corresponds to the initial claim notification and submission stage within claims processing. The source terminology aligns this stage with claim intake and FNOL (First Notice of Loss), followed by downstream claims processes such as claim validation, coverage verification, document collection, fraud screening, assessment, settlement, and closure.

## Scope
### In Scope
- Digital submission of a health insurance claim.
- Claim submission initiated by:
  - Policyholder
  - Claim intake user
- Use of the feature as part of the claims journey.
- Creation or capture of a claim record as part of intake.
- Support for digital claims process standardization and reduction of manual handling.
- Auditability and traceability of intake activity.
- Security and privacy controls applicable to claim submission.
- Validation of submitted input before intake is accepted.

### Out of Scope
- Claim validation rules after intake completion.
- Coverage verification behavior.
- Document collection workflows beyond the fact that claim-related documents exist in the domain model.
- Surveyor or assessor assignment.
- Fraud screening.
- Reserve creation.
- Approval, repudiation, settlement, recovery, subrogation, closure, or reopening.
- Complaint handling.
- Regulatory reporting.
- Exact integration flows, notifications, API endpoints, UI screens, and storage schemas not specified in the source.

## Application Type & Platform Context
Application type is not explicitly specified in the feature source.

Relevant platform context from the selected references indicates that the broader insurance platform supports:
- Web and mobile channels
- REST/JSON for synchronous interactions
- Claims service
- Document management service
- Identity and access management
- Audit and observability platform

Because the feature source does not state the delivery channel for digital claim intake, the specific platform target for this feature remains unresolved.

**Open Question**
- Is digital claim intake required on web, mobile, both, internal operations UI, API-only, or a combination of these channels?

## Actors and Permissions
### Actors
- Policyholder
- Claim intake user

### Related domain actors referenced in platform context
These roles exist in the broader insurance operating model but are not explicitly granted access to this feature by the source:
- Customer service representative
- Claims assessor
- Claims investigator
- Administrator
- Auditor
- Compliance officer

### Permissions and Access Constraints
- Policyholders must be able to submit a health insurance claim digitally.
- Claim intake users must be able to submit a health insurance claim digitally on behalf of intake operations or as part of the claims journey.
- Access must be governed by strong authentication and authorization.
- Access control must follow least privilege and role-based access control.
- Privileged and sensitive actions require MFA where applicable under platform standards.
- Claim intake actions must be auditable.

### Permission Gaps
The source does not define:
- Whether claim intake users can submit claims only for identified policyholders or also create intake without prior policy lookup.
- Whether policyholders can submit claims only for themselves or also for insured persons on their policy.
- Whether customer service representatives are included within “claim intake user.”
- Whether anonymous or unauthenticated submission is permitted.

## Feature Development Intent
This is feature-development work to introduce or enable digital submission of health insurance claims by policyholders and claim intake users. The behavior to be delivered is the ability to capture claim intake through a digital channel instead of relying solely on manual intake.

The intended outcome is:
- A claim can be submitted digitally as the first step in the claims journey.
- Intake data is captured in a structured and auditable form.
- The system supports standardization of claim intake for subsequent downstream claims processing.
- The feature reduces manual processing effort and turnaround time while improving claimant and operations experience.

## UI Design & Interaction Contract
The source does not define specific screens, page layouts, form composition, navigation model, copy, or visual design.

The following UI interaction expectations are supported by the source and applicable references:
- The feature must provide a digital interaction through which a policyholder or claim intake user can submit a health insurance claim.
- The interaction must support accessibility.
- The interaction must apply strong authentication and authorization where required by actor role and channel.
- Submitted data must be validated before intake is accepted.
- Sensitive data handling must be privacy-aware.
- Auditability must be preserved for user actions related to submission.
- Localization and timezone support must be considered where applicable to the selected channel.

Because no explicit screen design is provided, the UI contract is limited to behavioral requirements rather than layout requirements.

**Open Questions**
- What screens or steps are required for claim intake?
- What data fields must be shown to the submitting actor?
- Are supporting documents uploaded during intake or in a later document collection step?
- Is draft save/resume required?
- What confirmation message, reference number, or next-step guidance must be shown after submission?
- What inline validation messages and error copy are required?
- Is the experience different for policyholders versus claim intake users?

## API Contract
The source does not specify concrete APIs, endpoints, methods, payload schemas, response schemas, or error models.

Applicable contract-level constraints from the selected references:
- Synchronous interactions in the platform use REST/JSON.
- Correlation IDs are required across services.
- Input validation and output encoding are required.
- Role-based access control must apply.
- PII must be masked in logs.
- Audit records must be immutable.
- Retry, timeout, circuit breaker, and dead-letter handling are architectural patterns in the broader platform context, but no specific integration flow is defined for this feature.
- Events may be used for lifecycle changes in the broader platform, but no event is specified for claim intake in the source.
- Idempotency keys are specifically stated for payment and document operations; no source-supported idempotency requirement is stated for claim intake submission.

### Supported API Expectations
If this feature is implemented via API:
- It must accept a digital claim submission for a health insurance claim.
- It must enforce authentication and authorization according to actor role.
- It must validate input before creating or accepting claim intake.
- It must capture or propagate a correlation ID for traceability.
- It must avoid exposing sensitive data in logs.
- It must create auditable records for submission activity.

**Open Questions**
- What API operation(s) are required for digital claim intake?
- What request fields are mandatory, optional, conditional, or prohibited?
- What response payload is required on successful submission?
- What error codes and validation response format are required?
- Is claim submission required to be idempotent?
- Are documents submitted in the same API operation or separately?
- Are downstream claim lifecycle events emitted when intake is created?

## Business Logic & Rules
- A health insurance claim must be capable of being submitted digitally.
- Submission may be performed by either a policyholder or a claim intake user.
- Digital claim intake is part of the claims journey and aligns to the FNOL/intake stage.
- Claim intake precedes downstream claims processes such as validation, coverage verification, document collection, fraud screening, assessment, settlement, and closure.
- Access to submit claims must use strong authentication and authorization.
- Permissions must follow least privilege and role-based access control.
- Claim intake processing must preserve traceability and auditability.
- Input data must be validated before submission is accepted.
- Sensitive data must be handled with privacy and data minimization principles.
- Data in transit and at rest must be protected by encryption.
- Logs must mask PII.
- Audit records must be immutable.
- The feature must support standardization of insurance journeys and reduction of manual processing.

### Business Rule Gaps
The source does not define:
- Eligibility rules for who may submit against a given policy.
- Whether policy existence or active coverage must be verified during intake.
- Required business states for a newly submitted claim.
- Whether duplicate claim detection occurs during intake.
- Whether claim reference generation occurs at intake.
- Whether health claim type, jurisdiction, product, or channel-specific rules apply at submission.
- Whether documents are mandatory at submission.

## Data Model & Validation
### Source-Supported Entities
- Claim
- Claim document
- Policy
- Policyholder
- Insured
- Audit event
- Party / Customer, where relevant to actor identity

### Data Model Constraints
- Use immutable identifiers.
- Record source system and data ownership.
- Define required, optional, conditional, and prohibited fields.
- Store currency and timezone explicitly where applicable.
- Apply retention, archival, masking, and deletion rules by data category.
- Audit records must be immutable.

### Validation Expectations
- Input validation is required.
- Sensitive data handling must be explicit and privacy-aware.
- Data minimization applies.
- Actor, timestamp, and correlation ID must be captured where relevant for auditable actions.
- Before/after values and reason are required when relevant by the requirements quality rules; for a new submission, this implies at minimum auditable creation context rather than update deltas unless later business flow requires modification tracking.

### Data Elements Not Defined by Source
The source does not specify the claim intake fields. Examples such as diagnosis, treatment date, provider, amount claimed, policy number, insured person, event date, or attachments are not stated and therefore are not defined by this specification.

**Open Questions**
- What exact claim intake fields are required for a health insurance claim?
- Which fields are mandatory versus optional or conditional?
- Must the claim reference be generated at submission?
- Must policy, insured person, and claimant be selected or derived?
- Are claim documents part of initial submission data or a separate linked process?
- What retention and deletion policy applies to claim intake and claim documents for this feature?
- What timezone value must govern intake timestamps in user display and storage?

## Functional Requirements
- [FR1] **FR-001** The system shall allow a policyholder to submit a health insurance claim digitally.
- [FR2] **FR-002** The system shall allow a claim intake user to submit a health insurance claim digitally.
- [FR3] **FR-003** The system shall record each accepted digital claim submission as a claim intake within the claims journey.
- [FR4] **FR-004** The system shall validate submitted input before accepting a digital claim submission.
- [FR5] **FR-005** The system shall reject or prevent acceptance of a digital claim submission when required input validation fails.
- [FR6] **FR-006** The system shall enforce authentication and authorization for digital claim submission according to actor role and least-privilege access rules.
- [FR7] **FR-007** The system shall create an auditable record for digital claim submission activity.
- [FR8] **FR-008** The system shall capture or propagate a correlation ID for digital claim submission processing where the feature uses service interactions.
- [FR9] **FR-009** The system shall protect claim intake data using encryption in transit and at rest in accordance with platform standards.
- [FR10] **FR-010** The system shall mask PII in logs related to digital claim intake.
- [FR11] **FR-011** The system shall support accessibility requirements applicable to the selected digital channel.
- [FR12] **FR-012** The system shall support localization and timezone handling applicable to the selected digital channel and claim intake data where used.
- [FR13] **FR-013** The system shall record source system and data ownership for claim intake data.
- [FR14] **FR-014** The system shall use immutable identifiers for claim intake records and related audit records.
- [FR15] **FR-015** The system shall apply retention, archival, masking, and deletion rules to claim intake data categories according to enterprise policy.
- [FR16] **FR-016** The system shall maintain immutable audit records for digital claim submission actions.

### Validate / Pending Product Decision
- [FR17] **FR-017** Validate whether digital claim intake must also support claim document submission at the time of intake.
- [FR18] **FR-018** Validate whether the system must return a user-visible claim reference or confirmation artifact immediately upon successful submission.
- [FR19] **FR-019** Validate whether claim submission must verify policy or coverage eligibility during intake.
- [FR20] **FR-020** Validate whether separate submission permissions are required for self-service policyholder use versus internal claim intake operations use.

## Non-Functional Requirements
- The feature must support availability and resilience consistent with platform standards.
- The feature must enforce privacy and data minimization.
- The feature must enforce strong authentication and authorization.
- The feature must encrypt data in transit and at rest.
- The feature must maintain audit logging.
- The feature must support accessibility.
- The feature must support localization and timezone handling where applicable.
- The feature must support disaster recovery and business continuity consistent with platform standards.
- The feature must support observability and operational support.
- The feature must use input validation and output encoding.
- The feature must use role-based access control and least privilege.
- The feature must apply MFA for privileged and sensitive actions where applicable.
- The feature must ensure immutable audit records.
- The feature must mask PII in logs.
- The feature must remain traceable from requirement to business objective, rule, data, API, and test.

## Acceptance Scenarios
### Scenario 1: Policyholder submits a digital health insurance claim successfully
**Given** a policyholder has access to the digital claim intake feature  
**And** the policyholder is authenticated and authorized to submit a claim  
**When** the policyholder submits a valid health insurance claim digitally  
**Then** the system accepts the submission  
**And** records the submission as a claim intake in the claims journey  
**And** creates an immutable audit record for the submission activity

### Scenario 2: Claim intake user submits a digital health insurance claim successfully
**Given** a claim intake user has access to the digital claim intake feature  
**And** the claim intake user is authenticated and authorized to submit a claim  
**When** the claim intake user submits a valid health insurance claim digitally  
**Then** the system accepts the submission  
**And** records the submission as a claim intake in the claims journey  
**And** creates an immutable audit record for the submission activity

### Scenario 3: Submission fails validation
**Given** an authenticated and authorized submitting actor is using the digital claim intake feature  
**When** the actor submits claim intake data that fails validation  
**Then** the system does not accept the digital claim submission  
**And** the invalid submission is not recorded as an accepted claim intake

### Scenario 4: Unauthorized actor attempts claim submission
**Given** an actor does not have permission to submit a health insurance claim digitally  
**When** the actor attempts to use the digital claim intake feature  
**Then** the system denies the submission attempt  
**And** does not create an accepted claim intake record

### Scenario 5: Claim intake activity is traceable
**Given** a digital claim submission is processed through the system  
**When** the submission is accepted  
**Then** the processing flow includes auditable submission records  
**And** a correlation ID is captured or propagated where service interactions are involved  
**And** PII is masked in logs

### Scenario 6: Sensitive claim intake data is protected
**Given** a digital health insurance claim is submitted  
**When** the system stores or transmits claim intake data  
**Then** the data is protected using encryption in transit and at rest  
**And** handling of submitted data follows privacy and data minimization requirements

## Traceability Matrix
| Source ID | Requirement | Acceptance Criteria | Test Coverage |
|---|---|---|---|
| Feature 29401 / US QA231026-3 | FR-001 | Policyholder can submit a health insurance claim digitally | Functional test: policyholder valid submission accepted |
| Feature 29401 / US QA231026-3 | FR-002 | Claim intake user can submit a health insurance claim digitally | Functional test: claim intake user valid submission accepted |
| Feature 29401 / US QA231026-3; 03_process_catalog.md #2 | FR-003 | Accepted submission is recorded as claim intake within the claims journey | Integration/functional test: accepted submission creates claim intake record |
| Feature 29401 / US QA231026-3; 05_reference_architecture.md #0 | FR-004 | Submitted input is validated before acceptance | Negative/positive validation tests |
| Feature 29401 / US QA231026-3; 05_reference_architecture.md #0 | FR-005 | Invalid input is rejected and not accepted as claim intake | Negative test: invalid submission blocked |
| Feature 29401 / US QA231026-3; 01_business_context.md #0; 05_reference_architecture.md #0 | FR-006 | Only authenticated and authorized actors can submit claims digitally | Security test: unauthorized/forbidden access denied |
| Feature 29401 / US QA231026-3; 01_business_context.md #0; 04_requirements_quality_rules.md #0 | FR-007 | Submission activity creates an auditable record | Audit test: audit event exists with actor and timestamp |
| 05_reference_architecture.md #0; 04_requirements_quality_rules.md #0 | FR-008 | Correlation ID is captured or propagated for service interactions | Integration/observability test: correlation ID present in processing trace |
| 01_business_context.md #0 | FR-009 | Claim intake data is encrypted in transit and at rest | Security/configuration verification |
| 05_reference_architecture.md #0 | FR-010 | PII is masked in logs for claim intake processing | Logging test: PII absent or masked in logs |
| 01_business_context.md #0 | FR-011 | Digital claim intake supports accessibility requirements of the selected channel | Accessibility test for implemented channel |
| 01_business_context.md #0; 06_data_model_reference.md #0 | FR-012 | Localization/timezone handling is supported where applicable | Functional test: timezone/localization behavior meets configured expectations |
| 06_data_model_reference.md #0 | FR-013 | Source system and data ownership are recorded for claim intake data | Data test: stored record contains ownership metadata |
| 06_data_model_reference.md #0 | FR-014 | Immutable identifiers are used for claim intake and audit records | Data integrity test: identifiers are stable and immutable |
| 06_data_model_reference.md #0; 05_reference_architecture.md #0 | FR-015 | Retention, archival, masking, and deletion rules apply by data category | Policy/configuration verification |
| 05_reference_architecture.md #0 | FR-016 | Audit records for submission are immutable | Audit integrity test: records cannot be altered through supported flows |
| Feature 29401 / US QA231026-3; 06_data_model_reference.md #0 | FR-017 | Validate whether claim documents are included in initial intake | Pending product decision / no test until clarified |
| Feature 29401 / US QA231026-3 | FR-018 | Validate whether user-visible confirmation or claim reference is required | Pending product decision / no test until clarified |
| Feature 29401 / US QA231026-3; 03_process_catalog.md #2 | FR-019 | Validate whether policy or coverage eligibility is checked during intake | Pending product decision / no test until clarified |
| Feature 29401 / US QA231026-3; 01_business_context.md #0 | FR-020 | Validate whether policyholder and claim intake user require distinct permission scopes | Pending product decision / no test until clarified |

## Open Questions
1. What application channels are required for this feature: web, mobile, internal user interface, API-only, or multiple channels?
2. What exact claim intake data fields are required for a health insurance claim submission?
3. Which claim intake fields are required, optional, conditional, or prohibited?
4. Must a policyholder be restricted to submitting claims only for themselves, or can they submit for any insured person covered under their policy?
5. What exactly qualifies as a “claim intake user” role, and which internal roles are included?
6. Is anonymous or unauthenticated claim intake allowed for any channel or actor type?
7. Must the system verify policy existence or coverage eligibility during digital intake?
8. Must supporting claim documents be uploaded as part of initial submission, or is that handled later in document collection?
9. Is a draft, save-and-resume, or partially completed intake flow required?
10. What confirmation artifact is required after successful submission: acknowledgement only, claim reference, receipt, or next-step instructions?
11. What validation error messages and user-facing copy are required?
12. What are the required state values for a newly submitted claim intake record?
13. Is duplicate claim detection required at intake time?
14. Are there product-, jurisdiction-, channel-, or effective-date-specific claim intake rules that must be configurable?
15. What retention and deletion policy applies specifically to claim data and claim documents for this feature?
16. Is idempotency required for digital claim submission?
17. Are lifecycle events emitted when a claim intake is created?
18. Are notifications sent after submission, and if so, to whom and through which channels?
19. Are there distinct UI or workflow differences between policyholder self-service and internal claim intake user submission?
20. What timezone should govern display and storage of intake timestamps in the selected channel?

## Source References
- Feature ID: 29401
- Feature Reference: QA231026-3
- Feature Title: Digital claim intake
- User Story: US QA231026-3 — Digital claim intake
- Golden Repo:
  - `01_business_context.md`
  - `02_domain_glossary.md`
  - `03_process_catalog.md`
  - `04_requirements_quality_rules.md`
  - `05_reference_architecture.md`
  - `06_data_model_reference.md`