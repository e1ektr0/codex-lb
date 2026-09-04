## 1. Specification

- [x] 1.1 Define bounded same-owner recovery for replay-safe pre-created rate limits.
- [x] 1.2 Preserve owner binding, no-replay-after-output, and typed exhaustion errors.

## 2. Implementation

- [x] 2.1 Extend the existing capacity wait to owner-bound `rate_limit_exceeded` events.
- [x] 2.2 Keep one retry maximum and require the current owner.
- [x] 2.3 Return a typed 429 when owner recovery is exhausted.

## 3. Validation

- [x] 3.1 Add unit and endpoint regressions for recovery and exhaustion.
- [x] 3.2 Run focused suites, lint, type checks, strict OpenSpec validation, and review.
- [x] 3.3 Deploy the exact tested revision and verify readiness and diagnostics.
