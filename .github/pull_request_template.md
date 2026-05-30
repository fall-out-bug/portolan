# Pull Request

## Scope

- Spec or issue:
- User-facing change:
- Out of scope:

## Verification

Record every relevant check as `verified`, `failed`, `blocked`, or
`not_assessed`.

- [ ] `go test -count=1 ./...`
- [ ] `jq empty schema/*.json`
- [ ] `git diff --check`
- [ ] Affected CLI command or fixture smoke, if applicable:
- [ ] GitHub checks:

## Evidence-State Impact

- `verified`:
- `failed`:
- `blocked`:
- `source-visible` or N/A:
- `metadata-visible` or N/A:
- `runtime-visible` or N/A:
- `claim-only` or N/A:
- `unknown` or N/A:
- `cannot_verify` or N/A:
- `not_assessed` or N/A:

## Product-Claim Impact

- [ ] No public product claim changes.
- [ ] Product claims updated in `docs/product-claims.md`.
- [ ] Claim remains blocked, failed, rejected, or not_assessed and is not used as positive wording.

## Safety

- [ ] No new network access, daemon behavior, credentials, or target repository mutation without explicit approval.
- [ ] No private source, secrets, customer data, or sensitive vulnerability details are included.
- [ ] Docs, schemas, fixtures, task ledgers, and review artifacts are aligned where applicable.
