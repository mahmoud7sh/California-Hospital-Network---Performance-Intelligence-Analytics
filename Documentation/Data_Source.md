# Data Source

## Origin

- **Dataset:** California hospital annual utilization data, published by the California Department of Health Care Access and Information (HCAI).
- **Reporting year:** calendar year 2025 (operating periods in the source run from 1 January to 31 December 2025; the latest equipment acquisition date is early February 2026).
- **Coverage:** 501 licensed hospitals in 55 counties.
- **Downloaded as:** one wide Excel table, 503 rows (two hospitals appear twice because the operator changed during the year).
- **Source link:** *[add the exact HCAI download URL and the download date here]*
- **License / terms of use:** *[add the license or terms statement from the HCAI page here]*. The data is public; this repository contains the cleaned tables and the analysis, not a claim of ownership of the data.

## What the source contains

| Area | Fields (examples) |
|---|---|
| Facility | number, name, address, county, coordinates, license status, facility level, ownership type, trauma / teaching / rural designation, principal service type |
| Capacity and use | licensed beds, licensed bed-days, census days, discharges, by service line |
| Emergency | ED traffic, visits by acuity (admitted and not admitted), left without being seen, treatment stations, diversion hours (monthly and annual) |
| Procedures | surgeries and operating rooms, cardiac catheterization and cardiovascular procedures |
| Programs | acute psychiatric patients by unit and payer, hospice and palliative care |
| Services | 24-hour or on-call availability of seven services |
| Investment | equipment items above $500K, capital projects above $1M |

## Source characteristics to know

- **One row per hospital** (per operating period), one reporting year.
- **Threshold reporting:** equipment and project amounts are only reported above $500K and $1M respectively.
- **Blank cells** can mean "not applicable", "zero" or "not reported" depending on the field; see `Methodology.md` for the rules applied.
- **Total and subtotal columns** are present next to their components and are not additive.
- **IC columns** are named "IC" in the source; their exact meaning was not confirmed.
- **Source ALOS columns** hold 0 for hospitals with no data and were not used.

## Files in this repository

| Folder | Content |
|---|---|
| `Source/` | original download, untouched |
| `Raw_Data/` | tables split from the source, before cleaning |
| `Clean_Data/` | cleaned tables as loaded into the model (CSV) |
| `Documentation/` | methodology, data dictionary, DAX measures, walkthrough |

## Refreshing the data

1. Download the new annual file from the HCAI page.
2. Point the Power Query source to the new file (Data source settings).
3. Refresh and compare the validation values in `Methodology.md` section 6 (501 hospitals expected only if the hospital list is unchanged).
