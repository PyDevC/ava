# Backend / infra engineer

The **general-software baseline** underneath everything in this repo — the role most of the systems work in here converges on. Not ML-specific: it's the engineering that keeps services and pipelines running.

## Core skills

- **The language set**: a systems language (C++/Go/Rust) + a scripting language (Python) — the C++/Python combo here is directly it ([[Programming/cpp/PLAN]], [[Programming/python/PLAN]]).
- **Systems fundamentals**: Linux (processes, filesystems, networking), databases (SQL, key-value, indexing), distributed systems basics (consistency, partitions, retries, idempotency), caching.
- **Concurrency**: threads, async ([[Programming/python/asyncio]]), message queues, the mental model of "shared mutable state is the bug".
- **Build + deploy**: CMake/Docker/Kubernetes/CI — [[Programming/cpp/cpp-cmake]], [[Programming/python/packaging]], and the OSS workflow ([[OpenSource/PLAN]]).
- **Reliability**: logging, metrics, tracing, alerting; the debugging discipline in [[Programming/cpp/cpp-debugging]] and [[OpenSource/debugging-CI-failures]].

## A typical day

Debug a slow endpoint (profile → cache/query fix), review a PR that touches the build, add monitoring, fix a deployment, design a small service. The throughline: *make systems observable and correct under load*.

## The roadmap

1. **The basics**: Linux, shell, git (beyond basics — rebase, bisect, worktrees), SQL, networking.
2. **The languages**: Python deeply ([[Programming/python/core/PLAN]]) then a systems language (the [[Programming/cpp/PLAN]] path).
3. **Systems**: distributed-systems principles, databases, caches, queues; the "what happens when a machine dies" thinking.
4. **Production**: deployment, CI/CD, observability, on-call discipline.

## How this repo maps

- [[Programming/PLAN]] — the C++/Python/shell foundations.
- [[Compilers/MLIR/CMake_Guide]] + [[Programming/cpp/cpp-cmake]] — build systems.
- [[OpenSource/PLAN]] — PR/CI/review etiquette is the daily work.
- [[hpc-engineer]] — the high-performance extreme of this role.

## Related

- [[mlops-engineer]] — ML's version of this role.
- [[devops-engineer]] — the infra/ops variant.
- [[hpc-engineer]] — scale/systems depth.
- [[ai-framework-engineer]] — when the "system" is a DL framework.
