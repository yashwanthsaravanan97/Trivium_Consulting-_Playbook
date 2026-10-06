# 05 · Order Profile & ABC-XYZ — sizing the *work*, not the space

> **What this is.** A **demand / throughput analysis** — a cross-cutting piece of the work, *not* one of the sizing engines. Engines A and B size **storage**; this sizes the **work** — how much picking arrives, when it peaks, how it is shaped, and how many SKUs a system must present. Run it alongside a sizing engine or on its own. Reach for it when the client's question is about **automating the pick**, not about how much space the stock needs.

> **How to use it.** Interactive, like the rest. The inputs are order lines, not measurements — so this analysis runs off a despatch history, and it works even when there is **no volumetric data at all**.

---

## Inputs

- An **order-line history** — one row per order × item × despatch day. Twelve months is the target; anything less and seasonality is a guess.
- Minimum usable columns: **order number, item identifier, despatch date, quantity**. A site/channel code and an order date make it much stronger.
- Site-visit notes: operating hours and shift pattern, headcount, storage media, growth assumptions, service promise (cut-off and ship-by).

**You do not need volumetrics for this analysis.** If the client has none — and they often have none — this is the piece that still answers a useful question.

---

## The vocabulary (get this right first, or every number is wrong)

| Term | Meaning | Why it matters |
|---|---|---|
| **Pick line** | one order-line row | The pickable unit of work. **This is the unit everything is sized on.** |
| **Order** | distinct order number | Drives packing, despatch and the singles problem |
| **Unit / item** | the quantity on the line | Drives replenishment, *not* pick effort |
| **SKU** | distinct item | Drives how many faces the machine must present |
| **Despatch day** | a day anything shipped | **Never divide by 365** — divide by the days they actually ship |

The single most common mistake is conflating **lines** with **units**. A line is one pick — one visit to one face. A line for 6 units is still one pick. Size the machine on **lines**; size replenishment on **units**.

---

## What you do

### 1 · Establish the working calendar
Count **distinct despatch days**, not calendar days. Then check day-of-week coverage — a missing Saturday or Sunday is a finding, not a gap. Everything per-day divides by this number.

### 2 · The daily profile, and the peak factors
Pick lines per despatch day across the whole window, split by channel. From it:
- `avg day = total lines ÷ despatch days`
- `peak day = the single busiest day's lines`
- `peak day factor = peak day ÷ avg day`
- `peak week`, `peak week factor` the same way

**Size on the peak day, never the average.** State the factor explicitly — it is the multiple the pick engine must absorb.

### 3 · The day-of-week profile ⭐
Average lines per day for each weekday. **This is the highest-value chart in this analysis** and it is routinely ignored. If one day carries a wildly disproportionate share, the machine is being sized for a self-inflicted spike, and **levelling the order release is the cheapest capacity you will ever find** — it costs a planning change, not capital. Always put it to the client before specifying anything.

### 4 · The order shape
Two distributions, both from the line data:
- **Lines per order** — band it (1 / 2 / 3 / 4-5 / 6-10 / 11-20 / 21-50 / 51-100 / 101-500 / 500+). Singles are usually a huge share of *orders* and a small share of *work*: report both, or the conclusion inverts.
- **Units per line** — a high share of **single-unit lines is the goods-to-person case**. This is usually the strongest single argument for or against GtP.

Where two channels differ, show each as a share of **its own** total. Raw counts bury the smaller channel.

### 5 · ABC — how much work each SKU makes
Band on **pick lines per SKU**, not units, not value.

**Use fixed thresholds, not cumulative percentages.** A cumulative-Pareto cut (first 80% / next 15% / last 5%) is hard to explain, needs a helper table to compute, and **splits SKUs with identical pick counts across two bands**. A flat threshold is one line of logic, always reproducible, and every SKU with the same velocity lands in the same band:

```
A = SKU picked N+ times a year        (pick a round number near the 80% point)
B = next band down
C = the rest
```

Pick the edges by testing round numbers against the data until the line shares land near 80 / 15 / 5, then **state the thresholds in the deliverable** — "A = 500+ picks a year (about two a day)" is something a warehouse manager can hold in their head.

### 6 · The Pareto curve — how concentrated the work is
Plot **cumulative % of pick lines** against **% of SKUs, busiest first**, with a 45° reference line for "no concentration at all". Then quote the ladder: how many SKUs to cover 50 / 80 / 90 / 95 / 99% of lines.

**This is often the finding that decides the technology.** A steep curve (20% of SKUs → 80% of lines) means a compact fast-pick module works. A **flat** curve means there is no hot core to carve out, the system must reach the whole range, and the answer is a high-density store rather than a small module. Do not assume 80/20 holds — measure it.

### 7 · XYZ — how *often* each SKU moves
Band on the **number of despatch days the SKU moved on**, as a share of the working calendar:

```
X = moved on 50%+ of despatch days
Y = 10-50%
Z = under 10%
```

> **Do not use the coefficient of variation.** The textbook XYZ is CoV of weekly demand with X < 0.5. In a seasonal business that fails outright: on Project Seeds the *minimum* CoV across 5,008 SKUs was 0.47, so the textbook threshold classified **one** SKU as X. CoV just re-measures the season instead of separating SKUs. **Frequency discriminates properly and maps straight onto the slotting decision** — a SKU that moves most days needs a permanent fast face; one that moves twice a year does not.

Cross ABC × XYZ into the nine-box. Report **SKU count and pick lines in every cell**, and check the grand total ties to the whole population.

### 8 · The SKU treemap — which SKUs, and how fast
Tiles = SKUs, **area = pick lines**, grouped into blocks by a **speed band** (lines per despatch day), shaded light→dark for slow→fast.

- Include **every** SKU so the areas sum to the full line count — pooling the tail into one grey tile makes it dominate and reads as a single giant SKU.
- Label only the tiles big enough to carry text.
- Colour is an **ordered magnitude**, so use a **sequential single-hue ramp**, never categorical colours.

The treemap's job is to make concentration visible at a glance — it is the flat Pareto in one picture, and it lands with a non-technical audience where the curve does not.

> **Not the same as the range treemap in `03`.** That one profiles **what exists** (area = SKU count / cube / defects by category) at the data-understanding stage. This one profiles **how hard each SKU works** (area = pick lines, grouped by speed band). Build both; keep them on separate pages.

### 9 · Bands for the range (see also `12`)
- **Speed band** — lines per despatch day
- **Seasonality band** — weeks of the year the SKU moved. The count that must be resident **all year** is the floor on live faces; anything else is a de-slotting opportunity.
- **Channel band** — where a client picks the same SKU for two channels, count the overlap. **Double-slotted SKUs are usually the largest structural saving in the data** and are independent of vendor choice.

### 10 · Convert to a design basis
Per area or channel: annual, peak-week, peak-day and peak-hour lines. Then grow to the design year at the client's assumption. Keep every factor in a visible cell.

> **If there are no time stamps** — and often there are not — say so. Lines-per-hour then assumes a flat spread across the operating window, which **understates the true peak hour**. Flag it as an assumption and ask for time-stamped data; it is usually the single most useful thing the client can send next.

### 11 · Deeper SKU cuts — age, velocity quadrants & affinity *(when the data supports them)*

**SKU age.** From first-seen and last-seen dates: active days / weeks / months and **SKU age**. Band it — **< 6 months · 6–12 · 1–2 years · 2 years+** — then cross **age × velocity** for a portfolio view the client rarely has: *AA* = fast & established (the permanent core), *C* = new or slow (watch / exit). It reframes "150k SKUs" as "the 8k that actually run the business."

**Velocity quadrants.** Cross **picks/day** (velocity) with **order frequency** (days moved) into four quadrants — high-velocity/high-frequency, high/low, low/high, low/low. This maps straight onto slotting and automation: the high/high corner earns a permanent fast face and is the goods-to-person candidate; the low/low tail is bulk or exit. Don't rely on total quantity alone — a SKU picked 100,000 units *once* is nothing like one picked 100 units *every day*.

**Order affinity** *(advanced — carried into `18`).* Which SKUs are **ordered together**? A pair that co-occurs in a large share of orders (e.g. *A + B in 35%*) is an affinity-slotting opportunity — place them near each other and one pick tour covers more of the order, cutting travel. Even a top-20 affinity-pairs list is a compelling, non-obvious finding.

---

## Outputs

- Daily / day-of-week / monthly profile charts, with peak factors stated
- Order-shape and units-per-line distributions
- ABC bands, the Pareto curve and the SKU-count ladder
- XYZ on days moved, and the ABC-XYZ nine-box
- The SKU treemap, and speed / seasonality / channel bands
- A **summary table**: annual vs average-day vs peak-day for orders, lines, items and SKUs
- A design basis grown to the design year, with every assumption visible

---

## Checklist before you move on

- [ ] Everything per-day divides by **despatch days**, never calendar days.
- [ ] **Lines and units are never conflated** — the pick unit is the line.
- [ ] Peak day and peak week factors are stated, and the design sizes on peak.
- [ ] The day-of-week profile has been looked at, and any spike raised with the client.
- [ ] ABC uses **fixed, quotable thresholds** and the line shares are near 80/15/5.
- [ ] The Pareto was **measured, not assumed** — the SKU ladder is in the pack.
- [ ] XYZ is on **days moved**. If CoV was tried, the reason it was rejected is recorded.
- [ ] The nine-box and every band **sum to the whole population**.
- [ ] The treemap includes every SKU and uses a sequential ramp.
- [ ] Missing inputs (time stamps, volumetrics, stock, rates) are **stated, not guessed**.

---

> **Worked example — Project Seeds (Aug 2026).** 2,976,546 pick lines / 499,302 orders / 7,050,909 units / 5,008 SKUs over 274 despatch days. Peak day 41,162 lines = 3.79× average. **Monday alone carried 31.6% of the year and there was no Saturday despatch at all** — the biggest capex lever in the study. 92% of consumer lines were single-unit (the GtP case). The Pareto was **unusually flat**: 1,779 SKUs (35.5%) for 80% of lines, against the 20% a textbook 80/20 predicts — which ruled out a compact fast-pick module. ABC on fixed thresholds (500+ / 200-499 / under 200 picks a year) gave 81.7 / 13.2 / 5.1% of lines. XYZ on days moved (137+ / 27-136 / under 27 of 274) put 2,390 SKUs in X carrying 88% of picking — the number that sized the store. 2,087 SKUs were picked in **both** channels and double-slotted.

*Trivium Analytics · Warehouse Consulting Analysis Playbook · v0.2 · 2026-08-18 · then [[12_Bucketing]], [[15_Throughput_and_Forecast]] and [[18_Slotting]]*
