# 22 · Updating for New Data — new SKUs in, recalc, refresh everything

> **What this is.** Data changes: the client sends more SKUs, corrects some, or a new extract lands. The logic lives *in the model*, so an update should flow through — **if** you re-run the whole chain. This is where stale deliverables get caught.

> **How to use it.** Treat every downstream output as a query snapshot that must be regenerated. Miss one and two numbers disagree.

---

## Inputs
- The new/changed data (an append file, a corrected column, a fresh extract).
- The existing model + all deliverables.

## What you do
1. **Check the new data first** (`02` discipline) — is it distinct (pure append) or does it overlap existing keys? Same columns?
2. **Bring it in.** For a pure append, add a **second partition** (or append in Power Query) so the source stays intact and the append is reversible.
3. **Refresh + recalc.** Refresh the table so both partitions load and calculated columns/tables follow; if calc columns error, run `Refresh` type **`Calculate`**.
4. **Re-check the new totals** — SKUs, m³, units. Do they move by the expected amount?
5. **Regenerate every downstream deliverable** — each HTML, each Excel sheet, each PPT slide, the PDF. **Nothing may be left on the old basis.**
6. **Excel:** after any programmatic write, reopen via COM → `CalculateFull` → Save (the backend doesn't recalc). Follow linked/forecast sheets.

## The trap this file exists to prevent
- [ ] After an update, some outputs still showed the **old total**. → **Re-run the whole chain**, then diff old vs new totals on one page to prove consistency. (See [[24_Recheck_and_Common_Mistakes]].)

## Outputs
- An updated model at the new basis.
- Every deliverable regenerated and cross-checked to the same totals.
- A one-line before/after note (what changed and by how much).

## Checklist
- [ ] New data verified (distinct vs overlap) before loading.
- [ ] Model recalculated; new totals sanity-checked.
- [ ] **Every** HTML / Excel / PPT / PDF regenerated — none stale.
- [ ] A before/after reconciliation is shown.

---
> **Worked example — WAMAS.** Client sent 13,860 previously-missing SKUs — all distinct, zero overlap → pure append via a second partition (reversible by deleting it). WAMAS_Band 387,795 → 401,655 rows; totals 7,891 → 8,391 m³, 2,476 → 2,623 M units. Then regenerated all three HTMLs and both Excel sheets to the new basis (with a grey "before" row to show the shift).

*Trivium Analytics · Warehouse Consulting Analysis Playbook · v0.1 · 2026-07-28 · next [[23_Glossary_and_Reference]]*
