# 📈 Vendor Performance Analysis

This project uses a Jupyter Notebook (`Vendor_Performance_Analysis.ipynb`) to perform **detailed analysis of vendor performance** based on delivery efficiency, quality ratings, defect rates, and cost effectiveness. It's ideal for procurement, supply chain, and vendor management teams to assess vendor reliability and identify areas of improvement.

---

## 📁 Folder Structure

```plaintext
.
├── Ingestion_db.ipynb                <- Jupyter notebook with all ingestion logic
├── Vendor_Performance_Analysis.ipynb <- Jupyter notebook with full EDA & KPI logic
├── data/                             <- Input CSV files used for the analysis
│   └── Vendor_Data.csv
├── output/                           <- Optional: Exported reports or visualizations
└── README.md                         <- Project documentation (this file)
```

## 🧠 How It Works (Step-by-Step Breakdown)

### 🔹 Step 1: Import Libraries

```python
# Importing required libraries
import pandas as pd                     # For data manipulation and analysis
import numpy as np                      # For numerical operations
import matplotlib.pyplot as plt         # For plotting graphs
import seaborn as sns                   # For advanced statistical visualizations
import warnings                         # To handle warning messages
import sqlite3                          # For SQLite database operations
import scipy.stats as stats             # For statistical calculations
from scipy.stats import ttest_ind       # For two-sample T-test

warnings.filterwarnings("ignore")       # Ignoring warning messages
```

> These are the essential Python libraries we'll use.<br/>
> `pandas` for DataFrames, `sqlite3` for database access, `matplotlib` and `seaborn` for plotting and `scipy` for statistical analysis.

---
### 🔹 Step 2: Database connection

```python
# Creating the SQLite database connection
conn = sqlite3.connect('inventory.db')
```

> Creating a connection to the SQLite database named 'inventory.db'<br/>
> If the database file doesn't exist, it will be created in the current working directory `conn = sqlite3.connect('inventory.db')`

---

### 🔹 Step 3: Fetching data from the 'Vendor_Sales_Summary' table

```python
# Fetching data from the 'Vendor_Sales_Summary' table into a DataFrame
df = pd.read_sql_query("SELECT * FROM Vendor_Sales_Summary", conn)
```

> Fetching all records from the 'Vendor_Sales_Summary' table in the SQLite database<br/>
> The result is loaded directly into a pandas DataFrame

### 🔹 Step 4: Initial Data Inspection

```python
df.info()        # Displays summary information about the DataFrame, including column names, non-null counts, and data types
df.describe()    # Provides descriptive statistics for numeric columns (count, mean, std, min, max, etc.)
df.head()        # Displays the first 5 rows of the DataFrame to get a quick preview of the data
```

> The structure of your dataset (`info()`)<br/>
> The basic statistical profile (`describe()`)<br/>
> The actual content and format of the data (`head()`)
