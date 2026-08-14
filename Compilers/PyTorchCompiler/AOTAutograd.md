# AOTAutograd

AOTAutograd is the **piece between Dynamo and Inductor** (see [[../PLAN]]): it takes the forward-only FX graph from Dynamo and produces the *backward graph* too — ahead of time, before Inductor compiles kernels. This is what makes `torch.compile` fast on training: both the forward *and* backward become compiled Triton/C++.

## The problem it solves

In eager PyTorch, autograd builds the backward on the fly during `loss.backward()` (see [[../../PyTorch/PLAN]] autograd internals). In compile mode we want the backward **compiled as eagerly as the forward** — so AOTAutograd runs autograd's machinery at *compile time* on the traced graph:

```
forward_fx → AOTAutograd → (fwd_fx, bwd_fx)
                              ↓            ↓
                     Inductor compiles both
```

Result: one compile pass, then forward+backward both run as fused kernels with no Python.

## How it works

- Runs **the same autograd engine** (`torch.autograd.grad`) but on the *FX graph* instead of live tensors: traces `make_fx`-style functionalization of the forward, computes the backward for each op via its `torch.autograd.Function` rules.
- **Functionalization**: AOTAutograd turns mutations (`x.add_(1)`, in-place ops) into functional ops returning new tensors — because the FX graph / compiled world doesn't allow aliasing the way eager tensors do. This is a big semantic subtlety: compiled code must not rely on eager in-place aliasing semantics.
- Outputs: a forward graph (with any **joint** graph for grad) and a backward graph; **compiled backward** (`torch.compile` includes this by default on training).

## The consequences I keep hitting

- **Compile time doubles-ish**: both graphs get compiled. `torch.compile`'s training slowdown-on-first-call is usually *this*.
- **Mutating ops can break tracing** if the mutation pattern isn't functionalizable (a classic source of "compile fails, eager works").
- `torch.autograd.grad` inside the traced region, `view`/`alias` misuse, and `torch.no_grad()` boundaries all interact with AOTAutograd — the "joint" graph (forward+backward fused) exists so some fusions can cross the boundary.

## Related

- [[../TorchDynamo/Trace-Lifecycle]] — the graph source.
- [[../TorchInductor/TorchInductor]] — what consumes both graphs.
- [[../../PyTorch/PLAN]] — autograd internals the AOT part relies on.
- [[../TorchDynamo/Guards]] — both graphs share the same guards.
