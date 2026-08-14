# Interview prep notes

My per-role prep: what each interview loop actually asks, and which notes in this KB cover it. The common core is systems design, ML design, and coding — then each role adds a specialty axis. Start from [where-am-i-now](where-am-i-now.md) to see which boxes my existing notes tick.

## The common core

- **Coding**: the usual (arrays, strings, graphs, DP) — but for systems roles, expect *systems-flavored* variants: lock-free queue design, thread-pool, memory-layout questions (see [cpp-concurrency](../Programming/cpp/cpp-concurrency.md), [cpp-memory-layout](../Programming/cpp/cpp-memory-layout.md)). Practice on paper + in a REPL; time-box every problem.
- **Behavioral** ("tell me about a time..."): STAR format, and honestly the repo's own contribution plans make great stories — a debugging saga from [cpp-debugging](../Programming/cpp/cpp-debugging.md), a PR lifecycle from [PR-Lifecycle](../OpenSource/PR-Lifecycle.md).

## Systems design

- **Compiler pipeline design**: given a small language/model, walk through frontend → IR → passes → codegen (the exact ladder in [ai-compiler-engineer](ai-compiler-engineer.md)). Expect "how would you fuse these ops / lower this op / add a pass" — the vocab from [PLAN](../Compilers/MLIR/) and [PLAN](../Compilers/PyTorchCompiler/) covers it.
- **Serving architecture**: request → batch → GPU → response, with queueing, batching/continuous batching, KV cache, monitoring (see [mlops-engineer](mlops-engineer.md) and the deployment notes). Draw the boxes, then talk about *bottlenecks* — bandwidth-bound, latency, tail.
- **Framework/hardware design**: custom op + dispatch (see [Custom-Ops](../PyTorch/Custom-Ops.md), [Dispatch-Key](../PyTorch/Dispatch-Key.md)), memory allocator design, distributed training (see [distributed-training](../MachineLearning/deeplearning/distributed-training.md)).

## ML design

- **Model selection**: when to reach for which architecture/approach; trade-offs (accuracy vs latency vs cost). Grounded in [ml-engineer](ml-engineer.md) and the model notes.
- **Evaluation**: metrics choice, train/val/test discipline, failure analysis. The eval pieces in [GPT-evaluation](../MachineLearning/nlp/GPT-evaluation.md) and the CV eval notes are the concrete examples to cite.
- **Debugging training**: why loss is stuck — LR, data, label noise, overfitting — the checklist from the optimization/regularization notes ([regularization-normalization](../MachineLearning/deeplearning/regularization-normalization.md), [model-optimization](../MachineLearning/deeplearning/model-optimization.md)).

## Role-specific axes

- **Compiler roles** (AI compiler, framework engineer): IR design, pass ordering, lowering correctness, codegen targets — and *performance*: roofline, tiling, vectorization ([roofline-model](../Store/roofline-model.md), [TorchInductor](../Compilers/DLCompilers/TorchInductor/TorchInductor.md)). Expect whiteboard "lower this expression" questions.
- **ML/DS roles**: loss functions (when and why), overfitting/underfitting, metric choice per task, class imbalance. More statistics-fluent than the others.
- **MLOps/backend roles**: deployment, model versioning, monitoring, rollout, cost — the serving design above plus CI/CD ([CI_Infra](../PyTorch/CI_Infra.md), [devops-engineer](devops-engineer.md)).

## The drill

For each target role: pick 2 systems-design prompts + 2 ML-design prompts + 1 compiler/coding prompt, and answer them out loud with a timer, citing notes from this repo as my reference. Two passes before any interview; the goal is fluency, not memorization.

## Related

- [where-am-i-now](where-am-i-now.md) — the gap map that tells me what to prepare.
- Each role note: [ai-compiler-engineer](ai-compiler-engineer.md), [pytorch-developers](pytorch-developers.md), [rocm-developers](rocm-developers.md), [ml-engineer](ml-engineer.md), [mlops-engineer](mlops-engineer.md).
- [PLAN](./) — the roadmaps area this lives in.
