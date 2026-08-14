# The C API, pybind11, and extensions

Writing CPython extensions is how you make Python *fast* or bridge to C++ systems (PyTorch's `torch._C` is one giant extension). Two routes: the raw **Python/C API** (`PyObject`*) and **pybind11** (modern C++ bindings). Both integrate with the execution model in [bytecode](bytecode.md) and the memory model in [python-memory](python-memory.md).

## The C API — `PyObject`

Everything in CPython is a `PyObject*` whose `ob_type` describes it. Extensions:

- `Py_INCREF`/`Py_DECREF` — manual refcount management (leaks → crash, see [python-memory](python-memory.md)).
- `PyArg_ParseTuple`/`Py_BuildValue` — args in/out.
- Module init: `PyMODINIT_FUNC PyInit_mymodule(void)` (or `PyInit_mymodule_builtin`). The name *must* match the import.
- The GIL: `Py_BEGIN_ALLOW_THREADS` / `Py_END_ALLOW_THREADS` around long-running C work — the thing that lets an extension actually parallelize (see [gil-threading](gil-threading.md)).
- Build via `setuptools.Extension` / `build_ext`, or `pybind11`/`nanobind` to skip the boilerplate.

## pybind11 — the ergonomic route

```cpp
#include <pybind11/pybind11.h>
namespace py = pybind11;

int add(int a, int b) { return a + b; }

PYBIND11_MODULE(mymod, m) {
    m.def("add", &add, "adds two ints");
}
```

- Automatic conversions (C++ types ↔ Python), ownership rules (`std::unique_ptr`, `std::shared_ptr`, return policies), and it integrates with CMake trivially (`pybind11_add_module`).
- `py::class_<T>(m, "T")` — bind C++ classes with constructors, methods, properties; `py::overload_cast<>` for overloads.
- The deep reason it works: it *is* the C API, generated and type-safe. For anything nontrivial (a real library, like a PyTorch custom op's CPU/CUDA path), pybind11 is the sane choice (see [Custom-Ops](../../../PyTorch/Custom-Ops.md)).

## The interop triangle

- **ctypes** — call shared libs without compiling Python bindings (FFI, ABI-level). Slow-ish (per-call overhead) but zero-build.
- **cffi** — similar, nicer C declarations.
- **Cython** — C-like Python compiling to C; the "old" performance route (older numpy/scipy used it; pybind11/modern C++ mostly replaced it for new code).
- For ML work: the standard is **pybind11 + the existing library** (PyTorch extensions, ROCm/HIP kernels) — the C API only if you're hacking CPython internals or a minimal footprint.

## The compile story

- Distutils/`setuptools.build_ext` needs the interpreter headers (`python3-config --includes`); `pybind11_add_module` handles it.
- Linking against libpython: usually *not* needed (extensions resolve symbols at runtime); `--no-undefined` flags trip people up.
- The debugging connection: a segfault in an extension = a C bug; run under **ASAN/gdb** (see [debugging](../../cpp/cpp-debugging.md) plans) — Python frames in gdb via `py-bt` (Python gdb plugin).

## Related

- [python-memory](python-memory.md) — refcounting you now manage by hand.
- [gil-threading](gil-threading.md) — `Py_BEGIN_ALLOW_THREADS` in practice.
- [bytecode](bytecode.md) — what an extension module's call frame looks like.
- [Custom-Ops](../../../PyTorch/Custom-Ops.md) — the framework-level version of this.
