# 19 · Automation — suitability, sizing & simulation

> **What this is.** *(Layer 9.)* Where the study turns from *analysis* into *solution*. First decide **what** to automate (and what to leave manual); then **size** each technology from the demand and the peak; then **simulate** the sizing so the number is defensible. "We need 20 AMRs" is a guess; the *chain* that gets to 20 is consulting.

> **How to use it.** Rests on everything before it — the demand and peak (`05`, `15`), the pick mix (`06`), the bottleneck (`16`), the flow (`14`). Never size automation before the bottleneck is proven. Feeds the business case (`20`).

---

## Inputs

- Peak throughput per process (lines/hr, cartons/hr, pallets/hr, cycles/hr) at the **design peak**, from `05`/`15`/`06`/`14`.
- The **bottleneck** diagnosis (`16`) — automate the constraint, not a comfortable stage.
- The pick-type mix (`06`) and order shape (`05`) — they decide *which* technology fits.
- Vendor cycle-time / throughput assumptions (state them; they drive everything).

---

## What you do

### 1 · Suitability — automate / semi-automate / keep manual
Classify each process. Be willing to say "keep manual" — over-automation is a real failure mode.

| Process | Current | Future (example) |
|---|---|---|
| Pallet movement | Manual forklift | AGV |
| Tote movement | Manual trolley | AMR |
| Picking | Manual | Goods-to-person |
| Sorting | Manual | Sorter |
| Putaway | Manual | AS/RS |
| Packing | Manual | Semi-automated |

The pick mix and order shape from `06`/`05` justify each choice — e.g. 92% single-unit lines → goods-to-person; a flat Pareto → a high-density store, not a compact fast-pick module.

### 2 · Size each technology, on the peak
For each chosen technology, derive the requirement from the **peak** workload:

- **AMR:** tasks/hr · distance/task · cycle time · robot capacity · peak workload · **number of robots** · charging allowance.
- **Conveyor:** cartons/hr · peak cartons/hr · speed · accumulation · length · number of lines.
- **Sorter:** items/hr · peak items/hr · destinations · chutes · induction stations.
- **AS/RS:** storage locations · inbound & outbound cycles/hr · peak cycles · crane count.
- **Shuttle:** pallet positions · shuttle count · lifts · throughput.

### 3 · Simulate — make the number defensible ⭐
Don't state a fleet size; **derive** it, and show the chain:

```
peak demand → cycle time → capacity per robot → peak requirement
            → raw fleet → utilisation check → buffer/redundancy → FINAL fleet
```

A reader can challenge any step and see the effect. That transparency is what gets a capital number signed off — and it protects you when the vendor quotes their own.

### 4 · Design on the right peak, and grow it
Size on the **design-year peak** (from `15`, `20`), not today's average. State the peak factor and the growth assumption in visible cells, so the sizing moves when the assumption does.

---

### 5 · Write a vendor-neutral requirement, then phase it ⭐

The deliverable that survives procurement is **not** "buy 20 AMRs". It is a **requirement specification**
any vendor can quote against, and a phasing plan that de-risks it.

**The requirement — in performance terms, not product terms:**

| State | Not |
|---|---|
| Lines/hour sustained, and at design peak | "an AS/RS" |
| SKUs to be presented, and their cube/weight envelope | a named vendor's model |
| Order profile it must absorb (lines/order, units/line) | a fixed fleet count |
| Hours of operation, and the maintenance window | — |
| Growth it must scale to, and how | — |
| Interfaces: WMS, host, exceptions | — |

Quoting a *product* invites one vendor's answer. Quoting a *requirement* lets three vendors compete and
lets you compare their proposals on the same basis.

**Then phase it.** A single large cutover is the highest-risk shape. Prefer:

1. **Phase 0 — capital-free moves first.** Levelling, re-slotting, scheduling, de-ranging. These change
   the size of everything after them, so they must run first. *(On one engagement, levelling one channel's
   weekly release cut the required fleet by 86% — before a penny of capital.)*
2. **Phase 1 — the proven constraint**, at a size that works today.
3. **Phase 2 — scale to the design year**, once Phase 1's real rates are measured.

> **Size Phase 2 on Phase 1's measured performance, not on the vendor's brochure.** The gap between quoted
> and achieved throughput is the most common reason automation misses its business case.

---

## Outputs

- The **suitability matrix** (automate / semi / manual) with the justification per process.
- A **sizing sheet** per technology (the requirement figures).
- The **simulation chain** for each fleet — demand → cycle → capacity → fleet → utilisation → final.
- Throughput and equipment numbers ready to cost in `20`.

---

## Checklist before you move on

- [ ] Automation targets the **proven bottleneck** (`16`), not a comfortable stage.
- [ ] Technology choice is **justified** from the pick mix / order shape, not assumed.
- [ ] Everything is sized on the **design-year peak**, with the peak factor visible.
- [ ] Fleet sizes are **derived and shown** (the chain), not asserted.
- [ ] Utilisation and a **buffer/redundancy** allowance are included.
- [ ] Vendor assumptions (cycle times, rates) are **stated** and easy to flex.

---

> **Why this earns its place.** This is what the client is paying for — a sized, defensible solution. The suitability call stops money going to the wrong process; the peak-based sizing stops the system falling over on the busy day; and the visible simulation chain is what survives the vendor's counter-proposal and the CFO's scrutiny.

*Trivium Analytics · Warehouse Consulting Analysis Playbook · 19 · 2026-08-18 · pulls from `05`/`15`/`06`/`14`/`16`, feeds `20`*
