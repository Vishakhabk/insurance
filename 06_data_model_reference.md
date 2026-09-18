# Data Model Reference

## Core entities
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

## Data rules
- Use immutable identifiers.
- Store effective-from and effective-to dates for versioned business rules.
- Record source system and data ownership.
- Define required, optional, conditional, and prohibited fields.
- Define precision and rounding for monetary values.
- Store currency and timezone explicitly.
- Apply retention, archival, masking, and deletion rules by data category.
