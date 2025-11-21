---
title: Snowflake + Power BI: Tasty Bytes Analytics
---

# Tasty Bytes – End-to-End Analytics with Snowflake & Power BI

This project takes the **Tasty Bytes** food-truck dataset from Snowflake Marketplace and turns it into an end-to-end analytics solution:

- Data profiling and exploration in Snowflake  
- Building **dynamic tables** for dimensions and fact tables  
- Designing a **star schema** and exposing it to Power BI  
- Creating interactive dashboards for revenue, orders, and city-level performance  

It’s the kind of work I’d do as a data analyst or analytics engineer: understand the business, model the data properly, and then tell the story with visuals.

---

## Quick Overview

- **Role:** Data Analyst / Analytics Engineer  
- **Stack:** Snowflake, Dynamic Tables, SQL, Power BI  
- **Grain:** Order line and aggregated orders (DT_FACT_ORDER_DETAIL, DT_FACT_ORDER_HEADER, DT_FACT_ORDER_AGG)  
- **Dimensions:** Customer, Truck, Location, Date, Time, Franchise, Menu Item  

---

## Architecture

This is the high-level flow from raw marketplace data to analytics:

![Snowflake lineage](assets/diagrams/snowflake_lineage.png)

1. Raw marketplace tables (FRANCHISE, TRUCK, LOCATION, MENU, CUSTOMER_LOYALTY, ORDER_HEADER, ORDER_DETAIL).  
2. Dynamic tables to build dimensions:  
   - `DT_DIM_CUSTOMER`, `DT_DIM_TRUCK`, `DT_DIM_LOCATION`, `DT_DIM_FRANCHISE`, `DT_DIM_MENU_ITEM`, `DT_DIM_DATE`, `DT_DIM_TIME`  
3. Dynamic fact tables:  
   - `DT_FACT_ORDER_DETAIL`, `DT_FACT_ORDER_HEADER`, and the aggregated fact `DT_FACT_ORDER_AGG`  
4. Power BI connects to the **PowerBI schema** and uses the star schema for reports.

👉 [See ETL & pipeline details](pipelines/etl_pipeline.md)

---

## Data Model

Here’s the analytical model exposed to Power BI:

![Star schema](assets/diagrams/powerbi_star_schema.png)

At the centre is `DT_FACT_ORDER_AGG`, surrounded by:

- **dim_date**
- **dim_time**
- **dim_customer**
- **dim_truck**
- **dim_location**
- **dim_franchise**

Each dimension is cleaned and conformed so that analysts can slice measures by city, region, truck, franchise, time, and customer segments.

👉 [Full fact / dimension breakdown](data-model/fact_and_dimensions.md)

---

## Snowflake & SQL Work

All modelling is done in Snowflake using SQL and dynamic tables.

Key pieces:

- Marketplace data ingestion  
- Data profiling queries  
- Star schema creation (dimensions + facts)  
- Data-governance layer (access patterns, schemas, and roles)

👉 **Browse full SQL folder:**  
[View all Snowflake SQL scripts](sql/)

👉 **Direct links to individual SQL files:**  
- [1-data-profiling.sql](sql/1-data-profiling.sql)  
- [2-marketplace-data.sql](sql/2-marketplace-data.sql)  
- [3-create-star-schema.sql](sql/3-create-star-schema.sql)  
- [4-data-governance.sql](sql/4-data-governance.sql)  
- [tasty-bytes-intro.sql](sql/tasty-bytes-intro.sql)

---

## Dashboards & Insights

This Power BI **Order Summary** dashboard sits on top of the `DT_FACT_ORDER_AGG` fact table and the conformed dimensions.

![Order Summary Dashboard](assets/screenshots/powerbi_dashboard.jpg)

From this visual you can quickly see:

- How **sales distribution** shifts across global regions (APAC, EMEA, North America)
- The **top cities** and how much they contribute to total order volume
- Which **brands** dominate revenue and how they compare side-by-side
- Weekly demand patterns, showing which **days of the week** have the highest activity
- Clear **hour-of-day peaks**, highlighting lunch and evening rush periods
- Whether demand is spread across time or concentrated in specific peak windows