# Contract: Evidence Diff CLI

## Render Machine Diff

```bash
portolan diff --base base.json --head head.json --out diff.json [--force]
```

Success:

- exit code: `0`
- stdout: includes `wrote`
- stderr: empty
- side effects: writes only the explicit diff output file
- output: valid JSON with `schema_version`, `generated_by`, `summary`, and
  `facts`

Failure:

- exit code: non-zero
- stdout: empty
- stderr: includes `diff:` followed by a deterministic error
- no partial diff file is written for malformed inputs or duplicate fact
  identities

## Required Behavior

The diff command must:

- read only `--base` and `--head`;
- match nodes by `node:<id>`;
- match edges by `edge:<kind>:<from>:<to>`;
- report `added`, `removed`, `changed`, and `unchanged` facts;
- report evidence-state transitions without verdict language;
- preserve before/after evidence state, source, observed timestamp, and reason
  when present;
- write deterministic fact ordering.

## Help

```bash
portolan diff --help
```

Help output must mention:

- `--base`;
- `--head`;
- `--out`;
- JSON output;
- neutral change language;
- no readiness or pass/fail verdicts.
