# 12 · Bucketing — group the SKUs into bands the data actually needs

> **What this is.** Turn per-SKU results into the **buckets** the client reasons in — weight bands, cover/velocity bands, location bands, tote/size buckets. Buckets make a 400k-row model legible on one chart. Done in Power BI, shown in HTML. **Don't forget this step** — it's where the model becomes a story.

> **How to use it.** Decide each band set from the data (not arbitrary round numbers), then check the buckets sum back to the population.

---

## Inputs
- The per-SKU model output from `10` / `11`.
- Any client-preferred bands (ask — they often have house definitions).

## Common bucket types
- [ ] **Size / tote buckets** — which unit size each SKU lands in (16th … Pallet), or carton-per-pallet ranges.
- [ ] **Location bands** — how many locations a SKU needs: `1 · 2–5 · 6–10 · 11–20 · 20–50 · 50+`. Great for a stacked bar showing the deep-stock tail.
- [ ] **Weight bands** — for pallet/transport work.
- [ ] **Cover / velocity bands** — days of cover, or fast/medium/slow movers (if demand is in scope).
- [ ] **Department / area / medium** — the client's existing groupings.

## What you do
1. Choose the **edges** from the data — look at the distribution, put breaks where they're meaningful, not just at round numbers.
2. Build the band as a **calculated column** (a `SWITCH(TRUE(), …)` on the metric) so it's live.
3. Aggregate per band with `GROUPBY` / `SUMMARIZE` and chart it.
4. **Check the buckets sum to the whole** — every SKU in exactly one band; band counts add to the population.

## Outputs
- Band calculated columns in the model.
- HTML charts: distribution by band, and the size ladder split by location band.

## Checklist
- [ ] Band edges are justified by the data (or the client's own definition).
- [ ] Every SKU falls in exactly one band (no gaps, no overlaps).
- [ ] Band counts **sum to** the population.
- [ ] The chart makes the point you want the client to see (e.g. the deep-stock tail).

---
> **Worked example — WAMAS.** Location bands `1 / 2–5 / 6–10 / 11–20 / 20–50 / 50+` drove the stacked "storage locations by tote type" bars — instantly showing small totes are ~1 location each while Full/Full Big carry the deep-stock tail.

*Trivium Analytics · Warehouse Consulting Analysis Playbook · v0.1 · 2026-07-28 · next [[13_Current_State_and_Scenarios]]*
