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
