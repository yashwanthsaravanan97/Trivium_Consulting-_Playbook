# 11 · Engine B — Cartons per Pallet (carton → pallet qty)

> **What this is.** The packing engine: for each SKU, how many **cartons physically fit on one pallet** — testing box **orientations**, stacking into **layers** to a height limit, and respecting a **weight** limit. Heavy geometry can run in Python; the read-out stays HTML.

> **How to use it.** Get the core formula exactly right (especially that weight is a **total** constraint), then run the two scenarios.

---

## Inputs
- Per-SKU carton **L × W × H** and **gross weight**.
- Pallet **length, width, max height, board thickness, weight limit**. Compute **usable stack height = max height − board**.

## The core formula (both scenarios)
```
cartons_per_layer = FLOOR(pallet_L / Dim_A) × FLOOR(pallet_W / Dim_B)
max_layers        = FLOOR(usable_height / Dim_C)
max_by_volume     = cartons_per_layer × max_layers
max_by_weight     = FLOOR(weight_limit / carton_weight)      ← TOTAL, not per layer
total_cartons     = MIN(max_by_volume, max_by_weight)
```
> **The trap:** weight is a **whole-pallet** limit — `FLOOR(limit / carton_weight)`, *never* `FLOOR(limit / layer_weight)`. The per-layer version made thousands of SKUs return 0. See [[24_Recheck_and_Common_Mistakes]].

## The 6 orientations
Test all rotations of L×W×H (which dim runs along pallet length, along width, stacked as height). For each, compute `total_cartons`.

| # | along L | along W | stacked |
|---|---|---|---|
|1|L|W|H| |2|L|H|W| |3|W|L|H| |4|W|H|L| |5|H|L|W| |6|H|W|L|

## The two scenarios
- **Scenario 1 — any orientation (max fill):** test all 6, take the **highest** total.
- **Scenario 2 — fixed orientation:** pick the rotation with the **best base-layer footprint**, lock it for the whole pallet, apply the same MIN(volume, weight).
- Report which one wins per SKU, by how much, and the **recommendation** (S1 default; S2 where equal, for operational simplicity).

## Report the binding constraint
For each SKU, say whether **HEIGHT** or **WEIGHT** capped it (or N/A for cannot-palletise). It tells the client *why* the number is what it is.

## Edge cases
- [ ] **No orientation fits** → flag `CANNOT_PALLETISE`, don't drop.
- [ ] Vectorise across all SKUs (numpy) for speed; the logic is per-SKU and embarrassingly parallel.

## Outputs
- Per-SKU: best orientation, cartons/layer, layers, total cartons, total weight, binding constraint, utilisation.
- HTML/Excel: per-scenario tables + an S1-vs-S2 comparison with recommendation labels.

## Checklist
- [ ] Weight is a **TOTAL** constraint (`FLOOR(limit / carton_weight)`).
- [ ] Usable height = max − board (board deducted once).
- [ ] All 6 orientations tested; binding constraint reported.
- [ ] Cannot-palletise SKUs flagged and counted.

---
> **Worked example — Carton-Pallet.** Euro pallet 120 × 80, max 200 cm, board 15 → usable 185 cm, 1,000 kg. ~148k SKUs, numpy-vectorised. HEIGHT bound ~91%, WEIGHT ~9%. S1 avg ~98 cartons/pallet vs S2 ~96; S1 the default.

*Trivium Analytics · Warehouse Consulting Analysis Playbook · v0.1 · 2026-07-28 · next [[12_Bucketing]] · terms in [[23_Glossary_and_Reference]]*
