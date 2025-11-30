# formula-1-telemetry-pipeline
A high-performance local data lakehouse for analyzing Formula 1 race data. Built with DuckDB, Python, and SQL, implementing a Medallion Architecture from raw ingestion to analytical dashboards.

🏎️ Formula 1 Telemetry Pipeline
!(https://img.shields.io/badge/Status-Active-success) !(https://img.shields.io/badge/Stack-DuckDB%20|%20SQL%20|%20Python-blue)

A high-performance Local Data Lakehouse for analyzing historical Formula 1 race data. This project leverages DuckDB to process data using a modern Medallion Architecture, demonstrating advanced Data Engineering concepts without the need for expensive cloud infrastructure.

📋 Table of Contents
Overview

Architecture -(#data-model)

Prerequisites -(#setup--usage)

Example Analysis -(#roadmap)

🧐 Overview
The goal of this project is to simulate a production-grade data pipeline locally. Instead of relying on traditional row-based databases (like PostgreSQL/MySQL), this project uses DuckDB, an in-process SQL OLAP database designed for analytical workloads.

Key Features:

Robust Ingestion: Handles schema drift and "dirty" CSV data (e.g., mixed types, \N null values).

Efficient Processing: Uses columnar storage and vectorized execution for high-speed queries.

Business Logic: Transforms raw telemetry into meaningful KPIs like "Driver Dominance" and "Season Growth".

🏗️ Architecture
The project follows the Medallion Architecture pattern, organizing data into three logical layers within DuckDB:

Layer	Schema Name	Description
🥉 Bronze	raw_f1	Raw Ingestion. Direct copy of source CSV files. No transformations. Schema-on-read strategy to prevent ingestion failures.
🥈 Silver	staging_f1	Cleaned & Conform. Data typing (String -> Integer/Date), handling NULLs (\N), and column renaming to snake_case.
🥇 Gold	mart_f1	Business Aggregates. Dimensional models (Star Schema) optimized for BI tools and analytical queries.
💾 Data Model (Gold Layer)
The primary analytical model is fct_race_results, a wide fact table that denormalizes the data for easy querying.

Granularity: One row per driver per race.

Key Metrics: points, grid_position, finish_position, fastest_lap_speed.

Dimensions: Joined with drivers, constructors, races, and circuits.

🛠️ Prerequisites
Python 3.8+

Git

DuckDB (Command line tool or Python library)

🚀 Setup & Usage
1. Clone the repositorybash

git clone https://github.com/YOUR-USERNAME/formula-1-telemetry-pipeline.git cd formula-1-telemetry-pipeline


### 2. Setup Virtual Environment
```bash
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install duckdb pandas
3. Acquire Data

Download the F1 dataset (based on Ergast API) from Kaggle or similar sources.

Place the .csv files in the data/ directory.

Note: The data/ folder is ignored by git to avoid uploading large files.

4. Run the Pipeline

(Assuming you have a main.py or SQL scripts setup)

To initialize the database and run the SQL transformations using DuckDB CLI:

Bash
duckdb f1_telemetry.duckdb < sql/01_ingest.sql
duckdb f1_telemetry.duckdb < sql/02_transform.sql
duckdb f1_telemetry.duckdb < sql/03_mart.sql
📊 Example Analysis
Here is a sample SQL query to analyze Grid Position vs. Win Probability:

SQL
SELECT 
    grid_position,
    COUNT(*) AS total_starts,
    SUM(CASE WHEN finish_position = 1 THEN 1 ELSE 0 END) AS wins,
    ROUND((SUM(CASE WHEN finish_position = 1 THEN 1 ELSE 0 END) * 100.0 / COUNT(*)), 1) AS win_percentage
FROM mart_f1.fct_race_results
WHERE season_year >= 2000
GROUP BY grid_position
HAVING total_starts > 50
ORDER BY win_percentage DESC;
🔮 Roadmap
This project is designed to scale. Future improvements include:

[ ] Orchestration: Implement dlt (Data Load Tool) for automated API ingestion.

[ ] Transformation: Migrate SQL logic to dbt (data build tool) for better dependency management and testing.

[ ] Visualization: Build a dashboard using Evidence.dev or Streamlit.

[ ] CI/CD: Add GitHub Actions for SQL linting (sqlfluff).

👤 Author
Data Engineer / Formula 1 Enthusiast

[LinkedIn Profile Link]
