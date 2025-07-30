
# 📊 [Exploratory Data Analysis – SQLite Inventory]

This project uses a Jupyter Notebook to **analyze an SQLite database (`inventory.db`)** containing inventory, purchase, sales, and vendor data. The goal is to derive insights for decision-making such as **vendor profitability** and **product pricing optimization**—ideal for downstream use in Power BI, Tableau, or dashboards.

---

## 📁 Folder Structure

```plaintext
.
├── Exploratory Data Analysis.ipynb  <- This notebook (EDA logic & insights)
├── inventory.db                     <- SQLite database to be analyzed
└── data/                            <- (Optional) Folder if you export analysis-ready CSVs
```

---

## 🧠 What This Notebook Does

### ✅ Core Objectives:
- Understand structure & size of `inventory.db`
- Inspect all tables: Sales, Purchases, Inventory, Vendors, etc.
- Identify patterns & relationships across tables
- Recommend aggregated tables or views for Power BI/Reporting

---

## 🔍 Key Steps in the Notebook

### 🔹 Step 1: Load Libraries & Connect to DB

```python
import pandas as pd
import sqlite3
import os

conn = sqlite3.connect('inventory.db')
```

> `pandas` is used for EDA, `sqlite3` for querying, and `os` for checking file size.

---

### 🔹 Step 2: Check DB Size & Table Overview

```python
file_path = "inventory.db"
size_bytes = os.path.getsize(file_path)
size_mb = size_bytes / (1024 * 1024)
print(f"Database size: {size_mb:.2f} MB")

# List all tables
tables = pd.read_sql_query("SELECT name FROM sqlite_master WHERE type='table'", conn)
```

> Understand how large your DB is and confirm all available tables.

---

### 🔹 Step 3: Table-Wise Exploration

Each table is queried and analyzed using:

```python
df = pd.read_sql_query("SELECT * FROM table_name", conn)
df.info()
df.describe()
df.head()
```

Common patterns inspected:
- Missing values
- Data distributions
- Outliers
- Unmatched keys between tables

---

### 🔹 Step 4: Cross-Table Joins & Business Logic

You’ll find SQL joins and logical groupings, such as:
- Vendor performance (`total purchases`, `gross profit`)
- Inventory movement trends
- Product sales frequency

---

### 🔹 Step 5: Logging Setup

This section sets up a logger named `eda_logger` to write logs into a file `logs/eda_analysis.log`.

```python
import logging

# --- Ensure logs directory exists ---
os.makedirs('logs', exist_ok=True)

# --- LOGGER SETUP START ---
logger = logging.getLogger("get_vendor_summary")
logger.setLevel(logging.DEBUG)

log_file = os.path.join(os.getcwd(), 'logs', 'get_vendor_summary.log')
file_handler = logging.FileHandler(log_file, mode="a")
formatter = logging.Formatter("%(asctime)s - %(levelname)s - %(message)s")
file_handler.setFormatter(formatter)
file_handler.setLevel(logging.DEBUG)  # Explicitly set handler level

# Add console handler for debugging
console_handler = logging.StreamHandler()
console_handler.setFormatter(formatter)
console_handler.setLevel(logging.DEBUG)

logger.handlers.clear()  # Clear existing handlers to avoid duplication
logger.addHandler(file_handler)
logger.addHandler(console_handler)

logger.debug("Testing logger setup")  # Test log
```


---

## 📝 Example Log Output

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

### 📈 Potential Aggregated Views (for BI Use)

The notebook suggests or creates views such as:
- **Vendor Profitability Table**
- **Inventory Turnover View**
- **Product-wise Sales Summary**
- **Purchase Cost Trends**

These can be exported to `.csv` or loaded into Power BI for visual analytics.

---

## 🔧 Technologies Used

| Tool         | Use Case                          |
|--------------|-----------------------------------|
| Python       | Core language                     |
| pandas       | Data analysis                     |
| SQLite3      | SQL operations on DB              |
| Jupyter      | Interactive notebook environment  |
| SQL          | Custom querying                   |

---

## 📝 Example Output Snippets

```
Database size: 1.82 MB

Tables:
- begin_inventory
- end_inventory
- purchase_prices
- purchases
- Sales
- vendor_invoice

[Sales DataFrame Info]
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 450 entries, 0 to 449
Columns: 6 entries...
```

#### 👉 [`You can check EDA python file`]

---

## 📌 Final Thoughts

This EDA lays the groundwork for:
- Creating clean, aggregated tables for analytics
- Guiding dashboard visualizations
- Improving data pipeline structure before modeling

---
## 👤 Author & Contact

  [👤](https://shivs0147.github.io/my-portfolio/)
  [🖂](https://mail.google.com/mail/?view=cm&to=shivamgabani.744@outlook.com)
  [💬](https://www.linkedin.com/in/shivam-gabani-38192a36b/details/contact-info/)
  [📍](https://maps.app.goo.gl/JuFWwCt8ZEjvE5cE7)

## 🙌 Thanks for Scrolling!
If you found this helpful, consider starring ⭐ the repo or connecting with me.

Let’s keep building awesome data tools!  
**– Shivam Gabani**


