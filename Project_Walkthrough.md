# Project walkthrough: from raw data to insights

This document records, in order, what was done to turn one wide public table of California hospital utilization data into an 11-page Power BI report, and why each decision was made.

## 1. Source data

- **Source:** California hospital annual utilization data (HCAI), downloaded as a single wide Excel table: 503 rows, 501 unique hospitals (key `FAC_NO`).
- **Grain:** one row per hospital per reporting period, one reporting year.
- **First checks:** unique keys, empty columns, blank patterns, totals versus components.
- **First finding:** two facilities (`106150021` Kern Youth, `106190449` La Mirada) appear twice because the operator changed during the year. They are not true duplicates.

## 2. Splitting into a dimension and facts

The wide table was split by subject into one dimension and thirteen fact tables, all keyed on `FAC_NO`:

| Table | Content |
|---|---|
| `Dim_Hospital` | name, city, county, coordinates, ownership, license status, trauma / teaching / rural flags, service type |
| `Fact_Bed_Capacity`, `Fact_Bed_Days`, `Fact_Patient_Discharge` | licensed beds, bed-days and census days, discharges by service line |
| `Fact_ED`, `Fact_ED_Outcome`, `Fact_ED_Diversion_Monthly` | ED traffic and acuity, admissions and left-without-being-seen, monthly ambulance diversion |
| `Fact_ICU` | IC columns (labelled as in the source) |
| `Fact_Surgery`, `Fact_Cardiovascular`, `Fact_Clinical_Programs` | operating rooms and surgeries, catheterization and PCI, hospice / palliative / psychiatric programs |
| `Fact_Facility_Services` | 24-hour / on-call availability of seven services |
| `Fact_Equipment_Facility`, `Fact_Projects` | equipment above $500K, capital projects above $1M |

## 3. Cleaning in Power Query

| Issue | Decision |
|---|---|
| Operator-change duplicates | One dimension row per hospital (active operator kept, noted); fact periods combined, not dropped |
| Empty columns | Removed (for example, birth and age-group columns in clinical programs, unused bypass columns) |
| Blank values | Blank is not always zero: ED outcome blanks became 0, hospitals without data stay blank; labels such as "Non-Trauma" and "Not Reported" replace blanks in the dimension |
| ALOS source columns | Removed (they stored 0 for "no data"); ALOS is calculated instead |
| Wide equipment and project slots | Unpivoted to one row per item: 267 equipment items (106 hospitals) and 356 projects (138 hospitals) |
| Monthly diversion | Twelve monthly columns unpivoted to hospital x month; unpivot drops blanks, so blanks were set to 0 first: 326 ED hospitals x 12 months = 3,912 rows, 125,631 hours |
| Service flags | 14 "X" flag columns became one status per service: 24-Hour, On-Call, Not Available, Not Reported |
| Text | Trimmed; city and county in title case |
| Derived columns | `OWNERSHIP_GROUP` (Non-Profit, Investor-Owned, Government), `IS_TRAUMA_CENTER` |

## 4. Data model

- **Star schema:** `Dim_Hospital` in the centre, thirteen fact tables on the many side, one-to-many, single direction on `FAC_NO`.
- **Service availability** was moved into the dimension as seven columns so any slicer filters every page.
- **Helper tables:** `Dim_ServiceLine` (beds and discharges by service line), `Dim_Service` (availability by service), four What-if parameter tables for the Pressure Index (occupancy, diversion and LWBS weights, occupancy threshold).
- **Rules kept throughout:** totals and components are never added together (the seven itemised general-acute-care lines cover only part of the subtotal, so a "not itemised" row reconciles to 95,759 beds and 3,452,872 discharges); missing is not zero and not "No".

## 5. DAX measures

About 100 measures in `_Measures`, grouped in display folders: capacity, emergency, diversion, surgery, cardiology, services, investment, benchmarking, data quality, formatting colours and tooltips.

Key definitions: occupancy = census days / licensed bed days; ALOS = census days / discharges; LWBS = left without being seen / ED visits; High-Acuity % = severe and critical visits / all classified visits.

**Pressure Index** (analyst-defined, not a clinical standard): 100 x (0.5 x occupancy capped at 100% + 0.3 x diversion hours + 0.2 x LWBS rate), diversion and LWBS scaled to the highest hospital in the selection.

## 6. Problems found and fixed during build

| Problem | Fix |
|---|---|
| Hospital rows duplicated in two fact tables | Kept (sums are right); never average ALOS across rows |
| Diversion unpivot returned 2,131 rows instead of 3,912 | Unpivot drops blanks: replace blanks with 0 before unpivoting |
| ED outcome lost 72 hospitals (left-without-seen blank) | Blank became 0 instead of deleting rows: 347 hospitals restored |
| Pressure Index showed 100 for single hospitals | Scaling now uses the whole dimension table; a state-scale version serves single-hospital views |
| High-Acuity % was 55.7% | The five acuity columns in the ED table only cover visits not resulting in admission; including admitted visits gives 60.4% |
| ALOS 3,227 days for a state psychiatric hospital | Genuine long-stay facility; added an acute-care ALOS (5.56 days) and a count of long-stay hospitals (27) |
| Occupancy peers equalled the state value for a single hospital | Peer group now defined by the hospital's own service type |
| Top N filter changed Pressure Index values | Ranking visuals use a Pressure Rank filter instead of Top N |

## 7. Validation

Measures were checked with DAX queries against the live model and compared with totals computed independently in Python from the source:

- Hospitals 501, licensed beds 95,759, occupancy 64.5%, ALOS 6.51 days, discharges 3,452,872
- ED visits 15,551,325, LWBS 1.95%, diversion hours 125,631 (145 hospitals), 10 hospitals above 100% occupancy
- Service-line rows add up to the totals; every service availability row sums to 501 hospitals

## 8. Report design

- **Theme:** custom palette (navy, blue, teal, amber for attention, red only for real exceptions), Segoe UI.
- **Frame on every page:** header, page navigator, four synced slicers, reset button, six KPIs.
- **Colour rules:** one blue gradient for magnitudes, threshold rules for occupancy and pressure, diverging scale around the state value, never red-green.
- **Pages:** 01 Overview, 02 Network, 03 Capacity, 04 Patient Flow, 05 Emergency, 06 Pressure, 07 IC & Surgery, 08 Cardio & Clinical, 09 Investment, 10 Services, 11 Methodology & Data Quality. Each headline is a question the page answers.

## 9. Insights

All descriptive; none implies causation or clinical quality.

1. Network averages 64.5% occupancy; 85 hospitals sit at 85% or more and 10 report more than 100% of licensed bed-days.
2. Only 10 hospitals combine high occupancy with ambulance diversion, so high occupancy and diversion do not always coincide.
3. Diversion is concentrated: Los Angeles County holds about 60% of the hours; January is the peak (23,177 hours).
4. Length of stay depends on service type: 6.5 days overall, 5.6 days in acute care, with 27 long-stay facilities above 60 days.
5. 60.4% of classified ED visits are severe or critical; 2.0% of ED patients leave without being seen.
6. 24-hour laboratory coverage is 91% among reporting hospitals, but only 44.5% for 24-hour operating rooms; 155 hospitals reported no service availability.
7. Reported equipment totals $400.7M (almost all 2025 acquisitions); reported projects total $5.87B, over half from two hospitals.

## 10. Limitations

One reporting year (no trends except monthly diversion); IC columns unconfirmed; equipment and projects are threshold-reported; blank diversion months treated as zero; no population, financial or outcome data.

## 11. How to reproduce

1. Place the source Excel file where the Power Query source points, or update it in Data source settings.
2. Open the `.pbix`, Refresh, and compare the validation values in section 7.
3. Import the theme JSON if the report colours look different.
