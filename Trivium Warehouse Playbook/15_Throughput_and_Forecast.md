# 15 · Throughput & Forecast — from stock to flow, and forward in time

> **What this is.** Storage sizing answers "how much space"; throughput answers "how much *work*". If demand data is in scope, turn packs into lines, find the peak-day/hour load, and project the design year. Optional — only when the brief needs it.

> **How to use it.** Keep the factors in named workings cells so they're auditable; keep the links live so a data refresh flows through.

---

## Inputs
- A **demand / throughput data pack** — usually weekly packs by area, a peak-week profile, and rates/assumptions.
- The storage model from `10`/`13` (to sit storage + throughput side by side).

## What you do
1. **Packs → lines.** `Lines = Packs / picks-per-line` (the client's ratio — e.g. 1.2). Lines are the pickable unit of work.
2. **Peak factors.** Derive from the profile and keep them in workings cells:
   - `Peak Day = Peak Week × (max day ÷ week total)`
   - `Peak Hour = Peak Day × (max hour ÷ day total)`
3. **Storage + throughput table.** Per area/medium: the storage numbers (from the model) plus annual / peak-week / peak-day / peak-hour lines. Use **live formulas / links** to the source sheets where you can.
4. **Forecast.** Grow the base year by the client's volume assumptions to the **design year** (e.g. +10 years). Link the forecast base to the storage+throughput table so it follows a refresh.
5. **Seasonality ⭐.** Profile demand by **month · week · day · hour** and find the seasonal shape — Christmas, Black Friday, Diwali, Ramadan, summer, financial year-end, promotions, seasonal ranges. The number that matters most for solution design is the **peak-to-average ratio** (e.g. peak day 18,000 lines vs average 10,000 = 1.8×). Automation and capacity are sized on the *appropriate peak*, never the mean — a system built for the average falls over in December. State the ratio, and which peak scenario the design targets.
6. **Growth scenarios.** Beyond the single design-year forecast, carry the base into the **scenario set** — current / moderate / high growth / peak — that the future state and business case (`20`) compare. Keep every growth factor in a visible cell so the scenarios flex.

## Forecasting — use theirs first, then triangulate ⭐

**Ask for the client's own forecast before you build one.** They have a commercial plan, and a warehouse
sized against a number Operations does not recognise will not get funded.

Triangulate three views and show all three:

1. **The client's plan** — their commercial growth number. The one the board already agreed.
2. **The historical trend** — CAGR from the data you were given. State the window; a trend off 12 months
   including one Black Friday is not a trend.
3. **Capacity reality** — what the building can physically absorb before something breaks (`16`).

Where they disagree, **that gap is a finding**: a plan that outruns the building's capacity by year 3 is
precisely the thing the study exists to surface.

- **Do not fit a sophisticated model to thin data.** With 12 months of history, a stated CAGR applied to a
  measured base is more honest — and more defensible — than a seasonal decomposition dressed up as science.
- **Grow the shape, not just the total.** If the peak grows faster than the average (usually true in
  e-commerce), growing the average and applying today's peak factor **understates** the design peak.
- **Keep every growth factor in a visible cell**, and label it as the client's assumption or ours.

## Watch-outs
- [ ] Note what the data pack **doesn't** cover (e.g. no per-tote-size throughput, a missing area) — don't fabricate it.
- [ ] Keep every factor in a labelled cell, not buried in a formula.
- [ ] **Excel does not recalc on a programmatic write** — reopen via Excel COM, `CalculateFull`, Save, or linked/forecast cells stay stale (see `21`/`22`).

## Outputs
- A storage + throughput sheet (and a standalone self-contained copy if the client wants one without external links).
- A forecast to the design year.

## Checklist
- [ ] Lines derived with the client's picks-per-line ratio.
- [ ] Peak day/hour factors in visible workings cells.
- [ ] Live links intact; formulas recalculated (COM) after any write.
- [ ] Gaps in the demand data are stated, not filled with guesses.

---
> **Worked example — WAMAS.** Picks-per-line 1.2 → `Lines = Packs / 1.2`; Peak Day ≈ Peak Week × 21.5%, Peak Hour ≈ Peak Day × 7.8%. Built sheet "Storage + Throughput" (media rows + new tote-model rows), Packs/Lines as live links; a self-contained summary workbook with the two source packs hardcoded and the rest in-sheet formulas.

*Trivium Analytics · Warehouse Consulting Analysis Playbook · v0.2 · 2026-08-18 · next [[21_Deliverables_and_House_Style]] · scenarios & business case in [[20_Future_State_and_Business_Case]]*
