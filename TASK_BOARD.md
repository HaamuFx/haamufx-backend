# HaamuFx Task Board

_HaamuFx : Health Factor forecasting + alerts for Aave v3 on zkSync_

## Goal (North Star MVP)

A live web app where a user can:

1. Connect wallet → see Aave v3 zkSync positions + current Health Factor (HF)
2. See probabilistic forecasts of crossing risk bands (e.g., HF < 1.2) over 24h / 7d
3. Run what-if actions (repay / add collateral) and see forecast risk change
4. Configure alerts → receive notifications when risk thresholds are exceeded

**Non-goals (MVP):**

- No automated execution of funds (no autopilot transactions)
- No custody / no private key storage
- No token / no monetization required

---

## Repos / Components

- `haamufx-frontend` (Next.js UI)
- `haamufx-backend` (FastAPI + forecasting + alert scheduler)
- `haamufx-contracts` (optional: tiny on-chain RiskState contract)

---

## Suggested GitHub workflow (recommended)

- Use **Issues** for each task below.
- Use **Milestones** matching phases: `P0 Prep`, `P1 Position Viewer`, ...
- Use labels:
  - `frontend`, `backend`, `contracts`, `infra`, `data`, `model`, `alerts`, `ux`, `docs`, `security`
  - `priority:p0/p1/p2`, `size:xs/s/m/l`
- Use a **GitHub Project (Projects v2)**:
  - Columns: Backlog / Ready / In progress / Review / Done
  - Fields: Phase, Priority, Size, Repo, Due week

> Tip: Keep this file as the narrative plan, but track progress in Issues/Project.

---

## Definition of Done (DoD)

A task is “Done” when:

- [ ] Code merged to main
- [ ] Basic tests/lint pass in CI
- [ ] Deployed (if user-facing) or runnable via `make dev` / `docker compose up`
- [ ] Minimal docs updated (README or this file)
- [ ] For model changes: includes a sanity check + a short note on assumptions

---

# Milestones / Phases

## P0 — Prep (Days 1–3)

**Outcome:** repos created, CI running, deployments wired.

### Infra / Repo setup

- [x] Create repos (frontend/backend/contracts optional)
- [x] Add README skeletons + local dev instructions
- [x] Add formatting + linting + type checks (pre-commit optional)
- [x] Add GitHub Actions CI for lint/test on PR
- [ ] ~~Choose hosting:~~
  - [ ] ~~Frontend deploy (Vercel)~~
  - [ ] ~~Backend deploy (Render/Fly/Hetzner/etc.)~~
  - [ ] ~~Postgres provisioned~~
- [ ] Local-first milestone before hosting:
  - [ ] Have at least one FastAPI endpoint worth demoing (`/health` or `/positions/{address}`)
  - [ ] Have at least one UI page worth showing (simple dashboard stub is fine)
  - [ ] Document local run commands: `uv run uvicorn app.main:get_app --reload`, `docker compose up db`, `npm run dev` (once frontend exists)
- [ ] Local dev environment hardening:
  - [ ] Add `docker-compose.yml` with Postgres service for local use
  - [ ] Add `.env.example` with `DATABASE_URL=postgresql://...` (and other required vars)
  - [ ] Note VS Code helpers: Python + Pylance + Ruff, Docker, Thunder Client/REST Client, SQLTools (or external DB client)
- [ ] Concrete secrets management:
  - [ ] Keep `.env.example` up to date with all required variables (local + future deploy)
  - [ ] Document how to load secrets locally (e.g., `cp .env.example .env`) and note that CI should NOT require secrets yet
  - [ ] Add a reminder checklist for future hosting: where each env var will live (Vercel/Render/etc.) once accounts exist

### Skeleton apps

- [ ] Frontend: Next.js app boots and deploys
- [ ] Backend: FastAPI health endpoint `/health` deployed
- [ ] Optional: Docker Compose for local dev (frontend+backend+db)

**Acceptance**

- [ ] Visiting the deployed frontend shows a landing page
- [ ] Backend `/health` returns OK in prod

---

## P1 — Position Viewer MVP (Week 1)

**Outcome:** connect wallet → display correct Aave v3 zkSync position + HF.

### Frontend

- [ ] Wallet connect (WalletConnect) + network detection (zkSync)
- [ ] Dashboard page scaffold `/dashboard`
- [ ] Show connected address + chain
- [ ] Call backend `/positions/{address}` and render results

### Backend

- [ ] Implement `/positions/{address}`:
  - [ ] Read Aave v3 user account data (collateral, debt, HF)
  - [ ] Per-asset breakdown (supplied/borrowed) with correct decimals
- [ ] Add caching (per address, short TTL)
- [ ] Add basic error handling (unsupported chain, invalid address)

### QA

- [ ] Cross-check values vs Aave UI (manual test for at least 1 wallet)
- [ ] Unit tests for decimal handling and HF parsing

**Acceptance**

- [ ] The UI shows HF and totals matching Aave UI within rounding tolerance
- [ ] Response time < 2s (cached) for same address

---

## P2 — What-if Simulator (Week 2)

**Outcome:** actionable “repay/add collateral” simulation with instant HF recompute.

### Backend

- [ ] Implement `/simulate` endpoint:
  - Inputs: address, hypothetical repay amount, add collateral amount (token + qty)
  - Output: new HF + delta vs baseline + target solver helpers
- [ ] Implement “solve” helpers:
  - [ ] “Repay X to reach target HF”
  - [ ] “Add collateral Y to reach target HF”

### Frontend

- [ ] Simulator UI `/simulate`:
  - [ ] Inputs/sliders for repay/add collateral
  - [ ] Display baseline HF and simulated HF
  - [ ] Show “recommended action” for target HF presets (e.g., 1.3 / 1.5 / 2.0)

**Acceptance**

- [ ] User can try scenarios and see HF change immediately
- [ ] “Reach target HF” suggestions feel reasonable

---

## P3 — Forecasting v1 (Weeks 3–5)

**Outcome:** probabilistic forecast for crossing risk bands over 24h and 7d.

### Data layer

- [ ] Choose price history source(s) and implement adapters:
  - [ ] Fetch historical prices for relevant tokens
  - [ ] Store/cache time series (db or file cache)
- [ ] Add data “freshness” metadata to API responses

### Model v1 (simple + defensible)

- [ ] Implement EWMA volatility + correlation estimation
- [ ] Monte Carlo simulator:
  - [ ] Simulate correlated price paths
  - [ ] Recompute HF along each path
  - [ ] Estimate `P(HF < 1.2)` and `P(HF < 1.1)` for 24h/7d
- [ ] Add sanity checks:
  - [ ] Handle stablecoin/stable pairs
  - [ ] Clamp extreme outputs + flag low data quality
- [ ] Implement `/forecast/{address}?horizon=24h|7d`:
  - Output: probabilities, HF percentiles, key drivers

### Frontend

- [ ] Forecast UI `/forecast`:
  - [ ] Risk meter (probabilities)
  - [ ] HF percentile chart (e.g., 5/50/95)
  - [ ] “Drivers” panel (top contributing assets)
  - [ ] “Assumptions” panel (data window, method, disclaimers)
- [ ] Integrate forecast with simulator:
  - [ ] “Simulated action → new risk probabilities”

**Acceptance**

- [ ] Forecast returns stable outputs (seeded or enough samples)
- [ ] Output includes data freshness + model assumptions
- [ ] Simulator changes forecast in the expected direction (repay → lower risk)

---

## P4 — Alerts (Weeks 6–7)

**Outcome:** user sets thresholds → receives notifications.

### Backend

- [ ] DB schema for users/alerts:
  - [ ] wallet address
  - [ ] thresholds (e.g., P24h(HF<1.2) > X%)
  - [ ] channel config (email first)
- [ ] Alert evaluation job:
  - [ ] schedule (hourly or configurable)
  - [ ] recompute forecast
  - [ ] send message if triggered
- [ ] Notification provider integration:
  - [ ] Email (simple) OR Telegram bot (optional)

### Frontend

- [ ] Alerts UI `/alerts`:
  - [ ] Create/edit alert rules
  - [ ] Alert history list (sent events)
  - [ ] Test notification button (safe)

**Acceptance**

- [ ] Real alert delivered for real wallet when forced condition is met
- [ ] Alert message includes current HF + probabilities + suggested action summary

---

## P5 — Hardening & Quality (Weeks 8–10)

**Outcome:** reliable demo-ready product; less “false confidence.”

### Reliability

- [ ] Rate limiting + basic abuse protection
- [ ] Improved caching strategy (prices + position reads)
- [ ] Observability:
  - [ ] structured logs
  - [ ] error tracking (e.g., Sentry)
- [ ] Backups / migrations for DB

### Model improvements (pick 1–2)

- [ ] Heavy-tail returns (Student-t) OR
- [ ] Two-regime volatility OR
- [ ] Stress scenarios (“-10% overnight”, “correlation spike”)
- [ ] Add backtest-style diagnostics page (internal):
  - [ ] “Would this have warned me yesterday?”

### UX & Docs

- [ ] Clear disclaimers and “assumptions” copy
- [ ] “Data freshness” badges in UI
- [ ] Security/privacy statement (no keys, minimal storage)

**Acceptance**

- [ ] System behaves well under repeated use
- [ ] Forecast page explains assumptions without jargon
- [ ] Basic incident-proofing (logs + alerts on failures)

---

## P6 — Optional: On-chain oracle module (Weeks 11–12)

**Outcome:** off-chain computation committed on-chain (learning objective).

### Option A (simpler): backend writes on-chain

- [ ] `RiskState` contract:
  - [ ] store regime (GREEN/YELLOW/RED), timestamp, hash of inputs
  - [ ] emit events
- [ ] Backend daily job computes regime and updates contract
- [ ] UI reads and displays on-chain regime

### Option B (more “oracle-y”): Chainlink Functions

- [ ] Functions subscription setup + secrets handling
- [ ] Functions script fetches data and computes regime
- [ ] Contract fulfillment handler updates `RiskState`
- [ ] UI reads on-chain regime + shows last update time

**Acceptance**

- [ ] Regime updates on schedule and is visible in UI
- [ ] No funds custody; contract is minimal and well-tested

---

# Backlog / Nice-to-haves (post-MVP)

- [ ] Multi-wallet support
- [ ] Multi-market/multi-chain support
- [ ] Self-host mode (privacy-first)
- [ ] One-click assisted execution (prepare tx, user signs)
- [ ] Paymaster UX / gas-in-ERC20 (zkSync-native)
- [ ] Export reports (weekly PDF/CSV)
- [ ] “Pro” alert channels (SMS, push)

---

## Notes

- Start web-first (avoid app store policy friction).
- Keep automation out of MVP to reduce security risk.
- Prioritize: correctness of Aave position reads → what-if usefulness → forecast honesty → alerts reliability.
