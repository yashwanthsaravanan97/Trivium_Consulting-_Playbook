# 25 · Manual Power BI Check — you, verifying every number in the live model

> **What this is.** **The very last step, and you do it by hand.** Once the whole analysis is done and the deck is built, sit in front of Power BI and **manually reproduce every headline number as a live table**, ticking each off against the deliverable. It proves nothing was hand-typed or went stale — every figure traces back to the live model. This is the final gate after the recheck (`24`): do it before anything ships. Clients and bosses routinely ask "show me these numbers in Power BI" — this is where *you* catch the one that doesn't tie.

> **How to use it.** Interactive — build each visual, then tick it off against the matching page of the deliverable. If one number doesn't tie, **stop and find out why** before it ships. Screenshot each check and keep them with the pack.

---

## Why we do it
- The deck (PDF/HTML) is a **snapshot**; the model is **live**. This step shows they agree.
- It catches the classic errors — a stale number, a wrong filter, the wrong column, a rounding drift.
- It hands the client/boss a screenshot-level audit trail.

## Inputs
- The final deliverable and its key numbers (the overview totals, each model table, the reconciliations).
- The live model with the calculated columns built earlier (the sizing / category / bucket columns from `10` · `12` · `13`).

---

## What to build — Tier 1 (no new columns, do this first)
Make a "Check" report page straight off the main table:

- [ ] **Headline totals → Card visuals.** One card each: **Count** of the id/key column (total SKUs), **Sum** of the total-quantity column, **Sum** of the volume column. Cross-check against the deck's overview totals.
- [ ] **Each model table by its bucket → a Matrix.** Rows = the model's bucket column (tote size / band / category); Values = **Count** of the id column and **Sum** of the relevant quantity column. The **grand-total row** is what you cross-check against the deck's table total.

That already verifies the totals and the SKU/quantity split with zero setup.

## What to build — Tier 2 (a few helper columns → the full tables)
Some deck columns are **model outputs** (locations, full-unit equivalents) that aren't stored per row. Add a few **calculated columns**, then a Matrix. Template (adapt names/logic per project — this is the WAMAS spillover version):

```DAX
Rem_Category =
IF ( ISBLANK ( WAMAS_Band[New Location Type] ), BLANK(),
    IF ( WAMAS_Band[NavetteQty] > 0 || WAMAS_Band[ManualShelving_Qty] > 0
      || WAMAS_Band[Haz_Qty] > 0 || WAMAS_Band[Xilinx_Qty] > 0,
      "Cat 1 - Spillover", "Cat 2 - Outside" ) )
```
```DAX
Rem_Locations =
VAR remM3 = WAMAS_Band[Volume_M3] - WAMAS_Band[Navette_M3] - WAMAS_Band[ManualShelving_M3] - WAMAS_Band[Haz_M3] - WAMAS_Band[Xilinx_M3]
VAR fill = IF ( WAMAS_Band[NavetteQty] > 0 || WAMAS_Band[ManualShelving_Qty] > 0 || WAMAS_Band[Haz_Qty] > 0 || WAMAS_Band[Xilinx_Qty] > 0, 0.75, 0.5 )
VAR cap  = SWITCH ( WAMAS_Band[New Location Type], "1/16",0.0012775,"1/8",0.00511,"1/4",0.01022,"1/2",0.02044,"Full",0.04088,"Full Big",0.12768,"Pallet",1.44, BLANK() )
RETURN IF ( ISBLANK ( WAMAS_Band[New Location Type] ) || WAMAS_Band[New Location Type] = "Oversized", BLANK(), ROUNDUP ( DIVIDE ( remM3, cap * fill ), 0 ) )
```
```DAX
Rem_FullTote =
VAR frac = SWITCH ( WAMAS_Band[New Location Type], "1/16",DIVIDE(1,14),"1/8",DIVIDE(1,7),"1/4",DIVIDE(1,3.96),"1/2",DIVIDE(1,1.98),"Full",1, BLANK() )
RETURN IF ( ISBLANK ( frac ), BLANK(), WAMAS_Band[Rem_Locations] * frac )
```

**The reconciliation table everyone wants — put Category in the matrix _Columns_ well, not Rows:**
- Rows = the bucket (tote size) · **Columns = `Rem_Category`** · Values = **Count** of id (+ `Sum` of Locations, FullTote, units).
- Power BI adds a **Total column** automatically = Cat 1 + Cat 2 = the parent table. That one visual **is** the cross-check.
- Filter **`New Location Type` is not blank** to drop the not-applicable rows.

---

## The cross-check checklist
- [ ] Whole-warehouse totals (SKUs / units / volume) match the deck's overview.
- [ ] Each model table's **grand total** matches its deck page.
- [ ] **The parts add to the whole** — Cat 1 + Cat 2 = the Remaining / parent table (per bucket *and* in total).
- [ ] The **right column** feeds each visual (watch the near-identical twins — see below).
- [ ] Blank / not-applicable rows are understood and filtered where the deck excludes them.
- [ ] Every check is screenshotted and filed with the pack.

---

## Watch-outs (these actually bit us — WAMAS)
- **The blank row.** A size/category column is **blank** for rows the model excludes (SKUs with no reserve stock). The matrix shows a blank row, and its **grand total then equals the whole population, not the model total.** Add a visual filter *"column is not blank"* and the total drops to the model figure. *(WAMAS: total went 401,655 → 107,139 once the blank 294,516 rows were filtered.)*
- **The wrong-column trap.** Two near-identical columns (`New Location Type` vs `New Location Type (MS Excl)`). Tell-tale: the two versions show **identical row counts** — impossible if they were genuinely different. Always confirm which column feeds the visual.
- **Subset vs total confusion.** Cat 1, Cat 2 and the parent table have **different SKU counts on purpose** — Cat 1 and Cat 2 are the two halves, each smaller; together they equal the parent (57,831 + 49,308 = 107,139). Not an error.
- **Bucket sort order.** Text buckets sort alphabetically (1/16, 1/2, 1/4, 1/8 …). Ignore it, or sort by a value column — the totals are the proof.
- **Rounding.** Fractional columns (full-tote equivalents) can differ from a sum by ±1. Tie on the integer columns (SKUs, locations, units); footnote the ±1.

---

## Worked example — WAMAS (the numbers to hit)
- **Cards:** 401,655 SKUs · 2,623,003,406 units · 8,391 m³.
- **Reserve-stock tables (grand totals):** 4-media **107,139** SKUs / **1,575,614,461** units · 3-media **298,577** / **2,110,252,942**.
- **Category split (4-media), Category-in-columns matrix:**

| Tote size | Cat 1 | Cat 2 | **Total = Remaining** |
|---|--:|--:|--:|
| 1/16 | 10,791 | 6,697 | **17,488** |
| 1/8 | 13,754 | 7,399 | **21,153** |
| 1/4 | 8,828 | 5,385 | **14,213** |
| 1/2 | 8,239 | 6,750 | **14,989** |
| Full | 15,126 | 12,369 | **27,495** |
| Full Big | 1,005 | 9,385 | **10,390** |
| Pallet | 85 | 1,311 | **1,396** |
| Oversized | 3 | 12 | **15** |
| **Total** | **57,831** | **49,308** | **107,139** |

Locations reconcile the same way: Cat 1 85,249 + Cat 2 134,042 = **219,291**; full-tote 48,706 + 62,356 = **111,062**.

> **Tip — shortcut for building the check page:** you can also have the analyst generate the check tables as **calculated tables** (prefixed `Check_`) so the reviewer just drags them onto a page. Either way, the numbers must tie to the deck.

---
*Trivium Analytics · Warehouse Consulting Analysis Playbook · v0.2 · 2026-08-18 · the final gate, after [[24_Recheck_and_Common_Mistakes]] · pairs with [[21_Deliverables_and_House_Style]]*
