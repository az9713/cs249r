# Module 06 – Autograd Engine Test Tutorial

## What you learn
Autograd tests in `tinytorch/tests/06_autograd/test_autograd_core.py` walk through `Variable` creation, `requires_grad` flags, computational graph building, and derivative propagation for addition, multiplication, and exponentiation. You learn how backward traverses the graph, how gradients accumulate, and when they reset.

## How the tests teach it
- The creation tests build Variables with and without gradient tracking so you can see how metadata toggles accumulation.
- Operation-specific tests construct tiny graphs (`x + y`, `x * y`, `x ** 2`) then call `backward()` to verify analytical gradients.
- Additional cases ensure gradients initialize to `None`, accumulate over repeated backward passes, and clear correctly when `zero_grad` is called.

## Why this matters in 2026
Custom autograd knowledge lets you debug exploding gradients, memory leaks, and mixed-precision anomalies—core responsibilities when training frontier models or porting networks to accelerators lacking PyTorch. These tests are your blueprint for reasoning about graph-based differentiation instead of treating it as a black box.

## Try it yourself
- Implement a sine operation, wire it into the graph, and copy the gradient assertions to confirm correctness.
- Intentionally break gradient accumulation and watch how the tests fail so you recognize the symptoms when training real models.

