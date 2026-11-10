# Source manifest

Every file the build reads, by exact file name. Raw files are **not** redistributed:
HSCRC is the system of record and republishes some files under new names as figures
move from preliminary to final. Put the files in `data/raw/` (flat, original file
names; `python3 src/stage_raw.py --from <folder>` copies them from a research folder)
and run `make build`. File names are also the registry in `src/mdhosp/config.py`;
the two lists must agree, and `stage_raw.py --check` reports any file that is missing.

## Revenue and volume (Experience Data)

From **[HSCRC Financial Data](https://hscrc.maryland.gov/Pages/hsp_Data2.aspx)**, Revenue and Volume reports. Ten complete fiscal years.

| Fiscal year | File |
|---|---|
| 2017 | `FY17-Final-Experience-Data-20170927.xlsx` |
| 2018 | `FY18FinalExperienceReport.xlsx` |
| 2019 | `hscrc-revenue-vol-FY19JultoJune2019.xlsx` |
| 2020 | `hscrc-revenue-vol-FY20throughJuneFinal.xlsx` |
| 2021 | `FY2021FinalExperienceData-20211013.xlsx` |
| 2022 | `hscrc-revenue-vol-FY22JulythroughJune2022.xlsx` |
| 2023 | `hscrc-revenue-vol-FY23JulythroughJune2023_030624.xlsx` |
| 2024 | `hscrc-revenue-vol-FY24JulythroughJun2024_Final.xlsx` |
| 2025 | `hscrc_revenue_vol_FY25Julythroughjun2025_091525.xlsx` |
| 2026 | `hscrc_revenue_vol_FY26Julythroughjun2026_100626.xlsx` |

The header row moves between years; the reader finds it by searching for the hospital
number field. The FY2026 file adds Medicaid (`MCAID_*`) columns. The partial FY2026
file of May 2026 also broke out Kaiser Permanente; the final file used here does not.
HSCRC re-released the final FY2026 file on 9 October 2026 and no longer posts the
August version (`hscrc_revenue_vol_FY26Julythroughjun2026_080626.xlsx`). The
re-release has no `MCID` or `SER_TYPE` column, so the build carries FY2026 `ccn` and
`service_type` from the other years.

HSCRC revises the current year's file without notice and keeps only the latest version
online. This release uses `hscrc_revenue_vol_FY26Julythroughjun2026_100626.xlsx`
(8,240,411 bytes, last modified 9 October 2026, SHA-256
`7fcddcfe25044e115edce62ed54b4f53c6b5ea039280b25db7c083dcfb47ac65`). HSCRC posted a
further FY2026 version on 9 November 2026
(`hscrc_revenue_vol_FY26Julythroughjun2026_110926.xlsx`), which this release does not
use. A rebuild from a later version can differ slightly in FY2026.

## Financial condition and disclosure reports

From the same page and HSCRC's archive of annual reports. Twenty-three PDFs; the
edition is the fiscal year in the title, and each edition restates the two or three
prior fiscal years.

| Edition | File | Read by |
|---|---|---|
| 2003 | `Financial Disclosure FY2003.pdf` | financial parser (exhibit-table layout, not parsed) |
| 2004 | `Financial Disclosure FY2004.pdf` | financial parser (exhibit-table layout, not parsed) |
| 2005 | `Financial Disclosure FY2005.pdf` | financial parser (exhibit-table layout, not parsed) |
| 2006 | `Financial Disclosure FY2006.pdf` | financial parser (exhibit-table layout, not parsed) |
| 2007 | `Financial Disclosure FY2007.pdf` | financial parser |
| 2008 | `HSCRC_DisclosureReport-FY08.pdf` | financial parser (scanned image, no text layer; FY2008 recovered from the FY2009 and FY2010 editions) |
| 2009 | `HSCRC_DisclosureReport-FY09.pdf` | both parsers |
| 2010 | `HSCRC_DisclosureReport-FY2010.pdf` | both parsers |
| 2011 | `HSCRC-DisclosureReport-FY2011.pdf` | both parsers |
| 2012 | `hscrc-disclosure-report-fy-2012.pdf` | both parsers |
| 2013 | `HSCRC-Disclosure-Report-FY-2013.pdf` | both parsers |
| 2014 | `HSCRC-Financial-Disclosure-Report-FY-2014.pdf` | both parsers |
| 2015 | `HSCRC-Disclosure-Report-FY-2015-rev1.pdf` | both parsers |
| 2016 | `HSCRC-Disclosure-Report-FY-2016.pdf` | both parsers |
| 2017 | `HSCRCDisclosureReportFY2017.pdf` | both parsers |
| 2018 | `HSCRC-Disclosure-Report-FY-2018.pdf` | both parsers |
| 2019 | `Hospital Financial Condition Report FY 2019 FINAL.pdf` | financial parser |
| 2020 | `Hospital Financial Condition Report FY 2020.pdf` | financial parser |
| 2021 | `Hospital Financial Condition Report FY 2021.pdf` | financial parser |
| 2022 | `Final Hospital Financial Condition Report FY 2022.pdf` | financial parser |
| 2023 | `Final Hospital Financial Condition Report FY 2023.pdf` | financial parser |
| 2024 | `Final Hospital Financial Condition Report FY 2024 (2).pdf` | financial parser |
| 2025 | `Hospital Financial Condition Report FY 2025.pdf` | financial parser |

## Approved global budget targets

From **[Hospital Rate Orders and Unit Rates](https://hscrc.maryland.gov/pages/hsp_rates2.aspx)**: one rate year input file per rate year, sheet `Mid-Final Targets`. Hospital names and provider numbers come from the `Hospital List` sheet of the rate year 2026 file.

| Rate year | File |
|---|---|
| 2019 | `RY 2019_05_01_19- Final.xlsx` |
| 2020 | `RY 2020_07_01_19 - 10.27.2020.xlsx` |
| 2021 | `RY21 Input File 2023.05.16 _ (1).xlsx` |
| 2022 | `RY22 Input File 2023.06.012.xlsx` |
| 2023 | `FY2023 July 1_RY23 Input File (8).xlsx` |
| 2024 | `RY24 Input File_20240611 (2).xlsx` |
| 2025 | `RY25 Input File_20250630.xlsx` |
| 2026 | `RY26 Input File_January Update.xlsx` |

Rate year 2023 has two input files on the HSCRC page; the July 1 file is used.

## Quality program adjustments

From HSCRC's **Annual Update to Unit Rates and Global Budget Revenue** packages, one per July 1 (linked from [Hospital Rate Orders and Unit Rates](https://hscrc.maryland.gov/pages/hsp_rates2.aspx)). One workbook and sheet per program and rate year. The rate year is the one the document names: the July 1 2026 package re-files the rate year 2026 QBR workbook and contains no rate year 2027 QBR file.

| Program | Rate year | File | Sheet |
|---|---|---|---|
| MHAC | 2016 | `FY-2015-Scaling-for-Improvement-MHAC.xlsx` | FY15 Rev RY2015 Scaling |
| MHAC | 2018 | `MHACResults_51217.xlsx` | RY18 MHAC Revenue Adjustments |
| MHAC | 2019 | `RY19MHACScaling.xlsx` | MHAC Modeling Results |
| MHAC | 2020 | `RY2020MHACScaling.xlsx` | MHAC Results |
| MHAC | 2021 | `RY2021MHACScaling.xlsx` | MHAC Modeling Results |
| MHAC | 2022 | `RY2022MHAC.xlsx` | MHAC Modeling Results |
| MHAC | 2024 | `RY24 MHAC Scaling 06122023.xlsx` | MHAC Modeling |
| MHAC | 2025 | `RY25 Final MHAC Revenue Adjustment  (1).xlsx` | MHAC Revenue Adjustments |
| MHAC | 2026 | `RY26 MHAC Revenue Adjustment_Final (1).xlsx` | MHAC Revenue Adjustments |
| MHAC | 2027 | `RY27 MHAC FINAL Revenue Adjustments (final).xlsx` | MHAC Revenue Adjustments |
| PAU | 2018 | `PAU-62217.xlsx` | PAU Savings Adjustments |
| PAU | 2019 | `RY19PAUSavingsScaling.xlsx` | 7. PAU Savings to Use |
| PAU | 2020 | `RY2020PAUSavingsScaling.xlsx` | PAU Savings |
| PAU | 2021 | `RY2021PAUSavingsScaling.xlsx` | Hospital PAU Savings |
| PAU | 2022 | `FINAL_RY2022_PAU_Savings_Scaling.xlsx` | Hospital PAU Savings |
| PAU | 2023 | `FINAL_RY2023_PAU_Savings_Scaling v2 (1).xlsx` | Hospital PAU Savings |
| PAU | 2024 | `RY2024_PAU_Savings_Scaling_Final (05172023) (with .86%).xlsx` | Hospital PAU Savings |
| PAU | 2025 | `RY2025_PAU_Savings_Scaling_Final (New Methodology) v2 (2).xlsx` | Hospital PAU Savings (NEW) $0 |
| PAU | 2026 | `RY2026_PAU_Savings_Scaling_Final (New Methodology)  (1).xlsx` | Hospital PAU Savings (NEW) $0 |
| PAU | 2027 | `RY2027_PAU_Savings_Scaling_(New Methodology)_Final .xlsx` | Hospital PAU Savings (NEW) $0 |
| QBR | 2016 | `QBR-RY2016-Revenue-Adjustments-09042015-UPDATED.xlsx` | RY2016 Revenue Adjustments |
| QBR | 2017 | `FINAL-FY-2017-QBR-Scale-Modeling_20161122_SC.xlsx` | 6.QBR Modeling Results |
| QBR | 2018 | `QBR_51617.xlsx` | 6.QBR Modeling Results |
| QBR | 2019 | `RY2019FinalQBRScoring.xlsx` | SCALING |
| QBR | 2020 | `RY2020QBRScaling.xlsx` | QBR Revenue Adjustments |
| QBR | 2021 | `RY2021QBRScaling.xlsx` | 6.QBR Results |
| QBR | 2022 | `RY2022QBR.xlsx` | 6.QBR Results |
| QBR | 2024 | `FINAL RY24 QBR Revenue Adjustments (2).xlsx` | QBR Revenue Adjustments  |
| QBR | 2025 | `Final RY25 QBR Revenue Adj with 32% cutpoint.xlsx` | QBR Revenue Adjustments (32) |
| QBR | 2026 | `FINAL RY 2026 QBR Revenue Adjustments (1).xlsx` | QBR Revenue Adjustments |
| RRIP | 2018 | `RRIPResults_51217.xlsx` | RRIP Revenue Impact |
| RRIP | 2019 | `RY19RRIPScaling.xlsx` | RRIP Modeling Results |
| RRIP | 2020 | `RY2020RRIPScaling.xlsx` | RRIP Results |
| RRIP | 2021 | `RY2021RRIPScaling07092020.xlsx` | RRIP Modeling Results |
| RRIP | 2022 | `RY2022RRIP.xlsx` | RRIP Modeling Results |
| RRIP | 2023 | `RRIP & PAI Rev Adj RY23 (1).xlsx` | RRIP & PAI |
| RRIP | 2024 | `RY24 RRIP and Disparity Gap 072823.xlsx` | PAI revenue adjustments |
| RRIP | 2025 | `RY25 Final RRIP + Disparity Gap Revenue Adjustments (xlsx.xlsx` | RRIP Revenue Adjustments |
| RRIP | 2026 | `Final RY26 Blended Base Period RRIP + Disparity Gap Revenue Adjustments  (3).xlsx` | RRIP Revenue Adjustments |
| RRIP | 2027 | `RY27 RRIP FINAL Revenue Adjustments (2) (1).xlsx` | RRIP Revenue Adjustments |

No MHAC workbook is used for rate years 2017 and 2023, no QBR workbook for 2023 and 2027, and no PAU or RRIP workbook before 2018.

## Hospital attributes

`Hospital_General_Information.csv`, from **[CMS Provider Data, Hospitals](https://data.cms.gov/provider-data/topics/hospitals)** (Hospital General Information). Joined on CCN for city, county, ownership, type, emergency services and star rating.

## Rate center definitions

`reference/rate_centers.csv` is built by hand from three HSCRC documents, with the source recorded for each code:

- **CCD**: [Center Code Descriptions, 2012-02-23](https://hscrc.maryland.gov/documents/hospitals/ratestargets/centercodedescriptions-20120223.pdf)
- **SUB**: [Hospital Financial Data Reporting Submission Instructions](https://hscrc.maryland.gov/Documents/Strong%20als%20Folder/0%20-%20HDMI%20Team/4%20-%20HDMI%20Documents/Hospital%20Financial%20Data%20Reporting%20Submission%20Instructions.pdf)
- **S500**: [Accounting and Budget Manual, Section 500 (rev. 2024-10-01)](https://hscrc.maryland.gov/Documents/WayneN%20Files/SECTION%20500_10.1.24.pdf)

Four codes in the data (EK2, EM2, LA2 and OID without a suffix) are defined in none of these and are marked UNCONFIRMED.

## Name decisions

`reference/name_overrides.csv` records every decision that links a printed report name to a hospital number, or declines to, with its evidence. See `reference/NAME_RESOLUTION.md`.
