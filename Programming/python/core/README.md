# Python core internals

Notes on how CPython actually works, from the data model out to the C API. The overview is in `index.md` (Programming → Python); the map of this subtree lives there too.

- Data model & objects: [data-model](data-model.md), [type-system-depth](type-system-depth.md), [conditioning-on-a-python-subclass](conditioning-on-a-python-subclass.md)
- Execution: [frames-namespaces](frames-namespaces.md), [bytecode](bytecode.md)
- Concurrency & memory: [gil-threading](gil-threading.md), [python-memory](python-memory.md), [python-performance](python-performance.md)
- The C side: [python-c-api](python-c-api.md)
- Typing: [typechecking](typechecking.md)

## Related

- [python-performance](python-performance.md) — the practical payoff of knowing this layer.
- [gil-threading](gil-threading.md) — the GIL, and why threads don't parallelize Python.
