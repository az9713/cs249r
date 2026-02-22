# Module 14 – Profiling & Measurement Test Tutorial

## What you learn
Profiler tests (`tinytorch/tests/14_profiling/test_profiler_core.py`) teach you to instrument models: counting parameters, timing inference passes, and examining distributions. You learn how to quantify resource needs before optimizing.

## How the tests teach it
- Parameter count tests create `Linear` layers and assert expected totals so you can validate counting logic.
- Latency tests run synthetic models through `Profiler.measure_latency` with warmup and iteration control, teaching you to gather statistically meaningful numbers.
- Weight-distribution checks ensure histogram utilities work, a precursor to diagnosing quantization or pruning targets.

## Why this matters in 2026
Production ML requires evidence-based optimization. Profiling is the first step. These tests show how to build tooling that hardware teams and MLOps can trust when deciding what to deploy where.

## Try it yourself
- Add GPU timing hooks and extend the latency assertions to compare CPU versus CUDA.
- Feed a sparsified model to the histogram logic and verify it spots heavy zero mass.

