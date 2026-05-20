---
name: "portolan-spec-delivery"
description: "Use when implementing or reviewing Portolan SpecKit slices, taking the next ready spec, running review cycles with pi/Codex reviewers, improving a PR, marking it ready, or merging after explicit approval."
compatibility: "Portolan repository with SpecKit specs, docs/product-backlog.md, and AGENTS.md"
metadata:
  author: "portolan"
  source: "repo-local"
---

# Portolan Spec Delivery

Use this skill for requests such as:

- "бери ближайшую готовую спеку"
- "начинай с ревью через pi/codex subagents"
- "реализуй по слайсам"
- "сделай цикл ревью PR"
- "доведи PR до качественного результата"
- "сливай PR"

## Mode

Start in `REVIEW` unless the user only asks for status. Move to `IMPLEMENT`
only after the ready spec and review findings are understood. Move to `SHIP`
only when the PR state and evidence are coherent.

## Intake

1. Read `AGENTS.md`, `.specify/memory/constitution.md`, and
   `docs/product-backlog.md`.
2. Check `git status --short --branch` and existing worktrees.
3. If implementing a spec, choose the earliest ready backlog item that has
   concrete `spec.md`, `plan.md`, and `tasks.md`.
4. Work in a dedicated worktree from current `origin/main` or the requested base.
   Do not use a dirty main checkout for implementation.
5. State the decision gate:
   - Simpler/Faster
   - Blocking Edge Cases
   - Existing Open Source

## Pre-Implementation Review

Build a bounded review packet from:

- repo rules and constitution;
- backlog row;
- spec, plan, tasks, data-model, quickstart, contracts when present;
- schemas and current implementation files relevant to the slice.

Run independent review lanes. Prefer:

- one requirements/UX/DX lane;
- one security/evidence lane;
- model lanes through `pi` when useful, using enabled models from
  `~/.pi/agent/settings.json`.

For important PR/spec reviews, Claude and Gemini may be used. Count their output
only when the artifact is non-empty, on-task, and specific. Record empty or
stale output as `not_assessed`.

Write review dispositions under:

```text
specs/<NNN-short-name>/reviews/
```

Do not put review artifacts under a repo-root `reviews/` directory.

## Implementation Slices

For each slice:

1. Add or update focused tests before behavior changes unless the slice is
   docs-only.
2. Keep `cmd/portolan` thin; put behavior in `internal/...`.
3. Preserve evidence-state honesty. Never upgrade:
   - missing to success;
   - `claim-only` to observed evidence;
   - `unknown` or `cannot_verify` to green status.
4. Protect local-first/read-only boundaries:
   - no network calls;
   - no daemon behavior;
   - no credentials;
   - no target repository mutation;
   - output only to explicitly selected output paths.
5. Prefer stdlib and existing project patterns. Do not add dependencies without
   documenting OSS fit, license/maintenance risk, privacy posture, and
   integration cost.
6. Update docs, schema, fixtures, and task ledgers when behavior changes.

## Review Cycle After Each Slice

Run focused local verification first:

```bash
go test -count=1 ./...
jq empty schema/*.json
git diff --check
```

For CLI changes, also run the affected command and a fixture command.

Then run at least one independent review lane. Use more lanes when the slice
touches:

- evidence state semantics;
- graph identity or deterministic output;
- path traversal, symlinks, or output writes;
- schema compatibility;
- CLI user behavior.

Review findings must be dispositioned:

- accepted/fixed;
- accepted narrower than stated;
- rejected with local evidence;
- unresolved;
- `not_assessed`.

After fixes, rerun verification and a focused re-review for the changed risk.

## PR Review And Shipping

Before marking a PR ready:

1. Reconstruct PR head, diff, draft state, merge state, checks, and review
   artifacts with `gh pr view`, `gh pr diff`, and `gh pr checks`.
2. Run local verification.
3. Run independent review lanes. For high-risk review cycles, use Gemini and/or
   Claude through `pi` plus a repo-grounded local reviewer.
4. Fix accepted findings and record a PR review-cycle disposition under the
   spec's `reviews/` directory.
5. Push, refresh PR state, and mark ready only when blockers are fixed.

If GitHub has no checks configured, report CI as `not_assessed`, not green.

Merge only after explicit user approval. After merge, verify:

- PR state is `MERGED`;
- merge commit exists on `origin/main`;
- intended remote feature branch cleanup happened or is handled separately.
