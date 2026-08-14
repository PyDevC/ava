# Programming/python — Plan

## Goal

The **Python language area** of the knowledge base. Currently contains only `core/`; language-adjacent topics (tooling, packaging, performance) can live here as they grow.

## What's here

- [[core]] — Python internals: bytecode, typechecking, subclass conditioning (see [[core/PLAN]]).
- [[packaging]] — pip/pyproject/uv, wheels, virtualenvs.
- [[asyncio]] — the concurrency model beyond threads.

## What to add next (see also [[../PLAN]])

- [x] Packaging: pip/pyproject/uv, wheels, virtualenvs — how a Python env actually works
- [x] `asyncio` — the concurrency model beyond threads (GIL note in `core`)
- [ ] Common ecosystem tooling: linters (ruff), formatters, test runners (pytest)
- [ ] (C++/other languages would be new sibling sub-directories, per [[../PLAN]])
