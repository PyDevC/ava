# Bytecode

Python doesn't execute source directly — the compiler turns source into **bytecode**, a compact stack-machine instruction set, which the interpreter (CPython) then executes. You can see it with:

```python
import dis
dis.dis(my_function)
```

## Why it matters

- **Performance**: a Python function call overhead is really "set up a frame, run N bytecode instructions". Loops, attribute access, and function calls all map to bytecode instructions.
- **Debugging**: understanding `LOAD_ATTR`, `CALL_FUNCTION`, `BUILD_TUPLE` etc. explains why some code is slow.
- **Compilers**: this is *exactly* what TorchDynamo traces! Dynamo reads the bytecode of a function, walks it, and builds a graph. Every frame is bytecode; graph breaks happen at specific bytecode positions. See [[Compilers/PyTorchCompiler/TorchDynamo/GraphBreaks]] and [[Compilers/PyTorchCompiler/TorchDynamo/DebugWithTorchDynamo]].

## How a bytecode instruction works

The CPython VM is a **stack machine**:

```
LOAD_CONST 0      # push the constant
LOAD_FAST 1       # push a local variable
BINARY_OP +       # pop two, push the sum
STORE_FAST 2      # pop into a local
RETURN_VALUE
```

Each instruction has an opcode (1 byte historically, now extended) + an argument. The interpreter is a giant `switch` over opcodes.

## Typical opcodes to know

- `LOAD_FAST` / `STORE_FAST` — local variable access (fast).
- `LOAD_GLOBAL` / `STORE_GLOBAL` — globals (slower — dict lookup).
- `LOAD_ATTR` / `LOAD_METHOD` — attribute access (very common, a big chunk of Python overhead).
- `CALL_FUNCTION` / `CALL_METHOD` — function calls.
- `BINARY_OP` / `COMPARE_OP` / `UNARY_NOT` — arithmetic and comparisons.
- `BUILD_LIST` / `BUILD_TUPLE` / `BUILD_CONST_KEY_MAP` — container construction.
- `FOR_ITER` / `JUMP` — loops and control flow.
- `POP_JUMP_IF_FALSE` — `if` branches.

## Version churn

Opcodes change between Python versions (3.11 added specialized opcodes — the specializing adaptive interpreter; 3.13 switched to a **wordcode** layout). Never assume bytecode stability across versions.

## Related

- [[typechecking]] — static checking happens on the source/type level, *before* bytecode.
- The bytecode is what makes [[Compilers/PyTorchCompiler/TorchDynamo/GraphBreaks|Dynamo's tracing]] possible at all.
