# 03 · Understand the Data — profile it, and show that understanding in HTML

> **What this is.** *(Layer 2.)* Before you model, *look* at what the data is telling you — and put that understanding in front of the client as a **Trivium-styled HTML data-summary**. This is not optional: most modelling mistakes come from skipping straight to the calc.

> **How to use it.** Interactive — answer the questions by actually querying the data, then turn the answers into the HTML summary. The **range treemap** in section 3 is the centrepiece: it is the fastest way to show a client what their own estate actually contains.

---

## Inputs
- The clean, reconciled tables from `02`.
- The one-line goal from `01`.
- Item master (for category and size), order-line history (for the work), inventory (for stock).

---

## 1 · The questions to answer from the data

- [ ] **How big is the size spread?** Min / median / mean / max of box volume. Is one storage size ever going to fit all? (Almost never — this is *why* you mix sizes / cap outliers.)
- [ ] **What does this client actually sell?** SKU counts by product category / department / brand — the range composition.
- [ ] **Where does the stock actually sit today?** Counts, locations, quantity per medium / area / department.
- [ ] **What's the coverage?** How many SKUs have each measurement? How many are zero-cube / oversized / discontinued?
- [ ] **Do the totals reconcile?** Total SKUs, total volume (m³), total units, total lines. Does a "total" column equal the sum of its parts, or is there unattributed stock?
- [ ] **What's the shape of demand** (if throughput is in scope)? Lines by area, peak vs average.
- [ ] **How dirty is it, and where?** Error and blank counts **by category**, not just in total — defects cluster.
- [ ] **Is anything in here twice?** Not just duplicate *records* (`02`) — duplicate **business events**:
  the same order line arriving from two overlapping extracts, or a re-sent month. Test the natural business
  key (order + line, or SKU + store + date + hour) for repeats, and reconcile the count against the client's
  own figure. A silently doubled month inflates every peak in the study.

---

## 2 · What you do

0. **Plot volume by day across the whole window — before anything else.** ⭐ One line chart, lines or units
   per calendar day, per source table. It is the cheapest check in the playbook and it exposes, in seconds:
   **gaps** (a dead week nobody mentioned), **duplicate loads** (a step change that doubles and stays),
   **system-migration seams** (a discontinuity where the WMS changed), **truncated extracts** (a partial
   first or last month), and **the real seasonal shape**. Do this before you profile a single column —
   half the data-quality findings on a job are visible here.
1. Profile every column you will use — type, nulls, min/max, distinct count.
2. Build the **range treemap** (section 3) and the **box-and-whisker** distribution charts — see `03a`, which is where the capping and exclusion decisions come from.
3. **Reconcile on the page** — show that the parts tie to the whole. State any unattributed gap explicitly.
4. Assemble the **HTML data-summary** in the Trivium look (navy header, amber accents): profile table, key counts, the treemap, the size spread, the cleaning findings.
5. Open it (`Start-Process msedge …`) and walk the client/boss through it.
6. **Get the headline counts confirmed by the client — in writing, early.** ⭐ Send them the counts from
   section 4 (SKUs, lines, orders, units, window, sites) and ask one question: *"does this match what you
   believe you handle?"* If their number differs from yours, one of you is wrong about the **scope**, and
   it is far cheaper to find that out in week one than in the review. This single step catches wrong-site,
   wrong-window, missing-channel and double-counted extracts — the four errors that invalidate a whole study.

---

## 3 · The range treemap ⭐ — what this client actually handles

> **Why it earns its place.** A client's range is the one thing they think they know and usually cannot describe. *"We have 150,000 SKUs"* is a number; a treemap is a **picture of the business** — which categories dominate, which are long tails, where the big items are, and where the data is broken. Put it early, on the data-summary page, and it frames every later conversation. It is also the fastest way to catch a scope misunderstanding: if the client looks at it and says *"that block shouldn't be there,"* you have found a filtering error on day one instead of week three.

**Tiles = SKUs (or groups of SKUs). Area = the measure being profiled. Colour = a second, ordered measure.**

Run it as **four cuts of the same data** — the set is the analysis, not any single picture:

| # | Cut | Area | Colour | What it answers |
|---|---|---|---|---|
| 1 | **Range composition** | SKU **count** by category | share that is active | *What do they sell, and how much of it is dead?* |
| 2 | **Physical size** | **cube** (stock or unit volume) by category | average unit volume | *What does the range physically look like — where is the bulk?* |
| 3 | **The work** | **order lines / picks** by category | lines per SKU | *Where does the picking effort actually come from?* |
| 4 | **Data quality** | SKU count by category | **% of SKUs with a defect** | *Where is the data broken, and does it matter?* |

**The pay-off is the mismatch between cuts 1, 2 and 3.** A category that is huge by SKU count and tiny by lines is a long tail to de-range or bulk-store. One that is small by count and huge by lines is the fast core that earns automation. One that is small by count and huge by cube is eating the building. **Say the mismatch out loud — that sentence is the finding**, and it is what the client remembers.

**Build rules (these have bitten us — see `24`):**
- **Include every SKU** so tile areas sum to the population. Never pool the tail into one grey tile: it dominates the chart and reads as a single giant SKU.
- **Label only tiles big enough to carry text.** Everything else gets a tooltip.
- Colour is an **ordered magnitude** → use a **sequential single-hue ramp**, never categorical colours.
- Two levels is the maximum that stays readable: **category → sub-category**, or **category → SKU** for a small range.
- State the measure and the population under the chart. A treemap without a stated denominator is decoration.

> **Distinguish this from the velocity treemap in `05`.** This one profiles the **range** — what exists, at the data-understanding stage. Module `05` §8 builds a **velocity** treemap (tiles = SKUs, area = pick lines, grouped by speed band) which sizes the **work**. Both are useful, they answer different questions, and they belong on different pages. Do not merge them.

---

## 4 · The counts every data-summary must carry

State these plainly, because every later percentage divides by one of them:

- **SKUs** — total in master · active (moved in the window) · stocked · ordered · discontinued
- **Order lines · orders · units · cartons** — for the window, with the window stated
- **Despatch days** — and therefore the per-day denominator
- **Sites / locations / stores** served
- **Defects** — zero-cube, missing dimensions, impossible values, negative or zero quantities, unparseable dates — each as a **count and a % of the population it sits in**

---

## Outputs

- An HTML **data-summary** the client can read — the shared picture everyone agrees on before modelling.
- The **range treemap** (all four cuts, or the ones the data supports), with the mismatch stated in a sentence.
- The **size-spread** chart and the headline counts above.
- A short list of what the data **forces** on the model (mix of sizes, caps, exclusions, blocked analyses).

---

## Checklist before you move on

- [ ] I can describe the size spread and the current-state split in a sentence each.
- [ ] The **range treemap** is built, every SKU is included, and the areas **sum to the population**.
- [ ] The **mismatch between count, cube and lines** is stated as a sentence, not left for the reader to spot.
- [ ] Zero-cube / oversized / discontinued counts are known and shown.
- [ ] Defect counts are broken down **by category**, not given only as a total.
- [ ] Totals reconcile (or the gap is quantified and explained).
- [ ] The HTML summary exists and has been **shown** — not just numbers in my head.
- [ ] The client has looked at the treemap and agreed it describes their range.

---

> **Worked examples.** *WAMAS:* box-volume box-plot by concept group (median ~0.1 L to ~290 L — proof one tote can't fit all); reconciled to 401,655 SKUs / 8,391 m³ / 2,623 M units. The `Volume_M3` total did **not** decompose cleanly into the media (unattributed stock) → flagged, and "remaining" is always built by subtraction thereafter.
>
> *Project Bombay:* the range treemap showed **764,506 master SKUs of which only 54,829 moved** — 68% of the master was discontinued range. Cut 1 against cut 3 made the mismatch obvious: a handful of categories carried nearly all the picking while SKU count was dominated by dead range. Cut 4 put the defects where they belonged — **16 item-master records with an impossible cube** were distorting the storage number for the whole estate.

*Trivium Analytics · Warehouse Consulting Analysis Playbook · 03 · v0.3 · 2026-08-20 · next [[04_Warehouse_Business_Profile]]*
