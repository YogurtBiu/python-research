# apps/cli-gui.md

# CLI / GUI Tools

## Scope

This file applies to:
- CLI tools for batch processing
- small PySide6 desktop utilities
- small Streamlit dashboards for inspection and annotation

## Architecture Rule

Use this separation:

- `core`: data model and analysis logic
- `service`: file access / orchestration
- `ui`: CLI / GUI presentation

UI must call core functions.
Core functions must not depend on UI libraries.

## CLI Rules

- Every CLI command should correspond to a clear analysis action.
- Arguments must be explicit and documented.
- Long-running tasks should log progress.
- Save outputs to disk instead of only printing to console.

## GUI Rules

- GUI is for inspection, annotation, parameter entry, and batch trigger.
- GUI should not contain the only copy of analysis logic.
- GUI actions should create reproducible outputs or config files.
- Avoid storing critical state only in widget memory.

## Good Example

A PySide6 app loads a file, lets the user choose a threshold, then calls `run_segmentation(data, config)` from `src/project_name/analysis/`.

## Bad Example

A button callback contains 300 lines of data parsing, thresholding, plotting, and export logic.
