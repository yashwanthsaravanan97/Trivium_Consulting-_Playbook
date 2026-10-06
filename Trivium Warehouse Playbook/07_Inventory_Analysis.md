# 07 · Inventory Analysis — what's stored, how much, and how well it earns its space

> **What this is.** *(Layer 5.)* The stock side of the study. Not just "how many units" — but how much **cube** it occupies, how many **days of demand** it represents, how **old** it is, and where stock and movement are mismatched. This is where the biggest, cheapest wins usually hide: dead stock and overstock are space you can reclaim without buying anything.

> **How to use it.** Runs off an inventory snapshot (ideally several, to see movement). Pairs with the demand analysis (`05`) — inventory only means something *against demand*. Feeds storage sizing (`10`), location analysis (`08`) and slotting (`18`).

---

## Inputs

- An **inventory snapshot** — SKU × location × quantity (× batch/date if available). More than one snapshot lets you see average vs peak and movement.
- **Item master** with dimensions/weight (for cube; if missing, run `02a` first — cube is only as good as the dimensions).
- **Average daily demand per SKU** from `05` (units/day) — needed for coverage.
- Receipt / batch dates if aging is in scope.

---

## What you do

### 1 · Inventory level — quantity
Total, average, peak and minimum inventory, then break it down **by SKU · location · zone · storage type · product category**. Average vs peak matters: a warehouse sized on average inventory overflows at the stock peak.

### 2 · Inventory cube ⭐
Quantity alone hides the storage problem. Convert to **cube = quantity × unit volume** and analyse total / average / peak cube, and cube **by SKU / location / zone / storage type**. This is the number that sizes storage — a SKU with 2 units of a pallet-sized item needs more space than 2,000 units of a small part.

### 3 · Days of inventory / coverage ⭐
Per SKU: `Days of Inventory = current inventory ÷ average daily demand`. Then find the two mismatches that matter:

- **Overstocked** — high inventory, low movement (huge coverage). *Space you're paying for and not turning.*
- **Understocked** — low inventory, high movement (tiny coverage). *Service risk / emergency replenishment.*

Cross **inventory** against **movement** (from `05` velocity) in a 2×2. The **high-inventory / low-movement** quadrant is usually the single largest storage-reduction opportunity in the data.

### 3a · Inventory turns — the number a finance director already knows ⭐

Days-of-cover is the operational view; **turns is the financial view of the same thing**, and it is the
metric the client's own board reports on. Give both, because they open different conversations.

```
Inventory turns = annual demand (units or COGS) / average inventory (same unit)
Days of inventory = 365 / turns          -- or: average inventory / average daily demand
```

- Compute **per category and per site**, never only in total — a healthy blended figure routinely hides a
  category turning twice a year.
- Use **average** inventory across the snapshots you have, not a single month, and say which.
- Turns in **units** is the operational number; turns on **COGS** is the one finance recognises. If you
  have cost, give both.

**Read it across to space:** halving the cover on the slowest-turning categories is the cheapest storage
reduction available, and it needs no capital — only a purchasing decision. Put the turns table beside the
cube table so the reader sees which categories are eating the building.

> Slow turns are not automatically wrong — seasonal ranges, long-lead imports and safety stock on critical
> lines all turn slowly for good reasons. **Ask why before you call it excess.**

### 4 · Inventory aging
If receipt/batch dates exist, band the stock: **0–30 · 31–60 · 61–90 · 90–180 · 180–365 · 365+ days**. Then name:

- **Dead stock** (365+, no recent movement) — reclaimable space, and a write-off conversation.
- **Slow / excess stock** — candidates for consolidation or de-slotting.

Especially valuable in consumer goods, fashion and anything with shelf-life.

### 5 · Where the stock sits
Inventory by **storage medium / zone / temperature / hazard class** — the bridge to the current-state (`13`) and location (`08`) analyses, and the reconciliation that every stock number must tie back to.

---

## Outputs

- Inventory **quantity and cube** tables/charts by SKU, location, zone and storage type (avg and peak).
- A **days-of-inventory** distribution and the **inventory × movement 2×2** with the four quadrants sized.
- An **aging** profile with dead-stock and excess-stock quantified (units, cube, %, and value if cost is available).
- A one-line so-what per finding — e.g. *"18% of cube is stock that hasn't moved in a year — reclaim it and the footprint problem largely disappears."*

---

## Checklist before you move on

- [ ] Cube is computed from **verified** dimensions (`02a`), not trusted blindly.
- [ ] Coverage uses average daily demand over **working days**, not calendar days.
- [ ] Over- and under-stock are both surfaced, and the **quadrant sizes** are stated.
- [ ] Aging bands sum to the whole; dead/excess stock is quantified in **cube and %** (and value if possible).
- [ ] Every inventory total **reconciles** to the whole warehouse and to the current-state (`13`).
- [ ] Each finding carries a **so-what** (space / service / cash), not just a number.

---

> **Why this earns its place.** A future-state that needs less space is far easier to fund than one that needs a bigger building. Dead stock, excess coverage and the high-inventory/low-movement quadrant are space the client already owns and is wasting — reclaiming it is capital-free capacity, and it's usually the first recommendation a warehouse director will actually act on.

*Trivium Analytics · Warehouse Consulting Analysis Playbook · 07 · 2026-08-18 · pairs with `05`, feeds `10`, `08`, `18`*
