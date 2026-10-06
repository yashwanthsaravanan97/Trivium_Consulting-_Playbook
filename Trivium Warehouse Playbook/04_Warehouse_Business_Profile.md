# 04 · Warehouse Business Profile — the fact sheet

> **What this is.** *(Layer 2.)* Before you go deep, answer one question on one page: **"What kind of warehouse are we dealing with?"** A profile that frames the whole study — volumes, physical size, and how it runs — so every later finding has a denominator to sit against.

> **How to use it.** Build it early, right after `03`. It becomes the **front page of the deck** and the reference every reviewer keeps glancing back to. It's also your first sanity check: if the profile numbers feel wrong to the client, the data is wrong, and you find that out on day one instead of week three.

---

## Inputs

- The cleaned, reconciled data from `02` (order lines, inventory snapshot, master data, and whatever transactional data exists).
- **Site-visit / client answers** — the physical and operational facts rarely live in the data: floor area, aisle/rack counts, ceiling height, shifts, operating hours, cut-off times, service promise, seasonal peaks.
- The despatch calendar from `05` (so every "per day" divides by real working days, never 365).

> Half of a good fact sheet comes from the client, not the extract. Send the blank fact sheet as a **data-request checklist** before the visit.

---

## What you do — fill three panels, then one headline

### 1 · The volume panel *(what moves)*
Per **working day** (avg and peak), and per year:

- SKUs — total, active, stocked, ordered
- Orders / day · order **lines** / day · units / day
- Cartons / day · pallets / day
- Inbound pallets / day · outbound pallets / day · returns / day

### 2 · The physical panel *(what it's held in)*
- Total warehouse area · storage · picking · receiving · dispatch · staging · mezzanine
- Number of storage locations · aisles · racks · levels · pick faces
- Clear ceiling height (drives whether height is being used)

### 3 · The operational panel *(how it runs)*
- Operating hours · shifts / day · working days / week
- Peak periods (season, day, hour) · cut-off times · service promise (order-by / ship-by)
- Order profile in one line (see the headline below) · product characteristics (size, weight, hazard, temperature, fragility)

### 4 · The headline — classify the warehouse in one sentence
The single most useful output. From **lines per order** and **units per line** (both from `05`), say what this warehouse *is*:

- **Each-pick / e-commerce**: many small orders, high single-line and single-unit share → points to goods-to-person, put-walls, sortation.
- **Case / multi-line / bulk**: fewer, larger orders, high lines-per-order and units-per-line → points to zone/batch picking, pallet handling.

Most warehouses are a **blend** — state the split by channel. This one sentence steers every downstream recommendation, so get it right and put it top-of-page.

---

### 5 · Benchmark it — a number with no comparator means nothing ⭐

A fact sheet full of the client's own numbers tells them what they already know. **Put a comparator beside
every headline ratio** so the reader can see whether it is good, normal or alarming:

| Ratio | Compare against |
|---|---|
| Lines per order · units per line | Their **own channels**, and the sector norm for that channel type |
| Lines per FTE-hour | Their other sites; the client's own target; a published rate for that pick method |
| Peak : average day | Their other channels — this is where a self-inflicted spike shows up |
| Days of cover | Their own policy, and category norms |
| Storage utilisation | Their stated capacity, and the practical ceiling (never 100% — see `08`) |

**The three comparators, in order of strength:**
1. **The client against itself** — site vs site, channel vs channel, this year vs last. Strongest, because
   it needs no external assumption and they cannot argue the basis.
2. **The client against their own stated target** — the gap to *their* number is a finding they own.
3. **An industry figure** — weakest, and it must be **labelled as an assumption with its source**. Sector
   ranges vary enormously by product type; a published lines/hour for grocery says nothing about jewellery.

> Never quote an industry benchmark as fact. *"Typical range 80–120 lines/picker-hour for split-case
> (assumption — client to confirm against their own standards)"* is defensible. A bare "the benchmark is
> 100" is not, and a client who knows their operation will take the whole study apart on it.

---

## Outputs

- A **one-page Warehouse Fact Sheet** (Trivium HTML card / the deck's Executive Overview page): the three panels as KPI tiles + the one-line classification.
- Avg **and** peak for every "per day" figure — never just the average.
- A short "what this tells us" note under the sheet (the golden-rule "so-what": e.g. *"92% single-unit lines — this is a goods-to-person case"*).

---

## Checklist before you move on

- [ ] Every "per day" divides by **working / despatch days**, not calendar days.
- [ ] **Avg and peak** are both shown; the peak factor is visible.
- [ ] The physical and operational facts are **sourced** (data vs client) and dated.
- [ ] The one-line **classification** is stated and defensible from lines-per-order / units-per-line.
- [ ] The client has **eyeballed** the fact sheet and agrees it describes their warehouse.
- [ ] Any number the data can't give is marked **"client to confirm,"** not guessed.

---

> **Why this page earns its place.** It is the denominator for the whole study. "12% of locations are under-utilised" means nothing until the reader knows there are 100,000 locations; "18,000 lines on the peak day" means nothing until they know the average is 10,000. The fact sheet supplies every one of those anchors, so put it first and keep it visible.

*Trivium Analytics · Warehouse Consulting Analysis Playbook · 04 · 2026-08-18 · feeds `05`, `07`, `16` and the deck's Executive Overview*
