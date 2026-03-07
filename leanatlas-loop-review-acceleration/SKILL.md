---
name: leanatlas-loop-review-acceleration
description: Accelerate maintainer AI review by deterministic scope partitioning, staged narrowing, and pyramid reviewer escalation while preserving an integrated final closeout review.
---

## Use when
- You are in MAINTAINER mode and one review scope is large enough that a single `codex exec review` round is too slow or too context-heavy.
- You want to split review into deterministic partitions first, then narrow follow-up review to the high-signal partitions.
- You want to use a pyramid reviewer strategy where low-cost rounds surface obvious issues before an expensive integrated closeout review.

## Don't use when
- The review scope is already very small and a single integrated review is cheaper than partitioning overhead.
- You need true runtime concurrency or multi-worktree reconciliation; this skill is a review-planning aid, not a scheduler or merge manager.

## Outputs
- A deterministic partition list for the review scope.
- A pyramid review plan whose final integrated stage is the only closeout-eligible stage.
- A narrowed effective scope that can be fed back into later deep/strict stages after the fast scan.

## Must-run checks
- `uv run --locked python tests/contract/check_loop_review_strategy.py`
- `uv run --locked python tests/contract/check_loop_contract_docs.py`
- `uv run --locked python tests/contract/check_loop_python_sdk_contract_surface.py`
- `uv run --locked python tests/contract/check_skills_standard_headers.py`

## Preconditions
- MAINTAINER mode is active and an ExecPlan already exists.
- The target review scope is a non-empty file list rooted under the repository.

## Steps
1) Normalize and partition the review scope with `partition_review_scope_paths(...)`.
2) Run a fast partition scan first; treat its findings as provisional, not terminal.
3) Merge and narrow follow-up scope with `merge_partition_scope_paths(...)` using the partitions that produced real findings or manual selections.
4) Rebuild or refine the staged plan with `build_pyramid_review_plan(..., followup_partition_ids=..., effective_scope_paths=...)` once the selected partitions are known.
   If the fast scan finds nothing worth escalating, pass `followup_partition_ids=[]` explicitly so the plan records the no-escalation outcome instead of pretending all partitions remain selected.
5) Keep the final integrated closeout review over the effective main scope; never close out solely from partition-local intermediate rounds.
