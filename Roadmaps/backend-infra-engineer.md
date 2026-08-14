# Backend / infra engineer

The **general-software baseline** underneath everything in this repo — the role most of the systems work in here converges on. Not ML-specific: it's the engineering that keeps services and pipelines running.

## Core skills

- **The language set**: a systems language (C++/Go/Rust) + a scripting language (Python) — the C++/Python combo here is directly it ([PLAN](../Programming/cpp/PLAN.md), [PLAN](../Programming/python/PLAN.md)).
- **Systems fundamentals**: Linux (processes, filesystems, networking), databases (SQL, key-value, indexing), distributed systems basics (consistency, partitions, retries, idempotency), caching.
- **Concurrency**: threads, async ([asyncio](../Programming/python/asyncio.md)), message queues, the mental model of "shared mutable state is the bug".
- **Build + deploy**: CMake/Docker/Kubernetes/CI — [cpp-cmake](../Programming/cpp/cpp-cmake.md), [packaging](../Programming/python/packaging.md), and the OSS workflow ([PLAN](../OpenSource/PLAN.md)).
- **Reliability**: logging, metrics, tracing, alerting; the debugging discipline in [cpp-debugging](../Programming/cpp/cpp-debugging.md) and [debugging-CI-failures](../OpenSource/debugging-CI-failures.md).

## A typical day

Debug a slow endpoint (profile → cache/query fix), review a PR that touches the build, add monitoring, fix a deployment, design a small service. The throughline: *make systems observable and correct under load*.

## The roadmap

1. **The basics**: Linux, shell, git (beyond basics — rebase, bisect, worktrees), SQL, networking.
2. **The languages**: Python deeply ([PLAN](../Programming/python/core/PLAN.md)) then a systems language (the [PLAN](../Programming/cpp/PLAN.md) path).
3. **Systems**: distributed-systems principles, databases, caches, queues; the "what happens when a machine dies" thinking.
4. **Production**: deployment, CI/CD, observability, on-call discipline.

## How this repo maps

- [PLAN](../Programming/PLAN.md) — the C++/Python/shell foundations.
- [CMake_Guide](../Compilers/MLIR/CMake_Guide.md) + [cpp-cmake](../Programming/cpp/cpp-cmake.md) — build systems.
- [PLAN](../OpenSource/PLAN.md) — PR/CI/review etiquette is the daily work.
- [hpc-engineer](hpc-engineer.md) — the high-performance extreme of this role.

## Related

- [mlops-engineer](mlops-engineer.md) — ML's version of this role.
- [devops-engineer](devops-engineer.md) — the infra/ops variant.
- [hpc-engineer](hpc-engineer.md) — scale/systems depth.
- [ai-framework-engineer](ai-framework-engineer.md) — when the "system" is a DL framework.
