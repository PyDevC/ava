# The GIL and threading vs multiprocessing

The GIL (Global Interpreter Lock) makes CPython's *bytecode interpreter* single-threaded for Python code — one thread runs Python bytecode at a time. It's the #1 production-Python gotcha, and it's why "use threads" isn't the answer to "make my Python faster".

## What the GIL protects

CPython's memory model (refcounting, see [python-memory](python-memory.md)) is not thread-safe. The GIL serializes bytecode execution so refcounts/allocations stay consistent without locks everywhere. Consequences:

- **Pure-Python code does NOT parallelize with threads** — you get concurrency (interleaving), not parallelism.
- **I/O and C-extension code DO parallelize**: `time.sleep`, socket/network reads, and C functions that *release the GIL* (`Py_BEGIN_ALLOW_THREADS`) run while another thread takes the GIL. That's why "threads for I/O, processes for compute" is the rule.

## Threads vs processes — the decision

| | Threads | multiprocessing / subprocess |
|---|---|---|
| Parallelism for Python code | no (GIL) | **yes** (separate interpreters) |
| Shared memory | yes (objects, but lock everything) | no (pickle / shared-mem / files) |
| Overhead | low | high (spawn + IPC) |
| Use for | I/O-bound, waiting, coordinating | CPU-bound, heavy compute |

- **Practical hybrid**: threads for I/O/queue-worker orchestration; processes for the actual CPU work. The GIL release in numpy/torch kernels means *many* PyTorch workloads can use threads fine — the kernel runs without the GIL; only the Python glue is serialized.
- **3.13 free-threading**: a build option removing the GIL (`--disable-gil`/`free-threaded`). Same language, but mutable-shared-state code needs real synchronization now — the "GIL gives you safety" era is ending gradually (see the plan's note in [PLAN](../../)).

## The DataLoader connection

`num_workers>0` in PyTorch is *processes*, precisely because data transforms are Python and must run off the main thread (see [Performance](../../../PyTorch/Performance.md)). `pin_memory` and the transfer threads coordinate with the main loop without hitting the GIL on the hot path.

## Related

- [python-memory](python-memory.md) — the refcounting the GIL protects.
- [python-performance](python-performance.md) — when Python is the actual bottleneck.
- [Performance](../../../PyTorch/Performance.md) — the practical DataLoader pattern.
- [python-c-api](python-c-api.md) — `Py_BEGIN_ALLOW_THREADS`, the release mechanism.
