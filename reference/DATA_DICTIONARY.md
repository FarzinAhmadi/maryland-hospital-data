# Data dictionary

Generated from the built tables. Types, null rates and ranges are read off the actual output, so this file cannot drift from the data.


## `REVIEW_conflicting_report_rows`



| column | type | non-null | example | description |
|---|---|---:|---|---|
| `report_fy` | int64 | 100% | `2021` | Fiscal year of the source report. |
| `source_file` | object | 100% | `Hospital Financial Conditi` | Originating HSCRC file name, as listed in reference/source_manifest.md. |
| `entity_key` | object | 100% | `UPMC WESTERN MARYLAND` | Hospital or group label exactly as the source report prints it, before name resolution. |
| `fiscal_year` | int64 | 100% | `2020` | Fiscal year as the report prints it: the year in which the hospital's own reporting year ends. See `financial_hospital_annual.fy_end_month`. |
| `field` | object | 100% | `gross_revenue_regulated` | Name of the field, in this dataset's column naming, that the report gives two or more values for. |
| `n_distinct_values` | int64 | 100% | `2` | How many different values that one report gives for this hospital, year and field. |
| `min_value` | float64 | 100% | `202274300.0` | Smallest of those values. |
| `max_value` | float64 | 100% | `317291500.0` | Largest of those values. |
| `n_rows` | int64 | 100% | `2` | How many rows in the report carry a value for this hospital, year and field. |
| `value_used` | float64 | 100% | `317291500.0` | The value published for this entity, fiscal year and field in the financial tables. It can come from a later report than the one listed here, under the restatement rule. |

## `REVIEW_unresolved_hospital_names`

A to-do list, not a dataset. PDF hospital names that no automated match resolved with high confidence or by a recorded decision. Each stays unlinked until a decision with its evidence is added to `reference/name_overrides.csv`; `reference/NAME_RESOLUTION.md` sets out the rule.

| column | type | non-null | example | description |
|---|---|---:|---|---|
| `entity` | object | 100% | `ADVENTIST REHAB HOSPITAL O` | Entity name as printed in the source PDF, normalised for case and punctuation. |
| `hosp_num` | float64 | 0% | `` | HSCRC hospital identifier. Stable across years. **The join key.** |
| `match_score` | float64 | 75% | `0.808` | Name-match score, 0-1. |
| `matched_name` | object | 25% | `Adventist Rehab` | The best-scoring crosswalk name. For `medium` rows it is a candidate only and is not applied. |
| `match_basis` | object | 100% | `override (checked)` | Which name field produced the match, or `override (<evidence>)` for a recorded decision. |
| `match_confidence` | object | 100% | `none` | `high`: name-match score of 0.85 or more, `hosp_num` set. `decided`: set by `reference/name_overrides.csv`, which records the evidence. `medium` (0.62 to 0.85) and `none`: no `hosp_num`; for `medium` the best candidate is shown in `matched_name` but not applied. |
| `reviewer_note` | float64 | 0% | `` | Blank column for a human to fill in when resolving a name. |

## `approved_gbr_targets`

One row per hospital per rate year. The approved global budget target as published in HSCRC's mid-year and final target files for that rate year. This is what a hospital was permitted to collect; `hospital_annual.revenue` is what it collected. Join on `hosp_num`. `hospid` is kept as printed: it is the CMS Certification Number less 210000, which differs from `hosp_num` for five facilities, and the source files use both forms in different rate years.

| column | type | non-null | example | description |
|---|---|---:|---|---|
| `rate_year` | int64 | 100% | `2019` | HSCRC rate year, ending 30 June. Rate year 2019 covers July 2018 to June 2019. |
| `hospid` | int64 | 100% | `1` | Hospital identifier as printed in the rate-setting file: the CMS Certification Number less 210000, or for some rate years the HSCRC hospital number. Use `hosp_num` to join. |
| `hosp_num` | int64 | 100% | `1` | HSCRC hospital identifier. Stable across years. **The join key.** |
| `md_provider_number` | float64 | 96% | `210001.0` | Maryland provider number as printed in the source file. Equivalent to the CMS Certification Number, 210000 + hospital number. |
| `hospital_name` | object | 96% | `Meritus Medical Center` | Hospital name as printed in the source file. |
| `mid_year_target` | float64 | 79% | `185075342.2192` | Approved revenue target at the mid-year update, nominal dollars. |
| `year_end_target` | float64 | 97% | `370256624.1269` | Approved revenue target at the year-end update, nominal dollars. |
| `source_file` | object | 100% | `RY 2019_05_01_19- Final.xl` | Originating HSCRC file name, as listed in reference/source_manifest.md. |
| `source_sheet` | object | 100% | `Mid-Final Targets` | Worksheet of `source_file` the row was read from. |

## `disclosure_hospital_annual`

Hospital x fiscal year, extracted from the HSCRC Disclosure Reports, which cover years earlier than the Financial Condition Reports behind `financial_hospital_annual`. Column names are the report's own section and row labels, flattened and normalised, so several carry a section prefix and a few labels repeat in prefixed and bare form. Values are as printed. One row per `hosp_num` and fiscal year; the most recent report wins. Treat this table as provisional: it comes from a second, page-based PDF extraction and only the lines that `financial_hospital_annual` also carries have been checked against it. Column definitions were checked against the reports' explanation pages in November 2026. Percent columns are stored in percentage points (7.34 means 7.34%), unlike the shares in the revenue tables, which are proportions. The FY2014 report's line 'Potentially Preventable Conditions (PPC) Costs' is not extracted.

| column | type | non-null | example | description |
|---|---|---:|---|---|
| `hosp_num` | int64 | 100% | `1` | HSCRC hospital identifier. Stable across years. **The join key.** |
| `fiscal_year` | int64 | 100% | `2007` | Fiscal year as the report prints it: the year in which the hospital's own reporting year ends. See `fy_end_month`; most are June. |
| `fy_end_month` | object | 100% | `June` | Month the hospital's fiscal year ends, as printed in the report. Most are June. |
| `hospital_key` | object | 100% | `WASHINGTON COUNTY HOSPITAL` | Entity name as printed in the source PDF, used to match the row to `hosp_num`. |
| `match_score` | float64 | 71% | `1.0` | Name-match score, 0-1. |
| `match_basis` | object | 100% | `override (restatement)` | Which name field produced the match, or `override (<evidence>)` for a recorded decision. |
| `equivalent_inpatient_adms_eipa_regulated_services` | float64 | 54% | `22989.0` | Equivalent inpatient admissions (EIPA), an HSCRC volume statistic: admissions plus outpatient visits converted to admission equivalents, computed as admissions x gross patient revenue / gross inpatient revenue. Count of equivalent admissions. Values for fiscal years 2007 to 2013. Defined in the FY2009 Disclosure Report, p. 10. |
| `equivalent_inpatient_adms_eipa_total` | float64 | 54% | `27880.0` | Equivalent inpatient admissions (EIPA), an HSCRC volume statistic: admissions plus outpatient visits converted to admission equivalents, computed as admissions x gross patient revenue / gross inpatient revenue. Total line as printed (regulated and unregulated). Count of equivalent admissions. Values for fiscal years 2007 to 2013. Defined in the FY2009 Disclosure Report, p. 10. |
| `gross_patient_revenue_regulated_services` | float64 | 100% | `206966700.0` | Gross patient revenue for HSCRC rate-regulated services: patient care charges at established rates, whether or not the hospital expects to collect them, recorded in the period the service is provided. Includes charity care and bad debts. Nominal dollars. Values for fiscal years 2007 to 2018. Defined in the FY2018 Disclosure Report, p. 8. |
| `gross_patient_revenue_total` | float64 | 100% | `251003000.0` | Gross patient revenue for regulated and unregulated services combined (the report's TOTAL line): all patient care charges at established rates, accrual basis, charity care and bad debts included. Nominal dollars. Values for fiscal years 2007 to 2018. Defined in the FY2018 Disclosure Report, p. 8. |
| `gross_patient_revenue_unregulated_services` | float64 | 100% | `44036300.0` | Gross patient revenue for services whose rates HSCRC does not set, on the same basis as the regulated line: charges at established rates, accrual basis, charity care and bad debts included. Nominal dollars. Values for fiscal years 2007 to 2018. Defined in the FY2018 Disclosure Report, p. 8. |
| `inpatient_readmission_charges` | float64 | 52% | `27005043.0` | Inpatient readmission charges: revenue for 30-day, all-cause, all-hospital inpatient readmissions (planned readmissions excluded), the readmission part of HSCRC's potentially avoidable utilization measure. From the FY2015 report on, the charges are assigned to the hospital that receives the readmission. Nominal dollars. Values for fiscal years 2012 to 2018. The reports do not define this line separately; the definition is the readmission measure HSCRC describes under PAU. Each fiscal year comes from the latest report that prints it. HSCRC revised the PAU method between reports, so a value can differ from the figure first published for that year, and years taken from different reports may use different methods. Defined in the FY2018 Disclosure Report, p. 9. |
| `net_operating_profit_loss_regulated_services` | float64 | 100% | `5883961.0` | Net operating profit or loss (regulated): net operating revenue less operating expenses, i.e. the result of ordinary, recurring operations. Includes restricted donations for operating purposes spent as the donor intended during the year. Nominal dollars. Values for fiscal years 2007 to 2018. Defined in the FY2018 Disclosure Report, p. 9. |
| `net_operating_profit_loss_total` | float64 | 100% | `4479800.0` | Net operating profit or loss (regulated and unregulated combined): net operating revenue less operating expenses, i.e. the result of ordinary, recurring operations. Includes restricted donations for operating purposes spent as the donor intended during the year. Nominal dollars. Values for fiscal years 2007 to 2018. Defined in the FY2018 Disclosure Report, p. 9. |
| `net_operating_profit_loss_unregulated_services` | float64 | 100% | `-1404161.0` | Net operating profit or loss (unregulated): net operating revenue less operating expenses, i.e. the result of ordinary, recurring operations. Includes restricted donations for operating purposes spent as the donor intended during the year. The report prints no unregulated expense line; this equals unregulated NOR minus (total less regulated operating expenses). Nominal dollars. Values for fiscal years 2007 to 2018. Defined in the FY2018 Disclosure Report, p. 9. |
| `net_operating_revenue_nor_regulated_services` | float64 | 100% | `177059800.0` | Net operating revenue (regulated): net patient revenue plus other operating revenue. Nominal dollars. Values for fiscal years 2007 to 2018. Defined in the FY2018 Disclosure Report, p. 8. |
| `net_operating_revenue_nor_total` | float64 | 100% | `207734800.0` | Net operating revenue (regulated and unregulated combined): net patient revenue plus other operating revenue. Nominal dollars. Values for fiscal years 2007 to 2018. Defined in the FY2018 Disclosure Report, p. 8. |
| `net_operating_revenue_nor_unregulated_services` | float64 | 100% | `30675000.0` | Net operating revenue (unregulated): net patient revenue plus other operating revenue. Nominal dollars. Values for fiscal years 2007 to 2018. Defined in the FY2018 Disclosure Report, p. 8. |
| `net_patient_revenue_npr_regulated_services` | float64 | 100% | `175218800.0` | Net patient revenue (regulated): the patient care revenue the hospital actually realized, that is gross patient revenue less contractual allowances, charity care, bad debts and payer denials, on an accrual basis. Nominal dollars. Values for fiscal years 2007 to 2018. Defined in the FY2018 Disclosure Report, p. 8. |
| `net_patient_revenue_npr_total` | float64 | 100% | `204111200.0` | Net patient revenue (regulated and unregulated combined): the patient care revenue the hospital actually realized, that is gross patient revenue less contractual allowances, charity care, bad debts and payer denials, on an accrual basis. Nominal dollars. Values for fiscal years 2007 to 2018. Defined in the FY2018 Disclosure Report, p. 8. |
| `net_patient_revenue_npr_unregulated_services` | float64 | 100% | `28892400.0` | Net patient revenue (unregulated): the patient care revenue the hospital actually realized, that is gross patient revenue less contractual allowances, charity care, bad debts and payer denials, on an accrual basis. Nominal dollars. Values for fiscal years 2007 to 2018. Defined in the FY2018 Disclosure Report, p. 8. |
| `nor_per_eipa_regulated_services` | float64 | 54% | `7702.04` | Net operating revenue per EIPA, regulated: regulated NOR divided by regulated EIPAs. Nominal dollars per EIPA. Values for fiscal years 2007 to 2013. Not defined in the reports; this formula reproduces the printed values. |
| `nor_per_eipa_total` | float64 | 54% | `7451.04` | Net operating revenue per EIPA, total: total NOR divided by total EIPAs. Nominal dollars per EIPA. Values for fiscal years 2007 to 2013. Not defined in the reports; this formula reproduces the printed values. |
| `npr_per_eipa_regulated_services` | float64 | 54% | `7621.96` | Net patient revenue per EIPA, regulated: regulated NPR divided by regulated EIPAs. Nominal dollars per EIPA. Values for fiscal years 2007 to 2013. Not defined in the reports; this formula reproduces the printed values. |
| `npr_per_eipa_total` | float64 | 54% | `7321.06` | Net patient revenue per EIPA, total: total NPR divided by total EIPAs. Nominal dollars per EIPA. Values for fiscal years 2007 to 2013. Not defined in the reports; this formula reproduces the printed values. |
| `operating_expenses_per_eipa_regulated_services` | float64 | 54% | `7446.09` | Operating expenses per EIPA, regulated (the report's 'average cost per EIPA'): regulated operating expenses divided by regulated EIPAs. Nominal dollars per EIPA. Values for fiscal years 2007 to 2013. Defined in the FY2009 Disclosure Report, p. 10. |
| `operating_expenses_per_eipa_total` | float64 | 54% | `7290.35` | Operating expenses per EIPA, total: total operating expenses divided by total EIPAs. Nominal dollars per EIPA. Values for fiscal years 2007 to 2013. Defined in the FY2009 Disclosure Report, p. 10. |
| `other_operating_revenue_regulated_services` | float64 | 100% | `1841000.0` | Other operating revenue (regulated): revenue from normal day-to-day operations other than patient care, such as sales and services to non-patients, rental of hospital space, cafeteria and gift shop sales, research, and Medicare Part B physician services. Nominal dollars. Values for fiscal years 2007 to 2018. Defined in the FY2018 Disclosure Report, p. 8. |
| `other_operating_revenue_total` | float64 | 100% | `3623600.0` | Other operating revenue (regulated and unregulated combined): revenue from normal day-to-day operations other than patient care, such as sales and services to non-patients, rental of hospital space, cafeteria and gift shop sales, research, and Medicare Part B physician services. Nominal dollars. Values for fiscal years 2007 to 2018. Defined in the FY2018 Disclosure Report, p. 8. |
| `other_operating_revenue_unregulated_services` | float64 | 100% | `1782600.0` | Other operating revenue (unregulated): revenue from normal day-to-day operations other than patient care, such as sales and services to non-patients, rental of hospital space, cafeteria and gift shop sales, research, and Medicare Part B physician services. Nominal dollars. Values for fiscal years 2007 to 2018. Defined in the FY2018 Disclosure Report, p. 8. |
| `potentially_avoidable_utilization_costs` | float64 | 52% | `44766008.0` | Potentially avoidable utilization (PAU) costs: hospital revenue for care that is unplanned and may be prevented through better care, care coordination and community care. Measured as 30-day all-cause readmissions (planned excluded) plus admissions for AHRQ Prevention Quality Indicators (the 12 PQIs in the PQI-90 composite plus PQI 02, not PQI 09), over inpatient discharges and observation stays of more than 23 hours. Nominal dollars. Values for fiscal years 2012 to 2018. Each fiscal year comes from the latest report that prints it. HSCRC revised the PAU method between reports, so a value can differ from the figure first published for that year, and years taken from different reports may use different methods. The FY2014 report also counted potentially preventable complication costs in PAU; later reports do not. Defined in the FY2018 Disclosure Report, p. 9. |
| `risk_adjusted_ppc_rate` | float64 | 52% | `1.38` | Case-mix adjusted rate of potentially preventable complications (3M PPC measures: harmful events or negative outcomes after admission that result from care rather than the underlying disease; conditions present on admission excluded). Rate as printed; the reports do not state its scale (values cluster around 1). Values for fiscal years 2012 to 2018. Each fiscal year comes from the latest report that prints it. HSCRC revised the PAU method between reports, so a value can differ from the figure first published for that year, and years taken from different reports may use different methods. Defined in the FY2018 Disclosure Report, p. 10. |
| `risk_adjusted_readmission_percent` | float64 | 52% | `11.64` | Risk-adjusted readmission rate under the Readmission Reduction Incentive Program method: 30-day all-payer, all-hospital (intra- and inter-hospital) readmissions, adjusted for severity (APR-DRG severity of illness) and planned admissions. Inpatient discharges only, assigned to the index hospital. Percent, stored in percentage points (7.34 means 7.34%). Values for fiscal years 2012 to 2018. Each fiscal year comes from the latest report that prints it. HSCRC revised the PAU method between reports, so a value can differ from the figure first published for that year, and years taken from different reports may use different methods. Defined in the FY2018 Disclosure Report, pp. 9-10. |
| `total_direct_graduate_medical_education` | float64 | 18% | `0.0` | Direct graduate medical education expenditures: costs directly related to training residents, including residents' stipends and fringe benefits and the salaries and fringe benefits of the faculty who supervise them. Nominal dollars. Values for fiscal years 2012 to 2015. Same measure as `total_direct_medical_education`; the printed label changed between reports. Defined in the FY2018 Disclosure Report, p. 10. |
| `total_direct_medical_education` | float64 | 44% | `0.0` | Direct graduate medical education expenditures: costs directly related to training residents, including residents' stipends and fringe benefits and the salaries and fringe benefits of the faculty who supervise them. Nominal dollars. Values for fiscal years 2013 to 2018. Same measure as `total_direct_graduate_medical_education` (the FY2015 report's label); the printed label changed between reports. Defined in the FY2018 Disclosure Report, p. 10. |
| `total_excess_profit_loss` | float64 | 60% | `4712400.0` | Total excess profit or loss: the bottom line of the hospital's audited financial statements, equal to net operating profit (loss) plus non-operating profit (loss). Income tax provisions of proprietary hospitals are excluded. Nominal dollars. Values for fiscal years 2012 to 2018. The same line in the FY2009 to FY2013 reports is `total_non_operating_profit_loss_total_excess_profit`. Defined in the FY2018 Disclosure Report, p. 9. |
| `total_excess_profit_loss_net_operating_profit_of_regulated_nor` | float64 | 52% | `4.04` | Regulated net operating profit as a percent of regulated net operating revenue. Percent, stored in percentage points (7.34 means 7.34%). Values for fiscal years 2012 to 2018. The `total_excess_profit_loss_` prefix is the preceding heading carried forward; this is the same printed line as the `total_non_operating_profit_loss_` version from the FY2009 to FY2013 reports. Not defined in the reports; this formula reproduces the printed values. |
| `total_excess_profit_loss_net_total_operating_profit_of_total_nor` | float64 | 52% | `3.73` | Total net operating profit as a percent of total net operating revenue. Percent, stored in percentage points (7.34 means 7.34%). Values for fiscal years 2012 to 2018. The `total_excess_profit_loss_` prefix is the preceding heading carried forward; this is the same printed line as the `total_non_operating_profit_loss_` version from the FY2009 to FY2013 reports. Not defined in the reports; this formula reproduces the printed values. |
| `total_excess_profit_loss_total_excess_profit_of_total_revenue` | float64 | 52% | `2.98` | Total excess profit as a percent of total revenue, where total revenue is total net operating revenue plus non-operating revenue. Percent, stored in percentage points (7.34 means 7.34%). Values for fiscal years 2012 to 2018. The `total_excess_profit_loss_` prefix is the preceding heading carried forward; this is the same printed line as the `total_non_operating_profit_loss_` version from the FY2009 to FY2013 reports. Not defined in the reports; this formula reproduces the printed values. |
| `total_non_operating_profit_loss` | float64 | 100% | `1971600.0` | Total non-operating profit or loss: investment income (realized and unrealized, in the FY2018 wording), extraordinary gains and other non-operating gains and losses, i.e. non-operating revenue less non-operating expenses. Nominal dollars. Values for fiscal years 2007 to 2018. Defined in the FY2018 Disclosure Report, p. 9. |
| `total_non_operating_profit_loss_change_in_net_operating_profit_regulated` | float64 | 54% | `-12.05` | Percent change in regulated net operating profit from the prior fiscal year. Percent change, stored in percentage points. Values for fiscal years 2007 to 2013. The `total_non_operating_profit_loss_` prefix is an extraction artifact: the report prints this line on its own. It will be renamed in a later version. Not defined in the reports; this formula reproduces the printed values. |
| `total_non_operating_profit_loss_change_in_net_operating_profit_total` | float64 | 54% | `-5.15` | Percent change in total net operating profit from the prior fiscal year. Percent change, stored in percentage points. Values for fiscal years 2007 to 2013. The `total_non_operating_profit_loss_` prefix is an extraction artifact: the report prints this line on its own. It will be renamed in a later version. Not defined in the reports; this formula reproduces the printed values. |
| `total_non_operating_profit_loss_change_in_nor_per_eipa_regulated` | float64 | 54% | `6.68` | Percent change in regulated NOR per EIPA from the prior fiscal year. Percent change, stored in percentage points. Values for fiscal years 2007 to 2013. The `total_non_operating_profit_loss_` prefix is an extraction artifact: the report prints this line on its own. It will be renamed in a later version. Not defined in the reports; this formula reproduces the printed values. |
| `total_non_operating_profit_loss_change_in_npr_per_eipa_regulated` | float64 | 54% | `7.56` | Percent change in regulated NPR per EIPA from the prior fiscal year. Percent change, stored in percentage points. Values for fiscal years 2007 to 2013. The `total_non_operating_profit_loss_` prefix is an extraction artifact: the report prints this line on its own. It will be renamed in a later version. Not defined in the reports; this formula reproduces the printed values. |
| `total_non_operating_profit_loss_change_in_oper_expense_per_eipa_regulated` | float64 | 54% | `7.5` | Percent change in regulated operating expenses per EIPA from the prior fiscal year. Percent change, stored in percentage points. Values for fiscal years 2007 to 2013. The `total_non_operating_profit_loss_` prefix is an extraction artifact: the report prints this line on its own. It will be renamed in a later version. Not defined in the reports; this formula reproduces the printed values. |
| `total_non_operating_profit_loss_change_in_total_excess_profit` | float64 | 54% | `-0.15` | Percent change in total excess profit from the prior fiscal year. Percent change, stored in percentage points. Values for fiscal years 2007 to 2013. Not defined in the reports; this formula reproduces the printed values. |
| `total_non_operating_profit_loss_net_operating_profit_of_regulated_nor` | float64 | 55% | `3.32` | Regulated net operating profit as a percent of regulated net operating revenue. Percent, stored in percentage points (7.34 means 7.34%). Values for fiscal years 2007 to 2013. The `total_non_operating_profit_loss_` prefix is an extraction artifact: the report prints this line on its own. It will be renamed in a later version. Not defined in the reports; this formula reproduces the printed values. |
| `total_non_operating_profit_loss_net_total_operating_profit_of_total_nor` | float64 | 55% | `2.16` | Total net operating profit as a percent of total net operating revenue. Percent, stored in percentage points (7.34 means 7.34%). Values for fiscal years 2007 to 2013. The `total_non_operating_profit_loss_` prefix is an extraction artifact: the report prints this line on its own. It will be renamed in a later version. Not defined in the reports; this formula reproduces the printed values. |
| `total_non_operating_profit_loss_non_operating_expenses` | float64 | 100% | `346200.0` | Non-operating expenses as printed under Total Non-Operating Profit (Loss). Zero for many hospitals. Nominal dollars. Values for fiscal years 2007 to 2018. Defined in the FY2018 Disclosure Report, p. 9. |
| `total_non_operating_profit_loss_non_operating_revenue` | float64 | 100% | `2317800.0` | Non-operating revenue as printed under Total Non-Operating Profit (Loss). It can be negative because investment losses are reported here. Nominal dollars. Values for fiscal years 2007 to 2018. Defined in the FY2018 Disclosure Report, p. 9. |
| `total_non_operating_profit_loss_total_excess_profit` | float64 | 55% | `6451400.0` | Total excess profit or loss: the bottom line of the hospital's audited financial statements, equal to net operating profit (loss) plus non-operating profit (loss). Income tax provisions of proprietary hospitals are excluded. Nominal dollars. Values for fiscal years 2007 to 2013. The `total_non_operating_profit_loss_` prefix is an extraction artifact: the report prints this line on its own. It will be renamed in a later version. Same line as `total_excess_profit_loss` in later reports. Defined in the FY2018 Disclosure Report, p. 9. |
| `total_non_operating_profit_loss_total_excess_profit_of_total_revenue` | float64 | 55% | `3.07` | Total excess profit as a percent of total revenue, where total revenue is total net operating revenue plus non-operating revenue. Percent, stored in percentage points (7.34 means 7.34%). Values for fiscal years 2007 to 2013. The `total_non_operating_profit_loss_` prefix is an extraction artifact: the report prints this line on its own. It will be renamed in a later version. Not defined in the reports; this formula reproduces the printed values. |
| `total_operating_expenses_regulated_services` | float64 | 100% | `171175839.0` | Operating expenses for HSCRC-regulated services: the regulated part of total operating expenses, which HSCRC defines as the costs of regulated and unregulated inpatient and outpatient care plus the costs associated with other operating revenue, under GAAP except that bad debts are treated as a reduction in gross patient revenue rather than an expense. Nominal dollars. Values for fiscal years 2007 to 2018. Defined in the FY2018 Disclosure Report, p. 8. |
| `total_operating_expenses_total` | float64 | 100% | `203255000.0` | Total operating expenses, regulated and unregulated: the costs of inpatient and outpatient care plus the costs associated with other operating revenue, under GAAP except that bad debts are treated as a reduction in gross patient revenue. The report prints no separate unregulated line. Nominal dollars. Values for fiscal years 2007 to 2018. Defined in the FY2018 Disclosure Report, p. 8. |
| `total_regulated_inpatient_admissions` | float64 | 20% | `15558.0` | Inpatient admissions to HSCRC-regulated services in the fiscal year. Admissions include infants transferred to the hospital's own neonatal intensive care unit. Count. Values for fiscal years 2012 to 2014. The reports define no more than the line label. |
| `total_regulated_outpatient_visits` | float64 | 20% | `111239.0` | Outpatient visits to HSCRC-regulated services in the fiscal year, as printed. The report does not define a visit. Count. Values for fiscal years 2012 to 2014. The reports define no more than the line label. |
| `net_operating_profit_of_regulated_nor` | float64 | 99% | `3.32` | Regulated net operating profit as a percent of regulated net operating revenue. Percent, stored in percentage points (7.34 means 7.34%). Combines the FY2009 to FY2013 and FY2015 to FY2018 versions of this line. The FY2014 report does not print it, so fiscal year 2014 comes from the FY2015 report. Not defined in the reports; this formula reproduces the printed values. |
| `net_total_operating_profit_of_total_nor` | float64 | 99% | `2.16` | Total net operating profit as a percent of total net operating revenue. Percent, stored in percentage points (7.34 means 7.34%). Combines the FY2009 to FY2013 and FY2015 to FY2018 versions of this line. The FY2014 report does not print it, so fiscal year 2014 comes from the FY2015 report. Not defined in the reports; this formula reproduces the printed values. |
| `total_excess_profit_of_total_revenue` | float64 | 99% | `3.07` | Total excess profit as a percent of total revenue, where total revenue is total net operating revenue plus non-operating revenue. Percent, stored in percentage points (7.34 means 7.34%). Combines the FY2009 to FY2013 and FY2015 to FY2018 versions of this line. The FY2014 report does not print it, so fiscal year 2014 comes from the FY2015 report. Not defined in the reports; this formula reproduces the printed values. |

## `financial_extraction_log`

Per-PDF extraction outcome. Failures are visible here.

| column | type | non-null | example | description |
|---|---|---:|---|---|
| `report_fy` | object | 100% | `FY2003` | Fiscal year of the source report. |
| `file` | object | 100% | `Financial Disclosure FY200` | Source PDF file name. |
| `chars` | int64 | 100% | `133755` | Characters of extractable text. Near zero means a scanned image. |
| `status` | object | 100% | `NO_BLOCKS_PARSED` | Extraction outcome for that PDF. |
| `n_entities` | float64 | 78% | `11.0` | Distinct entities parsed from that report. |
| `n_records` | float64 | 78% | `360.0` | Field-level records parsed from that report. |
| `fys` | object | 78% | `[2005, 2006, 2007]` | Fiscal years of hospital detail found in that report. |

## `financial_hospital_annual`

Hospital-level financial condition from the same PDFs. One row per `hosp_num` and fiscal year where the printed name is linked to a hospital (the most recent report's row wins when a hospital was renamed between reports), and one row per printed name and fiscal year where it is not. `hosp_num` is set only by a name-match score of 0.85 or more, or by a decision recorded with its evidence in `reference/name_overrides.csv` (`match_confidence = decided`). The reports' ALL SPECIALTY HOSPITALS roll-up is not a facility and is not included. `fiscal_year` is the year in which the hospital's own reporting year ends, which is not always the Maryland fiscal year: `fy_end_month` gives the month. Before joining to the revenue and volume tables on `fiscal_year`, keep rows with `fy_end_month == 'June'` or allow for the offset.

| column | type | non-null | example | description |
|---|---|---:|---|---|
| `entity` | object | 100% | `ADVENTIST BEHAVIORAL HEALT` | Entity name as printed in the source PDF, normalised for case and punctuation. |
| `is_statewide_total` | bool | 100% | `False` | True for the statewide roll-up row rather than a hospital. |
| `fiscal_year` | int64 | 100% | `2007` | Fiscal year as the report prints it: the year in which the hospital's own reporting year ends. For most hospitals this is the Maryland fiscal year (July to June), but some report on a December or August year; see `fy_end_month`. A December 2020 row covers January to December 2020, six months later than Maryland FY2020. |
| `eipa_regulated` | float64 | 0% | `` | Equivalent Inpatient Admissions, regulated services. Not published after FY2012. |
| `eipa_total` | float64 | 0% | `` | Equivalent Inpatient Admissions. HSCRC stopped publishing this after FY2012. |
| `excess_profit` | float64 | 78% | `-819368.0` | Total excess profit, operating and non-operating. |
| `gross_revenue_regulated` | float64 | 87% | `53432546.0` | Gross patient revenue, regulated services. |
| `gross_revenue_total` | float64 | 100% | `2842250.0` | Gross revenue, regulated and unregulated combined. Nominal dollars. |
| `gross_revenue_unregulated` | float64 | 87% | `609207.0` | Gross revenue, non-rate-regulated services. Nominal dollars. |
| `inpatient_admissions` | float64 | 13% | `260.0` | Volume of the ADM rate center. Not ECMAD. |
| `net_operating_revenue_regulated` | float64 | 87% | `45122235.0` | Net operating revenue, regulated services. |
| `net_operating_revenue_total` | float64 | 100% | `2702504.0` | Net operating revenue, regulated and unregulated combined. Nominal dollars. |
| `net_operating_revenue_unregulated` | float64 | 64% | `674447.0` | Net operating revenue, non-rate-regulated services. Nominal dollars. |
| `net_patient_revenue_regulated` | float64 | 87% | `44597472.0` | Net patient revenue, regulated services. |
| `net_patient_revenue_total` | float64 | 100% | `2702504.0` | Net patient revenue, regulated and unregulated combined. Nominal dollars. |
| `net_patient_revenue_unregulated` | float64 | 87% | `609207.0` | Net patient revenue, non-rate-regulated services. Nominal dollars. |
| `non_operating_expenses` | float64 | 62% | `0.0` | Non-operating expenses. |
| `non_operating_profit` | float64 | 78% | `0.0` | Total non-operating profit or loss. |
| `non_operating_revenue` | float64 | 62% | `34017.0` | Non-operating revenue. |
| `nor_per_eipa_regulated` | float64 | 30% | `8690.87` | Net operating revenue per EIPA, regulated. |
| `nor_per_eipa_total` | float64 | 30% | `9283.31` | Net operating revenue per EIPA, all activities. |
| `npr_per_eipa_regulated` | float64 | 30% | `8437.68` | Net patient revenue per EIPA, regulated. HSCRC's own cost-per-case measure of the pre-2014 era. |
| `npr_per_eipa_total` | float64 | 30% | `8637.78` | Net patient revenue per EIPA, all activities. |
| `operating_expenses_regulated` | float64 | 87% | `43086297.0` | Operating expenses, rate-regulated services. Nominal dollars. |
| `operating_expenses_total` | float64 | 100% | `3521872.0` | Operating expenses, regulated and unregulated combined. Nominal dollars. |
| `operating_expenses_unregulated` | float64 | 28% | `1036079.0` | Operating expenses, non-rate-regulated services. Nominal dollars. |
| `operating_profit_regulated` | float64 | 64% | `2035938.0` | Net operating profit on regulated activities. |
| `operating_profit_total` | float64 | 78% | `-819368.0` | Net operating profit, regulated and unregulated combined. |
| `operating_profit_unregulated` | float64 | 64% | `-679974.0` | Net operating profit on unregulated activities. Negative in every year of the series. |
| `opex_per_eipa_regulated` | float64 | 30% | `7883.3` | Operating expense per EIPA, regulated. |
| `opex_per_eipa_total` | float64 | 30% | `8731.61` | Operating expense per EIPA, all activities. |
| `other_operating_revenue_regulated` | float64 | 87% | `524763.0` | Other operating revenue, rate-regulated services. Nominal dollars. |
| `other_operating_revenue_total` | float64 | 100% | `0.0` | Other operating revenue, regulated and unregulated combined. Nominal dollars. |
| `other_operating_revenue_unregulated` | float64 | 87% | `65240.0` | Other operating revenue, non-rate-regulated services. Nominal dollars. |
| `pct_change_excess_profit` | float64 | 10% | `260.2` | Year-over-year percent change in total excess profit, as printed by HSCRC. |
| `pct_change_nor_per_eipa_regulated` | float64 | 10% | `0.38` | Year-over-year percent change in net operating revenue per EIPA, regulated, as printed by HSCRC. |
| `pct_change_npr_per_eipa_regulated` | float64 | 10% | `1.08` | Year-over-year percent change in net patient revenue per EIPA, regulated, as printed by HSCRC. |
| `pct_change_operating_profit_regulated` | float64 | 10% | `-11.17` | Year-over-year percent change in regulated operating profit, as printed by HSCRC. |
| `pct_change_operating_profit_total` | float64 | 10% | `2.92` | Year-over-year percent change in total operating profit, as printed by HSCRC. |
| `pct_excess_profit_of_total_revenue` | float64 | 63% | `1.01` | Excess profit as a percent of total revenue, as printed by HSCRC. |
| `pct_operating_profit_of_regulated_nor` | float64 | 63% | `2.68` | The margin **as printed by HSCRC**. Blank FY2010-FY2013, when the reports omitted it. |
| `pct_operating_profit_of_total_nor` | float64 | 63% | `1.13` | Total operating margin as printed by HSCRC. |
| `latest_report_fy` | int64 | 100% | `2009` | Fiscal year of the most recent Financial Condition Report contributing to this row. Reports restate the prior two years, so this is at or after fiscal_year. |
| `reported_in_reports` | object | 100% | `FY2009` | Which annual reports carried a figure for this entity-year. |
| `n_source_reports` | int64 | 100% | `1` | How many reports covered it. Higher means more restatement passes. |
| `period_labels` | object | 100% | `YEAR 2007` | The fiscal-year-end label(s) printed in the source. Not always June: some hospitals report on a December or August year. |
| `fy_end_month` | object | 89% | `December` | Month in which the hospital's reporting year ends, read off `period_labels`: the month printed, or December where the report prints CY (calendar year). Blank where the label prints no month (`FY 2014`, `Year 2007`). |
| `name_variants` | object | 100% | `Adventist Behavioral Healt` | Every spelling of this entity found across reports, including fixed-width truncations. |
| `hosp_num` | float64 | 95% | `3478.0` | HSCRC hospital identifier. Stable across years. **The join key.** |
| `match_score` | float64 | 71% | `0.949` | Name-match score, 0-1. |
| `matched_name` | object | 69% | `ADVENTIST HEALTHCARE FORT ` | The best-scoring crosswalk name. For `medium` rows it is a candidate only and is not applied. |
| `match_basis` | object | 100% | `override (crosswalk)` | Which name field produced the match, or `override (<evidence>)` for a recorded decision. |
| `match_confidence` | object | 100% | `decided` | `high`: name-match score of 0.85 or more, `hosp_num` set. `decided`: set by `reference/name_overrides.csv`, which records the evidence. `medium` (0.62 to 0.85) and `none`: no `hosp_num`; for `medium` the best candidate is shown in `matched_name` but not applied. |
| `margin_regulated_pct` | float64 | 64% | `4.51` | Operating profit as a percent of regulated net operating revenue. Derived, so the series is continuous; matches HSCRC's printed value exactly wherever both exist. |
| `margin_total_pct` | float64 | 77% | `-30.32` | Operating profit as a percent of total net operating revenue. Derived. |

## `financial_statewide_annual`

Extracted from HSCRC Financial Condition / Disclosure PDFs. Each report restates the prior two years; values here are the most recent restatement, and `reported_in_reports` lists which reports carried each figure.

| column | type | non-null | example | description |
|---|---|---:|---|---|
| `entity` | object | 100% | `ALL ACUTE HOSPITALS` | Entity name as printed in the source PDF, normalised for case and punctuation. |
| `is_statewide_total` | bool | 100% | `True` | True for the statewide roll-up row rather than a hospital. |
| `fiscal_year` | int64 | 100% | `2005` | Fiscal year as the report prints it; see `period_labels`. These are HSCRC's printed statewide totals. |
| `eipa_regulated` | float64 | 38% | `933558.0` | Equivalent Inpatient Admissions, regulated services. Not published after FY2012. |
| `eipa_total` | float64 | 38% | `1008864.0` | Equivalent Inpatient Admissions. HSCRC stopped publishing this after FY2012. |
| `excess_profit` | float64 | 86% | `368650149.0` | Total excess profit, operating and non-operating. |
| `gross_revenue_regulated` | float64 | 100% | `9681250082.0` | Gross patient revenue, regulated services. |
| `gross_revenue_total` | float64 | 100% | `10675445671.0` | Gross revenue, regulated and unregulated combined. Nominal dollars. |
| `gross_revenue_unregulated` | float64 | 100% | `994195589.0` | Gross revenue, non-rate-regulated services. Nominal dollars. |
| `inpatient_admissions` | float64 | 0% | `` | Volume of the ADM rate center. Not ECMAD. |
| `net_operating_revenue_regulated` | float64 | 100% | `8460040439.0` | Net operating revenue, regulated services. |
| `net_operating_revenue_total` | float64 | 100% | `9199687074.0` | Net operating revenue, regulated and unregulated combined. Nominal dollars. |
| `net_operating_revenue_unregulated` | float64 | 100% | `739646635.0` | Net operating revenue, non-rate-regulated services. Nominal dollars. |
| `net_patient_revenue_regulated` | float64 | 90% | `9833602955.0` | Net patient revenue, regulated services. |
| `net_patient_revenue_total` | float64 | 90% | `10459541483.0` | Net patient revenue, regulated and unregulated combined. Nominal dollars. |
| `net_patient_revenue_unregulated` | float64 | 90% | `625938528.0` | Net patient revenue, non-rate-regulated services. Nominal dollars. |
| `non_operating_expenses` | float64 | 14% | `13760242.0` | Non-operating expenses. |
| `non_operating_profit` | float64 | 86% | `99529166.0` | Total non-operating profit or loss. |
| `non_operating_revenue` | float64 | 29% | `11087375.0` | Non-operating revenue. |
| `nor_per_eipa_regulated` | float64 | 43% | `9062.15` | Net operating revenue per EIPA, regulated. |
| `nor_per_eipa_total` | float64 | 43% | `9118.86` | Net operating revenue per EIPA, all activities. |
| `npr_per_eipa_regulated` | float64 | 43% | `8957.57` | Net patient revenue per EIPA, regulated. HSCRC's own cost-per-case measure of the pre-2014 era. |
| `npr_per_eipa_total` | float64 | 43% | `8838.27` | Net patient revenue per EIPA, all activities. |
| `operating_expenses_regulated` | float64 | 100% | `8044819951.0` | Operating expenses, rate-regulated services. Nominal dollars. |
| `operating_expenses_total` | float64 | 100% | `8930566091.0` | Operating expenses, regulated and unregulated combined. Nominal dollars. |
| `operating_expenses_unregulated` | float64 | 29% | `2840382402.0` | Operating expenses, non-rate-regulated services. Nominal dollars. |
| `operating_profit_regulated` | float64 | 100% | `415220488.0` | Net operating profit on regulated activities. |
| `operating_profit_total` | float64 | 100% | `269120983.0` | Net operating profit, regulated and unregulated combined. |
| `operating_profit_unregulated` | float64 | 100% | `-146099505.0` | Net operating profit on unregulated activities. Negative in every year of the series. |
| `opex_per_eipa_regulated` | float64 | 43% | `8617.38` | Operating expense per EIPA, regulated. |
| `opex_per_eipa_total` | float64 | 43% | `8852.1` | Operating expense per EIPA, all activities. |
| `other_operating_revenue_regulated` | float64 | 100% | `97627822.0` | Other operating revenue, rate-regulated services. Nominal dollars. |
| `other_operating_revenue_total` | float64 | 100% | `283073343.0` | Other operating revenue, regulated and unregulated combined. Nominal dollars. |
| `other_operating_revenue_unregulated` | float64 | 100% | `185445521.0` | Other operating revenue, non-rate-regulated services. Nominal dollars. |
| `pct_change_excess_profit` | float64 | 24% | `40.64` | Year-over-year percent change in total excess profit, as printed by HSCRC. |
| `pct_change_nor_per_eipa_regulated` | float64 | 24% | `4.36` | Year-over-year percent change in net operating revenue per EIPA, regulated, as printed by HSCRC. |
| `pct_change_npr_per_eipa_regulated` | float64 | 24% | `4.21` | Year-over-year percent change in net patient revenue per EIPA, regulated, as printed by HSCRC. |
| `pct_change_operating_profit_regulated` | float64 | 0% | `` | Year-over-year percent change in regulated operating profit, as printed by HSCRC. |
| `pct_change_operating_profit_total` | float64 | 24% | `33.45` | Year-over-year percent change in total operating profit, as printed by HSCRC. |
| `pct_excess_profit_of_total_revenue` | float64 | 86% | `3.96` | Excess profit as a percent of total revenue, as printed by HSCRC. |
| `pct_operating_profit_of_regulated_nor` | float64 | 86% | `4.91` | The margin **as printed by HSCRC**. Blank FY2010-FY2013, when the reports omitted it. |
| `pct_operating_profit_of_total_nor` | float64 | 62% | `1.26` | Total operating margin as printed by HSCRC. |
| `latest_report_fy` | int64 | 100% | `2007` | Fiscal year of the most recent Financial Condition Report contributing to this row. Reports restate the prior two years, so this is at or after fiscal_year. |
| `reported_in_reports` | object | 100% | `FY2007` | Which annual reports carried a figure for this entity-year. |
| `n_source_reports` | int64 | 100% | `1` | How many reports covered it. Higher means more restatement passes. |
| `period_labels` | object | 100% | `Year 2005` | The fiscal-year-end label(s) printed in the source. Not always June: some hospitals report on a December or August year. |
| `name_variants` | object | 100% | `ACUTE HOSPITALS TOTAL` | Every spelling of this entity found across reports, including fixed-width truncations. |
| `margin_regulated_pct` | float64 | 100% | `4.91` | Operating profit as a percent of regulated net operating revenue. Derived, so the series is continuous; matches HSCRC's printed value exactly wherever both exist. |
| `margin_total_pct` | float64 | 100% | `2.93` | Operating profit as a percent of total net operating revenue. Derived. |

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
| `medicaid_volume` | float64 | 10% | `3000705.0` | Medicaid (FY2026 onward only) volume, in the rate center's own unit of rate. |
| `medicaid_revenue` | float64 | 10% | `26057083.98` | Medicaid revenue. FY2026 onward only. |
| `revenue_inpatient` | float64 | 91% | `192822272.0` | Regulated revenue, inpatient setting. |
| `revenue_outpatient` | float64 | 97% | `133130799.0` | Regulated revenue, outpatient setting. |
| `outpatient_revenue_share` | float64 | 97% | `0.4084354799651511` | Outpatient share of regulated revenue. **Regulated only** -- care that migrates to an unregulated setting leaves this measure entirely. |
| `inpatient_admissions` | float64 | 88% | `15794.0` | Volume of the ADM rate center. Not ECMAD. |
| `inpatient_admissions_medicare` | float64 | 88% | `7259.0` | ADM rate center volume attributable to Medicare, Maryland residents. |
| `medicare_revenue_share` | float64 | 99.8% | `0.4901082655745629` | Medicare share of **revenue**, Maryland residents. |
| `medicare_volume_share` | float64 | 99.8% | `0.5611296650493259` | Medicare share of **volume**, Maryland residents. A different quantity from the revenue share; the two do not track each other. Can exceed 1 where the source's Medicare volume exceeds its total volume (hosp_num 27 in every year). |
| `md_resident_revenue_share` | float64 | 100% | `0.8483782133164808` | Maryland-resident share of revenue. |
| `medicare_advantage_share_of_medicare_revenue` | float64 | 99.7% | `0.1121153837952448` | Non-FFS share of Medicare revenue. Relevant to AHEAD, whose targets anchor on FFS. |
| `medicaid_revenue_share` | float64 | 10% | `0.0439107894108892` | Medicaid share of revenue. FY2026 onward only. |

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
| `medicaid_volume` | float64 | 10% | `2943869.0` | Medicaid (FY2026 onward only) volume, in the rate center's own unit of rate. |
| `medicaid_revenue` | float64 | 10% | `25741913.37` | Medicaid revenue. FY2026 onward only. |
| `months_reported` | int64 | 100% | `12` | Distinct months present. Compare only complete years. |
| `medicare_revenue_share` | float64 | 99.9% | `0.5779256891036386` | Medicare share of **revenue**, Maryland residents. |
| `medicare_volume_share` | float64 | 99.9% | `0.5953583839386206` | Medicare share of **volume**, Maryland residents. A different quantity from the revenue share; the two do not track each other. Can exceed 1 where the source's Medicare volume exceeds its total volume (hosp_num 27 in every year). |
| `md_resident_revenue_share` | float64 | 100% | `0.8551495959968773` | Maryland-resident share of revenue. |
| `medicare_advantage_share_of_medicare_revenue` | float64 | 98% | `0.1087153360768486` | Non-FFS share of Medicare revenue. Relevant to AHEAD, whose targets anchor on FFS. |
| `medicaid_revenue_share` | float64 | 10% | `0.0820437105768875` | Medicaid share of revenue. FY2026 onward only. |

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

One row per hospital per rate year per program. The four quality programs that move a hospital's approved revenue: MHAC (Maryland Hospital Acquired Conditions), PAU (Potentially Avoidable Utilization), RRIP (Readmissions Reduction Incentive Program) and QBR (Quality Based Reimbursement). A program reports a percentage, a dollar amount, or both, depending on the rate year, so both columns are kept and either may be null. Join on `hosp_num`, not `hospid` (see `approved_gbr_targets`).

| column | type | non-null | example | description |
|---|---|---:|---|---|
| `rate_year` | int64 | 100% | `2016` | HSCRC rate year, ending 30 June. Rate year 2019 covers July 2018 to June 2019. |
| `program` | object | 100% | `MHAC` | Quality program: `MHAC`, `PAU`, `RRIP` or `QBR`. |
| `hospid` | int64 | 100% | `1` | Hospital identifier as printed in the rate-setting file: the CMS Certification Number less 210000, or for some rate years the HSCRC hospital number. Use `hosp_num` to join. |
| `hosp_num` | int64 | 100% | `1` | HSCRC hospital identifier. Stable across years. **The join key.** |
| `hospital_name` | object | 95% | `Meritus Hospital` | Hospital name as printed in the source file. |
| `pct_adjustment` | float64 | 99.4% | `9.737242354468378e-05` | Adjustment to approved revenue, as a proportion. Negative is a reduction. |
| `dollar_adjustment` | float64 | 92% | `-476649.1196559378` | Adjustment in nominal dollars, where the source file reports one. |
| `source_file` | object | 100% | `FY-2015-Scaling-for-Improv` | Originating HSCRC file name, as listed in reference/source_manifest.md. |
| `source_sheet` | object | 100% | `FY15 Rev RY2015 Scaling` | Worksheet of `source_file` the row was read from. |
| `pct_column` | object | 100% | `REVENUE NEUTRAL ADJUSTED P` | Label of the source-file column the percentage was read from. Labels vary by rate year, so the original is retained. |
| `dollar_column` | object | 92% | `$ Revenue Impact` | Label of the source-file column the dollar amount was read from. |

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
| `total_revenue_all_years` | float64 | 99% | `83353409.0` | Revenue accounted for by this centre across the whole panel. |
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
| `medicaid_volume` | float64 | 10% | `291724307.73` | Medicaid (FY2026 onward only) volume, in the rate center's own unit of rate. |
| `medicaid_revenue` | float64 | 10% | `2451797737.66` | Medicaid revenue. FY2026 onward only. |
| `n_hospitals` | int64 | 100% | `59` | Facilities reporting that year. Moves between 58 and 62. |
| `n_rate_centers` | int64 | 100% | `75` | Distinct rate centers that year. |
| `months_reported` | int64 | 100% | `12` | Distinct months present. Compare only complete years. |
| `revenue_inpatient` | float64 | 100% | `9849745404.69` | Regulated revenue, inpatient setting. |
| `revenue_outpatient` | float64 | 100% | `7286836304.93` | Regulated revenue, outpatient setting. |
| `outpatient_revenue_share` | float64 | 100% | `0.4252211105111688` | Outpatient share of regulated revenue. **Regulated only** -- care that migrates to an unregulated setting leaves this measure entirely. |
| `inpatient_admissions` | float64 | 100% | `564644.04` | Volume of the ADM rate center. Not ECMAD. |
| `medicare_revenue_share` | float64 | 100% | `0.4037913809992886` | Medicare share of **revenue**, Maryland residents. |
| `medicare_volume_share` | float64 | 100% | `0.4182395332489769` | Medicare share of **volume**, Maryland residents. A different quantity from the revenue share; the two do not track each other. Can exceed 1 where the source's Medicare volume exceeds its total volume (hosp_num 27 in every year). |
| `md_resident_revenue_share` | float64 | 100% | `0.9139157339148992` | Maryland-resident share of revenue. |
| `medicare_advantage_share_of_medicare_revenue` | float64 | 100% | `0.0693969919090736` | Non-FFS share of Medicare revenue. Relevant to AHEAD, whose targets anchor on FFS. |
| `medicaid_revenue_share` | float64 | 10% | `0.0992359182781048` | Medicaid share of revenue. FY2026 onward only. |

## `revenue_volume_monthly`

Hospital x month x rate center x setting. The `_IN`/`_OUT` suffix in the HSCRC source means INPATIENT/OUTPATIENT; patient residency is a separate dimension, carried here in the `_md_resident` / `_nonmd_resident` column suffixes. Medicaid columns are populated from FY2026 only.

| column | type | non-null | example | description |
|---|---|---:|---|---|
| `hosp_num` | int64 | 100% | `1` | HSCRC hospital identifier. Stable across years. **The join key.** |
| `ccn` | float64 | 99.8% | `210001.0` | CMS Certification Number. Reported by HSCRC as MCID where available, otherwise derived as 210000 + hosp_num. |
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
| `flag_payer_exceeds_total` | bool | 100% | `False` | True where Medicare volume or revenue exceeds the all-payer total for the same residency group, or Medicaid exceeds the all-payer total, by more than 1. About 1% of rows; most of their revenue is in CDS and MSS, which are cost-denominated. Rows are kept, not dropped. Filter these out for analyses needing strictly additive payer shares. |
| `flag_negative_value` | bool | 100% | `False` | True where volume or revenue is negative, which signals a retrospective adjustment posted to a later month. |
| `source_file` | object | 100% | `FY17-Final-Experience-Data` | Originating HSCRC file name, as listed in reference/source_manifest.md. |
| `fiscal_year_complete` | bool | 100% | `True` | False where the source file does not yet cover all 12 months. |
| `medicaid_volume` | float64 | 5% | `0.0` | Medicaid (FY2026 onward only) volume, in the rate center's own unit of rate. |
| `medicaid_revenue` | float64 | 5% | `0.0` | Medicaid revenue. FY2026 onward only. |
| `medicaid_volume_md_resident` | float64 | 5% | `0.0` | Medicaid (FY2026 onward only) volume, Maryland residents, in the rate center's own unit of rate. |
| `medicaid_volume_nonmd_resident` | float64 | 5% | `0.0` | Medicaid (FY2026 onward only) volume, out-of-state residents, in the rate center's own unit of rate. |
| `medicaid_revenue_md_resident` | float64 | 5% | `0.0` | Medicaid (FY2026 onward only) revenue, Maryland residents, in nominal dollars. |
| `medicaid_revenue_nonmd_resident` | float64 | 5% | `0.0` | Medicaid (FY2026 onward only) revenue, out-of-state residents, in nominal dollars. |
