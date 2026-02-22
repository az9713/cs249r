# Module 12_attention - Attention Mechanisms Test Report
**Test date:** 22 February 2026\n
## Environment
- Windows 11 (Git Bash + PowerShell)
- Project virtual environment: `.venv` (Python 3.13)
- Notebooks exported via `tito dev export --all` before testing
- Environment overrides: `PYTHONIOENCODING=utf-8`, `PYTHONUTF8=1`, PATH includes `.venv/\Scripts`\n
## Commands Run
1. `python ./bin/tito module test 12_attention -v`
2. `python -m pytest tests/12_attention --tinytorch --no-cov`\n
## Results
| Phase | Description | Status |
|-------|-------------|--------|
| 1 | Inline sanity checks | ✅ Pass |
| 2 | Pytest suite with `--tinytorch` overlay | ✅ Pass |
| 3 | Integration coverage up to this module | ✅ Pass |\n
## Key Learnings
- Multi-head attention gradient checks passed without numerical drift after full export.
- Regression tests for KV-cache prep (Module 18) depend on this module; the chain works end-to-end.

## Issues / Follow-ups
- None specific to this module; see overall test summary for CLI/milestone action items.

## Verification Artifacts
- TinyTorch CLI Phase 1-3 log captured locally during `tito module test` run.
- Pytest transcript for `tests/12_attention/` included in the full-suite log.
- No extra files generated; rerun commands above to reproduce.\n