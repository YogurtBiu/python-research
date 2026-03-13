# guides/pre-implementation-checklist.md

# Pre-Implementation Checklist

Before writing code for a new analysis task, answer these questions:

## Data
- What is the raw input format?
- Which fields are mandatory?
- Which metadata matters scientifically?
- What are the canonical units?

## Output
- What is the final output: figure, table, fitted parameters, mask, or app state?
- Does the output need to be reproducible in batch mode?
- What intermediate files should be saved?

## Validation
- What values are physically impossible?
- What missing-data behavior is acceptable?
- Which assumptions must be logged?

## Architecture
- Which part belongs in `src/`?
- Which part is only a script entry point?
- Does this need a CLI, a notebook, or a GUI?

## Reuse
- Is there already a loader, normalizer, plotting helper, or fit function for similar data?
- Should this become a general utility or remain project-specific?

## Long-Term
- Will this analysis be rerun on future batches?
- Could another student reproduce this in six months?
- If the figure is questioned later, can we trace it back to raw data and parameters?
