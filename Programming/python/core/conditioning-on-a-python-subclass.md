# Conditioning on a python subclass

Conditioning on a python subclass means changing behavior a subclass based on the
conditions

There are actually multiple ways to achive this

## The scenario

You have a base class and subclasses, and some code must behave differently *depending on which subclass* the object is. (This comes up a lot in compiler/runtime code — e.g. PyTorch tensors dispatch on subclass, or inductor generating different code for different tensor subclasses.)

## Ways to achieve it

1. **`isinstance` checks** — the blunt way:
   ```python
   if isinstance(obj, SpecialSubclass):
       ...
   ```
   Works, but fragile: every new subclass needs an edit here, and `isinstance` does the whole MRO walk.

2. **Polymorphism (the OO way, preferred)** — the base defines a method, each subclass overrides it:
   ```python
   class Base:
       def behavior(self): ...
   class Special(Base):
       def behavior(self): ...   # overrides
   ```
   Callers just call `obj.behavior()` — no conditionals. New subclasses need no caller edits. This is the cleanest approach in most code.

3. **Conditional registration / dispatch** — register behavior in a dict keyed by subclass:
   ```python
   handlers = {}
   handlers[Special] = special_handler
   handler = handlers.get(type(obj), default_handler)
   ```
   Good when you can't (or shouldn't) touch the subclass classes — e.g. dispatching on a *third-party* subclass, or in framework plugin systems.

4. **`__init_subclass__`** — the base class hooks into the creation of each subclass at class-definition time, so behavior is wired up automatically when the subclass is declared.

5. **Protocols / structural checks** — don't condition on the subclass at all; check it *has* the attribute/method (`hasattr` or a `Protocol`), which is more flexible (see [typechecking](typechecking.md)).

## The rule of thumb

If you control the class hierarchy → **polymorphism** (option 2). If the classes are external → **registration** (option 3). `isinstance` chains (option 1) are a code smell that usually indicates you should have used the class itself.
