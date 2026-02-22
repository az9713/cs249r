# Module 20 – Capstone System Test Tutorial

## What you learn
Capstone tests (`tinytorch/tests/20_capstone/test_capstone_core.py`) verify the submission pipeline that bundles benchmarks, gradient checks, and layer sanity tests. You learn how to prove the entire TinyTorch stack works as a cohesive system.

## How the tests teach it
- `BenchmarkSuite` tests ensure teams can register custom models and datasets and collect comparable stats.
- Validation tests re-check tensors, gradients, and linear layers to guard against late regressions.
- Integration pieces mimic end-to-end submissions so you practice automating final deliverables.

## Why this matters in 2026
Shipping AI systems requires confidence that every dependency still works when combined. The capstone harness mirrors real production sign-offs where QA reruns health checks before deployment.

## Try it yourself
- Add an automated Markdown report that collates metrics from the benchmark suite, and validate it by rerunning the capstone integration tests.
- Extend the submission check to include profiling or quantization statistics and observe how the harness surfaces failures.

