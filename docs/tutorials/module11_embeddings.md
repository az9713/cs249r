# Module 11 – Embedding Test Tutorial

## What you learn
Embedding tests in `tinytorch/tests/11_embeddings/test_embeddings_core.py` cover token-to-vector lookup, batched inputs, trainable parameters, and positional encodings. You learn how discrete IDs become dense representations and how positional information augments them.

## How the tests teach it
- The lookup tests feed single tokens and batches to `Embedding` to verify shape transformations from `(batch, seq)` to `(batch, seq, embed_dim)` and to confirm gradients track the underlying weight matrix.
- Additional sections ensure the embedding weights update when gradients flow, proving embeddings are trainable parameters.
- Positional encoding tests validate sinusoidal tables and broadcasting so you can trace how sequence order injects structure into the model.

## Why this matters in 2026
Embeddings are the front door of every text, code, or multimodal model. Efficiently handling vocabulary growth, batching, and positional context is mandatory for building custom LLMs or integrating retrieval-augmented pipelines.

## Try it yourself
- Add learned positional embeddings and mirror the shape assertions from the sinusoidal tests.
- Experiment with freezing embeddings and confirm the optimizer-related tests catch missing gradients.

