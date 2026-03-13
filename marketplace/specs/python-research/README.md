# README.md

# Python Research Development Guidelines

Development guidelines for Python projects used in:
- scientific data processing
- reproducible analysis pipelines
- small desktop / web tools for data inspection and preprocessing

## Scope

This spec is for projects where Python is used to:
1. read and clean raw experimental data
2. transform data into structured analysis-ready tables / arrays
3. generate figures, reports, or intermediate results
4. build small utilities such as CLI tools, PySide6 apps, or Streamlit dashboards

## Core Principles

- Reproducibility over convenience
- Explicit metadata over hidden assumptions
- Batch pipeline over manual clicking
- Small pure functions over long procedural scripts
- Validation before plotting
- Raw data is immutable
- Every saved result must be traceable to input + parameters + code version

## Hard Rules

1. No core analysis logic lives only in notebooks.
2. Raw data is immutable.
3. Every exported figure must be regenerable from code.
4. Every fitted parameter must be associated with:
   - input file
   - config
   - fit method
   - code version
5. Units must be explicit in variable names or metadata.
6. Batch processing is the default for repeatable experiments.
7. GUI tools are wrappers around reusable core logic, not the logic itself.
8. Add one regression test for every bug that once changed a scientific result.
9. All reusable scientific code must be documented with NumPy-style docstrings.

## Recommended Stack

- Core: Python 3.11+
- Numerical: numpy, scipy
- Tabular data: pandas
- Labeled array / multidimensional data: xarray
- Plotting: matplotlib
- Configuration: pydantic-settings / dataclass / yaml
- CLI: typer or argparse
- Testing: pytest
- Lint / format: ruff
- Packaging: pyproject.toml

Do not require all tools in all projects.
Choose the smallest stack that fits the problem.
