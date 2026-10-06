# 08 · Storage & Location Analysis — utilisation, fragmentation & the SKU-split

> **What this is.** *(Layer 6.)* The location side of storage: how full the building actually is, where it's wasted, and how scattered each SKU is. **Fragmentation** — one SKU spread across many locations — is one of the most valuable and most-missed findings in a warehouse study, and it's pure opportunity: it costs travel, blocks consolidation, and hides real capacity.

> **How to use it.** Runs off an inventory-by-location snapshot. Sits between inventory (`07`) and slotting (`18`); its utilisation numbers feed capacity (`16`) and its fragmentation numbers feed the business case.

---

## Inputs

- **Inventory by location** — one row per SKU × location × quantity, with location type.
- **Location master** — location type, and its **capacity** (in pallets / cartons / totes / bins / cube / weight). Without a capacity you can measure occupancy but not utilisation.
- Pick frequency per location (from `06`) — to overlay activity on occupancy.
- The physical panel from `04` (aisles, racks, levels, zones).

> If there's no location capacity, ask for it — or derive a nominal one from the location type. State which.

---

## What you do

### 1 · Location profile
For every location: type · assigned SKU(s) · quantity · cube · weight · pick frequency · occupancy. This is the base table everything else rolls up from.

### 2 · Utilisation ⭐
`Utilisation = occupied capacity ÷ available capacity`, computed **per basis** — by pallet, carton, tote, bin, **cube** and **weight** — because they disagree, and the disagreement is the finding. Roll utilisation up **by aisle · rack · level · zone · storage type**. Then flag:

- **Under-utilised** locations (low occupancy) — reclaimable or re-slottable.
- **Overloaded** locations (over capacity / spilling) — a service and safety issue.
- **Empty** locations — real spare capacity, or stranded (wrong type/zone)?

> **Average utilisation lies about peak.** A building at 85% average can be at 100% at the stock peak. Always show peak utilisation next to average.

### 3 · Storage fragmentation ⭐⭐
For every SKU: **how many locations does it occupy?**

| SKU | Stock | Locations |
|---|---|---|
| SKU A | 1,000 | 1 |
| SKU B | 1,000 | 5 |
| SKU C | 1,000 | 15 |

Then compute **average locations / SKU** and **% of SKUs occupying more than one location**. A high figure means stock is scattered — every extra location is extra travel, extra replenishment, and capacity you can't see because it's half-empty faces.

### 4 · SKU-split distribution
Band the SKUs: in **1 · 2 · 3 · 4–5 · 6–10 · 10+** locations. Compute the **fragmentation rate**. This is the number you carry straight into the current-vs-future comparison — an optimised future state consolidates the tail, and "10,432 SKUs in 4+ locations today → 1 each" is a headline saving.

### 5 · Density opportunities
Cross **occupancy** with **height/level use** and **pick activity** to find high-density opportunities: cold, tall, half-empty zones that could hold far more if re-slotted or re-racked.

---

## Outputs

- Utilisation by **basis** and by **aisle/rack/level/zone/type**, avg **and** peak, with under/over/empty flagged.
- The **fragmentation** table: average locations/SKU, % multi-location, and the **SKU-split band** distribution.
- A quantified consolidation opportunity (locations reclaimable, % capacity freed) — a direct opportunity-tree branch.
- So-whats attached: *"32% of SKUs sit in 2+ locations; consolidating the 4+ tail frees ~9,000 faces."*

---

## Checklist before you move on

- [ ] Utilisation is on **available capacity**, per **basis**, and **peak** is shown, not just average.
- [ ] Fragmentation is per **SKU across locations**, and the split bands sum to the SKU total.
- [ ] Empty locations are split into **usable spare** vs **stranded** (wrong type/zone).
- [ ] The consolidation opportunity is **quantified** (locations / % / travel).
- [ ] Location totals **reconcile** to inventory (`07`) and the current-state (`13`).

---

> **Why this earns its place.** Clients believe they're "full" and need a bigger building. Fragmentation and true utilisation usually prove they're *scattered*, not full — capacity is hiding in half-empty, multi-slotted faces. Turning "we're out of space" into "you have 15% hidden capacity once the top-2,000 SKUs are consolidated" is one of the highest-value moves a warehouse consultant makes, and it defers or kills a capital project.

*Trivium Analytics · Warehouse Consulting Analysis Playbook · 08 · 2026-08-18 · pairs with `07`, feeds `16`, `18`, `20`*
