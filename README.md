# SOVR Build OS — Estimating Workbench

**Production estimating workbench v5.0** — a single-file, offline-capable construction estimating aid that moves a project from a concept allowance to a traceable, quantity-based estimate while keeping prices, sources, scope decisions, revisions, and quality checks in one place.

[![Live demo](https://img.shields.io/badge/live%20demo-sovr--estimator.vercel.app-11766e)](https://sovr-estimator.vercel.app)
![single file](https://img.shields.io/badge/build-none%20required-brightgreen)
![dependencies](https://img.shields.io/badge/dependencies-0-blue)
![network calls](https://img.shields.io/badge/network%20calls-0%20offline-8a8a8a)
![offline](https://img.shields.io/badge/offline-capable-brightgreen)

**Live:** <https://sovr-estimator.vercel.app>

---

## Table of contents

- [What this is](#what-this-is)
- [What this is not](#what-this-is-not)
- [Quick start](#quick-start)
- [Feature tour](#feature-tour)
- [The two pricing modes](#the-two-pricing-modes)
- [Calculation reference](#calculation-reference)
- [Concept allowance model](#concept-allowance-model)
- [Detailed unit-price model](#detailed-unit-price-model)
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
- [Limitations](#limitations)
- [Disclaimer](#disclaimer)
- [License](#license)

---

## What this is

A **field estimating workbench** for residential and light-commercial work — ADUs, garage conversions, home additions, kitchen and bathroom remodels, and whole-home renovations.

It is built around one idea: **an estimate is only as good as the quantities and the provenance of its rates.** So the tool never hides a number. Every division, credit, adder, and total is shown as a visible roll-up, every rate can carry a source and an as-of date, and a panel of automated checks reports what is still unverified — while refusing to ever claim the estimate is approved.

Everything runs in the browser. There is no backend, no account, no telemetry, and no network traffic of any kind.

## What this is not

This tool is explicit about its boundaries, both in the interface and in the code:

| Not this | Why |
| --- | --- |
| A bid, quote, or guaranteed price | No prices are contracted, negotiated, or committed |
| A permit set or code determination | No code compliance is evaluated or certified |
| An engineering document | No load calculations, structural design, or licensed design work |
| Live or verified pricing | No vendor feeds, no ZIP-based rate lookup, no market data service |
| Shared production infrastructure | No accounts, access control, cloud sync, concurrent editing, or approval workflow |
| An audit log | Snapshots are browser-local and trivially editable |

The interface repeats these limits in the hero footer, the estimate method banner, every panel's disclaimer note, and the generated proposal text.

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

### First estimate in five steps

1. **Project identity** — pick a project type, name the job, and enter the site city and ZIP.
2. **Geometry and program** — set length, width, stories, bedrooms, bathrooms, finish reference, and climate zone. Gross area recalculates as you type.
3. **Choose a pricing method** — stay on the *concept allowance model* for early ROM work, or switch to *detailed unit-price line items* for a traceable build-up.
4. **Price it** — in detailed mode, open **Cost book** and enter real local rates, then pull them into **Line items**.
5. **Check and issue** — open **Scope & QA**, resolve the critical gaps, record a review, then export the ledger CSV or the proposal worksheet.

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

Line-item rates are **copied snapshots, not live links**. Changing a cost-book rate does not retroactively change estimates that already consumed it — which is deliberate: it preserves the record of what a given estimate was priced from.

### Cost book

Your regional price memory. Eleven CSI-seeded rows ship unpriced so you can fill in real numbers. Every row carries material rate, labor hours, equipment, waste, source/vendor, as-of date, and a verified flag. Supports **Import CSV**, **Export CSV**, add, and delete.

Live stats track entries, how many are priced at all, and how many carry both a source and a date alongside the verified flag.

> "Verified" is a **user attestation**. Nothing checks it against an external feed.

### Scope & QA

Four free-text scope fields (included, excluded, allowances/owner selections, open risks), a commercial review record, and the full automated preflight panel. This is where an estimate stops being a number and becomes a document someone can hand to another human.

### Takeoffs & schedules

Dimension-derived planning quantities — deliberately labelled as conceptual coordination aids, not measured takeoffs. Four sub-tables plus room allocation cards.

### Timeline

A nine-phase preliminary sequence with week ranges, and an indicative completion window if a target start date is set.

### Proposal / RFQ

A live text preview of the full transmittal — project basis, base cost by division, commercial summary, inclusions, exclusions, allowances, risks, selected RFQ scope, and the full disclaimer. Plus a 15-item RFQ scope checklist. Copy to clipboard or download as `.txt`.

### Revisions

Named snapshots with the total at the moment of saving, delta against the current estimate, and one-click restore. Keeps the last 20 snapshots and the last 40 activity entries.

---

## The two pricing modes

The mode selector in **Project & pricing setup → 03 · Estimate method** is the single most consequential setting in the tool.

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
material   = quantity × materialRate   × (1 + waste%) × marketFactor
labor      = quantity × laborHours     × laborRate   × (1 + burden%)  × marketFactor
equipment  = quantity × equipmentRate                       × marketFactor
extended   = material + labor + equipment
```

A row with `kind = credit` is negated (`−1` sign applied to all three components) and reduces base cost.

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

`taxableBase` is material + equipment of base rows, with credit-row material and equipment subtracted. All rounding is to whole dollars at each step; the rate is formatted to two decimals.

> Verify contingency, burden, overhead, profit, and escalation treatment against your own contract and accounting policy. Different firms apply these to different bases.

---

## Concept allowance model

A transparent ROM formula built from geometry, program, climate zone, finish reference, trench length, and market factor. Nine CSI-level bases:

| CSI | Division | Basis |
| --- | --- | --- |
| `02 30 00` | Civil work & utility trench | Base site allowance + entered trench length + area |
| `06 11 00` | Framing & structural wood | Area, partition layout, bedroom and bathroom count |
| `07 21 00` | Insulation & moisture barrier | Climate-zone rate + area factor |
| `23 00 00` | HVAC / mechanical | Climate-zone rate × ceiling configuration |
| `26 00 00` | Electrical | Area + layout / bathroom allowance |
| `22 00 00` | Plumbing & water heating | Bathroom count + kitchen / heater allowance |
| `08–09` | Openings, drywall & finishes | Finish reference $/SF × 32% |
| `01 41 00` | Permits & fees | Generic planning allowance; jurisdiction fees vary |
| `01 40 00` | Engineering & design | Concept design / engineering allowance |

Each base is split into material and labor using fixed division ratios (`civil` 40/60, `framing` 55/45, `insulation` 60/40, `mep` 45/55, `finishes` 50/50, `permits` and `engineering` material-only), then scaled by the market factor and by `(laborRate ÷ 85) × (1 + burden%)`.

**Existing-condition credits** apply when you tick the corresponding checkbox:

| Condition | Credit |
| --- | --- |
| Existing slab / foundation | area × $14 × market factor |
| Existing framing | area × $18 × market factor |
| Existing utilities available | area × $10 × market factor |

Credits are capped at the positive direct cost so the total can never go negative from credits alone.

**These numbers are placeholders.** They are inherited ROM assumptions with no quote behind them, which is exactly why the preflight panel marks concept mode as a critical gap unconditionally.

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

## Cost book

### Seed rows

Eleven CSI codes ship unpriced as a starting structure:

```
01 40 00  Engineering / design              LS
01 41 00  Permits / jurisdiction fees        LS
02 30 00  Civil work / utility trench        LF
03 30 00  Cast-in-place concrete             CY
06 11 00  Framing & structural wood          SF
07 21 00  Insulation & moisture barrier      SF
08 50 00  Windows / doors                    EA
09 29 00  Drywall / finishes                 SF
22 00 00  Plumbing / water heating           EA
23 00 00  HVAC / mechanical                  EA
26 00 00  Electrical                         SF
```

### CSV columns

Import and export use these headers (case-insensitive on import; `csi` and `description` are required):

```
csi, description, unit, materialRate, laborHours, equipmentRate, wastePct, source, sourceDate, verified
```

`verified` imports only when the value is exactly `true` (case-insensitive). Imports append to the existing book; exports are UTF-8 with a BOM and CRLF line endings so they open cleanly in Excel.

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
2. **Project basis** — dimensions, area, program, finish reference, climate placeholder, market adjustment with source and date, labor rate and burden
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
- Each row shows **Change vs current** — the signed delta between the snapshot total and the live estimate, so you can see the impact of rate or scope changes at a glance.
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

The finish rate is used in concept mode for the finishes division at 32% of gross area. In detailed mode it is carried into the proposal text as the finish reference only.

### Climate zones

| Zone | Label | Insulation factor | HVAC factor | Wall / ceiling placeholders |
| --- | --- | --- | --- | --- |
| `03` | San Diego coast | 7.5 | 3.8 | R-13 / R-30 |
| `04` | San Jose Bay Area | 5.2 | 3.2 | R-15 / R-30 |
| `09` | Los Angeles basin | 7.4 | 3.4 | R-19 / R-30 |
| `12` | Sacramento | 8.5 | 5.0 | R-21 + CI / R-38 |
| `14` | Bakersfield | 9.4 | 5.8 | R-21 + CI / R-38 |

These are **planning placeholders, not energy-code determinations.**

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
| Market index | 0.70–1.50 | 1.00 |
| Base labor rate | $20–$250/hr | $85/hr |
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

Setting labor burden to `0` is correct if your hourly rate is already fully loaded.

---

## Data, storage, and privacy

### What is stored, and where

Everything lives in **`localStorage`** in the current browser profile:

| Key | Contents |
| --- | --- |
| `sovr-build-os-estimator-v5` | Full state, schema version 2 |
| `sovr-build-os-estimator-v4-draft` | Read-only legacy draft, auto-migrated on first load |

Autosave is debounced 180 ms after the last change. The status dot in the top bar reports `Saving locally` → `Draft saved locally`, or **`Browser storage unavailable`** if the write fails — for example in private-browsing modes or with storage disabled.

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
  "version": "5.0",
  "schemaVersion": 2,
  "exportedAt": "2026-10-04T21:00:00.000Z",
  "state": {
    "project":  { /* type, name, client, location, zip, preparedBy, startDate,
                     length, width, stories, bedrooms, bathrooms, layout, finish,
                     zone, heating, ceiling, trench, slab, framing, utilities */ },
    "settings": { /* mode, marketFactor, marketSource, marketDate, marketVerified,
                     laborRate, laborBurden, taxRate, contingency, overhead, profit,
                     escalation, rateMaxAgeDays, pricingReviewed */ },
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

- Merges against defaults, so missing keys are safe
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
| Styling | Inline CSS with custom properties, ~29 lines of minified rules |
| Dependencies | **None** |
| Build step | **None** |
| Formatting | `Intl.NumberFormat` (USD, integer and 2-decimal variants) |
| Dates | `Intl.DateTimeFormat` plus `toLocaleString` |
| Persistence | `localStorage`, debounced 180 ms |
| File I/O | `Blob` + `URL.createObjectURL` + programmatic anchor click |
| CSV | Hand-rolled RFC-4180-style parser handling quotes, escaped quotes, CRLF, and BOM |
| XSS defence | `esc()` helper escaping `& < > " '` on every interpolated value |
| Icons | Inline SVG |
| Sizes | ~654 lines, ~130 KB, of which the CSS is a small fraction |

### Rendering strategy

`renderDynamic()` recomputes the estimate and repaints the header, KPIs, ledger, QA, takeoffs, timeline, proposal, and history on every change. A deliberate exception: **active form inputs are never rebuilt while you type**, so the caret does not jump. Table row repaints are likewise scoped so only the affected row's extended total is touched.

`renderEverything()` — the full repaint including form hydration, cost book, and line items — runs only on initial load, import, reset, template apply, and snapshot restore.

### Constants

All tunable model data is declared at the top of the script: project types, templates, finish rates, climate zones, division material/labor splits, the seed cost book, the RFQ scope options, and defaults. This is the intended extension point.

### Browser support

Any evergreen browser: Chrome / Edge 90+, Firefox 90+, Safari 15+. Relies on ES2020, `Intl`, `localStorage`, `Blob`, and optional Clipboard API (with fallback).

---

## Repository layout

```
NEW_sovr-production-estimator/
├── index.html          # the entire application (v5.0)
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
| Storage keys | `STORAGE_KEY`, `LEGACY_KEY` |
| Project types and templates | `TYPES`, `TYPE_TEMPLATES` |
| Finish rates and labels | `FINISH_RATES`, `FINISH_LABELS` |
| Climate zones | `CLIMATE` |
| Division material/labor splits | `SPLITS` |
| RFQ scope options | `SCOPE_OPTIONS` |
| Seed cost book | `SEED_BOOK` |
| Default project state | `DEFAULTS` |
| Concept calculation | `calcConcept()` |
| Commercial stack | `finishCalculation()` |
| Line-item math | `itemAmounts()` |
| Detailed calculation | `calcDetailed()` |
| Preflight checks | `getQaChecks()` |
| Takeoff derivations | `renderPlanSchedules()` |
| Timeline phases | `renderTimeline()` |
| Proposal text | `proposalText()` |
| Import validation | `validateState()` |

**Practical next steps if you build on this:** add real cost-book content for your market; add a rate-age alert on the cost book itself; split takeoffs into per-sheet quantity exports; add a second currency; add a company letterhead to the proposal output.

---

## Limitations

- **Placeholder concept rates.** The ROM model's numbers are illustrative, unverified, and carry no quote behind them.
- **Dimension-derived takeoffs.** Not measured takeoffs. Openings are not deducted from wall areas; roof pitch, overhangs, and structure are not modelled.
- **No engineering.** No load calculations, no structural design, no energy-code compliance determination, no accessibility analysis.
- **No code determination.** Permit requirements, jurisdiction fees, egress, glazing safety, and insulation values must be confirmed with the actual authority and licensed professionals.
- **Fixed allowance buckets.** The electrical and openings schedules use example protection ratings and conductor sizes, explicitly labelled as examples.
- **Sequential schedule.** Trade overlap is not modelled, so calendar duration will realistically be shorter.
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