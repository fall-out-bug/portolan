# Data Model: Runtime Security Boundary

## Runtime Observation

- `id`: stable observation ID
- `source`: local file path
- `observed_at`: optional timestamp
- `subjects`: services, processes, endpoints, or systems observed
- `relationships`: observed communications or dependencies
- `coverage`: complete, partial, unknown, or not_assessed

## Runtime Relationship

- `from`: observed source
- `to`: observed target
- `kind`: communication, dependency, or event
- `evidence_state`: `runtime-visible`
- `reason`: why the observation supports the relationship

## Threat Record

- `risk`: prompt injection, path traversal, secret leakage, unsafe query/MCP exposure
- `surface`: artifact or command surface
- `mitigation`: implementation or documentation control
- `verification`: command/test/review evidence
- `state`: verified, failed, blocked, or not_assessed
