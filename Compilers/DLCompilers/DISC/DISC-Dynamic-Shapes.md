# DISC's dynamic-shape handling

DISC's founding problem (see [DISC](DISC.md)): static-shape compilers (classic XLA) recompile per shape, and fusing dynamic ops is hard. DISC's answer is to make shapes **symbolic from the start** and only specialize when it provably pays off.

## Symbolic shapes as expressions

At the graph level, dynamic dims are **symbols** (`s0`, `s1`), and derived shapes are **symbolic expressions** (`s0 * s1`, `s0 + 64`). The IR carries these as first-class values, so passes can reason about them without knowing concrete numbers:

- Fusion legality: "can ops A and B fuse" becomes "can their symbolic shapes be shown equal/compatible" — provable at compile time, no runtime branch.
- Index math, buffer sizes, and tiling bounds are all symbolic expressions, compiled once and evaluated at runtime with the actual values.

## Shape-constrained vs shape-agnostic kernels

DISC classifies each fused group at compile time:

- **Shape-constrained**: the kernel still needs *some* shape assumptions to generate good code (e.g. a tiling that depends on a dim being a multiple of the tile). The compiler emits *specialized* variants plus a runtime check that dispatches to the right one. Like XLA's "shape polymorphism", but the dispatch is cheap and chosen at compile time.
- **Shape-agnostic**: the kernel is generated with fully generic bounds — loops over symbolic sizes, no recompilation ever. Typically small elementwise/reduction kernels where a generic loop is near-optimal.

The compiler decides which category each kernel lands in (the "shape speculation" row in [Comparison-Matrix](../Comparison-Matrix.md)); a kernel only gets a specialized variant when the shape is *likely* to recur (based on prior observation/annotations). That's how it avoids the recompile storm of static compilers while keeping the performance of static code.

## How it differs from static-shape compilers

- **Static (classic XLA / TensorRT default)**: shape known → everything optimized for it → new shape → recompile. Dynamic shapes either recompile per call or fall back to slow generic kernels.
- **DISC**: symbols from the start → one compilation, symbolic codegen, plus cheap specialized variants with a runtime pick. No per-shape recompilation, no interpreter-style dynamic dispatch.

## Why it matters

Dynamic shapes are the #1 production blocker for DL compilers (variable batch, seq length, image sizes). DISC's bet — symbols plus a small set of specialized variants — is the version of the answer that works without recompile storms, and it's why it wins on dynamic workloads where XLA/TensorRT choke.

## Related

- [DISC](DISC.md) — the motivation and paper.
- [Two-level design](DISC-Two-Level-Design.md) — where this machinery runs.
- [Serving](DISC-Serving.md) — real workloads it's deployed on.
- [SymPy symbolic shapes](../../PyTorchCompiler/TorchDynamo/SymPy-Symbolic-Shapes.md) — Inductor's different answer to the same problem.
