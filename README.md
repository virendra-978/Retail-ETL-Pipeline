# Retail ETL Pipeline

An ETL pipeline for retail data processing using Medallion Architecture (Bronze → Silver → Gold) on Databricks.

## Overview

This project implements an end-to-end data pipeline that ingests raw retail data from S3, applies transformations through multiple layers, and delivers analytics-ready datasets.

**Key Features:**
* Medallion Architecture (Bronze, Silver, Gold layers)
* SCD Type 2 for customer dimension
* CDC for products and stores
* Star schema for analytics
* Automated data archival

## Architecture

```
S3 Raw Data (CSV)
       ↓
🥉 BRONZE LAYER (bronze_catalog.retail_bronze)
   • Raw data ingestion
   • customers, products, stores, sales
       ↓
🥈 SILVER LAYER (silver_catalog.retail_silver)
   • Data cleansing & transformations
       ↓
🥇 GOLD LAYER (gold_catalog.retail_gold)
    • dim_customers (SCD Type 2)
   • dim_products, dim_stores (CDC)
   • fact_sales (Star Schema)
   • Analytics-ready datasets
```

## Technologies

* **Platform**: Databricks (AWS)
* **Storage**: Delta Lake, AWS S3
* **Languages**: Python, SQL

## Data Sources

| Dataset | Location | Format |
|---------|----------|--------|
| Customers | `s3://retail-etl-lakehouse/landing/customers/` | CSV |
| Products | `s3://retail-etl-lakehouse/landing/products/` | CSV |
| Stores | `s3://retail-etl-lakehouse/landing/stores/` | CSV |
| Sales | `s3://retail-etl-lakehouse/landing/sales/` | CSV |

## Catalog Structure

```
bronze_catalog.retail_bronze
  ├── bronze_customers
  ├── bronze_products
  ├── bronze_stores
  └── bronze_sales

silver_catalog.retail_silver
  ├── silver_customers 
  ├── silver_products 
  ├── silver_stores 
  └── silver_sales 

gold_catalog.retail_gold
  ├── DimCustomer
  ├── DimProduct
  ├── DimStore
  └── FactSales
```

## Pipeline Workflow

Execute notebooks in this order:

1. **[schemas-creation](#notebook-3885210314204597)** - Create catalogs and schemas
2. **[archival_process](#notebook-19946330665726)** - Archive old files
3. **[Bronze_load](#notebook-3885210314204598)** - Ingest raw data from S3
4. **[Silver_load](#notebook-3885210314204602)** - Transform and cleanse data
5. **[Gold_load](#notebook-3885210314204603)** - Create dimensional model
6. **[Validation & Testcases](#notebook-1324652314039115)** - Validate data quality

### Workflow Details

**Bronze Layer**: Load raw CSV files from S3 into Delta tables

**Silver Layer**: 
* Apply data cleansing and standardization


**Gold Layer**:
* Build dimensional model (star schema)
* Create surrogate keys
* Implement SCD Type 2 for customers (track historical changes)
* Apply CDC for products and stores
* Create fact table with referential integrity

**Archival**: Keep only most recent files in landing zone, move older files to archive

**Validation**: Run data quality checks and test cases

## Setup Instructions

### Prerequisites
* Databricks workspace on AWS
* S3 access (IAM role or credentials)
* Unity Catalog enabled

### Quick Start

1. **Clone repository** to Databricks Repos

2. **Configure S3 access** in cluster settings or notebook

3. **Run notebooks in sequence**:
   ```
   archival_process → schemas-creation → Bronze_load → Silver_load → Gold_load →  Validation
   ```

### Schedule as Job (Optional)

Create a Databricks workflow with 4 tasks in sequence:
* Schedule: Daily at 2 AM UTC
* Compute: Serverless
* Notifications: On failure

## Project Structure

```
Retail-ETL-Pipeline/
├── README.md
├── schemas-creation.ipynb
├── Bronze_load.ipynb
├── Silver_load.ipynb
├── Gold_load.ipynb
├── archival_process.ipynb
└── Validation & Testcases.ipynb
```

## Data Transformations

### SCD Type 2 (Customers)
Tracks historical changes with `StartDate`, `EndDate`, `IsActive` columns

### Star Schema (Gold Layer)
* **Fact Table**: FactSales with surrogate keys
* **Dimensions**: DimCustomer, DimProduct, DimStore
---

**Last Updated**: 08 May 2026
