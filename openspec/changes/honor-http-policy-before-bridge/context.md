## Decision

Bridge admission is a transport decision, so it must occur after explicit
transport precedence and effective per-key/global policy resolution. If the
result is HTTP, no bridge session or upstream WebSocket may be created. This
prevents the failure before dispatch rather than replaying an ambiguous
mid-stream execution.

Explicit `upstream_stream_transport=websocket` continues to win. A per-key
`always_websocket` override can therefore retain bridge behavior when the base
transport remains automatic, while a per-key `always_http` override bypasses
the bridge even if the global policy prefers WebSocket.
