---
name: leanatlas-dedup
description: Run or maintain LeanAtlas DedupGate V0. Use when you need a DedupReport from the current source-backed instance scan, or when you need to tighten DedupGate reporting/allowlists without overclaiming compiled-environment support.
---

## Use when
- You need the current DedupGate V0 report from the source-backed instance scan in `tools/dedup/dedup.py`.
- You are tightening DedupGate reporting, allowlist, or Phase3 documentation around the implemented V0 surface.

## Don't use when
- You are trying to "rename" or "move" declarations as a fix (DedupGate is a scanner; remediation happens in Promotion/GC workflows).
- You need compiled-environment duplicate facts or binder-canonicalization guarantees; that scanner is not implemented yet.

## Outputs
- `.cache/leanatlas/dedup/scan/DedupReport.json`
- `.cache/leanatlas/dedup/scan/DedupReport.md`

## Must-run checks
- `uv run --locked python tools/dedup/dedup.py --repo-root . --out-root .cache/leanatlas/dedup/scan`
- `uv run --locked python tests/run.py --profile core`

# LeanAtlas DedupGate (Phase3)

This skill is for **DedupGate** work: finding duplicates that would make the library confusing or unstable.

## Hard constraints

- **Do not mutate the repo.** DedupGate is a scanner.
- **Current V0 implementation is the source-backed Python scan in `tools/dedup/dedup.py`.**
- **Compiled-environment DedupGate scanning is follow-on work, not current behavior.**
- **Always write a report.** The output must include `DedupReport.json` and `DedupReport.md`.
- If you add/modify a command, artifact, or schema, update `tools/capabilities/phase3.yaml` in the same change.

## Read first (source of truth)

- `docs/schemas/DedupReport.schema.json`
- `tools/capabilities/phase3.yaml`
- ExecPlan: `docs/agents/execplans/phase3_dedup_gate_v0.md`

## Standard workflow

1) **Run DedupGate**

```bash
uv run --locked python tools/dedup/dedup.py \
  --repo-root . \
  --out-root .cache/leanatlas/dedup/scan
```

2) **Inspect outputs**

- `.cache/leanatlas/dedup/scan/DedupReport.json`
- `.cache/leanatlas/dedup/scan/DedupReport.md`

3) **If you need capabilities beyond the current V0**

- Start from a new ExecPlan instead of describing future compiled-environment scanning as already landed.
- Keep V0 focused on source-backed detection of “hard duplicates” in **typeclass instances**.
- If PromotionGate consumes Dedup evidence, make the evidence path explicit in the report/artifact surface rather than relying on aspirational wording.
