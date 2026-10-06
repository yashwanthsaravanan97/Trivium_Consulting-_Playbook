# 10 · Engine A — Cubic Sizing (size → footprint)

> **What this is.** The tote/pallet **cubic** engine: size each SKU's storage unit from its **box cube**, count units from **stock cube × fill %**, cap the outliers, and roll up into a footprint. Runs in Power BI (calculated columns / DAX), visualised in HTML.

> **How to use it.** Build it step by step; **calibrate the fill % to reproduce the client's actuals** before you trust any projection.

> **Want it fully worked?** `10a` is a complete implementation of this engine from a real client — the tote ladder, 60% fill, caps 5 / 20 / Pallet, the escalation logic and the DAX. Read this module for the *why*, then build from `10a`.

---

## Inputs
- Per-SKU **box cube** (from box dims) and **stock cube** (total volume of stock held).
- The **unit ladder** + capacities (the tote sizes, or pallet), and the **caps**.
- The client's **actuals** to calibrate against.

## The model, step by step
1. **Step 1 — size (no fill).** Choose the **smallest unit whose *full* capacity holds one box cube**. This picks the tote/pallet size from the physical box, not the stock depth.
2. **Step 2 — count.** `locations = ROUNDUP( stock_cube / (chosen_capacity × fill%) )`, min 1. The **fill % lives only in Step 2**.
3. **Caps (Step 3).** Stop any one SKU sprawling: small units capped (e.g. ≤ 5), large units capped higher (e.g. ≤ 20) and **escalate one size up** when over the cap; the top size is uncapped (the ceiling). Caps can *escalate the size* so the count stays within the cap.
4. **Roll up per size:** SKUs, locations, **full-unit equivalents**, utilisation, stock qty, cube.

## The two derived measures (get these right)
- [ ] **Full-unit equivalents = locations × the unit-NAME fraction, empty-adjusted** — *not* the volume ratio. (WAMAS: 16th ÷14, 8th ÷7, Quarter ÷3.96, Half ÷1.98, Full ×1; Full Big / Pallet blank.) This bit us once — see [[24_Recheck_and_Common_Mistakes]].
- [ ] **Utilisation = stock cube ÷ (locations × capacity)** — how full the chosen units actually are.

## Two-track fill (when SKUs differ)
Split the population by a meaningful flag (e.g. already in an automated medium vs not): denser fill (say 75%, consolidate into fewest units) for one track, looser (say 50%, box-cube sizing) for the other. Keep the tracks explicit.

## DAX patterns that work
- Persist logic as **calculated columns** (size, locations, category) so it's live.
- After adding calc columns, run **`model_operations Refresh` type `Calculate`** (ProcessRecalc) or queries error "column … needs to be recalculated".
- Aggregate with **`GROUPBY` + `SUMX(CURRENTGROUP(), …)`**; use `SUMX(CURRENTGROUP(), 1)` for a row count (`COUNTROWS(CURRENTGROUP())` errors).
- Build "remaining stock" by **subtraction** (total − kept media), never sum-of-parts (see `13`).

## Calibrate, then trust
- [ ] Tune the fill % until the model **reproduces the client's actual location count** (per size, not just the total).
- [ ] Only then run scenarios / projections.

## Outputs
- Live calc columns + a Trivium HTML: the size ladder table, KPI cards, a **locations-by-size chart split by location band**.

## Checklist
- [ ] Fill % calibrated to actuals (not guessed).
- [ ] Caps applied and escalating correctly.
- [ ] Full-unit equivalents use the **name fraction**, utilisation uses **cube ÷ (loc × cap)**.
- [ ] Edge SKUs (zero-cube, oversized) shown and counted.
- [ ] Everything reconciles to the whole (`13`).

---
> **Worked example — WAMAS.** Tote ladder (m³): 16th 0.0012775 · 8th 0.00511 · Quarter 0.01022 · Half 0.02044 · Full 0.04088 · Full Big 0.12768 · Pallet 1.44. Caps 5 (16th–Half) / 20 (Full, Full Big) escalating → Pallet (uncapped). Fill 75% in-media / 50% not. Reconciled to 401,655 SKUs / 8,391 m³ / 2,623 M units.

*Trivium Analytics · Warehouse Consulting Analysis Playbook · v0.1 · 2026-07-28 · next [[12_Bucketing]] · terms in [[23_Glossary_and_Reference]]*
