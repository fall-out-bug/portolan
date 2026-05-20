# PR Readiness Closeout: 009 Map Command Artifacts

Date: 2026-05-20

## PR State

| Surface | Status | Evidence |
| --- | --- | --- |
| PR | verified | GitHub PR #9: `https://github.com/fall-out-bug/portolan/pull/9`. |
| Head | verified | `codex/009-map-command-artifacts` at `559db09d4b10cd7e770d5d6855d597ef3f13e3d9`. |
| Base | verified | `main`. |
| Draft state before closeout | verified | PR was draft during reconstruction. |
| Merge state | not_assessed | GitHub reported `UNKNOWN` during closeout reconstruction. |
| GitHub checks | not_assessed | `gh pr checks 9` reported no checks on the branch. |

## Local Verification

| Check | Status |
| --- | --- |
| `go test ./...` | verified |
| `jq empty schema/*.json corpora/apache-bigtop/manifest.json` | verified |
| `go run ./cmd/portolan map --root testdata/map-command/repo --out /tmp/portolan-map-run --force` | verified |
| `jq empty /tmp/portolan-map-run/run.json /tmp/portolan-map-run/graph.json` | verified |
| JSONL parse check over `/tmp/portolan-map-run/findings.jsonl` | verified |
| `git diff --check` | verified |

## PR Review Evidence

| Lane | Status | Notes |
| --- | --- | --- |
| `openrouter/deepseek/deepseek-v4-pro` | verified | Minor fixture duplication and graph schema concern; fixture duplication fixed, schema concern rejected with local evidence. |
| `openrouter/qwen/qwen3.6-plus` | verified | Minor broad output path, missing flag tests, rollback-error concerns, and stale 007 runbook. Accepted fixes applied where actionable. |
| `openrouter/~google/gemini-pro-latest` | verified | Minor fixture duplication only; fixed. |
| Local review | verified | Output safety, evidence-state honesty, JSONL/run metadata, and CLI behavior reviewed in `implementation-review-disposition-2026-05-20.md`. |

## Status Matrix

| Surface | Status |
| --- | --- |
| Implementation | verified |
| Local verification | verified |
| Review evidence | verified with minor accepted fixes applied |
| PR state | draft before closeout; ready-for-review after this closeout if GitHub accepts state change |
| GitHub checks | not_assessed |
| Merge readiness | not_assessed; no human approval and no GitHub checks |
| Stop reason | Ready-for-review PR is the correct stop point; merge is not authorized. |

## Residual Risks

- GitHub checks are absent, not green.
- Merge approval is absent.
- Relationship, duplication, configuration, and technical-debt detectors remain
  `not_assessed` placeholder findings by design for this slice.
