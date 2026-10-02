# Data Dictionary

This file documents the main tables and fields used in the Toronto Rideshare Operations Analysis.

The project uses two source datasets from the City of Toronto and one date table created in Power BI.

## Tables

| Table | Type | Description |
|---|---|---|
| `Trips_Q2_2026` | Source / transformed table | Combined trip data for April, May, and June 2026. The monthly files were combined in Power Query. |
| `summary_stats` | Source table | Daily operational summary data, including reported trips started and active vehicles. |
| `DimDate` | Created table | Date table created for time-based analysis and consistent filtering across the model. |

## Trips_Q2_2026

The source trip data is already aggregated by hour and location. One row can represent multiple completed trips.

| Field | Source / Created | Type | Description |
|---|---|---|---|
| `dt` | Source | Date | Date of passenger pickup. |
| `pickup_hr` | Source | Date/Time | Date and hour of passenger pickup. |
| `pickup_municipality` | Source | Text | Municipality at the passenger pickup point. |
| `pickup_ward` | Source | Text | Toronto ward at the passenger pickup point. |
| `trips_total` | Source | Whole Number | Number of completed trips represented by the row. |
| `waittime_avg` | Source | Decimal Number | Average passenger wait time in minutes for the grouped trips. |
| `Pickup Hour` | Created | Whole Number | Hour extracted from `pickup_hr` for hourly demand analysis. |
| `Ward Filter` | Created | Text | Used to separate records with valid ward detail from records labelled `Not included elsewhere`. |

### Created Columns

#### Pickup Hour

I created this column so I could compare trip activity by hour of day.

```DAX
Pickup Hour =
HOUR(Trips_Q2_2026[pickup_hr])
