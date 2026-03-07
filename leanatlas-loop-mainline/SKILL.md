---
name: leanatlas-loop-mainline
description: Route work through the current LOOP mainline system in LeanAtlas, using the canonical mainline doc, capability matrix, and role boundaries before choosing maintainer/operator/review paths.
---

## Use when
- You need to understand what LOOP capabilities are already available in LeanAtlas mainline.
- You need to route a task to the right LOOP entrypoint before editing code or planning a review.
- You need to distinguish `LOOP core` from `LeanAtlas adapters` and avoid treating planned features as if they were already implemented.

## Don't use when
- You are already deep inside a narrow LOOP implementation task and have a more specific skill, such as `.agents/skills/leanatlas-loop-maintainer-ops/SKILL.md`.
- You are solving a single problem in `Problems/**` without any need to reason about system-level LOOP behavior.

## Outputs
- The canonical mainline entry doc used for the task
- A clear route to one of:
  - maintainer LOOP work
  - operator proof-loop work
  - review acceleration / review orchestration work
- An explicit statement of whether the requested capability is implemented, partial, or planned

## Must-run checks
- `uv run --locked python tests/contract/check_loop_mainline_docs_integration.py`
- `uv run --locked python tests/contract/check_loop_mainline_productization_scope.py`
- `uv run --locked python tests/contract/check_skills_standard_headers.py`

## Read first
- `docs/agents/LOOP_MAINLINE.md`
- `docs/agents/MAINTAINER_WORKFLOW.md`
- `docs/agents/OPERATOR_WORKFLOW.md`

## Steps
1) Read `docs/agents/LOOP_MAINLINE.md` and identify the requested surface in the capability matrix.
2) Decide whether the task belongs to:
   - LOOP core
   - a LeanAtlas adapter/workflow layer
   - a planned/not-yet-implemented theme
3) If the task is a non-trivial maintainer change, switch to the ExecPlan path via `.agents/skills/leanatlas-maintainer-execplan/SKILL.md`.
4) If the task is review-specific, route into:
   - `.agents/skills/leanatlas-loop-maintainer-ops/SKILL.md`
   - or `.agents/skills/leanatlas-loop-review-acceleration/SKILL.md`
5) If the task is operator/use-phase only, stay inside `docs/agents/OPERATOR_WORKFLOW.md` and avoid maintainer-only surfaces.
