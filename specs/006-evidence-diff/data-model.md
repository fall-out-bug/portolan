# Data Model: Evidence Graph Diff

## Diff Command Inputs

```bash
portolan diff --base testdata/evidence-diff/base.json --head testdata/evidence-diff/head.json --out diff.json --force
```

Rules:

- `--base` is the earlier graph.
- `--head` is the later graph.
- Both files must be local evidence graph JSON.
- The command must not read repositories, selection files, metadata exports, or
  runtime inputs.
- Malformed input prevents output creation.

## Fact Identity

Node identity:

```text
node:<node.id>
```

Edge identity:

```text
edge:<edge.kind>:<edge.from>:<edge.to>
```

Rules:

- Duplicate node identities in one graph are invalid.
- Duplicate edge identities in one graph are invalid.
- Matching is exact. No label matching, rename detection, or fuzzy matching is
  part of this slice.

## Diff Output

Initial diff JSON shape:

```json
{
  "schema_version": "0.1.0",
  "generated_by": "portolan diff",
  "base": "testdata/evidence-diff/base.json",
  "head": "testdata/evidence-diff/head.json",
  "summary": {
    "added": 1,
    "removed": 1,
    "changed": 1,
    "unchanged": 1,
    "evidence_state_transitions": 1
  },
  "facts": [
    {
      "id": "node:payments-api",
      "fact_kind": "node",
      "change": "changed",
      "before": {
        "evidence_state": "unknown",
        "source": "base fixture"
      },
      "after": {
        "evidence_state": "metadata-visible",
        "source": "head fixture"
      },
      "evidence_transition": {
        "from": "unknown",
        "to": "metadata-visible"
      }
    }
  ]
}
```

Rules:

- `change` is one of `added`, `removed`, `changed`, or `unchanged`.
- `fact_kind` is one of `node` or `edge`.
- `before` is absent for added facts.
- `after` is absent for removed facts.
- `evidence_transition` appears only when a matched fact changes evidence state.
- The output must not include verdict fields such as `pass`, `fail`,
  `readiness`, `degraded`, `improved`, or `score`.
- Facts should be sorted by fact identity for deterministic output.

## Change Detection

A fact is:

- `added` when its identity appears only in the head graph;
- `removed` when its identity appears only in the base graph;
- `unchanged` when the matched fact is semantically identical;
- `changed` when the matched fact has different kind, label, endpoint fields,
  evidence state, evidence source, observed timestamp, or reason.

The exact comparison should use the graph model rather than raw JSON byte order.
