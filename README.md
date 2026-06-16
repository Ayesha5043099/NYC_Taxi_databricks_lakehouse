
# NYC Taxi Databricks Lakehouse

An end-to-end Data Engineering project built on Databricks, implementing a Bronze → Silver → Gold lakehouse architecture using NYC Yellow Taxi trip data.

---

## Project Overview

This project demonstrates a complete data engineering pipeline that ingests, cleans, transforms, and visualizes NYC Yellow Taxi trip data using Delta Lake on Databricks. The pipeline follows the Medallion Architecture pattern with full data quality checks and SQL-based dashboards.

---

## Architecture

```
Kaggle Dataset
      │
      ▼
┌─────────────┐
│   BRONZE    │  Raw ingestion — data as-is with ingestion timestamp
│  Delta Lake │
└─────────────┘
      │
      ▼
┌─────────────┐
│   SILVER    │  Cleaned, validated, type-cast, filtered data
│  Delta Lake │  + engineered features (duration, hour, day, month)
└─────────────┘
      │
      ▼
┌─────────────┐
│    GOLD     │  Aggregated analytics-ready tables
│  Delta Lake │  for dashboards and reporting
└─────────────┘
      │
      ▼
┌─────────────┐
│  DASHBOARD  │  Databricks SQL — interactive visualizations
└─────────────┘
```

---

## Dataset

- **Source**: [NYC Yellow Taxi Trip Data — Kaggle](https://www.kaggle.com/datasets/elemento/nyc-yellow-taxi-trip-data)
- **Coverage**: NYC Yellow Taxi trips (2015 data with coordinates)
- **Format**: CSV
- **Key columns**: pickup/dropoff datetime, coordinates, fare, distance, passenger count, payment type

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Platform | Databricks (Free Edition) |
| Storage | Delta Lake |
| Processing | Apache Spark (PySpark) |
| Transformation | Spark SQL |
| Orchestration | Databricks Notebooks |
| Visualization | Databricks SQL Dashboard |
| Source Control | GitHub |

---

## Project Structure

```
nyc-taxi-databricks-lakehouse/
│
├── 01_bronze_ingestion.ipynb     # Kaggle download, extract, raw Delta table
├── 02_silver_layer.ipynb         # Cleaning, casting, filtering, feature engineering
├── 03_gold_layer.ipynb           # Aggregated Gold tables
├── 04_quality_checks.ipynb       # Data quality validation report
│
├── Hourly Trip Volume.sql        # Trips by hour of day
├── Payment Type Breakdown.sql    # Credit card vs cash analysis
├── Distance Bucket Analysis.sql  # Short/medium/long trip breakdown
├── Monthly Revenue Trend.sql     # Revenue trend over time
│
└── README.md
```

---

## Lakehouse Tables

### Bronze Layer — `nyc_taxi_project.raw_data`

| Table | Description |
|-------|-------------|
| `bronze_taxi` | Raw CSV data with `ingestion_time` timestamp added |

### Silver Layer — `nyc_taxi_project.silver_data`

| Table | Description |
|-------|-------------|
| `silver_taxi` | Cleaned and validated data with engineered features |

**Transformations applied:**
- All STRING columns cast to correct numeric types
- Invalid rows removed: negative fares, zero distance, impossible coordinates
- Coordinate bounds restricted to NYC only (lat/lon range)
- Passenger count validated: 1–6
- Trip duration calculated in minutes
- Time features extracted: hour, day of week, month, year
- Duplicates removed

### Gold Layer — `nyc_taxi_project.gold_data`

| Table | Description |
|-------|-------------|
| `gold_hourly_trips` | Trip volume, revenue, avg fare by hour |
| `gold_payment_summary` | Breakdown by payment type per month |
| `gold_trip_distance_buckets` | Short / Medium / Long / Very Long trip analysis |

---

## Data Quality Report

| Check | Result |
|-------|--------|
| Null values | 0 — enforced via dropna |
| Duplicates | 0 — enforced via dropDuplicates |
| Fare range | $0.01 – $500 |
| Distance range | 0.01 – 100 miles |
| Duration range | 1 – 180 minutes |
| Coordinate bounds | NYC only |

---

## Dashboard — NYC Taxi Analytics

Built in Databricks SQL with 4 visualizations:

- **Hourly Trip Volume** — Bar chart showing peak hours (6 PM – 10 PM busiest)
- **Payment Type Breakdown** — Pie chart: Credit Card vs Cash distribution
- **Distance Bucket Analysis** — Bar chart: short vs medium vs long trips
- **Monthly Revenue Trend** — Line chart showing revenue over time

---

## How to Run

### Prerequisites
- Databricks account (Free Edition works)
- Kaggle API credentials

### Steps

1. Clone this repo into your Databricks workspace via Git integration
2. Set your Kaggle credentials in `01_bronze_ingestion`:
```python
os.environ["KAGGLE_USERNAME"] = "your_username"
os.environ["KAGGLE_KEY"] = "your_key"
```
3. Run notebooks in order:
   - `01_bronze_ingestion`
   - `02_silver_layer`
   - `03_gold_layer`
   - `04_quality_checks`
4. Open Databricks SQL Editor and run the 4 query files
5. Add visualizations to dashboard

---

## Key Insights

- Evening hours (6 PM – 10 PM) have the highest trip demand
- Credit card is the dominant payment method
- Majority of trips are short distance (under 3 miles)
- Average fare across all trips is approximately $12–$14

---
