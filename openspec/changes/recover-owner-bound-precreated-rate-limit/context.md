## Decision

A positively identified rate limit before upstream acceptance is safe to retry
once because no response id, model output, downstream output, sequence, or tool
effect exists. Account-scoped continuity still forbids migration, so the proxy
waits for the same owner rather than selecting a sibling account.

The wait reuses the existing 30-second capacity-recovery loop and remains
bounded by the original request deadline. Shared/account response-create
admission is released while the request stays in the bridge queue and retains
the response-create gate. Hidden public startup waits emit no vendor event;
backend streams may receive existing capacity keepalives.

If the owner does not recover, the known rate limit remains a 429. A genuinely
missing owner, policy conflict, authentication failure, or post-output failure
retains its existing classification.
