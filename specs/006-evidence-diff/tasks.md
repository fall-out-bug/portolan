# Tasks: Evidence Graph Diff

**Input**: Design documents from `specs/006-evidence-diff/`
**Prerequisites**: `spec.md`, `plan.md`, `research.md`, `data-model.md`,
`contracts/diff-cli.md`, `quickstart.md`
**Tests**: Required. Write focused tests before behavior implementation.

## Phase 1: Fixtures And Contract Tests

- [ ] T001 [P] Add evidence diff fixtures in `testdata/evidence-diff/base.json`, `testdata/evidence-diff/head.json`, `testdata/evidence-diff/duplicate-node.json`, `testdata/evidence-diff/duplicate-edge.json`, and `testdata/evidence-diff/malformed.json`.
- [ ] T002 [P] [US1] Add failing CLI help test for `portolan diff --help` in `internal/app/app_test.go`.
- [ ] T003 [P] [US1] Add failing CLI diff test for `diff --base testdata/evidence-diff/base.json --head testdata/evidence-diff/head.json --out <file> --force` in `internal/app/app_test.go`.
- [ ] T004 [P] [US1] Add failing test proving fixture diff reports added, removed, unchanged, changed, and evidence-state transition facts in `internal/app/app_test.go`.
- [ ] T005 [P] [US2] Add failing test proving diff JSON contains no readiness, pass/fail, degraded, improved, or score field names in `internal/app/app_test.go`.
- [ ] T006 [P] [US3] Add failing malformed input and duplicate identity tests proving no partial diff file is written in `internal/app/app_test.go`.
- [ ] T007 [P] [US3] Add failing deterministic ordering test for repeated fixture diffs in `internal/app/app_test.go`.

## Phase 2: Diff Model

- [ ] T008 [US1] Add `internal/diff` package with graph loading, node fact identity, edge fact identity, and duplicate identity validation.
- [ ] T009 [US1] Add diff output types in `internal/diff` for summary, facts, before/after evidence, and evidence transitions.
- [ ] T010 [US3] Add deterministic sorting helpers in `internal/diff` for facts and summary output.

## Phase 3: Diff Engine

- [ ] T011 [US1] Implement added, removed, and unchanged fact detection in `internal/diff`.
- [ ] T012 [US1] Implement changed fact detection for node fields, edge fields, and evidence fields in `internal/diff`.
- [ ] T013 [US1] Implement evidence-state transition reporting in `internal/diff`.
- [ ] T014 [US2] Ensure diff output uses only neutral change language and no verdict field names in `internal/diff`.
- [ ] T015 [US3] Ensure malformed graph input and duplicate identities return deterministic errors before any output file is written.

## Phase 4: CLI Wiring

- [ ] T016 [US1] Wire `diff --base <file> --head <file> --out <file> [--force]` through `internal/app/app.go`.
- [ ] T017 [US1] Return clear stdout/stderr behavior and non-zero exit codes for invalid flags, malformed inputs, duplicate identities, and output write failures.
- [ ] T018 [US1] Keep `cmd/portolan/main.go` thin and route behavior through `internal/app`.

## Phase 5: Documentation, Review, And PR

- [ ] T019 Update `README.md` command examples after evidence diff exists.
- [ ] T020 Update `docs/product-backlog.md` and `specs/006-evidence-diff/spec.md` status after implementation.
- [ ] T021 Record pre-implementation review disposition under `specs/006-evidence-diff/reviews/`.
- [ ] T022 Record post-slice review disposition under `specs/006-evidence-diff/reviews/`.
- [ ] T023 Run `go test -count=1 ./...`.
- [ ] T024 Run `jq empty schema/*.json`.
- [ ] T025 Run `go run ./cmd/portolan diff --base testdata/evidence-diff/base.json --head testdata/evidence-diff/head.json --out /tmp/portolan-diff.json --force`.
- [ ] T026 Run `jq empty /tmp/portolan-diff.json`.
- [ ] T027 Run `git diff --check`.
- [ ] T028 Create or update PR and run PR review cycle.

## Dependencies

- Phase 1 fixtures and tests precede implementation.
- Phase 2 diff model unblocks Phase 3 engine work.
- Phase 3 engine precedes Phase 4 CLI wiring.
- User Story 1 is the MVP; User Story 2 preserves product boundary; User Story
  3 makes the output agent/tool-friendly.

## Parallel Execution Examples

- T001, T002, T003, T004, T005, T006, and T007 can be drafted in parallel after
  the fixture shape is agreed.
- T008 and T009 can proceed in parallel if fact identity and output types are
  agreed first.
- T011, T012, and T013 should be integrated in one pass after T008 through T010.
- T019 can be drafted while final CLI verification runs.

## Implementation Strategy

Deliver the smallest coherent slice: fixtures, failing tests, diff model,
domain-specific diff engine, CLI wiring, docs, review disposition,
verification, and PR review. Do not add JSON Patch libraries, graph libraries,
human-readable diff packets, schema validation dependencies, rename detection,
scoring, readiness verdicts, or policy evaluation in this slice.
