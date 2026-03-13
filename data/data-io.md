
# data/data-io.md

# Data I/O

## Principle

Input formats differ across instruments.
Internal representations should not.

## Required Pattern

Implement I/O in two layers:

1. Loader layer:
   - reads vendor / csv / txt / excel / image / binary files
   - preserves raw columns and metadata
2. Standardization layer:
   - maps raw fields into project-level standard names and structures

## Rules

- Each instrument format gets its own loader.
- Never scatter file parsing logic across notebooks and scripts.
- Standardize column names early.
- Preserve original metadata separately.
- Explicitly record skipped rows, missing values, and parse assumptions.

## Internal Data Standard

Prefer one of:
- pandas DataFrame for tabular data
- xarray DataArray / Dataset for labeled multidimensional data
- numpy arrays only when labels are unnecessary and shape is fixed

## File Naming

Processed outputs should include:
- sample id
- date or batch id
- processing stage
- optional parameter hash or version

Example:
`sampleA_20260313_cleaned.parquet`
`sampleA_fit_v2.json`

## Preferred Output Formats

- tabular: parquet or csv
- structured metadata: json or yaml
- numerical arrays: npy / npz
- figures: png for review, pdf/svg for publication when needed

## Forbidden

- Repeated manual copying from Excel into code
- Writing intermediate data only into clipboard or notebook variables
- Saving processed scientific data as screenshots
