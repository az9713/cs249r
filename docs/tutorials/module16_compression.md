# Module 16 – Compression & Pruning Test Tutorial

## What you learn
Compression tests (`tinytorch/tests/16_compression/test_compressor_core.py`) explore sparsity measurement, magnitude pruning, and structured pruning utilities. You learn how to remove weights safely and report compression ratios.

## How the tests teach it
- `measure_sparsity` tests craft matrices with known zero ratios so you can verify calculations exactly.
- `magnitude_prune` and `structured_prune` tests prune toy models then assert sparsity increases without breaking tensor shapes.
- Integration tests verify pruned models still execute forward passes, proving compression does not silently break computation.

## Why this matters in 2026
Pruning unlocks faster inference and cheaper storage, especially when combined with hardware that understands sparsity. Understanding how these tests validate pruning ensures you can deploy compressed models confidently.

## Try it yourself
- Add a global pruning routine and replicate the sparsity assertions to keep yourself honest.
- Build visualization scripts that read the test outputs and highlight which weights were kept versus zeroed.

