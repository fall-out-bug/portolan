# Tasks: Agent Adapter Layer

**Input**: Design documents from `specs/042-agent-adapter-layer/`

**Tests**: Required for adapter fixtures and evidence-state mapping.

## Phase 1: Setup

- [ ] T001 Create pre-implementation review in `specs/042-agent-adapter-layer/reviews/requirements-product-vision-drift-2026-05-27.md`
- [ ] T002 Record analyze disposition in `specs/042-agent-adapter-layer/reviews/analyze-disposition-2026-05-27.md`

## Phase 2: Foundational

- [ ] T003 Create first-wave candidate ledger in `specs/042-agent-adapter-layer/reviews/oss-candidate-ledger-2026-05-27.md`
- [ ] T004 Define Graphify supported subset in `docs/adapter-contracts/graphify-profile.md`

## Phase 3: User Story 1 - Evaluate First-Wave OSS Inputs (Priority: P1)

- [ ] T005 [US1] Evaluate Graphify license, maintenance, privacy, and adapter cost
- [ ] T006 [US1] Evaluate SCIP/Serena-style symbol index fit
- [ ] T007 [US1] Evaluate Repomix context pack fit

## Phase 4: User Story 2 - Normalize A Graphify-Like Output (Priority: P1)

- [ ] T008 [US2] Add Graphify-style fixture in `testdata/oss-adapter-contract/graphify-minimal.json`
- [ ] T009 [US2] Add focused adapter validation test for Graphify confidence mapping
- [ ] T010 [US2] Implement the minimal Graphify adapter/profile behavior in `internal/adapter/`
- [ ] T011 [US2] Run `go test -count=1 ./internal/adapter ./internal/app`

## Phase 5: User Story 3 - Publish Adapter Profiles For Symbol And Context Tools (Priority: P2)

- [ ] T012 [US3] Add symbol-index profile documentation in `docs/adapter-contracts/symbol-index-profile.md`
- [ ] T013 [US3] Add Repomix profile documentation in `docs/adapter-contracts/repomix-profile.md`
- [ ] T014 [US3] Update `docs/oss-composition.md` with first-wave decisions

## Phase 6: Review And Closeout

- [ ] T015 Run baseline checks
- [ ] T016 Run independent review lanes and record `slice-review-disposition-2026-05-27.md`
- [ ] T017 Update spec, task ledger, product claims if supported, and P5-042 backlog status
- [ ] T018 Prepare PR readiness closeout
