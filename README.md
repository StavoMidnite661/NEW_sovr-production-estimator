# SOVR Build OS — Estimating Workbench

**Production estimating workbench v6.0** — a single-file, offline-capable construction estimating aid that moves a project from a concept allowance to a traceable, quantity-based estimate while keeping prices, sources, scope decisions, revisions, and quality checks in one place.

[![Live demo](https://img.shields.io/badge/live%20demo-sovr--estimator.vercel.app-11766e)](https://sovr-estimator.vercel.app)
![single file](https://img.shields.io/badge/build-none%20required-brightgreen)
![dependencies](https://img.shields.io/badge/dependencies-0-blue)
![network calls](https://img.shields.io/badge/network%20calls-0%20offline-8a8a8a)
![offline](https://img.shields.io/badge/offline-capable-brightgreen)

**Live:** <https://sovr-estimator.vercel.app>
**Baseline rate library:** Kern County / Bakersfield, California

---

## Table of contents

- [What this is](#what-this-is)
- [What this is not](#what-this-is-not)
- [What's new in v6.0](#whats-new-in-v60)
- [Quick start](#quick-start)
- [Feature tour](#feature-tour)
- [The two pricing modes](#the-two-pricing-modes)
- [Calculation reference](#calculation-reference)
- [Concept allowance model](#concept-allowance-model)
- [Detailed unit-price model](#detailed-unit-price-model)
- [Kern County rate library](#kern-county-rate-library)
- [Regional presets](#regional-presets)
- [Cost book](#cost-book)
- [Automated preflight checks](#automated-preflight-checks)
- [Takeoffs and coordination schedules](#takeoffs-and-coordination-schedules)
- [Preliminary timeline](#preliminary-timeline)
- [Proposal / RFQ worksheet](#proposal--rfq-worksheet)
- [Revisions and snapshots](#revisions-and-snapshots)
- [Project templates](#project-templates)
- [Input reference](#input-reference)
- [Data, storage, and privacy](#data-storage-and-privacy)
- [Import and export formats](#import-and-export-formats)
- [Project file schema](#project-file-schema)
- [Printing and PDF](#printing-and-pdf)
- [Accessibility](#accessibility)
- [Technical architecture](#technical-architecture)
- [Repository layout](#repository-layout)
- [Deployment](#deployment)
- [Extending the tool](#extending-the-tool)
- [Calibration notes](#calibration-notes)
- [Limitations](#limitations)
- [Disclaimer](#disclaimer)
- [License](#license)

---

## What this is

A **field estimating workbench** for residential and light-commercial work — ADUs, garage conversions, home additions, kitchen and bathroom remodels, and whole-home renovations.

It is built around one idea: **an estimate is only as good as the quantities and the provenance of its rates.** So the tool never hides a number. Every division, credit, adder, and total is shown as a visible roll-up, every rate can carry a source and an as-of date, and a panel of automated checks reports what is still unverified — while refusing to ever claim the estimate is approved.

Everything runs in the browser. There is no backend, no account, no telemetry, and no network traffic of any kind.

## What this is not

| Not this | Why |
| --- | --- |
| A bid, quote, or guaranteed price | No prices are contracted, negotiated, or committed |
| A permit set or code determination | No code compliance is evaluated or certified |
| An engineering document | No load calculations, structural design, or licensed design work |
| Live or verified pricing | No vendor feeds, no ZIP-based rate lookup, no market data service |
| Shared production infrastructure | No accounts, access control, cloud sync, concurrent editing, or approval workflow |
| An audit log | Snapshots are browser-local and trivially editable |

The interface repeats these limits in the hero footer, the estimate method banner, every panel's disclaimer note, and the generated proposal text.

**The shipped Kern County rate library is a planning baseline, not a quote book.** Every row ships unverified and undated on purpose, so the tool's own freshness and provenance checks flag them honestly until you replace them with real vendor pricing.

---

## What's new in v6.0

v6.0 is a recalibration release. v5.0's concept model was tuned for a small accessory unit and produced materially understated numbers for ordinary dwellings; several line items could not scale with the actual program at all.

**Bug fix**

- **Rate freshness check repaired.** `ageDays()` contained a double-backslash regex literal (`/^\\d{4}-.../`) that matched a literal `\d{4}` instead of a date, so it returned `Infinity` for every input. The consequence: in detailed mode the *Rate freshness* preflight check could **never** pass, permanently reporting a false warning even for rates quoted today. Now returns correct day counts.

**Kern County rate library**

- 30 priced CSI rows researched for **Kern County / Bakersfield** residential construction — material rates, labor hours per unit, equipment, and waste percentages — replacing the 11 empty placeholder rows shipped in v5.
- Five **regional presets** (`kern`, `la`, `sac`, `sd`, `bay`) that load the library scaled by a transparent material delta plus a regional loaded labor rate and climate zone.
- Rate library ships **unverified and undated** so provenance checks report honestly.

**Recalibrated concept model**

| Division | v5.0 | v6.0 |
| --- | --- | --- |
| Site work | `800 + trench×6.5 + area×2.1` | `1500 + slabCY×78 + trench×35` |
| Foundation & slab | *absent* | New division, `area×11.97 + slabCY×276 + 1160`; drops to a verification allowance when an existing slab is credited |
| Framing | `area×7.2 + beds×950 + baths×1200` | `area×28.5 + envelopeSF×5.7` |
| Roofing | *absent* | New division, `area×9.87` |
| HVAC | `area×hvac×1.10` (hvac ≈ 3.4) | `area×12.47×climateMul×1.10` |
| Electrical | `area×4.2 + baths×650` | `area×20.75 + 2215` |
| Plumbing | `baths×3200 + 1800 + heater` | `fixtures×777 + pipingLF×23.4 + water heater + water service` |
| Interior finishes | `area×finishRate×0.32` | `area×finishRate×0.24 + caseworkLF×328` |
| Openings & exterior | *bundled into finishes* | New separate division, `openings×(597+965) + perimeter×8×15.06` |
| Permits | `2800 + area×3.5` | `3000 + area×2.5` |
| Engineering | `2500 + area×1.5` | `3000 + area×2.75` |
| Existing-condition credits | flat `$14 / $18 / $10` per SF | derived from the actual affected division base |

**All v6.0 concept coefficients are derived from the shipped cost book** at the $85/hr reference labor point, not hand-tuned. The concept path and the detailed cost-book path are two views of one rate set, which is why they agree within 1.7% on the worked test case.

**Market index semantics corrected**

- The market index now applies to **material and equipment only**. Previously it multiplied labor as well, so one index was silently discounting labor markets *and* material markets by the same number. Labor is driven by the hourly rate and burden, which is where a labor market actually lives.

**New inputs**

- **Laundry hookup / laundry room** checkbox — adds plumbing and electrical allowance for washer, dryer, supply and drain.
- **Regional rate preset** selector with a load button.

**New outputs**

- **Takeoffs & coordination CSV export** — structural, electrical, openings and room-allocation schedules to one UTF-8 BOM CSV, including the verification warning.
- **Cost-book rate-age card** — a provenance banner reporting stale rates, undated rates, or all-current, against the configured refresh target.

**Storage**

- New `sovr-build-os-estimator-v6` key with automatic migration from v5 and v4 workspaces on first load.

**Calibration effect** — a 1,274 SF / 3 bed / 1 bath plan at Kern County defaults moved from **$119,065 ($93.46/SF)** in v5.0 to **$231,224 ($181.49/SF)** in v6.0, with 10% contingency and no overhead, profit, or tax in either case. That figure is now cross-validated against the detailed cost-book path to within 1.7% — see [Calibration notes](#calibration-notes).

---

## Quick start

There is nothing to install.

```bash
git clone https://github.com/StavoMidnite661/NEW_sovr-production-estimator.git
cd NEW_sovr-production-estimator
```

Then either:

- **Open it live** at <https://sovr-estimator.vercel.app>, or
- **Run it locally** — open `index.html` in any modern browser, or serve the folder:

```bash
npx serve .
# or
python -m http.server 8080
```

The estimator requires JavaScript for calculation, validation, and exports; a `<noscript>` notice is shown when scripting is off.

### First estimate in six steps

1. **Project identity** — pick a project type, name the job, and enter the site city and ZIP.
2. **Geometry and program** — set length, width, stories, bedrooms, bathrooms, laundry, finish reference, and climate zone. Gross area recalculates as you type.
3. **Load your market** — in *Market & labor factors*, pick a regional preset and click **Load preset rate library & labor**. This sets the climate zone, the loaded labor rate, and replaces the cost book with that region's scaled library.
4. **Choose a pricing method** — stay on the *concept allowance model* for early ROM work, or switch to *detailed unit-price line items* for a traceable build-up.
5. **Price it** — in detailed mode, open **Cost book** and replace the library rates with real vendor quotes and as-of dates, then pull them into **Line items**.
6. **Check and issue** — open **Scope & QA**, resolve the critical gaps, record a review, then export the ledger CSV, takeoffs CSV, or the proposal worksheet.

---

## Feature tour

The workspace is organised into eight tabs.

### Overview

The transparent roll-up. Three summary cards (material + equipment, labor, credits), a proportional base-cost-mix bar chart, the readiness banner, and the full cost ledger as a table with a printed foot that walks from *positive direct cost subtotal* down to *planning total · base scope*. Exports the ledger to CSV and copies the current total to the clipboard.

### Line items

The measured cost build-up. Each row is a real estimating line with editable CSI code, description, notes, cost/credit type, quantity, unit, material rate per unit, labor hours per unit, equipment per unit, waste percentage, source/quote reference, as-of date, a user-verified checkbox, and a classification. Extended cost is shown per row.

Three ways to populate it:

- **Add concept allowances** — transfers the concept model's division allowances into editable lump-sum rows and switches to detailed mode, so you can refine a ROM rather than rebuild it.
- **Add a rate-book item** — copies a row from your cost book onto the estimate as a snapshot.
- **Add blank line** — a clean row to fill in.

Line-item rates are **copied snapshots, not live links**. Changing a cost-book rate does not retroactively change estimates that already consumed it — deliberate, because it preserves the record of what a given estimate was priced from.

### Cost book

Your regional price memory. 30 CSI rows ship pre-priced for Kern County. Every row carries material rate, labor hours, equipment, waste, source/vendor, as-of date, and a verified flag. Supports **Import CSV**, **Export CSV**, add, and delete.

Live stats track entries, how many are priced, and how many carry both a source and a date. A **provenance banner** below the stats reports stale rates, undated rates, or all-current against the refresh target.

> "Verified" is a **user attestation**. Nothing checks it against an external feed.

### Scope & QA

Four free-text scope fields (included, excluded, allowances/owner selections, open risks), a commercial review record, and the full automated preflight panel. This is where an estimate stops being a number and becomes a document someone can hand to another human.

### Takeoffs & schedules

Dimension-derived planning quantities — deliberately labelled as conceptual coordination aids, not measured takeoffs. Four sub-tables plus room allocation cards, and a **CSV export** of the whole panel.

### Timeline

A nine-phase preliminary sequence with week ranges, and an indicative completion window if a target start date is set.

### Proposal / RFQ

A live text preview of the full transmittal — project basis, base cost by division, commercial summary, inclusions, exclusions, allowances, risks, selected RFQ scope, and the full disclaimer. Plus a 15-item RFQ scope checklist. Copy to clipboard or download as `.txt`.

### Revisions

Named snapshots with the total at the moment of saving, delta against the current estimate, and one-click restore. Keeps the last 20 snapshots and the last 40 activity entries.

---

## The two pricing modes

The mode selector in *Project & pricing setup → 03 · Estimate method* is the single most consequential setting in the tool.

| | Concept allowance model | Detailed unit-price line items |
| --- | --- | --- |
| **Purpose** | Early ROM and budget conversations | Traceable, priced estimates |
| **Cost source** | Built-in placeholder allowances | Your editable quantities and cost book |
| **Rate provenance** | None — no quote dates exist | Source, as-of date, verified flag per line |
| **Typical accuracy** | Order-of-magnitude only | Only as good as the numbers you enter |
| **Preflight status** | Always reports a **critical** gap | Reports gaps in quantities, units, provenance |
| **Banner** | `ROM · Unverified` | Banner hidden; badge reads `Unit-priced` |

**Critical detail:** the project type selects a **template and a scope label only. No hidden project-type multiplier is applied.** Two identical geometries priced identically produce identical numbers regardless of project type — the app states this explicitly in the method banner so nobody assumes a type is secretly inflating a bid.

---

## Calculation reference

All calculations are explicit and inspectable. Here is the complete model.

### Line-item extension (detailed mode)

```
material   = quantity × materialRate   × (1 + waste%) × material market index
labor      = quantity × laborHours     × laborRate   × (1 + burden%)
equipment  = quantity × equipmentRate                       × material market index
extended   = material + labor + equipment
```

A row with `kind = credit` is negated (`−1` sign applied to all three components) and reduces base cost.

**The market index does not touch labor.** Labor markets and material markets diverge — a region can see lumber move 20% while wages hold flat — so v6.0 applies your material index to material and equipment only, and leaves labor entirely to the hourly rate and burden you enter. Configure the loaded rate correctly and leave burden at `0`.

### Classification behaviour

| Classification | In base total? | Effect |
| --- | --- | --- |
| `base` + `cost` | Yes | Adds to positive direct cost and taxable basis |
| `base` + `credit` | Yes, negatively | Reduces net direct cost; reduces taxable basis |
| `alternate` | No | Tracked and reported separately as *alternates outside base* |
| `owner` | No | Listed as owner-supplied in the proposal |
| `excluded` | No | Counted and reported, never added |

### Commercial stack

Applied in this exact order, on `positiveDirect` (the sum of non-credit base cost):

```
tax         = taxableBase × taxRate%
contingency = positiveDirect × contingency%
overhead    = positiveDirect × overhead%
profit      = (positiveDirect + overhead) × profit%
escalation  = (positiveDirect + tax + contingency + overhead + profit) × escalation%

netDirect   = positiveDirect − creditApplied
grandTotal  = max(0, netDirect + tax + contingency + overhead + profit + escalation)
costPerSF   = grandTotal ÷ area
```

`taxableBase` is material + equipment of base rows, with credit-row material and equipment subtracted. In concept mode, permits and engineering are **excluded from the market index** because jurisdiction fees and design fees are local, not commodity-driven. All rounding is to whole dollars at each step; the rate is formatted to two decimals.

> Verify contingency, burden, overhead, profit, and escalation treatment against your own contract and accounting policy. Different firms apply these to different bases.

### Material / labour splits

Each concept division carries a fixed installed-cost split used to present material and labor separately in the ledger.

| Division key | Material | Labor |
| --- | --- | --- |
| `site` | 35% | 65% |
| `foundation` | 50% | 50% |
| `framing` | 55% | 45% |
| `insulation` | 60% | 40% |
| `roofing` | 60% | 40% |
| `hvac` | 45% | 55% |
| `electrical` | 45% | 55% |
| `plumbing` | 40% | 60% |
| `interior` | 50% | 50% |
| `exterior` | 55% | 45% |
| `permits` | 100% | 0% |
| `engineering` | 100% | 0% |

Labor is then scaled by `laborRate ÷ 85 × (1 + burden%)`, with $85/hr as the reference point.

---

## Concept allowance model

A transparent ROM formula built from geometry, program, climate zone, finish reference, trench length, and market factors. Twelve CSI-level bases:

| CSI | Division | Basis formula |
| --- | --- | --- |
| `02 20 00` | Site work, grading & utility trench | `1500 + slabCY×78 + trench×35` |
| `03 11 00` | Foundation, footings & slab | `area×11.97 + slabCY×276 + 1160`, or `1200` if an existing slab is credited |
| `06 11 00` | Framing, sheathing & structural wood | `area×28.5 + envelopeSF×5.7` |
| `07 21 00` | Insulation, air & moisture barrier | `envelopeSF×2.1×zoneInsulationMul + 230` |
| `07 92 00` | Roofing, underlayment & flashing | `area×9.87` |
| `23 00 00` | HVAC / mechanical | `area×12.47×zoneHvacMul×(1.15 vaulted / 1.10 flat)` |
| `26 00 00` | Electrical | `area×20.75 + 2215` |
| `22 00 00` | Plumbing, water heating & laundry | `fixtures×777 + pipingLF×23.4 + 2575 + 410 + waterService 5560` |
| `09 00 00` | Interior finishes, drywall & flooring | `area×finishRate×0.24 + caseworkLF×328` |
| `08 50 00` | Openings, doors, windows & exterior finish | `openings×(597 + 965) + perimeter×stories×8×15.06` |
| `01 41 00` | Permits, plan check & inspections | `3000 + area×2.5` (excluded from market index) |
| `01 40 00` | Engineering & design | `3000 + area×2.75` (excluded from market index) |

Derived quantities:

```
slabCY     = ((footprint × 4/12 ÷ 27) + (perimeter × 0.25 × 0.5 ÷ 27)) × 1.10
wallLF     = perimeter × stories + (partitioned ? length × 0.6 × stories : 0)
envelopeSF = wallLF × 8 + footprint × stories        // walls + ceiling basis
pipingLF   = round(perimeter × 0.8)
caseworkLF = 18 + bedrooms×4 + bathrooms×10
openings   = 2 + bedrooms + bathrooms                  // doors and windows each
fixtures   = bathrooms×3 + 1 + (laundry ? 2 : 0)
```

Gas water heating subtracts $200 from the plumbing base relative to a heat pump.

**Every coefficient above is derived from the shipped Kern County cost book at the $85/hr reference labor point**, not tuned by hand. That means the concept model and the detailed cost-book path are two views of one rate set, and they agree with each other by construction rather than by coincidence.

**Existing-condition credits** are now derived from the division they actually offset, rather than from a flat per-square-foot guess:

| Condition | Credit |
| --- | --- |
| Existing slab / foundation | 90% of the foundation division base |
| Existing framing | 55% of the framing division base |
| Existing utilities available | 25% of the plumbing division base |

Credits are capped at the positive direct cost so the total can never go negative from credits alone.

**These numbers remain placeholders.** They are inherited ROM assumptions with no quote behind them, which is exactly why the preflight panel marks concept mode as a critical gap unconditionally.

---

## Detailed unit-price model

Everything is driven by your line items, grouped into the ledger by CSI code.

**Line item fields**

| Field | Notes |
| --- | --- |
| `csi` | CSI MasterFormat-style code, free text |
| `csiDescription` | Division name used for ledger grouping |
| `description` | Line description |
| `notes` | Assumption / scope note for the row |
| `kind` | `cost` or `credit` |
| `qty`, `unit` | Quantity and unit of measure (`EA`, `SF`, `LF`, `CY`, `LS`, …) |
| `materialRate` | Material cost per unit |
| `laborHours` | Labor hours per unit |
| `equipmentRate` | Equipment cost per unit |
| `wastePct` | Waste allowance, clamped 0–200% |
| `source` | Vendor / quote / internal study reference |
| `sourceDate` | As-of date for that source |
| `verified` | User attestation flag |
| `classification` | `base`, `alternate`, `owner`, `excluded` |
| `rateBookId` | Provenance link back to the cost-book row it was copied from |

---

## Kern County rate library

Thirty priced rows researched for residential construction in **Kern County / Bakersfield, California**. Material rate is per unit; labor is expressed as **hours per unit** so it reprices automatically when you change the loaded hourly rate.

| CSI | Description | Unit | Material | Labor hr | Equip | Waste | Installed |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `01 21 00` | Temporary power & site facilities | LS | $1,250.00 | 4.0 | — | — | $1,590 |
| `01 40 00` | Engineering / design | LS | $6,500.00 | — | — | — | $6,500 |
| `01 41 00` | Permits, plan check & inspections | LS | $5,200.00 | 12.0 | — | — | $6,220 |
| `02 20 00` | Excavation & grading | CY | — | 0.35 | $46.00 | — | $76/CY |
| `02 30 00` | Utility trench & backfill | LF | $9.00 | 0.10 | $18.00 | — | $36/LF |
| `03 11 00` | Footings & stem wall | CY | $185.00 | 0.55 | $35.00 | 5% | $276/CY |
| `03 30 00` | Slab on grade — mesh, vapor barrier, finish | SF | $6.40 | 0.055 | $0.90 | — | $10.3/SF |
| `03 35 00` | Concrete curing & protection | LS | $480.00 | 8.0 | — | — | $1,160 |
| `06 11 00` | Framing & structural wood | SF | $13.50 | 0.16 | $0.35 | 8% | $23.7/SF |
| `06 40 00` | Wall & roof sheathing | SF | $2.85 | 0.03 | — | 10% | $4.78/SF |
| `07 21 00` | Insulation & air barrier | SF | $1.35 | 0.008 | — | 5% | $1.86/SF |
| `07 52 00` | Fireblocking & draft stopping | LF | $2.10 | 0.02 | — | — | $3.20/LF |
| `07 92 00` | Roofing shingles & underlayment | SF | $5.60 | 0.045 | — | 8% | $8.52/SF |
| `08 11 00` | Interior & exterior doors | EA | $385.00 | 2.5 | — | — | $523/EA |
| `08 50 00` | Windows & glazed openings | EA | $625.00 | 4.0 | — | — | $845/EA |
| `09 22 00` | Gypsum board, tape & texture | SF | $3.15 | 0.062 | — | 7% | $6.78/SF |
| `09 63 00` | Flooring & base | SF | $6.20 | 0.045 | — | 6% | $9.05/SF |
| `09 91 00` | Interior & exterior paint | SF | $3.40 | 0.055 | — | 3% | $6.53/SF |
| `10 21 00` | Bathroom accessories & hardware | EA | $240.00 | 2.0 | — | — | $350/EA |
| `11 40 00` | Plumbing fixtures & trim | EA | $480.00 | 3.5 | — | — | $673/EA |
| `22 40 00` | Domestic water piping | LF | $16.50 | 0.075 | — | 3% | $21.1/LF |
| `22 60 00` | Water heater / heat pump water heater | EA | $2,150.00 | 5.0 | — | — | $2,425 |
| `23 05 00` | HVAC ducting & equipment | SF | $7.80 | 0.055 | — | — | $10.8/SF |
| `26 05 00` | Electrical service & panel | EA | $1,450.00 | 9.0 | — | — | $1,945 |
| `26 20 00` | Branch wiring & devices | SF | $9.40 | 0.085 | — | — | $14.1/SF |
| `26 30 00` | Lighting & controls | SF | $2.60 | 0.018 | — | — | $3.59/SF |
| `28 00 00` | Casework & countertops | LF | $185.00 | 1.6 | — | 4% | $280/LF |
| `31 00 00` | Demolition of existing structure | SF | $1.10 | 0.18 | $0.35 | — | $11.4/SF |
| `32 50 00` | Exterior siding & trim | SF | $9.80 | 0.055 | — | 6% | $13.4/SF |
| `33 11 00` | Water service & utilities allowance | LS | $4,200.00 | 16.0 | — | — | $5,080 |

*Installed column = material × (1 + waste) + labor hours × $85/hr + equipment, i.e. at the model's $85/hr reference labor point.*

Every row's `source` is set to `Kern County 2026 planning library — unverified, replace with a current vendor quote`, and every `sourceDate` is **blank** with `verified` **false**.

> **These are planning baselines, not quotes.** They exist so the tool opens with something structurally realistic instead of a wall of zeros, and so the preflight panel has honest provenance gaps to report. Bid them out before you rely on any of them.

---

## Regional presets

One researched library, five transparent regional adjustments. Loading a preset sets the climate zone, the loaded labor rate, **the material market index**, and rebuilds the cost book with material and equipment rates scaled by the region's material delta. Labor rates are **fully loaded** — burden is set to `0`.

| Preset | Zone | Material delta | Loaded labor |
| --- | --- | --- | --- |
| **Kern County · Bakersfield** | 14 | 1.00 | $55/hr |
| Los Angeles basin | 09 | 1.06 | $71/hr |
| Sacramento valley | 12 | 1.04 | $60/hr |
| San Diego coast | 03 | 1.05 | $68/hr |
| San Jose Bay area | 04 | 1.08 | $82/hr |

Loading a preset **resets the `marketVerified` flag to false** — a regional delta is an adjustment, not a verification.

### Climate zone multipliers

Insulation and HVAC bases are derived from the cost book, then multiplied by a **climate multiplier** relative to Sacramento (1.00).

| Zone | Label | Insulation × | HVAC × | Wall / ceiling placeholders |
| --- | --- | --- | --- | --- |
| `03` | San Diego coast | 0.88 | 0.85 | R-13 / R-30 |
| `04` | San Jose Bay Area | 0.94 | 0.80 | R-15 / R-30 |
| `09` | Los Angeles basin | 0.95 | 0.88 | R-19 / R-30 |
| `12` | Sacramento | 1.00 | 1.00 | R-21 + CI / R-38 |
| `14` | Bakersfield | 1.08 | 1.10 | R-21 + CI / R-38 |

Bakersfield carries the highest multipliers because desert conditions push both envelope R-value and cooling equipment size up.

These are **planning placeholders, not energy-code determinations.**

---

## Cost book

### CSV columns

Import and export use these headers (case-insensitive on import; `csi` and `description` are required):

```
csi, description, unit, materialRate, laborHours, equipmentRate, wastePct, source, sourceDate, verified
```

`verified` imports only when the value is exactly `true` (case-insensitive). Imports append to the existing book; exports are UTF-8 with a BOM and CRLF line endings so they open cleanly in Excel.

### Provenance banner

Below the cost-book stats, a readiness banner reports:

| State | Condition | Badge |
| --- | --- | --- |
| Stale | Any rate has a `sourceDate` older than the refresh target | `Stale` (critical styling) |
| Undated | No stale rates, but some rate has no `sourceDate` | `Undated` |
| Current | Every rate is dated within the refresh target | `Current` (good styling) |

A rate with no date can never satisfy the freshness test, so an undated library correctly reports *Undated* rather than falsely claiming currency.

### Capacity limits

Cost book is capped at 500 rows. Numbers are clamped to non-negative; waste is clamped to 0–200%.

---

## Automated preflight checks

The QA panel runs live on every change and reports three levels: **good** (✓), **warn** (·), **critical** (!). It is a gap-finder, never an approver — the summary line reads *"Checks passing is not an approval, contract, or guarantee of price."*

| # | Check | Good when |
| --- | --- | --- |
| 1 | Project and client identity | Project name and client are populated |
| 2 | Site location / ZIP | City set and ZIP matches `#####` or `#####-####` |
| 3 | Regional market factor | Verified flag **and** source **and** as-of date all present |
| 4\* | Pricing method is concept-only | *Always critical in concept mode* |
| 4a | Detailed base-scope lines | At least one base cost line exists |
| 4b | Quantities and units | Every included line has quantity > 0 and a unit |
| 4c | Rate provenance | Every included line has verified + source + as-of date |
| 5 | Rate freshness | No line date and no market date older than the refresh target |
| 6 | Inclusions and exclusions | Both scope fields are documented |
| 7 | Commercial settings review | Estimator attested to tax/burden/contingency/OH&P treatment |
| 8 | Human estimator review | A reviewer name and review date are recorded |
| 9 | Engineering, code & trade validation | *Always warn — requires licensed review* |

\* Check 4 replaces 4a–4c in concept mode, giving 9 checks in concept mode and 11 in detailed mode.

**Readiness summary states**

- Any critical gap → *"N critical preflight gaps"* · badge **Not ready**
- Any warn, no critical → *"N review items remain"* · badge **Review required**
- All clear → *"Automated checks complete"* · badge **Human approval required**

The ZIP check validates **format only**. A syntactically valid ZIP does not validate pricing, jurisdiction, or any rate.

---

## Takeoffs and coordination schedules

All quantities are derived from entered dimensions and are explicitly conceptual.

### Structural and envelope

| Component | Derivation |
| --- | --- |
| Concrete slab + perimeter footing | 4-in slab over footprint + nominal 12 × 18-in perimeter allowance, × 1.10 — ground footprint only |
| Exterior building perimeter | `2 × (length + width)`, per storey |
| Interior partition allowance | 60% of entered length per storey (partitioned layout only) |
| Gross wall coverage | `(perimeter + partition) × 8-ft nominal height`, openings not deducted |
| 2×6 studs @ 16" o.c. | `ceil((wallLF × 0.75 + 8 + bedrooms×3 + bathrooms×3) × 1.10)` |
| 2×4 plate stock, 8-ft | `ceil(wallLF × 3 ÷ 8 × 1.10)` |
| Wall sheathing 4×8 | `ceil(wallSF ÷ 32 × 1.10)` |
| Roof sheathing 4×8 | `ceil(footprint ÷ 32 × 1.10)` |
| Roof truss lines | `ceil(length ÷ 2) + 1` |
| Utility trench | Entered linear feet, or "no trench allowance" at 0 |
| Insulation / envelope target | Climate-zone R-value placeholders, flagged as not an energy-code determination |

### Electrical coordination schedule

Nine systems with planning quantities, example protection ratings, example conductor sizes, and a coordination note. **No load calculation and no NEC design is performed.** Representative derivations:

- General receptacles: `max(3, ceil(area ÷ 80))` points
- Lighting circuits: `max(1, ceil(area ÷ 400))` circuits
- Smoke / CO alarms: `1 + bedrooms (partitioned only) + bathrooms`, minimum 2

### Openings schedule

Auto-generated marks `D-01`…`D-03` and `W-01`…`W-03` with nominal sizes and review items. Living-area windows: `max(2, min(8, round(width ÷ 12))) × stories`. Bedroom egress and bathroom privacy-window allowances appear only when the program calls for them.

### Room area allocation

Gross area is split by share with **largest-remainder rounding**, so the allocated areas always sum exactly to the gross area.

| Layout | Allocation |
| --- | --- |
| Open studio | Kitchen 22% · bathrooms share · remainder to living/studio |
| Partitioned | Kitchen 18% · circulation/hall 8% · bedrooms share · bathrooms share · remainder to living |

Bathroom share = `min(18%, 10% + (bathrooms − 1) × 3.5%)`, split evenly across bathrooms.

### CSV export

**Export takeoffs CSV** writes one UTF-8 BOM CSV containing the project header block, all three schedule tables with their column headings, the room allocation, and a closing `WARNING` row restating that these are conceptual planning quantities requiring verification against actual plans and the applicable jurisdiction.

---

## Preliminary timeline

Nine sequential phases, each with a min/max week range driven by floor area:

| Phase | Duration basis |
| --- | --- |
| Permitting & plan check | 4–5 weeks, fixed |
| Site preparation & demolition | `ceil(area ÷ 600)`, +1 for max |
| Foundation / slab work | `ceil(area ÷ 500)`, or review-only if existing slab is credited |
| Framing & structural work | `ceil(area ÷ 500)`, +1 for max |
| Rough mechanical / electrical / plumbing | `ceil(area ÷ 350)`, +1 for max |
| Insulation & drywall | `ceil(area ÷ 500)`, +1 for max |
| Finish work | `ceil(area ÷ 300)`, +1 for max |
| Final MEP, hardware & punch | 1–2 weeks |
| Final inspection & closeout | 1–2 weeks |

With a target start date set, the tool shows an indicative completion window. **This is a sequential planning window; trade overlap shortens the calendar.** Permit review, inspections, weather, utility lead times, material availability, design revisions, and crew capacity can all change it materially.

---

## Proposal / RFQ worksheet

The generated transmittal contains:

1. Header block — project, type, client, site, date, method, prepared by
2. **Project basis** — dimensions, area, program, finish reference, climate placeholder, material market index with source and date, labor rate and burden
3. **Base cost by division** — CSI-aligned, column-formatted
4. **Commercial summary** — positive direct, credits, net direct, each adder with its percentage, planning total, cost/SF, alternates outside base, owner-supplied items, excluded row count
5. **Inclusions** / **Exclusions** — one bullet per line from the scope fields
6. **Allowances and open risks** — labelled separately
7. **Request for quote** — the selected scope items from the 15-item checklist
8. **Disclaimer** — the full validation language
9. Generation date

RFQ checklist options (selections do **not** change the estimate):

```
Demolition & site preparation          Flooring
Foundation / slab work                 Cabinets & countertops
Structural framing                     Interior / exterior paint
Roofing modifications                  Appliance installation
Electrical rough-in & finish           Final clean & punch list
Plumbing rough-in & finish
HVAC installation
Insulation
Drywall, tape & texture
Doors & windows
```

---

## Revisions and snapshots

- **Save revision snapshot** captures project, settings, scope, cost book, and line items, plus the label, timestamp, mode, project name, and total at that moment.
- Each row shows **Change vs current** — the signed delta between the snapshot total and the live estimate.
- **Restore** replaces the working state while keeping the snapshot list and activity log intact.
- Keeps the **20 most recent** snapshots and the **40 most recent** activity entries.
- Snapshot labels default to `Revision N` when left blank.

Snapshots live in `localStorage` and are lost if browser data is cleared. **Export the project JSON for a portable backup.** This is not a tamper-proof audit log.

---

## Project templates

**Apply selected project template** seeds geometry and program from the selected project type. It changes dimensions, bedrooms, bathrooms, layout, and finish reference — nothing else.

| Project type | Dimensions | Program | Finish |
| --- | --- | --- | --- |
| ADU New Construction | 24 × 24 ft | 1 BR / 1 BA, partitioned | Standard |
| Garage Conversion | 20 × 22 ft | 1 BR / 1 BA, partitioned | Standard |
| Home Addition | 20 × 16 ft | 1 BR / 1 BA, partitioned | Standard |
| Kitchen Remodel | 16 × 14 ft | 0 BR / 1 BA, open studio | Standard |
| Bathroom Remodel | 10 × 10 ft | 0 BR / 1 BA, open studio | Premium |
| Whole Home Renovation | 36 × 28 ft | 3 BR / 2 BA, partitioned | Standard |

Templates only define the **starting scope envelope**. They do not set rates and they carry no cost multiplier.

### Finish references

| Reference | Rate |
| --- | --- |
| Economy | $85/SF |
| Standard | $125/SF |
| Premium | $175/SF |
| Luxury | $250/SF |

Used in concept mode for the interior finishes division at 24% of gross area, plus a separate casework allowance. In detailed mode the finish reference is carried into the proposal text as a reference only.

---

## Input reference

Every numeric input is clamped on both entry and import.

| Field | Range | Default |
| --- | --- | --- |
| Length, width | 8–250 ft | 24 ft |
| Stories | 1–4 | 1 |
| Bedrooms | 0–10 | 1 |
| Bathrooms | 1–10 | 1 |
| Utility trench | 0–300 LF | 25 LF |
| Laundry hookup | boolean | checked |
| Material market index | 0.70–1.50 | 1.00 |
| Base labor rate | $20–$250/hr | $55/hr |
| Labor burden | 0–100% | 0% |
| Tax on material/equipment | 0–20% | 0% |
| Contingency | 0–50% | 10% |
| Overhead | 0–40% | 0% |
| Profit | 0–40% | 0% |
| Escalation | 0–40% | 0% |
| Rate refresh target | 30 / 60 / 90 / 180 / 365 days | 180 days |
| Line item quantity | ≥ 0 | 1 |
| Line item waste | 0–200% | 0% |

Other structural limits: **500** cost-book rows, **1,500** line items, **20** snapshots, **40** activity entries, 150-character activity messages, 90-character project names, 100-character locations, 120-character market sources, 160-character review notes, 6,000-character scope fields.

Setting labor burden to `0` is correct if your hourly rate is already fully loaded — which is how every regional preset ships.

---

## Data, storage, and privacy

### What is stored, and where

Everything lives in **`localStorage`** in the current browser profile:

| Key | Contents |
| --- | --- |
| `sovr-build-os-estimator-v6` | Full state, schema version 2 (active) |
| `sovr-build-os-estimator-v5` | v5 state, auto-migrated on first v6 load |
| `sovr-build-os-estimator-v4-draft` | Legacy flat draft, auto-migrated on first v6 load |

On load, v6 checks its own key first, then falls back to v5, then to v4. A migrated workspace is validated, saved to the v6 key, and logged to the activity list so the migration is visible rather than silent.

Autosave is debounced 180 ms after the last change. The status dot in the top bar reports `Saving locally` → `Draft saved locally`, or **`Browser storage unavailable`** if the write fails.

### What is never done

- No network requests of any kind
- No analytics, telemetry, tracking pixels, or error reporting
- No cookies
- No accounts, authentication, or access control
- No cloud storage, backup, or sync
- No concurrent editing or collaboration
- No CRM, accounting, ERP, or vendor-feed integration
- No third-party fonts, scripts, or CDN assets — all CSS and JS is inline

The file is completely self-contained. You can disconnect from the network after the page loads and it will keep working.

### Explicitly absent

No encrypted team database, no formal approval workflow, no rate-lock, no tamper-proof audit trail. **Do not use this as shared production infrastructure** without adding those services yourself.

Because data is browser-local and per-profile, it does not sync across browsers, devices, or incognito windows. **Export the project JSON before switching machines.**

---

## Import and export formats

| Action | Output | Notes |
| --- | --- | --- |
| **Save JSON** | `<Project>_SOVR_Project.json` | Complete portable backup: full state plus a calculated summary |
| **Open** | Reads the same format | Validated and clamped on import; invalid files are rejected with a message |
| **Export ledger CSV** | `<Project>_Estimate.csv` | Division roll-up and commercial summary; **appends every line item when in detailed mode** |
| **Export takeoffs CSV** | `<Project>_Takeoffs.csv` | Structural, electrical, openings and room-allocation schedules plus warning row |
| **Export CSV** (cost book) | `<Project>_Rate_Book.csv` | UTF-8 BOM, CRLF, quoted fields |
| **Import CSV** (cost book) | — | Appends; requires `csi` and `description` headers |
| **Download .txt** | `<Project>_Proposal_RFQ.txt` | Full transmittal |
| **Copy total** | Clipboard | Current planning total |
| **Copy text** | Clipboard | Full proposal text |

Filenames are slugified from the project name and truncated to 55 characters.

Clipboard copy uses the async Clipboard API where available and secure, with a `document.execCommand` fallback. If both are blocked, the app says so and points you to the download action.

---

## Project file schema

```jsonc
{
  "application": "SOVR Build OS Estimating Workbench",
  "version": "6.0",
  "schemaVersion": 2,
  "exportedAt": "2026-10-04T21:00:00.000Z",
  "state": {
    "project":  { /* type, name, client, location, zip, preparedBy, startDate,
                     length, width, stories, bedrooms, bathrooms, layout, finish,
                     zone, heating, ceiling, trench, laundry, slab, framing,
                     utilities */ },
    "settings": { /* mode, preset, marketFactor, marketSource, marketDate,
                     marketVerified, laborRate, laborBurden, taxRate, contingency,
                     overhead, profit, escalation, rateMaxAgeDays, pricingReviewed */ },
    "scope":    { /* included, excluded, allowances, risks, reviewedBy, reviewNote,
                     reviewDate, rfq[] */ },
    "rateBook": [ { /* id, csi, description, unit, materialRate, laborHours,
                      equipmentRate, wastePct, source, sourceDate, verified */ } ],
    "items":    [ { /* id, csi, csiDescription, description, notes, kind, qty, unit,
                      materialRate, laborHours, equipmentRate, wastePct, source,
                      sourceDate, verified, classification, rateBookId */ } ],
    "versions": [ { /* id, savedAt, label, mode, projectName, total, payload */ } ],
    "activity": [ { /* time, message */ } ]
  },
  "calculatedSummary": {
    "mode": "concept",
    "total": 0,
    "area": 576,
    "costPerSF": 0
  }
}
```

Every import runs through `validateState()`, which:

- Merges against defaults, so missing keys are safe (this is how the v5 → v6 migration adds `laundry` and `preset`)
- Coerces every number with `Number()` and clamps it to range
- Rejects invalid enum values and falls back to defaults
- Validates all dates against `^\d{4}-\d{2}-\d{2}$`
- Filters the RFQ list to known scope options only
- Caps array lengths (500 / 1,500 / 20 / 40)
- Coerces everything to strings or booleans

A malformed or hostile file cannot inject unbounded values or invalid option strings.

---

## Printing and PDF

**Print / PDF** in the top bar (and the per-tab print buttons) triggers a print stylesheet that:

- Hides the input sidebar, tab strip, action buttons, toast, and scope checklists
- Forces the active panel visible even when its tab is not selected
- Removes shadows and page-breaks tables and cards where possible
- Applies `print-color-adjust: exact` to the hero and grand-total rows
- Compresses type scale and spacing for density
- **Appends a footer line:** *"Concept / estimating aid only. Not a bid, permit set, load calculation, or engineered design."*

Use your browser's print dialog and choose **Save as PDF**.

---

## Accessibility

- Semantic landmarks: `header`, `main`, `aside`, `section`, `footer`
- Full ARIA tab pattern with `role="tablist"` / `tab` / `tabpanel`, `aria-selected`, and `aria-controls`
- **Keyboard tab navigation:** ← → to move, `Home` / `End` to jump; focus follows the active tab
- `aria-live="polite"` on the KPI strip and the toast
- Visible `:focus-visible` outlines throughout (3 px teal ring)
- All dynamic table inputs carry descriptive `aria-label`s
- Status conveyed by text as well as colour
- `.sr-only` class for visually hidden but screen-reader-available labels
- `prefers-reduced-motion: reduce` disables animation and smooth scrolling
- Responsive from 360 px phones to wide desktops: three breakpoints at 1250 px, 900 px, and 640 px
- All interpolated text is HTML-escaped before insertion

---

## Technical architecture

### Constraints

The hard design constraint was: **one file, zero dependencies, zero network.** That rules out bundlers, frameworks, CDNs, and web fonts, and it is what makes the tool trivially portable — email it, drop it on a USB stick, or open it from disk.

### Implementation

| Aspect | Choice |
| --- | --- |
| Language | Vanilla ES2020 in a single IIFE, `'use strict'` |
| Markup | Semantic HTML5 with ARIA |
| Styling | Inline CSS with custom properties |
| Dependencies | **None** |
| Build step | **None** |
| Formatting | `Intl.NumberFormat` (USD, integer and 2-decimal variants) |
| Dates | `Intl.DateTimeFormat` plus `toLocaleString` |
| Persistence | `localStorage`, debounced 180 ms, with v6/v5/v4 key fallback |
| File I/O | `Blob` + `URL.createObjectURL` + programmatic anchor click |
| CSV | Hand-rolled RFC-4180-style parser handling quotes, escaped quotes, CRLF, and BOM |
| XSS defence | `esc()` helper escaping `& < > " '` on every interpolated value |
| Icons | Inline SVG |
| Size | ~720 lines, ~140 KB |

### Rendering strategy

`renderDynamic()` recomputes the estimate and repaints the header, KPIs, ledger, QA, takeoffs, timeline, proposal, and history on every change. A deliberate exception: **active form inputs are never rebuilt while you type**, so the caret does not jump. Table row repaints are likewise scoped so only the affected row's extended total is touched.

`renderEverything()` — the full repaint including form hydration, cost book, and line items — runs only on initial load, import, reset, template apply, preset load, and snapshot restore.

### Constants

All tunable model data is declared at the top of the script: project types, templates, finish rates, climate zones, division material/labor splits, the Kern County rate rows, market presets, RFQ scope options, and defaults. This is the intended extension point.

### Browser support

Any evergreen browser: Chrome / Edge 90+, Firefox 90+, Safari 15+. Relies on ES2020, `Intl`, `localStorage`, `Blob`, and optional Clipboard API (with fallback).

---

## Repository layout

```
NEW_sovr-production-estimator/
├── index.html          # the entire application (v6.0)
├── README.md           # this document
├── .gitignore          # .vercel
└── .vercel/            # Vercel project link (git-ignored)
```

There is no build step, no source directory, and no dependency manifest — `index.html` is the whole application. Edit it directly, or split the `<style>` and `<script>` blocks into separate files if you prefer to manage them independently.

---

## Deployment

Live on Vercel at **<https://sovr-estimator.vercel.app>**, deployed from the `main` branch of this repository via the Vercel Git integration — **pushing to `main` redeploys automatically.**

```bash
# CLI equivalent
vercel --prod
```

No build configuration is required: Vercel serves the file as a static asset.

---

## Extending the tool

Points of interest in `index.html`, in order:

| What | Where |
| --- | --- |
| Storage keys and legacy fallback | `STORAGE_KEY`, `LEGACY_KEYS` |
| Project types and templates | `TYPES`, `TYPE_TEMPLATES` |
| Finish rates and labels | `FINISH_RATES`, `FINISH_LABELS` |
| Climate zones | `CLIMATE` |
| Division material/labor splits | `SPLITS` |
| **Kern County rate library** | `RATE_ROWS`, `RATE_SOURCE` |
| **Regional presets** | `MARKET_PRESETS`, `buildBook()` |
| RFQ scope options | `SCOPE_OPTIONS` |
| Default project state | `DEFAULTS` |
| Concept calculation | `calcConcept()` |
| Commercial stack | `finishCalculation()` |
| Line-item math | `itemAmounts()` |
| Detailed calculation | `calcDetailed()` |
| Preflight checks | `getQaChecks()` |
| Cost-book age banner | `renderBook()` |
| Takeoff derivations | `renderPlanSchedules()` |
| Takeoffs CSV | `exportTakeoffCsv()` |
| Timeline phases | `renderTimeline()` |
| Proposal text | `proposalText()` |
| Import validation | `validateState()` |
| Schema migration | `init()` |

**To add your own market:** edit `RATE_ROWS` in place, or add a preset to `MARKET_PRESETS` with its own `materialDelta`, `laborRate`, and `zone`. Keep `verified: false` and `sourceDate: ''` until you have actually quoted the work.

**Practical next steps if you build on this:** add company letterhead to the proposal output; add per-sheet material takeoff exports; split the interior finishes division into drywall, flooring, paint and trim; add a second currency; add jurisdiction-specific permit fee tables.

---

## Calibration notes

The concept model was validated by an independent cross-check: measure the worked plan off its own takeoff derivations, price it line-by-line through the detailed cost-book path, and compare against the concept-model result for the same geometry.

**Test case** — 1,274 SF single storey, 24'-6" × 52'-0", 3 bed / 1 bath, laundry, standard finish, Zone 14, $55/hr loaded labor, 153 LF perimeter, 18.1 CY slab + footing, 1,474 SF gross wall.

### Cross-check result

| Path | Direct cost | $/SF direct | With 10% cont + 12% OH + 10% profit + 4% esc |
| --- | --- | --- | --- |
| **Detailed** (30 priced cost-book rows, real quantities) | $213,886 | $167.89 | $296,292 · **$232.57/SF** |
| **Concept** (12 derived formula divisions) | $210,204 | $165.00 | $291,191 · **$228.56/SF** |
| **Variance** | **−1.7%** | | **−1.7%** |

The two pricing paths agree to within 1.7% because the concept coefficients are derived from the same cost book rather than tuned independently.

### Kern County defaults — the 1,274 SF plan

Concept mode, Zone 14, $55/hr loaded, 10% contingency, no overhead/profit/tax:

| Division | Material | Labor | Total |
| --- | --- | --- | --- |
| `02 20 00` Site work, grading & utility trench | $1,325 | $1,592 | $2,917 |
| `03 11 00` Foundation, footings & slab | $10,700 | $6,924 | $17,624 |
| `06 11 00` Framing, sheathing & structural wood | $28,584 | $15,132 | $43,716 |
| `07 21 00` Insulation, air & moisture barrier | $3,877 | $1,673 | $5,550 |
| `07 92 00` Roofing, underlayment & flashing | $7,544 | $3,254 | $10,798 |
| `23 00 00` HVAC / mechanical | $8,650 | $6,841 | $15,491 |
| `26 00 00` Electrical | $12,893 | $10,196 | $23,089 |
| `22 00 00` Plumbing, water heating & laundry | $6,425 | $6,236 | $12,661 |
| `09 00 00` Interior finishes, drywall & flooring | $25,670 | $16,610 | $42,280 |
| `08 50 00` Openings, doors, windows & exterior finish | $15,293 | $8,096 | $23,389 |
| `01 41 00` Permits, plan check & inspections | $6,185 | — | $6,185 |
| `01 40 00` Engineering & design | $6,504 | — | $6,504 |
| **Positive direct cost** | | | **$210,204** |
| Contingency 10% | | | + $21,020 |
| **Planning total** | | | **$231,224** |
| **Cost / SF** | | | **$181.49** |

For reference, the tool's default 24 × 24 ADU at the same settings prices at **$126,468 ($219.56/SF)** — small units carry a higher rate per square foot, as expected.

**These figures are order-of-magnitude planning anchors derived from a formula, not a bid.** They are published so you can see what the model does and argue with it. Your local quotes are the estimate.

---

## Limitations

- **The rate library is unverified.** All 30 Kern County rows ship undated and unflagged on purpose. They are structurally realistic, not quoted.
- **The concept model is still a formula.** It has no site grading model, no foundation type selection, no roof geometry, no utility capacity logic, and no assembly-level detail. It scales with area and room counts because that is all a floor plan contains.
- **No room count is a proxy for design.** A 3 bed / 1 bath plan and a 3 bed / 3 bath plan differ far more than the bathroom scaling captures.
- **Dimension-derived takeoffs.** Not measured takeoffs. Openings are not deducted from wall areas; roof pitch, overhangs, and structure are not modelled.
- **No engineering.** No load calculations, no structural design, no energy-code compliance determination, no accessibility analysis.
- **No code determination.** Permit requirements, jurisdiction fees, egress, glazing safety, and insulation values must be confirmed with the actual authority and licensed professionals.
- **Fixed allowance buckets.** The electrical and openings schedules use example protection ratings and conductor sizes, explicitly labelled as examples.
- **Sequential schedule.** Trade overlap is not modelled, so calendar duration will realistically be shorter.
- **Regional deltas are coarse.** The four non-Kern presets are single material multipliers and one labor rate. Real markets differ by trade, not uniformly.
- **Browser-local state.** No sync, no multi-device continuity, no collaboration.
- **Not an audit log.** Snapshots are local and editable.
- **US-centric.** USD formatting, US climate zones, and US CSI division codes throughout.

---

## Disclaimer

This tool is a **planning and estimating aid**. It is not a vendor quote, guaranteed price, purchase order, contract, permit set, code determination, engineering document, or electrical load calculation.

No tax advice, permit fee lookup, utility quote, engineering, code review, supplier pricing, labor-market verification, or construction contract is generated here.

Prices, quantities, exclusions, taxes, market factor, allowances, schedule, and credits require project-specific verification. Concept mode uses illustrative placeholders. Detailed mode is only as reliable as the quantities and rate sources you enter.

Obtain current local vendor and trade pricing, confirm scope and site conditions, review tax and contract treatment with qualified professionals, and secure authorized human approval before external issue.

---

## License

**No license has been specified for this repository.** Without one, default copyright applies and no permission to use, modify, or redistribute is granted beyond GitHub's terms of service.

If you intend to share or build on this, add a `LICENSE` file — `MIT`, `Apache-2.0`, or `AGPL-3.0` are common fits for a tool of this type.