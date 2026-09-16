> This report is the output of the pipeline run of 2026-08-19. Tables added to the
> release after that run are not described here. `MANIFEST.json` is written from
> this release's own files and lists everything that ships in it.

# Build report

Version **0.1.0** — built 2026-08-19

## Tables

| table | rows | cols | csv | parquet |
|---|---:|---:|---:|---:|
| `hospital_crosswalk` | 66 | 18 | 0.02 MB | 0.00 MB |
| `statewide_annual` | 10 | 31 | 0.00 MB | 0.00 MB |
| `hospital_annual` | 598 | 30 | 0.19 MB | 0.14 MB |
| `hospital_annual_by_setting` | 1,129 | 27 | 0.30 MB | 0.19 MB |
| `revenue_volume_monthly.csv.gz` | 300,730 | 43 | 16.80 MB | 23.92 MB |
| `financial_extraction_log` | 22 | 7 | 0.00 MB | 0.00 MB |
| `financial_statewide_annual` | 20 | 46 | 0.01 MB | 0.00 MB |
| `financial_hospital_annual` | 1,274 | 51 | 0.48 MB | 0.23 MB |
| `REVIEW_unresolved_hospital_names` | 75 | 7 | 0.01 MB | 0.00 MB |
| `rate_center_reference` | 82 | 11 | 0.01 MB | 0.00 MB |

## Validation notes

- FY2017: 148 rows where Medicare volume exceeds total volume
- FY2018: 103 rows where Medicare volume exceeds total volume
- FY2019: 155 rows where Medicare volume exceeds total volume
- FY2020: 150 rows where Medicare volume exceeds total volume
- FY2021: 120 rows where Medicare volume exceeds total volume
- FY2022: 126 rows where Medicare volume exceeds total volume
- FY2023: 104 rows where Medicare volume exceeds total volume
- FY2024: 100 rows where Medicare volume exceeds total volume
- FY2025: 116 rows where Medicare volume exceeds total volume
- FY2026: 243 rows where Medicare volume exceeds total volume
- Financial PDFs: 17 of 22 reports parsed. 153 of 173 hospital names linked to hosp_num (98 high confidence); the rest are listed in REVIEW_unresolved_hospital_names.csv.
- Financial PDF FY2003: NO_BLOCKS_PARSED
- Financial PDF FY2004: NO_BLOCKS_PARSED
- Financial PDF FY2005: NO_BLOCKS_PARSED
- Financial PDF FY2006: NO_BLOCKS_PARSED
- Financial PDF FY2008: NO_TEXT_LAYER (scanned image; needs OCR)
