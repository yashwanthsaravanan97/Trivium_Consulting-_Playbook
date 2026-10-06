# 14 · Inbound · Putaway · Outbound · Dispatch — the two ends of the building

> **What this is.** *(Layer 4 / flow.)* Most storage-sizing work looks only at the middle (stock and picks). This module looks at the **ends** — what arrives, how it's put away, what ships, and *when it ships*. The two findings that matter most: the **inbound-vs-outbound balance** (what constrains the warehouse), and the **dispatch wave** (when outbound congestion actually happens).

> **How to use it.** Runs off receipt and dispatch data. Optional but powerful for automation and dock/staging design. Feeds bottleneck (`16`) and the future state (`20`).

---

## Inputs

- **Inbound / receipt data** — receipts by SKU, quantity, pallets/cartons, date/time, supplier, ASN.
- **Outbound / dispatch data** — shipments by order, units/cartons/pallets, date/time, carrier, destination, route.
- **Putaway transactions** if available — from-to location, time, operator.
- Cut-off times, carrier departure times, dock and staging details from `04`.

---

## What you do

### 1 · Inbound
Receipts per **day · hour**; pallets / cartons / units per day; SKU receipts; ASN and supplier volume. Find the **receiving peak** and **dock utilisation**. A warehouse that receives in tight morning bursts needs different dock and putaway capacity than one with a flat inbound.

### 2 · Putaway
Putaway quantity · lines · locations · time · distance · productivity. Flag **replenishment demand** created downstream, and count **how many SKUs are put away into multiple locations** — that's fragmentation (`08`) being created at source.

### 3 · Outbound
Orders / lines / units / cartons / pallets shipped per **day**, plus shipments per day, broken down by **carrier · customer · destination · route**. Reconcile outbound against order lines (`05`) — they should agree.

### 4 · The dispatch wave ⭐
The highest-value chart here. Plot **outbound volume by hour** against **cut-off and carrier-departure times**. It reveals *when* the warehouse is under pressure — usually a sharp spike into a late-afternoon cut-off, which sizes staging space, dock doors, sortation and the labour peak. Add **staging volume/cube** and **pallets/cartons per truck** for the dock design.

### 5 · Inbound vs outbound balance ⭐
Put inbound and outbound volume side by side. The ratio tells you what the warehouse *is*:

| If… | The warehouse is… |
|---|---|
| Outbound ≫ inbound activity | **Picking / outbound constrained** — the automation case is on the pick |
| Inbound ≫ outbound | **Receiving / putaway constrained** — the case is on the dock and putaway |
| Both modest, stock high | **Storage constrained** — the case is on density |

This one comparison steers where the whole study should push.

---

## Outputs

- Inbound and outbound volume profiles (day/hour), with peaks and dock utilisation.
- The **dispatch-wave** chart against cut-offs, and staging/truck-load figures.
- The **inbound-vs-outbound balance** and the resulting "what constrains this warehouse" statement.
- Putaway productivity and the multi-location-putaway (fragmentation-at-source) finding.

---

## Checklist before you move on

- [ ] Outbound **reconciles** to order lines (`05`); inbound ties to receipts.
- [ ] Everything per-day/hour is on **working** time, and **peak** is shown.
- [ ] The **dispatch wave** is plotted against real cut-off / departure times.
- [ ] The **balance** statement (inbound / outbound / storage / picking constrained) is made explicitly.
- [ ] Staging and dock figures are captured if dock design is in scope.

---

> **Why this earns its place.** Two sentences from this module reframe an entire study: *"This is an outbound-constrained warehouse — 78% of the pressure is a two-hour dispatch spike into the 16:00 cut-off."* That tells the client where to spend, and it's invisible in any stock- or pick-total that isn't cut by the hour.

*Trivium Analytics · Warehouse Consulting Analysis Playbook · 14 · 2026-08-18 · pairs with `05`, feeds `16`, `19`, `20`*
