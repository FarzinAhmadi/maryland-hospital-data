# Changelog

## 0.1.3 (2026-11-10)

Corrections to the hospital financial tables and the rate-setting keys, and every
table now built by the tested pipeline. Published figures change in the financial,
disclosure and rate-setting tables. The hospital crosswalk is byte-for-byte
identical to 0.1.2. The four revenue and volume tables and the rate center reference
are identical to 0.1.2 for FY2017 to FY2025 and change in FY2026 only, which is
rebuilt from HSCRC's re-released FY2026 file (below).

### Corrections

- **Tables running into each other in the financial reports.** A table without a
  `FISCAL YEAR ENDING` header (the specialty hospital tables headed `Year Ending`,
  and one header printed as `FISCAL YEAR ENDING3`) was read into the block of the
  hospital printed before it. WVU Medicine Garrett Regional carried the ALL
  SPECIALTY HOSPITALS totals for FY2022 to FY2024, Western Maryland Regional and
  UPMC Western Maryland carried specialty hospitals' figures, and Greater
  Baltimore Medical Center carried Holy Cross Germantown's. These were the cause of
  the seven rows in 0.1.2 that failed net operating revenue = net patient revenue +
  other operating revenue or the gross revenue identity. A block now also ends when
  a field repeats.
- **The within-report rule.** 0.1.2 published the larger of two values field by
  field, which mixed tables in one row. A report's first block for an entity now
  supplies the whole row. Cells where a report gives two values fall from 400 to 37,
  all UPMC Western Maryland FY2020, which the reports print for two periods (June
  2020 and December 2020) after its change of fiscal year end; one period is kept
  per hospital-year.
- **Section headings with a stray figure** ("Net Operating Revenue(NOR)  0" in the
  FY2012 report) were not recognized, so the lines under them were filed as other
  operating revenue. ALL ACUTE HOSPITALS FY2010 total other operating revenue was $12.6
  billion in 0.1.2; it is $497 million.
- **A missing column label.** Where a header labels fewer columns than it prints
  (TidalHealth McCready in the FY2021 report, University UMCC in the FY2013 report),
  values are placed by the dashed rule under the header rather than by order.
- **University Specialty Hospital** was counted as the ALL SPECIALTY HOSPITALS
  roll-up because its name contains "specialty". It is now its own row.
- **A name read from the wrong line.** The FY2020 report's Adventist Behavioral
  Health Rockville block was published under the name "MERGED WITH WASHINGTON".
- **Name links.** A name-match score from 0.62 to 0.85 is no longer applied on its
  own. Thirteen links in 0.1.2 change, 11 of them from scores between 0.62 and
  0.85: eight names are relinked, four are left unlinked and one misread name is
  corrected. Among the wrong links were Maryland General Hospital to
  Atlantic General, Washington County Hospital to White Oak, Johns Hopkins Suburban
  to the Johns Hopkins Hospital, and UM Upper Chesapeake Medical Center Aberdeen to
  Upper Chesapeake rather than the former Harford Memorial. Every link not made by a
  score of 0.85 or more is now a recorded decision in
  `reference/name_overrides.csv` (71 rows), each citing its evidence: the
  crosswalk's own names, identical figures printed under two names in two reports,
  or regulated revenue matching the revenue panel. See
  `reference/NAME_RESOLUTION.md`. Eight printed names remain unlinked.
- **One row per hospital-year.** `financial_hospital_annual` carried a hospital
  renamed between reports once under each name (200 hospital-years in 0.1.2). The
  most recent report's row now wins, as the restatement rule already did for one
  name; 221 rows are folded in and their names kept in `name_variants`. The table
  can now be summed by year. 1,332 rows become 1,107.
- **The ALL SPECIALTY HOSPITALS roll-up** is no longer published in the hospital
  table, where it was counted as a facility.
- **Rate-setting keys.** `approved_gbr_targets` and `quality_adjustments` gain
  `hosp_num`. Their `hospid` is the CMS Certification Number less 210000, which
  differs from `hosp_num` for five facilities, and four of those ids (56, 57, 58,
  64) had no crosswalk row. Every row now maps.
- **Disclosure table.** The second parser did not recognize "Net Operating Revenue
  (NOR)" as a heading, so in 0.1.2 the `other_operating_revenue_*` columns held net
  operating revenue for most rows. Three `net_operating_revenue_nor_*` columns are
  added and the other operating revenue columns corrected. Links use the same
  decisions as the main table: 589 hospital-years (540 in 0.1.2).

### FY2026 revenue and volume file

- HSCRC withdrew the FY2026 file that 0.1.2 was built on
  (`hscrc_revenue_vol_FY26Julythroughjun2026_080626.xlsx`, posted August 2026) and on
  9 October 2026 posted `hscrc_revenue_vol_FY26Julythroughjun2026_100626.xlsx` in its
  place. FY2026 is rebuilt from the new file. Statewide FY2026 regulated revenue moves from
  $24,708.9 million to $24,706.8 million (-0.01%). Three hospitals' annual revenue moves
  by more than 0.1% (hospital 49 -0.84%, 27 +0.46%, 5034 +0.33%) and none by more than
  1%. The monthly table has 308,195 rows (308,207 in 0.1.2): 22 rows are gone and 10
  are new, all in FY2026.
- HSCRC revises the current year's file without notice, and its page says the data
  are subject to change at any time. It posted another FY2026 version on 9 November
  2026 (`hscrc_revenue_vol_FY26Julythroughjun2026_110926.xlsx`). This release stays on
  `hscrc_revenue_vol_FY26Julythroughjun2026_100626.xlsx` (SHA-256 begins
  `7fcddcfe25044e11`); later revisions will go into the next version.
- The re-released file has no `MCID` or `SER_TYPE` column. FY2026 `ccn` is carried from
  each hospital's most recent earlier year and `service_type` from the rate center's
  value in the other years. Both reproduce the August file's values in every row the
  two files share.

### New columns

`hosp_num` and `source_sheet` in the two rate-setting tables (`source_file` now
names the HSCRC workbook rather than an intermediate file); `non_operating_revenue`
and `non_operating_expenses` in the two financial tables, where a report prints
them; the three disclosure columns above.

`fy_end_month` in `financial_hospital_annual`: the month in which the hospital's
reporting year ends, read off the printed period label (850 June, 121 December,
16 August; blank in 120 rows where the label prints no month). `fiscal_year` in
the financial tables is the year in which that reporting year ends, which is not
always the Maryland fiscal year. 0.1.2's data dictionary described it as the
Maryland fiscal year ending 30 June; that was wrong for the hospitals on a
December or August year, and a join to the revenue panel on `fiscal_year` alone
mixed periods for them. The dictionary now says so.

### Build

- `approved_gbr_targets`, `quality_adjustments` and `disclosure_hospital_annual`
  are built by `src/` from source files listed in `src/mdhosp/config.py`, instead of
  by separate scripts. They reproduce the 0.1.2 values except where corrected above.
- `reference/DATA_DICTIONARY.md` now reads every row of each table. It read the
  first 60,000 rows, so the monthly table's FY2026-only Medicaid columns and its
  empty `ccn` for two facilities were misreported.
- Tests: 46, up from 29, including the accounting identities, one row per
  hospital-year, every rate-setting row mapping to the crosswalk, linked rows
  agreeing with the revenue panel, and the two extractions of the disclosure
  reports agreeing.
- `make repro-data` builds the dataset twice and compares every table's hash.
- The archived build code builds the dataset only. The old dashboard builder
  (`build_site.py`) and its two tests are not part of it; the public tracker is
  built separately from the released tables.
- `requirements.txt` pins pdfplumber and pdfminer.six. The build was run with
  pdftotext 22.02.

## 0.1.2 — 2026-09-24

Documentation release. The data are byte-for-byte identical to 0.1.1; nothing was
recomputed.

- Adds `.zenodo.json`, so the archived record carries the right metadata: dataset
  rather than software, ORCID, CC BY 4.0, keywords, and a link to the tracker.
- This is the first release archived at Zenodo, and the first to carry a DOI.

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

Reproducibility fixes. **No published figure changes**, with one exception noted
below. Everything here was found by rebuilding v0.1.0 from source and comparing.

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
