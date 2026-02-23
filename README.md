# European Airline Routes Big Data Analytics

This repository contains my complete end-to-end Big Data project built on **Databricks**, using a real-world style **European air routes dataset**.  
The workflow demonstrates the core components of a modern data pipeline built using Databricks and Delta Lake. 
✔ Data ingestion  
✔ Cleaning and feature engineering  
✔ Delta Lake storage  
✔ Notebook analysis & data visualisations  
✔ SQL analysis  
✔ SQL dashboard creation  
✔ Machine learning model  

This README explains every step in detail so the entire solution is transparent and reproducible.

---

# 1. Repository Contents

| File | Description |
|------|-------------|
| **europe_air_routes.csv** | Raw dataset containing European flight routes, prices, schedules, airport details, and geolocation data. |
| **EU_air_routes_Analysis.ipynb** | ETL notebook performing ingestion, cleaning, type handling, and advanced feature engineering (distance, price-per-km, day flags, etc.). |
| **Europe_air_routes_notebook_ANalysis.ipynb** | Notebook for EDA, summary statistics, null analysis, and custom visualisations. |
| **EU_air_routes_SQL_Analysis.ipynb** | SQL queries used for analytics and dashboarding. |
| **EU_air_routes_Dashboard.lvdash.json** | Exported Databricks SQL dashboard with 3+ visuals and global filters. |
| **EU_Air_MAchine_learning.ipynb** | Machine Learning notebook implementing a regression model predicting ticket prices. |

---

# 2. Dataset Description

The dataset includes detailed information on airline routes across Europe:

### Route & Airport Identifiers
- `id`
- `iata_from`, `iata_to`
- `departure_city`, `arrival_airport_city_name`, …
- `departure_country`, `arrival_airport_country`

### Pricing & Flight Schedule
- `price`  
- `day1`–`day7` (string flags: yes/no)  
- `common_duration`  
- `flights_per_day` (text like “1 flight”, “0–1 flights”)  
- `flights_per_week`

### Airport Metadata
- ICAO codes  
- Airport names & countries  
- Route counts per airport  

### Geographic Coordinates
- Latitude, longitude, altitude for both arrival and departure airports  

These fields enable both operational and geospatial analysis.

---

# 3. Project Architecture (End-to-End Flow)

1. **Upload CSV into Databricks → Register as raw Delta table**  
   `workspace.eu_air_routes.europe_air_routes_raw`

2. **ETL Notebook → Clean & enrich dataset**  
   - Type standardisation  
   - Creation of weekday flags  
   - Operational features (days operated, daily route indicator)  
   - Numerical approximation for `flights_per_day`  
   - Haversine distance calculation  
   - Price-per-km metric  
   **Output:**  
   `workspace.eu_air_routes.eu_air_routes_clean`

3. **EDA Notebook → Exploration & Visualisation**  
   - Summary statistics  
   - Null analysis  
   - Route frequency insights  
   - Several matplotlib charts  

4. **SQL Notebook → Analytics queries**  
   - Busiest routes  
   - Country-level flight volume  
   - Distance vs price aggregation  
   - Daily routes  
   - Price band segmentation  

5. **SQL Dashboard → Interactive visual reporting**  
   Uses all SQL queries with filters for:
   - Departure country  
   - Arrival country  
   - Departure airport  

6. **ML Notebook → Regression model**  
   Predicts ticket price using distance, weekly frequency, and operating days.

---

# 4. ETL & Feature Engineering (What Happens in the ETL Notebook)

### 4.1 Raw Data Loading
Reads the CSV from Unity Catalog and inspects schema.

### 4.2 Safe Type Handling
- Only truly numeric columns are cast to numeric.  
- Weekday columns and `flights_per_day` remain strings.  
This prevents conversion errors.

### 4.3 Day Flags & Operating Days
Each of `day1`–`day7` is converted into a numeric flag.  
A new column `days_operated` counts active days, and `is_daily_route` marks fully active routes (7-day routes).

### 4.4 Haversine Distance
Computes `distance_km` between airports using latitude/longitude.  
Critical for:
- Distance distribution visualisation  
- Price–distance regression

### 4.5 Price-per-Kilometre
Divides ticket price by distance to study pricing fairness across route lengths.

### 4.6 Final Clean Delta Table
The cleaned and enriched dataset is saved into Unity Catalog for use by all notebooks and dashboards.

---

# 5. Notebook EDA & Custom Visualisations

The EDA notebook explores the cleaned dataset through:

### 5.1 Schema & Summary Stats
- `printSchema()`  
- `.describe()`  
- Row count, unique route count  

### 5.2 Null Analysis
A null table helps identify missing coordinates and incomplete fields.

### 5.3 Operational Insights
- Busiest departure airports  
- Busiest arrival airports  
- Distribution of operating days  

### 5.4 Visualisations
The notebook includes major matplotlib plots such as:
- European geographic scatterplot  
- Route distance histogram  
- Price vs distance scatterplot  
- Weekly flights by country bar chart  

These help build intuition for the SQL dashboard and ML model.

---

# 6. SQL Analysis – Query Purposes

Below are the major SQL query headings and **explanations of what they do**.

### 6.1 Top 15 Busiest Routes
Identifies the busiest routes in Europe based on weekly frequency.  
Highlights high-demand travel corridors.

### 6.2 Weekly Flights by Departure Country
Aggregates all weekly flights originating from each country.  
Reveals leading aviation markets.

### 6.3 Average Price by Distance Bucket
Groups routes by distance ranges and calculates average ticket price and price-per-km.  
Shows price trends and cost behaviour as distance increases.

### 6.4 Daily Operating Routes (7-Day Routes)
Returns only routes that operate every single day.  
These routes represent consistent and essential travel connections.

### 6.5 Price Band Distribution by Country
Categorises prices into tiers (e.g., 0–49€, 50–99€, etc.) and counts routes in each tier for every country.  
Helps compare affordability across countries.

---

# 7. Dashboard – Visuals & Filters

The Databricks SQL Dashboard includes the following visuals:

### 7.1 Busiest Routes (Bar Chart)
Shows the top 15 most frequently operated routes.

### 7.2 Weekly Flights by Country (Bar Chart)
Displays total weekly flights per departure country.

### 7.3 Price vs Distance Bucket (Line Chart)
Shows how prices trend with increasing route distance.

### 7.4 Optional Visuals
- Daily route table  
- Price band distribution (stacked bar or pie)

### Dashboard Filters
Filters applied to all relevant visuals:
- Departure country  
- Arrival country  
- Departure airport (IATA code)  

These allow dynamic, user-driven exploration.

---

# 8. Machine Learning – Price Prediction Model

The ML notebook builds a **linear regression model** using:

### Features:
- `distance_km`  
- `flights_per_week`  
- `days_operated`  

### Data Preparation:
- Outlier filtering (removing extreme prices for stability)  
- Feature vector assembly  
- Train/test split  

### Model Output:
- Regression coefficients  
- RMSE & R² metrics  
- Scatterplot of actual vs predicted prices  

### Insight:
The model confirms that **distance is the strongest predictor of price**, with flight frequency and operational days having secondary influence.

---

# 9. Full Workflow Summary

1. Upload CSV → Create raw Delta table  
2. ETL notebook cleans and enriches the data  
3. EDA notebook visualises insights  
4. SQL notebook builds analytical queries  
5. Dashboard presents interactive visuals  
6. ML notebook builds regression model  
7. All project files pushed to GitHub main branch  

---


# 10. Learning Outcomes

By completing this project, the following skills were demonstrated:

- Building data pipelines on Databricks  
- Using PySpark for ETL and feature engineering  
- Managing datasets with Delta Lake and Unity Catalog  
- Carrying out notebook-based EDA  
- Designing SQL analytics  
- Building interactive dashboards  
- Training ML models on big data  
- Structuring a production-style analytics repository

---


