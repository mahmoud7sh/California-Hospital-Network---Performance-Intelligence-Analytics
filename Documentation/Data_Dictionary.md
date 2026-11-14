# Data Dictionary

Tables and columns of the Power BI model, with the grain of each table. Column meanings follow the source naming; where the source meaning is not confirmed this is stated. Statistics (non-null share, distinct values, range or examples) were computed from the cleaned tables.

**Key:** every fact table joins to `Dim_Hospital[FAC_NO]` (many-to-one, single direction). Equipment also has an automatic date relationship (can be ignored).

## Dim_Hospital

Hospital dimension: identity, location, ownership and designations.

- **Grain:** one row per hospital
- **Rows:** 501   **Columns:** 16

| Column | Type | Description | Non-null | Distinct | Range / examples |
|---|---|---|---|---|---|
| `FAC_NO` | whole number | Hospital (facility) number; key to Dim_Hospital | 100% | 501 | unique key |
| `FAC_NAME` | text | Hospital name (not unique: use FAC_NO as key) | 100% | 497 | VISTA SPECIALTY HOSPITAL OF , TELECARE MLK ADULT PSYCHIATR, KERN PSYCHIATRIC HEALTH FACI, ... |
| `FAC_CITY` | text | City | 100% | 257 | La Mirada, Los Angeles, Bakersfield, ... |
| `COUNTY` | text | County | 100% | 55 | Los Angeles, Kern, San Bernardino, ... |
| `LATITUDE` | decimal | Latitude | 100% | 497 | 32.62 to 41.77 |
| `LONGITUDE` | decimal | Longitude | 100% | 497 | -124.2 to -114.6 |
| `LICENSE_STATUS` | text | License status (Open, Closed, Suspense) | 100% | 3 | Open, Closed, Suspense |
| `FACILITY_LEVEL` | text | Facility level: Parent, Consolidated or Distinct Part facility | 100% | 3 | Parent Facility, Consolidated Facility, Distinct Part Facility |
| `LICENSE_CATEGORY` | text | License category | 100% | 4 | General Acute Care Hospital, Psychiatric Health Facility, Acute Psychiatric Hospital, Chemical Dep. Recovery Hospital |
| `OWNERSHIP_TYPE` | text | Type of license holder (source categories) | 100% | 10 | Investor - Limited Liability, Investor - Corporation, State, ... |
| `PRINCIPAL_SERVICE_TYPE` | text | Principal type of service | 100% | 10 | General Medical / Surgical, Psychiatric, Physical Rehabilitation, ... |
| `TRAUMA_CTR` | text | Trauma center level; "Non-Trauma" when none | 100% | 10 | Non-Trauma, Level IV, Level I, ... |
| `TEACH_HOSP` | text | Teaching hospital flag; "Not Reported" when blank in source | 100% | 3 | No, Not Reported, Yes |
| `RURAL_HOSP` | text | Rural hospital flag; "Not Reported" when blank | 100% | 3 | No, Not Reported, Yes |
| `OWNERSHIP_GROUP` | text | Derived: Non-Profit, Investor-Owned, Government, Not Reported | 100% | 4 | Investor-Owned, Government, Non-Profit, Not Reported |
| `IS_TRAUMA_CENTER` | text | Derived: Yes when TRAUMA_CTR is not "Non-Trauma" | 100% | 2 | No, Yes |

**Calculated columns added in the model (folder "Service Availability"):** `Svc Anesthesiologist`, `Svc Lab`, `Svc Operating Room`, `Svc Pharmacist`, `Svc Physician`, `Svc Psychiatric ER`, `Svc Radiology`. Values: 24-Hour, On-Call, Not Available, Not Reported. They look up `Fact_Facility_Services` so any slicer on them filters every page.

## Fact_Bed_Capacity

Licensed beds by service line.

- **Grain:** one row per hospital
- **Rows:** 501   **Columns:** 17

| Column | Type | Description | Non-null | Distinct | Range / examples |
|---|---|---|---|---|---|
| `FAC_NO` | whole number | Hospital (facility) number; key to Dim_Hospital | 100% | 501 | foreign key |
| `MED_SURG_LIC_BEDS` | whole number | Licensed beds: medical/surgical | 100% | 217 | 0 to 654 |
| `PERINATAL_LIC_BEDS` | whole number | Licensed beds: perinatal (obstetrics) | 100% | 71 | 0 to 114 |
| `PEDIATRIC_LIC_BEDS` | whole number | Licensed beds: pediatric | 100% | 42 | 0 to 284 |
| `CORONARY_CARE_LIC_BEDS` | whole number | Licensed beds: coronary care | 100% | 23 | 0 to 32 |
| `ACUTE_RESPIRATORY_CARE_LIC_BEDS` | whole number | Licensed beds: acute respiratory care | 100% | 5 | 0 to 10 |
| `BURN_LIC_BEDS` | whole number | Licensed beds: burn | 100% | 8 | 0 to 20 |
| `REHAB_CTR_LIC_BEDS` | whole number | Licensed beds: physical rehabilitation | 100% | 40 | 0 to 138 |
| `CHEM_DEPEND_RECOVERY_LIC_BEDS` | whole number | Licensed beds: chemical dependency recovery | 100% | 11 | 0 to 100 |
| `ACUTE_PSYCHIATRIC_LIC_BEDS` | whole number | Licensed beds: acute psychiatric | 100% | 84 | 0 to 1,004 |
| `SN_LIC_BEDS` | whole number | Licensed beds: skilled nursing | 100% | 70 | 0 to 769 |
| `INTERMEDIATE_CARE_LIC_BEDS` | whole number | Licensed beds: intermediate care | 100% | 6 | 0 to 1,450 |
| `INTERMEDIATE_CARE_DEV_DIS_LIC_BEDS` | whole number | Licensed beds: intermediate care (developmental disability) | 100% | 2 | 0 to 590 |
| `TOT_LIC_BEDS` | whole number | Total licensed beds (all lines) | 100% | 294 | 0 to 1,500 |
| `GAC_CDRS_LIC_BEDS` | whole number | Licensed beds: GAC CDRS memo category (meaning not confirmed; not additive) | 100% | 5 | 0 to 38 |
| `ACUTE_PSYCH_LIC_BEDS` | whole number | Licensed beds: acute psychiatric memo category (not additive) | 100% | 5 | 0 to 22 |
| `GAC_SUBTOT_LIC_BEDS` | decimal | General acute care licensed beds (subtotal) | 82% | 258 | 0 to 915 |

## Fact_Bed_Days

Licensed bed-days and census days by service line.

- **Grain:** one row per hospital (a hospital with an operator change has two period rows)
- **Rows:** 502   **Columns:** 31

| Column | Type | Description | Non-null | Distinct | Range / examples |
|---|---|---|---|---|---|
| `FAC_NO` | whole number | Hospital (facility) number; key to Dim_Hospital | 100% | 501 | foreign key |
| `MED_SURG_LIC_BED_DAYS` | whole number | Licensed bed-days: medical/surgical | 100% | 222 | 0 to 2.387e+05 |
| `PEDIATRIC_LIC_BED_DAYS` | whole number | Licensed bed-days: pediatric | 100% | 43 | 0 to 1.037e+05 |
| `PERINATAL_LIC_BED_DAYS` | whole number | Licensed bed-days: perinatal (obstetrics) | 100% | 74 | 0 to 4.161e+04 |
| `CORONARY_CARE_LIC_BED_DAYS` | whole number | Licensed bed-days: coronary care | 100% | 25 | 0 to 1.168e+04 |
| `ACUTE_RESPIRATORY_CARE_LIC_BED_DAYS` | whole number | Licensed bed-days: acute respiratory care | 100% | 5 | 0 to 3,650 |
| `BURN_LIC_BED_DAYS` | whole number | Licensed bed-days: burn | 100% | 9 | 0 to 7,300 |
| `REHAB_CTR_LIC_BED_DAYS` | whole number | Licensed bed-days: physical rehabilitation | 100% | 45 | 0 to 5.037e+04 |
| `GAC_SUBTOT_LIC_BED_DAYS` | decimal | Licensed bed-days: general acute care (subtotal of the itemised GAC lines plus others in the source) | 81% | 268 | 460 to 3.34e+05 |
| `CHEM_DEPEND_RECOVERY_LIC_BED_DAYS` | whole number | Licensed bed-days: chemical dependency recovery | 100% | 11 | 0 to 3.65e+04 |
| `ACUTE_PSYCHIATRIC_LIC_BED_DAYS` | whole number | Licensed bed-days: acute psychiatric | 100% | 88 | 0 to 3.665e+05 |
| `SN_LIC_BED_DAYS` | whole number | Licensed bed-days: skilled nursing | 100% | 71 | 0 to 2.807e+05 |
| `INTERMEDIATE_CARE_LIC_BED_DAYS` | whole number | Licensed bed-days: intermediate care | 100% | 6 | 0 to 5.292e+05 |
| `INTERMEDIATE_CARE_DEV_DIS_LIC_BED_DAYS` | whole number | Licensed bed-days: intermediate care (developmental disability) | 100% | 2 | 0 to 2.154e+05 |
| `TOT_LIC_BED_DAYS` | decimal | Licensed bed-days: all service lines (total) | 99% | 309 | 460 to 5.475e+05 |
| `MED_SURG_CEN_DAYS` | decimal | Census (occupied) days: medical/surgical | 78% | 374 | 0 to 2.322e+05 |
| `PERINATAL_CEN_DAYS` | decimal | Census (occupied) days: perinatal (obstetrics) | 50% | 198 | 0 to 2.141e+04 |
| `PEDIATRIC_CEN_DAYS` | decimal | Census (occupied) days: pediatric | 36% | 83 | 0 to 6.779e+04 |
| `CORONARY_CARE_CEN_DAYS` | decimal | Census (occupied) days: coronary care | 30% | 65 | 0 to 1.768e+04 |
| `ACUTE_RESPIRATORY_CARE_CEN_DAYS` | decimal | Census (occupied) days: acute respiratory care | 23% | 5 | 0 to 2,960 |
| `BURN_CEN_DAYS` | decimal | Census (occupied) days: burn | 24% | 13 | 0 to 4,326 |
| `REHAB_CTR_CEN_DAYS` | decimal | Census (occupied) days: physical rehabilitation | 34% | 80 | 0 to 4.811e+04 |
| `GAC_SUBTOT_CEN_DAYS` | decimal | Census (occupied) days: general acute care (subtotal of the itemised GAC lines plus others in the source) | 79% | 397 | 1 to 2.996e+05 |
| `CHEM_DEPEND_RECOV_CEN_DAYS` | decimal | Census (occupied) days: chemical dependency recovery | 23% | 10 | 0 to 2.774e+04 |
| `ACUTE_PSYCHIATRIC_CEN_DAYS` | decimal | Census (occupied) days: acute psychiatric | 45% | 153 | 0 to 2.644e+05 |
| `SN_CEN_DAYS` | decimal | Census (occupied) days: skilled nursing | 35% | 92 | 0 to 1.885e+05 |
| `INTERMEDIATE_CARE_CEN_DAYS` | decimal | Census (occupied) days: intermediate care | 23% | 9 | 0 to 4.565e+05 |
| `TOT_CEN_DAYS` | decimal | Census (occupied) days: all service lines (total) | 98% | 489 | 1 to 4.809e+05 |
| `GAC_CDRS_CEN_DAYS` | decimal | Census (occupied) days: GAC CDRS memo category (meaning not confirmed; not additive) | 25% | 4 | 0 to 3,813 |
| `ACUTE_PSYCH_CEN_DAYS` | decimal | Census (occupied) days: acute psychiatric memo category (not additive) | 26% | 7 | 0 to 2,831 |
| `NEWBORN_NURSERY_CEN_DAYS` | decimal | Census (occupied) days: newborn nursery | 53% | 196 | 0 to 1.25e+04 |

## Fact_Patient_Discharge

Discharges by service line. ALOS source columns were removed on purpose (they stored 0 for no data); ALOS is a DAX measure.

- **Grain:** one row per hospital (operator-change hospital has two period rows)
- **Rows:** 502   **Columns:** 18

| Column | Type | Description | Non-null | Distinct | Range / examples |
|---|---|---|---|---|---|
| `FAC_NO` | whole number | Hospital (facility) number; key to Dim_Hospital | 100% | 501 | foreign key |
| `MED_SURG_DISCHARGES` | decimal | Discharges: medical/surgical | 78% | 366 | 0 to 3.705e+04 |
| `PERINATAL_DISCHARGES` | decimal | Discharges: perinatal (obstetrics) | 51% | 194 | 0 to 7,085 |
| `PEDIATRIC_DISCHARGES` | decimal | Discharges: pediatric | 37% | 83 | 0 to 1.596e+04 |
| `CORONARY_CARE_DISCHARGES` | decimal | Discharges: coronary care | 32% | 62 | 0 to 3,212 |
| `ACUTE_RESPIRATORY_CARE_DISCHARGES` | decimal | Discharges: acute respiratory care | 25% | 5 | 0 to 259 |
| `BURN_DISCHARGES` | decimal | Discharges: burn | 26% | 13 | 0 to 328 |
| `REHAB_CTR_DISCHARGES` | decimal | Discharges: physical rehabilitation | 35% | 77 | 0 to 3,257 |
| `GAC_SUBTOT_DISCHARGES` | decimal | Discharges: general acute care (subtotal of the itemised GAC lines plus others in the source) | 79% | 389 | 5 to 4.65e+04 |
| `CHEM_DEPEND_RECOVERY_DISCHARGES` | decimal | Discharges: chemical dependency recovery | 24% | 9 | 0 to 1,544 |
| `ACUTE_PSYCHIATRIC_DISCHARGES` | decimal | Discharges: acute psychiatric | 45% | 144 | 0 to 6,668 |
| `SN_DISCHARGES` | decimal | Discharges: skilled nursing | 35% | 74 | 0 to 3,083 |
| `INTERMEDIATE_CARE_DISCHARGES` | decimal | Discharges: intermediate care | 23% | 9 | 0 to 802 |
| `INTERMEDIATE_CARE_DEV_DIS_DISCHARGES` | decimal | Discharges: intermediate care (developmental disability) | 21% | 2 | 0 to 135 |
| `TOT_DISCHARGES` | decimal | Discharges: all service lines (total) | 96% | 467 | 15 to 4.65e+04 |
| `GAC_CDRS_DISCHARGES` | decimal | Discharges: GAC CDRS memo category (meaning not confirmed; not additive) | 27% | 4 | 0 to 746 |
| `ACUTE_PSYCH_DISCHARGES` | decimal | Discharges: acute psychiatric memo category (not additive) | 27% | 7 | 0 to 253 |
| `NEWBORN_NURSERY_INFANTS` | decimal | Newborn nursery infants discharged | 53% | 192 | 0 to 8,264 |

## Fact_ED

Emergency department traffic, EMS visit acuity (visits not resulting in admission), stations and diversion total.

- **Grain:** one row per hospital reporting ED data
- **Rows:** 347   **Columns:** 13

| Column | Type | Description | Non-null | Distinct | Range / examples |
|---|---|---|---|---|---|
| `FAC_NO` | whole number | Hospital (facility) number; key to Dim_Hospital | 100% | 347 | foreign key |
| `EMSA_TRAUMA_DESIGNATION` | text | EMSA adult trauma designation | 23% | 4 | Level I, Level II, Level IV, Level III |
| `LIC_ED_LEV_BEGIN` | text | Licensed ED level at start of year | 95% | 3 | Basic, Standby, Comprehensive |
| `EMS_VISITS_NON_URGENT_TOT` | decimal | ED visits not resulting in admission: non-urgent | 98% | 282 | 0 to 6.436e+04 |
| `EMS_VISITS_URGENT_TOT` | decimal | ED visits not resulting in admission: urgent | 98% | 319 | 0 to 4.338e+04 |
| `EMS_VISITS_MODERATE_TOT` | decimal | ED visits not resulting in admission: moderate | 98% | 323 | 0 to 9.951e+04 |
| `EMS_VISITS_SEVERE_TOT` | decimal | ED visits not resulting in admission: severe | 98% | 325 | 0 to 7.509e+04 |
| `EMS_VISITS_CRITICAL_TOT` | decimal | ED visits not resulting in admission: critical | 99% | 322 | 0 to 6.084e+04 |
| `EMER_MED_TREAT_STATIONS_ON_1231` | whole number | Emergency treatment stations on 31 Dec | 100% | 73 | 0 to 106 |
| `NON_EMER_VISITS_IN_EMER_DEPT` | decimal | Non-emergency visits in the emergency department | 72% | 59 | 0 to 7.05e+04 |
| `EMER_DEPT_HR_DIVERSION_TOT` | decimal | Ambulance diversion hours, annual total | 42% | 130 | 2 to 5,468 |
| `LIC_ED_LEV_END` | text | Licensed ED level at end of year | 95% | 3 | Basic, Standby, Comprehensive |
| `ER_TRAFFIC_TOT` | decimal | Total ED traffic (visits) | 94% | 324 | 426 to 1.546e+05 |

## Fact_ED_Outcome

ED outcomes: admissions, left-without-being-seen and admitted visits by acuity.

- **Grain:** one row per hospital reporting ED data
- **Rows:** 347   **Columns:** 9

| Column | Type | Description | Non-null | Distinct | Range / examples |
|---|---|---|---|---|---|
| `FAC_NO` | whole number | Hospital (facility) number; key to Dim_Hospital | 100% | 347 | foreign key |
| `EMER_DEPT_VISITS_NOT_RESULT_ADMISSIONS_TOT` | whole number | ED visits not resulting in admission, total | 100% | 326 | 0 to 1.455e+05 |
| `EMER_REGISTRATIONS_PATS_LEAVE_WO_BEING_SEEN` | whole number | Patients who left without being seen (LWBS) | 100% | 274 | 0 to 8,690 |
| `EMS_VISITS_NON_URGENT_ADMITTED` | whole number | Admitted ED visits: non-urgent | 100% | 35 | 0 to 1.696e+04 |
| `EMS_VISITS_URGENT_ADMITTED` | whole number | Admitted ED visits: urgent | 100% | 68 | 0 to 5,768 |
| `EMS_VISITS_MODERATE_ADMITTED` | whole number | Admitted ED visits: moderate | 100% | 154 | 0 to 1.686e+04 |
| `EMS_VISITS_SEVERE_ADMITTED` | whole number | Admitted ED visits: severe | 100% | 231 | 0 to 1.227e+04 |
| `EMS_VISITS_CRITICAL_ADMITTED` | whole number | Admitted ED visits: critical | 100% | 292 | 0 to 2.7e+04 |
| `ADMITTED_FROM_EMER_DEPT_TOT` | whole number | Patients admitted from the ED, total | 100% | 317 | 0 to 4.57e+04 |

## Fact_ED_Diversion_Monthly

Ambulance diversion hours by hospital and month (326 ED hospitals x 12 months).

- **Grain:** one row per hospital per month
- **Rows:** 3,912   **Columns:** 4

| Column | Type | Description | Non-null | Distinct | Range / examples |
|---|---|---|---|---|---|
| `FAC_NO` | whole number | Hospital (facility) number; key to Dim_Hospital | 100% | 326 | foreign key |
| `MONTH_NUM` | whole number | Month number (1-12); sort key for MONTH_NAME | 100% | 12 | 1 to 12 |
| `MONTH_NAME` | text | Month name (Jan-Dec) | 100% | 12 | Jan, Feb, Mar, ... |
| `DIVERSION_HOURS` | whole number | Ambulance diversion hours in the month (blank months set to 0 for ED hospitals) | 100% | 316 | 0 to 983 |

## Fact_ICU

IC columns as named in the source (meaning unconfirmed); newborn IC kept separate.

- **Grain:** one row per hospital
- **Rows:** 501   **Columns:** 11

| Column | Type | Description | Non-null | Distinct | Range / examples |
|---|---|---|---|---|---|
| `FAC_NO` | whole number | Hospital (facility) number; key to Dim_Hospital | 100% | 501 | foreign key |
| `IC_LIC_BEDS` | whole number | IC licensed beds (IC as named in the source; meaning unconfirmed) | 100% | 69 | 0 to 130 |
| `IC_NEWBORN_LIC_BEDS` | whole number | Newborn IC licensed beds | 100% | 53 | 0 to 153 |
| `IC_LIC_BED_DAYS` | whole number | IC licensed bed-days | 100% | 73 | 0 to 4.745e+04 |
| `IC_NEWBORN_LIC_BED_DAYS` | whole number | Newborn IC licensed bed-days | 100% | 56 | 0 to 5.164e+04 |
| `IC_DISCHARGES` | decimal | IC discharges | 71% | 270 | 0 to 5,542 |
| `IC_NEWBORN_DISCHARGES` | decimal | Newborn IC discharges | 44% | 136 | 0 to 2,005 |
| `IC_INTRA_TRANSFERS` | decimal | IC intra-hospital transfers | 67% | 285 | 0 to 1.059e+04 |
| `IC_NEWBORN_INTRA_TRANSFERS` | decimal | Newborn IC intra-hospital transfers | 36% | 90 | 0 to 2,049 |
| `IC_CEN_DAYS` | decimal | IC census days | 71% | 319 | 0 to 4.005e+04 |
| `IC_NEWBORN_CEN_DAYS` | decimal | Newborn IC census days | 43% | 153 | 0 to 2.707e+04 |

## Fact_Surgery

Surgical operations and operating-room capacity.

- **Grain:** one row per hospital
- **Rows:** 501   **Columns:** 11

| Column | Type | Description | Non-null | Distinct | Range / examples |
|---|---|---|---|---|---|
| `FAC_NO` | whole number | Hospital (facility) number; key to Dim_Hospital | 100% | 501 | foreign key |
| `INPATIENT_SURG_OPER` | decimal | Inpatient surgical operations | 74% | 341 | 0 to 1.428e+04 |
| `OUTPATIENT_SURG_OPER` | decimal | Outpatient surgical operations | 73% | 334 | 0 to 2.308e+04 |
| `INPAT_OPER_RM` | decimal | Operating rooms: inpatient | 40% | 15 | 0 to 44 |
| `OUTPAT_OPER_RM` | decimal | Operating rooms: outpatient | 40% | 13 | 0 to 16 |
| `INPAT_OUTPAT_OPER_RM` | decimal | Operating rooms: shared inpatient/outpatient | 70% | 31 | 0 to 57 |
| `OPER_RM_TOT` | decimal | Operating rooms, total | 73% | 37 | 1 to 57 |
| `INPATIENT_SURG_OPER_RM_MINS` | decimal | Operating-room minutes: inpatient | 74% | 351 | 0 to 3.517e+06 |
| `OUTPATIENT_SURG_OPER_RM_MINS` | decimal | Operating-room minutes: outpatient | 72% | 346 | 0 to 2.424e+06 |
| `INPATIENT_AVG_PER_SURGERY` | decimal | Average minutes per inpatient surgery | 70% | 308 | 14.8 to 295.5 |
| `OUTPATIENT_AVG_PER_SURGERY` | decimal | Average minutes per outpatient surgery | 69% | 290 | 4.7 to 193.3 |

## Fact_Cardiovascular

Cardiac catheterization and cardiovascular procedures.

- **Grain:** one row per reporting hospital
- **Rows:** 263   **Columns:** 24

| Column | Type | Description | Non-null | Distinct | Range / examples |
|---|---|---|---|---|---|
| `FAC_NO` | whole number | Hospital (facility) number; key to Dim_Hospital | 100% | 263 | foreign key |
| `LIC_CARDIOLOGY_CARDIOVASCULAR_SURG_SERVICES` | text | Licensed cardiology / cardiovascular surgery services | 76% | 2 | Cardiac Catheterization Only, Cardiovascular Surgery Services |
| `CARDIOVASCULAR_OPER_RM` | decimal | Cardiovascular operating rooms | 83% | 14 | 0 to 13 |
| `CARDIOVASCULAR_SURG_OPER_BYPASS_USED_TOT` | decimal | Cardiovascular surgeries using bypass (total) | 49% | 112 | 1 to 4,784 |
| `CORONARY_ARTERY_BYPASS_GRAFT_SURG` | decimal | Coronary artery bypass graft (CABG) surgeries | 79% | 92 | 0 to 712 |
| `CARDIAC_CATHETERIZATION_LAB_RM` | decimal | Cardiac catheterization lab rooms | 94% | 12 | 0 to 16 |
| `CARDIAC_CATHETERIZATION_DIAGNOSTIC_VISITS_TOT` | decimal | Diagnostic cardiac catheterization visits | 71% | 182 | 1 to 2.858e+04 |
| `DIAGNOSTIC_CARDIAC_CATH_PROC` | decimal | Diagnostic cardiac catheterization procedures | 91% | 178 | 0 to 5,947 |
| `MYOCARDIAL_BIOPSY` | decimal | Myocardial biopsy procedures | 79% | 28 | 0 to 734 |
| `IMPLANABLE_CARDIO_DEFIB_IMPLANTATION` | decimal | Implantable cardioverter-defibrillator implantations | 88% | 79 | 0 to 540 |
| `PCI_WITH_STENT` | decimal | Percutaneous coronary interventions with stent | 89% | 146 | 0 to 2,130 |
| `PCI_WO_STENT` | decimal | Percutaneous coronary interventions without stent | 87% | 86 | 0 to 685 |
| `ATHERECTOMY` | decimal | Atherectomy procedures | 83% | 71 | 0 to 401 |
| `THROMBOLYTIC_AGENTS` | decimal | Thrombolytic agent administrations | 80% | 21 | 0 to 52 |
| `PTBV` | decimal | Percutaneous transluminal balloon valvuloplasty | 82% | 34 | 0 to 209 |
| `CATHETER_ABLATION` | decimal | Catheter ablation procedures | 84% | 106 | 0 to 2,259 |
| `PERIPHERAL_VASCULAR_ANGIOGRAPHY` | decimal | Peripheral vascular angiography | 88% | 108 | 0 to 1,389 |
| `PERIPHERAL_VASCULAR_INTERVENTIONAL` | decimal | Peripheral vascular interventional procedures | 88% | 103 | 0 to 2,911 |
| `CAROTID_STENTING` | decimal | Carotid stenting | 81% | 30 | 0 to 82 |
| `INTRA_AORTIC_BALLOON_PUMP_INSERTION` | decimal | Intra-aortic balloon pump insertions | 87% | 55 | 0 to 180 |
| `CATHETER_BASED_VENTRICULAR_ASSIST_DEVICE_INSERTION` | decimal | Catheter-based ventricular assist device insertions | 83% | 52 | 0 to 178 |
| `ALL_OTHER_CATHETERIZATION_PROC` | decimal | All other catheterization procedures | 89% | 158 | 0 to 5,804 |
| `CATHETERIZATION_PROC_TOT` | decimal | Catheterization procedures, total (do not add to the components) | 72% | 184 | 2 to 1.858e+04 |
| `CARDIAC_CATHETERIZATION_THERAPEUTIC_VISITS_TOT` | decimal | Therapeutic cardiac catheterization visits | 62% | 155 | 1 to 7,493 |

## Fact_Clinical_Programs

Acute psychiatric patients, hospice and palliative care programs.

- **Grain:** one row per reporting hospital
- **Rows:** 491   **Columns:** 26

| Column | Type | Description | Non-null | Distinct | Range / examples |
|---|---|---|---|---|---|
| `FAC_NO` | whole number | Hospital (facility) number; key to Dim_Hospital | 100% | 491 | foreign key |
| `ACUTE_PSYCHIATRIC_PATS_LOCKED_ON_1231` | decimal | Acute psychiatric patients on 31 Dec: locked unit | 44% | 63 | 0 to 1,279 |
| `ACUTE_PSYCHIATRIC_PATS_OPEN_ON_1231` | decimal | Acute psychiatric patients on 31 Dec: open unit | 33% | 25 | 0 to 80 |
| `ACUTE_PSYCHIATRIC_PATS_TOT_BY_UNIT_ON_1231` | decimal | Acute psychiatric patients on 31 Dec: total by unit type | 29% | 67 | 2 to 1,279 |
| `ACUTE_PSYCHIATRIC_PATS_MED_TRAD_ON_1231` | decimal | By payer: Medicare traditional | 39% | 25 | 0 to 302 |
| `ACUTE_PSYCHIATRIC_PATS_MED_MANAGED_CARE_ON_1231` | decimal | By payer: Medicare managed care | 34% | 18 | 0 to 37 |
| `ACUTE_PSYCHIATRIC_PATS_MED_CAL_TRAD_ON_1231` | decimal | By payer: Medi-Cal traditional | 39% | 39 | 0 to 122 |
| `ACUTE_PSYCHIATRIC_PATS_MED_CAL_MANAGED_CARE_ON_1231` | decimal | By payer: Medi-Cal managed care | 31% | 22 | 0 to 217 |
| `ACUTE_PSYCHIATRIC_PATS_COUNTY_INDIGENT_PROG` | decimal | By payer: county indigent program | 30% | 20 | 0 to 440 |
| `ACUTE_PSYCHIATRIC_PATS_3RD_PARTIES_TRAD` | decimal | By payer: third parties traditional | 31% | 19 | 0 to 238 |
| `ACUTE_PSYCHIATRIC_PATS_3RD_PARTIES_MANAGED_CARE` | decimal | By payer: third parties managed care | 31% | 16 | 0 to 34 |
| `ACUTE_PSYCHIATRIC_PATS_SHORT_DOYLE` | decimal | By payer: Short-Doyle | 28% | 15 | 0 to 71 |
| `ACUTE_PSYCHIATRIC_PATS_OTHER_INDIGENT` | decimal | By payer: other indigent | 27% | 5 | 0 to 6 |
| `ACUTE_PSYCHIATRIC_PATS_OTHER_PAYERS` | decimal | By payer: other payers | 34% | 22 | 0 to 977 |
| `ACUTE_PSYCHIATRIC_PATS_TOT_BY_PAYOR` | decimal | Acute psychiatric patients: total by payer | 29% | 67 | 2 to 1,279 |
| `SHORT_DOYLE_SERVICES_OFFERED` | text | Short-Doyle services offered | 100% | 2 | No, Yes |
| `INPATIENT_HOSPICE_PROG_OFFERED` | text | Inpatient hospice program offered | 100% | 2 | No, Yes |
| `INPATIENT_PALLIATIVE_CARE_PROG_OFFERED` | text | Inpatient palliative care program offered | 100% | 2 | Yes, No |
| `INPATIENT_PALLIATIVE_CARE_PROG_NURSES` | decimal | Palliative care program: nurses | 38% | 11 | 0 to 15 |
| `INPATIENT_PALLIATIVE_CARE_PROG_NURSES_CERTIFIED` | decimal | Palliative care program: certified nurses | 37% | 8 | 0 to 7 |
| `INPATIENT_PALLIATIVE_CARE_PROG_PHYSICIAN` | decimal | Palliative care program: physicians | 38% | 13 | 0 to 15 |
| `INPATIENT_PALLIATIVE_CARE_PROG_PHYSICIAN_CERTIFIED` | decimal | Palliative care program: certified physicians | 37% | 15 | 0 to 15 |
| `INPATIENT_PALLIATIVE_CARE_PROG_SOCIAL_WORKER` | decimal | Palliative care program: social workers | 37% | 9 | 0 to 33 |
| `INPATIENT_PALLIATIVE_CARE_PROG_SOCIAL_WORKER_CERTIFIED` | decimal | Palliative care program: certified social workers | 35% | 6 | 0 to 12 |
| `INPATIENT_PALLIATIVE_CARE_PROG_CHAPLAINS` | decimal | Palliative care program: chaplains | 37% | 5 | 0 to 4 |
| `OUTPATIENT_PALLIATIVE_CARE_SERV_OFFERED` | text | Outpatient palliative care services offered | 100% | 2 | No, Yes |

## Fact_Facility_Services

Availability of seven services (hidden in the model; copied into Dim_Hospital as Svc columns).

- **Grain:** one row per hospital
- **Rows:** 501   **Columns:** 8

| Column | Type | Description | Non-null | Distinct | Range / examples |
|---|---|---|---|---|---|
| `FAC_NO` | whole number | Hospital (facility) number; key to Dim_Hospital | 100% | 501 | foreign key |
| `ANESTHESIOLOGIST_AVAILABILITY` | text | Anesthesiologist: 24-Hour / On-Call / Not Available / Not Reported | 100% | 4 | On-Call, 24-Hour, Not Reported, Not Available |
| `LAB_AVAILABILITY` | text | Laboratory availability | 100% | 4 | 24-Hour, Not Reported, On-Call, Not Available |
| `OPER_RM_AVAILABILITY` | text | Operating room availability | 100% | 4 | 24-Hour, On-Call, Not Reported, Not Available |
| `PHARMACIST_AVAILABILITY` | text | Pharmacist availability | 100% | 4 | 24-Hour, Not Reported, On-Call, Not Available |
| `PHYSICIAN_AVAILABILITY` | text | Physician availability | 100% | 3 | 24-Hour, Not Reported, On-Call |
| `PSYCHIATRIC_ER_AVAILABILITY` | text | Psychiatric emergency room availability | 100% | 4 | On-Call, 24-Hour, Not Reported, Not Available |
| `RADIOLOGY_AVAILABILITY` | text | Radiology availability | 100% | 4 | 24-Hour, Not Reported, On-Call, Not Available |

## Fact_Equipment_Facility

Equipment items above $500K.

- **Grain:** one row per equipment item
- **Rows:** 267   **Columns:** 5

| Column | Type | Description | Non-null | Distinct | Range / examples |
|---|---|---|---|---|---|
| `FAC_NO` | whole number | Hospital (facility) number; key to Dim_Hospital | 100% | 106 | foreign key |
| `EQUIPMENT_DESC` | text | Equipment description (items above $500K only) | 100% | 254 | ADV DAVINCI ROBOT, GETECH SFTWR UPGRADE 1666, CONTRAST INJECTOR SYSTEM, ... |
| `EQUIPMENT_VALUE` | whole number | Equipment value (US$) | 100% | 261 | 5.004e+05 to 1.791e+07 |
| `ACQUISITION_DATE` | date | Acquisition date | 100% | 128 | 2022-01-04 to 2026-02-03 |
| `ACQUISITION_MEANS` | text | How the equipment was acquired | 100% | 2 | Purchase, Lease |

## Fact_Projects

Capital projects above $1M.

- **Grain:** one row per project
- **Rows:** 356   **Columns:** 3

| Column | Type | Description | Non-null | Distinct | Range / examples |
|---|---|---|---|---|---|
| `FAC_NO` | whole number | Hospital (facility) number; key to Dim_Hospital | 100% | 138 | foreign key |
| `PROJECT_DESC` | text | Capital project description (projects above $1M only) | 100% | 354 | 5700 TELEGRAPH RENO FLR1, BOILER RPLCMENTS (3), 2cd Floor Interventional Rad, ... |
| `PROJECT_EXPENDITURE` | whole number | Project expenditure to date (US$) | 100% | 330 | 1e+06 to 1.562e+09 |

## Helper tables (calculated, not in the source)

| Table | Purpose |
|---|---|
| `Dim_ServiceLine` | 13 service lines (including "General acute care (not itemised)"), used by `Beds by Service Line` and `Discharges by Service Line` so the rows add up to the totals |
| `Dim_Service` | 7 services, used by `Svc 24-Hour`, `Svc On-Call`, `Svc Not Available`, `Svc Not Reported` |
| `Occ Weight`, `Div Weight`, `LWBS Weight` | What-if parameters for the Pressure Index (defaults 0.5, 0.3, 0.2) |
| `Occupancy Threshold` | What-if parameter (default 85) for High-Pressure Hospitals |

## Columns removed from the source

- The 32 ALOS columns (`*_ALOS_CY`, `*_ALOS_PY`) of the discharge table (stored 0 for no data).
- Columns that were empty for every hospital (for example birth and age-group columns in clinical programs).
- Parent-corporation address, phone, administrator, legislative-district and census-key columns of the hospital table.
- The 12 monthly diversion columns of the ED table (moved to `Fact_ED_Diversion_Monthly`) and the wide equipment / project slots (unpivoted).
