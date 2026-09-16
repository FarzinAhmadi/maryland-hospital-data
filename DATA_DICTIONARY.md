# Data dictionary

Generated from the built tables. Types, null rates and ranges are read off the actual output, so this file cannot drift from the data.


## `REVIEW_unresolved_hospital_names`

A to-do list, not a dataset. PDF hospital names that no automated match resolved with high confidence. Resolve with a source, then add to the crosswalk.

| column | type | non-null | example | description |
|---|---|---:|---|---|
| `entity` | object | 100% | `ADVENTIST BEHAVIORAL HEALT` | Entity name as printed in the source PDF, normalised for case and punctuation. |
| `hosp_num` | float64 | 73% | `4013.0` | HSCRC hospital identifier. Stable across years. **The join key.** |
| `match_score` | float64 | 100% | `0.574` | Name-match score, 0-1. |
| `matched_name` | object | 73% | `Adventist BH-Rockville` | The crosswalk name that matched. |
| `match_basis` | object | 100% | `UNMATCHED` | Which name field produced the match. |
| `match_confidence` | object | 100% | `none` | `high` (>=0.85), `medium` (>=0.62), or `none`. Treat `medium` as provisional. |
| `reviewer_note` | float64 | 0% | `` | Blank column for a human to fill in when resolving a name. |

## `approved_gbr_targets`

One row per hospital per rate year. The approved global budget target as published in HSCRC's mid-year and final target files for that rate year. This is what a hospital was permitted to collect; `hospital_annual.revenue` is what it collected. Join on `hospid`, which uses the same numbering as `hosp_num`.

| column | type | non-null | example | description |
|---|---|---:|---|---|
| `rate_year` | int64 | 100% | `2019` | HSCRC rate year, ending 30 June. Rate year 2019 covers July 2018 to June 2019. |
| `hospid` | float64 | 100% | `1.0` | HSCRC hospital identifier as printed in the rate-setting files. Same numbering as `hosp_num`. |
| `md_provider_number` | float64 | 96% | `210001.0` | Maryland provider number as printed in the source file. Equivalent to the CMS Certification Number, 210000 + hospital number. |
| `hospital_name` | object | 96% | `Meritus Medical Center` | Hospital name as printed in the source file. |
| `mid_year_target` | float64 | 79% | `185075342.2192` | Approved revenue target at the mid-year update, nominal dollars. |
| `year_end_target` | float64 | 97% | `370256624.1269` | Approved revenue target at the year-end update, nominal dollars. |
| `source_file` | object | 100% | `ry_2019_05_01_19_final__mi` | Originating HSCRC file name. |

## `disclosure_hospital_annual`

Hospital x fiscal year, extracted from the HSCRC Disclosure Reports, which cover years earlier than the Financial Condition Reports behind `financial_hospital_annual`. Column names are the report's own section and row labels, flattened and normalised, so several carry a section prefix and a few labels repeat in prefixed and bare form. Values are as printed. Treat this table as provisional: it comes from PDF extraction and has not been reconciled line by line against the source reports.

| column | type | non-null | example | description |
|---|---|---:|---|---|
| `hosp_num` | int64 | 100% | `1` | HSCRC hospital identifier. Stable across years. **The join key.** |
| `fiscal_year` | int64 | 100% | `2009` | Maryland fiscal year, ending 30 June. FY2025 = July 2024 - June 2025. |
| `fy_end_month` | object | 100% | `June` | Month the hospital's fiscal year ends, as printed in the report. Most are June. |
| `hospital_key` | object | 100% | `MERITUS MEDICAL CENTER` | Entity name as printed in the source PDF, used to match the row to `hosp_num`. |
| `match_score` | float64 | 100% | `1.0` | Name-match score, 0-1. |
| `match_basis` | object | 100% | `cms_name` | Which name field produced the match. |
| `equivalent_inpatient_adms_eipa_regulated_services` | float64 | 52% | `24851.0` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `equivalent_inpatient_adms_eipa_total` | float64 | 52% | `29102.0` | Equivalent inpatient adms eipa, regulated and unregulated combined. Nominal dollars. |
| `gross_patient_revenue_regulated_services` | float64 | 99% | `243018300.0` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `gross_patient_revenue_total` | float64 | 99% | `284584700.0` | Gross patient revenue, regulated and unregulated combined. Nominal dollars. |
| `gross_patient_revenue_unregulated_services` | float64 | 99% | `41566400.0` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `inpatient_readmission_charges` | float64 | 54% | `27005043.0` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `net_operating_profit_loss_regulated_services` | float64 | 99% | `4634953.0` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `net_operating_profit_loss_total` | float64 | 99% | `3773700.0` | Net operating profit loss, regulated and unregulated combined. Nominal dollars. |
| `net_operating_profit_loss_unregulated_services` | float64 | 99% | `-861253.0` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `net_patient_revenue_npr_regulated_services` | float64 | 99% | `203634400.0` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `net_patient_revenue_npr_total` | float64 | 99% | `230865600.0` | Net patient revenue npr, regulated and unregulated combined. Nominal dollars. |
| `net_patient_revenue_npr_unregulated_services` | float64 | 99% | `27231200.0` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `nor_per_eipa_regulated_services` | float64 | 52% | `8273.02` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `nor_per_eipa_total` | float64 | 52% | `8077.77` | Net operating revenue per EIPA, all activities. |
| `npr_per_eipa_regulated_services` | float64 | 52% | `8194.1` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `npr_per_eipa_total` | float64 | 52% | `7932.99` | Net patient revenue per EIPA, all activities. |
| `operating_expenses_per_eipa_regulated_services` | float64 | 52% | `8086.52` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `operating_expenses_per_eipa_total` | float64 | 52% | `7948.1` | Operating expenses per eipa, regulated and unregulated combined. Nominal dollars. |
| `other_operating_revenue_regulated_services` | float64 | 99% | `205595800.0` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `other_operating_revenue_total` | float64 | 99% | `235079200.0` | Other operating revenue, regulated and unregulated combined. Nominal dollars. |
| `other_operating_revenue_unregulated_services` | float64 | 99% | `29483400.0` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `potentially_avoidable_utilization_costs` | float64 | 54% | `44766008.0` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `risk_adjusted_ppc_rate` | float64 | 54% | `1.38` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `risk_adjusted_readmission_percent` | float64 | 54% | `11.64` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `total_direct_graduate_medical_education` | float64 | 21% | `0.0` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `total_direct_medical_education` | float64 | 42% | `0.0` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `total_excess_profit_loss` | float64 | 61% | `4712400.0` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `total_excess_profit_loss_net_operating_profit_of_regulated_nor` | float64 | 54% | `4.04` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `total_excess_profit_loss_net_total_operating_profit_of_total_nor` | float64 | 54% | `3.73` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `total_excess_profit_loss_total_excess_profit_of_total_revenue` | float64 | 54% | `2.98` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `total_non_operating_profit_loss` | float64 | 99% | `-4587500.0` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `total_non_operating_profit_loss_change_in_net_operating_profit_regulated` | float64 | 52% | `-50.72` | Total non operating profit loss change in net operating profit, rate-regulated services. Nominal dollars. |
| `total_non_operating_profit_loss_change_in_net_operating_profit_total` | float64 | 52% | `-50.65` | Total non operating profit loss change in net operating profit, regulated and unregulated combined. Nominal dollars. |
| `total_non_operating_profit_loss_change_in_nor_per_eipa_regulated` | float64 | 52% | `2.18` | Total non operating profit loss change in nor per eipa, rate-regulated services. Nominal dollars. |
| `total_non_operating_profit_loss_change_in_npr_per_eipa_regulated` | float64 | 52% | `1.65` | Total non operating profit loss change in npr per eipa, rate-regulated services. Nominal dollars. |
| `total_non_operating_profit_loss_change_in_oper_expense_per_eipa_regulated` | float64 | 52% | `4.93` | Total non operating profit loss change in oper expense per eipa, rate-regulated services. Nominal dollars. |
| `total_non_operating_profit_loss_change_in_total_excess_profit` | float64 | 52% | `-109.44` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `total_non_operating_profit_loss_net_operating_profit_of_regulated_nor` | float64 | 53% | `2.25` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `total_non_operating_profit_loss_net_total_operating_profit_of_total_nor` | float64 | 53% | `1.61` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `total_non_operating_profit_loss_non_operating_expenses` | float64 | 99% | `147500.0` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `total_non_operating_profit_loss_non_operating_revenue` | float64 | 99% | `-4440000.0` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `total_non_operating_profit_loss_total_excess_profit` | float64 | 53% | `-813800.0` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `total_non_operating_profit_loss_total_excess_profit_of_total_revenue` | float64 | 53% | `-0.35` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `total_operating_expenses_regulated_services` | float64 | 99% | `200960847.0` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `total_operating_expenses_total` | float64 | 99% | `231305500.0` | Total operating expenses, regulated and unregulated combined. Nominal dollars. |
| `total_regulated_inpatient_admissions` | float64 | 22% | `15558.0` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `total_regulated_outpatient_visits` | float64 | 22% | `111239.0` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `net_operating_profit_of_regulated_nor` | float64 | 98% | `2.25` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `net_total_operating_profit_of_total_nor` | float64 | 98% | `1.61` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |
| `total_excess_profit_of_total_revenue` | float64 | 98% | `-0.35` | As printed in the source report under this section and row label. Definition not yet vetted against the report. |

## `financial_extraction_log`

Per-PDF extraction outcome. Failures are visible here.

| column | type | non-null | example | description |
|---|---|---:|---|---|
| `report_fy` | object | 100% | `FY2003` | Fiscal year of the source report. |
| `file` | object | 100% | `Financial Disclosure FY200` | Source PDF file name. |
| `chars` | int64 | 100% | `133755` | Characters of extractable text. Near zero means a scanned image. |
| `status` | object | 100% | `NO_BLOCKS_PARSED` | Extraction outcome for that PDF. |
| `n_entities` | float64 | 77% | `11.0` | Distinct entities parsed from that report. |
| `n_records` | float64 | 77% | `360.0` | Field-level records parsed from that report. |
| `fys` | object | 77% | `[2005, 2006, 2007]` | Fiscal years of hospital detail found in that report. |

## `financial_hospital_annual`

Hospital-level financial condition from the same PDFs. `hosp_num` is populated by fuzzy name matching -- always check `match_confidence`.

| column | type | non-null | example | description |
|---|---|---:|---|---|
| `entity` | object | 100% | `ADVENTIST BEHAVIORAL HEALT` | Entity name as printed in the source PDF, normalised for case and punctuation. |
| `is_statewide_total` | bool | 100% | `False` | True for the statewide roll-up row rather than a hospital. |
| `fiscal_year` | int64 | 100% | `2007` | Maryland fiscal year, ending 30 June. FY2025 = July 2024 - June 2025. |
| `eipa_regulated` | float64 | 0% | `` | Equivalent Inpatient Admissions, regulated services. Not published after FY2012. |
| `eipa_total` | float64 | 0% | `` | Equivalent Inpatient Admissions. HSCRC stopped publishing this after FY2012. |
| `excess_profit` | float64 | 81% | `-819368.0` | Total excess profit, operating and non-operating. |
| `gross_revenue_regulated` | float64 | 88% | `53432546.0` | Gross patient revenue, regulated services. |
| `gross_revenue_total` | float64 | 100% | `2842250.0` | Gross revenue, regulated and unregulated combined. Nominal dollars. |
| `gross_revenue_unregulated` | float64 | 88% | `609207.0` | Gross revenue, non-rate-regulated services. Nominal dollars. |
| `inpatient_admissions` | float64 | 12% | `260.0` | Volume of the ADM rate center. Not ECMAD. |
| `net_operating_revenue_regulated` | float64 | 88% | `45122235.0` | Net operating revenue, regulated services. |
| `net_operating_revenue_total` | float64 | 100% | `2702504.0` | Net operating revenue, regulated and unregulated combined. Nominal dollars. |
| `net_operating_revenue_unregulated` | float64 | 64% | `674447.0` | Net operating revenue, non-rate-regulated services. Nominal dollars. |
| `net_patient_revenue_regulated` | float64 | 88% | `44597472.0` | Net patient revenue, regulated services. |
| `net_patient_revenue_total` | float64 | 100% | `2702504.0` | Net patient revenue, regulated and unregulated combined. Nominal dollars. |
| `net_patient_revenue_unregulated` | float64 | 88% | `609207.0` | Net patient revenue, non-rate-regulated services. Nominal dollars. |
| `non_operating_profit` | float64 | 82% | `0.0` | Total non-operating profit or loss. |
| `nor_per_eipa_regulated` | float64 | 31% | `8690.87` | Net operating revenue per EIPA, regulated. |
| `nor_per_eipa_total` | float64 | 31% | `9283.31` | Net operating revenue per EIPA, all activities. |
| `npr_per_eipa_regulated` | float64 | 31% | `8437.68` | Net patient revenue per EIPA, regulated. HSCRC's own cost-per-case measure of the pre-2014 era. |
| `npr_per_eipa_total` | float64 | 31% | `8637.78` | Net patient revenue per EIPA, all activities. |
| `operating_expenses_regulated` | float64 | 88% | `43086297.0` | Operating expenses, rate-regulated services. Nominal dollars. |
| `operating_expenses_total` | float64 | 100% | `3521872.0` | Operating expenses, regulated and unregulated combined. Nominal dollars. |
| `operating_expenses_unregulated` | float64 | 24% | `1036079.0` | Operating expenses, non-rate-regulated services. Nominal dollars. |
| `operating_profit_regulated` | float64 | 64% | `2035938.0` | Net operating profit on regulated activities. |
| `operating_profit_total` | float64 | 76% | `-819368.0` | Net operating profit, regulated and unregulated combined. |
| `operating_profit_unregulated` | float64 | 64% | `-679974.0` | Net operating profit on unregulated activities. Negative in every year of the series. |
| `opex_per_eipa_regulated` | float64 | 31% | `7883.3` | Operating expense per EIPA, regulated. |
| `opex_per_eipa_total` | float64 | 31% | `8731.61` | Operating expense per EIPA, all activities. |
| `other_operating_revenue_regulated` | float64 | 88% | `524763.0` | Other operating revenue, rate-regulated services. Nominal dollars. |
| `other_operating_revenue_total` | float64 | 100% | `0.0` | Other operating revenue, regulated and unregulated combined. Nominal dollars. |
| `other_operating_revenue_unregulated` | float64 | 88% | `65240.0` | Other operating revenue, non-rate-regulated services. Nominal dollars. |
| `pct_change_excess_profit` | float64 | 11% | `260.2` | Year-over-year percent change in total excess profit, as printed by HSCRC. |
| `pct_change_nor_per_eipa_regulated` | float64 | 11% | `0.38` | Year-over-year percent change in net operating revenue per EIPA, regulated, as printed by HSCRC. |
| `pct_change_npr_per_eipa_regulated` | float64 | 11% | `1.08` | Year-over-year percent change in net patient revenue per EIPA, regulated, as printed by HSCRC. |
| `pct_change_operating_profit_regulated` | float64 | 11% | `-11.17` | Year-over-year percent change in regulated operating profit, as printed by HSCRC. |
| `pct_change_operating_profit_total` | float64 | 11% | `2.92` | Year-over-year percent change in total operating profit, as printed by HSCRC. |
| `pct_excess_profit_of_total_revenue` | float64 | 68% | `1.01` | Excess profit as a percent of total revenue, as printed by HSCRC. |
| `pct_operating_profit_of_regulated_nor` | float64 | 68% | `2.68` | The margin **as printed by HSCRC**. Blank FY2010-FY2013, when the reports omitted it. |
| `pct_operating_profit_of_total_nor` | float64 | 68% | `1.13` | Total operating margin as printed by HSCRC. |
| `reported_in_reports` | object | 100% | `FY2009` | Which annual reports carried a figure for this entity-year. |
| `n_source_reports` | int64 | 100% | `1` | How many reports covered it. Higher means more restatement passes. |
| `period_labels` | object | 100% | `YEAR 2007` | The fiscal-year-end label(s) printed in the source. Not always June -- several hospitals report on a December or September year. |
| `name_variants` | object | 100% | `Adventist Behavioral Healt` | Every spelling of this entity found across reports, including fixed-width truncations. |
| `hosp_num` | float64 | 91% | `4013.0` | HSCRC hospital identifier. Stable across years. **The join key.** |
| `match_score` | float64 | 100% | `0.574` | Name-match score, 0-1. |
| `matched_name` | object | 91% | `Adventist BH-Rockville` | The crosswalk name that matched. |
| `match_basis` | object | 100% | `UNMATCHED` | Which name field produced the match. |
| `match_confidence` | object | 100% | `none` | `high` (>=0.85), `medium` (>=0.62), or `none`. Treat `medium` as provisional. |
| `margin_regulated_pct` | float64 | 64% | `4.51` | Operating profit as a percent of regulated net operating revenue. Derived, so the series is continuous; matches HSCRC's printed value exactly wherever both exist. |
| `margin_total_pct` | float64 | 75% | `-30.32` | Operating profit as a percent of total net operating revenue. Derived. |

## `financial_statewide_annual`

Extracted from HSCRC Financial Condition / Disclosure PDFs. Each report restates the prior two years; values here are the most recent restatement, and `reported_in_reports` lists which reports carried each figure.

| column | type | non-null | example | description |
|---|---|---:|---|---|
| `entity` | object | 100% | `ALL ACUTE HOSPITALS` | Entity name as printed in the source PDF, normalised for case and punctuation. |
| `is_statewide_total` | bool | 100% | `True` | True for the statewide roll-up row rather than a hospital. |
| `fiscal_year` | int64 | 100% | `2005` | Maryland fiscal year, ending 30 June. FY2025 = July 2024 - June 2025. |
| `eipa_regulated` | float64 | 40% | `933558.0` | Equivalent Inpatient Admissions, regulated services. Not published after FY2012. |
| `eipa_total` | float64 | 40% | `1008864.0` | Equivalent Inpatient Admissions. HSCRC stopped publishing this after FY2012. |
| `excess_profit` | float64 | 80% | `368650149.0` | Total excess profit, operating and non-operating. |
| `gross_revenue_regulated` | float64 | 100% | `9681250082.0` | Gross patient revenue, regulated services. |
| `gross_revenue_total` | float64 | 100% | `10675445671.0` | Gross revenue, regulated and unregulated combined. Nominal dollars. |
| `gross_revenue_unregulated` | float64 | 100% | `994195589.0` | Gross revenue, non-rate-regulated services. Nominal dollars. |
| `inpatient_admissions` | float64 | 0% | `` | Volume of the ADM rate center. Not ECMAD. |
| `net_operating_revenue_regulated` | float64 | 100% | `8460040439.0` | Net operating revenue, regulated services. |
| `net_operating_revenue_total` | float64 | 100% | `9199687074.0` | Net operating revenue, regulated and unregulated combined. Nominal dollars. |
| `net_operating_revenue_unregulated` | float64 | 90% | `739646635.0` | Net operating revenue, non-rate-regulated services. Nominal dollars. |
| `net_patient_revenue_regulated` | float64 | 90% | `9833602955.0` | Net patient revenue, regulated services. |
| `net_patient_revenue_total` | float64 | 90% | `10459541483.0` | Net patient revenue, regulated and unregulated combined. Nominal dollars. |
| `net_patient_revenue_unregulated` | float64 | 90% | `625938528.0` | Net patient revenue, non-rate-regulated services. Nominal dollars. |
| `non_operating_profit` | float64 | 80% | `99529166.0` | Total non-operating profit or loss. |
| `nor_per_eipa_regulated` | float64 | 45% | `9062.15` | Net operating revenue per EIPA, regulated. |
| `nor_per_eipa_total` | float64 | 45% | `9118.86` | Net operating revenue per EIPA, all activities. |
| `npr_per_eipa_regulated` | float64 | 45% | `8957.57` | Net patient revenue per EIPA, regulated. HSCRC's own cost-per-case measure of the pre-2014 era. |
| `npr_per_eipa_total` | float64 | 45% | `8838.27` | Net patient revenue per EIPA, all activities. |
| `operating_expenses_regulated` | float64 | 100% | `8044819951.0` | Operating expenses, rate-regulated services. Nominal dollars. |
| `operating_expenses_total` | float64 | 100% | `8930566091.0` | Operating expenses, regulated and unregulated combined. Nominal dollars. |
| `operating_expenses_unregulated` | float64 | 25% | `2840382402.0` | Operating expenses, non-rate-regulated services. Nominal dollars. |
| `operating_profit_regulated` | float64 | 95% | `415220488.0` | Net operating profit on regulated activities. |
| `operating_profit_total` | float64 | 95% | `269120983.0` | Net operating profit, regulated and unregulated combined. |
| `operating_profit_unregulated` | float64 | 95% | `-146099505.0` | Net operating profit on unregulated activities. Negative in every year of the series. |
| `opex_per_eipa_regulated` | float64 | 45% | `8617.38` | Operating expense per EIPA, regulated. |
| `opex_per_eipa_total` | float64 | 45% | `8852.1` | Operating expense per EIPA, all activities. |
| `other_operating_revenue_regulated` | float64 | 100% | `97627822.0` | Other operating revenue, rate-regulated services. Nominal dollars. |
| `other_operating_revenue_total` | float64 | 100% | `283073343.0` | Other operating revenue, regulated and unregulated combined. Nominal dollars. |
| `other_operating_revenue_unregulated` | float64 | 100% | `185445521.0` | Other operating revenue, non-rate-regulated services. Nominal dollars. |
| `pct_change_excess_profit` | float64 | 25% | `40.64` | Year-over-year percent change in total excess profit, as printed by HSCRC. |
| `pct_change_nor_per_eipa_regulated` | float64 | 25% | `4.36` | Year-over-year percent change in net operating revenue per EIPA, regulated, as printed by HSCRC. |
| `pct_change_npr_per_eipa_regulated` | float64 | 25% | `4.21` | Year-over-year percent change in net patient revenue per EIPA, regulated, as printed by HSCRC. |
| `pct_change_operating_profit_regulated` | float64 | 0% | `` | Year-over-year percent change in regulated operating profit, as printed by HSCRC. |
| `pct_change_operating_profit_total` | float64 | 25% | `33.45` | Year-over-year percent change in total operating profit, as printed by HSCRC. |
| `pct_excess_profit_of_total_revenue` | float64 | 80% | `3.96` | Excess profit as a percent of total revenue, as printed by HSCRC. |
| `pct_operating_profit_of_regulated_nor` | float64 | 80% | `4.91` | The margin **as printed by HSCRC**. Blank FY2010-FY2013, when the reports omitted it. |
| `pct_operating_profit_of_total_nor` | float64 | 55% | `2.87` | Total operating margin as printed by HSCRC. |
| `reported_in_reports` | object | 100% | `FY2007` | Which annual reports carried a figure for this entity-year. |
| `n_source_reports` | int64 | 100% | `1` | How many reports covered it. Higher means more restatement passes. |
| `period_labels` | object | 100% | `Year 2005` | The fiscal-year-end label(s) printed in the source. Not always June -- several hospitals report on a December or September year. |
| `name_variants` | object | 100% | `ACUTE HOSPITALS TOTAL` | Every spelling of this entity found across reports, including fixed-width truncations. |
| `margin_regulated_pct` | float64 | 95% | `4.91` | Operating profit as a percent of regulated net operating revenue. Derived, so the series is continuous; matches HSCRC's printed value exactly wherever both exist. |
| `margin_total_pct` | float64 | 95% | `2.93` | Operating profit as a percent of total net operating revenue. Derived. |

## `hospital_annual`

Hospital x fiscal year, both settings combined.

| column | type | non-null | example | description |
|---|---|---:|---|---|
| `fiscal_year` | int64 | 100% | `2017` | Maryland fiscal year, ending 30 June. FY2025 = July 2024 - June 2025. |
| `hosp_num` | int64 | 100% | `1` | HSCRC hospital identifier. Stable across years. **The join key.** |
| `volume` | float64 | 100% | `54414558.0` | Volume in the rate center's own unit of rate -- patient days, RVUs, minutes, procedures, or cost-denominated EIPA. **Not comparable across rate centers.** |
| `revenue` | float64 | 100% | `325953071.0` | Regulated revenue, all payers, nominal dollars. |
| `volume_md_resident` | float64 | 100% | `43978983.0` | Volume attributable to Maryland residents. |
| `volume_nonmd_resident` | float64 | 100% | `10435575.0` | Volume attributable to out-of-state residents. |
| `revenue_md_resident` | float64 | 100% | `276531484.0` | Revenue from Maryland residents. |
| `revenue_nonmd_resident` | float64 | 100% | `49421587.0` | Revenue from out-of-state residents. |
| `medicare_volume_md_resident` | float64 | 100% | `24677912.0` | Medicare volume, Maryland residents. |
| `medicare_volume_nonmd_resident` | float64 | 100% | `4712388.0` | Medicare volume, out-of-state residents, in the rate center's own unit of rate. |
| `medicare_revenue_md_resident` | float64 | 100% | `135530366.0` | Medicare revenue, Maryland residents. |
| `medicare_revenue_nonmd_resident` | float64 | 100% | `20052593.0` | Medicare revenue, out-of-state residents, in nominal dollars. |
| `medicare_ffs_volume_md_resident` | float64 | 100% | `22102486.0` | Medicare fee-for-service volume, Maryland residents, in the rate center's own unit of rate. |
| `medicare_nonffs_volume_md_resident` | float64 | 100% | `2575426.0` | Medicare non-FFS (Medicare Advantage) volume, Maryland residents, in the rate center's own unit of rate. |
| `medicare_ffs_revenue_md_resident` | float64 | 100% | `120335327.0` | Medicare fee-for-service revenue, Maryland residents. |
| `medicare_nonffs_revenue_md_resident` | float64 | 100% | `15195039.0` | Non-FFS Medicare (Medicare Advantage) revenue, Maryland residents. |
| `medicaid_volume` | float64 | 10% | `3093644.0` | Medicaid (FY2026 onward only) volume, in the rate center's own unit of rate. |
| `medicaid_revenue` | float64 | 10% | `25370527.87` | Medicaid revenue. FY2026 onward only. |
| `kaiser_volume` | float64 | 10% | `22936.0` | Kaiser Permanente (FY2026 onward only) volume, in the rate center's own unit of rate. |
| `kaiser_revenue` | float64 | 10% | `165810.28` | Kaiser Permanente revenue. FY2026 onward only. |
| `revenue_inpatient` | float64 | 91% | `192822272.0` | Regulated revenue, inpatient setting. |
| `revenue_outpatient` | float64 | 97% | `133130799.0` | Regulated revenue, outpatient setting. |
| `outpatient_revenue_share` | float64 | 97% | `0.4084354799651511` | Outpatient share of regulated revenue. **Regulated only** -- care that migrates to an unregulated setting leaves this measure entirely. |
| `inpatient_admissions` | float64 | 88% | `15794.0` | Volume of the ADM rate center. Not ECMAD. |
| `inpatient_admissions_medicare` | float64 | 88% | `7259.0` | ADM rate center volume attributable to Medicare, Maryland residents. |
| `medicare_revenue_share` | float64 | 100% | `0.4901082655745629` | Medicare share of **revenue**, Maryland residents. |
| `medicare_volume_share` | float64 | 100% | `0.5611296650493259` | Medicare share of **volume**, Maryland residents. A different quantity from the revenue share; the two do not track each other. |
| `md_resident_revenue_share` | float64 | 100% | `0.8483782133164808` | Maryland-resident share of revenue. |
| `medicare_advantage_share_of_medicare_revenue` | float64 | 100% | `0.1121153837952448` | Non-FFS share of Medicare revenue. Relevant to AHEAD, whose targets anchor on FFS. |
| `medicaid_revenue_share` | float64 | 10% | `0.0577060731694371` | Medicaid share of revenue. FY2026 onward only. |

## `hospital_annual_by_setting`

Hospital x fiscal year x setting.

| column | type | non-null | example | description |
|---|---|---:|---|---|
| `fiscal_year` | int64 | 100% | `2017` | Maryland fiscal year, ending 30 June. FY2025 = July 2024 - June 2025. |
| `hosp_num` | int64 | 100% | `1` | HSCRC hospital identifier. Stable across years. **The join key.** |
| `setting` | object | 100% | `inpatient` | `inpatient` or `outpatient`. |
| `volume` | float64 | 100% | `33879314.0` | Volume in the rate center's own unit of rate -- patient days, RVUs, minutes, procedures, or cost-denominated EIPA. **Not comparable across rate centers.** |
| `revenue` | float64 | 100% | `192822272.0` | Regulated revenue, all payers, nominal dollars. |
| `volume_md_resident` | float64 | 100% | `27409850.0` | Volume attributable to Maryland residents. |
| `volume_nonmd_resident` | float64 | 100% | `6469464.0` | Volume attributable to out-of-state residents. |
| `revenue_md_resident` | float64 | 100% | `164891888.0` | Revenue from Maryland residents. |
| `revenue_nonmd_resident` | float64 | 100% | `27930384.0` | Revenue from out-of-state residents. |
| `medicare_volume_md_resident` | float64 | 100% | `16318684.0` | Medicare volume, Maryland residents. |
| `medicare_volume_nonmd_resident` | float64 | 100% | `3243975.0` | Medicare volume, out-of-state residents, in the rate center's own unit of rate. |
| `medicare_revenue_md_resident` | float64 | 100% | `95295258.0` | Medicare revenue, Maryland residents. |
| `medicare_revenue_nonmd_resident` | float64 | 100% | `12773445.0` | Medicare revenue, out-of-state residents, in nominal dollars. |
| `medicare_ffs_volume_md_resident` | float64 | 100% | `14580240.0` | Medicare fee-for-service volume, Maryland residents, in the rate center's own unit of rate. |
| `medicare_nonffs_volume_md_resident` | float64 | 100% | `1738444.0` | Medicare non-FFS (Medicare Advantage) volume, Maryland residents, in the rate center's own unit of rate. |
| `medicare_ffs_revenue_md_resident` | float64 | 100% | `84935202.0` | Medicare fee-for-service revenue, Maryland residents. |
| `medicare_nonffs_revenue_md_resident` | float64 | 100% | `10360056.0` | Non-FFS Medicare (Medicare Advantage) revenue, Maryland residents. |
| `medicaid_volume` | float64 | 10% | `3035432.0` | Medicaid (FY2026 onward only) volume, in the rate center's own unit of rate. |
| `medicaid_revenue` | float64 | 10% | `25023423.69` | Medicaid revenue. FY2026 onward only. |
| `kaiser_volume` | float64 | 10% | `15729.0` | Kaiser Permanente (FY2026 onward only) volume, in the rate center's own unit of rate. |
| `kaiser_revenue` | float64 | 10% | `100915.73` | Kaiser Permanente revenue. FY2026 onward only. |
| `months_reported` | int64 | 100% | `12` | Distinct months present. Compare only complete years. |
| `medicare_revenue_share` | float64 | 100% | `0.5779256891036386` | Medicare share of **revenue**, Maryland residents. |
| `medicare_volume_share` | float64 | 100% | `0.5953583839386206` | Medicare share of **volume**, Maryland residents. A different quantity from the revenue share; the two do not track each other. |
| `md_resident_revenue_share` | float64 | 100% | `0.8551495959968773` | Maryland-resident share of revenue. |
| `medicare_advantage_share_of_medicare_revenue` | float64 | 98% | `0.1087153360768486` | Non-FFS share of Medicare revenue. Relevant to AHEAD, whose targets anchor on FFS. |
| `medicaid_revenue_share` | float64 | 10% | `0.1070061197415623` | Medicaid share of revenue. FY2026 onward only. |

## `hospital_crosswalk`

One row per facility. The join key for everything else. Always join on `hosp_num`, never on a name -- 53 of 66 facilities appear under more than one name across FY2017-FY2026.

| column | type | non-null | example | description |
|---|---|---:|---|---|
| `hosp_num` | int64 | 100% | `1` | HSCRC hospital identifier. Stable across years. **The join key.** |
| `ccn` | int64 | 100% | `210001` | CMS Certification Number. Reported by HSCRC as MCID where available, otherwise derived as 210000 + hosp_num. |
| `ccn_source` | object | 100% | `reported (MCID)` | `reported (MCID)` or `derived`. |
| `hscrc_name` | object | 100% | `Meritus` | Hospital name as used in the most recent fiscal year. |
| `short_name` | object | 100% | `Meritus` | Name with the HSCRC system prefix removed. |
| `system` | object | 68% | `University of Maryland Med` | Health system, expanded from HSCRC's short-name prefix. Null = independent. |
| `cms_name` | object | 73% | `MERITUS MEDICAL CENTER` | Facility name in CMS Hospital General Information. |
| `city` | object | 73% | `HAGERSTOWN` | City, from CMS. |
| `county` | object | 73% | `WASHINGTON` | County, from CMS. |
| `hospital_type` | object | 73% | `Acute Care Hospitals` | CMS facility type. |
| `ownership` | object | 73% | `Voluntary non-profit - Pri` | CMS ownership category. |
| `emergency_services` | object | 73% | `Yes` | Whether CMS records emergency services. |
| `cms_star_rating` | object | 73% | `3` | CMS overall hospital rating, where assigned. |
| `cms_matched` | bool | 100% | `True` | True if the CCN matched a current CMS record. False is expected for freestanding EDs, psychiatric and chronic-care facilities, and closed hospitals. |
| `first_observed_fy` | int64 | 100% | `2017` | First fiscal year the facility appears in the Experience files. |
| `last_observed_fy` | int64 | 100% | `2026` | Last fiscal year it appears. |
| `name_changed` | bool | 100% | `False` | True if more than one name variant was observed. |
| `name_history` | object | 100% | `Meritus (FY2017-FY2026)` | Every name variant with the fiscal-year range it was used. Retains HSCRC's raw `_x0020_` export artifacts so the provenance stays visible. |

## `quality_adjustments`

One row per hospital per rate year per program. The four quality programs that move a hospital's approved revenue: MHAC (Maryland Hospital Acquired Conditions), PAU (Potentially Avoidable Utilization), RRIP (Readmissions Reduction Incentive Program) and QBR (Quality Based Reimbursement). A program reports a percentage, a dollar amount, or both, depending on the rate year, so both columns are kept and either may be null.

| column | type | non-null | example | description |
|---|---|---:|---|---|
| `rate_year` | int64 | 100% | `2016` | HSCRC rate year, ending 30 June. Rate year 2019 covers July 2018 to June 2019. |
| `program` | object | 100% | `MHAC` | Quality program: `MHAC`, `PAU`, `RRIP` or `QBR`. |
| `hospid` | int64 | 100% | `60` | HSCRC hospital identifier as printed in the rate-setting files. Same numbering as `hosp_num`. |
| `hospital_name` | object | 95% | `Fort Washington Medical Ce` | Hospital name as printed in the source file. |
| `pct_adjustment` | float64 | 99% | `-0.01` | Adjustment to approved revenue, as a proportion. Negative is a reduction. |
| `dollar_adjustment` | float64 | 92% | `0.0` | Adjustment in nominal dollars, where the source file reports one. |
| `source_file` | object | 100% | `ry2016__fy_2015_scaling_fo` | Originating HSCRC file name. |
| `pct_column` | object | 100% | `REVENUE NEUTRAL ADJUSTED P` | Label of the source-file column the percentage was read from. Labels vary by rate year, so the original is retained. |
| `dollar_column` | object | 93% | `$ Revenue Adjustment` | Label of the source-file column the dollar amount was read from. |

## `rate_center_reference`

Official HSCRC rate center codes with names and units of rate, sourced to HSCRC documents, joined to what each code actually accounts for in the panel.

| column | type | non-null | example | description |
|---|---|---:|---|---|
| `rate_center` | object | 100% | `ADD` | HSCRC rate center code. See `rate_center_reference`. |
| `center_name` | object | 98% | `Adolescent Neuropsychiatry` | Official rate center name from an HSCRC document. |
| `schedule` | object | 76% | `A26` | HSCRC schedule line (A daily service, B ambulatory, C ancillary). |
| `category` | object | 93% | `Daily service` | Daily service, Ambulatory, or Ancillary. |
| `unit_of_rate` | object | 91% | `Patient days` | The unit the rate is set per. Differs by centre; volumes are not comparable across centres. |
| `source` | object | 98% | `CCD;SUB` | Which HSCRC document defines the code. `UNCONFIRMED` = present in the data, defined in no document located. |
| `fy_first` | float64 | 99% | `2017.0` | First fiscal year this rate center appears in the panel. |
| `fy_last` | float64 | 99% | `2026.0` | Last fiscal year it appears. |
| `total_revenue_all_years` | float64 | 99% | `79928084.0` | Revenue accounted for by this centre across the whole panel. |
| `inpatient_revenue_share` | float64 | 95% | `1.0` | Share of this centre's revenue billed inpatient. |
| `in_published_panel` | bool | 100% | `True` | Whether the code appears in the published revenue panel. |

## `statewide_annual`

Statewide roll-up. Check `months_reported` before comparing years.

| column | type | non-null | example | description |
|---|---|---:|---|---|
| `fiscal_year` | int64 | 100% | `2017` | Maryland fiscal year, ending 30 June. FY2025 = July 2024 - June 2025. |
| `volume` | float64 | 100% | `3259907236.86` | Volume in the rate center's own unit of rate -- patient days, RVUs, minutes, procedures, or cost-denominated EIPA. **Not comparable across rate centers.** |
| `revenue` | float64 | 100% | `17136581709.62` | Regulated revenue, all payers, nominal dollars. |
| `volume_md_resident` | float64 | 100% | `2921711339.57` | Volume attributable to Maryland residents. |
| `volume_nonmd_resident` | float64 | 100% | `338195871.55` | Volume attributable to out-of-state residents. |
| `revenue_md_resident` | float64 | 100% | `15661391649.94` | Revenue from Maryland residents. |
| `revenue_nonmd_resident` | float64 | 100% | `1475189998.13` | Revenue from out-of-state residents. |
| `medicare_volume_md_resident` | float64 | 100% | `1221975186.95` | Medicare volume, Maryland residents. |
| `medicare_volume_nonmd_resident` | float64 | 100% | `144122530.96` | Medicare volume, out-of-state residents, in the rate center's own unit of rate. |
| `medicare_revenue_md_resident` | float64 | 100% | `6323934962.7` | Medicare revenue, Maryland residents. |
| `medicare_revenue_nonmd_resident` | float64 | 100% | `576447645.41` | Medicare revenue, out-of-state residents, in nominal dollars. |
| `medicare_ffs_volume_md_resident` | float64 | 100% | `1141731342.91` | Medicare fee-for-service volume, Maryland residents, in the rate center's own unit of rate. |
| `medicare_nonffs_volume_md_resident` | float64 | 100% | `80243827.93` | Medicare non-FFS (Medicare Advantage) volume, Maryland residents, in the rate center's own unit of rate. |
| `medicare_ffs_revenue_md_resident` | float64 | 100% | `5885072874.84` | Medicare fee-for-service revenue, Maryland residents. |
| `medicare_nonffs_revenue_md_resident` | float64 | 100% | `438862063.44` | Non-FFS Medicare (Medicare Advantage) revenue, Maryland residents. |
| `medicaid_volume` | float64 | 10% | `157690030.35` | Medicaid (FY2026 onward only) volume, in the rate center's own unit of rate. |
| `medicaid_revenue` | float64 | 10% | `1311870329.26` | Medicaid revenue. FY2026 onward only. |
| `kaiser_volume` | float64 | 10% | `31630227.96` | Kaiser Permanente (FY2026 onward only) volume, in the rate center's own unit of rate. |
| `kaiser_revenue` | float64 | 10% | `203936341.69` | Kaiser Permanente revenue. FY2026 onward only. |
| `n_hospitals` | int64 | 100% | `59` | Facilities reporting that year. Moves between 58 and 62. |
| `n_rate_centers` | int64 | 100% | `75` | Distinct rate centers that year. |
| `months_reported` | int64 | 100% | `12` | Distinct months present. Compare only complete years. |
| `revenue_inpatient` | float64 | 100% | `9849745404.69` | Regulated revenue, inpatient setting. |
| `revenue_outpatient` | float64 | 100% | `7286836304.93` | Regulated revenue, outpatient setting. |
| `outpatient_revenue_share` | float64 | 100% | `0.4252211105111688` | Outpatient share of regulated revenue. **Regulated only** -- care that migrates to an unregulated setting leaves this measure entirely. |
| `inpatient_admissions` | float64 | 100% | `564644.04` | Volume of the ADM rate center. Not ECMAD. |
| `medicare_revenue_share` | float64 | 100% | `0.4037913809992886` | Medicare share of **revenue**, Maryland residents. |
| `medicare_volume_share` | float64 | 100% | `0.4182395332489769` | Medicare share of **volume**, Maryland residents. A different quantity from the revenue share; the two do not track each other. |
| `md_resident_revenue_share` | float64 | 100% | `0.9139157339148992` | Maryland-resident share of revenue. |
| `medicare_advantage_share_of_medicare_revenue` | float64 | 100% | `0.0693969919090736` | Non-FFS share of Medicare revenue. Relevant to AHEAD, whose targets anchor on FFS. |
| `medicaid_revenue_share` | float64 | 10% | `0.0713603731303199` | Medicaid share of revenue. FY2026 onward only. |

## `revenue_volume_monthly`

Hospital x month x rate center x setting. The `_IN`/`_OUT` suffix in the HSCRC source means INPATIENT/OUTPATIENT; patient residency is a separate dimension, carried here in the `_md_resident` / `_nonmd_resident` column suffixes. Medicaid and Kaiser columns are populated from FY2026 only.

| column | type | non-null | example | description |
|---|---|---:|---|---|
| `hosp_num` | int64 | 100% | `1` | HSCRC hospital identifier. Stable across years. **The join key.** |
| `ccn` | int64 | 100% | `210001` | CMS Certification Number. Reported by HSCRC as MCID where available, otherwise derived as 210000 + hosp_num. |
| `fiscal_year` | int64 | 100% | `2017` | Maryland fiscal year, ending 30 June. FY2025 = July 2024 - June 2025. |
| `report_date` | object | 100% | `2016-07-01` | First day of the reported month. |
| `rate_center` | object | 100% | `ADM` | HSCRC rate center code. See `rate_center_reference`. |
| `setting` | object | 100% | `inpatient` | `inpatient` or `outpatient`. |
| `volume` | float64 | 100% | `1353.0` | Volume in the rate center's own unit of rate -- patient days, RVUs, minutes, procedures, or cost-denominated EIPA. **Not comparable across rate centers.** |
| `revenue` | float64 | 100% | `217768.0` | Regulated revenue, all payers, nominal dollars. |
| `volume_md_resident` | float64 | 100% | `1174.0` | Volume attributable to Maryland residents. |
| `volume_nonmd_resident` | float64 | 100% | `179.0` | Volume attributable to out-of-state residents. |
| `revenue_md_resident` | float64 | 100% | `189201.0` | Revenue from Maryland residents. |
| `revenue_nonmd_resident` | float64 | 100% | `28567.0` | Revenue from out-of-state residents. |
| `medicare_volume_md_resident` | float64 | 100% | `609.0` | Medicare volume, Maryland residents. |
| `medicare_volume_nonmd_resident` | float64 | 100% | `71.0` | Medicare volume, out-of-state residents, in the rate center's own unit of rate. |
| `medicare_revenue_md_resident` | float64 | 100% | `97949.0` | Medicare revenue, Maryland residents. |
| `medicare_revenue_nonmd_resident` | float64 | 100% | `11504.0` | Medicare revenue, out-of-state residents, in nominal dollars. |
| `medicare_ffs_volume_md_resident` | float64 | 100% | `550.0` | Medicare fee-for-service volume, Maryland residents, in the rate center's own unit of rate. |
| `medicare_nonffs_volume_md_resident` | float64 | 100% | `59.0` | Medicare non-FFS (Medicare Advantage) volume, Maryland residents, in the rate center's own unit of rate. |
| `medicare_ffs_volume_nonmd_resident` | float64 | 100% | `56.0` | Medicare fee-for-service volume, out-of-state residents, in the rate center's own unit of rate. |
| `medicare_nonffs_volume_nonmd_resident` | float64 | 100% | `15.0` | Medicare non-FFS (Medicare Advantage) volume, out-of-state residents, in the rate center's own unit of rate. |
| `medicare_ffs_revenue_md_resident` | float64 | 100% | `88453.0` | Medicare fee-for-service revenue, Maryland residents. |
| `medicare_nonffs_revenue_md_resident` | float64 | 100% | `9496.0` | Non-FFS Medicare (Medicare Advantage) revenue, Maryland residents. |
| `medicare_ffs_revenue_nonmd_resident` | float64 | 100% | `9189.0` | Medicare fee-for-service revenue, out-of-state residents, in nominal dollars. |
| `medicare_nonffs_revenue_nonmd_resident` | float64 | 100% | `2315.0` | Medicare non-FFS (Medicare Advantage) revenue, out-of-state residents, in nominal dollars. |
| `beds_reported` | int64 | 100% | `0` | CNTR_BED as reported by HSCRC. |
| `admissions_reported` | int64 | 100% | `0` | CNTR_ADM as reported by HSCRC. |
| `service_type` | object | 100% | `AN` | HSCRC SER_TYPE flag as reported. |
| `flag_payer_exceeds_total` | bool | 100% | `False` | True where a payer subtotal exceeds its all-payer total. ~0.45% of rows, concentrated in CDS and MSS, which are cost-denominated. Rows are kept, not dropped. Filter these out for analyses needing strictly additive payer shares. |
| `flag_negative_value` | bool | 100% | `False` | True where volume or revenue is negative, which signals a retrospective adjustment posted to a later month. |
| `source_file` | object | 100% | `FY17-Final-Experience-Data` | Originating HSCRC file name. |
| `fiscal_year_complete` | bool | 100% | `True` | False where the source file does not yet cover all 12 months. |
| `medicaid_volume` | float64 | 0% | `` | Medicaid (FY2026 onward only) volume, in the rate center's own unit of rate. |
| `medicaid_revenue` | float64 | 0% | `` | Medicaid revenue. FY2026 onward only. |
| `medicaid_volume_md_resident` | float64 | 0% | `` | Medicaid (FY2026 onward only) volume, Maryland residents, in the rate center's own unit of rate. |
| `medicaid_volume_nonmd_resident` | float64 | 0% | `` | Medicaid (FY2026 onward only) volume, out-of-state residents, in the rate center's own unit of rate. |
| `medicaid_revenue_md_resident` | float64 | 0% | `` | Medicaid (FY2026 onward only) revenue, Maryland residents, in nominal dollars. |
| `medicaid_revenue_nonmd_resident` | float64 | 0% | `` | Medicaid (FY2026 onward only) revenue, out-of-state residents, in nominal dollars. |
| `kaiser_volume` | float64 | 0% | `` | Kaiser Permanente (FY2026 onward only) volume, in the rate center's own unit of rate. |
| `kaiser_revenue` | float64 | 0% | `` | Kaiser Permanente revenue. FY2026 onward only. |
| `kaiser_volume_md_resident` | float64 | 0% | `` | Kaiser Permanente (FY2026 onward only) volume, Maryland residents, in the rate center's own unit of rate. |
| `kaiser_volume_nonmd_resident` | float64 | 0% | `` | Kaiser Permanente (FY2026 onward only) volume, out-of-state residents, in the rate center's own unit of rate. |
| `kaiser_revenue_md_resident` | float64 | 0% | `` | Kaiser Permanente (FY2026 onward only) revenue, Maryland residents, in nominal dollars. |
| `kaiser_revenue_nonmd_resident` | float64 | 0% | `` | Kaiser Permanente (FY2026 onward only) revenue, out-of-state residents, in nominal dollars. |
