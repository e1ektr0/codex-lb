## ADDED Requirements

### Requirement: HTTP transport policy is resolved before bridge admission

The proxy MUST resolve explicit upstream transport and the effective per-key or
global downstream-HTTP transport policy before creating an HTTP bridge session
or opening its upstream WebSocket. When the result is HTTP, the proxy MUST
bypass the bridge and send the request through the raw upstream HTTP/SSE path.
This pre-dispatch decision MUST NOT replay an already submitted request or
change native downstream WebSocket behavior.

#### Scenario: Global always-HTTP policy bypasses bridge

- **GIVEN** bridge support is enabled and base upstream transport is automatic
- **AND** effective HTTP policy is `always_http` or `pinned`
- **WHEN** an HTTP/SSE Responses request is admitted
- **THEN** no bridge session or upstream WebSocket is created
- **AND** the request uses upstream HTTP

#### Scenario: Per-key policy retains precedence

- **GIVEN** bridge support is enabled and base upstream transport is automatic
- **WHEN** an API key supplies a non-null transport-policy override
- **THEN** that override decides bridge admission ahead of the global policy

#### Scenario: Explicit WebSocket transport retains precedence

- **GIVEN** upstream transport is explicitly configured as WebSocket
- **WHEN** an HTTP/SSE request is admitted
- **THEN** explicit transport precedence retains bridge eligibility
