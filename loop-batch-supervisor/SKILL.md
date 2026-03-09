---
name: loop-batch-supervisor
description: Materialize and execute parent-supervisor batches with explicit publication, human-ingress, rematerialization, and integrated closeout evidence.
---

## Use when
- You need a parent LOOP to supervise multiple child waves rather than handing work off manually between waves.
- You need to publish capability artifacts, record bounded human external input, or rematerialize downstream context packs explicitly.
- You need the generic supervisor/publication runtime before deciding whether a host-specific adapter such as LeanAtlas worktrees is required.

## Don't use when
- You only need a single staged review bundle with no batch supervision; use `.agents/skills/loop-review-orchestration/SKILL.md`.
- You are changing LeanAtlas-specific worktree or maintainer workflow behavior rather than the generic parent-supervisor path.
- You only need a one-off document explanation with no runtime or contract impact.

## Outputs
- A deterministic batch-supervisor plan/state/journal with integrated closeout authority.
- Explicit publication, human-ingress, and rematerialized context-pack artifacts for downstream adoption.
- A clear boundary between reusable supervisor/publication semantics and host-specific adapters such as LeanAtlas worktrees.
- An explicit layered supervisor route: root supervisor kernel, wave supervisors, subgraph supervisors, and workers.

## Must-run checks
- `uv run --locked python tests/contract/check_loop_batch_supervisor.py`
- `uv run --locked python tests/contract/check_loop_publication_runtime.py`
- `uv run --locked python tests/contract/check_loop_worktree_adapter.py`
- `uv run --locked python tests/contract/check_loop_contract_docs.py`

## Read first
- `docs/setup/LOOP_LIBRARY_QUICKSTART.md`
- `looplib/session.py`
- `tools/loop/batch_supervisor.py`
- `tools/loop/publication.py`

## Steps
1) Materialize the parent batch with `materialize_batch_supervisor(...)` before executing child waves.
2) Publish new capability state explicitly with `publish_capability_event(...)` and bounded human input explicitly with `record_human_external_input(...)`.
3) Regenerate downstream adoption state with `rematerialize_context_pack(...)`; do not rely on hidden conversational memory.
4) Execute the batch through `execute_batch_supervisor(...)` and preserve reroute/retry decisions in the append-only journal.
5) Treat host adapters such as LeanAtlas git worktrees as optional child-wave execution modes layered on top of this generic supervisor surface.
