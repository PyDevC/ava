# Python ecosystem tooling

The modern Python toolchain — linters, formatters, type checkers, test runners, pre-commit — is what makes a Python repo feel professional. PyTorch CI runs exactly this stack (see [CI-Infra](../../PyTorch/CI-Infra.md)), so this note doubles as "how to read that CI's checks".

## Linters and formatters: ruff

- **ruff** is the linter of choice in 2026: one binary, written in Rust, replaces flake8/isort/pycodestyle at once. `ruff check .` lints; `ruff format .` formats (Black-compatible by default).
- Config lives in `pyproject.toml` under `[tool.ruff]` — rulesets like `select = ["E", "F", "I", "UP"]` (errors, pyflakes, import sort, pyupgrade). `ruff check --fix` auto-fixes most findings.
- Rule of thumb: format first, lint after, and keep both in CI so style never becomes a review conversation (see [code-review-skills](../../OpenSource/code-review-skills.md)).

## Type checkers: mypy and pyright

- **mypy** — the reference checker, strict-mode capable (`--strict`). **pyright** — the fast one (TypeScript-based), powers the Pylance IDE experience.
- Both read type annotations (see [typechecking](core/typechecking.md) for how the type system works); config in `pyproject.toml` (`[tool.mypy]` / `[tool.pyright]`).
- The value: catching `None`-sneaks and API-shape mistakes before runtime. Start with `--check-untyped-defs`, grow to strict on new files.

## Test runners: pytest

- **Fixtures**: `@pytest.fixture` returns a resource; tests take it as a parameter. `scope="module"`/`"session"` to reuse expensive setup (a model, a GPU tensor).
- **Parametrize**: `@pytest.mark.parametrize("x", [1, 2, 3])` runs the test per value — how PyTorch's test suites generate huge matrices from one function.
- **Marks**: `@pytest.mark.skipif(not torch.cuda.is_available(), ...)`, `pytest.mark.slow`, `pytest.mark.xfail`. Selection: `pytest tests/test_x.py -k "pattern" -m "not slow"`.
- **Coverage**: `pytest --cov=package` with `pytest-cov`; the report shows the lines tests never hit (see [code-review-skills](../../OpenSource/code-review-skills.md) — "missing test" is the #1 review comment).

## pre-commit — the gate

- `pre-commit` runs a fixed set of hooks (ruff, mypy, black) on staged files before every commit, via `.pre-commit-config.yaml`.
- The design: hooks are slow-ish, so it runs them only on changed files; `pre-commit run --all-files` when you want everything. CI tools (PyTorch's `lintrunner`, see [Contributing](../../PyTorch/Contributing.md)) are the same idea enforced server-side.

## Virtualenvs and uv

- **uv** is the modern env/package manager (replaces pip+venv+poetry): `uv venv`, `uv pip install`, `uv run`, and `uv sync` from a `pyproject.toml` — dramatically faster resolution than pip.
- Virtualenvs are the "separate environment per project" contract (see [packaging](packaging.md)); `uv` just makes the ritual painless. Never `pip install` into system Python.

## Why it matters

Every Python repo I'll work in has this exact stack, and the PR flow is the same everywhere: pre-commit locally → CI re-checks → review looks at *design*, not lint, because the tools already enforced style. Master the tools once, and every repo's CI is immediately readable.

## Related

- [typechecking](core/typechecking.md) — what the type checker is actually checking.
- [packaging](packaging.md) — the env/pip/uv layer underneath.
- [CI-Infra](../../PyTorch/CI-Infra.md) — a real CI running this stack.
- [code-review-skills](../../OpenSource/code-review-skills.md) — why the tools exist (to keep reviews about design).
