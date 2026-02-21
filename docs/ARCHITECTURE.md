# Machine Learning Systems Repository — Architecture Guide

This document explains how the `cs249r_book-dev` repository fits together so that new contributors can reason about the system without external references. It intentionally alternates between **big-picture maps** and **low-level traceability** to prevent gaps.

---

## 1. System Landscape

```
+------------------------------------------------------------------------------------+
|                                     LEARNING LAYERS                                |
+-----------------------------+-----------------------------+------------------------+
| Reader Experience           | Builder Experience          | Deployment Experience  |
| - book/quarto (chapters)    | - tinytorch/src (modules)   | - kits/, labs/ (labs)  |
| - book/cli binder CLI       | - tinytorch/bin/tito        | - hardware + sensors   |
+-----------------------------+-----------------------------+------------------------+
| Shared Tooling                                                                              |
| - book/cli/core (ConfigManager)  - book/tools/scripts (automation)  - root pyproject.toml   |
+---------------------------------------------------------------------------------------------+
```

* **Reader experience** content is rendered through the Binder/Quarto toolchain wired up in `book/cli/main.py:39-205` and driven by the format-aware `ConfigManager` in `book/cli/core/config.py:15-108`.
* **Builder experience** is the TinyTorch curriculum. The CLI shim in `tinytorch/bin/tito:1-34` bootstraps into the extensible `TinyTorchCLI` defined in `tinytorch/tito/main.py:76-210` so the same workflow works for both students and developers.
* **Deployment experience** is captured in the hardware `kits/` and applied `labs/` projects. Their landing pages (for example `kits/README.md:1-60` and `labs/README.md:1-60`) explain which boards, sensors, and OS images each lab targets.

---

## 2. Content Production (Quarto/Binder Stack)

```
+--------------+      +-----------------+      +-------------------------+      +------------------+
| CLI (binder) | ---> | ConfigManager   | ---> | Quarto Renderer (HTML)  | ---> | _build/html site |
| ./binder ... |      | load/symlink    |      | PDF / EPUB renderers    |      | PDF/EPUB assets  |
+--------------+      +-----------------+      +-------------------------+      +------------------+
       |                     |                             |                                |
       |                     |                             |                                |
       v                     v                             v                                v
  book/cli/main.py     book/cli/core/config.py     book/quarto/config/_quarto-*.yml   book/quarto/_build/*
```

1. **Command dispatch**: `MLSysBookCLI` (`book/cli/main.py:39-205`) parses user commands (`build`, `preview`, `pdf`, etc.) and reports progress through `rich`.
2. **Format selection**: `ConfigManager` (`book/cli/core/config.py:15-108`) rewires `_quarto.yml` to point at the right format-specific configuration before rendering.
3. **Rendering rules**: Each Quarto config (for example `book/quarto/config/_quarto-html.yml:1-60`) specifies output directories, post-render scripts, social metadata, and validation hooks.
4. **Automation**: The manual publisher (`book/tools/scripts/publish/publish.sh:1-70`) sequences verification, compression, GitHub releases, and CDN cache refreshes when a maintainer needs deterministic builds.

**Communication Flow (format switching)**

```
User intention
    |
    v
CLI parser ----> ConfigManager ----> Symlink/_quarto.yml ----> Quarto CLI ----> Output artifacts
```

Every arrow above is implemented in code: CLI dispatch (main.py), config mutation (config.py), system call to Quarto (`BuildCommand`), and the generated HTML/PDF/EPUB tree inside `book/quarto/_build/`.

---

## 3. TinyTorch Architecture

```
+--------------------------------------------------------------------------------------+
|                        TinyTorch Learning & Execution Stack                          |
+------------------+---------------------+----------------------+---------------------+
| Data Ingestion   | Autograd Core       | Training Orchestrator| Performance Layers  |
| DataLoader       | Tensor + Function   | Trainer + Schedules  | Profiling/Quant/Acc |
| (05_dataloader)  | (01_tensor,06_autograd)| (08_training)     | (14-19 modules)     |
+------------------+---------------------+----------------------+---------------------+
```

* **Tensor + Autograd**: Module 01 defines the `Tensor` body (`tinytorch/src/01_tensor/01_tensor.py:258-520`). Module 06 adds differentiation primitives via `Function` subclasses (`tinytorch/src/06_autograd/06_autograd.py:233-410`).
* **Data pipeline**: `DataLoader` batches and augments datasets (`tinytorch/src/05_dataloader/05_dataloader.py:575-890`), feeding every later module through a consistent iterator API.
* **Training loop**: The `Trainer` object (`tinytorch/src/08_training/08_training.py:502-780`) coordinates forward passes, backward passes, checkpointing, and cosine schedules.
* **Attention/Transformers**: Multi-head attention is implemented in `tinytorch/src/12_attention/12_attention.py:691-884`, then composed into the transformer stack in Module 13.
* **Optimization layers**: Quantization infrastructure (`tinytorch/src/15_quantization/15_quantization.py:372-1380`) accepts tensors or entire models, producing INT8-friendly weights and calibration summaries.

**Communication Flow (tito CLI to modules)**

```
+-------------+      +---------------------------+      +---------------------+      +--------------------+
| bin/tito    | ---> | TinyTorchCLI (tito/main)  | ---> | Command Handlers    | ---> | Module notebooks   |
| (shim)      |      | registers commands        |      | (e.g., DevCommand)  |      | tinytorch/src/*    |
+-------------+      +---------------------------+      +---------------------+      +--------------------+
                                                          |                                ^
                                                          v                                |
                                            Trainer / modules emit artifacts ---------------
```

1. Users run `./bin/tito module start 01`. The shim in `tinytorch/bin/tito:1-34` injects TinyTorch into `sys.path`.
2. `TinyTorchCLI` (`tinytorch/tito/main.py:76-220`) resolves the project root, loads configuration, and routes to the requested command group.
3. Command objects import the relevant source module—notebooks under `tinytorch/src/*`—and execute helper utilities such as module reset, verification, or grading.
4. The trainer and profiling modules emit exports to `.tinytorch/` for progress tracking, enabling the milestone and benchmarking commands to read consistent metadata.

---

## 4. Hardware Kits and Applied Labs

```
Learner
  |
  | chooses board (Arduino / Seeed / Raspberry Pi)
  v
kits/contents/<board>/lab_X.qmd  --> Quarto render  --> kits/_build/*
                                         |                    |
                                         v                    v
                                Device flash scripts   Datasheets/assets
```

* Each kit provides progressive labs (`kits/README.md:20-70`) with prerequisites, BOM, and cross-links to the matching `labs/` experiments.
* The labs directory mirrors the kits pipeline and focuses on skill sequences (see `labs/README.md:1-60`), enabling content teams to iterate on experiments independently of kit hardware refreshes.
* Both kits and labs reuse the Quarto workflow, so learning how the book renders automatically carries over to hardware documentation updates.

---

## 5. Automation & Governance Layers

* **Dependency management**: Root `pyproject.toml:1-120` defines core dependencies (`jupyter`, `quarto`, `pyyaml`, etc.) plus optional `dev`, `ai`, and `build` extras so environments stay reproducible.
* **Scripting toolbox**: `book/tools/scripts/common/*` house logging, validation, and YAML utilities consumed by maintenance scripts such as `book/tools/scripts/maintenance/preflight.py` (sanity checks before publishing) and `book/tools/scripts/docs/README.md` (documentation policies).
* **Publishing guardrails**: `book/tools/scripts/publish/publish.sh:1-120` orchestrates manual releases (running tests, compressing outputs, tagging, pushing to GitHub, then invalidating caches) for cases where CI/CD is insufficient.

---

## 6. Traceability Checklist

Use this checklist when onboarding or reviewing pull requests:

| Concern | Where to Inspect | Why it Matters |
|---------|------------------|----------------|
| CLI routing | `book/cli/main.py:39-205` | Ensures build/preview commands map to the right subroutines |
| Format switching | `book/cli/core/config.py:15-108` | Prevents HTML/PDF/EPUB configs from drifting |
| Site semantics | `book/quarto/config/_quarto-html.yml:1-120` | Controls navigation, metadata, analytics |
| Manual publish | `book/tools/scripts/publish/publish.sh:1-160` | Documents the human-in-the-loop release plan |
| Tensor core | `tinytorch/src/01_tensor/01_tensor.py:258-520` | Foundation for every later module |
| Autograd graphs | `tinytorch/src/06_autograd/06_autograd.py:233-410` | Gradient correctness and debugging |
| Training loop | `tinytorch/src/08_training/08_training.py:502-780` | Where evaluation, metrics, and checkpoints connect |
| Attention stack | `tinytorch/src/12_attention/12_attention.py:691-884` | Validates transformer readiness |
| Quantization | `tinytorch/src/15_quantization/15_quantization.py:372-1380` | Gateway to hardware deployment |
| Hardware docs | `kits/README.md:1-120`, `labs/README.md:1-120` | Show how documentation aligns with real devices |

Keeping these artifacts in sync guarantees there is **zero documentation debt**: every subsystem is both visualized (diagrams above) and tied back to concrete source files you can inspect immediately.
