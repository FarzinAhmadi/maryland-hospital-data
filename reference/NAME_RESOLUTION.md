# How hospital names are resolved

The revenue and volume files carry HSCRC's hospital number. The Financial
Condition Reports and the older Disclosure Reports do not: they identify a
hospital only by a printed name. Those names are inconsistent across eras,
truncated to fit fixed-width columns, and subject to genuine renames and
mergers. Linking them to the stable `hosp_num` is therefore a judgment, and
this file records how that judgment is made.

## The rule

A printed name gets a `hosp_num` in one of two ways, and every row says which:

1. **Scored match.** The build scores the printed name against every name HSCRC
   has used for a hospital in the revenue and volume files, plus the CMS name.
   A score of 0.85 or better is applied (`match_confidence = high`).
2. **Recorded decision.** A row in `reference/name_overrides.csv`, which ships
   with the data. It beats the scorer, carries `match_confidence = decided`, and
   cites its evidence.

Everything else keeps a null `hosp_num` and is listed in
`REVIEW_unresolved_hospital_names.csv`. A score from 0.62 to 0.85 is shown there
as a candidate (`matched_name`) but **not applied**. Version 0.1.2 applied those
scores, and several were wrong: Maryland General Hospital went to Atlantic
General, Washington County Hospital to White Oak, Potomac Ridge to Fort
Washington, and University UMCC, a unit reported separately in the same years,
to the University of Maryland Medical Center. Nothing is dropped.

## Evidence

The `evidence` column takes one of these values. The standard is that someone
else can check the claim without taking our word for it.

| value | meaning | how to check it |
|---|---|---|
| `crosswalk` | The target hospital's own names in `hospital_crosswalk.csv` contain the printed name or an unambiguous abbreviation of it. | Look up the `hosp_num` in the crosswalk. |
| `restatement` | Two reports print identical gross revenue, net patient revenue, other operating revenue and operating expenses for the same fiscal year, once under this name and once under a name already linked. Each report restates the two prior years, so a renamed hospital appears under both names. | Open the two reports named in `source` at the fiscal year named. |
| `revenue` | For FY2017 onward, the report's gross regulated revenue (gross patient revenue where the report prints no regulated split) equals the hospital's regulated revenue in `hospital_annual` to within 0.1% in at least two fiscal years, and no other hospital matches. | Compare the two tables for the years named. |
| `document` | A named HSCRC or CMS document records the rename or merger. | The `source` column cites it. |
| `not_a_facility` | A report roll-up or heading, not a hospital. Excluded from the hospital table. | |
| `checked` | A person checked and the scorer's candidate is wrong, but no hospital number is supported yet. `hosp_num` is `UNRESOLVED`. | The `note` says what would settle it. |

The test suite checks that every linked FY2017+ row on a June fiscal year agrees
with the revenue panel to within 3%; the two wrong links in v0.1.2 that this covers, Johns
Hopkins Suburban Hospital and UM Upper Chesapeake Medical Center Aberdeen, were
off by 67% to 92%.

## Still unresolved, and why

| printed name | years | what is known | what would settle it |
|---|---|---|---|
| ADVENTIST REHAB HOSPITAL OF MD | FY2005-FY2019 | Same facility as the Rockville name below (identical FY2019 figures). The scorer's candidate, 89, first reports in FY2020. | A document naming the HSCRC number of the Rockville facility; revenue points to 3029. |
| ADVENTIST REHAB HOSPITAL OF MD ROCKVILLE | FY2019-FY2024 | Calendar-year filer, so revenue cannot be matched to the fiscal year exactly; closest is 3029. | As above. |
| ADVENTIST REHAB HOSPITAL OF MD TAKOMA PARK | FY2020-FY2024 | Calendar-year filer; closest is 89. | As above. |
| BRADDOCK HOSPITAL | FY2007-FY2009 | Merged into Western Maryland Regional (27); merged entities do not restate under one name. | A document dating the merger. |
| MEMORIAL HOSP AND MED CTR OF CUMBERLAND | FY2007-FY2009 | As Braddock. | As Braddock. |
| UNIVERSITY UMCC | FY2007-FY2012 | Reported alongside the University of Maryland Medical Center in the same years, at about $60 million, so a separate unit. | Whether it has an HSCRC number at all. |
| UNIVERSITY SPECIALTY HOSPITAL | FY2005-FY2014 | A specialty facility; v0.1.2 counted it as the ALL SPECIALTY HOSPITALS roll-up. | Whether it has an HSCRC number. |
| ST LUKE INSTITUTE | FY2005-FY2014 | Specialty facility, not in the crosswalk. | Whether it is rate-regulated. |

A row with no hospital number is a smaller problem than a row attached to the
wrong hospital.

## Effect on published tables

Decisions change which hospital a financial row belongs to, so they can change
hospital-level figures between versions. Any such change is listed in
`CHANGELOG.md` for the version that introduces it.
