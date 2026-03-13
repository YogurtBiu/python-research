# index.md

# Python Research Spec Index

## Status

| Guideline | File | Status |
|---|---|---|
| Project Structure | shared/project-structure.md | Filled |
| Environment and Dependencies | shared/environment.md | Filled |
| Code Quality | shared/code-quality.md | Filled |
| Data I/O | data/data-io.md | Filled |
| Metadata and Units | data/metadata-units.md | Filled |
| Processing Pipeline | analysis/processing-pipeline.md | Filled |
| Visualization | analysis/visualization.md | Filled |
| CLI / GUI Tools | apps/cli-gui.md | Filled |
| Notebook Usage | notebooks/notebook-guidelines.md | Filled |
| Testing and Validation | testing/validation.md | Filled |
| Pre-Implementation Checklist | guides/pre-implementation-checklist.md | Filled |

## How to Use

- Read this index first.
- When starting a new project, follow `project-structure.md` and `environment.md`.
- When adding analysis logic, follow `processing-pipeline.md`.
- When plotting or exporting results, follow `visualization.md`.
- When building a small tool, follow `cli-gui.md`.
- When using notebooks, follow `notebook-guidelines.md`.
- Update spec files when a bug reveals a missing convention.

## Non-Goals

- This spec does not prescribe one fixed scientific library stack.
- This spec does not force packaging every project as a pip package.
- This spec does not ban notebooks.
- This spec does ban hidden, irreproducible, one-off analysis logic.
