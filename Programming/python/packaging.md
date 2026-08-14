# Python packaging

How a Python environment actually works — `pip`/`uv`, `pyproject.toml`, wheels, virtualenvs. This is the layer that makes "it works in my venv, fails in prod" happen, and it's the ground truth under every `pip install torch`.

## The pieces

- **Virtualenv**: an isolated `bin/` + `site-packages/` + a `python` that points at a specific interpreter (see [[frames-namespaces]] for what `sys.path` actually does). `uv venv`/`python -m venv` create them; `conda` is a heavier environment manager (also handles non-Python deps).
- **pip/uv**: dependency resolvers. `uv` is the fast modern replacement (Rust, same wheels).
- **`pyproject.toml`**: the modern single metadata file — `[project]` (name, deps, optional-deps), `[build-system]` (hatchling/setuptools), `[tool.*]` (ruff/mypy/pytest config). Replaces `setup.py`/`setup.cfg`/`requirements.txt` for *packages*.
- **Requirements files**: `requirements.txt` pins the *environment* (transitive deps); `pyproject` pins the *package* contract. Don't mix the two roles.

## Wheels: why installs are fast

- A **wheel** (`.whl`) is a *pre-built* bundle (compiled extensions included, see [[python-c-api]]) — pip just unzips it. A **sdist** (`.tar.gz`) requires building on your machine.
- **Platform tags**: `torch-2.5.0-cp312-cp312-manylinux_2_28_x86_64.whl` — Python version (cp312), ABI, OS/musl tag, arch. The tag *is* the compatibility contract; "unsupported platform" errors = tag mismatch (e.g. CUDA vs CPU torch wheels differ by the `+cu121`/`+cpu` suffix).
- **`pip install --no-cache-dir`** only fixes disk, not resolution.

## The classic failure modes

- **Environment drift**: prod has older transitive deps → pin with a lockfile (`uv lock`, `pip-compile`), not a floating `requirements.txt`.
- **Extraneous Python mismatch**: `pip install --python 3.10` vs the `python` on `PATH` — venv confusion. Always activate first (`source .venv/bin/activate`).
- **Source vs wheel**: missing a compiled dep → pip falls back to building the sdist → needs a compiler + headers. Prefer wheels; check `--only-binary=:all:` for repro.
- **GPU vs CPU torch**: the CUDA wheel is a *different package* (`+cu121`); installing the CPU wheel on a GPU box silently gives slow CPU torch. This bites constantly (see [[PyTorch/Performance]]).

## The modern stack

`uv` handles venv+lock+install in one: `uv init` → `uv add torch` → `uv sync`. Reproducible, fast, and the de-facto 2025+ tool. pip+venv+pyproject still work and still matter for CI that pins pip.

## Related

- [[python-c-api]] — why wheels contain compiled extensions.
- [[frames-namespaces]] — what `sys.path`/import resolution is.
- [[OpenSource/PR-Lifecycle]] — packaging changes get reviewed as code.
- [[Compilers/MLIR/CMake_Guide]] — building the compiled parts.
