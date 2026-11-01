# OrbitAlgo — Autonomous Algorithmic Trading System

**An institutional-grade, multi-strategy trading platform — engineered, audited, and validated under a sealed out-of-sample protocol.**

[![CI — private production repo: green, 1,700+ tests](https://img.shields.io/badge/CI_%C2%B7_private_production_repo-green_%C2%B7_1%2C700%2B_tests-brightgreen)](https://github.com/kopekijay11-hub/OrbitAlgo/blob/main/docs/audit-summary.md)

> This repository is a **showcase**. The production codebase is private; what is documented here is the engineering: the architecture, the validation discipline, and the verified results. No strategy code, configuration, or credentials are published.

---

## Public vs private

This repository is the public artifact of a larger private system. The boundary is deliberate:

**Public (this repository):** architecture and system design · validation methodology and philosophy · the demo book's composition · audited engineering results and summaries · the workflow a research hypothesis follows from registration to a seating decision.

**Private (never published):** the production implementation and live execution code · trade configuration and risk-engine internals · the MetaTrader 5 bridge implementation · telemetry and operational data · proprietary strategy logic.

The point of the boundary is not secrecy for its own sake — it is that the engineering discipline is the portfolio piece, and it can be documented honestly and completely without exposing the system it governs.

## Project at a glance

| | |
|---|---|
| **System type** | Autonomous multi-strategy algorithmic trading platform |
| **Scope** | 24 registered symbols — precious metals, energy, equity indices, major & Canadian-dollar forex — traded on a multi-timeframe platform spanning 5-minute to 12-hour bars and everything in between; live execution runs on the hourly and 4-hour engines today, with certified coverage being extended across the full range; the research corpus spans seven timeframes and is being extended to ten |
| **Main components** | Strategy engines (ten families) · walk-forward research pipeline · idea foundry (governed strategy generation) · market-data accessor over a checksummed corpus · ML layer (observer + shadow specialists) · risk layer with hard circuit breakers · MetaTrader 5 execution bridge · supervision stack (watchdog, bridge guardian, dashboard) |
| **Runtime environment** | Python on Linux (WSL2) bridged to a Windows-hosted MetaTrader 5 terminal; systemd-supervised services |
| **Validation methodology** | Sealed SELECT → VALIDATE → HOLDOUT protocol; every trial charged to an auditable ledger; family-wise (Šidák) significance correction before any result is called evidence |
| **Risk controls** | Hard breakers on drawdown, daily loss, and consecutive losses; per-trade and portfolio caps; news-event blackout; position sizing tiered by evidence quality |
| **Status** | Running autonomously on a MetaTrader 5 **demo account**; promotion to live capital is gated on clean out-of-sample evidence, not on a schedule |

## How to read this repository

- **README.md** (this file) — the overview: what the system is, what it has proven, and the results it stands on.
- **[docs/architecture.md](docs/architecture.md)** — system design: components, data flow, the supervision stack, and how the desk recovers from failure.
- **[docs/validation.md](docs/validation.md)** — the sealed validation protocol, the trial ledger, and what a PASS verdict does and does not mean.
- **[docs/risk-controls.md](docs/risk-controls.md)** — risk as code: breakers, blackout layers, sizing tiers, and the operational controls around the book.
- **[docs/audit-summary.md](docs/audit-summary.md)** — the independent audit record and how its findings were dispositioned.
- **[examples/workflow-example.md](examples/workflow-example.md)** — a walkthrough of one research hypothesis travelling the full pipeline, from registration to a seating decision.

---

## What it is

A fully autonomous trading system that researches, validates, and executes strategies across **24 registered symbols** — precious metals (gold, silver, platinum, palladium, copper), energy (crude and refined products), equity indices (German and Japanese benchmarks), and major & Canadian-dollar forex pairs — across a multi-timeframe range spanning 5-minute to 12-hour bars, with live execution seated on the hourly and 4-hour engines, quoted end-to-end in the broker's native instrument symbols via MetaTrader 5. The execution bridge is broker-agnostic by design: any MetaTrader 5 terminal can sit on the execution side.

The system runs end-to-end without human intervention: market data ingestion, signal generation across **ten strategy families**, an ML specialist layer, risk management with hard circuit breakers, order execution through a MetaTrader 5 bridge, a live monitoring dashboard, and automated alerting. It currently operates on a demo account by design: promotion to live capital is gated on clean out-of-sample evidence, under governance the system was specifically built to enforce. That discipline is the point of the engineering.

## Engineering highlights

- **40,000+ lines of Python**, one cohesive system: strategy engines, a walk-forward tuning pipeline, a bridge service connecting Linux (WSL2) to a Windows-hosted MetaTrader 5 terminal, a real-time dashboard, and a watchdog supervisor.
- **1,700+ automated tests**, with CI fully green on the current build — the full pytest suite, the strict type gate, and the 65% coverage floor all passing. The suite is hermetic by construction: a corpus-presence guard makes every data-dependent test declare itself and skip explicitly where the corpus is absent, and a mechanised pre-push gate runs the touched modules' tests plus the linter locally before any branch leaves the machine — so local and CI verdicts can no longer silently diverge. The type-checking budget is enforced in CI as a ratchet: when CI revealed the true type-error count was higher than local runs had measured, the difference was root-caused and the debt was paid down to the original ceiling, file by file. The ceiling was never raised.
- **A single shared market-data layer, proven equivalent before it went live.** All market data — a stored corpus of 273 files spanning 39 symbols × 7 timeframes (~1.3 GB, 2018–present, including five years of 5-minute bars for every symbol) — is now read through one accessor backed by a sha256 checksum manifest. The repoint onto it was behavior-preserving by construction and by proof: output hashes captured before the change match the new layer's output exactly, across a 148-capture equivalence sweep of every served symbol, with zero discrepancies.
- **A book that announces itself.** At every startup the engines publish a machine-readable manifest of exactly which strategy arms loaded and which registry rows were skipped, with the gate that stopped each one named. A watchdog check compares the live book against the registry continuously and raises a critical alert on any unexplained absence — a detection, logging, and delivery chain proven end-to-end by live drills, including a deliberately planted phantom registry row.
- **Repeated independent full-system audits** (October 2026) spanning the execution bridge, risk layer, strategy engines, research pipeline, data layer, ML layers, and repository hygiene — the latest, graded October 29, 2026 under an expanded rubric that added runtime-activation evidence and the structured-logging standard, stands at **82/100 (B)**, with every material finding remediated or formally dispositioned and the corrections graded by the operator rather than self-certified.
- **An idea foundry for strategy generation (October 2026).** A generation system mints strategy candidates from a finite grammar of 12,420 configurations across the ten families — every candidate given a permanent, never-reused name *before* testing, charged to the trial ledger at generation, and archived permanently whatever its result, so the graveyard is as auditable as the book. Before its first programme, a census of the ledger mapped the unspent certification budget of every symbol × timeframe (122 of 234 series certifiable at census time; the corpus has since been completed to 273 series). Generation runs under hard caps — 200 per batch, 2,000 per programme — so the multiple-testing charge is bounded by design, and nothing the foundry produces trades without a protocol PASS and an operator seating decision. The first full programme has since completed: 2,000 candidates generated under a pre-registration committed before generation, 1,971 carried through the funnel, and **zero holdout budget consumed** — the certification slices it might have spent remain unspent. No candidate was seated (seating requires a protocol PASS), and a forensic autopsy of the run root-caused three generation-machinery defects — duplicate candidates minted by the generator, a collapse of family coverage, and a recording cap on the performance metric — each fixed, with the fixes enforced mechanically in the second programme's design rather than left to vigilance.
- **A machine-checked engine registry.** Every timeframe engine is inventoried in a declarative registry — module, order-routing identity, routed symbols, live status — verified against the code by a checker that exits PASS. The census behind it (October 31, 2026) fixed the desk's ground truth: H1 live with seven arms, H4 live with four, the 15-minute engine evaluated-and-gated, the 5-minute engine absent by design until it ships. Twin audits followed: the 4-hour engine's zero-activity window was traced to decision checkpoints that returned silently, and the engine now emits structured miss-visibility logging, so a quiet engine is an observable one; the 15-minute engine's machinery was certified sound — 99 evaluated would-be entries correctly suppressed by its closed gate in a four-day window — while its legacy validation was rejected on provenance and a sealed re-validation specified in its place (trial budget 500, family-wise α ≈ 1×10⁻⁴).
- **ML train/serve parity as a tested invariant.** The feature assembly that trains the expected-R model and the assembly that serves it live were proven to read the same bars — measured skew 0.0 across 127 live entries — and six hermetic tests now guard that parity permanently. Parity is an invariant with a test suite, not an assumption.
- **Sealed validation protocol** — SELECT → VALIDATE → HOLDOUT with fail-closed governance: a holdout segment is spent exactly once, every tuning trial is written to an auditable ledger (**122,405 charged runs**), and family-wise significance is charged (Šidák correction) before any result may be called evidence.
- **A full independent audit cycle** (four phases) that found the original out-of-sample numbers were contaminated by selection leakage — then rebuilt the measurement pipeline so the leak is structurally impossible, and re-measured the entire book before trusting a single figure.
- **Supervision and hardening, second generation.** The desk runs as systemd user services with linger enabled (tracker, watchdog, dashboard), each with supervised restarts; the tracker and watchdog now also supervise *each other* — a dedicated thread in the tracker watches the watchdog's heartbeat, a failure mode found by drill, fixed, and then proven by a second drill in which the watchdog was deliberately stopped and the tracker's alert fired and cleared as designed. On the Windows side, a dedicated Bridge Guardian scheduled task keeps the MetaTrader 5 bridge alive, relaunching it within 10 seconds of a crash — a recovery proven by a live kill drill — and the terminal itself now autostarts at logon. Bridge authentication was rebuilt so the token lives only in its own gitignored file, with health verified by fingerprint, and later hardened to fail closed at import rather than run unauthenticated. A startup trap that re-archived trade history on every launch was found, fixed with a single-pass batch rewrite, and proven cured by a clean cold start after a full Windows reboot; Windows auto-restart has been disarmed, with a documented recovery path in its place. Every live path now emits structured, timestamped JSON logs under a single standard.
- **Repository discipline.** Nineteen feature branches across two consolidation sittings — the second landing the entire October research campaign, eight branches in fixed order — were merged into main behind a secrets scan and per-merge CI gates, with desk invariants (bridge fingerprint, registry hash, arm count) proven identical before and after. The second sitting's pre-flight refused to begin until one unexamined local commit had been inspected and cleared: the gate working as designed. The main branch, the running desk, and the documented state are the same code, verified by commit hash.
- **Reliability and security engineering**: a tracker-freeze root-cause investigation traced intermittent hangs to watchdog restart logic interacting with blocking I/O; the fix (isolated worker, hard timeouts, fail-fast reads) shipped with proof tests. A configuration audit caught a subsystem that could have routed orders outside its mandate; it was disabled and verified — the trade journal proved zero orders had ever been placed through it. A later audit found a secrets file tracked in version control; it was untracked and every live credential in it was rotated, with the running system verified healthy afterward.
- **Risk as code**: hard breakers on peak-equity drawdown, daily loss, and consecutive losses; per-trade and portfolio risk caps; a news-event blackout calendar wired into every engine; position sizes tiered by evidence quality — with the effective breaker levels (25% peak-equity drawdown, 10% daily loss, 8 consecutive losses) formally ratified as an operator decision after an audit caught documentation drift on exactly those numbers.

## The live demo book

The system currently runs an **eleven-arm book autonomously on a MetaTrader 5 demo account** — every arm below was confirmed loaded and cycling by name in the live startup manifest at the most recent supervised restart (October 30, 2026), across a market-data feed covering 24 symbols with zero gaps:

- **H1** — GOLD range grid · SILVER trend · USDJPY mean reversion · HEATOIL trend · LSGOIL trend · USOIL breakout · PLATINUM trend
- **H4** — CADCHF trend · CADNOK trend · GER40 trend · JPN225 trend

Every seat comes from a configuration the sealed protocol has measured — no arm trades on an untested parameter set. Position sizes are tiered by evidence quality, and seats that exist to accumulate forward evidence are labelled as such in the registry and capped at reduced size.

## The validation story

When the clean protocol re-measured all 22 tradable symbols, the results reset the expectations set by the old, leaky pipeline — which is exactly what the rebuild was for. On the H1 book, GOLD trend and SILVER trend recorded pass-class verdicts on never-before-seen data (GOLD trend: +6.92% on its sealed holdout across 146 trades); in the H4 timeframe program, CADCHF validated outright and now trades as one of the book's highest-conviction seats. The book continues to run on demo while candidates accumulate forward evidence. Promotion under the project's governance requires clearing a multiple-comparisons significance charge as well as the protocol's own bar — no candidate gets promoted on a single measurement.

That is the discipline this project demonstrates: measurement infrastructure rigorous enough to be trusted when it says *yes*.

## The full package — front end to back end

This is a full-stack system, designed and built end-to-end by one engineer:

- **Front end** — a real-time operations dashboard (NiceGUI web UI): live positions, equity, per-strategy-arm state, risk-breaker status, and alerting, served locally by design. It is the operator's window into everything below it.
- **Middle layer** — an execution bridge API (FastAPI, token-authenticated REST with idempotent order endpoints) connecting the Linux compute environment (WSL2) to a Windows-hosted MetaTrader 5 terminal (demo account), with an independent account-guard check on the compute side.
- **Back end** — the strategy engines, the walk-forward research pipeline with its schema-versioned trial ledger, the risk layer with hard circuit breakers, a SQLite trade journal, and a heartbeat-monitored watchdog supervisor.

## Architecture at a glance

- **Strategy engines** on four timeframe tracks, all inventoried in a machine-checked engine registry — the H1 engine (primary, live) and the H4 engine (live) run the book today; a 15-minute engine is scaffolded with its new-entries gate disabled — its machinery audit-certified, its sealed re-validation specified — pending certification; a 5-minute engine is in design, its data layer already complete — hosting ten families — trend, breakout, mean-reversion, range grid, session breakout, liquidity sweep, Donchian, swing, and statistical arbitrage
- **Market-data layer**: one shared accessor over a checksummed corpus (273 files, 39 symbols × 7 timeframes, ~1.3 GB, 2018–present) with a sha256 manifest — every consumer reads through the same door, and the layer's equivalence to the parsers it replaced is hash-proven
- **Walk-forward tuner** with a sealed segment pipeline and a schema-versioned trial ledger
- **Idea foundry**: governed candidate generation over a 12,420-configuration grammar — named before testing, ledger-charged at generation, failures archived permanently — feeding the same sealed funnel as any hand-written hypothesis
- **ML layer** (gradient-boosted models): an expected-R model (LightGBM) serves in an observer role informing position sizing, and a specialist layer — one bundle set per strategy family — runs in permanent shadow mode after a dedicated audit of its training-data provenance, per-family expectancy, and sizing impact, scoring and logging only while it accumulates the evidence base that will govern any future retrain decision
- **Execution bridge** between the Linux compute environment and MetaTrader 5, with token authentication held in a dedicated gitignored file, fingerprint-verified health checks, and an independent account-guard check on the compute side
- **Supervision**: systemd user services with linger (tracker, watchdog, dashboard) under supervised restarts, a Windows Bridge Guardian that relaunches the MT5 bridge within 10 seconds of a crash, heartbeat monitoring with failure classification, bot-based alerting, and a local operations dashboard
- **Research stack** (isolated environment): indicator-parity certification against TA-Lib, vectorized-backtest reconciliation, and seeded, replayable hyperparameter studies

## Roadmap and current status

- **Phase 1 — Research and validation infrastructure.** Complete: the sealed SELECT → VALIDATE → HOLDOUT protocol, the auditable trial ledger, and the audit cycle that rebuilt the measurement pipeline after selection leakage was found in it.
- **Phase 2 — Autonomous demo deployment.** Live: the eleven-arm book runs unattended on a MetaTrader 5 demo account under the supervision stack described above.
- **Phase 3 — Evidence accumulation and measurement programmes.** In progress: a pre-registered pattern-screening programme has completed under the sealed protocol; the idea foundry's first full programme has completed (2,000 candidates under pre-registration, zero holdout budget spent, its autopsy folded into the successor's design); the second programme is in build-out on a widened grammar — risk-profile bundles, H2/H3/H6 timeframes, the FX-major universe, external mechanism data (petroleum inventories, Commitments of Traders positioning, dollar and Treasury-yield series), a candlestick family, and a GOLD 5-minute certification block; a sealed re-validation of the 15-minute engine is specified; the market-data corpus is being extended from 273 to 390 series; and the forward demo evidence accumulates trade by trade.
- **Phase 4 — Operational hardening.** Ongoing: fail-closed bridge authentication, mutual tracker–watchdog supervision, and reboot-proofing are live and drill-proven; a full unattended logon-recovery drill is the remaining formal proof.

Promotion to live capital is deliberately **not** a roadmap phase with a date: it is a governance decision, gated on clean out-of-sample evidence under the protocol in [docs/validation.md](docs/validation.md).

## Skills demonstrated

Python (large-system design) · full-stack delivery (web dashboard UI · REST API bridge · execution engines · data layer) · pytest at scale · type-safe engineering (mypy ratchet) · CI/CD · statistical validation & multiple-testing discipline · walk-forward / out-of-sample methodology · API & bridge integration (MetaTrader 5) · Linux/WSL systems work · systemd service supervision · incident root-cause analysis · technical documentation and audit reporting

---

*Built and operated by one self-taught engineer. The private repository contains the full implementation, the complete audit trail, and the trial ledger; it is available for review in the context of employment or contract discussions.*
