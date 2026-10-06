# 17 · Labour, Equipment & Travel — the cost of the work

> **What this is.** *(Layer 7.)* The resource side of operations: how many people and machines the work takes, how hard they're used, and how far they travel to do it. This is where the future-state **savings** come from — labour and travel are the OPEX lines automation attacks, so quantify them now or you can't build the business case later (`20`).

> **How to use it.** Labour and equipment need HR/MHE data; travel needs **location coordinates** (rare, but the most powerful input in the whole playbook). Feeds the bottleneck view (`16`), slotting (`18`) and the business case (`20`).

---

## Inputs

- **Labour data** — headcount, shifts, hours, by process; overtime; cost per hour if the business case needs money.
- **Equipment (MHE) list** — type, count, operating hours, and (ideally) task/trip logs.
- **Location coordinates** (x, y, or aisle/bay/level) — to turn picks into distance. Without them, travel is estimated, not measured — say so.
- Pick and putaway transactions from `06`, `14`.

---

## What you do

### 1 · Labour
Headcount and **FTE**, split **by process**. Then productivity: picks / orders / units **per FTE**, hours/day, overtime, shift utilisation, and — if cost is available — **labour cost per order** and **per line**. Model **current labour vs future-state labour** so the saving is explicit.

### 2 · Equipment / MHE
List every truck and machine — forklifts, reach/VNA trucks, pallet jacks, LLOP/MLOP, conveyors, sorters, AMRs, AGVs, cranes. For each: **count · utilisation · operating hours · trips · capacity · bottleneck · future requirement**. Under-used MHE is a cost to remove; over-used MHE is a constraint to relieve.

### 3 · Travel ⭐
If coordinates exist, this is the strongest analysis in the study. Compute distance travelled **per pick · per order · per SKU · per zone · per shift**, and split **loaded vs empty** travel (empty travel is pure waste). Roll up to **total annual travel** (km, or hours), then estimate the reduction available from slotting (`18`) and automation (`19`).

> **No coordinates?** Estimate travel from zone/aisle bands and state it as an assumption. Then ask for a location map — it is usually the single most valuable follow-up dataset a client can send.

---

### 4 · Where the rate comes from — say it, always ⭐

Every labour number in the study rests on a **rate** (lines per hour, cases per hour, pallets per hour).
Where that rate came from decides how defensible the whole business case is. In order of strength:

| Source of rate | Strength | Use it as |
|---|---|---|
| **Engineered standard** (MTM / time study for this site) | Strongest | Fact |
| **Measured** — the client's own throughput ÷ their own hours | Strong | Fact, if hours are clean |
| **Client's stated target** | Moderate | Their number — quote it as theirs |
| **Industry typical** | Weak | **Assumption**, with the range and the source |

- **Derive the measured rate wherever you can**: total lines ÷ total picker-hours, per area, per shift. It
  needs only volume and hours, and it beats any published figure because it is *their* operation.
- **State the rate on the page**, next to the number it produced. A reader must be able to change it and
  see the answer move.
- **Give a range, not a point**, whenever the rate is assumed — and carry that range into the sensitivity
  table in `20`. A single assumed rate presented as fact is the fastest way to lose a business case in review.

> **Never blend rates across pick types.** Each-picking, case-picking and pallet-moving have rates that
> differ by an order of magnitude. One blended "lines per hour" for a mixed operation is a meaningless
> number that will size the wrong machine.

---

## Outputs

- Labour by process and productivity vs benchmark; **current-vs-future FTE**.
- MHE utilisation table with under/over-used flags and future requirement.
- Travel per pick/order and **total annual travel**, loaded vs empty, with the reduction opportunity quantified.
- OPEX-ready numbers (FTE, hours, £) to carry into `20`.

---

## Checklist before you move on

- [ ] FTE is split **by process**, and current-vs-future is modelled.
- [ ] MHE utilisation is stated, with under/over-used flagged.
- [ ] Travel is **measured** where coordinates exist, or clearly flagged as **estimated**.
- [ ] Loaded vs empty travel is separated; the reduction opportunity is **quantified**.
- [ ] Every figure that feeds the business case is in **FTE / hours / £**, ready for `20`.

---

> **Why this earns its place.** The business case lives or dies on OPEX, and OPEX is mostly labour and travel. "Re-slotting cuts travel 35% ≈ 6 FTE ≈ £Xk/yr" is the sentence that funds a project. You can't write it without this module — so even a rough labour and travel estimate beats none.

*Trivium Analytics · Warehouse Consulting Analysis Playbook · 17 · 2026-08-18 · feeds `16`, `18`, `20`*
