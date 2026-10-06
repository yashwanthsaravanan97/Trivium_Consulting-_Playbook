# 18 · Slotting — where SKUs *should* be stored

> **What this is.** *(Layer 8.)* The most actionable output a warehouse consultancy produces: for each SKU, **current location vs recommended location**, and the travel / space / replenishment saving from moving it. It ties together velocity (`05`/`06`), cube (`07`), fragmentation (`08`) and travel (`17`) into one recommendation the client can implement next Monday — often with no capital at all.

> **How to use it.** Needs velocity, cube and the current layout. It's the pay-off of the current-state analysis and a headline of the future state (`20`). Even a "principles + top-N moves" version lands hard.

---

## Inputs

- SKU **velocity** (lines/day, days-moved) from `05`; **cube** from `07`; current **location** from `08`.
- Pick-face and replenishment findings from `06`; travel/coordinates from `17`.
- Constraints from `04`: golden-zone definition, temperature/hazard zones, weight/ergonomic rules, product compatibility.

---

## What you do

### 1 · The slotting logic
For each SKU, derive a recommended location from a ranked set of drivers:

**velocity · cube · weight · pick frequency · order affinity · replenishment frequency · ergonomics · temperature · hazard status · product compatibility.**

The first-order rule: **fast + small → golden zone / most accessible; slow + big → bulk / reserve.** Then the constraints (hazard, temperature, weight, compatibility) override — they're hard limits, not preferences.

### 2 · Cube-per-pick ⭐
`Cube-per-pick = storage cube required ÷ picking activity`. It surfaces SKUs that **consume large space but generate little activity** — prime candidates for bulk storage, a different medium, inventory reduction, or de-slotting out of the fast zone. High cube-per-pick in a golden location is money on the floor.

### 3 · Order affinity ⭐⭐ *(advanced)*
Which SKUs are frequently **ordered together**? (e.g. *SKU A + B in 35% of orders*). Affinity-slotting places co-ordered SKUs near each other, so one pick tour covers more of an order — a direct travel cut. It's an advanced, impressive analysis; even a top-20 affinity pairs list is compelling.

### 4 · Dedicated pick faces
From `06`'s pick-face work: which SKUs are hot enough to earn a **dedicated, correctly-sized face** (right days-of-demand, low replenishment)? Undersized faces on fast movers are the replenishment problem in disguise.

### 5 · Storage-medium fit
Match each SKU profile to the right **medium** — floor / selective rack / drive-in / carton flow / shelving / bin / tote / VLM / AS-RS / shuttle / mobile rack — from velocity + inventory + cube + accessibility. The wrong medium is a slotting problem one level up.

### 6 · Current vs recommended
Produce the move list: **SKUs that should move · should stay · need a dedicated face**. Quantify it — number of moves, **travel reduction %**, faces freed, replenishment reduction — so it reads as an opportunity, not an opinion.

---

## Outputs

- A per-SKU **current-vs-recommended** table (with the reason each move is made).
- **Cube-per-pick** ranking and the "space hogs in prime locations" list.
- **Order-affinity** pairs and the affinity-slotting opportunity.
- Dedicated-face recommendations and storage-medium fit.
- The headline: *"Re-slot the top N SKUs → ~X% travel cut ≈ Y FTE, Z faces freed."*

---

## Checklist before you move on

- [ ] The slotting logic and its **driver priority** are stated (and constraints applied as hard limits).
- [ ] **Cube-per-pick** is computed; the space-hog list is surfaced.
- [ ] Affinity is on SKUs **ordered together**, not just co-stocked.
- [ ] The move list is **quantified** (moves, travel %, faces, replenishment).
- [ ] Recommendations respect hazard / temperature / weight / compatibility.
- [ ] The saving is expressed in **travel, FTE and £** for the business case (`20`).

---

> **Why this earns its place.** Slotting is the recommendation clients act on fastest, because it's usually **capital-free** — the same racks, the same building, just a better arrangement. A quantified re-slot ("top 2,000 SKUs, ~35% less travel, ~6 FTE") is often the single highest-ROI line in the whole study, and it buys credibility for the bigger automation ask.

*Trivium Analytics · Warehouse Consulting Analysis Playbook · 18 · 2026-08-18 · pulls from `05`/`07`/`08`/`06`/`17`, feeds `19`, `20`*
