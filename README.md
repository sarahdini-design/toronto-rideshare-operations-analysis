# Toronto Rideshare Operations Analysis

An analysis of Toronto rideshare activity during Q2 2026, focused on trip demand, vehicle availability, passenger wait times, and vehicle utilization.

**Tools:** Power BI · Power Query · DAX · GitHub


## Project Overview

This project looks at Toronto rideshare operations during **Q2 2026**, covering **April, May, and June**.

When I first reviewed the data, I wanted to understand more than just how many trips were being completed. I was interested in how **trip demand**, **vehicle availability**, and **passenger wait time** changed together, and whether higher vehicle utilization was connected with longer waits.

I also wanted to see where and when rideshare activity was concentrated across Toronto. This led me to look at patterns by **date**, **day of week**, **pickup hour**, and **pickup ward**.

The main goal of the project was to build a clearer picture of how demand, supply, and service performance interacted during the quarter.


## Data Sources

This project uses publicly available rideshare data from the **City of Toronto Open Data Portal**:

- [Private Transportation Companies – Summary and Trip Data](https://open.toronto.ca/dataset/private-transportation-companies-summary-and-trip-data)

The analysis covers **April, May, and June 2026**.

I used two parts of the dataset:

- monthly trip data, which includes fields for date, pickup hour, pickup municipality, pickup ward, trip counts, and average wait time;
- daily summary data, which includes operational measures such as **reported trips started** and **active vehicles**.

I combined the three monthly trip files into one **Q2 2026** dataset in Power Query.

Using the trip data together with the daily summary data allowed me to look at both trip activity and vehicle availability during the quarter.


## Power BI Dashboard

I built the report in **Power BI** with two main pages:

- **Operations Dashboard** — a high-level view of trip volume, wait time, active vehicles, vehicle utilization, day-of-week patterns, and pickup locations.
- **Operational Insights** — a deeper look at the relationship between **vehicle utilization and wait time**, changes in **trip demand and active vehicles over time**, and wait-time differences across Toronto pickup wards.

I also added **Month** and **Day Type** slicers so the results can be compared across **April, May, and June 2026** and between **weekdays and weekends**.
![Toronto Rideshare Operations Dashboard](images/operations-dashboard.png)


## Main Analytical Question

How did **trip demand**, **vehicle availability**, **passenger wait time**, and **vehicle utilization** interact across Toronto rideshare operations during **Q2 2026**?
