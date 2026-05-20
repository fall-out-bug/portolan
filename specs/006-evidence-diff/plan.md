# Implementation Plan: Evidence Graph Diff

**Branch**: `006-evidence-diff` | **Date**: 2026-05-20 | **Spec**: [spec.md](spec.md)
**Input**: Product backlog P1-006: compare two evidence graphs and show what
became visible, changed, or stayed unknown.

## Summary

Add a verdict-free machine-readable diff for two Portolan evidence graph files.
The CLI reads a base graph and a head graph, matches nodes and edges by stable
fact identity, reports added, removed, changed, and unchanged facts, records
evidence-state transitions, and writes deterministic JSON to an explicit output
path.

## Technical Context

**Language/Version**: Go 1.26 module, standard library first.
**Primary Dependencies**: Go standard library; `jq` for JSON syntax checks.
**Input Format**: `schema/evidence-graph.schema.json` shaped graph JSON.
**Output Format**: Portolan diff JSON, documented in `data-model.md`.
**Storage**: Local graph JSON inputs and explicit diff JSON output.
**Testing**: `go test -count=1 ./...`; fixture diff command; `jq empty
schema/*.json`; `git diff --check`.
**Target Platform**: Local CLI on macOS/Linux first.
**Project Type**: Single Go CLI.
**Performance Goals**: Fixture diff completes in under 1 second.
**Constraints**: No network, no daemon, no credentials, no target repository
reads, no readiness or pass/fail verdict fields.
**Scale/Scope**: Two graph files per command invocation; exact matching only.

## Decision Gate

| Question | Answer |
| --- | --- |
| Simpler/Faster | Implement domain-specific graph fact matching with stdlib JSON instead of adding a generic diff or graph library. |
| Blocking Edge Cases | Duplicate fact identities, malformed graphs, and evidence-state transitions need deterministic behavior; the output must not imply readiness, degradation, or improvement. |
| Existing Open Source | JSON Patch and graph diff libraries exist, but they compare syntax or graph structure rather than Portolan evidence facts. A small in-house matcher is justified for this domain-specific contract. |

## OSS Fit Review

| Candidate | Fit | Maturity | License Risk | Integration Cost | Decision |
| --- | --- | --- | --- | --- | --- |
| Go stdlib JSON plus fact matcher | Best first fit for Portolan-specific graph identities. | Stable. | None. | Low. | Accept. |
| JSON Patch / RFC 6902 libraries | Good for JSON document edits, weak for evidence fact semantics. | Mature. | Library-dependent. | Medium. | Reject for first slice. |
| Generic graph diff libraries | Useful for richer graph algorithms, overkill for current schema. | Varies. | Library-dependent. | Medium to high. | Defer. |
| Text diff tools | Easy to run but unsuitable for machine consumers. | Mature. | Low. | Low. | Reject. |

## Constitution Check

| Rule | Status | Evidence |
| --- | --- | --- |
| Local-first and read-only | Pass | Diff reads two local graph files and writes only the selected diff output. |
| Evidence state honesty | Pass | Output records exact evidence transitions without verdict language. |
| Complement existing tools | Pass | Diff consumes Portolan graph output and does not replace policy or observability tools. |
| SpecKit before implementation | Pass | This spec, plan, data model, contract, quickstart, and tasks make P1-006 implementable before code changes. |
| Test-first behavior | Pass | Tasks start with fixtures and failing CLI/diff tests. |

## Project Structure

```text
cmd/portolan/
└── main.go

internal/
├── app/
├── diff/
└── graph/

testdata/
└── evidence-diff/
    ├── base.json
    ├── head.json
    ├── duplicate-node.json
    ├── duplicate-edge.json
    └── malformed.json
```

## Design Decisions

| Decision | Rationale | Rejected Alternative | Reversibility | Risk If Wrong | Confidence |
| --- | --- | --- | --- | --- | --- |
| Add `portolan diff --base --head --out` | Clear local IO and mirrors existing output commands. | Hide diff under packet renderer. | High. | Command taxonomy may need adjustment if graph subcommands are grouped later. | High |
| Match by graph fact identity | Stable and deterministic for current graph schema. | Fuzzy matching or rename detection. | Medium. | Renamed facts will appear as remove/add or changed labels. | High |
| Emit JSON first | Supports agents and downstream tools. | Markdown summary first. | High. | Human UX needs a later packet extension. | High |
| Use neutral change language | Preserves Portolan's non-gate boundary. | Add improvement/degradation labels. | High. | Users may ask for interpretation sooner. | High |
| Fail on duplicate identities | Prevents ambiguous diffs. | Pick first duplicate or merge silently. | Medium. | Some current graph producers may need cleanup. | High |

## Verification Plan

- Unit tests for node and edge fact identity generation.
- Unit tests for added, removed, unchanged, changed, and evidence-state
  transition detection.
- CLI tests for `diff --base <file> --head <file> --out <file> [--force]`.
- Malformed graph and duplicate identity tests proving no partial output.
- Test proving generated output contains no readiness, pass/fail, degraded,
  improved, or score field names.
- Deterministic ordering test for repeated fixture diffs.
- `go test -count=1 ./...`.
- `jq empty schema/*.json`.
- `go run ./cmd/portolan diff --base testdata/evidence-diff/base.json --head
  testdata/evidence-diff/head.json --out /tmp/portolan-diff.json --force`.
- `jq empty /tmp/portolan-diff.json`.
- `git diff --check`.

## Risks

- The graph schema may gain richer fact identity later. Mitigation: isolate
  identity generation in `internal/diff` and document current matching.
- Diff JSON has no schema artifact at first. Mitigation: fixture tests and
  data-model contract define the shape; add `schema/evidence-diff.schema.json`
  when external consumers need formal validation.
- Users may want verdicts. Mitigation: keep Portolan neutral and leave policy
  interpretation to later explicit tools.
