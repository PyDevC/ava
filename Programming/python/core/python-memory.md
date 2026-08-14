# Python memory: refcounting, GC, weakref

CPython's memory model is **reference counting** (immediate frees) plus a **cycle-detecting garbage collector** for the things refcounting can't handle (reference cycles). Long-running training pipelines die on memory bugs here — leaks in a `@lru_cache`, cycles in model graphs, un-`del`'d references to big tensors.

## Refcounting — the fast path

- Every object has a `Py_ssize_t ob_refcnt`; `INCREF`/`DECREF` on bind, unbind, pass, return. Refcount hits 0 → freed *immediately*, destructors run *then*, not later. This is why Python memory "just works" for the common case and why `del x` actually releases.
- **The cost**: every reference manipulation is a `#ifdef`'d atomic-ish op — a big chunk of the GIL-held work (see [gil-threading](gil-threading.md)).
- Deterministic destruction is why context managers (`with open(...)`) and `finally` matter: they guarantee *when* things happen.

## The cycle collector (GC)

- Refcounting alone leaks **cycles**: `a.b = b; b.a = a` never reach 0. The GC (generational: 3 tiers) periodically finds unreachable *cycles* via a tri-color mark algorithm.
- Practical: cycles are cheap to create accidentally (callbacks, closures in lists, `__del__` + exception tracebacks holding frames).
- `gc.collect()` — force a sweep; `gc.disable()` for speed-critical loops (dangerous: leaks on cycles), `gc.get_objects()` for leak hunting.

## weakref — observing without owning

`weakref.ref(obj)` / `weakref.WeakValueDictionary` / `weakref.finalize` hold a reference that **doesn't count**:

- **WeakValueDictionary**: cache that auto-drops entries when the object dies — the correct cache type (a plain dict cache = a permanent leak).
- **`weakref.finalize`**: run cleanup *when the object is collected* — the modern replacement for `__del__` (predictable, no cycle interaction).
- PyTorch's Tensor: `tensor.data`/`storage` refs, hook registrations (`tensor.register_hook`), and autograd's saved-tensor weakrefs are exactly this pattern (see [Autograd-Internals](../../../PyTorch/Autograd-Internals.md)).

## The leak-detection playbook

1. `gc.gc()` + `objgraph.show_growth()` / `gc.get_objects()` — diff object counts across iterations.
2. Suspicion order: caches, closures, globals, exception tracebacks (`sys.exc_info` holding frames), **tensor storage** (see [Performance](../../../PyTorch/Performance.md) for the allocator, not Python, on GPU).
3. **Weakref everywhere that's a cache**. Del anything a name doesn't need.

## Related

- [gil-threading](gil-threading.md) — the GIL protects this refcount model.
- [frames-namespaces](frames-namespaces.md) — namespaces hold the references.
- [Performance](../../../PyTorch/Performance.md) — GPU memory vs Python memory.
- [python-performance](python-performance.md) — profiling to find the leak first.
