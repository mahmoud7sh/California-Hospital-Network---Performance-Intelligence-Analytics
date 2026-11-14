# California-Hospital-Network---Performance-Intelligence Analytics

## Dashboard Preview

![Executive Overview](<img width="1522" height="880" alt="01_overview" src="https://github.com/user-attachments/assets/c367bd45-edcc-4c6d-b22f-dccce0ed01ed" />


[View the complete 11-page dashboard walkthrough](Report/California Hospital Network - Performance Intelligence.pdf)
An 11-page Power BI report built on California hospital utilization data (HCAI annual report). It answers one question: **where is pressure building in the hospital network, and what should be read with care?**

> One reporting year, 501 hospitals, 55 counties. All findings are descriptive; none implies causation or clinical quality.

## Headline numbers

| Metric | Value |
|---|---|
| Hospitals | 501 |
| Licensed beds | 95,759 |
| Occupancy (census days / licensed bed days) | 64.5% |
| Discharges | 3.45 million |
| Emergency department visits | 15.55 million |
| Left without being seen (LWBS) | 1.95% |
| Ambulance diversion hours | 125,631 (145 hospitals) |

## Business questions

1. Which hospitals and counties operate near or above licensed capacity?
2. Where is emergency demand concentrated, and who leaves unseen?
3. Which hospitals combine high occupancy with ambulance diversion?
4. How is the network structured (ownership, size, service type)?
5. What critical-care, surgical, cardiac and service-readiness capacity exists?
6. Where are hospitals investing in equipment and capital projects?

## Key findings (descriptive)

- **Capacity:** 85 hospitals run at 85% occupancy or more; **10 report occupancy above 100%** of licensed bed-days (kept and flagged, not removed).
- **Pressure is concentrated:** only **10 hospitals** combine occupancy of 85% or more with ambulance diversion. Los Angeles County alone holds about 60% of all diversion hours, and January is the peak month (23,177 hours).
- **Occupancy and diversion do not always coincide:** Sacramento runs near 79% occupancy with almost no diversion.
- **Length of stay needs context:** the all-hospital average is 6.5 days, but long-stay state psychiatric, long-term-care and developmental facilities (27 hospitals above 60 days; one reaches about 3,227 days) pull it up. The acute-care view is **5.6 days**.
- **Emergency acuity:** 60.4% of classified ED visits are severe or critical (admitted and not admitted visits combined).
- **Service readiness:** 24-hour laboratory coverage is 91% among hospitals that reported, but only 44.5% for 24-hour operating rooms; 155 hospitals reported no service availability at all.
- **Investment:** reported equipment above $500K totals $400.7M (almost all acquired in 2025); reported projects above $1M total $5.87B, and two hospitals (Kaiser and Providence Saint Joseph) account for more than half of it.

## Data and model

- **Source:** California hospital utilization data (HCAI), delivered as one wide table.
- **Model:** star schema. `Dim_Hospital` (501 rows, key `FAC_NO`) in the centre; 13 fact tables on the many side (bed capacity, bed days, discharges, ED, ED outcomes, monthly ED diversion, IC, surgery, cardiovascular, clinical programs, facility services, equipment, projects); relationships are many-to-one, single direction.
- **Helper tables:** `Dim_ServiceLine`, `Dim_Service`, four What-if parameter tables for the Pressure Index.
- **Measures:** about 100 DAX measures in a `_Measures` table, organised in display folders (capacity, emergency, diversion, surgery, cardiology, services, investment, benchmarking, data quality, formatting).

## Data preparation decisions

- Split the wide table into one dimension and 13 fact tables; cleaned in Power Query.
- Two facilities changed operator mid-year and appeared twice; their periods were combined, not dropped.
- Wide equipment and project slots were unpivoted into one row per item.
- Blank monthly diversion values for hospitals with an ED are treated as zero hours (the source cannot separate them from reported zeros).
- ALOS is derived (census days / discharges) instead of using source columns that contained zeros for "no data".
- "Not Reported" availability is never treated as "No".
- Total and component columns are never added together (the itemised general-acute-care beds cover only part of the subtotal).

## Pressure Index (analyst-defined, not a clinical standard)

`100 x (0.5 x occupancy capped at 100% + 0.3 x diversion hours + 0.2 x LWBS rate)`, with diversion and LWBS scaled to the highest hospital in the current selection. Weights and the 85% threshold are adjustable What-if parameters. Because of the scaling, scores are relative to the selection.

## Report pages

01 Overview, 02 Network, 03 Capacity, 04 Patient Flow, 05 Emergency, 06 Pressure, 07 IC & Surgery, 08 Cardio & Clinical, 09 Investment, 10 Services, 11 Methodology & Data Quality.

## Limitations

- One reporting year: no trends or year-over-year comparison (only diversion has a monthly grain).
- IC columns are labelled as in the source; their exact meaning is unconfirmed.
- Equipment above $500K and projects above $1M only; these are threshold-reported amounts, not total capital spend.
- No population, financial or quality-outcome data, so findings describe operations, not performance quality.

## Tools

Power BI Desktop, Power Query , DAX, Python (pandas) for preparation and validation ,AI TOOLS .

```
README.md
```


