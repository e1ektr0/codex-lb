## Why

An owner-bound HTTP bridge continuation can receive an upstream rate limit
before `response.created`. codex-lb currently marks the owner rate-limited and
immediately tries to reconnect to that same required owner. Selection then
fails during the short cooldown, replacing the known rate limit with
`stream_incomplete`; subsequent client retries surface
`previous_response_owner_unavailable` until the owner recovers.

## What Changes

- Wait once for the existing bounded account-recovery interval when a
  replay-safe pre-created `rate_limit_exceeded` request must remain on its
  current owner.
- Retry that same owner at most once without enabling cross-account replay.
- Preserve a typed 429 owner-rate-limit error when bounded recovery is
  exhausted instead of rewriting it as `stream_incomplete` or owner missing.
- Keep existing diagnostics and all post-output no-replay guarantees.

## Capabilities

### New Capabilities

(none)

### Modified Capabilities

- `responses-api-compat`: owner-bound pre-created rate limits receive bounded
  same-owner recovery and retain their rate-limit classification.
- `sticky-session-operations`: temporary owner rate limiting is distinct from
  a missing or unusable continuity owner.

## Impact

HTTP bridge recovery and error classification only. No settings, schema,
migration, direct-WebSocket, account cooldown, or routing-policy changes.
