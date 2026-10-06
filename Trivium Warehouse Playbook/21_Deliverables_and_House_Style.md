# 21 · Deliverables & House Style — the Trivium look, and every output format

> **What this is.** How the work leaves the building: the Trivium visual style, and the mechanics of every format — HTML, image, Excel, PPT, PDF. Same maths (Power BI), many read-outs.

> **How to use it.** Pick the formats the audience (`01`) needs; apply the house style to all of them.

---

## The Trivium house style

> **Verified against the firm's own client deck (Aug 2026).** The PPT palette and font below are read
> straight from the template's theme — **not** from memory. Earlier versions of this module said
> "Calibri" and quoted a different navy/amber; both were wrong. **The deck is the source of truth.**

### The PPT theme — what the template actually carries

- **Font: `Montserrat`** for *both* heading and body. It is set in the theme, so **never set a font
  in code** — type inherits. Setting Calibri (or anything else) is how a slide starts looking foreign.
- **Theme colours** (use these names, not hex, wherever the API allows):

| Theme slot | Hex | Used for |
|---|---|---|
| `accent1` | `#023047` | Deep navy — table headers, title emphasis |
| `lt2` | `#219EBC` | Mid blue — secondary series, rules |
| `dk2` | `#8ECAE6` | Pale blue — light fills, banding |
| `accent2` / `accent3` | `#FCCA46` / `#FCBF49` | Yellow-amber — cover ground, highlights |
| `accent4` | `#FB8500` | Orange — the logo mark, strong accent |
| `accent5` / `accent6` | `#F25C54` / `#D62828` | Coral / red — exceptions, warnings |
| `lt1` | `#202020` | Ink — body text |
| `dk1` | `#FFFFFF` | White — text on dark or amber grounds |

- **Sizes actually used:** cover title inherits (large); cover subtitle **28 pt**; agenda/section number
  **54 pt**; body and callout text **12 pt**; dense reference tables **8.5–10 pt**.

### The deck skeleton

**Cover → Agenda → content block → Agenda → content block → … → Next Steps → Thank You.**
The `Agenda` layout is reused as a **section divider** before each block (5 times in the reference deck),
carrying a big numbered section title such as *"01 Analysis"*.

- **`Title Only with Logo` is the workhorse** — 20 of 28 slides in the reference deck. Content is placed
  as **exported images** positioned on the open canvas, not into picture placeholders.
- **Content canvas:** roughly `y = 1.6" → 7.0"`, full width `x = 0.37" → 12.96"` on a 13.333 × 7.5" slide.
- **The title is ONE line.** The title placeholder is `12.59 × 0.38"` at `(0.37, 0.61)` and the subtitle
  sits immediately under it at `(0.37, 1.18)`. A title that wraps to two lines **collides with the
  subtitle** — shorten the title and push the detail into the subtitle.
- **Titles are findings, not labels.** The reference deck reads *"Manual Shelving consistently has the
  highest range and is consistent"*, not *"Storage by area"*. Write the conclusion as the title; the
  exhibit underneath is the evidence.
- **Useful layouts:** `1_Title + Image 1` (cover) · `Agenda` (section divider) · `Title Only with Logo`
  (content) · `Title + Table` (full-width table, `12.59 × 4.79"` at `(0.37, 1.98)`) · `3_6 Callouts`
  (six rounded cards, 12 pt) · `Split 2 Callouts` · `Statement` · `Thank You`.

### Build mechanics

- **Build from the firm deck itself**, so theme, fonts, logo, footer and slide numbers inherit:
  load it, drop every existing slide (`prs.part.drop_rel(sldId.get(qn('r:id')))` **and** remove the
  `sldId`, or the file corrupts), then add slides and **set text only**.
- Dropping the slides also drops the weight — the 81 MB reference deck becomes ~1 MB.
- **Let the template's table style do the banding.** Adding your own row fill on top produces
  double-banding that fights the theme.
- **Verify by rendering.** Export every slide to PNG via PowerPoint COM and look at it. Overflowing
  titles and colliding placeholders are invisible in code and obvious in a picture.

### The HTML / Excel look (unchanged — these are ours, not the client template)
- **Colours:** navy `#1F3864` (headers, titles) · amber `#FBAE40` (accents, rules, key cells) · green `#1F8A4C` (good/result lines) · grey text `#6B7480` · ink `#2E3444`.
- **Type:** Segoe UI / Calibri, ~10–13 pt.
- **Local HTML needs its own `<meta charset="utf-8">`** or `·` and `—` render as mojibake when
  screenshotted; and headless Edge follows the OS dark-mode setting, so force `data-theme="light"`
  on anything bound for a slide.
- **HTML tables:** navy header row + white bold; amber "key" columns; zebra rows; totals in bold navy; edge rows greyed/italic.
- **Excel:** navy bold title, grey subtitle, **amber rule**, left bullets / right figure, footer with the amber bow-tie ✕ mark + "© 2026 Trivium Analytics Limited".
- **PPT:** do **not** apply the above by hand — the firm template already carries it. Use its layouts and set text only.
- **Charts inside a client-facing pack** may need the client's own tool styling rather than the Trivium palette — on Project Seeds the boss asked for charts that read as native **Power BI** (default theme teal `#01B8AA` / coral `#FD625E`, white card, 1px `#E1DFDD` border, Segoe UI, horizontal-only gridlines, square-cornered marks). Page chrome stayed Trivium; only the charts changed. Ask which is wanted.

## The formats and their mechanics
- **HTML** — the primary read-out. Self-contained Trivium card (header, step boxes, table, KPI cards, CSS bar chart). Open with `Start-Process msedge.exe "file:///…/file.html"` (encode spaces as `%20`).
- **HTML → PNG** (for slides): `msedge --headless=new --screenshot=out.png --window-size=W,H --force-device-scale-factor=2 --hide-scrollbars --disable-gpu --no-sandbox --user-data-dir=<temp> file:///…` then **Pillow autocrop** (`ImageChops.difference` vs a corner-pixel bg → `getbbox` + margin). `python` is blocked on the box — use the **`py`** launcher.
- **Excel** — write values/formulas, but **the write backend does NOT recalc.** Reopen via Excel **COM** → `CalculateFull()` → `Save()`, or formula/linked cells stay stale. To create a *new* xlsx (MCP can't): make a blank via COM (`Workbooks.Add` + `SaveAs 51`) first, then write.
- **PPT** — **always build from the firm's own template, never from a blank layout.** *(Corrected 2026-08-11 — this section previously said to hand-draw the navy/amber styling on a blank 13.333 × 7.5 layout. The boss rejected exactly that: firm decks must use the template's own layouts, fonts and design as-is, with no custom shapes and no invented tables. Calibri, the amber bow-tie footer and the copyright line all come through from the master automatically.)*
  - **Template:** `c:\Claude\Trivium training\Resources\Trivium 2026 Template presentation Claude Use_V1.pptx` — 16:9, 39 layouts.
  - **Structure, always:** cover (`1_Title + Image 1`) → "What this covers" (`Statement`) → content → `Thank You` last.
  - **Useful layouts:** `Layout + Image` (title + explanation + a 6.12 × 6.41″ picture box — the read-out workhorse), `Split 2 Callouts` / `Split 3 Callouts`, `3_6 Callouts`, `Title + Table`, `Divider`.
  - **Callout text:** heading run **12pt**, detail run **10pt**, `auto_size = NONE`. There is spare room in the boxes, so write *fuller* detail. Add each paragraph with its own `add_paragraph()` — a `\n\n` inside one run renders as an empty bullet.
  - **Build recipe:** load template → drop the example slides (`prs.part.drop_rel(sid.get(qn('r:id')))` **as well as** removing the `sldId`, or the file is corrupt) → `add_slide(layout_by_name(…))` → set text only, so fonts and colours inherit.
  - **Pictures:** read the placeholder's L/T/W/H, remove the placeholder, then `add_picture` scaled by `min(W/iw, H/ih)` centred. `insert_picture` centre-crops and mangles wide charts and tall tables.
  - **A 3:1 chart sits small in a near-square picture box** — stack the chart above its table into one composite image, so the exhibit fills the box *and* carries the numbers beside it.
  - Verify by exporting slides to PNG via PowerPoint COM and reading them. Release the file lock (`Stop-Process POWERPNT`) before rebuilding.
- **PDF** (for non-HTML audiences): render each read-out to PNG, then assemble one PDF per page with **Pillow** (`img.save(pdf, save_all=True, append_images=[…], resolution=200)`) — one deliverable per page so nothing is cut off.
- **A "site"** — an `index.html` landing page (headline + comparison + nav cards) linking the individual pages; a combined "all read-outs" page stacks every card + embeds context charts via `<iframe>`.

## The standard client deck — 11–13 slides

A full consulting study lands as a **deck of roughly 11–13 content slides**, built from the **firm template** (above): cover → "What this covers" → the pages below → Thank You. Each page is one read-out from the model, rendered **HTML → PNG** and dropped into the template's picture box — **set text only, styling inherits.** Run only the pages the job supports.

| # | Slide | Draws from |
|---|---|---|
| 1 | **Executive Overview** — SKUs, orders, lines, units, inventory, utilisation, peak, key opportunities | `04`, `00a` opportunity tree |
| 1a | **Range Profile** — the **range treemap** (count · cube · lines · data quality) and the **box-and-whisker** spread | `03`, `03a` |
| 2 | **Demand Profile** — orders/lines/units trend, day-of-week, hourly heatmap, seasonality, peak vs avg | `05`, `15` |
| 3 | **Order Profile** — lines/order, order-size bands, single- vs multi-line, customer mix | `05` |
| 4 | **SKU Analysis** — ABC, XYZ, velocity, age, Pareto, **velocity treemap** | `05` |
| 5 | **Picking** — picks, lines, picks/hr, pick type, zones, hot locations, pick Pareto | `06` |
| 6 | **Inventory** — quantity, cube, aging, days-of-inventory, slow/non-moving, overstock | `07` |
| 7 | **Storage** — locations, occupancy, utilisation, fragmentation, storage type | `08` |
| 8 | **Warehouse Capacity** — area, cube, pallet/tote positions, utilisation, peak, future | `13`, `08`, `16` |
| 9 | **Slotting** — current vs recommended, hot/cold, travel, replenishment, pick-face | `18` |
| 10 | **Process Performance** — receiving → … → dispatch with volume/capacity/utilisation and the **bottleneck** | `16`, `14` |
| 11 | **Automation** — suitability, candidates, throughput requirement, equipment & fleet sizing | `19` |
| 12 | **Future State** — current vs future: space, labour, throughput, equipment, utilisation | `20` |
| 13 | **Business Case** — CAPEX, OPEX, savings, ROI, payback, sensitivity | `20` |

For a shorter brief, collapse to the audience's question — a storage-only job might be Overview → Demand → Storage → Capacity → Future State (≈5 slides, as WAMAS was). **Always the same template, always model-driven, always verified by rendering the slides and looking at them.**

## Checklist
- [ ] House colours, fonts, footer applied consistently across every output.
- [ ] Number formats clean (`#,##0`, `%`, weights); dates and labels correct.
- [ ] Every image/slide/PDF **verified by rendering and looking at it**, not assumed.
- [ ] Excel formulas recalculated via COM after any programmatic write.

---
> **Worked examples.** *WAMAS:* HTML site + PNGs + a 5-slide deck + a shareable PDF. *Carton-Pallet:* a formatted multi-tab Excel workbook (Version Control, Assumptions, scenarios, comparison) in the Trivium style.

*Trivium Analytics · Warehouse Consulting Analysis Playbook · v0.2 · 2026-08-18 · next [[22_Updating_for_New_Data]]*
