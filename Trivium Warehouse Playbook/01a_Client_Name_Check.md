# 01a · Client-Name Check — scrub it before you analyse it

> **What this is.** The **intake gate**. The moment client data lands, decide whether this job is **named** or **anonymised** — and if it is anonymised, strip every identifier **before** a single number is calculated. Anonymising at the end is retrofitting; anonymising at intake is design.

> **How to use it.** Work it in order, once, on day one. It takes an hour at intake and saves a week at delivery. Its twin is `24a`, the pre-ship gate — that one re-checks the finished artifacts; this one makes sure there was never anything to find.

> **Why it sits here and not at the end.** Every downstream artifact inherits whatever you loaded. If the client's name is in the model, it is in the measure names, the slicer values, the chart titles, the file names, the screenshots and the deck — and each of those is a separate place to miss it. Scrub the *source*, and all of it is clean by construction.

---

## Inputs

- The raw client files, exactly as received.
- The brief from `01` — who will see the deliverable, and outside which walls.
- The client's own confidentiality / NDA position, if there is one.

---

## 1 · Decide the mode — first, once, in writing

- **Named.** The client sees their own name; leave it in. Still run section 5 to catch a *different* client's name carried over from a template.
- **Anonymised.** The name and every identifier is replaced with an agreed **placeholder** — a neutral token (`XX`, `the client`, or a codename). Set it once and use it everywhere.

Write the mode and the placeholder on the brief (`01`). **If you are unsure, assume anonymised** — it is trivial to put a name back and expensive to take one out.

> Default to anonymised whenever the work may be reused as a template, shown to another client, used as a case study, or shared outside the immediate team.

---

## 2 · What counts as an identifier

Not just the company name — anything that fingerprints the client:

- Company, brand, trading and **own-label product-line** names
- Site, depot, city or address names unique to them
- Their WMS / system name, internal codes, department codes, file-naming conventions
- Staff names — often hiding in a "buyer", "controller" or "owner" column
- Customers, suppliers, consignees and carriers they trade with
- **Distinctive data values** — real SKU descriptions, MPNs, batch references, store names

Write the full list down before you start. **You can only find what you are looking for.**

---

## 3 · Scrub at load, not after

Do the replacement **as the data is loaded**, so nothing downstream ever sees the original:

| Real thing | Replace with |
|---|---|
| Item / product codes | `SKU-000001`, `ALT-00001` |
| Store / site / depot | `STORE-00001`, `DC-1`, `3PL-1` |
| Internal system or zone codes | `AREA-A` … `AREA-F` |
| Suppliers, customers, consignees, carriers | `SUP-0001`, `CUST-001`, `CONS-001`, `PROV-01` |
| Brands and brand streams | `BRAND-01`, `STREAM-A` |
| Staff names | `CTRL-001` |
| Geographic regions | `REGION-001` |
| Free-text descriptions | **drop the column**; keep the category |

**Keep a crosswalk** mapping every surrogate back to the real value — you will need it to answer client questions and to re-run on refreshed data.

> ⚠ **The crosswalk must live outside the output folder.** Put it somewhere working-only (`_work\crosswalk\`), never in the folder you ship from. It is the one file that undoes the whole exercise.

**Rename the artifacts too.** Table names, column names, file names and the model/database name are all identifier hiding places — a column called `<Client> Item Code` leaks just as surely as a data value.

---

## 4 · What to keep

Anonymising is not deleting. Keep everything the analysis needs:

- Quantities, dates, volumes, weights, dimensions — untouched
- Category, sub-category, hazard / temperature / licence flags — these carry the operational meaning
- The **relationships** between surrogates, so joins still work across files
- Generic geography (country, region type) where it is analytically useful and not identifying on its own

If scrubbing a field would destroy the analysis, say so and negotiate — do not quietly keep the real value.

**The three fields where scrubbing genuinely costs analysis** — decide these deliberately:

| Field | What surrogating it destroys | Usual answer |
|---|---|---|
| **Store / site identifiers** | Any geographic clustering, regional pattern, distance-to-DC work | Surrogate the ID but **keep a non-identifying region or distance band** |
| **Product description** | Category inference where the category field is poor or missing | Drop the text, but **derive the category first** and keep that |
| **Date granularity** | Nothing — never blur dates | Keep dates exact; they are not identifying on their own |

**Who holds the crosswalk.** Name an owner on the brief. It must survive the analyst leaving and the job
being picked up months later — store it with the project, not in someone's personal folder, and note in
the deliverable that it exists and where. A crosswalk nobody can find makes a refresh impossible.

---

## 5 · Verify — search, don't eyeball

- Search **every** identifier from section 2 across every output file, not just the company name.
- **Check data values, not just headers.** A clean column header can sit above a column full of the client's own brand abbreviation.
- **Beware compressed files.** Grepping a Parquet or zipped file for a short string produces false positives from the compressed bytes. Verify by **decoding the file and scanning the distinct values of every text column**, not by grepping the binary.
- Check file names, table names, column names, and document properties (Author, Title, Company).
- Record the result: *"N identifier terms searched across the whole output folder — zero hits."*

---

## Outputs

- The anonymisation **mode and placeholder**, recorded on the brief.
- The **identifier list** for this client.
- A loaded dataset that is clean **at source**, with surrogate keys and working joins.
- A **crosswalk**, stored outside the shipping folder.
- A recorded **verification result**.

---

## Checklist before you analyse anything

- [ ] Mode decided (named / anonymised) and placeholder agreed, written on the brief.
- [ ] Identifier list written — names, brands, sites, systems, staff, trading partners, descriptions.
- [ ] Scrub applied **at load**, so no downstream step ever sees the original values.
- [ ] Surrogate keys preserve every join the analysis needs.
- [ ] Table, column, file and model names carry no identifier.
- [ ] Crosswalk exists and is **outside** the output folder.
- [ ] A search for every identifier across the outputs returns **zero hits** — verified on decoded values, not on compressed bytes.
- [ ] Result recorded, ready to be re-run at `24a` before shipping.

---

> **Worked example — Project Bombay.** Anonymised from intake, placeholder `XX`. 769,150 item codes, 3,244 stores, 21 sites, 3,840 trading partners, 8 brands, 28 staff names and 121 regions were replaced with surrogates at load; free-text SKU descriptions were dropped entirely. Two traps showed up at verification, both worth remembering:
> **(1)** a brand-stream column passed a header review but carried the client's own three-character brand abbreviation as a *data value* — headers were clean, values were not;
> **(2)** grepping the compressed Parquet exports reported 122 hits that did not exist — they were byte sequences inside the compression. Decoding and scanning the distinct values of every text column returned the true answer: **zero**.

*Trivium Analytics · Warehouse Consulting Analysis Playbook · 01a · v0.1 · 2026-08-20 · next [[02_Load_and_Clean]] · pre-ship twin [[24a_Client_Name_and_Confidentiality_Check]]*
