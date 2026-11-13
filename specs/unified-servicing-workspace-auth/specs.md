# Feature: Unified Cloud-Ready CM and AM Web Workspace
Status: NEW
Owner: Astra
Last Updated: 2026-11-13

## Summary
- Deliver a unified, cloud-ready internal web workspace named **Unified CM & AM Workspace**.
- Replace supported legacy CM and AM desktop access paths for in-scope functions.
- Centralize:
  - enterprise SSO entry
  - authenticated session handling
  - entitlement-based module access
  - single-record search
  - unified customer + account summary display
  - protected route navigation
  - related module launch
  - audit visibility
  - operational monitoring
- Expected outcome:
  - retail servicing staff can securely enter one workspace, search one identifier, and review customer/account context together without legacy tool switching.

## Scope
**In scope**
- Browser-based authenticated workspace entry via WorkOS SSO.
- JWT-backed protected session validation.
- Authenticated workspace home with authorized tiles/navigation only.
- Persistent global search for a single-record identifier.
- Unified search workspace showing customer summary and related account summary together.
- Controlled states for:
  - invalid input
  - no match
  - no related account
  - retrieval error
  - access denied
  - feature disabled
  - session expired
- Protected route navigation with context preservation only where permitted.
- Related protected module launch (including Mimir) with reused identity context.
- Audit event capture and read-only export support.
- Monitoring/admin views for availability, lookup performance, audit export, and feature flags/version labels.
- Feature-flag-aware hiding/denial behavior for in-scope functions.
- Versioned API contract labels such as `/api/v1`.

**Out of scope**
- Editing customer or account data from summary views.
- Multi-record comparison, bulk search, or cross-customer navigation.
- Advanced personalization/customization of workspace or summaries.
- Full analyst/analytics-heavy dashboards.
- Unsupported legacy CM/AM functions.
- Full payment instrument detail display beyond permitted masked values.
- Full self-service feature flag management console.
- Cross-browser synchronization across tabs.
- Preserving unsaved edits across all CM/AM pages.
- New enterprise monitoring platform.

## Application Type & Platform Context
- **Type:** Mixed web application + protected backend/API support.
- **Primary user platform:** Browser-based internal web app, desktop-first responsive.
- **Architecture style:** monolith.
- **Evidence:**
  - “User lands on the unified workspace URL in a browser”
  - “Create a cloud-ready internal web application”
  - “Design a responsive desktop-first authenticated enterprise workspace”
  - stories require protected backend endpoints, JWT validation, audit persistence, telemetry, and versioned API routes.

## Actors and Permissions
- **Service Agent**
  - Can sign in via enterprise SSO.
  - Can use single-record search.
  - Can navigate supported CM/AM routes during active session.
- **Account Management Specialist**
  - Can access workspace home.
  - Sees only authorized CM, AM, and explicitly permitted related modules.
  - Can review customer + account summaries together.
  - Can launch Mimir/related modules if authorized.
- **Compliance Role**
  - Can review/export immutable audit events via read-only admin view.
- **System Administrator**
  - Can view monitoring, API performance, audit export, and feature flag/version status admin views.
  - Relies on rollout configuration controlling visibility and access of in-scope functions.
- **Access constraints**
  - Unauthorized internal identities must be denied workspace entry.
  - Unauthorized modules must be hidden, not merely disabled visually.
  - Destination modules enforce their own authorization.
  - Audit/Admin visibility appears only for admin roles if applicable.

## Feature Development Intent
- Build a new authenticated web workspace replacing fragmented legacy access.
- Add or change behavior to:
  - redirect unauthenticated users to WorkOS SSO
  - validate callback, identity, JWT/session, and authorization scope
  - render only entitled modules and routes
  - perform coordinated customer/account summary lookup from a single identifier
  - prevent stale record data across searches and route changes
  - preserve session and allowed context across supported routes
  - reuse identity context for protected module launch
  - block data on expired/invalid sessions, denial, or disabled features
  - capture audit events and operational telemetry without restricted payload exposure

## UI Design & Interaction Contract
- **Design direction**
  - Internal enterprise B2B UI
  - cloud-ready, modern, clean, trustworthy
  - desktop-first responsive
  - compact density
  - strong hierarchy with cards, panels, tables, badges
  - secure-state styling for denied/expired/error states

- **Named frames/pages**
  - 01 Public Entry
  - 02 Sign-In Failure
  - 03 Access Denied
  - 04 Workspace Home
  - 05 Search Workspace - Empty
  - 06 Search Workspace - Loading
  - 07 Search Workspace - Success
  - 08 Search Workspace - No Account
  - 09 Search Workspace - No Match
  - 10 Search Workspace - Error
  - 11 Protected Route - Context Preserved
  - 12 Protected Route - Default State
  - 13 Session Expired
  - 14 Module Launch - Success
  - 15 Module Launch - Denied
  - 16 Monitoring Overview
  - 17 API Performance
  - 18 Audit Export
  - 19 Feature Flags & Versioning

- **Public Entry / Auth Redirect**
  - Minimal pre-auth shell only.
  - Show:
    - workspace name
    - “Redirecting to secure sign-in”
    - loading indicator
  - Variants:
    - redirecting to SSO
    - sign-in failed
    - access denied
    - invalid callback
  - Must not display protected data.

- **Sign-In Failure / Access Denied**
  - Controlled, non-technical screens.
  - Include:
    - concise explanation
    - primary action: Retry sign-in
    - secondary action: Return to workspace URL
    - support/help text
  - Must not expose stack traces, token details, or internal field names.

- **Authenticated Workspace Home**
  - Layout:
    - top global header
    - left nav rail or collapsible sidebar
    - main content with authorized tiles
  - Header includes:
    - workspace title
    - persistent global search
    - active user identity chip
    - session status indicator
    - notifications/help
    - logout/profile menu
  - Nav includes authorized destinations only:
    - Customer Management
    - Account Management
    - Mimir / related modules
    - Audit / Admin only if role-entitled
  - Hide unauthorized and feature-disabled functions completely.
  - If entitlement load fails, show safe error state instead of tiles.

- **Single-Record Search Workspace**
  - One unified screen, not separate tools.
  - Above the fold:
    - persistent header
    - search bar labeled like “Search by customer/account identifier”
    - search action
    - clear/reset action
    - active record context banner/chip
  - States:
    - empty/default
    - validating
    - loading
    - success
    - no match
    - customer found/no account
    - retrieval error
  - New valid search must immediately remove/replace stale prior summaries with skeletons.

- **Customer Summary**
  - Read-only card/panel.
  - Representative fields:
    - customer name
    - customer ID
    - contact summary
    - status
    - segment/type
    - servicing indicators
  - States:
    - loading skeleton
    - no-data/empty messaging

- **Account Summary**
  - Distinct coordinated card beside customer summary.
  - Representative fields:
    - account ID
    - account type
    - account status
    - balance/summary indicators if allowed
    - payment-related details only if permitted
  - States:
    - normal
    - no related account
    - retrieval failure
    - masked restricted fields
  - PCI-safe presentation required.

- **Controlled Error/Empty States**
  - Reusable state panels for:
    - invalid identifier input
    - no data found
    - customer found but no account summary
    - upstream retrieval error
    - unauthorized route/function
    - feature disabled
    - session expired
  - Each includes:
    - clear title
    - short explanation
    - recovery action
    - no technical internals

- **Protected Route Navigation**
  - Show breadcrumb or context banner, route title, persistent active record chip when allowed.
  - If route supports inherited context, preserve active customer/account context.
  - If not, show protected default state with no stale data.
  - On refresh:
    - valid session → restore route and permitted context
    - invalid session → block content and reauthenticate
  - Protected data must not flash on expired sessions.

- **Related Module Launch**
  - Launch cards/buttons for Mimir and protected modules.
  - States:
    - success
    - denied
    - unavailable/failure
  - Controlled messaging only.

- **Session Expired**
  - Full-page secure interruption state.
  - Primary action: Sign in again
  - Secondary action: Return to workspace home
  - No customer/account data visible.

- **Admin views**
  - Read-only where appropriate.
  - Monitoring Overview:
    - workspace availability
    - auth success/failure rates
    - protected API health
    - degraded/failure/recovery statuses
  - API / Lookup Performance:
    - single-record lookup latency
    - 2-second SLA status
    - success/failure counts
    - degraded/timeout indicators
  - Audit Export:
    - immutable audit table
    - export action
    - no edit/delete controls
  - Feature Flags & Versioning:
    - enabled/disabled states
    - version labels like `/api/v1`
    - hidden/out-of-scope handling

- **Accessibility**
  - accessible color contrast
  - visible focus states
  - keyboard-friendly navigation
  - clear labels and inline validation
  - status/masked indicators not based on color alone

## API Contract
- Source supports protected backend/API behavior but not exact endpoint definitions.
- Required contract behaviors:
  - protected requests must validate JWT session before returning CM/AM data
  - session validation must reject expired, malformed, or tampered tokens
  - callback handling must validate required callback data before session creation
  - protected APIs used by workspace must be available through explicit versioned routes such as `/api/v1`
  - requests to configured version must return expected contract for enabled functions
  - single-record lookup must coordinate retrieval of customer summary and related account summary
  - direct route/API access to disabled functions must return controlled denial and must not execute protected operation
  - destination modules must apply their own authorization checks
  - audit export must provide reviewable structured output preserving original values, ordering metadata, and outcomes
- Error/denial responses must be controlled and non-technical.
- Unsupported exact methods, schemas, payloads, and status codes are Open Questions.

## Business Logic & Rules
- Unauthenticated access to workspace URL redirects to WorkOS SSO before protected content is shown.
- Valid callback + authorized internal identity create session and route user to workspace.
- Invalid/missing callback data or failed auth shows sign-in failure state and creates no session.
- Unauthorized internal identity shows access denied and no protected content.
- Every protected request validates JWT/session first.
- Least-privilege role checks apply to protected modules.
- Home page and nav show only authorized, in-scope, enabled modules.
- Missing entitlement = do not render module.
- Entitlement load failure = safe error state, no default broad visibility.
- Disabled functions are hidden from discovery; direct access attempts are denied.
- Out-of-scope legacy replacement functions are excluded from discovery/presentation.
- Search input must be validated before request.
- Valid search resolves active customer/account context and retrieves customer + account summaries in one coordinated flow.
- Search results must correspond to searched identifier and must not mix unrelated records.
- New valid search clears prior record context immediately and replaces displayed summaries.
- If no related account exists, keep customer summary visible and show controlled no-account state.
- Summary retrieval failures show controlled error without internals.
- Payment-related values must be masked/restricted per role and privacy obligations; full PAN/sensitive payment details must never render.
- Route changes preserve session; preserve record context only on routes that allow inherited context.
- Unsupported inherited-context routes open in protected default state with no stale data.
- Refresh on protected route restores route/context only when session is still valid.
- Expired/invalid session blocks protected content and routes to reauthentication.
- Shared identity context may be reused for protected module launch, but permissions do not carry beyond approved scope.
- Each successful/failed protected customer/account summary access creates audit record.
- Administrative session events captured separately include:
  - sign-in success
  - sign-in failure
  - token validation failure
  - protected access denial
- Audit storage is append-only through CM/AM application path; update/delete/overwrite attempts are rejected.
- If audit persistence fails, emit operational logs/metrics and do not falsely report successful audit.
- Monitoring telemetry must exclude sensitive content while preserving correlation-friendly identifiers.
- Operational alert generated when lookup performance breaches 2-second threshold under expected load.

## Data Model & Validation
- **Supported entities / records**
  - authenticated session/JWT
  - entitlement/authorization scope
  - active record context
  - customer summary
  - account summary
  - audit event
  - monitoring/telemetry event
  - feature flag / rollout state
  - API version label

- **Representative fields named in source**
  - Customer summary:
    - customer name
    - customer ID
    - contact summary
    - status
    - segment/type
    - servicing indicators
  - Account summary:
    - account ID
    - account type
    - account status
    - balance/summary indicators if allowed
    - payment-related details only if permitted
  - Audit event:
    - eventId
    - event type
    - occurredAt / timestamp
    - actorId or attemptedActorId / user identity
    - module name
    - target type
    - target ID / target record identifier
    - outcome
    - correlation ID
    - source route/API label

- **Validation / quality rules**
  - required search input format must validate before request
  - invalid or missing identifiers must not leave prior results displayed as current
  - callback state and required identity claims must be validated before session creation
  - invalid or partial audit records must be rejected from audit persistence
  - audit event IDs cannot be overwritten
  - unknown entitlement codes ignored
  - valid route mapping required before rendering module tile
  - missing authorization data treated as deny-by-default
  - monitoring and audit data must exclude sensitive token contents and restricted payload values

## Functional Requirements
1. [FR1] The system shall redirect unauthenticated access to the workspace URL to the configured WorkOS enterprise SSO flow before any protected content is rendered.
2. [FR2] The system shall validate SSO callback data, authenticated identity, token/session, and authorization scope before creating a workspace session.
3. [FR3] The system shall deny workspace entry for unauthorized internal identities and show a controlled access-denied state.
4. [FR4] The system shall show a controlled sign-in-failure state when callback validation fails or authentication fails, without creating a session.
5. [FR5] The system shall process sign-in and callback handling over encrypted transport and use secure session handling appropriate for protected servicing access.
6. [FR6] The system shall validate JWT-backed session state on each protected workspace request and reject expired, malformed, or tampered tokens before CM/AM data is returned.
7. [FR7] The system shall derive application access from authenticated enterprise identity and apply least-privilege role checks to protected modules.
8. [FR8] The workspace home shall render only authorized, explicitly permitted, and enabled module tiles/navigation entries.
9. [FR9] The workspace shall hide unauthorized, disabled, and out-of-scope functions from navigation, tiles, and discovery responses.
10. [FR10] If entitlement retrieval fails on home load, the workspace shall show a safe non-technical error state and shall not display protected tiles by default.
11. [FR11] Selecting an authorized module tile shall route within the active authenticated workspace session to the corresponding protected entry point.
12. [FR12] The global single-record search shall be persistent across authenticated pages.
13. [FR13] The system shall validate required single-record identifier input format before performing lookup.
14. [FR14] Supported single-record searches shall return matching customer and account summaries within 2 seconds under expected load.
15. [FR15] The unified search workspace shall display customer summary and related account summary together in the same web view for a valid authorized record when both are available.
16. [FR16] When a new valid search is performed, the system shall immediately clear/replace prior summary content so no prior record values remain visible as current data.
17. [FR17] If a valid searched identifier resolves to a customer with no related account, the workspace shall keep customer summary visible and show a controlled no-account state.
18. [FR18] If no matching record is found, the workspace shall show a controlled no-data state and remove prior record context from current display.
19. [FR19] If summary retrieval fails, the workspace shall show a controlled error state without stack traces, internal field names, or restricted payload details.
20. [FR20] The system shall mask or suppress prohibited PCI-sensitive payment values and shall never render full PAN or other prohibited payment details.
21. [FR21] The workspace shall preserve authenticated session across supported CM/AM route changes within the active session window.
22. [FR22] The workspace shall preserve active customer/account context only for destination routes that support inherited context.
23. [FR23] For routes that do not support inherited context, the workspace shall open a protected default state with no stale record data.
24. [FR24] On browser refresh of a supported protected route during a valid session, the workspace shall restore the same route and permitted context.
25. [FR25] If session expiration or token validation failure is detected during navigation or protected API activity, the workspace shall block protected content and route to secure reauthentication.
26. [FR26] Authorized launch to Mimir or another protected module shall reuse authenticated identity context without second login.
27. [FR27] Receiving protected modules shall enforce their own authorization and shall not inherit permissions beyond approved scope.
28. [FR28] Launch failures or denied transitions shall show controlled messages without protected routing or security internals.
29. [FR29] The system shall create immutable audit events for each successful or failed protected customer/account summary access.
30. [FR30] The system shall create separate audit events for sign-in success, sign-in failure, token validation failure, and protected access denial.
31. [FR31] The application path used by CM and AM services shall not permit update or delete of existing audit events, and overwrite attempts for an existing event ID shall be rejected.
32. [FR32] The system shall provide read-only audit export of audit records in a reviewable structured format preserving original values, event ordering metadata, and outcomes.
33. [FR33] If audit persistence is unavailable, the system shall record operational failure telemetry/logs and shall not falsely indicate successful auditing.
34. [FR34] The system shall capture monitoring telemetry for authentication outcomes, workspace availability, protected API health, and single-record lookup performance.
35. [FR35] The system shall generate an operational alert when lookup performance breaches the configured 2-second threshold under expected load.
36. [FR36] Monitoring status shall distinguish healthy/degraded/failed/recovery states rather than generic up/down.
37. [FR37] Telemetry and admin displays shall exclude sensitive customer/account content and token contents while preserving correlation-friendly identifiers.
38. [FR38] Protected backend APIs used by the workspace shall use explicit versioned routes for enabled functions.
39. [FR39] Requests to disabled functions by direct route or API call shall return a controlled denial response and shall not execute the protected operation.
40. [FR40] Feature Flags & Versioning admin view shall present read-only enabled/disabled rollout state and version labels such as `/api/v1`.

## Non-Functional Requirements
- **Performance**
  - single-record search returns matching customer + account summaries within 2 seconds under expected load
  - feature flag evaluation/version resolution adds negligible latency
  - instrumentation must not materially increase load time or lookup latency
- **Security**
  - HTTPS/encrypted transport for sign-in/callback
  - secure session handling
  - CSRF-safe callback handling
  - JWT validation on protected requests
  - least-privilege authorization
  - no protected data flash on expired sessions
  - no token contents in client-visible errors or audit records
- **Privacy / data handling**
  - PCI-sensitive values never fully rendered
  - restricted values masked/suppressed
  - no raw backend payloads in UI/admin screens
- **Reliability**
  - controlled denial/error/expired states
  - detect and surface audit persistence failures operationally
- **Accessibility**
  - accessible contrast
  - visible focus
  - keyboard-friendly controls
  - clear labels/inline validation
  - non-color-only status indicators
- **Observability**
  - audit and telemetry throughout auth, search, navigation, denial, and failure paths
  - correlation-friendly non-sensitive identifiers
- **Operational**
  - admin views are lightweight, read-only where appropriate, and structured rather than analytics-heavy

## Acceptance Scenarios
### 1. Unauthenticated entry redirects to SSO
**Given** a user opens the unified workspace URL without an active session  
**When** the page loads  
**Then** the browser is redirected to WorkOS SSO before any protected CM or AM content is shown.

### 2. Successful SSO entry lands in workspace
**Given** a valid WorkOS callback for an authorized internal identity  
**When** callback data and authorization scope are validated  
**Then** the system creates a session and routes the user to Workspace Home without separate legacy login.

### 3. Invalid callback shows sign-in failure
**Given** a WorkOS callback is missing required validation data or is invalid  
**When** the workspace processes the callback  
**Then** no session is created and the user sees a controlled sign-in failure state.

### 4. Unauthorized identity is denied
**Given** an authenticated identity is not authorized for internal servicing functions  
**When** workspace entry is evaluated  
**Then** access is denied and no customer or account workspace content is rendered.

### 5. Home page shows only authorized modules
**Given** an authenticated user with module entitlements  
**When** Workspace Home loads successfully  
**Then** only authorized CM, AM, and explicitly permitted related module tiles/navigation entries are rendered.

### 6. Entitlement retrieval failure hides tiles
**Given** Workspace Home cannot retrieve authorization entitlements  
**When** the page initializes  
**Then** a safe non-technical error state is shown and protected module tiles are not displayed by default.

### 7. Valid search returns both summaries
**Given** an authenticated authorized user on an authenticated page  
**And** a valid single-record identifier is submitted  
**When** the system resolves the active record context and retrieves summaries successfully  
**Then** customer summary and related account summary are shown together within 2 seconds under expected load.

### 8. New search removes stale data
**Given** customer/account summaries for one record are visible  
**When** the user submits a new valid identifier  
**Then** previous summary values are immediately cleared or replaced with loading skeletons  
**And** only data for the new active record is shown as current.

### 9. Customer found with no related account
**Given** a valid identifier resolves to a customer with no related account summary  
**When** results are displayed  
**Then** the customer summary remains visible  
**And** the account area shows a controlled no-account-available state.

### 10. Search input validation blocks invalid request
**Given** a user enters an identifier that does not meet required format  
**When** the user attempts search  
**Then** the system shows controlled validation feedback  
**And** does not complete the lookup request.

### 11. Retrieval failure shows controlled error
**Given** summary retrieval encounters an upstream or processing error  
**When** the result state is rendered  
**Then** the workspace shows a controlled error state  
**And** does not expose stack traces, internal field names, or restricted payload details.

### 12. PCI-restricted values are masked
**Given** account summary includes payment-related fields  
**When** the workspace renders allowed data  
**Then** prohibited PCI-sensitive values are excluded  
**And** partially displayable values are masked according to configured rules.

### 13. Context is preserved on supported route
**Given** an authenticated user has an active customer/account context  
**When** the user navigates to a supported CM/AM route that allows inherited context  
**Then** the same permitted context is preserved and loaded on the destination route.

### 14. Unsupported-context route opens safe default
**Given** an authenticated user has an active customer/account context  
**When** the user navigates to a protected route that does not allow inherited context  
**Then** the route opens in protected default state  
**And** no stale record data is shown.

### 15. Refresh restores route during valid session
**Given** a user refreshes a supported protected route during a valid session  
**When** the page reloads  
**Then** the same route and permitted customer/account context are restored without repeating the prior search.

### 16. Expired session blocks protected content
**Given** session expiration or token validation failure occurs during navigation or protected API activity  
**When** the workspace detects the invalid session  
**Then** protected content is blocked  
**And** the user is routed to the secure reauthentication state  
**And** prior page data is no longer visible.

### 17. Protected module launch reuses identity
**Given** a user is authorized to launch Mimir or another protected module  
**When** the user selects the module launch action  
**Then** the workspace reuses authenticated identity context without prompting a second login  
**And** the destination module performs its own authorization checks.

### 18. Module launch denial is controlled
**Given** a protected module launch is denied or unavailable  
**When** the launch attempt completes  
**Then** the workspace shows a controlled denial/failure message  
**And** does not expose routing or security internals.

### 19. Protected access writes audit events
**Given** a user successfully or unsuccessfully accesses protected customer or account summary data  
**When** the access attempt is processed  
**Then** an audit record is created with event type, user identity, timestamp, target identifier, module name, and outcome.

### 20. Audit failure is operationally visible
**Given** audit persistence is temporarily unavailable during a protected access event  
**When** the event occurs  
**Then** the system records the audit failure in operational logs/metrics  
**And** does not falsely report that the access event was successfully audited.

### 21. Disabled feature is hidden and denied by direct access
**Given** a supported function is disabled in rollout configuration  
**When** an authenticated user views navigation/home  
**Then** that function is not displayed  
**And when** the user attempts direct route or API access  
**Then** the system returns a controlled denial and does not execute the protected operation.

### 22. Monitoring captures lookup SLA and alerts
**Given** users perform single-record summary lookups  
**When** telemetry is emitted  
**Then** lookup latency, success/failure, and degraded/timeout status are recorded without sensitive payload data  
**And when** the configured 2-second threshold is breached under expected load  
**Then** an operational alert is generated.

## Traceability Matrix
| Source ID | Requirement | Acceptance Criteria | Test Coverage |
|---|---|---|---|
| US 78114 | FR-1 to FR-5 | Redirect unauthenticated users; valid callback creates session; invalid callback fails; unauthorized identity denied; encrypted transport/secure session | Auth redirect, callback validation, denial, session creation, no protected pre-auth content |
| US 78122 | FR-6, FR-7, FR-25 | JWT validation on each protected request; least privilege; session failure logging without token exposure | Expired/malformed/tampered token rejection; role-scope enforcement; secure expiry handling |
| US 78131 | FR-8 to FR-11 | Authorized modules only; absent entitlement means absent module; entitlement failure shows safe error; authorized tile routes correctly | Home tile rendering, hidden modules, deny-by-default entitlement failures, tile navigation |
| US 78138 | FR-12 to FR-14, FR-18 to FR-20 | Search within 2s; correct/non-mixed data; masking/restriction by role/privacy; happy path; input validation | Search validation, SLA, correct binding to identifier, masking checks, no stale data |
| US 78147 | FR-15 to FR-20 | Customer + account together; refresh on new search without stale data; no-account state; PCI masking; controlled error state | Unified summary rendering, new-search replacement, partial result, masked fields, error state |
| US 78155 | FR-26 to FR-28 | Shared identity without second login; destination enforces own auth; controlled denial/failure; happy path; input validation | Launch success, no second login, downstream auth denial, controlled failure messaging |
| US 78164 | FR-21 to FR-25 | Session preserved across supported routes; carried context on allowed routes; safe default on unsupported routes; expiry blocks content; refresh restores valid route/context | Navigation, context carry rules, refresh restore, expired-session redirect/no flash |
| US 78171 | FR-29 to FR-33 | Protected access audit events; admin session audit events; append-only immutable storage; reviewable export; failure telemetry on audit persistence issues | Audit write success/failure, immutable path rejection, export schema/content, denial/sign-in event capture |
| US 78180 | FR-9, FR-38 to FR-40 | Disabled hidden; enabled exposed without role update; versioned routes; disabled direct access denied; out-of-scope excluded | Feature flag visibility, version label usage, direct route/API denial, discovery exclusion |
| US 78188 | FR-34 to FR-37 | Monitor auth, availability, API health; record response time for 2-second target; alert on breach; distinguish failure/degraded/recovery; exclude sensitive content | Telemetry emission, SLA alerting, status transitions, payload scrubbing, admin monitoring views |
| US 78098 / Feature 78094 | FR-1 to FR-40 | End-to-end unified workspace behavior across auth, home, search, navigation, launch, expiry, masking, audit, telemetry | Integrated workflow and state coverage across named frames/pages |

## Open Questions
- What exact single-record identifier formats are supported?
- What exact customer/account summary retrieval API endpoints, methods, and schemas are approved?
- Is the coordinated customer/account retrieval one API call or multiple orchestrated calls?
- What exact route list supports inherited context vs default protected state?
- What exact module catalog is in scope beyond CM, AM, and Mimir/related modules?
- What exact admin roles can access Monitoring, Audit Export, and Feature Flags views?
- What exact role model/claim mapping determines module entitlement and field-level masking?
- What exact masked display rules apply to partially displayable payment values?
- What exact version negotiation behavior applies beyond displaying labels like `/api/v1`?
- What export formats are required for audit export?
- What filtering behavior, pagination, and retention apply to audit export table data?
- What exact monitoring alert thresholds, aggregation windows, and notification channels are required beyond the 2-second lookup breach?
- What exact secure session storage/cookie settings are mandated?
- What exact controlled message copy is approved for invalid identifier, no match, retrieval error, feature disabled, and session expired states?
- Are audit events required “before” response completion or “before or alongside” it for each event type when latency tradeoffs occur?
- What exact supported browsers are required for label/destination consistency testing?

## Source References
- Feature 78094: Unified Cloud-Ready CM and AM Web Workspace
- Feature Reference: 78094
- User Story US 78098: Unified authenticated workspace entry
- User Story US 78114: Sign in to unified workspace with enterprise SSO
- User Story US 78122: JWT-backed application sessions validated on protected workspace requests
- User Story US 78131: Workspace home page shows only allowed modules
- User Story US 78138: Search by a single-record identifier
- User Story US 78147: Show customer summary and related account summary together
- User Story US 78155: Open Mimir and protected modules with shared identity context
- User Story US 78164: Preserve session and page context across supported CM and AM screens
- User Story US 78171: Immutable audit trail for protected access and session events
- User Story US 78180: Rollout controlled with versioned APIs and feature flags
- User Story US 78188: Monitoring for workspace availability and lookup performance