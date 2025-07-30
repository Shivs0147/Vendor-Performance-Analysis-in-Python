
# 📊 Exploratory Data Analysis – SQLite Inventory

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

#### 👉 [`You can check EDA file`](Explaratory%20Data%20Analysis.ipynb)

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


