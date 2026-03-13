# shared/git.md

# Git Workflow

## Purpose

This file defines Git rules for scientific Python projects.

The goal is to keep history readable, protect the main branch, and make code
changes traceable to scientific intent.

## Branch Strategy

### Required
- `main` is the protected branch.
- Do not commit directly to `main`.
- All changes must be developed in topic branches.
- Branch names must indicate intent.

### Preferred
- Use branch prefixes such as:
  - `feat/...`
  - `fix/...`
  - `docs/...`
  - `refactor/...`
  - `test/...`
  - `chore/...`

### Forbidden
- Direct pushes to `main`
- Long-lived personal branches with unrelated changes mixed together
- Branch names such as `new`, `test2`, `temp`, `final`

## Commit Messages

### Required
- Use Conventional Commits format:
  - `<type>[optional scope]: <description>`
- The first line must describe what changed.
- One commit should represent one logical change.

### Preferred
- Use types such as:
  - `feat`
  - `fix`
  - `docs`
  - `refactor`
  - `test`
  - `chore`
- Add a scope when helpful, such as:
  - `fix(io): handle missing wavelength column`
  - `feat(pipeline): add batch export for fit summaries`

### Forbidden
- Messages such as:
  - `update`
  - `fix bug`
  - `modify files`
  - `final version`
- Mixing unrelated code, docs, and data changes in one commit

## Pull Requests

### Required
- Merge changes into `main` through pull requests.
- Pull requests must pass required status checks.
- Pull requests must describe the purpose of the change.

### Preferred
- Keep pull requests focused and reviewable.
- Explain scientific impact when results, parameters, or analysis behavior
  change.
- Link the pull request to the relevant issue, task, or bug when available.

### Forbidden
- Large unreviewable pull requests mixing many unrelated changes
- Merging failing checks
- Squashing unrelated work into one unclear PR

## Scientific Data Protection

### Required
- Raw data directories such as `data_raw/` are immutable.
- Do not modify raw data files in Git-tracked history.
- Generated results should not be committed unless they are intentionally
  versioned outputs.

### Preferred
- Track scripts, configs, and metadata rather than bulky regenerated outputs.
- Keep `.gitignore` aligned with caches, temporary files, figures under review,
  and local environments.

### Forbidden
- Editing raw instrument exports in place
- Committing temporary files, IDE state, or notebook checkpoints
- Using Git history as a substitute for data provenance documentation

## Enforcement

### Required
- Use local hooks for fast checks before commit.
- Use remote branch protection for main-branch enforcement.
- Required checks must run in CI before merge.

### Preferred
- Run formatting, linting, and lightweight tests in `pre-commit`.
- Validate commit-message format in `commit-msg`.
- Enforce protected-branch rules in GitHub.

### Forbidden
- Relying only on verbal rules
- Assuming local checks cannot be bypassed
- Allowing force-push to protected branches in normal workflows

## Good Example

- Branch: `fix/io-metadata-loss`
- Commit: `fix(io): preserve original wavelength unit in metadata`
- PR: focused on one loader bug, with passing checks and a clear description

## Bad Example

- Branch: `test`
- Commit: `update`
- Direct push to `main`
- One commit containing refactor, bug fix, notebook output, and generated figures

## Review Checklist

Before merging, check:

- Is the branch purpose clear from its name?
- Does each commit describe one logical change?
- Does the commit message follow the project format?
- Does the PR explain scientific or technical impact?
- Are protected files and raw data untouched?
- Did all required checks pass?
