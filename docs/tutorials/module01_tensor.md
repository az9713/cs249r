# Module 01 – Tensor Foundations Test Tutorial

## What you learn
The tensor tests walk through how TinyTorch stores scalars, vectors, matrices, and 4-D image blocks. You see tensors created from Python lists or NumPy arrays, converted to float32, reshaped, broadcast, multiplied, and reduced so that you understand every primitive required before training a network. Reading `tinytorch/tests/01_tensor/test_tensor_core.py` shows exactly which edge cases—dtype normalization, contiguous storage, and view semantics—the framework must handle before anything else works.

## How the tests teach it
- `tinytorch/tests/01_tensor/test_tensor_core.py` stresses creation, dtype coercion, broadcasting, and math operators so you can trace each primitive step.
- `tinytorch/tests/01_tensor/test_tensor_integration.py` composes those primitives into small pipelines (for example chained matmul plus reduction) to verify gradients downstream will see sane shapes.
- `tinytorch/tests/01_tensor/test_01_tensor_progressive.py` replays notebook milestones so you learn to validate each feature incrementally instead of debugging everything at once.

## Why this matters in 2026
Every modern AI workload—LLMs, diffusion, recommender systems—reduces to tensor programs. Mismanaging dtype or layout leads directly to silent accuracy bugs or GPU crashes. Mastering these tests means you can reason about memory footprints, batched math, and interoperability with NumPy/PyTorch, skills employers expect even for “systems” roles.

## Try it yourself
- Implement a tensor normalization utility, run it through the creation and reduction tests above, and log how dtype changes propagate.
- Modify broadcasting rules and re-run the integration tests to see how a single shape rule ripples through matrix multiplies and reductions.

