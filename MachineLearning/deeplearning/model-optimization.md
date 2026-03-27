# Model optmization

optmizers are the algorithms that decide how to adjust the model parameters to reduce the model prediction error

## Why use optmizer.zero_grad() method before starting each batch loop.

Gradients can add up when we try to calculate them a bunch of times. This leads to accumulation of Gradients which can lead to higher computation and lower accuracy, to prevent this from happening we need to zero the gradients.

There are few ways to zero gradients of a tensor:
- Using optmizer.zero_grad()
- Calling tensor.grad.zero_() method
