# Tasks: Landscape Map Orchestration

**Input**: Design documents from `specs/016-landscape-map-orchestration/`

**Prerequisites**: `spec.md`, `plan.md`, `data-model.md`, `quickstart.md`,
`contracts/landscape-map-cli.md`

**Tests**: Required. Add focused failing tests before behavior changes.

## Phase 1: Contract And Fixture Tests

- [ ] T001 [P] Add CLI tests for `portolan map --selection <selection> --out <dir>` in `internal/app/app_test.go`.
- [ ] T002 [P] Add CLI tests proving `--selection` and `--root` are mutually exclusive in `internal/app/app_test.go`.
- [ ] T003 [P] Add regression tests proving `portolan map --root` still writes the existing bundle in `internal/app/app_test.go`.
- [ ] T004 [P] Add fixture `testdata/landscape-map/selection.json` with at least four repositories, metadata, runtime, claims, black boxes, and local tool-output files.
- [ ] T005 [P] Add Bigtop incomplete-coverage fixture under `testdata/apache-bigtop-landscape/` that omits an active product repository and must block acceptance before scan execution.
- [ ] T006 [P] Add artifact validation tests for `coverage.json`, `run.json`, `graph.json`, `findings.jsonl`, and `map.md`.

## Phase 2: Selection And Coverage Model

- [ ] T007 Extend selection parsing to represent imported local tool outputs or document and implement their supported metadata encoding in `internal/selection/`.
- [ ] T008 Add coverage data structures and deterministic writer in `internal/coverage/`.
- [ ] T009 Implement per-input coverage records for repositories, metadata, runtime, claims, black boxes, and tool outputs.
- [ ] T010 Implement Bigtop corpus-manifest coverage comparison against a landscape selection, including source-repository requirements for active and external product targets.
- [ ] T011 Implement the full-corpus gate: any omitted or non-source-visible active/external Bigtop product repository blocks acceptance before artifact writes.
- [ ] T012 Add schema or contract documentation for `coverage.json` under `schema/` or `specs/016-landscape-map-orchestration/contracts/`.

## Phase 3: Map Orchestration

- [ ] T013 Add `--selection` parsing to `runMap` in `internal/app/app.go`.
- [ ] T014 Refactor `internal/maprun` options so map can run from either a root shortcut or an explicit selection.
- [ ] T015 Implement root shortcut as generated one-repository selection while preserving existing behavior.
- [ ] T016 Orchestrate multi-repository scan inputs without collapsing repository identities.
- [ ] T017 Ensure output path validation rejects unsafe paths and prevents generated artifacts from being mapped as source inputs.
- [ ] T018 Write `coverage.json` alongside `run.json`, `graph.json`, `findings.jsonl`, and `map.md`.

## Phase 4: OSS Tool Output Composition

- [ ] T019 [P] Add local SBOM/dependency tool-output fixture and importer normalization test.
- [ ] T020 [P] Add local code-size/language inventory tool-output fixture and importer normalization test.
- [ ] T021 [P] Add local duplication tool-output fixture and importer normalization test.
- [ ] T022 [P] Add local configuration or contract surface tool-output fixture and importer normalization test.
- [ ] T023 Implement tool-output attribution with tool name, version when available, input path, evidence state, and limitations.
- [ ] T024 Ensure imported tool findings do not include raw private code snippets or secret values.

## Phase 5: Landscape Graph And Findings

- [ ] T025 Preserve stable selection ids in graph nodes and edges for every repository and imported evidence source.
- [ ] T026 Emit relationship findings across selected repositories, metadata, runtime exports, claims, and imported tool outputs.
- [ ] T027 Emit contract/surface findings from supported local manifests and imported tool outputs.
- [ ] T028 Emit duplication findings from imported local tool evidence.
- [ ] T029 Emit configuration findings with secret-value redaction and source pointers only.
- [ ] T030 Emit technical-debt findings derived from relationship, duplication, config, importer, black-box, unknown, and cannot-verify evidence without readiness verdicts.
- [ ] T031 Emit `not_assessed` findings for unsupported detector families and unsupported languages.

## Phase 6: CTO Packet And Agent Surfaces

- [ ] T032 Update packet rendering to include landscape inventory, repo/product matrix, relationships, contracts/surfaces, duplication, configuration, legacy/debt, unknowns, and next-agent tasks.
- [ ] T033 Ensure `map.md` is generated only from `graph.json`, `findings.jsonl`, `coverage.json`, and `run.json`.
- [ ] T034 Update `agent/AGENT_GUIDE.md`, `agent/START_HERE.md`, and portable skill content to prefer `Landscape: <selection.json>` and `map --selection`.
- [ ] T035 Update `.cursor/rules/portolan-map.mdc` to delegate to the portable landscape workflow without copying Bigtop-specific instructions.
- [ ] T036 Update Bigtop acceptance documentation to use the full landscape selection and full-corpus gate.

## Phase 7: Full Bigtop Readiness Verification

- [ ] T037 Generate or validate a Bigtop landscape selection from `corpora/apache-bigtop/manifest.json` with 100% local source-repository coverage for active and external product repositories.
- [ ] T038 Run the incomplete Bigtop fixture and verify it blocks before acceptance.
- [ ] T039 Run the complete Bigtop landscape selection and verify all five artifacts are written.
- [ ] T040 Inspect `coverage.json` and confirm every active/external Bigtop product repository is local and source-visible, and every non-source inventory id is represented with the correct evidence state.
- [ ] T041 Inspect `map.md` and confirm CTO packet sections are present and artifact-backed.
- [ ] T042 Record the first full Bigtop landscape scan result under `specs/016-landscape-map-orchestration/reviews/`.

## Phase 8: Baseline Verification And Closeout

- [ ] T043 Run `go test ./...`.
- [ ] T044 Run `jq empty schema/*.json`.
- [ ] T045 Run JSON syntax checks for all new fixture and generated example JSON files.
- [ ] T046 Run `git diff --check`.
- [ ] T047 Update `docs/product-backlog.md` so P1/P2 status reflects the landscape orchestration gate.
- [ ] T048 Record implementation closeout with verified, failed, blocked, and not-assessed surfaces under `specs/016-landscape-map-orchestration/reviews/`.

## Dependencies

- Phase 1 blocks implementation.
- Phase 2 blocks Bigtop full-corpus gating.
- Phase 3 blocks all real `map --selection` runs.
- Phase 4 can proceed in parallel after Phase 2 data contracts stabilize.
- Phase 5 depends on Phases 3 and 4.
- Phase 6 depends on machine artifacts from Phases 3-5.
- Phase 7 is the acceptance gate and must not be replaced by fixture success.
