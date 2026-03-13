# testing/validation.md

# Testing and Validation

## Principle

For research code, “works on my dataset” is not enough.

## Test Layers

### Unit Tests
Test small pure functions:
- unit conversion
- baseline subtraction
- peak finding on synthetic arrays
- filename parsing
- metadata normalization

### Integration Tests
Test whole pipelines on small representative datasets:
- load -> clean -> analyze -> save
- one test file per important instrument format

### Scientific Validation
Use known or synthetic cases:
- expected peak position
- expected decay constant
- conservation or monotonicity constraints
- shape / dimension invariants

## Required

- Add tests for every bug that could silently alter scientific conclusions.
- Test edge cases: NaN, empty arrays, negative values, missing columns, duplicated timestamps.
- Use tolerance-based assertions for floating-point comparisons.

## Good Example

```python
def test_ms_to_s_conversion():
    df = pd.DataFrame({"time_ms": [0, 500, 1000]})
    out = convert_time(df)
    assert np.allclose(out["time_s"], [0.0, 0.5, 1.0])
```

## Bad Example

- Only testing that the script runs without crashing.
