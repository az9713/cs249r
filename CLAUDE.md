# CLAUDE.md — Operating Brief for AI Assistants

Use this document whenever an AI coding assistant (Claude, GPT, etc.) collaborates on `cs249r_book-dev`. It keeps the model aligned with the project’s expectations so responses stay trustworthy.

---

## 1. Repository Snapshot

| Stack | Location | Notes |
|-------|----------|-------|
| Book builder | `book/` (Quarto sources) | Commands routed through `book/cli/main.py:39-205` with format control managed by `book/cli/core/config.py:15-108`. |
| TinyTorch | `tinytorch/` | CLI shim `tinytorch/bin/tito` → dispatcher `tinytorch/tito/main.py`. Modules live in `tinytorch/src/*`. |
| Hardware docs | `kits/`, `labs/` | Render with Quarto just like the book. |
| Global tooling | Root `pyproject.toml`, `.pre-commit-config.yaml`, `book/tools/scripts/*`. |

Always point humans to the detailed references in `docs/ARCHITECTURE.md`, `docs/DEVELOPERS.md`, and `docs/USERS.md` before inventing new explanations.

---

## 2. Response Checklist

1. **Confirm scope**: restate the request in your own words.
2. **Cite real files**: refer to `path:line` (for example `book/cli/main.py:39`) so contributors can jump straight to code.
3. **Prefer documented workflows**: recommend commands that already exist (`./binder build`, `./bin/tito module start 01`, maintenance scripts). Only invent new commands if absolutely required.
4. **Warn about prerequisites**: remind users to activate `.venv` and install Quarto when tasks touch the book/kits/labs.
5. **Surface troubleshooting tips**: reuse the tables from `docs/DEVELOPERS.md` when errors arise.
6. **Stay ASCII**: avoid adding new emoji to prevent encoding issues on Windows terminals.

---

## 3. Safety Rails

* Never delete or overwrite generated assets manually—use `./binder clean`, `./bin/tito module reset`, or the scripts under `book/tools/scripts/maintenance/`.
* When editing docs, keep the “Documentation Map” in `README.md` consistent (update links if you move files).
* If you cannot find information locally, say so and ask for clarification rather than guessing.

Follow this brief every time you help on the repository to keep contributions consistent and debt-free.
