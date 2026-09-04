# proxy-runtime-observability Delta

## ADDED Requirements

### Requirement: Response continuity failure chains are diagnosable

The service MUST emit bounded diagnostics whenever an HTTP Responses bridge
reader fails before a pending request reaches `response.completed`. The
diagnostics MUST identify
the failure origin, aggregate request phase and stage, total and draining
pending counts, whether upstream output was observed, the maximum downstream
sequence number when present, whether an account-health penalty was requested, and the
pre-failure session state. When exactly one request is pending, the diagnostic
MUST include its request-log or request correlation id.

When selection of a required continuity owner fails, the service MUST emit a
diagnostic that identifies whether the owner is missing, in a non-active
status, in cooldown, in transient error backoff, at the relevant local capacity
limit, or unavailable because of another selection policy or recovery-probe
condition. The diagnostic MUST include bounded runtime counts and remaining
backoff or cooldown seconds needed to distinguish those cases.

These diagnostics MUST NOT change routing, retry, account-health, timeout, or
public error behavior. They MUST NOT contain request payloads, credentials, raw
continuity identifiers, account emails, proxy endpoints, or upstream response
content.

#### Scenario: Mid-stream transport drop records causal state

- **GIVEN** an HTTP bridge request has emitted upstream response events and a
  downstream sequence number
- **WHEN** the upstream WebSocket drops before `response.completed`
- **THEN** the reader-failure warning identifies a streaming request phase,
  failure origin, sequence number, pending counts, and account-health action
- **AND** no request payload or raw continuity identifier is logged

#### Scenario: Pinned owner is in transient error backoff

- **GIVEN** a follow-up resolves a required previous-response owner
- **AND** that account is temporarily excluded by transient error backoff
- **WHEN** required-owner selection fails closed
- **THEN** the warning records `reason=transient_error_backoff`, error count,
  and bounded remaining backoff seconds
- **AND** the request still receives the existing retryable continuity error
