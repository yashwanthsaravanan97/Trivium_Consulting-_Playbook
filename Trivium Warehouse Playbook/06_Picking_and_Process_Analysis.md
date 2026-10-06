# 06 · Picking & Process Analysis — the actual work

> **What this is.** *(Layer 4.)* The demand analysis (`05`) tells you what *arrives*; this tells you how the warehouse *does the work* — pick volumes and types, where the activity concentrates, how productive it is, whether pick faces are the right size, and how much replenishment they trigger. This is the layer that decides which picking technology fits.

> **How to use it.** Runs off **pick / task transactions** (with location and, ideally, time and operator). Where those don't exist, fall back to order lines from `05`. Feeds bottleneck (`16`), slotting (`18`) and automation (`19`).

---

## Inputs

- **Pick transactions** — one row per pick: SKU, location, quantity, order, and (ideally) timestamp, operator, equipment, zone.
- Order lines from `05` (as a fallback and to reconcile).
- Location + capacity from `08`; daily demand per SKU from `05`.
- Labour/shift data if productivity is in scope (see `17`).

---

## What you do

### 1 · Pick volume
Total picks, then per **day · hour · shift**, and the ratios: picks / SKU · picks / order · picks / order-line · units / pick · lines / pick. Break down **by location · zone · picker · equipment** where the data allows. *Reconcile picks back to order lines* — a big gap is a data finding.

### 2 · Pick-type analysis ⭐
Split activity into **each · split-case · case/carton · pallet · full-pallet · replenishment · bulk** picks, and report each as a **% of total activity**. This split, more than anything, points to the technology:

| Dominant pick type | Typically points to |
|---|---|
| Each / split-case, single-unit | Goods-to-person, pick-to-light, put-walls, A-frame, sortation |
| Case / carton | Pick-to-light, voice, conveyor |
| Pallet / full-pallet | AS/RS, pallet shuttle, VNA, AGV |

### 2a · Which picking *method* is running? ⭐

Pick **type** (each / case / pallet) says what is grabbed. Pick **method** says how the work is organised —
and it is the lever that changes throughput without buying anything. Establish which is in use, per area:

| Method | One picker handles | Best when | Trade-off |
|---|---|---|---|
| **Discrete** (order-at-a-time) | one order, start to finish | Few, large orders | Most travel per line |
| **Batch** | many orders, one tour, sorted after | Many small orders, common SKUs | Needs a sort step |
| **Zone** | one zone, orders pass between zones | Big building, distinct product types | Needs consolidation; multi-zone orders wait |
| **Wave** | released in timed waves against cut-offs | Hard despatch cut-offs, carrier windows | Creates peaks by design |
| **Cluster** | several orders in one trolley/cart | Small orders, single-unit heavy | Cart size limits it |
| **Zone + batch** | the usual real answer | High volume, mixed profile | Most complex to schedule |

**Diagnose it from the data**, don't just ask: lines per picker-tour, orders touched per tour, how many
zones an order spans, and the gap between order-release time and first pick.

> **The finding that pays for itself:** *multi-zone orders*. Count how many orders span 2, 3, 4+ zones and
> what share of total time they consume. Consolidation, not picking, is usually where the time goes — and
> re-slotting to collapse an order into fewer zones is a **capital-free** throughput gain.

### 3 · Pick concentration (Pareto) ⭐
How many **SKUs** generate 80% of picks? How many **locations** generate 80% of travel? How many SKUs carry 80% of order lines? A steep curve → a compact fast-pick zone works; a flat one (measure it, don't assume 80/20 — see `05`) → the system must reach the whole range.

### 4 · Picking productivity *(if labour data exists — see `17`)*
Picks / lines / units / orders **per person-hour**, travel distance and time per pick/order, pick time per order. Compare against a **benchmark or target**, and express the gap in FTE — that's the labour saving the future state banks.

### 5 · Hot & cold locations ⭐
Rank every location by pick activity. **Hot** = high activity; **cold** = near-idle. Then ask the question that opens slotting: **are the hot SKUs in the most accessible locations (golden zone, low bays, near despatch)?** Usually they are not — and the mismatch is quantifiable travel.

### 6 · Pick-face analysis
For each fast SKU: **daily demand vs pick-face capacity** → *how many days of demand does the face hold?* Then flag **face too small** (triggers constant replenishment) and **face too large** (wastes prime picking space), and recommend an **ideal face size** per SKU band.

### 7 · Replenishment
Replenishment frequency and quantity, replenishments / day and / SKU, **emergency replenishments** and **pick-face shortages**. Identify the SKUs driving excessive replenishment — usually undersized faces on fast movers, which is a slotting fix, not a labour one.

---

## Outputs

- Pick-volume tables (day/hour/shift and the ratios) and the **pick-type split** with technology read-across.
- The **pick / travel Pareto** and the SKU-count ladder.
- Productivity vs benchmark (if data allows), with the gap in FTE.
- **Hot/cold** location map and the hot-SKU-vs-accessible-location mismatch (→ `18`).
- Pick-face fit (days-of-demand) and the replenishment drivers.

---

## Checklist before you move on

- [ ] Picks **reconcile** to order lines; any gap is explained.
- [ ] Everything per-day/hour divides by **working** days/hours, and **peak** is shown.
- [ ] The **pick-type split** is quantified and read across to technology options.
- [ ] Concentration is **measured**, not assumed 80/20.
- [ ] Hot SKUs vs accessible locations is stated as a **quantified** mismatch (→ slotting).
- [ ] Pick-face and replenishment findings name the **driving SKUs**, not just totals.

---

> **Why this earns its place.** Vendors quote against **lines per hour at peak**, and the **pick-type mix** decides whether the answer is a goods-to-person store, a conveyor-and-sorter line, or pallet automation. Get this layer wrong and the automation sizing (`19`) is built on sand. Get the hot/cold and pick-face findings right and you've written half of slotting (`18`) for free.

*Trivium Analytics · Warehouse Consulting Analysis Playbook · 06 · 2026-08-18 · pairs with `05`, feeds `16`, `17`, `18`, `19`*
