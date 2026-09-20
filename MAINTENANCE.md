# Maintenance

The single biggest risk to this project is not a bug — it is the dataset quietly
going stale while people keep citing it. Everything here exists to make staleness
**loud** and refreshing **cheap**.

## The quarterly loop (about 30 minutes)

`monitor-hscrc` runs on the 1st of February, May, August and November and opens a
GitHub issue when HSCRC posts a file the project doesn't have. When that issue
appears:

1. **Read the issue.** New files are grouped by what they feed, so you can see at
   a glance whether it's a main-panel rebuild or a v0.2 source.
2. **Download** the relevant file into `data/raw/`.
3. **Update `src/mdhosp/config.py`** — `REVENUE_VOLUME_FILES` or `FINANCIAL_PDFS`.
   Filenames change every release; this is the only hand edit required.
4. **`make all`** — rebuilds datasets, dashboard and data dictionary.
5. **Read `data/processed/BUILD_REPORT.md`.** New validation warnings mean HSCRC
   changed something structural. Investigate before committing.
6. **`make test`**, then commit. Bump the version in `config.py` and add a
   `CHANGELOG.md` entry.
7. **`make accept`** to clear the issue from the monitor's baseline.
8. Mint a new Zenodo version so the DOI resolves to the current release.

## Why the monitor doesn't auto-download

HSCRC republishes the same fiscal year under new filenames as figures move from
preliminary to final — `..._FY24...Final.xlsx` versus `..._FY25..._091525.xlsx`.
An auto-ingest would cheerfully overwrite a final file with a preliminary one and
no one would notice. A human spending two minutes deciding which file is real is
the correct amount of friction.

## The guards

| Guard | What it catches | Where |
|---|---|---|
| `make check` | The shipped dashboard no longer matches what the pipeline produces | `build` workflow, every PR |
| `make test` | Aggregates that don't reconcile, orphaned `hosp_num`, shares that aren't fractions, derived margins that disagree with HSCRC's printed values | `build` workflow |
| `make repro` | The build reading anything outside `data/processed/` -- the clock, the environment, an unpinned dependency | `build` workflow, every PR |
| `make fresh` | Data more than one complete fiscal year behind, or a build older than ~2 quarters | `build` workflow (non-blocking) + local |
| `make monitor` | New HSCRC files | quarterly cron |

`make check` exists specifically because the previous version of this project
shipped a dashboard whose numbers its own build script no longer generated. That
must never happen again, so it is enforced in CI rather than by discipline.

For that to work, the build must be a pure function of the data in
`data/processed/`. It was not: `build_site.py` stamped `date.today()` into the
page, so `make check` failed every day after the release and would have trained
everyone to ignore it. Nothing in the build may read the clock, the environment,
or an unpinned dependency. `make repro` builds twice and compares, so a
regression shows up as itself rather than as a confusing `make check` failure.

## First-time setup

The monitor ships with an empty baseline, so its first run will report every file
on the watched pages as new. Run `make accept` once to record the current state,
then commit `reference/source_inventory.json`.

## Expected cadence

| What | When | Effort |
|---|---|---|
| Revenue & volume, final full year | ~Sept–Oct for the FY that ended 30 June | 30 min |
| Revenue & volume, partial-year updates | quarterly | 20 min |
| Financial Condition Report | ~May, for the FY that ended 23 months earlier | 30 min |
| CPI-U, for `reference/cpi_u_annual.csv` | ~mid January, for the calendar year just ended | 5 min |
| Casemix weights / ECMAD | annually, by rate year | v0.2 |
| Quality program policies | continuously, 2+ years ahead | v0.2 |

Roughly two focused hours a year, plus whatever you choose to add.

## Where the build can run

`build_all.py` deletes `data/processed/revenue_volume_monthly.csv` after gzipping it.
In a cloud-synchronised working copy (OneDrive, Dropbox) that delete can be blocked,
and the build stops partway leaving a 74 MB intermediate behind. It is also wasteful
to sync that intermediate on every run. Build in a local scratch copy and copy the
finished tables back:

```bash
cp -R src tests reference geocode data docs Makefile CITATION.cff ~/gbrbuild/
cd ~/gbrbuild && rm -f data/processed/* && python3 src/build_all.py
cp ~/gbrbuild/data/processed/* /path/to/maryland-hospital-data/data/processed/
```

## Tables not built by `build_all.py`

Three published tables come from `tools/` in the research directory and are copied in:

| table | source |
|---|---|
| `quality_adjustments.csv` | `processed_data/hscrc_annual_update/quality_adjustments_by_hospital_rateyear.csv` |
| `approved_gbr_targets.csv` | `processed_data/hscrc_rate_orders/approved_gbr_targets_by_hospital_rateyear.csv` |
| `disclosure_hospital_annual.csv` | `processed_data/hscrc_disclosure_reports/disclosure_hospital_annual_linked.csv` |

## The restatement rule

Each Financial Condition Report restates the prior two fiscal years, so one
hospital-year can appear in three reports with three values. The rule, asserted in
`tests/`: sort the long table by `report_fy`, `source_file`, `period_label`, `entity`,
`field`, `value` with a **stable** sort and take the last. Later reports correct
earlier ones, so the most recent restatement wins; the remaining keys only break ties
within a single report and exist to make the ordering total. `aggfunc="last"` skips
nulls, which is how FY2008 — whose own report is a scanned image — is recovered from
the FY2009 report. Do not reorder these keys without re-reading that test.

## Rate year comes from the document, not the folder

`tools/build_quality_adjustments.py` reads the rate year from the `ry####__` filename
prefix, which is the folder the workbook was filed in. HSCRC does re-file a prior
year's workbook in the current year's package. Where the document's own name states a
rate year, that wins, and the override is printed. Note that `\b` does not work in
that regex: `_` is a word character and the failing filename is
`final_ry_2026_qbr_revenue_adjustments`.

## The recorded source-check date

`reference/source_check.json` holds the date the HSCRC and CMS source pages were last
reviewed by hand. The tracker prints it. It is deliberately not the build date:
rebuilding the page proves nothing about whether HSCRC has posted anything since.
Update it whenever you check, whether or not anything was found.
