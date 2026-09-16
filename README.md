# Maryland Hospital Global Budget Revenue

A tidy, versioned, machine readable compilation of the Maryland Health Services
Cost Review Commission's public reports on the state's all payer hospital global
budget system. Thirteen tables, 306,852 rows, covering FY2005 to FY2026.

Maryland is the only state that caps what each hospital may collect in a year.
HSCRC publishes the underlying numbers, but across spreadsheets, PDFs and rate
year files that do not share a key or a naming convention. This repository puts
them in one place, joined on a stable hospital identifier, with every column
defined and every source document named.

**Tracker built on this data:** https://farzinahmadi.github.io/maryland-gbr-tracker/

## Get the data

Download the whole release as one archive from the
[Releases](https://github.com/farzinahmadi/maryland-hospital-data/releases/latest)
page, or read a single table straight from the repository:

```python
import pandas as pd
url = ("https://raw.githubusercontent.com/farzinahmadi/maryland-hospital-data/"
       "main/data/hospital_annual.csv")
df = pd.read_csv(url)
```

```r
df <- read.csv(paste0("https://raw.githubusercontent.com/farzinahmadi/",
                      "maryland-hospital-data/main/data/hospital_annual.csv"))
```

The monthly file is gzipped. `pandas.read_csv` and R's `read.csv` both read it
directly from the compressed file.

## What is in each table

**Revenue and volume.** What each hospital collected, as reported to HSCRC.

| file | contents | rows | columns |
|---|---|---:|---:|
| `statewide_annual.csv` | Statewide totals, one row per fiscal year | 10 | 31 |
| `hospital_annual.csv` | One row per hospital per fiscal year | 598 | 30 |
| `hospital_annual_by_setting.csv` | Split into inpatient and outpatient | 1,129 | 27 |
| `revenue_volume_monthly.csv.gz` | The underlying monthly filings, by rate center | 300,730 | 43 |

**Approved budgets and adjustments.** What each hospital was permitted to collect, and the programs that moved it.

| file | contents | rows | columns |
|---|---|---:|---:|
| `approved_gbr_targets.csv` | Approved mid year and year end targets, by rate year | 461 | 7 |
| `quality_adjustments.csv` | MHAC, PAU, RRIP and QBR adjustments, by hospital and rate year | 1,845 | 9 |

**Hospital finances.** Margins and the statements behind them, from the published reports.

| file | contents | rows | columns |
|---|---|---:|---:|
| `financial_hospital_annual.csv` | Hospital by fiscal year, from the Financial Condition Reports | 1,274 | 51 |
| `financial_statewide_annual.csv` | Statewide totals from the same reports | 20 | 46 |
| `disclosure_hospital_annual.csv` | Earlier years, extracted from the Disclosure Reports | 540 | 56 |

**Reference and provenance.** What you need to join the tables and to check the extraction.

| file | contents | rows | columns |
|---|---|---:|---:|
| `hospital_crosswalk.csv` | Names, CCN, jurisdiction and system for every facility | 66 | 18 |
| `rate_center_reference.csv` | Rate center service code definitions | 82 | 11 |
| `financial_extraction_log.csv` | Which report yielded which table, and which did not | 22 | 7 |
| `REVIEW_unresolved_hospital_names.csv` | Names that could not be resolved, published unresolved | 75 | 7 |

`MANIFEST.json` carries the row count, column count, byte size and full SHA-256
of every file in the release.

## Joining the tables

Join on `hosp_num`, never on a name. Fifty three of the 66 facilities appear
under more than one name across FY2017 to FY2026. `hospital_crosswalk.csv` maps
`hosp_num` to the CMS Certification Number, every observed name variant, the
jurisdiction, the system and the CMS facility attributes. The two rate setting
tables use `hospid`, which is the same numbering.

`reference/DATA_DICTIONARY.md` defines every column in every table, with the
type, the non-null rate and an example value read off the shipped data.
`reference/source_manifest.md` names every HSCRC document the tables were built
from and where to download it.

## Things to know before you use it

- **FY2026 is nine months**, not a full year. Do not compare it directly with a
  complete fiscal year.
- **Regulated revenue is not total health care spending.** It excludes physician
  services, most post acute care and everything outside the rate regulated
  hospital setting.
- **A hospital's location is not its patients' residence.** In FY2025 the share of
  a hospital's revenue coming from Maryland residents ranged from 71 percent to 99
  percent across the 61 hospitals reporting, so hospital revenue should not be read
  as spending by the population of the county the hospital sits in.
- **Volume units differ by rate center.** The `volume` column is denominated in
  each rate center's own unit of rate: patient days, RVUs, minutes, procedures,
  or cost denominated equivalent inpatient admissions. It is not comparable
  across rate centers.
- **`hospital_crosswalk` has no status field.** Leaving the data is not the same
  as closing; at least one facility converted from an inpatient hospital to a
  freestanding one and its revenue fell accordingly.

## Known issues

These are open and are recorded here rather than quietly fixed:

1. **The financial merge is not yet deterministic.** Each Financial Condition
   Report restates the prior two fiscal years, so a hospital year can appear in
   two or three reports with different values. Nine rows of 1,274 in
   `financial_hospital_annual` are decided by iteration order rather than by a
   rule. Scheduled for 0.1.1, which is what a DOI will be minted against.
2. **`disclosure_hospital_annual` is provisional.** It comes from PDF extraction
   of the older Disclosure Reports. Its column names are the reports' own section
   and row labels, flattened, and 36 of them have not been reconciled line by
   line against the source. Values are as printed. The data dictionary marks
   them.
3. **Medicare volume exceeds total volume in some rows.** Between 100 and 243
   rows a year. These are as published by HSCRC and are retained with a flag
   rather than corrected.
4. **Four of 22 financial PDFs did not parse and one is a scanned image.**
   `financial_extraction_log.csv` records the outcome for every report.
5. **The gzipped table's file hash changes between builds** because gzip stores a
   timestamp in its header. `MANIFEST.json` also carries the SHA-256 of the
   uncompressed stream, which is stable.

## Provenance

HSCRC is the system of record. Every figure here is derived from files HSCRC and
CMS publish; nothing is modelled, imputed or estimated. `BUILD_REPORT.md` carries
the validation output of the pipeline run behind the core tables.

Raw HSCRC source files are not committed. They are large and HSCRC publishes
them; `reference/source_manifest.md` names each one and links to the page it
comes from.

## Licence

Data are licensed [CC BY 4.0](LICENSE-DATA). The accompanying documentation is
MIT licensed (`LICENSE`). Attribution to HSCRC as the source of the underlying
reports is expected in addition to citing this compilation.

## Citation

> Ahmadi, F. (2026). *Maryland Hospital Global Budget Revenue: a compiled dataset
> from Health Services Cost Review Commission filings* (Version 0.1.0) [Data set].

Machine readable metadata is in `CITATION.cff`. A Zenodo DOI will be minted at
0.1.1 and added here.

## Contact

Farzin Ahmadi, Assistant Professor of Healthcare Management, Towson University.
fahmadi@towson.edu

Corrections are welcome. Open an issue with the table, the row and the source
document that contradicts it, and it will be recorded in the changelog.

This project is not affiliated with, endorsed by, or speaking for the Health
Services Cost Review Commission.
