# Vendor Performance Analysis in Python

## 📊 CSV to SQLite [Ingestion](Ingestion.md) Pipeline

<p align='justify'>
This project uses a Python script (inside a Jupyter Notebook) to <strong>automate the loading of multiple CSV files into a SQLite database</strong>. It's useful for small ETL jobs or prepping data for analysis tools like Power BI or Tableau.
</p>

---

## 📁 Folder Structure

```plaintext
.
├── Ingestion_db.ipynb        <- Jupyter notebook with all ingestion logic
├── data/                     <- Place your CSV files here
│   ├── begin_inventory.csv
│   ├── end_inventory.csv
│   ├── purchase_prices.csv
│   ├── purchases.csv
│   ├── Sales.csv
│   ├── vendor_invoice.csv
│   └── ...
├── logs/
│   └── ingestion_db.log      <- All logs about the ingestion process
└── inventory.db              <- SQLite database created automatically

```
> [`Data File`](https://github.com/Shivs0147/Vendor-Performance-Analysis-in-Python/releases/download/v1.0.0/data.zip)

# 📊 CSV to SQLite Ingestion Pipeline

Automates the ingestion of multiple `.csv` files into a local SQLite database using Python. Logs activity, tracks execution time, and creates one table per CSV inside `inventory.db`.

## Steps Overview

🔹 Step 1: Import Libraries  
🔹 Step 2: Setup Logger to logs/ingestion_db.log  
🔹 Step 3: Connect to SQLite via SQLAlchemy  
🔹 Step 4: Define function `ingest_db(df, table_name, engine)`  
🔹 Step 5: Loop through files in `data/` folder  
🔹 Step 6: Log start time, ingest, end time

## Example Log Output

```
2025-07-30 16:57:01,935 - INFO - Ingesting begin_inventory.csv into DB
2025-07-30 16:57:07,322 - INFO - Ingesting end_inventory.csv into DB
2025-07-30 16:57:22,446 - INFO - Ingesting purchases.csv into DB
2025-07-30 16:58:08,618 - INFO - Ingesting purchase_prices.csv into DB
2025-07-30 16:58:11,830 - INFO - Ingesting Sales.csv into DB
2025-07-30 16:58:50,223 - INFO - Ingesting vendor_invoice.csv into DB
2025-07-30 16:58:50,541 - INFO - ----------- Ingestion Completed -----------
2025-07-30 16:58:50,541 - INFO - Total Time Taken: 1.82 minutes
```
