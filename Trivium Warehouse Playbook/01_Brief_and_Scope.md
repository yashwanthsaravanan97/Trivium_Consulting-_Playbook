# 01 · Brief & Scope — take the project, pin the goal

> **What this is.** The very first phase: before you touch data or open Power BI, get crystal-clear on **what the client actually wants at the end**. Half of all rework comes from modelling the wrong thing. This file is deliberately question-driven — you can't move on until you can answer them.

> **How to use it.** Work the questions out loud with whoever owns the brief (often via the boss). Write the answers down. If you can't answer one, that's your first client question — don't guess.

---

## The one line that governs everything
- [ ] **Can you say, in a single sentence, what the client will be holding at the end?**
  - *"A per-SKU number of totes / pallets the warehouse needs."*
  - *"How many cartons fit on a pallet, per SKU."*
  - *"A current-vs-proposed comparison of storage footprint."*
  - *"The impact of adding/removing an option (e.g. a storage medium)."*
  - *"Where the operation breaks on the peak day, and what to do about it."*
  - *"Whether to automate the pick, what to automate, and how big it needs to be."*
  - *"A costed list of moves, ranked by prize and by what they cost to unlock."*

If you can't write that sentence, stop and ask. Everything downstream (engine, buckets, deliverables) is chosen to serve it.

## The questions to nail (work these first)
- [ ] **Unit of storage** — totes? pallets? both? (drives the engine — file `09`)
- [ ] **The physical limits / parameters** — pallet L×W×H, usable height, board thickness, weight limit; or tote ladder + capacities + fill %. Get the *client's* numbers, not textbook ones.
- [ ] **What measurements will they give us?** — a ready volume? raw L×W×H? weights? per-medium quantities? (This is the input to everything — see `02`. Source changes every job; measurements don't.)
- [ ] **Current state or future state (or both)?** — do they want us to reproduce how they store today, size a new system, or compare?
- [ ] **Scope of SKUs** — all of them? one depot? one product family? in/out of a medium?
- [ ] **What does "done" look like?** — an HTML site, an Excel model, a PPT for the board, a single number? (drives `21`)
- [ ] **Who is the audience?** — the boss, the client's ops team, the board? Sets the tone of the deliverable.
- [ ] **Any known landmines?** — dodgy data columns, prior calculations that were wrong, assumptions the client is sensitive about.

## Growth and horizon — ask on day one, not in `20`

- [ ] **What growth assumption should we design to?** Their number, in their words (units, lines, orders, SKUs — say which).
- [ ] **What is the design horizon?** +3 / +5 / +10 years.
- [ ] **Is there a known step change coming?** A new channel, a site closure, an acquisition, a range expansion.

Without these, `15` and `20` cannot be run and you will end up inventing a scenario set. Ask now.

---

## The data request ⭐ — send this before the site visit

Map what they can send to what it unlocks (`00a` §6), then send **one list** rather than drip-feeding
requests for three weeks. Each row is a layer that lives or dies on it:

| Ask for | Unlocks | If missing |
|---|---|---|
| **Order-line history**, 12+ months — order no, item, date, **time**, qty, site/channel | Demand, order shape, ABC/XYZ, peak, automation sizing | No design basis at all |
| **Item master** — dims (state the UoM), weight, pack qty, category, hazard/temp flags | Cube, storage sizing, slotting, cube-per-pick | Engines A and B both blocked |
| **Inventory snapshot(s)** — SKU × **location** × qty, ideally several dates | Inventory, coverage, utilisation, fragmentation | No storage current state |
| **Pick / task transactions** — location, timestamp, operator, equipment | Productivity, hot/cold, travel, bottleneck | Layer 7 blocked |
| **Inbound receipts & outbound despatch** — with times | Dispatch wave, inbound/outbound balance | Flow analysis blocked |
| **Location master** — type and **capacity** per location | Utilisation %, fragmentation | Occupancy only, no utilisation |
| **Site layout / coordinates** — x,y or aisle-bay-level | Travel distance, affinity slotting | Travel can only be estimated |
| **Labour & MHE** — headcount by function, shifts, hours, fleet list, rates | FTE, productivity, equipment, the OPEX case | Business case cannot be closed |
| **Cost inputs** — labour £/hr, CAPEX rates, maintenance/energy % | CAPEX / OPEX / ROI / payback | Money side blocked |

> **The single most valuable line in that table is time-stamped transactions.** They turn a flat daily
> average into a real peak hour, and the peak is what sizes the solution. If you get one extra thing, get times.

**Say what each gap costs.** *"Without a location extract we can report occupancy but not utilisation, and
fragmentation is impossible"* is a sentence that gets data sent. *"Please send everything you have"* is not.

---

## What you do
1. Capture the answers in a short scope note (even a paragraph). Restate the goal back to the boss/client in your own words and get a "yes, that's it."
2. **Send the data request** above, with what each gap costs.
3. List the **parameters** you'll need as inputs (they become the Assumptions tab / config block later).
4. Flag the **likely engine** (A cubic / B cartons-per-pallet / C demand-throughput) as a hypothesis — you'll confirm it in `09` once you've seen the data.
5. Note what you *don't* yet know — those are your opening client questions.

## Outputs
- A one-line goal statement everyone agrees on.
- A parameter list (limits, capacities, fills).
- A first guess at the engine, and the list of open questions.

## Checklist before you move on
- [ ] The **data request** has been sent, with what each gap costs.
- [ ] **Growth assumption and design horizon** captured (or logged as an open question).
- [ ] The one-line goal is written and confirmed.
- [ ] I know the unit, the limits, and what measurements are coming.
- [ ] I know what the final deliverable is and who reads it.
- [ ] Open questions are listed and sent.

---
> **Worked examples.** *WAMAS:* "size the whole warehouse into totes and show current vs proposed, and test removing a medium." *Carton-Pallet:* "max cartons per Euro pallet per SKU, under two packing scenarios, recommend one."

*Trivium Analytics · Warehouse Consulting Analysis Playbook · 01 · v0.2 · 2026-08-20 · next [[01a_Client_Name_Check]] · see [[00_Overview]]*
