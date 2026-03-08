---
name: loop-review-reconciliation
description: Use the LOOP Review supersession / reconciliation runtime to settle multi-round or multi-partition findings into an authoritative ledger with CONFIRMED, DISMISSED, and SUPERSEDED dispositions.
---

## Use when
- You need the LOOP Review supersession / reconciliation runtime for authoritative finding settlement.
- You need to reconcile findings across multiple review rounds, partitions, or integrated closeout passes.
- You need to determine whether a finding is `CONFIRMED`, `DISMISSED`, or `SUPERSEDED` using deterministic LOOP evidence rather than free-form reviewer text.

## Don't use when
- You are only planning review scope partitioning or staged narrowing and do not yet need authoritative finding settlement.
- You are doing a LeanAtlas-specific maintainer workflow change that does not touch review reconciliation semantics.
- You are solving a one-off operator problem with no multi-round review evidence to reconcile.

## Outputs
- An authoritative reconciliation artifact that validates against `docs/schemas/ReviewSupersessionReconciliation.schema.json`
- Deterministic settlement of finding occurrences as `CONFIRMED`, `DISMISSED`, or `SUPERSEDED`
- Append-only reconciliation persistence artifacts suitable for replay/audit

## Must-run checks
- `uv run --locked python tests/contract/check_loop_review_reconciliation_runtime.py`
- `uv run --locked python tests/contract/check_loop_review_reconciliation_skill_integration.py`
- `uv run --locked python tests/contract/check_skills_standard_headers.py`

## Read first
- `docs/contracts/LOOP_WAVE_EXECUTION_CONTRACT.md`
- `docs/contracts/LOOP_PYTHON_SDK_CONTRACT.md`
- `docs/schemas/ReviewSupersessionReconciliation.schema.json`
- `tools/loop/review_reconciliation.py`

## Steps
1) Confirm that the task truly needs authoritative finding settlement rather than simple review planning.
2) Read the reconciliation contract surface and identify the key evidence dimensions:
   - `finding_key`
   - `finding_group_key`
   - `scope_lineage_key`
   - authoritative integrated closeout round
3) Build or inspect the review-round evidence set that will feed reconciliation.
4) Use the reconciliation runtime to settle occurrences into `CONFIRMED`, `DISMISSED`, or `SUPERSEDED`.
5) Validate the resulting artifact with `assert_review_reconciliation_ready(...)` and, when persistence is needed, materialize the immutable ledger plus append-only journal via `persist_review_reconciliation(...)`.
6) Ensure later closeout logic consumes the reconciled authoritative finding set rather than raw advisory findings.
