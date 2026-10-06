# 00a · The Analysis Framework — the consulting spine

> **What this is.** The mindset that separates a **client-ready consulting analysis** from a pile of KPIs. Read it before any module. Every other file in the playbook is one branch of the chain set out here; this file is the trunk.

> **The one idea.** You don't just calculate numbers — you build a **chain**:
> **Raw data → Data validation → Current-state understanding → Demand & inventory behaviour → Process analysis → Capacity analysis → Bottleneck identification → Future-state requirements → Solution sizing → Business case → Recommendations.**
> A KPI answers "what". Consulting answers "what → so what → now what". Hold that in your head on every chart you make.

---

## 1 · The 10 layers of a warehouse analysis

Think of every project as ten layers. You rarely run all ten — the client's question and the data they sent decide which — but you should always know *where in the stack* you are, and which layer the client actually cares about.

| Layer | Main question | Playbook files |
|---|---|---|
| **1. Data quality** | Can we trust the data? | `02`, `02a`, `02b` |
| **2. Business profile** | What does the warehouse actually handle? | `03`, `04` |
| **3. Demand analysis** | What, how much, and *when* do customers order? | `05`, `15` |
| **4. Order & picking analysis** | How does work flow through the warehouse? | `05`, `06` |
| **5. Inventory analysis** | What is stored, how much, and where? | `07` |
| **6. Warehouse / capacity analysis** | How much physical capacity is being used? | `08`, `10`, `11`, `13` |
| **7. Operational analysis** | Where are the bottlenecks and inefficiencies? | `16`, `17` |
| **8. SKU / slotting analysis** | Where *should* SKUs be stored? | `18` |
| **9. Automation analysis** | What should be automated, and how much? | `19` |
| **10. Future state & business case** | What should the client implement? | `20` |

The layers build on each other: you cannot size automation (9) without the demand and peak (3–4), and you cannot defend a business case (10) without the current-state numbers (2–7). **Skipping a lower layer to jump to the answer is the most common way a study gets torn apart in the client review.**

---

## 2 · The golden rule — the five questions

For every dataset, don't ask only *"what can I calculate from this?"* Run every finding through five questions. This is the single most important habit in the playbook.

| # | Question | Example |
|---|---|---|
| 1 | **What is happening?** | "Picking volume is up 25%." |
| 2 | **Why is it happening?** | "Order lines rose while average lines/order fell — more, smaller orders." |
| 3 | **Where is it happening?** | "78% of the increase is in Zone A." |
| 4 | **What is the impact?** | "Picker travel up 18%; peak capacity now exceeded." |
| 5 | **What should the client do?** | "Re-slot the top 2,000 SKUs and add zone-based picking / AMR support." |

That progression — **DATA → INSIGHT → ROOT CAUSE → IMPACT → RECOMMENDATION** — is what turns data analysis into consulting analysis. A slide that stops at question 1 is reporting; a slide that reaches question 5 is consulting. **Every headline in the deck should be able to answer all five.**

> **Worked example — the five questions on real data (Project Bombay).**
> **1. What?** The e-commerce channel picks 7,736,829 lines on Tuesdays — 37.0% of its entire year — against 1.87–2.59 M on every other weekday.
> **2. Why?** It is not customer behaviour; buyers do not shop four times harder on Tuesday. It is an order-release cadence, and it is concentrated on one node: that site is 81.05% Tuesday and active on only 250 of 364 days.
> **3. Where?** One fulfilment centre. The other four nodes sit at 19–22% and run all 364 days.
> **4. Impact?** Sized on that arrival peak the goods-to-person store needs **51 workstations and 286 robots**, running at 94.5% utilisation for one hour a week and 7.3% at the mean hour. Sized on a levelled release, identical annual volume needs **7 workstations and 25 robots**.
> **5. Do what?** Make release levelling **Phase 0** of the automation programme — it precedes and re-sizes every capital line after it. Establish first whether the Tuesday cadence is a commercial commitment or an inherited allocation rule.
>
> Note what makes it consulting: question 1 is a number anyone could produce. Question 2 is the one that took judgement. Question 5 is the one the client pays for.

---

## 3 · The insight layer — chain the numbers, don't list them

Don't show a fact. Show a **funnel** that lands an implication. The difference:

> ❌ *"There are 150,000 SKUs."*
>
> ✅ *"150,000 SKUs → 42,000 active → 8,000 SKUs generate 80% of picks → 2,500 SKUs generate 60% → 18% of storage locations are fragmented → 12% are under-utilised → 35% of travel comes from 8% of SKUs."*

The second version is a recommendation already forming in the reader's head. Whenever you can, **compress a chain of related numbers into one sentence that ends in a "so-what"**. That is the layer clients remember and pay for.

---

## 4 · The opportunity tree — quantify every problem

A problem named without a number attached is worth nothing. For every inefficiency, **quantify the opportunity** and hang it on a tree, so the study ends with a costed list of moves, not a list of complaints.

```
Warehouse inefficiency
├── Storage      → fragmentation · poor utilisation · excess stock · poor slotting
├── Picking      → excess travel · poor pick-face sizing · high replenishment · manual movement
├── Outbound     → peak congestion · manual sorting · staging space
└── Labour       → high travel · low productivity · peak overtime
```

For each branch, attach a number the client can act on:

- Potential **space** saving (locations / m³ / %)
- Potential **labour** saving (FTE / hours / cost)
- Potential **throughput** increase (lines/hr / %)
- Indicative **CAPEX** to unlock it

That turns "your slotting is poor" into "re-slotting the top 2,000 SKUs cuts travel ~35% ≈ 6 FTE ≈ £Xk/yr for a £Yk fit-out" — which is a decision, not an observation.

> ### ⚠ The branch you cannot quantify
> Some branches will have no number, because the data to size them was never sent. **Show them anyway,
> with the missing input named** — never with an estimate dressed as a measurement:
>
> | Branch | Opportunity | Prize | Status |
> |---|---|---|---|
> | Labour | Travel reduction from re-slotting | **BLOCKED** | needs location coordinates |
> | Labour | FTE saving vs benchmark | **BLOCKED** | needs T&A export and rate table |
>
> A blocked branch is a **finding and a data request**, not a hole to fill with a plausible figure. The
> pressure to put *something* in the box is exactly where fabricated numbers enter a study.

---

## 4a · When two layers disagree — adjudicate, don't pick

It happens: two analyses, both internally correct, produce incompatible numbers for the same thing.
This is normal in a multi-layer study and it must be **settled before either number ships**.

1. **Reproduce both** from source, independently. Do not argue from the outputs.
2. **Find the basis difference** — almost always a different population, window, grain or field.
3. **Test each against an independent third source** — the client's own control total, a different field
   that measures the same thing, or the physical reality.
4. **Publish the adjudication**, not just the winner: what each side computed, why they differ, which
   basis is right, and what the corrected figure is.
5. **Propagate** — every downstream number built on the losing basis is now wrong; re-run them (`22`).

> **Worked example — Project Bombay.** One module reported a site's peak storage cube as 15,252 m³ and
> declared the raw item-master field inflated by 68%; two others used the raw field and reported 25,598 m³.
> Both were internally consistent. Adjudicated from first principles: only **16 stocked SKUs of 62,206**
> carried an impossible cube; corrected, the true peak was **24,635 m³ — overstated by 3.9%, not 68%**.
> Sizing on the raw field over-provisions by ~960 m³; sizing on the over-corrected figure
> **under**-provisions by ~9,400 m³, which is far more dangerous. Neither original answer was usable.

---

## 5 · Which analyses to master first — the tiers

You don't run every analysis on every project; some the data won't support, some the client won't value. But as a **default order of attack**, prioritise like this:

**Tier 1 — always do** *(the spine; a study without these is incomplete)*
Data quality · data reconciliation · volume profile · order analysis · order-line analysis · SKU analysis · pick analysis · inventory analysis · storage capacity · utilisation · peak analysis · seasonality · ABC / Pareto · process flow · bottleneck analysis.

**Tier 2 — usually do** *(what makes it a proper consulting study)*
SKU velocity · ABC-XYZ · inventory aging · days-of-inventory · storage fragmentation · location utilisation · pick-face analysis · replenishment · slotting · labour productivity · equipment utilisation · travel analysis · inbound / outbound analysis.

**Tier 3 — advanced consulting** *(what makes the work stand out)*
SKU affinity · cube-per-pick · demand variability · automation suitability · automation sizing · capacity simulation · growth scenarios · future-state modelling · CAPEX / OPEX · ROI / payback · sensitivity analysis · optimisation scenarios.

> **How to use the tiers.** On a fixed-scope job, promise Tier 1, aim for Tier 2, and reach into Tier 3 for the one or two analyses that answer *this* client's specific question. Never skip a Tier 1 layer to show off a Tier 3 one — a slick affinity chart on top of un-reconciled data is worse than nothing.

---

## 6 · What each layer needs (so you know what to ask for)

Map the client's data to the layers up front, in file `01`, so you know on day one which layers are live and which are blocked for want of data.

| If they can send… | …it unlocks |
|---|---|
| **Order-line history** (order, item, date, qty) | Layers 3–4: demand, order shape, ABC/XYZ, peak, automation sizing — *even with no volumetrics* |
| **Item master with dimensions / weight** | Layers 5–6: cube, storage sizing, slotting, cube-per-pick |
| **Inventory snapshot** (SKU × location × qty) | Layers 5–6: inventory, coverage, utilisation, fragmentation |
| **Pick / task transactions** (with location, time, operator) | Layers 4 & 7: productivity, hot/cold, travel, bottleneck |
| **Inbound / receipt & dispatch data** | Layer 4/flow: inbound vs outbound balance, the dispatch wave |
| **Labour & MHE data** | Layer 7: FTE, productivity, equipment utilisation |
| **Site layout / location coordinates** | Layer 7–8: travel distance, slotting, the strongest analyses of all |

**The single most useful follow-up you can ever ask for is time-stamped transactions** — they turn a flat daily average into a real peak-hour, and the peak is what sizes the solution.

---

## Checklist — before the study ships

- [ ] Every headline finding answers all **five golden-rule questions**, not just "what happened".
- [ ] Every named problem carries a **quantified opportunity** (space / labour / throughput / CAPEX).
- [ ] The **Tier 1** layers are all present, or their absence is explained by missing data.
- [ ] The story is a **chain to a recommendation**, not a list of KPIs.
- [ ] Everything is **sized on the peak**, not the average, and the peak factor is stated.
- [ ] Every number **reconciles** to the whole (see `24`, `25`).

---
*Trivium Analytics · Warehouse Consulting Analysis Playbook · 00a · 2026-08-18 · then work the layers via `00_Overview`*
