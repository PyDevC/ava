# Type system depth: Protocol, generics, Literal

The static side of Python typing beyond the basics in [[typechecking]]: **structural typing** (`Protocol`), **generics** (`TypeVar`), and **literal/widening types** — the tools that make type checkers actually useful on real codebases (and on the frameworks like PyTorch).

## Protocol — structural typing

- `typing.Protocol`: a type defined by *attributes/methods*, not inheritance. `def f(x: Sized)`: `Sized` is a Protocol (`__len__`). Any object with `__len__` satisfies it — no `isinstance`/subclass needed.
- The static mirror of the runtime protocol system in [[data-model]]: descriptors/dunders are runtime protocols; `Protocol` makes them *checkable*.
- Use it for duck-typed interfaces (the pytest/fixture-world, `os.PathLike`, "anything that can be called").

## TypeVar and generics

```python
T = TypeVar("T")
def first(xs: list[T]) -> T: ...
```

- `TypeVar` binds the type *relationship* (output type = input element type), with optional bounds (`TypeVar("T", bound=Number)`) or constraints (`TypeVar("T", int, str)`).
- Generic containers: `class Stack(Generic[T])` — type checkers then infer `Stack[int]`.
- Modern syntax: `list[T]` (PEP 585) needs no `typing.List`; `def first[T](...)` (PEP 695) for the modern concise form.

## Literal, widening, and overloads

- `Literal["a", "b"]`: a type that is *exactly* one of a few values — perfect for flag parameters (`mode: Literal["cpu", "cuda"]`).
- **Widening**: `x = 1` infers `int`, not `Literal[1]` — use `Literal` explicitly when you want literal precision.
- `@overload` — multiple signatures for one function body (e.g. `torch.Tensor.__getitem__` typing).

## The runtime/static split

- `typing` (static annotations) vs `types` (runtime structures — `types.MethodType`, `types.SimpleNamespace`). `typing` names mostly *don't exist at runtime* meaningfully — which is why `isinstance(x, list[str])` fails (check `get_args` instead).
- Frameworks (and Dynamo's `VariableTracker`, see [[../../Compilers/PyTorchCompiler/TorchDynamo/VariableTracker]]) distinguish static types (what mypy sees) from runtime types (`__class__`, `type(x)`) — a mismatch is a real bug source (see [[conditioning-on-a-python-subclass]] for the runtime side).

## The practical rules

- Annotate **public function signatures** (args + return) and let inference do the rest.
- Use `Protocol` for interfaces, `TypeVar` for relationships, `Literal` for flags.
- Type checking is a *separate tool* (mypy/pyright) from linting (ruff) — run both in CI (see [[packaging]]/[[OpenSource/debugging-CI-failures]]).

## Related

- [[typechecking]] — the basics this extends.
- [[data-model]] — the runtime protocols these mirror.
- [[../../Compilers/PyTorchCompiler/TorchDynamo/VariableTracker]] — the static/runtime split in action.
