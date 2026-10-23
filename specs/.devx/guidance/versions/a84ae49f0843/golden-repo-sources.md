# Golden Repository Source Excerpts

These excerpts were copied from the vectorized Golden Repository cache during specs generation.

Repository: Insurance golden repo
Selection mode: all vectorized files
Source excerpts: 12

## 1. 01_business_context.md #0

Score: 1.000

````text
- Digitize and standardize insurance journeys.
- Reduce manual processing and turnaround time.
- Improve customer, agent, broker, and operations experience.
- Ensure traceability, segregation of duties, and auditability.
- Support configurable products, jurisdictions, currencies, taxes, and channels.
- Digitize and standardize insurance journeys.
- Reduce manual processing and turnaround time.
- Improve customer, agent, broker, and operations experience.
- Ensure traceability, segregation of duties, and auditability.
- Support configurable products, jurisdictions, currencies, taxes, and channels.
- Customer / policyholder
- Insured person
- Proposer / applicant
- Beneficiary / nominee
- Agent / advisor
- Broker
- Underwriter
- Claims assessor
- Claims investigator
- Finance user
- Customer service representative
- Compliance officer
- Administrator
- Auditor
- Availability and resilience
- Privacy and data minimization
- Strong authentication and authorization
- Encryption in transit and at rest
- Audit logging
- Accessibility
- Localization and timezone support
- Disaster recovery and business continuity
- Observability and operational support
- Customer / policyholder
- Insured person
- Proposer / applicant
- Beneficiary / nominee
- Agent / advisor
- Broker
- Underwriter
- Claims assessor
- Claims investigator
- Finance user
- Customer service representative
- Compliance officer
- Administrator
- Auditor
- Availability and resilience
- Privacy and data minimization
- Strong authentication and authorization
- Encryption in transit and at rest
- Audit logging
- Accessibility
- Localization and timezone support
- Disaster recovery and business continuity
- Observability and operational support
````

## 2. 02_domain_glossary.md #0

Score: 1.000

````text
| Term | Definition |
|---|---|
| Policy | Contract of insurance issued by the insurer |
| Policyholder | Person or organization owning the policy |
| Insured | Person, asset, or interest covered by the policy |
| Premium | Amount payable for insurance coverage |
| Sum insured | Maximum insured amount, subject to policy terms |
| Deductible | Amount borne by the insured before coverage applies |
| Rider / endorsement | Additional or modified policy provision |
| Quote | Indicative price and coverage before binding |
| Proposal | Application requesting insurance |
| Underwriting | Evaluation of risk and eligibility |
| FNOL | First Notice of Loss |
| Reserve | Estimated amount set aside for a claim |
| TPA | Third-party administrator |
| Reinsurance | Risk transfer from insurer to reinsurer |
| Grace period | Permitted period after a due date |
| Lapse | Policy status resulting from unmet conditions, often non-payment |
| Reinstatement | Restoration of a lapsed policy subject to rules |
````

## 3. 03_process_catalog.md #0

Score: 1.000

````text
- Customer registration
- Identity verification and KYC
- Quote generation
- Product comparison
- Proposal submission
- Document upload
- Payment collection
- Policy issuance
````

## 4. 03_process_catalog.md #1

Score: 1.000

````text
- View policy
- Download policy documents
- Change contact details
- Change nominee / beneficiary
- Add or remove coverage
- Endorsement
- Cancellation
- Renewal
- Reinstatement
- Premium payment and receipts
````

## 5. 03_process_catalog.md #2

Score: 1.000

````text
- FNOL
- Claim validation
- Coverage verification
- Document collection
- Surveyor / assessor assignment
- Fraud screening
- Reserve creation
- Approval and repudiation
- Settlement
- Recovery and subrogation
- Claim closure and reopening
````

## 6. 03_process_catalog.md #3

Score: 1.000

````text
- Product approval
- Rule versioning
- Approval workflows
- Delegation of authority
- Complaint and grievance handling
- Regulatory reporting
- Audit and retention
````

## 7. 04_requirements_quality_rules.md #0

Score: 1.000

````text
Every requirement must be:
- Atomic: one primary behavior or obligation.
- Testable: measurable acceptance criteria exist.
- Unambiguous: avoid vague terms such as fast, user-friendly, or appropriate.
- Traceable: linked to business objective, process, rule, data, API, and test.
- Role-specific: actor and permissions are explicit.
- State-aware: preconditions, status transitions, and exceptions are defined.
- Configurable where appropriate: product, jurisdiction, channel, and effective date are identified.
- Secure and privacy-aware: sensitive data handling is explicit.
- Auditable: actor, timestamp, before/after values, reason, and correlation ID are defined when relevant.
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
````

## 8. 05_reference_architecture.md #0

Score: 1.000

````text
- Web and mobile channels
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
- REST/JSON for synchronous interactions
- Events for lifecycle changes
- Batch interfaces for legacy and regulatory exchanges
- Idempotency keys for payment and document operations
- Correlation IDs across all services
- Retry, timeout, circuit breaker, and dead-letter handling
- Least privilege and role-based access control
- MFA for privileged and sensitive actions
- Secrets managed outside source code
- Input validation and output encoding
- PII masking in logs
- Immutable audit records
- Retention and deletion policies
````

## 9. 06_data_model_reference.md #0

Score: 1.000

````text
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
- Use immutable identifiers.
- Store effective-from and effective-to dates for versioned business rules.
- Record source system and data ownership.
- Define required, optional, conditional, and prohibited fields.
- Define precision and rounding for monetary values.
- Store currency and timezone explicitly.
- Apply retention, archival, masking, and deletion rules by data category.
````

## 10. code-transformation-specification-53f0d353-e27d-4521-8202-abfc8b41ed7c.md #0

Score: 1.000

````text
This document is an evidence-based planning specification for AI-assisted development tools. It is not an automated rewrite.
- **Repository:** Aishwarya-Clinic
- **Project type:** generic
- **Languages:** PHP, Shell, CSS, JavaScript, SQL, Markdown, JSON, Gitignore/Gitattributes
- **Runtimes:** Node.js unknown, PHP unknown
- **Frameworks:** Not detected
- **Files assessed:** 61
- **Evidence available:** source yes, manifests yes, lockfiles no
Detected module paths are starting points only. They are not a complete impact inventory.
````

## 11. code-transformation-specification-53f0d353-e27d-4521-8202-abfc8b41ed7c.md #1

Score: 1.000

````text
| Concern | Current evidence | Selected target |
|---|---|---|
| API / backend | PHP, Shell, CSS, JavaScript, SQL, Markdown, JSON, Gitignore/Gitattributes | ASP.NET Core |
| UI | Not detected from assessment | Angular |
````

## 12. code-transformation-specification-53f0d353-e27d-4521-8202-abfc8b41ed7c.md #2

Score: 1.000

````text
- Map the current generic implementation to ASP.NET Core and Angular only after repository discovery confirms each component.
- Usage is not yet known. The implementation agent must inspect the repository before deciding which files must change.
Do not make compatibility conclusions or begin implementation edits until the repository impact inventory is complete.
- Preserve existing request/response contracts until the impact inventory identifies a required change.
- Do not make compatibility conclusions or begin implementation edits until the repository impact inventory is complete.
- Rebuild UI composition in Angular from discovered screens, routes, and state — not from guessed file lists.
- Usage is not yet known. The implementation agent must inspect the repository before deciding which files must change.
- Treat persistence, schemas, and integrations as unchanged until discovery records the exact models and connection points.
- Record runtime, build, CI, container, and environment changes only after they are found in the repository.
- This specification does not claim that empty seed-path lists mean unused code.
- No extra transformation constraints were supplied.
Package tasks refer to the completed impact inventory. They never assume that source locations were supplied or that an empty path list proves there is no usage.
- **Action:** Establish a baseline and complete a repository impact inventory for every selected transformation.
- **Done when:** Every affected path, API, configuration key, and protecting test is recorded, including negative-search results.
- **Action:** Create the ASP.NET Core application scaffold and module layout from the completed inventory.
- **Done when:** The target API project builds and hosts the discovered entrypoints without unverified rewrites.
- **Action:** Recreate discovered UI surfaces in Angular using the inventory, not assumed file lists.
- **Done when:** Each discovered screen or API-only behavior has a measurable acceptance check.
````
