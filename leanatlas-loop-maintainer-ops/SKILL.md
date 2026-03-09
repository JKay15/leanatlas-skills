---
name: leanatlas-loop-maintainer-ops
description: Maintain and upgrade LOOP provider routing, Python SDK/MCP interfaces, reviewer-history consistency, and LeanAtlas-side wiring around generic review reconciliation in MAINTAINER mode.
---

## Use when
- You are changing LOOP contracts/schemas/tests related to provider routing (`codex exec` or other `agent_provider`) and need deterministic evidence.
- You are upgrading LOOP Python SDK/MCP surfaces and must keep docs/contracts/schemas/tests aligned.
- You are fixing reviewer-history contradiction/nitpick detection, generic review reconciliation wiring, or instruction-scope (`AGENTS.md` chain) visibility guarantees.

## Don't use when
- You are proving a single theorem/problem in OPERATOR mode with no system-contract changes.
- You only need a small wording tweak in docs without any contract/schema/test impact.

## Outputs
- Updated LOOP contracts/schemas/tests with replayable verification (`core` and `nightly` pass)
- Maintainer-facing decision notes in the active ExecPlan
- LeanAtlas wrapper docs/tests kept in sync with generic LOOP semantics such as the root supervisor kernel mapping and root-issued exception artifacts

## Must-run checks
- `uv run --locked python tests/contract/check_loop_contract_docs.py`
- `uv run --locked python tests/contract/check_loop_schema_validity.py`
- `uv run --locked python tests/contract/check_loop_wave_execution_policy.py`
- `uv run --locked python tests/contract/check_loop_python_sdk_contract_surface.py`
- `uv run --locked python tests/contract/check_skills_standard_headers.py`
- `uv run --locked python tests/run.py --profile core`
- `uv run --locked python tests/run.py --profile nightly`

## Preconditions
- MAINTAINER mode is active (root `AGENTS.override.md` local file enabled).
- Active ExecPlan exists under `docs/agents/execplans/`.

## Steps
1) Read LOOP contracts and schemas first:
   - `docs/contracts/LOOP_WAVE_EXECUTION_CONTRACT.md`
   - `docs/contracts/LOOP_MCP_CONTRACT.md`
   - `docs/contracts/LOOP_PYTHON_SDK_CONTRACT.md`
   - `docs/schemas/WaveExecutionLoopRun.schema.json`
   - `docs/schemas/LoopSDKCallContract.schema.json`
2) Apply TDD: update contract checks/fixtures first, then implementation docs/schema.
3) Ensure provider routing evidence is explicit (`agent_provider_id`, resolved invocation, instruction scope refs).
4) Ensure reviewer-history evidence is append-only and re-fed to later rounds (`history_context_refs`, contradiction/nitpick summary).
5) If the task specifically concerns authoritative finding settlement, route the core semantics through `.agents/skills/loop-review-reconciliation/SKILL.md` and keep this skill focused on LeanAtlas-local docs/tests/workflow wiring.
6) Run required checks and record outcomes in ExecPlan decision log.
