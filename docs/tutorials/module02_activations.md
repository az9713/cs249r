# Module 02 – Activation Functions Test Tutorial

## What you learn
Activation tests demystify why networks need non-linear steps. In `tinytorch/tests/02_activations/test_activations_core.py` you inspect ReLU, Sigmoid, Tanh, and Softmax forward passes plus their gradient masks. Integration suites feed tensors through activation stacks to catch instability, saturation, and mismatched shapes so you develop intuition about gradient flow.

## How the tests teach it
- `tinytorch/tests/02_activations/test_activations_core.py` encodes unit tests for each activation, including gradient masks and large-value behavior, so you learn to guard against overflow and dead neurons.
- `tinytorch/tests/02_activations/test_tensor_activations_integration.py` pushes raw Tensor outputs through activations to ensure autograd metadata is preserved.
- `tinytorch/tests/02_activations/test_activations_integration.py` chains multiple activations (for example Sigmoid→Tanh) to highlight how saturation in one stage affects the next.

## Why this matters in 2026
As models scale, stable activations decide whether you can train at all. ReLU dead zones, Sigmoid saturation, or naive Softmax implementations still derail trillion-parameter transformers on new accelerators. These tests teach you to reason about gradient health and numerical safeguards—competencies needed when deploying models on edge boards or optimizing inference kernels.

## Try it yourself
- Add a LeakyReLU class, mirror the gradient assertions from the core tests, and explain when you would select it over standard ReLU.
- Instrument Softmax with log-sum-exp trickery and watch the integration suite confirm that you eliminated overflow.

