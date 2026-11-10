# Feature: Premium Eligibility Decisioning for Age and Health
Status: NEW
Owner: Astra
Last Updated: 2026-11-10

## Summary
This feature enables premium eligibility decisioning for health insurance underwriting using applicant age and health information. It covers three connected capabilities:

1. capture and validate applicant age and required health inputs for underwriting,
2. apply approved age and health rules to produce premium eligibility decisions, and
3. manage the age- and health-based eligibility rules through governed configuration.

The feature is intended to ensure that premium eligibility outcomes are based only on complete, validated, and permitted underwriting inputs; that sensitive health information is securely accessed only by authorized roles; and that both rule administration and decision execution are traceable for audit and regulatory review.

## Scope
In scope:
- Underwriting capture of applicant age using entered age and/or date of birth tied to an application record.
- Capture of required health information fields used by underwriting, including declared conditions, tobacco use, and relevant yes/no disclosures when configured as required health fields.
- Validation of missing, invalid, unsupported, conflicting, contradictory, or out-of-range age and health inputs before underwriting progression or eligibility submission.
- Persistence and retrieval of applicant age and health inputs for use by the underwriting workflow and premium eligibility processing.
- Underwriting evaluation screen behavior that loads calculated age, captured health information, application identifier, and data completeness status before eligibility decision submission.
- Restriction of health information view and update access to authorized underwriting roles, with audit logging of health-information view and update actions.
- Restriction of risk evaluation and eligibility submission to authenticated users with underwriting permissions.
- System execution of premium eligibility decisions using validated age and health inputs and approved underwriting and eligibility rules.
- Blocking of decision execution when required inputs or permitted rule references are unavailable.
- Storage of premium eligibility decision traces, including outcome, input values or evaluated data snapshot, rule version, timestamp, and actor or system source.
- Product Administrator management of premium eligibility rules for age and health, including create, edit, validate, activate, deactivate, publish, and view operations without direct database changes or code deployment.
- Versioning of published rule sets, preservation of prior versions unchanged, and activation of the published version for subsequent decisions.
- Validation of rule definitions for completeness, overlapping age ranges, invalid health condition references, invalid operators, and publish readiness.
- Immutable audit logging for rule create, update, publish, and deactivate actions.

Out of scope:
- Premium eligibility rule calculation and final decision outcome generation beyond the scope stated in the feature source is partially contradicted by later stories; this feature includes decision execution because US 101730 explicitly requires it, but does not include rebuilding the underwriting decision engine logic beyond consuming the published rule version.
- Advanced medical questionnaire branching beyond the required underwriting health fields in US 101717.
- External clinical data ingestion from third-party health systems.
- Bulk migration of historical premium decisions to a newly published rule version.
- Advanced simulation, what-if analysis, or external rule authoring tools.
- Advanced customization called out as future work in US 101730.

## Application Type & Platform Context
Application type: mixed.

Source evidence:
- “The underwriting evaluation screen loads the applicant's calculated age, captured health information, application identifier, and current data completeness status before an eligibility decision can be submitted.”
- “Expose API and UI”
- “Product Administrator opens the premium eligibility rule management screen for age and health rules.”

Platform context supported by source:
- UI/screens exist for underwriting intake, underwriting evaluation, and premium eligibility rule management.
- API/service behavior exists for decision execution and rule management because the source explicitly requires exposed API and UI.
- The selected architecture style is monolith.

Open Question:
- The source does not specify whether the UI targets web, desktop, mobile, or multiple channels.

## Actors and Permissions
### Underwriter
Supported capabilities:
- Open an applicant record in the underwriting intake workflow.
- View protected age and health fields when authorized.
- Enter and save applicant age-related data and health responses.
- Submit a valid underwriting assessment and eligibility outcome.
- Retrieve completed underwriting decisions for review with applicant record, assessment details, and trace information.

Constraints:
- Only authorized underwriting roles can view or update health information.
- Risk evaluation and eligibility submission are restricted to authenticated users with underwriting permissions.
- Users without the required underwriting role must receive an authorization error.

### Underwriting System
Supported capabilities:
- Calculate premium eligibility decisions using validated customer age and health information as permitted decision inputs.
- Apply only approved underwriting and eligibility rules.
- Block decision execution when required inputs or permitted rule references are unavailable.
- Store decision outcome and trace metadata for auditability.

Constraints:
- Must use only approved rules and permitted decision inputs.

### Product Administrator
Supported capabilities:
- Create, edit, validate, activate, deactivate, publish, and view premium eligibility rules that use age and health criteria.
- Manage rules without direct database changes or code deployment.
- Access current active rule set, draft changes if present, and version history metadata.

Constraints:
- Create/update/publish/deactivate actions are restricted to users with Product Administrator access.

Open Questions:
- The source does not define whether Underwriters can view rule versions or rule details beyond decision traces.
- The source does not define whether Product Administrators can view applicant health data.
- The source does not define whether read-only audit access is available to any actor besides those explicitly named.

## Feature Development Intent
This is feature-development work to build end-to-end premium eligibility decisioning influenced by age and health in a controlled underwriting workflow.

Behavior to be built or changed:
- Enforce required capture and validation of underwriting age and health inputs before records are marked ready for premium eligibility decisioning.
- Provide underwriting evaluation behavior that presents the calculated age, captured health information, application identifier, and completeness status before decision submission.
- Build decision execution that uses validated age and health inputs together with only approved rules.
- Add governed rule-management capability so Product Administrators can update age- and health-based eligibility rules without code deployment.
- Add versioning and immutable auditability for both rule changes and decision execution.
- Add role-based access controls and trace logging for sensitive health-information access and updates.

Outcome to be delivered:
- Premium eligibility decisions are consistent, compliant, secure, and traceable, and can be updated through managed rule configuration rather than engineering changes.

## UI Design & Interaction Contract
### Underwriting intake workflow
Supported interaction:
1. Underwriter opens an applicant record in the underwriting intake workflow.
2. The system displays age and required health information fields with protected access controls.
3. The Underwriter enters age-related data and health responses.
4. The system validates required fields, value ranges, supported values, and cross-field consistency during save or submit.
5. If validation fails, the system highlights the exact fields with actionable, field-level validation messages and blocks progression/submission.
6. If validation passes, the system saves the data to the underwriting record and marks the application ready for premium eligibility input consumption.

Required UI-visible data/behavior:
- Applicant age capture via entered age and/or date of birth.
- Required health information fields used by underwriting.
- Field-level errors for:
  - missing values,
  - conflicting values,
  - out-of-range age values,
  - blank required health responses,
  - unsupported health values,
  - contradictory related answers.
- Protected access controls for health information.
- Save outcome sufficient to support later retrieval in underwriting workflow.
- No UI support for manual entry of unapproved derived factors.

### Underwriting evaluation screen
Supported interaction:
- Before an eligibility decision can be submitted, the screen loads:
  - applicant calculated age,
  - captured health information,
  - application identifier,
  - current data completeness status.
- If required underwriting inputs are missing, inconsistent, or fail validation, the system blocks submission of the eligibility outcome and displays field-specific validation messages identifying the exact issue.
- When the Underwriter submits a valid assessment, the system stores the eligibility outcome and associated trace data.

### Premium eligibility rule management screen
Supported interaction:
1. Product Administrator opens the premium eligibility rule management screen for age and health rules.
2. The system loads:
   - current active rule set,
   - draft changes if present,
   - version history metadata.
3. Product Administrator creates or edits a rule by entering:
   - age bounds,
   - health condition criteria,
   - comparison operators,
   - eligibility outcome.
4. The system validates the draft for:
   - required fields,
   - overlapping age ranges,
   - invalid condition references,
   - conflicting active rules.
5. Product Administrator publishes the validated rule set or saves it as a draft.
6. The system creates a new version, writes audit entries, and marks the published configuration as the active rule set for future decisions.

Required UI-visible validation behavior:
- Field-specific validation errors on rule publication failure when:
  - required fields are missing,
  - age ranges overlap within the same product context,
  - referenced health conditions are invalid,
  - operators are invalid.

Accessibility expectations:
- The source does not specify accessibility standards or detailed accessibility behaviors.

Copy/tone:
- The source requires actionable and field-specific validation messages identifying the exact issue.
- No further copy or tone requirements are specified.

Open Questions:
- Exact screen names, layouts, field labels, grouping, and navigation structure are not specified.
- Exact wording of validation and authorization messages is not specified.
- The source mentions masking or withholding protected fields from unauthorized users, but the exact UI treatment is not specified.
- The source does not specify whether completeness status is editable, read-only, or system-derived display only.

## API Contract
Source-supported API/service contract expectations:
- The feature must expose API and UI.
- The system must support operations corresponding to:
  - saving applicant age and health underwriting inputs,
  - retrieving persisted underwriting inputs for workflow use,
  - executing premium eligibility decisioning using validated age and health inputs,
  - creating, editing, validating, publishing, activating, deactivating, and viewing premium eligibility rules,
  - retrieving active rule set, draft changes if present, and version history metadata,
  - retrieving completed underwriting decisions with associated record, assessment details, and trace information.

Input contract supported by source:
- Applicant age from entered age and/or date of birth.
- Required health question responses keyed by applicationId.
- Rule definition inputs including:
  - ruleName,
  - minAge,
  - maxAge,
  - healthConditionCode,
  - operator,
  - comparisonValue,
  - eligibilityOutcome,
  - status.
- Decision execution inputs must include validated customer age and health information as permitted decision inputs.

Output/persistence contract supported by source:
- Successfully saved underwriting record persists applicant age and health inputs.
- Decision execution stores:
  - decision outcome,
  - input values or evaluated age and health data snapshot,
  - rule version,
  - timestamp,
  - actor or system source,
  - matched rule identifier where required by US 101733,
  - applicantId,
  - underwritingAssessmentId,
  - decision source.
- Rule publication assigns a unique version identifier and preserves prior version unchanged.
- Completed underwriting decisions are retrievable with applicant record, assessment details, and trace information.

Error/authorization contract supported by source:
- Submission is prevented for missing, unsupported, contradictory, conflicting, or out-of-range input conditions.
- Decision execution is blocked when required inputs or permitted rule references are unavailable.
- Users without required underwriting permissions receive an authorization error.
- Publication of a rule set is prevented when required fields are missing, age ranges overlap within the same product context, or referenced health conditions/operators are invalid.

Idempotency:
- The source does not specify idempotency requirements.

Integration behavior:
- Data source references include:
  - underwriting application database tables,
  - application database,
  - rule configuration tables,
  - reference tables for health condition codes,
  - underwriting decision trace records,
  - immutable audit log storage.

Open Questions:
- No endpoint paths, methods, request schemas, response schemas, status codes, or authentication protocol details are specified.
- The source does not specify whether validation can be invoked independently from save/publish.
- The source does not specify synchronous versus asynchronous decision execution.
- The source does not define API behavior for draft rule conflict resolution or concurrent edits.

## Business Logic & Rules
- A record cannot be marked ready for premium eligibility decisioning until applicant age and configured health fields are present.
- Age must be validated as a realistic numeric value based on date of birth or entered age.
- If both date of birth and entered age are provided, the system must validate them for consistency and reject conflicting values.
- Required health responses cannot be blank.
- Health responses must use supported values.
- Health responses that contradict related answers in the same application record must be rejected.
- The system must not allow unapproved derived factors to be entered manually.
- Eligibility outcome submission is blocked when required underwriting inputs are missing, inconsistent, or fail validation.
- Premium eligibility decisions must be calculated using validated customer age and health information as permitted decision inputs.
- The system must apply only approved underwriting and eligibility rules.
- Decision execution must be blocked when required inputs are unavailable.
- Decision execution must be blocked when permitted rule references are unavailable.
- Product Administrators can manage premium eligibility rules without direct database changes or code deployment.
- Publishing a rule set creates a unique version identifier.
- Publishing a rule set preserves the prior version unchanged.
- The active published version is used for subsequent premium eligibility decisions.
- Publication must be rejected when required fields are missing.
- Publication must be rejected when age ranges overlap within the same product context.
- Publication must be rejected when referenced health conditions are invalid.
- Publication must be rejected when operators are invalid.
- For rule validation, minAge must be less than or equal to maxAge.
- Referenced health condition codes must exist.
- Decision traces created after publication must include the exact published version applied and the matched rule identifier.
- Audit records for rule create, update, publish, and deactivate must be immutable.
- Audit records for health-information view and update must include application identifier, actor, action type, and timestamp.

Open Questions:
- The source does not specify the exact definition of “realistic numeric value” for age.
- The source does not specify exact contradiction rules among health questions.
- The source does not specify the full applicability scope dimensions for “same product context.”
- The source does not define decision outcomes or the rule evaluation precedence when multiple rules could match.
- The source does not define whether draft rules can overlap with active rules prior to publication.

## Data Model & Validation
### Source-supported entities and fields
#### Underwriting application / record
Supported fields:
- applicationId
- applicantId
- applicant age
- dateOfBirth, when available
- health question responses
- current data completeness status

Validation:
- age required before ready for premium eligibility decisioning
- configured health fields required before ready for premium eligibility decisioning
- age must be numeric and realistic
- age/dateOfBirth consistency required when both are present
- required health responses must not be blank
- health responses must use supported enumerations/values
- contradictory related answers must be rejected

Storage constraints:
- age, dateOfBirth when available, and health responses must be stored keyed by applicationId
- persisted inputs must be retrievable by underwriting workflow
- unapproved derived factors must not be manually enterable

#### Underwriting assessment / decision
Supported fields:
- underwritingAssessmentId
- eligibility outcome
- evaluated age and health data snapshot
- decision timestamp
- decision source
- actor or system source
- input values
- rule version
- matched rule identifier

Retrieval expectations:
- each completed underwriting decision must be retrievable for review with associated applicant record, assessment details, and trace information showing when and from which source the decision was produced

#### Premium eligibility rule
Supported fields:
- ruleName
- minAge
- maxAge
- healthConditionCode
- operator
- comparisonValue
- eligibilityOutcome
- status
- unique version identifier

Validation:
- required fields must be present
- minAge <= maxAge
- age ranges must not overlap within the same product context
- referenced health condition codes must exist
- referenced health conditions and operators must be valid

#### Audit record
Supported fields:
- actor
- timestamp
- action type
- before values
- after values
- rule version
- application identifier

Validation/quality constraints:
- audit details for rule create/update/publish/deactivate must be immutable
- health information view/update actions must be recorded

Retention expectations:
- The source requires auditability and traceability but does not specify retention periods.

Open Questions:
- The source does not specify the full set of configured health fields.
- The source does not specify allowed enumerations for health responses.
- The source does not specify field formats, lengths, nullability beyond stated validations, or whether age is stored as entered value, calculated value, or both.
- The source does not define whether before/after values apply to all rule fields or changed fields only.
- The source does not specify version-history metadata fields beyond version identifier.
- The source does not specify whether product context is an entity/field and, if so, how it is represented.

## Functional Requirements
FR-1. The underwriting data capture flow shall require applicant age and the configured health fields before the record can be marked ready for premium eligibility decisioning.

FR-2. The system shall accept applicant age using entered age and/or date of birth tied to the application record.

FR-3. The system shall validate age as a realistic numeric value based on date of birth or entered age.

FR-4. When both entered age and date of birth are provided, the system shall validate them for consistency and shall display field-level errors for conflicting values.

FR-5. The system shall display field-level errors for missing age values and out-of-range age values.

FR-6. The system shall prevent save or submission progression when required health responses are blank, use unsupported values, or contradict related answers in the same application record.

FR-7. The system shall display field-specific validation messages identifying the exact issue for invalid age or health inputs.

FR-8. Only authorized underwriting roles shall be permitted to view or update health information.

FR-9. The system shall record each health-information view or update action with application identifier, actor, action type, and timestamp.

FR-10. A successfully saved underwriting record shall persist applicant age and health inputs so they are retrievable by the underwriting workflow.

FR-11. The system shall not allow unapproved derived factors to be entered manually.

FR-12. The underwriting evaluation screen shall load the applicant's calculated age, captured health information, application identifier, and current data completeness status before an eligibility decision can be submitted.

FR-13. If required underwriting inputs are missing, inconsistent, or fail validation, the system shall block submission of the eligibility outcome.

FR-14. When an Underwriter submits a valid assessment, the system shall store the eligibility outcome together with applicantId, underwritingAssessmentId, evaluated age and health data snapshot, decision timestamp, and decision source.

FR-15. The system shall restrict risk evaluation and eligibility submission to authenticated users with underwriting permissions and shall return an authorization error for users without the required role.

FR-16. Each completed underwriting decision shall be retrievable for review with its associated applicant record, assessment details, and trace information showing when and from which source the decision was produced.

FR-17. The system shall calculate premium eligibility decisions using validated customer age and health information as permitted decision inputs.

FR-18. The system shall apply only approved underwriting and eligibility rules during premium eligibility decision execution.

FR-19. The system shall block decision execution when required inputs are not available.

FR-20. The system shall block decision execution when permitted rule references are not available.

FR-21. The system shall store decision outcome, input values, rule version, timestamp, and actor or system source for audit traceability and regulatory review.

FR-22. Authorized users with Product Administrator access shall be able to create, edit, validate, activate, deactivate, publish, and view premium eligibility rules that use age and health criteria without direct database changes or code deployment.

FR-23. The rule management capability shall allow entry of age bounds, health condition criteria, comparison operators, eligibility outcome, and status for premium eligibility rules.

FR-24. When a rule set is published, the system shall assign a unique version identifier, preserve the prior version unchanged, and make the active version available for subsequent premium eligibility decisions.

FR-25. The system shall prevent publication of a rule set when required fields are missing.

FR-26. The system shall prevent publication of a rule set when age ranges overlap within the same product context.

FR-27. The system shall prevent publication of a rule set when referenced health conditions or operators are invalid.

FR-28. The system shall enforce minAge less than or equal to maxAge for rule validation.

FR-29. The system shall validate that referenced health condition codes exist.

FR-30. The system shall display field-specific validation errors when rule publication is blocked.

FR-31. Every rule create, update, publish, and deactivate action shall record immutable audit details including actor, timestamp, action type, before/after values, and rule version.

FR-32. Each premium eligibility decision executed after publication shall store a trace record that includes the evaluated applicant age input, health data inputs used by the rule, the matched rule identifier, and the exact published version applied.

FR-33. The system shall load the current active rule set, draft changes if present, and version history metadata in the premium eligibility rule management screen.

FR-34. The system shall allow a Product Administrator to save a rule set as a draft when additional edits are needed.

FR-35. The system shall create audit entries and mark the published configuration as the active rule set for future decisions upon successful publication.

FR-36. The feature shall expose both UI and API support for the supported underwriting capture, decisioning, retrieval, and rule-management capabilities.

## Non-Functional Requirements
- Security:
  - Health-information read and write access must be restricted to authorized underwriting roles.
  - Risk evaluation and eligibility submission must be restricted to authenticated users with underwriting permissions.
  - Create/update/publish/deactivate rule actions must be restricted to Product Administrator RBAC permissions.
  - User inputs must be sanitized where stated by the source.
  - Audit records must be protected from modification where the source requires immutability.
  - Unauthorized users must receive an authorization error.
- Traceability and auditability:
  - Health-information view and update actions must be auditable.
  - Underwriting decisions must be traceable to applicant/assessment context and source.
  - Premium eligibility decisions must be traceable to matched rule identifier and exact published rule version.
  - Rule administration actions must produce immutable audit details.
- Observability:
  - The system must log validation failures, save outcomes, access attempts, and audit record creation with applicationId and actorId for traceability.
  - The system must emit structured logs and metrics for rule validation failures, publish events, active version changes, and decision-to-rule-version linkage failures.
- Performance:
  - The system must save and retrieve age and health data within standard underwriting form response expectations without delaying the intake flow.
  - Active rules and version history must load efficiently.
  - Publish operations should complete within normal admin workflow expectations without locking decision execution for unrelated products.
  - Decisioning and related feature behavior must meet SLA requirements, though no numeric SLA is provided.
- Reliability:
  - Published rule changes must become available for subsequent premium eligibility decisions.
  - Prior published versions must remain unchanged after a new publication.
- Compliance:
  - The feature must support compliant, traceable, and secure use of age and health information in eligibility outcomes as stated in the feature description.

Open Questions:
- No quantitative performance thresholds, SLA values, availability targets, or log retention requirements are provided.
- No explicit accessibility standard is specified.
- No encryption, masking, or redaction standard is specified beyond role-based restriction and possible withholding/masking of protected fields.

## Acceptance Scenarios
### AS-1: Capture valid age and health inputs and mark record ready
Given an authorized Underwriter opens an applicant record in the underwriting intake workflow  
When the Underwriter enters applicant age and all configured required health fields with valid, supported, and non-contradictory values and saves the record  
Then the system persists the applicant age and health inputs keyed to the application record  
And the record can be marked ready for premium eligibility decisioning  
And the underwriting workflow can retrieve the saved inputs later.

### AS-2: Block readiness when age is missing
Given an authorized Underwriter is completing underwriting data capture  
When applicant age is missing  
Then the system shall not mark the record ready for premium eligibility decisioning  
And shall display a field-level validation error for the missing age field.

### AS-3: Block readiness when age is out of range or unrealistic
Given an authorized Underwriter enters applicant age information  
When the entered age or age derived from date of birth is not a realistic numeric value or is out of range  
Then the system shall block save or progression as applicable  
And shall display a field-level validation error identifying the exact issue.

### AS-4: Block readiness when date of birth and entered age conflict
Given an authorized Underwriter enters both date of birth and entered age  
When the two values are inconsistent  
Then the system shall block progression to premium eligibility decisioning  
And shall display field-level errors for the conflicting values.

### AS-5: Block readiness when required health responses are invalid
Given an authorized Underwriter enters health responses  
When any required health response is blank, uses an unsupported value, or contradicts a related answer in the same application record  
Then the system shall block submission or progression  
And shall display field-specific validation messages identifying the exact invalid field and issue.

### AS-6: Audit health-information view and update access
Given an authorized underwriting user views or updates health information on an application  
When the view or update occurs  
Then the system shall create an audit record containing the application identifier, actor, action type, and timestamp.

### AS-7: Restrict unauthorized access to health information
Given a user without authorized underwriting role access attempts to view or update health information  
When the access attempt is made  
Then the system shall deny the action  
And shall return an authorization error.

### AS-8: Load underwriting evaluation data before decision submission
Given an authenticated user with underwriting permissions opens the underwriting evaluation screen  
When the screen loads  
Then the system shall display the applicant's calculated age, captured health information, application identifier, and current data completeness status before an eligibility decision can be submitted.

### AS-9: Block eligibility outcome submission when underwriting inputs are invalid
Given an authenticated user with underwriting permissions is on the underwriting evaluation screen  
When required underwriting inputs are missing, inconsistent, or fail validation  
Then the system shall block submission of the eligibility outcome  
And shall display field-specific validation messages identifying the exact issue.

### AS-10: Store a valid underwriting assessment decision
Given an authenticated Underwriter with required permissions has valid underwriting inputs on the evaluation screen  
When the Underwriter submits a valid assessment  
Then the system shall store the eligibility outcome with applicantId, underwritingAssessmentId, evaluated age and health data snapshot, decision timestamp, and decision source.

### AS-11: Retrieve completed underwriting decision with trace details
Given a completed underwriting decision exists  
When an authorized user retrieves it for review  
Then the system shall return the associated applicant record, assessment details, and trace information showing when and from which source the decision was produced.

### AS-12: Execute premium eligibility decision using validated inputs and approved rules
Given validated customer age and health information is available  
And an approved active rule version exists  
When the system executes premium eligibility decisioning  
Then the system shall calculate the decision using only the permitted age and health inputs  
And shall apply only approved underwriting and eligibility rules  
And shall store the decision outcome, input values, rule version, timestamp, and actor or system source.

### AS-13: Block decision execution when required inputs are unavailable
Given a premium eligibility decision is requested  
When required age or health inputs are unavailable  
Then the system shall block decision execution.

### AS-14: Block decision execution when permitted rule references are unavailable
Given validated inputs are available  
When the system cannot resolve permitted approved rule references for decision execution  
Then the system shall block decision execution.

### AS-15: Create and publish a valid premium eligibility rule set
Given an authorized Product Administrator opens the premium eligibility rule management screen  
And enters a complete, valid rule set with non-overlapping age ranges, valid health condition references, valid operators, minAge less than or equal to maxAge, and required fields populated  
When the Product Administrator publishes the rule set  
Then the system shall assign a unique version identifier  
And preserve the prior version unchanged  
And mark the published configuration as the active rule set for subsequent premium eligibility decisions  
And create immutable audit records for the publish action.

### AS-16: Save a rule set as draft
Given an authorized Product Administrator is editing premium eligibility rules  
When additional edits are needed before publication  
Then the system shall allow the rule set to be saved as a draft  
And the rule management screen shall load draft changes if present.

### AS-17: Block publication for overlapping age ranges
Given an authorized Product Administrator is publishing a rule set  
When age ranges overlap within the same product context  
Then the system shall prevent publication  
And shall display field-specific validation errors.

### AS-18: Block publication for invalid rule references
Given an authorized Product Administrator is publishing a rule set  
When required fields are missing, a referenced health condition is invalid, or an operator is invalid  
Then the system shall prevent publication  
And shall display field-specific validation errors.

### AS-19: Audit rule administration actions
Given an authorized Product Administrator creates, updates, publishes, or deactivates a rule  
When the action completes  
Then the system shall record immutable audit details including actor, timestamp, action type, before/after values, and rule version.

### AS-20: Trace executed decision to matched rule version
Given a premium eligibility decision is executed after a rule set has been published  
When the decision trace is stored  
Then the trace shall include the evaluated applicant age input, health data inputs used by the rule, the matched rule identifier, and the exact published version applied.

## Traceability Matrix
| Source ID | Requirement | Acceptance Criteria | Test Coverage |
|---|---|---|---|
| US 101717 AC1 | FR-1 | Record requires age and configured health fields before ready state | AS-1, AS-2, AS-5 |
| US 101717 AC2 | FR-2, FR-3, FR-4, FR-5 | Validate realistic numeric age based on DOB or entered age; field-level errors for missing, conflicting, out-of-range | AS-2, AS-3, AS-4 |
| US 101717 AC3 | FR-6, FR-7 | Prevent submission for blank, unsupported, contradictory health responses | AS-5 |
| US 101717 AC4 | FR-8, FR-9 | Only authorized underwriting roles can view/update health info; all view/update actions audited | AS-6, AS-7 |
| US 101717 AC5 | FR-10, FR-11 | Persist age and health inputs for retrieval; disallow manual unapproved derived factors | AS-1 |
| US 101717 AC6 | FR-12 | Evaluation screen loads calculated age, health info, application identifier, completeness status before submission | AS-8 |
| US 101717 AC7 | FR-13, FR-7 | Block eligibility outcome submission on invalid inputs with field-specific errors | AS-9 |
| US 101717 AC8 | FR-14 | Store eligibility outcome with applicantId, underwritingAssessmentId, data snapshot, timestamp, source | AS-10 |
| US 101717 AC9 | FR-15 | Restrict evaluation/submission to authenticated underwriting users; authorization error otherwise | AS-7, AS-8 |
| US 101717 AC10 | FR-16 | Retrieve completed decision with applicant record, assessment details, and trace info | AS-11 |
| US 101730 AC1 | FR-17 | Calculate premium eligibility using validated age and health information as permitted inputs | AS-12 |
| US 101730 AC2 | FR-18, FR-19, FR-20 | Apply only approved rules; block execution when required inputs or permitted rule references are unavailable | AS-12, AS-13, AS-14 |
| US 101730 AC3 | FR-21 | Store decision outcome, input values, rule version, timestamp, actor/system source | AS-12 |
| US 101730 AC4 | FR-36 | User can apply approved age and health rules through documented happy-path flow | AS-12 |
| US 101730 AC5 | FR-19 | Validate required inputs before completing decisioning action | AS-13 |
| US 101733 AC1 | FR-22, FR-23 | Product Administrator can create/edit/validate/activate/deactivate/view rules without DB changes or code deployment | AS-15, AS-16 |
| US 101733 AC2 | FR-24, FR-35 | Publish assigns unique version, preserves prior version, active version used subsequently | AS-15 |
| US 101733 AC3 | FR-25, FR-26, FR-27, FR-28, FR-29, FR-30 | Prevent publication for missing fields, overlapping ages, invalid condition references/operators | AS-17, AS-18 |
| US 101733 AC4 | FR-31 | Immutable audit details for create/update/publish/deactivate | AS-19 |
| US 101733 AC5 | FR-32 | Decision trace includes evaluated age, health inputs used, matched rule identifier, exact published version | AS-20 |
| US 101733 Flow/Description | FR-33, FR-34 | Rule management screen loads active set, drafts, version history; draft save supported | AS-16 |

## Open Questions
1. What exact platform(s) host the underwriting and Product Administrator screens: web, desktop, mobile, or multiple?
2. What are the exact configured health fields required for underwriting in this feature?
3. What are the supported values/enumerations for each health response field?
4. What exact age range or algorithm defines a “realistic numeric value” for age?
5. If both entered age and date of birth are present, what tolerance or matching rule determines conflict?
6. What exact contradiction rules apply across related health answers?
7. What are the exact eligibility outcome values that can be stored by assessments and rules?
8. What is the exact definition of “decision source” and “actor or system source,” and are both required on every decision record?
9. What is the exact “product context” used to evaluate overlapping age ranges, and which fields determine that scope?
10. How should the system behave when multiple approved active rules could match a single applicant?
11. What is the rule precedence or conflict-resolution strategy during decision execution?
12. Are rule drafts versioned before publication, and how are concurrent edits handled?
13. What exact version-history metadata must be displayed in the rule management screen beyond unique version identifier?
14. What API endpoints, methods, request/response schemas, and status codes are required for underwriting capture, decision execution, retrieval, and rule management?
15. Is decision execution synchronous in the user flow, or can it be asynchronous?
16. What exact authorization error payload or UI message should be returned/displayed for unauthorized access attempts?
17. For unauthorized access to health information, should protected fields be hidden entirely, masked, or shown read-only with no values?
18. Who is authorized to retrieve completed underwriting decisions for review besides Underwriters, if anyone?
19. Can Product Administrators view decision traces, and if so, with what scope?
20. What retention period and storage policy apply to underwriting decisions, decision traces, and immutable audit records?
21. What SLA or numeric performance thresholds apply to save, load, retrieval, publish, and decision execution actions?
22. What accessibility standards or requirements apply to the UI portions of this feature?
23. What exact sanitization requirements apply to rule-management and underwriting input fields?
24. Are there any localization, time zone, or timestamp-format requirements for audit and decision records?

## Source References
- Feature ID 101666 - Premium Eligibility Decisioning for Age and Health
- User Story US 101717 - Capture applicant age and health information for premium eligibility decisioning
- User Story US 101730 - Apply approved age and health rules to premium eligibility
- User Story US 101733 - Manage premium eligibility rules for age and health
- Source signal: User-selected Architecture Style - monolith