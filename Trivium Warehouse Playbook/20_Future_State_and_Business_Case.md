# 20 · Future State, Scenarios & Business Case — what to do, and what it's worth

> **What this is.** *(Layer 10.)* The end of the chain: grow the demand to a design horizon, test it under scenarios, lay the future state against today, and turn the whole study into **money** — CAPEX, OPEX, savings, payback, ROI. This is the slide the board decides on, so every number on it must trace back through the analysis that produced it.

> **How to use it.** It consumes the whole playbook — the current-state numbers, the bottleneck, the slotting and automation sizing. Build it last. It is the deck's closing section.

---

## Inputs

- Current-state numbers from every prior module (space, labour, travel, throughput, utilisation).
- The forecast and peak factors from `15`; the automation sizing from `19`; the slotting saving from `18`.
- The client's **growth assumptions** and design horizon (from `01`).
- Cost inputs — MHE/automation/racking/software CAPEX, labour/maintenance/energy rates — from the client or vendor.

---

## What you do

### 1 · Growth to the design year
Never design for today. Grow **orders · lines · units · SKUs · inventory · pallets · cartons · peak volume** to **+3 / +5 / +10 years** at the client's assumption. Keep every growth factor in a visible cell so the model flexes.

### 2 · Scenarios
Build at least four, and compare them on space · labour · equipment · throughput · CAPEX · OPEX:

- **S1 — Current state**
- **S2 — Moderate growth**
- **S3 — High growth**
- **S4 — Peak scenario**

Scenarios turn a single fragile forecast into a defensible range, and they show the client the risk of under- or over-building.

### 3 · Current vs future state ⭐
The headline comparison — usually *the* slide of the deck:

| Metric | Current | Future | Improvement |
|---|---|---|---|
| Orders/hr | 500 | 800 | +60% |
| Picks/hr | 700 | 1,200 | +71% |
| Labour (FTE) | 100 | 65 | −35% |
| Storage locations | 100k | 75k | −25% |
| Travel | 100% | 55% | −45% |
| Utilisation | 65% | 82% | +17 pts |

Every row traces to an earlier module (travel → `17`/`18`, locations → `08`, picks → `06`/`19`).

### 4 · The business case — turn it into money
- **CAPEX:** automation, racking, conveyors, sorters, AMRs, AS/RS, software, installation, integration.
- **OPEX:** labour, maintenance, energy, software, consumables — **current vs future**.
- Then: **annual savings · payback period · ROI · NPV** (and **IRR** if asked).

### 4a · Cost the do-nothing option ⭐

**Every business case needs a baseline, and the baseline is not "today frozen".** It is *"today, growing,
with no investment"* — and it usually gets expensive on its own.

Model what happens if the client does nothing:

- **When does a stage exceed capacity?** From `16` and the growth curve — name the year.
- **What does coping cost?** Overtime, agency labour, weekend shifts, external storage, expedited freight,
  service failures. These are real costs the client is already paying or soon will.
- **What breaks first, and what does that cost?** A missed cut-off, a lost customer, a failed peak.

The comparison the board actually decides on is **investment vs the cost of not investing** — not
investment vs a static present. A case that says *"£X buys you 60% more throughput"* is weaker than one
that says *"doing nothing costs £Y/yr from year 2 and fails peak in year 3; £X avoids that and buys
headroom to year 10."*

> Where you cannot cost the do-nothing option for want of rate or cost data, **say so and describe it
> physically instead**: *"on the current growth assumption, picking exceeds capacity on the peak day in
> year 2."* A physical statement with no money attached still frames the decision honestly.

### 5 · Sensitivity
Flex the big assumptions — growth rate, peak factor, labour rate, throughput achieved — and show payback/ROI moving. It pre-empts the CFO's "but what if…" and proves the case isn't knife-edge.

---

## Outputs

- The growth model to the design year, with visible factors.
- The **scenario comparison** (S1–S4) across space/labour/equipment/CAPEX/OPEX.
- The **current-vs-future** headline table.
- The **business case**: CAPEX, OPEX delta, annual savings, payback, ROI (NPV/IRR as needed).
- A **sensitivity** table on the key assumptions.
- The **opportunity tree** (`00a`) totalled — every quantified saving from the study, summed into the case.

---

## Checklist before you ship

- [ ] Sized on the **design-year peak**, not today's average; growth factors visible.
- [ ] At least **four scenarios**, compared on the same metrics.
- [ ] Every **current-vs-future** row traces back to the module that produced it.
- [ ] CAPEX and OPEX are itemised; savings, **payback and ROI** are shown.
- [ ] **Sensitivity** on the big assumptions is included.
- [ ] Every headline number **reconciles** back through the study (`24`, `25`).

---

> **Why this earns its place.** Everything before this is evidence; this is the verdict. A board doesn't buy "your picking is at 120%" — it buys "£X CAPEX, £Y/yr saved, Z-year payback, robust from moderate to high growth." If the analysis was honest, this slide writes itself and survives scrutiny; if it wasn't, this is where it falls apart — which is exactly why it comes last, after the recheck.

*Trivium Analytics · Warehouse Consulting Analysis Playbook · 20 · 2026-08-18 · consumes the whole pack, closes the deck (`21`)*
