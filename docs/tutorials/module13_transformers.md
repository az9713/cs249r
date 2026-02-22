# Module 13 – Transformer Block Test Tutorial

## What you learn
Transformer tests (`tinytorch/tests/13_transformers/test_transformers_core.py`) focus on the standard block: attention plus residual plus feed-forward plus layer norm. You learn stacking behavior, residual connections, and gradient stability.

## How the tests teach it
- Shape tests prove each `TransformerBlock` preserves `(batch, seq, embed)` so stacking is straightforward.
- Multi-block tests iterate through lists of blocks to ensure layer norm plus residual wiring stays numerically stable.
- Progressive suites run mini language-model passes to highlight how dropout and layer norms interact.

## Why this matters in 2026
Deploying or modifying GPT-style stacks requires confidence that custom tweaks (adapter layers, gating) will not break invariants. These tests instill that confidence and give you guardrails when experimenting.

## Try it yourself
- Add a gated feed-forward variant and confirm the stacking tests still hold.
- Simulate half-precision execution and use the suite to check for NaNs early.

