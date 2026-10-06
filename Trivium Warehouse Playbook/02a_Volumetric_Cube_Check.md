# 02a · Volumetric / Cube Data Check — deep data-quality validation

> **What this is.** *(Layer 1.)* The deep audit of the client's physical measurements. It answers two
> separate questions — *is the cube internally consistent?* and *can I rebuild the pack quantity from it?* —
> and ends in a pass / qualified-pass / fail verdict plus a costed re-measurement scope. Run it whenever
> anything downstream depends on volume.

> **Where this sits.** A specialised deep-dive of `02_Load_and_Clean`, run before you model. Reach for it whenever the measurements are **dimensions / cube** and the work downstream leans on that volume — Engine A cubic sizing (`10`), tote/pallet counts, fill %, or a derived pack quantity. It answers two things up front: *is the cube data trustworthy,* and *can I rebuild the pack quantity from it?* Pass its verdict into `03_Understand_the_Data` and `10`.

**Module type:** Data quality validation
**Run when:** Any client supplies item master or inventory data containing physical dimensions, and any downstream work depends on volume - pack quantities, carton or tote counts, pallet building, container fill, storage costing, slotting, or freight.
**Output:** A pass / qualified-pass / fail verdict on the volumetric data, plus a defect list and a re-measurement scope if it fails.
**Typical runtime:** 2-4 hours for a first pass on ~50k SKUs.

---

## 1. Why this check exists

Clients routinely supply a "units per carton", "pack quantity", or "carton qty" field and assume it is trustworthy. It very often is not. It tends to get populated from receipts, replenishment quantities, or minimum order quantities rather than from the physical box.

Meanwhile the dimension fields, which nobody looks at, are usually far more reliable.

**The core move in this module: stop trusting the stated pack quantity, derive it from measured volume instead, then test whether the measurements themselves hold up.**

The check answers two separate questions. Keep them apart.

| Question | What it tests |
|---|---|
| **A. Is the cube data internally consistent?** | Do items that should share a volume actually share one? |
| **B. Does the stated pack quantity agree with the cube?** | Is the client's existing field defensible? |

A can pass while B fails badly. That is the most common outcome, and it is a good result: it means you can rebuild the pack quantity from the cube.

---

## 2. Data you need

### Required

| Field | Notes |
|---|---|
| SKU / item code | Unique per row per snapshot |
| Unit height, width, length | Confirm the unit of measure: m, cm or mm |
| Unit volume | If absent, derive it. If present, verify it equals H x W x L |
| A grouping key | Style, product code, or model. See section 4 |

### Strongly recommended

| Field | Why |
|---|---|
| Net weight | Enables the density cross-check in section 8, the only independent test of the dimensions |
| Size / variant | Explains whether volume differences are legitimate |
| Stock on hand | Lets you weight defects by business impact |
| Existing pack / carton qty | Needed for question B |
| Product group / category | Targets the re-measurement effort |

### Ask the client up front

1. **What is the container capacity, and is it gross or usable?** The single most important input. A 72 L tote might mean 72 L of internal space, or 72 L of usable space after void loss. The difference moves every derived figure by 15-20%.
2. **Is there a weight limit per container?** Manual handling limits are commonly 15-20 kg. A pure volume rule will overfill heavy items.
3. **What unit of measure are the dimensions in?**
4. **Are items packed loose, folded, hanging, or in inner packs?** Hanging garments and inner-packed items do not follow a simple volume division.
5. **Which items are excluded from the standard container by design?** Bulky goods, luggage, furniture.

> Record the answers in the deliverable. If the client cannot answer question 1, state the assumption prominently and give the sensitivity.

---

## 3. Step 1 - Structural validation

Before any volumetric maths, confirm the file is what it claims to be.

- [ ] Row count per file, and total across files
- [ ] Column layout identical across all files in a series
- [ ] Duplicate keys within a single snapshot (should be zero)
- [ ] Null / blank counts per column
- [ ] Check the stored data types in the raw file, not after loading (see section 12)
- [ ] If multiple snapshots: does master data drift between them? Dimensions and weights should be static. Anything that moves is a source-system inconsistency worth reporting.

---

## 4. Step 2 - Choose the grouping key

The consistency test compares items that *should* share a volume. Choosing the key wrong makes the whole analysis wrong.

| Key | Groups together | Use when |
|---|---|---|
| **Style** | All sizes and all colours | Default. Colour does not change the folded size of a garment. |
| **Style + colour** | All sizes of one colourway | The client insists colourways are packed separately |
| **Style + size** | Colours of one size | Rarely useful, too narrow to catch anything |

**Default to style alone.** It is the widest defensible grouping, so it catches the most inconsistency. Confirm the choice with the client and state it in the deliverable, because the numbers change materially between keys.

> **Worked example.** Grouping by style+colour flagged 66% of groups as inconsistent. Regrouping by style alone, and switching the metric to volume-derived, showed 95.4% were actually fine. Same data, completely different story. The key and the metric must both be stated.

---

## 5. Step 3 - Cube integrity

Run these before deriving anything.

| Check | Threshold | Action if it fails |
|---|---|---|
| Volume = H x W x L | within 0.5% | Investigate. The volume field may be stale or independently keyed |
| Volume > 0 | must hold | Cannot derive. Exclude and flag for re-measure |
| Any dimension < 1 cm | flag | Placeholder value, not a real measurement |
| Volume > container capacity | flag | Item does not fit. Needs its own packing rule, not an error |
| Dimensions identical across a whole group | expected | Good sign. This is what correct data looks like |

Record each population separately. They need different remedies and should not be lumped into one "bad data" number.

---

## 6. Step 4 - Derive the standard pack quantity

```
Items Per Container = Container Capacity (m3) / Unit Volume (m3)
```

For a 72 L tote that is `0.072 / unit volume`.

Two derived forms:

- **Raw.** Keep decimals for ratio and spread maths.
- **Whole.** `FLOOR(raw)`, floored at 1. This is the usable operational figure.

**Sanity-check the distribution before going further.** A healthy one is a single broad peak with a long thin right tail. Report the median and interquartile range.

> If the distribution is bimodal or has a fat right tail, you probably have mixed units of measure, some rows in cm and some in m. Check before proceeding.

### Apply a weight cap if net weight is available

```
Final Pack Qty = MIN( FLOOR(capacity / volume), FLOOR(weight limit / unit weight) )
```

Report which constraint binds for each item. A pure volume rule frequently produces containers well above manual handling limits.

---

## 7. Step 5 - The consistency test

For every group with more than one SKU:

```
Spread = MAX(items per container) / MIN(items per container)
```

A group where every item shares one volume returns exactly **1.0**.

### Severity bands

| Band | Spread | Interpretation |
|---|---|---|
| Consistent | 1.0 | One cube throughout. Correct. |
| Minor | up to 2x | May be legitimate, larger sizes are genuinely bulkier |
| Moderate | 2-3x | Suspicious |
| Major | 3-5x | Cannot be physical |
| Severe | 5-10x | Certainly a keying error |
| Critical | 10x and above | Placeholder or decimal-point error |

**Report both share of groups and share of SKUs.** They differ, and the difference is informative. If the SKU share is lower than the group share, the defects sit in small groups rather than the core range.

### Which value is right within a bad group?

**The largest volume is almost always the credible one.** Errors are overwhelmingly decimal slips and placeholders that make items too small. Do not average the group. Take the plausible measurement and apply it across.

### Isolate the actual defective records

Group-level counts overstate the workload. Within each bad group, flag only the individual records that deviate:

```
Off-norm = volume > 3 x group median  OR  volume < (1/3) x group median
```

> **Worked example.** 282 bad styles contained 1,680 SKUs, but only 47 records were actually off-norm. Reporting 1,680 as the re-measurement scope would have overstated the job roughly thirteenfold.

---

## 7a. Step 5b - Cross-check against the client's own picked volume ⭐

Density (section 8) is one independent test. If any **outbound or movement** file carries a volume per
line, you have a **second and usually better one** — the client's own system telling you what it thinks
the item measures, every time it was handled.

```
implied_each_cc = (sum of picked volume m3 x 1,000,000) / (sum of units picked)
```

Compare that against the master each-volume, per SKU:

| Ratio master : implied | Reading |
|---|---|
| 0.8 – 1.25 | Agrees. Master is sound for this SKU. |
| 1.25 – 5 | Drift — packaging void, or a stale record. Note, don't act. |
| **over 5** | **The master is wrong.** Two independent sources disagree by more than 5x. |

- Require **both** independent checks to fail before condemning a record: master vs picked **and**
  master vs outer-derived. One disagreement is a question; two is a verdict.
- Substitute the **picked** cube (or the outer-derived one) for the master where both fail, and
  **publish the substitution rule** so the correction is reproducible.

> **Why this matters more than it looks.** The defective records are usually few but enormous. On one
> engagement **16 SKUs of 62,206** carried an impossible each-cube; three of them alone distorted a whole
> site's peak storage requirement. A density check would not have caught them — their density was
> plausible. Only the client's own picked volume exposed them.

---

## 7b. When there are no dimensions at all ⭐

The module so far assumes measurements exist and asks whether they are good. Very often **most of the
range has no measurements** — and that is a different job, not a failure.

1. **Split the population first.** Report coverage on the SKUs that **matter**, not on the master:
   *total master* · *active (moved in the window)* · *stocked* · *coverage % of each*. A master that is
   32% dimensioned can still be **100% dimensioned on everything that moves** — which is a pass, not a fail.
   State it that way or you will condemn good data.
2. **Weight coverage by the work.** *"98.9% of pick lines resolve to a SKU with a measured cube"* is the
   number that decides whether Engine A can run. Unweighted SKU coverage is close to meaningless.
3. **If the active range is genuinely un-dimensioned**, the honest options in order:
   - **Sample and measure** — a stratified sample by category and velocity, then apply the category median.
     State the sample size and the error you are accepting.
   - **Infer from the outer** — `outer volume / units per outer`, where the outer is measured.
   - **Fall back to Engine C** (`05`) — demand and throughput need **no volumetrics at all**. This is the
     single most useful fact in the module: a client with no measurements is not a client with no study.
4. **Never impute a cube silently.** Any derived or sampled volume is flagged in the model and counted in
   the deliverable.

---

## 8. Step 6 - Density cross-check

**The most valuable check in this module.** Dimensions and weight are captured separately, so an impossible density proves one of them is wrong without needing any other reference.

```
Density = Net Weight (kg) / Unit Volume (m3)
```

| Range kg/m3 | Reading |
|---|---|
| 80 to 500 | Plausible for folded soft goods |
| under 30 | Lighter than packing foam, so volume is overstated |
| over 1000 | Denser than water, so volume is understated |

Adjust the plausible band by product type. Footwear, hardware and liquids sit higher; padded and hollow goods sit lower. Use the **impossible** bounds (30 and 1000) for flagging, not the plausible band, or you will flag a quarter of the range.

**Recommend this as a standing validation rule at item creation.** It catches the same errors before they ever reach an extract.

---

## 9. Step 7 - Is the client's existing pack quantity trustworthy?

Only relevant if such a field exists. Four tests. If it fails three or more, discard the field.

| Test | A real packing figure | A repurposed field |
|---|---|---|
| Correlation with derived standard | near +1 | near 0 |
| Correlation with unit volume | strongly negative | near 0 |
| Implied container fill (qty x volume / capacity) | clusters at 1.0 | scattered, many far below, some above 1.0 |
| Variation across sizes of one group | flat when volume is flat | varies with size popularity |

Two further tells:

- **A ceiling.** Few distinct values and a hard maximum suggests truncation.
- **Tail-size bias.** If the smallest and largest sizes cluster at 1 while mid sizes do not, the field is tracking how sizes *move*, not how they *pack*.

> **Worked example.** Correlation 0.02 with the standard. Only 2.3% of items filled the container. 35.8% of XXS set to 1 against 0.3% of size M on identical volumes. 98% of values at or below 20 when the cube said 68% should exceed 20. Conclusion: an operational quantity mapped into the wrong column. Discarded entirely.

---

## 10. Step 8 - Outlier options

**Never clean silently. Present a costed menu and get written sign-off.**

State plainly: removing an outlier means excluding a suspect measurement from cube analysis and flagging it for re-measurement. It does not delete stock records.

### Candidate rules, costed independently

| Tier | Rule | Catches |
|---|---|---|
| 1 | Volume <= 0 | Broken records |
| 1 | Any dimension < 1 cm | Placeholders |
| 1 | Volume > container capacity | Genuinely oversized. Separate rule, not an error |
| 2 | Implies more than 200 items per container | Physically implausible |
| 2 | Density outside 30-1000 kg/m3 | Impossible density |
| 3 | Robust z-score on log volume, absolute z > 3.5 | Statistical outliers |
| 3 | Volume more than 3x or less than 1/3 of group median | The odd one out in its own group |

Use a **robust** z-score built on median and MAD, applied to log volume. A plain mean/SD z-score is dragged by the very outliers you are hunting. Log-transform first, because volume distributions are heavily right-skewed.

**Rules to cost but not recommend:** a strict density band (over-removes), and a plain IQR fence on volume (removes legitimate large products).

### Present as cumulative tiers

For each tier report SKUs excluded, percentage of range, units affected, and **the effect on the headline defect metric**. That last column is what makes the decision. A tier that removes 7% of SKUs and cuts the worst spread from 46x to 8x is obviously worth it; one that removes 23% and barely moves the needle is not.

---

## 10a. Prioritise the re-measurement list ⭐

A defect list of 20,000 SKUs gets ignored. A list of 200 gets measured. **Rank by business impact, not by
severity**, and hand over a list the client can actually action:

```
impact = stock cube affected  x  pick lines affected
```

- Sort descending, and cut where the cumulative impact curve flattens — usually a few hundred SKUs.
- Show the **cumulative prize**: *"re-measuring the top 180 SKUs corrects 94% of the affected cube."*
- Give them **the fields to measure** (L, W, H, units per outer, weight) and the **format to return them in**.
- Never hand over an unranked defect dump. Ranking is the difference between a finding and a chore.

---

## 11. Step 9 - Verify before delivering

**Do not skip this.** Every figure that reaches a client should be reproduced by a route independent of the one that produced it.

- [ ] Read the raw file with a second library, bypassing your analysis pipeline entirely
- [ ] Recompute every headline number and compare programmatically, not by eye
- [ ] Reproduce the same figures in the BI tool with a filter matching the analysis scope
- [ ] Hand-check 2-3 individual records end to end, including the arithmetic
- [ ] Confirm every table's parts sum to its stated total
- [ ] Re-read every calculated field definition line by line
- [ ] Confirm the export row count matches the BI tool under the same filter

Build the comparison as a **pass/fail script**, not a visual scan. A tolerance-checked comparison catches things reading never will.

> **Worked example.** A verification pass over 103 reported figures returned 97 pass and 6 fail. Five were a bin-boundary convention. One was a genuine denominator error that had gone unnoticed through three revisions.

Ship the verification table as a tab in the deliverable. It is cheap, and it is the difference between a client trusting the work and re-checking it.

---

## 12. Pitfalls

**Numbers stored as text.** Extracts frequently store IDs and quantities as text. Loaders coerce silently, so you never notice, until the client opens the raw file, runs `=SUM()` and gets 0. Check stored types in the raw file and warn them. Give them `=SUMPRODUCT(--range)`.

**Denominator drift.** When quoting "X% of SKUs", is that of all SKUs, or only those with a value? Both are defensible. Mixing them across a document is not. Write the denominator into the label.

**Bin boundary conventions.** Most binning functions are upper-bound inclusive; readers assume the opposite. State the convention next to any histogram.

**Non-additive columns in BI tools.** Ratios, spreads and per-unit figures must never be summed. A spread of 46x repeated across 5 rows totals a meaningless 232x. Set them to "Don't summarize" or "Max", or switch totals off.

**Grouping context after appending snapshots.** If you append daily files into one table, every group-level calculation must include the snapshot date in its grouping, or it will span all dates. Symptom: counts roughly N times too large.

**Superseded fields left in the model.** When an approach is replaced, move the old calculations to a clearly named folder or delete them. Two similarly named measures returning 63% and 4.6% is a serious client-facing hazard.

**Regional number formatting.** Confirm the locale matches the client's, not yours. Lakh grouping in a UK deliverable reads as an error.

**Hardcoded source paths.** Parameterise the folder path before handing over a BI file, or refresh fails on any other machine.

---

## 13. The verdict

Close with a plain statement. Suggested wording:

**Pass**

> The volumetric data is sound. X% of multi-item groups carry a single consistent cube. Volume reconciles to dimensions on N of M records. A pack quantity derived from [capacity] is safe to adopt across N items.

**Qualified pass**

> The volumetric data is broadly sound but carries a defect tail. X% of groups are consistent. N groups covering M items contradict themselves, of which K individual records are actually defective. The derived standard is safe for the remaining N items. Recommend re-measuring the K flagged records and adopting the standard now for the rest.

**Fail**

> The volumetric data cannot support a derived pack quantity. X% of groups are inconsistent and N records carry placeholder or impossible measurements. Recommend a physical re-measurement exercise before any cube-based planning. Priority: [groups or categories].

Always state alongside the verdict:

- The container capacity used, and whether gross or usable
- The grouping key
- Whether any cleaning was applied, and if not, say so explicitly
- Any threshold set from the data rather than from a client standard

---

## 14. Deliverables checklist

- [ ] Findings report: verdict, defect scale, worst offenders, concentration by category
- [ ] Both share-of-groups and share-of-SKUs wherever a proportion is quoted
- [ ] Re-measurement export, filtered to actionable severity, with the off-norm flag
- [ ] Verification tab showing the independent cross-check
- [ ] Cleaning options costed, with a recommendation, explicitly not applied
- [ ] Assumptions and open questions stated on the front page
- [ ] BI model: derived columns, severity bands, outlier flag, superseded fields quarantined

---

## 15. Reference code

### Adapt these first

The code below uses placeholder names. Map them to the client's actual columns before running.

| Placeholder | Meaning |
|---|---|
| `volume` | Unit volume in cubic metres |
| `height`, `width`, `length` | Unit dimensions in metres |
| `weight` | Unit net weight in kg |
| `GROUP_KEY` | The grouping column chosen in section 4 |
| `CAPACITY` | Container capacity in cubic metres |
| `WEIGHT_CAP` | Container weight limit in kg, or `None` |
| `'Items'` | The fact table name in the BI model |
| `SnapshotDate` | Only if the table holds multiple extracts. Remove if not |

### Python

```python
import numpy as np
import pandas as pd

CAPACITY = 0.072       # m3 - CONFIRM gross vs usable with the client
WEIGHT_CAP = 15.0      # kg - set to None to disable
GROUP_KEY = "style"

# derived fields
df["items_per_container"] = np.where(df["volume"] > 0, CAPACITY / df["volume"], np.nan)
df["density"] = np.where(df["volume"] > 0, df["weight"] / df["volume"], np.nan)
df["min_dim"] = df[["height", "width", "length"]].min(axis=1)

# integrity: does the stated volume match the dimensions?
df["vol_matches_dims"] = (
    (df["height"] * df["width"] * df["length"] - df["volume"]).abs()
    <= 1e-6 + 0.005 * df["volume"].abs()
)

# consistency within group
grp = df.groupby(GROUP_KEY)["items_per_container"]
df["group_spread"] = grp.transform("max") / grp.transform("min")
df["group_size"] = df.groupby(GROUP_KEY)["volume"].transform("size")

bands = [0, 1.0001, 2, 3, 5, 10, np.inf]
labels = ["Consistent", "Minor", "Moderate", "Major", "Severe", "Critical"]
df["severity"] = pd.cut(df["group_spread"], bins=bands, labels=labels, right=True)

# the records actually needing re-measurement
med = df.groupby(GROUP_KEY)["volume"].transform("median")
df["off_norm"] = (df["volume"] / med > 3) | (df["volume"] / med < 1 / 3)

# robust z-score on log volume - median and MAD, NOT mean and SD
lv = np.log10(df["volume"].where(df["volume"] > 0))
centre = lv.median()
mad = (lv - centre).abs().median()
df["robust_z"] = 0.6745 * (lv - centre) / mad

# dual-constraint pack quantity
vol_cap = np.floor(CAPACITY / df["volume"]).clip(lower=1)
if WEIGHT_CAP:
    wt_cap = np.floor(WEIGHT_CAP / df["weight"]).clip(lower=1)
    df["pack_qty"] = np.minimum(vol_cap, wt_cap)
    df["binding_constraint"] = np.where(wt_cap < vol_cap, "Weight", "Volume")
else:
    df["pack_qty"] = vol_cap
```

### DAX calculated columns

Drop `'Items'[SnapshotDate]` from every `ALLEXCEPT` if the table holds a single extract.

```
Items Per Container =
DIVIDE ( 0.072, 'Items'[Volume] )
```

```
Pack Qty =
VAR s = DIVIDE ( 0.072, 'Items'[Volume] )
RETURN
    IF ( ISBLANK ( s ), BLANK (), MAX ( 1, INT ( s ) ) )
```

```
Density =
DIVIDE ( 'Items'[Weight], 'Items'[Volume] )
```

```
Min Dimension =
MIN ( 'Items'[Height], MIN ( 'Items'[Width], 'Items'[Length] ) )
```

```
Group Spread =
VAR mn =
    CALCULATE (
        MIN ( 'Items'[Items Per Container] ),
        ALLEXCEPT ( 'Items', 'Items'[GroupKey], 'Items'[SnapshotDate] )
    )
VAR mx =
    CALCULATE (
        MAX ( 'Items'[Items Per Container] ),
        ALLEXCEPT ( 'Items', 'Items'[GroupKey], 'Items'[SnapshotDate] )
    )
RETURN
    DIVIDE ( mx, mn )
```

```
Severity Band =
VAR k =
    CALCULATE (
        COUNTROWS ( 'Items' ),
        ALLEXCEPT ( 'Items', 'Items'[GroupKey], 'Items'[SnapshotDate] )
    )
VAR r = 'Items'[Group Spread]
RETURN
    SWITCH (
        TRUE (),
        k = 1,         "Single item group",
        ISBLANK ( r ), "No cube",
        r <= 1,        "0. Consistent",
        r <= 2,        "1. Minor (up to 2x)",
        r <= 3,        "2. Moderate (2-3x)",
        r <= 5,        "3. Major (3-5x)",
        r <= 10,       "4. Severe (5-10x)",
                       "5. Critical (10x+)"
    )
```

```
Outlier Tier =
VAR v = 'Items'[Volume]
VAR ipc = DIVIDE ( 0.072, v )
VAR dens = DIVIDE ( 'Items'[Weight], v )
VAR sm =
    CALCULATE (
        MEDIAN ( 'Items'[Volume] ),
        ALLEXCEPT ( 'Items', 'Items'[GroupKey], 'Items'[SnapshotDate] )
    )
VAR dev = DIVIDE ( v, sm )
RETURN
    SWITCH (
        TRUE (),
        v <= 0,                             "Tier 1 - broken record",
        'Items'[Min Dimension] < 0.01,      "Tier 1 - broken record",
        v > 0.072,                          "Tier 1 - broken record",
        ipc > 200,                          "Tier 2 - implausible",
        NOT ( dens >= 30 && dens <= 1000 ), "Tier 2 - implausible",
        dev > 3 || dev < 0.3333,            "Tier 3 - statistical",
                                            "Keep"
    )
```

### Power Query - stack a folder of dated snapshots

Only needed when the client supplies a series of dated files rather than one extract.

```
let
    Source = Folder.Files( SourceFolderParameter ),
    Files = Table.SelectRows(
        Source,
        each Text.Lower( [Extension] ) = ".xlsx"
            and not Text.StartsWith( [Name], "~$" )
    ),
    // find the 8-digit ddMMyyyy token; skip files without one
    WithToken = Table.AddColumn(
        Files,
        "DateToken",
        each List.First(
            List.Select(
                Text.Split( Text.BeforeDelimiter( [Name], ".", {0, RelativePosition.FromEnd} ), " " ),
                each Text.Length( _ ) = 8 and Text.Select( _, {"0".."9"} ) = _
            ),
            null
        ),
        type nullable text
    ),
    Valid = Table.SelectRows( WithToken, each [DateToken] <> null ),
    WithDate = Table.AddColumn(
        Valid,
        "Snapshot Date",
        each #date(
            Number.FromText( Text.End( [DateToken], 4 ) ),
            Number.FromText( Text.Middle( [DateToken], 2, 2 ) ),
            Number.FromText( Text.Start( [DateToken], 2 ) )
        ),
        type date
    )
in
    WithDate
```

---

## 16. Quick reference

| Parameter | Default | Confirm with client |
|---|---|---|
| Container capacity | none | **Always.** Gross or usable? |
| Weight cap | 15 kg | Their manual handling limit |
| Grouping key | Style | How they define a product |
| Implausibility ceiling | 200 per container | Sense-check against their range |
| Density bounds, impossible | 30 and 1000 kg/m3 | Adjust for product type |
| Density band, plausible | 80 and 500 kg/m3 | Reporting only, not flagging |
| Placeholder dimension | under 1 cm | - |
| Off-norm deviation | 3x group median | - |
| Robust z threshold | absolute z > 3.5 | - |
| Actionable severity | 3x spread and above | - |

> All defaults above were derived from a single apparel dataset. Revisit them after two or three more engagements to see which are universal and which need setting per client.
