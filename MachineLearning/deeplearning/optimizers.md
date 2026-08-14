# Model optmization

optmizers are the algorithms that decide how to adjust the model parameters to reduce the model prediction error

The optimizer's job: given gradients, decide the weight update. The family is gradient descent; the differences are in *how* the gradient is used (momentum, per-parameter scaling, preconditioning).

## The main optimizers

- **SGD** — `w ← w - η·∇`. Simple; with momentum it accelerates along consistent directions.
- **SGD + momentum** — keeps a velocity; overshoots small local minima, converges faster.
- **Adam** — per-parameter learning rates from the (exponential) mean and variance of past gradients. The default for most deep nets; `weight_decay` → AdamW.
- **AdamW** — decoupled weight decay (correctly implemented), the default for Transformers/BERT-family models.
- **RMSProp / Adagrad** — adaptive per-param scaling ancestors of Adam.

## Why use optmizer.zero_grad() method before starting each batch loop.

Gradients can add up when we try to calculate them a bunch of times. This leads to accumulation of Gradients which can lead to higher computation and lower accuracy, to prevent this from happening we need to zero the gradients.

There are few ways to zero gradients of a tensor:
- Using optmizer.zero_grad()
- Calling tensor.grad.zero_() method

Why *accumulation* happens: PyTorch **accumulates** gradients by default (they `+=` into `.grad`). That's intentional for gradient *accumulation* (simulating big batches over several forward/backward passes), but if you don't reset, every new batch's gradient is added on top of the old ones → the update step uses garbage and training breaks.

## The canonical training loop

```python
for x, y in dataloader:
    optimizer.zero_grad()       # reset grads
    loss = loss_fn(model(x), y)
    loss.backward()             # compute gradients
    optimizer.step()            # apply the update
```

Note: `zero_grad()` must be called *before* `backward()` — doing it after `step()` (or not at all) means the next batch's gradients get added to the current ones.

## Related

- The learning-rate part of optimization is handled by schedulers — see [how-to-use-lr-scheduler](how-to-use-lr-scheduler.md).
- Tuning optimizer/LR/weight-decay is the core of [hyperparameter-search](hyperparameter-search.md).
