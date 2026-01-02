# Contributing to HaamuFx Backend

This project is currently a solo / small-team MVP. The guidelines below keep changes predictable and easy to reason about.

## Workflow

- Use GitHub Issues for tasks, aligned with phases in `TASK_BOARD.md`.
- Work in feature branches and open Pull Requests into `main`.
- Keep PRs small and focused on a single concern.

## Code style

- Python backend uses FastAPI.
- Prefer type hints everywhere; keep functions small and focused.
- Follow the configured formatter/linter (to be finalized in P0):
  - Formatter: e.g. `black` or `ruff format`.
  - Linter: e.g. `ruff`.

## Tests

- Add or update tests under `tests/` for any non-trivial change.
- For model/forecasting changes, add at least one sanity-check test.

## Documentation

- Update `README.md` and/or `TASK_BOARD.md` when behavior or scope changes.
- If adding a new API endpoint, ensure it is documented via FastAPI/OpenAPI and, ideally, with an example payload.

These guidelines will be refined as the project matures.
