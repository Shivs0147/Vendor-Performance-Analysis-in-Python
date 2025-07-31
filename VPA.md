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

### 🔹 Step 5: Summary Statistics

```python
df.describe().T
```

> Provides descriptive statistics for numeric columns (count, mean, std, min, max, etc.)

```python

# ------- DISTRIBUTION PLOTS FOR NUMERICAL COLUMNS --------

# Selecting all numerical columns from the DataFrame
num_cols = df.select_dtypes(include=[np.number]).columns

# Setting up the overall figure size for multiple plots
plt.figure(figsize=(20, 15))

# Looping through each numerical column to plot its distribution
for i, col in enumerate(num_cols):
    plt.subplot(5, 4, i + 1)                         # Creating a subplot grid (5 rows x 4 columns)
    plt.style.use('dark_background')                # Applying a dark background style to the plots
    sns.histplot(df[col], kde=True, bins=30, color='lightblue')  # Plotting histogram with KDE (density curve)
    plt.title(f'Distribution of {col}')             # Adding a title to each subplot
    plt.xlabel(col)                                 # Labeling the x-axis with column name

# Adjusting layout to prevent overlap between plots
plt.tight_layout()

# Displaying all distribution plots
plt.show()

```

![DISTRIBUTION PLOTS FOR NUMERICAL COLUMNS](https://github.com/Shivs0147/Vendor-Performance-Analysis-in-Python/blob/main/Snapshot_of_Dist_Plots_Num_Cols.png?raw=true)

#### 🧠 What These Plots Help Identify:

- Shape of distribution (normal, skewed, uniform, multimodal, etc.)
- Skewness: Whether data is heavily biased toward higher or lower values
- Peaks and gaps: Indicate dominant ranges or sparsely populated intervals
- Potential outliers or data anomalies
- Need for data transformation (e.g., log transformation for right-skewed data)

#### 📈 Observations from the Plots:

- Most columns such as `SalesDollars`, `GrossProfit`, `TotalPurchaseDollars`, `ExciseTax`, etc., show right-skewed distributions with long tails.
- `ProfitMargin` has a left-skewed (or negatively skewed) distribution, indicating many records with low or negative margins.
- Columns like `Brand`, `VendorNumber`, and `StockTurnOver` appear bimodal or multimodal, suggesting multiple clusters or categories in the data.
- The data contains a wide range of values, confirming the presence of extreme values and possible outliers.

#### 🎯 Why This is Useful:

- Understanding the distribution helps in choosing the right statistical methods.
- Highly skewed features may require normalization or transformation.
- Multimodal distributions may hint at segmentation or grouping in the data.

```python

# ------- OUTLIER DETECTION USING BOX PLOTS --------

# Setting up the overall figure size for multiple boxplots
plt.figure(figsize=(20, 15))

# Looping through each numerical column to plot its boxplot
for i, col in enumerate(num_cols):
    plt.subplot(5, 4, i + 1)                         # Creating a subplot grid (5 rows x 4 columns)
    plt.style.use('dark_background')                # Applying a dark background style to the plots
    sns.boxplot(y=df[col], color='lightblue')       # Drawing a boxplot to visualize outliers and spread
    plt.title(f'Boxplot of {col}')                  # Adding a title to each subplot
    plt.xlabel(col)                                 # Labeling the x-axis with column name (though it's not essential here)

# Adjusting layout to prevent overlap between plots
plt.tight_layout()

# Displaying all boxplots
plt.show()

```

![OUTLIER DETECTION USING BOX PLOTS](https://github.com/Shivs0147/Vendor-Performance-Analysis-in-Python/blob/main/Snapshot_of_Outlier_Detection.png?raw=true)

#### 🔍 What the Box Plots Show:

- Median: The central line in each box indicates the median value of the column.
- IQR (Interquartile Range): The box represents the range between the 25th percentile (Q1) and the 75th percentile (Q3).
- Whiskers: Extend to 1.5×IQR from Q1 and Q3, covering most of the data.
- Dots/Circles (●): Represent outliers, i.e., values outside the whisker range.

#### 📈 Observations from the Plots:

- Almost all numeric columns contain some outliers.
- Columns like `SalesDollars`, `GrossProfit`, `TotalPurchaseDollars`, and `TotalFreight` show high extreme values.
- Columns such as `ProfitMargin` and `StockTurnOver` have negative outliers, which could indicate losses or low inventory movement.
- Some variables (e.g., `Brand`, `VendorNumber`) appear numeric but might represent categorical codes, which may not be meaningful in a boxplot.

#### 🎯 Why It Matters:

- Outliers can skew summary statistics and impact the performance of machine learning models.
- Some outliers may be data entry errors, while others could be critical business events (e.g., unusually high sales).
- This analysis helps decide whether to keep, remove, or treat outliers during preprocessing.

### ✨ Summary Statistics Insights

- Negative & Zero Values
  - Gross Profit: The minimum value of -685,658.66 indicates that some transactions resulted in substantial losses, possibly due to selling below cost or high cost of goods sold.
  - Profit Margin: If referring to net profit, the minimum of -91,421.15 suggests significant losses on some products. If it's a percentage, a negative value would imply that costs exceeded revenues.
  - Sales Quantity & Sales Dollars: Minimum values of 1 and 101 mean every product sold at least once. However, those with very low sales may be slow-moving or nearing obsolescence.

- Outliers Indicated by High Standard Deviations:
  - Purchase & Actual Prices: The max values (2,290.07 & 2,999.99) are significantly higher than the mean (17.17 & 25.54), indicating potential premium products.
  - Freight Cost: The wide range in freight costs (0.27 to 257,032.07) indicates variability in shipment size, logistics strategies, or possible inefficiencies.
  - Stock Turnover: Ranges from 0 to 102, implying some products sell extremely fast while others remain in stock indefinitely. Value more than 1 indicates that Sold quantity for that product is higher than purchased quantity due to either sales are being fulfilled from older stock.
