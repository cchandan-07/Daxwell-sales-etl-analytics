

# Daxwell Sales ETL & Analytics

This project is like an **Arduino circuit**, but for **data**:

- S3 = breadboard (where data wires are connected)
- Glue = microcontroller (runs logic)
- Crawler = auto-detector (reads pin mapping / schema)
- Athena = serial monitor (see and test results)
- Power BI = LCD display (shows final output to users)

We take **raw sales data** → clean it → join it → send it to a **dashboard**.

---

## 1. What This Project Does

- Reads **raw CSV files** (customers, orders, products) from **S3**  
- Uses **AWS Glue Crawler** to create **table schema automatically**  
- Uses **AWS Glue ETL (PySpark)** to:
  - Clean data  
  - Join tables  
  - Calculate sales amount  
- Saves **final clean data** back into another **S3 bucket**  
- Uses **Amazon Athena** to validate data with SQL  
- Uses **Power BI** to build a **sales dashboard**

---

## 2. Tools Used (Like Components List in Arduino)

- **AWS S3** – storage for raw and output data  
- **AWS Glue Crawler** – auto-detects schema (columns & data types)  
- **AWS Glue ETL (PySpark)** – ETL code (main program logic)  
- **AWS Glue Data Catalog** – stores table definitions  
- **Amazon Athena** – runs SQL on S3  
- **Power BI** – visualization and dashboards  
- **Python / PySpark** – transformation logic  
- **SQL** – checks, joins, and validation

---

## 3. Data Flow (Step by Step)

Like `setup()` and `loop()` in Arduino, here’s the flow:

1. **Upload raw CSVs to S3**
   - `customers.csv`
   - `orders.csv`
   - `products.csv`

2. **Run Glue Crawler**
   - Points to S3 raw bucket
   - Auto-creates tables in `daxwell_db`
   - Example tables:
     - `customers_raw`
     - `orders_raw`
     - `products_raw`

3. **Run Glue ETL Job (PySpark)**
   - Reads tables from `daxwell_db`
   - Cleans and joins data
   - Calculates `sales_amount`
   - Writes **curated data** to:
     - `s3://<output-bucket>/fact_sales/`

4. **Use Athena (SQL)**
   - Query curated data
   - Check joins (LEFT JOIN)
   - Validate row counts and metrics

5. **Load to Power BI**
   - Download curated CSV from S3
   - Load into Power BI
   - Build dashboard (charts, KPIs, filters)

---

## 4. Buckets (Like Input & Output Pins)

- **Input bucket (raw)**  
  `s3://<raw-bucket>/`  
  Stores:
  - `customers.csv`
  - `orders.csv`
  - `products.csv`

- **Output bucket (curated)**  
  `s3://<output-bucket>/fact_sales/`  
  Stores:
  - Clean, joined, analytics-ready file(s)

---

## 5. Glue Crawler (What It Really Does)

- Scans raw S3 bucket  
- Detects:
  - Columns  
  - Data types  
- Creates tables in **Glue Data Catalog** under `daxwell_db`  
- **Does NOT** do ETL or cleaning  
- Purpose:
  - So you **don’t** need to write manual `CREATE TABLE` for each file

Think of it as **“auto pin mapping”** for data.

---

## 6. Glue ETL PySpark Job (Core Logic)

Main tasks in ETL code:

- Read from catalog tables:
  - `orders_raw`
  - `customers_raw`
  - `products_raw`
- Clean data:
  - Handle nulls
  - Fix types
  - Parse dates
- Join data:
  - Orders + Customers
  - Orders + Products
- Calculate:
  - `sales_amount = price * quantity - discount`
- Write curated result to **output S3 bucket**

This is like your **Arduino loop()** that continuously processes sensor data and sends output.

---

## 7. Athena (SQL Checkpoint)

Use Athena to:

- Run `SELECT` & `JOIN` queries
- Test LEFT JOINs between orders, customers, products
- Check if:
  - Row counts make sense  
  - Sales totals match expectations  

Athena is mainly used here for **validation and quick analysis**.

---

## 8. Power BI Dashboard

Power BI uses the **curated data** and shows:

- Total sales
- Sales per month (e.g., April vs August)
- Sales by product
- Sales by city
- Sales by payment method (online vs others)
- A detailed table with:
  - Order ID
  - Date
  - Customer
  - Product
  - City
  - Amount
  - Discount

Also includes **filters**:
- When you click a product (e.g., “Biodegradable Bags”), all charts update.

---

## 9. Folder Structure (Sketch of the Project)

```text
daxwell-sales-etl-analytics/
├─ data/
│  ├─ raw/                # sample raw csv (optional)
│  └─ transformed/        # sample curated csv (optional)
├─ etl/
│  └─ glue_job_pyspark.py # main ETL logic
├─ sql/
│  ├─ athena_queries.sql  # sample validation queries
│  └─ athena_ddl.sql      # table DDL if needed
├─ powerbi/
│  └─ daxwell_sales_dashboard.pbix
└─ README.md
