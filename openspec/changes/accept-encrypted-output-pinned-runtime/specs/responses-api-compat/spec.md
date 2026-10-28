## ADDED Requirements

### Requirement: Encrypted output include is accepted and preserved
Native and public Responses request validation MUST accept
`output.encrypted_content` alongside `reasoning.encrypted_content` and forward
both selectors unchanged. Unknown include selectors MUST remain rejected.

#### Scenario: Codex requests encrypted output
- **WHEN** a native or public Responses request includes both encrypted selectors
- **THEN** request validation succeeds and both selectors remain in its upstream payload

#### Scenario: Unsupported selector is rejected
- **WHEN** a native or public Responses request includes an unknown selector
- **THEN** validation rejects it before upstream dispatch
