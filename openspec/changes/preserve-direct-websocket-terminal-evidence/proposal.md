## Why

Production direct `/v1/responses` WebSocket traffic exposed a two-stage failure
chain. A socket vanished without a close frame and codex-lb charged the loss to
the serving account. Later, an already-selected continuation owner returned a
retryable terminal event, but codex-lb replaced that real event with
`Previous response owner account is unavailable` even though the owner had
completed the preceding request 578 ms earlier.

Transport evidence and owner-selection evidence must remain distinct. Request
progress controls replay safety; it does not make a frame-less transport loss
an account fault. A terminal event returned by a selected owner is not proof
that the owner was unavailable before dispatch.

## What Changes

- Treat direct Responses WebSocket endings without an upstream-authored close
  frame as account-neutral regardless of response progress.
- Keep post-output direct requests fail-closed and non-replayable.
- Preserve the original sanitized upstream terminal event when account-bound
  migration is unsafe.
- Keep `previous_response_owner_unavailable` for genuine pre-dispatch required
  owner selection/connect failures.
- Preserve settlement-before-health ordering and apply health classification
  exactly once through normal finalization.

## Capabilities

### New Capabilities

(none)

### Modified Capabilities

- `responses-api-compat`: direct WebSocket terminal transport and selected-owner
  error evidence retain their correct classifications.

## Impact

Direct Responses WebSocket failure classification and regression coverage only.
No setting, schema, migration, routing policy, replay eligibility, or external
API shape is added.
