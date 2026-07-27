# ETEST SPC Analyzer

**Statistical process control for semiconductor Electrical Test / parametric test data — entirely in your browser.**

[![No install](https://img.shields.io/badge/install-none-3fd0c9)](#quick-start)
[![Single file](https://img.shields.io/badge/build-single%20HTML%20file-5b9bd5)](#whats-in-the-repo)
[![Data stays local](https://img.shields.io/badge/data-never%20leaves%20your%20machine-4fbf7b)](#privacy)
[![License: MIT](https://img.shields.io/badge/license-MIT-a78bfa)](#license)

One HTML file. Open it, drop in a CSV or Excel export from your test floor, and get control charts, capability indices, Western Electric rule detection, wafer maps, lot-to-lot comparison, Cpk forecasting, and a live FMEA — with no server, no upload, and no installation of any kind.

---

## Table of contents

- [Why this exists](#why-this-exists)
- [Quick start](#quick-start)
- [Features](#features)
- [Data format](#data-format)
- [Default spec limits](#default-spec-limits)
- [Walkthrough](#walkthrough)
- [How the numbers are calculated](#how-the-numbers-are-calculated)
- [Western Electric rules](#western-electric-rules)
- [Known behaviour worth understanding](#known-behaviour-worth-understanding)
- [Performance](#performance)
- [Privacy](#privacy)
- [Deploying to GitHub Pages](#deploying-to-github-pages)
- [Browser support](#browser-support)
- [What's in the repo](#whats-in-the-repo)
- [Roadmap](#roadmap)
- [License](#license)

---

## Why this exists

Parametric test data is where a process tells you the truth first. Vt drifts before yield moves, Ioff goes lognormal before the reliability team notices, and a contact resistance shift shows up at ETEST weeks before it shows up in a customer return.

The tools that read that data well are usually locked behind a fab's licensed SPC suite, tied to a database you can't reach from a laptop, or gated by an IT request. This is the opposite: a single file you can email to yourself, open on any machine, and use on data you are not allowed to upload anywhere.

It is built for the engineer who has a CSV in front of them and forty minutes before the meeting.

---

## Quick start

**Option 1 — just open it**

1. Download `etest-spc-analyzer.html`
2. Double-click it

The app boots with a simulated lot already loaded, so every tab works before you touch a file.

**Option 2 — use the hosted version**

Open the GitHub Pages link for this repo. Same file, nothing installed.

**Option 3 — your own data**

1. Open the app
2. **Data & columns** → drop a `.csv`, `.xlsx`, or `.xls` file
3. Confirm the column roles the app guessed
4. Set your spec limits on the **Spec limits** tab
5. Everything else populates

---

## Features

### Data handling
- **CSV, XLSX and XLS upload** — drag and drop or browse. Delimiter is auto-detected (comma, tab, semicolon), quoted fields and embedded commas are handled correctly.
- **Built-in sample lots** — two realistic 300 mm parametric datasets so the tool is useful before you have data in front of you.
- **User-mappable columns** — nothing is hardcoded. Any numeric column can be a parameter; lot, wafer, die X, die Y, timestamp and bin roles are all assignable.
- **Stress-test generator** — one click builds a 52,000-row lot to prove the tool holds up on production-sized exports.

### Control charts
Every chart shades the 1σ, 2σ and 3σ zones, marks the centre line, and overlays your spec limits.

| Chart | Use it for |
|---|---|
| **Individuals (I)** | Per-measurement behaviour, with rule violations marked in red |
| **Moving range (MR)** | Short-term variation and the σ<sub>within</sub> estimate |
| **X̄** | Lot-level control, subgrouped by wafer or by fixed n |
| **R** | Within-subgroup spread |
| **EWMA** | Small sustained shifts, λ adjustable |
| **CUSUM** | Fastest detection of a shift you already suspect, k and h adjustable |
| **Histogram** | Distribution shape against the spec window |

Subgrouping can be **by wafer** — the unit that matters for lot disposition — or by fixed n from 2 to 10.

### Capability
Cp, Cpk, Pp, Ppk, sigma level, theoretical PPM, observed PPM, per-parameter yield, σ<sub>within</sub>, σ<sub>overall</sub>, and a drift ratio that tells you at a glance whether the process is noisy or whether it moved. Colour-coded against the usual 1.00 / 1.33 thresholds, with an all-parameters table underneath and CSV export.

### Western Electric rules
All eight rules, each independently toggleable, each with a live hit count. Violations table gives you point number, lot, wafer, die coordinates, value, distance from centre in sigma, and which rules fired — exportable to CSV for the 8D.

### Wafer map
- **Value heatmap** — continuous colour ramp across the selected parameter
- **Pass / fail** — a die passes when every mapped parameter sits inside its limits
- **Bin code** — coloured by bin, with bin 1 treated as pass

Plus per-wafer yield bars and a **radial signature** plot that separates centre-to-edge process signatures from random defectivity. Hover any die for its coordinates, value, and pass state.

### Before vs after
Load a baseline into slot A and a comparison lot into slot B. Get mean shift, sigma change, ΔCpk, out-of-control point counts, and yield shift per parameter, with a Cpk bar comparison and a distribution overlay. This is the tab for proving a fix worked.

### Trend and roadmap
Cpk is computed per time block, fitted with least squares, and projected forward with a 95% band. The read-out tells you the slope per block and how many blocks until you reach Cpk 1.33 at the current rate. Underneath, a **variation reduction roadmap** shows what σ has to become for Cpk targets of 1.00, 1.33, 1.67 and 2.00, the percentage reduction that implies, and the PPM you would land on.

### FMEA
A ten-row default FMEA seeded with real ETEST failure modes — implant dose drift, silicide starve, gate oxide integrity, probe card degradation, edge die loss, chuck contamination. Severity, occurrence and detection are editable on a 1–10 scale with RPN recalculating live. Add rows, delete rows, sort by RPN, export to CSV.

---

## Data format

The app expects one row per measurement. A typical ETEST export looks like this:

```csv
LOT,WAFER,DIE_X,DIE_Y,SITE,TIMESTAMP,Vt,Idsat,Ioff,Rc,Rs,BVdss,Igate,BIN
L26A0631,W01,-3,7,1,2026-06-02 06:00:02,0.41983,712.44,5.9871,20.412,7.8123,6.3417,0.7215,1
L26A0631,W01,-3,7,2,2026-06-02 06:00:04,0.42107,709.18,5.6042,21.008,7.9014,6.2988,0.6903,1
```

### Column roles

| Role | Required | Notes |
|---|---|---|
| **Parameters** | Yes | Any numeric columns. Select as many as you like. |
| **Lot ID** | No | Enables lot filtering |
| **Wafer ID** | No | Enables wafer filtering, wafer-subgrouped X̄-R, per-wafer yield |
| **Die X / Die Y** | No | Required for the wafer map and radial signature |
| **Timestamp** | No | Used for ordering context |
| **Bin code** | No | Bin 1 is treated as pass for the bin-based yield read-out |

Column names are auto-detected on load — `WAFER`, `DIE_X`, `Vt`, `Idsat` and their common variants are recognised — but every role is reassignable on the **Data & columns** tab. Nothing is locked.

Rows with non-numeric or empty parameter values are skipped for that parameter rather than dropping the whole row.

---

## Default spec limits

Recognised parameter names are pre-loaded with plausible limits so the app is immediately useful. **These are starting points, not your process window** — set your own on the Spec limits tab.

| Parameter | Unit | LSL | USL | Notes |
|---|---|---|---|---|
| Vt | V | 0.390 | 0.450 | Two-sided |
| Idsat | µA/µm | 660 | — | One-sided; higher is better |
| Ioff | nA/µm | — | 12 | One-sided; lower is better |
| Rc | Ohm | — | 24 | One-sided |
| Rs | Ohm/sq | 6.2 | 9.8 | Two-sided |
| BVdss | V | 5.2 | — | One-sided; higher is better |
| Igate | nA | — | 2.5 | One-sided |

A blank limit is treated as genuinely one-sided — Cpk uses the single side rather than assuming symmetry. For an unrecognised parameter, limits are auto-fitted at ±4σ of the loaded data, which is a placeholder and nothing more.

**Auto-fit from data (±4σ)** refits every limit at once. Useful for exploring an unfamiliar dataset, dangerous if you forget you pressed it.

---

## Walkthrough

The fastest way to understand what the tool does is to run the sample lots, which are built to tell a story rather than to look tidy.

**Lot A — baseline.** Eight wafers, 377 die each, three scribe-line PCM sites per die, in randomised touchdown order. Wafers 06–08 carry a Vt-adjust implant dose excursion that pulls Idsat down and Ioff with it, exactly the way a dose error propagates in a real process.

**Lot B — after the fix.** The same process after the dose correction, with tighter wafer-to-wafer control.

Load both, then open **Before vs after**:

| Metric | Lot A | Lot B |
|---|---|---|
| Vt Cpk | 0.99 | 2.48 |
| Vt Ppk | 0.40 | 2.36 |
| Vt mean | 0.432 V | 0.421 V |
| Parametric yield | 82.4% | 100% |

The gap between Cpk 0.99 and Ppk 0.40 in lot A is the whole diagnosis in two numbers: the process is capable within a wafer and incapable across the lot, which means it **moved** rather than being noisy. The wafer map and the per-wafer yield bars then show you exactly which three wafers moved, and the radial signature confirms the excursion is uniform rather than edge-driven.

That is the workflow the tool is shaped around: notice it on the chart, quantify it on capability, locate it on the map, prove the fix on the comparison tab.

---

## How the numbers are calculated

**σ within** — for individuals, MR̄ / 1.128 (d₂ for n = 2). For subgrouped data, R̄ / d₂(n). Cp and Cpk use this, so they describe the process as it could be if it only had its short-term noise.

**σ overall** — the sample standard deviation across every point in the current filter. Pp and Ppk use this, so they describe the process as the customer actually received it, drift included.

**Control limits**

| Chart | Centre | Limits |
|---|---|---|
| I | X̄ | X̄ ± 3σ<sub>within</sub> |
| MR | MR̄ | UCL = 3.267·MR̄, LCL = 0 |
| X̄ | X̿ | X̿ ± A₂R̄ |
| R | R̄ | D₃R̄ and D₄R̄ |

Constants come from the standard d₂ / A₂ / D₃ / D₄ tables for n = 2 to 25.

**EWMA** — zᵢ = λxᵢ + (1−λ)zᵢ₋₁ with limits X̄ ± Lσ√(λ/(2−λ)·(1−(1−λ)²ⁱ)). The limits widen out from the start-up value rather than being flat, which is the correct form and the one most spreadsheet implementations get wrong.

**CUSUM** — standardised tabular form: SH = max(0, SH₋₁ + zᵢ − k), SL = max(0, SL₋₁ − zᵢ − k), signalling past ±h. Defaults k = 0.5 and h = 5 detect a 1σ shift in roughly ten points.

**PPM and sigma level** — theoretical PPM integrates the normal tails beyond each limit using σ<sub>overall</sub>. Observed PPM counts real out-of-spec rows, which is the honest number when the distribution is skewed. Sigma level is Z<sub>bench</sub> + 1.5, following the usual shift convention.

**Yield** — a die passes when every mapped parameter is inside its limits. If a bin column is mapped, bin 1 is treated as pass and the bin-based yield is reported alongside.

**Cpk projection** — Cpk is computed per time block, fitted by least squares, and extended forward with a 95% interval from the residual standard error. The roadmap inverts the Cpk equation to find the σ required for each target, holding the mean where it is.

---

## Western Electric rules

| # | Rule | Detects |
|---|---|---|
| 1 | One point beyond 3σ | Gross excursion, single-point failure |
| 2 | Nine consecutive points on the same side of centre | Sustained mean shift |
| 3 | Six consecutive points rising or falling | Drift, tool wear, bath depletion |
| 4 | Fourteen consecutive points alternating | Over-adjustment, two-stream mixing |
| 5 | Two of three consecutive beyond 2σ, same side | Sudden moderate shift |
| 6 | Four of five consecutive beyond 1σ, same side | Small sustained shift |
| 7 | Fifteen consecutive within ±1σ | Stratification, or limits that no longer match the process |
| 8 | Eight consecutive beyond 1σ, either side | Mixture of two distributions |

Each rule can be switched off independently, and the hit counts update live so you can see what a rule is costing you before you decide to keep it.

---

## Known behaviour worth understanding

**The run rules over-fire on per-die data, and that is not a bug.** Rules 2, 6 and 8 were written for charts of 25 to 50 subgroup points. Run them across 9,000 individual die measurements and a run of nine on one side becomes common by construction. It gets more common still when the parameter is skewed — Ioff and Igate are lognormal by nature, and Rc and Idsat pick up skew from their radial component, so more than half their points sit on one side of the mean and long runs follow.

Rule 1 stays trustworthy at any sample size. For lot-level control, set the subgroup selector to **By wafer** and read the X̄ chart, where one point per wafer is the unit the rules were actually designed for.

**Capability assumes normality.** Cp, Cpk, Pp, Ppk and theoretical PPM all integrate a normal distribution. For Ioff and Igate, compare theoretical PPM against observed PPM — a wide gap tells you the normal assumption is failing and the observed number is the one to quote.

**The excursion in sample lot A is deliberate.** If the rules tab looks alarming on first load, that is because lot A is genuinely out of control. Load lot B to see what the same tool says about a healthy process.

---

## Performance

Tested on the built-in 52,026-row stress lot:

| Operation | Time |
|---|---|
| Generate, parse and index 52k rows | ~1.3 s |
| Full SPC and rule analysis, one parameter | ~170 ms |
| All-parameters capability table | ~1.1 s |
| Chart redraw on filter change | under 100 ms |

Data is held in typed arrays in a column-oriented store. Statistics are computed over **every** row; charts plot a decimated subset — 2,000, 4,000, 8,000 or all points, selectable — so scrolling and hovering stay smooth on large lots. When decimation is active the app says so above the I chart, so you always know whether you are looking at all of it.

---

## Privacy

This matters more than any feature in the list.

- Your file is read by the browser's `FileReader` and parsed in the tab. It is never transmitted.
- There are no `fetch` calls, no XHR, no analytics, no telemetry, no error reporting.
- Nothing is written to `localStorage`, `sessionStorage`, cookies, or IndexedDB. Close the tab and it is gone.
- The only network activity is loading two libraries from a CDN when the page first opens.

**For a genuinely air-gapped machine**, download `chart.umd.min.js` and `xlsx.full.min.js`, save them next to the HTML file, and change the two `<script src="...">` tags to point at the local copies. The app then works with no network at all.

Your fab's data is your fab's data. Verify all of the above yourself — it is one file, and you can read it.

---

## Deploying to GitHub Pages

Free public hosting, no build step.

1. Create a repository — for example `etest-spc-analyzer`
2. Upload `etest-spc-analyzer.html` and this `README.md`
3. Rename the HTML file to `index.html`, or add a small `index.html` that redirects to it
4. Go to **Settings → Pages**
5. Under **Source**, choose **Deploy from a branch**
6. Select branch `main` and folder `/ (root)`, then **Save**
7. Wait about a minute

Your tool is then live at:

```
https://ikoghoddds-bit.github.io/etest-spc-analyzer/
```

Anyone with the link can use it, and their data still never leaves their machine — GitHub Pages serves the file and nothing else. To update, replace the file and commit.

> **One caution.** A GitHub Pages site is public. Publish the tool, never a dataset. Keep sample and customer data out of the repository entirely.

---

## Browser support

Chrome, Edge, Firefox and Safari, current versions. Requires a browser with `FileReader`, typed arrays, and canvas — anything from the last several years qualifies. Works offline once loaded, apart from the initial CDN fetch. Responsive down to tablet width; the wafer map is most usable on a real screen.

---

## What's in the repo

```
etest-spc-analyzer.html    the entire application — HTML, CSS and JavaScript in one file
README.md                  this document
```

That is the whole repository. No `package.json`, no `node_modules`, no bundler, no config. Two CDN dependencies, both loaded by `<script>` tag:

- [Chart.js 4.4.1](https://www.chartjs.org/) — all charts
- [SheetJS 0.18.5](https://sheetjs.com/) — XLSX and XLS parsing

CSV parsing, all statistics, all SPC logic, and the wafer map renderer are written from scratch in the file with no dependencies.

---

## Roadmap

- Advanced Packaging companion app — bump height, warpage, die shift, wire bond pull, ball shear, solder void, RDL, TSV, coplanarity, underfill
- Gauge R&R module
- Multi-lot trend view across more than two slots
- PDF report export
- Non-normal capability using Box-Cox or Johnson transforms, for Ioff and Igate

Ideas and bug reports are welcome in Issues.

---

## Author

**Joseph Ikogho** — Process Engineer, semiconductor. Built for engineers who need to answer a parametric question before the meeting starts.

---

## License

MIT. Use it, fork it, take it into your fab, change it to match your process. No warranty — verify the statistics against your own reference data before you disposition material on them.
