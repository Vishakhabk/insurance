# Reference Architecture

## Suggested logical components
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

## Integration patterns
- REST/JSON for synchronous interactions
- Events for lifecycle changes
- Batch interfaces for legacy and regulatory exchanges
- Idempotency keys for payment and document operations
- Correlation IDs across all services
- Retry, timeout, circuit breaker, and dead-letter handling

## Security baseline
- Least privilege and role-based access control
- MFA for privileged and sensitive actions
- Secrets managed outside source code
- Input validation and output encoding
- PII masking in logs
- Immutable audit records
- Retention and deletion policies
