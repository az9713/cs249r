# Module 05 – DataLoader Test Tutorial

## What you learn
DataLoader tests in `tinytorch/tests/05_dataloader/test_dataloader_core.py` teach batching, shuffling, and iteration semantics. You learn why deterministic iteration is critical for evaluation and how `batch_size` interacts with the final partial batch.

## How the tests teach it
- The basic iteration tests verify `__iter__`/`__next__`, batch sizing, and shuffle randomness so you can see how datasets become streams of tensors.
- Progressive tests add memory pressure, collate functions, and multi-epoch coverage so you see how loader state resets each pass.
- Edge-case checks ensure even ragged datasets produce final batches without crashing training loops.

## Why this matters in 2026
Modern training runs spend as much time waiting on data as they do crunching math. Knowing how these tests work prepares you to design streaming pipelines, reproducible evaluation passes, and fault-tolerant iterators—skills in demand for data-centric AI teams.

## Try it yourself
- Add a custom `collate_fn` that pads sequences and confirm the iteration assertions still hold.
- Simulate multi-epoch training and measure how shuffle seeds affect convergence metrics captured by the tests.

