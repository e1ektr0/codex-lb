## ADDED Requirements

### Requirement: Direct frame-less WebSocket endings are account-neutral

The proxy MUST treat a terminal direct Responses WebSocket ending without an
upstream-authored close frame as account-neutral when it is represented by a
close-kind message or by an error-kind message with positive transport-ending
provenance. This attribution MUST NOT depend on response-event count, buffered
model output, or downstream visibility. When no more-specific classified
network or liveness code exists, the interrupted request MUST still fail as
`stream_incomplete`; otherwise the specific account-neutral transport code MUST
remain visible. Existing progress and sequence guards MUST continue to prohibit
transparent replay after output.

#### Scenario: Direct WebSocket disappears after output

- **GIVEN** a direct Responses WebSocket request has emitted or buffered output
- **AND** the ending has no more-specific classified network or liveness code
- **WHEN** its upstream transport ends without an upstream-authored close frame
- **THEN** the request terminates as `stream_incomplete`
- **AND** no transparent replay occurs
- **AND** the serving account receives no failure-health write

#### Scenario: Direct protocol error lacks terminal provenance

- **GIVEN** a direct adapter emits an error-kind protocol failure without transport-ending provenance
- **WHEN** the direct relay finalizes the request
- **THEN** the failure retains its existing account-health treatment

### Requirement: Selected-owner terminal errors preserve their classifier

The direct Responses WebSocket proxy MUST preserve a retryable terminal event's
sanitized error when a required continuity owner has already been selected and
returns that event. The preserved fields include the event's
type, code, message, status, parameter, and supported metadata when transparent
migration is not proven safe. It MUST NOT replace that event with
`previous_response_owner_unavailable` or a generic owner-unavailable terminal
event. The proxy MUST apply normal settlement-before-health ordering exactly
once and MUST NOT replay or migrate the request.

#### Scenario: Selected owner returns a quota error and migration is unsafe

- **GIVEN** the required owner accepted an account-bound direct WebSocket request
- **AND** the owner returns a retryable quota or capacity terminal event before output
- **AND** no verified account-neutral replay body exists
- **WHEN** the proxy handles the terminal event
- **THEN** the client receives the original sanitized quota or capacity classification
- **AND** the request is not replayed or sent to another account
- **AND** account health is updated once after settlement

#### Scenario: Required owner cannot be selected before dispatch

- **GIVEN** a continuation requires a recorded owner
- **WHEN** that owner cannot be selected or connected before dispatch
- **THEN** the existing `previous_response_owner_unavailable` behavior remains unchanged
