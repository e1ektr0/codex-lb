## Why

The HTTP Responses bridge currently opens an upstream WebSocket before the
configured downstream-HTTP transport policy is considered. Production is set
to `always_http`, yet an HTTP/SSE request still used the bridge and failed
mid-stream when that upstream WebSocket vanished without a close frame.

## What Changes

- Resolve the effective downstream-HTTP transport policy before bridge
  admission.
- Bypass bridge creation and force upstream HTTP when that policy resolves to
  HTTP.
- Preserve explicit upstream transport precedence and per-API-key policy
  precedence.
- Keep native downstream WebSocket behavior and all diagnostics unchanged.

## Capabilities

### New Capabilities

(none)

### Modified Capabilities

- `responses-api-compat`: bridge admission obeys the existing HTTP transport
  policy contract before any upstream dispatch.

## Impact

HTTP/SSE transport selection only. No schema, setting, migration, replay, or
native WebSocket change.
