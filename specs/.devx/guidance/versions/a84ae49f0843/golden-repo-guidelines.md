# Golden Repository Implementation Guidelines

Generated for: qa231026
Repository: Insurance golden repo
Generated at: 2026-10-23T13:19:39.807Z
File selection mode: all vectorized files

## Warnings

- None

## How To Use

- Read this file before implementation in the IDE.
- Use `golden-repo-sources.md` for exact source excerpts and file-path attribution.
- Use Golden Repo guidance for conventions, patterns, validation expectations, architecture style, testing expectations, and implementation constraints.
Golden Repo grounding rules:
- If a selected Golden Repo reference states a rule, constraint, term, or pattern that applies to this feature, the spec must reflect it and cite that file path in Source References.
- Do not force unrelated Golden Repo concepts into the specification. If a reference does not apply to this feature, leave it out.
- Do not invent implementation details that are absent from the Golden Repo or the requirement. Exact endpoints, hosts, schemas, tables, queue names, and infrastructure components that are not stated must be marked Validate or left as Open Questions.
- Also incorporate applicable UI design guidelines, architecture guidelines, policies, business rules, standards, and validation expectations when they apply to this feature.
- If a relevant Golden Repo rule applies but the feature source lacks a product decision, write the gap under Open Questions instead of inventing behavior.
- If a user story conflicts with a Golden Repo example, follow the user story for what to build and keep only the Golden Repo terminology and rules that still apply.
- Cite the Golden Repo file paths of the references you actually used in Source References.
- Existing code patterns in the target workspace override generic Golden Repo guidance when they conflict on stack mechanics.
- Do not require live Golden Repository access inside the implementation workspace.

## Extracted Guidance

GOLDEN REPO KNOWLEDGE CONTEXT (spec generation)
Repository: Insurance golden repo
The following is a distilled, de-duplicated consolidation of Golden Repository conventions. Use a convention only when it is materially relevant to the requested feature. Do not force unrelated concepts into the specification. Do not invent endpoints, hosts, schemas, tables, or queue names that this consolidation does not state; mark those as Validate.

## Architecture & System Design

### Business and Platform Objectives

- [G1] Digitize and standardize insurance journeys.
- [G2] Reduce manual processing and turnaround time.
- [G3] Improve customer, agent, broker, and operations experience.
- [G4] Ensure traceability, segregation of duties, and auditability.
- [G5] Support configurable products, jurisdictions, currencies, taxes, and channels.
- [G6] Support web and mobile channels.
- [G7] Support disaster recovery and business continuity.

### Reference Architecture Components

- [G8] Include the following reference architecture components:
  - API gateway
  - Identity and access management
  - Customer and party service
  - Product and rating service
  - Quote and proposal service
  - Underwriting and rules engine
  - Policy administration service
  - Billing and payment service
  - Claims service
  - Document management service
  - Notification service
  - Workflow and task service
  - Reporting and regulatory data service
  - Audit and observability platform
  - Integration layer / event bus

### Integration and Interaction Patterns

- [G9] Use REST/JSON for synchronous interactions.
- [G10] Use events for lifecycle changes.
- [G11] Use batch interfaces for legacy and regulatory exchanges.

### Transformation and Repository-Discovery Rules

- [G12] Establish a baseline and complete a repository impact inventory for every selected transformation.
- [G13] Map current implementation to ASP.NET Core and Angular only after repository discovery confirms each component.
- [G14] Do not make compatibility conclusions or begin implementation edits until repository impact inventory is complete.
- [G15] Preserve existing request/response contracts until impact inventory identifies a required change.
- [G16] Rebuild UI composition in Angular from discovered screens, routes, and state, not guessed file lists.
- [G17] Treat persistence, schemas, and integrations as unchanged until discovery records exact models and connection points.
- [G18] Record runtime, build, CI, container, and environment changes only after they are found in the repository.
- [G19] Empty seed-path lists do not imply unused code.
- [G20] Package tasks must refer to completed impact inventory; never assume supplied source locations are complete or that empty path lists prove no usage.

### Transformation Completion Criteria

- [G21] Repository impact inventory is complete only when it records:
  - Every affected path
  - Every API
  - Every configuration key
  - Every protecting test
  - Negative-search results
- [G22] Create ASP.NET Core application scaffold and module layout from the completed inventory.
- [G23] Transformation of the target API is done when the target API project builds and hosts discovered entrypoints without unverified rewrites.
- [G24] Recreate discovered UI surfaces in Angular using the inventory.
- [G25] UI transformation is done when each discovered screen or API-only behavior has a measurable acceptance check.

## API & Integration Conventions

### Interaction Patterns

- [G26] Use REST/JSON for synchronous interactions.
- [G27] Use events for lifecycle changes.
- [G28] Use batch interfaces for legacy and regulatory exchanges.

### Cross-Service Request Standards

- [G29] Require correlation IDs across all services.
- [G30] Require idempotency keys for payment and document operations.

### Contract Stability

- [G31] Preserve existing request/response contracts until an impact inventory identifies a required change.

## Data Model & Validation

### Core Entities

- [G32] Model the following core entities:
  - Party
  - Customer
  - Organization
  - Agent / Broker
  - Product
  - Plan
  - Coverage
  - Quote
  - Proposal
  - Risk
  - Underwriting decision
  - Policy
  - Policy version
  - Insured object
  - Beneficiary / nominee
  - Premium schedule
  - Invoice
  - Payment
  - Endorsement
  - Claim
  - Claim document
  - Assessment
  - Reserve
  - Settlement
  - Complaint
  - Notification
  - Audit event

### Identifiers, Ownership, and Traceability

- [G33] Use immutable identifiers.
- [G34] Record source system and data ownership.

### Field Definitions and Validation

- [G35] Define required, optional, conditional, and prohibited fields.
- [G36] Input validation is required.
- [G37] Every requirement must define data inputs and outputs.

### Temporal, State, and Configuration Modeling

- [G38] Store effective-from and effective-to dates for versioned business rules.
- [G39] Every requirement must be state-aware:
  - Define preconditions.
  - Define status transitions.
  - Define exceptions.
- [G40] Every requirement must be configurable where appropriate:
  - Identify product.
  - Identify jurisdiction.
  - Identify channel.
  - Identify effective date.

### Monetary, Currency, and Time Standards

- [G41] Define precision and rounding for monetary values.
- [G42] Store currency and timezone explicitly.

### Data Lifecycle and Protection

- [G43] Apply retention, archival, masking, and deletion rules by data category.

## Security & Compliance

### Core Security and Privacy Requirements

- [G44] Every requirement must be secure and privacy-aware; handling of sensitive data must be explicit.
- [G45] Require privacy and data minimization.
- [G46] Ensure traceability, segregation of duties, and auditability.

### Authentication, Authorization, and Access Control

- [G47] Require strong authentication and authorization.
- [G48] Apply least privilege and role-based access control.
- [G49] Require MFA for privileged and sensitive actions.
- [G50] Ensure segregation of duties.

### Data Protection

- [G51] Require encryption in transit and at rest.
- [G52] Manage secrets outside source code.
- [G53] Apply retention and deletion policies.

### Secure Input and Output Handling

- [G54] Require input validation and output encoding.

### Logging and Audit

- [G55] Require audit logging.
- [G56] Use immutable audit records.
- [G57] Mask PII in logs.
- [G58] Ensure traceability and auditability.

## Testing & Quality Expectations

### Requirement Quality Standards

- [G59] Every requirement must be:
  - **Atomic**: one primary behavior or obligation.
  - **Testable**: measurable acceptance criteria exist.
  - **Unambiguous**: avoid vague terms such as `fast`, `user-friendly`, or `appropriate`.
  - **Traceable**: linked to business objective, process, rule, data, API, and test.
  - **Role-specific**: actor and permissions are explicit.
  - **State-aware**: preconditions, status transitions, and exceptions are defined.
  - **Configurable where appropriate**: product, jurisdiction, channel, and effective date are identified.
  - **Secure and privacy-aware**: sensitive data handling is explicit.
  - **Auditable**: actor, timestamp, before/after values, reason, and correlation ID are defined when relevant.

### Required Requirement Specification Content

- [G60] Requirement specification must include:
  - Requirement ID
  - Title
  - Business rationale
  - Actor
  - Trigger
  - Preconditions
  - Main flow
  - Alternate and exception flows
  - Business rules
  - Data inputs and outputs
  - Permissions
  - Audit requirements
  - Notifications
  - Acceptance criteria
  - Dependencies
  - Traceability links

### Acceptance Checks and Test Coverage

- [G61] Each discovered screen or API-only behavior must have a measurable acceptance check.
- [G62] For transformation work, record every protecting test in the impact inventory.

## UI/UX & Accessibility

### Core UI/UX Requirements
- [G63] Improve the experience for customers, agents, brokers, and operations users.
- [G64] Accessibility is required.

### Localization and Regional Support
- [G65] Localization is required.
- [G66] Timezone support is required.

### UI Reconstruction Guidance
- [G67] Recreate UI surfaces in Angular based on discovered screens, routes, and state.
- [G68] Do not derive or assume UI surfaces from file lists alone.

## Domain & Business Rules

### Supported Actors and Roles

- [G69] Customer / policyholder
- [G70] Insured person
- [G71] Proposer / applicant
- [G72] Beneficiary / nominee
- [G73] Agent / advisor
- [G74] Broker
- [G75] Underwriter
- [G76] Claims assessor
- [G77] Claims investigator
- [G78] Finance user
- [G79] Customer service representative
- [G80] Compliance officer
- [G81] Administrator
- [G82] Auditor

### Insurance Domain Glossary

- [G83] **Policy**: contract of insurance issued by the insurer
- [G84] **Policyholder**: person or organization owning the policy
- [G85] **Insured**: person, asset, or interest covered by the policy
- [G86] **Premium**: amount payable for insurance coverage
- [G87] **Sum insured**: maximum insured amount, subject to policy terms
- [G88] **Deductible**: amount borne by the insured before coverage applies
- [G89] **Rider / endorsement**: additional or modified policy provision
- [G90] **Quote**: indicative price and coverage before binding
- [G91] **Proposal**: application requesting insurance
- [G92] **Underwriting**: evaluation of risk and eligibility
- [G93] **FNOL**: First Notice of Loss
- [G94] **Reserve**: estimated amount set aside for a claim
- [G95] **TPA**: Third-party administrator
- [G96] **Reinsurance**: risk transfer from insurer to reinsurer
- [G97] **Grace period**: permitted period after a due date
- [G98] **Lapse**: policy status resulting from unmet conditions, often non-payment
- [G99] **Reinstatement**: restoration of a lapsed policy subject to rules

### Sales and Onboarding Processes

- [G100] Customer registration
- [G101] Identity verification and KYC
- [G102] Quote generation
- [G103] Product comparison
- [G104] Proposal submission
- [G105] Document upload
- [G106] Payment collection
- [G107] Policy issuance

### Policy Administration Processes

- [G108] View policy
- [G109] Download policy documents
- [G110] Change contact details
- [G111] Change nominee / beneficiary
- [G112] Add or remove coverage
- [G113] Endorsement
- [G114] Cancellation
- [G115] Renewal
- [G116] Reinstatement
- [G117] Premium payment and receipts

### Claims Processes

- [G118] FNOL
- [G119] Claim validation
- [G120] Coverage verification
- [G121] Document collection
- [G122] Surveyor / assessor assignment
- [G123] Fraud screening
- [G124] Reserve creation
- [G125] Approval and repudiation
- [G126] Settlement
- [G127] Recovery and subrogation
- [G128] Claim closure and reopening

### Governance Processes

- [G129] Product approval
- [G130] Rule versioning
- [G131] Approval workflows
- [G132] Delegation of authority
- [G133] Complaint and grievance handling
- [G134] Regulatory reporting
- [G135] Audit and retention

## Naming, Structure & Code Patterns

### Requirement Definition Standards

- [G136] Requirement definitions must explicitly include:
  - Actor
  - Permissions
  - Audit requirements
  - Traceability links

### Structure for Transformation Work

- [G137] For transformation work, create the ASP.NET Core application scaffold and module layout from the completed inventory.

## Error Handling & Resilience

### Availability and Resilience

- [G138] Require availability and resilience in system design and operation.
- [G139] Support disaster recovery and business continuity.

### Failure Handling Patterns

- [G140] Implement retry handling.
- [G141] Implement timeout handling.
- [G142] Implement circuit breaker handling.
- [G143] Implement dead-letter handling.

## Performance & Observability

### Observability and Operational Support

- [G144] Require observability and operational support as part of the architecture.
- [G145] Include an audit and observability platform in the architecture.

### Audit Logging

- [G146] Require audit logging.
- [G147] For auditable requirements, capture:
  - actor
  - timestamp
  - before and after values
  - reason
  - correlation ID, when relevant

### Correlation and Traceability

- [G148] Require correlation IDs across all services.