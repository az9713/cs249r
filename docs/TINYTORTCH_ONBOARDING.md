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

## 6. Troubleshooting Recap

| Symptom | Cause | Fix |
|---------|-------|-----|
| `Python was not found; ... App execution aliases` | Git Bash looks for `python3` | Alias `python3=python`, copy `python.exe`, or run `python ./bin/tito ...`. |
| CLI can’t find the environment | Venv not active or created in wrong folder | Ensure `.venv` sits at repo root and run `source .venv/Scripts/activate` before commands. |
| `./bin/tito` “Permission denied” | File lost executable bit during checkout | Run `chmod +x bin/tito` inside `tinytorch`. |
| Quarto commands later complain about symlinks | Windows developer mode disabled | Enable Developer Mode or run Git Bash as admin once when binder CLI creates symlinks. |

---

## 7. Ready for Experiments

After completing these steps you can:

1. Walk through modules sequentially: `python ./bin/tito module start 01`, `... start 02`, etc.
2. Extend modules by editing `tinytorch/src/<module>/<module>.py` and adding tests under `tinytorch/tests/<module>/`.
3. Use the same environment for the rest of the repo (`book/`, `kits/`, `labs/`) because the venv lives at the top level.

Revisit this checklist whenever a new machine or teammate joins—the flow solves the exact issues we hit during setup.
