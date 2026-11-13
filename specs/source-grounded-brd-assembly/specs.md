# Feature: DemoIntern Business Requirements Document
Status: NEW
Owner: Astra
Last Updated: 2026-11-13

## Summary
- Deliver a source-grounded Business Requirements Document for DemoIntern as a generated Markdown artifact.
- The feature solves inconsistent, manually assembled BRD output by enforcing:
  - approved-source-only compilation
  - deterministic Markdown structure
  - governed section formatting
  - explicit marking of unspecified information
  - traceability and validation before delivery
- Expected outcome:
  - authorized users can generate/review a DemoIntern BRD in Markdown
  - invalid or structurally non-compliant output is blocked
  - missing source details are marked, not inferred

## Scope
### In Scope
- Compile DemoIntern BRD content from approved source requirements and project inputs only.
- Generate a non-empty Markdown BRD artifact.
- Enforce consistent heading hierarchy and section order for the same input set.
- Validate and reject malformed/disallowed formatting.
- Render Document Control as a two-column Markdown table.
- Render Section 6 requirements as a five-column Markdown table.
- Include document version and status when present; explicitly mark when absent.
- Include supplied citation catalog in References and reuse entries consistently in Traceability outputs.
- Exclude duplicate citation entries.
- Mark unspecified information explicitly across affected BRD sections.
- Prevent invented content for missing approvals, access controls, exception handling, retention, fallback behavior, and similar unspecified details.
- Enforce DemoIntern project identity and project-management context alignment.
- Authenticate and authorize BRD generation/review access.
- Log generation, validation, traceability, and audit-relevant events.
- Provide UI for Documentation Lead review/manage flows across:
  - BRD Workspace / Draft Review
  - Missing Information Flags
  - Validation Results
  - Source Inputs Review
  - Audit Trail

### Out of Scope
- DOCX, PDF, rich-text, or other non-Markdown exports.
- User-defined BRD section reordering.
- Custom Markdown themes, styling extensions, or repository-specific rendering tweaks.
- Redesigning Document Control fields.
- Multi-column alternatives or rich-text table formatting for Document Control.
- Redefining Section 6 field meaning.
- Editing requirement source content in the renderer.
- Approval workflow actions after Section 6 generation.
- Editing project purpose text beyond approved source inputs.
- Cross-project template customization outside DemoIntern project-management context.
- Advanced customization noted as future work.

## Application Type & Platform Context
- Mixed application:
  - Web application for review and management flows.
  - Generation/service behavior for Markdown assembly and validation.
- Source evidence:
  - "Create a system design web application..."
  - "Desktop-first layout."
  - "Expose API and UI."
  - "The generation service composes headings, section bodies, lists, and line breaks into a single Markdown payload."

## Actors and Permissions
- Project Manager
  - Can request DemoIntern BRD generation.
  - Must be authenticated and have access to BRD generation.
- Business Analyst
  - Can initiate BRD generation/preview.
  - Can compile source-grounded BRD content.
- Requirements Analyst
  - Can initiate generation/preview for Section 6.
- Documentation Lead
  - Can review/manage BRD generation and missing-information handling.
- Review Coordinator
  - Consumes governance-relevant status/version details in the BRD.
- Authorized BRD generation/review users
  - Can execute context-alignment generation flow or view validation results.
- Unauthorized users
  - Cannot trigger generation/export/Section 6 assembly or view protected validation results.

## Feature Development Intent
- Build controlled BRD generation and review behavior for DemoIntern.
- Replace manual/incomplete assembly with governed compilation, formatting, validation, and auditability.
- Deliver:
  - a valid Markdown BRD artifact
  - section-level source traceability
  - explicit missing-information markers
  - structurally compliant Document Control and Section 6 tables
  - gated submission/review flow based on validation

## UI Design & Interaction Contract
### Supported UI Surfaces
- BRD Workspace / Draft Review
- Missing Information Flags
- Validation Results
- Source Inputs Review
- Audit Trail

### Workspace / Draft Review
- Header includes:
  - product name: `DemoIntern BRD Manager`
  - breadcrumbs: `Projects / DemoIntern / Epic BRD`
  - draft status pill: `In Review`
  - last saved timestamp
  - actions: `Save Draft`, `Validate`, `Submit for Review`
- Left sidebar navigation:
  - Overview
  - Source Inputs
  - BRD Sections
  - Missing Information Flags
  - Validation Results
  - Audit Trail
- Main content:
  - left: BRD section navigator accordion
  - right: selected section detail
- Section list includes:
  - Business Objective
  - Current State
  - Desired State
  - Functional Requirements
  - User Interaction Flow
  - Technical Considerations
  - Out of Scope
  - Success Metrics
  - Approvals
  - Access Controls
  - Exception Handling
  - Retention
  - Fallback Behavior
- Section status chip states:
  - Complete
  - Needs Review
  - Missing Source Info
- Missing-information marker component:
  - warning icon
  - title: `Unspecified in source`
  - helper text: `No source information provided. Do not infer content.`
  - amber emphasis
  - optional `Flag for Reviewer` control
- Source traceability panel beneath content:
  - source coverage
  - linked source excerpts
  - confidence/coverage badge
  - note: `No replacement content generated for unspecified items`
- Sticky review utility panel:
  - Total sections
  - Missing info markers
  - Reviewer flags added
  - Validation status
  - CTAs: `Run Validation`, `View Missing Information List`

### Missing Information Flags Page
- Header:
  - title: `Missing Information Flags`
  - subtitle: `Track unspecified source details without generating assumptions.`
- Toolbar:
  - search flags
  - filter by section
  - filter by marker status
  - export list
- Table columns:
  - BRD Section
  - Missing Detail Type
  - Marker Text
  - Reviewer Flag
  - Validation State
  - Source Coverage
  - Last Updated
- Rows include at least:
  - Approvals
  - Access Controls
  - Exception Handling
  - Retention
  - Fallback Behavior
  - other sections with missing source detail
- Row elements:
  - amber `Unspecified in source` badge
  - flag toggle/checkbox
  - validation status chip such as `Needs validation` or `Valid`
  - row actions: View section, Edit flag, View source
- Bulk actions when rows selected:
  - Flag selected
  - Remove flags
  - Validate selected
- Empty state required when filtered view has no unspecified information.

### Validation Results Page
- Status banner:
  - success: `Validation complete`
  - error: `Validation failed`
- Rules panel lists:
  - required inputs present before completing flagging flow
  - markers applied consistently
  - no replacement content generated for unspecified approvals/access/exception/retention/fallback details
  - draft contains source traceability references
- Result cards:
  - Passed checks
  - Failed checks
  - Warnings
- Issue list columns:
  - Severity
  - Section
  - Problem
  - Recommended action
- Blocking issue examples supported:
  - `Reviewer flag action incomplete: required input missing`
  - `Section contains inferred content where source was unspecified`
  - `Missing marker not applied consistently`
- Footer actions:
  - `Back to Draft`
  - `Re-run Validation`
  - `Submit for Review`
- `Submit for Review` is disabled when blocking errors exist.

### Source Inputs Review
- Shows source input inventory for the epic.
- Supported input groupings:
  - Context & Background
  - Current State
  - Desired State
  - Key Functionality
  - User Interaction Flow
  - Technical Considerations
  - Out of Scope
  - Success Metrics
  - Persona
- For each source input show:
  - raw source text snippet
  - status: Available / Partial / Missing
  - mapped BRD sections
- Callout:
  - `Source gaps must be marked explicitly in the BRD. Do not fill gaps with assumptions.`
- Compare view toggle:
  - source input left
  - generated BRD text right

### Audit Trail
- Timeline of:
  - BRD generated
  - Missing markers inserted
  - Reviewer flags added
  - Validation run
  - Draft saved
  - Submission attempted
- Event details:
  - timestamp
  - actor
  - action
  - impacted section
  - result

### Interaction Requirements
- Sidebar navigation between all 5 pages.
- Section accordion selection in Workspace.
- Flag toggle interactions.
- `Run Validation` navigates to Validation Results.
- `Back to Draft` returns to Workspace.
- Submit uses confirmation modal and success state with submitted badge.
- Table row click from Missing Information Flags opens related BRD section.
- Save Draft shows confirmation toast: `Draft saved successfully`
- Blocking validation shows error toast: `Validation blocked. Resolve required inputs.`

### Accessibility / Readability
- Markdown output must remain readable in standard Markdown tools.
- Unsupported executable/raw markup and malformed structure must be excluded.

## API Contract
- Source supports system behaviors, not concrete endpoint definitions.
- Required service/API capabilities:
  - submit BRD generation request for DemoIntern
  - return Markdown artifact on success
  - return generation failure with specific violated formatting rule on structural validation failure
  - support generation/preview flows for sections such as Document Control and Section 6
  - support validation execution and returning pass/fail issues
  - support persistence/retrieval of drafts, markers, flags, and validation state
- Security contract:
  - authenticated access required
  - generation/export restricted to authorized roles
  - validation results visible only to authorized generation/review users
- Error contract:
  - do not publish artifact when required structural validation fails
  - flag generation error instead of returning malformed Document Control output
  - block generation on conflicting project identifier
- Observability contract:
  - log requestId, documentId, validation result, and rule failure details
  - log section-specific details where defined (e.g., sectionId `"6"`, rowCount, missingCellCount, requestingUserId)

## Business Logic & Rules
- Compile BRD content only from approved source requirements and project inputs in the allowed source set.
- Exclude unapproved, archived, or unmapped source records from compilation.
- Do not persist text from ineligible source records in generated BRD sections.
- Each generated BRD section must have one or more source links; otherwise flag a compilation issue and do not save untraceable content.
- Preserve DemoIntern terminology from selected sources.
- Do not add unsupported/inferred business statements.
- Generate non-empty Markdown artifact only.
- Use consistent heading hierarchy and fixed section order for identical source inputs.
- Reject disallowed HTML/raw executable markup and malformed structural syntax that breaks Markdown readability.
- If structural validation fails, do not publish artifact; return violated rule.
- Document Control rules:
  - render as Markdown table
  - exactly two columns per data row: field label and field value
  - include document version and document status when source metadata provides them
  - if source value absent, output approved missing-value marker
  - escape/normalize table-breaking characters such as unescaped pipe
  - if valid two-column structure cannot be built, raise generation error
- Section 6 rules:
  - render requirement rows in mandated five-column table
  - preserve column order across all rows
  - include source requirement identifier and description without truncation, swapping, or silent omission
  - if a required column value is unavailable, render row and mark only missing cell
  - render table header exactly once whether there are one, many, or zero requirements
- Citation rules:
  - include supplied citation catalog in References
  - reuse citation entries consistently between References and Traceability outputs
  - exclude duplicate citation entries
- Missing-information rules:
  - explicitly mark unspecified information wherever source inputs are incomplete
  - do not generate replacement content for missing approvals, access controls, exception handling, retention, fallback behavior
  - apply markers consistently across affected sections
- Context-alignment rules:
  - BRD generated from DemoIntern source set must include `DemoIntern` as project name in framing fields populated by this flow
  - purpose/scope framing must use project-management-aligned language derived from source inputs
  - conflicting non-DemoIntern project identifier blocks generation
  - unrelated domain context not mapped to approved project-management classification is excluded and logged
- Submission/review rules:
  - `Submit for Review` remains disabled until validation passes
  - reviewer flag action requires required-input validation before completion

## Data Model & Validation
### Source-Supported Data Elements
- documentId
- requestId
- sectionId (`"6"` explicitly referenced for Section 6 logging)
- projectName
- context classification values
- source record IDs / source IDs
- approval status / approval-state metadata
- document version
- document status
- citation catalog / citation entries
- requirementId
- requirementText
- three additional mandated Section 6 columns (names not provided)
- source links / source references
- reviewer flags
- validation state / validation result
- blocked phrases
- generation timestamp / last updated / timestamp
- requestingUserId
- actor
- impacted section
- result
- rowCount
- missingCellCount
- excluded source count

### Validation Rules
- Markdown artifact must be non-empty.
- Heading tokens and ordered section map must validate.
- Required section sequence must validate.
- Disallowed HTML and malformed table syntax must be rejected if unsupported.
- Document Control table must validate to exactly two columns per row.
- Section 6 table must validate to exactly five columns per row.
- Rows with missing values must remain present with configured missing-information formatting.
- Each compiled section must have at least one approved source reference before persistence.
- Conflicting project identifiers must cause validation failure.
- Required inputs must validate before completing flagging flow.
- Unauthorized users must be blocked from generation/export/review actions.

## Functional Requirements
1. [FR1] The system shall compile DemoIntern BRD content only from approved source requirements and project inputs in the allowed source set.
2. [FR2] The system shall exclude unapproved, archived, or otherwise ineligible source records from compilation and shall not persist their text in generated BRD content.
3. [FR3] The system shall store each generated BRD section with at least one source link to originating approved records.
4. [FR4] The system shall flag a compilation issue when a BRD section lacks at least one approved source reference and shall not save untraceable section content.
5. [FR5] The system shall preserve DemoIntern terminology from selected source material and shall not add unsupported business statements.
6. [FR6] The system shall return a non-empty Markdown artifact for successful DemoIntern BRD generation.
7. [FR7] The system shall use a consistent heading hierarchy and preserve defined BRD section order for the same source input set.
8. [FR8] The system shall exclude unsupported formatting constructs, including raw executable markup and malformed structural syntax that breaks Markdown readability.
9. [FR9] The system shall block artifact publication and return the specific violated formatting rule when structural validation fails.
10. [FR10] The system shall require authenticated access for BRD generation.
11. [FR11] The system shall restrict generation/export/review actions to authorized users.
12. [FR12] The system shall sanitize source content before rendering and prevent script or unsafe raw markup injection.
13. [FR13] The system shall render Document Control as a Markdown table with exactly two columns per data row.
14. [FR14] The system shall place document version and document status in corresponding Document Control rows when source metadata includes those values.
15. [FR15] The system shall output the approved missing-value marker in a Document Control value cell when the source value is absent.
16. [FR16] The system shall escape or normalize table-breaking characters in Document Control values to preserve the two-column layout.
17. [FR17] The system shall flag Document Control as a generation error when a valid two-column structure cannot be built.
18. [FR18] The system shall render DemoIntern BRD Section 6 as a five-column Markdown table with preserved column order.
19. [FR19] The system shall include requirement identifier and requirement description in each Section 6 row without truncation, field swapping, or silent omission.
20. [FR20] The system shall render Section 6 rows even when some required column values are unavailable and shall mark only missing cells using configured missing-information formatting.
21. [FR21] The system shall render the Section 6 table header exactly once for one, many, or zero requirements.
22. [FR22] The system shall record an auditable event for authorized Section 6 generation attempts with document identifier and timestamp.
23. [FR23] The system shall include the supplied citation catalog in the References section.
24. [FR24] The system shall reuse citation entries consistently between References and Traceability outputs.
25. [FR25] The system shall exclude duplicate citation entries.
26. [FR26] The system shall explicitly mark unspecified DemoIntern BRD information wherever source inputs are incomplete.
27. [FR27] The system shall not generate replacement content for missing approvals, access controls, exception handling, retention, or fallback behavior.
28. [FR28] The system shall apply missing-information markers consistently across affected sections.
29. [FR29] The system shall include `DemoIntern` as the project name in document framing fields populated by this flow.
30. [FR30] The system shall use project-management-aligned language in purpose and scope framing content derived from source inputs.
31. [FR31] The system shall block BRD generation when a conflicting project identifier different from `DemoIntern` is detected and shall identify the conflicting value.
32. [FR32] The system shall exclude unrelated domain context not mapped to the approved project-management classification and shall log the exclusion.
33. [FR33] The system shall include document status and version details in the BRD when present in source inputs and preserve those governance labels consistently across the artifact.
34. [FR34] The system shall explicitly mark unavailable status or version details rather than leaving empty ambiguous values.
35. [FR35] The system shall provide the five specified UI pages for Documentation Lead review/manage flow.
36. [FR36] The system shall disable `Submit for Review` until validation passes.
37. [FR37] The system shall validate required inputs before completing reviewer flag actions.
38. [FR38] The system shall persist reviewer flags and validation states.
39. [FR39] The system shall provide source traceability visibility in the UI for reviewed BRD sections.
40. [FR40] The system shall log generation, validation, traceability, and key draft activity events for audit and troubleshooting.

## Non-Functional Requirements
- Security
  - authenticated access required
  - authorization required for generation/export/review actions
  - sanitize content fragments before rendering
  - prevent script/raw HTML injection
- Performance
  - generation should complete within an acceptable request window for a single BRD
  - Document Control rendering should add negligible latency
  - Section 6 should support full requirement set without noticeable delay
  - context validation should not materially delay small document builds
- Reliability / Determinism
  - identical source inputs should produce textually consistent Markdown output
  - table rendering and generation should remain deterministic across repeated runs
- Observability
  - structured logging for requestId, documentId, validation outcomes, rule failures, source inclusion/exclusion, row/missing counts, timestamps, and actor context
- Readability
  - generated Markdown must be readable in standard Markdown preview/repository tools

## Acceptance Scenarios
### Scenario 1: Successful BRD Markdown generation
**Given** an authenticated authorized user requests BRD generation for DemoIntern with approved source inputs  
**When** generation succeeds  
**Then** the system returns a non-empty Markdown artifact  
**And** the artifact uses a consistent heading hierarchy and defined section order  
**And** the content excludes disallowed raw executable markup and malformed structural syntax

### Scenario 2: Structural validation failure blocks publication
**Given** a DemoIntern BRD generation attempt produces invalid structure  
**When** structural validation runs  
**Then** the system does not publish the artifact  
**And** returns a generation failure response identifying the specific violated formatting rule

### Scenario 3: Approved-source-only compilation
**Given** candidate source records include approved and unapproved DemoIntern inputs  
**When** BRD compilation executes  
**Then** only approved records in the allowed source set are used  
**And** unapproved/ineligible record text is excluded from generated sections  
**And** each saved section has at least one source link

### Scenario 4: Untraceable section is blocked
**Given** a BRD section cannot be compiled with at least one approved source reference  
**When** the system attempts to save compiled output  
**Then** the system flags the section as a compilation issue  
**And** does not save untraceable content

### Scenario 5: Document Control with present values
**Given** source metadata contains document version and document status  
**When** Document Control is generated  
**Then** it is rendered as a Markdown table with exactly two columns per data row  
**And** version and status appear in corresponding rows

### Scenario 6: Document Control with missing or unsafe values
**Given** a Document Control value is absent or contains a table-breaking pipe character  
**When** the section is generated  
**Then** absent values are replaced with the approved missing-value marker  
**And** unsafe table-breaking characters are escaped or normalized  
**And** if valid two-column output still cannot be built, generation returns a section error

### Scenario 7: Section 6 with mixed completeness
**Given** Section 6 has requirement records and some mapped column values are unavailable  
**When** Section 6 is generated  
**Then** the system renders one header row and one row per requirement in a five-column table  
**And** preserves requirement identifier and description  
**And** marks only missing cells rather than dropping the row

### Scenario 8: Section 6 with zero requirements
**Given** zero requirements are available for Section 6  
**When** the section is generated  
**Then** the table header is rendered exactly once  
**And** the Markdown remains structurally valid

### Scenario 9: Missing-information handling
**Given** source inputs are incomplete for approvals, access controls, exception handling, retention, or fallback behavior  
**When** the BRD is generated/reviewed  
**Then** the system shows explicit missing-information markers  
**And** does not generate replacement content for those unspecified details  
**And** applies the marker format consistently across affected sections

### Scenario 10: Context alignment failure
**Given** a source item contains a project identifier different from DemoIntern  
**When** BRD generation runs  
**Then** generation is blocked  
**And** the validation response identifies the conflicting value

### Scenario 11: Citation catalog reuse and deduplication
**Given** the supplied citation catalog contains entries referenced multiple times  
**When** References and Traceability outputs are generated  
**Then** the system includes the catalog in References  
**And** reuses entries consistently across References and Traceability  
**And** excludes duplicate citation entries

### Scenario 12: Validation-gated submit in UI
**Given** a Documentation Lead is reviewing a BRD draft with unresolved blocking validation issues  
**When** the user attempts `Submit for Review`  
**Then** the submit action is disabled or blocked  
**And** the user is taken to Validation Results highlighting blocking issues  
**And** an error message indicates required inputs must be resolved

## Traceability Matrix
| Source ID | Requirement | Acceptance Criteria | Test Coverage |
|---|---|---|---|
| US 80999 | FR6-FR12 | AC1-AC5 | Markdown artifact generation, structure validation, auth/security tests |
| US 81007 | FR13-FR17 | AC1-AC5 | Document Control table structure, missing marker, escaping, error handling tests |
| US 81014 | FR18-FR22 | AC1-AC5 | Section 6 table shape, preservation, missing-cell, zero-row, auth/audit tests |
| US 81021 | FR23-FR25 | AC1-AC5 | References inclusion, Traceability reuse, dedupe, input validation tests |
| US 81030 | FR29-FR32 | AC1-AC5 | Project identity, context alignment, conflict blocking, exclusion logging, auth tests |
| US 81037 | FR14-FR15, FR33-FR34 | AC1-AC5 | Status/version population, governance label consistency, missing marker, input validation tests |
| US 80982 | FR1-FR5, FR40 | AC1-AC5 | Approved-source filtering, exclusion, traceability persistence, terminology preservation tests |
| US 80990 | FR26-FR28, FR35-FR39 | AC1-AC5 | Missing-information markers, no inference, consistency, validation gating, persistence tests |
| US 80978 | FR1-FR5 | Story description | Source-grounded assembly tests |

## Open Questions
- What is the exact approved missing-value marker text to use in Markdown cells/sections?
- What is the exact missing-information formatting for Section 6 missing cells?
- What are the mandated row labels for Document Control beyond version and status?
- What are the exact five Section 6 column names and required order?
- What are the exact BRD section names/order for the generated Markdown artifact beyond the UI section navigator and specifically governed sections?
- What are the precise framing fields where the project name `DemoIntern` must appear?
- What exact phrases/tags define approved project-management context vs unrelated domain context?
- What are the exact authorized roles/permissions for generation, review, export, preview, and validation-result access?
- What are the required inputs for completing the reviewer flag action?
- Should the web UI generate full BRD artifacts, previews only, or both?
- What persistence model is required for drafts, markers, reviewer flags, validation results, and submitted state?
- What is the exact API surface, transport, and response schema for generation, validation, save draft, submit, and audit retrieval?
- What is the expected behavior for References/Traceability output format in Markdown?
- What are the SLA/response-time thresholds referenced as acceptable request window / meet SLA requirements?
- What coverage/confidence badge values and rules should the UI display?
- Are there retention requirements for drafts, logs, audit trail entries, and source-link metadata?

## Source References
- Feature 80977 — DemoIntern Business Requirements Document
- US 80978 — Source-Grounded BRD Content Assembly
- US 80999 — Markdown BRD generation
- US 81007 — Document Control two-column structure
- US 81014 — Section 6 five-column table
- US 81021 — Citation catalog in References
- US 81030 — DemoIntern project-management context alignment
- US 81037 — Document status and version details
- US 80982 — Compile BRD content from approved source requirements
- US 80990 — Flag unspecified DemoIntern BRD information
- Source design prompt included in SOURCE CONTEXT for supported web UI pages, navigation, states, and interactions
- No Golden Repo file paths were provided in source context.