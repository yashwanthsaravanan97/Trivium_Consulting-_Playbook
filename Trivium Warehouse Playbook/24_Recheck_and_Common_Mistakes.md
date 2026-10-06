# 24 · Recheck & Common Mistakes — the final pass before it ships

> **What this is.** The last thing you do before anything goes to the client or to the boss: re-check the whole project against the mistakes that have *actually* bitten us. Tick every box. If one fails, fix it and **re-run the chain** (file `22`).

> **How to use it.** An interactive checklist — work top to bottom. Each item is worded so that "no / not sure" means *stop and fix*, not "probably fine".

---

## 0 · Did we build what the client actually asked for?
- [ ] I can state the client's wanted output in **one line** (a footprint number? a per-pallet quantity? a current-vs-proposed comparison?).
- [ ] The final deliverable answers *that* question — not a related one we found more interesting.
- [ ] The unit and the engine match the brief (tote vs pallet · cubic vs cartons-per-pallet).

## 1 · Does it reconcile? *(the number-one check)*
- [ ] Every model total ties back to the whole — total SKUs, total m³, total units (or total cartons).
- [ ] Sub-tables (per medium, per category, per tote size) **sum to** the grand total.
- [ ] "Everything except X" was built by **subtracting X from the total**, not by adding up the pieces (there is always unattributed stock).
- [ ] Edge SKUs (zero-cube, oversized) are **shown and counted**, not silently dropped.

## 2 · Is the data right?
- [ ] Units are consistent — box dims parsed to one unit (mm), no mm/cm/dm mix.
- [ ] Missing / impossible measurements handled — zero-cube (no dimensions) is **flagged**, not blank-and-forgotten.
- [ ] Display glitches caught (e.g. `######` weights coerced to NaN, placeholder ≤1 cm rows removed).
- [ ] Row counts before/after cleaning are recorded and explainable.

## 3 · Is the model right?
- [ ] Fill % was **calibrated to reproduce the client's actuals**, not guessed.
- [ ] Location **caps** applied (small ≤ 5, Full / Full Big ≤ 20 escalating, Pallet uncapped) — or the project's agreed caps.
- [ ] **Full-tote equivalents use the tote-NAME fraction** (16th ÷14, 8th ÷7 …), *not* the volume ratio. *(This bit us — see §6.)*
- [ ] Weight is a **TOTAL** constraint — `FLOOR(limit ÷ unit weight)`, *not* per-layer. *(This bit us too.)*
- [ ] Both scenarios / orientations tested where the engine calls for it; the **binding constraint** is reported.

## 4 · Are the buckets right?
- [ ] Buckets / bands are defined from the data (weight, cover, location bands, tote buckets) and their edges make sense.
- [ ] Bucket counts **sum to** the population.

## 5 · Are the deliverables consistent?
- [ ] **Every** HTML, Excel sheet and PPT slide is on the **same data basis** — nothing left on the old total after an update.
- [ ] Excel formulas were **recalculated** — the backend does *not* recalc; open via Excel COM, `CalculateFull`, Save; linked sheets (forecast) followed.
- [ ] Power BI calc columns were **recalculated** (`Refresh` type `Calculate`) or the queries error.
- [ ] Number formats clean (`#,##0`, `%`), the house style consistent, dates and labels correct.

---

## 6 · Mistakes we actually made — and the rule that came from each

| What happened | The rule now |
|---|---|
| Full-tote used the **volume ratio (÷32)** for the 16th — obviously wrong; a 16th is a *half-height* tote (1.28 L = 1/32 by volume, but named "16th"). | Full-tote = **tote-name fraction**, empty-adjusted: 16th ÷14, 8th ÷7, Quarter ÷3.96, Half ÷1.98, Full ×1; Full Big / Pallet blank. |
| Weight limit applied **per layer** → 12,416 SKUs returned 0 cartons. | Weight is a **total** constraint: `FLOOR(limit ÷ unit weight)`. |
| After appending 13,860 missing SKUs, some tables still showed the **old total** (387,795 vs 401,655). | **Re-run the whole chain** — recalc the model, then refresh *every* HTML, workbook and slide (file `22`). |
| Excel showed **stale formula values** after a programmatic write. | The write backend doesn't recalc — open via Excel **COM**, `CalculateFull`, Save. |
| Power BI query failed: *"column … needs to be recalculated."* | Run `Refresh` type `Calculate` after adding calculated columns, before querying. |
| A "total" wouldn't split cleanly into its parts. | **Subtract, don't sum** — unattributed stock is normal; derive "remaining" by subtraction. |
| Modelled before really looking at the data → surprises later. | **Understand the data first** (file `03`) — the HTML data-summary is not optional. |
| **XYZ on the textbook CoV of weekly demand classified 1 SKU out of 5,008 as X.** The minimum CoV in the data was 0.47 against a 0.5 threshold — a seasonal business is seasonal for *every* product, so CoV just re-measured the season. | Band **XYZ on days moved**, as a share of despatch days (X ≥ 50% · Y 10–50% · Z < 10%). Frequency discriminates, and maps straight onto the slotting decision. See `05`. |
| **ABC on a cumulative-Pareto cut split 6 SKUs that tied on pick count across two bands**, needed a helper table to compute, and couldn't be explained in one sentence. | Band **ABC on fixed pick-count thresholds**, tuned so line shares land near 80/15/5. Same velocity → same band, always. See `05`. |
| Assumed 80/20 held, and nearly recommended a compact fast-pick module. The real curve needed **35.5% of SKUs to reach 80% of lines**. | **Measure the Pareto, never assume it.** Quote the SKU ladder (50/80/90/95/99%) — a flat curve changes the technology choice. |
| Divided daily workload by 365 and understated the peak. | **Divide by despatch days** — the days anything actually shipped (Seeds: 274, not 365). |
| Counted a column containing blanks and reported the low figure as the total. | Power BI's `Count` **ignores blanks** — use a row count for volume. On Seeds this read 2,729,106 against a true 2,976,546. **Never drop rows because one cell is empty:** it broke the tie to the client's own control total and understated peak season by 18%. |
| A treemap pooled the slow tail into one tile, which then dominated the chart and read as a single giant SKU. *(Applies to **both** treemaps — the range treemap in `03` and the velocity treemap in `05`.)* | Include **every** SKU so the areas sum to the population; label only tiles big enough for text; colour an ordered measure with a **sequential single-hue ramp**, never categorical colours. |

---
*Trivium Analytics · Warehouse Playbook · v0.2 · 2026-08-11*
