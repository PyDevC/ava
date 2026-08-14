# Contributing to PyTorch: the checklist

The practical path from "I want to help" to "my PR is green and reviewed". PyTorch's contribution flow is bigger than most (huge test matrix, flaky tests, bots) but the shape is standard OSS — see [PR-Lifecycle](../OpenSource/PR-Lifecycle.md) for the generic flow and [writing-a-good-commit](../OpenSource/writing-a-good-commit.md) for the commit side.

## Pick the right issue

- Start with **good-first-issue** and **low-priority** labels; op implementations, tests, and docs are the classic entry points. Read the issue fully *and* the module's CONTRIBUTING notes before claiming anything.
- Comment "taking this" and reference the issue in the PR body — maintainers expect the link (see [PR-Lifecycle](../OpenSource/PR-Lifecycle.md)).
- Small and correct beats ambitious and stalled: a merged 50-line test PR teaches the CI flow better than a three-month op rewrite.

## Get the build + a passing baseline first

- Before writing a line, build from source (see [Build-From-Source](Build-From-Source.md)) and run the test file you'll touch to confirm your baseline passes. Nothing is worse than a red PR that's red *before* your change.

## Tests: how PyTorch tests run

- Tests live in `test/` (`test/test_*.py`), driven by pytest: `pytest test/test_ops.py -k "add"` runs the matching subset. CI runs the whole matrix (CPU/CUDA/ROCm, plus lint) via the workflows in [CI_Infra](CI_Infra.md).
- **Write the test that proves the fix**: a regression test for the bug, placed in the module's existing test file, following the file's conventions (see [code-review-skills](../OpenSource/code-review-skills.md) — missing/weak tests are the #1 review complaint).
- New ops need tests in the right generated suites; a custom op needs `torch.library` coverage (see [Custom-Ops](Custom-Ops.md)).

## Linters and typing

- Run **lintrunner** (`lintrunner` / `lintrunner -a` for all files) — PyTorch's own pre-commit equivalent — before pushing; CI enforces it (see [python-tooling](../Programming/python/python-tooling.md) for the underlying ruff/mypy stack).
- Type hints matter in `torch/` and `test/`; run `mypy` on the files you touched when the CI config demands it.

## CI etiquette

- CI on your PR runs the full matrix automatically. **Read the failures yourself before re-triggering** — a flaky test is different from a real one.
- **Flaky tests**: if a failure is unrelated (a known-flaky test, a machine/network issue), say so in the PR thread with a link; maintainers `@pytorchbot rerun` it. Don't spam reruns — the bots track them.
- Keep the PR **small and focused**: one logical change, one PR. `@pytorchbot` handles merges once approvals are in; reverts are done by the same bot, so a fast, clean, green PR is what keeps trunk healthy.

## The submission sequence

1. Build + baseline tests pass locally.
2. Make the change; add the regression test in the right `test/` file.
3. `lintrunner` clean; mypy clean on touched files.
4. Commit with a good message (see [writing-a-good-commit](../OpenSource/writing-a-good-commit.md)), push a branch.
5. Open the PR linking the issue, fill the template, watch CI, fix/rerun as needed.

## Why it matters

This checklist *is* the PyTorch contribution experience — most first PRs fail on step 2 (weak tests) or step 5 (CI etiquette), not on the code. Following it means your first merged PR is fast and painless, and it's the same shape as every other OSS contribution (see [code-review-skills](../OpenSource/code-review-skills.md)).

## Related

- [Build-From-Source](Build-From-Source.md) — the mandatory first step.
- [CI_Infra](CI_Infra.md) — what CI runs and how to read failures.
- [Custom-Ops](Custom-Ops.md) — the "write a custom op" contribution path.
- [Dispatch-Key](Dispatch-Key.md) — for dispatch-bug fixes.
- [pytorch-developers](../Roadmaps/pytorch-developers.md) — the role roadmap with PR-sized tasks.
