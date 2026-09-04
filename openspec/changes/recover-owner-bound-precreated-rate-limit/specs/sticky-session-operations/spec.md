## ADDED Requirements

### Requirement: Temporary rate limiting does not erase continuity ownership

The proxy MUST keep a successfully selected continuity owner that later returns
a pre-created rate limit as the required owner for bounded recovery. The
proxy MUST NOT treat temporary rate limiting as permission to rebind the
continuation. Exhausted recovery MUST be classified as
`continuity_owner_rate_limited`; `previous_response_owner_unavailable` remains
reserved for owner loss or other existing unavailable-owner conditions.

#### Scenario: Rate-limited owner has a healthy sibling

- **GIVEN** account A owns the continuation and account B is healthy
- **WHEN** account A returns a replay-safe pre-created rate limit
- **THEN** bounded recovery retries only account A
- **AND** account B does not receive account A's continuation
