# data/metadata-units.md

# Metadata and Units

## Principle

A result without units, acquisition conditions, and sample identity is incomplete.

## Required Metadata

Every processed dataset should be traceable to:
- sample id
- acquisition date or batch
- instrument / measurement mode
- operator or source file
- processing parameters
- code version or pipeline version

## Units

### Required
- Encode units in variable names when ambiguity exists.
- Use names like `time_s`, `voltage_V`, `current_nA`, `wavelength_nm`.

### Forbidden
- Mixing units silently
- Storing time in ms in one file and s in another without conversion
- Plot labels without units

## Conversion Rules

- Convert to project-standard units at the standardization layer.
- Document every conversion.
- Keep the original unit in metadata.

## Good Example

```python
df["time_s"] = df["time_ms"] / 1000.0
metadata["original_time_unit"] = "ms"
metadata["internal_time_unit"] = "s"
```

## Bad Example

```python
df["time"] = df["time"] / 1000
```

## Scientific Constants and Calibration

- Keep calibration constants in config files, not hardcoded across scripts.
- Each calibration must have a source and date.
