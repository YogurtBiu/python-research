# analysis/processing-pipeline.md

# Processing Pipeline

## Purpose

This file defines rules for building reproducible scientific analysis pipelines.

The goal is to turn raw or standardized data into validated scientific outputs
through explicit, reviewable, and repeatable stages.

A good pipeline should make it clear:
- what data entered the workflow
- what checks were performed
- what transformations were applied
- what parameters controlled the analysis
- what outputs were produced

## Pipeline Structure

### Required
- Organize analysis into explicit stages:
  1. load
  2. validate
  3. clean
  4. transform
  5. analyze
  6. save
  7. plot or report
- Keep each stage conceptually distinct, even if some are implemented in the
  same module.
- Core analysis logic must be callable without notebook, GUI, or CLI
  dependencies.
- The same input data and parameters must reproduce the same outputs.

### Preferred
- Make stage boundaries visible in function names and module structure.
- Keep the pipeline entry point thin.
- Save important intermediate outputs when they are scientifically meaningful or
  expensive to regenerate.

### Forbidden
- One large script that loads data, cleans it, fits a model, plots, and exports
  everything in one procedural block
- Pipeline steps that depend on hidden manual edits
- Analysis logic that only exists inside a notebook cell sequence

## Validation Before Analysis

### Required
- Validate data structure before any fitting, segmentation, or plotting.
- Check required fields, shape, dtype, and identifier completeness.
- Define how NaN, inf, empty arrays, duplicated timestamps, or missing channels
  are handled.
- Reject physically impossible or structurally invalid inputs unless a
  scientifically justified exception exists.

### Preferred
- Separate structural validation from physical plausibility checks.
- Log or save validation results for batch workflows.
- Flag suspicious data even when the pipeline continues.

### Forbidden
- Running fitting or plotting before checking core assumptions
- Silently coercing invalid inputs into plausible-looking outputs
- Treating validation as optional for “small” analyses

## Cleaning and Transformation

### Required
- Make cleaning rules explicit and deterministic.
- Distinguish clearly between:
  - raw data
  - cleaned data
  - transformed data
- Document baseline subtraction, smoothing, interpolation, resampling,
  normalization, thresholding, and filtering rules.
- Record whether transformed data remains physically comparable to raw
  measurements.

### Preferred
- Keep cleaning and transformation functions separate when they serve different
  scientific purposes.
- Preserve original indices or coordinates when practical.
- Save parameter values used in transformations.

### Forbidden
- Undocumented smoothing
- Manual point deletion without recording it
- In-place modification of upstream data without documentation
- Mixing exploratory parameter tweaks into production pipeline code

## Analysis and Model Application

### Required
- Analysis functions must take explicit inputs and explicit parameters.
- Fitting, segmentation, classification, or feature extraction must declare the
  model or rule being applied.
- Numerical outputs must remain traceable to both data and parameter settings.
- Distinguish clearly between direct measurements and derived quantities.

### Preferred
- Return structured result objects for nontrivial analyses.
- Include fit diagnostics, residual metrics, convergence status, or quality
  indicators where relevant.
- Keep derived parameters and raw observables in separate fields.

### Forbidden
- Returning unlabeled tuples for complex results
- Using default parameters that materially affect conclusions without
  documenting them
- Overwriting raw observables with fitted or normalized values under the same
  name

## Configuration and Parameters

### Required
- Analysis parameters must come from config files, structured config objects, or
  explicit function arguments.
- Critical thresholds, fitting bounds, calibration constants, and normalization
  choices must not be hidden in scattered code.
- Saved outputs must be traceable to the parameter set used.

### Preferred
- Use dataclass, pydantic model, or structured dictionaries for pipeline
  configuration.
- Keep project-wide defaults in one place.
- Save parameter snapshots with final outputs.

### Forbidden
- Hardcoding critical scientific parameters in multiple scripts
- Adjusting thresholds manually in notebooks without recording them
- Mixing user-interface state with scientific configuration

## Batch Processing

### Required
- Repeated analyses must support batch mode.
- Batch pipelines must produce outputs that preserve sample identity.
- Failures in one dataset must not silently corrupt results from others.

### Preferred
- Save per-sample logs or status summaries.
- Support partial completion with explicit failure reporting.
- Provide a batch summary table for processed samples.

### Forbidden
- Copy-pasting one-sample scripts for each dataset
- Losing file-to-sample mapping during aggregation
- Stopping an entire large batch without indicating which input caused the
  failure

## Intermediate and Final Outputs

### Required
- Save outputs at scientifically meaningful boundaries.
- Final outputs must be traceable to:
  - source input
  - processing parameters
  - code or pipeline version
- Distinguish intermediate outputs from final publication-ready outputs.
- Save figure-ready data when figures are part of the scientific claim.

### Preferred
- Store intermediate outputs in dedicated directories such as
  `data_interim/`, `data_processed/`, and `results/`.
- Save machine-readable summaries for derived parameters.
- Save logs or run manifests for major pipeline executions.

### Forbidden
- Keeping important intermediate results only in memory
- Saving only figures without the underlying processed data
- Writing final scientific conclusions into ad hoc spreadsheets without
  provenance

## Reproducibility

### Required
- Pipelines must be rerunnable from code and input files.
- Outputs must not depend on manual GUI steps unless those steps are recorded
  and exportable.
- Randomized procedures must set and record seeds when they affect results.
- Version-sensitive outputs must record code version, environment, or pipeline
  version when relevant.

### Preferred
- Save a manifest containing input files, config, timestamps, and output paths.
- Use deterministic ordering in batch processing.
- Keep one command or entry point for each repeatable workflow.

### Forbidden
- Producing final results through undocumented manual intervention
- Depending on notebook execution order for production outputs
- Assuming that rerunning the same pipeline later will “probably” reproduce the
  same result

## Good Example

```python
from __future__ import annotations

from dataclasses import dataclass
from pathlib import Path

import pandas as pd


@dataclass
class DecayFitConfig:
    baseline_counts: float = 0.0
    min_points: int = 10


def validate_decay_table(df: pd.DataFrame) -> None:
    """
    Validate the structure of a decay dataset.

    Parameters
    ----------
    df : pd.DataFrame
        Input decay table. Must contain `time_ns` and `intensity_counts`.

    Raises
    ------
    ValueError
        If required columns are missing or the dataset is too short.
    """
    required_columns = {"time_ns", "intensity_counts"}
    missing_columns = required_columns - set(df.columns)
    if missing_columns:
        raise ValueError(
            f"Missing required columns: {sorted(missing_columns)}"
        )

    if len(df) < 10:
        raise ValueError("Decay dataset is too short for analysis.")


def clean_decay_table(
    df: pd.DataFrame,
    config: DecayFitConfig,
) -> pd.DataFrame:
    """
    Apply deterministic preprocessing to a decay dataset.

    Parameters
    ----------
    df : pd.DataFrame
        Validated decay table.
    config : DecayFitConfig
        Pipeline configuration.

    Returns
    -------
    pd.DataFrame
        Cleaned decay table with baseline-corrected intensity.
    """
    cleaned = df.copy()
    cleaned["intensity_corrected_counts"] = (
        cleaned["intensity_counts"] - config.baseline_counts
    )
    return cleaned


def run_decay_pipeline(
    input_path: Path,
    config: DecayFitConfig,
) -> pd.DataFrame:
    """
    Run a reproducible decay-analysis pipeline.

    Parameters
    ----------
    input_path : Path
        Path to the processed decay input table.
    config : DecayFitConfig
        Parameters controlling preprocessing.

    Returns
    -------
    pd.DataFrame
        Cleaned analysis-ready decay table.
    """
    df = pd.read_parquet(input_path)
    validate_decay_table(df)
    cleaned = clean_decay_table(df, config)
    return cleaned
```

## Bad Example

Problems:
- all stages are merged together
- validation is missing
- parameters are implicit
- side effects are mixed with analysis
- output is not traceable

```python
import pandas as pd
import matplotlib.pyplot as plt


def main(path):
    df = pd.read_csv(path)
    df["y"] = df["y"] - 100
    plt.plot(df["x"], df["y"])
    plt.show()
    df.to_csv("final.csv", index=False)
```

## Review Checklist

Before merging a pipeline, check:
- Are load, validate, clean, transform, analyze, and save steps explicit?
- Are all critical parameters visible and recorded?
- Can the pipeline be rerun on the same input and produce the same output?
- Are intermediate and final outputs clearly distinguished?
- Does batch mode preserve sample identity and failure context?
- Would another researcher be able to audit how a final figure or parameter was produced?
  
