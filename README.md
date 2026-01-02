# HaamuFx Backend

FastAPI-based backend for HaamuFx — a dApp that forecasts Aave v3 zkSync Health Factor risk, powers what-if simulations, and sends alerts.

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

## Local development (placeholder)

End-to-end dev workflow (FastAPI + Postgres + CI) will be filled in during **P0 Prep**.

For now, the plan is:

1. Create a FastAPI app under `app/` with a `/health` endpoint.
2. Add dependency management, linting, and tests.
3. Add `docker-compose.yml` for Postgres and local dev.

See `TASK_BOARD.md` for the detailed milestone plan.
