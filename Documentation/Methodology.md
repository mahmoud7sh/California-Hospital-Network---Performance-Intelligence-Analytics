# Methodology

How the metrics are defined, how the data was treated, and how the results should be read.

## 1. Objective and analytical framework

The report answers one question: **where is pressure building in California's hospital network, and what should be read with care?** Each page answers a narrower question and follows the same logic: network size, capacity, utilisation, patient demand, operational pressure.

| Page | Question |
|---|---|
| 01 Overview | Where is pressure building in the network? |
| 02 Network | Who operates the hospitals, and where? |
| 03 Capacity | Which hospitals operate near or above licensed capacity? |
| 04 Patient Flow | How many patients flow through, and how long do they stay? |
| 05 Emergency | Where is emergency demand concentrated, and who leaves unseen? |
| 06 Pressure | Which hospitals combine high occupancy with ambulance diversion? |
| 07 IC & Surgery | What critical-care and surgical capacity exists? |
| 08 Cardio & Clinical | Where are advanced cardiac and specialised services available? |
| 09 Investment | Where are hospitals investing in equipment and capital projects? |
| 10 Services | Which hospitals are ready 24 hours a day? |
| 11 Methodology | How reliable are these numbers? |

## 2. Metric definitions

| Metric | Definition | Reading notes |
|---|---|---|
| Occupancy % | census days / licensed bed days | Aggregates are total census days over total bed-days, not an average of hospital percentages |
| ALOS | census days / discharges | Includes long-stay facilities; `ALOS (Acute Care)` excludes psychiatric, long-term care and developmental hospitals |
| Beds Turnover | discharges / licensed beds | |
| LWBS % | patients who left without being seen / ED visits | |
| ED Admission % | patients admitted from the ED / ED visits | |
| High-Acuity % | (severe + critical visits) / all classified visits, admitted and not admitted | Classified visits slightly under-count total ED visits (about 0.5%) |
| Outpatient Surgery % | outpatient operations / all operations | |
| Surgeries per OR | operations / operating rooms | |
| 24H service % | hospitals with 24-hour availability / hospitals that reported any service | "Not Reported" is excluded from the denominator and never counted as "No" |
| Investment per Bed | (reported equipment value + reported project spend) / licensed beds | Threshold-reported amounts only |

## 3. Pressure Index

An analyst-defined prioritisation score, **not** a clinical or safety rating.

`Pressure Index = 100 x (0.5 x occupancy + 0.3 x diversion hours + 0.2 x LWBS rate)`

- Occupancy is capped at 100%.
- Diversion hours and LWBS rate are divided by the highest value among the hospitals left by the slicers, so each component is between 0 and 1 and the score between 0 and 100.
- Weights and the 85% threshold of `High-Pressure Hospitals` are What-if parameters, so the reader can test other assumptions.
- Because of the scaling the score is **relative to the current selection**. Hospital-detail pages use `Pressure Index (State Scale)`, which scales against all hospitals.
- `High-Pressure Hospitals` counts hospitals with occupancy at or above the threshold and at least one diversion hour.

## 4. Benchmarks

State and peer benchmarks are shown as differences in percentage points. Peers are hospitals with the same principal service type. Rankings use `Pressure Rank` or `Occupancy Rank` as visual filters; Top N filters are avoided because they change the scale of relative measures.

## 5. Data-handling rules

1. A hospital that appears twice because the operator changed mid-year is one hospital: dimension row of the active operator, fact periods combined.
2. Blank is not always zero. ED outcome counts are zero for hospitals that reported an ED; hospitals with no data remain blank.
3. Blank monthly diversion values for ED hospitals are treated as zero hours. The source does not allow separating them from reported zeros; 1,781 of the 3,912 hospital-months (46%) were converted this way, and the annual total (125,631 hours) is unaffected.
4. "Not Reported" is a category of its own for ownership, teaching, rural and service availability.
5. Total and component columns are never added. The seven itemised general-acute-care lines cover only part of the subtotal, so a derived "not itemised" row makes service lines add to 95,759 beds and 3,452,872 discharges.
6. Hospitals above 100% occupancy (10) are kept and flagged; possible causes include temporary beds and reporting differences.
7. ALOS source columns were not used because they stored 0 for "no data".

## 6. Validation

Measures were validated with DAX queries against the live model and compared with totals computed independently from the source in Python: 501 hospitals, 95,759 beds, 22,484,296 census days, 3,452,872 discharges, 15,551,325 ED visits, 125,631 diversion hours, 145 diverting hospitals. Reconciliation checks: service-line rows equal totals; each service availability row sums to 501; the monthly diversion table equals the annual total.

## 7. Limitations and interpretation

- **One reporting year.** No trends or year-over-year comparison; only diversion has a monthly grain.
- **Descriptive only.** Patterns are not causal and no hospital is classified as unsafe from an operational indicator.
- **Service mix.** Differences in ALOS and occupancy between ownership groups mostly reflect service mix (for example state psychiatric hospitals with stays of years).
- **IC columns** are labelled as in the source; their exact meaning is not confirmed.
- **Threshold-reported investment.** Equipment above $500K and projects above $1M only; two hospitals account for more than half of reported project spend.
- **No population, finance or outcome data.** The report describes operations, not quality of care.
