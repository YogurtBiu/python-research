# notebooks/notebook-guidelines.md

# Notebook Guidelines

## Principle

Notebooks are allowed for exploration, explanation, and quick validation.
They are not the final home of core logic.

## Allowed Uses

- exploratory analysis
- trying fit strategies
- visual inspection
- teaching / demonstration
- assembling narrative figures from already-tested functions

## Required Rules

- Import reusable logic from `src/`.
- Keep notebook cells linear and restartable.
- At the top, state input files and purpose.
- Before publication or formal reporting, migrate stable logic into `src/` or `scripts/`.

## Forbidden

- The only implementation of a key algorithm living in a notebook
- Hidden out-of-order execution dependencies
- Manually editing intermediate variables to get the “right” figure without recording it

## Good Example

A notebook compares several smoothing methods by calling imported helper functions and records conclusions.

## Bad Example

A notebook with 80 cells where raw data cleaning, fitting, and final plots depend on cell execution order and manual edits.
