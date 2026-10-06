# 09 · Choose the Engine — tote (cubic) or pallet (cartons-per-pallet)?

> **What this is.** The fork in the road. This playbook holds **two sizing engines**; a project runs one or the other (sometimes both). You decide here — now that you understand the data (`03`) and the client's goal (`01`).

> **How to use it.** Answer the decision questions; they point you to `10` or `11`.

---

## The three engines
- **Engine A — Cubic sizing** *(size → footprint).* Size the storage unit (tote *or* pallet) from the **box cube**, count how many units are needed from **stock cube × a realistic fill %**, cap runaway SKUs, and roll up into a footprint (locations, full-unit equivalents, utilisation). → **`10`**
- **Engine B — Cartons-per-pallet packing** *(carton → pallet qty).* For each SKU, find how many **cartons physically fit on one pallet** by testing box **orientations**, stacking into **layers** to a height limit, respecting a **weight** limit. → **`11`**
- **Engine C — Order profile & ABC-XYZ** *(order lines → throughput).* Size the **work**, not the space: daily and day-of-week profile, peak factors, order shape, ABC on pick count, the Pareto curve, XYZ on days moved, and the SKU treemap. Runs off a despatch history and **needs no volumetric data**. → **`05`**

## The decision questions
- [ ] **What number does the client want per SKU?** "How much storage does this need?" → **A.** "How many cartons per pallet?" → **B.** "How much picking work, and how fast does it arrive?" → **C.**
- [ ] **Is the question about space, pack quantities, or the pick operation?** Footprint → A. Replenishment / transport / depot ordering → B. **Automating or resizing the pick** → C.
- [ ] **What does the data support?** Only a stock volume + box dims → A works cleanly. Precise carton L×W×H + weights + pallet limits → B is available. **An order-line history and no measurements at all → C is the only engine that runs.**
- [ ] **Is storage measured in totes or pallets?** Totes → almost always A. Pallets → could be either (A for "how many pallets of stock", B for "how many cartons per pallet").
- [ ] **Is the client buying a machine that picks?** Goods-to-person, shuttle, AMR, pocket sorter — any of these → **C is mandatory**, because the vendor will quote against lines per hour at peak, not against cubic metres.
- [ ] **More than one?** Some jobs need A for the storage footprint *and* B for pallet quantities. A **storage + throughput** brief needs **A and C** — C sizes the work, A sizes the space to hold it. Run them separately and keep them clearly labelled.

## What you do
1. State the engine choice in one line, tied back to the goal from `01`.
2. Confirm you have the inputs that engine needs (measurements + limits).
3. Go to `10` or `11`.

## Outputs
- A recorded engine decision with its reason.

## Checklist before you move on
- [ ] The engine matches the client's wanted output, not the data we happen to have.
- [ ] The inputs that engine needs are all present (or listed as questions).
- [ ] If both engines, they're scoped separately.

---
> **Worked examples.** *WAMAS* → **Engine A** (tote footprint across ~400k SKUs). *Carton-Pallet* → **Engine B** (cartons per Euro pallet). *Project Seeds* → **Engine C** (order profile and ABC-XYZ to size a goods-to-person pick; the client had a 3.3M-row despatch history and, confirmed on site, **no volumetric data whatsoever** — so A and B were both unavailable and C was the whole job). It genuinely varies job to job — pick to the question.

*Trivium Analytics · Warehouse Playbook · v0.2 · 2026-08-11 · then [[10_Sizing_Cubic]], [[11_Sizing_CartonsPerPallet]] or [[05_Order_Profile_and_ABC_XYZ]]*
