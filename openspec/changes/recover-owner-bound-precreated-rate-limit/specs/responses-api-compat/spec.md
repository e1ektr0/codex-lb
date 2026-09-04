## ADDED Requirements

### Requirement: Owner-bound pre-created rate limits receive bounded recovery

The HTTP bridge MUST wait for at most the existing account-recovery interval
and never beyond the original request deadline when an upstream
`rate_limit_exceeded` event arrives before `response.created`, the event is
unambiguously attributed to one request, existing replay guards prove that no
upstream or downstream output or side effect occurred, and that request must
remain on the current account. The request MUST retain bridge queue and create
gate ownership while shared and account response-create admission is released.
The proxy MUST retry the same owner at most once and MUST NOT authorize account
migration.

#### Scenario: Required owner recovers during the bounded wait

- **GIVEN** an owner-bound request receives `rate_limit_exceeded` before acceptance
- **AND** the request remains replay-safe
- **WHEN** the required owner recovers during the bounded wait
- **THEN** the proxy sends exactly one replacement attempt to that owner
- **AND** the request may complete without a client retry

#### Scenario: Output or ambiguous attribution blocks recovery

- **GIVEN** a rate-limit event follows response output or cannot be attributed to one request
- **WHEN** the bridge processes the event
- **THEN** the proxy MUST NOT wait and transparently replay the request

### Requirement: Exhausted owner-rate-limit recovery remains a rate limit

When bounded same-owner recovery cannot select the owner, the HTTP bridge MUST
surface HTTP 429 or an equivalent terminal SSE failure with code
`continuity_owner_rate_limited` and type `rate_limit_error`. It MUST NOT replace
the known failure with `stream_incomplete` or
`previous_response_owner_unavailable`. True owner deletion and non-rate-limit
owner failures retain their existing classifications.

#### Scenario: Required owner remains rate-limited

- **GIVEN** a replay-safe owner-bound request received a pre-created rate limit
- **AND** the bounded wait has completed
- **WHEN** the required owner is still rate-limited
- **THEN** the request terminates with `continuity_owner_rate_limited`
- **AND** no sibling account receives the request
- **AND** no third upstream send is attempted
