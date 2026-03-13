# analysis/visualization.md

# Visualization

## Principle

Plots are scientific outputs, not decoration.

## Required

- Axis labels must include quantity and unit.
- Titles should identify sample / condition, not restate the axis.
- Color meaning must be consistent across a project.
- Save figure-generation code, not only final images.
- Separate exploratory plots from publication plots.

## Figure Saving Rules

When saving final figures:
- save source data or a plotting-ready table
- save plotting parameters if nontrivial
- use deterministic filenames

Example:
- `results/figures/sampleA_pl_spectrum.png`
- `results/figures_data/sampleA_pl_spectrum.csv`

## Plotting Conventions

- Use matplotlib as default baseline.
- Prefer object-oriented matplotlib API.
- Set physical ranges intentionally.
- Do not smooth data unless documented.
- Do not crop out inconvenient regions without stating it.

## Good Example

```python
fig, ax = plt.subplots()
ax.plot(df["wavelength_nm"], df["intensity_counts"])
ax.set_xlabel("Wavelength (nm)")
ax.set_ylabel("Intensity (counts)")
ax.set_title(f"Sample {sample_id}")
fig.savefig(output_path, dpi=300, bbox_inches="tight")
```

## Bad Example

- No axis labels
- Manual recoloring in presentation software
- Screenshots pasted from notebook output
- Different samples plotted with inconsistent normalization without note
