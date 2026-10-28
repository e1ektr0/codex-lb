# Exilion live backport

- Backport the feature and regression-test commits from upstream PR #2682 onto
  the deployed `1.25.0-beta.1` direct-WebSocket diagnostics baseline.
- Retain existing native Responses empty-terminal behavior: native collected
  JSON does not backfill an empty terminal from item events on this baseline.
  Adjust the imported transport test expectation accordingly; public Responses
  output backfill and native SSE item preservation remain covered.
- Upstream dependency-maintenance commits are separate from this compatibility
  fix and are not included in this backport.
- The deployment branch in `e1ektr0/codex-lb` is `exilion-live`.
