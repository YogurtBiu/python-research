# analysis/processing-pipeline.md

# Processing Pipeline

## Pipeline Stages

Use explicit stages:

1. load
2. validate
3. clean
4. transform
5. analyze / fit / segment / extract features
6. save outputs
7. plot / report

Do not merge all stages into one large function.

## Required Rules

- Validation happens before fitting or plotting.
- Cleaning rules must be deterministic.
- Analysis parameters must come from config or explicit function arguments.
- The same input and config should reproduce the same output.

## Validation Checklist

Before analysis, check:
- required columns exist
- dtypes are valid
- shape is expected
- NaN / inf policy is explicit
- value ranges are physically plausible
- sample identifiers are not missing

## Pure Analysis Functions

Core algorithms should be pure where possible:
- input arrays / tables in
- result object out
- no hidden file I/O
- no GUI dependencies

## Good Example

```python
def run_pipeline(input_path: Path, config: FitConfig) -> FitResult:
    raw = load_measurement(input_path)
    validated = validate_measurement(raw, config)
    cleaned = clean_measurement(validated, config)
    return fit_measurement(cleaned, config)
```

## Bad Example

```python
def main():
    # reads files, modifies globals, draws plots, asks user for values,
    # runs fit, writes excel, and opens window
    ...
```

## Batch Processing

- Support directory-level processing whenever datasets are repeated or comparable.
- One-sample interactive scripts are acceptable only for exploratory work.
- Production analysis should support batch mode.
