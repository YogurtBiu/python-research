# data/data-io.md

# Data I/O

## Purpose

This file defines rules for reading, standardizing, validating, and exporting
scientific data.

The goal is to make raw data ingestion reproducible, traceable, and consistent
across instruments, file formats, and projects.

Different instruments may produce different raw formats. Internal data
structures should be much more consistent.

## General Principles

### Required
- Preserve raw data in its original form.
- Separate file parsing from scientific interpretation.
- Standardize data structures early in the pipeline.
- Keep metadata and measurement values linked.
- Make all parsing assumptions explicit.

### Preferred
- Use one internal representation for each major data type:
  - `pandas.DataFrame` for tabular data
  - `xarray.DataArray` or `xarray.Dataset` for labeled multidimensional data
  - `numpy.ndarray` only when labels are unnecessary and shape is fixed
- Keep raw metadata in a structured object such as `dict`, dataclass, or model.
- Normalize units during the standardization stage, not later in scattered code.

### Forbidden
- Manually editing raw data files before loading
- Embedding file-format parsing logic in notebooks or plotting scripts
- Mixing parsing, cleaning, fitting, and plotting in one loader function
- Saving scientific results only as screenshots or clipboard content

## Loader Design

### Required
- Implement data loading in two layers:
  1. loader layer
  2. standardization layer
- The loader layer must read files as they are and preserve raw fields.
- The standardization layer must map raw fields into project-level names,
  units, and structures.
- Each instrument or raw format must have its own loader or parser entry point.

### Preferred
- Keep loader functions narrow in scope.
- Name loaders by source format or instrument type.
- Return both data and metadata from loaders when metadata is scientifically
  relevant.

### Forbidden
- A single generic loader that guesses too many unrelated file formats
- Hidden unit conversion in the raw loader stage
- Dropping metadata without documentation

## Input Validation

### Required
- Validate that required files, columns, headers, or channels exist.
- Validate file extension only as a weak check; validate actual structure too.
- Detect malformed rows, missing headers, invalid delimiters, or inconsistent
  column counts when relevant.
- Raise informative errors when parsing fails.

### Preferred
- Report which line, column, or file failed parsing when possible.
- Validate physical plausibility after structural parsing.
- Distinguish parsing failure from scientific invalidity.

### Forbidden
- Silently skipping malformed rows unless explicitly configured
- Replacing missing values without recording that behavior
- Continuing with partially parsed data when scientific meaning is uncertain

## Metadata and Units

### Required
- Keep metadata associated with the loaded dataset.
- Record source file path or source identifier.
- Preserve original units in metadata when unit conversion is applied.
- Use explicit internal units and field names after standardization.

### Preferred
- Include acquisition date, instrument mode, operator, sample id, and batch id
  when available.
- Store metadata in machine-readable form such as JSON-compatible structures.
- Record parsing or conversion notes in metadata.

### Forbidden
- Storing processed numerical columns without unit context
- Losing sample identity during merge, reshape, or export
- Mixing multiple unit systems in the same internal representation

## Missing Data and Invalid Values

### Required
- Define how missing values are represented after loading.
- Make NaN, empty strings, sentinel values, and invalid numbers explicit.
- Document whether invalid rows are removed, flagged, or preserved.

### Preferred
- Convert known placeholder values such as `-9999` to explicit missing values.
- Add a quality flag column when partial retention is scientifically useful.

### Forbidden
- Treating missing data as zero without documentation
- Dropping invalid rows silently
- Using different missing-data policies in different scripts for the same data
  type without documentation

## Internal Data Standardization

### Required
- Standardize column names and key variable names early.
- Use names that reflect physical meaning and units.
- Ensure repeated analyses use the same internal field names.
- Convert dates, timestamps, and categorical fields into consistent internal
  forms.

### Preferred
- Use project-wide canonical names such as:
  - `time_s`
  - `wavelength_nm`
  - `intensity_counts`
  - `voltage_V`
  - `current_nA`
  - `sample_id`
- Keep a mapping table from raw field names to internal field names when file
  formats are unstable.

### Forbidden
- Carrying vendor-specific raw column names throughout the whole analysis stack
- Using ambiguous names such as `x`, `y`, `signal`, or `value` in processed
  tables when better names are available

## Export Rules

### Required
- Save processed outputs in open or well-documented formats.
- Choose output formats appropriate for the data structure:
  - `parquet` or `csv` for tabular data
  - `json` or `yaml` for metadata and parameters
  - `npy` or `npz` for numerical arrays
- Make exported files reproducible from inputs and code.
- Save enough metadata to trace the output back to source data and parameters.

### Preferred
- Export both processed data and accompanying metadata.
- Use deterministic file naming.
- Save figure-ready tables separately when they differ from analysis-ready data.

### Forbidden
- Exporting important intermediate results only inside notebooks
- Writing ambiguous filenames such as `final.csv`, `new_data.xlsx`, or
  `result2.txt`
- Exporting processed data without date, sample, or processing-stage context

## File Naming

### Required
- Filenames for processed outputs must identify:
  - sample or dataset id
  - processing stage
  - date, batch, or run context when relevant
- Names must distinguish raw, intermediate, and processed outputs.

### Preferred
- Use deterministic names such as:
  - `sampleA_20260313_raw.csv`
  - `sampleA_20260313_cleaned.parquet`
  - `sampleA_decayfit_v2.json`

### Forbidden
- Names such as `final_final.csv`, `test_new2.xlsx`, `output.txt`
- Reusing the same filename for outputs produced by different parameter sets

## Good Example

```python
from __future__ import annotations

from pathlib import Path

import pandas as pd


def load_pl_csv(file_path: Path) -> tuple[pd.DataFrame, dict]:
    """
    Load a photoluminescence spectrum exported as CSV.

    Parameters
    ----------
    file_path : Path
        Path to the raw CSV file.

    Returns
    -------
    tuple[pd.DataFrame, dict]
        A tuple containing:
        - raw tabular data as loaded from the file
        - metadata describing source and parsing context

    Raises
    ------
    FileNotFoundError
        If the input file does not exist.
    ValueError
        If required columns are missing.
    """
    if not file_path.exists():
        raise FileNotFoundError(f"Input file not found: {file_path}")

    df = pd.read_csv(file_path)

    required_columns = {"Wavelength", "Intensity"}
    missing_columns = required_columns - set(df.columns)
    if missing_columns:
        raise ValueError(
            f"Missing required columns {sorted(missing_columns)} in {file_path}"
        )

    metadata = {
        "source_file": str(file_path),
        "raw_columns": list(df.columns),
        "original_wavelength_unit": "nm",
        "original_intensity_unit": "counts",
    }
    return df, metadata


def standardize_pl_dataframe(
    raw_df: pd.DataFrame,
    metadata: dict,
) -> tuple[pd.DataFrame, dict]:
    """
    Standardize raw photoluminescence data into project-level field names.

    Parameters
    ----------
    raw_df : pd.DataFrame
        Raw spectrum table with vendor or instrument-exported column names.
    metadata : dict
        Metadata associated with the raw file.

    Returns
    -------
    tuple[pd.DataFrame, dict]
        Standardized table and updated metadata.
    """
    standardized = raw_df.rename(
        columns={
            "Wavelength": "wavelength_nm",
            "Intensity": "intensity_counts",
        }
    ).copy()

    metadata = dict(metadata)
    metadata["internal_wavelength_unit"] = "nm"
    metadata["internal_intensity_unit"] = "counts"

    return standardized, metadata
```

## Bad Example

Problems:
- parsing logic and scientific interpretation are mixed
- source metadata is lost
- column assumptions are implicit
- units are undocumented
- output is not traceable
  
```python
import pandas as pd


def load_data(path):
    df = pd.read_csv(path)
    df.columns = ["x", "y"]
    df["y"] = df["y"] / 1000
    return df
```

## Review Checklist

Before merging a new loader or exporter, check:
- Does the loader preserve raw structure and source metadata?
- Are parsing and standardization separated?
- Are units explicit after standardization?
- Are missing values and malformed rows handled explicitly?
- Can the exported file be traced back to raw input and parameters?
- Would another researcher understand the data structure without opening the original instrument software?
