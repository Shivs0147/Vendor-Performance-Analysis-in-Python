# Vendor Performance Analysis in Python

## 📊 [CSV to SQLite Ingestion Pipeline](Ingestion.md) 

<p align='justify'>
This project uses a Python script (inside a Jupyter Notebook) to <strong>automate the loading of multiple CSV files into a SQLite database</strong>. It's useful for small ETL jobs or prepping data for analysis tools like Power BI or Tableau.
</p>

---

### 📁 Folder Structure

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
> [`Ingestion`](https://github.com/Shivs0147/Vendor-Performance-Analysis-in-Python/releases/download/v1.0.0/data.zip)

### CSV to SQLite Ingestion Pipeline

Automates the ingestion of multiple `.csv` files into a local SQLite database using Python. Logs activity, tracks execution time, and creates one table per CSV inside `inventory.db`.

### Steps Overview

🔹 Step 1: Import Libraries  
🔹 Step 2: Setup Logger to logs/ingestion_db.log  
🔹 Step 3: Connect to SQLite via SQLAlchemy  
🔹 Step 4: Define function `ingest_db(df, table_name, engine)`  
🔹 Step 5: Loop through files in `data/` folder  
🔹 Step 6: Log start time, ingest, end time

### Example Log Output

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

## 📌 [Project Overview: Exploratory Data Analysis on SQLite Inventory DB](EDA.md)

This project performs **exploratory data analysis (EDA)** on a structured SQLite database (`inventory.db`) containing inventory, vendor, purchase, and sales data. Using a **Jupyter Notebook**, the goal is to uncover actionable insights like **vendor profitability**, **pricing trends**, and **inventory movement**—preparing the data for downstream BI tools like **Power BI**, **Tableau**, or custom dashboards.

---

## 📁 Folder Structure

```plaintext
.
├── Exploratory Data Analysis.ipynb  <- This notebook (EDA logic & insights)
├── inventory.db                     <- SQLite database to be analyzed
└── data/                            <- (Optional) Folder if you export analysis-ready CSVs
```

## 🔍 Key Highlights

- Analyzes all key tables: `sales`, `purchases`, `vendors`, `inventory`, etc.
- Performs data profiling: null checks, value distribution, cross-table validation
- Uses **SQL joins + pandas** for deriving:
  - Vendor performance
  - Product-level sales
  - Stock turnover
- Implements **logging** for traceability and debugging
- Outputs analysis-ready tables and metrics like:
  - **Gross Profit**, **Profit Margin**
  - **Sales-to-Purchase Ratio**
  - **Inventory Turnover**
  - **Excise Tax Trends**
- Enables easy export to `.csv` or integration into reporting dashboards

### Example Log Output

```
2025-07-22 22:20:41,552 - DEBUG - Testing logger setup
2025-07-22 22:20:41,557 - INFO - Creating Vendor Summary Table...
2025-07-22 22:20:51,357 - INFO - 
   VendorNumber                               VendorName  Brand                   Description  PurchasePrice  ActualPrice Volume  TotalPurchaseQty  TotalPurchaseDollors  ExciseTax  SalesDollars  SalesPrice  SalesQuantity  TotalFreight
0             2  IRA GOLDMAN AND WILLIAMS, LLP            90085  Ch Lilian 09 Ladouys St Este          23.86        36.99    750                 7                167.02       0.22           213       36.99              2         27.08
1           105              ALTAMAR BRANDS LLC            8412     Tequila Ocho Plata Fresno          35.71        49.99    750               140               4999.40      11.84          7935      699.86             15         62.39
2           287              APPOLO VINEYARDS LLC         24921            Appolo Vyds Seyval          10.40        15.49    750                36                374.40       0.33          1191       46.47              3         12.28
3           287              APPOLO VINEYARDS LLC         24922        Appolo Vyds China Girl          10.47        15.49    750                36                376.92       0.55          2655       77.45              5         12.28
4           388              ATLANTIC IMPORTING COMPANY    2034     Glendalough Double Barrel          21.32        32.99    750               828              17652.96     117.56         73937     3652.74            149        211.74
2025-07-22 22:20:51,357 - INFO - Cleaning Data...
2025-07-22 22:20:51,389 - INFO - 
   VendorNumber                     VendorName  Brand                   Description  PurchasePrice  ActualPrice  Volume  TotalPurchaseQty  TotalPurchaseDollors  ExciseTax  SalesDollars  SalesPrice  SalesQuantity  TotalFreight  GrossProfit  ProfitMargin  StockTurnOver  SalesToPurchaseRatio
0             2  IRA GOLDMAN AND WILLIAMS, LLP  90085  Ch Lilian 09 Ladouys St Este          23.86        36.99   750.0                 7                167.02       0.22           213       36.99              2         27.08        45.98     21.586854       0.285714              1.275296
1           105             ALTAMAR BRANDS LLC   8412     Tequila Ocho Plata Fresno          35.71        49.99   750.0               140               4999.40      11.84          7935      699.86             15         62.39      2935.60     36.995589       0.107143              1.587190
2           287           APPOLO VINEYARDS LLC  24921            Appolo Vyds Seyval          10.40        15.49   750.0                36                374.40       0.33          1191       46.47              3         12.28       816.60     68.564232       0.083333              3.181090
3           287           APPOLO VINEYARDS LLC  24922        Appolo Vyds China Girl          10.47        15.49   750.0                36                376.92       0.55          2655       77.45              5         12.28      2278.08     85.803390       0.138889              7.043935
4           388     ATLANTIC IMPORTING COMPANY   2034     Glendalough Double Barrel          21.32        32.99   750.0               828              17652.96     117.56         73937     3652.74            149        211.74     56284.04     76.124322       0.179952              4.188363
2025-07-22 22:20:51,390 - INFO - Inserting Data...
2025-07-22 22:20:51,621 - INFO - Completed
2025-07-29 17:42:05,207 - DEBUG - Testing logger setup
2025-07-29 17:42:05,212 - INFO - Creating Vendor Summary Table...
2025-07-29 17:42:14,454 - INFO - 
   VendorNumber                               VendorName  Brand                   Description  PurchasePrice  ActualPrice Volume  TotalPurchaseQty  TotalPurchaseDollors  ExciseTax  SalesDollars  SalesPrice  SalesQuantity  TotalFreight
0             2  IRA GOLDMAN AND WILLIAMS, LLP            90085  Ch Lilian 09 Ladouys St Este          23.86        36.99    750                 7                167.02       0.22           213       36.99              2         27.08
1           105              ALTAMAR BRANDS LLC            8412     Tequila Ocho Plata Fresno          35.71        49.99    750               140               4999.40      11.84          7935      699.86             15         62.39
2           287              APPOLO VINEYARDS LLC         24921            Appolo Vyds Seyval          10.40        15.49    750                36                374.40       0.33          1191       46.47              3         12.28
3           287              APPOLO VINEYARDS LLC         24922        Appolo Vyds China Girl          10.47        15.49    750                36                376.92       0.55          2655       77.45              5         12.28
4           388              ATLANTIC IMPORTING COMPANY    2034     Glendalough Double Barrel          21.32        32.99    750               828              17652.96     117.56         73937     3652.74            149        211.74
2025-07-29 17:42:14,454 - INFO - Cleaning Data...
2025-07-29 17:42:14,467 - INFO - 
   VendorNumber                     VendorName  Brand                   Description  PurchasePrice  ActualPrice  Volume  TotalPurchaseQty  TotalPurchaseDollors  ExciseTax  SalesDollars  SalesPrice  SalesQuantity  TotalFreight  GrossProfit  ProfitMargin  StockTurnOver  SalesToPurchaseRatio
0             2  IRA GOLDMAN AND WILLIAMS, LLP  90085  Ch Lilian 09 Ladouys St Este          23.86        36.99   750.0                 7                167.02       0.22           213       36.99              2         27.08        45.98     21.586854       0.285714              1.275296
1           105             ALTAMAR BRANDS LLC   8412     Tequila Ocho Plata Fresno          35.71        49.99   750.0               140               4999.40      11.84          7935      699.86             15         62.39      2935.60     36.995589       0.107143              1.587190
2           287           APPOLO VINEYARDS LLC  24921            Appolo Vyds Seyval          10.40        15.49   750.0                36                374.40       0.33          1191       46.47              3         12.28       816.60     68.564232       0.083333              3.181090
3           287           APPOLO VINEYARDS LLC  24922        Appolo Vyds China Girl          10.47        15.49   750.0                36                376.92       0.55          2655       77.45              5         12.28      2278.08     85.803390       0.138889              7.043935
4           388     ATLANTIC IMPORTING COMPANY   2034     Glendalough Double Barrel          21.32        32.99   750.0               828              17652.96     117.56         73937     3652.74            149        211.74     56284.04     76.124322       0.179952              4.188363
2025-07-29 17:42:14,467 - INFO - Inserting Data...
2025-07-29 17:42:14,738 - INFO - Completed
```

---

## 🛠 Tech Stack

| Tool         | Purpose                           |
|--------------|-----------------------------------|
| Python       | Core analysis language            |
| pandas       | Data analysis & manipulation      |
| SQLite3      | SQL operations on the database    |
| SQL          | Custom querying & joins           |
| Jupyter      | Interactive notebook environment  |

> 📁 Logs are stored in: `logs/eda_analysis.log`

## 📊 [Detailed Analysis Overview of Vender Performance](VPA.md)

This project provides a comprehensive vendor performance analysis using data from the `Vendor_Sales_Summary` table. The notebook applies structured exploratory data analysis (EDA) to uncover patterns, detect anomalies, and support data-driven vendor evaluation.

---

```plaintext
Vendor_Performance_Analysis/
├── data/                                   # Raw input files
│   └── Vendor_Data.csv
│
├── notebooks/                              # Jupyter Notebooks for analysis
│   └── Vendor_Performance_Analysis.ipynb   # Main analysis and EDA notebook
│
├── output/                                 # Saved charts, visualizations, or reports
│   ├── Snapshot_of_Dist_Plots_Num_Cols.png
│   ├── Snapshot_of_Outlier_Detection.png
│   ├── Snapshot_of_Correlation_heatmap.png
│   └── most_frequent_vendors_products.png
│
├── docs/                                   # Documentation (optional)
│   └── README.md                           # Project overview and summary


```

### 🔍 Key Components of the Analysis

#### ✅ Data Profiling & Summary Statistics
Begins with an overview of all numerical fields (e.g., `GrossProfit`, `SalesDollars`, `PurchasePrice`, etc.) using `describe()` and `.info()` to understand the shape, scale, and quality of the data.

#### 📈 Distribution Analysis
Histograms and KDE plots help visualize how key metrics are distributed — identifying skewness, peaks, and the presence of outliers.

#### 🧹 Data Cleaning / Filtering
To ensure valid comparisons, only records with positive `Sales Quantity`, `Gross Profit`, and `Profit Margin` are retained. This removes irrelevant or inconsistent rows (e.g., zero or negative sales/profit).

#### 📦 Outlier Detection via Boxplots
Boxplots (before and after filtering) highlight extreme values and help distinguish between data noise and meaningful exceptions (e.g., exceptionally high freight costs or profits).

#### 🏷️ Top Vendors & Products
Count plots for the most frequent `VendorName` and `Description` values offer insight into which vendors and SKUs dominate the dataset.

#### 🔁 Correlation Heatmap
A colored matrix shows how numeric features relate to each other. This is useful to:

- Detect multicollinearity (e.g., `SalesDollars` ↔ `SalesQuantity`)
- Spot unexpected relationships or feature dependencies

---

### 🎯 Why It Matters

This analysis is ideal for **procurement**, **inventory**, and **finance** teams aiming to:

- Evaluate vendor profitability and efficiency  
- Identify underperforming or high-impact suppliers  
- Make informed decisions based on clear data visuals and statistical indicators
