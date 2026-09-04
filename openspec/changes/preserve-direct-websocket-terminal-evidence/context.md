## Decision

Direct WebSocket transport endings use the same structured frame-less predicate
as the HTTP bridge: a close-kind event, or an error-kind event with positive
`transport_ended` provenance, and no upstream-authored close frame (`None` or
synthetic `1006`). The request still terminates as `stream_incomplete`; only the
account-health attribution changes.

When an owner has already been selected and returns a retryable terminal event,
unsafe migration means "surface this event without replay", not "the owner was
unavailable". The original sanitized event proceeds through normal request
finalization so reservation settlement and request-log ownership still precede
one health update.

True owner-unavailable responses remain at the account-selection/connect layer,
before any upstream request is dispatched.
