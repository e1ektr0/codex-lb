## Why

An upstream WebSocket drop can fail a long-running OpenCode turn after output
has started, then make the pinned previous-response owner temporarily
unselectable. Existing logs show the transport error and owner selection miss,
but not enough state to prove whether the request was eventless or mid-stream,
whether account health was penalized, or whether the owner was blocked by
transient backoff, cooldown, status, or local capacity.

## What Changes

- Enrich HTTP bridge reader-failure logs with bounded request lifecycle,
  downstream sequence, pending-state, failure-origin, and account-health action
  fields.
- Emit a bounded continuity-owner selection diagnostic with runtime health,
  backoff, cooldown, and local-capacity state.
- Preserve all routing, retry, account-health, timeout, and public error
  behavior.
- Do not log request payloads, credentials, raw continuity identifiers, account
  emails, or upstream endpoints.

## Capabilities

### New Capabilities

(none)

### Modified Capabilities

- `proxy-runtime-observability`: bridge stream truncation and pinned-owner
  selection failures MUST expose enough safe state to reconstruct their causal
  chain.

## Impact

Structured console diagnostics and focused tests only. No API, database,
setting, metric-label, dashboard, or routing behavior changes.
