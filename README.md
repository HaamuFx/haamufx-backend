# HaamuFx Backend

FastAPI-based backend for HaamuFx; a dApp that forecasts Aave v3 zkSync Health Factor risk, powers what-if simulations, and sends alerts.

This repo corresponds to the **backend** component described in `TASK_BOARD.md`.

## Scope

- Exposes read-only APIs for:
  - Aave v3 zkSync positions and Health Factor (HF)
  - What-if simulations (repay / add collateral)
  - Risk forecasts (24h / 7d)
  - Alerts management
- Uses Postgres for persistence (alerts, user settings, cached data).
- No private key custody and no transaction automation.

## Project structure (initial)

- `TASK_BOARD.md` – narrative roadmap and milestones
- `app/` – backend application code (FastAPI, services, models)
  - `api/` – API route definitions
  - `core/` – config, settings, and shared utilities
  - `models/` – Pydantic/ORM models
  - `services/` – integration logic (Aave, price feeds, forecasting, alerts)
- `tests/` – test suite

This structure will evolve as features are implemented.

## Local development

End-to-end dev workflow (FastAPI + Postgres + CI) is being built out during **P0 Prep**, but you can already work inside the repo using [uv](https://github.com/astral-sh/uv) for dependency management.

### Prerequisites

- Python 3.11+
- uv installed (e.g., `pip install uv` or download from the releases page)

### Install dependencies

```bash
uv sync --extra dev
```

This creates/updates `.venv/` with runtime dependencies plus the dev tooling stack (`ruff`, `mypy`, `pre-commit`).

### Common commands

```bash
# Format & lint
uv run ruff format app tests
uv run ruff check app tests

# Type checking
uv run mypy app

# (Once FastAPI routes exist) run the dev server
uv run uvicorn app.main:get_app --reload
```

### Git hooks

Pre-commit hooks keep formatting/lint/type checks consistent:

```bash
uv run pre-commit install
uv run pre-commit run --all-files  # optional manual run
```

### Roadmap reference

See `TASK_BOARD.md` for the detailed milestone plan (P0–P6).
