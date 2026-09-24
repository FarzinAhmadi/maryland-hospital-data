# Changelog

## 0.1.1 — 2026-09-18

First release built on a complete FY2026. Adds a fiscal year of revenue and volume,
a fiscal year of hospital finances, and one new review table; corrects two extraction
faults. Published figures change.

### New data

- **FY2026 Revenue and Volume, full twelve months** (HSCRC file of 2026-08-07)
  replaces the nine month file of 2026-05-08. `revenue_volume_monthly` grows from
  300,730 to 308,207 rows and FY2026 becomes a complete fiscal year, so every series
  on the tracker now ends there rather than at FY2025.
- **FY2025 Hospital Financial Condition Report** added as a twenty third report.
  `financial_statewide_annual` extends to FY2025 (20 to 21 rows) and
  `financial_hospital_annual` grows from 1,274 to 1,332 rows.
- **`REVIEW_conflicting_report_rows`** (400 rows): hospital-year-field cells where a
  single report gives two different values, normally a facility appearing both in its
  own detail block and in a later summary table. The larger value is published; the
  table exists so the choice can be checked against the PDF.

### Corrections

- **Quality adjustments took the rate year from the enclosing folder.** HSCRC re-filed
  `FINAL RY 2026 QBR Revenue Adjustments` in the RY2027 package and posted no RY2027
  QBR file, so forty rows of RY2026 adjustments were published as RY2027. The rate
  year now comes from the document's own name where it states one.
  `quality_adjustments` falls from 1,845 to 1,805 rows.
- **The restatement merge is deterministic in a shipped build for the first time.**
  The rule (most recent restatement wins, with a total sort order to break ties) was
  written after the v0.1.0 release; v0.1.0 shipped nine rows decided by iteration
  order.

### Changed by the source, not by us

- **Kaiser Permanente is no longer broken out.** The partial year FY2026 file carried
  `KP_*` columns; the final FY2026 file does not. Six Kaiser columns therefore leave
  `revenue_volume_monthly` (43 to 37), and two leave `statewide_annual` and
  `hospital_annual`. Medicaid (`MCAID_*`) is unaffected.

### Earlier work in this version

Reproducibility fixes. FY2026 is now a complete year, from HSCRC's final FY2026 Revenue and Volume file: statewide regulated revenue for FY2026 is $24.7 billion over twelve months, where v0.1.0 carried $18.4 billion over nine. Every other change below is a reproducibility fix and changes no published figure, with the one hash exception noted.

### The build is now a pure function of the data

- `build_site.py` stamped `date.today()` into the dashboard, so regenerating it
  changed a byte every day and `make check` — the CI guard that exists to catch a
  dashboard drifting from its data — failed on every run after the release date.
  The build date now comes from `MANIFEST.json`.
- `build_all.py` honours `SOURCE_DATE_EPOCH`, so a past release can be rebuilt
  byte-for-byte.
- Dependencies are pinned. Unpinned pandas decided whether an empty rate-center
  column serialised as `NaN` or `null`, which changed ~30 values in the published
  dashboard payload with no change to the data.
- New `make repro`: builds twice, byte-compares, and runs in CI ahead of
  `make check`.

### The financial merge is deterministic

Each Financial Condition Report restates the prior two fiscal years, so a
hospital-year exists in two or three reports with different values. The merge
intended to take the most recent restatement but sorted with pandas' default
unstable sort, so rows sharing a `report_fy` were ordered arbitrarily and nine of
1,274 rows in `financial_hospital_annual` changed value between runs — one by a
factor of five. The rule is now explicit (`financial_pdf.SORT_KEYS`, stable
sort), documented, and asserted in tests. A field the newest report leaves blank
still falls back to the most recent report that filled it, which is how FY2008 is
recovered from the FY2009 report.

That first fix was necessary but not sufficient, and testing it against the real
reports rather than a synthetic case showed why. A hospital can be matched twice
in the *same* report, once in its own detail block and once in a summary table
later on, so `(report_fy, source_file, period_label, entity, field)` is not
unique and a stable sort still left the winner to whatever order the extractor
emitted. The ordering now ends on `field` and `value`, which makes it total.

Those self-conflicts are a data question, not just an ordering one, so they are
published rather than resolved quietly: `REVIEW_conflicting_report_rows.csv`
lists every hospital-year-field cell where one report gives two values, with
the value that reached the published table, and the build report notes the
count. On the current sources this is Greater Baltimore Medical Center in the
FY2015 report and UPMC Western Maryland in the FY2021 and FY2022 reports, both
of which are in the set of rows the 4 September reproduction flagged.

`financial_hospital_annual` and `financial_statewide_annual` gain
`latest_report_fy`, the newest report contributing to each row.

### Manifest hashes are over content, not containers

gzip stores an mtime in its header, so `revenue_volume_monthly.csv.gz` had a
different file hash on every build and its manifest entry could never match.
Hashes are now taken over the uncompressed CSV, and gzip is written with
`mtime=0`. **This is the one changed value**: that table's `sha256` moves from
`8eec0facf56d57be` to `518282f97dba6f73`. The data is byte-identical; only what
is hashed changed. The other nine tables are unaffected.

### The dashboard payload is valid JSON

`json.dumps` emits a bare `NaN` for `float('nan')`. That is legal JavaScript, so
the page rendered, but not legal JSON, so anyone extracting `DATA` to reuse it
hit a parse error. NaN is converted to null and `allow_nan=False` makes any
future leak fail loudly.

### The tracker lives in the repository

The public tracker's three build scripts sat outside the repo and read an
absolute path into a mounted drive, so they ran in exactly one place. They are
now `src/build_public_*.py` with the geocoding pipeline in `geocode/`, wired up as
`make tracker`. `build_public_page.py` refuses to build when no hospital has
projected coordinates, instead of silently shipping an empty map.

### Real-dollar companions

`src/mdhosp/deflate.py` and `src/build_claims.py` state any dollar claim both
ways — nominal and constant-dollar. **The published tables remain nominal and
unchanged**; this is for quoting them. CPI-U annual averages are in
`reference/cpi_u_annual.csv` and ship marked `verified=no`; the code refuses to
produce a real figure until they are checked against BLS.

### Public tracker: map and table

The map's color coding did not work. Fills were written as inline attributes from
custom properties read back through `getComputedStyle`, which returns an empty
string often enough to leave every circle on the stylesheet default. Fills are
now the CSS classes the stylesheet already defined and nothing had been using.
A light or dark switch now repaints without script.

Alongside it, three data defects the color coding was hiding:

- `public_data.json` carried bare `NaN` for three inpatient only rehabilitation
  hospitals with no outpatient revenue. NaN passes a `!= null` check, so they
  were sorted into the outpatient share quintiles and drawn in the lowest class,
  as though they had the least outpatient care in the state. A facility that
  reports in one setting only now records a true zero for the other where the
  reported side accounts for all of its regulated revenue, and both tracker
  scripts serialise with `allow_nan=False`.
- Eight outpatient only facilities, including four freestanding emergency
  departments, now read 100.0% rather than 100.1% to 100.6%: the share had been
  derived by dividing two figures already rounded to $0.1M.
- Figure 1's caption looked up `hospitals[0]` and a hardcoded percentage. Both
  comparison figures are now looked up by name.

The four map controls are now one query rather than four independent filters.
Choosing a jurisdiction lists only the systems operating there and vice versa,
each with a count, and a selection that the other control invalidates is
cleared. The free text search is a proper combobox following the WAI-ARIA
pattern, listing the hospitals the current filters leave; choosing one selects it
and moves the map to its neighbourhood. Clicking a jurisdiction on the map
filters to it, so the map is an input as well as an output.

The map gained a scale bar in kilometres, drawn from the projection's own metres
per unit, which `build_public_geo.py` now exports; a jurisdiction label; and
per jurisdiction figures on hover. The legend states the class boundaries and the
classification method instead of only the endpoints, and counts the hospitals
that do not report the selected measure instead of hiding them in the lowest
class.

The map and the table are now a designed pair rather than two overlapping
listings: the map carries two encodings and answers where, the table carries the
same filtered set at full precision and answers how much, and every measure the
map can be colored by appears as a table column, one at a time, marked, so a
color can always be resolved to a number.

Two layout defects, both of the same kind, where content was silently clipped
rather than wrapped: the hospital search input lost its placeholder below about
1150px, and the table's tenth column disappeared because a heading's minimum
width pushed the table past a wrapper that hides overflow. Controls now stack
before they squeeze, every table column carries an explicit width summing to
100%, and headings wrap.

### Tests

14 → 26, covering every defect above.

## 0.1.0 — 2026-08-19

First release.

- Hospital crosswalk: 66 facilities, FY2017–FY2026, keyed on `hosp_num` with CCN,
  every observed name variant, system, county, and CMS type/ownership.
- Revenue & volume panel: 300,730 rows, FY2017–FY2026, hospital × month × rate
  center × setting, with Medicare (FFS/MA), Medicaid and Kaiser splits where HSCRC
  reports them, and Maryland/non-Maryland residency throughout.
- Financial condition: FY2005–FY2024 statewide, FY2007–FY2024 hospital-level,
  extracted from 22 HSCRC PDFs.
- Rate center reference: 82 codes with official names and units, sourced to HSCRC
  documents; four codes marked UNCONFIRMED rather than guessed.
- Dashboard: single self-contained HTML file, no external requests.

Known gaps are listed in README.md and generated into
`data/processed/BUILD_REPORT.md` on every build.
