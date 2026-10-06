# 03a · Box-and-Whisker Analysis — the spread, not the average

> **What this is.** *(Layer 2.)* The chart that kills the most dangerous number in warehouse consulting: **the average**. A box plot shows the whole distribution — median, middle half, reach and outliers — for each category at once. It is how you prove *"one size cannot fit all"* in a single picture, and it is where the **capping** and **exclusion** decisions come from.

> **How to use it.** Build it in `03`, alongside the range treemap, on whatever measure the job turns on. The treemap answers *"what is in the range?"*; the box plot answers *"how wildly does it vary?"* Run both — they are the two halves of understanding the data.

---

## Why this earns its place

An average tells you nothing about a warehouse, because warehouses are made of extremes.

> ❌ *"The average SKU is 4.2 litres."*
>
> ✅ *"Median 0.8 L, middle half 0.3–2.1 L, top whisker 14 L, and 340 SKUs beyond 100 L — so a single tote size serves the middle half and strands the tail."*

The second sentence contains a design decision. The first contains nothing. **A mean is only meaningful when the distribution is symmetric, and almost nothing in a warehouse is** — cube, velocity, order size, cover and weight are all long-tailed. Averaging them produces a number no real SKU resembles.

The box plot is also the honest way to show a client that their range is heterogeneous *without* accusing anyone: the data simply shows it.

---

## Inputs

- The clean, reconciled tables from `02` (already anonymised — see `01a`).
- A **measure** to distribute and a **grouping** to split it by.
- The population count for every group, so `n` can be shown.

---

## 1 · What to plot — the standard set

Run the ones the data supports. Each has a decision hanging off it:

| Measure (the spread) | Split by | The decision it drives |
|---|---|---|
| **Unit / box cube** | product category, storage medium | Tote and location sizing; the cap on runaway SKUs |
| **Outer / carton cube** | category, supplier | Case handling, conveyor and pallet build |
| **Weight** | category, medium | Ergonomic limits, per-location weight caps |
| **Pick lines per SKU** | category, channel | Fast-pick zone sizing, ABC edges |
| **Units per line** | channel, pick area | Each-pick vs case-pick technology |
| **Lines per order** | channel, customer type | Pack bench, sortation, put-wall sizing |
| **Days of cover** | category, site | Over- and under-stock; space to reclaim |
| **Daily lines** | channel, weekday | Peak resourcing and levelling |

**Start with cube by category, and units-per-line by channel.** Those two answer most briefs.

---

## 2 · How to build it properly

- **Show `n` under every box.** A box over 12 SKUs and a box over 40,000 look identical and mean completely different things.
- **Use a log scale when the spread crosses orders of magnitude** — and label it as log. Warehouse cube routinely spans 1 cc to 1 m³; on a linear axis every box collapses to a line at the bottom.
- **Define the whiskers and say which definition you used.** Default to **Tukey: 1.5 × IQR**, with everything beyond drawn as outlier points.
- **Never hide the outliers.** They are not noise — they *are* the capping decision, the oversized list and the re-measure list. Count them and put the count on the chart.
- **Order the categories by median**, not alphabetically. The reader should be able to scan left to right and see the range widen.
- **Overlay the proposed cut** — the tote size, the weight limit, the cap — as a horizontal line. The chart then shows exactly how many SKUs the cut strands.
- Keep the house style: navy boxes, amber median line, grey whiskers, outliers as small grey dots.

> **Box plot or histogram?** A histogram shows one distribution in detail; a box plot compares **many** distributions at once. Use the box plot to compare categories, and drop to a histogram only when one category needs interrogating on its own.

---

## 2a · Publish the percentile table alongside the picture ⭐

The box plot is how you *show* the spread; the **percentile table is how you size from it**. A designer
does not size on a whisker — they size on **P95**. Publish both:

| Category | n | P50 | P75 | P90 | **P95** | P99 | Max |
|---|---:|---:|---:|---:|---:|---:|---:|

- **P50** is the typical SKU; **P75–P90** sizes the standard location; **P95** is the normal design point;
  **P99 and Max** tell you what the exception handling has to cope with.
- Sizing on the **max** over-builds for a handful of records that are often data errors anyway. Sizing on
  the **mean** under-builds, because these distributions are skewed. **P95 with a stated exception route**
  is the professional default — say which percentile you designed to, and why.

## 2b · Expect log-normal, and say so

Warehouse distributions — cube, velocity, order size, coverage, weight — are **almost always right-skewed
and roughly log-normal**. Two consequences worth stating on the page:

- **Mean ≫ median is normal, not an error.** If someone quotes "the average SKU", that number describes
  almost no real SKU. This is the entire reason the module exists.
- **Use a log axis** when the spread crosses orders of magnitude, and label it as log. On a linear axis
  every box collapses to a line at the bottom and the chart says nothing.

## 2c · Mind the sample size

- **Show `n` under every box** (already a rule) — and **grey out or suppress any group under ~30**.
  *(Heuristic — the point is that a box over 12 SKUs is not comparable to one over 40,000, and drawing
  them side by side invites a false conclusion.)*
- For very small groups, show the **points themselves** rather than a box.

---

## 3 · How to read it out loud

Every box plot in a deliverable gets a sentence in this shape:

> *"Median X, middle half from A to B, whisker to C, and **n** SKUs beyond that — so [the design consequence]."*

Then name the three things the client actually cares about:

1. **The middle half (IQR)** — what a "typical" SKU really looks like, and what the standard location should be sized for.
2. **The reach** — how far the whisker goes, and therefore what the *second* size has to cover.
3. **The outliers** — how many, and what they cost. These are the SKUs that force a cap, an oversized medium, or an exclusion.

**The comparison between boxes is the finding.** If one category's median sits above another's upper whisker, they cannot share a storage medium — and that sentence is worth more than the chart.

---

## 4 · Where the numbers go next

- The **IQR** sizes the standard location; the **upper whisker** sizes the second medium → `10`, `12`.
- The **outlier count** becomes the oversized / cannot-palletise flag → `02a`, `24`.
- The **cap** you draw on the chart is the cap the model applies → `10`, `24` §3.
- The spread in **units per line** and **lines per order** feeds the technology choice → `06`, `19`.

---

## Outputs

- A box plot per measure in scope, split by the client's own grouping, with `n` shown.
- The **one-sentence read-out** for each, in the shape above.
- An **outlier list** — counted, quantified, and carried forward as a flag, never silently dropped.
- The proposed cut / cap drawn on the chart, with the number of SKUs it strands.

---

## Checklist before you move on

- [ ] The measure and the grouping are the ones the **brief** turns on, not the ones that were easy.
- [ ] `n` is shown for every box.
- [ ] The axis is **log** where the spread demands it, and labelled as such.
- [ ] The whisker definition is **stated** (default Tukey 1.5 × IQR).
- [ ] Outliers are **shown and counted**, never trimmed to make the chart pretty.
- [ ] Categories are ordered by median.
- [ ] Any proposed cut / cap is drawn on the chart, with the stranded count quantified.
- [ ] Each chart has its one-sentence read-out, ending in a design consequence.

---

> **Worked examples.** *WAMAS:* box-volume box plot by concept group — median ranged from ~0.1 L to ~290 L across groups. That single chart ended the "one tote size" conversation and set the tote ladder.
> *Project Bombay:* units-per-line by channel showed the two operations are not variants of each other — Dotcom's distribution sits almost entirely on 1 unit (79.4% of lines), while Core Retail's middle half sits in the 6–10 band at 9.77 units per line. Two boxes, no overlap, opposite technologies. The cube box plot also surfaced the outliers that mattered: **16 SKUs whose stated each-cube was more than 5× anything their own picking or outer records implied** — a re-measure list, and a 3.9% correction to the peak storage number.

*Trivium Analytics · Warehouse Consulting Analysis Playbook · 03a · v0.1 · 2026-08-20 · pairs with [[03_Understand_the_Data]] · feeds [[10_Sizing_Cubic]] and [[12_Bucketing]]*
