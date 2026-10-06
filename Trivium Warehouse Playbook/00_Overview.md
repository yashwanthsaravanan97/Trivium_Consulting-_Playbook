# 00 · Overview — Warehouse Consulting Analysis Playbook

> **What this is.** A step-by-step method for a **warehouse consulting analysis** — the whole chain a warehouse / automation consulting job runs on. Take whatever the client sends (order lines, picking, inventory, inbound, outbound, master data), prove you can trust it, reproduce how the warehouse runs today, find where it hurts, size the future state, and build the business case. Storage-sizing is one part of this, not the whole.

> **The consulting spine.** Every job is the same chain:
> **Raw data → Data validation → Current-state understanding → Demand & inventory behaviour → Process analysis → Capacity analysis → Bottleneck identification → Future-state requirements → Solution sizing → Business case → Recommendations.**
> The *mindset* that turns numbers into consulting — the **10 analysis layers**, the **golden rule** (Data → Insight → Root cause → Impact → Recommendation), and the **opportunity tree** — lives in **`00a_Analysis_Framework.md`**. Read that first; it is the spine everything else hangs off.

> **When to reach for it.** Any warehouse project: right-sizing a footprint, an automation business case, a slotting study, a throughput / peak sizing, a current-vs-future comparison. **Not every module runs on every job** — the data you're given decides. Sometimes it's a whole-warehouse dataset (run most of the arc); sometimes it's only outbound, or only inbound — then run only those modules. See the tiers and the data-map in `00a`.

---

## How we work — Power BI + HTML, at every step

This is the through-line, and it never changes: **the data and the maths live in Power BI; every read-out is a Trivium-styled HTML view** (or Excel / PPT / PDF off the same model).

We **load** the data into Power BI → **clean** it there → **understand** it there and show that understanding in HTML → **analyse** it there (demand, inventory, process, capacity) and chart it → **size** the solution there → and turn it into a client **deck and business case**. Power BI is the engine room; HTML / PPT is the window we show the client and the boss. *(One exception: heavy cartons-per-pallet packing geometry may run in Python instead — but the read-out stays the same.)*

**Load *all* the client's data first, and append with Power Query (not DAX)** — see `02`. Only once everything is in and appended do you start the analysis; the very last step is *you* checking every number by hand in Power BI (`25`).

---

## The three engines — pick to the question

The playbook holds **three engines**. Two size the **space**; the third sizes the **work**. Which one a
project needs is decided once you understand the data (file `09`).

- **Engine A — Cubic sizing** *(size → footprint).* Size the storage unit — tote *or* pallet — from the **box cube**, then count how many units it needs from its **stock cube** × a realistic **fill %**. **Cap** runaway SKUs, and roll up into a **footprint** (locations, full-unit equivalents, utilisation). → file `10`.
- **Engine B — Cartons-per-pallet packing** *(carton → pallet qty).* For each SKU, find how many **cartons physically fit on one pallet** by testing box **orientations**, stacking into **layers** to a height limit, and respecting a **weight** limit. → file `11`.

- **Engine C — Order profile & ABC-XYZ** *(order lines → throughput).* Size the **work**: daily and
  day-of-week profile, peak factors, order shape, ABC on pick count, the Pareto curve, XYZ on days moved,
  and the SKU treemap. Runs off a despatch history and **needs no volumetric data at all**. → file `05`.

**A and B need measurements; C needs only history.** That matters more than it sounds — plenty of clients
have no volumetrics and never will, and C is the engine that still answers a useful question for them. It
is also the engine any **pick-automation** decision rests on, because vendors quote against lines per hour
at peak, not cubic metres. The rest of the playbook builds the **case** around whichever engine ran.

**The input to the sizing engines is always measurements** (a volume, raw L × W × H, weights; derive volume = L × W × H where missing). The demand and process analyses run off **order-line and transaction history** instead — they work even with no volumetrics at all.

---

## The arc — grouped by phase

Follow the spine in `00a`. Files are numbered in the order you'd generally work them — analysis first, then ship, then check. **Run only the phases the client's data supports.**

### Set up & trust the data *(Layers 1–2)*
| # | File | The phase |
|---|---|---|
| 01 | `01_Brief_and_Scope.md` | **Take the project.** What's asked, the goal, the units/limits, what "done" looks like |
| 01a | `01a_Client_Name_Check.md` | **Scrub it before you analyse it.** Decide named vs anonymised, strip every identifier **at load**, keep the crosswalk outside the shipping folder |
| 02 | `02_Load_and_Clean.md` | **Load *all* data to Power BI, append (Power Query, not DAX) & clean.** Record-level checks, **cross-table reconciliation** (orders → lines → picks → cartons → pallets → shipments), date coverage |
| 02a | `02a_Volumetric_Cube_Check.md` | **Deep cube-data check** — is the cube consistent, and can you rebuild pack quantity from it? |
| 02b | `02b_Data_Model_Architecture.md` | **How a professional builds the model** — Roche's Maxim, where each calculation belongs, star schema, grain, query folding, the control table, will it fit? |
| 03 | `03_Understand_the_Data.md` | **What is the data telling us?** Profile it (size spread, coverage, key counts) and build the **range treemap** — what the client actually handles — shown in HTML |
| 03a | `03a_Box_and_Whisker_Analysis.md` | **The spread, not the average** — distribution by category; where the caps, the second medium and the oversized list come from |
| 04 | `04_Warehouse_Business_Profile.md` | **Warehouse fact sheet** — what kind of warehouse is this? Volumes, physical, operational at a glance |

### Understand the demand & the work *(Layers 3–4)*
| # | File | The phase |
|---|---|---|
| 05 | `05_Order_Profile_and_ABC_XYZ.md` | **Demand analysis** — order & line profile, peak factors, ABC / XYZ / Pareto / treemap, **SKU velocity, age & affinity**, design basis |
| 06 | `06_Picking_and_Process_Analysis.md` | **Picking** — pick types, productivity, hot/cold locations, pick-face sizing, replenishment |

### Storage, inventory & sizing *(Layers 5–6)*
| # | File | The phase |
|---|---|---|
| 07 | `07_Inventory_Analysis.md` | **Inventory** — cube, days-of-inventory / coverage, aging, over/under-stock |
| 08 | `08_Storage_and_Location_Analysis.md` | **Storage & locations** — utilisation, occupancy, **fragmentation & SKU-split**, hot/cold |
| 09 | `09_Choose_the_Engine.md` | **Tote calc or pallet calc?** Pick the sizing engine from the question and the data |
| 10 | `10_Sizing_Cubic.md` | **Tote / cubic calc** — tote ladder, size-by-box-cube → count-by-stock-cube × fill, caps, full-tote equivalents |
| 10a | `10a_Cubic_Tote_Sizing_Worked_Method.md` | **Engine A, fully worked** — a real tote ladder, 60% fill, caps 5 / 20 / Pallet, with the DAX written out |
| 11 | `11_Sizing_CartonsPerPallet.md` | **Pallet calc** — orientations, layers, height & weight limits |
| 12 | `12_Bucketing.md` | **Create the buckets** — weight, cover, location and tote bands |
| 13 | `13_Current_State_and_Scenarios.md` | **Actuals per medium** and **reconcile** to the whole; remaining-stock and by-category scenarios |

### The flow & operations *(Layers 4 & 7)*
| # | File | The phase |
|---|---|---|
| 14 | `14_Inbound_Outbound_Dispatch.md` | **Inbound / putaway / outbound / dispatch** — receipts, putaway, shipments, the dispatch wave |
| 15 | `15_Throughput_and_Forecast.md` | Packs → lines, peak-day/hour factors, **seasonality**, the storage + throughput table, the multi-year forecast |
| 16 | `16_Process_Flow_and_Bottleneck.md` | **Process flow & bottleneck** — demand vs capacity at every stage; where it breaks |
| 17 | `17_Labour_Equipment_Travel.md` | **Labour, equipment & travel** — FTE, MHE utilisation, travel distance |

### Design the future state *(Layers 8–10)*
| # | File | The phase |
|---|---|---|
| 18 | `18_Slotting.md` | **Slotting** — current vs recommended location; cube-per-pick, order affinity, pick-face fit |
| 19 | `19_Automation_Suitability_and_Sizing.md` | **Automation** — what to automate, then **size & simulate** the fleet (AMR / GTP / sorter / AS-RS) on peak |
| 20 | `20_Future_State_and_Business_Case.md` | **Future state, scenarios & business case** — current-vs-future, CAPEX / OPEX, savings, ROI, payback |

### Ship it — then check it (the last steps)
| # | File | The phase |
|---|---|---|
| 21 | `21_Deliverables_and_House_Style.md` | Final read-outs: the Trivium look, HTML → image, Excel, PDF, and the **standard 11–13 slide client deck** |
| 22 | `22_Updating_for_New_Data.md` | New/missing SKUs → recalculate → refresh **every** table, workbook and slide |
| 23 | `23_Glossary_and_Reference.md` | Terms, tote/pallet capacities, key formulas, reference numbers — keep it open beside you |
| 24 | `24_Recheck_and_Common_Mistakes.md` | **The final pass** — re-check the whole study against the mistakes that have bitten us before |
| 24a | `24a_Client_Name_and_Confidentiality_Check.md` | **The anonymisation gate** — check every artifact for the client's name; remove/replace |
| 25 | `25_Manual_PowerBI_Check.md` | **Manual Power BI check (very last)** — *you*, reproducing every headline number as a live table and ticking it off before it ships |

*(`01`–`04` — including the intake scrub `01a` — and the "ship it & check" files `21`–`25` are shared by every job. Under storage-sizing you run `10` **or** `11`; under a demand / automation job you lean on `05`–`08` and `14`–`20`. **Run only the modules the data supports** — an outbound-only dataset runs `05`, `14`, parts of `06`/`16`, and stops.)*

---

## Who this is written for

Assumes an analyst who can **write DAX and M**, **read a distribution** (median, quartile, percentile,
skew), **reconcile a total**, and **hold a grain in their head**. It does not teach those. It does teach
the warehouse domain, the consulting chain, and the mistakes that have cost us time.

**Rough weight of each phase** on a whole-warehouse job — use it to plan, not to bill:

| Phase | Share of the effort | Why |
|---|---|---|
| Trust the data (`01`–`02b`) | **~40%** | Always the largest block, always underestimated |
| Understand it (`03`–`04`) | ~10% | Fast once the data is trustworthy |
| Analyse it (`05`–`08`, `14`–`17`) | ~25% | The bulk of the *analysis*, but not of the *work* |
| Design & size (`09`–`13`, `18`–`20`) | ~15% | Fast if the layers beneath are solid |
| Ship & check (`21`–`25`) | ~10% | Non-negotiable; cutting it is how errors ship |

If data-trust is taking less than a third of your time, you are probably about to discover why it should have.

---

## How to use it

- **Read `00a` first** — the 10 layers, the golden rule and the opportunity tree are the spine; every module is one branch of it.
- **First time on a job:** work the phases in order — trust the data, understand it, analyse it, design, then ship, then check.
- **Next time:** jump to the module you need; each stands alone with **Inputs → What you do → Outputs → Checklist**.
- **Adapt to the data you're given.** This is the *master* — a whole-warehouse dataset uses most of it; a single-feed job (only inbound, only outbound) uses just those modules. Map the data to the layers in `00a` first, then run what's live.
- **The files are interactive** — question prompts and tick-boxes, not just prose. Work the questions and you land what the client asked for; `24` is the recheck and `25` is *your* final manual Power BI verification.

---

## Principles we hold to (the hard-won ones)

- **Nail the exact output the client wants — first.** Before you model, say in one line what the client holds at the end. File `01` forces this.
- **Load all the data, append with Power Query, then analyse.** Never append with DAX (`UNION`) — see `02`.
- **The golden rule, on every finding.** Never stop at *what* happened — carry each number through **Data → Insight → Root cause → Impact → Recommendation**. That progression is what makes it consulting, not reporting (see `00a`).
- **Quantify every opportunity.** A problem named without a number ("poor slotting") is worth nothing; "35% of travel comes from 8% of SKUs" is a recommendation waiting to happen.
- **Power BI + HTML, always.** The maths lives in Power BI (calculated columns / DAX); every read-out is a styled view. Same tools from clean-up to final chart.
- **Understand the data before you model it.** Half the mistakes come from modelling before you've seen what the data actually says.
- **Size on the peak, never the average.** Automation and capacity are sized for the busy day / hour, not the mean — state the peak factor explicitly.
- **Reconcile everything.** Every model ties back to the whole — total SKUs, m³, units, lines, orders. If it doesn't reconcile, it's wrong until proven otherwise.
- **Subtract, don't sum.** *(Engine A.)* To get "everything except X", subtract X from the total; a total rarely decomposes cleanly into the parts you think you know.
- **Check it by hand at the end.** The last thing before it ships is *you* reproducing every number live in Power BI (`25`) — nothing hand-typed, nothing stale.

---

## Reference implementations

Live projects this playbook is distilled from — where a file says **"worked example,"** these are the source:

- **Cubic tote sizing (Engine A):** WAMAS per-SKU data, ~400k SKUs, in Power BI + HTML.
- **Cartons per pallet (Engine B):** the SKU Carton-Pallet project — Euro-pallet fit across box orientations, in Python.
- **Demand / order profile (`05`):** Project Seeds order-line history — ~3.0M lines / 5,008 SKUs / 274 despatch days, ABC-XYZ and the design basis.
- **Whole-chain consulting run (`01a`–`25`):** Project Bombay — 15 client files, 66.4M rows, three channels, anonymised from intake; the most complete end-to-end run of this playbook to date.

---
*Trivium Analytics · Warehouse Consulting Analysis Playbook · v0.6 · 2026-08-24*
