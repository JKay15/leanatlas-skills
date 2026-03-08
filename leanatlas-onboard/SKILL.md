---
name: leanatlas-onboard
description: "First-run onboarding: print the LeanAtlas onboarding visual card, ask for consent, initialize env + contracts + core tests, and generate Codex App automation prompts."
---

## What this skill is

This skill turns a fresh clone into a usable workspace **without asking the human to run commands**.

It is intentionally designed to feel like a product onboarding:

1) show a visual hero + info card,
2) explain what will happen,
3) ask for consent,
4) run setup + verify,
5) record a local done-state.

## Use when

- The user just cloned the repo and this is the first Codex prompt (including a plain greeting like `hi`).
- The user says “initialize”, “bootstrap”, “setup”, or “make it ready”.
- The onboarding state file is missing/outdated, or `operational_ready` is not true.

## Don't use when

- The user is in OPERATOR mode proving a single problem and explicitly wants *no framework actions*.
- The user says “skip setup”.

## Outputs

- Onboarding summary with selected option (A/B/C) and preflight result.
- Mandatory post-onboarding automation install checklist generated from `docs/agents/AUTOMATIONS.md` + `automations/registry.json`.
- After `bootstrap` + `doctor` + `real_agent_cmd` all pass: `.cache/leanatlas/onboarding/state.json` with environment completion.
- On operational readiness: same state file with `steps.automations=ok` and `operational_ready=true`.
- Post-onboarding LOOP defaults use the reserved local path `.cache/leanatlas/onboarding/loop_preferences.json` when explicitly staged.
- After `bootstrap` + `doctor` + `real_agent_cmd` all pass: compacted root `AGENTS.md` onboarding block.

## Must-run checks

- Preflight checks: `test -x ./.venv/bin/python`, deps import, and `lake --version`.
- Verification gate for setup path: `./.venv/bin/python tests/run.py --profile core`.

## Banner (locale-aware, print on first-run)

Default visual (from `docs/agents/BRANDING.md`):

```text
+------------------------------------------------------------------------------+
| LEANATLAS :: Powered by LeanAtlas                                            |
+------------------------------------------------------------------------------+
| [i] Welcome to LeanAtlas                                                     |
| Choose: A) Full init (Recommended)  B) Python-only  C) Skip                  |
| Operational gate: install/verify active automations before normal tasks.     |
+------------------------------------------------------------------------------+
```

Locale-aware rule:

- `docs/agents/BRANDING.md`
- `docs/agents/locales/zh-CN/ONBOARDING_BANNER.md`

Routing:
- If the user prompt is Chinese/CJK, render the zh-CN onboarding visual from the locale asset.
- Otherwise, render the default onboarding visual from `docs/agents/BRANDING.md`.

## Consent gate (hard rule)

Before running *any* networked install or writing setup state, ask the user to choose:

**A) Full maintainer initialization (recommended)**
- Execute `INIT_FOR_CODEX.md` (Steps 0–7, plus optional Step 8).

**B) Python-only setup (safe + fast)**
- Ensure `uv` exists.
- Create/sync `.venv` via `uv sync --locked`.
- Still run the promised repo-local onboarding gates:
  - ensure Repo-B skills are mounted (`.agents/skills/**`)
  - install repo hooks via `bash scripts/install_repo_git_hooks.sh`
  - run Lean warmup verification (`importGraph` check + `lake build LeanAtlas` + `lake lint`)
  - run core contracts: `.venv/bin/python tests/run.py --profile core`

**C) Skip (do nothing)**
- Continue with the user’s task.

If the user does not clearly choose A/B/C, do not proceed.

## First-run detection

Treat onboarding as required when this file is missing/outdated, or when automation readiness is missing:

- `.cache/leanatlas/onboarding/state.json`
- required operational gate: `steps.automations == "ok"` and `operational_ready == true`
- post-onboarding LOOP defaults use the reserved path `.cache/leanatlas/onboarding/loop_preferences.json` when explicitly staged

This path is gitignored.

## Preflight before any install/update (hard rule)

Before running A or B, perform local readiness checks:

1) `test -x ./.venv/bin/python`
2) `./.venv/bin/python -c "import yaml, jsonschema; print('deps-ok')"`
3) `lake --version`

If all pass:

- report that the local environment is already satisfied,
- skip redundant install/update steps (`uv sync --locked`, `lake update`),
- continue with verification gates and state write.

## Execution (A/B)

### A) Full maintainer init

Follow `INIT_FOR_CODEX.md` as an executable checklist.

Hard requirements:

- Prefer `./.venv/bin/python` once it exists.
- If preflight passes, skip redundant install/update commands and continue with verification steps.
- Fix root causes (no retry/jitter hacks).
- Keep diffs minimal.
- Any produced artifacts must land under `artifacts/**`.

### B) Python-only

1) Verify `uv`:
   - `uv --version`
2) If preflight failed, sync venv:
   - `uv sync --locked`
3) Verify critical imports (always):
   - `./.venv/bin/python -c "import yaml, jsonschema; print('deps-ok')"`
4) Ensure Repo-B skills are mounted (`.agents/skills/**`); if they are missing, initialize the skill subtree before continuing.
5) Install repo-local git hooks:
   - `bash scripts/install_repo_git_hooks.sh`
6) Run Lean warmup verification:
   - `test -d ./.lake/packages/importGraph`
   - `lake build LeanAtlas`
   - `lake lint`
7) Run core contracts:
   - `./.venv/bin/python tests/run.py --profile core`

## Codex App automations (required operational gate after environment setup)

After A or B succeeds, Codex must drive installation via the checklist and block normal task work until verified:

- Read: `docs/agents/AUTOMATIONS.md` and `automations/registry.json`.
- Use install template: `docs/agents/templates/AUTOMATION_INSTALL_CHECKLIST.md`.
- Generate one install block per active automation id (do not ask the user to author prompts manually).
- Present blocks in checklist order and ask for a short per-item confirmation.
- Require one manual trigger per automation and then verify evidence:
  - `./.venv/bin/python tools/onboarding/verify_automation_install.py --mark-done`

Important:

- Automations are configured in the Codex App UI.
- Codex should not claim it can silently install them without user confirmation.
- If the automation gate is not completed, reply with install/verification instructions only.

## Post-onboarding LOOP defaults

Once the environment is operational-ready, explain/stage LOOP defaults as a post-onboarding preference step, not as a bootstrap blocker.

Supported presets:
- `Budget Saver`
  - current default reviewer path: `FAST + low`
  - current default reviewer tier policy: `LOW_PLUS_MEDIUM`
- `Auditable`
 - `Balanced`

Important:
- `LOW_PLUS_MEDIUM` is the committed default reviewer tier policy.
- `medium` is the standard bounded escalation tier.
- Do not imply that onboarding already auto-writes or auto-applies these defaults unless that wiring has landed.
- `medium` is a bounded escalation only for small-scope high-risk core logic.
- `STRICT / xhigh` is exceptional and should not be presented as the normal default.

## Done state

If A or B completes its local setup and verification gates, update:

- `.cache/leanatlas/onboarding/state.json`

Use the minimal schema defined in `docs/agents/ONBOARDING.md`.

Record the bootstrap/doctor progress first:

- `./.venv/bin/python tools/onboarding/finalize_onboarding.py --step bootstrap`
- `./.venv/bin/python tools/onboarding/finalize_onboarding.py --step doctor`

Environment completion is stricter:
- do not mark the environment complete before `bootstrap` + `doctor` + `real_agent_cmd` all pass
- do not compact the root `AGENTS.md` onboarding block before `real_agent_cmd` passes
- when real-agent configuration succeeds, also run:
  - `./.venv/bin/python tools/onboarding/finalize_onboarding.py --step real_agent_cmd`

Expected result after `bootstrap` + `doctor` + `real_agent_cmd` all pass:
- root `AGENTS.md` onboarding section is compacted
- verbose copy remains in `docs/agents/archive/AGENTS_ONBOARDING_VERBOSE.md`
- environment completion can be true while operational readiness is still blocked until the automations step passes

## Deliverables (what to report back)

- Which option (A/B/C) was selected.
- Preflight result (ready/not-ready) and why.
- Which steps were skipped because requirements were already satisfied.
- The key verification outputs (short excerpts only).
- Where the onboarding state was written.
- Which active automations must be installed next, with install order.
- Whether onboarding is only environment-complete or fully operational-ready.
- Any remaining TODOs (file paths).
