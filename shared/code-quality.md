# shared/code-quality.md

# Code Quality

## Function Design

### Required
- Prefer small functions with one clear responsibility.
- Keep I/O separate from numerical logic.
- Make data transformations explicit.
- Return structured objects, not ambiguous tuples, when output has more than two fields.

### Preferred
- Use type hints on public functions.
- Use `pathlib.Path` instead of raw path strings.
- Use dataclass or pydantic model for configuration and structured outputs.

### Forbidden
- Functions that both load files, clean data, fit a model, and save figures
- Silent mutation of input DataFrame unless clearly documented
- Hidden global state controlling algorithm behavior

## Naming

- Use descriptive names based on scientific meaning.
- Prefer `time_ns`, `wavelength_nm`, `intensity_counts`.
- Avoid `a`, `b`, `temp2`, `data_new_final`.

## Logging

- Use `logging` for pipeline progress and warnings.
- Use `print()` only for short CLI user feedback.

## Error Handling

- Raise informative errors with scientific context.
- Mention which file, column, unit, or parameter failed validation.

## Good Example

```python
def normalize_spectrum(
    wavelength_nm: np.ndarray,
    intensity_counts: np.ndarray,
    baseline_counts: float,
) -> np.ndarray:
    corrected = intensity_counts - baseline_counts
    if np.any(corrected < 0):
        raise ValueError("Baseline correction produced negative intensity values.")
    return corrected / corrected.max()
```

## Bad Example

```python
def do_it(x, y, z):
    y = y - z
    return y / max(y)

```
