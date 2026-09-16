# Source manifest

Raw HSCRC files are **not** committed — they are large and HSCRC is the system of
record. Download them into `data/raw/` (flat, original filenames) and run `make build`.

## Revenue & volume ("Experience Data")

From **[HSCRC Financial Data](https://hscrc.maryland.gov/Pages/hsp_Data2.aspx)** →
Revenue and Volume reports.

| FY | Filename in `data/raw/` |
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
| 2026 | `hscrc_revenue_vol_FY26Julythroughmar2026_050826.xlsx` (partial: 9 months) |

Filenames change with every release and the header row moves between years — the
reader detects it rather than assuming a position. Update `REVENUE_VOLUME_FILES` in
`src/mdhosp/config.py` when a new file lands.

**FY2026 adds 24 columns** (`MCAID_*`, `KP_*`) breaking out Medicaid and Kaiser
Permanente for the first time. Earlier years carry Medicare and residency splits only.

## Financial condition

From the same page, plus the archive of annual reports. Twenty-two PDFs,
FY2003–FY2024; see `FINANCIAL_PDFS` in `config.py` for the expected filenames.

Each report restates the prior two fiscal years, so coverage overlaps. That overlap is
load-bearing: **FY2008's own report is a scanned image with no text layer**, and FY2008
is recovered from the FY2009 report instead.

## Hospital geography and ownership

`Hospital_General_Information.csv` from
**[CMS Provider Data — Hospitals](https://data.cms.gov/provider-data/topics/hospitals)**
(Hospital General Information dataset). Joined on CCN.

## Rate center definitions

`reference/rate_centers.csv` is hand-built from three HSCRC documents, with the source
recorded per code:

- **CCD** — [Center Code Descriptions, 2012-02-23](https://hscrc.maryland.gov/documents/hospitals/ratestargets/centercodedescriptions-20120223.pdf)
- **SUB** — [Hospital Financial Data Reporting Submission Instructions](https://hscrc.maryland.gov/Documents/Strong%20als%20Folder/0%20-%20HDMI%20Team/4%20-%20HDMI%20Documents/Hospital%20Financial%20Data%20Reporting%20Submission%20Instructions.pdf)
- **S500** — [Accounting and Budget Manual, Section 500 (rev. 2024-10-01)](https://hscrc.maryland.gov/Documents/WayneN%20Files/SECTION%20500_10.1.24.pdf)

A current XLSX version exists at
[Center Code Descriptions (xlsx)](https://hscrc.maryland.gov/Documents/Hospitals/RatesTargets/Center%20Code%20Descriptions-forweb%20%281%29.xlsx),
linked from [Hospital Rate Orders and Unit Rates](https://hscrc.maryland.gov/pages/hsp_rates2.aspx).
It very likely defines **EK2, EM2, LA2 and bare OID**, which are the four codes present
in the data that no document consulted here defines. Resolving them from that file is
the single easiest open contribution to this project.

COMAR 10.37.01.02 incorporates the Accounting and Budget Manual by reference but
contains no code list of its own.

## Not yet integrated

| Source | For |
|---|---|
| ECMAD file and RY casemix weight workbooks (GBR Adjustments page) | The volume denominator GBR actually uses |
| MHAC / RRIP / QBR / PAU policy PDFs and scaling workbooks (Quality page) | Quality program panel — must carry a methodology-version column |
| BLS CPI, medical care | Real-dollar series |
| Census ACS population | Per-capita series |
| CMS Geographic Variation PUF | Total cost of care context, unregulated-setting leakage |
