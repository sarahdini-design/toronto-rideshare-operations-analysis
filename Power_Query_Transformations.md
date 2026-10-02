# Power Query Transformations

This file documents the main data preparation steps I used in Power Query for the Toronto Rideshare Operations Analysis.

I kept the transformation process relatively simple because the source data was already structured and published in an analysis-ready format by the City of Toronto.

The main goal was to combine the monthly files, check the structure of the data, and prepare the fields for use in the Power BI model.

---

## Trip Data

The trip data was provided in separate monthly files.

For this project, I used:

- **April 2026**
- **May 2026**
- **June 2026**

I imported the three monthly files into Power Query and combined them into one table:

`Trips_Q2_2026`

This created one dataset covering the full **Q2 2026** analysis period.

---

## Combining the Monthly Files

The three monthly trip files followed the same structure, so I combined them into a single table using Power Query's Combine Files process.

The combined table allowed the same calculations and visuals to be used across the full quarter instead of working with each month separately.

The final analysis period covered:

**April 1, 2026 through June 30, 2026**

This represents **91 calendar days**.

---

## Data Type Preparation

After combining the monthly files, I reviewed and standardized the data types used in the analysis.

In Power Query, the main retained fields were assigned the following data types:

| Field | Power Query Type | Purpose |
|---|---|---|
| `dt` | Date | Keeps only the calendar date for daily analysis and model relationships. |
| `pickup_hr` | Date/Time/Timezone | Preserves the pickup timestamp and timezone information for hourly analysis. |
| `pickup_municipality` | Text | Geographic filtering and grouping. |
| `pickup_community_council` | Text | Geographic reference field. |
| `pickup_ward` | Text | Ward-level analysis. |
| `trips_total` | Whole Number | Completed trip volume. |
| `fare_avg` | Decimal Number | Average fare value from the source data. |
| `distance_avg` | Decimal Number | Average trip distance from the source data. |
| `waittime_avg` | Decimal Number | Average passenger wait time. |
| `duration_avg` | Decimal Number | Average trip duration. |

The `dt` field was converted to a date-only field for daily analysis and model relationships as **Date**, while `pickup_hr` was kept as **Date/Time/Timezone** so the pickup hour could still be derived later.

## Data Quality Checks

Before building the dashboard, I reviewed the combined data for basic quality issues.

I checked:

- the date range;
- column structure across the three monthly files;
- duplicate records;
- missing values in fields used in the analysis;
- field data types.

No duplicate rows were found in the combined trip dataset.

The data covered all **91 days of Q2 2026**, which confirmed that the three monthly files had been combined correctly.

---

## Daily Summary Data

The City of Toronto also provides a separate daily summary dataset.

I loaded this table separately and kept it at its original **daily grain**.

The main fields used from this table were:

- `dt`
- `reported_trips_started`
- `active_vehicles`

This table was later used to compare daily trip activity with vehicle availability.

Unlike the trip table, I did not combine this data with the trip records in Power Query because the two datasets have different levels of detail.

Instead, both tables were connected through the `DimDate` table in the Power BI data model.

---

## Why the Tables Were Kept Separate

The two source tables represent different levels of detail:

| Table | Grain |
|---|---|
| `Trips_Q2_2026` | Aggregated by time and location |
| `summary_stats` | One daily operational summary |

I kept them as separate tables instead of joining them directly.

This avoided repeating daily summary values across many trip-level groups and allowed each table to keep its original structure.

The tables were connected later through the shared date dimension.

---

## What I Did Not Change in Power Query

I tried to keep the original City of Toronto data as close to the published structure as possible.

I did not use Power Query to create the main analytical KPIs.

Measures such as:

- **Total Trips**
- **Weighted Avg Wait Time**
- **Average Active Vehicles**
- **Trips per Active Vehicle-Day**
- **Avg Daily Trips**
- **Reported Trips Started**
- **Avg Trips per Hour**
- **Utilization-Wait Correlation**

were created later using DAX.

The DAX calculations are documented in:

[`DAX_Measures.md`](./DAX_Measures.md)

---

## Additional Model Calculations

Some fields needed for the dashboard were also created after the Power Query stage.

These included:

- `DimDate`
- `Pickup Hour`
- `Ward Filter`

These calculations are documented separately because they were created in the Power BI model rather than in Power Query.

See:

[`Model_Calculations.md`](./Model_Calculations.md)

---

## Final Data Preparation Flow

The preparation process for the project was:

**City of Toronto source files**

↓

**Import into Power Query**

↓

**Combine April, May, and June trip files**

↓

**Check field structure and data types**

↓

**Validate dates, duplicates, and fields used in the analysis**

↓

**Load trip and daily summary tables separately**

↓

**Create the Power BI data model**

↓

**Add calculated tables, columns, and DAX measures**

↓

**Build the dashboard and analytical visuals**

---

## Why I Kept the Transformation Process Simple

The source files were already structured and aggregated by the City of Toronto.

Because of this, I did not want to make unnecessary changes to the source data.

Most of the analytical work in this project happened after the data preparation stage through:

- data modeling;
- calculated columns;
- DAX measures;
- filtering;
- and dashboard analysis.

Power Query was mainly used to bring the monthly data together and prepare a consistent dataset for the model.
