# Feature: As a Service Agent, I want to search by a single-record identifier so that I can retrieve the correct customer and account summary quickly
Status: NEW
Owner: Astra
Last Updated: 2026-11-13

## Summary
Service Agents currently rely on fragmented legacy retrieval paths to look up customer and account information, which slows daily servicing operations and increases the risk of retrieving incorrect or mismatched records. This feature introduces a single-record identifier search within the Unified CM & AM Workspace that allows a Service Agent to retrieve a coordinated customer summary and account summary for the exact record searched, within a defined performance SLA. The expected outcome is a faster, more reliable, and auditable lookup experience that replaces manual or incomplete legacy processes with a single, unified search and summary view.

## Scope
**In scope:**
- Single-record identifier search entry point (global search) within the authenticated workspace.
- Input validation of the identifier prior to request submission.
- Coordinated retrieval and display of customer summary and account summary tied to the same searched identifier.
- Handling of no-match, partial-match (customer found, no account), and retrieval-error states.
- Replacement of prior search context/data when a new valid search is performed (no stale data carryover).
- Role-based masking/restriction of protected and PCI-sensitive fields in the account summary.
- Performance requirement: summary retrieval within 2 seconds under expected load.
- Audit event logging and operational telemetry for the search/lookup flow.

**Out of scope:**
- Advanced search customization (e.g., multi-record/batch search, saved searches) — explicitly deferred to a future story.
- Module-specific deep functionality within Customer Management (CM), Account Management (AM), or Mimir beyond the summary view and launch behavior.
- Admin/monitoring dashboard functionality beyond what supports observability of this lookup flow (covered at a high level only where it directly relates to search SLA/audit).
- Authentication/SSO implementation details beyond their role as a precondition for accessing the search feature.

## Application Type & Platform Context
This is a **mixed** feature: it requires both a backend API/service capability (identifier validation, data retrieval, coordinated customer/account summary assembly) and a web UI (authenticated internal workspace) that exposes the search bar, results panels, and controlled states.

Evidence:
- "• Expose API and UI" (Key Functionality)
- "Create a cloud-ready internal web application called 'Unified CM & AM Workspace' for retail servicing staff."
- "User lands on the unified workspace URL in a browser."
- "Design a responsive desktop-first authenticated enterprise workspace with these pages and flows"
- Architecture style selected: monolith (user-selected).

## Actors and Permissions
- **Service Agent** — Primary actor. Authenticated internal user who performs single-record identifier searches and views customer/account summaries necessary for servicing.
- **Admin role** (referenced for workspace navigation/audit visibility) — Has access to Audit/Admin views; not the primary actor of this feature but relevant to shared workspace navigation rules ("Audit / Admin visibility only for admin roles if applicable").

Access constraints:
- Only authenticated users may access the search feature; unauthenticated access redirects to SSO sign-in.
- Users see and can act on only entitled/authorized modules and functions; unauthorized or disabled functions are hidden, and direct access attempts are denied.
- Protected/PCI-sensitive fields within the account summary are masked or restricted according to role-based authorization and applicable customer privacy obligations (per Acceptance Criterion 3).

**Open Question:** The source does not define specific role tiers beyond "Service Agent" and "admin roles" for audit visibility, nor does it define exactly which fields are restricted per role. The specific role-to-field masking matrix is not specified and should be clarified before implementation.

## Feature Development Intent
This is feature-development work because it requires building new end-to-end capability: input validation logic, coordinated backend retrieval of two related data summaries (customer and account) tied to one identifier, UI states for all outcomes (empty, loading, success, partial, no-match, error), masking logic for protected fields, and audit/telemetry emission. The desired outcome is that a Service Agent can enter a valid identifier once and reliably retrieve the correct, non-mixed customer and account summary within SLA, replacing today's manual or fragmented lookup process.

## UI Design & Interaction Contract
**Entry point:** Persistent global search bar in the workspace header, labeled "Search by customer/account identifier," available across authenticated pages, with a search action and a clear/reset action.

**Search Workspace states** (per Design Prompt and named frames):
- **Empty/default prompt** — initial state before any search is performed.
- **Validating input** — shown while the identifier format is checked before the request is submitted.
- **Loading summaries** — loading skeletons shown for both Customer Summary and Account Summary panels while retrieval is in progress.
- **Successful result** — Customer Summary and Account Summary panels populated with data tied to the same searched identifier, displayed together as a unified view.
- **No matching record** — controlled empty state with clear title, short explanation, and recovery action; no technical internals exposed.
- **Partial result (customer found, no related account)** — Customer Summary remains visible; Account Summary shows a controlled "no account" state rather than an error.
- **Retrieval error** — controlled error panel with title, explanation, and recovery action (e.g., retry); no stack traces or internal payload details.

**Active record context banner:** Displays the current searched identifier and resolved context above the summary panels.

**Critical behavior — no stale data:**
- When a new valid search is submitted, the system must immediately clear prior summary values or replace them with loading skeletons.
- Previous record data must never be displayed as current after the identifier changes.

**Customer Summary panel:**
- Read-only card/panel with fields such as customer name, customer ID, contact summary, status, segment/type, and servicing indicators.
- Includes a loading skeleton state and a controlled empty/no-data message.

**Account Summary panel:**
- Rendered alongside Customer Summary on the same page, as a distinct but coordinated panel.
- Fields such as account ID, account type, account status, balance/summary indicators (if allowed), and payment-related details (only if permitted).
- PCI-safe presentation: all prohibited payment values are masked; full PAN or sensitive payment details are never shown; partially displayable values are masked.
- States: normal data, no related account available, retrieval failure, masked restricted fields.

**Validation messaging:** Input must be validated for required format before a search request is completed; invalid identifier input shows a controlled inline validation message (clear title, short explanation, recovery action), not a raw system error.

**Accessibility:**
- Accessible color contrast, visible focus states, keyboard-friendly navigation.
- Clear form labels and inline validation on the search input.
- Status and masked-field indicators must be distinguishable through means other than color alone.

**Tone:** Professional, concise, operational; plain-language error/validation messaging; no technical jargon, stack traces, or internal field names exposed to the agent.

## API Contract
The source specifies that the feature must "Expose API and UI" and describes a coordinated retrieval flow, but does not define concrete endpoint paths, request/response schemas, or versioned contract names beyond a generic reference to versioned API labels (e.g., "/api/v1") used in an unrelated admin Feature Flags context.

Source-supported behavioral contract (implementation-level details marked as Open Questions where not specified):
- **Operation:** Accepts a single-record identifier as input and returns a coordinated customer summary and related account summary in one request/response flow ("requests customer summary plus related account summary in one coordinated flow").
- **Validation:** The system validates the identifier (required format/presence) before processing the lookup; invalid input is rejected prior to retrieval.
- **Success response:** Returns matching customer summary and account summary data tied to the same identifier.
- **Partial response:** If no related account exists, returns customer summary with a controlled indicator of no account data (not treated as a hard error).
- **No-match response:** Returns a controlled "no data found" outcome when the identifier does not resolve to any record.
- **Error response:** Returns a controlled retrieval-error outcome without exposing internal payload, stack traces, or backend implementation details.
- **Authorization:** Requests must be authenticated; role-based authorization determines whether protected/PCI-sensitive fields are included, masked, or restricted in the response/presentation layer.
- **Performance:** Supported identifier searches must return the matching customer and account summary within 2 seconds under expected load (Acceptance Criterion 1).

**Open Questions:**
- Exact endpoint path(s), HTTP method(s), request/response schema, and field-level contract are not specified in the source and must be defined before implementation.
- Idempotency behavior for repeated searches on the same identifier is not specified.
- Whether masking is applied server-side (in the API response) or client-side (in UI rendering only) is not specified.
- Specific error codes/taxonomy for no-match vs. retrieval error vs. invalid input are not specified.

## Business Logic & Rules
- A search is only processed after the identifier passes required input validation (presence/format) — Acceptance Criterion 5.
- Search results must correspond exactly to the searched identifier; customer or account data from unrelated records must never be mixed into the displayed result — Acceptance Criterion 2.
- When no related account exists for a found customer, the system keeps the customer summary visible and shows a controlled no-account state rather than treating it as an error.
- When a new valid search is submitted, all prior search context and summary data must be cleared or replaced before/while loading new results — stale data must never persist as "current."
- Protected and PCI-sensitive fields in the account summary must be masked or restricted according to role-based authorization and applicable customer privacy obligations — Acceptance Criterion 3. Full PAN or sensitive payment details must never be rendered; partially displayable values must be masked.
- If session expires, token validation fails, the feature is disabled (feature flag), or authorization is denied, the system must block protected data and route the user to a controlled error, denial, or reauthentication state rather than exposing any summary data.
- All lookup activity must generate audit events and operational telemetry without exposing restricted payload data.
- Disabled/unauthorized functions must be hidden from navigation entirely, and direct access attempts must produce a controlled denial state rather than a technical error.

## Data Model & Validation
Source-supported entities and representative fields (exact schema not defined in source; treat as summary-level display fields):

**Customer Summary** (read-only):
- Customer name
- Customer ID
- Contact summary
- Status
- Segment/type
- Servicing indicators

**Account Summary** (read-only, PCI-safe presentation):
- Account ID
- Account type
- Account status
- Balance/summary indicators (if allowed by authorization)
- Payment-related details (only if permitted; masked where restricted)

**Validation rules:**
- The single-record identifier is required and must pass format validation before a search request is submitted.
- No other input fields are defined in the source for this search.

**Open Questions:**
- The source does not specify which identifier types are "supported" (e.g., customer ID, account number, SSN-derived identifier) or their exact format/validation rules.
- Exact field-level data types, lengths, and reference data (e.g., allowed status/segment values) are not specified.
- Data retention and audit log retention requirements are not specified beyond "immutable audit events."

## Functional Requirements
1. [FR1] The system must provide a persistent, authenticated search entry point labeled for single-record identifier search, available across workspace pages. *(Source: Design Prompt, Search Workspace spec)*
2. [FR2] The system must validate the identifier format/presence before submitting a search request, and must display inline validation messaging for invalid input. *(Acceptance Criterion 5)*
3. [FR3] The system must retrieve and display a customer summary and a related account summary in one coordinated result, both tied to the same searched identifier. *(Acceptance Criterion 2, 4)*
4. [FR4] The system must return results within 2 seconds under expected load for supported identifier searches. *(Acceptance Criterion 1)*
5. [FR5] The system must never display customer or account summary data from a record other than the one matching the current search. *(Acceptance Criterion 2)*
6. [FR6] When a customer is found but no related account exists, the system must display the customer summary and a controlled "no account" state instead of an error. *(Design Prompt step 8)*
7. [FR7] When a new valid search is performed, the system must clear or replace all prior summary data before or while displaying new results — no stale data may appear as current. *(Design Prompt step 9; Interaction Details)*
8. [FR8] The system must mask or restrict protected and PCI-sensitive fields in the account summary according to role-based authorization and privacy obligations, never rendering full PAN or full sensitive payment values. *(Acceptance Criterion 3)*
9. [FR9] The system must display a controlled error state (title, explanation, recovery action, no technical internals) when summary retrieval fails. *(Controlled Error and Empty States spec)*
10. [FR10] The system must display a controlled "no matching record" state when the identifier does not resolve to any record. *(Search Workspace states)*
11. [FR11] The system must block display of protected summary data and route to a controlled error, denial, or reauthentication state if the session is expired, authentication fails, the feature is disabled, or authorization is denied. *(Design Prompt step 12)*
12. [FR12] The system must emit audit events and operational telemetry for search/lookup activity without exposing restricted payload data. *(Design Prompt step 13)*
13. [FR13] The system must present all summary data as read-only (no inline edit controls) within the search results view. *(Customer/Account Summary spec)*

## Non-Functional Requirements
- **Performance:** Single-record lookups must return coordinated customer and account summaries within a 2-second SLA under expected load (Acceptance Criterion 1).
- **Security:** All search requests require authentication; input validation is mandatory before processing (Technical Considerations: Security).
- **Data protection:** PCI-sensitive and other protected fields must be masked according to role-based authorization and applicable privacy obligations; full PAN or sensitive payment details must never be exposed.
- **Auditability:** Lookup activity must produce audit events and operational telemetry suitable for compliance review, without leaking restricted payload content.
- **Accessibility:** Accessible color contrast, visible focus states, keyboard-friendly navigation, clear labels, and non-color-dependent status/masking indicators.
- **Reliability:** No critical defects in production; feature must work per defined acceptance criteria (Success Metrics).
- **Data integrity:** No cross-record data mixing; new searches must fully supersede prior search context.

## Acceptance Scenarios

**Scenario 1 — Successful single-record search (happy path)**
- Given an authenticated Service Agent on the Search Workspace page
- When the agent enters a valid, supported single-record identifier and submits the search
- Then the system validates the input, retrieves the coordinated customer and account summary within 2 seconds, and displays both panels populated with data corresponding only to the searched identifier.

**Scenario 2 — Invalid identifier input**
- Given an authenticated Service Agent on the Search Workspace page
- When the agent submits an identifier that fails required format validation
- Then the system does not submit the search request and displays an inline validation message describing the issue without technical jargon.

**Scenario 3 — Customer found, no related account**
- Given an authenticated Service Agent performs a valid search
- When the system finds a matching customer but no related account
- Then the system displays the Customer Summary panel populated with data and displays a controlled "no account" state in the Account Summary panel.

**Scenario 4 — No matching record**
- Given an authenticated Service Agent performs a valid search
- When no customer or account record matches the identifier
- Then the system displays a controlled "no matching record" state with a clear title, explanation, and recovery action, and no stale prior data is shown.

**Scenario 5 — New search replaces prior context**
- Given an authenticated Service Agent has previously searched and is viewing Customer/Account summary results
- When the agent submits a new, different valid identifier
- Then the system clears or replaces the prior summary data immediately (via skeleton loading or clearing) and displays fresh results tied only to the new identifier, with no mixing of old and new data.

**Scenario 6 — Retrieval error**
- Given an authenticated Service Agent performs a valid search
- When the backend summary retrieval fails
- Then the system displays a controlled error state with a clear title, short explanation, and recovery action, without exposing internal error details.

**Scenario 7 — Masked protected fields**
- Given an authenticated Service Agent views an Account Summary for a record containing PCI-sensitive or protected fields
- When the agent's role does not permit full visibility of those fields
- Then the system masks the restricted values and visually indicates the restriction without exposing the underlying data.

**Scenario 8 — Session/authorization failure during lookup**
- Given a Service Agent's session has expired, authentication fails, the feature is disabled, or authorization is denied
- When the agent attempts to perform or view a single-record search
- Then the system blocks protected data from rendering and routes the agent to the appropriate controlled error, denial, or reauthentication state.

## Traceability Matrix
| Source ID | Requirement | Acceptance Criteria | Test Coverage |
|---|---|---|---|
| US 78138-AC1 | Search returns coordinated customer/account summary within 2 seconds under expected load | AC1 | Scenario 1 |
| US 78138-AC2 | Results must correspond only to searched identifier; no mixed/unrelated data | AC2 | Scenario 1, Scenario 5 |
| US 78138-AC3 | Protected/PCI fields masked per role-based authorization and privacy obligations | AC3 | Scenario 7 |
| US 78138-AC4 | Documented happy-path search flow retrieves correct customer and account summary | AC4 | Scenario 1 |
| US 78138-AC5 | System validates required input before completing search | AC5 | Scenario 2 |
| US 78138-DesignPrompt-8 | Customer found with no related account shows controlled no-account state | Design Prompt step 8 | Scenario 3 |
| US 78138-DesignPrompt-9 | New valid search clears stale prior context and replaces summaries | Design Prompt step 9 | Scenario 5 |
| US 78138-DesignPrompt-12 | Blocked access on session expiry/auth failure/disabled feature/denied authorization | Design Prompt step 12 | Scenario 8 |
| US 78138-DesignPrompt-13 | Audit events and telemetry emitted without exposing restricted data | Design Prompt step 13 | Scenario 7, Scenario 8 (supporting) |
| US 78138-ErrorStates | Controlled error/no-match/retrieval-error states with no technical internals | Controlled Error and Empty States spec | Scenario 4, Scenario 6 |

## Open Questions
- What specific identifier types are supported for "single-record identifier" search (e.g., customer ID, account number), and what are their exact format/validation rules?
- What is the exact API endpoint path, method, request/response schema, and versioning contract for the search/lookup operation?
- Is field masking applied server-side (API response) or client-side (UI only), and what is the precise role-to-field masking matrix?
- What specific error codes/taxonomy distinguish invalid input, no-match, and retrieval-error responses at the API level?
- What are the exact role definitions and permission boundaries beyond "Service Agent" and "admin," and how do they affect which summary fields are visible or restricted?
- What is the expected data retention period for audit events and search telemetry?
- Is idempotency a requirement for repeated identical searches (e.g., caching behavior), and if so, what are the rules?
- What constitutes "expected load" for the 2-second SLA (e.g., concurrent user count, request volume)?

## Source References
- Feature ID: 78138 — "As a Service Agent, I want to search by a single-record identifier so that I can retrieve the correct customer and account summary quickly"
- User Story: US 78138 (Acceptance Criteria 1–5)
- Design Prompt sections: "Single-Record Search Workspace," "Customer Summary," "Account Summary," "Controlled Error and Empty States," "Protected Route Navigation Flow," "Data/Security Presentation Rules," "Interaction Details"
- Named frames: 05–10 (Search Workspace states), 11–13 (Protected Route/Session states)
- Persona: Service Agent (Service Agent)