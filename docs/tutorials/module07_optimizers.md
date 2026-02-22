# Module 07 – Optimizer Test Tutorial

## What you learn
Optimizer tests in `tinytorch/tests/07_optimizers/test_optimizer_core.py` focus on SGD and Adam. You learn how learning rate, momentum terms, and `zero_grad()` interplay to update Tensor parameters consistently with autograd-generated gradients.

## How the tests teach it
- The SGD sections seed gradients, call `optimizer.step()`, and compare parameter deltas against the expected formula to prove updates actually happen.
- Adam tests track first and second moment buffers to show how bias correction and adaptive learning rates are implemented.
- Dedicated cases ensure `zero_grad()` traverses parameter lists and clears buffers so stale gradients cannot bleed into future steps.

## Why this matters in 2026
Every practitioner tunes optimizers weekly. Understanding the exact arithmetic behind `step()` is vital when implementing fused kernels, distributed training, or adapting to novel hardware. These tests ensure you can reason beyond “call optimizer.step()” and verify real updates occur.

## Try it yourself
- Extend SGD with momentum and add checks showing exponential smoothing behaves as expected.
- Instrument `zero_grad()` to optionally set grads to `None` and rerun the suite to see downstream memory effects.

