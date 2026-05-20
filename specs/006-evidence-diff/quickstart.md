# Quickstart: Evidence Graph Diff

This quickstart describes the target behavior for the evidence diff slice.

## Prepare Fixture

```bash
mkdir -p /tmp/portolan-diff
cat >/tmp/portolan-diff/base.json <<'JSON'
{
  "schema_version": "0.1.0",
  "generated_by": "fixture",
  "nodes": [
    {
      "id": "payments-api",
      "kind": "service",
      "label": "Payments API",
      "evidence": {
        "state": "unknown",
        "source": "base fixture",
        "reason": "service declared without metadata"
      }
    },
    {
      "id": "legacy-worker",
      "kind": "service",
      "label": "Legacy Worker",
      "evidence": {
        "state": "claim-only",
        "source": "base fixture"
      }
    }
  ],
  "edges": []
}
JSON
cat >/tmp/portolan-diff/head.json <<'JSON'
{
  "schema_version": "0.1.0",
  "generated_by": "fixture",
  "nodes": [
    {
      "id": "payments-api",
      "kind": "service",
      "label": "Payments API",
      "evidence": {
        "state": "metadata-visible",
        "source": "head fixture"
      }
    },
    {
      "id": "ledger-api",
      "kind": "service",
      "label": "Ledger API",
      "evidence": {
        "state": "metadata-visible",
        "source": "head fixture"
      }
    }
  ],
  "edges": [
    {
      "from": "payments-api",
      "to": "ledger-api",
      "kind": "depends-on",
      "evidence": {
        "state": "metadata-visible",
        "source": "head fixture"
      }
    }
  ]
}
JSON
```

## Run

```bash
go run ./cmd/portolan diff --base /tmp/portolan-diff/base.json --head /tmp/portolan-diff/head.json --out /tmp/portolan-diff/diff.json --force
jq empty /tmp/portolan-diff/diff.json
```

## Expected Outcome

- `payments-api` is reported as changed.
- The `payments-api` evidence transition is `unknown` to `metadata-visible`.
- `ledger-api` is reported as added.
- `legacy-worker` is reported as removed.
- The dependency edge is reported as added.
- The diff contains neutral change facts and no readiness, pass/fail, improved,
  degraded, or score verdicts.
