# C++ concurrency: threads, atomics, and lock-free

The C++ side of "make it parallel": `std::thread`, mutexes, and `std::atomic`. Python devs get a false sense of safety from the GIL (see [gil-threading](../python/core/gil-threading.md)); C++ gives you *real* parallelism and *real* footguns — every rule here exists because the alternative is a data race.

## Threads

- `std::thread t(f, args...);` starts `f` on a new thread. `t.join()` waits for it (the RAII-friendly way — the destructor of a *joinable* thread calls `std::terminate`, so always join or detach). `t.detach()` lets it run free — only safe if the function outlives everything it touches (threads + lambdas capturing stack refs = use-after-free).
- Compare with Python: `threading.Thread` (see [gil-threading](../python/core/gil-threading.md)) — same shape, but no GIL here, so CPU-bound C++ *does* parallelize.
- `std::async` is a thin wrapper that manages a thread pool for you; the C++ threading model still treats each `thread` as a real OS thread.

## Mutexes and locks

- `std::mutex` + `std::lock_guard<std::mutex>` (RAII lock, see [cpp-memory-model](cpp-memory-model.md)): lock scope = mutex held. `std::unique_lock` if you need to unlock early or condition-variable waiting.
- `std::condition_variable` for wait/notify patterns (producer-consumer). **Always** wait on a predicate, never just `wait()` — spurious wakeups are real.
- **The data race**: two threads accessing the *same non-atomic* object where at least one writes is UB — the compiler and CPU are free to reorder/keep stale values. Locks prevent it; atomics prevent it; "it happened to work" is not preventing it.

## std::atomic and memory_order

- `std::atomic<T>` (integral, pointer, or your own type if trivially copyable): every op is an atomic RMW. Default `memory_order_seq_cst` — the strongest, gives you one total order, but the most expensive on weak-hardware CPUs.
- **The memory_order ladder** (see [cpp-memory-model](cpp-memory-model.md) for why orderings even exist): `seq_cst` (safe default) → `acq_rel`/`acquire`/`release` (lock-free queue protocol: acquire pairs with release, no reordering past them) → `relaxed` (only atomicity, no ordering — fine for counters). Start with `seq_cst`, downgrade only with a proof, not a hunch.
- **Don't hand-roll**: `std::atomic_flag` for spinlocks, `std::mutex` for anything more. The practical rules: use `atomic` for flags/counters and lock-free queue handoff, `mutex` for actual shared data.

## Lock-free data structures — the hard part

Lock-free = no thread ever blocks; progress guaranteed by atomics + CAS loops. Why it's hard:

- **CAS loops are tricky to get correct** (`compare_exchange_strong` retry loops, ABA problem below) and the memory-ordering burden is entirely on you.
- **ABA problem**: thread A reads pointer `X`; thread B pops `X` and pushes `X` again (different object, same address); A's CAS succeeds against the *wrong* object. Fixes: version counters, hazard pointers, epoch reclamation — each a rabbit hole.
- **Memory reclamation**: who frees a node a lock-free consumer just read? The classic reasons `std::mutex` is still the default — lock-free only wins when contention is real and you've measured.

## Why it matters

Every parallel codebase you'll read (PyTorch's caching allocator, IREE's runtime, NCCL/RCCL, see [Distributed-Collectives](../../PyTorch/Distributed-Collectives.md)) is threads + atomics + a couple of mutexes. Understanding the model means reading those as "obvious", and TSAN (see [cpp-debugging](cpp-debugging.md)) finds the races you'll write on the first try.

## Related

- [cpp-memory-model](cpp-memory-model.md) — why orderings and races exist.
- [cpp-memory-layout](cpp-memory-layout.md) — false sharing, the layout/threads intersection.
- [cpp-debugging](cpp-debugging.md) — TSAN/heli-grind for finding races.
- [gil-threading](../python/core/gil-threading.md) — the Python counterpart: threads that *don't* parallelize.
- [Distributed-Collectives](../../PyTorch/Distributed-Collectives.md) — concurrency at cluster scale.
