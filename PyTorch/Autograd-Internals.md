# Autograd internals

Autograd is what turns `loss.backward()` into per-parameter gradients. The mechanism: during the forward pass, every tensor op **records** the operation in a graph (the `Function` DAG); `backward()` then walks it. This is the runtime counterpart of the compiled story in [[Compilers/PyTorchCompiler/AOTAutograd]].

## The forward recording

- A tensor with `requires_grad=True` gets an **`AutogradMeta`**: the `grad_fn` (the `torch.autograd.Function` that created it), `grad_accumulator`, `grad` (the accumulated buffer), and `base` (view metadata).
- Each op with `requires_grad` inputs creates a `Function` node holding (a) a **saved-for-backward** snapshot (inputs/outputs needed by its backward) and (b) a pointer to the node that produced each input — forming a DAG.
- `torch.no_grad()` / `torch.inference_mode()` *skip* node creation — the same tensor ops, no graph → faster, no memory growth (see the [[PyTorch]] perf notes).

## The backward walk

```
loss.backward() → walks the DAG from loss
   for each node: compute_grad(grad_of_output, saved_tensors) → grad_of_inputs
```

- **Gradient accumulation**: multiple paths to a tensor sum their contributions (`grad = a + b`). The `grad` buffer on each leaf accumulates across the batch loop.
- `retain_graph=True` keeps the graph for a second pass; by default it's freed after `backward()`.
- Grad flows to **leaf tensors** (parameters); intermediate (non-leaf) tensors' grads are released unless `retain_graph`/`allow_unused`/hooks say otherwise.

## Custom autograd

`torch.autograd.Function` lets you define a custom differentiable op:

```python
class MyOp(torch.autograd.Function):
    @staticmethod
    def forward(ctx, x): ctx.save_for_backward(x); return x.sin()
    @staticmethod
    def backward(ctx, grad_out):
        x, = ctx.saved_tensors
        return grad_out * x.cos()
```

Rules: `save_for_backward` (not `self`) for tensors you need in backward; the backward's input is the incoming gradient (this is the chain rule, see [[MachineLearning/deeplearning/backpropagation]]). Custom ops must *also* be `torch.library`-registered with `FakeTensor`/`Meta` impls for `torch.compile` to trace them (see [[Custom-Ops]]).

## Why it matters to the compiler story

- `torch.compile` does this *ahead of time* on the FX graph ([[Compilers/PyTorchCompiler/AOTAutograd]]), so eager recording disappears in the compiled region.
- Understanding the DAG is what makes debugging backward bugs (`RuntimeError: a leaf Variable that requires grad...`, `element 0 of tensors does not require grad`) straightforward.

## Related

- [[Dispatch-Key]] — Autograd is one of the keys in the stack.
- [[Custom-Ops]] — where custom autograd meets `torch.library`.
- [[MachineLearning/deeplearning/backpropagation]] — the math it implements.
- [[Compilers/PyTorchCompiler/AOTAutograd]] — the compiled-mode version.
