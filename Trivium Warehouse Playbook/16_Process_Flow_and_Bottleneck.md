# 16 · Process-Flow & Bottleneck Analysis — where it actually breaks

> **What this is.** *(Layer 7.)* The analysis that turns a pile of findings into a diagnosis. Map the warehouse as a **flow**, quantify demand and capacity at **every stage**, and find the **bottleneck** — the one stage where demand exceeds capacity. "Picking is the bottleneck at 120% of capacity on the peak day" is worth ten times "picking is inefficient", because it tells the client exactly where to spend.

> **How to use it.** It sits on top of every other analysis — it needs the demand (`05`, `15`), the picks (`06`), the inbound/outbound (`14`), and the labour/equipment (`17`). Run it late, once the pieces exist. It is the bridge from *current state* to *future state* (`19`, `20`).

---

## Inputs

- The full process the warehouse runs (from the site visit / `04`).
- Stage-level **demand** — volume through each stage per day/hour, at peak (from `05`, `15`, `06`, `14`).
- Stage-level **capacity** — the rate each stage can sustain (from labour/MHE `17`, the site, or the client).
- Consistent units per stage (pallets/hr, cartons/hr, lines/hr, orders/hr) — pick one per stage and state it.

---

## What you do

### 1 · Map the flow
Lay out the stages end to end:

```
Receiving → Inspection → Putaway → Storage → Replenishment → Picking → Sorting → Packing → Staging → Dispatch
```

Drop stages the warehouse doesn't have; add any it does. This diagram is itself a deliverable — most clients have never seen their whole flow on one page.

### 2 · Quantify each stage
For every stage, put a number on **volume · time · labour · space · equipment**. Now the flow carries data, not just boxes.

### 3 · Demand vs capacity, at peak ⭐
The core of the module. For each stage, at the **peak** (never the average): `Utilisation = required rate ÷ available capacity`.

| Process | Required | Capacity | Utilisation |
|---|---|---|---|
| Receiving | 100 pallets/hr | 120 | 83% |
| Picking | 600 lines/hr | 500 | **120%** |
| Packing | 450 orders/hr | 500 | 90% |
| Dispatch | 400 cartons/hr | 700 | 57% |

### 3a · The 85% rule — a stage does not fail at 100% ⭐

The single most misunderstood thing in capacity work: **utilisation and queueing are not linear.** As a
stage approaches full utilisation, waiting time rises asymptotically — arrivals are variable, so queues
build long before the average says "full".

| Utilisation | What actually happens |
|---|---|
| under 70% | Comfortable. Queues clear between arrivals. |
| 70 – 85% | Working hard. Queues form at peaks but recover. |
| **85 – 95%** | **Fragile.** Small variability creates large delays; recovery is slow. |
| over 95% | Effectively saturated. Any hiccup cascades downstream. |

- **Design to ~85%, not 100%.** A stage sized to exactly meet peak demand *will* miss it, because demand
  arrives unevenly within the hour.
- Flag any stage **over 85% at peak** as a constraint, not just those over 100%.
- The more **variable** the arrivals, the more headroom you need — a channel with a 10x peak-hour factor
  needs far more slack than one at 2x.

> **State it plainly to the client:** *"picking runs at 88% of capacity on the peak day — that is not
> 12% spare, it is a stage that will queue and not recover."* This is the sentence that justifies
> investment before anything visibly breaks.

### 4 · Name the bottleneck
The stage over 100% is the constraint — here, **picking**. Everything upstream just feeds a queue; everything downstream is starved. State it plainly: *"On the peak day, picking runs at 120% of capacity — it is the binding constraint, and it is where the future-state investment has to land."*

### 5 · Look past the first bottleneck
Once you'd relieve the first constraint, which stage becomes the next? Re-run demand-vs-capacity assuming the fix, and show the **second** bottleneck too — it stops the client solving one problem only to hit the next, and it frames the phased investment.

---

## Outputs

- The **process-flow diagram**, quantified at every stage.
- The **demand-vs-capacity table** at peak, with utilisation per stage and the bottleneck flagged.
- A one-line diagnosis naming the constraint and where investment must go.
- The **next** bottleneck after the fix — the phasing story.

---

## Checklist before you move on

- [ ] Every stage compares demand to capacity at the **peak**, not the average.
- [ ] Units are consistent within a stage and **stated**.
- [ ] The **bottleneck** is named explicitly, with its utilisation.
- [ ] Capacity figures are **sourced** (measured / client / benchmark), not assumed.
- [ ] The **second** bottleneck (post-fix) is identified.
- [ ] The diagnosis carries a **so-what** that points straight at the future state.

---

> **Why this earns its place.** This is the single most consulting-grade analysis in the pack. It converts a dozen separate charts into one sentence a director can act on, it justifies exactly where the CAPEX goes, and it stops the classic failure of automating a stage that was never the constraint. If you do only one Tier-1 synthesis, do this.

*Trivium Analytics · Warehouse Consulting Analysis Playbook · 16 · 2026-08-18 · pulls from `05`/`15`/`06`/`14`/`17`, feeds `19`, `20`*
