# shared/code-quality.md

# Code Quality

## Purpose

This file defines code-quality rules for reusable scientific Python code.
The goal is not stylistic perfection, but clarity, traceability, and
reproducibility.

## Function Design

### Required
- Prefer small functions with one clear responsibility.
- Keep file I/O, visualization, and numerical logic separate.
- Make data transformations explicit rather than implicit.
- Validate critical inputs before analysis.
- Return structured objects instead of ambiguous tuples when the output has
  more than two fields.
- Keep reusable analysis functions deterministic for the same input and
  parameters.

### Preferred
- Use type hints on all public functions.
- Use `pathlib.Path` instead of raw path strings.
- Use `dataclass`, `TypedDict`, or pydantic models for configuration and
  structured outputs.
- Keep side effects at the pipeline boundary, not inside core analysis
  functions.

### Forbidden
- Functions that load files, clean data, fit a model, and save figures in one
  step
- Silent mutation of an input `DataFrame`, array, or config object unless
  clearly documented
- Hidden global state controlling algorithm behavior
- Undocumented unit conversion inside a general-purpose function

## Naming

### Required
- Use descriptive names based on scientific meaning.
- Include units in variable names when ambiguity is possible.
- Use names that reflect the actual data structure and physical quantity.

### Preferred
- Prefer names such as `time_ns`, `wavelength_nm`, `intensity_counts`,
  `voltage_V`, `current_nA`.
- Name intermediate variables by role, such as `baseline_corrected`,
  `normalized_intensity`, `fit_result`.

### Forbidden
- Names such as `a`, `b`, `tmp`, `temp2`, `data_new_final`
- Names that hide units or scientific meaning
- Reusing the same short variable for different physical quantities in one
  function

## Logging

### Required
- Use `logging` for pipeline progress, warnings, and recoverable issues.
- Log enough context to identify the file, sample, stage, or parameter set
  related to a warning or failure.

### Preferred
- Use `INFO` for major pipeline stages.
- Use `WARNING` for suspicious but nonfatal conditions.
- Use `ERROR` when execution cannot continue.

### Forbidden
- Using `print()` as the main mechanism for diagnostics in reusable code
- Logging messages without scientific or processing context

## Error Handling

### Required
- Raise informative errors with scientific context.
- State which file, column, unit, parameter, shape, or assumption failed.
- Fail early when input data violates physical or structural expectations.

### Preferred
- Raise domain-appropriate exceptions.
- Include expected and actual values when useful.

### Forbidden
- Silent fallback behavior that changes scientific meaning
- Catching exceptions broadly and continuing without explanation
- Error messages such as `something went wrong` or `invalid input`

## Documentation

### Required
- All public modules, classes, and functions must include NumPy-style
  docstrings.
- Docstrings must describe:
  - purpose
  - parameters
  - return values
  - units where relevant
  - expected shapes for arrays or tables where relevant
  - assumptions and limitations
  - raised exceptions when important
- Inline comments must explain scientific rationale, assumptions, or
  non-obvious implementation choices.

### Preferred
- Add examples for reusable analysis functions.
- Explain scientific meaning, not only programming behavior.
- Document whether inputs are copied or modified in place.

### Forbidden
- Missing docstrings on reusable public functions
- Comments that only restate the code literally
- Undocumented unit assumptions
- Comments used to compensate for unclear naming or poor function structure

## Good Example

```python
from __future__ import annotations

import numpy as np


def normalize_spectrum(
    wavelength_nm: np.ndarray,
    intensity_counts: np.ndarray,
    baseline_counts: float,
) -> np.ndarray:
    """
    Normalize a spectrum after constant-baseline subtraction.

    Parameters
    ----------
    wavelength_nm : np.ndarray
        Wavelength axis in nanometers. Must have the same shape as
        ``intensity_counts``.
    intensity_counts : np.ndarray
        Measured spectral intensity in detector counts.
    baseline_counts : float
        Constant baseline to subtract before normalization.

    Returns
    -------
    np.ndarray
        Baseline-corrected and normalized intensity array.

    Raises
    ------
    ValueError
        If the input arrays have inconsistent shapes, if baseline correction
        produces negative values, or if the corrected signal has zero maximum.

    Notes
    -----
    This function assumes that a constant baseline model is appropriate for
    the input spectrum.
    """
    if wavelength_nm.shape != intensity_counts.shape:
        raise ValueError(
            "wavelength_nm and intensity_counts must have the same shape."
        )

    corrected = intensity_counts - baseline_counts
    if np.any(corrected < 0):
        raise ValueError(
            "Baseline correction produced negative intensity values."
        )

    max_intensity = corrected.max()
    if max_intensity <= 0:
        raise ValueError(
            "Corrected intensity must contain at least one positive value."
        )

    return corrected / max_intensity


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
    if time_ns.shape != intensity_counts.shape:
        raise ValueError(
            "time_ns and intensity_counts must have the same shape."
        )

    corrected = intensity_counts - baseline_counts
    max_intensity = corrected.max()
    if max_intensity <= 0:
        raise ValueError(
            "Corrected decay must contain at least one positive value."
        )

    return corrected / max_intensity
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

## Review Checklist

Before merging reusable code, check:
- Does the function have one clear responsibility?
- Are units and scientific meaning explicit?
- Are inputs validated before analysis?
- Are side effects separated from core logic?
- Does the docstring describe assumptions and failure modes?
- Would another researcher understand and reuse this function in six months?
