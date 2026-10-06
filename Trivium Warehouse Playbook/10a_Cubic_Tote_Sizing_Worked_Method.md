# 10a · Cubic-Volume Tote Sizing — 60% fill, caps 5 / 20 / Pallet

> **What this is.** The detailed spec of the **cubic tote-sizing** method: turn each SKU's stock into a **cube (m³)**, fill totes to **60%** of capacity, size to the **smallest tote that works**, and **cap** how many of each size a SKU may take before it escalates to the next size up. It's the worked engine behind Engine A (`10_Sizing_Cubic`).

> **When to use it.** Any storage-sizing job where you have a per-SKU **stock cube** (or box dimensions to derive it) and need a tote/pallet **footprint** — locations per SKU, rolled up to the warehouse.

> **How this relates to `10`.** Module `10` is the *method* — the steps, the two derived measures, the calibration discipline. This is a **complete worked implementation** of it from a real client, with the ladder, the fill and the caps all pinned to actual values and the DAX written out. Use `10` to understand what you are doing; use this to build it fast. **The parameters here are that client's, not universal** — a different job re-calibrates them (see `10` "Calibrate, then trust").

---

## The tote ladder (usable capacities)

| Tote | Capacity (m³) | Capacity (L) |
|---|---|---|
| 1/16 (half-height) | 0.0012775 | 1.28 |
| 1/8 | 0.00511 | 5.11 |
| 1/4 | 0.01022 | 10.22 |
| 1/2 | 0.02044 | 20.44 |
| Full | 0.04088 | 40.88 |
| Full Big | 0.12768 | 127.68 |
| Pallet | 1.44 | 1,440 |

---

## The parameters

- **Fill factor = 60%** — a tote only ever counts as holding 60% of its geometric capacity (void loss, honeycombing, pick access). So usable = capacity × 0.6.
- **The caps** — the maximum number of a tote size one SKU may occupy before it escalates to the next size up:

| Tote size | Cap | If it would exceed the cap → |
|---|---|---|
| 1/16 · 1/8 · 1/4 · 1/2 | **5** | escalate up through the small ladder, then to **Full** |
| Full | **20** | escalate to **Full Big** |
| Full Big | **20** | escalate to **Pallet** |
| Pallet | **uncapped** | stays Pallet (any number) |

The caps stop a SKU sprawling across dozens of tiny totes — past 5 smalls it's cheaper in one bigger tote; past 20 Fulls it belongs on a pallet.

---

## The method

1. **Get the stock cube** per SKU — `stock_cube = stock_qty ÷ pack_qty × box_volume`, or a recorded m³. (Derive box volume = W × L × H where missing; validate the cube first — see `02a`.)
2. **Box-fit floor.** The tote can't be smaller than the SKU's carton — find the smallest tote whose **full** capacity holds one box (`boxIdx`). A SKU never sizes below this.
3. **Cube-fit at the 5-cap.** Find the smallest of the four small totes (1/16 → 1/2) that holds the stock in **≤ 5 totes at 60% fill**; if none do, it's at least **Full**. *(Test `stock_cube ÷ (5 × 0.6) = stock_cube ÷ 3.0` against each small capacity.)*
4. **The 20-cap escalation.** For Full and Full Big, allow up to **20** totes at 60%; over that, step up (Full → Full Big → Pallet). *(Test `stock_cube ÷ (20 × 0.6) = stock_cube ÷ 12` against Full then Full Big; over Full Big → Pallet.)*
5. **Chosen size** = the larger of the box-fit floor and the cube-fit size, then the 20-cap escalation.
6. **Count the totes.** `locations = ROUNDUP( stock_cube ÷ (chosen_capacity × 0.6) , 0 )`.

---

## The DAX (one calculated column per SKU, or split as you like)

```DAX
VAR c16 = 0.0012775  VAR c8 = 0.00511  VAR c4 = 0.01022  VAR c2 = 0.02044
VAR c1  = 0.04088    VAR cFB = 0.12768 VAR cP = 1.44
VAR fill = 0.6
VAR bv = 'T'[Box_W_mm] * 'T'[Box_L_mm] * 'T'[Box_H_mm] / 1000000000   -- one box, m³
VAR tc = 'T'[Stock_Cube_M3]                                           -- stock cube, m³

-- box-fit floor: smallest tote whose FULL capacity holds one box
VAR boxIdx = SWITCH ( TRUE(),
    bv <= c16,1, bv <= c8,2, bv <= c4,3, bv <= c2,4, bv <= c1,5, bv <= cFB,6, bv <= cP,7, 8 )

-- cube-fit with the 5-cap on small totes (÷3.0 = ÷(5 × 0.6)); caps at Full = 5
VAR sizeIdx = SWITCH ( TRUE(),
    DIVIDE(tc,3.0) <= c16,1, DIVIDE(tc,3.0) <= c8,2, DIVIDE(tc,3.0) <= c4,3, DIVIDE(tc,3.0) <= c2,4, 5 )

-- 20-cap escalation for Full / Full Big (÷12 = ÷(20 × 0.6)); over Full Big → Pallet
VAR cap20 = SWITCH ( TRUE(),
    DIVIDE(tc,12) <= c1,5, DIVIDE(tc,12) <= cFB,6, 7 )

VAR bb  = IF ( boxIdx > sizeIdx, boxIdx, sizeIdx )
VAR idx = IF ( bb >= 5, IF ( bb > cap20, bb, cap20 ), bb )

VAR cap = SWITCH ( idx, 1,c16, 2,c8, 3,c4, 4,c2, 5,c1, 6,cFB, 7,cP, BLANK() )
VAR ToteSize  = SWITCH ( idx, 1,"1/16", 2,"1/8", 3,"1/4", 4,"1/2", 5,"Full", 6,"Full Big", 7,"Pallet", "Oversized" )
VAR Locations = IF ( idx = 8, BLANK(), ROUNDUP ( DIVIDE ( tc, cap * fill ), 0 ) )
RETURN Locations   -- (return ToteSize / cap in sibling columns as needed)
```

- `idx = 8` = **Oversized** (box bigger than a pallet) — flag it, don't size it.
- Key constants to remember: **÷3.0 is the 5-cap on smalls (5 × 0.6)**, **÷12 is the 20-cap on Full/Full Big (20 × 0.6)**, and **× 0.6** is the fill on the final count.

---

## Full-tote equivalents (optional roll-up)

To express the footprint as "full totes," convert by the tote-name fraction, **empty-adjusted** (some locations sit empty): 1/16 ÷ 14 · 1/8 ÷ 7 · 1/4 ÷ 3.96 · 1/2 ÷ 1.98 · Full × 1 · **Full Big & Pallet shown as "—"** (not converted).

---

## Worked examples

| SKU stock cube | Small-tote test (÷3.0) | 20-cap test (÷12) | Chosen size | Locations = ⌈cube ÷ (cap×0.6)⌉ |
|---|---|---|---|---|
| 0.015 m³ | 0.005 → smallest that fits is **1/8** (≤ 0.00511) | — | **1/8** | ⌈0.015 ÷ 0.003066⌉ = **5** |
| 0.30 m³ | 0.10 → over 1/2 → Full | 0.025 ≤ 0.04088 → stays Full | **Full** | ⌈0.30 ÷ 0.02453⌉ = **13** |
| 1.00 m³ | Full | 0.083 → over Full, ≤ Full Big → **Full Big** | **Full Big** | ⌈1.00 ÷ 0.07661⌉ = **14** |
| 5.00 m³ | Full | 0.417 → over Full Big → **Pallet** | **Pallet** | ⌈5.00 ÷ 0.864⌉ = **6** |

*(At 1.0 m³, plain Full would need 41 totes — over the 20-cap — so it escalates to Full Big at 14. That's the cap doing its job.)*

> ⚠ **Row 1 is the one people get wrong.** The test is *smallest tote that works*, and the DAX
> `SWITCH` takes the **first** branch that passes — so 0.005 is tested against 1/16, then 1/8, and
> stops there. It is tempting to eyeball 0.005 ≤ 0.02044 and answer "1/2", but 1/8 also holds it
> (in exactly 5 totes, right on the cap) and 1/8 is smaller. **Always walk the ladder from the
> bottom.** Answering 1/2 here would under-count locations by more than half and quietly buy the
> wrong tote.
>
> Sense-check your implementation against all four rows before you run it on a client's data. If
> row 1 returns 1/2 × 2 instead of 1/8 × 5, the ladder is being tested top-down.

---

## Checklist

- [ ] Stock cube derived from **validated** dimensions (not a trusted pack-qty field).
- [ ] **60% fill** applied to the count, but the **box-fit floor** uses full capacity (a carton must physically fit).
- [ ] Small totes capped at **5**, Full / Full Big at **20**, Pallet **uncapped** — and escalation tested, not assumed.
- [ ] **Oversized** (box > pallet) flagged, not silently sized.
- [ ] Locations **reconcile** to the whole warehouse (Σ locations, Σ cube, Σ units).
- [ ] The fill % and the caps were **calibrated to this client's actuals** before any projection was run (`10`).

---
*Source: a client whole-warehouse tote model (anonymised) — box-cube sizing, 60% fill, caps 5 / 20 / Pallet.*

*Trivium Analytics · Warehouse Consulting Analysis Playbook · 10a · v0.1 · 2026-08-20 · implements [[10_Sizing_Cubic]] · validate the cube first in [[02a_Volumetric_Cube_Check]] · bands in [[12_Bucketing]]*
