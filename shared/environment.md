# shared/environment.md

# Environment and Dependencies

## Python Version

- Use Python 3.11 or newer unless instrument software or legacy dependencies prevent it.
- Pin the minimum version in `pyproject.toml`.

## Dependency Management

- Manage dependencies in `pyproject.toml`.
- Separate runtime and development dependencies.
- Add a package only if it reduces real complexity.
- Prefer standard library for filesystem, logging, pathlib, csv, json, argparse-level tasks.

## Required Tooling

- `ruff` for linting and formatting
- `pytest` for tests

## Reproducibility Rules

- Record package versions for published or archived analysis.
- For figure-producing pipelines, keep a frozen environment file or lockfile.
- Log the code version or git commit when saving final results.

## Forbidden

- Global reliance on local IDE interpreter state
- Hidden dependencies installed manually but not declared
- Importing from sibling scripts by hacking `sys.path`

## Good Example

Store runtime dependencies in `pyproject.toml`, create a CLI command, and run the analysis in a clean environment.

## Bad Example

Running a notebook on one machine with five manually installed packages and no version record.
