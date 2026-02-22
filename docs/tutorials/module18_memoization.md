# Module 18 – Memoization & KV Cache Test Tutorial

## What you learn
Memoization tests (`tinytorch/tests/18_memoization/test_kv_cache_core.py` plus integration suites) cover key-value caching for autoregressive decoding. You learn how caches store per-layer, per-head tensors and how advancing sequence positions affects retrieval.

## How the tests teach it
- Core tests instantiate `KVCache`, update it one token at a time, and assert retrieval returns identical K/V tensors.
- Advanced tests check multi-layer caches and sequence advancement, ensuring offsets move forward correctly.
- Integration suites plug caches into TinyGPT to prove latency drops while outputs stay deterministic.

## Why this matters in 2026
Streaming inference for chatbots, copilots, and speech models depends on KV caching to maintain throughput. Engineers must understand cache layout and lifecycle to serve millions of requests reliably.

## Try it yourself
- Extend `KVCache` to support chunked decoding and mimic the retrieval assertions.
- Visualize memory usage per layer as the tests exercise `cache.advance()`.

