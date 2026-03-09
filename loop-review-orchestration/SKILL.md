---
name: loop-review-orchestration
description: Compile and execute staged LOOP review bundles through the generic orchestration surface, including default preference-backed closeout planning.
---

## Use when
- You need the reusable staged review-orchestration surface rather than a LeanAtlas-only review wrapper.
- You want to compile a deterministic staged review plan into an executable graph/bundle or run the default preference-backed review path.
- You need to explain or test the generic review-orchestration boundary for another host project.

## Don't use when
- You only need authoritative finding reconciliation; use `.agents/skills/loop-review-reconciliation/SKILL.md`.
- You are doing LeanAtlas-local maintainer provider wiring or onboarding routing; use the `leanatlas-loop-*` skills instead.
- You need a parent supervisor across multiple child waves; use `.agents/skills/loop-batch-supervisor/SKILL.md`.

## Outputs
- A deterministic staged review plan or default review bundle rooted in the generic LOOP orchestration surface.
- A route to the canonical orchestration helpers under `looplib` and `tools/loop/review_orchestration.py`.
- A clear explanation of which stage is authoritative for closeout and how preferences affect the no-escalation path.

## Must-run checks
- `uv run --locked python tests/contract/check_loop_review_strategy.py`
- `uv run --locked python tests/contract/check_loop_review_orchestration.py`
- `uv run --locked python tests/contract/check_loop_review_automation_runtime.py`
- `uv run --locked python tests/contract/check_loop_python_sdk_contract_surface.py`

## Read first
- `docs/setup/LOOP_LIBRARY_QUICKSTART.md`
- `looplib/review.py`
- `tools/loop/review_orchestration.py`

## Steps
1) Use `build_default_review_orchestration_bundle(...)` when you want the committed default path: stored preference artifact, `FAST + low` baseline, and bounded `medium` escalation only when justified.
2) Use `build_review_orchestration_bundle(...)` and `build_review_orchestration_graph(...)` when you need explicit staged-review compilation from a frozen strategy plan.
3) Keep the final integrated closeout stage authoritative; partition scans and deep follow-up rounds are acceleration aids only.
4) For helper-authored no-escalation runs, preserve the explicit LOW closeout path only when deep follow-up is empty; otherwise keep the integrated closeout at `MEDIUM` unless an explicit strict exception plan is justified.
5) Route reconciliation of findings through `.agents/skills/loop-review-reconciliation/SKILL.md` before claiming final closeout authority.
