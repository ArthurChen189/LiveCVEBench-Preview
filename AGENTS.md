# Repository Guidelines

## Project Structure & Module Organization

- `tasks/LiveCVEBench/` and `tasks/PatchEval/` contain original benchmark tasks; their `-verified` counterparts contain maintained versions preferred for evaluation. Preserve originals for historical comparison.
- Each `cve-YYYY-NNNN/` task packages its environment and evaluation: `Dockerfile`, `docker-compose.yaml`, `task.yaml`, `solution.sh`, `run-tests.sh`, and `tests/`. Some tasks include `task-deps/` for supporting code and assets.
- `cve-sampler/` contains Python tools for reproducibility scoring and diverse monthly sampling. Read its `README.md` and `CVE_SELECTION_GUIDE.md` before changing selection logic.
- Root `README.md` documents Terminal Bench migration; `tasks/README.md` explains verification changes.

## Build, Test, and Development Commands

Use Docker and the Terminal Bench 1.0 workflow documented in the root README. Builds and dependencies are task-specific; there is no repository-wide build command.

- `uv tool install terminal-bench`: install the evaluation CLI; ensure the installed version meets the documented 1.0 requirement.
- `tb run --dataset-path tasks/LiveCVEBench-verified`: evaluate the verified LiveCVEBench suite.
- `tb run --dataset-path tasks/PatchEval-verified`: evaluate the verified PatchEval suite.

From `cve-sampler/`, with a local CVE List V5 checkout:

- `python cve_reproducer_filter.py --cves-dir ./cvelistV5/cves --latest 5000`: generate scoring summaries from a limited input set.
- `python run_monthly_sampling.py --months 2025-07 2025-11`: sample candidates using `output/summary.json`.

## Coding Style & Naming Conventions

Match neighboring files: four-space Python indentation, `snake_case` functions, and descriptive names. Keep task directory names lowercase (`cve-2025-1234`) and preserve harness filenames. Follow existing YAML indentation and shell conventions. No shared formatter or linter configuration is provided.

## Testing Guidelines

Tests are task-local, commonly using pytest and shell helpers. Use `test_*.py` files and `test_*` functions. Run each task's `run-tests.sh` inside its configured container with the harness-provided `TEST_DIR`, not directly on the host. Verify vulnerable and repaired behavior, including ordinary functionality. Prefer dynamic behavioral assertions over source-text matching. No repository-wide coverage threshold is defined.

## Commit & Pull Request Guidelines

History uses short, plain summaries such as `update tasks`; no formal commit convention is evident. Use specific imperative summaries identifying the suite or CVE. PRs should explain affected tasks, behavioral changes, validation commands and results, and related issues or CVEs.

## Environment & Benchmark Integrity

Use the standard `client` service and `T_BENCH_*` variables. Avoid host port mappings and prefix secondary container names to prevent collisions. Keep reference fixes and test artifacts out of agent-visible workspaces to prevent answer leakage.
