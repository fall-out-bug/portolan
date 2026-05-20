# Research: Evidence Graph Diff

## Machine-Readable Diff First

Decision: Emit JSON diff output before adding human-readable summaries.

Rationale: Portolan's graph and packet workflow depends on machine-readable
artifacts. A structured JSON diff can feed later packet rendering, agent review,
or adapter workflows without scraping CLI text.

Alternatives considered: Markdown-first diff output was rejected because it
would make downstream automation brittle and risk turning the diff into a
narrative report.

## Deterministic Fact Matching

Decision: Match nodes by `node:<id>` and edges by `edge:<kind>:<from>:<to>`.

Rationale: The current graph schema gives nodes stable ids and edges stable
structural identity. This is enough for the first diff and avoids fuzzy matching
or rename heuristics.

Alternatives considered: Label-based matching and rename detection were rejected
because they introduce ambiguity and judgement before stable graph identities
are proven.

## Verdict-Free Language

Decision: Use neutral change kinds: `added`, `removed`, `changed`, and
`unchanged`, plus evidence-state transition fields.

Rationale: Portolan is not a readiness gate. The same state transition can mean
different things depending on context, so the first diff should report movement
without pass/fail, degraded, improved, ready, or score language.

Alternatives considered: Scoring visibility or labeling transitions as better
or worse was rejected because it would create policy semantics outside
Portolan's scout boundary.

## Existing Open Source And Patterns

Decision: Implement the first diff with Go standard-library JSON processing and
domain-specific fact identities.

Rationale: General JSON diff libraries compare syntax trees rather than
Portolan graph facts. Graph-diff algorithms are unnecessary until graph
structure becomes richer.

Alternatives considered: Using a generic JSON Patch or graph library was
rejected for this slice because it would not naturally preserve evidence-state
semantics and would add dependency review cost.
