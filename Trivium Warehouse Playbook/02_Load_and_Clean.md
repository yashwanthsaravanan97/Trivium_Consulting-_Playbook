# 02 · Load & Clean — into Power BI, then fix what's wrong

> **What this is.** Get the source into Power BI and make it trustworthy. Every project starts with a data file in *some* shape (a WMS export, a spreadsheet, a tab-delimited dump). Load it, then run a disciplined data-quality pass. **The maths lives in Power BI from here on** — so this is where it starts.

> **How to use it.** Interactive — each check below is a question that must end in "yes, handled" before you model anything.

---

> **Before you load: run `01a`.** If this job is anonymised, the identifier scrub happens *as* the data loads, not afterwards. Loading the real names and cleaning them later means every downstream artifact inherits them.

## Inputs
- The client's source file(s) — whatever form they arrive in.
- The parameter/measurement list from `01`.

## What you do
1. **Load into Power BI** (Power Query). Keep the raw load untouched; do transforms as steps so they're re-runnable.
2. **Profile the columns you'll use** — types, nulls, min/max, distinct counts. (Power Query's column profiling, or a quick read-only DAX scan.)
3. **Run the quality checks** (below), fixing or flagging each.
4. **Record row counts before and after** cleaning, and *why* each row left.

> **Rule — load *all* the client's data first, append it, *then* analyse. Append with Power Query, never with DAX.** ⭐
> Get **every** dataset into Power BI up front. Where the client sends one logical table split across files (monthly extracts, multiple sites, a daily-file series), **append them in Power Query** — a folder-combine or an M append query — so you end up with **one clean physical table** before any analysis starts.
> **Never append with DAX (`UNION`).** A Power Query append happens at the *source*: it refreshes cleanly, keeps column types, and every downstream measure and calculated column sees a single table. A DAX `UNION` is a *virtual* table rebuilt at query time — slower, type-fragile, invisible to Power Query, and a refresh trap. **Append in M; reserve DAX for calculation.** Only once all data is loaded and appended do you begin the analysis. *(Hard-won on a previous client.)*

> **But check it fits first.** "Load all the data" is not "load it twice, or at any size." Estimate rows x columns and watch high-cardinality columns; keep the model under about half of free RAM; and **never load the same rows twice** (a source table plus an appended copy of it doubles your largest object for no analytical gain). If it will not fit: drop unused columns, reduce cardinality, then aggregate at source. Full guidance in **`02b`**.

## The quality checks (tick every one)
- [ ] **Units consistent?** Parse all dimensions to one unit (mm is safest). No mixing mm/cm/dm. Derive volume if it's missing (`V = L × W × H`).
- [ ] **Missing / impossible measurements** — zero or negative dims, placeholder rows (all dims ≤ 1). Decide: remove or flag. *Zero-cube = flag and count it, never blank-and-forget.*
- [ ] **Display glitches** — Excel `######`, text in number columns, stray units. Coerce to number → NaN → handle.
- [ ] **Duplicates** — is the key really unique (one row per SKU)? Check before you trust counts.
- [ ] **Impossible-to-store items** — box bigger than the pallet/largest tote. Flag as `CANNOT_PALLETISE` / `Oversized`, don't silently drop.
- [ ] **Columns you don't need** — drop them (blank `outer_type`, unused flags) to keep the model clean.
- [ ] **Pre-existing calculated columns** — treat the client's own `pal_qty` / `cartons_per_tier` as suspect. Recalculate; don't inherit.
- [ ] **Totals that don't decompose** — if a "total" column doesn't equal the sum of its parts, note the gap now (you'll use **subtraction**, not sum — see `13`).

## For multi-dataset consulting jobs — reconcile across tables ⭐

Storage-sizing usually has one table; a full consulting job has several — orders, order lines, picks, inventory, inbound, outbound. Before you analyse any of them, **reconcile them against each other**, or every downstream number inherits an unexplained gap.

**Record-level checks — run on *every* dataset:** total records · unique IDs · duplicates · nulls / blanks · invalid values · **negative or zero quantities** · negative / zero dimensions or weights · **invalid or future dates** · missing SKU / order / location / pallet / tote IDs.

**Cross-table reconciliation — walk the chain and quantify every gap:**

```
Orders → Order Lines → Picks → Cartons → Pallets → Shipments
```

| Metric | Order data | Pick data | Outbound |
|---|---|---|---|
| Orders | 100,000 | 99,500 | 98,900 |
| Lines | 500,000 | 495,000 | 490,000 |
| Units | 2.00 M | 1.98 M | 1.96 M |

Then **investigate why the numbers differ** — cancelled orders, short picks, unshipped lines, capture gaps. An unexplained gap is a *finding* (often a client data-quality issue worth reporting on its own). Then pick the **one table you'll size on**, and state it.

## Date coverage ⭐

For any transactional history, establish the window before you touch it:
- **First date · last date · number of days / weeks / months / years**
- **Missing days / weeks** — a missing Saturday is a *finding*, not a gap (see `05`)
- The **seasonality** the window does or doesn't capture

This is critical for peak demand: everything per-day later divides by **actual working / despatch days**, never by 365, and a window that misses the peak season understates the design basis. Under 12 months of history → seasonality becomes a guess; say so.

## Outputs
- Clean, typed, unit-consistent tables in Power BI — **each at a stated grain** (`02b`). A storage-sizing job may end with one row per SKU; a full consulting job ends with several tables at different grains, and that is correct.
- A **cleaning log**: raw rows → clean rows, with a reason for every removal and every flag.

## Checklist before you move on
- [ ] Every check above is "handled", not "probably fine".
- [ ] Row counts before/after are recorded and explainable in one line each.
- [ ] Flagged edge cases (zero-cube, oversized) are still in the model, marked — not deleted.
- [ ] Nothing has been modelled yet — cleaning only.

---
> **Worked examples.** *WAMAS:* a tab-CSV of ~400k SKUs; parsed box dims to mm, flagged zero-cube, kept per-medium `*_M3`/`*_Qty`/`*_Locations`. *Carton-Pallet:* removed 612 placeholder (≤1 cm) rows + 1 `######` weight; replaced the corrupt `pal_qty` column entirely.

*Trivium Analytics · Warehouse Consulting Analysis Playbook · 02 · v0.3 · 2026-08-24 · next [[02a_Volumetric_Cube_Check]] then [[02b_Data_Model_Architecture]] · see [[24_Recheck_and_Common_Mistakes]]*
