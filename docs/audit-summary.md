# Audit summary

The system is audited the way it is built: repeatedly, adversarially, and with the findings written down. This page summarises the audit record. It documents **engineering** quality — it is not, and does not imply, a profitability claim.

## The audit lineage

- **First full-system audit (October 2026)** — an initial independent pass over execution, risk, and research code, followed by a four-phase remediation programme. Its most consequential finding: the original out-of-sample results were contaminated by **selection leakage** in the tuning pipeline. The pipeline was rebuilt so leakage is structurally impossible, and the entire book was re-measured before any figure was trusted again.
- **Clean-board re-measurement** — all 22 tradable symbols re-measured under the sealed protocol. Verdicts were accepted as measured, including the uncomfortable ones (see [validation.md](validation.md)).
- **Cleanup and re-audit (October 29, 2026)** — graded **82/100 (B)** under an expanded rubric that newly scored *runtime-activation evidence* (does the running desk provably match the code?) and the *structured-logging standard*. The six-point change from the prior 88/100 reflects the wider rubric surfacing visibility gaps — a book that could not yet announce its own contents — not a regression in the underlying systems. Those gaps were then closed by the bookkeeping work below, and re-graded by drill.

## October 30–31, 2026 — research-platform expansion

The audit standard — fixed, activated, and *proven* — was applied to a four-part expansion of the research platform, each part landed on its own branch behind a green CI gate:

- **Idea foundry built** — governed candidate generation (12,420-configuration grammar), a permanent name registry, a failure archive, and a measurement adapter reusing the sealed funnel. Its first smoke batch was refused by the eligibility gate exactly as designed: the out-of-sample slices it reached for were already spent, and the gate does not negotiate.
- **Certification-budget census** — the full trial ledger (122,405 rows) streamed and reconciled against known anchors, mapping the unspent out-of-sample budget of every symbol × timeframe before any new programme was scoped.
- **Data platform completion** — the corpus grew from 234 to 273 series (39 symbols × 7 timeframes, ~1.3 GB), including five years of 5-minute data for every symbol; the checksum manifest verifies 273/273. A pre-pull spot-verification gate refuted the census's own depth classification on a sample, and the backfill it made pointless was cancelled rather than executed — the verification layer catching its own side's error is the system working as intended.
- **Test hermeticity** — research smoke tests that silently depended on the local data corpus were made to skip explicitly, with the reason stated, when the corpus is absent — returning the affected branch to green in CI, where the corpus does not exist.

## October 31 – November 1, 2026 — programme autopsy, engine census, consolidation

- **Foundry programme autopsy** — the first full programme's 2,000-candidate archive was forensically re-analysed. Three machinery defects were root-caused — the generator minted duplicate candidates (the 25 highest-ranked results were one candidate repeated), family coverage collapsed to three of ten families, and the performance metric was silently capped in the archive — and each became a mechanical requirement of the successor programme: coordinate-hash deduplication at generation, pre-registered family quotas, and uncapped metric recording. The autopsy also settled two design questions with data: a proposed fold-stability gate was rejected (no fold-stable population existed to gate on), and the programme's zero yield was attributed to slice geometry — trade-opportunity density — rather than to its pass bars, redirecting the successor toward bar-richer timeframes.
- **Engine census and twin audits** — all four timeframe engines inventoried into a machine-checked registry. The 4-hour engine's silence was audited to a verdict of *unobservable with the records that existed* — no gate provably stuck, decisive checkpoints silent — and closed with instrumentation, not speculation. The 15-minute engine's machinery was certified sound (99 gated would-be entries in four days) and its legacy validation rejected on provenance in the same report; a sealed re-validation was specified (500-trial budget, family-wise α ≈ 1×10⁻⁴) rather than the old numbers being trusted.
- **ML parity audit** — a reported train/serve feature skew was investigated to ground truth, refuted on the serving path, and hardened anyway; measured skew across 127 live entries: 0.0. Parity is now guarded by dedicated hermetic tests.
- **Consolidation sitting** — eight branches merged into main in fixed order, each merge individually CI-verified (nine consecutive green runs), desk invariants identical before and after, the tracker never restarted. The sitting's pre-flight halted on an unexamined local commit and a dirty working tree; both were resolved by inspection and preservation — never by deletion or reset — before the first merge ran.

## Findings and how they were dispositioned

A standing backlog tracks every finding with a status the operator can audit. Recent closures illustrate the standard a finding must meet to close — fixed, activated, and *proven*, not merely patched:

- **A configuration subsystem could route orders outside its mandate** → disabled; the trade journal was audited to prove zero orders had ever flowed through it.
- **A secrets file was tracked in version control** → untracked; every live credential rotated; running system verified healthy afterward.
- **Discord alert delivery failed for one critical class** (payload exceeded the platform's size limit while detection itself fired correctly) → payload compacted, regression-tested at fifty times the realistic row count, and closed only after a sanctioned live test alert was **visually confirmed delivered**.
- **The watchdog could not be missed** — stopping it produced no alert from the tracker, because the check lived on a loop that could block → check moved to a dedicated thread; closed only after a drill in which the alert fired (~5 minutes) and cleared as designed.
- **Bridge authentication failed open** if its token file could not be read → import made fail-closed; proven at a gated activation restart where the services started clean on the real token.
- **A tracker startup trap** re-archived the entire trade history on every launch → single-pass batch rewrite, byte-identical output, proven by clean cold starts including after a full host reboot.

## What the grades measure

The engineering grade measures the engineering: correctness, supervision, measurement integrity, security hygiene, and whether the running system provably matches its description. It says nothing about returns, and this showcase makes no profitability claim in either direction. The book's forward evidence accumulates on the demo account, in the open, under the protocol described in [validation.md](validation.md) — that record, over time, is the only verdict on the trading itself.
