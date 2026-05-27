# Contract: Runtime Security Boundary

## Runtime Observation Input

Supported local JSON shape:

```json
{
  "schema_version": "0.1.0",
  "observations": [
    {
      "id": "obs-1",
      "observed_at": "2026-05-27T00:00:00Z",
      "from": "service-a",
      "to": "service-b",
      "kind": "http-call",
      "coverage": "partial",
      "source": "runtime/export.json"
    }
  ]
}
```

Partial coverage never proves complete topology.

## Threat Model Contract

The threat model must cover:

- untrusted repo instructions in Markdown/config/source;
- path traversal and symlink escapes;
- secret value leakage;
- future MCP/query exposure;
- stale evidence reuse.

Each threat needs mitigation, verification state, and residual risk.
