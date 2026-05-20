# Feature Specification: Evidence Graph Diff

**Feature Branch**: `006-evidence-diff`
**Created**: 2026-05-20
**Status**: Ready for implementation
**Input**: Product backlog P1-006: compare two evidence graphs and show what became visible, changed, or stayed unknown.

## User Scenarios & Testing

### User Story 1 - Compare Two Runs (Priority: P1)

A platform lead compares two Portolan graph outputs and sees added, removed,
unchanged, and changed graph facts. The output is machine-readable so follow-up
tools can parse it without scraping text.

**Why this priority**: Once Portolan can emit graphs, users need to understand
what changed between scans without turning the diff into a readiness gate.

**Independent Test**: Fixture graphs produce a diff where one fact moves from `unknown` to `metadata-visible`.

**Acceptance Scenarios**:

1. **Given** two graph JSON files, **When** `portolan diff --base before.json
   --head after.json --out diff.json` runs, **Then** the output contains
   deterministic added, removed, changed, and unchanged fact lists.
2. **Given** a node exists in both graphs but its evidence state changes from
   `unknown` to `metadata-visible`, **When** diff runs, **Then** the output
   records an evidence-state transition without calling it success or
   improvement.
3. **Given** an edge is present only in the head graph, **When** diff runs,
   **Then** it is reported as an added edge with its evidence state and source.

### User Story 2 - Avoid Readiness Verdicts (Priority: P1)

A reviewer sees movement facts without Portolan declaring improvement,
degradation, readiness, modernization progress, or failure.

**Why this priority**: Portolan is a scout and normalizer, not a policy gate.
The diff must preserve evidence movement without inventing judgement.

**Independent Test**: Diff output reports state transitions but no pass/fail or readiness verdict.

**Acceptance Scenarios**:

1. **Given** a graph fact disappears between runs, **When** diff runs, **Then**
   the output reports removal as a fact change and does not label it as bad,
   degraded, or failed.
2. **Given** a fact becomes more visible, **When** diff runs, **Then** the
   output reports the exact state transition and does not label it as passed,
   ready, or improved.
3. **Given** a diff is rendered in later human output, **When** summary text is
   generated, **Then** it uses neutral language such as added, removed, changed,
   unchanged, and evidence transition.

### User Story 3 - Keep Diff Stable For Agents And Tools (Priority: P2)

An agent can consume the diff JSON and decide which facts need follow-up without
depending on line-oriented text.

**Why this priority**: The diff is a product substrate for later packet,
review, and adapter workflows.

**Independent Test**: A fixture diff validates as JSON and every fact entry has
a deterministic id, fact kind, change kind, and before/after evidence when
available.

**Acceptance Scenarios**:

1. **Given** equivalent graphs are diffed repeatedly, **When** diff runs, **Then**
   fact ordering and summary counts are deterministic.
2. **Given** one graph is malformed, **When** diff runs, **Then** the command
   fails clearly and does not write a partial diff.
3. **Given** graph ids are stable, **When** diff runs, **Then** matching uses
   node ids and edge identity, not labels or fuzzy rename detection.

## Edge Cases

- Base graph path is missing or malformed: return a clear error and do not
  write a partial diff.
- Head graph path is missing or malformed: return a clear error and do not write
  a partial diff.
- A node id exists in both graphs but the node kind changes: report a changed
  node, not a remove plus add.
- An edge has the same `from`, `to`, and `kind` but evidence changes: report a
  changed edge with an evidence transition.
- An edge references a missing node in one graph: still diff the edge by edge
  identity and preserve the malformed reference as graph input evidence; graph
  structural validation remains a separate concern until schema validation is
  added.
- Duplicate node ids or duplicate edge identities appear in one graph: fail
  with a deterministic validation error unless a later graph merge policy
  defines duplicates.
- Fact labels change while ids stay the same: report a changed fact without
  treating it as a rename.
- Diff output must not contain verdict fields such as `pass`, `fail`,
  `readiness`, `degraded`, `improved`, or `score`.

## Requirements

### Functional Requirements

- **FR-001**: System MUST accept two local evidence graph JSON files.
- **FR-002**: System MUST report added, removed, unchanged, and changed facts.
- **FR-003**: System MUST report evidence-state transitions for changed facts.
- **FR-004**: System MUST NOT emit readiness, modernization, degradation,
  improvement, pass/fail, or score verdicts.
- **FR-005**: System MUST support machine-readable JSON diff output before human
  summaries.
- **FR-006**: System MUST use deterministic fact identities for matching:
  `node:<id>` for nodes and `edge:<kind>:<from>:<to>` for edges.
- **FR-007**: System MUST fail clearly and avoid partial output when either
  input graph is malformed or contains duplicate fact identities.
- **FR-008**: System MUST read only the two graph files and write only the
  explicit diff output file.
- **FR-009**: System MUST preserve source and evidence details from before and
  after facts when reporting changes.
- **FR-010**: System MUST keep rename detection, fuzzy matching, semantic
  scoring, and policy evaluation out of scope for the first diff.

### Key Entities

- **Base Graph**: Earlier graph JSON input.
- **Head Graph**: Later graph JSON input.
- **Fact Identity**: Deterministic key for matching nodes and edges.
- **Diff Fact**: Machine-readable entry describing one added, removed,
  unchanged, or changed node or edge.
- **Evidence Transition**: Change from one evidence state to another for the
  same fact identity.
- **Diff Summary**: Aggregate counts by fact kind, change kind, and evidence
  transition.

## Success Criteria

- **SC-001**: Fixture diff output identifies at least one added node, one
  removed fact, one changed edge, one unchanged fact, and one evidence-state
  transition.
- **SC-002**: Diff output validates as JSON.
- **SC-003**: No generated diff field uses pass/fail/readiness/degradation
  verdict language.
- **SC-004**: Re-running the same fixture diff produces semantically identical
  JSON with deterministic ordering.
- **SC-005**: `go run ./cmd/portolan diff --base
  testdata/evidence-diff/base.json --head testdata/evidence-diff/head.json
  --out <diff.json> --force` exits 0 and writes parseable JSON.

## Assumptions

- Graph IDs are stable enough for first diff tests.
- Sophisticated matching and rename detection are out of scope for the first diff.
- JSON Schema validation for graph inputs may remain syntax and structural
  fixture validation until a runtime schema validator is justified.
- Human-readable diff summaries belong to a later packet extension unless the
  implementation slice needs minimal CLI stdout.
