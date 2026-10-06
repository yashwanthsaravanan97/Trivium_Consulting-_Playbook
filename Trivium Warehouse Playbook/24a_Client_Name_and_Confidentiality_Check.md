# 24a · Client-Name & Confidentiality Check — the anonymisation gate

> **This is the second gate, not the first.** The scrub itself belongs at intake — see `01a`, which strips identifiers *as the data loads*. This module re-checks the finished artifacts before they ship. If `01a` was done properly, this pass should find nothing.

> **Where this sits.** A pre-ship gate, run with `12_Recheck` before anything leaves the building. Decide once per project whether the deliverable is **named** (the client sees their own name) or **anonymised** (their name must not appear — because it will be shown outside the client, reused as a template / case study, or the client requires it). If anonymised, nothing that identifies them may survive in any artifact.

**Module type:** Pre-delivery confidentiality gate
**Run when:** Before sharing any deliverable externally, whenever the client's name must not appear — anonymised packs, template / reference reuse, cross-client sharing, or an explicit NDA / confidentiality requirement.
**Output:** A clean deliverable with every identifier removed or replaced with the agreed placeholder, plus a ticked-off sign-off checklist.

---

## 1. Decide the mode — first, once

- **Named.** The client sees their own name; leave it in. Still run section 5 to catch a *different* client's name accidentally carried over from a template or a previous job.
- **Anonymised.** The client name and every identifier must be replaced with the agreed **placeholder** — a neutral token (`XX`, `the client`, or an agreed codename). Set it once and use it everywhere.

Write the mode and the placeholder on the project brief (`01`). If unsure, ask the client before you build — retrofitting anonymisation across a finished pack is far more error-prone than starting with it.

---

## 2. What counts as an identifier

Not just the company name — anything that fingerprints the client:

- Company / brand / trading names, and their well-known product-line names
- Site, depot, city or address names unique to them
- Their WMS / system name, internal codes, or file-naming conventions
- Logos, colour schemes, and staff names
- Distinctive data values — real SKU descriptions, MPNs, supplier or brand names sitting *inside* the data

Write the full list down before you start searching. You can only find what you're looking for.

---

## 3. Where the name hides — check every one

Client names leak into places nobody looks. For **each artifact** (PBIX, HTML, PDF, Excel, PPT, images), check:

| Artifact | Hiding places |
|---|---|
| **File names** | `Farnell_Option3.pbix`, `WAMAS_…` — the filename itself often carries it |
| **Power BI** | model / database name, table, column & measure names, a **Version Control** table, slicer values, and the data itself |
| **HTML** | `<title>`, headers, footers, chart titles, the source / notes line, image alt text |
| **Excel** | tab / sheet names, the title row, a Version Control tab, cell values, headers / footers, document **properties** (Author, Company) |
| **PPT** | slide titles, footers, the slide **master**, speaker notes, file properties |
| **PDF** | the rendered text **and** the document **Title / Author metadata** |
| **Images** | any client text baked into the picture |

---

## 4. How to check — search, don't eyeball

- Keep the identifier list from section 2 to hand, and search each artifact for **every** entry — not just the company name.
- Find / Replace in Excel & PPT; text / `<title>` search in HTML; the model search in Power BI; text search in the PDF.
- For HTML and other text outputs, a **grep across the whole output folder** catches every occurrence in one pass.
- Check **document properties / metadata** explicitly — Author, Title, Company. It is the single most-missed spot.
- Don't forget the **file name** and any **tab / table names** — searching cell or body text alone won't surface them.

---

## 5. Replace consistently

- Swap every hit for the agreed placeholder — the **same** token everywhere (mixing `XX` and `the client` looks sloppy and reads as a miss).
- Rename files and Power BI tables / columns that embed the name.
- Clear the document properties.
- Where the project config carries a `CLIENT_NAME` value, set it **before** you export and everything downstream picks it up automatically.

---

## 6. Checklist — tick before it ships

- [ ] Mode decided (named / anonymised) and placeholder agreed, recorded on the brief (`01`)
- [ ] Identifier list written (name, brands, sites, systems, staff)
- [ ] Every file **name** checked and cleaned
- [ ] Power BI: model name, table / column / measure names, Version Control, slicer values, the data
- [ ] Every HTML: title, headers, footers, source line, alt text
- [ ] Every Excel: tabs, title rows, Version Control, cells, document properties
- [ ] Every PPT: titles, footers, master, notes, properties
- [ ] Every PDF: body text **and** Title / Author metadata
- [ ] Images: no client text baked in
- [ ] A final search for **each** identifier returns **zero** hits across the whole output folder
- [ ] Sign-off recorded

> Run this the moment before delivery, together with `12_Recheck`. One leaked name — in a footer, a file name, or a document property — undoes the whole anonymisation.

---
*Trivium Analytics · Warehouse Consulting Analysis Playbook · module 12a · 2026-08-13*
