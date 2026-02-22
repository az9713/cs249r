# Module 15 – Quantization Test Tutorial

## What you learn
Quantization tests (`tinytorch/tests/15_quantization/test_quantizer_core.py`) prove float32 tensors can become int8 tensors plus scale/zero-point metadata, and that `QuantizedLinear` layers stay functional. You learn round-trip error budgeting and model size accounting.

## How the tests teach it
- Quantizer tests check that quantized values stay within [-128, 127], ensuring safe int8 storage.
- Dequantization comparisons show how much error accumulates, giving you intuition for calibration ranges.
- `QuantizedLinear` cases compare parameter counts and forward outputs against baseline `Linear` layers so you see the trade-off between accuracy and footprint.

## Why this matters in 2026
Deploying LLMs on laptops, phones, or edge devices hinges on quantization. Being able to implement and validate per-layer quantization is a core industry skill.

## Try it yourself
- Implement per-channel scales and amend the tests to compare each channel independently.
- Profile latency before and after quantization using the profiler module to see the holistic impact noted by the benchmarking tests.

