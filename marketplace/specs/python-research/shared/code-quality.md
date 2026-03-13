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

## Documentation

### Required
- All public modules, classes, and functions must include NumPy-style docstrings.
- Docstrings must describe:
  - purpose
  - parameters
  - return values
  - units where relevant
  - expected shapes for arrays or tables where relevant
  - assumptions and limitations
  - raised exceptions when important

### Preferred
- Add examples for reusable analysis functions.
- Explain scientific meaning, not only programming behavior.

### Forbidden
- Missing docstrings on reusable public functions
- Comments that only repeat the code literally
- Undocumented unit assumptions

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

def normalize_decay(
    time_ns: np.ndarray,
    intensity_counts: np.ndarray,
    baseline_counts: float = 0.0,
) -> np.ndarray:
    """
    Normalize a decay trace after baseline subtraction.

    Parameters
    ----------
    time_ns : np.ndarray
        Time axis in nanoseconds. Must have the same shape as
        ``intensity_counts``.
    intensity_counts : np.ndarray
        Measured decay intensity in detector counts.
    baseline_counts : float, default=0.0
        Constant baseline to subtract before normalization.

    Returns
    -------
    np.ndarray
        Baseline-corrected and normalized decay intensity.

    Raises
    ------
    ValueError
        If the input arrays have inconsistent shapes or if the corrected
        signal has no positive maximum.

    Notes
    -----
    This function assumes that a constant baseline model is valid for the
    input decay trace.
    """
```

## Bad Example

```python
def do_it(x, y, z):
    y = y - z
    return y / max(y)

def normalize_decay(t, y, b=0):
    # normalize data
    return (y - b) / max(y - b)
```
