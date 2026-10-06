# 23 · Glossary & Reference — keep this one open beside you

> **What this is.** The lookup sheet: terms, capacities, formulas, and reference numbers you reach for mid-build. Not a phase — a companion to all of them.

---

## Key formulas
**Engine A — cubic sizing**
- Size = smallest unit whose **full capacity ≥ one box cube**.
- `locations = ROUNDUP( stock_cube / (capacity × fill%) )`, min 1.
- `full-unit equivalents = locations × unit-name-fraction (empty-adjusted)`.
- `utilisation = stock_cube / (locations × capacity)`.
- `remaining = total − Σ(kept media)` — **by subtraction, never sum-of-parts**.

**Engine B — cartons per pallet**
- `usable_height = max_height − board_thickness`
- `cartons_per_layer = FLOOR(pallet_L / A) × FLOOR(pallet_W / B)`
- `max_layers = FLOOR(usable_height / C)`
- `max_by_weight = FLOOR(weight_limit / carton_weight)` ← **TOTAL constraint**
- `total_cartons = MIN(layer×layers, max_by_weight)`; test all **6 orientations**.

**Engine C — order profile & throughput** *(file `05`)*
- `avg day = total pick lines ÷ despatch days` ← **despatch days, never 365**
- `peak day factor = peak day lines ÷ avg day lines`; same for `peak week factor`
- `peak hour ≈ peak day ÷ operating hours` — only valid with **no** time stamps; flag it as an even-spread assumption, because it understates the true peak hour
- `ABC` on **pick lines per SKU**, using **fixed thresholds** (e.g. A ≥ 500/yr · B 200–499 · C < 200). Tune the edges so line shares land near **80 / 15 / 5**. Never a cumulative-Pareto cut — it splits tied SKUs across bands and can't be explained in one sentence.
- `XYZ` on **days moved ÷ despatch days**: X ≥ 50% · Y 10–50% · Z < 10%. **Never CoV** — see the mistakes table in `24`.
- `SKU lines per day = SKU pick lines ÷ despatch days` → the speed band for the treemap
- Design year: grow peak day by the client's growth assumption, and **state whether that assumption is cumulative or per annum** — over five years the two differ by roughly 2×.

## Reference capacities & parameters
**Tote ladder (m³)** — 16th 0.0012775 · 8th 0.00511 · Quarter 0.01022 · Half 0.02044 · Full 0.04088 · Full Big 0.12768 · Pallet 1.44.
*(litres = m³ × 1000: 1.28 / 5.11 / 10.22 / 20.44 / 40.88 / 127.68 / 1,440 L.)*

**Full-tote fractions (empty-adjusted)** — 16th ÷14 · 8th ÷7 · Quarter ÷3.96 · Half ÷1.98 · Full ×1 · Full Big / Pallet blank. *(Name fraction, NOT volume ratio.)*

**Euro pallet (Carton-Pallet ref)** — 120 × 80 cm, max height 200 cm, board 15 cm → usable **185 cm**, weight limit 1,000 kg, 6 orientations.

**Typical fills** — in-media / consolidate 75% · not-in-media / box-cube 50%. Always **calibrate to the client's actuals**.

**Typical caps** — small units (16th–Half) ≤ 5 · Full / Full Big ≤ 20 (escalate one size up) · top size (Pallet) uncapped.

## Glossary
- **Box cube** — volume of one carton (`L×W×H`). Sizes the *unit*.
- **Stock cube** — total volume of the stock a SKU holds. Sizes the *count*.
- **Fill %** — how full a unit is assumed to be packed; lives in the count step.
- **Full-tote / full-unit equivalent** — locations expressed as whole top-units, by name fraction.
- **Utilisation** — stock cube ÷ available cube in the chosen units.
- **Location band** — bucket of how many locations a SKU needs (1 / 2–5 / …).
- **Binding constraint** — what capped a pallet: HEIGHT or WEIGHT.
- **Cannot-palletise / Oversized** — box too big for any orientation / the top unit; flagged, not dropped.
- **Remaining stock** — total minus the kept media, by subtraction.
- **Design year** — the future year the forecast sizes for.
- **Pick line** *(Engine C)* — one order-line row: one visit to one face. **The unit all throughput is sized on.** A line for 6 units is still one pick.
- **Despatch day** — a day on which anything actually shipped. All per-day maths divides by this, never by 365.
- **Order** — a distinct order number. Drives packing and the singles problem, not pick effort.
- **Units / items** — the quantity on a line. Drives replenishment, **not** picking work. Never conflate with lines.
- **ABC** *(Engine C)* — bands on pick count per SKU: how much work a SKU makes.
- **XYZ** *(Engine C)* — bands on **days moved**: how *often* a SKU moves. Frequency, not variability.
- **Speed band** — pick lines per despatch day for a SKU; the grouping/colour field for the treemap.
- **Seasonality band** — weeks of the year a SKU moved. The all-year count is the floor on live faces.
- **Peak day / week factor** — peak ÷ average. The multiple the pick engine must absorb.

## Environment / tooling cheats
- `python` is blocked (Windows Store alias) → use **`py`** (Python 3.14). Pillow, python-pptx, numpy available.
- Power BI: live model via `mcp__powerbi-modeling`; port changes each restart → `ListLocalInstances` then `Connect`. **`Refresh` type `Calculate`** after adding calc columns. Aggregate with `GROUPBY` + `SUMX(CURRENTGROUP(),1)`.
- Excel: backend doesn't recalc → Excel **COM** `CalculateFull` + `Save`. New file → blank via COM `Workbooks.Add`/`SaveAs 51` first.
- Edge headless for HTML→PNG; Pillow autocrop; Pillow multi-image → PDF.

## Reference results (sanity anchors)
- **WAMAS** whole warehouse: 401,655 SKUs / 8,391 m³ / 2,623 M units.
- **Carton-Pallet:** ~148k SKUs; S1 ≈ 98 vs S2 ≈ 96 cartons/pallet; ~91% HEIGHT-bound.

---
*Trivium Analytics · Warehouse Consulting Analysis Playbook · v0.1 · 2026-07-28 · back to [[00_Overview]] · recheck with [[24_Recheck_and_Common_Mistakes]]*
