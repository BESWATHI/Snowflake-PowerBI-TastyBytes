# Data Model: Facts and Dimensions

This page documents the analytical model that Power BI uses on top of Snowflake.

At a high level, the model follows a classic **star schema**:

- One central **fact table**: `DT_FACT_ORDER_AGG`
- Several **conformed dimensions**: Date, Time, Customer, Location, Truck, Franchise (plus Menu Item in the detailed model)

---

## Grain

There are two main grains in this project:

1. **Order line level** – stored in `DT_FACT_ORDER_DETAIL`  
2. **Aggregated order level** – stored in `DT_FACT_ORDER_AGG`  

Power BI mostly uses `DT_FACT_ORDER_AGG` for performance and simplicity.

---

## Fact: DT_FACT_ORDER_AGG

**Grain:** One row per combination of `customer_id`, `truck_id`, `date_id`, `time_id`, `location_id`, `franchise_id`.

**Key columns (example):**

- `customer_id`
- `truck_id`
- `location_id`
- `date_id`
- `time_id`
- `franchise_id`

**Measures:**

- `order_count` – number of orders  
- `order_line_count` – total order lines  
- `order_qty` – total quantity ordered  
- `order_total` – revenue amount

This table is built from `DT_FACT_ORDER_HEADER` and `DT_FACT_ORDER_DETAIL`, aggregating to the level that aligns with dashboard needs.

---

## Dimensions

### 1. dim_date (`DT_DIM_DATE`)

Represents the calendar date.

Typical columns:

- `date_id` (surrogate key)
- `date`
- `day_of_month`
- `day_of_week`
- `day_of_year`
- `month`
- `month_name`
- `week_of_year`
- `weekday` (Y/N or name)

Used to analyse trends over time, seasonality, and weekday/weekend patterns.

---

### 2. dim_time (`DT_DIM_TIME`)

Describes the time of day.

Typical columns:

- `time_id`
- `time`
- `am_or_pm`
- `hour`
- `hour_am_pm`
- `minute`
- `second`

Useful for seeing which hours drive the most orders (e.g., lunch vs dinner rush).

---

### 3. dim_customer (`DT_DIM_CUSTOMER`)

Represents individual Tasty Bytes customers.

Example attributes (based on the model):

- `customer_id`
- `customer_city`
- `customer_country`
- `customer_dob`
- `customer_email`
- `customer_children_count`
- `customer_favorite_band`
- `customer_first_name`
- `customer_full_name`
- `customer_gender`

Lets the business examine demand by customer demographics and location.

---

### 4. dim_truck (`DT_DIM_TRUCK`)

Represents food trucks in the Tasty Bytes fleet.

Example attributes:

- `truck_id`
- `franchise_id`
- `ev_flag` (electric vehicle flag)
- `franchise_flag`
- `truck_age`
- `truck_brand_name`
- `truck_city`
- `truck_country`
- `truck_country_iso`

Supports analysis of performance by truck type, geography, and sustainability (EV vs non-EV).

---

### 5. dim_location (`DT_DIM_LOCATION`)

Represents physical locations where trucks operate.

Example attributes:

- `location_id`
- `location`
- `location_city`
- `location_country`
- `location_country_iso`
- `location_region`

Used to slice by city, region, and country.

---

### 6. dim_franchise (`DT_DIM_FRANCHISE`)

Represents franchise entities in the Tasty Bytes network.

Example attributes:

- `franchise_id`
- `franchise_city`
- `franchise_country`
- `franchise_email`
- `franchise_first_name`
- `franchise_last_name`
- `franchise_phone_number`

Useful for comparing franchise performance and monitoring multi-location operators.

---

### 7. dim_menu_item (`DT_DIM_MENU_ITEM`)

Used in the detailed model and in `DT_FACT_ORDER_DETAIL`.

Example attributes:

- `menu_item_id`
- `menu_item_name`
- `menu_item_category`
- `ingredients`
- `cogs_usd`
- `is_healthy_flag`
- `is_dairy_free_flag`
- `is_gluten_free_flag`
- `is_nut_free_flag`

Allows product-level analysis: which items sell best, and how dietary labels affect demand.

---

## Power BI Model View

High-level star schema:

![Power BI star schema](../assets/diagrams/powerbi_star_schema.jpg)

Extended model with order detail and menu items:

![Power BI full model](../assets/diagrams/powerbi_full_model.jpg)

The key design decision here was to keep **one clean, aggregated fact** for most dashboards, while preserving more detailed tables for deeper analysis when needed.
