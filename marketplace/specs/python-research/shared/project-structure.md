# shared/project-structure.md

# Project Structure

## Required Directory Layout

Use this structure for medium-sized research projects:

project_root/
- pyproject.toml
- README.md
- .gitignore
- configs/
- data_raw/
- data_interim/
- data_processed/
- results/
- notebooks/
- scripts/
- src/project_name/
- tests/

## Rules

### Raw Data
- `data_raw/` is read-only.
- Never overwrite files in `data_raw/`.
- Never manually edit raw instrument exports.

### Derived Data
- `data_interim/` stores temporary cleaned or merged outputs.
- `data_processed/` stores final analysis-ready data.
- `results/` stores figures, tables, reports, and logs.

### Source Code
- Put reusable logic in `src/project_name/`.
- Do not put core logic only in notebooks.
- Do not put complex business logic in `scripts/`.

### Scripts
- `scripts/` is for thin entry points only.
- A script may parse arguments, load config, and call functions in `src/`.
- A script must not contain the only implementation of a core algorithm.

## Recommended Package Layout

src/project_name/
- io/
- preprocessing/
- analysis/
- models/
- visualization/
- utils/
- app/

Not all folders are required.

## Good Example

- `src/project_name/io/load_csv.py`
- `src/project_name/analysis/fit_decay.py`
- `scripts/run_decay_fit.py`

## Bad Example

- `final_version2_really_final.py`
- `notebooks/analysis_all_steps.ipynb` containing all data cleaning, fitting, and plotting logic
- figures saved into the project root without metadata
