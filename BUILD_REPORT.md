> This report is the output of the pipeline run of 2026-11-03. Tables added to the
> release after that run are not described here. `MANIFEST.json` is written from
> this release's own files and lists everything that ships in it.

# Build report

Version **0.1.3** — built 2026-11-03

## Tables

| table | rows | cols | csv | parquet |
|---|---:|---:|---:|---:|
| `hospital_crosswalk` | 66 | 18 | 0.02 MB | 0.00 MB |
| `statewide_annual` | 10 | 29 | 0.00 MB | 0.00 MB |
| `hospital_annual` | 598 | 28 | 0.18 MB | 0.14 MB |
| `hospital_annual_by_setting` | 1,129 | 25 | 0.29 MB | 0.19 MB |
| `revenue_volume_monthly.csv.gz` | 308,195 | 37 | 17.27 MB | 24.90 MB |
| `financial_extraction_log` | 23 | 7 | 0.00 MB | 0.00 MB |
| `REVIEW_conflicting_report_rows` | 37 | 10 | 0.01 MB | 0.00 MB |
| `financial_statewide_annual` | 21 | 49 | 0.01 MB | 0.00 MB |
| `financial_hospital_annual` | 1,107 | 55 | 0.45 MB | 0.24 MB |
| `REVIEW_unresolved_hospital_names` | 8 | 7 | 0.00 MB | 0.00 MB |
| `disclosure_hospital_annual` | 589 | 59 | 0.24 MB | 0.21 MB |
| `approved_gbr_targets` | 461 | 9 | 0.06 MB | 0.00 MB |
| `quality_adjustments` | 1,805 | 11 | 0.30 MB | 0.00 MB |
| `rate_center_reference` | 82 | 11 | 0.01 MB | 0.00 MB |

## Validation notes

- FY2017: 148 rows where Maryland-resident Medicare volume exceeds Maryland-resident total volume
- FY2018: 103 rows where Maryland-resident Medicare volume exceeds Maryland-resident total volume
- FY2019: 155 rows where Maryland-resident Medicare volume exceeds Maryland-resident total volume
- FY2020: 150 rows where Maryland-resident Medicare volume exceeds Maryland-resident total volume
- FY2021: 120 rows where Maryland-resident Medicare volume exceeds Maryland-resident total volume
- FY2022: 126 rows where Maryland-resident Medicare volume exceeds Maryland-resident total volume
- FY2023: 104 rows where Maryland-resident Medicare volume exceeds Maryland-resident total volume
- FY2024: 100 rows where Maryland-resident Medicare volume exceeds Maryland-resident total volume
- FY2025: 116 rows where Maryland-resident Medicare volume exceeds Maryland-resident total volume
- FY2026: 135 rows where Maryland-resident Medicare volume exceeds Maryland-resident total volume
- FY2026: workbook has no MCID; ccn carried from each hospital's latest earlier year
- FY2026: workbook has no SER_TYPE; service_type taken from the rate center's value in other years
- 37 hospital-year-field cells where a single report gives two different values (1 entities: UPMC WESTERN MARYLAND): two reporting periods in one fiscal year. One period is published, from the most recent report; see REVIEW_conflicting_report_rows.csv.
- 21 rows of report roll-ups that are not facilities (ALL SPECIALTY HOSPITALS) are not published.
- 221 rows printed under an older name for a hospital-year a later report prints under another name were collapsed into the later report's row (one row per hosp_num and fiscal year).
- Financial PDFs: 18 of 23 reports parsed. 164 of 173 hospital names linked to hosp_num (96 high confidence); the rest are listed in REVIEW_unresolved_hospital_names.csv.
- Financial PDF FY2003: NO_BLOCKS_PARSED
- Financial PDF FY2004: NO_BLOCKS_PARSED
- Financial PDF FY2005: NO_BLOCKS_PARSED
- Financial PDF FY2006: NO_BLOCKS_PARSED
- Financial PDF FY2008: NO_TEXT_LAYER (scanned image; needs OCR)
- Disclosure reports FY2009-FY2018: 509 pages, 589 hospital-years linked to hosp_num; 4 printed names left unlinked.
