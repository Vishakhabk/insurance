# Implementation Requirements Checklist

**Purpose**: Provide an implementation acceptance checklist that agents can execute one item at a time.  
**Feature**: Unified Cloud-Ready CM and AM Web Workspace

## Functional Acceptance Criteria

- [ ] [R1] Unauthenticated access to the unified workspace URL renders only the minimal public entry shell and redirects to the configured enterprise WorkOS SSO flow before any protected CM or AM content is displayed
- [ ] [R2] WorkOS callback handling validates required callback data, authenticated identity, session/token state, and authorization scope before creating a workspace session
- [ ] [R3] Valid authorized sign-in routes the user into the authenticated workspace home without requiring any separate legacy desktop login
- [ ] [R4] Invalid callback, failed authentication, unauthorized internal identity, token validation failure, feature-disabled access, and protected access denial each route to a controlled non-technical state with recovery actions and no protected data shown
- [ ] [R5] The authenticated workspace home renders a global header, authorized navigation, and authorized module tiles only for entitled and enabled in-scope modules
- [ ] [R6] Missing module entitlement, disabled rollout state, or out-of-scope function exclusion prevents the module from appearing visually on the home page, navigation, discovery responses, and launch surfaces
- [ ] [R7] If entitlement loading fails or times out during workspace home initialization, the home page shows a safe access retrieval error state and does not default to showing protected module tiles
- [ ] [R8] Selecting an authorized CM, AM, or related module tile routes the user to the corresponding protected module entry point within the active authenticated session
- [ ] [R9] Global single-record search is available across authenticated pages and supports the documented happy-path flow from search submission through unified summary display
- [ ] [R10] Search input is validated for required format before any protected lookup request is sent; invalid input shows inline validation and controlled invalid-identifier state
- [ ] [R11] A valid identifier search resolves the active customer/account context and retrieves customer summary plus related account summary in one coordinated workflow
- [ ] [R12] Successful lookup displays customer summary and account summary together in the same unified workspace view, clearly tied to the active searched identifier
- [ ] [R13] If a valid identifier resolves to a customer with no related account, the workspace keeps customer summary visible and shows a controlled no-account-available state instead of a blank or broken page
- [ ] [R14] If no matching record is found, the workspace shows the controlled no-match state and clears prior active record context from the visible page
- [ ] [R15] If a new valid search is performed, prior record context and visible summary values are cleared immediately or replaced with loading skeletons so previous-record data is never shown as current
- [ ] [R16] If summary retrieval fails because of upstream or processing errors, the workspace shows controlled summary-area error states without exposing stack traces, internal field names, or restricted payload details
- [ ] [R17] Supported protected route transitions preserve authenticated session state during the active session window without forcing a new login prompt
- [ ] [R18] Protected route navigation preserves customer/account context only on routes that explicitly support inherited context
- [ ] [R19] Routes that do not support inherited context open in a protected default state with no stale customer/account data displayed
- [ ] [R20] Browser refresh on a supported protected route restores the same route and permitted context when the session remains valid
- [ ] [R21] Session expiration or invalid session detection during navigation or protected API activity blocks protected content, prevents protected-data flash, and routes to the secure reauthentication state
- [ ] [R22] Authorized launch from the workspace to Mimir or other protected modules reuses authenticated identity context without a second login prompt
- [ ] [R23] Receiving protected modules continue to enforce their own role-based authorization and do not inherit permissions beyond the user’s approved scope
- [ ] [R24] Module launch denial, failure, or unavailability shows controlled messaging without exposing protected routing or security internals
- [ ] [R25] Successful and failed protected customer/account summary access events are recorded automatically in immutable audit records without user action
- [ ] [R26] Administrative session events for sign-in success, sign-in failure, invalid callback, token validation failure, access denial, and session-related failures are captured as distinct audit events with reviewable outcome detail
- [ ] [R27] Audit records are exportable in a structured reviewable format that preserves original field values, ordering metadata, and outcome information
- [ ] [R28] Audit export views remain read-only and expose no edit, delete, overwrite, or mutation path through the application
- [ ] [R29] Feature flags control visibility and access for supported CM and AM functions so disabled functions are hidden and enabled functions become available without manual user-role updates
- [ ] [R30] Protected backend capabilities used by the workspace are exposed through explicit versioned API routes and return the expected contract for the configured version
- [ ] [R31] Direct route or API attempts to access disabled or unauthorized functions return controlled denial responses and do not execute the protected operation
- [ ] [R32] Monitoring/admin views provide the defined lightweight operational tabs for Monitoring Overview, API / Lookup Performance, Audit Event Export, and Feature Flags / Rollout Status
- [ ] [R33] Primary, alternate, and failure flows across public entry, sign-in, workspace home, search, route navigation, module launch, session expiry, and admin views are implemented and verifiable in the application

## UI Acceptance Criteria

- [ ] [R34] Public Entry/Auth Redirect screen shows only workspace branding, “Redirecting to secure sign-in”, a loading indicator, and secure minimal pre-auth messaging with no protected CM or AM content
- [ ] [R35] Sign-In Failure and Access Denied screens provide controlled plain-language variants for authentication failed, unauthorized identity, and invalid or expired callback/session, with Retry sign-in and Return to workspace actions plus support/help text
- [ ] [R36] Authenticated Workspace Home includes the required top global header, left navigation rail or collapsible sidebar, and main content area with authorized module tiles only
- [ ] [R37] Global header includes workspace title, persistent single-record search entry, active user identity chip, session status indicator, notifications/help, and logout/profile menu
- [ ] [R38] Navigation/sidebar renders only authorized destinations and includes Audit/Admin entries only when the role is entitled
- [ ] [R39] Disabled and unauthorized modules are hidden completely from tiles, navigation, and other visual discovery surfaces
- [ ] [R40] Workspace home shows a safe non-technical error state instead of tiles when entitlement retrieval fails
- [ ] [R41] Search Workspace screen is implemented as one unified servicing page rather than separate disconnected tools
- [ ] [R42] Search Workspace includes the labeled search bar, search action, clear/reset action, active record context banner, and explicit states for empty, validating, loading, success, no match, no account, and retrieval error
- [ ] [R43] Customer Summary and Account Summary render as distinct coordinated read-only cards/panels within the same results area
- [ ] [R44] Summary cards implement loading skeletons, controlled empty/no-data messaging, retrieval-failure presentation, and masked restricted-field presentation where applicable
- [ ] [R45] Controlled reusable state components are implemented for invalid identifier, no data found, customer found with no account, upstream retrieval error, unauthorized route/function, feature disabled, and session expired
- [ ] [R46] Protected route screens include breadcrumb or context banner, route title, and active record chip only where carried context is permitted
- [ ] [R47] Session Expired / Reauthentication screen uses a full-page secure interruption presentation with sign-in-again and return-home actions and no customer/account data visible
- [ ] [R48] Related module launch screens provide controlled success and denied/unavailable interstitial states without exposing routing internals
- [ ] [R49] Admin screens use secure, operational, read-only layouts rather than analytics-heavy dashboards
- [ ] [R50] Monitoring Overview, API Performance, Audit Export, and Feature Flags & Versioning screens use the required cards, tables, badges, and status treatments
- [ ] [R51] Audit event table exposes only read-only controls and includes loading, empty, export-in-progress, and export-failure states
- [ ] [R52] Required reusable UI components are implemented: global header, authorized module tile, left nav/sidebar, search bar with validation states, context banner/active record chip, summary cards, masked data field pattern, empty state panel, error state panel, session expired view, access denied panel, feature disabled banner/panel, toasts, audit event table, monitoring metric cards, and status badges
- [ ] [R53] Status badges for healthy, degraded, failed, unauthorized, disabled, and masked are visually distinguishable and not dependent on color alone
- [ ] [R54] Loading skeletons are used for tiles, summary cards, and tables where specified
- [ ] [R55] UI styling matches the internal enterprise B2B direction: desktop-first, compact density, strong information hierarchy, neutral professional palette, and consistent secure-state styling
- [ ] [R56] Protected views visibly indicate authenticated secure context
- [ ] [R57] Error, denial, disabled, and expired states avoid technical jargon, stack traces, token details, raw payloads, and internal field names
- [ ] [R58] Keyboard navigation, visible focus states, clear labels, inline validation, accessible contrast, and responsive desktop/laptop behavior are implemented for the defined screens and controls

## API and Integration Acceptance Criteria

- [ ] [R59] WorkOS SSO integration is implemented for unauthenticated redirect, callback validation, and session establishment using encrypted transport and secure session handling
- [ ] [R60] Callback validation enforces required state and identity claims before session creation; invalid or incomplete callbacks do not create sessions
- [ ] [R61] Protected workspace requests validate JWT-backed session state and reject expired, malformed, tampered, or otherwise invalid tokens before CM or AM data is returned
- [ ] [R62] Session validation derives access from authenticated enterprise identity and applies least-privilege authorization checks to protected modules
- [ ] [R63] Entitlement retrieval returns only authorized in-scope modules and is treated as deny-by-default when authorization data is missing, invalid, or errored
- [ ] [R64] Unknown entitlement codes or entries without a valid route mapping are ignored and not rendered as tiles or navigation options
- [ ] [R65] Single-record lookup API/service flow retrieves customer summary and related account summary in one coordinated protected request path
- [ ] [R66] Lookup responses support success, no match, customer-with-no-account, masked-field, retrieval-error, unauthorized, feature-disabled, and session-invalid outcomes needed by the UI
- [ ] [R67] Protected module launch integration reuses authenticated identity context while allowing the destination module to perform its own authorization checks
- [ ] [R68] Audit write operations capture required event fields for protected access and session/admin events, including event type, actor or attempted actor, occurred timestamp, module, target type, target ID, outcome, correlation ID, and source route/API label
- [ ] [R69] Audit persistence rejects invalid or partial audit records and rejects overwrite attempts for an existing audit event identifier
- [ ] [R70] Audit storage behavior remains append-only through CM and AM application paths with no update/delete capability
- [ ] [R71] Audit export operation returns reviewable structured output preserving original values and ordering metadata
- [ ] [R72] Monitoring/telemetry instrumentation is emitted from workspace shell, auth/session handling, protected APIs, audit write paths, and health checks with correlation-friendly non-sensitive identifiers only
- [ ] [R73] Monitoring data captures authentication success/failure, workspace availability, protected API health, lookup latency, degraded state, failure state, and recovery state
- [ ] [R74] Operational alerting is implemented for breach of the configured 2-second lookup target under expected load conditions
- [ ] [R75] Feature flag evaluation occurs after authenticated session establishment and before protected module authorization/rendering
- [ ] [R76] Protected APIs used by the workspace are available via explicit versioned routes such as approved `/api/v1` labels; unsupported version strings or invalid rollout configuration must not be silently accepted
- [ ] [R77] Existing contracts remain backward-compatible unless an explicit versioned route is used for changed behavior

## Business Logic and Data Acceptance Criteria

- [ ] [R78] The workspace enforces deny-by-default behavior for unauthenticated users, unauthorized identities, missing entitlements, disabled functions, and unsupported routes
- [ ] [R79] Authorization logic ensures users see and can launch only entitled, enabled, in-scope modules
- [ ] [R80] Search validation ensures required identifier format is checked before lookup and invalid or missing identifiers do not leave prior results visible as current data
- [ ] [R81] New valid searches replace the active record context and summary state for both customer and account data in a single coordinated refresh
- [ ] [R82] Customer summary and account summary remain aligned to the same resolved active record context and do not mix unrelated customer/account data
- [ ] [R83] When the active record does not change, unnecessary duplicate summary fetches and redraws are avoided where source-supported
- [ ] [R84] If no related account exists for a valid customer result, business logic preserves customer summary and renders the controlled no-account state
- [ ] [R85] PCI-sensitive payment values are never rendered in full; any partially displayable values are masked according to configured display rules and role-based authorization
- [ ] [R86] Role-based and privacy-based field restrictions are enforced consistently in account summary presentation and protected API responses
- [ ] [R87] Protected route context carryover rules are enforced explicitly so only supported routes inherit active customer/account context
- [ ] [R88] Unsupported context carryover clears or blocks prior record state so stale data is not shown on destination pages
- [ ] [R89] Session-expired or invalid-session handling removes or blocks access to protected view state before rendering protected content
- [ ] [R90] Audit records are written with a consistent schema and stored/read as immutable records
- [ ] [R91] Audit write failure does not falsely mark an access event as successfully audited; the failure is instead surfaced through operational logs/metrics
- [ ] [R92] Monitoring/admin data shown in the UI excludes sensitive customer/account content while preserving approved correlation identifiers and approved API version labels
- [ ] [R93] Out-of-scope legacy replacement functions are excluded from module discovery and are not presented as available workspace capabilities
- [ ] [R94] Hidden or unauthorized module metadata is not exposed through normal UI rendering paths
- [ ] [R95] Error handling covers auth failures, invalid callbacks, access denials, invalid identifier input, no data found, no account found, summary retrieval failures, feature-disabled access, launch failures, audit write failures, and session expiration

## Non-Functional Acceptance Criteria

- [ ] [R96] The implementation uses the selected monolith architecture style and does not introduce an unrelated distributed domain or unsupported infrastructure pattern
- [ ] [R97] All sign-in, callback, session, and protected data flows use encrypted transport and secure handling appropriate for protected customer/account servicing access
- [ ] [R98] Secure session handling uses protections appropriate for browser-based protected access, including prevention of sensitive token exposure in UI and logs
- [ ] [R99] Protected data never flashes on screen during expired-session, invalid-session, unauthorized, or reauthentication transitions
- [ ] [R100] Customer/account lookup meets the 2-second response target under expected load conditions for supported single-record searches
- [ ] [R101] Instrumentation and audit capture are lightweight enough not to materially degrade workspace entry or protected lookup performance
- [ ] [R102] Audit, telemetry, and monitoring payloads exclude sensitive customer/account values, full PCI data, raw token contents, stack traces, and raw backend payloads
- [ ] [R103] Operational telemetry distinguishes healthy, degraded, failed, unauthorized, disabled, and recovery conditions where source-supported
- [ ] [R104] Read-only admin and audit views maintain immutability expectations and do not permit mutation through browser controls or service operations
- [ ] [R105] The implementation follows the approved professional, concise, operational content tone and enterprise secure-state presentation rules
- [ ] [R106] Existing project design-system and local UI conventions are followed for cards, panels, tables, badges, spacing, and responsive authenticated workspace behavior
- [ ] [R107] Tests or verification cover highest-risk behaviors: SSO entry/callback, protected session validation, entitlement filtering, stale-context prevention, masked PCI-safe rendering, route-context preservation rules, session-expiry blocking, immutable audit capture, feature-flag denial, and 2-second lookup observability/alerting

## Traceability

- [ ] [R108] Every implemented change maps back to the feature requirements and selected user-story acceptance criteria for authenticated entry, JWT-protected sessions, entitlement-filtered home, single-record search, unified summary display, route-context preservation, protected module launch, immutable audit trail, feature flags/versioned APIs, and monitoring
- [ ] [R109] Every implemented controlled state maps to a source-supported scenario: sign-in failure, access denied, invalid callback, empty search, loading, success, no account, no match, retrieval error, unsupported context route, session expired, module launch denied, feature disabled, and admin read-only states
- [ ] [R110] Every non-blocking Open Question that was implemented has a recorded decision + one-line rationale in assumptions documentation (no Open Question is silently assumed)
- [ ] [R111] No BLOCKING Open Question is implemented as an assumption; unresolved blocking details for identifier format, route eligibility matrix, module catalog, rollout configuration source, audit export format, alert thresholds beyond the stated 2-second target, and exact API/version contracts must be held for clarification if required for implementation completion

## Assumptions

### ASM-1
- [R112] Statement: The unified workspace is implemented as a browser-based internal web application within a monolith, with server-rendered or client-rendered pages/components permitted as long as the required authenticated workspace behavior is delivered.
- [R113] Source: Feature Description, "Create a cloud-ready internal web application", and user-selected architecture style "monolith".
- [R114] Rationale: The source defines the application as a web workspace and selects monolith architecture, but does not prescribe rendering strategy.
- [R115] Potential impact: UI/application composition decisions may affect routing, session handling, and component implementation approach.
- [R116] Confidence: high
- [R117] Recommended resolution: Confirm the preferred rendering/routing approach in implementation design if project standards require one.

### ASM-2
- [R118] Statement: The unified single-record search supports a single shared identifier input that may resolve to customer and/or account context through backend resolution logic rather than exposing separate customer and account search modes in the UI.
- [R119] Source: Repeated requirement for a "global single-record identifier search" and "Search by customer/account identifier".
- [R120] Rationale: The source requires one persistent search entry and coordinated context resolution, but does not define separate user-selectable identifier types or modes.
- [R121] Potential impact: Validation rules and backend resolver behavior may need adjustment if multiple explicit identifier types are later required.
- [R122] Confidence: medium
- [R123] Recommended resolution: Confirm the supported identifier types and format rules before finalizing validation and resolver behavior.

### ASM-3
- [R124] Statement: The lightweight admin views are part of the same workspace application and gated by role/entitlement rather than being a separate administration product.
- [R125] Source: "PAGE: Audit & Monitoring Admin Views" within the same workspace flow and requirement that Audit/Admin visibility appears only for admin roles if applicable.
- [R126] Rationale: The source embeds these views in the workspace navigation and frame list, but does not explicitly state whether they are separate deployment units.
- [R127] Potential impact: Authorization, routing, and navigation implementation may differ if admin views are split into a separate application.
- [R128] Confidence: high
- [R129] Recommended resolution: Confirm whether admin screens live under the same app shell and protected route system.

### ASM-4
- [R130] Statement: Controlled denial/error pages may be reused across multiple denial causes when they preserve the required plain-language variant messaging and recovery actions.
- [R131] Source: Repeated requirements for controlled non-technical states across sign-in failure, access denied, feature disabled, unauthorized route/function, and module launch denied.
- [R132] Rationale: The source defines required variants and behavior but does not require wholly separate component implementations for each cause.
- [R133] Potential impact: Shared-state component design could affect routing, analytics labels, and test coverage granularity.
- [R134] Confidence: high
- [R135] Recommended resolution: Confirm preferred reuse boundaries in UI architecture if design-system conventions differ.

## Notes

- [R136] Never resolve an Open Question silently. In an unattended run, record the chosen assumption + rationale in the appropriate assumptions documentation; blocking questions must instead hold the feature at needs-clarification.
- [R137] Mark an item complete only after verifying actual implementation code and behavior.