# �️ Enterprise Data Quality Monitor (ELT + Observability)

> **A Production-Grade Data Engineering Portfolio Project**
> *Built with Python, Apache Airflow (Docker), Snowflake, dbt Core, Elementary & Streamlit.*

---

## 📖 Overview

The **Enterprise Data Quality Monitor** is a fully automated ELT pipeline designed to simulate, process, and monitor data quality in a high-volume e-commerce environment.

Unlike standard "happy path" tutorials, this project simulates a **hostile data environment**. It includes a **Chaos Monkey 2.0** engine that intentionally corrupts incoming data (Math Errors, Future Dates, Broken Keys, Typos) to force the data engineering pipeline to detect, isolate, and report these issues in real-time.

### 🏆 Key Features

* **🌪️ Chaos Engineering:** A Python engine that generates synthetic data with probabilistic failures.
* **🏗️ Dual-DAG Architecture:** Implements a robust "Init vs Incremental" strategy for scalable ingestion.
* **❄️ Snowflake Data Lakehouse:** Uses `Variant` tables for RAW loading and Normalized Marts for analytics.
* **✅ dbt & Elementary:** Advanced transformation with automated testing and anomaly detection (Data Observability).
* **📊 Financial Impact Dashboard:** A Streamlit app that calculates the "Cost of Poor Data Quality" (Raw Revenue vs. Clean Revenue).

---

## 🏗️ Architecture

![Architecture Diagram](https://raw.githubusercontent.com/username/repo/main/docs/architecture.png)

The pipeline is containerized using Docker and orchestrated by Airflow.

### 1. Ingestion Phase

* **Source:** Python Synthetic Generator (Faker).
* **Chaos Engine:** Injects 5 types of errors (Calculation, Temporal, Integrity, Domain, Duplication).
* **Destination:** AWS S3 (Landing Zone).
* **Strategy:**
  * **Init Script:** Generates 12 months backbone history.
  * **Daily Script:** "Smart" filler that only generates missing days (Watermark logic).

### 2. Storage & Compute (Snowflake)

* **RAW Layer:** Tables `RAW_ORDERS`, `RAW_CUSTOMERS` loaded via `COPY INTO`.
* **Storage Integration:** Secure access to S3 without exposing keys.

### 3. Transformation (dbt Core)

* **Staging:** Casting and basic cleaning.
* **Marts:** Business logic (`fct_orders`) and derivation of Quality Flags (`is_future_order`, `has_math_error`).
* **Tests:** `dbt test` + `Elementary` for schema validation and freshness checks.

### 4. Visualization

* **Streamlit App:** Connects directly to Snowflake.
* **Key Metric:** "Revenue Impact" — showing stakeholders exactly how much money is "at risk" due to bad data.

---

## 🛠️ Technology Stack

| Component | Technology | Role |
| :--- | :--- | :--- |
| **Orchestration** | Apache Airflow 2.8 | DAG Management (Docker) |
| **Compute** | Snowflake | Data Warehouse |
| **Transformation** | dbt Core 1.7 | Modeling & Testing |
| **Observability** | Elementary Data | DQ Monitoring |
| **Ingestion** | Python (Boto3, Pandas) | Chaos Generator |
| **Frontend** | Streamlit | Executive Dashboard |
| **Infrastructure** | Docker Compose | Container Management |

---

## 🚀 How to Run (Deployment Guide)

### Prerequisites

* Docker & Docker Compose installed.
* Snowflake Account.
* AWS S3 Bucket.

### 1. Setup Environment

Create a `.env` file in the root directory:

```bash
SNOWFLAKE_ACCOUNT=...
SNOWFLAKE_USER=...
SNOWFLAKE_PASSWORD=...
SNOWFLAKE_WAREHOUSE=COMPUTE_WH
SNOWFLAKE_DATABASE=EDQM_DB
AWS_ACCESS_KEY_ID=...
AWS_SECRET_ACCESS_KEY=...
S3_BUCKET_NAME=...
```

### 2. Launch Infrastructure

Start the containerized environment:

```bash
docker compose up -d
```

Access Airflow UI at `http://localhost:8080` (User/Pass: `admin`).

### 3. Initialize the System ("The Big Bang")

In Airflow, trigger the **`init_backfill_project`** DAG.

* ✅ Cleans S3 Landing Zone.
* ✅ Generates 12 Months of Historical Data (37k+ Orders).
* ✅ Performs Full Load & Transform.

### 4. Enable Daily Monitoring

Enable the **`enterprise_data_quality_monitor`** DAG (@daily).

* ✅ Checks Watermark.
* ✅ Generates only new data (today's orders).
* ✅ Incrementally loads to Snowflake.

### 5. View Dashboard

Run the frontend locally:

```bash
python -m streamlit run app.py
```

---

## 📂 Project Structure

```bash
├── dags/                   # Airflow DAGs
│   ├── init_backfill.py    # One-time Setup Pipeline
│   └── elt_pipeline.py     # Daily Incremental Pipeline
├── src/                    # Python Source Code
│   ├── chaos_monkey.py     # Data Corruption Engine
│   ├── generator.py        # Synthetic Data Factory
│   └── snowflake_loader.py # Universal Loader (Full/Inc)
├── dbt_project/            # Modern Data Stack
│   ├── models/             # SQL Models (Staging/Marts)
│   └── tests/              # Generic & Singular Tests
├── app.py                  # Streamlit Dashboard
└── docker-compose.yaml     # Infrastructure as Code
```

---

## 👨‍💻 Author

**Francisco (JillProyectos)**
*Enterprise Data Engineer*

> *"Bad data is not just an irritation, it's a business expense."*
