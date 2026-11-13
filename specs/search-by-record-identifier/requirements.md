# Implementation Requirements Checklist

**Purpose**: Provide an implementation acceptance checklist that agents can execute one item at a time.
**Feature**: 78138 — As a Service Agent, I want to search by a single-record identifier so that I can retrieve the correct customer and account summary quickly

## Functional Acceptance Criteria

- [ ] [R1] Service Agent can submit a single-record identifier via the persistent global search and trigger a coordinated customer + account summary lookup
- [ ] [R2] Search returns matching customer summary and related account summary within 2 seconds under expected load (AC1)
- [ ] [R3] Returned summary data corresponds strictly to the searched identifier; no mixing of customer/account data from unrelated records (AC2)
- [ ] [R4] When a new valid identifier is searched, prior summary data is cleared or replaced by loading skeletons before new results render — stale record data must never appear as current (AC2, design prompt step 9)
- [ ] [R5] Partial result path implemented: customer found with no related account keeps customer summary visible and shows a controlled no-account state (design prompt step 8)
- [ ] [R6] No-match path implemented: controlled "no data found" state shown when identifier yields no record
- [ ] [R7] Retrieval error path implemented: controlled upstream error state shown on summary fetch failure
- [ ] [R8] Required input validation occurs before the search request is submitted (AC5)
- [ ] [R9] Happy-path flow (enter identifier → validate → resolve context → fetch → display unified summary) is implemented end-to-end (AC4)

## UI Acceptance Criteria

- [ ] [R10] Search Workspace screen renders customer summary and account summary together in one unified view tied to the active searched record
- [ ] [R11] Search bar includes label ("Search by customer/account identifier"), search action, and clear/reset action
- [ ] [R12] Active record context banner displays the current searched identifier and resolved context
- [ ] [R13] All required search states are implemented: empty/default, validating input, loading summaries, successful result, no matching record, partial result (customer-only), retrieval error
- [ ] [R14] Customer Summary panel implemented as read-only card with representative fields (name, customer ID, contact summary, status, segment/type, servicing indicators), including loading skeleton and empty/no-data messaging
- [ ] [R15] Account Summary panel implemented alongside Customer Summary with representative fields (account ID, type, status, balance/summary indicators, payment details only if permitted), including normal, no-account, retrieval-failure, and masked-restricted-field states
- [ ] [R16] PCI-sensitive and restricted fields are masked using the masked-data-field pattern; full PAN or sensitive payment values are never rendered
- [ ] [R17] Reusable empty/error state components used for invalid identifier input, no data found, customer-without-account, and upstream retrieval error, each with title, short explanation, and recovery action — no technical internals exposed
- [ ] [R18] Inline validation messaging shown for invalid identifier format prior to request submission
- [ ] [R19] Accessible color contrast, visible focus states, keyboard navigation, clear form labels, and status/masked indicators distinguishable without relying on color alone
- [ ] [R20] Desktop-first responsive layout consistent with existing design-system direction (compact density, cards/panels/badges hierarchy)

## API and Integration Acceptance Criteria

- [ ] [R21] Single-record identifier search operation implemented as a coordinated service call returning customer summary and related account summary in one flow
- [ ] [R22] API/service inputs: single identifier value; validated for required format before invoking backend lookup
- [ ] [R23] API/service outputs: customer summary, account summary (or explicit no-account indicator), and error/no-match conditions distinctly represented
- [ ] [R24] Role-based authorization applied to the lookup so protected/restricted fields are masked or withheld per entitlement before reaching the UI (AC3)
- [ ] [R25] Versioned API contract labels (e.g., `/api/v1`) used per Feature Flags / Rollout Status convention — exact endpoint path/schema is Open Question (see below) and must not be invented
- [ ] [R26] Existing authenticated-session and authorization middleware reused; no bypass of WorkOS SSO/session validation for this search flow
- [ ] [R27] Backward compatibility preserved for any existing customer/account retrieval contracts this feature extends

## Business Logic and Data Acceptance Criteria

- [ ] [R28] Identifier resolution logic correctly maps a single-record identifier to its active customer/account context before data retrieval
- [ ] [R29] Business rule: new valid search invalidates/replaces all prior active record context server- and client-side (no cross-record leakage)
- [ ] [R30] Business rule: customer summary remains independently valid and displayable even when no related account exists
- [ ] [R31] Required entity fields for Customer Summary and Account Summary persisted/retrieved per the representative field lists in the source design
- [ ] [R32] Field-level validation rule enforced for identifier format prior to persistence-layer query execution
- [ ] [R33] Error handling covers: invalid input, no match, partial result (no account), and upstream retrieval failure as distinct, non-overlapping states
- [ ] [R34] Data persistence/retrieval uses the application database as the source of truth for customer/account summary data

## Non-Functional Acceptance Criteria

- [ ] [R35] 2-second SLA for identifier lookup is measured, enforced, and surfaced via operational telemetry (AC1)
- [ ] [R36] Authentication and authorization checks (WorkOS SSO, session/token validation, authorization scope) gate access to the search feature and its data
- [ ] [R37] Input validation guards against malformed/invalid identifiers before query execution
- [ ] [R38] Role-based field masking/restriction applied consistently per customer privacy obligations and PCI constraints (AC3)
- [ ] [R39] Audit events are written for each identifier search/lookup without exposing restricted payload data
- [ ] [R40] Operational telemetry (latency, success/failure counts, degraded/timeout indicators) emitted for the lookup path, consumable by Monitoring/API Performance admin views
- [ ] [R41] Feature-flag-aware behavior respected: if this search capability is disabled, it is hidden from navigation/search entry and direct access attempts produce a controlled denial state
- [ ] [R42] No stack traces, token contents, internal field/API names, or raw backend payloads exposed in any UI or error state
- [ ] [R43] Highest-risk behavior under test/verification: cross-record data leakage prevention, SLA timing, masking of restricted fields, and no-account/no-match/error state correctness

## Traceability

- [ ] [R44] Every implemented change maps back to US 78138 Acceptance Criteria 1–5 and the Design Prompt's Single-Record Search Workspace, Customer Summary, and Account Summary specifications
- [ ] [R45] Every non-blocking Open Question that was implemented has a recorded decision + one-line rationale in specs/78138/assumptions.md (no Open Question is silently assumed)
- [ ] [R46] No BLOCKING Open Question was implemented as an assumption (a feature with an unresolved blocking question is held at needs-clarification, not completed)

**Open Questions (must not be implemented as assumptions):**
- [R47] Exact identifier format/pattern (e.g., account number, customer ID, SSN-based, composite key) is not specified in source — validation rules must not be invented; hold as Open Question until clarified.
- [R48] Exact API endpoint path, request/response schema, and backend data source tables/fields beyond "application database" are not specified — must be validated against actual backend contracts before implementation, not invented.
- [R49] Specific role/permission matrix governing which fields are masked for which Service Agent roles is not specified — masking logic must not assume a specific rule set beyond "role-based authorization" and "PCI-sensitive masking" already stated.
- [R50] Definition of "expected load" for the 2-second SLA (concurrent users, request volume) is not specified — must be clarified before performance testing thresholds are finalized.

## Assumptions

- [R51] (none)

## Notes

- [R52] Never resolve an Open Question silently. In an unattended run, record the chosen assumption + rationale in specs/78138/assumptions.md; blocking questions must instead hold the feature at needs-clarification.
- [R53] Mark an item complete only after verifying actual implementation code and behavior.