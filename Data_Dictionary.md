# Data Dictionary

This file documents the main tables and fields used in the Toronto Rideshare Operations Analysis.

The project uses two source datasets from the **City of Toronto** and one date table created in **Power BI**.

This dictionary focuses on the fields that were used in the analysis and dashboard rather than listing every field available in the original source files.

---

## Tables

| Table | Type | Description |
|---|---|---|
| `Trips_Q2_2026` | Source / Transformed | Trip data for **April, May, and June 2026**, combined into one Q2 table in Power Query. |
| `summary_stats` | Source | Daily operational summary data, including reported trips started and active vehicles. |
| `DimDate` | Created | Date table created in Power BI for time-based analysis, sorting, and consistent filtering across the model. |

---

## Trips_Q2_2026

The original City of Toronto trip data is already **aggregated by hour and location**.

This means one row does not represent one individual ride. A row can represent multiple completed trips with the same time and location grouping.

### Source Fields Used

| Field | Source / Created | Data Type | Description | Used For |
|---|---|---|---|---|
| `dt` | Source | Date | Date of passenger pickup. | Daily and quarterly trip analysis. |
| `pickup_hr` | Source | Date/Time | Date and hour of passenger pickup. | Creating the hourly demand view. |
| `pickup_municipality` | Source | Text | Municipality at the passenger pickup point. | Keeping the geographic analysis focused on Toronto. |
| `pickup_ward` | Source | Text | Toronto ward at the passenger pickup point when ward-level detail is available. | Comparing trip volume and wait time across Toronto wards. |
| `trips_total` | Source | Whole Number | Number of completed trips represented by the grouped row. | Total Trips and weighted wait-time calculations. |
| `waittime_avg` | Source | Decimal Number | Average passenger wait time in minutes for the trips represented by the row. | Passenger wait-time analysis. |

---

## Created Columns in Trips_Q2_2026

I created a small number of additional columns to make the source data easier to analyze in Power BI.

### Pickup Hour

I created `Pickup Hour` to extract the hour of day from the original pickup timestamp.

This allowed me to compare hourly demand patterns and build the **Weekday vs Weekend** hourly demand visual.

```DAX
Pickup Hour =
HOUR(Trips_Q2_2026[pickup_hr])
```

| Field | Source / Created | Data Type | Description |
|---|---|---|---|
| `Pickup Hour` | Created | Whole Number | Hour of passenger pickup, from 0 to 23. |

---

### Ward Filter

The City of Toronto documentation explains that some records are not published with ward-level detail because of privacy rules.

These records appear in `pickup_ward` as **Not included elsewhere**.

I created `Ward Filter` so these records could be excluded when comparing individual Toronto wards.

```DAX
Ward Filter =
IF(
    Trips_Q2_2026[pickup_ward] = "Not included elsewhere",
    "Exclude",
    "Include"
)
```

| Field | Source / Created | Data Type | Description |
|---|---|---|---|
| `Ward Filter` | Created | Text | Labels records as `Include` or `Exclude` for ward-level analysis. |

The excluded records were not removed from the overall trip analysis. They were excluded only where valid ward-level detail was required.

---

## summary_stats

The `summary_stats` table contains daily operational measures published by the City of Toronto.

Unlike the trip table, which is grouped by hour and location, this table is reported at the **daily level**.

### Source Fields Used

| Field | Source / Created | Data Type | Description | Used For |
|---|---|---|---|---|
| `dt` | Source | Date | Date of the daily summary record. | Connecting daily operational data to the date table. |
| `reported_trips_started` | Source | Whole Number | Number of completed trips that started on the specified date. | Daily trip-demand and vehicle-supply comparisons. |
| `active_vehicles` | Source | Whole Number | Number of unique vehicles active on a PTC platform during the day. | Vehicle availability and utilization analysis. |

The source table contains additional operational fields, but only the fields used directly in this project are documented here.

---

## DimDate

I created a separate `DimDate` table in Power BI so both source tables could use the same date structure.

This made it possible to use consistent filters for **month**, **day of week**, and **weekday/weekend** across the report.

### Date Fields

| Field | Source / Created | Description | Used For |
|---|---|---|---|
| `Date` | Created | Calendar date used to connect the model tables. | Relationships, filtering, and daily analysis. |
| `Year` | Created | Calendar year. | Date organization and filtering. |
| `Month` | Created | Month name. | Month slicer and monthly comparisons. |
| `Month Number` | Created | Numeric month value. | Sorting month names in calendar order. |
| `Day Name` | Created | Name of the day of week. | Day-of-week demand and wait-time analysis. |
| `Day Number` | Created | Numeric day-of-week value. | Sorting day names in the correct order. |
| `Day Type` | Created | Groups dates into `Weekday` or `Weekend`. | Comparing weekday and weekend demand patterns. |

---

## Why I Used a Separate Date Table

Using a separate date table gave me one consistent calendar structure across the report.

Instead of creating separate date logic inside each source table, I could use the same fields for:

- **Month**
- **Day of Week**
- **Weekday vs Weekend**
- **Daily trend analysis**

This also allowed the same slicers to work across visuals built from both `Trips_Q2_2026` and `summary_stats`.

---

## Measures

The calculated measures used in the dashboard are documented separately in:

[`DAX_Measures.md`](./DAX_Measures.md)

The main measures include:

- **Total Trips**
- **Reported Trips Started**
- **Weighted Avg Wait Time**
- **Average Active Vehicles**
- **Trips per Active Vehicle-Day**
- **Avg Daily Trips**
- **Avg Trips per Hour**
- **Utilization-Wait Correlation**

Keeping the measures in a separate file makes it easier to see the calculation logic without mixing it with the source-field documentation.

---

## Data Notes

A few details from the City of Toronto technical documentation were important when working with the data:

- Trip data is already **aggregated by hour and pickup/drop-off location**.
- Some records do not include ward-level detail because of privacy rules and appear as **Not included elsewhere**.
- `waittime_avg` is an average for each grouped row, so I created a **weighted average wait-time measure** using `trips_total`.
- `active_vehicles` is available at the **daily level**, so vehicle availability could be compared by date but not directly by ward or hour.
- The City notes that cancellation data from **January 2026 onward** may have been affected by a methodological change. Cancellation measures were therefore not used in the main analysis.