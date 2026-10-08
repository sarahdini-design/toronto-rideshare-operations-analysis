# DAX Measures

This file contains the main DAX measures I created for the GTA Rideshare Operations Analysis.

These measures were used to calculate trip activity, passenger wait time, active vehicle count, trips per active vehicle-day, and the relationship between trips per active vehicle-day and wait time.

## Total Trips

Calculates the total number of completed trips in the selected filter context.

```DAX
Total Trips =
SUM(Trips_Q2_2026[trips_total])
```

## Reported Trips Started

Calculates the total number of reported completed trips started from the daily summary table.

```DAX
Reported Trips Started =
SUM(summary_stats[reported_trips_started])
```

## Weighted Avg Wait Time

Calculates average passenger wait time while weighting each grouped row by the number of trips it represents.
I used this instead of a simple average because the source trip data is already aggregated and different rows can represent different numbers of completed trips.

```DAX
Weighted Avg Wait Time (min) =
DIVIDE(
    SUMX(
        FILTER(
            Trips_Q2_2026,
            NOT ISBLANK(Trips_Q2_2026[waittime_avg])
        ),
        Trips_Q2_2026[waittime_avg] * Trips_Q2_2026[trips_total]
    ),
    SUMX(
        FILTER(
            Trips_Q2_2026,
            NOT ISBLANK(Trips_Q2_2026[waittime_avg])
        ),
        Trips_Q2_2026[trips_total]
    )
)
```

## Average Active Vehicles

Calculates the average number of active vehicles in the selected period.

```DAX
Average Active Vehicles =
AVERAGE(summary_stats[active_vehicles])
```

## Trips per Active Vehicle-Day

Calculates reported trips started per active vehicle-day in the selected filter context.

I use this as a simple vehicle activity/productivity measure. It does not measure how much time vehicles were actively engaged or available.

```DAX
Trips per Active Vehicle-Day =
DIVIDE(
    [Reported Trips Started],
    SUM(summary_stats[active_vehicles])
)
```

## Avg Daily Trips

Calculates average trip activity per day in the selected filter context.

```DAX
Avg Daily Trips =
DIVIDE(
    [Total Trips],
    DISTINCTCOUNT(DimDate[Date])
)
```

## Avg Trips per Hour

Calculates average trip volume for each pickup hour across the selected dates.

```DAX
Avg Trips per Hour =
DIVIDE(
    [Total Trips],
    DISTINCTCOUNT(Trips_Q2_2026[dt])
)
```

## Activity-Wait Correlation

Calculates the Pearson correlation between daily Trips per Active Vehicle-Day and Weighted Avg Wait Time. In the measure name, Activity refers specifically to Trips per Active Vehicle-Day.

```DAX
Activity-Wait Correlation (r) =
VAR __CORRELATION_TABLE = VALUES('DimDate'[Date])

VAR __COUNT =
    COUNTX(
        KEEPFILTERS(__CORRELATION_TABLE),
        CALCULATE([Trips per Active Vehicle-Day] * [Weighted Avg Wait Time (min)])
    )

VAR __SUM_X =
    SUMX(
        KEEPFILTERS(__CORRELATION_TABLE),
        CALCULATE([Trips per Active Vehicle-Day])
    )

VAR __SUM_Y =
    SUMX(
        KEEPFILTERS(__CORRELATION_TABLE),
        CALCULATE([Weighted Avg Wait Time (min)])
    )

VAR __SUM_XY =
    SUMX(
        KEEPFILTERS(__CORRELATION_TABLE),
        CALCULATE([Trips per Active Vehicle-Day] * [Weighted Avg Wait Time (min)] * 1.)
    )

VAR __SUM_X2 =
    SUMX(
        KEEPFILTERS(__CORRELATION_TABLE),
        CALCULATE([Trips per Active Vehicle-Day] ^ 2)
    )

VAR __SUM_Y2 =
    SUMX(
        KEEPFILTERS(__CORRELATION_TABLE),
        CALCULATE([Weighted Avg Wait Time (min)] ^ 2)
    )

RETURN
    DIVIDE(
        __COUNT * __SUM_XY - __SUM_X * __SUM_Y * 1.,
        SQRT(
            (__COUNT * __SUM_X2 - __SUM_X ^ 2)
                * (__COUNT * __SUM_Y2 - __SUM_Y ^ 2)
        )
    )
```

## Supporting Measure

### Simple Avg Wait Time

I created this measure as a basic comparison with the weighted wait-time calculation. It was not used as the main wait-time KPI in the final dashboard.

```DAX
Simple Avg Wait Time =
AVERAGE(Trips_Q2_2026[waittime_avg])
```

## Data Quality and Validation Measures

These measures were created to quantify data-quality checks used in the project documentation. They were not used as headline dashboard KPIs.


### Trips with Missing Wait Time

Counts the completed trips represented by rows where `waittime_avg` is blank.

```DAX
Trips with Missing Wait Time =
CALCULATE(
    [Total Trips],
    FILTER(
        Trips_Q2_2026,
        ISBLANK(Trips_Q2_2026[waittime_avg])
    )
)
```


### Missing Wait Time Trip Share

Calculates the share of total Q2 trip volume represented by records with missing wait time.

```DAX
Missing Wait Time Trip Share =
DIVIDE(
    [Trips with Missing Wait Time],
    [Total Trips]
)
```


### Toronto Pickup Trips

Calculates completed trips with a pickup municipality of Toronto.

```DAX
Toronto Pickup Trips =
CALCULATE(
    [Total Trips],
    Trips_Q2_2026[pickup_municipality] = "Toronto"
)
```


### Toronto Not Included Elsewhere Trips

Calculates Toronto pickup trips where ward-level detail was published as `Not included elsewhere`.

```DAX
Toronto Not Included Elsewhere Trips =
CALCULATE(
    [Total Trips],
    Trips_Q2_2026[pickup_municipality] = "Toronto",
    Trips_Q2_2026[pickup_ward] = "Not included elsewhere"
)
```


### Toronto Not Included Elsewhere Share

Calculates the share of Toronto pickup trips without usable ward-level detail.

```DAX
Toronto Not Included Elsewhere Share =
DIVIDE(
    [Toronto Not Included Elsewhere Trips],
    [Toronto Pickup Trips]
)
```

### Validation Results

| Check | Q2 2026 Result |
|---|---:|
| Trips with Missing Wait Time | **948 trips** |
| Missing Wait Time Trip Share | **0.0038%** |
| Toronto Pickup Trips | **22,899,091 trips** |
| Toronto Not Included Elsewhere Trips | **1,375,889 trips** |
| Toronto Not Included Elsewhere Share | **6.01%** |


