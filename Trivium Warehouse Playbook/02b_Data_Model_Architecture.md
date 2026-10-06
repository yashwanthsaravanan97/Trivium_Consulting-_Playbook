# 02b · Data-Model Architecture — how a professional builds the model

> **What this is.** *(Layer 1.)* The engineering standard for the Power BI model itself. `02` says *load everything and append it*; this says **how to structure what you loaded** so the model is fast, correct, refreshable and readable by someone who isn't you. Skipping it is the difference between a model that answers questions in a second and one that hangs for an afternoon.

> **House standard, not universal law.** What follows is the mainstream enterprise Power BI standard —
> star schema, Roche's Maxim, measures over columns — and it is what a model review will test you against.
> It is not what every analyst everywhere does: small models live happily as flat tables, and a Fabric or
> Databricks shop pushes most of this into SQL. **Rules marked *(heuristic)* are judgement, not doctrine** —
> do not quote them to a client as if they were.

> **How to use it.** Build to this from the first table. Retrofitting a model architecture after the analysis has started means rewriting every measure, so decide the shape *before* the second table goes in.

---

## Inputs
- The loaded source files from `02`, already scrubbed at intake (`01a`).
- The grain and scope decisions from `01`.

---

## 1 · The rule that governs every other decision ⭐

> **Transform as far upstream as possible, and as far downstream as necessary.**
> *(Roche's Maxim of Data Transformation.)*

The order of preference is fixed:

**Source system → SQL / database / file prep → Power Query (M) → DAX calculated column → DAX measure**

Do each transformation at the **leftmost point you can**. Every step pushed right costs refresh time, model memory, or query time. The `02` rule *"append in Power Query, never DAX"* is one instance of this maxim — the maxim is what lets you decide the cases the playbook never anticipated.

---

## 2 · Where does this calculation belong? ⭐

The single most useful table in this module. When you don't know where to put something, look here.

| Put it in | Use it when | Cost | Example |
|---|---|---|---|
| **Source / SQL / file prep** | The data is bigger than the machine, or the shape is wrong at source | Cheapest — nothing enters the model | Pre-aggregating 38 M pick rows to a daily grain |
| **Power Query (M)** | Structural work done once at refresh: shaping, appending, merging, typing, splitting, cleaning | Refresh time only | Appending three channel files; joining SKU attributes; parsing dates |
| **DAX calculated column** | You must **slice, filter, group or sort** by it and it cannot come from M | Stored in memory forever; recomputed on every refresh | ABC band, XYZ band, speed band |
| **DAX measure** | Anything **aggregated**, or that must react to the user's filters | Nothing until queried | Pick lines, peak-day factor, % of lines |

**The rule of thumb: if it can be a measure, make it a measure.** A calculated column is computed at refresh and stored; a measure costs nothing until someone asks for it and respects filter context. The junior instinct is to make everything a column — that is how models become slow and enormous.

**Columns for slicing. Measures for aggregating.** If you would never put it on an axis, in a slicer or in a legend, it should not be a column.

> ### ⚠ The rule that will save you a day
> **A calculated column must never scan a large fact table row by row.**
> A column using `CALCULATE(… ALL(Fact) …)` is evaluated **once per row of its own table**, and each evaluation can scan the whole fact. On a 63,531-row table against a 61 M-row fact that is four billion operations — the engine will thrash, not finish, and every later model change re-triggers it.
> Compute per-entity aggregates **in one pass** — in Power Query, in the calculated *table* expression, or at source — never in a per-row calculated column.

---

## 3 · Star schema — not one flat table ⭐

Power BI's engine (VertiPaq) is built for a **star schema**. Model to it.

```
              DIM Date            DIM SKU
                    \              /
                     \            /
        DIM Store ——— FACT Outbound ——— DIM Site
                            |
                        DIM Area
```

- **Fact tables** hold *events* at their natural grain — keys and numbers, nothing descriptive.
- **Dimension tables** hold the *attributes you slice by* — one row per thing, unique key.
- Relationships are **many-to-one, single-direction**, fact → dimension. Avoid bi-directional filters; they create ambiguity and slow queries. Turn one on only for a specific, understood reason.
- **Do not build one wide flat table.** It compresses worse, it duplicates dimension attributes on every row, and it makes every measure harder to write.
- **Do not snowflake** unless you must — flatten dimension hierarchies into the dimension.

> **A flat wide table is an *extract*, not a model.** It is perfectly legitimate to produce a single wide per-SKU table for eyeballing, cross-checking or handing to Excel — but produce it *from* the star schema and label it as an extract. The model underneath stays a star.

---

## 4 · State the grain of every fact table ⭐

Write it in one sentence, and put it in the table description:

> *"One row per order line, per despatch date."*
> *"One row per SKU, per site, per monthly snapshot."*

**If you cannot write that sentence, the table is wrong.** Most modelling disasters are unstated-grain disasters — two tables get joined that were never at the same grain, and every number after it double-counts.

Where two facts are at different grains, do **not** merge them. Relate both to shared dimensions and let DAX do the work.

---

## 5 · The three-layer query pattern

Do not write one query per file and load them all. Use three layers, and put them in **query groups**:

| Group | Does | Loads? |
|---|---|---|
| **01 Source** | Connects to the file / database. Nothing else. | ❌ Disable load |
| **02 Staging** | Cleans, types, renames, filters, derives row-level columns | ❌ Disable load |
| **03 Model** | Appends / merges the staged queries into the table the model uses | ✅ Loads |

- Use **Reference**, not **Duplicate** — a reference chains, so a fix upstream flows through; a duplicate silently forks and you will fix the same bug twice.
- Only the **03 Model** queries load. Everything else is plumbing and should not appear in the field list.
- Name the groups so anyone opening the file sees the lineage in five seconds.

---

## 6 · Query folding — check it, don't assume it

**Folding** is Power Query pushing your steps back to the source as a native query. When it folds, the source does the work. When it breaks, everything downstream runs locally on the whole dataset.

- Right-click a step → **View Native Query**. Greyed out = folding has broken **at that step**.
- Keep folding alive as long as possible; put un-foldable steps (custom M functions, index columns, some merges) **last**.
- On file sources (CSV/Excel) there is no folding — that is exactly when you prepare the data at source instead.

---

## 7 · Model hygiene — the things professionals always do

- **Turn OFF Auto Date/Time.** *File → Options → Data Load.* Power BI silently creates a hidden date table **for every date column**; a model with fourteen date columns carries fourteen hidden tables. Build **one** date table and **Mark as date table**.
- **Hide every technical column** — surrogate keys, sort helpers, foreign keys on the fact side. If a user shouldn't drag it onto a page, hide it.
- **Display folders for measures** — group them (`01 Demand`, `02 Design Basis`, `03 Range`…). A field list with 60 loose measures is unusable.
- **Format at the model, not the visual** — set `#,##0`, `0.0%`, currency once on the measure, and every visual inherits.
- **Name in business language.** `Pick Lines`, not `sum_pick_lines_v2`. The field list is a user interface.
- **Sort-by columns** for anything that must not sort alphabetically (month names, size bands, ABC).
- **Recalculate after adding calculated columns** — `Refresh` type `Calculate`, or queries error with *"column … needs to be recalculated."*

---

## 8 · The control / audit table ⭐

Build one small table holding, per source file, the **row count and control totals as received**. Then a measure differencing model against source.

| Source file | Rows as received | Units as received |
|---|---:|---:|
| Outbound - Core Retail | 38,551,447 | 376,567,184 |
| Outbound - Dotcom | 20,935,208 | 29,420,202 |

`CHECK = [Model Rows] − [Source Rows]` **must return 0**, and it must stay visible on a check page for the life of the project. This is not paperwork — it is what catches a silently dropped row after a refresh, and it is what `25` verifies by hand at the end.

---

## 9 · Will it actually fit? — the scale check ⭐

`02` says *load all the client's data*. Do it **with your eyes open**:

- Estimate before you load: **rows × columns**, and watch **cardinality** — high-cardinality text columns (order numbers, timestamps, free text) are what actually consume memory, not row count.
- Import mode holds the whole model in RAM, and refresh needs **headroom on top of** the model size. *(Heuristic)* keep the model under roughly **half** of free RAM — there is no canonical figure, but refresh routinely needs about as much again as the model itself.
- If it will not fit, in this order: **drop columns you will never use** → **reduce cardinality** (split datetime into date + time; surrogate long keys) → **aggregate at source** → **incremental refresh** on the big fact → **aggregation tables** with the detail in DirectQuery.
- **Never load the same data twice.** A source table plus an appended copy of the same rows doubles the largest object in the model for no analytical gain.

---

## Outputs

- A **star schema** — fact tables at a stated grain, conformed dimensions, single-direction relationships.
- Power Query organised in **01 Source / 02 Staging / 03 Model** groups, with only the model layer loading.
- A **date table**, marked, with Auto Date/Time off.
- Measures in **display folders**, formatted at the model, business-named.
- A **control table** with a difference measure reading zero.
- A one-line **grain statement** on every fact table.

---

## Checklist before you analyse

- [ ] Every transformation sits at the **leftmost** point it could (Roche's Maxim).
- [ ] Nothing is a calculated column that could have been a **measure**.
- [ ] **No calculated column scans a large fact table per row.**
- [ ] The model is a **star**, not a flat table; a wide extract (if any) is labelled as an extract.
- [ ] Every fact table has a written **grain statement**.
- [ ] Query groups exist; staging queries **do not load**; references not duplicates.
- [ ] Folding checked, and un-foldable steps pushed last.
- [ ] **Auto Date/Time off**; one date table, marked.
- [ ] Technical columns hidden; measures foldered and formatted.
- [ ] **Control table reads zero.**
- [ ] The model **fits in memory with headroom**, and no data is loaded twice.

---

> **Worked example — Project Bombay (what went wrong, and why it is in this module).**
> Two architecture failures, both textbook, both expensive:
>
> **(1) The same data loaded twice.** The three outbound files were loaded as separate tables *and* as an appended fact — 61.5 M rows duplicated, taking the model to ~128 M rows on a 15.5 GB machine. Free RAM fell to 0.44 GB and the engine began thrashing. *Rule 9: never load the same data twice.*
>
> **(2) Per-row calculated columns over a huge fact.** `Weeks Moved` and `Despatch Days` were written as calculated columns using `CALCULATE(… ALL(Fact) …)` — each re-scanning the 61.5 M-row fact once per SKU, 63,531 times over. The refresh ran for hours without completing, and every subsequent model edit re-triggered it. The same figures computed in **one pass** inside the calculated-table expression return in seconds. *Rule 2: a calculated column must never scan a large fact per row.*
>
> A third, minor: **Auto Date/Time was left on**, quietly adding **14 hidden `LocalDateTable_*` tables** to the model. *Rule 7.*

*Trivium Analytics · Warehouse Consulting Analysis Playbook · 02b · v0.1 · 2026-08-24 · follows [[02_Load_and_Clean]] · next [[03_Understand_the_Data]] · verified at [[25_Manual_PowerBI_Check]]*
