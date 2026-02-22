# Module 17 – Acceleration Kernel Test Tutorial

## What you learn
Acceleration tests (`tinytorch/tests/17_acceleration/test_acceleration_core.py`) validate vectorized matmul, fused GELU, and tiled matmul routines. You learn to compare optimized kernels against reference NumPy implementations and ensure finite, accurate results.

## How the tests teach it
- `vectorized_matmul` tests compare outputs against `np.matmul` with tight tolerances, reinforcing the need for correctness before chasing speed.
- `fused_gelu` tests inspect special values (for example GELU(0) equals 0) and finite outputs to catch numerical instability.
- `tiled_matmul` cases ensure block-matrix algorithms recombine correctly, preparing you for writing custom CUDA kernels.

## Why this matters in 2026
Vendors chase every microsecond. Engineers who can verify math-optimized kernels are invaluable when porting LLMs to new ASICs or GPUs. These tests are a miniature checklist for that verification.

## Try it yourself
- Swap in half-precision tensors and re-run the suite to evaluate tolerance needs.
- Instrument timing around `vectorized_matmul` to quantify the speedup relative to a naive matmul.

