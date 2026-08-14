# Debugging CI failures

A red CI on your PR is normal — the skill is reading the *right* log quickly. Big-project CI (PyTorch, LLVM, ROCm) runs a matrix of jobs; your job is to find *your* failure, not the 40 unrelated ones (see [CI-Infra](../PyTorch/CI-Infra.md) for a real runner map).

## Reading the matrix

- Every repo's CI runs **many jobs** (per-OS, per-build-type, per-GPU, lint+test, coverage). The PR check list shows *which* failed. A job that fails in the **setup/compile** phase is a build problem; a job that compiles then fails in a **test** is a code problem. They have different debug paths.
- **Infra failures ≠ your failure**: "runner died", "timeout", "connection reset", flaky-known tests — check the failure *category* before touching your code. Big repos mark flaky jobs (`flaky:` prefix, rerun via the merge bot).

## The debug order

1. **Find the first error, not the last**: scroll to the first `FAILED`/`error:`/`AssertionError`, then look at the ~30 lines *before* it (the traceback root). Log tail after the first error is usually cascade.
2. **The classic three**:
   - **Lint/format/typing failures** — `clang-format`, `ruff`, `mypy`, pre-commit. Fastest: run the exact local command from the CI config (`git diff --check`, project's lint script) and fix.
   - **Build failures** — missing include, CMake misconfig, codegen mismatch. For MLIR/PyTorch C++: check the *TableGen/codegen* includes (see [CMake-Guide](../Compilers/MLIR/CMake-Guide.md)), and that generated `.inc` files are regenerated.
   - **Test failures** — a `pytest`/`unittest`/CTest failure: run *that test* locally with the same args/flags (`pytest path::test -k ...`), reproduce, fix, rerun.
3. **Flaky/ordering**: tests that pass locally but fail on CI → test *ordering* dependency, resource contention (GPU OOM), or nondeterminism (seeds, see [tuning-methodology](../MachineLearning/automl/hyperparameter-tuning/tuning-methodology.md) for seed discipline).

## Tooling that saves hours

- `TORCH_LOGS` / `--mlir-print-ir-after-failure` / `gdb` stack traces in the CI log — the project-specific debug flags (see [Explore](../PyTorch/Explore.md) and [DebugWithTorchDynamo](../Compilers/PyTorchCompiler/TorchDynamo/DebugWithTorchDynamo.md)).
- **Minimize**: reduce the failing test to the smallest repro, then bisect (git bisect on your branch for regressions you introduced).
- **Run CI locally first**: the repo's "quick checks"/dev-container scripts mirror the matrix — always cheaper than a CI round-trip.

## The etiquette

- Don't spam "rerun" on a red job before diagnosing — the bots and maintainers notice.
- Post the *first error line* (not a giant log) when asking for help; include the job name and commit.
- When you fixed it: link the rerun (green) so reviewers see the resolution without re-reading the thread.

## Related

- [PR-Lifecycle](PR-Lifecycle.md) — CI is the gate inside the flow.
- [CI-Infra](../PyTorch/CI-Infra.md) — a concrete runner/infra map.
- [Explore](../PyTorch/Explore.md) — TORCH_LOGS and debug flags.
- [writing-a-good-commit](writing-a-good-commit.md) — CI etiquette starts at the commit.
