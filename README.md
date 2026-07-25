# ci-templates

Shared CI / code-quality templates for the research repositories, so the pipeline logic lives in
**one** place (DRY): fix it here → every repo benefits.

## What's here

| File | Purpose |
|---|---|
| `.github/workflows/python-quality.yml` | **reusable** workflow: `ruff` lint + format check, then `pytest` |
| `ruff.toml` | canonical lint/format rules (copy into each repo) |
| `pre-commit-config.yaml` | canonical pre-commit hooks (copy into each repo as `.pre-commit-config.yaml`) |

## Use it in a repo

**1. Caller workflow** — add `.github/workflows/ci.yml`:

```yaml
name: CI
on: [push, pull_request]
jobs:
  quality:
    uses: danielstaps/ci-templates/.github/workflows/python-quality.yml@main
    with:
      python-version: "3.11"
```

**2. Lint/format config** — copy `ruff.toml` into the repo (or merge into `pyproject.toml`).

**3. Local hooks** — copy `pre-commit-config.yaml` → `.pre-commit-config.yaml`, then:

```bash
pip install pre-commit
pre-commit install          # run automatically on every commit
pre-commit run --all-files  # one-off: format + lint the whole repo now
```

## What it enforces

- consistent formatting (ruff-format, black-compatible)
- sorted imports, **no unused imports** (ruff `I`, `F401`)
- modernized syntax (`pyupgrade`) and common bug patterns (`flake8-bugbear`)
- no committed venvs / large data dumps (`check-added-large-files`)
- passing smoke tests (`pytest`)
