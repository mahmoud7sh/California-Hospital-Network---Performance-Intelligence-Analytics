# DAX Measures

101 measures in the `_Measures` table plus 4 What-if parameter measures. Measures are grouped by display folder. Table and column names are those of the Power BI model; relationships are many-to-one from `Dim_Hospital[FAC_NO]` to each fact table.

**Conventions:** existing measures are reused rather than duplicated; percentages are stored as fractions and formatted as %; hospital-level measures aggregate by summing numerators and denominators (never by averaging percentages); "Not Reported" is never treated as "No".

## What-if parameters

| Measure | Parameter | Default | Use |
|---|---|---|---|
| `Occ Weight Value` | Occ Weight | 0.5 | Pressure Index weight for occupancy |
| `Div Weight Value` | Div Weight | 0.3 | weight for diversion hours |
| `LWBS Weight Value` | LWBS Weight | 0.2 | weight for LWBS rate |
| `Occupancy Threshold Value` | Occupancy Threshold | 85 | occupancy threshold in High-Pressure Hospitals |

The three weights should add up to 1 (defaults 0.5 + 0.3 + 0.2); if they do not, the index is not capped at 100. The occupancy threshold is stored in percentage points (85); the measures accept 85 or 0.85.

## 01 Capacity

### Hospitals

Number of hospitals in the selection.

```DAX
DISTINCTCOUNT(Dim_Hospital[FAC_NO])
```

### Licensed Beds

Sum of total licensed beds.

```DAX
SUM(Fact_Bed_Capacity[TOT_LIC_BEDS])
```

### Licensed Bed Days

Sum of licensed bed-days.

```DAX
SUM(Fact_Bed_Days[TOT_LIC_BED_DAYS])
```

### Census Days

Sum of occupied (census) days.

```DAX
SUM(Fact_Bed_Days[TOT_CEN_DAYS])
```

### Discharges

Sum of total discharges.

```DAX
SUM(Fact_Patient_Discharge[TOT_DISCHARGES])
```

### Occupancy %

Census days / licensed bed days.

```DAX
DIVIDE([Census Days], [Licensed Bed Days])
```

### ALOS

Average length of stay = census days / discharges (all hospitals).

```DAX
DIVIDE([Census Days], [Discharges])
```

### Beds Turnover

Discharges per licensed bed.

```DAX
DIVIDE([Discharges], [Licensed Beds])
```

### Beds per Hospital

Average licensed beds per hospital.

```DAX
DIVIDE([Licensed Beds], [Hospitals])
```

### ALOS (Acute Care)

ALOS excluding Psychiatric, Long-Term Care and Developmentally Disabled hospitals (5.56 vs 6.51 days).

```DAX
CALCULATE([ALOS], KEEPFILTERS(NOT(Dim_Hospital[PRINCIPAL_SERVICE_TYPE] IN {"Psychiatric", "Long-Term Care (SN / IC)", "Developmentally Disabled"})))
```

### Beds by Service Line

Licensed beds per Dim_ServiceLine row; the "not itemised" row = GAC subtotal minus the seven itemised GAC lines, so rows add to 95,759.

```DAX
VAR l = SELECTEDVALUE(Dim_ServiceLine[Service Line])
VAR gac7 = SUM(Fact_Bed_Capacity[MED_SURG_LIC_BEDS]) + SUM(Fact_Bed_Capacity[PERINATAL_LIC_BEDS]) + SUM(Fact_Bed_Capacity[PEDIATRIC_LIC_BEDS]) + SUM(Fact_Bed_Capacity[CORONARY_CARE_LIC_BEDS]) + SUM(Fact_Bed_Capacity[ACUTE_RESPIRATORY_CARE_LIC_BEDS]) + SUM(Fact_Bed_Capacity[BURN_LIC_BEDS]) + SUM(Fact_Bed_Capacity[REHAB_CTR_LIC_BEDS])
RETURN SWITCH(l,
    "Med/Surg", SUM(Fact_Bed_Capacity[MED_SURG_LIC_BEDS]),
    "Perinatal", SUM(Fact_Bed_Capacity[PERINATAL_LIC_BEDS]),
    "Pediatric", SUM(Fact_Bed_Capacity[PEDIATRIC_LIC_BEDS]),
    "Coronary care", SUM(Fact_Bed_Capacity[CORONARY_CARE_LIC_BEDS]),
    "Acute respiratory care", SUM(Fact_Bed_Capacity[ACUTE_RESPIRATORY_CARE_LIC_BEDS]),
    "Burn", SUM(Fact_Bed_Capacity[BURN_LIC_BEDS]),
    "Rehabilitation", SUM(Fact_Bed_Capacity[REHAB_CTR_LIC_BEDS]),
    "General acute care (not itemised)", SUM(Fact_Bed_Capacity[GAC_SUBTOT_LIC_BEDS]) - gac7,
    "Chemical dependency recovery", SUM(Fact_Bed_Capacity[CHEM_DEPEND_RECOVERY_LIC_BEDS]),
    "Acute psychiatric", SUM(Fact_Bed_Capacity[ACUTE_PSYCHIATRIC_LIC_BEDS]),
    "Skilled nursing", SUM(Fact_Bed_Capacity[SN_LIC_BEDS]),
    "Intermediate care", SUM(Fact_Bed_Capacity[INTERMEDIATE_CARE_LIC_BEDS]),
    "Intermediate care (dev. disability)", SUM(Fact_Bed_Capacity[INTERMEDIATE_CARE_DEV_DIS_LIC_BEDS]),
    SUM(Fact_Bed_Capacity[TOT_LIC_BEDS]))
```

### Discharges by Service Line

Discharges per Dim_ServiceLine row; rows add to 3,452,872.

```DAX
VAR l = SELECTEDVALUE(Dim_ServiceLine[Service Line])
VAR gac7 = SUM(Fact_Patient_Discharge[MED_SURG_DISCHARGES]) + SUM(Fact_Patient_Discharge[PERINATAL_DISCHARGES]) + SUM(Fact_Patient_Discharge[PEDIATRIC_DISCHARGES]) + SUM(Fact_Patient_Discharge[CORONARY_CARE_DISCHARGES]) + SUM(Fact_Patient_Discharge[ACUTE_RESPIRATORY_CARE_DISCHARGES]) + SUM(Fact_Patient_Discharge[BURN_DISCHARGES]) + SUM(Fact_Patient_Discharge[REHAB_CTR_DISCHARGES])
RETURN SWITCH(l,
    "Med/Surg", SUM(Fact_Patient_Discharge[MED_SURG_DISCHARGES]),
    "Perinatal", SUM(Fact_Patient_Discharge[PERINATAL_DISCHARGES]),
    "Pediatric", SUM(Fact_Patient_Discharge[PEDIATRIC_DISCHARGES]),
    "Coronary care", SUM(Fact_Patient_Discharge[CORONARY_CARE_DISCHARGES]),
    "Acute respiratory care", SUM(Fact_Patient_Discharge[ACUTE_RESPIRATORY_CARE_DISCHARGES]),
    "Burn", SUM(Fact_Patient_Discharge[BURN_DISCHARGES]),
    "Rehabilitation", SUM(Fact_Patient_Discharge[REHAB_CTR_DISCHARGES]),
    "General acute care (not itemised)", SUM(Fact_Patient_Discharge[GAC_SUBTOT_DISCHARGES]) - gac7,
    "Chemical dependency recovery", SUM(Fact_Patient_Discharge[CHEM_DEPEND_RECOVERY_DISCHARGES]),
    "Acute psychiatric", SUM(Fact_Patient_Discharge[ACUTE_PSYCHIATRIC_DISCHARGES]),
    "Skilled nursing", SUM(Fact_Patient_Discharge[SN_DISCHARGES]),
    "Intermediate care", SUM(Fact_Patient_Discharge[INTERMEDIATE_CARE_DISCHARGES]),
    "Intermediate care (dev. disability)", SUM(Fact_Patient_Discharge[INTERMEDIATE_CARE_DEV_DIS_DISCHARGES]),
    SUM(Fact_Patient_Discharge[TOT_DISCHARGES]))
```

## 02 Emergency

### ED Visits

Total ED traffic.

```DAX
SUM(Fact_ED[ER_TRAFFIC_TOT])
```

### ED Admissions

Patients admitted from the ED.

```DAX
SUM(Fact_ED_Outcome[ADMITTED_FROM_EMER_DEPT_TOT])
```

### LWBS Patients

Patients who left without being seen.

```DAX
SUM(Fact_ED_Outcome[EMER_REGISTRATIONS_PATS_LEAVE_WO_BEING_SEEN])
```

### LWBS %

LWBS patients / ED visits.

```DAX
DIVIDE([LWBS Patients], [ED Visits])
```

### ED Admission %

ED admissions / ED visits.

```DAX
DIVIDE([ED Admissions], [ED Visits])
```

### ED Stations

Emergency treatment stations on 31 Dec.

```DAX
SUM(Fact_ED[EMER_MED_TREAT_STATIONS_ON_1231])
```

### Visits per ED Station

ED visits / stations.

```DAX
DIVIDE([ED Visits], [ED Stations])
```

### High-Acuity %

Severe + critical EMS visits as a share of all classified visits, admitted and not admitted (the first version used non-admitted visits only: 55.7% vs 60.4%).

```DAX
VAR sevCrit = SUM(Fact_ED[EMS_VISITS_SEVERE_TOT]) + SUM(Fact_ED[EMS_VISITS_CRITICAL_TOT]) +
    SUM(Fact_ED_Outcome[EMS_VISITS_SEVERE_ADMITTED]) + SUM(Fact_ED_Outcome[EMS_VISITS_CRITICAL_ADMITTED])
VAR allVisits = SUM(Fact_ED[EMS_VISITS_NON_URGENT_TOT]) + SUM(Fact_ED[EMS_VISITS_URGENT_TOT]) + SUM(Fact_ED[EMS_VISITS_MODERATE_TOT]) +
    SUM(Fact_ED[EMS_VISITS_SEVERE_TOT]) + SUM(Fact_ED[EMS_VISITS_CRITICAL_TOT]) +
    SUM(Fact_ED_Outcome[EMS_VISITS_NON_URGENT_ADMITTED]) + SUM(Fact_ED_Outcome[EMS_VISITS_URGENT_ADMITTED]) + SUM(Fact_ED_Outcome[EMS_VISITS_MODERATE_ADMITTED]) +
    SUM(Fact_ED_Outcome[EMS_VISITS_SEVERE_ADMITTED]) + SUM(Fact_ED_Outcome[EMS_VISITS_CRITICAL_ADMITTED])
RETURN DIVIDE(sevCrit, allVisits)
```

### ED Hospitals

Hospitals with ED traffic reported (326).

```DAX
CALCULATE(DISTINCTCOUNT(Fact_ED[FAC_NO]), Fact_ED[ER_TRAFFIC_TOT] > 0)
```

## 03 Diversion

### Diversion Hours

Sum of ambulance diversion hours.

```DAX
SUM(Fact_ED_Diversion_Monthly[DIVERSION_HOURS])
```

### Hospitals Diverting

Hospitals with at least one diversion hour.

```DAX
CALCULATE(
        DISTINCTCOUNT(Fact_ED_Diversion_Monthly[FAC_NO]),
        Fact_ED_Diversion_Monthly[DIVERSION_HOURS] > 0
    )
```

### Peak Diversion Month

Month with the most diversion hours in the current selection.

```DAX
VAR t = ADDCOLUMNS(VALUES(Fact_ED_Diversion_Monthly[MONTH_NAME]), "@h", [Diversion Hours])
    RETURN MAXX(TOPN(1, t, [@h], DESC), Fact_ED_Diversion_Monthly[MONTH_NAME])
```

### Diverting %

Hospitals diverting / ED hospitals.

```DAX
DIVIDE([Hospitals Diverting], [ED Hospitals])
```

## 04 Surgery

### Surgeries

Inpatient + outpatient surgical operations.

```DAX
SUM(Fact_Surgery[INPATIENT_SURG_OPER]) + SUM(Fact_Surgery[OUTPATIENT_SURG_OPER])
```

### Outpatient Surgery %

Outpatient share of surgeries.

```DAX
DIVIDE(SUM(Fact_Surgery[OUTPATIENT_SURG_OPER]), [Surgeries])
```

### Operating Rooms

Total operating rooms.

```DAX
SUM(Fact_Surgery[OPER_RM_TOT])
```

### Surgeries per OR

Surgeries / operating rooms.

```DAX
DIVIDE([Surgeries], [Operating Rooms])
```

## 05 Cardiology

### Cath Procedures

Total catheterization procedures.

```DAX
SUM(Fact_Cardiovascular[CATHETERIZATION_PROC_TOT])
```

### PCI Procedures

PCI with + without stent.

```DAX
SUM(Fact_Cardiovascular[PCI_WITH_STENT]) + SUM(Fact_Cardiovascular[PCI_WO_STENT])
```

### Cath Lab Hospitals

Hospitals with at least one cath-lab room.

```DAX
CALCULATE(
        DISTINCTCOUNT(Fact_Cardiovascular[FAC_NO]),
        Fact_Cardiovascular[CARDIAC_CATHETERIZATION_LAB_RM] > 0
    )
```

### Diagnostic Cath Visits

Diagnostic cath visits.

```DAX
SUM(Fact_Cardiovascular[CARDIAC_CATHETERIZATION_DIAGNOSTIC_VISITS_TOT])
```

### Therapeutic Cath Visits

Therapeutic cath visits.

```DAX
SUM(Fact_Cardiovascular[CARDIAC_CATHETERIZATION_THERAPEUTIC_VISITS_TOT])
```

### CABG Procedures

CABG surgeries.

```DAX
SUM(Fact_Cardiovascular[CORONARY_ARTERY_BYPASS_GRAFT_SURG])
```

### Cath Lab Rooms

Cath lab rooms.

```DAX
SUM(Fact_Cardiovascular[CARDIAC_CATHETERIZATION_LAB_RM])
```

## 06 Services

### Hospitals Reporting Services

Hospitals that reported any service availability.

```DAX
CALCULATE(
        COUNTROWS(Fact_Facility_Services),
        Fact_Facility_Services[LAB_AVAILABILITY] <> "Not Reported"
    )
```

### 24H Lab %

Share of reporting hospitals with 24-hour lab.

```DAX
DIVIDE(
        CALCULATE(COUNTROWS(Fact_Facility_Services), Fact_Facility_Services[LAB_AVAILABILITY] = "24-Hour"),
        [Hospitals Reporting Services]
    )
```

### 24H Radiology %

Share of reporting hospitals with 24-hour radiology.

```DAX
DIVIDE(
        CALCULATE(COUNTROWS(Fact_Facility_Services), Fact_Facility_Services[RADIOLOGY_AVAILABILITY] = "24-Hour"),
        [Hospitals Reporting Services]
    )
```

### 24H Operating Room %

Share of reporting hospitals with 24-hour operating room.

```DAX
DIVIDE(
        CALCULATE(COUNTROWS(Fact_Facility_Services), Fact_Facility_Services[OPER_RM_AVAILABILITY] = "24-Hour"),
        [Hospitals Reporting Services]
    )
```

### Services Coverage %

Share of hospitals that reported any service availability.

```DAX
DIVIDE([Hospitals Reporting Services], [Hospitals])
```

### Svc 24-Hour

Hospitals whose availability of the service on the axis (Dim_Service) is "24-Hour"; the four measures add to 501 for every service.

```DAX
SWITCH(SELECTEDVALUE(Dim_Service[Service]),
    "Anesthesiologist", CALCULATE([Hospitals], Dim_Hospital[Svc Anesthesiologist] = "24-Hour"),
    "Laboratory", CALCULATE([Hospitals], Dim_Hospital[Svc Lab] = "24-Hour"),
    "Operating room", CALCULATE([Hospitals], Dim_Hospital[Svc Operating Room] = "24-Hour"),
    "Pharmacist", CALCULATE([Hospitals], Dim_Hospital[Svc Pharmacist] = "24-Hour"),
    "Physician", CALCULATE([Hospitals], Dim_Hospital[Svc Physician] = "24-Hour"),
    "Psychiatric ER", CALCULATE([Hospitals], Dim_Hospital[Svc Psychiatric ER] = "24-Hour"),
    "Radiology", CALCULATE([Hospitals], Dim_Hospital[Svc Radiology] = "24-Hour"),
    BLANK())
```

### Svc On-Call

Hospitals whose availability of the service on the axis (Dim_Service) is "On-Call"; the four measures add to 501 for every service.

```DAX
SWITCH(SELECTEDVALUE(Dim_Service[Service]),
    "Anesthesiologist", CALCULATE([Hospitals], Dim_Hospital[Svc Anesthesiologist] = "On-Call"),
    "Laboratory", CALCULATE([Hospitals], Dim_Hospital[Svc Lab] = "On-Call"),
    "Operating room", CALCULATE([Hospitals], Dim_Hospital[Svc Operating Room] = "On-Call"),
    "Pharmacist", CALCULATE([Hospitals], Dim_Hospital[Svc Pharmacist] = "On-Call"),
    "Physician", CALCULATE([Hospitals], Dim_Hospital[Svc Physician] = "On-Call"),
    "Psychiatric ER", CALCULATE([Hospitals], Dim_Hospital[Svc Psychiatric ER] = "On-Call"),
    "Radiology", CALCULATE([Hospitals], Dim_Hospital[Svc Radiology] = "On-Call"),
    BLANK())
```

### Svc Not Available

Hospitals whose availability of the service on the axis (Dim_Service) is "Not Available"; the four measures add to 501 for every service.

```DAX
SWITCH(SELECTEDVALUE(Dim_Service[Service]),
    "Anesthesiologist", CALCULATE([Hospitals], Dim_Hospital[Svc Anesthesiologist] = "Not Available"),
    "Laboratory", CALCULATE([Hospitals], Dim_Hospital[Svc Lab] = "Not Available"),
    "Operating room", CALCULATE([Hospitals], Dim_Hospital[Svc Operating Room] = "Not Available"),
    "Pharmacist", CALCULATE([Hospitals], Dim_Hospital[Svc Pharmacist] = "Not Available"),
    "Physician", CALCULATE([Hospitals], Dim_Hospital[Svc Physician] = "Not Available"),
    "Psychiatric ER", CALCULATE([Hospitals], Dim_Hospital[Svc Psychiatric ER] = "Not Available"),
    "Radiology", CALCULATE([Hospitals], Dim_Hospital[Svc Radiology] = "Not Available"),
    BLANK())
```

### Svc Not Reported

Hospitals whose availability of the service on the axis (Dim_Service) is "Not Reported"; the four measures add to 501 for every service.

```DAX
SWITCH(SELECTEDVALUE(Dim_Service[Service]),
    "Anesthesiologist", CALCULATE([Hospitals], Dim_Hospital[Svc Anesthesiologist] = "Not Reported"),
    "Laboratory", CALCULATE([Hospitals], Dim_Hospital[Svc Lab] = "Not Reported"),
    "Operating room", CALCULATE([Hospitals], Dim_Hospital[Svc Operating Room] = "Not Reported"),
    "Pharmacist", CALCULATE([Hospitals], Dim_Hospital[Svc Pharmacist] = "Not Reported"),
    "Physician", CALCULATE([Hospitals], Dim_Hospital[Svc Physician] = "Not Reported"),
    "Psychiatric ER", CALCULATE([Hospitals], Dim_Hospital[Svc Psychiatric ER] = "Not Reported"),
    "Radiology", CALCULATE([Hospitals], Dim_Hospital[Svc Radiology] = "Not Reported"),
    BLANK())
```

## 07 Investment

### Equipment Value

Sum of reported equipment value (above $500K).

```DAX
SUM(Fact_Equipment_Facility[EQUIPMENT_VALUE])
```

### Project Spend

Sum of reported project expenditure (above $1M).

```DAX
SUM(Fact_Projects[PROJECT_EXPENDITURE])
```

### Investment per Bed

(Equipment + project spend) / licensed beds (threshold-reported amounts).

```DAX
DIVIDE([Equipment Value] + [Project Spend], [Licensed Beds])
```

### Equipment Items

Equipment items reported.

```DAX
COUNTROWS(Fact_Equipment_Facility)
```

### Hospitals with Equipment Reported

Hospitals with at least one equipment item.

```DAX
DISTINCTCOUNT(Fact_Equipment_Facility[FAC_NO])
```

### Projects Reported

Projects reported (rows).

```DAX
COUNTROWS(Fact_Projects)
```

### Hospitals with Projects Reported

Hospitals with at least one project.

```DAX
DISTINCTCOUNT(Fact_Projects[FAC_NO])
```

## 08 Benchmarking

### Occupancy % (State)

Occupancy of all hospitals (ignores filters).

```DAX
CALCULATE([Occupancy %], ALL(Dim_Hospital))
```

### Occupancy vs State (pp)

Occupancy minus state occupancy, in percentage points.

```DAX
([Occupancy %] - [Occupancy % (State)]) * 100
```

### Occupancy % (Peers)

Occupancy of hospitals with the same principal service type as the hospital in view.

```DAX
VAR st = SELECTEDVALUE(Dim_Hospital[PRINCIPAL_SERVICE_TYPE])
RETURN IF(ISBLANK(st),
    CALCULATE([Occupancy %], ALLEXCEPT(Dim_Hospital, Dim_Hospital[PRINCIPAL_SERVICE_TYPE])),
    CALCULATE([Occupancy %], ALL(Dim_Hospital), Dim_Hospital[PRINCIPAL_SERVICE_TYPE] = st))
```

### Occupancy Rank

Rank by occupancy within the current selection.

```DAX
RANKX(ALLSELECTED(Dim_Hospital[FAC_NO]), [Occupancy %], , DESC, Dense)
```

### Pressure Rank

Rank by Pressure Index within the selection (1 = highest). Use as a visual filter instead of Top N.

```DAX
RANKX(ALLSELECTED(Dim_Hospital), [Pressure Index], , DESC, Dense)
```

### LWBS % (State)

LWBS rate of all hospitals.

```DAX
CALCULATE([LWBS %], ALL(Dim_Hospital))
```

### LWBS vs State (pp)

LWBS minus state LWBS, percentage points.

```DAX
([LWBS %] - [LWBS % (State)]) * 100
```

### ALOS (State)

ALOS of all hospitals.

```DAX
CALCULATE([ALOS], ALL(Dim_Hospital))
```

### ALOS vs State (days)

ALOS minus state ALOS.

```DAX
[ALOS] - [ALOS (State)]
```

## 09 Data Quality

### Hospitals Over 100% Occupancy

Hospitals whose census days exceed licensed bed-days.

```DAX
COUNTROWS(FILTER(VALUES(Dim_Hospital[FAC_NO]), [Occupancy %] > 1))
```

### Teaching Hospitals

Hospitals flagged as teaching.

```DAX
CALCULATE([Hospitals], Dim_Hospital[TEACH_HOSP] = "Yes")
```

### Rural Hospitals

Hospitals flagged as rural.

```DAX
CALCULATE([Hospitals], Dim_Hospital[RURAL_HOSP] = "Yes")
```

### Trauma Centers

Hospitals that are trauma centers.

```DAX
CALCULATE([Hospitals], Dim_Hospital[IS_TRAUMA_CENTER] = "Yes")
```

### Not Open Hospitals

Hospitals whose license status is not Open.

```DAX
CALCULATE([Hospitals], Dim_Hospital[LICENSE_STATUS] <> "Open")
```

### Counties

Counties in the selection.

```DAX
DISTINCTCOUNT(Dim_Hospital[COUNTY])
```

### Non-Profit Share %

Share of hospitals run by non-profit organisations.

```DAX
DIVIDE(CALCULATE([Hospitals], Dim_Hospital[OWNERSHIP_GROUP] = "Non-Profit"), [Hospitals])
```

### Long-Stay Hospitals (ALOS > 60)

Hospitals with ALOS above 60 days (27).

```DAX
COALESCE(COUNTROWS(FILTER(VALUES(Dim_Hospital[FAC_NO]), [ALOS] > 60)), 0)
```

## 10 Pressure Index

### Pressure Index

Average 0-100 pressure score of the hospitals in the selection: 50% occupancy (capped at 100%), 30% diversion hours, 20% LWBS rate (weights from the parameters), diversion and LWBS scaled to the highest hospital left by slicers. Analyst-defined, relative to the selection, not a clinical standard.

```DAX
VAR sel   = ALLSELECTED(Dim_Hospital)
VAR divMx = MAXX(sel, [Diversion Hours])
VAR lwMx  = MAXX(sel, [LWBS %])
RETURN AVERAGEX(VALUES(Dim_Hospital[FAC_NO]),
    VAR occ = MIN(COALESCE([Occupancy %], 0), 1)
    VAR dv  = COALESCE(DIVIDE([Diversion Hours], divMx), 0)
    VAR lw  = COALESCE(DIVIDE([LWBS %], lwMx), 0)
    RETURN 100 * ([Occ Weight Value] * occ + [Div Weight Value] * dv + [LWBS Weight Value] * lw))
```

### High-Pressure Hospitals

Hospitals with occupancy at or above the threshold parameter AND at least one diversion hour (threshold accepted as 85 or 0.85).

```DAX
VAR thr = IF([Occupancy Threshold Value] > 1, [Occupancy Threshold Value] / 100, [Occupancy Threshold Value])
RETURN COALESCE(COUNTROWS(FILTER(VALUES(Dim_Hospital[FAC_NO]), [Occupancy %] >= thr && [Diversion Hours] > 0)), 0)
```

### Pressure Index (State Scale)

Pressure Index scaled against all hospitals (ignores slicers and drill-through); for single-hospital views.

```DAX
VAR divMx = MAXX(ALL(Dim_Hospital), [Diversion Hours])
VAR lwMx  = MAXX(ALL(Dim_Hospital), [LWBS %])
RETURN AVERAGEX(VALUES(Dim_Hospital[FAC_NO]),
    VAR occ = MIN(COALESCE([Occupancy %], 0), 1)
    VAR dv  = COALESCE(DIVIDE([Diversion Hours], divMx), 0)
    VAR lw  = COALESCE(DIVIDE([LWBS %], lwMx), 0)
    RETURN 100 * ([Occ Weight Value] * occ + [Div Weight Value] * dv + [LWBS Weight Value] * lw))
```

### Pressure Rank (State)

Rank among all hospitals by the state-scaled index.

```DAX
VAR me = [Pressure Index (State Scale)]
RETURN IF(ISBLANK(me), BLANK(), COUNTROWS(FILTER(ALL(Dim_Hospital), CALCULATE([Pressure Index (State Scale)]) > me)) + 1)
```

## 11 Titles

### Overview Title

Dynamic headline for the overview.

```DAX
"Occupancy " & FORMAT([Occupancy %], "0.0%") & " across " & FORMAT([Hospitals], "#,0") & " hospitals"
```

### Insight Occupancy

Dynamic sentence: occupancy versus the statewide benchmark.

```DAX
VAR d = [Occupancy vs State (pp)]
VAR cmp = IF(ABS(d) < 0.05, "in line with", IF(d > 0, FORMAT(d, "0.0") & " pp above", FORMAT(-d, "0.0") & " pp below"))
RETURN "Occupancy is " & FORMAT([Occupancy %], "0.0%") & ", " & cmp & " the statewide benchmark of " & FORMAT([Occupancy % (State)], "0.0%") & "."
```

### Insight Pressure

Dynamic sentence: high-pressure hospitals and hospitals above 100%.

```DAX
VAR t = IF([Occupancy Threshold Value] > 1, [Occupancy Threshold Value], [Occupancy Threshold Value] * 100)
RETURN [High-Pressure Hospitals] & " of " & [Hospitals] & " hospitals have occupancy of at least " & FORMAT(t, "0") & "% and report ambulance diversion; " & [Hospitals Over 100% Occupancy] & " report occupancy above 100%."
```

### Insight ED

Dynamic sentence: ED visits, LWBS and peak diversion month.

```DAX
"Emergency departments recorded " & FORMAT([ED Visits], "#,0") & " visits; " & FORMAT([LWBS %], "0.0%") & " left without being seen, and diversion peaked in " & COALESCE([Peak Diversion Month], "n/a") & "."
```

### Hospital Title

Name of the selected hospital (profile page, tooltips).

```DAX
SELECTEDVALUE(Dim_Hospital[FAC_NAME], "Select a hospital")
```

### Hospital Subtitle

City, county, ownership and service type of the selected hospital.

```DAX
IF(ISBLANK(SELECTEDVALUE(Dim_Hospital[FAC_NO])), "Right-click a hospital in the directory and choose Drill through > Hospital Profile", SELECTEDVALUE(Dim_Hospital[FAC_CITY]) & ", " & SELECTEDVALUE(Dim_Hospital[COUNTY]) & " County · " & SELECTEDVALUE(Dim_Hospital[OWNERSHIP_GROUP]) & " · " & SELECTEDVALUE(Dim_Hospital[PRINCIPAL_SERVICE_TYPE]))
```

## 12 Critical Care (IC)

### IC Licensed Beds

IC licensed beds (IC as named in the source).

```DAX
SUM(Fact_ICU[IC_LIC_BEDS])
```

### IC Census Days

IC census days.

```DAX
SUM(Fact_ICU[IC_CEN_DAYS])
```

### IC Discharges

IC discharges.

```DAX
SUM(Fact_ICU[IC_DISCHARGES])
```

### IC Occupancy %

IC census days / IC licensed bed-days.

```DAX
DIVIDE([IC Census Days], SUM(Fact_ICU[IC_LIC_BED_DAYS]))
```

### IC Newborn Beds

Newborn IC licensed beds.

```DAX
SUM(Fact_ICU[IC_NEWBORN_LIC_BEDS])
```

### IC Newborn Census Days

Newborn IC census days.

```DAX
SUM(Fact_ICU[IC_NEWBORN_CEN_DAYS])
```

### IC Newborn Occupancy %

Newborn IC census days / bed-days.

```DAX
DIVIDE([IC Newborn Census Days], SUM(Fact_ICU[IC_NEWBORN_LIC_BED_DAYS]))
```

## 13 Clinical Programs

### Acute Psychiatric Beds

Acute psychiatric licensed beds.

```DAX
SUM(Fact_Bed_Capacity[ACUTE_PSYCHIATRIC_LIC_BEDS])
```

### Hospitals with Inpatient Hospice

Hospitals offering inpatient hospice.

```DAX
CALCULATE(DISTINCTCOUNT(Fact_Clinical_Programs[FAC_NO]), Fact_Clinical_Programs[INPATIENT_HOSPICE_PROG_OFFERED] = "Yes")
```

### Hospitals with Inpatient Palliative Care

Hospitals with inpatient palliative care.

```DAX
CALCULATE(DISTINCTCOUNT(Fact_Clinical_Programs[FAC_NO]), Fact_Clinical_Programs[INPATIENT_PALLIATIVE_CARE_PROG_OFFERED] = "Yes")
```

### Hospitals with Outpatient Palliative Care

Hospitals with outpatient palliative care.

```DAX
CALCULATE(DISTINCTCOUNT(Fact_Clinical_Programs[FAC_NO]), Fact_Clinical_Programs[OUTPATIENT_PALLIATIVE_CARE_SERV_OFFERED] = "Yes")
```

## 14 Formatting

### Occupancy Color

Hex colour for conditional formatting: red at 100% or more, amber at 85% or more, otherwise blue.

```DAX
SWITCH(TRUE(), [Occupancy %] >= 1, "#C65353", [Occupancy %] >= 0.85, "#E9A23B", "#1F7896")
```

### Pressure Color

Hex colour: red at 60 or more, amber at 35 or more, otherwise blue.

```DAX
SWITCH(TRUE(), [Pressure Index] >= 60, "#C65353", [Pressure Index] >= 35, "#E9A23B", "#1F7896")
```

### LWBS Color

Hex colour: amber at 4% or more, otherwise blue.

```DAX
IF([LWBS %] >= 0.04, "#E9A23B", "#1F7896")
```

### Diversion Color

Hex colour: amber when there are diversion hours, otherwise slate.

```DAX
IF([Diversion Hours] > 0, "#E9A23B", "#64748B")
```

### Month Highlight Color

Amber for the peak month among the months shown, blue for others.

```DAX
VAR t = ADDCOLUMNS(ALLSELECTED(Fact_ED_Diversion_Monthly[MONTH_NAME]), "@h", CALCULATE([Diversion Hours]))
VAR pk = MAXX(TOPN(1, t, [@h], DESC), Fact_ED_Diversion_Monthly[MONTH_NAME])
RETURN IF(SELECTEDVALUE(Fact_ED_Diversion_Monthly[MONTH_NAME]) = pk, "#E9A23B", "#1F7896")
```

## 15 Tooltips

### Hospital Status

Plain-language status: above capacity, high occupancy and diversion, high occupancy, normal.

```DAX
VAR occ = [Occupancy %]
VAR t = IF([Occupancy Threshold Value] > 1, [Occupancy Threshold Value] / 100, [Occupancy Threshold Value])
RETURN SWITCH(TRUE(), ISBLANK(occ), "No bed data reported", occ >= 1, "Above licensed capacity",
    occ >= t && [Diversion Hours] > 0, "High occupancy and ambulance diversion", occ >= t, "High occupancy", "Within normal range")
```

### County Title

County name for the tooltip.

```DAX
SELECTEDVALUE(Dim_Hospital[COUNTY], "All counties")
```

### County Subtitle

Hospitals, beds and occupancy summary for the tooltip.

```DAX
[Hospitals] & " hospitals · " & FORMAT([Licensed Beds], "#,0") & " beds · " & FORMAT([Occupancy %], "0.0%") & " occupancy"
```

### Top Diversion County

County with the most diversion hours in context.

```DAX
VAR t = ADDCOLUMNS(VALUES(Dim_Hospital[COUNTY]), "@h", [Diversion Hours])
VAR top1 = TOPN(1, FILTER(t, [@h] > 0), [@h], DESC)
RETURN COALESCE(MAXX(top1, Dim_Hospital[COUNTY]), "None")
```

### Top County Diversion Share

Share of diversion hours in context held by the top county.

```DAX
VAR c = [Top Diversion County]
RETURN DIVIDE(CALCULATE([Diversion Hours], Dim_Hospital[COUNTY] = c), [Diversion Hours])
```

### Occupancy vs State Label

Text such as "+44.9 pp vs state".

```DAX
VAR d = [Occupancy vs State (pp)]
RETURN IF(ISBLANK(d), "", FORMAT(d, "+0.0;-0.0;0.0") & " pp vs state")
```

## Known behaviours

- `Pressure Index`, `Pressure Rank` and `Occupancy Rank` are relative to the hospitals left by slicers and page filters; use a visual filter on `Pressure Rank` instead of Top N.
- On a single-hospital page use `Pressure Index (State Scale)` and `Pressure Rank (State)`; the relative versions would always show 100 and 1.
- `ALOS` includes long-stay facilities (up to about 3,227 days); `ALOS (Acute Care)` excludes them.
- Optional formatting measure used in the build guide: `Vs State Color` = SWITCH(TRUE(), [Occupancy vs State (pp)] > 5, "#E9A23B", [Occupancy vs State (pp)] < -5, "#2A9D8F", "#64748B").
