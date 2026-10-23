# Golden Repository Implementation Guidelines

Generated for: qa231026
Repository: Insurance golden repo
Generated at: 2026-10-23T13:24:58.672Z
File selection mode: selected mapped files

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

### Core Architecture Objectives

- [G1] Digitize and standardize insurance journeys.
- [G2] Reduce manual processing and turnaround time.
- [G3] Improve experience for:
  - customers
  - agents
  - brokers
  - operations users

### Configurability and Market Support

- [G4] Support configurable:
  - products
  - jurisdictions
  - currencies
  - taxes
  - channels
- [G5] Support localization and timezone handling.

### Control, Traceability, and Compliance Support

- [G6] Ensure:
  - traceability
  - segregation of duties
  - auditability

### Reliability, Continuity, and Operations

- [G7] Design for availability and resilience.
- [G8] Support disaster recovery and business continuity.
- [G9] Provide observability and operational support.

## Security & Compliance

### Privacy and Data Minimization
- [G10] Enforce privacy protections.
- [G11] Apply data minimization so only necessary data is collected, processed, and retained.

### Authentication and Authorization
- [G12] Require strong authentication.
- [G13] Require strong authorization controls.

### Encryption
- [G14] Encrypt data in transit.
- [G15] Encrypt data at rest.

### Auditability and Logging
- [G16] Maintain audit logging.
- [G17] Ensure the system supports auditability.

### Segregation of Duties
- [G18] Ensure segregation of duties.

## UI/UX & Accessibility

### User Experience

- [G19] Improve the experience for:
  - Customers
  - Agents
  - Brokers
  - Operations users

### Accessibility

- [G20] Ensure accessibility.

### Localization and Timezone Support

- [G21] Support localization.
- [G22] Support timezone handling.

## Domain & Business Rules

### Supported Roles and Parties

- [G23] The domain must support the following roles and parties:
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

## Error Handling & Resilience

### Availability and Resilience

- [G24] Design for availability and resilience.

### Disaster Recovery and Business Continuity

- [G25] Support disaster recovery and business continuity.

## Performance & Observability

### Performance

- [G26] Reduce turnaround time.

### Observability & Operational Support

- [G27] Provide observability and operational support.