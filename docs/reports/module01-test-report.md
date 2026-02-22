# Module 01 (`01_tensor`) Test Report
**Test date:** 22 February 2026  
**Environment:** Windows 11 (Git Bash & PowerShell), Python 3.13 inside project `.venv`

## Goals
1. Verify the Module 01 tensor implementation using TinyTorch’s three-phase test harness.
2. Ensure the pytest-powered Module Tests (Phase 2) run with the `--tinytorch` flag without manual intervention.
3. Capture any issues encountered and document the remediation steps.

## Commands Executed
| Step | Command |
|------|---------|
| Activate repo venv | `source ../.venv/Scripts/activate` (Git Bash) |
| Install TinyTorch package for pytest plugin | `pip install -e .[dev]` |
| Export notebooks → package (`tinytorch/core/*`) | `python ./bin/tito dev export --all` |
| (Alternate) Single module export when debugging | `python ./bin/tito dev export 01` |
| Phase 1–3 run | `python ./bin/tito module test 01 -v` |
| Direct pytest reproduction | `python -m pytest tests/01_tensor --tinytorch --no-cov -q` |

> **Note:** When running the TinyTorch CLI outside the repo venv, set `PATH` to include `.\.venv\Scripts` and force UTF-8 output via `PYTHONIOENCODING=utf-8` to avoid the `cp1252` encoding errors in Rich output.

## Results Summary
| Phase | Description | Result |
|-------|-------------|--------|
| 1 | Inline module sanity checks embedded in `01_tensor.py` | ✅ Pass |
| 2 | Pytest suite (`tests/01_tensor/*`) with `--tinytorch` WHAT/WHY overlay | ✅ Pass (after registering CLI flag in `conftest.py`) |
| 3 | Integration verification (Module 01 → 01) | ✅ Pass |

### Observed Console Output (abridged)
```
Phase 1 PASSED: Inline unit tests
Phase 2 PASSED: Module tests
Phase 3 PASSED: Integration tests
```

## Issues Encountered & Fixes
| Issue | Impact | Resolution |
|-------|--------|------------|
| `pytest: error: unrecognized arguments: --tinytorch` | Phase 2 failed before collection | Registered the `--tinytorch` flag inside `tinytorch/tests/conftest.py` via `pytest_addoption`, and wired config flags to enable/disable the Rich reporter. |
| CLI refused to run (`TinyTorch must run inside a virtual environment`) when using system Python | Could not export modules or run tests | Prepended `PATH` with `.\.venv\Scripts` and executed commands via `..\.\.venv\Scripts\python.exe ...` so TinyTorch recognizes the active venv. |
| Pytest couldn’t import `tinytorch.core.tensor` (`PACKAGE NOT EXPORTED`) | Collection aborted | Ran `python ./bin/tito dev export --all` to regenerate notebooks and the `tinytorch/core/*.py` exports. |
| Rich console `UnicodeEncodeError` when printing ❌/box characters | Cosmetic crash after success | Forced UTF-8 output with `PYTHONIOENCODING=utf-8` when invoking CLI/pytest from PowerShell. |
| Final CLI traceback `TypeError: sequence item 1: expected str instance, NoneType found` | CLI exits non-zero despite successful phases | Issue logged for follow-up (TinyTorch CLI bug: attempts to `"\n".join(all_output)` when one entry is `None`). Tests themselves completed successfully before this exception. |

## Follow-Up Recommendations
1. **Fix CLI log joiner** – guard against `None` entries before calling `"\n".join(...)` in `ModuleTestCommand.test_module`.
2. **Document UTF-8 requirement** – add explicit note in TinyTorch CLI docs to set `PYTHONIOENCODING=utf-8` on Windows terminals to avoid Rich encoding errors.
3. **Add CI smoke test** – run `tito module test 01 --unit-only` in CI to ensure the pytest flag registration remains intact.
4. **Expose `tito dev export` status** – after exports, confirm `tinytorch/core/*` exists and perhaps warn if a module stays missing to prevent the `PACKAGE NOT EXPORTED` confusion.

## Conclusion
Module 01’s implementation is verified end-to-end. The tensor behaves identically to NumPy for the tested operations, and Tensor memory-analysis instrumentation works as expected. Remaining work is limited to CLI ergonomics (UTF-8 logging and the final-string `TypeError`), which do not affect test correctness.
