---
name: loop-mainline
description: Route generic LOOP work through the reusable mainline surfaces, including looplib, standalone docs, and the core-vs-host adapter boundary.
---

## Use when
- You need the generic LOOP entrypoint before deciding whether the task belongs to runtime, review orchestration, or batch supervision.
- You are using LOOP in a non-LeanAtlas host and want reusable docs/examples instead of LeanAtlas workflow wrappers.
- You need to distinguish reusable `looplib` surfaces from host adapters such as LeanAtlas worktree orchestration.

## Don't use when
- You are already inside LeanAtlas-specific workflow wiring and should use `.agents/skills/leanatlas-loop-mainline/SKILL.md`.
- You only need authoritative finding settlement; use `.agents/skills/loop-review-reconciliation/SKILL.md` first.
- You are solving a single Lean problem under `Problems/**` with no system-level LOOP changes.

## Outputs
- The generic mainline entry doc and quickstart used for the task.
- A clear route to reusable `looplib` runtime/review/supervisor helpers or to a host adapter when the task is not role-neutral.
- An explicit statement of whether the requested capability is reusable LOOP core, generic orchestration, or host-specific adapter behavior.
- An explicit statement of whether the conversation-facing task agent should act as the root supervisor kernel for this non-trivial task.

## Must-run checks
- `uv run --locked python tests/contract/check_loop_library_packaging.py`
- `uv run --locked python tests/contract/check_loop_mainline_docs_integration.py`
- `uv run --locked python tests/contract/check_skills_standard_headers.py`

## Read first
- `docs/agents/LOOP_MAINLINE.md`
- `docs/setup/LOOP_LIBRARY_QUICKSTART.md`
- `looplib/__init__.py`

## Steps
1) Read `docs/agents/LOOP_MAINLINE.md` to identify whether the requested surface is implemented and whether it belongs to LOOP core, generic orchestration, or a host adapter.
2) If the task is reusable/non-LeanAtlas, route through `docs/setup/LOOP_LIBRARY_QUICKSTART.md` and `looplib/**`.
3) For non-trivial work, assume the conversation-facing task agent is the root supervisor kernel unless a narrower host adapter explicitly overrides that mapping.
4) If the task is review-specific, route into `.agents/skills/loop-review-orchestration/SKILL.md`.
5) If the task is parent-wave supervision, publication, or context rematerialization, route into `.agents/skills/loop-batch-supervisor/SKILL.md`.
6) If the task is LeanAtlas-specific workflow wiring, hand off to the corresponding `leanatlas-*` wrapper skill instead of pretending the adapter is generic LOOP.
