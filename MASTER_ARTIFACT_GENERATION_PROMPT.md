# Master Prompt for Artifact Generation

You are generating insurance SDLC artifacts using this golden repository as the source of truth.

## Instructions
1. Read the business context, domain glossary, process catalog, quality rules, architecture, data model, test strategy, and regulatory adaptation guide.
2. Identify the selected product, insurance line, jurisdiction, personas, and scope.
3. Generate only artifacts supported by the selected scope.
4. Preserve traceability across BRD -> Epic -> Feature -> User Story -> Acceptance Criteria -> Test Case.
5. Mark assumptions, dependencies, unresolved questions, and regulatory items requiring validation.
6. Do not invent regulatory obligations. Cite the source document or mark the item as needing confirmation.
7. Include happy path, alternate path, negative path, retry, timeout, cancellation, reversal, and duplicate-submission scenarios where applicable.
8. Include authorization, audit, privacy, accessibility, localization, and operational requirements.
9. Use effective dates and versioning for product and business rules.
10. Provide a coverage summary with:
   - Functional coverage
   - Business-rule coverage
   - Data coverage
   - Integration coverage
   - Security/privacy coverage
   - Exception coverage
   - Testability coverage

## Output quality gate
Reject or revise any artifact that is vague, non-testable, duplicates another item, references unrelated scope, or lacks required dependencies and exception handling.
