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

| Metric                  | Count | Mean        | Std Dev      | Min        | 25%        | 50%        | 75%        | Max         |
|-------------------------|-------|-------------|--------------|------------|------------|------------|------------|-------------|
| VendorNumber            | 6879  | 9642.33     | 16323.36     | 2.00       | 3664.00    | 7153.00    | 9552.00    | 173357.00   |
| Brand                   | 6879  | 17229.63    | 13289.89     | 58.00      | 4978.50    | 16398.00   | 24531.50   | 90089.00    |
| PurchasePrice           | 6879  | 17.18       | 39.70        | 0.38       | 6.70       | 10.06      | 17.28      | 2290.07     |
| ActualPrice             | 6879  | 25.54       | 54.12        | 0.49       | 9.99       | 14.99      | 25.99      | 2999.99     |
| Volume                  | 6879  | 862.58      | 611.40       | 50.00      | 750.00     | 750.00     | 750.00     | 18000.00    |
| TotalPurchaseQty        | 6879  | 2007.63     | 6029.74      | 1.00       | 48.00      | 354.00     | 1557.00    | 160735.00   |
| TotalPurchaseDollors    | 6879  | 18818.69    | 62877.56     | 0.71       | 603.15     | 3909.25    | 15629.58   | 1584606.00  |
| ExciseTax               | 6879  | 202.02      | 1050.24      | 0.02       | 2.36       | 13.31      | 73.38      | 37163.76    |
| SalesDollars            | 6879  | 83653.16    | 146968.31    | 101.00     | 3661.50    | 21941.00   | 93206.50   | 1138033.00  |
| SalesPrice              | 6879  | 2339.09     | 4661.87      | 0.98       | 143.88     | 689.32     | 2564.61    | 64889.97    |
| SalesQuantity           | 6879  | 355.40      | 1065.19      | 1.00       | 14.00      | 67.00      | 288.00     | 28544.00    |
| TotalFreight            | 6879  | 66254.96    | 63166.38     | 0.27       | 15884.82   | 55551.82   | 89286.27   | 257032.10   |
| GrossProfit             | 6879  | 64834.48    | 118592.69    | -685658.66 | 2042.50    | 15940.68   | 72571.45   | 1049297.00  |
| ProfitMargin            | 6879  | 29.15       | 1160.27      | -91421.15  | 65.69      | 82.84      | 90.99      | 99.99668    |
| StcakTurnOver           | 6879  | 0.70        | 3.21         | 0.000024   | 0.13       | 0.19       | 0.33       | 102.00      |
| SalesToPurchaseRatio    | 6879  | 33.99       | 472.29       | 0.001093   | 2.91       | 5.83       | 11.10      | 30093.25    |


> Provides descriptive statistics for numeric columns (count, mean, std, min, max, etc.)

#### DISTRIBUTION PLOTS FOR NUMERICAL COLUMNS

```python

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

 🧠 **What These Plots Help Identify:**

- Shape of distribution (normal, skewed, uniform, multimodal, etc.)
- Skewness: Whether data is heavily biased toward higher or lower values
- Peaks and gaps: Indicate dominant ranges or sparsely populated intervals
- Potential outliers or data anomalies
- Need for data transformation (e.g., log transformation for right-skewed data)

 📈 **Observations from the Plots:**

- Most columns such as `SalesDollars`, `GrossProfit`, `TotalPurchaseDollars`, `ExciseTax`, etc., show right-skewed distributions with long tails.
- `ProfitMargin` has a left-skewed (or negatively skewed) distribution, indicating many records with low or negative margins.
- Columns like `Brand`, `VendorNumber`, and `StockTurnOver` appear bimodal or multimodal, suggesting multiple clusters or categories in the data.
- The data contains a wide range of values, confirming the presence of extreme values and possible outliers.

 🎯 **Why This is Useful:**

- Understanding the distribution helps in choosing the right statistical methods.
- Highly skewed features may require normalization or transformation.
- Multimodal distributions may hint at segmentation or grouping in the data.

#### OUTLIER DETECTION USING BOX PLOTS

```python

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

 🔍 **What the Box Plots Show:**

- Median: The central line in each box indicates the median value of the column.
- IQR (Interquartile Range): The box represents the range between the 25th percentile (Q1) and the 75th percentile (Q3).
- Whiskers: Extend to 1.5×IQR from Q1 and Q3, covering most of the data.
- Dots/Circles (●): Represent outliers, i.e., values outside the whisker range.

 📈 **Observations from the Plots:**

- Almost all numeric columns contain some outliers.
- Columns like `SalesDollars`, `GrossProfit`, `TotalPurchaseDollars`, and `TotalFreight` show high extreme values.
- Columns such as `ProfitMargin` and `StockTurnOver` have negative outliers, which could indicate losses or low inventory movement.
- Some variables (e.g., `Brand`, `VendorNumber`) appear numeric but might represent categorical codes, which may not be meaningful in a boxplot.

 🎯 **Why It Matters:**

- Outliers can skew summary statistics and impact the performance of machine learning models.
- Some outliers may be data entry errors, while others could be critical business events (e.g., unusually high sales).
- This analysis helps decide whether to keep, remove, or treat outliers during preprocessing.

 ✨ **Summary Statistics Insights:**

> Negative & Zero Values<br/>
  > → Gross Profit: The minimum value of -685,658.66 indicates that some transactions resulted in substantial losses, possibly due to selling below cost or high cost of goods sold.<br/>
  > → Profit Margin: If referring to net profit, the minimum of -91,421.15 suggests significant losses on some products. If it's a percentage, a negative value would imply that costs exceeded revenues.<br/>
  > → Sales Quantity & Sales Dollars: Minimum values of 1 and 101 mean every product sold at least once. However, those with very low sales may be slow-moving or nearing obsolescence.

> Outliers Indicated by High Standard Deviations:<br/>
  > → Purchase & Actual Prices: The max values (2,290.07 & 2,999.99) are significantly higher than the mean (17.17 & 25.54), indicating potential premium products.<br/>
  > → Freight Cost: The wide range in freight costs (0.27 to 257,032.07) indicates variability in shipment size, logistics strategies, or possible inefficiencies.<br/>
  > → Stock Turnover: Ranges from 0 to 102, implying some products sell extremely fast while others remain in stock indefinitely. Value more than 1 indicates that Sold quantity for that product is higher than purchased quantity due to either sales are being fulfilled from older stock.

#### FILTERING OUT INCONSISTENT / INVALID RECORDS

```python
# Execute a SQL query using pandas to fetch data from the Vendor_Sales_Summary table
# Only include rows where GrossProfit, ProfitMargin, and SalesQuantity are all greater than 0
Vendor_Summary = pd.read_sql_query(
    """
    select * from Vendor_Sales_Summary       -- Select all columns from the Vendor_Sales_Summary table
    where GrossProfit > 0                    -- Keep only rows with positive Gross Profit
    and ProfitMargin > 0                     -- Keep only rows with positive Profit Margin
    and SalesQuantity > 0                    -- Keep only rows where sales quantity is greater than 0
    """,
    conn                                     # Use the existing database connection object to run the query
)

# Generate descriptive statistics (count, mean, std, min, max, etc.) for each numeric column
# Transpose the result for better readability (statistics become rows, columns become index)
Vendor_Summary.describe().T

```
| Metric                  | Count | Mean        | Std Dev      | Min        | 25%        | 50%        | 75%        | Max         |
|-------------------------|--------|-------------|--------------|------------|------------|------------|------------|-------------|
| VendorNumber            | 6367   | 9659.47     | 16394.56     | 2.00       | 3252.00    | 7153.00    | 9552.00    | 173357.00   |
| Brand                   | 6367   | 17245.30    | 13271.21     | 58.00      | 5092.00    | 16329.00   | 24554.50   | 90089.00    |
| PurchasePrice           | 6367   | 14.31       | 16.57        | 0.38       | 6.62       | 9.67       | 16.05      | 351.55      |
| ActualPrice             | 6367   | 21.53       | 24.18        | 0.49       | 9.99       | 14.99      | 23.99      | 499.99      |
| Volume                  | 6367   | 859.91      | 548.88       | 50.00      | 750.00     | 750.00     | 750.00     | 18000.00    |
| TotalPurchaseQty        | 6367   | 2025.24     | 5804.55      | 1.00       | 48.00      | 378.00     | 1671.50    | 160735.00   |
| TotalPurchaseDollors    | 6367   | 17396.75    | 46109.97     | 0.71       | 537.75     | 3883.95    | 15640.37   | 952383.20   |
| ExciseTax               | 6367   | 194.30      | 842.92       | 0.02       | 2.86       | 15.74      | 83.32      | 18757.86    |
| SalesDollars            | 6367   | 88513.71    | 148062.59    | 101.00     | 4885.00    | 26520.00   | 102456.50  | 1138033.00  |
| SalesPrice              | 6367   | 2378.91     | 4409.21      | 0.98       | 174.88     | 773.19     | 2725.01    | 61670.62    |
| SalesQuantity           | 6367   | 368.62      | 1039.11      | 1.00       | 18.00      | 80.00      | 314.50     | 28544.00    |
| TotalFreight            | 6367   | 65856.57    | 62701.78     | 0.27       | 15884.82   | 55551.82   | 89286.27   | 257032.10   |
| GrossProfit             | 6367   | 71116.96    | 120036.28    | 0.28       | 3637.46    | 20511.70   | 81880.24   | 1049297.00  |
| ProfitMargin            | 6367   | 78.33       | 19.47        | 0.09       | 71.48      | 84.24      | 91.62      | 99.99668    |
| StcakTurnOver           | 6367   | 0.75        | 3.33         | 0.0076     | 0.14       | 0.20       | 0.34       | 102.00      |
| SalesToPurchaseRatio    | 6367   | 36.68       | 490.81       | 1.0009     | 3.51       | 6.34       | 11.93      | 30093.25    |

#### DISTRIBUTION PLOTS FOR NUMERICAL COLUMNS (UPDATED FOR VENDOR_SUMMARY):

```python

# Select all numerical columns from the Vendor_Summary DataFrame
numerical_cols = Vendor_Summary.select_dtypes(include=[np.number]).columns

# Set the overall size of the figure to accommodate multiple subplots
plt.figure(figsize=(20, 15))

# Loop over each numerical column for plotting
for i, col in enumerate(numerical_cols):
    # Define the position of the current subplot in a 5-row by 4-column grid
    plt.subplot(5, 4, i + 1)
    
    # Apply a dark background style to the plot (enhances visibility with light colors)
    plt.style.use('dark_background')
    
    # Plot a histogram with KDE (Kernel Density Estimate) overlay
    # - bins=30 defines the number of histogram bins
    # - color='lightblue' sets the histogram color
    sns.histplot(Vendor_Summary[col], kde=True, bins=30, color='lightblue')
    
    # Set the title for each subplot to show which column it represents
    plt.title(f'Distribution of {col}')
    
    # Label the x-axis with the column name
    plt.xlabel(col)

# Adjust spacing to prevent overlapping of subplots
plt.tight_layout()

# Display all the generated plots
plt.show()

```

![DISTRIBUTION PLOTS FOR NUMERICAL COLUMNS (UPDATED FOR VENDOR_SUMMARY)](https://github.com/Shivs0147/Vendor-Performance-Analysis-in-Python/blob/main/Snapshot_of_updated_Dist_Plots_Num_Cols.png?raw=true)

 ✅ **Positive Effects:**

- Cleaner Distributions: Outlier-heavy or zero-heavy columns (like SalesQuantity, GrossProfit) will now show more meaningful spread since invalid/zero entries are removed.
- Improved KDE curves: KDE (density lines) will better reflect actual data shape, as extreme low/no-value points are removed.
- Less Noise: Columns won't be skewed by large numbers of zero or negative values.
- Better Visual Comparison: Now that all rows are "valid", distributions across columns are more comparable and interpretable.

 ⚠️ **Potential Caveats:**

- Distribution Shape Changes: Some distributions might shift right (positively) since all 0 and negative values are excluded.
- Sample Size Reduced: You're plotting on fewer records, so some plots may appear sparse or less smooth, especially for lower-frequency data.
- Outliers Stand Out More: With the bulk of noise removed, true outliers may visually pop more (which can be a good thing).
  
 **Summary:**
> → Filtering the data to keep only positive and valid values improves the clarity and accuracy of boxplots by removing irrelevant or problematic data points. This results in cleaner, less skewed visuals and better detection of true outliers that matter for business analysis.<br/>
> → However, this filtering reduces the dataset size, which may make some boxplots appear less dense. Also, outlier ranges and central values might shift toward higher numbers since negative or zero values are excluded.

#### OUTLIER DETECTION USING BOX PLOTS (UPDATED FOR VENDOR_SUMMARY):

```python
# Set up the overall figure size to fit multiple boxplots neatly
plt.figure(figsize=(20, 15))

# Loop through each numerical column in the dataset
for i, col in enumerate(numerical_cols):
    # Create a subplot in a grid of 5 rows and 4 columns
    plt.subplot(5, 4, i + 1)
    
    # Apply a dark background style for better contrast with light plots
    plt.style.use('dark_background')
    
    # Draw a vertical boxplot for the current column
    # Boxplots help identify outliers, spread, median, and quartiles of the data
    sns.boxplot(y=Vendor_Summary[col], color='lightblue')
    
    # Set the title of the subplot to indicate which column is plotted
    plt.title(f'Boxplot of {col}')
    
    # Label the x-axis with the column name (optional for vertical boxplots)
    plt.xlabel(col)

# Adjust spacing between subplots to prevent overlap
plt.tight_layout()

# Display all the boxplots on the screen
plt.show()

```

![OUTLIER DETECTION USING BOX PLOTS (UPDATED FOR VENDOR_SUMMARY))](https://github.com/Shivs0147/Vendor-Performance-Analysis-in-Python/blob/main/Snapshot_of_updated_Outlier_Detection.png?raw=true)

 ✅ **Positive Effects:**

- Cleaner Boxplots: Now the boxplots are based only on relevant, positive-value data — which makes the plots clearer and easier to read.
- Less Skewed Visuals: By removing zero or negative values, the box size and whiskers appear more balanced and better represent the distribution.
- Focused Outlier Detection: True outliers (very high or unusual values) are now more visible and can be treated as actual exceptions or points of interest.
- No Invalid Data Points: The plots are now based only on rows that make real business sense (i.e. profitable, actual sales data).

 ⚠️ **Potential Caveats:**

- Reduced Data Size: Since you're filtering out invalid data, there are fewer rows, so some boxplots might appear less dense or less populated.
- Outlier Range May Change: Previously hidden outliers (due to negative or zero values) may now stand out more clearly in the plots.
- Visual Shift to the Right: With all low/invalid values removed, the central values (like median) and spread of the data may appear shifted toward higher values.

 **Summary:**
> → Filtering out zero and negative values leads to cleaner, more meaningful distributions with improved density curves and less noise. This makes visual comparisons across columns easier and more reliable.<br/>
> → Removing these values can shift distributions toward higher values and reduce sample size, causing some plots to look sparser or less smooth. Also, true outliers become more prominent in the visuals.

#### TOP 10 MOST FREQUENT VENDORS AND PRODUCT DESCRIPTIONS:

```python

# Define the categorical columns to plot count distributions for
Categorical_cols = ["VendorName", "Description"]

# Set the figure size to be wide enough for two side-by-side plots
fig = plt.figure(figsize=(12, 5))

# Apply a dark background style to the plots for better contrast
plt.style.use('dark_background')

# Loop through each categorical column to create count plots
for i, col in enumerate(Categorical_cols):
    # Create a subplot with 1 row and 2 columns; position depends on loop index
    plt.subplot(1, 2, i + 1)
    
    # Plot a horizontal countplot showing the frequency of top 10 values in the column
    sns.countplot(
        y=Vendor_Summary[col], 
        order=Vendor_Summary[col].value_counts().index[:10],  # Top 10 categories by frequency
        color='skyblue'
    )
    
    # Set the title of the subplot
    plt.title(f'Count plot of {col}')
    
    # Label the y-axis with the column name
    plt.ylabel(col)

# Adjust the layout so plots don’t overlap
plt.tight_layout()

# Display the plots
plt.show()

```

![TOP 10 MOST FREQUENT VENDORS AND PRODUCT DESCRIPTIONS](https://github.com/Shivs0147/Vendor-Performance-Analysis-in-Python/blob/main/Snapshot_of_Most_frequent_vendor_product.png?raw=true)

**Summary:**

> → This code generates horizontal count plots for the two categorical columns "VendorName" and "Description", displaying the top 10 most frequent categories in each. The dark background and sky-blue bars make the plots visually appealing and easy to interpret, helping identify the most common vendors and product descriptions in the dataset.

#### CORRELATION HEATMAP OF NUMERICAL FEATURES:

```python
# Set the figure size for the heatmap
plt.figure(figsize=(12, 8))

# Calculate the correlation matrix for all numerical columns
correlation_matrix = Vendor_Summary[numerical_cols].corr()

# Apply dark background styling for better contrast
plt.style.use('dark_background')

# Create a heatmap:
# - annot=True shows the correlation values in each cell
# - cmap='coolwarm' applies a diverging color palette (blue to red)
# - fmt='.3f' limits correlation values to 3 decimal places
# - linewidths=1 adds spacing between cells
sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm', fmt='.3f', linewidths=1)

# Add a title to the heatmap
plt.title('Correlation Heatmap')

```

![CORRELATION HEATMAP OF NUMERICAL FEATURES](https://github.com/Shivs0147/Vendor-Performance-Analysis-in-Python/blob/main/Snapshot_of_Correlation_heatmap.png?raw=true)

**Summary:**

> → This block generates a correlation heatmap to visualize how strongly the numerical columns in the `Vendor_Summary` dataset are related to each other. The `coolwarm` color scale helps quickly identify the nature of these relationships: positive correlations appear in red, negative correlations in blue, and weak or no correlation in near-white shades. This visualization is useful for detecting multicollinearity and uncovering meaningful patterns among key metrics such as `GrossProfit`, `SalesDollars`, `PurchasePrice`, and others.
