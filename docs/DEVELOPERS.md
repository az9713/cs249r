# Developer Enablement Guide

This guide is written for engineers who understand C/C++/Java but are new to full-stack or web tooling. Follow it **in order**—every step is explicit so you never have to guess or search elsewhere.

---

## 1. Workstation Prerequisites

| Tool | Why | Install Instructions |
|------|-----|----------------------|
| **Git ≥ 2.40** | Required for cloning and keeping history clean | [git-scm.com/downloads](https://git-scm.com/downloads) |
| **Python 3.10–3.12** | Matches the matrix declared in `pyproject.toml:11-65` | Windows Store or python.org; on macOS use `brew install python@3.12`; on Linux use your package manager |
| **Quarto CLI ≥ 1.4** | Builds the book, kits, and labs | [quarto.org/docs/get-started](https://quarto.org/docs/get-started) |
| **Node.js 18+ (optional)** | Only needed for developing Quarto filters with npm packages | [nodejs.org](https://nodejs.org) |
| **Make (optional)** | Convenience wrapper for repetitive Quarto builds | Windows: `choco install make`; macOS: `xcode-select --install`; Linux packages |

Verify everything once:

```powershell
git --version
python --version
quarto check
```

If any command fails, fix it now before proceeding.

---

## 2. Clone and Bootstrap

1. **Clone** the repository:
   ```powershell
   cd C:\workspace
   git clone https://github.com/harvard-edge/cs249r_book-dev.git
   cd cs249r_book-dev
   ```
2. **Create a Python virtual environment** at the repo root:
   ```powershell
   python -m venv .venv
   .\.venv\Scripts\Activate.ps1          # Windows
   # source .venv/bin/activate           # macOS/Linux
   ```
3. **Install shared dependencies**:
   ```powershell
   python -m pip install --upgrade pip
   pip install -r requirements.txt
   pip install -e .[dev]
   ```
   The extras (`.[dev]`) activate the test, lint, and publishing tools defined inside `pyproject.toml:69-120`.

Keep the environment active whenever you work on the project.

---

## 3. Daily Workflow — Book & Site

The Binder CLI acts as your command center. It is defined in `book/cli/main.py:39-205` and wires together the Quarto build system via `ConfigManager` (`book/cli/core/config.py:15-108`).

1. **Install Quarto extensions (first run only)**:
   ```powershell
   quarto install extension qmd-lua/filters
   ```
2. **Bootstrap binder CLI**:
   ```powershell
   cd book
   ./binder setup            # Creates symlinks, checks Quarto, primes caches
   ./binder doctor           # Runs health checks described in CLI help tables
   ```
3. **Build HTML output**:
   ```powershell
   ./binder build
   ```
   The CLI selects `book/quarto/config/_quarto-html.yml:1-80` via `ConfigManager` and dispatches to Quarto.
4. **Preview specific chapters with hot reload**:
   ```powershell
   ./binder preview intro,workflow
   ```
5. **Produce PDF/EPUB**:
   ```powershell
   ./binder pdf --all
   ./binder epub --all
   ```
6. **Clean artifacts**:
   ```powershell
   ./binder clean
   ```

If the CLI reports a configuration mismatch, re-run `./binder doctor`—it prints the active `_quarto.yml` target, which should match the output you expect.

---

## 4. Daily Workflow — TinyTorch

TinyTorch has its own CLI but shares Python tooling. The shim (`tinytorch/bin/tito:1-34`) ensures `tito` runs without a global install, and the core dispatcher lives in `tinytorch/tito/main.py:76-220`.

1. **Install TinyTorch extras**:
   ```powershell
   cd tinytorch
   python -m pip install -r requirements.txt
   pip install -e .[dev]
   ```
2. **Initialize the TinyTorch CLI**:
   ```powershell
   ./bin/tito setup
   ./bin/tito system health
   ```
3. **Work through modules**:
   ```powershell
   ./bin/tito module start 01    # Tensors (see tinytorch/src/01_tensor/01_tensor.py:258-520)
   ./bin/tito module status
   ./bin/tito module test 01
   ```
4. **Access advanced tooling**:
   ```powershell
   ./bin/tito dev preflight
   ./bin/tito benchmark run mnist
   ```

Artifacts (exports, checkpoints) are placed in `.tinytorch/` so the `milestone` and `community` commands can read them later. This path is created automatically the first time you run `tito`.

---

## 5. Testing & Quality Gates

| Scope | Command | Notes |
|-------|---------|-------|
| Root unit tests | `pytest` | Uses configuration under `[tool.pytest.ini_options]` in `pyproject.toml:130-162` |
| Book tooling | `pytest book/tests` | Validates Quarto helpers and YAML utilities |
| TinyTorch modules | `pytest tests` (inside `tinytorch/`) | Covers 600+ tests referenced in `tinytorch/README.md` |
| TinyTorch full suite (20 modules) | `cd tinytorch && .\.venv\Scripts\python.exe ./bin/tito module test --all --verbose` | Runs Phase 1–3 for Modules 01–20, streams WHAT/WHY context, and drops verbose logs under `docs/reports/moduleXX_<name>-test.log` plus the aggregate in `docs/reports/tinytorch-full-test-log.txt`. |
| Static analysis | `pre-commit run --all-files` | Hooks defined in `.pre-commit-config.yaml` (root and tinytorch) |
| Formatting | `black .` and `isort .` | Parameters also declared in `pyproject.toml:186-225` |
| Security | `bandit -r book/tools` | Excludes directories listed in `[tool.bandit]` |

**Release checks**: Run the maintenance preflight before publishing.

```powershell
python book/tools/scripts/maintenance/preflight.py   # entry point at book/tools/scripts/maintenance/preflight.py:40
```

Need proof that the full TinyTorch curriculum is green? Re-run the command above (UTF-8-safe on Windows because `tinytorch/bin/tito` now forces `PYTHONIOENCODING=utf-8`) and consult `docs/reports/tinytorch-test-summary.md` for the latest pass/fail snapshot plus troubleshooting notes.

---

## 6. Publishing & Release Routines

1. **Manual release (if CI is unavailable)**:
   ```bash
   cd book/tools/scripts/publish
   ./publish.sh
   ```
   The script (`book/tools/scripts/publish/publish.sh:1-150`) guides you through version bumps, build verification, GitHub releases, and CDN cache purges with a text UI.
2. **Automated release**: GitHub Actions (`.github/workflows/*`) mirror the same sequence. Always keep the manual instructions updated so they align with CI/CD.
3. **Artifacts**: HTML/PDF/EPUB end up in `book/quarto/_build/`, TinyTorch wheels appear under `tinytorch/dist/`, and kit/lab builds render inside their respective `_build/` folders.

---

## 7. Contribution Expectations

* **Keep documentation synchronized**: Any feature touching CLI routing, module interfaces, or Quarto templates must mention the change in:
  * `docs/ARCHITECTURE.md` (component traceability)
  * `docs/USERS.md` (if the user experience shifts)
  * `docs/DEVELOPERS.md` (this file) for new workflows
* **Reference source paths in commit messages** so others can jump straight to implementation files (`path:line`).
* **Prefer scripts over manual steps**: if you find yourself running more than three ad-hoc commands, encode them inside `book/tools/scripts` or a Makefile target.
* **CI mirrors local checks**: the `book`, `tinytorch`, and `kits` workflows call the same commands listed above. If something fails remotely, reproduce it locally with the documented steps instead of guessing.

---

## 8. Troubleshooting Cheatsheet

| Symptom | Diagnosis | Fix |
|---------|-----------|-----|
| `FileNotFoundError: _quarto.yml` | CLI cannot find the active config | Run `./binder switch html` (invokes `ConfigManager` to link `_quarto.yml` -> `config/_quarto-html.yml`) |
| `tito` complains about encoding on Windows | Console not forced to UTF-8 | Keep the shim `tinytorch/bin/tito` untouched; it sets `PYTHONIOENCODING` automatically |
| `pytest` slow when notebooks included | Coverage tries to scan generated folders | Ensure `_build/` paths stay in `.gitignore` and add them to `omit` under `[tool.coverage.run]` if needed |
| Quarto preview stuck | Another process holds the port | `Ctrl+C`, then run `quarto preview --port 4173 --no-browser` manually |
| TinyTorch module exports missing | `.tinytorch/` not created | Run `./bin/tito setup` once per machine to bootstrap the workspace |

---

## 9. First-Day Checklist

1. Install tools → verify with `git --version`, `python --version`, `quarto check`.
2. Clone repo → create `.venv` → `pip install -r requirements.txt && pip install -e .[dev]`.
3. Run `./binder setup && ./binder doctor`.
4. Run `./binder build` and open `_build/html/index.html`.
5. `cd tinytorch && ./bin/tito setup`.
6. Execute `./bin/tito module start 01` to prove the learning stack works.
7. Run `pytest` at the root and under `tinytorch/`.
8. Document anything you learn by updating the relevant section here.

Complete these steps and you will have a fully operational development environment with zero loose ends.
