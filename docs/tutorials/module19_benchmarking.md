# Module 19 – Benchmarking Harness Test Tutorial

## What you learn
Benchmark tests (`tinytorch/tests/19_benchmarking/test_benchmark_core.py`) teach you to run repeatable latency/throughput measurements, aggregate metrics, and generate compliance-style reports such as MLPerf.

## How the tests teach it
- `Benchmark.run_latency_benchmark` tests verify each result records mean and variance so you treat performance statistically.
- MLPerf-related cases simulate accuracy-versus-latency checks, mirroring industry submissions.
- Integration tests confirm benchmark scripts can iterate over model/dataset pairs without manual wiring.

## Why this matters in 2026
Companies care about tokens per second, watts, and accuracy simultaneously. Mastering these tests enables you to build dashboards and CI gates that catch regressions before you ship models to customers.

## Try it yourself
- Add energy-measurement stubs and log them next to latency, replicating the assertion style.
- Wire a new model into `BenchmarkSuite` and ensure the tests still pass to validate extensibility.

