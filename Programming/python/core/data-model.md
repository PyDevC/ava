# The data model: dunders, descriptors, metaclasses

Python's "magic" is a **protocol system**: special methods (`__dunder__`) let user objects participate in built-in syntax and operations. Descriptors and metaclasses are the two power features behind every framework — understanding them explains PyTorch modules, properties, and subclass dispatch.

## Dunders and operator overloading

- `__add__`, `__eq__`, `__getitem__`, `__call__`, `__enter__`/`__exit__` (context managers), `__iter__`/`__next__` (iterators), `__len__`, `__contains__`, `__repr__`/`__str__` — each *protocol* is triggered by syntax (`+`, `in`, `for`, `with`, `[]`, `()`, `repr()`).
- `__init_subclass__` / `__set_name__` — class-creation hooks: a base class can run code *whenever it's subclassed*, and a descriptor can learn its attribute name when the class is created. The PyTorch `nn.Module` machinery (parameter registration on subclass) uses exactly these.
- The precedence rule: **reflected** (`r`) versions (`int.__add__` when the left operand is a subclass), **data descriptor > instance dict > non-data descriptor** — get this wrong and attributes silently shadow (the classic `property` + `__dict__` confusion).

## Descriptors

A descriptor is any object with `__get__`/`__set__`/`__delete__`:

- **`property`** — a non-data-ish descriptor wrapping getter/setter (`__get__` only → the instance `__dict__` can shadow it).
- **`classmethod`/`staticmethod`** — descriptors that wrap the function.
- **Data descriptors** (`__set__` present) win over `__dict__` lookup — that's *why* `obj.attr = 5` can't silently overwrite a `property`'s setter.
- Framework pattern: `WeakMethod`, `cached_property`, `__slots__`-based lazy attrs are all descriptor tricks.

## Metaclasses and `__init_subclass__`

- A metaclass = the *type* of a class: `class Meta(type)`, then `class C(metaclass=Meta)`. `Meta.__new__`/`__init__` run when `class C` is *defined*, letting you rewrite the class (collect methods, inject attrs, register subclasses). The old-school way to build framework plumbing (`abc.ABCMeta`, Django's `Model`).
- **`__init_subclass__`** is the modern, simpler 90%: `class Base:` with `def __init_subclass__(cls, **kw)` runs on every subclass definition — PyTorch uses this pattern to auto-collect `nn.Module` parameters/buffers. Prefer it over a metaclass unless you need `__new__`-level control.
- Connections: [conditioning-on-a-python-subclass](conditioning-on-a-python-subclass.md) shows `__instancecheck__`/`__subclasscheck__` overrides (how `isinstance` can lie) — the same protocol layer.

## Why it matters for frameworks

Every framework is "define a subclass, the base class does the rest" — that's `__init_subclass__` + descriptors + dunders working together. Reading `nn.Module`, `Enum`, `dataclasses`, `functools.cached_property` with this lens turns "magic" into protocol mechanics.

## Related

- [conditioning-on-a-python-subclass](conditioning-on-a-python-subclass.md) — `isinstance`/dispatch overrides.
- [frames-namespaces](frames-namespaces.md) — where names/attrs resolve.
- [typechecking](typechecking.md) — `Protocol` as the static mirror of these protocols.
- [VariableTracker](../../../Compilers/PyTorchCompiler/TorchDynamo/VariableTracker.md) — Dynamo must model all of this symbolically.
