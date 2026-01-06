# Late-Arriving Events Medallion Pipeline (Databricks + Delta Lake)

## Overview

This project demonstrates a **production-style Data Engineering pipeline** that handles **late-arriving events** using a **Medallion Architecture (Bronze → Silver → Gold)** on **Databricks Community Edition** with **Delta Lake**.

The focus is on:
- Incremental ingestion
- Late data correction
- Idempotent processing
- OLAP-ready analytics tables

This is **not a toy ETL** — it mirrors real-world pipeline challenges.

---

## Problem Statement

Event data arrives continuously, but:
- Some events arrive **late** (ingest_time > event_time by several minutes)
- Existing aggregates must be **corrected** when late data arrives
- Full recomputation of analytics tables is expensive

We need a system that:
- Ingests raw events safely
- Cleans and deduplicates data
- Updates downstream aggregates **only where necessary**

---

## Architecture

### 🥉 Bronze Layer — Raw Ingestion
- Append-only
- Stores raw events exactly as received
- Partitioned by `event_date`
- Handles schema flexibility

### 🥈 Silver Layer — Cleaned & Corrected
- Deduplicated by `event_id`
- Uses **Delta MERGE** to handle late arrivals
- Updates records only when newer `ingest_time` arrives
- Idempotent

### 🥇 Gold Layer — Analytics
- Aggregated metrics per `event_date` and `user_id`
- Includes latency-based KPIs
- Recomputed selectively based on updated partitions

---

## Key Concepts Demonstrated

- Delta Lake `MERGE INTO` for late-arriving data
- Incremental processing without full recomputation
- Partition-aware analytics rebuilds
- OLAP-friendly data modeling
- Databricks Community Edition constraints & workarounds

---

## Dataset

Synthetic event data with:
- `event_id`
- `user_id`
- `event_time`
- `ingest_time`
- `amount`
- `event_date`

Late arrivals are intentionally simulated.

---

## Pipeline Flow

1. **Generate Events**
   - Simulate on-time and late-arriving events

2. **Bronze Load**
   - Append raw data to Delta table

3. **Silver Merge**
   - Deduplicate by `event_id`
   - Update only if newer `ingest_time` arrives

4. **Gold Aggregation**
   - Compute:
     - total events
     - total amount
     - late event percentage
   - Partitioned by `event_date`

---

## Why Delta Lake?

Delta enables:
- ACID guarantees on data lakes
- Safe incremental updates
- Schema enforcement
- Time travel (conceptually, even if not explored deeply here)

---

## Databricks Community Edition Notes

- Public DBFS root is restricted
- Tables are created using `saveAsTable`
- Tables are dropped & recreated during development
- Schema evolution handled explicitly

This mirrors **real dev → prod differences**.

---

## Interview Talking Points

- Why Bronze is append-only
- Why Silver uses MERGE instead of overwrite
- How late data affects aggregates
- Why Gold is recomputed selectively
- Difference between dev resets and prod pipelines
- Delta vs traditional RDBMS ETL

---

## Tech Stack

- Apache Spark (PySpark)
- Delta Lake
- Databricks Community Edition
- SQL + Window Functions

---

## How This Would Scale in Production

- Bronze: streaming ingestion (Kafka / Kinesis)
- Silver: dbt incremental models or Delta Live Tables
- Gold: partition-aware incremental rebuilds
- Orchestration: Airflow / Databricks Workflows
- Storage: S3 / ADLS with Delta

---

## Status

✅ Core pipeline implemented  
✅ Late-arrival handling demonstrated  
✅ Interview-ready explanations  

---

## Author

**Vinil Gupta**  
Data Engineering / Analytics Engineering  

