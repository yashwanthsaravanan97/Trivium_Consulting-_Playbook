# 13 · Current State & Scenarios — reproduce actuals, reconcile, then flex

> **What this is.** Anchor the model to reality (reproduce how the client stores **today**), reconcile every number to the whole, then run **scenarios** — the "what if" questions that are usually the real point of the job. Mostly an Engine A phase.

> **How to use it.** Reconcile first; you can't run credible scenarios off a model that doesn't tie out.

---

## Inputs
- The calibrated model (`10`).
- The client's current-state actuals (per medium / area).
- The whole-warehouse totals from `03`.

## What you do
1. **Reproduce the current state.** Per medium/area: SKUs, locations, quantity, cube — from the live data, matching the client's own figures.
2. **Reconcile to the whole.** Sub-tables must sum to the grand total (SKUs, m³, units). If they don't, it's wrong until proven otherwise.
3. **Subtract, don't sum.** To get "everything except X", **subtract X from the total** — never add up the pieces you think you know. Totals rarely decompose cleanly; there's almost always unattributed stock.
4. **Run the scenarios.** Typical ones:
   - **Remaining-stock** — size only the stock left outside the kept media into the new system.
   - **By-category / whole-warehouse** — re-slot everyone's total stock, split by a flag (in a medium vs not), with different fills.
   - **Add / remove an option** — e.g. drop a storage medium and fold its stock into the new system; compare Option A vs Option B.
5. **Present as a comparison** — current vs proposed, or Option A vs Option B, with the deltas the client cares about.

## The golden rule for scenarios
- [ ] **Change one thing at a time** and re-run the *whole* chain. Before trusting a swapped model, prove it **reproduces the baseline exactly** with the old setting, *then* make the change.

## Outputs
- A current-state HTML that matches the client's actuals.
- Scenario HTML(s) with clear Option-A-vs-B / current-vs-proposed comparisons, all reconciling to the whole.

## Checklist
- [ ] Current state matches the client's own numbers.
- [ ] Every sub-table sums to the grand total.
- [ ] "Remaining / everything-except" built by **subtraction**.
- [ ] Each scenario changes one variable; the baseline is reproducible.
- [ ] Deltas (the answer to the client's question) are front and centre.

---
> **Worked example — WAMAS.** Reproduced Navette/MS/Haz/Xilinx actuals; built "remaining stock = total − those media" (by subtraction). Then the **Manual-Shelving scenario**: removed MS from the media set, folded its stock into totes, validated the rebuild reproduced the 4-media model **exactly** before the swap, and showed Option A (MS kept) vs Option B (MS folded in) — all reconciling to 401,655 SKUs / 8,391 m³ / 2,623 M units.

*Trivium Analytics · Warehouse Consulting Analysis Playbook · v0.1 · 2026-07-28 · next [[15_Throughput_and_Forecast]]*
