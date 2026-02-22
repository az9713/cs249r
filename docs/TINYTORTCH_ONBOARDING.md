# TinyTorch First-Time Setup (Windows + Git Bash)

This guide reproduces the exact steps (including gotchas) we followed on a fresh Windows laptop using Git Bash. It assumes **zero prior experience** with the repository or tooling and calls out every prompt/error you might see.

---

## 0. Prerequisites

| Tool | Version | Notes |
|------|---------|-------|
| Git for Windows | Latest | Includes Git Bash terminal. |
| Python | 3.10+ (python.org installer) | Select “Add python.exe to PATH”. |
| GitHub CLI (optional but recommended) | Latest | Used earlier for authentication; not required for TinyTorch itself. |

> **Tip:** Windows often installs Python as `python.exe` only. TinyTorch scripts expect `python3`. We’ll fix that below.

---

## 1. Get the Code

Open Git Bash and clone the repository anywhere you like (Downloads used here):

```bash
cd ~/Downloads
git clone git@github.com:az9713/cs249r.git
cd cs249r
```

The TinyTorch source lives inside `tinytorch/`, but we keep the Python environment at the **repo root** so all subprojects can share it.

---

## 2. Create a Virtual Environment at the Repo Root

Still inside Git Bash:

```bash
python -m venv .venv
```

Why root? We want Book/TinyTorch/Kits to share one environment. The command above creates `C:\Users\<you>\Downloads\cs249r\.venv`.

### Activation commands

| Shell | Command |
|-------|---------|
| Git Bash | `source .venv/Scripts/activate` |
| PowerShell | `.\.venv\Scripts\Activate.ps1` |

Always activate before running CLI commands; your prompt will show `(.venv)` when active.

---

## 3. Deal with the `python` vs `python3` Shebang Issue

TinyTorch’s CLI (`./bin/tito`) starts with `#!/usr/bin/env python3`. On Windows, only `python.exe` exists, so running `./bin/tito` in Git Bash prints:

```
Python was not found; run without arguments to install from the Microsoft Store...
```

Fix options (pick one):

1. **Temporary alias (per session):**
   ```bash
   alias python3=python
   ```
2. **Permanent fix:** add the alias to `~/.bashrc`, or copy the interpreter:
   ```bash
   cp .venv/Scripts/python.exe .venv/Scripts/python3.exe
   ```
3. **Bypass shebang:** run the script through Python directly:
   ```bash
   python ./bin/tito setup
   ```

In our session we used option 3 (`python ./bin/tito setup`) after seeing the error. Any of the three approaches work.

---

## 4. Install TinyTorch Dependencies via the CLI

Activate the venv, `cd tinytorch`, then launch setup:

```bash
cd tinytorch
python ./bin/tito setup
```

You will see the welcome banner and an interactive wizard:

1. **Virtual environment** – it notices `.venv` and configures a TinyTorch-specific venv inside `tinytorch/.venv`.
2. **Package installation** – installs NumPy, Jupyter, Jupytext, nbdev, rich, etc. If they already exist you’ll see “Already installed…”.
3. **Profile** – prompts for name/email/affiliation and writes `~/.tinytorch/profile.json`. Example:
   ```
   Your name (Tiny🔥Torch Developer): az9713
   Your email (optional): az9713@yahoo.com
   Your affiliation: Independent
   ```
4. **Validation** – checks Python version, Jupyter kernel registration, and CLI health.

Successful output ends with:

```
🔥 Tiny🔥Torch Setup Complete!
👋 Welcome, <name>!
```

If you encounter the “Python was not found” message again, confirm the alias or run through `python ./bin/tito ...`.

### Install the TinyTorch package into your environment

Phase 2 of the module tests uses a pytest plugin (`--tinytorch` flag). Pytest only knows that flag if the TinyTorch package is installed in the interpreter you are using. Do **one** of the following before running `tito module test`:

1. **Use the CLI-managed venv**  
   ```
   source .venv/bin/activate      # inside tinytorch/.venv (Git Bash)
   # or .\.venv\Scripts\Activate.ps1 (PowerShell, from tinytorch/.venv)
   ```
   When `tito setup` ran, it created this venv and installed TinyTorch+pytest plugins already.
2. **Install TinyTorch into the repo root venv**  
   Keep using the root `.venv`, but install the package there:
   ```bash
   cd C:/Users/simon/Downloads/cs249r_book-dev/tinytorch
   pip install -e .[dev]
   ```

If you skip this step, `python ./bin/tito module test ...` will fail during Phase 2 with `error: unrecognized arguments: --tinytorch`.

---

## 5. Daily TinyTorch Commands

All commands below assume the venv is active and you’re inside `tinytorch/`.

| Action | Command | Notes |
|--------|---------|-------|
| List modules | `python ./bin/tito module list` | Shows IDs 01–20. |
| Start a module | `python ./bin/tito module start 01` | Opens the Jupytext notebook (Module 01: tensors). |
| Run module tests | `python ./bin/tito module test 01` | Uses `tinytorch/tests/01_tensor`. |
| Track progress | `python ./bin/tito module status` | Reads `.tinytorch/state.json`. |
| View milestones | `python ./bin/tito milestone status` | Shows larger goals (CNN, GPT, MLPerf). |
| Reset work | `python ./bin/tito module reset 01` | Clears outputs/checkpoints for that module. |

You can replace `python` with `./bin/tito` once you’ve defined the `python3` alias or created `python3.exe`.

---

## 6. TinyTorch Workflow Overview

- **CLI entry point**: `tinytorch/bin/tito` sets UTF-8 output and delegates to `tinytorch/tito/main.py`, which registers commands such as `setup`, `module`, `milestone`, `dev`, `benchmark`, etc. Running `./bin/tito setup` creates `.tinytorch/`, validates Python dependencies, and prints module availability.
- **Module notebooks**: Live under `tinytorch/src/<id_name>/<id_name>.py`. Each file is a Jupytext notebook combining Markdown explanations with executable cells (e.g., tensors `src/01_tensor/01_tensor.py`, autograd `src/06_autograd/06_autograd.py`, training `src/08_training/08_training.py`, attention `src/12_attention/12_attention.py`, quantization `src/15_quantization/15_quantization.py`). Modules import only previously completed capabilities.
- **Tests**: Mirror modules under `tinytorch/tests/<id_name>/` with progressive and integration suites (e.g., `tests/08_training/test_training_core.py`, `tests/15_quantization/test_quantizer_core.py`). Use them after every change.
- **CLI experiments**:
  - `python ./bin/tito module start 01` — open Module 01 in your default editor/Jupyter server.
  - `python ./bin/tito module test 06` — run module-specific tests.
  - `python ./bin/tito module status` — summarize `.tinytorch/state.json`.
  - `python ./bin/tito milestone status` — track larger achievements (CNN, GPT, MLPerf benchmarks described in `tinytorch/site/milestones/*.md`).
  - `python ./bin/tito benchmark run mnist` — trigger reproducible experiments defined in `tinytorch/tito/commands/benchmark.py` (e.g., training the Module 09 CNN on MNIST).

---

## 7. Suggested Experiments

1. **Tensor extensions** — Modify `src/01_tensor/01_tensor.py` to add broadcasting rules or new ops, then run `python ./bin/tito module test 01` and integration tests.
2. **New activation** — Extend `src/02_activations/02_activations.py`, propagate through `src/03_layers/03_layers.py`, and update `tests/02_activations/test_activations_core.py`.
3. **Scheduler tweaks** — Adjust `CosineSchedule` and `Trainer` (see `src/08_training/08_training.py:223` and `502`), validate via `python ./bin/tito module test 08`.
4. **Attention variant** — Modify `MultiHeadAttention` in `src/12_attention/12_attention.py:691` for relative position bias or flash attention approximations; run `tests/12_attention/test_attention_core.py`.
5. **Quantization research** — Extend the pipeline around `src/15_quantization/15_quantization.py:372` for per-channel scales or mixed precision; combine `module test 15` with profiling code in `src/14_profiling/14_profiling.py`.
6. **Add a module** — Copy any existing module folder, rename files (`<id>_<name>.py`, `ABOUT.md`, `module.yaml`), register it in `tinytorch/tito/core/modules.py`, document it under `tinytorch/site/modules/`, and add matching tests.

---

## 8. Step-by-Step Workflow (Recap)

1. **Environment setup**
   - `cd C:\Users\simon\Downloads\cs249r_book-dev\tinytorch`
   - `python -m venv ..\.venv` (then activate, install requirements)
   - `python ./bin/tito setup`
   - `python ./bin/tito system health`
2. **Explore existing modules**
   - `python ./bin/tito module list`
   - `python ./bin/tito module start 01`
   - `python ./bin/tito module test 01`
   - Track with `module status` / `milestone status`
3. **Run experiments**
   - Example (attention): `python ./bin/tito module start 12`, modify `src/12_attention/12_attention.py`, run `module test 12` plus `pytest tinytorch/tests/integration/test_transformer_gradient_flow.py`. If you touch Trainer/optimizers, re-run Module 08 tests.
4. **Add a new module**
   - Duplicate a folder (e.g., `src/15_quantization` → `src/21_sparse_attention`)
   - Update `module.yaml` prerequisites and `MODULES` metadata (`tinytorch/tito/core/modules.py`)
   - Create tests under `tinytorch/tests/21_sparse_attention`
   - Update site docs (optional but recommended)
   - Verify with `module list`, `module start`, `module test`
5. **Extend benchmarks / CLI commands**
   - Add handlers in `tinytorch/tito/commands/benchmark.py`
   - Register help text in the same file
   - Document under `tinytorch/site/extra/benchmarks/` if needed
   - Run via `python ./bin/tito benchmark run <name>`
6. **Git Bash quirks**
   - Alias `python3=python` or copy `python.exe` to `python3.exe`
   - Enable Developer Mode or run Git Bash as admin to allow symlink creation (used by binder/kits workflows)
   - Use Bash-style env vars (`export PYTHONIOENCODING=utf-8`) when following instructions originally written for PowerShell.

---

## 11. Run the Complete 20-Module Test Suite

After you can open the notebooks, validate the entire TinyTorch stack in one go. The CLI prints WHAT/WHY context for each Module 01–20 test phase and saves the transcripts for later audits.

1. Activate the root `.venv` (see Section 2) and enter the TinyTorch folder:
   ```powershell
   cd C:\Users\simon\Downloads\cs249r_book-dev\tinytorch
   ```
2. Run every module with verbose output and stop on failure:
   ```powershell
   .\.venv\Scripts\python.exe ./bin/tito module test --all --verbose > ..\docs\reports\tinytorch-full-test-log.txt
   ```
   The CLI now forces UTF-8 output on Windows via `tinytorch/bin/tito`, so the Rich banner and emoji no longer throw `UnicodeEncodeError`. If you are using an older checkout or a custom entry point, you can achieve the same result by prefixing `python -X utf8`.
3. Optional: capture per-module logs for students by rerunning each module with `--verbose` and redirecting to `docs/reports/moduleXX_<name>-test.log`. The summary document lives at `docs/reports/tinytorch-test-summary.md`.

**Troubleshooting:** If you previously saw `UnicodeEncodeError: 'charmap' codec can't encode character '\U0001f525'`, that was the Windows console refusing emojis. The fix is already in the shim (sets `PYTHONIOENCODING=utf-8`, `PYTHONUTF8=1`, and reconfigures `stdout`/`stderr`), but the fallback `python -X utf8 ./bin/tito ...` works in restricted shells.

---

## 9. Troubleshooting Recap

| Symptom | Cause | Fix |
|---------|-------|-----|
| `Python was not found; ... App execution aliases` | Git Bash looks for `python3` | Alias `python3=python`, copy `python.exe`, or run `python ./bin/tito ...`. |
| CLI can’t find the environment | Venv not active or created in wrong folder | Ensure `.venv` sits at repo root and run `source .venv/Scripts/activate` before commands. |
| `./bin/tito` “Permission denied” | File lost executable bit during checkout | Run `chmod +x bin/tito` inside `tinytorch`. |
| Quarto commands later complain about symlinks | Windows developer mode disabled | Enable Developer Mode or run Git Bash as admin once when binder CLI creates symlinks. |
| Phase 2 tests fail with `--tinytorch` argument error | Pytest plugin not installed in current venv | Activate `tinytorch/.venv` **or** run `pip install -e .[dev]` inside `tinytorch/` for the root `.venv`. |

---

## 10. Ready for Experiments

After completing these steps you can:

1. Walk through modules sequentially: `python ./bin/tito module start 01`, `... start 02`, etc.
2. Extend modules by editing `tinytorch/src/<module>/<module>.py` and adding tests under `tinytorch/tests/<module>/`.
3. Use the same environment for the rest of the repo (`book/`, `kits/`, `labs/`) because the venv lives at the top level.

Revisit this checklist whenever a new machine or teammate joins—the flow solves the exact issues we hit during setup.
